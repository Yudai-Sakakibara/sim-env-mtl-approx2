# RISC-V クロスコンパイラの作成スクリプト

## クイックスタート

    ```bash
    sudo make install_deps  # ビルドに必要なものを apt で入れる
    make clone              # gcc のコードをクローンしてくる
    
    # ビルド
    make build_riscv64_linux_7_1_1 -j8        # 7.1.1 linux
    make build_riscv64_elf_7_1_1 -j8          # 7.1.1 elf
    make build_riscv64_linux_8_1 -j8          # 8.1   linux
    make build_riscv64_elf_8_1 -j8            # 8.1   elf
    make build_riscv64_linux_11_1 -j8         # 11.1  linux
    make build_riscv64_linux_11_1_approx -j8  # 11.1  linux （アセンブラが近似命令に対応）
    make build_riscv64_elf_11_1 -j8           # 11.1  elf
    make build_riscv64_elf_11_1_approx -j8    # 11.1  elf （アセンブラが近似命令に対応）
    ```

## その他

* ${HOME}/opt/gcc/ 以下にインストールされるので，変更したい場合は必要に応じて make ファイルを書き換えて欲しい
* 失敗する場合は，make clean をしてからやり直す
* riscv-binutils-gdb についてなにかエラーが出た場合は，riscv-binutils-gdb を1回丸っと消して再チェックアウトすれば直ることもある

## Dockerで行う場合

「クイックスタート」の手順の代わりに以下を行うことで、GCC 11.1 LinuxとGCC 11.1 ELF（いずれもアセンブラが近似命令に対応）のDockerイメージを構築できる。

    ```bash
    make docker-build  # Dockerイメージを構築する
    ```

このイメージをもとに新しいコンテナを作成して、そこでクロスコンパイラを実行することができる。

    ```bash
    make docker-run           # コンテナを作成して、その中のシェルにログインする。ここではクロスコンパイラのコマンドを実行できる（例：「riscv64-unknown-linux-gnu-gcc」や「riscv64-unknown-elf-gcc」）。コンテナ内の/workディレクトリにはこのREADME.mdが存在するがマウントされている。シェルからログアウトするとコンテナが削除される
    make docker-test-version  # コンテナを作成して、クロスコンパイラのバージョンを表示したのち、コンテナを削除する
    make docker-test-compile  # コンテナを作成して、クロスコンパイラでtest.cをコンパイルしたのち、コンテナを削除する
    ```

このREADME.mdが存在するディレクトリとは別のディレクトリにあるソースコードをコンパイルしたい場合は、そのディレクトリまで移動して、

    ```bash
    docker run -ti --rm -v $(pwd):/work -e USER_ID=$(id -u) -e GROUP_ID=$(id -g) shioyadan-sim-env-riscv-compiler:latest riscv64-unknown-linux-gnu-gcc foo.c
    ```

などとすれば、当該ディレクトリをマウントしたコンテナの作成・コンパイラの実行・コンテナの削除が一気に行われる。
