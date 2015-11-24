#PowerShell のクラスを持つカスタム DSC リソースの作成

> 5.0 に適用されます。 Windows Windows PowerShell

Windows PowerShell 5.0 PowerShell クラスの概要については、クラスを作成することで DSC リソースをここで定義できます。 クラスでは、個別の MOF ファイルを作成する必要はありませんので、スキーマと、リソースの実装の両方を定義します。 クラス ベースのリソース用のフォルダー構造はシンプルですもあるため、 **DSCResources** フォルダーが必要ではありません。

クラスに基づく DSC リソースを使用して、スキーマはプロパティの種類を指定する属性を使用して変更できるクラスのプロパティとして定義されている. によって、リソースが実装される **Get()**, 、**Set()**, 、および **Test()** メソッド (に相当する、 **Get TargetResource**, 、**セット TargetResource**, 、および **テスト TargetResource** スクリプト リソース内の関数。

このトピックでは、という名前の単純なリソースを作成お **FileResource** 指定されたパス内のファイルを管理します。

DSC リソースに関する詳細については、次を参照してください [ビルド カスタム Windows PowerShell 必要な状態の構成に関するリソース](authoringResource.md)。

##クラス リソース用のフォルダー構造

PowerShell のクラスを使用して、DSC のカスタム リソースを実装するのには、次のフォルダー構造を作成します。 クラスが定義されている **MyDscResource.psm1** で、モジュール マニフェストが定義されていると **MyDscResource.psd1**です。

```
$env: psmodulepath (folder)
    |- MyDscResources (folder)
        |- MyDscResource.psm1 
           MyDscResource.psd1 
```

##クラスを作成します。

PowerShell のクラスを作成するのにには、クラスのキーワードを使用します。 クラスが DSC リソースであることを指定するには、使用、 **DscResource()** 属性です。 クラスの名前は、DSC リソースの名前です。

```powershell
[DscResource()]
class FileResource{
}
```

###プロパティを宣言します。

DSC リソースのスキーマは、クラスのプロパティとして定義されます。 3 つのプロパティには、次のように宣言します。

```powershell
[DscProperty(Key)]
[string]$Path


[DscProperty(Mandatory)]
[Ensure] $Ensure


[DscProperty(Mandatory)]
[string] $SourcePath

[DscProperty(NotConfigurable)]
[Nullable[datetime]] $CreationTime
```

属性によって、プロパティを変更することに注意してください。 Attribues は、次のように、MOF クラスで使用される同等の属性にマップします。

クラスの MOF の属性の説明のプロパティの属性
DscProperty(Key)
キー
プロパティが必要です。 プロパティは、キーです。 すべてのプロパティの値には、構成内のリソースのインスタンスを一意に識別するキーを組み合わせる必要がありますがマークされています。
DscProperty(Mandatory)
必要な
プロパティが必要です。
DscProperty(NotConfigurable)
読み取り
プロパティとは、読み取り専用です。 この属性でマークされたプロパティでは、構成では、設定することはできませんが、存在する場合に、Get() メソッドによって設定されます。
DscProperty()
書き込み
プロパティが、構成可能なには、必須ではありません。

**$Path** と **$SourcePath** プロパティは、両方の文字列。  **$CreationTime** は、 [DateTime](https://technet.microsoft.com/en-us/library/system.datetime.aspx) プロパティです。  **$Ensure** プロパティは、次のように定義されている列挙型の場合。

```powershell
enum Ensure 
{ 
    Absent 
    Present 
}
```

###メソッドの実装

**Get()**, 、**Set()**, 、および **Test()** メソッドに似ています。、 **Get TargetResource**, 、**セット TargetResource**, 、および **テスト TargetResource** スクリプト リソース内の関数。

このコードは、元のファイルをコピーするヘルパー関数である CopyFile() 関数も含まれています。 **$SourcePath** に **$Path**です。

```powershell
<#
        This method is equivalent of the Set-TargetResource script function.
        It sets the resource to the desired state.
    #>
    [void] Set()
    {        
        $fileExists = $this.TestFilePath($this.Path)
        if($this.ensure -eq [Ensure]::Present)
        {
            if(-not $fileExists)
            {
                $this.CopyFile()
            }
        }
        else
        {
            if($fileExists)
            {
                Write-Verbose -Message "Deleting the file $($this.Path)"
                Remove-Item -LiteralPath $this.Path -Force
            }
        }
    }

    <# 

        This method is equivalent of the Test-TargetResource script function. 
        It should return True or False, showing whether the resource
        is in a desired state.         
    #>
    [bool] Test()
    {
        $present = $this.TestFilePath($this.Path)

        if($this.Ensure -eq [Ensure]::Present)
        {
            return $present
        }
        else
        {
            return -not $present
        }
    }


    <#
        This method is equivalent of the Get-TargetResource script function.
        The implementation should use the keys to find appropriate resources.
        This method returns an instance of this class with the updated key
         properties.
    #>
    [FileResource] Get()
    {
        $present = $this.TestFilePath($this.Path)        

        if ($present)
        {
            $file = Get-ChildItem -LiteralPath $this.Path
            $this.CreationTime = $file.CreationTime
            $this.Ensure = [Ensure]::Present
        }
        else
        {
            $this.CreationTime = $null
            $this.Ensure = [Ensure]::Absent
        }        

        return $this
    }

    <#
        Helper method to check if the file exists and it is file
    #>
    [bool] TestFilePath([string] $location)
    {      
        $present = $true

        $item = Get-ChildItem -LiteralPath $location -ea Ignore
        if ($item -eq $null)
        {
            $present = $false            
        }
        elseif( $item.PSProvider.Name -ne "FileSystem")
        {
            throw "Path $($location) is not a file path."
        }
        elseif($item.PSIsContainer)
        {
            throw "Path $($location) is a directory path."
        }
        return $present
    }

    <#
        Helper method to copy file from source to path
    #>
    [void] CopyFile()
    { 
        if(-not $this.TestFilePath($this.SourcePath))
        {
            throw "SourcePath $($this.SourcePath) is not found."
        }

        [System.IO.FileInfo] $destFileInfo = new-object System.IO.FileInfo($this.Path)
        if (-not $destFileInfo.Directory.Exists)
        {
            Write-Verbose -Message "Creating directory $($destFileInfo.Directory.FullName)"

            #use CreateDirectory instead of New-Item to avoid code
            # to handle the non-terminating error
            [System.IO.Directory]::CreateDirectory($destFileInfo.Directory.FullName)
        }

        if(Test-Path -LiteralPath $this.Path -PathType Container)
        {
            throw "Path $($this.Path) is a directory path"
        }

        Write-Verbose -Message "Copying $($this.SourcePath) to $($this.Path)"

        #DSC engine catches and reports any error that occurs
        Copy-Item -LiteralPath $this.SourcePath -Destination $this.Path -Force
    }
}<# 
        This method replaces the Set-TargetResource DSC script function.
        It sets the resource to the desired state. 
    #>
    [void] Set() 
    {       
        $fileExists = Test-Path -path $this.Path -PathType Leaf 
        if($this.ensure -eq [Ensure]::Present)
        {
            if(-not $fileExists)
            {
                $this.CopyFile()            
            }
        }
        else
        {
            if($fileExists)         
            {
                Write-Verbose -Message "Deleting the file $this.Path"
                Remove-Item -LiteralPath $this.Path                     
            }
        } 
    } 


    <# 

        This method replaces the Test-TargetResource function. 
        It should return True or False, showing whether the resource
         is in a desired state.         
    #> 

    [bool] Test()
    {
        if(Test-Path -path $this.Path -PathType Container)
        {
            throw "Path '$this.Path' is a directory path."
        }

        $fileExists = Test-Path -path $this.Path -PathType Leaf

        if($this.ensure -eq [Ensure]::Present)
        {
            return $fileExists
        }

        return (-not $fileExists)
    }


    <# 
        This method replaces the Get-TargetResource function.         
        The implementation should use the keys to find appropriate resources. 
        This method returns an instance of this class with the updated key properties. 
    #> 

    [FileResource] Get() 
    {
        $file = Get-item $this.Path
        return $this
    }

    <#
        Helper method to copy file from source to path.
        Because this resource provider run under system,
        Only the Administrators and system have full
         access to the new created directory and file
    #>
    CopyFile()
    {
        if(Test-Path -path $this.SourcePath -PathType Container)
        {
            throw "SourcePath '$this.SourcePath' is a directory path"
        }

        if( -not (Test-Path -path $this.SourcePath -PathType Leaf))
        {
            throw "SourcePath '$this.SourcePath' is not found."
        }

        [System.IO.FileInfo] $destFileInfo = new-object System.IO.FileInfo($this.Path)
        if (-not $destFileInfo.Directory.Exists)
        {         
            Write-Verbose -Message "Creating directory $($destFileInfo.Directory.FullName)"

            #use CreateDirectory instead of New-Item to avoid lines
            # to handle the non-terminating error
            [System.IO.Directory]::CreateDirectory($destFileInfo.Directory.FullName)
        }

        if(Test-Path -path $this.Path -PathType Container)
        {
            throw "Path '$this.Path' is a directory path"
        }

        Write-Verbose -Message "Copying $this.SourcePath to $this.Path"

        #DSC engine catches and reports any error that occurs
        Copy-Item -Path $this.SourcePath -Destination $this.Path -Force
    } 
 <# 
        This method replaces the Set-TargetResource DSC script function.
        It sets the resource to the desired state. 
    #>
    [void] Set() 
    {       
        $fileExists = Test-Path -path $Path -PathType Leaf 
        if($ensure -eq [Ensure]::Present)
        {
            if(-not $fileExists)
            {
                $this.CopyFile()            
            }
        }
        else
        {
            if($fileExists)         
            {
                Write-Verbose -Message "Deleting the file $Path"
                Remove-Item -LiteralPath $Path                     
            }
        } 
    } 


    <# 

        This method replaces the Test-TargetResource function. 
        It should return True or False, showing whether the resource
         is in a desired state.         
    #> 

    [bool] Test()
    {
        if(Test-Path -path $Path -PathType Container)
        {
            throw "Path '$Path' is a directory path."
        }

        $fileExists = Test-Path -path $Path -PathType Leaf
if($ensure -eq [Ensure]::Present)
        {
            return $fileExists
        }

        return (-not $fileExists)
    }


    <# 
        This method replaces the Get-TargetResource function.         
        The implementation should use the keys to find appropriate resources. 
        This method returns an instance of this class with the updated key properties. 
    #> 

    [FileResource] Get() 
    {
        $file = Get-item $Path
        return $this
    }

    <#
        Helper method to copy file from source to path
        Because this resource provider run under system,
        Only the Administrators and system have full
         access to the new created directory and file
    #>
    CopyFile()
    {
        if(Test-Path -path $SourcePath -PathType Container)
        {
            throw "SourcePath '$SourcePath' is a directory path"
        }

        if( -not (Test-Path -path $SourcePath -PathType Leaf))
        {
            throw "SourcePath '$SourcePath' is not found."
        }

        [System.IO.FileInfo] $destFileInfo = new-object System.IO.FileInfo($Path)
        if (-not $destFileInfo.Directory.Exists)
        {         
            Write-Verbose -Message "Creating directory $($destFileInfo.Directory.FullName)"

            #use CreateDirectory instead of New-Item to avoid lines
            # to handle the non-terminating error
            [System.IO.Directory]::CreateDirectory($destFileInfo.Directory.FullName)
        }

        if(Test-Path -path $Path -PathType Container)
        {
            throw "Path '$Path' is a directory path"
        }

        Write-Verbose -Message "Copying $SourcePath to $Path"

        #DSC engine catches and reports any error that occurs
        Copy-Item -Path $SourcePath -Destination $Path -Force
    }
```

###完全なファイル

完全なクラス ファイルに依存します。

```powershell
enum Ensure
{
    Absent
    Present
}

<#
   This resource manages the file in a specific path.
   [DscResource()] indicates the class is a DSC resource
#>

[DscResource()]
class FileResource
{
    <# 
       This property is the fully qualified path to the file that is
       expected to be present or absent.

       The [DscProperty(Key)] attribute indicates the property is a
       key and its value uniquely identifies a resource instance.
       Defining this attribute also means the property is required
       and DSC will ensure a value is set before calling the resource.

       A DSC resource must define at least one key property.
    #>
    [DscProperty(Key)]
    [string]$Path

    <#
        This property indicates if the settings should be present or absent
        on the system. For present, the resource ensures the file pointed
        to by $Path exists. For absent, it ensures the file point to by
        $Path does not exist.

        The [DscProperty(Mandatory)] attribute indicates the property is
        required and DSC will guarantee it is set.

        If Mandatory is not specified or if it is defined as 
        Mandatory=$false, the value is not guaranteed to be set when DSC
        calls the resource.  This is appropriate for optional properties.
    #>
    [DscProperty(Mandatory)]
    [Ensure] $Ensure    

    <#
       This property defines the fully qualified path to a file that will
       be placed on the system if $Ensure = Present and $Path does not
        exist.

       NOTE: This property is required because [DscProperty(Mandatory)] is
        set.
    #>
    [DscProperty(Mandatory)]
    [string] $SourcePath

    <#
       This property reports the file's create timestamp.

       [DscProperty(NotConfigurable)] attribute indicates the property is
       not configurable in DSC configuration.  Properties marked this way
       are populated by the Get() method to report additional details
       about the resource when it is present.

    #>
    [DscProperty(NotConfigurable)]
    [Nullable[datetime]] $CreationTime

    <#
        This method is equivalent of the Set-TargetResource script function.
        It sets the resource to the desired state.
    #>
    [void] Set()
    {        
        $fileExists = $this.TestFilePath($this.Path)
        if($this.ensure -eq [Ensure]::Present)
        {
            if(-not $fileExists)
            {
                $this.CopyFile()
            }
        }
        else
        {
            if($fileExists)
            {
                Write-Verbose -Message "Deleting the file $($this.Path)"
                Remove-Item -LiteralPath $this.Path -Force
            }
        }
    }

    <# 

        This method is equivalent of the Test-TargetResource script function. 
        It should return True or False, showing whether the resource
        is in a desired state.         
    #>
    [bool] Test()
    {
        $present = $this.TestFilePath($this.Path)

        if($this.Ensure -eq [Ensure]::Present)
        {
            return $present
        }
        else
        {
            return -not $present
        }
    }


    <#
        This method is equivalent of the Get-TargetResource script function.
        The implementation should use the keys to find appropriate resources.
        This method returns an instance of this class with the updated key
         properties.
    #>
    [FileResource] Get()
    {
        $present = $this.TestFilePath($this.Path)        

        if ($present)
        {
            $file = Get-ChildItem -LiteralPath $this.Path
            $this.CreationTime = $file.CreationTime
            $this.Ensure = [Ensure]::Present
        }
        else
        {
            $this.CreationTime = $null
            $this.Ensure = [Ensure]::Absent
        }        

        return $this
    }

    <#
        Helper method to check if the file exists and it is file
    #>
    [bool] TestFilePath([string] $location)
    {      
        $present = $true

        $item = Get-ChildItem -LiteralPath $location -ea Ignore
        if ($item -eq $null)
        {
            $present = $false            
        }
        elseif( $item.PSProvider.Name -ne "FileSystem")
        {
            throw "Path $($location) is not a file path."
        }
        elseif($item.PSIsContainer)
        {
            throw "Path $($location) is a directory path."
        }
        return $present
    }

    <#
        Helper method to copy file from source to path
    #>
    [void] CopyFile()
    { 
        if(-not $this.TestFilePath($this.SourcePath))
        {
            throw "SourcePath $($this.SourcePath) is not found."
        }

        [System.IO.FileInfo] $destFileInfo = new-object System.IO.FileInfo($this.Path)
        if (-not $destFileInfo.Directory.Exists)
        {
            Write-Verbose -Message "Creating directory $($destFileInfo.Directory.FullName)"

            #use CreateDirectory instead of New-Item to avoid code
            # to handle the non-terminating error
            [System.IO.Directory]::CreateDirectory($destFileInfo.Directory.FullName)
        }

        if(Test-Path -LiteralPath $this.Path -PathType Container)
        {
            throw "Path $($this.Path) is a directory path"
        }

        Write-Verbose -Message "Copying $($this.SourcePath) to $($this.Path)"

        #DSC engine catches and reports any error that occurs
        Copy-Item -LiteralPath $this.SourcePath -Destination $this.Path -Force
    }
}# This module defines a class for a DSC "FileResource" provider. 


enum Ensure 
{ 
    Absent 
    Present 
} 


<# This resource manages the file in a specific path. 
[DscResource()] indicates the class is a DSC resource 
#> 

[DscResource()]
class FileResource{ 

    <# This is a key property 
       [DscResourceKey()] also means the property is required.
       It is guaranteed to be set, other properties may not
        be set if the configuration did not specify values.
    #> 
    [DscResourceKey()]
    [string]$Path

    <# 
       [DscResourceMandatory()] means the property is required.
       It is guaranteed to be set, other properties may not be set
        if the configuration did not specify values.
    #>
    [DscResourceMandatory()]
    [Ensure] $Ensure

    <# 
       [DscResourceMandatory()] means the property is required.
    #>
    [DscResourceMandatory()]
    [string] $SourcePath     

    [DscResource

    <# 
        This method replaces the Set-TargetResource DSC script function.
        It sets the resource to the desired state. 
    #>
    [void] Set() 
    {       
        $fileExists = Test-Path -path $this.Path -PathType Leaf 
        if($this.ensure -eq [Ensure]::Present)
        {
            if(-not $fileExists)
            {
                $this.CopyFile()            
            }
        }
        else
        {
            if($fileExists)         
            {
                Write-Verbose -Message "Deleting the file $this.Path"
                Remove-Item -LiteralPath $this.Path                     
            }
        } 
    } 


    <# 

        This method replaces the Test-TargetResource function. 
        It should return True or False, showing whether the resource
         is in a desired state.         
    #> 

    [bool] Test()
    {
        if(Test-Path -path $this.Path -PathType Container)
        {
            throw "Path '$this.Path' is a directory path."
        }

        $fileExists = Test-Path -path $this.Path -PathType Leaf

        if($this.ensure -eq [Ensure]::Present)
        {
            return $fileExists
        }

        return (-not $fileExists)
    }


    <# 
        This method replaces the Get-TargetResource function.         
        The implementation should use the keys to find appropriate resources. 
        This method returns an instance of this class with the updated key properties. 
    #> 

    [FileResource] Get() 
    {
        $file = Get-item $this.Path
        return $this
    }

    <#
        Helper method to copy file from source to path.
        Because this resource provider run under system,
        Only the Administrators and system have full
         access to the new created directory and file
    #>
    CopyFile()
    {
        if(Test-Path -path $this.SourcePath -PathType Container)
        {
            throw "SourcePath '$this.SourcePath' is a directory path"
        }

        if( -not (Test-Path -path $this.SourcePath -PathType Leaf))
        {
            throw "SourcePath '$this.SourcePath' is not found."
        }

        [System.IO.FileInfo] $destFileInfo = new-object System.IO.FileInfo($this.Path)
        if (-not $destFileInfo.Directory.Exists)
        {         
            Write-Verbose -Message "Creating directory $($destFileInfo.Directory.FullName)"

            #use CreateDirectory instead of New-Item to avoid lines
            # to handle the non-terminating error
            [System.IO.Directory]::CreateDirectory($destFileInfo.Directory.FullName)
        }

        if(Test-Path -path $this.Path -PathType Container)
        {
            throw "Path '$this.Path' is a directory path"
        }

        Write-Verbose -Message "Copying $this.SourcePath to $this.Path"

        #DSC engine catches and reports any error that occurs
        Copy-Item -Path $this.SourcePath -Destination $this.Path -Force
    }
} 
```

##マニフェストを作成します。

クラス ベースのリソースを DSC エンジンを使用できるようににする必要があります、 **DscResourcesToExport** を使用すると、リソースをエクスポートするのには、モジュール マニフェスト ファイル内のステートメント。 次のように、マニフェストにはなります。

```powershell
@{

# Script module or binary module file associated with this manifest.
RootModule = 'MyDscResource.psm1'

DscResourcesToExport = 'FileResource'

# Version number of this module.
ModuleVersion = '1.0'

# ID used to uniquely identify this module
GUID = '81624038-5e71-40f8-8905-b1a87afe22d7'

# Author of this module
Author = 'Microsoft Corporation'

# Company or vendor of this module
CompanyName = 'Microsoft Corporation'

# Copyright statement for this module
Copyright = '(c) 2014 Microsoft. All rights reserved.'

# Description of the functionality provided by this module
# Description = ''

# Minimum version of the Windows PowerShell engine required by this module
PowerShellVersion = '5.0'

# Name of the Windows PowerShell host required by this module
# PowerShellHostName = ''
} 
```

##リソースをテストします。

クラスとマニフェスト ファイルで既に説明したように、フォルダー構造を保存した後は、新しいリソースを使用する構成を作成できます。 DSC 構成を実行する方法については、次を参照してください。 [構成を制定](enactingConfigurations.md)です。 次の構成が確認するかどうかにファイル `c:\test\test.txt` が存在し、そうでない場合からのファイルをコピー `c:\test.txt` (を作成する必要があります `c:\test.txt` 構成を実行する前に)。

```powershell
Configuration Test
{
    Import-DSCResource -module MyDscResource
    FileResource file
    {
        Path = "C:\test\test.txt"
        SourcePath = "c:\test.txt"
        Ensure = "Present"
    } 
}
Test
Start-DscConfiguration -Wait -Force Test
```

##関連項目

###概念

[カスタムの Windows PowerShell が必要な状態の構成のリソースを作成します。](authoringResource.md)



