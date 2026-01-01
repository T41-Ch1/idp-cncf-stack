# idp-cncf-stack
Creating IDP from scratch by using tools adopted by CNCF.


# Tech Stack

|Capability|Tool|
|--|--|
|ContainerRegistry|GitHub Packages|

# Backstage

## 行ったこと

[1. Create your Backstage App](https://backstage.io/docs/getting-started/#1-create-your-backstage-app)を参考に```npx @backstage/create-app@latest```を実行し、backstage-appフォルダを作成した。

PostgreSQLを用意するのが面倒なので、暫定的にインメモリDBを利用するように、Backstage設定ファイル（app-config.production.yaml）を編集した。将来的にはマネージドDBを利用する予定。

[Host Build](https://backstage.io/docs/deployment/docker#host-build)を参考に、イメージビルド＆実行が可能な状態にした。

イメージビルド＆プッシュ用のCIワークフローを作成した。コンテナレジストリとしてGitHub Packagesを利用している。

## イメージビルド及び実行手順
```sh
docker image build . -f packages/backend/Dockerfile --tag backstage
```

```sh
docker run -it -p 7007:7007 backstage
```

# kind

kind/kind-config.yamlにkind設定ファイルを作成した。

```sh
# クラスタ作成
kind create cluster --config kind/kind-config.yaml --name dev-cluster
```

```sh
# クラスタ削除
kind delete cluster -n dev-cluster
```