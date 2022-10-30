# Thread execution hijacking

1. Locate and open a target process to control (OpenProcess).
2. Allocate memory region for malicious code (VirtualAllocEx).
3. Write malicious code to allocated memory (WriteProcessMemory).
4. Identify the thread ID of the target thread to hijack (CreateToolhelp32Snapshot(), Thread32First(), and Thread32Next(), to loop through a snapshot of a process and extend capabilities to enumerate process information).
5. Open the target thread (OpenThread).
6. Suspend the target thread (SuspendThread).
7. Obtain the thread context (GetThreadContext).
8. Update the instruction pointer to the malicious code (`context.Rip = (DWORD_PTR)remoteBuffer;`).
9. Rewrite the target thread context (SetThreadContext).
10. Resume the hijacked thread (ResumeThread).

## Resources

* [Thread execution hijacking](https://attack.mitre.org/techniques/T1055/003/)
