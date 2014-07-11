NTEmacs64
=========

Windows版Emacs(通称NTEmacs)の64ビット版

バイナリ説明
------------

**emace.zip** を展開すると **emacs/** フォルダが出来るので **emacs/bin/runemacs.exe** を実行する

### 特記事項
* 画像対応させる為の最低限の DLL を同梱
 * 本来は SVG の表示にも対応可能だが、依存 DLL(主にGTK+関連) が多すぎるので含めていない
* libxml2, GnuTLS の DLL も同梱
 * elisp で実装されたテキストブラウザ M-x eww も動作確認済み

### 注意事項
* gcc に **-Ofast -march=corei7 -mtune=corei7** を付けて最適化ビルドされている
* configure でビルドした都合上 NTEmacs24.3 とはフォルダ構成が変わっている
* 起動時に site-lisp/ が無視されている
 * ddskk をインストールしても、起動時に設定が有効にならない
      * 明示的に以下の設定が必要    
      ```emacs-lisp
      (add-to-list 'load-path "c:/emacs/share/emacs/site-lisp/skk")    
      (require 'skk-autoloads)    
      ```
 * package.el でインストールしたものは問題無し
* movemail.exe で POP が使用出来ない (使ってる人は居ないと思うけど…)
* IMEパッチは適用していないので、日本語入力に問題がある

ビルド方法
----------

<http://alpha.gnu.org/gnu/emacs/pretest/emacs-24.3.92.tar.xz>
を、ソースに一切手を加ずにビルドする。

### MSYS2をインストールする
<http://sourceforge.net/projects/msys2/>
から **msys2-x86_64-20140704.exe** を取得しインストールする。

### 64ビット環境用のシェルを起動する
インストールディレクトリ直下の **mingw64_shell.bat** を起動する。

### ビルド関連パッケージのインストール
    $ pacman -S base-devel
    $ pacman -S mingw-w64-x86_64

    どっちも超時間掛かる

### ビルドとインストール
    $ export MSYSTEM=MINGW32

    デフォでMINGW64になってconfigureがコケるから騙す為 (32bitビルドになる訳ではない)
    これがミソ
    
    $ tar tvJf emacs-24.3.92.tar.xz
    $ cd emacs-24.3.92/

    ソースを展開して移動する

    $ CFLAGS='-Ofast -march=corei7 -mtune=corei7' ./configure --prefix=c:/emacs24.4 --without-pop

    --without-pop すると movemail.exe でPOPが使えなくなるが movemail.exe のビルドが
    コケるから仕方なく… (ソースいじらずに通す方法を思案中)
    インストールディレクトリや最適化オプションは適当に

    $ make bootstrap && make install

    これでおしまい (make install-strip するとなぜかexeが壊れるw)

### 今後の予定
* ~~ビルドしたものをコミットする~~
* 不具合の修正
 * 起動時に site-lisp/ が無視されている (ddskk をインストールしても、起動時に設定が有効にならない)
 * movemail.exe の POP 対応
* IMEパッチの適用
