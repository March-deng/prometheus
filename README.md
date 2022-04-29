# Prometheus


关于Prometheus的详细文档，访问[prometheus.io](https://prometheus.io)。

Prometheus，作为一个CNCF的毕业项目，是一个系统和服务监控系统。它以给定的间隔从配置好的目标上收集指标，对规则表达式进行求值并展示其结果，而且在给定条件满足时出发告警。


让Prometheus有别于其他指标和监控系统的功能室：



- 一个多维度的数据模型，时序数据由指标名称和一组标签所定义
- PromQL, a **powerful and flexible query language** to leverage this dimensionality
- PromQL，一个强大而又灵活的查询语言，以提升这多多维度的表达力。
- 不依赖于分布式存储。一个服务器节点就是自洽的。
- 时序数据收集基于HTTP 拉数据的方式
- 对于批量任务来说，通过一个中间网关推送指标数据也是支持的。
- 监控目标可动态发现也可静态配置。
- 多种图形化和大盘化展示的支持
- 支持层级式或水平式的联邦集群。


## Architecture overview

![](https://cdn.jsdelivr.net/gh/prometheus/prometheus@c34257d069c630685da35bcef084632ffd5d6209/documentation/images/architecture.svg)

## Install

There are various ways of installing Prometheus.

### Precompiled binaries

Precompiled binaries for released versions are available in the
[*download* section](https://prometheus.io/download/)
on [prometheus.io](https://prometheus.io). Using the latest production release binary
is the recommended way of installing Prometheus.
See the [Installing](https://prometheus.io/docs/introduction/install/)
chapter in the documentation for all the details.

### Docker images

Docker images are available on [Quay.io](https://quay.io/repository/prometheus/prometheus) or [Docker Hub](https://hub.docker.com/r/prom/prometheus/).

You can launch a Prometheus container for trying it out with

    $ docker run --name prometheus -d -p 127.0.0.1:9090:9090 prom/prometheus

Prometheus will now be reachable at http://localhost:9090/.

### Building from source

To build Prometheus from source code, You need:
* Go [version 1.16 or greater](https://golang.org/doc/install).
* NodeJS [version 16 or greater](https://nodejs.org/).
* npm [version 7 or greater](https://www.npmjs.com/).

You can directly use the `go` tool to download and install the `prometheus`
and `promtool` binaries into your `GOPATH`:

    $ GO111MODULE=on go install github.com/prometheus/prometheus/cmd/...
    $ prometheus --config.file=your_config.yml

*However*, when using `go install` to build Prometheus, Prometheus will expect to be able to
read its web assets from local filesystem directories under `web/ui/static` and
`web/ui/templates`. In order for these assets to be found, you will have to run Prometheus
from the root of the cloned repository. Note also that these directories do not include the
React UI unless it has been built explicitly using `make assets` or `make build`.

An example of the above configuration file can be found [here.](https://github.com/prometheus/prometheus/blob/main/documentation/examples/prometheus.yml)

You can also clone the repository yourself and build using `make build`, which will compile in
the web assets so that Prometheus can be run from anywhere:

    $ mkdir -p $GOPATH/src/github.com/prometheus
    $ cd $GOPATH/src/github.com/prometheus
    $ git clone https://github.com/prometheus/prometheus.git
    $ cd prometheus
    $ make build
    $ ./prometheus --config.file=your_config.yml

The Makefile provides several targets:

  * *build*: build the `prometheus` and `promtool` binaries (includes building and compiling in web assets)
  * *test*: run the tests
  * *test-short*: run the short tests
  * *format*: format the source code
  * *vet*: check the source code for common errors
  * *assets*: build the React UI

### Service discovery plugins

Prometheus is bundled with many service discovery plugins.
When building Prometheus from source, you can edit the [plugins.yml](./plugins.yml)
file to disable some service discoveries. The file is a yaml-formated list of go
import path that will be built into the Prometheus binary.

After you have changed the file, you
need to run `make build` again.

If you are using another method to compile Prometheus, `make plugins` will
generate the plugins file accordingly.

If you add out-of-tree plugins, which we do not endorse at the moment,
additional steps might be needed to adjust the `go.mod` and `go.sum` files. As
always, be extra careful when loading third party code.

### Building the Docker image

The `make docker` target is designed for use in our CI system.
You can build a docker image locally with the following commands:

    $ make promu
    $ promu crossbuild -p linux/amd64
    $ make npm_licenses
    $ make common-docker-amd64

*NB* if you are on a Mac, you will need [gnu-tar](https://formulae.brew.sh/formula/gnu-tar).

## Using Prometheus as a Go Library

### Remote Write

We are publishing our Remote Write protobuf independently at
[buf.build](https://buf.build/prometheus/prometheus/assets).

You can use that as a library:

```shell
$ go get go.buf.build/protocolbuffers/go/prometheus/prometheus
```

This is experimental.

### Prometheus code base

In order to comply with [go mod](https://go.dev/ref/mod#versions) rules,
Prometheus release number do not exactly match Go module releases. For the
Prometheus v2.y.z releases, we are publishing equivalent v0.y.z tags.

Therefore, a user that would want to use Prometheus v2.35.0 as a library could do:

```
$ go get github.com/prometheus/prometheus@v0.35.0
```

This solution makes it clear that we might break our internal Go APIs between
minor user-facing releases, as [breaking changes are allowed in major version
zero](https://semver.org/#spec-item-4).

## React UI Development

For more information on building, running, and developing on the React-based UI, see the React app's [README.md](web/ui/README.md).

## More information

  * Godoc documentation is available via [pkg.go.dev](https://pkg.go.dev/github.com/prometheus/prometheus). Due to peculiarities of Go Modules, v2.x.y will be displayed as v0.x.y. 
  * You will find a CircleCI configuration in [`.circleci/config.yml`](.circleci/config.yml).
  * See the [Community page](https://prometheus.io/community) for how to reach the Prometheus developers and users on various communication channels.

## Contributing

Refer to [CONTRIBUTING.md](https://github.com/prometheus/prometheus/blob/main/CONTRIBUTING.md)

## License

Apache License 2.0, see [LICENSE](https://github.com/prometheus/prometheus/blob/main/LICENSE).


[hub]: https://hub.docker.com/r/prom/prometheus/
[circleci]: https://circleci.com/gh/prometheus/prometheus
[quay]: https://quay.io/repository/prometheus/prometheus
