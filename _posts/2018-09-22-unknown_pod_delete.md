---
layout: post
category: "kubernetes"
title: "Unknown 상태의 Pod 삭제하기"
tags: ["kubernetes"]
---


<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/100.JPG?raw=true" width="px">

Pod가 생성되는 도중에 또는 잘 생성되었다가 어떤 이유 때문에 저렇게 Unknown 상태가 되는 경우가 있다. 그래서 Pod를 삭제하려고 했는데 deleted라는 메시지는 뜨면서 그 이후로 응답이 없다. 그래서 Ctrl + C로 빠져나왔고 kubectl delete로도 지워봤지만 역시나 되지 않았다. 그림에는 없지만 저 상태에서 kubectl get pods를 입력하면 Pod가 삭제되지 않고 남아있다. 필자의 경우는 왜 저렇게 Unknown 상태가 되었는지 살펴보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/101.JPG?raw=true" width="750px">

kubectl describe pod(s) [Pod명] 을 입력하면 현재 Pod 상태를 자세하게 볼 수 있다. 현재 pooh-db-nginx-pod는 Terminating, 즉, 종료된 상태이며 Reason(이유)는 NodeLost(노드를 잃어버림)이다. 그 밑의 Message를 보면 pooh-db-nginx-pod를 실행하는 노드인 worker-node2가 unresponsive(응답이 없음) 상태라고 나온다. 메시지대로 필자의 경우 worker-node2에 Pod를 생성했고 처음에 Running 상태였으나 worker-node2의 Pod가 삭제되지 않은 채로 worker-node2가 꺼져버렸기 때문에 저런 에러가 떴던 것이다. 이 외에도 여러 이유가 있을 수 있다. 중요한 건 Unknown의 이유가 아니라 Unknown 상태 자체이다. 이럴 때는 Pod 삭제를 못하는 것일까? 아니다. 다음처럼 해주면 된다.

kubectl delete pod(s) [Pod명] --grace-period=0 --force

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/102.JPG?raw=true" width="750px">

이렇게 해주면 해당 Pod가 잘 삭제된다. kubectl get pods로 확인해보면 Pod가 잘 삭제되었음을 확인할 수 있다.

<br><br><br>참고 사이트 : 

- https://github.com/kubernetes/kubernetes/issues/43279
