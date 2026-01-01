# idp-cncf-stack
Creating IDP from scratch by using tools adopted by CNCF.


# Tech Stack

|Capability|Tool|
|--|--|
|ContainerRegistry|GitHub Packages|

# Backstageイメージビルド及び実行手順

```sh
docker image build . -f packages/backend/Dockerfile --tag backstage
```

```sh
docker run -it -p 7007:7007 backstage
```

[参考資料](https://backstage.io/docs/deployment/docker/#host-build)