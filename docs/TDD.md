# Technical Design Document: Hello World

## System Architecture & Flow

Input: Command-line arguments (argc, argv) ↓ Parse arguments (argc check) ↓ ├─ argc < 2 → Print usage to stderr, exit(1) └─ argc >= 2 → Extract argv[1] as name ↓ Format & print "Hello, [name]!" to stdout ↓ Exit with status 0

**Program Entry:** `main(int argc, char *argv[])`  
**Single Executable:** hello (compiled C binary)  
**Standard Flow:** Argument validation → Greeting generation → Output → Termination

---

## Component Specifications

### Function: `main(int argc, char *argv[])`

**Purpose:** Program entry point; argument parsing and control flow

**Signature:**
```c
int main(int argc, char *argv[])
Parameters:
•	argc: Argument count (integer)
•	argv: Argument vector (pointer to string array)
Logic:
1.	Check if argc < 2 
o	If true: invoke error handling (see below)
o	If false: proceed to step 2
2.	Extract argv[1] (user-provided name)
3.	Print formatted string: "Hello, %s!\n" with argv[1] to stdout
4.	Return 0 (success)
Return Value:
•	0 on successful execution
•	1 on argument error
Implementation Constraints:
•	No dynamic memory allocation required
•	No buffer overflow protection needed (assume argv is safe)
•	Use standard library only: <stdio.h>, <stdlib.h>
________________________________________
Error Handling & Exit Codes
Condition	Action	Exit Code
No arguments provided	Print "Usage: hello <name>\n" to stderr	1
Valid argument	Print greeting to stdout	0
stderr Output:
fprintf(stderr, "Usage: %s <name>\n", argv[0]);
Rationale: Usage message to stderr allows piping stdout separately; exit(1) signals error to shell/caller.
________________________________________
Verification & Testing
Compilation
gcc -o hello hello.c -Wall -Wextra
Manual Test Cases
Test 1: Valid Input
$ ./hello Alice
Expected Output: Hello, Alice!
Exit Code: 0
Test 2: Missing Argument
$ ./hello
Expected stderr: Usage: ./hello <name>
Exit Code: 1
Test 3: Name with Spaces (shell quoted)
$ ./hello "Bob Smith"
Expected Output: Hello, Bob Smith!
Exit Code: 0
Test 4: Multiple Arguments (uses first)
$ ./hello John Doe
Expected Output: Hello, John!
Exit Code: 0
Verification Steps
1.	Compile without warnings (-Wall -Wextra)
2.	Run all 4 test cases
3.	Verify exit codes with echo $?
4.	Confirm stderr vs stdout separation with 2>&1 redirection tests

