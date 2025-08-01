# Docker-Compose-Infras

存放日常服务开发过程中常用到的一些基础设施服务的`docker-compose.yml`容器配置

- apt-tools.md: 包含了一些 docker容器内的命令操作
- docker-helper.md: 包含了一些常用docker操作

## 常见的一些容器服务开发
```shell
$ tree -L 2
.
├── apt-tools.md
├── cache
│   └── redis-single
├── devops
│   └── prometheus-grafana
├── docker-helper.md
├── LICENSE
├── mq
│   └── kafka
├── README.md
├── search
│   ├── elk-cluster
│   └── elk-single
├── storage
│   ├── clickhouse
│   └── mysql-single
└── www
    ├── caddy
    └── nginx
```