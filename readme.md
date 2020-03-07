# win10 vagrant/virtualbox+docker開発環境 構築手順


## 目的

Win10だと開発しにくいので、仮想化環境にUbuntuを動かしてdockerを適宜作動させることで  
linux環境下での開発を実現。参考URLをもとに作成。


## 参考URL

- [Ubuntu 18.04 の日本語版デスクトップ環境を Vagrant + VirtualBox で作成する](https://kmiya-bbm.hatenablog.com/entry/2019/01/07/194238)
- [Windows10+VirtualBox+VagrantでDockerはじめました](http://www.nct-inc.jp/engineer_blog/2807/)  
- [Docker 公式リファレンス for Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce-1)
- [Docker 公式リファレンス Dockerfile](http://docs.docker.jp/engine/reference/builder.html)
- [Docker 公式リファレンス Docker Compose](http://docs.docker.jp/compose/toc.html)
- [さくらのナレッジ Docker入門](https://knowledge.sakura.ad.jp/13265/)  


## 手順概要

1. vagrant/virtualbox で仮想化環境にUbuntuを構築
2. dockerをインストール
3. 各種dockerコンテナを構築

## 手順

### 1. vagrant/virtualbox で仮想化環境にUbuntuを構築  

   1. 公式サイトから[vagrant](https://www.vagrantup.com/) & [virtualbox](https://www.virtualbox.org/)をダウンロードする。  
   2. cmdから``` mkdir C:\Vagrant\Ubuntu18.04 ``` とディレクトリ作成(ディレクトリの場所は任意)。  
   3. そのディレクトリで以下のコマンド実行。    
      ```vagrant init bento/ubuntu-18.04``` ※ベンダー名/box名  
   1. こうするとvagrantfileができるから編集(vagrantfileはレポジトリのものをコピペ)  
   2. ```vagrant up``` で起動。  
   3. このときvagrantfileに記述されたshellが実行されている。
      apt によるGUIのダウンロードが1時間以上かかるが静かに見守る。 
      またこのとき virtualbox が立ち上がって Ubuntu のCUIログイン画面になるけどなにもしないでよい。  
      (id/pw vagrant/vagrant でログインはできる)  
   3. ダウンロードが終わったら terataerm か virtualboxのCUI画面(ログインせずに放っておいたもの)で接続してログインしてみる。
      ip : 127.0.0.1 もしくは vagrantファイル記述のIPアドレス(192.168.1.32)  
      port : 2222  
      id/pw : vagrant/vagrant  
   4. ログインに成功したら再起動する。
   5. そうするとvirtualboxでubuntuのログイン画面が現れるので上記のid/pwでログインする  
   6. これを参考に日本語設定をGUIで行う。[Ubuntu 18.04 その91 - 日本語環境の構築と確認・日本語入力の設定](https://kledgeb.blogspot.com/2018/04/ubuntu-1804-91.html)  
      日本語の入力はこれを参考に  
      [Ubuntu 18.04: iBus + Mozcで日本語入力する](https://www.hiroom2.com/2018/04/29/ubuntu-1804-ibus-mozc-ja/)  
      右上の画面で 日本語(Mozc)の入力モードが **あ** であることを確認。  
      ※Ctrl+space で変換するなら、Mozc プロパティのキーの設定(最初はMS-IME)から編集→変換中  
      入力キー Hankaku/Zenkaku のものを Ctrl+Spaceにする。そんで再起動。  


### 2. dockerをインストール  

   1. [公式サイト Get Docker Engine - Community for Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce-1) に従ってインストールする。  
   1. ```sudo docker run hello-world``` で動作確認。うまくいけば成功。  
   1. docker composeを導入。バージョンは適宜 [dockerのgithub](https://github.com/docker/compose/releases) をみて判断する  
    ```sudo curl -L https://github.com/docker/compose/releases/download/<version hogehoge>/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose```  
    ```sudo chmod +x /usr/local/bin/docker-compose```  
    バージョンを確認  
    ```docker-compose --version``` 表示すれば成功。  
   1. このままだと、dockerコマンドにsudoが必要。一般ユーザで使えるようにする  
      docker グループにユーザーを追加  
      ```sudo gpasswd -a $(whoami) docker```  
      docker.sock にグループ書き込み権限を付与  
      ```sudo chgrp docker /var/run/docker.sock```  
      ```sudo reboot```  
      ```docker ps -a```で確認。  
   
   ### 3. 各種dockerコンテナを構築

   0. まずサンプルとしてnginxコンテナを例示する(忘れたとき用)  
        1. コンテナの基礎  
        めんどくさいからこれみてね  [Docker入門（第二回）～Dockerセットアップ、コンテナ起動～](https://knowledge.sakura.ad.jp/13795/)  
        ただ注意点として、ホストOS(windows)からアクセスするのは、  
        **vagrantfaileに書いたIPアドレス＋コンテナで指定したポート番号**  というかたちになる  
        ex. 
        vagrantfile...ip:192.168.1.32  
        ```docker run -d --name nginx-container -p 8181:80 nginx```  
        としたときこんな感じのURLでアクセス 
        http://192.168.1.32:8181/  
        1. docker compose  
         docker network は、docker composeで起動したコンテナ郡はすべておなじネットワークに所属する。  
         これ見てね  
         [Docker入門（第六回）〜Docker Compose〜](https://knowledge.sakura.ad.jp/16862/)

   1. JupyterLab + python 環境  
      1. jupyter/scipy-notebook をもとに作成
      2. 基本的には任意のディレクトリにdockerフォルダをデプロイ
      3. jupyterlabで```docker-compose up --build``` で最初はビルド
      4. 次からは```docker-compose up/down```
      
## TODO
- 共有フォルダの設定の見直し
- ポート設定の見直し
- IPアドレス設定の見直し

