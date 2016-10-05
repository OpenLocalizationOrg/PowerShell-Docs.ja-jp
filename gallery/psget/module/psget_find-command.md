# Find コマンド

モジュールで PowerShell コマンドを検索します。

## 説明
Find コマンド コマンドレットは、コマンドレット、エイリアス、関数、およびワークフローなどの PowerShell コマンドを検索します。 Find コマンドは、登録されているリポジトリでモジュールを検索します。
このコマンドレットを検索するコマンドごとに、PSGetCommandInfo オブジェクトを返します。 PSGetCommandInfo オブジェクトは、コマンドが含まれたモジュールをインストールするインストール モジュールのコマンドレットに渡すことができます。

- Find コマンドは、バージョン パラメーターを使用してフィルター処理できます。 MinimumVersion RequiredVersion、AllVersions です。
  - これらのパラメーターでは、相互に排他的です。
  - これらのバージョン パラメーターは、任意のワイルドカードを含まない 1 つのモジュール名のみで許可されます。
  - RequiredVersion パラメーターが指定されていない場合、検索コマンドは、最低限のバージョンが指定されていない場合、同じかまたは指定された最小のバージョンや、モジュールの最新のバージョンよりも大きいは、モジュールの最新バージョンを返します。
  - RequiredVersion パラメーターが指定されている場合、検索コマンドには、指定されたバージョンを完全に一致するモジュールのバージョンのみを返します。
- モジュールのメタデータの検索コマンドをフィルターできますタグ パラメーターを使用
- リポジトリに固有の検索の言語で検索コマンドを絞り込むフィルター パラメーターを使用します。
- Find コマンドは、登録されているリポジトリのほとんどまたはすべてのモジュールにフィルター処理できます。

## コマンドレット構文
```powershell
Get-Command -Name Find-Command -Module PowerShellGet -Syntax
```

## コマンドレットのオンライン ヘルプ リファレンス

[Find コマンド](http://go.microsoft.com/fwlink/?LinkId=733636)

## コマンド例
```powershell

# Find a specific command
Find-Command -Name Get-ScriptAnalyzerRule

Name                                Version    ModuleName                          Repository
----                                -------    ----------                          ----------
Get-ScriptAnalyzerRule              1.5.0      PSScriptAnalyzer                    PSGallery

# Find multiple commands
Find-Command -Name Get-ScriptAnalyzerRule, Invoke-ScriptAnalyzer

# Find all available commands from all registered repositories
Find-Command

# Find available commands from few registered repositories
Find-Command -Repository PSGallery,PrivatePSGallery

# Find all commands in a specified repository
Find-Command -Repository PSGallery

# Find a command defined in a specific module
Find-Command -Name Get-ScriptAnalyzerRule -Module PSScriptAnalyzer

# Find commands from all versions of a module
Find-Command -ModuleName PSReadline -AllVersions

# Find commands with module name and MinimumVersion.
Find-Command -ModuleName PSReadline -MinimumVersion 1.1

# Find commands with module name and exact version
Find-Command -ModuleName AzureRM -RequiredVersion 1.4.0

# Find commands defined modules with -Filter based search. -Filter searches in description and module names
Find-Command -Filter Cookbook
Find-Command -Filter RBAC

# Find all commands with tags Azure or DSC in module metadata
Find-Command -Tag Azure, DSC

```

<!--HONumber=Oct16_HO1-->


