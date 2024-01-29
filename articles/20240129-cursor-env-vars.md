---
title: "windowsでcursorを設定後、codeコマンドでvscodeが開けなくなったときの対処法"
emoji: "🐈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["cursor", "vscode"]
published: false
---
## 環境変数の編集
* windows設定の検索から「システムの詳細設定」を選択
![windowsの設定画面](/images/articles//20240129-cursor-env-vars/img1.webp)

* システムのプロパティから環境変数ボタンをクリック
![システムのプロパティ画面](/images/articles//20240129-cursor-env-vars/img2.webp)

* システム環境変数
ユーザー環境変数とシステム環境変数の２つがあるが、下のシステム環境変数を編集する。
「Path」を選択した状態で編集ボタンをクリックする。
![システム環境変数画面](/images/articles//20240129-cursor-env-vars/img3.webp)

* 編集内容
おそらく最初はcursorのパスしか設定されていない。
「C:\Users\\{ユーザー名}\AppData\Local\Programs\cursor\resources\app\bin」
そこにvscodeのパスを追加する。
「C:\Users\\{ユーザー名}\AppData\Local\Programs\Microsoft VS Code\bin」

ここで重要なのが設定の順番で、vscodeのパスがcursorの上に来るように設定する必要がある。
![変数設定項目](/images/articles//20240129-cursor-env-vars/img4.webp)


ここまで設定できたら、ターミナルを再起動するとcodeコマンドでvscodeが開くようになっているはず。
またcursorを起動したいときは、cursorコマンドで開く。


## 終わりに
とりあえずこれでvscodeとcursorを分けて起動させることができましたが、wsl上ではうまく使い分けができないっぽいです。
cursorはまだリリースしたばかりなので、対応してくれるまで気長に待つしかないですね。。。