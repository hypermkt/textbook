# 採用技術と研修での使い方

## Docker

[Docker](https://www.docker.com/)はオープンソースのコンテナ型仮想化エンジンです。
Dockerではアプリケーションとその実行環境を「コンテナ」と呼ばれる単位でパッケージングでき、そこから作ったイメージ（コンテナの元となるもの）をそのまま別のマシンへ移して動かすことが可能です。チーム内に配布すれば全員が同じ環境が使用できますし、イメージを本番環境へデプロイして動かす事例も発表されています。Web開発研修で使用したherokuも、[Dockerイメージをそのままデプロイできる仕組みを持っています](https://devcenter.heroku.com/changelog-items/926)。
ペパボでは、社内CIでアプリケーションのテストを行う際や、SUZURIの画像合成サーバを手元で動かす際にDockerが利用されています。また、詳しくは[DockerとPuppet](docker_puppet.md)で述べますが、Dockerコンテナはその仕組み上、[VirtualBox](https://www.virtualbox.org/)などによる仮想マシンよりも起動が高速で、軽量VMとしての側面も持ち合わせています。先の話なので断言はできませんが、Dockerベースのデプロイが安定して稼働すれば、手元のDockerコンテナからイメージをつくり、それをそのままNyahの上にデプロイすればサービスが動く未来が来るかもしれません。
2015年の研修ではVirtualBoxと[Vagrant](https://www.vagrantup.com/)でローカル環境をつくりましたが、以上の実績と優位性、将来性から、今年はDockerを使用します。

## Puppetによる構成管理

インフラの構成管理とは、サービスを正常に提供することができるようにサーバの状態を管理することを指します。全てをシェルスクリプトで管理するのも構成管理の1つのやり方ですが、近年は構成管理ツールと呼ばれるもの使って、サーバがあるべき状態をコードで管理することが一般的です。サーバがあるべき状態をロールと呼び、多くの場合ロールは複数存在します。インフラ構築時に構成管理ツールが担うのは、サーバを立ててOSをインストールした後の、ミドルウェアのインストールや設定変更をしてサービスを提供できるようにする過程です。継続的に構成管理ツールを適用して、あるべき状態と実際の状態の乖離を常に防ぐ運用が行われる場合もあります。
構成管理ツールとして、ペパボでは[Puppet](https://puppet.com/product/how-puppet-works)が主に使われており、この研修でもこれを使います。研修の目的としては、ツールの使い方を学ぶことよりも、自動化や構成管理を実際に行い、その利点を学んでもらうことが重要ではありますが、実際のサービスに用いられているPuppetマニフェストなどを参考にしながら、Puppet自体についても学んで欲しいと思います。[DockerとPuppet](docker_puppet.md)でも触れていますが、この研修ではStandaloneと呼ばれる方式で使っていきます。
なお、他にも[Chef](https://www.chef.io/chef/), [Itamae](http://itamae.kitchen/), [Ansible](https://www.ansible.com/)などが代表的な構成管理ツールとして知られています。興味のある人は調べてみてください。

## Serverspecとinfratasterによるテスト

Web開発研修でテストを書いたように、Webオペレーション研修でもテストを書いていきます。使用するツールは[Serverspec](https://github.com/mizzy/specinfra)と[infrataster](https://github.com/ryotarai/infrataster)で、それぞれ異なる目的で使用します。
Serverspecでは、内部の仕組みがわかっている前提で、設定ファイルが配置されているか、その内容が適切か、といった項目をテストしていきます。一方、infratasterでは、外から見たときの動作をテストします。例としては、Webサーバにあるリクエストを送ったときに、想定した応答が返ってくるか、などです。

## ベンチマーク

インフラを構築していく過程で、何度かベンチマークを取ってもらいます。

### 計測項目

自分たちが行った作業によって、Webサービスにどのような改善ができたかを計測してください。計測する項目は、1秒間に捌けたリクエスト数は必ず測ってもらいますが、他にも改善効果が見えそうな項目や、逆に、この数値に大きな変化は無いはずなのでそれを確認したいというものがあれば、適宜計測してください。
単純に大量のリクエストを送るのではなく、ユーザがアクセスするシナリオを決めて行うこともありますが、今回は発展課題とします。

### 計測条件

ベンチマークは、闇雲にリクエストを送ってスループットを見るわけではありません。同時リクエスト数や負荷をかける時間など、いくつか決めるべき項目があります。実際のサービスでは、過去のリクエスト数の記録などを元にこれらを決定しますが、今回のアプリでは決めようがありませんので、同時リクエスト数は50〜500程度、時間は10秒程度に設定してください。計測毎にバラバラでなければ良いです。

### 計測環境

本来は本番にできるだけ近い環境での計測が望ましいですが、以下の理由から今回は手元のDocker環境を使用してください。

- 参考値となるが、値の変化は確認できる
- 試行錯誤しやすい

### ベンチマークツール

ベンチマークツールとして何を使うかは、そのときのチームメンバーで話し合って選んでください。2016年現在の有名なベンチマークツールとしては、以下が挙げられます。

- [Apache Bench](https://httpd.apache.org/docs/2.4/programs/ab.html)
- [Gatling](http://gatling.io/#/)
- [wrk](https://github.com/wg/wrk)
- [h2load](https://nghttp2.org/documentation/h2load-howto.html)

## 参考

- [Docker基礎](http://www.slideshare.net/mainya/dockerdocker09-010)