必要なものをダウンロードし設定を記述したはずなのに「pyenvが動かない」「nvmがみつからない」、ということがたまにあります。
正常にインストールされていればだいたい以下が原因だと思います。以下で解決しない場合は正常にインストールされていない可能性があります。


0. ### shellを再起動していない
.bashrcはshellのログイン時に読み込まれるものなので、記述後に再起動しないと反映されません。
shellの再起動は```exec $SHELL -l```コマンドで実行することができます。(ログインshellに設定されているもので再ログインを行います。)
何かと再起動する場面は多いので、aliasを張っておくと便利です。

0. ### .bashrcの読み込み順序に問題がある
.bashrcと.bash_profileを両方使っている場合に発生することがあります。
.bash_profileを開いてみると、まず.bashrcを読み込んだあとにPATHをexportしていると、.bashrcで行ったPATHの設定が上書きされてしまいます。
.bashrcの読み込みを後にするか、.bash_profileのPATHのexport以下に設定を記述することで解決します。
.bashrcと.bash_profileの書き分けについては以下の記事が詳しいです。これに則れば、.bashrcではPATHに触らないことが良さそうなので、.bash_profileに追記するのが良いのかと考えます。
##### [本当に正しい .bashrc と .bash_profile の使ひ分け](http://qiita.com/magicant/items/d3bb7ea1192e63fba850)

0. ### 設定が.bashrc/.bash_profileに記述されていない
```$ echo hogehoge > .bash_profile```などを行った場合にたまにあります。
どこか作業ディレクトリに.bash_profileを作成してしまっていることがあります。
記事内での説明コマンドが``` > ~/.zshrc```だったり、各自適切に書き換えることを前提として``` > ~/.your_profile```だったりすることもあります。
[anyenv](https://github.com/riywo/anyenv)を使っている場合は、githubのコマンド例が.your_profileを指しているので注意が必要です。

0. ### 記述しているpathが別ユーザを指している
サイトに書いてある通りのコマンドを追記した場合、それが自分のホームディレクトリを指していない可能性があります。
```/root/.pyenv```などとなっている場合は、```$HOME/.pyenv``` ```~/.pyenv```等に修正することで動作します。
類似の案件として、**別のユーザのホームディレクトリに配置されている**ということもあります。root直下にあったりとか。

0. ### zshを使用している
あなたのshellがbashでない場合、.bashrcを読み込んでいないことがあります。
bash/zshがよくわからない場合、何かのサイトに言われるがままに環境を整えてしまった場合など、こういうことがあります。
(勧められてzshにしたら色々なコマンドが使えなくなった、とかいう若い思い出があります。)
画面が綺麗だったりするとzshかもしれません。```echo $SHELL```で現在のshellが何であるかでてきます。
```chsh -s /bin/bash```でbashに戻すか、shellを少し勉強してzshを使うかがいいと思います。
0. ### [番外編] ubuntuに入れたnodeがみつからない
apt-getだと```nodejs```コマンドが使用できるようになります。```node```コマンドではありません。
nodesourceを使っても同じです。
以下の記事のように、nodeと名前を変えるとnodeコマンド及びnodeコマンドを探す各種ツール等が使えます。
逆に、後々何かおかしい場合は、以下の手続きを行ったことがもしかしたら何かの原因になるかもしれないので、このような処理を行ったことは記録しておきましょう。
##### [Ubuntu でapt を使用してNode.js をインストールする3 つの方法(Ubuntu 15.04, Ubuntu 14.04.2 LTS)](http://qiita.com/TsutomuNakamura/items/7a8362efefde6bc3c68b)


上記で解決しない場合は正しくダウンロードされていない可能性があります。
.pyenvディレクトリ等がみつからないなどの場合は、ダウンロードコマンドを見直してみるなどをお勧めします。
