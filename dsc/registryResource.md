#DSC レジストリのリソース

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

**レジストリ** リソースで Windows PowerShell 必要な状態 Configuration (DSC) は、レジストリ キーとターゲット ノード上の値を管理するメカニズムを提供します。

##構文

```
Registry [string] #ResourceName
{
    Key = [string]
    ValueName = [string]
    [ Ensure = [string] { Absent | Present }  ]
    [ Force =  [bool]   ]
    [ Hex = [bool] ]
    [ DependsOn = [string[]] ]
    [ ValueData = [string[]] ]
    [ ValueType = [string] { Binary | Dword | ExpandString | MultiString | Qword | String }  ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| キー| 特定の状態を確認するレジストリ キーのパスを示します。このパスは、hive を含める必要があります。|
| 値名| レジストリ値の名前を示します。|
| 確認します。| キーと値の存在を示します。"Present"には、このプロパティを設定を行うことを確認します。確実に存在しないことに「存在しない」プロパティを設定します。既定値は、"Present"です。|
| Force| 指定されたレジストリ キーが存在する場合 __Force__ 新しい値で上書きします。|
| 16 進数| 16 進形式でデータを表現するかどうかを示します。指定した場合、DWORD または QWORD 値のデータは 16 進数形式で表示されます。その他の種類では無効です。既定値は __$false__です。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。リソースの構成の ID はスクリプト ブロックを実行する場合が最初はたとえば、 __ResourceName__ あり、型が __リソースの種類__, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|
| セグ| レジストリ値のデータ。|
| ValueType| 値の型を示します。サポートされている型は次のとおりです。

<ul>
            <li>文字列 (REG_SZ)</li>
<li>バイナリ (REG バイナリ)</li>
<li>Dword 32 ビット (REG_DWORD)</li>
<li>Qword 64 ビット (REG_QWORD)</li>
<li>複数行文字列 (REG_マルチ_SZ)</li>
<li>展開可能な文字列 (REG_展開_SZ)</li></ul>

##例

```powershell
Registry RegistryExample
{
    Ensure = "Present"  # You can also set Ensure to "Absent"
    Key = "HKEY_LOCAL_MACHINE\SOFTWARE\ExampleKey"
    ValueName = "TestValue"
    ValueData = "TestData"
}
```





