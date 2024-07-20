---
title: "Docker ci 模版"
description: "Lorem ipsum dolor sit amet"
pubDate: "Jul 20 2024"
heroImage: "https://media.licdn.com/dms/image/D4D12AQGlzVCppSBdoA/article-cover_image-shrink_720_1280/0/1685105557545?e=2147483647&v=beta&t=BzgfLTqi5zicmWqE-oLMxMNpPaBPDwyDJ7ShPtCjLM0"
---

`Github Action` 的工作流模板

```js
name: Docker Image CI

on:
  push:
    branches: ['dev']
  pull_request:
    branches: ['dev']

jobs:
  build-push-image:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      # 为构建 Docker 镜像设置 Buildx，可以提高构建速度和兼容性
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      # 自动获取当前日期并作为环境变量
      - name: Get the current date
        id: get-date
        run: echo "DATE=$(date +'%Y%m%d')" >> $GITHUB_ENV

      - name: Build the Docker image
        run: docker build . --file Dockerfile --tag uvestor_api_server:${{ env.DATE }}

      - name: Tag the Docker image
        run: docker tag uvestor_api_server:${{ env.DATE }} docker-registry.lovemypet.asia/uvestor_api:${{ env.DATE }}

      - name: Push the Docker image
        run: docker push docker-registry.lovemypet.asia/uvestor_api:${{ env.DATE }}

```
