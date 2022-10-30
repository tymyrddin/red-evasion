# TrickBot

1. Open Target Process (OpenProcess)
2. Allocate memory (VirtualAllocEx)
3. Copy function into allocated memory (WriteProcessMemory)
4. Copy shellcode into allocated memory (WriteProcessMemory)
5. Flush cache to commit changes (FlushInstructionCache)
6. Create a remote thread (RemoteThread)
7. Resume the thread or fallback to create a new user thread (ResumeThread or RtlCreateUserThread)

## Resources

* [How TrickBot Malware Hooking Engine Targets Windows 10 Browsers](https://www.sentinelone.com/labs/how-trickbot-malware-hooking-engine-targets-windows-10-browsers/)