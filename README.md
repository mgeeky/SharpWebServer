# SharpWebServer

A Red Team oriented simple **HTTP & WebDAV** server written in C# with functionality to capture Net-NTLM hashes.
To be used for serving payloads on compromised machines for lateral movement purposes.

Requires .NET Framework 4.5 and _System.Net_ and _System.Net.Sockets_ references.

## Usage

```
    :: SharpWebServer ::
    a Red Team oriented C# Simple HTTP Server with Net-NTLMv1/2 hashes capture functionality

Authors:
    - Can Güney Aksakalli (github.com/aksakalli)          - original implementation
    - harrypatrick442 (github.com/harrypatrick442)        - aksakalli's fork & changes
    - Dominic Chell (@domchell) from MDSec                - Net-NTLMv2 hashes capture code borrowed from Farmer
    - Mariusz B. / mgeeky, <mb [at] binary-offensive.com> - combined all building blocks together,
                                                            added connection keep-alive to NTLM Authentication

Usage:
    SharpWebServer.exe <port=port> [dir=path] [verbose=true] [ntlm=true] [logfile=path]

Options:
    port    - TCP Port number on which to listen (1-65535)
    dir     - Directory with files to be hosted.
    verbose - Turn verbose mode on.
    seconds - Specifies how long should the server be running. Default: indefinitely
    ntlm    - Require NTLM Authentication before serving files. Useful to collect NetNTLMv2 hashes
              (in MDSec's Farmer style)
    logfile - Path to output logfile.

```

## Example

Example use-case serving files and capturing Net-NTLM hashes at the same time:

**Server**:
```
C:\> SharpWebServer.exe port=8888 dir=C:\Windows\Temp verbose=true ntlm=true

    :: SharpWebServer ::
    a Red Team oriented C# Simple HTTP & WebDAV Server with Net-NTLM hashes capture functionality

Authors:
    - Dominic Chell (@domchell) from MDSec                - Net-NTLM hashes capture code borrowed from Farmer
    - Mariusz B. / mgeeky, <mb [at] binary-offensive.com> - WebDAV implementation, NTLM Authentication keep-alive,
                                                            all the rest.

Usage:
    SharpWebServer.exe <port=port> [dir=path] [verbose=true] [ntlm=true] [logfile=path]

Options:
    port    - TCP Port number on which to listen (1-65535)
    dir     - Directory with files to be hosted.
    verbose - Turn verbose mode on.
    seconds - Specifies how long should the server be running. Default: indefinitely
    ntlm    - Require NTLM Authentication before serving files. Useful to collect NetNTLM hashes
              (in MDSec's Farmer style)
    logfile - Path to output logfile.
```

**Client**:
```
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
```
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

- WebDAV implementation, NTLM Authentication keep-alive logic & all the rest `Mariusz B. / mgeeky, '21, <mb [at] binary-offensive.com>`
