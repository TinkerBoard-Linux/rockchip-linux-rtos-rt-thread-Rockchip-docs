# Rockchip RT-Thread Documents Buiding Guide

## 安装mkdocs

```shell
$ wget https://bootstrap.pypa.io/get-pip.py
$ python get-pip.py
$ pip install mkdocs
$ mkdocs --version
mkdocs, version 1.0.4 from /home/cmc/.local/lib/python2.7/site-packages/mkdocs (Python 2.7)
```

## 下载

   本文档工程是为了适配我司的 RT-Thread 工程（后简称 RTT ），其构建脚本对 RTT 的路径有依赖，所以请确保在 RTT 根目录去下载本工程。

```shell
$ cd /path/to/rtt
$ git clone "ssh://git@10.10.10.29:29418/rtos/rt-thread/Rockchip-docs" && scp -p -P 29418 git@10.10.10.29:hooks/commit-msg "Rockchip-docs/.git/hooks/"  #请把git换成你的用户名
$ ls -l
drwxrwxr-x  5 cmc cmc 4096 Mar  3 10:42 Rockchip-docs
```

## 生成

```shell
./build.sh soc_name                        #soc_name即你要生成的soc名字，如：rk2108
```

## 浏览文档

   生成的文档是 html 格式，所以用浏览器打开 ./site/index.html 即可。