#複合リソース: リソースとして DSC 構成を使用します。

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

実際の状況では、構成は長くて複雑な場合は、多くのさまざまなリソースを呼び出すと、多くの異なるプロパティの設定になります。 このような複雑さに対処するには、その他の構成のリソースとして Windows PowerShell 必要な状態 Configuration (DSC) の構成を使用できます。 我々 はこれを複合リソースです。 複合リソースは、パラメーターを受け取る DSC 構成です。 構成のパラメーターは、リソースのプロパティとして機能します。 ファイルと構成を保存、* *。 schema.psm1** の拡張機能は、および標準的な DSC リソースで MOF のスキーマとリソースの両方の場所がスクリプトでは (DSC リソースに関する詳細については、次を参照してください。 [Windows PowerShell の必要な状態構成リソース](resources.md)です。

##複合のリソースの作成

この例では、多数の仮想マシンを構成する既存のリソースを起動するための構成を作成します。 構成ブロックで設定する値を指定するには、代わりには、構成は、さまざまな構成ブロックでために使用されるパラメータを受け取る。

```powershell
Configuration xVirtualMachine
{
param
(
# Name of VMs
[Parameter(Mandatory)]
[ValidateNotNullOrEmpty()]
[String[]]$VMName,

# Name of Switch to create
[Parameter(Mandatory)]
[ValidateNotNullOrEmpty()]
[String]$SwitchName,

# Type of Switch to create
[Parameter(Mandatory)]
[ValidateNotNullOrEmpty()]
[String]$SwitchType,

# Source Path for VHD
[Parameter(Mandatory)]
[ValidateNotNullOrEmpty()]
[String]$VhdParentPath,

# Destination path for diff VHD
[Parameter(Mandatory)]
[ValidateNotNullOrEmpty()]
[String]$VHDPath,

# Startup Memory for VM
[Parameter(Mandatory)]
[ValidateNotNullOrEmpty()]
[String]$VMStartupMemory,

# State of the VM
[Parameter(Mandatory)]
[ValidateNotNullOrEmpty()]
[String]$VMState
)

# Import the module that defines custom resources
Import-DscResource -Module xComputerManagement,xHyper-V

# Install the HyperV role 
WindowsFeature HyperV
{
    Ensure = "Present"
    Name = "Hyper-V" 
}

# Create the virtual switch 
xVMSwitch $switchName
{
    Ensure = "Present"
    Name = $switchName
    Type = $SwitchType
    DependsOn = "[WindowsFeature]HyperV"
}

# Check for Parent VHD file
File ParentVHDFile
{
    Ensure = "Present"
    DestinationPath = $VhdParentPath
    Type = "File"
    DependsOn = "[WindowsFeature]HyperV"
}

# Check the destination VHD folder
File VHDFolder
{
    Ensure = "Present"
    DestinationPath = $VHDPath
    Type = "Directory"
    DependsOn = "[File]ParentVHDFile"
}

 # Creae VM specific diff VHD
foreach($Name in $VMName)
{
    xVHD "VhD$Name"
    {
        Ensure = "Present"
        Name = $Name
        Path = $VhDPath
        ParentPath = $VhdParentPath
        DependsOn = @("[WindowsFeature]HyperV",
                      "[File]VHDFolder")
    }
}

# Create VM using the above VHD
foreach($Name in $VMName)
{
    xVMHyperV "VMachine$Name"
    {
        Ensure = "Present"
        Name = $Name
        VhDPath = (Join-Path -Path $VhDPath -ChildPath $Name)
        SwitchName = $SwitchName
        StartupMemory = $VMStartupMemory
        State = $VMState
        MACAddress = $MACAddress 
        WaitForIP = $true
        DependsOn = @("[WindowsFeature]HyperV",
                      "[xVHD]Vhd$Name")
    }
} 
}
```

###複合リソースとして構成を保存します。

DSC リソースとしてには、パラメーター化された構成を使用するには、いずれかの他の MOF ベース リソース、そのようなディレクトリ構造に保存し、名前を使用して、 **. schema.psm1** 拡張機能です。 この例では、名前をファイル **xVirtualMachine.schema.psm1**です。 という名前のマニフェストを作成する必要があります **xVirtualMachine.psd1** を次の行が含まれています。 他に、これは **MyDscResources.psd1**, 、モジュールのマニフェストを下にあるすべてのリソースを **MyDscResources** フォルダーです。

```powershell
RootModule = 'xVirtualMachine.schema.psm1'
```

完了したら、ときに、フォルダー構造は次のようにする必要があります。

```
$env: psmodulepath
    |- MyDscResources 
           MyDscResources.psd1
        |- DSC Resources 
            |- xVirtualMachine
                |- xVirtualMachine.psd1 
                |- xVirtualMachine.schema.psm1
```

Get DscResource コマンドレットを使用して、リソースが探索可能なおよびそのプロパティがそのいずれかのコマンドレットによって、またはを使用して探索可能な **Ctrl キーを押しながら Space キー** Windows PowerShell ISE でオート コンプリートします。

##複合のリソースを使用します。

次に複合のリソースを呼び出すの構成を作成します。 この構成は、仮想マシンを作成する xVirtualMachine リソースを呼び出すしを呼び出して、 **xComputer** リソース名を変更します。

```powershell
configuration RenameVM
{
Import-DscResource -Module TestCompositeResource

Node localhost
{
    xVirtualMachine VM
    {
        VMName = "Test"
        SwitchName = "Internal"
        SwitchType = "Internal"
        VhdParentPath = "C:\Demo\Vhd\RTM.vhd"
        VHDPath = "C:\Demo\Vhd"
        VMStartupMemory = 1024MB
        VMState = "Running"
    }
    }
   Node "192.168.10.1"
   {   
    xComputer Name
    {
        Name = "SQL01"
        DomainName = "fourthcoffee.com" 
    }                                                                                                                                                                                                                                                               
}
} 
```

##関連項目

###概念

* [MOF を持つカスタム DSC リソースの作成](authoringResourceMOF.md)
* [Windows PowerShell の必要な状態構成を使ってみる](overview.md)



