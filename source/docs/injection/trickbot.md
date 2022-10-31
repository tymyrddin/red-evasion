# TrickBot

1. Open Target Process ([OpenProcess](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-openprocess))
2. Allocate memory ([VirtualAllocEx](https://learn.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualallocex))
3. Copy function into allocated memory ([WriteProcessMemory](https://learn.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-writeprocessmemory))
4. Copy shellcode into allocated memory ([WriteProcessMemory](https://learn.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-writeprocessmemory))
5. Flush cache to commit changes ([FlushInstructionCache](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-flushinstructioncache))
6. Create a remote thread ([CreateRemoteThread](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-createremotethread))
7. Resume the thread or fallback to create a new user thread ([ResumeThread](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-resumethread) or RtlCreateUserThread)

## Resources

* [How TrickBot Malware Hooking Engine Targets Windows 10 Browsers](https://www.sentinelone.com/labs/how-trickbot-malware-hooking-engine-targets-windows-10-browsers/)