# Process hollowing

1. Create a target process in a suspended state (CreateProcessA).
2. Obtain a handle for the malicious image (CreateFileA)
3. Allocate enough memory for the image inside the processes own address space (VirtualAlloc, GetFileSize can be used to retrieve the size of the malicious image for dwSize).
4. Write to local process memory (ReadFile and CloseHandle).
5. Identify the location of the process in memory and the entry point (GetThreadContext and ReadProcessMemory).
6. Un-map legitimate code from process memory (ZwUnmapViewOfSection, imported from ntdll.dll).
7. Obtain the size of the image found in file headers (e_lfanew and SizeOfImage from the Optional header).
8. Write the PE headers then the PE sections (WriteProcessMemory).
9. Write each section (NumberOfSections, e_lfanew, and WriteProcessMemory).
10. Change EAX to point to the entry point (SetThreadContext).
11. Take the process out of suspended state (ResumeThread).

## Resources

* [Process hollowing](https://attack.mitre.org/techniques/T1055/012/)
