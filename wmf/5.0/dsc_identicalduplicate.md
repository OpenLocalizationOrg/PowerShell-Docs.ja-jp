# 構成における同一重複リソースの許容

DSC は、構成内で競合するリソース定義を許可せず、処理しません。 競合を解決するのではなく、ただの失敗となります。 複合リソースなどの間で構成の再利用が多く行われるようになるほど、競合は頻繁に発生します。 競合するリソース定義が一致する場合、DSC は賢くこれを許可します。 このリリースにより、同一の定義を持つ複数のリソース インスタンスをサポートするようになりました:

```powershell
Configuration IIS_FrontEnd
{
    WindowsFeature FE_IIS         #Identical resource
    {
        Ensure = 'Present'
        Name = 'Web-Server'
    }

    WindowsFeature FTP
    {
        Ensure = 'Present'
        Name = 'Web-FTP-Server'
    }
}

Configuration IIS_Worker
{
    WindowsFeature Worker_IIS      #Identical resource
    {
        Ensure = 'Present'
        Name = 'Web-Server'
    }

    WindowsFeature ASP
    {
        Ensure = 'Present'
        Name = 'Web-ASP-Net45'
    }
}

Configuration WebApplication
{
    IIS_Frontend Web {}

    IIS_Worker ASP {}
}
```

以前のリリースでは、'Web サーバー' ロールが確実にインストールされるようにするために発生する WindowsFeature FE_IIS と WindowsFeature Worker_IIS インスタンス間の競合により、コンパイルの失敗に終わっていました。 構成が実行される*すべて*のプロパティは、これら 2 つの構成間で一致していることに注目してください。 これらの 2 つのプロパティのリソースは*すべて*一致するため、結果として正常にコンパイルできるようになりました。 

2 つのリソース間でいずれかのプロパティが異なる場合、一致していないと見なされ、コンパイルは失敗します:

```powershell
Configuration IIS_FrontEnd
{
    WindowsFeature FE_IIS
    {
        Ensure = 'Present'     # Ensure is Present here
        Name = 'Web-Server'
    }

    WindowsFeature FTP
    {
        Ensure = 'Present'
        Name = 'Web-FTP-Server'
    }
}

Configuration IIS_Worker
{
    WindowsFeature Worker_IIS
    {
        Ensure = 'Absent'      # Ensure property is Absent instead of Present
        Name = 'Web-Server'
    }

    WindowsFeature ASP
    {
        Ensure = 'Present'
        Name = 'Web-ASP-Net45'
    }
}

Configuration WebApplication
{
    IIS_Frontend Web {}

    IIS_Worker ASP {}
}
```

この非常に類似した構成は、WindowsFeature FE_IIS と WindowsFeature Worker_IIS リソースが一致せず、競合が発生するようになったため、失敗します。

<!--HONumber=Oct16_HO1-->


