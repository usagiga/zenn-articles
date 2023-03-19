---
title: "macOS と Homebrew で一瞬で環境をお引っ越し"
emoji: "🍻"
type: "tech"
topics: ["macOS", "Homebrew", "dotfiles"]
published: true
---

Homebrew には優秀なサブコマンド `brew bundle` があります。
これを使うことで、現在 Homebrew を使って管理しているパッケージのリストを出力したり、
リストからまとめてパッケージをインストールしたりできます。

たとえば、複数台の macOS で同じ環境を構築したいときに便利です！

この記事では、そのときの操作方法、どう使うと活かせるかに触れます。


# 手順

## 概要

1. `brew bundle dump --global` でパッケージ一覧を出力
2. `brew bundle --global` でパッケージ一覧からパッケージをまとめてインストール
3. おわり

## 古い macOS でやること

```sh
$ brew bundle dump --global
```

これで、 `~/.Brewfile` ができます。

```ruby:.Brewfile
tap "hashicorp/tap"
tap "homebrew/bundle"
tap "homebrew/cask"
tap "homebrew/core"
tap "homebrew/services"
tap "kayac/tap"
tap "yukiarrr/tap"
brew "anyenv"
brew "awscli"
brew "bash"
brew "direnv"
brew "docker", link: false
brew "docker-compose", link: false
brew "docker-credential-helper", link: false
brew "ffmpeg"
brew "fish"
brew "gh"
brew "ghq"
brew "gibo"
brew "git"
brew "imagemagick"
brew "jq"
brew "make"
brew "neofetch"
brew "peco"
brew "shellcheck"
brew "tmux"
brew "tree"
brew "hashicorp/tap/terraform"
brew "kayac/tap/ecspresso"
brew "yukiarrr/tap/ecsk"
cask "alacritty"
cask "amethyst"
cask "aws-vault"
cask "chromium"
cask "google-cloud-sdk"
cask "jetbrains-toolbox"
cask "slack"
cask "zoom"
```

Homebrew でインストールしているパッケージが列挙されていますね！


## 新しい macOS でやること

まずは前提となる

- Command Line Tools for Xcode
    - Xcode を使って開発する人は Xcode に入っているものを使うでもだいじょうぶです
- [Homebrew](https://brew.sh/)

をインストールします。

```sh
$ xcode-select --install
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

次に、さっきの `.Brewfile` をホームディレクトリ直下に配置して、以下のコマンドを実行します。

```sh
$ brew bundle --global
```

これで、古い macOS に入っていたのと同じパッケージを新しい macOS にもインストールすることができます。


# どう使うと便利なのか？

## パッケージ管理をなるべく Homebrew に寄せる

自分が使うもののほとんどをパッケージマネージャーに寄せてしまうと、
手動でインストールしなくて済みますし、環境移行完了後もバージョン管理がおてがるです。

とくに、 Homebrew Cask も使えばほとんどのアプリケーションが管理できます。自分の使っているアプリケーションが Homebrew で管理できないか、探してみるとよいでしょう。たとえばこれらは Homebrew で管理できます:

- iTerm 2
- Alacritty
- Chromium
- Firefox
- Slack
- Zoom
- JetBrains Toolbox


## dotfiles として管理する

`.Brewfile` をお手持ちの dotfiles に入れてしまいましょう！

自分の実装例ですが、 dotfiles のインストールスクリプト内で `brew bundle --global` を実行させることで、コマンド一発で同じ環境を構築できるようにしています。

- https://github.com/usagiga/dotfiles/blob/96c6716b47f8d5f0f24a8b11b9434fb961b616a6/darwin/.Brewfile
- https://github.com/usagiga/dotfiles/blob/96c6716b47f8d5f0f24a8b11b9434fb961b616a6/docs/install.sh
- https://github.com/usagiga/dotfiles/blob/96c6716b47f8d5f0f24a8b11b9434fb961b616a6/darwin/dotfiles.init.d/install_brew.sh

# 参考

- https://github.com/Homebrew/homebrew-bundle

## その他、便利そうな記事

- [Apple Silicon 未対応のアプリケーションをbrew bundleでインストールしないようにする](https://zenn.dev/mactkg/articles/71d09e350315f9)
    - 実は `.Brewfile` は Ruby の DSLになっているのですが、そこをうまく使った実装例です
