NTEmacs64
=========

Windows版Emacs(通称NTEmacs)の64ビット版 version 24.3.92 (24.4 の pretest 版)

バイナリ説明
------------

**emacs24.3.92.zip** を展開すると **emacs24.3.92/** フォルダが出来るので **emacs24.3.92/bin/runemacs.exe** を実行します。

### 特記事項
* ソースには一切手を加ずにビルドしています
* MSYS2 (MSYS の改良版) を使用してビルドしているので Cygwin に依存していません
 * Emacs 上でのパスの扱いなどが自然になります
* gcc に **-Ofast -march=corei7 -mtune=corei7** を付けて最適化ビルドされています
* 画像対応させる為の最低限の DLL を同梱しています (**GIF, PNG, JPEG, TIFF**)
 * 本来は SVG の表示にも対応可能ですが、依存 DLL(主に GTK+ 関連) が多すぎるので含めていません
* **libxml2, GnuTLS, D-Bus** の DLL も同梱しています
 * elisp で実装されたテキストブラウザ M-x eww も動作確認済みです
 * D-Bus は Linux 環境では便利な機能ですが、Windows 上ではほぼ使い道がありませんが、ビルドが通ったため同梱しています

### 注意事項
* configure でビルドした都合上 NTEmacs24.3 とはフォルダ構成が変わっています
 * DDSKK がフォルダ構成を認識できないようなので make する前に SKK-CFG の書き換えが必要になります
* movemail.exe で POP3 が使用出来ません (使ってる人は居ないと思いますが…)
 * Gnus は elisp で POP3 の通信を行うので movemail.exe は必須ではありません
* IMEパッチは適用していないので MS-IME などを使用した日本語入力に問題があります
 * これは公式ビルドであっても同じです
 * 「Google日本語入力」にするとパッチ無しでスムーズに日本語が入力できるようです (未確認)

ビルド方法
----------

<http://alpha.gnu.org/gnu/emacs/pretest/emacs-24.3.92.tar.xz>
を、ソースに一切手を加ずにビルドします。

### MSYS2 のインストール
<http://sourceforge.net/projects/msys2/>
から **msys2-x86_64-20140704.exe** を取得しインストールします。

### 64ビット環境用のシェルの起動
インストールディレクトリ(c:/msys64)直下の **mingw64_shell.bat** を起動します。

### ビルド関連パッケージのインストール
    $ pacman -S base-devel
    $ pacman -S mingw-w64-x86_64

    どっちもかなり時間が掛かります

### ビルドとインストール
    $ tar xvJf emacs-24.3.92.tar.xz
    $ cd emacs-24.3.92/

    ソースを展開してディレクトリに移動します

    $ export MSYSTEM=MINGW32

    デフォで MINGW64 になって configure がコケるので騙す為です (32bitビルドになる訳ではありせん)
    これがミソ
    
    $ CFLAGS='-Ofast -march=corei7 -mtune=corei7' ./configure --without-pop

    --without-pop すると movemail.exe で POP が使えなくなりますが movemail.exe のビルドがコケるので仕方なく…
    (原因は判明していますが、ソースいじらずに通す方法を思案中)
    最適化オプションは適当に
    ※ --prefix=c:/emacs24.4 と指定した時に site-lisp/ が認識されない不具合が発生したので --prefix は付けません

    $ make bootstrap && make install

    これでおしまい (make install-strip するとなぜか exe が壊れます…原因調査中)
    
    --prefix を付けないと c:/msys64/usr/local/ 以下にインストールされています

### 今後の予定
* 不具合の修正
 * ~~起動時に site-lisp/ が無視されている (ddskk をインストールしても、起動時に設定が有効にならない)~~
 * movemail.exe の POP3 対応 (使ってる人は居ないと思うので、優先度低)
* IME パッチの適用 (基本的にソースには一切手を加えない方針なので、優先度は低)
* 未パッチ版とパッチ適用版を作成して、movemail.exe の POP3 対応と IME パッチを適用する (優先度高)
 * IME パッチは <https://gist.github.com/ganta/5139150> を使用させてもらうかも
