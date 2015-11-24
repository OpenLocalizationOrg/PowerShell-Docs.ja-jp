#カスタムの Windows PowerShell が必要な状態の構成のリソースを作成します。

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

Windows PowerShell 必要な状態 Configuration (DSC) には、環境を設定するのに使用できる組み込みのリソースがあります。 (詳細については、次を参照してください [組み込み Windows PowerShell 必要な状態の構成に関するリソース](builtInResource.md)。)。 このトピックでは、リソースおよび特定の情報と例を含むトピックへのリンクの開発の概要を示します。

##DSC リソースのコンポーネント

DSC リソースとは、Windows PowerShell モジュールです。 モジュールには、リソースのスキーマ (構成可能なプロパティの定義) と実装 (の構成で指定する実際の処理を行うコード) の両方が含まれています。 DSC リソースのスキーマは、MOF ファイルに定義することができ、実装は、スクリプト モジュールによって実行します。 以降のバージョン 5 で PowerShell のクラスのサポートでは、スキーマと実装両方で定義できますクラスです。 以下のトピックは、さらに詳しく DSC リソースを作成する方法を説明します。

* [MOF を持つカスタム DSC リソースの作成](authoringResourceMOF.md)
* [DSC リソースを実装する (C#)](authoringResourceMofCS.md)
* [PowerShell のクラスを持つカスタム DSC リソースの作成](authoringResourceClass.md)
* [複合リソース: リソースとして DSC 構成を使用します。](authoringResourceComposite.md)
* [リソース デザイナー ツールを使用します。](authoringResourceMofDesigner.md)



