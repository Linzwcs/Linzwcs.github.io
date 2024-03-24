---
title: "Data Parallelism"
date: 2024-03-24
permalink: /posts/2024/03/blog-post-1/
tags:
  - model training
---

With the size of neural networks increasing, training the model on a single device becomes difficult. Batch size is an important hyperparameter in the training process; a small batch size sometimes leads to lower performance. To solve this, researchers have proposed a type of method called data parallelism, which has undergone various improvements.

Data parallelism refers to a parallel computing technique where the same operation is performed on multiple data elements simultaneously. It aims to distribute the workload across multiple processing units to speed up computation.

So far, Data parallelism has three different types: Data Parallelism (DP), Distributed Data Parallelism (DDP), and Fully Shared Data Parallelism (FSDP). There are some differences between these methods.

---

/table

## DP

DP possesses one master process and multiple sub-threads. The training process overview can be displayed as follows:

1. The master process loads the model.
2. The master process broadcasts its model weights to other threads on different devices.
3. Data chunks are sent to these threads. Upon receiving a data chunk, each thread calculates the gradients and then informs the master process of the gradients.
4. The master process combines all gradients and upates the model weights, then returns to step 2.

## DDP

In contrast to DP, DDP possesses multiple processes, and each device sustains a model copy. While training, they share their gradients and update model parameters simultaneously.

The training process can be displayed as follows:

1. Data chunks are sent to each device.
2. Each process calculates its own gradients.
3. Each process shares its gradients with other process
4. Upon receiving all gradients, update own model parameters.（ Here, I simplify it, and in fact, it's more difficult.）
5. return to step 1

## FSDP

In model training, model parameters occupy only a small part of the total storage. The majority of the occupied storage is taken up by gradients, optimizer states, and other elements. Therefore, even if we train a model with a size of 7.5B, it still requires 120GB of GPU storage.

As mentioned earlier, DDP involves a lot of repeated calculations. Thus, some researchers propose dividing the optimizer into pieces and distributing them across different devices. This way, each device only needs to update its respective part of the parameters. Once all devices have updated their respective parts of the model parameters, they share and synchronize their parameters.
