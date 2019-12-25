---
title: "Use homebrew services to manage services on MacOS"
layout: post
date: 2019-12-25 22:06
guid: urn:uuid:f766abd4-c95b-4439-8174-1cdce2c43bfd
author: "Lixiang"
tags:
  - Tool
---

[Homebrew Services](https://github.com/Homebrew/homebrew-services). It integrates Homebrew formulae with macOS' launchctl manager.

With homebrew services, we can manage the services so simplify.

#### Start

`brew services start service_name`

e.g.
Start the MySQL service at login with:

```shell
brew services start mysql
```

#### Stop

`brew services stop service_name`

e.g.
Stop the MySQL service with:

```shell
brew services stop mysql
```

#### Restart
`brew services restart service_name`

e.g.
Restart the MySQL service with:

```shell
brew services restart mysql
```

#### List
List all services managed by brew services with:

```shell
brew services list
```

#### Cleanup
Remove all unused services with:

```shell
brew services cleanup
```
