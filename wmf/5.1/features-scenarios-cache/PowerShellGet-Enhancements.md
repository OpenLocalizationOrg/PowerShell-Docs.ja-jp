---
title: "機能またはシナリオ書き込みのテンプレート例"
contributor: KeithB
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 025565404b60cebefac27e51c70d70edb5e47bc9

---

>注: わかりやすいタイトルと簡単な説明を提供します

## PowerShellGet の機能強化 ##
PowerShellGet モジュールのコマンドレットは、WMF 5.1 で大幅に更新されています。 次の新しいシナリオはサポートされているようになりました。

- **プロキシ サポート:** PowerShellGet コマンドレットを使用すると、内部のプロキシでできるようになって、PowerShellGet モジュールで、検索、インストールで、保存、および発行コマンドレットのすべてにプロキシと ProxyCredential パラメーターの追加することで (例: 保存モジュールの検索モジュールのインストール、モジュール、発行 Module)。 
- **カタログの署名を適用するには:** PowerShellGet のすべてのコマンドレットは今すぐ確認し、署名されたモジュールの更新を適用します。 送信中、モジュールが変更されていないこと、およびモジュールへの更新は、元のパブリッシャーからのみで発生ことを確認 PowerShellGet コマンドレットを使用して、新しいカタログ コマンドレットを使用して署名したモジュールがチェックされます。 これは、インストール モジュール、および更新プログラム モジュールのコマンドレットに影響します。 
- **ロールの機能を共有し、ロックを獲得しよう:** ロール機能はだけ十分な管理を制限するためを構成するために使用するエンドポイントの定義と PowerShell モジュールで PowerShell ギャラリー経由で共有されます。 コマンドレットには、検索 RoleCapability、および新規 PSRoleCapabilityFile が含まれます。 
- **Find コマンド:** 検索コマンドにより、ユーザーが探しているコマンドが含まれる PowerShell ギャラリーでモジュールを検索します。 
- **認証のリポジトリ:** Visual Studio パッケージの管理のフィードには、認証、PowerShellGet コマンドレットを使用してできるようになっているものが必要です。

ユーザーからのフィードバックが 5.1 で処理された改善のための追加の領域を識別します。

- **インストール スクリプトの変更:** インストール スクリプトによって使用される場所は、5.1 で自動的にユーザーのパスには追加されません。 この変更は、PowerShellGet のスタンドアロン バージョンにあったし、WMF 5.1 になります。
- **既存のスクリプトへのメタデータ フィールドの追加:** 、PowerShellGallery への公開に必要な既定のメタデータ フィールドを追加する既存のスクリプトを更新 ScriptFileInfo を使用できます。 以前のユーザーは、これを既存のスクリプトに手動でマージに必要です。
- **下でバージョン管理されたモジュールをギャラリーに発行:** ことが現在までの最上位バージョンよりも低いバージョン番号を含むモジュールを発行モジュールを使用してギャラリーに発行できます。 1.x バージョンからの変更を中断すると、モジュールのバージョン 2.0.0 がパブリッシャーにリリースされる場合、簡単を解放できませんでした 1.5.0 のバージョンに修正します。 これで、PowerShell ギャラリー内のモジュールを維持するためのサポートを強化する、1.5.1 を公開できます。 
- **スクリプトおよびモジュールをインストールするときに、コマンドの上書きのチェック:** モジュールのインストールとインストール スクリプトは今すぐに、システムが既に存在するコマンドが含まれているかどうかをおよびチェック アウトする場合の動作によって既定のエラーです。 
- **発行コマンドレットで Nuget のブートス トラップ:** 以前発行モジュールまたは特定のオートメーション タスクを非常に困難にする公開スクリプトを使用する場合、Nuget プロバイダーを自動的にインストールする方法がありません。 プロンプトをバイパスする Publish コマンドに強制的にユーザーを追加のようになりました。 

>注: 新しい概念、実装、例などに関する追加詳細は正規のテクニカル ドキュメントにリンクしてください。

**PowerShellGet の使用に関する詳細します。**
- [CatalogSigning について]()
- []()
- [CompatiblePSEditions で Get モジュールの結果をフィルター処理します。]()
- [PowerShell の互換性のあるエディションで実行するいないと、スクリプトの実行を防止します。]()






<!--HONumber=Oct16_HO1-->


