# Shellcode injection

1. Open a target process with all access rights (OpenProcess).
2. Allocate target process memory for the shellcode (VirtualAllocEx).
3. Write shellcode to allocated memory in the target process (WriteProcessMemory).
4. Execute the shellcode using a remote thread (CreateRemoteThread).