# PowerShell 5.0 の新しい言語機能 

PowerShell 5.0 では、Windows PowerShell に次の新しい言語要素が導入されています。

## class キーワード

**class** キーワードでは新しいクラスを定義します。 これは、真の .NET Framework 型です。 クラス メンバーはパブリックですが、モジュール スコープ内でのみパブリックです。
型名を文字列として参照することはできません (たとえば、`New-Object` は機能しません)。このリリースでは、クラスが定義されているスクリプトまたはモジュール ファイルの外で type リテラル (`[MyClass]` など) を使用することはできません。

```powershell
class MyClass
{
    ...
}
```

## enum キーワードおよび列挙

区切り文字として改行文字を使用する **enum** キーワードのサポートが追加されました。
現在の制限事項: 列挙子自体から列挙子を定義することはできませんが、次の例に示すように、別の enum から enum を初期化できます。
また、現在、基本型を指定することはできません。基本型は常に [int] です。

```powershell
enum Color2
{
    Yellow = [Color]::Blue
}
```

列挙子の値は、解析時定数である必要があります。呼び出されたコマンドの結果に設定することはできません。

```powershell
enum MyEnum
{
    Enum1
    Enum2
    Enum3 = 42
    Enum4 = [int]::MaxValue
}
```

次の例に示すように、enum では算術演算がサポートされます。

```powershell
enum SomeEnum { Max = 42 }
enum OtherEnum { Max = [SomeEnum]::Max + 1 }
```

## Import-DscResource

**Import-DscResource** は真の動的キーワードです。
PowerShell では、**DscResource** 属性を含むクラスを探して、指定されたモジュールのルート モジュールを解析します。

## ImplementingAssembly

新しいフィールド **ImplementingAssembly** が ModuleInfo に追加されました。 これは、スクリプトでクラスが定義されている場合はスクリプト モジュールに作成された動的アセンブリに設定されるか、またはバイナリ モジュールの読み込み済みアセンブリに設定されます。 ModuleType = Manifest の場合は設定されません。 

**ImplementingAssembly** フィールドのリフレクションでは、モジュール内のリソースが検出されます。 これは、PowerShell または他の管理言語で記述されたリソースを検出できることを意味します。

初期化子を含むフィールド:      

```powershell
[int] $i = 5
```

static がサポートされています。これは型の制約と同様に属性のように機能するため、任意の順序で指定できます。

```powershell
static [int] $count = 0
```

型は省略できます。

```powershell
$s = "hello"
```

すべてのメンバーはパブリックです。 

## コンストラクターとインスタンス化

Windows PowerShell クラスはコンストラクターを持つことができます。コンストラクターはクラスと同じ名前を持ちます。 コンストラクターはオーバーロードできます。 静的コンストラクターがサポートされています。 初期化式を持つプロパティは、コンストラクター内のコードを実行する前に初期化されます。 静的プロパティは、静的コンストラクターの本体の前に初期化され、インスタンスのプロパティは、非静的コンストラクターの本体の前に初期化されます。 現時点では、(C\# 構文 ": this()" のような) 別のコンストラクターからコンストラクターを呼び出す構文はありません。 対応策は、一般的な Init メソッドを定義することです。 

このリリースでクラスをインスタンス化する方法を次に示します。

既定のコンストラクターを使用したインスタンス化。 New-Object はこのリリースではサポートされていないことに注意してください。

```powershell
$a = [MyClass]::new()
```

パラメーターを持つコンストラクターの呼び出し

```powershell
$b = [MyClass]::new(42)
```

複数のパラメーターを持つコンストラクターに配列を渡す
```powershell
$c = [MyClass]::new(@(42,43,44), "Hello")
```

このリリースでは、New-Object は Windows PowerShell で定義されたクラスでは機能しません。 また、このリリースでは、型名は語彙的にのみ表示され、クラスを定義するモジュールまたはスクリプトの外部では表示されません。 関数は、Windows PowerShell で定義されているクラスのインスタンスを返すことができ、インスタンスは、モジュールまたはスクリプトの外部でも機能します。

`Get-Member -Static` ではコンストラクターが一覧表示されるため、他のメソッドと同様にオーバーロードを表示できます。 この構文のパフォーマンスは、New-Object より大幅に高速です。

次の例に示すように、**new** という名前の擬似静的メソッドは .NET 型で機能します。

```powershell
[hashtable]::new()
```

Get-Member を使用して、またはこの例で示すように、コンストラクターのオーバーロードを確認できます。

```powershell
PS> [hashtable]::new
OverloadDefinitions
-------------------
hashtable new()
hashtable new(int capacity)
hashtable new(int capacity, float loadFactor)
```

## メソッド

Windows PowerShell のクラス メソッドは、end ブロックのみを持つ ScriptBlock として実装されます。 すべてのメソッドはパブリックです。 **DoSomething** という名前のメソッドを定義する例を次に示します。

```powershell
class MyClass
{
    DoSomething($x)
    {
        $this._doSomething($x) # method syntax
    }
    private _doSomething($a) {}
}
```

メソッドの呼び出し:

```powershell
$b = [MyClass]::new()
$b.DoSomething(42) 
```

オーバーロードされたメソッド、つまり、既存のメソッドと同じ名前を持つが、指定した値によって区別されるメソッドもサポートされています。

## プロパティ 

すべてのプロパティはパブリックです。 プロパティには、改行文字かセミコロンが必要です。 オブジェクトの種類が指定されていない場合、プロパティの型はオブジェクトです。

検証属性または引数変換属性を使用するプロパティ (`[ValidateSet("aaa")]` など) は期待どおりに動作します。

## Hidden

新しいキーワード **Hidden** が追加されました。 **Hidden** は、プロパティとメソッド (コンストラクターを含む) に適用できます。

Hidden のメンバーはパブリックですが、-Force パラメーターを追加しない限り、Get-Member の出力には含められません。

Hidden のメンバーを定義するクラスで完了が発生しない限り、タブの完了時、または IntelliSense の使用時に Hidden のメンバーは含まれません。

C# コードで Windows PowerShell 内と同じセマンティクスを使用できるように、新しい属性 **System.Management.Automation.HiddenAttribute** が追加されました。

## 戻り値の型

戻り値の型は、コントラクトです。戻り値は、予期された型に変換されます。 戻り値の型が指定されていない場合、戻り値の型は void です。 オブジェクトのストリーミングはありません。オブジェクトが意図的または誤ってパイプラインに書き込まれることはありません。

## 属性

2 つの新しい属性 **DscResource** と **DscProperty** が追加されました。

## 変数の語彙的スコープ

今回のリリースで語彙的スコープが機能する方法の例を次に示します。

```powershell
$d = 42 # Script scope

function bar
{
    $d = 0 # Function scope
    [MyClass]::DoSomething()
}

class MyClass
{
    static [object] DoSomething()
    {
        return $d # error, not found dynamically
        return $script:d # no error
        $d = $script:d
        return $d # no error, found lexically
    }
}

$v = bar
$v -eq $d # true
```

## エンド ツー エンドの例

次の例では、HTML 動的スタイル シート言語 (DSL) を実装するいくつかの新しいカスタム クラスを作成します。 次に、モジュールのスコープの外部では型を使用できないため、この例では見出しスタイルやテーブルなど、要素クラスの一部として特定の要素型を作成するヘルパー関数を追加します。

```powershell
# Classes that define the structure of the document
#
class Html
{
    [string] $docType
    [HtmlHead] $Head
    [Element[]] $Body
    
    [string] Render()
    {
        $text = "<html>`n<head>`n"
        $text += $this.Head
        $text += "`n</head>`n<body>`n"
        $text += $this.Body -join "`n" # Render all of the body elements
        $text += "</body>`n</html>"
        return $text
    }
    [string] ToString() { return $this.Render() }
}

class HtmlHead
{
    $Title
    $Base
    $Link
    $Style
    $Meta
    $Script
    [string] Render() { return "<title>$($this.Title)</title>" }
    [string] ToString() { return $this.Render() }
}

class Element
{
    [string] $Tag
    [string] $Text
    [hashtable] $Attributes
    [string] Render() {
        $attributesText= ""
        if ($this.Attributes)
        {
            foreach ($attr in $this.Attributes.Keys)
            {
                #BUGBUG - need to validate keys against attribute
                $attributesText += " $attr=`"$($this.Attributes[$attr])\`""
            }
        }
        return "<$($this.tag)${attributesText}>$($this.text)</$($this.tag)>`n"
    }
[string] ToString() { return $this.Render() }
}

#
# Helper functions for creating specific element types on top of the classes.
# These are required because types aren’t visible outside of the module.
#

function H1 { [Element] @{ Tag = "H1" ; Text = $args.foreach{$_} -join " " }}
function H2 { [Element] @{ Tag = "H2" ; Text = $args.foreach{$_} -join " " }}
function H3 { [Element] @{ Tag = "H3" ; Text = $args.foreach{$_} -join " " }}
function P { [Element] @{ Tag = "P" ; Text = $args.foreach{$_} -join " " }}
function B { [Element] @{ Tag = "B" ; Text = $args.foreach{$_} -join " " }}
function I { [Element] @{ Tag = "I" ; Text = $args.foreach{$_} -join " " }}
function HREF
{
    param (
        $Name,
        $Link
    )
    return [Element] @{
        Tag = "A"
        Attributes = @{ HREF = $link }
        Text = $name
    }
}
function Table
{
    param (
    [Parameter(Mandatory)]
    [object[]]
        $Data,
    [Parameter()]
    [string[]]
        $Properties = "*",
    [Parameter()]
    [hashtable]
        $Attributes = @{ border=2; cellpadding=2; cellspacing=2 }
    )
$bodyText = ""
# Add the header tags
$bodyText += $Properties.foreach{TH $_}
# Add the rows
$bodyText += foreach ($row in $Data)
    {
        TR (-join $Properties.Foreach{ TD ($row.$\_) } )
    }

    $table = [Element] @{
        Tag = "Table"
        Attributes = $Attributes
        Text = $bodyText
    }
$table
}
function TH { ([Element] @{ Tag = "TH" ; Text = $args.foreach{$_} -join " " }) }
function TR { ([Element] @{ Tag = "TR" ; Text = $args.foreach{$_} -join " " }) }
function TD { ([Element] @{ Tag = "TD" ; Text = $args.foreach{$_} -join " " }) }
function Style

{
    return [Element] @{
        Tag = "style"
        Text = "$args"
    }
}

# Takes a hash table, casts it to and HTML document
# and then returns the resulting type.
#
function Html ([HTML] $doc) { return $doc }
```

<!--HONumber=Oct16_HO1-->


