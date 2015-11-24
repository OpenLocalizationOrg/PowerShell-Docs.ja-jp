#DSC の構成

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

DSC 構成は、特殊な種類の関数を定義する PowerShell スクリプトです。 
構成を定義するには、PowerShell のキーワードを使用する __構成__です。

```powershell
Configuration MyDscConfiguration {

    Node “TEST-PC1” {
        WindowsFeature MyFeatureInstance {
            Ensure = “Present”
            Name =  “RSAT”
        }
        WindowsFeature My2ndFeatureInstance {
            Ensure = “Present”
            Name = “Bitlocker”
        }
    }
}
```

スクリプトを .ps1 ファイルとして保存します。

##構成の構文

構成スクリプトは、次の部分で構成されます。

- **構成** ブロックします。 これは、最も外側にあるスクリプト ブロックです。 使用して定義する、 **構成** キーワードと名前を指定します。 この場合は、構成の名前は"MyDscConfiguration"です。
- 1 つまたは複数 **ノード** ブロックします。 これらは、構成しているノード (コンピューターまたは Vm) を定義します。 上記の構成がある **ノード** "テスト PC1"という名前のコンピューターを対象とするブロック。
- 1 つまたは複数のリソースのブロックします。 これは、構成がそれを構成しているリソースのプロパティを設定します。 この場合は、"WindowsFeature"のリソースを呼び出すの 2 つのリソース ブロックがあります。

内で、 **構成** ブロックを通常どおり PoweShell 関数では何も行うことができます。 たとえば、前の例では、構成では、対象のコンピュータの名前をハードコーディングする必要がある場合する可能性がありますパラメーターを追加、ノード名の。

```powershell
Configuration MyDscConfiguration {

    param(
        [string[]]$computerName="localhost"
    )
    Node $computerName {
        WindowsFeature MyFeatureInstance {
            Ensure = “Present”
            Name =  “RSAT”
        }
        WindowsFeature My2ndFeatureInstance {
            Ensure = “Present”
            Name = “Bitlocker”
        }
    }
}
```

この例で $computerName パラメーターとして渡すことによって、ノードの名前を指定するときにする [、構成をコンパイル] (#、構成のコンパイル)。 名前の既定値は"localhost"です。

##構成のコンパイル

構成を適用することができます、前に、MOF ドキュメントへのコンパイルする必要があります。 PowerShell 関数と同様に、構成を呼び出してこれを行います。
> __注:__ 、構成を呼び出すには、関数はでは、(その他の PowerShell 関数の場合は) あるグローバル スコープで必要があります。 この発生するか、「ドット ソース」、スクリプトを行うことができますか、f5 キーを使用するか、[構成のスクリプトを実行して、 __スクリプトの実行__ ISE でボタンをクリックします。 スクリプトをドット ソースにコマンドを実行します '。 .\myConfig.ps1` 場所 `myConfig.ps1' を構成を含むスクリプト ファイルの名前を指定します。

構成を呼び出すときに、次のことを作成します。

- 現在のディレクトリで、構成と同じ名前のフォルダーです。
- という名前のファイル _NodeName_で、新しいディレクトリの .mof、 _ノード名_ 構成のターゲット ノードの名前を指定します。 1 つ以上のノードがある場合は、MOF ファイルは、ノードごとに作成されます。

> __注__: MOF ファイルには、すべてのターゲット ノードの構成情報が含まれています。 このため、セキュリティで保護する重要な勧めします。 詳細については、次を参照してください。 [MOF ファイルをセキュリティで保護する](secureMOF.md)です。

最初の結果では、次のフォルダー構造上の構成をコンパイルするには。

```powershell
PS C:\users\default\Documents\DSC Configurations> . .\MyDscConfiguration.ps1
PS C:\users\default\Documents\DSC Configurations> MyDscConfiguration
    Directory: C:\users\default\Documents\DSC Configurations\MyDscConfiguration
Mode                LastWriteTime         Length Name                                                                                              
----                -------------         ------ ----                                                                                         
-a----       10/23/2015   4:32 PM           2842 TEST-PC1.mof
```

場合は、構成では、コンパイル時に指定する必要のある 2 番目の例のように、パラメーターを受け取ります。 方法になります。 次に示します。

```powershell
PS C:\users\default\Documents\DSC Configurations> . .\MyDscConfiguration.ps1
PS C:\users\default\Documents\DSC Configurations> MyDscConfiguration -computerName 'MyTestNode'
    Directory: C:\users\default\Documents\DSC Configurations\MyDscConfiguration
Mode                LastWriteTime         Length Name                                                                                              
----                -------------         ------ ----                                                                                         
-a----       10/23/2015   4:32 PM           2842 MyTestNode.mof
```

##DependsOn を使用します。

便利な DSC キーワードは __DependsOn__です。 通常 (ただし、必ずしも常にではありません)、DSC には、構成内で出現する順序内のリソースが適用されます。 ただし、 __DependsOn__ 指定リソース、その他のリソースに依存して、LCM により、これらのリソースのインスタンスが定義されている順序に関係なく、適切な順序で適用されます。 たとえば、構成のように指定のインスタンス、 __ユーザー__ リソースの存在に依存して、 __グループ__ インスタンス。

```powershell
Configuration DependsOnExample {
    Node Test-PC1 {
        Group GroupExample {
            Ensure = “Present”
            GroupName = “TestGroup”
        }
User UserExample {
Ensure = “Present”
FullName = “TestUser”
DependsOn = “GroupExample”
}
    }
}
```

##構成で新しいリソースを使用します。

前の例を実行した場合お気付きかもしれません明示的にインポートすることがなく、リソースを使用していたことを警告するでした。
現在のところ、DSC は、12 のリソースで、PSDesiredStateConfiguration モジュールの一部として出荷されます。 外部モジュールには、その他のリソースを配置する必要があります `$env:path: PSModulePath` 、LCM によって認識されるようにします。 新しいコマンドレットでは、 [Get DscResource](https://technet.microsoft.com/en-us/library/dn521625.aspx), 、どのようなリソースが、システムにインストールされていて、使用可能な LCM を判断するために使用できます。 
これらのモジュールに配置された後 `$env:path: PSModulePath` によって正しく認識される、 [Get DscResource](https://technet.microsoft.com/en-us/library/dn521625.aspx), が、構成内で読み込まれる必要があります。 __インポート DscResource__ 内でのみ認識できる動的キーワードには、 __構成__ (つまりではありませんコマンドレット) をブロックします。 __インポート DscResource__ 2 つのパラメーターをサポートしています。
* __ModuleName__ を使用することをお勧め __インポート DscResource__です。 (およびモジュール名の文字列の配列) にインポートするリソースを含むモジュールの名前を受け入れます。
* __名前__ をインポートするリソースの名前を指定します。 によって、"Name"として返されるフレンドリ名ではありません [Get DscResource](https://technet.microsoft.com/en-us/library/dn521625.aspx), 、クラス名を使用するは、リソースのスキーマを定義するが、(として返されます __ResourceType__ によって [Get DscResource](https://technet.microsoft.com/en-us/library/dn521625.aspx))。

##関連項目

* [Windows PowerShell の必要な状態の構成の概要](overview.md)
* [DSC リソース](resources.md)
* [ローカルの Configuration Manager の構成](metaconfig.md)



