# Lecture 8 - January 30, 2018

## Analyzing Text

### Abstract Information Retrieval Architecture

#### Offline
- docs
- rep function
- build index

#### Online
- Represent queries
- Compare to index
- Output results

### Document Term matrix
- list of terms, which documents they're in.
- could put boolean values
- TDF.Id rankings
- Positional information of terms
  - Allows for ordered queries (`Hot dog` is different than `the dog was hot`)
- Sparse matrix
  - Lots of terms and documents
  - little overlap between documents and terms

#### Postings Lists
- Sparse matrix
- Keep track of terms and which documents they are in
- documents are always stored in sorted order.

##### Things to keep track of

###### Term Frequency
- Frequency of terms in an individual document

###### Document Frequency
- Number of documents that a term appears in

###### Example

> One fish, two fish

**Fish:**
- document freqency: 1
- Term frequency: 2
- Position: [2, 4]

#### MapReduce Problem: Buffering the Postings

```scala
val p = new List()

for (postings) {
....
  p.append((docid, ...))
}
p.sort()
```

##### Solution
- Move the document id to the key
- `(term, docid)` is the key
- Let the framework handle the sorting
  - Primary on the term
  - Secondary on the doc id

###### Reducer Idea
- Iterate over all documents for a term
- We can buffer here because of compression.
  - Store the PostingsList in compressed form

## Compression: Postings Encoding
- Instead of encoding the pure document ids, record the cumulative ids (gaps / d-gaps)
  - This gets better with the more documents we have, then the gaps get smaller.
  - This is why we must process the gaps in sorted order
  - `1, 4, 7, 9 -> 1, 3, 4, 2`
- This should give smaller values that are easier to compress
- **Assumption**: Terms must be able to fit into memory **in compressed form**

### Integer Compression

#### VByte
- Use only as many bytes as you actually need
- Reserve 1 bit per byte as the "continuation bit"
- If it's `1` then the number spills over to the next block.
- Want values to fit into as few blocks as possible.

##### Branch Misprediction
- CPU can speculate on which branches to take
- predicting is probably wrong
- have to flush the CPU pipeline, no-ops, slows things down, bad

#### Simple-9
How many ways to encode 28-bits (9 total)?
- 28 1-bit
- 14 2-bit
- 9 3-bit

Use the remaining 4-bits (get to 32-bits) as a selector, which tells you which configuration your in.
- Can be extended to work on 64-bit words.
- Partially solves branch mispredicts
  - Make decisions on entire groups of integers
  - Can hard-code the decompression scheme based on the selector

#### Bit Packing

> What is the smallest number of bits to code a block of 128 integers?

##### PForDelta: What if all fit in 3, except for 1 in 5?
- Bit Packing
- Some bits may overflow, just put them elsewhere
- decompression: pull back in the overflow bits.

#### Golomb Codes
- Very efficient for compression
- Have to set the `b` param for each term.
  - Need the document frequency to set b
  - Relative Frequency-like problem
  - Emit / sort the document frequencies first

## Merging Postings: Different way to think about Inverted Indexing
- Define a merge operation for 2 postings fragments
  - Basically does a merge sort on the fragments to combine
  - This is a monoid!
    - Set of all sorted integers
    - operation: Merge
  - In Spark
    - `flatMap`: Emit singleton postings
      - Buffer in memory until full, compress, emit, and flush buffer until full.
    - `reduceByKey`: Merge them all together
      - Take 2 compressed representations, produce a merged compressed
    - Merge happens in compressed form

### Trade-offs

#### Individual Postings
- Lots of intermediate key value pairs
- Framework does the sorting
- Compress once

#### List Posting
- Fewer intermediate key value pairs
- lots of compressing and sorting
- `aggregateByKey`: In Spark
  - `seqOp`: Type `U` are longer postings lists that you merge
  - Type `V` are collected in the buffer until you merge.

## Recall: Algorithm Design in a Nutshell

### Commutative Monoids First
- LP

### Sorting if you can't
- IP

## Indexing Problem
- Doesn't have to be in real time
- Large batch operation
- Perfect for MapReduce

## Retrieval Problem
- Pretty much real time
- Few results

### Boolean Retrieval: Assume everything fits in Memory (for now)
- Boolean queries, returns the set of results that satisfy the query.
- Build syntax tree, parse query
- Look up the postings for each clause

#### Term-at-a-Time
- Traverse the postings and apply the appropriate set operators
  - Intersect -> AND
  - Union -> OR
  - Negation -> NOT
- Iterating over lists of sorted integers
  - Analysis is linear
- Reverse Polish Notation
  - Postfix notation
  - Avoids having to build a complicated parser
  - basically stack routines

#### Document-at-a-Time
- Traverse the postings for all of the query terms in step
- Take all of the documents, does it satisfy the query?
- Allows the possibility of streaming the results to the user.
- Con: Lots of shifting around of postings, poor memory access

### Ranked Retrieval
- Order the documents by how likely they are to be relevant
- Sort documents by "relevance", documents similar to the query are more likely to be relevant.

#### Vector Space Model
- Number of dimensions is the number of terms
- Each document is a vector
- The query can be inserted into this vector space
- Find the documents that are closest to the query

##### Cosine Similarity Metrix
- Measure the angle between two vectors
- Can't use Euclidean Distance
  - Documents will be long and therefore very similar
  - Queries short
- Inner Product

##### Term Weighting: TF-IDF
- Local Component (TF - Term Frequency): how important is the term to the document
  - A term that is found frequently in a document should be ranked higher
- Global (IDF - Inverse Document Frequency): How important is the term in the collection
  - A term that is found frequently accross all documents is weighted less

![latex-e3a7a2e1-ede7-4a33-a420-419dea60dc18](data/lecture8/latex-e3a7a2e1-ede7-4a33-a420-419dea60dc18.png)
- Log helps to scale the values

##### Retrieval
- Look up postings list for each query terms

###### Document-at-atime
- Evaluate documents one at a time and score all of the query terms
- Have a list of accumulators
- is the document score in the top k?
  - yes: Insert the document score, extract-min if heap is full (note: Use a k-min heap)
  - No: Move on
- Trade-offs
  - Low memory: Only tracking the top k terms
  - Jumping around in the postings list, worse memory access

###### Term-At-a-time
- Evaluate documents one query term at a time
- Start with the most rare term (use the tf-sorting)
- Accumulator: Hash table based
  - Need to store more than the top k
  - As you compute add to the hash, accumulate
- Trade-offs
  - Potentially high memory useage
    - Entry for every postings list that exists
  - Possibility for filtering
  - Possibility for early terminations
    - Maybe you don't need the "top" results
    - Return whats good enough
    - Least frequent terms may be the most important.

### Why store df as part of postings?
- Otherwise you need to make an additional query
- document frequency first allows immediate access

### Retrieval: Things may not fit in memory
1. Partitioning
2. Replication
3. Caching
4. Routing

#### Term vs. Document Partitioning
- Can only do it by the rows (terms)
  - Doesn't really work
- Columns (documents)
  - Slice until it's small enough to fit on a single machine
- Get the top-k results from each partition

#### Replication
- Build n-replications for each partition
- Allows dispatch of queries to each replica
- But need a front end to manage all of the queries (search endpoints)
  - Connects to brokers, which make sure you hit each partition
  - Doesn't matter which replica you hit because they're all the same.

#### Cache
- Results for the most frequent queries
- Hit the cache first

#### Tires
- Divide documents based on quality
- Store highest quality in memory, medium in SSDs, low in HDDs
- Brokers traverse down until quality results are produced.

#### Datacenters
- A company probably has multiple data centers
- The entire structure is replicated across multiple datacenters
- Geo load balancer manager which data centers your queries hit.
