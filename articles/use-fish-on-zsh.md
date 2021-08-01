---
title: "zsh からおおまかに設定を引き継いでターミナルで fish を使う"
emoji: "🐟"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["fish"]
published: false
---

[fish shell](https://fishshell.com/) が便利らしいと聞いたし使ってみたいけど、

- `fish` をターミナルのデフォルトのシェルで使うとWMが立ち上がらないとかの不具合が出て困ったり
    - Manjaro(GNOME) とかで起きます
- `.zshrc` に書いた環境変数やコマンド群を設定し直すのが面倒だったり

してちょっと手が伸びない……

という人に向けて、 `zsh` ( `.zshrc` ) をログインシェル・各種設定に使ったまま、 `fish` を使う設定を書いておきます。


# こうする

`.zshrc` の **末尾に** このように書きます。

```sh:.zshrc
if [[ -o interactive ]]; then
    exec fish
fi
```


# なぜこうなるのか

## 環境変数を引き継ぐ

`exec fish` とすることで、環境変数を引き継いだ状態で `fish` を起動することができます。

つまり、 `.zshrc` に書いてある既存の環境変数の設定なんかをすべて引き継いで起動できるというわけです。


## POSIX sh に依存しているものを避ける

一部のWMやディストロでは POSIX 互換なシェル(ないし bash)で動くことが前提のシェルスクリプトが使われていることがあります。 `fish` は POSIX 非互換なシェルなので、そういったシステムに対してデフォルトシェルを `fish` に設定するとエラーが発生してしまうことがあります。

これを避けるには、 POSIX sh が依存していないタイミングでのみ `fish` を起動する必要があります。それいつ？という感じですが、たいていの場合は「インタラクティブシェルとして呼び出されているとき(≒ターミナルエミュレータから呼び出されているとき)」です。

`zsh` では `-o interactive` でインタラクティブシェルかどうかを判定できます。これを使って、POSIX sh 依存のものが動かないタイミングで `fish` を起動するようにしています。


# bash ではこうする

デフォルトシェルが `bash` のディストロ(ほとんどですね)では、以下の記事を読むとよいです。

- [fish をデフォルトシェルに設定しない (fish - ArchWiki)](https://wiki.archlinux.jp/index.php/Fish#fish_.E3.82.92.E3.83.87.E3.83.95.E3.82.A9.E3.83.AB.E3.83.88.E3.82.B7.E3.82.A7.E3.83.AB.E3.81.AB.E8.A8.AD.E5.AE.9A.E3.81.97.E3.81.AA.E3.81.84)

実はあまり意識しなくてもよくて、 `.bashrc` に `exec fish` と書くだけでよかったりするのですが……。
ちゃんとやる場合の戦略は本記事と同様、インタラクティブシェルかどうかを判定する流れになるようです。


# こんなときは

## ターミナル・WM が起動しなくなった！

`fish` を使うようにしたことでターミナルを開く・WMを起動するのに失敗することがあります。
以下のような方法で復帰ができるかもしれません。

- ターミナルの設定を操作し、起動時のコマンドを `sh` や他のシェルを実行し、設定変更を試みる
    - zsh がデフォルトシェルの人は bash がまだ使える可能性が高いです
- Ctrl + Alt + F2 (, F3, F4...) で他の tty への切り替えて、設定変更を試みる
- 適当なLinuxのブートディスクを用意して、それを使って設定変更を試みる
    - GUIがいい人は Ubuntu のインストールディスクが楽です
    - CLIでいい人は ArchLinux のインストールディスクで `arch-chroot` するのが楽です


# おわりに

MacOSだと単に `exec fish` で問題なかったのですが、
Manjaroに同じ `.zshrc` を食わせたらログインできなくなり、
悲しみを感じました。

`zsh` はログインシェルであろうと非ログインシェルであろうと `.zshrc` を読もうとするのでこういうハマりがちょくちょく発生します。


# References

- `zsh` の `exec`
    - https://zsh.sourceforge.io/Doc/Release/Shell-Builtin-Commands.html
- `zsh` の `test -o`
    - https://zsh.sourceforge.io/Doc/Release/Conditional-Expressions.html#Conditional-Expressions