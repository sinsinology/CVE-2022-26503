# CVE-2022-26503

## Summary:

Vulnerability (CVE-2022-26503) in Veeam Agent *for Microsoft Windows* allows local privilege escalation. An attacker who successfully exploited this vulnerability could run arbitrary code with LOCAL SYSTEM privileges.

Shout out to @ultrayoba

## The Patch:

The implemented patch shows blood trail of Deserialization:

![Patch](images/patch.png)

**Veeam official KB mentions:**

> Veeam Agent *for Microsoft Windows* uses Microsoft .NET data serialization mechanisms. A local user may send malicious code to the network port opened by Veeam Agent for Windows Service *(TCP 9395 by default),* which will not be deserialized properly.
> 

## Analysis:

Reviewing process behind the specified port results in finding `Veeam.EndPoint.Service.exe`

![01](images/01.png)

Reviewing `Veeam.EndPoint.Service.exe` indicates registration of `VeeamService` for .NET Remoting

![02](images/02.png)

Processes communicating with the registered channel gives out information about `Veeam.EndPoint.Tray.exe` showing this channel gets used by Tray process

![03](images/03.png)

Loaded modules by the Tray indicate `Veeam.Common.Remoting.dll`

![04](images/04.png)

Use of `TcpClientChannel` with enabled `Secure`

![05](images/05.png)

## Exploit:

![06](images/PoC.gif)
