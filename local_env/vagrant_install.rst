====================================================
ローカルPC上にVagrantの環境を構築してみる
====================================================

ローカルPCでの環境構築を行った際のメモ書きです。

.. warning:: 
   | Windows環境を前提とした説明になっています。
   | 他のOSを使用している場合は、本ページを参考に自分でアレンジして進めてください。
   
.. contents:: 目次
    :local:

-----------------------------
Vagrantとは
-----------------------------
* 各種Virtualマシンのセットアップを容易にするツール
  プログラマブルに設定できる(設定がRubyで書ける)
* 標準はVirtualBoxだったが、最近はVMWareやEC2に対応している

ローカル環境では、VirtualBoxを入れてみることにします。

-----------------------------
やってみよう
-----------------------------
| 以下のページを参考に進めてみます。
| http://knowledge.sakura.ad.jp/tech/1552/
| http://qiita.com/taiki45/items/b46a2f32248720ec2bae

-----------------------------
Vagrantのインストール
-----------------------------
| `Vagrantの公式サイト <http://www.vagrantup.com/downloads>`_ にインストーラがあるので、Windows用をDLしてインストールします。
| 今回使用したバージョンは「1.6.2」でした。
| 特に設定するところもない(インストールディレクトリ)くらいなので、迷うことはないと思います。

.. warning::
    | マシン再起動しないと使用できるようにはなりません。
    | インストール完了のダイアログで何も考えずにOK押すと、マシン再起動がかかるので注意。

| マシン再起動後にコマンドプロンプトから 

:: 

    vagrant -v

を実行して、バージョン番号が表示されたらインストール完了です。

-----------------------------
VirtualBoxのインストール
-----------------------------
|  `VirtualBoxの公式サイト <https://www.virtualbox.org/wiki/Downloads>_` にインストーラがあるので、Windows用をDLしてインストールします。
| 今回使用したバージョンは「4.3.12」でした。
| 特に設定するところもない(インストールディレクトリ)くらいなので、迷うことはないと思います。

-----------------------------
boxファイルの選定
-----------------------------
| boxとは、Vagrantの設定ファイルやディスクイメージ、メタデータをまとめたファイルのこと。
| 今回は `Vagrantbox.es <http://www.vagrantbox.es/>`_ にあるboxを使うことにしました。
| 対象は「CentOS 6.4 x86_64 Minimal (VirtualBox Guest Additions 4.3.2, Chef 11.8.0, Puppet 3.3.1) 」にしてみました。

-----------------------------
Vagrant init
-----------------------------

``Vagrant init`` コマンドで、先程のboxファイルを使用したひな形を作成します。

::

	vagrant init centos64-x64 http://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.4-x86_64-v20131103.box

「centos64-x64」の部分はこの構成に付ける名前、その後ろに使いたいboxファイルのURLを設定します。

| 実行するとカレントディレクトリに「Vagrantfile」が作成されます。

----------------------------
vagrant up
----------------------------

``Vagrant up`` コマンドで、仮想マシンを立ち上げてみます。

、、とエラーになりました。

    .. image:: ../img/local_env/vagrant_up_error.jpg
        :width: 700px

| VirtualBoxにパス通せばいいみたいです。
| 参考URL：http://shoprev.hatenablog.com/entry/2013/05/22/151224
| 普通にインストールした場合「C:\Program Files\Oracle\VirtualBox」にいるので、ここをpathに追加しておきます。

| パス通して再実行で、立ち上げが始まりました。そしてエラーが。。。

    .. image:: ../img/local_env/vagrant_up_error_2.jpg
        :width: 700px

| でも ``vagrant status`` で確認するとrunningになっている。。

    .. image:: ../img/local_env/vagrant_status.jpg
        :width: 700px

| でも ``vagrant ssh`` すると ``ssh_exchange_identification: Connection closed by remote host`` と言われる。。

::

  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
  end

といれるとGUIで起動状況が分かるらしいので、入れてみたらエラーが出た。。

| これ：http://qiita.com/rch850/items/ba254063df4a9ff15354 経由の、これ：http://d.hatena.ne.jp/yohei-a/20110124/1295887695
| でも、BIOSがうまく起動しないので、めんどくさくなってやめました。

| 32bitならいいのかしら？

::

	vagrant init centos64-32 http://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.4-i386-v20131103.box

これだと先ほどのエラー系は全て出なくなり、起動およびssh接続ができた。

    .. image:: ../img/local_env/vagrant_up_32bit.jpg
        :width: 700px

ということは、やはりBIOS設定を変えれば64bitも使えるということだ。。
