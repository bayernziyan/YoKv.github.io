﻿﻿﻿﻿﻿﻿﻿﻿---
title: docker命令记录(持续更新)
date: 2018-03-24 17:30:27
categories:
- DevOps
---

## 删除所有停止的容器
```
docker rm $(docker ps -a -q)
```
## 删除所有停止的镜像

```
docker rmi $(docker images -q)
```

<!--more-->

## docker官方提供的练习服务器
登陆docker账号即可使用
[play-with-docker.com](https://labs.play-with-docker.com)
[教程](https://training.play-with-docker.com)







