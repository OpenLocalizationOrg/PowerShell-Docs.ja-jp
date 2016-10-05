# Find-DscResource

モジュールの DSC リソースを検索します。

## 説明

検索 DscResource コマンドレットを検索 [Desired State Configuration (DSC)](https://msdn.microsoft.com/en-us/PowerShell/dsc/overview) 登録されているリポジトリから指定した条件に一致するモジュールに含まれるリソースです。
このコマンドレットが検出された各モジュールは、検索 DscResource は、このコマンドレットを返すリソースを含むモジュールをインストールするインストール モジュールにパイプできる PSGetDscResourceInfo オブジェクトを返します。

DSC は、ソフトウェア サービスの構成データを展開および管理し、これらのサービスの実行環境を管理できる、Windows PowerShell の新しい管理プラットフォームです。

Desired State Configuration (DSC) リソースは、DSC 構成の構成要素を提供します。 リソースは、構成 (スキーマ) 可能なプロパティを公開し、また、指定されたことを実現するためにローカル構成マネージャー (LCM) が呼び出す PowerShell スクリプト関数が含まれています。

リソースでは、ファイルのように汎用的なものをモデル化したり、IIS サーバー設定のように具体的なものをモデル化したりできます。 リソースなどのグループは、DSC モジュールに結合されます。DSC モジュールでは、リソースの使用目的を識別するためのメタデータが含まれている移植可能な構造にすべての必須ファイルが編成されます。

- 検索 DscResource がバージョン パラメーターを使用してフィルター処理できます。 MinimumVersion RequiredVersion、AllVersions です。
  - これらのパラメーターでは、相互に排他的です。
  - これらのバージョン パラメーターは、任意のワイルドカードを含まない 1 つのモジュール名のみで許可されます。
  - RequiredVersion パラメーターが指定されていない場合、検索 DscResource は最低限のバージョンが指定されていない場合、同じかまたは指定された最小のバージョンや、モジュールの最新のバージョンよりも大きいは、モジュールの最新バージョンを返します。
  - RequiredVersion パラメーターが指定されている場合、指定したバージョンを完全に一致するモジュールのバージョンは検索 DscResource だけを返します。
- モジュールのメタデータにフィルターされます検索 DscResource タグ パラメーターを使用。
- リポジトリに固有の検索の言語にフィルターされます。 検索 DscResource フィルター パラメーターを使用します。
- 検索 DscResource は、登録されているリポジトリのほとんどまたはすべてのモジュールにフィルター処理できます。

## コマンドレット構文
```powershell
Get-Command -Name Find-DscResource -Module PowerShellGet -Syntax
```

## コマンドレットのオンライン ヘルプ リファレンス

[検索 DscResource](http://go.microsoft.com/fwlink/?LinkId=517196)

## コマンド例
```powershell

# Find a specific DSC Resource
Find-DscResource -Name xIisFeatureDelegation

Name                                Version    ModuleName                          Repository
----                                -------    ----------                          ----------
xIisFeatureDelegation               1.10.0.0   xWebAdministration                  PSGallery

# Find all available DSC Resources from all registered repositories
Find-DscResource

# Find a DSC resource by name
Find-DscResource -Name xWebsite

# Find multiple DSC Resources
Find-DscResource -Name xIisHandler, xFirewall

# Find all DSC resources contained within a specific module
Find-DscResource -ModuleName xNetworking
Find-DscResource -ModuleName xWebAdministration

# Find all DSC resources in modules with DSCResourceKit or DesiredStateConfiguration
Find-DscResource -Tag DesiredStateConfiguration, DSCResourceKit

# Find available DSC Resources from few registered repositories
Find-DscResource -Repository PSGallery,PrivatePSGallery

# Find all DSC Resources in a specified repository
Find-DscResource -Repository PSGallery

# Find DSC Resources from all versions of a module
Find-DscResource -ModuleName xNetworking -AllVersions

# Find DSC Resources with module name and MinimumVersion.
Find-DscResource -ModuleName xNetworking -MinimumVersion 1.1

# Find DSC Resources with module name and exact version
Find-DscResource -ModuleName xNetworking -RequiredVersion 2.1.1

# Find DSC Resources defined modules with -Filter based search. -Filter searches in description and module names
Find-DscResource -Filter Domain

# Find all DSC Resources with tags Azure or DSC in module metadata
Find-DscResource -Tag Azure, DSC

```


<!--HONumber=Oct16_HO1-->


