# Holy Lambda
[![Clojars Project](https://img.shields.io/clojars/v/io.github.FieryCod/holy-lambda.svg?logo=clojure&logoColor=white)](https://clojars.org/io.github.FieryCod/holy-lambda)
[![CircleCI](https://circleci.com/gh/FieryCod/holy-lambda/tree/master.svg?style=svg)](https://circleci.com/gh/FieryCod/holy-lambda/tree/master)
[![codecov](https://codecov.io/gh/FieryCod/holy-lambda/branch/master/graph/badge.svg)](https://codecov.io/gh/FieryCod/holy-lambda)
[![cljdoc badge](https://cljdoc.org/badge/io.github.FieryCod/holy-lambda)](https://cljdoc.org/d/io.github.FieryCod/holy-lambda/CURRENT)
[![Slack](https://img.shields.io/badge/Slack-holy--lambda-blue?logo=slack)](https://clojurians.slack.com/messages/holy-lambda/)
[![DockerHub](https://img.shields.io/docker/pulls/fierycod/graalvm-native-image.svg?logo=docker)](https://hub.docker.com/r/fierycod/graalvm-native-image)
[![MIT Licence](https://badges.frapsoft.com/os/mit/mit.svg?v=103)](https://opensource.org/licenses/mit-license.php)

---

The micro framework that integrates Clojure with AWS Lambda on either Java, Clojure Native, or Babashka runtime. 

**Supported runtimes**
  - Babashka
  - Native Clojure runtime
  - Java based Clojure runtime
  - (incoming) Node.js runtime for Clojurescript

**Stable releases**

``` clojure
io.github.FieryCod/holy-lambda                     {:mvn/version "0.2.2"}

;; Babashka runtime layer `0.4.6`
;; `:runtime:version` are the inner properties of `:holy-lambda/options` in bb.edn
:holy-lambda/options:runtime:version                             "0.1.2" 

;; Default retriever is built in holy-lambda. For `core.async` support use `async-retriever`
io.github.FieryCod/holy-lambda-default-retriever   {:mvn/version "0.0.6"}

;; Not supported in babashka runtime
io.github.FieryCod/holy-lambda-async-retriever     {:mvn/version "0.0.6"}

;; Babashka tasks docker images
fierycod/graalvm-native-image:ce                   ;; GraalVM CE 21.1.0
fierycod/graalvm-native-image:dev                  ;; GraalVM CE-dev 21.3.0-dev-20210706_1943 (https://github.com/graalvm/graalvm-ce-dev-builds/releases/)

;; In bb.edn :deps
io.github.FieryCod/holy-lambda-babashka-tasks      {:git/url     "https://github.com/FieryCod/holy-lambda"
                                                    :deps/root   "./modules/holy-lambda-babashka-tasks"
                                                    :sha         "f0fb9a181445a8b153f11895475ad76cf8a5da5e"}
```

[Jump here](https://cljdoc.org/d/io.github.FieryCod/holy-lambda/CURRENT/doc/tutorial) to learn more and start the journey with the Holy Lambda.

**Interceptors namespace is work in progress and subject to change. Consider it as an alpha.**

### Minimal code example

``` clojure
(ns some.ns
  (:gen-class)
  (:require 
    [fierycod.holy-lambda.core :as h]
    [fierycod.holy-lambda.interceptor :as i]
    [fierycod.holy-lambda.native :as native]
    [fierycod.holy-lambda.response :as hr]))

(i/definterceptor LambdaLogger
  {:enter (fn [request]
            (println "REQUEST:" request)
            request)
   :leave (fn [response]
            (println "RESPONSE:" response)
            response)})
 
(h/deflambda ExampleLambda
  "I can run on Java, Babashka or Native runtime..."
  < {:interceptors [LambdaLogger]}
  [{:keys [event ctx]}]
  (hr/text "Hello world"))
  
(native/entrypoint [#'ExampleLambda])
```

## Quickstart

Generate a new project from template via:

``` clojure
clojure -M:new -m clj-new.create holy-lambda basic.example && cd basic.example && bb stack:sync
```

Alternatively you can use `lein new`:

``` clojure
lein new holy-lambda example ;; <-- Replace `example` with the name of the project
```

## What it does?
It allows you to write one code which might run on Official Java AWS Runtime, Native Custom AWS Runtime built into your codebase or Babashka runtime. Holy Lambda ships with `Babashka tasks` to ease development and deployment of an application. 



## Thanks to
- Daria - Thank you that you were always beside me, fighting for me when I had no faith and energy.
- @KrzysztofTucholski - Thank you for boosting my ego
- @uswitch - Thank you for interests in AWS Lambda Functions and providing us the `lambada`
- @hjhamala - Thank you for sharing [post](https://dev.solita.fi/2018/12/07/fast-starting-clojure-lambdas-using-graalvm.html) about native lambda functions. You have inspired me to write `holy-lambda`.
- Rum - deflambda parse mechanism is adapted from rum.
- Ring - code from fierycod.holy-lambda.response is adapted from ring-core. 

## License
Copyright © 2021 Karol Wojcik aka Fierycod

Released under the MIT license.
