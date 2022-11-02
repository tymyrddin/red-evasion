# Behavioural signatures

Obfuscating functions and properties can achieve a lot with minimal modification. Even after breaking static 
signatures attached to a file, modern engines may still observe the behavior and functionality of the binary. 
This presents problems for attackers that cannot be solved with simple obfuscation.

Modern antivirus engines will employ two common methods to detect behaviour: observing imports and hooking known 
malicious calls. While imports can be easily obfuscated or modified with minimal requirements, hooking requires 
complex techniques.

## Lab

Obfuscate the following C snippet, ensuring no suspicious API calls are present in the IAT:

    #include <windows.h>
    #include <stdio.h>
    #include <lm.h>
    
    int main() {
        printf("GetComputerNameA: 0x%p\\n", GetComputerNameA);
        CHAR hostName[260];
        DWORD hostNameLength = 260;
        if (GetComputerNameA(hostName, &hostNameLength)) {
            printf("hostname: %s\\n", hostName);
        }
    }

## Resources

* [The difference between signature-based and behavioural detections](https://s3cur3th1ssh1t.github.io/Signature_vs_Behaviour/)
* [The Journey of Evasion Enters Behavioural Phase](https://www.virusbulletin.com/virusbulletin/2016/07/journey-evasion-enters-behavioural-phase/)
