NTEmacs64
=========

Windows版Emacs(通称NTEmacs)の64ビット版 (version 24.3.92)

バイナリ説明
------------

**emace.zip** を展開すると **emacs/** フォルダが出来るので **emacs/bin/runemacs.exe** を実行します。

### 特記事項
* ソースには一切手を加ずにビルドしています
* MSYS2 (MSYSの改良版) を使用してビルドしているので Cygwin に依存していません
* gcc に **-Ofast -march=corei7 -mtune=corei7** を付けて最適化ビルドされています
* 画像対応させる為の最低限の DLL を同梱しています (**GIF, PNG, JPEG, TIFF**)
 * 本来は SVG の表示にも対応可能ですが、依存 DLL(主にGTK+関連) が多すぎるので含めていません
* **libxml2, GnuTLS** の DLL も同梱しています
 * elisp で実装されたテキストブラウザ M-x eww も動作確認済みです

### 注意事項
* configure でビルドした都合上 NTEmacs24.3 とはフォルダ構成が変わっています
* 起動時に site-lisp/ が無視されています
 * ddskk をインストールしても、起動時に設定が有効になりません
      * 明示的に以下の設定が必要です    
      ```emacs-lisp
      (add-to-list 'load-path "c:/emacs/share/emacs/site-lisp/skk")    
      (require 'skk-autoloads)    
      ```
 * package.el でインストールしたものは問題ありません
* movemail.exe で POP が使用出来ません (使ってる人は居ないと思いますが…)
* IMEパッチは適用していないので、日本語入力に問題があります

ビルド方法
----------

<http://alpha.gnu.org/gnu/emacs/pretest/emacs-24.3.92.tar.xz>
を、ソースに一切手を加ずにビルドします。

### MSYS2 のインストール
<http://sourceforge.net/projects/msys2/>
から **msys2-x86_64-20140704.exe** を取得しインストールします。

### 64ビット環境用のシェルの起動
インストールディレクトリ直下の **mingw64_shell.bat** を起動します。

### ビルド関連パッケージのインストール
    $ pacman -S base-devel
    $ pacman -S mingw-w64-x86_64

    どっちもかなり時間掛かります

### ビルドとインストール
    $ export MSYSTEM=MINGW32

    デフォで MINGW64 になって configure がコケるので騙す為です (32bitビルドになる訳ではありせん)
    これがミソ
    
    $ tar xvJf emacs-24.3.92.tar.xz
    $ cd emacs-24.3.92/

    ソースを展開してディレクトリに移動します

    $ CFLAGS='-Ofast -march=corei7 -mtune=corei7' ./configure --prefix=c:/emacs24.4 --without-pop

    --without-pop すると movemail.exe で POP が使えなくなりますが movemail.exe のビルドが
    コケるので仕方なく… (ソースいじらずに通す方法を思案中)
    インストールディレクトリや最適化オプションは適当に

    $ make bootstrap && make install

    これでおしまい (make install-strip するとなぜかexeが壊れます…)

### 今後の予定
* 不具合の修正
 * 起動時に site-lisp/ が無視されている (ddskk をインストールしても、起動時に設定が有効にならない)
 * movemail.exe の POP 対応 (使ってる人は居ないと思うので、優先度低)
* IMEパッチの適用 (基本的にソースには一切手を加えない方針なので、優先度は低)
