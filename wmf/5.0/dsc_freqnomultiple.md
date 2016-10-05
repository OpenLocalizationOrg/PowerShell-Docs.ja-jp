# RefreshMode と ConfigurationMode の出現頻度は互いの倍数である必要はありません

以前のバージョンの DSC では、LCM は `RefreshFrequencyMins` と `ConfigurationModeFrequencyMins` を互いの倍数として処理します。 WMF 5.0 RTM では、これらのプロパティは互いに独立して処理されます。 

詳細については、「[ローカル構成マネージャーの構成](https://msdn.microsoft.com/powershell/dsc/metaconfig)」を参照してください。

<!--HONumber=Oct16_HO1-->


