# Thread execution hijacking

This is very similar to [Process Hollowing](hollowing.md) but targets an existing process rather than creating a process in a 
suspended state.

## Overview

1. Locate and open a target process to control ([OpenProcess](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-openprocess)).
2. Allocate memory region for malicious code ([VirtualAllocEx](https://learn.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualallocex)).
3. Write malicious code to allocated memory ([WriteProcessMemory](https://learn.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-writeprocessmemory)).
4. Identify the thread ID of the target thread to hijack ([CreateToolhelp32Snapshot()](https://learn.microsoft.com/en-us/windows/win32/api/tlhelp32/nf-tlhelp32-createtoolhelp32snapshot), [Process32First()](https://learn.microsoft.com/en-us/windows/win32/api/tlhelp32/nf-tlhelp32-process32first), and [Process32Next()](https://learn.microsoft.com/en-us/windows/win32/api/tlhelp32/nf-tlhelp32-process32next), to loop through a snapshot of a process and extend capabilities to enumerate process information).
5. Open the target thread ([OpenThread](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-openthread)).
6. Suspend the target thread ([SuspendThread](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-suspendthread)).
7. Obtain the thread context ([GetThreadContext](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-getthreadcontext)).
8. Update the instruction pointer to the malicious code (`context.Rip = (DWORD_PTR)remoteBuffer;`).
9. Rewrite the target thread context ([SetThreadContext](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-setthreadcontext)).
10. Resume the hijacked thread ([ResumeThread](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-resumethread)).

## C++ code

* [ZeroMemoryEx/Thread-Hijacking](https://github.com/ZeroMemoryEx/Thread-Hijacking/blob/master/Thread-Hijacking/Thread-Hijacking.cpp)

## Resources

* [MITRE: Thread execution hijacking](https://attack.mitre.org/techniques/T1055/003/)
