__この記事は2016/7/18時点のPreview Build 14390に基づきます。__
- 自分の検証範囲での動作確認のため、環境や処理内容やビルドバージョンによっては動作しません。
- 内容に誤りや検証不足の項目があった場合はご指摘いただけますと幸いです。

# 概要
- bash on windowsを__windowsから起動すると__`chsh`によるログインシェル変更が適用されず`/bin/bash`が呼ばれる。(sshでログインすれば適用される。)
- bash起動時の処理の中に、zshを起動する処理を書く。
- `/etc/profile`に書いても良いが、`/bin/bash`を書き換えるのが速そうなのでこちらを変更する。

# 手順
- `sudo apt-get install zsh`等で`/bin/zsh`にインストールする。
- `/bin/bash`を`/bin/bashbin`等に名前を変更する。(念のため、他にもバックアップを作成すると良いです。)
- `/bin/bash`を以下のように記述する。

```bash
#!/bin/sh
if [ $SHELL = /bin/bash ]; then
  export SHELL=/bin/zsh
  exec $SHELL
else
  /bin/bashbin $*
fi
```

- `sudo chmod 755 /bin/bash`で権限を変更する。

(失敗した際は`C:\Users\{$USER}\AppData\Local\lxss\rootfs\bin`のbashを元のものに戻しましょう。)

# 補足
- `/etc/profile`を読むより前なので、そちらを書き換えるより速いはず。
- `bash`コマンドでのインストールスクリプト等の実行も問題なさそう。(参考元では動かなかったため)
- tmuxは$SHELLのshellで起動するので、はじめからzshが起動する。
- `exec $SHELL -l`でshellを再起動した場合も同じくzshが起動する。
- 参考元と同様に`pstree`コマンドを実行するとはじめからzshが動いていることになっている。
- /bin/bashは最初にしか読まれていないようである。(プリントデバッグで検証。)

# 参考
- [Ubuntu on Windows で zshをデフォルトシェルにする - Qiita](http://qiita.com/nsmr0604@github/items/7dd38faed1abd9189a83)
- [【シェル芸人への道】Bash on Windowsを探検する - Qiita](http://qiita.com/t_nakayama0714/items/9d7cfc5029a9b2ea423d)
