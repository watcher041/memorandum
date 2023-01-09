
# 備忘録（asdf）

## asdf で必要なパッケージ（Linux）

### 事前準備
```
sudo apt install g++ make git curl
```

### php 環境構築に必要なペッケージ
```
sudo apt install locate autoconf bison re2c libxml2-dev libssl-dev libsqlite3-dev zlib1g-dev  libcurl4-openssl-dev libgd-dev  libonig-dev libpq-dev  libreadline-dev  libzip-dev libbz2-dev
```

### R 環境構築に必要なコマンド
```
sudo apt install libpcre2-dev gfortran
sudo ln -s /usr/include/locale.h /usr/include/xlocale.h
wget https://github.com/unicode-org/icu/releases/download/release-58-3/icu4c-58_3-src.tgz
tar xvfz icu4c-58_3-src.tgz
cd icu/source
./configure
make
sudo make install
```

### Amber で必要なパッケージ
```
sudo apt install libpcre3-dev libyaml-dev libevent-dev
```

### pythonに必要なパッケージ
```
sudo apt install gcc make zlib1g-dev libffi-dev libssl-dev libreadline-dev  libsqlite3-dev libbz2-dev
```

### rust で必要なパス
```
export PATH=$PATH:`asdf where rust`/bin
```
→ cargo install でインストールしたコマンドを実行するために必要（dieselなど）

### Amethystで必要なパッケージ
```
sudo apt install libasound2-dev libxcb-composite0-dev

sudo Xcode-select --install
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
```

### LaTeX のインストール

ソースからインストールする場合
```
wget http://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz
tar xvf install-tl-unx.tar.gz
cd install-tl-2*
sudo ./install-tl -no-gui

sudo ./install-tl -no-gui -profile installation.profile

sudo /usr/local/texlive/????/bin/*/tlmgr path add
```

コマンドで一発でインストール
```
sudo apt install texlive-lang-cjk texlive-fonts-recommended texlive-fonts-extra
```

vscodeでlatexを使う場合
```
"latex-workshop.latex.recipes": [
        {
        "name": "compile_command",
        "tools": [
            "platex",
            "dvipdfmx"
        ]
    }
    ],
    "latex-workshop.latex.tools": [
    {
        "name": "platex",
        "command": "/usr/bin/platex",
        "args": [
            "-kanji=utf8",
            "-interaction=nonstopmode",
            "%DOC%.tex"
        ]
    },
    {
        "name": "dvipdfmx",
        "command": "/usr/bin/dvipdfmx",
        "args": [
            "%DOC%.dvi"
        ]
    }
    ],
```
