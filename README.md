# 关于本文档

本文档旨在记录本人在实验环境中，用**二进制**、**纯手动**的方式部署一个**最小的**、**非高可用**及**高可用**的K8s集群的步骤，以便日后回顾，并基于此研究K8s各组件的源码。同时，也供感兴趣的人参考。其中：

- 最小的定义：只部署能使K8s集群正常运行的的必须组件。具体为：
  - [control plane组件](https://kubernetes.io/docs/concepts/overview/components/#control-plane-components)：kube-apiserver、kube-controller-manager、kube-scheduler
  - [node组件](https://kubernetes.io/docs/concepts/overview/components/#node-components)：kubelet、kube-proxy、contrainer runtime
  - 必须的插件：CNI网络插件、DNS插件

- 非高可用的定义：只部署单节点的[control plane组件](https://kubernetes.io/docs/concepts/overview/components/#control-plane-components)
- 高可用的定义：部署多节点的[control plane组件](https://kubernetes.io/docs/concepts/overview/components/#control-plane-components)

部署过程中参考了一些开源项目、博客及k8s官方文档，会对应的文档中贴出出处，以便对照参考。



# 本文档不涉及的部分

本文档**不讨论**各组件、插件等的参数调优，也**不会**对部署过程中涉及到的一些概念做详细解释，如：CA证书的原理、私有CA证书的意义、K8s的基本概念、K8s的基本使用、K8s的网络原理等。对这一部分没有概念或者不了解的人，需要自行去查阅相关资料。



# 参考项目及文献

本人在部署过程中主要参考了如下项目及文献：

1. [easzlab/kubeasz](https://github.com/easzlab/kubeasz)
2. [kubernetes-the-hard-way](https://github.com/kelseyhightower/kubernetes-the-hard-way)
3. [Kubernetes Documentation](https://kubernetes.io/docs/home/)
4. 《Kubernetes网络权威指南：基础、原理与实践》



# FAQ

## 1. 为什么要手动搭一遍K8s集群

可能有人会问，社区里已经有很多部署或管理K8s的工具，如：kubeasz、kubeadm、kubespary、Rancher等，为何还要费事自己手动搭一遍？个人认为有如下好处：

1. 社区里的部署工具屏蔽了部署过程中各组件之间的配置复杂性。手动部署一遍集群有助于深入理解K8s各组件之间的协作关系

2. 为部署**高度灵活**的K8s集群架构提供参考，这个灵活性甚至可以精确到证书签发、CA的规划上

3. 开发属于你的K8s集群自动化部署程序

4. 对于想要研究K8s源码的人来说，对源码进行DIY后，可以在实验环境中快速替换对应的二进制组件，并观察效果。

5. 由于众所周知的原因，在某些情况下可以利用二进制进行离线部署

6. 更好地利用kubeadm、Rancher等部署工具

7. 排障时思路能够更清晰

   ……

简而言之，做个不恰当的比喻：使用已有的部署工具好比是开”自动挡“汽车——方便快捷、容易上手，但牺牲了挡位切换的灵活性，并且会让你对汽车的基本工作原理也不是很了解，导致在出现故障时没有头绪；手动部署好比是开”手动挡的“汽车——繁琐、复杂，但好处是一旦熟悉后，便能拥有最大的驾驶灵活性，并且你能对汽车的底层原理有一定了解，遇到故障也能够有头绪去解决。一旦熟练“手动档”的操作后，你便能从容应对各种情况，并且换回“自动档”时也能游刃有余了。



## 2. 我可以按照这个文档来部署生产环境吗？

**不能**。可以参考文档中的步骤，但**一定不能直接照搬**文档中的步骤进行生产部署！本文档旨在提供参考，以便深入理解K8s的架构及各组件之间的关系，但并不是一个Production Ready的部署手册！如果你要部署自己的生产环境，必须根据实际情况进行修改，包括但不限于如：系统基线刷写，K8s组件参数的调优、网络插件的选型、Ingress Controller的选型等等。同时你应当**充分理解**在生产环境中所做的每一步操作！
