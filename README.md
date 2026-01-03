# idp-cncf-stack

Creating IDP from scratch by using tools adopted by CNCF.

# Tech Stack

| Capability        | Tool            |
| ----------------- | --------------- |
| ContainerRegistry | GitHub Packages |

# Backstage

## 行ったこと

[1. Create your Backstage App](https://backstage.io/docs/getting-started/#1-create-your-backstage-app)を参考に`npx @backstage/create-app@latest`を実行し、backstage-app フォルダを作成した。

PostgreSQL を用意するのが面倒なので、暫定的にインメモリ DB を利用するように、Backstage 設定ファイル（app-config.production.yaml）を編集した。将来的にはマネージド DB を利用する予定。

[Host Build](https://backstage.io/docs/deployment/docker#host-build)を参考に、イメージビルド＆実行が可能な状態にした。

イメージビルド＆プッシュ用の CI ワークフローを作成した。コンテナレジストリとして GitHub Packages を利用している。

## イメージビルド及び実行手順

```sh
docker image build . -f packages/backend/Dockerfile --tag backstage
```

```sh
docker run -it -p 7007:7007 backstage
```

# kind

kind/kind-config.yaml に kind 設定ファイルを作成した。

```sh
# クラスタ作成
kind create cluster --config kind/kind-config.yaml --name dev-cluster
```

```sh
# クラスタ削除
kind delete cluster -n dev-cluster
```

## k8s

### デプロイ手順

必要な Secret リソースを imperative に作成する
manifest フォルダ配下のマニフェストを apply する

port-forwarding してブラウザからアクセスする

```sh
kubectl port-forward --namespace=backstage svc/backstage 8090:80
```

### Secret 作成

#### GitHub 統合

Backstage の GitHub 統合のためには GtHub Token を app-config.yaml 経由 or Secret リソースとして渡す必要がある。必要な権限は[Token scopes](https://backstage.io/docs/integrations/github/locations/#token-scopes)を参照。

GitHub Token の代わりに[GitHub Apps を利用](https://backstage.io/docs/integrations/github/locations/#authentication-with-github-apps)も可能らしいが一旦 PAT 利用で進める。

```sh
k create secret generic backstage-secrets -n backstage --from-literal=GITHUB_TOKEN=$GITHUB_TOKEN_BACKSTAGE_INTEGRATION
```

#### Private レジストリからのコンテナイメージ Pull

※注: 現状はレジストリを Public にしているため下記操作は不要

```sh
k create secret docker-registry ghcr-image-pull-secret -n backstage --docker-server=ghcr.io --docker-username=T41-Ch1 --docker-password=GITHUB_TOKEN=$GITHUB_TOKEN_GHCR_PULL_IMAGE --docker-email=$MY_EMAILADDRESS
```
