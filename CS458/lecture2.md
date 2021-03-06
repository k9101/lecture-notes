# Lecture 2 - May 7, 2018

## Smashing the Stack for Fun and Profit

### Buffer Overflow Attacks
- Corrupt the execution stack by writing past the end of some buffer. Thereby, corrupting later parts of the stack in potentially harmful ways.
- Example: Jump to a different return address then expected.

A buffer is a contiguous allocation of memory. Dynamic buffers are typically allocated on the stack at run-time.

### Process Memory Organization

Recall the 3 parts of a program:
1. Text
  - Includes the instructions (code) and any read-only data.
2. Data
  - Initialized and uninitialized
  - static variables
3. Stack

#### Recall: Stacks
- Designed with functions / Procedure calls in mind
- Dynamically allocate local variables of a function onto the stack
- Used when calling a function to pass parameters to a new function, recieve a return value from a function, and contains the return address at the base
- Stack Pointer (SP): Points to the top of the stack, points to the last address of the stack.
  - The Lowest numerical address of the stack
- Frame Pointer (FP): Points to a fixed location within a stack frame.
- The stack typically grows down, towards lower memory addresses.
- Local variables can be referenced by offsets from SP or FP
  - Local variables have negative offsets from FP
  - Function Parameters have positive offsets from FP

# Lecture 2: Secure Programs

Recall: Security
1. Confidentiality
2. Integrity
3. Availability

Why is it hard
- New bugs which compomise CIA
- Hard to write new bugs without bugs
- Security-relevent programs, have security relevant bugs
  - Example: Android vulnerability in certificate checking

## Flaws, faults, and failures

A **flaw** is a problem with a program.
- A security flaw is a problem that effects security in some way. i.e. compromises (C, I, A)
- Two Types
  1. Fault: A mistake "behind the scenes", error in the code, a **potential problem**
  2. Failure: As soon as the problem occurs, it becomes a failure. A deviation from the desired behaviour
    - Note that it may not need to deviate from the specified behaviour, as the specification could be wrong

How to find a fault?
- If a user experiences a failure, they'll probably tell you about it
- Intentionally try to cause failures, think like an attacker on your own system
- Unit testing, regression testing
- **Note**: If it can cause your program to crash, then it effects availability, and is therefore a security flaw

Fix faults
- Usually make small patches, **penetrate and patch**

### Problems with Patching
- High pressure to fix the patch quickly, developers have a narrow scope to fix the issue. Rather than looking at the broader scope of the problem.
- The fault may have caused other, unnoticed failures. Partial Fix may cause other problems
- A patch may introduce new faults
- Alternatives to patching?
  - Not really
  - The product is already shipped to the customer, little you can do
  - mitigation
    - blue team, red team excersices: have internal teams which try to attack the product. Find them internally, fix them before any harm is caused.
    - Follow software engineering practices

### Unexpected Behaviour
- Program's behaviour typically has a specification
- Problem: Most implementors wouldn't care if the program completed additional tasks
- This is good in terms of functionality, but provides additional areas for attacks. Harder to manage the codebase
  - Example: `ls` command, has 22 unique flags
- When implementing a security / privacy relevant program you should assume "and nothing else" is appended to the spec.
- Example: "Forgotton password" page, it would be nice if the page told you that you typed your email address incorrectly.
  - This would allow people to probe for usernames
- How to Avoid? **Fuzz Testing**
  - Test against random values, things that aren't expected.

### Types of Security Flaws
- intentional: specifically designed to exploit CIA
  - Virus
  - Malicious: Attack systems
    - General: Anyone
    - Targetted: Some specific entity
  - Non-Malicious: Backdoor with the intention of debugging
- Unitentional


![graph-144af039-5598-482c-9a7e-31be104e63f4](data/lecture2/graph-144af039-5598-482c-9a7e-31be104e63f4.svg)

### Unintentional Security Flaws

#### HeartBleed Bug (SSL/TLS)
- Heartbeat designed to keep a connection open between parties
- Send random messages and the length to a party, they should send it back to you.
- Problem: There was no check for the length.
  - Ask for the maximum packet size (64kb), for 1 character
  - You might get some good data

Handshake
1. User sends a packet to a server
  - Packet contains: Type, Length, Payload
2. Server should echo that packet back, they need to check the length

#### Apple's SSL/TLS Bug
- Used to check validity of certificates
- Problem was missing brackets around the if statement, meaning the third contention never gets evaluated
- Meaning attackers could use invalid certificates

#### Buffer Overflows
- Problematic functions
  - `gets`: get data from stdin
  - `strcpy`: copy string to a buffer
- No bounds checking was performed, meaning it is possible to corrupt memory, for example change the return address at the bottom of the stack.
- Registers
  - `ebp`: Base pointer to the stack frame
    - Old stack, and bottom of the current stack
  - `eip`: Instruction Pointer
  - `esp`: Top of the stack frame

| The Stack |
|-|
| Caller Return |
| Base Pointer |
| Local Variables |
| function params (multiple could be here |
| Return Address |
| Base pointer |
| Local variables (for example a char buffer) |

- Recall that the stack grows down (from high memory -> to low addresses)
  - Except the buffer would grow up (from low -> high memory)

```c
void authenticate() {
  char password[1024];
  gets(password);
  // Do some processing to check the password.
}
```
- If the password is < 1024 characters, then all is good
- If it's over, it would continue to write and begin corrupting areas down the stack (Base ptr, return address)
- Typically this would cause the program to crash, but if your smart you can overwrite the return address to point to some payload.

| Stack |
|-|
| X - return address |
| 4 bytes: Base Pointer |
| 1024 bytes: Buffer |

This becomes interesting when interacting with a web server. If you can obtain root access, then you can cause damages.
Your payload can be some machine instructions, note that it cannot contain the null character (recall that C strings are null terminated).

Problems:
- We don't we exactly which instruction to return to. Use gdb to figure it out.
- Even if you have it, the starting address may not be `0x00`. you don't know where things will eventually be.
  - Use `nop` instructions, to act as a 'sled'. return to somewhere in the nops, which will eventually run your code.

##### Kinds of Buffer Overflows
- **Off by 1**: Attacks which work when a single byte can be written past the end of the buffer.
  - Often exploits some off by 1 error.
  - `for(int i = 0; i <= 256; ++i)`
  - Could potentially allow you to change the base pointer, have another instruction that you want to jump to which would contain your malicious shell code.
- **Heap Overflow**:
  - If a buffer is allocated on the heap, may be able to corrupt memory.

##### Defences against Buffer Overflows

Problems to protect against:
1. Overwrite the stack (i.e. the return address)
2. Execution of code on the stack

- Not use C
  - Not alway avoidable
- Use a **Canary**
  - The compiler inserts some piece of data, check that it hasn't been modified
  - If it was modified, then something bad probably happened
  - Make it `0, CR, LF, -1`, these are ways of ending strings
  - Make it random: then the compiler inserts instructions to check that it hasn't changed
- Make memory pages Writeable XOR Executable
  - You can either write to a page of memory, or execute it.

#### Return Oriented Programming
1. The stack is software (some data structure)
  - The layout of the stack tells what it is for, no need for explicit hardware specifications.
- Try to forge stack frames, return to them

#### Integer Overflows
- Machine Integers can only represent a certain number of values
- If a program assumes that numbers are always positive, can voilate the assumption by overflowing.

#### Format String Vulnerabilities
- `printf("%s", buffer)`
- Use format codes to specify data formats to print
- Suppose the buffer contains `...%s%s...`, calling `printf(buffer)` will cause it to look down the stack for parameters. Printing areas of the stack.
- `%n` will write to a memory address on the stack

| Stack |
|-|
| ... |
| Return |
| `buffer = "%s%s"` |
| `printf` |
