# アイテムをリストから外す

**理由は、オプションとして公開されない PowerShell ギャラリーから項目を削除するでしょうか。**

PowerShell ギャラリーでは、ユーザーがアイテムを完全削除することはできません。 それにより、他のユーザーが既存のアイテムを活用しても、その依存性が壊されることがありません。 たとえば、Pester モジュールは Azure モジュールに依存しているとき、Azure モジュールをギャラリーから削除すると、Pester モジュールを使用できなくなります。

そこで、アイテムを削除する代わりに、アイテムをリストから外します。

**非公開 PowerShell ギャラリー項目は何ですか。**

PowerShell ギャラリーでモジュールやスクリプトなどのアイテムをリストから外すと、そのアイテムは [アイテム] タブから削除されます。
また、リストから外されたアイテムは検索バーで検索できなくなります。
リストから外されたアイテムをダウンロードする唯一の方法は、アイテムの正確な名前とバージョンを指定することです。
以上のような理由から、アイテムをリストから外しても、そのアイテムに依存する他のモジュールやスクリプトがなくなることはありません。

アイテムをリストから外すには、アイテム詳細ページにアクセスし、'項目の削除' を選択します。 'Listed' (一覧に表示する) チェックボックスのチェックを外し、[保存] をクリックします。

**項目を削除する方法はありますか**

アイテムを削除しなければならない場合、PowerShell ギャラリーの管理者にお問い合わせください。
削除が必要になる場合:
- 著作権侵害が問題になった。
- アイテムに有害なコンテンツが含まれている。
- アイテムに機密データが含まれている。

PowerShell ギャラリーの管理者にアイテムの削除依頼を送信するには、アイテム詳細ページにアクセスし、[Contact Support] (サポートに問い合わせる) を選択します。  




<!--HONumber=Oct16_HO1-->


