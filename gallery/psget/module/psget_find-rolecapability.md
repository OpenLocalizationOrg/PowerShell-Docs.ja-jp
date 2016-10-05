# 検索 RoleCapability

モジュール内のロールの機能を検索します。

## 説明
検索 RoleCapability コマンドレットは、モジュールで PowerShell ロール機能を検索します。 検索 RoleCapability は、登録されているリポジトリでモジュールを検索します。 このコマンドレットが検出された各ロール機能の PSGetRoleCapabilityInfo オブジェクトを返します。 PSGetRoleCapabilityInfo オブジェクトは、ロール機能を含むモジュールをインストールするインストール モジュールのコマンドレットに渡すことができます。
アプリケーションというようにユーザーが利用できる、だけ十分な Administration (JEA) エンドポイントでを PowerShell ロール機能は、コマンドを定義します。 ロールの機能は、.psrc 拡張子の付いたファイルによって定義されます。

- 検索 RoleCapability がバージョン パラメーターを使用してフィルター処理できます。 MinimumVersion RequiredVersion、AllVersions です。
  - これらのパラメーターでは、相互に排他的です。
  - これらのバージョン パラメーターは、任意のワイルドカードを含まない 1 つのモジュール名のみで許可されます。
  - RequiredVersion パラメーターが指定されていない場合、検索 RoleCapability は最低限のバージョンが指定されていない場合、同じかまたは指定された最小のバージョンや、モジュールの最新のバージョンよりも大きいは、モジュールの最新バージョンを返します。
  - RequiredVersion パラメーターが指定されている場合、指定したバージョンを完全に一致するモジュールのバージョンは検索 RoleCapability だけを返します。
- モジュールのメタデータにフィルターされます検索 RoleCapability タグ パラメーターを使用。
- リポジトリに固有の検索の言語にフィルターされます。 検索 RoleCapability フィルター パラメーターを使用します。
- 検索 RoleCapability は、登録されているリポジトリのほとんどまたはすべてのモジュールにフィルター処理できます。

## コマンドレット構文
```powershell
Get-Command -Name Find-RoleCapability -Module PowerShellGet -Syntax
```

## コマンドレットのオンライン ヘルプ リファレンス

[検索 RoleCapability](http://go.microsoft.com/fwlink/?LinkId=718029)

## コマンド例
```powershell

# Find a specific role capability
Find-RoleCapability -Name Maintenance

Name                                Version    ModuleName                          Repository
----                                -------    ----------                          ----------
Maintenance                         1.5.0      RoleCapModule                       PrivatePSGallery

# Find multiple role capabilities
Find-RoleCapability -Name MyJeaRole, Maintenance

# Find all available role capabilities from all registered repositories
Find-RoleCapability

# Find available role capabilities from few registered repositories
Find-RoleCapability -Repository PSGallery,PrivatePSGallery

# Find all role capabilities in a specified repository
Find-RoleCapability -Repository PSGallery

# Find a role capability defined in a specific module
Find-RoleCapability -Name Maintenance -ModuleName RoleCapModule

# Find role capabilities from all versions of a module
Find-RoleCapability -ModuleName RoleCapModule -AllVersions

# Find role capabilities with module name and MinimumVersion.
Find-RoleCapability -ModuleName RoleCapModule -MinimumVersion 1.1

# Find role capabilities with module name and exact version
Find-RoleCapability -ModuleName RoleCapModule -RequiredVersion 1.4.0

# Find role capabilities defined modules with -Filter based search. -Filter searches in description and module names
Find-RoleCapability -Filter Cookbook
Find-RoleCapability -Filter RBAC

# Find all role capabilities with tags Azure or DSC in module metadata
Find-RoleCapability -Tag Azure, DSC

```

<!--HONumber=Oct16_HO1-->


