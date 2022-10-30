# Dynamic-link library injection

The most common method of process injection is DLL Injection, which is popular due to how easy it is. A program can 
simply drop a DLL to the disk and then use "CreateRemoteThread" to call "LoadLibrary" in the target process, the 
loader will then take care of the rest. 

1. Locate a target process to inject (CreateToolhelp32Snapshot(), Process32First(), and Process32Next()).
2. Open the target process (GetModuleHandle, GetProcAddress, or OpenProcess).
3. Allocate memory region for malicious DLL (VirtualAllocEx).
4. Write the malicious DLL to allocated memory (WriteProcessMemory).
5. Load and execute the malicious DLL (LoadLibrary imported from kernel32. Once loaded, CreateRemoteThread can be used to execute memory using LoadLibrary as the starting function).

## Resources

* [Dynamic-link library injection](https://attack.mitre.org/techniques/T1055/001/)
