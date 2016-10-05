# JEA エンドポイントの作成および接続
JEA エンドポイントを作成するには、特別に構成された PowerShell セッション構成ファイルを作成し、登録する必要があります。このファイルは、**New-PSSessionConfigurationFile** コマンドレットで登録できます。

```powershell
New-PSSessionConfigurationFile -SessionType RestrictedRemoteServer -TranscriptDirectory "C:\ProgramData\JEATranscripts" -RunAsVirtualAccount -RoleDefinitions @{ 'CONTOSO\NonAdmin_Operators' = @{ RoleCapabilities = 'Maintenance' }} -Path "$env:ProgramData\JEAConfiguration\Demo.pssc" 
```

これにより、次のようなセッション構成ファイルが作成されます。 
```powershell
@{

# Version number of the schema used for this document
SchemaVersion = '2.0.0.0'

# ID used to uniquely identify this document
GUID = 'a384fdd3-5830-4a2c-ac86-cdd1822c3afd'

# Author of this document
Author = 'Administrator'

# Description of the functionality provided by these settings
# Description = ''

# Session type defaults to apply for this session configuration. Can be 'RestrictedRemoteServer' (recommended), 'Empty', or 'Default'
SessionType = 'RestrictedRemoteServer'

# Directory to place session transcripts for this session configuration
TranscriptDirectory = 'C:\ProgramData\JEATranscripts'

# Whether to run this session configuration as the machine's (virtual) administrator account
RunAsVirtualAccount = $true

# Groups associated with machine's (virtual) administrator account
# RunAsVirtualAccountGroups = 'Remote Desktop Users', 'Remote Management Users'

# Scripts to run when applied to a session
# ScriptsToProcess = 'C:\ConfigData\InitScript1.ps1', 'C:\ConfigData\InitScript2.ps1'

# User roles (security groups), and the Role Capabilities that should be applied to them when applied to a session
RoleDefinitions = @{
    'CONTOSO\NonAdmin_Operators' = @{
        'RoleCapabilities' = 'Maintenance' } }

} 
```
JEA エンドポイントを作成する場合は、コマンドの次のパラメーター (およびファイル内の対応するキー) を設定する必要があります。
1.  SessionType を RestrictedRemoteServer に
2.  RunAsVirtualAccount を **$true** に
3.  TranscriptPath を各セッションの後 "over the shoulder" トランスクリプトが保存されるディレクトリに
4.  RoleDefinitions をどのグループがどの "ロール機能" にアクセスできるかを定義するハッシュテーブルに設定します。  このフィールドでは、このエンドポイントで**どのユーザー**が**どの操作**を実行できるかを定義します。   ロール機能は特別なファイルで、これについては後で説明します。


RoleDefinitions フィールドでは、どのグループがどのロール機能にアクセスできるかを定義します。  ロール機能は、接続ユーザーに公開される一連の機能を定義するファイルです。  ロール機能は、**New-PSRoleCapabilityFile** コマンドを使用して作成できます。

```powershell
New-PSRoleCapabilityFile -Path "$env:ProgramFiles\WindowsPowerShell\Modules\DemoModule\RoleCapabilities\Maintenance.psrc" 
```

これにより、次のようなテンプレート ロール機能が生成されます。
```
@{

# ID used to uniquely identify this document
GUID = '9287a34f-3f0e-4fbe-9dd7-f1361ba9fd65'

# Author of this document
Author = 'Administrator'

# Description of the functionality provided by these settings
# Description = ''

# Company associated with this document
CompanyName = 'Unknown'

# Copyright statement for this document
Copyright = '(c) 2015 Administrator. All rights reserved.'

# Modules to import when applied to a session
# ModulesToImport = 'MyCustomModule', @{ ModuleName = 'MyCustomModule'; ModuleVersion = '1.0.0.0'; GUID = '4d30d5f0-cb16-4898-812d-f20a6c596bdf' }

# Aliases to make visible when applied to a session
# VisibleAliases = 'Item1', 'Item2'

# Cmdlets to make visible when applied to a session
# VisibleCmdlets = 'Invoke-Cmdlet1', @{ Name = 'Invoke-Cmdlet2'; Parameters = @{ Name = 'Parameter1'; ValidateSet = 'Item1', 'Item2' }, @{ Name = 'Parameter2'; ValidatePattern = 'L*' } }

# Functions to make visible when applied to a session
# VisibleFunctions = 'Invoke-Function1', @{ Name = 'Invoke-Function2'; Parameters = @{ Name = 'Parameter1'; ValidateSet = 'Item1', 'Item2' }, @{ Name = 'Parameter2'; ValidatePattern = 'L*' } }

# External commands (scripts and applications) to make visible when applied to a session
# VisibleExternalCommands = 'Item1', 'Item2'

# Providers to make visible when applied to a session
# VisibleProviders = 'Item1', 'Item2'

# Scripts to run when applied to a session
# ScriptsToProcess = 'C:\ConfigData\InitScript1.ps1', 'C:\ConfigData\InitScript2.ps1'

# Aliases to be defined when applied to a session
# AliasDefinitions = @{ Name = 'Alias1'; Value = 'Invoke-Alias1'}, @{ Name = 'Alias2'; Value = 'Invoke-Alias2'}

# Functions to define when applied to a session
# FunctionDefinitions = @{ Name = 'MyFunction'; ScriptBlock = { param($MyInput) $MyInput } }

# Variables to define when applied to a session
# VariableDefinitions = @{ Name = 'Variable1'; Value = { 'Dynamic' + 'InitialValue' } }, @{ Name = 'Variable2'; Value = 'StaticInitialValue' }

# Environment variables to define when applied to a session
# EnvironmentVariables = @{ Variable1 = 'Value1'; Variable2 = 'Value2' }

# Type files (.ps1xml) to load when applied to a session
# TypesToProcess = 'C:\ConfigData\MyTypes.ps1xml', 'C:\ConfigData\OtherTypes.ps1xml'

# Format files (.ps1xml) to load when applied to a session
# FormatsToProcess = 'C:\ConfigData\MyFormats.ps1xml', 'C:\ConfigData\OtherFormats.ps1xml'

# Assemblies to load when applied to a session
# AssembliesToLoad = 'System.Web', 'System.OtherAssembly, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'

} 

```
JEA セッション構成で使用するには、ロール機能を有効な PowerShell モジュールとして "RoleCapabilities" という名前のディレクトリに保存する必要があります。 必要に応じて、1 つのモジュールに複数のロール機能ファイルを含めることができます。

ユーザーが JEA セッションに接続するときにどのコマンドレット、関数、エイリアス、およびスクリプトにアクセスできるかの構成を開始するには、コメント アウトされたテンプレートに従ってロール機能ファイルに独自のルールを追加します。 ロール機能を構成する方法の詳細については、完全な[エクスペリエンス ガイド](http://aka.ms/JEA)をご覧ください。

最後に、セッション構成と関連ロール機能のカスタマイズが完了した後、このセッション構成を登録し、**Register-PSSessionConfiguration** を実行してエンドポイントを作成します。

```powershell
Register-PSSessionConfiguration -Name Maintenance -Path "C:\ProgramData\JEAConfiguration\Demo.pssc" 
```

## JEA エンドポイントへの接続
JEA エンドポイントへの接続は、他の PowerShell エンドポイントへの接続と同じように機能します。  **New-PSSession**、**Invoke-Command**、または **Enter-PSSession** の "ConfigurationName" パラメーターとして JEA エンドポイント名を付けることのみが必要です。

```powershell
Enter-PSSession -ConfigurationName Maintenance -ComputerName localhost
```
JEA セッションに接続した後は、自分がアクセス権を持つロール機能のホワイトリストに登録されたコマンドのみを実行できます。 ロールに許可されていないコマンドを実行しようとすると、エラーが発生します。

<!--HONumber=Oct16_HO1-->


