---
title: "golang并发总结"
date: 2021-04-08T16:01:23+08:00
lastmod: 2021-04-08T16:01:23+08:00
draft: false
tags: ["go", "并发", "总结"]
categories: ["知识总结"]
author: "clavenzhang"

weight: 1

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
# comment: false
# toc: true
mathjax: true
---

## 前言
Go语言最得天独厚的一个优势，就是并发处理能力，掌握Go Routine和Channel等这些概念的底层基本原理和使用技巧。疏通分布式并发系统开发的相关知识节点，既要能做造火箭的拧螺丝工，也要从全局的角度来认识为什么螺丝必须这么拧。

**并发编程学习矩阵**

![go_concurency](/blog/2021/go_concurency.jpeg)

关于Go并发编程，有两条主线，分别是知识主线和学习主线。从图中可以看到，在知识主线层面，这门课程的核心内容设计了5个模块：
* 基本并发原语：在这部分，我会介绍 Mutex、RWMutex、Waitgroup、Cond、Pool、Context 等标准库中的并发原语，这些都是传统的并发原语，在其它语言中也很常见，是我们在并发编程中常用的类型。
* 原子操作：在这部分，我会介绍 Go 标准库中提供的原子操作。原子操作是其它并发原语的基础，学会了你就可以自己创造新的并发原语。
* Channel：Channel 类型是 Go 语言独特的类型，因为比较新，所以难以掌握。但是别怕，我会带你全方位地学习 Channel 类型，你不仅能掌握它的基本用法，而且还能掌握它的处理场景和应用模式，避免踩坑。
* 扩展并发原语：目前来看，Go 开发组不准备在标准库中扩充并发原语了，但是还有一些并发原语应用广泛，比如信号量、SingleFlight、循环栅栏、ErrGroup 等。掌握了它们，就可以在处理一些并发问题时，取得事半功倍的效果。
* 分布式并发原语：分布式并发原语是应对大规模的应用程序中并发问题的并发类型。我主要会介绍使用 etcd 实现的一些分布式并发原语，比如 Leader 选举、分布式互斥锁、分布式读写锁、分布式队列等，在处理分布式场景的并发问题时，特别有用。