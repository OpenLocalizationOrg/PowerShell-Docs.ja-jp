#C で DSC リソースの作成

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

通常、Windows PowerShell 必要な状態 Configuration (DSC) のカスタム リソースは、PowerShell スクリプトに実装されます。 ただし、c# でのコマンドレットを作成することによって、DSC カスタム リソースの機能を実装することもできます。 C# でのコマンドレットの作成の概要については、次を参照してください。 [Windows PowerShell コマンドレットの書き込み](https://technet.microsoft.com/en-us/library/dd878294.aspx)です。

コマンドレットと c# では、リソースを実装することとは別の MOF のスキーマの作成、フォルダー構造を作成する、インポート、およびカスタム DSC リソースを使用して、プロセスは、同じです」の説明に従って [MOF を持つカスタム DSC リソースを記述する](authoringResourceMOF.md)です。

##コマンドレット ベースのリソースを作成します。

この例では、テキスト ファイルとその内容を管理する単純なリソースを実装します。

###MOF のスキーマを作成します。

MOF のリソース定義を次に示します。

```
[ClassVersion("1.0.0"), FriendlyName("xDemoFile")]
class MSFT_XDemoFile : OMI_BaseResource
{
                [Key, Description("path")] String Path;
                [Write, Description("Should the file be present"), ValueMap{"Present","Absent"}, Values{"Present","Absent"}] String Ensure;
                [Write, Description("Contentof file.")] String Content;                   
};
```

###Visual Studio プロジェクトの設定

####コマンドレットのプロジェクトの設定

1. Visual Studio を開きます。
1. C# プロジェクトを作成し、名前を指定します。
1. 選択 **クラス ライブラリ** 使用できるプロジェクト テンプレートからです。
1. クリックして **Ok**です。
1. アセンブリ System.Automation.Management.dll への参照をプロジェクトに追加します。
1. リソース名と一致するアセンブリの名前を変更します。 アセンブリの名前を持つ必要がありますここでは、 **MSFT_XDemoFile**です。

###コマンドレットのコードの記述

次の c# コードを実装する、 **Get TargetResource**, 、**セット TargetResource**, 、および **テスト TargetResource** コマンドレットです。

```C#
Get-TargetResource
       [OutputType(typeof(System.Collections.Hashtable))]
       [Cmdlet(VerbsCommon.Get, "TargetResource")]
       public class GetTargetResource : PSCmdlet
       {
              [Parameter(Mandatory = true)]
              public string Path { get; set; }

///<summary>
/// Implement the logic to write the current state of the resource as a 
/// Hash table with keys being the resource properties 
/// and the Values are the corresponding current values on the target machine.

///</summary>
              protected override void ProcessRecord()
              {
// Download the zip file at the end of this blog to see sample implementation.
 }

Set-TargetResouce
         [OutputType(typeof(void))]
    [Cmdlet(VerbsCommon.Set, "TargetResource")]
    public class SetTargetResource : PSCmdlet
    {
        privatestring _ensure;
        privatestring _content;

[Parameter(Mandatory = true)]
        public string Path { get; set; }

[Parameter(Mandatory = false)]      
       [ValidateSet("Present", "Absent", IgnoreCase = true)]
       public string Ensure {
            get
            {
                // set the default to present.
               return (this._ensure ?? "Present");
            }
            set
            {
                this._ensure = value;
            }
           } 
            public string Content {
            get { return (string.IsNullOrEmpty(this._content) ? "" : this._content); }
            set { this._content = value; }
        }

///<summary>
        /// Implement the logic to set the state of the machine to the desired state.
        ///</summary>
        protected override void ProcessRecord()
        {
//Implement the set method of the resource 
/* Uncomment this section if your resource needs a machine reboot.
PSVariable DscMachineStatus = new PSVariable("DSCMachineStatus", 1, ScopedItemOptions.AllScope);
                this.SessionState.PSVariable.Set(DscMachineStatus);
*/     
  }
    }
Test-TargetResource    
       [Cmdlet("Test", "TargetResource")]
    [OutputType(typeof(Boolean))]
    public class TestTargetResource : PSCmdlet
    {   

        private string _ensure;
        private string _content;

        [Parameter(Mandatory = true)]
        public string Path { get; set; }

        [Parameter(Mandatory = false)]
        [ValidateSet("Present", "Absent", IgnoreCase = true)]
        public string Ensure
        {
            get
            {
                // set the default to present.
                return (this._ensure ?? "Present");
            }
            set
            {
                this._ensure = value;
            }
        }

        [Parameter(Mandatory = false)]
        public string Content
        {
            get { return (string.IsNullOrEmpty(this._content) ? "“:this._content);}
            set { this._content = value; }
        }

///<summary>
/// Write a Boolean value which indicates whether the current machine is in    
/// desired state or not.
        ///</summary>
        protected override void ProcessRecord()
        {
                // Implement the test method of the resource.
        }
}
```

###リソースを展開します。

スクリプト ベースのリソースと同様のファイル構造では、コンパイル済み dll ファイルを保存する必要があります。 このリソースのフォルダー構造は、次のとおりです。

```
$env: psmodulepath (folder)
    |- MyDscResources (folder)
        |- DSCResources (folder)
            |- MSFT_XDemoFile (folder)
                |- MSFT_XDemoFile.psd1 (file, optional)
                |- MSFT_XDemoFile.dll (file, required)
                |- MSFT_XDemoFile.schema.mof (file, required)
```

###関連項目

####概念

[MOF を持つカスタム DSC リソースの作成](authoringResourceMOF.md)
####その他のリソース

[Windows PowerShell コマンドレットを作成します。](https://msdn.microsoft.com/en-us/library/dd878294.aspx)



