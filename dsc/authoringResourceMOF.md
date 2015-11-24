#MOF を持つカスタム DSC リソースの作成

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

このトピックでは、MOF ファイルでは、Windows PowerShell 必要な状態 Configuration (DSC) のカスタム リソースのスキーマを定義し、Windows PowerShell スクリプト ファイルにリソースを実装おはします。 このカスタムのリソースでは、作成、および web サイトを維持するためです。

##MOF のスキーマの作成

スキーマでは、DSC 構成のスクリプトによって構成可能なリソースのプロパティを定義します。

###MOF リソース用のフォルダー構造

MOF のスキーマを持つ DSC カスタム リソースを実装するのには、次のフォルダー構造を作成します。 ファイルのデモで MOF のスキーマが定義されている_IISWebsite.schema.mof、およびリソースのスクリプトがデモで定義されている_IISWebsite.ps1 です。 必要に応じて、モジュール マニフェスト (psd1) ファイルを作成することができます。

```
$env: psmodulepath (folder)
    |- MyDscResources (folder)
        |- DSCResources (folder)
            |- Demo_IISWebsite (folder)
                |- Demo_IISWebsite.psd1 (file, optional)
                |- Demo_IISWebsite.psm1 (file, required)
                |- Demo_IISWebsite.schema.mof (file, required)
```

DSCResources を最上位のフォルダーの下にあるという名前のフォルダーを作成する必要があると、各リソースのフォルダーは、リソースと同じ名前である必要がありますに注意してください。

###MOF ファイルの内容

カスタム web サイトのリソースに対して使用できる MOF ファイルの例を次に示します。 この例に従って操作、このスキーマをファイルに保存し、ファイルを呼び出す *Demo_IISWebsite.schema.mof*です。

```
[ClassVersion("1.0.0"), FriendlyName("Website")] 
class Demo_IISWebsite : OMI_BaseResource
{
  [Key] string Name;
  [Required] string PhysicalPath;
  [write,ValueMap{"Present", "Absent"},Values{"Present", "Absent"}] string Ensure;
  [write,ValueMap{"Started","Stopped"},Values{"Started", "Stopped"}] string State;
  [write] string Protocol[];
  [write] string BindingInfo[];
  [write] string ApplicationPool;
  [read] string ID;
};
```

前のコードについては、次に注意してください。

* `FriendlyName` DSC 構成スクリプトでこのカスタムのリソースを参照する際の名前を定義します。 この例で `web サイト` はフレンドリ名に相当 `アーカイブ` のアーカイブの組み込みのリソース。
* クラス定義から、カスタムのリソースが派生する必要があります `OMI_BaseResource`です。
* 型修飾子、 `[キー]`, のプロパティでは、このプロパティのリソースのインスタンスを一意に識別することを示します。 A `[キー]` プロパティも必要です。
* `[必須]` 修飾子は、プロパティが必要であることを示します (このリソースを使用するすべての構成スクリプトの値を指定する必要があります)。
* `[書き込み]` 修飾子では、このプロパティを構成スクリプトでカスタムのリソースを使用する場合は省略可能なことを示します。  `[読み取り]` 修飾子は、プロパティは構成では、設定することはできませんし、レポート目的でのみことを示します。
* `値` で定義された値の一覧のプロパティに割り当てることができる値を制限して `ValueMap`です。 詳細については、次を参照してください。 [ValueMap と値の修飾子を組み合わせて](https://msdn.microsoft.com/library/windows/desktop/aa393965.aspx)です。
* 呼ばれるプロパティを含む `を確認してください` DSC の組み込みのリソースとの一貫性のあるスタイルを維持する方法として、リソースにはお勧めします。
* 次のように、カスタムのリソースのスキーマ ファイルの名前を付けます: `classname.schema.mof`, ここで、 `classname` に続く識別子を指定します、 `クラス` 、スキーマ定義内のキーワードです。

###リソースのスクリプトの記述

リソースのスクリプトでは、リソースのロジックを実装します。この章では、呼び出された 3 つの関数を含める必要があります **Get TargetResource**, 、**セット TargetResource**, 、および **テスト TargetResource**です。3 つの関数には、一連のプロパティは、リソースの作成した MOF スキーマで定義されているのと同じであるパラメーターのセットを考慮する必要があります。このドキュメントでは、このプロパティのセットと呼びます [リソースのプロパティ]これら 3 つの関数で、ファイルと呼ばれるストア <ResourceName>.psm1 します。次の例では、関数は Demo_IISWebsite.psm1 をという名前のファイルに格納されます。

> **注**: と、リソースで、同じ構成スクリプトを複数回実行し、エラーは発生しませんされ、リソースが、スクリプトを 1 回実行すると同じ状態に保持します。 これを実現することを確認、 **Get TargetResource** と **テスト TargetResource** 変更せずに、リソース、関数のままにし、その呼び出し、 **セット TargetResource** 1 回で同じパラメーターを持つ一連の値は常に、1 回の呼び出しに相当するよりも多くの機能です。

**Get TargetResource** 実装の関数を指定したリソースのインスタンスの状態を確認する、パラメーターとして提供される主要なリソースのプロパティ値を使用します。 この関数では、キーと、対応する値としてこれらのプロパティの実際の値として、すべてのリソース プロパティを表示するハッシュ テーブルを返す必要があります。 次のコードでは、例を示します。

```powershell
# DSC uses the Get-TargetResource function to fetch the status of the resource instance specified in the parameters for the target machine
function Get-TargetResource 
{
    param 
    (       
        [ValidateSet("Present", "Absent")]
        [string]$Ensure = "Present",

        [Parameter(Mandatory)]
        [ValidateNotNullOrEmpty()]
        [string]$Name,

        [Parameter(Mandatory)]
        [ValidateNotNullOrEmpty()]
        [string]$PhysicalPath,

        [ValidateSet("Started", "Stopped")]
        [string]$State = "Started",

        [string]$ApplicationPool,

        [string[]]$BindingInfo,

        [string[]]$Protocol
    )

        $getTargetResourceResult = $null;

        <# Insert logic that uses the mandatory parameter values to get the website and assign it to a variable called $Website #>
        <# Set $ensureResult to "Present" if the requested website exists and to "Absent" otherwise #>

        # Add all Website properties to the hash table
        # This simple example assumes that $Website is not null
        $getTargetResourceResult = @{
                                      Name = $Website.Name; 
                                        Ensure = $ensureResult;
                                        PhysicalPath = $Website.physicalPath;
                                        State = $Website.state;
                                        ID = $Website.id;
                                        ApplicationPool = $Website.applicationPool;
                                        Protocol = $Website.bindings.Collection.protocol;
                                        Binding = $Website.bindings.Collection.bindingInformation;
                                    }

        $getTargetResourceResult;
}
```

構成のスクリプトでは、リソースのプロパティで指定されている値によって、 **セット TargetResource** 、次のいずれかを実行する必要があります。

* 新しい web サイトを作成します。
* 既存の web サイトを更新します。
* 既存の web サイトを削除します。

次の例では、これを示しています。

```powershell
# The Set-TargetResource function is used to create, delete or configure a website on the target machine. 
function Set-TargetResource 
{
    [CmdletBinding(SupportsShouldProcess=$true)]
    param 
    (       
        [ValidateSet("Present", "Absent")]
        [string]$Ensure = "Present",

        [Parameter(Mandatory)]
        [ValidateNotNullOrEmpty()]
        [string]$Name,

        [Parameter(Mandatory)]
        [ValidateNotNullOrEmpty()]
        [string]$PhysicalPath,

        [ValidateSet("Started", "Stopped")]
        [string]$State = "Started",

        [string]$ApplicationPool,

        [string[]]$BindingInfo,

        [string[]]$Protocol
    )

    <# If Ensure is set to "Present" and the website specified in the mandatory input parameters does not exist, then create it using the specified parameter values #>
    <# Else, if Ensure is set to "Present" and the website does exist, then update its properties to match the values provided in the non-mandatory parameter values #>
    <# Else, if Ensure is set to "Absent" and the website does not exist, then do nothing #>
    <# Else, if Ensure is set to "Absent" and the website does exist, then delete the website #>
}
```

最後に、 **テスト TargetResource** 関数として設定する同じパラメーターを受け取る必要があります **Get TargetResource** と **セット TargetResource**です。 実装で **テスト TargetResource**, 、キーのパラメーターで指定されているリソースのインスタンスの状態を確認します。 リソースのインスタンスの実際の状態で、パラメーター セットで指定された値が一致しない場合は、返す **$false**です。 それ以外の場合、返す **$true**です。

次のコードを実装して、 **テスト TargetResource** 関数。

```powershell
function Test-TargetResource
{
[CmdletBinding()]
[OutputType([System.Boolean])]
param
(
[ValidateSet("Present","Absent")]
[System.String]
$Ensure,

[parameter(Mandatory = $true)]
[System.String]
$Name,

[parameter(Mandatory = $true)]
[System.String]
$PhysicalPath,

[ValidateSet("Started","Stopped")]
[System.String]
$State,

[System.String[]]
$Protocol,

[System.String[]]
$BindingData,

[System.String]
$ApplicationPool
)

#Write-Verbose "Use this cmdlet to deliver information about command processing."

#Write-Debug "Use this cmdlet to write debug information while troubleshooting."


#Include logic to 
$result = [System.Boolean]
#Add logic to test whether the website is present and its status mathes the supplied parameter values. If it does, return true. If it does not, return false.
$result 
}
```

**注**。 簡単にデバッグでは、次のように使用します。、 **書き込み-詳細** 前の 3 つの関数の実装でのコマンドレットです。 このコマンドレットでは、詳細メッセージのストリームにテキストを書き込みます。 既定では、詳細メッセージのストリームが表示されないの値を変更することで、これを表示することが、 **$VerbosePreference** 変数またはを使用して、 **Verbose** DSC コマンドレットのパラメーター = new します。

###モジュール マニフェストを作成します。

最後に、使用して、 **New-modulemanifest** を定義するコマンドレット、 <ResourceName>、カスタムのリソース モジュール .psd1 ファイルです。このコマンドレットを呼び出すときは、前のセクションで説明されているスクリプト モジュール (.psm1) ファイルを参照します。含める **Get TargetResource**, 、**セット TargetResource**, と **テスト TargetResource** をエクスポートする関数の一覧にします。マニフェスト ファイルの例を次に示します。

```powershell
# Module manifest for module 'Demo.IIS.Website'
#
# Generated on: 1/10/2013
#

@{

# Script module or binary module file associated with this manifest.
# RootModule = ''

# Version number of this module.
ModuleVersion = '1.0'

# ID used to uniquely identify this module
GUID = '6AB5ED33-E923-41d8-A3A4-5ADDA2B301DE'

# Author of this module
Author = 'Contoso'

# Company or vendor of this module
CompanyName = 'Contoso'

# Copyright statement for this module
Copyright = 'Contoso. All rights reserved.'

# Description of the functionality provided by this module
Description = 'This Module is used to support the creation and configuration of IIS Websites through Get, Set and Test API on the DSC managed nodes.'

# Minimum version of the Windows PowerShell engine required by this module
PowerShellVersion = '4.0'

# Minimum version of the common language runtime (CLR) required by this module
CLRVersion = '4.0'

# Modules that must be imported into the global environment prior to importing this module
RequiredModules = @("WebAdministration")

# Modules to import as nested modules of the module specified in RootModule/ModuleToProcess
NestedModules = @("Demo_IISWebsite.psm1")

# Functions to export from this module
FunctionsToExport = @("Get-TargetResource", "Set-TargetResource", "Test-TargetResource")

# Cmdlets to export from this module
#CmdletsToExport = '*'

# HelpInfo URI of this module
# HelpInfoURI = ''
}
```



