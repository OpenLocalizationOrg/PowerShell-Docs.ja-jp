# 統一された一貫性のある状態とステータスの表現

今回のリリースでは、LCM 状態と DSC ステータスのビルドの自動化に対して、一連の拡張機能が加えられました。 これには、統一された一貫性のある状態とステータスの表現、Get-DscConfigurationStatus コマンドレットから返されるステータス オブジェクトの管理しやすい日時プロパティ、Get-DscLocalConfigurationManager コマンドレットから返される拡張された LCM 状態の詳細プロパティが含まれます。

LCM 状態と DSC 操作ステータスの形式を再検討し、次の規則に従って統合されました。
1.  未処理のリソースは、LCM 状態と DSC ステータスに影響を与えません。
2.  LCM は、再起動を要求するリソースに到達すると、リソースの処理を停止します。
3.  再起動を要求するリソースは、再起動が実際に発生するまで、必要な状態になりません。
4.  失敗したリソースを検出した場合は、そのリソースに依存していない限り、LCM は他のリソースの処理を続けます。
5.  Get-DscConfigurationStatus コマンドレットによって返される状態の全体は、すべてのリソースのステータスのスーパー セットです。
6.  PendingReboot 状態は、PendingConfiguration 状態のスーパーセットです。

次の表は、いくつかの一般的なシナリオでのプロパティに関連した状態とステータスの結果を示しています。

| **シナリオ**                    | **LCMState\ ***       | **状態** | **再起動要求**  | **ResourcesInDesiredState**  | **ResourcesNotInDesiredState** |
|---------------------------------|----------------------|------------|---------------|------------------------------|--------------------------------|
| S**^**                          | アイドル                 | 成功    | $false        | S                            | $null                          |
| F**^**                          | PendingConfiguration | 障害    | $false        | $null                        | F                              |
| S、F                             | PendingConfiguration | 障害    | $false        | S                            | F                              |
| F、S                             | PendingConfiguration | 障害    | $false        | S                            | F                              |
| S<sub>1</sub>、F、S<sub>2</sub> | PendingConfiguration | 障害    | $false        | S<sub>1</sub>、S<sub>2</sub> | F                              |
| F<sub>1</sub>、S、F<sub>2</sub> | PendingConfiguration | 障害    | $false        | S                            | F<sub>1</sub>、F<sub>2</sub>   |
| S、r                            | PendingReboot        | 成功    | $true         | S                            | r                              |
| F、r                            | PendingReboot        | 障害    | $true         | $null                        | F、r                           |
| r、S                            | PendingReboot        | 成功    | $true         | $null                        | r                              |
| r、F                            | PendingReboot        | 成功    | $true         | $null                        | r                              |

^
S<sub>は</sub>: F を正常に適用されたリソースのシリーズ<sub>は</sub>: 一連の再起動を必要とする r: リソースが適用されなかったリソース \*

```powershell
$LCMState = (Get-DscLocalConfigurationManager).LCMState
$Status = (Get-DscConfigurationStatus).Status

$RebootRequested = (Get-DscConfigurationStatus).RebootRequested

$ResourcesInDesiredState = (Get-DscConfigurationStatus).ResourcesInDesiredState

$ResourcesNotInDesiredState = (Get-DscConfigurationStatus).ResourcesNotInDesiredState
```
## Get-DscConfigurationStatus コマンドレットの機能拡張

このリリースでは、Get-DscConfigurationStatus コマンドレットのいくつかの機能が拡張されました。 以前は、コマンドレットによって返されるオブジェクトの StartDate プロパティは文字列型でした。 今では、Datetime 型になったため、Datetime オブジェクトに備わっているプロパティに基づいて複雑な選択やフィルター処理を実行できるようになりました。
```powershell
(Get-DscConfigurationStatus).StartDate | fl \*
DateTime : Friday, November 13, 2015 1:39:44 PM
Date : 11/13/2015 12:00:00 AM
Day : 13
DayOfWeek : Friday
DayOfYear : 317
Hour : 13
Kind : Local
Millisecond : 886
Minute : 39
Month : 11
Second : 44
Ticks : 635830187848860000
TimeOfDay : 13:39:44.8860000
Year : 2015
```

今日と同じ曜日に発生したすべての DSC 操作レコードを返す例を次に示します。
```powershell
(Get-DscConfigurationStatus –All) | where { $\_.startdate.dayofweek -eq (Get-Date).DayOfWeek }
```

ノードの構成を変更しない操作 (つまり、読み取り専用の操作) のレコードは除外されます。 そのため、Get-DscConfigurationStatus コマンドレットから返されるオブジェクトに Test-DscConfiguration 操作と Get-DscConfiguration 操作が混入されなくなりました。
メタ構成の設定操作のレコードが Get-DscConfigurationStatus コマンドレットの戻り値に追加されます。

次の例は、Get-DscConfigurationStatus –All コマンドレットから返される結果を示しています。
```powershell
All configuration operations:

Status StartDate Type RebootRequested
------ --------- ---- ---------------
Success 11/13/2015 11:38:16 AM Consistency False
Success 11/13/2015 11:23:16 AM Reboot False
Success 11/13/2015 11:21:43 AM Reboot True
Success 11/13/2015 11:20:44 AM Initial True
Success 11/13/2015 11:20:44 AM LocalConfigurationManager False
```

## Get-DscLocalConfigurationManager コマンドレットの機能拡張
Get-DscLocalConfigurationManager コマンドレットから返されるオブジェクトに、新しい LCMStateDetail フィールドが追加されました。 このフィールドは、LCMState が "ビジー" の時にデータが設定されます。 これは、次のコマンドレットで取得できます。
```powershell
(Get-DscLocalConfigurationManager).LCMStateDetail
```

リモート ノードで 2 回のリブートを必要とする構成を継続的に監視した場合の出力の例を次に示します。
```powershell
Start a configuration that requires two reboots

Monitor LCM State:
LCM State: Busy, LCM is applying a new configuration.
LCM State: PendingReboot,
Machine is rebooting...
LCM State: Busy, LCM is continuing applying configuration after last reboot.
LCM State: PendingReboot,
Machine is rebooting...
LCM State: Busy, LCM is continuing applying configuration after last reboot.
LCM State: Idle,
LCM State: Busy, LCM is performing a consistency check.
LCM State: Idle,
```


<!--HONumber=Oct16_HO1-->


