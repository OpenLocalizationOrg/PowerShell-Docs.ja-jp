# 基本クラス コンストラクターの呼び出し

サブクラスから基本クラス コンストラクターを呼び出すには、キーワード **base** を使用します。

```PowerShell
class A 
{
    [int]$a

    A([int]$a)
    {
        $this.a = $a
    }
}

class B : A
{
    B() : base(103) {}
}

[B]::new().a # return 103
```

基本クラスに既定の (パラメーターなし) コンストラクターがある場合は、明示的なコンストラクター呼び出しを省略できます。

```PowerShell
class C : B
{
    C([int]$c) {}
}
```

<!--HONumber=Oct16_HO1-->


