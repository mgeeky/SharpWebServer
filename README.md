# SharpWebServer

A Red Team oriented simple **HTTP & WebDAV** server written in C# with functionality to capture Net-NTLM hashes.
To be used for serving payloads on compromised machines for lateral movement purposes.

Requires .NET Framework 4.5 and _System.Net_ and _System.Net.Sockets_ references.

## Usage

```powershell
    :: SharpWebServer ::
    a Red Team oriented C# Simple HTTP Server with Net-NTLMv1/2 hashes capture functionality

Authors:
    - Can Güney Aksakalli (github.com/aksakalli)          - original implementation
    - harrypatrick442 (github.com/harrypatrick442)        - aksakalli's fork & changes
    - Dominic Chell (@domchell) from MDSec                - Net-NTLMv2 hashes capture code borrowed from Farmer
    - Mariusz Banach / mgeeky, <mb [at] binary-offensive.com> - combined all building blocks together,
                                                            added connection keep-alive to NTLM Authentication

Usage:
    SharpWebServer.exe <port=port> [dir=path] [verbose=true] [ntlm=true] [redir=true] [logfile=path]

Options:
    port    - TCP Port number on which to listen (1-65535)
    dir     - Directory with files to be hosted.
    verbose - Turn verbose mode on.
    seconds - Specifies how long should the server be running. Default: indefinitely
    ntlm    - Require NTLM Authentication before serving files. Useful to collect NetNTLM hashes
              (in MDSec's Farmer style)
    redir   - Redirect after NTLM authentication based on redir paramerer in the url (e.g. ?redir=https://example.com)
    logfile - Path to output logfile.

```

## Example

Example use-case serving files and capturing Net-NTLM hashes at the same time:

**Server**:

```powershell
C:\> SharpWebServer.exe port=8888 dir=C:\Windows\Temp verbose=true ntlm=true

    :: SharpWebServer ::
    a Red Team oriented C# Simple HTTP & WebDAV Server with Net-NTLM hashes capture functionality

[.] Serving HTTP server on port  : 8888
[.] Will run for this long       : 60 seconds
[.] Verbose mode turned on.
[.] NTLM mode turned on.
[.] Serving files from directory : C:\Windows\Temp

SharpWebServer [29.03.21, 17:55:14] NTLM: Sending 401 Unauthorized due to lack of Authorization header.
SharpWebServer [29.03.21, 17:55:14] ::1 - "GET /test.txt" - len: 0 (401)
SharpWebServer [29.03.21, 17:55:14] NTLM: Sending 401 Unauthorized with NTLM Challenge Response.
SharpWebServer [29.03.21, 17:55:14] ::1 - "GET /test.txt" - len: 0 (401)

[+] SharpWebServer: Net-NTLM hash captured:
TestUser:::1122334455667788:66303EE2DF9417E2FE07E1B7FD663205:010100000000000092EC04E8B324D701C2B561D5FECBB325000000000200060053004D0042000100160053004D0042002D0054004F004F004C004B00490054000400120073006D0062002E006C006F00630061006C000300280073006500720076006500720032003000300033002E0073006D0062002E006C006F00630061006C000500120073006D0062002E006C006F00630061006C00080030003000000000000000010000000020000045E18A336DA58F5F0F826F846C699F77DCCF02BA5135525AC52EFBB0C0A1F1160A0010000000000000000000000000000000000009001C0048005400540050002F006C006F00630061006C0068006F00730074000000000000000000

SharpWebServer [29.03.21, 17:55:14] ::1 - "GET /test.txt" - len: 11 (200)
```

**Client**:

```powershell
C:\> curl -sD- http://localhost:8888/test.txt --ntlm --negotiate -u TestUser:TestPassword
HTTP/1.1 401 Unauthorized
Transfer-Encoding: chunked
WWW-Authenticate: NTLM
Date: Mon, 29 Mar 2021 15:55:14 GMT

HTTP/1.1 401 Unauthorized
Transfer-Encoding: chunked
WWW-Authenticate: NTLM TlRMTVNTUAACAAAABgAGADgAAAAFAomiESIzRFVmd4gAAAAAAAAAAIAAgAA+AAAABQLODgAAAA9TAE0AQgACAAYAUwBNAEIAAQAWAFMATQBCAC0AVABPAE8ATABLAEkAVAAEABIAcwBtAGIALgBsAG8AYwBhAGwAAwAoAHMAZQByAHYAZQByADIAMAAwADMALgBzAG0AYgAuAGwAbwBjAGEAbAAFABIAcwBtAGIALgBsAG8AYwBhAGwAAAAAAA==
Date: Mon, 29 Mar 2021 15:55:14 GMT

HTTP/1.1 200 OK
Content-Length: 6
Content-Type: text/plain
Date: Mon, 29 Mar 2021 15:55:14 GMT

foobar
```

**WebDAV client**:

```powershell
C:\> dir \\localhost@8888\test
 Volume in drive \\localhost@8888\test has no label.
 Volume Serial Number is 0000-0000

 Directory of \\localhost@8888\test

30.03.2021  05:12    <DIR>          .
30.03.2021  05:12    <DIR>          ..
30.03.2021  04:27                11 test2.txt
30.03.2021  05:12                12 test3.txt
30.03.2021  05:12    <DIR>          test4
               2 File(s)             23 bytes
               3 Dir(s)  225 268 776 960 bytes free

C:\> type \\localhost@8888\test\test4\test5.txt
Hello world!

C:\> copy \\localhost@8888\test\test4\test5.txt .
        1 file(s) copied.
```

## Authors

- NTLM hashes capture code & TCP Listener backbone borrowed from MDSec ActiveBreach Farmer project written by Dominic Chell (@domchell):
  - https://github.com/mdsecactivebreach/Farmer

- WebDAV implementation, NTLM Authentication keep-alive logic & all the rest `Mariusz Banach / mgeeky, '21, <mb [at] binary-offensive.com>`



---

### ☕ Show Support ☕

This and other projects are outcome of sleepless nights and **plenty of hard work**. If you like what I do and appreciate that I always give back to the community,
[Consider buying me a coffee](https://github.com/sponsors/mgeeky) _(or better a beer)_ just to say thank you! 💪 

---

```   
   Mariusz Banach / mgeeky, 21
   <mb [at] binary-offensive.com>
   (https://github.com/mgeeky)
```
