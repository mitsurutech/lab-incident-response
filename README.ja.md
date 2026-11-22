# インシデント対応とフォレンジックのラボ：侵害された Linux サーバーを調査する

[English](README.md) | [Français](README.fr.md) | [Español](README.es.md) | [日本語](README.ja.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-incident-response/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-incident-response/actions/workflows/check.yml)

授業でそのまま使える**インシデント対応とデジタルフォレンジックのラボ**です。学校やカレッジが生徒一人ひとりに専用のマシンとブラウザー上のターミナルを渡せる [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)で提供しています。生徒にはそれぞれ専用の Ubuntu VM が渡され、その中に**侵害された Web サーバーのログとファイル**が入っています。ブルートフォース攻撃を受けた SSH ログ、スキャナーだらけの Web サーバーのアクセスログ、アカウントファイル、crontab、ファイルの更新時刻の一覧です。生徒は**攻撃者の IP アドレス、侵入されたアカウント、最初のログインの時刻、侵入者が残した永続化の仕掛け**を見つけ、見つけたことを `answers.txt` に書きます。生徒のコンピューターには何もインストールしません。

**マルウェアもエクスプロイトコードもありません。** すべてテキストで、どの Linux マシンにもあるツール（`grep`、`awk`、`sort`、`uniq`、`cut`、`less`）で読みます。事件には意図的におとりがあります。ログイン失敗がいちばん多いアドレスは一度も侵入に成功せず、管理者はパスワードを打ち間違えてから成功し、正当なユーザーと古い SSH 鍵が侵入者の変更のそばにあります。そのため、雑な `grep` では解けず、時系列を丁寧に読めば解けます。

## 生徒がすること

1. ラボがホームフォルダーに置く README を読みます。証拠の場所と見つけるものは書いてありますが、答えは一つも書いてありません。
2. `case/var/log/auth.log` と `case/var/log/nginx/access.log` を調べて、攻撃とノイズを見分け、アドレスとアカウントを見つけます。
3. そのアドレスからの最初のログイン成功の時刻を割り出します。
4. crontab、アカウント、鍵、`case/mtimes.txt` を調べて、侵入者が残したもの、それがあるファイル、実行し続けているプログラムを見つけます。
5. `answers.txt` に、ラベル付きの6行を書きます。

## 採点のしくみ

このラボには**チェック**があります。提出された VM を、教員が中身を読まなくても採点するスクリプトです。`answers.txt` を読み（大文字小文字と空白は区別しません）、各答えを事件の内容と照らし合わせて、**最初に間違っている項目を示しますが、正しい答えは決して教えません**。証拠ファイルが答えに合わせて書き換えられた事件も受け付けません。事件はすべての生徒で同じで、VM の初回起動時に固定のシードから作られます。

生徒は自分の VM で `sudo` を使えるので、root のファイルを読めば鍵を見つけられます。これは許容しています。ラボは練習であり、採点されるのは書かれた答えだからです。

## ファイルの中身

`incident-response.lab.yaml` は、プラットフォームが実行するそのままの **VM ラボの定義**一式です。

| キー | 内容 |
| --- | --- |
| `cpus`, `memory`, `disk` | 生徒ごとの VM の大きさ：CPU 1個、1024 MB、10 GB |
| `open` | ブラウザーで開くポート：なし。ラボはターミナルです |
| `setup` | 各生徒の VM が初めて起動したときに、root で一度だけ実行されるスクリプト。事件を作り、README と空の `answers.txt` を書き、イメージに Python がない場合に限り Ubuntu のアーカイブから Python をインストールします |
| `check` | 採点スクリプト。新しく起動した後、保存されたディスクのコピーに対して root で実行されます。終了ステータスが結果で、`0` なら合格です |

## プラットフォームで使う

**クラスの課題として出すには：**

1. モジュールで課題を追加し、**Ubuntu VM ラボ**のカードを選びます。
2. `incident-response.lab.yaml` の内容を貼り付けます。プラットフォームにサンプルがあれば、サンプルのボタンを押しても構いません。サンプルを使うとテキストが置き換わり、元に戻すと自分のテキストが戻ります。
3. 公開して、まず自分用のコピーで試します。生徒にはそれぞれ専用の VM が渡され、セッションをまたいで保たれます。
4. 他の課題と同じように配布します。提出されたら、クラス一覧で生徒の行からチェックを実行します。保存された生徒のマシンのコピーを起動し（保存し直しはされません）、合格したかどうかと出力された内容を表示します。

残りはヘルプマニュアルの[このラボのページ](https://mitsurutech.co.uk/ja/help/lab-incident-response)、[Ubuntu VM ラボのガイド](https://mitsurutech.co.uk/ja/help/labs)、[サンプルラボのページ](https://mitsurutech.co.uk/ja/help/sample-labs)をご覧ください。自分の事件を作るには、`setup` の中の生成部分（シード、アドレス、アカウント、書き出すファイル）を変更します。

まだカレッジのアカウントがありませんか？ 生徒50人までなら[最初の1年は無料](https://mitsurutech.co.uk/ja/pricing)で、[登録](https://mitsurutech.co.uk/ja/signup)にはメールアドレスだけが必要です。

## ファイルの検査

push のたびに、`.github/workflows/check.yml` が次のことを調べます。ファイルが上の5つのキーを持つ有効な YAML であること、`setup` と `check` がそれぞれプラットフォームの 16 KB の上限に収まっていること、採点スクリプトが有効な Python であること。VM は起動しません。それはプラットフォーム自身のマシンで行います。

## ラボでできないこと

VM が接続できるのは Ubuntu のパッケージアーカイブ、PyPI、npm、GitHub だけで、それ以外には接続できません。そのため、ラボからインターネット上のホストを指すことはできません。読むためのログがそろった VM が1台あるだけで、ライブの攻撃者を観察することも、攻撃する2台目のマシンもありません。

## 提供元

[Mitsuru Technologies](https://mitsurutech.co.uk/ja) が [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)のために作りました。学校とカレッジ向けに、Python、Java、Go、Node、SQL、Linux をブラウザーで学べ、コンテナラボ、VM ラボ、GitHub からデプロイする Web アプリ、自動採点されるクイズがそろっています。アカウントなしで、[プレイグラウンド](https://mitsurutech.co.uk/ja/playground)でコードを試せます。

同じところのほかのラボ：[lab-netlab](https://github.com/mitsurutech/lab-netlab)、[lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop)、[lab-dvwa](https://github.com/mitsurutech/lab-dvwa)、[lab-webgoat](https://github.com/mitsurutech/lab-webgoat)、[lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin)、[lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea)、[lab-sample](https://github.com/mitsurutech/lab-sample)。

## ライセンス

この作品は [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) でライセンスされています。Mitsuru Technologies Ltd のクレジットを表示し、商用に使わず、これをもとに作ったものを同じライセンスで共有するなら、学習と授業のために共有・改変できます。全文は `LICENSE` ファイルにあります。

事件の中のアドレスはすべて、ドキュメント用に予約された範囲かプライベートの範囲のものであり、実在のデータは一つも含まれていません。
