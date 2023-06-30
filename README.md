# Purpose

This fork is dedicated to explore [Gin(v1.9.1)](https://gin-gonic.com/)'s routes tree structure. It
helps to inspect the internal of Gin's routes tree.

# Types

```go
type methodTrees []methodTree

type methodTree struct {
    method string
    root   *node
}

type node struct {
    path      string        // path segment stored in this node
    indices   string        // indices to access child node in children, each rune index a child node's path segment
    wildChild bool          // whether a child node's nodeType is param or catchAll
    nType     nodeType      // node's type: static, root, param, catchAll
    priority  uint32        // number of handlers explicitly registered in this node
    children  []*node       // child nodes, at most 1 :param style node at the end of the array
    handlers  HandlersChain // handlers with middlewares at the beginning
    fullPath  string        // full path chained from the root node to the current node
}
```

# Quick Start

Import this fork and write your routes:

```go
package main

import (
	gin "github.com/dushaoshuai/explore-gin-routes-tree"
)

func docInstall(ctx *gin.Context)  {}
func docGetStart(ctx *gin.Context) {}
func docCmd(ctx *gin.Context)      {}
func docUpload(ctx *gin.Context)   {}
func refMod(ctx *gin.Context)      {}
func refSpec(ctx *gin.Context)     {}
func login(ctx *gin.Context)       {}
func logout(ctx *gin.Context)      {}

func main() {
	r := gin.Default()

	docGroup := r.Group("/doc")
	{
		docGroup.GET("/install", docInstall)
		docGroup.GET("/tutorial/getting-started", docGetStart)
		docGroup.GET("/cmd", docCmd)
		docGroup.POST("/upload", docUpload)
	}

	refGroup := r.Group("/ref")
	{
		refGroup.GET("/mod", refMod)
		refGroup.GET("/spec", refSpec)
	}

	r.POST("/login/:user", login)
	r.POST("/logout/*user_id", logout)

	r.Run() // default :8080
}
```

Run your app and visit http://localhost:8080/debug-trees:

```bash
$ curl http://localhost:8080/debug-trees
====================
       GET
====================

nodeType: root
priority: 7
wildChild: false
path: /
fullPath: /
indices: dr
     │
     │──d─┐
     │     nodeType: static
     │     priority: 5
     │     wildChild: false
     │     path: d
     │     fullPath: /d
     │     indices: oe
     │          │
     │          │──o─┐
     │          │     nodeType: static
     │          │     priority: 4
     │          │     wildChild: false
     │          │     handlers:
     │          │        github.com/dushaoshuai/explore-gin-routes-tree.LoggerWithConfig.func1
     │          │        github.com/dushaoshuai/explore-gin-routes-tree.CustomRecoveryWithWriter.func1
     │          │        main.doc
     │          │     path: oc
     │          │     fullPath: /doc
     │          │     indices: /
     │          │          │
     │          │          │──/─┐
     │          │          │     nodeType: static
     │          │          │     priority: 3
     │          │          │     wildChild: false
     │          │          │     path: /
     │          │          │     fullPath: /doc/
     │          │          │     indices: itc
     │          │          │          │
     │          │          │          │──i─┐
     │          │          │          │     nodeType: static
     │          │          │          │     priority: 1
     │          │          │          │     wildChild: false
     │          │          │          │     handlers:
     │          │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.LoggerWithConfig.func1
     │          │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.CustomRecoveryWithWriter.func1
     │          │          │          │        main.docInstall
     │          │          │          │     path: install
     │          │          │          │     fullPath: /doc/install
     │          │          │          │
     │          │          │          │──t─┐
     │          │          │          │     nodeType: static
     │          │          │          │     priority: 1
     │          │          │          │     wildChild: false
     │          │          │          │     handlers:
     │          │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.LoggerWithConfig.func1
     │          │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.CustomRecoveryWithWriter.func1
     │          │          │          │        main.docGetStart
     │          │          │          │     path: tutorial/getting-started
     │          │          │          │     fullPath: /doc/tutorial/getting-started
     │          │          │          │
     │          │          │          │──c─┐
     │          │          │          │     nodeType: static
     │          │          │          │     priority: 1
     │          │          │          │     wildChild: false
     │          │          │          │     handlers:
     │          │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.LoggerWithConfig.func1
     │          │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.CustomRecoveryWithWriter.func1
     │          │          │          │        main.docCmd
     │          │          │          │     path: cmd
     │          │          │          │     fullPath: /doc/cmd
     │          │
     │          │──e─┐
     │          │     nodeType: static
     │          │     priority: 1
     │          │     wildChild: false
     │          │     handlers:
     │          │        github.com/dushaoshuai/explore-gin-routes-tree.LoggerWithConfig.func1
     │          │        github.com/dushaoshuai/explore-gin-routes-tree.CustomRecoveryWithWriter.func1
     │          │        github.com/dushaoshuai/explore-gin-routes-tree.Default.func1
     │          │     path: ebug-trees
     │          │     fullPath: /debug-trees
     │
     │──r─┐
     │     nodeType: static
     │     priority: 2
     │     wildChild: false
     │     path: ref/
     │     fullPath: /ref/
     │     indices: ms
     │          │
     │          │──m─┐
     │          │     nodeType: static
     │          │     priority: 1
     │          │     wildChild: false
     │          │     handlers:
     │          │        github.com/dushaoshuai/explore-gin-routes-tree.LoggerWithConfig.func1
     │          │        github.com/dushaoshuai/explore-gin-routes-tree.CustomRecoveryWithWriter.func1
     │          │        main.refMod
     │          │     path: mod
     │          │     fullPath: /ref/mod
     │          │
     │          │──s─┐
     │          │     nodeType: static
     │          │     priority: 1
     │          │     wildChild: false
     │          │     handlers:
     │          │        github.com/dushaoshuai/explore-gin-routes-tree.LoggerWithConfig.func1
     │          │        github.com/dushaoshuai/explore-gin-routes-tree.CustomRecoveryWithWriter.func1
     │          │        main.refSpec
     │          │     path: spec
     │          │     fullPath: /ref/spec


====================
       POST
====================

nodeType: root
priority: 5
wildChild: false
path: /
fullPath: /
indices: ucd
     │
     │──u─┐
     │     nodeType: static
     │     priority: 2
     │     wildChild: false
     │     path: user/log
     │     fullPath: /user/log
     │     indices: io
     │          │
     │          │──i─┐
     │          │     nodeType: static
     │          │     priority: 1
     │          │     wildChild: true
     │          │     path: in/
     │          │     fullPath: /user/login/:user
     │          │          │
     │          │          │────┐
     │          │          │     nodeType: param
     │          │          │     priority: 1
     │          │          │     wildChild: false
     │          │          │     handlers:
     │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.LoggerWithConfig.func1
     │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.CustomRecoveryWithWriter.func1
     │          │          │        main.userLogin
     │          │          │     path: :user
     │          │          │     fullPath: /user/login/:user
     │          │
     │          │──o─┐
     │          │     nodeType: static
     │          │     priority: 1
     │          │     wildChild: true
     │          │     path: out/
     │          │     fullPath: /user/logout/:user
     │          │          │
     │          │          │────┐
     │          │          │     nodeType: param
     │          │          │     priority: 1
     │          │          │     wildChild: false
     │          │          │     handlers:
     │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.LoggerWithConfig.func1
     │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.CustomRecoveryWithWriter.func1
     │          │          │        main.userLogout
     │          │          │     path: :user
     │          │          │     fullPath: /user/logout/:user
     │
     │──c─┐
     │     nodeType: static
     │     priority: 2
     │     wildChild: false
     │     path: company/log
     │     fullPath: /company/log
     │     indices: io
     │          │
     │          │──i─┐
     │          │     nodeType: static
     │          │     priority: 1
     │          │     wildChild: false
     │          │     path: in
     │          │     fullPath: /company/login/*company
     │          │     indices: /
     │          │          │
     │          │          │──/─┐
     │          │          │     nodeType: catchAll
     │          │          │     priority: 1
     │          │          │     wildChild: true
     │          │          │     path: 
     │          │          │     fullPath: /company/login/*company
     │          │          │          │
     │          │          │          │────┐
     │          │          │          │     nodeType: catchAll
     │          │          │          │     priority: 1
     │          │          │          │     wildChild: false
     │          │          │          │     handlers:
     │          │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.LoggerWithConfig.func1
     │          │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.CustomRecoveryWithWriter.func1
     │          │          │          │        main.companyLogin
     │          │          │          │     path: /*company
     │          │          │          │     fullPath: /company/login/*company
     │          │
     │          │──o─┐
     │          │     nodeType: static
     │          │     priority: 1
     │          │     wildChild: false
     │          │     path: out
     │          │     fullPath: /company/logout/*company
     │          │     indices: /
     │          │          │
     │          │          │──/─┐
     │          │          │     nodeType: catchAll
     │          │          │     priority: 1
     │          │          │     wildChild: true
     │          │          │     path: 
     │          │          │     fullPath: /company/logout/*company
     │          │          │          │
     │          │          │          │────┐
     │          │          │          │     nodeType: catchAll
     │          │          │          │     priority: 1
     │          │          │          │     wildChild: false
     │          │          │          │     handlers:
     │          │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.LoggerWithConfig.func1
     │          │          │          │        github.com/dushaoshuai/explore-gin-routes-tree.CustomRecoveryWithWriter.func1
     │          │          │          │        main.companyLogout
     │          │          │          │     path: /*company
     │          │          │          │     fullPath: /company/logout/*company
     │
     │──d─┐
     │     nodeType: static
     │     priority: 1
     │     wildChild: false
     │     handlers:
     │        github.com/dushaoshuai/explore-gin-routes-tree.LoggerWithConfig.func1
     │        github.com/dushaoshuai/explore-gin-routes-tree.CustomRecoveryWithWriter.func1
     │        main.docUpload
     │     path: doc/upload
     │     fullPath: /doc/upload
```
