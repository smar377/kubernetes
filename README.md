# Kubernetes

_*Disclaimer:*_ This repo was created in order to keep track of all my personal notes while studying and experimenting with Kubernetes. This includes my journey throughout various online Kubernetes courses and the labs they come with them.   

## Definition

Kubernetes is an open-source container orchestration system for automating software deployment, scaling, and management. Google originally designed Kubernetes, but the Cloud Native Computing Foundation now maintains the project [(Kubernetes | Wiki)](https://en.wikipedia.org/wiki/Kubernetes).

## History

Kubernetes (κυβερνήτης, Greek for "helmsman," "pilot," or "governor", and the etymological root of cybernetics) was announced by Google in mid-2014. The project was created by Joe Beda, Brendan Burns, and Craig McLuckie, who were soon joined by other Google engineers, including Brian Grant and Tim Hockin.

The design and development of Kubernetes was influenced by Google's Borg cluster manager. Many of its top contributors had previously worked on Borg; they codenamed Kubernetes "Project 7" after the Star Trek ex-Borg character Seven of Nine and gave its logo a seven-spoked wheel. Unlike Borg, which was written in C++, Kubernetes source code is in the Go language.

Kubernetes 1.0 was released on July 21, 2015. Google worked with the Linux Foundation to form the Cloud Native Computing Foundation (CNCF) and offer Kubernetes as a seed technology. In February 2016, the Helm package manager for Kubernetes was released.

Google was already offering managed Kubernetes services, while Red Hat was supporting Kubernetes as part of OpenShift since the inception of the Kubernetes project in 2014. In 2017, the principal competitors rallied around Kubernetes and announced adding native support for it:

 - in August, VMWare (proponent of Pivotal Cloud Foundry)
 - in September, Mesosphere, Inc. (proponent of Marathon and Mesos)
 - in October, Docker, Inc. (proponent of Docker)
 - later the same October, Microsoft Azure
 - in November, AWS announced support for Kubernetes via the Elastic Container Service for Kubernetes (EKS)

On March 6, 2018, Kubernetes Project reached ninth place in the list of GitHub projects by the number of commits, and second place in authors and issues, after the Linux kernel.

Until version 1.18, Kubernetes followed an N-2 support policy, meaning that the three most recent minor versions receive security updates and bug fixes. Starting with version 1.19, Kubernetes follows an N-3 support policy.
