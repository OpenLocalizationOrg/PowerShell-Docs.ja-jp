# OData エンドポイントに基づく PowerShell コマンドレットの生成
OData エンドポイントに基づく Windows PowerShell コマンドレットの生成
--------------------------------------------------------------

**Export-ODataEndpointProxy** は、特定の OData エンドポイントによって公開される機能に基づいて、一連の Windows PowerShell コマンドレットを生成するコマンドレットです。

次の例では、この新しいコマンドレットを使用する方法を示します。

Export-ODataEndpointProxy の \# 基本ユース ケース

```powershell
Export-ODataEndpointProxy -Uri 'http://services.odata.org/v3/(S(snyobsk1hhutkb2yulwldgf1))/odata/odata.svc' -OutputModule C:\Users\user\Generated.psd1

ipmo 'C:\Users\user\Generated.psd1'
# Cmdlets are created based on the following heuristics
# New-<EntityType> -<Key> [-<Other Attributes>]
#
# Get-<EntityType> [-<Key> -Top –Skip –Filter -OrderBy]
# # If there is a complex key, the keys will actually be -<Key1> -<Key2>…
# # Note this rule applies to any other instances of the key
#
# Set-<EntityType> -<Key> [-<Other Attributes>]
#
# Remove-<EntityType> -<Key>
#
# Invoke-<EntityType><Action> [-<Key> -<Other Parameters>]
#
#
# Cmdlets from associations (Note: Get and Remove get additional parameter sets)
# Get-<EntityType> -<AssociatedEntity>
# New-<EntityType> -<AssociatedEntity> -<Key>
# Remove-<EntityType> -<AssociatedEntity> -<Key>
#
#
# Note: Every cmdlet has the –ConnectionURI parameter for explicitly setting the URI of the endpoint. This normally uses the same address that you gave the Export-ODataEndpointProxy cmdlet, but can be overridden in this fashion for the sake of similar endpoints.
#
```

この機能の開発に関して、次に示すものを含め、主なユース ケースがまだあります。
-   関連付け
-   ストリームの引き渡し

ODataUtils を使用した OData エンドポイントに基づく Windows PowerShell コマンドレットの生成
------------------------------------------------------------------------------
ODataUtils モジュールにより、OData をサポートする REST エンドポイントから Windows PowerShell コマンドレットを生成できます。 Microsoft.PowerShell.ODataUtils Windows PowerShell モジュールには次の増分拡張があります。
-   サーバー側エンドポイントからクライアント側への追加情報のチャネル。
-   クライアント側ページング サポート
-   -Select パラメーターを使用したサーバー側フィルタリング
-   Web 要求のヘッダーのサポート

Export-ODataEndPointProxy コマンドレットによって生成されたプロキシ コマンドレットは、情報ストリーム (新しい Windows PowerShell 5.0 の機能) 上のサーバー側 OData エンドポイントから (クライアント側プロキシの生成中に使用される $metadata には含まれない) 追加情報を提供します。 次に、その情報を取得する方法の例を示します。
```powershell
Import-Module Microsoft.PowerShell.ODataUtils -Force
$generatedProxyModuleDir = Join-Path -Path $env:SystemDrive -ChildPath 'ODataDemoProxy'
$uri = "http://services.odata.org/V3/(S(fhleiief23wrm5a5nhf542q5))/OData/OData.svc/"
Export-ODataEndpointProxy -Uri $uri -OutputModule $generatedProxyModuleDir -Force -AllowUnSecureConnection -Verbose -AllowClobber
Import-Module $generatedProxyModuleDir -Force

# In the below command, we are retrieving top 1 product.
# By specifying -IncludeTotalResponseCount parameter,
# we are getting the total count of all the Product records
# available on the server side. This information
# is surfaced on the client side through the Information stream.
$product = Get-Product -Top 1 -AllowUnsecureConnection -AllowAdditionalData -IncludeTotalResponseCount -InformationVariable infoStream
# The Information stream contains the additional
# information sent from the server side.
$additionalInfo = $infoStream.GetEnumerator() | % MessageData
# 'Odata.Count' indicates the total product records
# available on the server side Odata endpoint.
$additionalInfo['odata.count']
```

クライアント側ページング サポートを使用して、サーバー側からバッチのレコードを取得できます。 これは、ネットワーク経由でサーバーから大量のデータを取得する必要がある場合に役立ちます。
```powershell
$skipCount = 0
$batchSize = 3
# Client-Side Paging Support: The records from the server side
# are retrieved in batches of $batchSize
while($skipCount -le $additionalInfo['odata.count'])
{
Get-Product -AllowUnsecureConnection -AllowAdditionalData -Top $batchSize -Skip $skipCount
$skipCount += $batchSize
}
```

生成されたプロキシ コマンドレットは、クライアントが必要なレコード プロパティのみを受信するためのフィルターとして使用できる -Select パラメーターをサポートします。 フィルター処理はサーバー側で行われるため、ネットワーク経由で転送されるデータの量が削減されます。
```powershell
# In the below example only the Name property of the
# Product record is retrieved from the server side.
Get-Product -Top 2 -AllowUnsecureConnection -AllowAdditionalData -Select Name
```

Export-ODataEndpointProxy コマンドレットと、これによって生成されたプロキシ コマンドレットは、ヘッダー パラメーター (ハッシュ テーブルとして値を指定する) をサポートします。ヘッダー パラメーターは、サーバー側 OData エンドポイントで想定される追加情報をチャネルするために使用できます。 次の例では、認証用のサブスクリプション キーを想定するサービスのヘッダーによってサブスクリプション キーをチャネルすることができます。
```powershell
# As an example, in the below command 'XXXX' is the authentication used by the
# Export-ODataEndpointProxy cmdlet to interact with the server-side
# OData endpoint accessed through $endPointUri.

Export-ODataEndpointProxy -Uri $endPointUri -OutputModule $generatedProxyModuleDir -Force -AllowUnSecureConnection -Verbose -Headers @{'subscription-key'='XXXX'}
```


<!--HONumber=Oct16_HO1-->


