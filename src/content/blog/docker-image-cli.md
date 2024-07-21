---
title: "Docker 镜像脚手架"
description: "处理镜像到私有镜像仓库的 cli 脚手架"
pubDate: "Jul 21 2024"
heroImage: "https://miro.medium.com/v2/resize:fit:912/1*DPC6qsPLlL-PScG_TX_29w.png"
---

`shell` 脚本的具体实现。

```bash
#!/bin/bash

show_help() {
    echo "Usage: $0 [-h] [-n IMAGE_NAME] [-v IMAGE_VERSION]"
    echo
    echo "Options:"
    echo " -h Show help message"
    echo " -n Specify the image name"
    echo " -v Specify the image version"
}

version=latest
registry=example.com

# 处理带选项的参数
while getopts ":hn:v:" opt; do
    case ${opt} in
    h)
        show_help
        exit 0
        ;;

    n)
        name=$OPTARG
        ;;

    v)
        version=$OPTARG
        ;;

    \?)
        echo "Invalid option: -$OPTARG" >&2
        show_help
        exit 1
        ;;

    :)
        echo "Option -$OPTARG requires an argument." >&2
        show_help
        exit 1
        ;;
    esac
done

shift $((OPTIND - 1))

# 检查必须的参数
if [ -z "$name" ]; then
    echo "Error: Image name is required." >&2
    show_help
    exit 1
fi

# 执行docker pull命令
docker pull $name:$version
if [ $? -ne 0 ]; then
    echo "Failed to pull Docker image $name:$version." >&2
    exit 1
fi

# 执行docker tag命令
docker tag $name:$version $registry/$name:$version
if [ $? -ne 0 ]; then
    echo "Failed to tag Docker image $name:$version as $registry/$name:$version." >&2
    exit 1
fi

# 执行docker push命令
docker push $registry/$name:$version
if [ $? -ne 0 ]; then
    echo "Failed to push Docker image $registry/$name:$version." >&2
    exit 1
fi

echo "Docker image $name:$version successfully pulled, tagged, and pushed to $registry."

```

执行下述命令给 `shell` 脚本赋予可执行的权限

```bash
chmod +x cli.sh
```

生成可执行的 `cli` 脚手架

```bash
mv ./cli.sh /usr/local/bin/image_cli
```

最后即可执行使用
![截图](https://assets.lovemypet.asia/blog/iShot_2024-07-21_11.21.28.png)
