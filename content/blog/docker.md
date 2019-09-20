---
title: "Docker镜像名称"
date: 2019-09-20T16:35:41+08:00
---

  docker的镜像名称可能包含一个可选的registry域名。如果没有，则使用docker.io。
因为镜像名称并没有包含https://之类的协议前缀，那docker是怎么区分这个镜像名称是否包含了registry域名的呢？ 
根据[docker的开源registry的文档](https://docs.docker.com/registry/spec/api/)的说明：

> Classically, repository names have always been two path components where each path component is less than 30 characters. The V2 registry API does not enforce this. The rules for a repository name are as follows:
>
> 1. A repository name is broken up into path components. A component of a repository name must be at least one lowercase, alpha-numeric characters, optionally separated by periods, dashes or underscores. More strictly, it must match the regular expression `[a-z0-9]+(?:[._-][a-z0-9]+)*`.
> 2. If a repository name has two or more path components, they must be separated by a forward slash (“/”).
> 3. The total length of a repository name, including slashes, must be less than 256 characters.

根据第一点的正则，一个域名也是一个合法的仓库名称。这就让人疑惑了，example.com/abc到底是example.com服务器上一个名为abc的镜像呢，还是docker.io上的镜像呢？
去https://hub.docker.com/signup地址终于找到了答案，在Docker ID入非字母数字的字符时，提示“Please use only alphanumeric characters.”。原来虽然docker的开源registry，允许`._-`字符作为仓库名称，但是hub.docker.com是不允许任何符号作为Docker ID的。所以根据自己的猜测和通过`docker push`实验，总结如下：

1. 如果镜像名没有包含"/"，则自动加上"docker.io/library/"前缀，并使用docker.io作为registry。
2. 如果镜像名包含"/"，并且第一个"/"前的部分不包含"."，则自动加上"docker.io/"前缀，并使用docker.io作为registry。
3. 如果镜像名包含"/"，并且第一个"/"前的部分包含"."，则使用改部分作为registry。

docker这样的设计，让官方的镜像名称更简洁了。只是也让我费了好多脑筋。

