# [概要](overview.md)

# [構成](configurations.md)
## [構成の適用](enactingConfigurations.md)
## [複数のバージョンでリソースの使用](sxsResource.md)
## [ユーザーの資格情報を持つ DSC を実行しています。](runAsUser.md)
## [ノード間の依存関係を指定します。](crossNodeDependencies.md)
## [構成データ](configData.md)
### [構成データでの資格情報オプション](configDataCredentials.md)
## [構成の MOF ファイルをセキュリティで保護します。](secureMOF.md)
## [一部の構成](partialConfigs.md)
## [ヘルプの DSC 構成を作成](configHelp.md)

# [リソース](resources.md)
## [組み込みのリソース](builtInResource.md)
### [アーカイブ リソース](archiveResource.md)
### [環境リソース](environmentResource.md)
### [ファイル リソース](fileResource.md)
### [グループ リソース](groupResource.md)
### [ログ リソース](logResource.md)
### [パッケージ リソース](packageResource.md)
### [レジストリ リソース](registryResource.md)
### [スクリプト リソース](scriptResource.md)
### [サービス リソース](serviceResource.md)
### [ユーザー リソース](userResource.md)
### [WindowsFeature リソース](windowsfeatureResource.md)
### [WindowsProcess リソース](windowsProcessResource.md)
## [カスタム リソースを作成します。](authoringResource.md) 
### [MOF ベースのカスタム リソース](authoringResourceMOF.md)
#### [C# で MOF ベースのリソース](authoringResourceMofCS.md)
### [カスタム リソースのクラスに基づく](authoringResourceClass.md)
### [複合リソース](authoringResourceComposite.md)
### [単一インスタンスの DSC リソース (ベスト プラクティス) の書き込み](singleInstance.md)
### [リソースの作成のチェックリスト](resourceAuthoringChecklist.md)
## [DSC リソースをデバッグします。](debugResource.md)
## [DSC リソース メソッドの直接呼び出し](directCallResource.md)

# [ローカル構成マネージャー (LCM) を構成します。](metaConfig.md)
## [PowerShell 4.0 で、LCM を構成します。](metaConfig4.md)

# DSC プル モデル
## [Setting up a web pull server (Web プル サーバーのセットアップ)](pullServer.md)
## [SMB の DSC プル サーバーをセットアップします。](pullServerSMB.md)
## [プル クライアントのセットアップ](pullClient.md)
### [構成名を使用したプル クライアントのセットアップ](pullClientConfigNames.md)
### [構成 ID を使用したプル クライアントのセットアップ](pullClientConfigID.md)
## [DSC のレポート サーバーを使用します。](reportServer.md)
## [Server のベスト プラクティスをプルします。](secureServer.md)

# [DSC のトラブルシューティング](troubleshooting.md)

# [ナノ サーバー上の DSC を使用してください。](nanoDsc.md)

# Linux での DSC
## [Linux 向け DSC の概要](lnxGettingStarted.md)
## [Linux 用の組み込みのリソース](lnxBuiltInResources.md)
### [nxArchive リソース](lnxArchiveResource.md)
### [nxEnvironment リソース](lnxEnvironmentResource.md)
### [nxFile リソース](lnxFileResource.md)
### [nxFileLine リソース](lnxFileLineResource.md)
### [nxGroup リソース](lnxGroupResource.md)
### [nxPackage リソース](lnxPackageResource.md)
### [nxService リソース](lnxServiceResource.md)
### [nxSshAuthorizedKeys リソース](lnxSshAuthorizedKeysResource.md)
### [nxUser リソース](lnxUserResource.md)

# DSC MOF の参照
## [MSFT_DSCLocalConfigurationManager クラス](msft-dsclocalconfigurationmanager.md)
### [MSFT_DSCLocalConfigurationManager クラスの ApplyConfiguration メソッド](msft-dsclocalconfigurationmanager-applyconfiguration.md)
### [MSFT_DSCLocalConfigurationManager クラスの DisableDebugConfiguration メソッド](msft-dsclocalconfigurationmanager-disabledebugconfiguration.md)
### [MSFT_DSCLocalConfigurationManager クラスの EnableDebugConfiguration メソッド](msft-dsclocalconfigurationmanager-enabledebugconfiguration.md)
### [MSFT_DSCLocalConfigurationManager クラスの GetConfiguration メソッド](msft-dsclocalconfigurationmanager-getconfiguration.md)
### [MSFT_DSCLocalConfigurationManager クラスの GetConfigurationResultOutput メソッド](msft-dsclocalconfigurationmanager-getconfigurationresultoutput.md)
### [MSFT_DSCLocalConfigurationManager クラスの GetConfigurationStatus メソッド](msft-dsclocalconfigurationmanager-getconfigurationstatus.md)
### [MSFT_DSCLocalConfigurationManager クラスの GetMetaConfiguration メソッド](msft-dsclocalconfigurationmanager-getmetaconfiguration.md)
### [MSFT_DSCLocalConfigurationManager クラスの PerformRequiredConfigurationChecks メソッド](msft-dsclocalconfigurationmanager-performrequiredconfigurationchecks.md)
### [MSFT_DSCLocalConfigurationManager クラスの RemoveConfiguration メソッド](msft-dsclocalconfigurationmanager-removeconfiguration.md)
### [MSFT_DSCLocalConfigurationManager クラスの ResourceGet メソッド](msft-dsclocalconfigurationmanager-resourceget.md)
### [MSFT_DSCLocalConfigurationManager クラスの ResourceSet メソッド](msft-dsclocalconfigurationmanager-resourceset.md)
### [MSFT_DSCLocalConfigurationManager クラスの ResourceTest メソッド](msft-dsclocalconfigurationmanager-resourcetest.md)
### [MSFT_DSCLocalConfigurationManager クラスの rollBack メソッド](msft-dsclocalconfigurationmanager-rollback.md)
### [MSFT_DSCLocalConfigurationManager クラスの SendConfiguration メソッド](msft-dsclocalconfigurationmanager-sendconfiguration.md)
### [MSFT_DSCLocalConfigurationManager クラスの SendConfigurationApply メソッド](msft-dsclocalconfigurationmanager-sendconfigurationapply.md)
### [MSFT_DSCLocalConfigurationManager クラスの SendConfigurationApplyAsync メソッド](msft-dsclocalconfigurationmanager-sendconfigurationapplyasync.md)
### [MSFT_DSCLocalConfigurationManager クラスの SendMetaConfigurationApply メソッド](msft-dsclocalconfigurationmanager-sendmetaconfigurationapply.md)
### [MSFT_DSCLocalConfigurationManager クラスの StopConfiguration メソッド](msft-dsclocalconfigurationmanager-stopconfiguration.md)
### [MSFT_DSCLocalConfigurationManager クラスの TestConfiguration メソッド](msft-dsclocalconfigurationmanager-testconfiguration.md)

# その他のリソース
## [ホワイト ペーパー](whitepapers.md)



<!--HONumber=Oct16_HO1-->


