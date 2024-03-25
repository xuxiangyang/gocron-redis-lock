# redislock

[![CI State](https://github.com/xuxiangyang/gocron-redis-lock/actions/workflows/go_test.yml/badge.svg?branch=main&event=push)](https://github.com/xuxiangyang/gocron-redis-lock/actions)
![Go Report Card](https://goreportcard.com/badge/github.com/xuxiangyang/gocron-redis-lock) [![Go Doc](https://godoc.org/github.com/xuxiangyang/gocron-redis-lock?status.svg)](https://pkg.go.dev/github.com/xuxiangyang/gocron-redis-lock)

## About

Copy from [go-co-op/gocron-redis-lock](https://github.com/go-co-op/gocron-redis-lock/) to use redis v8

## install

```
go get github.com/xuxiangyang/gocron-redis-lock/v2
```

## usage

Here is an example usage that would be deployed in multiple instances

```go
package main

import (
	"fmt"
	"time"

	"github.com/go-co-op/gocron/v2"
	"github.com/go-redis/redis/v8"

	redislock "github.com/xuxiangyang/gocron-redis-lock/v2"
)

func main() {
	redisOptions := &redis.Options{
		Addr: "localhost:6379",
	}
	redisClient := redis.NewClient(redisOptions)
	locker, err := redislock.NewRedisLocker(redisClient, redislock.WithTries(1))
	if err != nil {
		// handle the error
	}

	s, err := gocron.NewScheduler(gocron.WithDistributedLocker(locker))
	if err != nil {
		// handle the error
	}
	_, err = s.NewJob(gocron.DurationJob(500*time.Millisecond), gocron.NewTask(func() {
		// task to do
	}, 1))
	if err != nil {
		// handle the error
	}

	s.Start()
}
```

The redis UniversalClient can also be used

```go
package main

import (
	"fmt"
	"time"

	"github.com/redis/go-redis/v8"

	redislock "github.com/xuxiangyang/gocron-redis-lock/v2"
)

func main() {
	redisOptions := &redis.UniversalOptions{
		Addrs: []string{"localhost:6379"},
	}
	redisClient := redis.NewUniversalClient(redisOptions)
	locker, err := redislock.NewRedisLocker(redisClient, redislock.WithTries(1))
	if err != nil {
		// handle the error
	}
}
```
