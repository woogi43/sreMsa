# Kubernetes 각종 레이블 관리와 모니터링
* ./Lab1.Kubespray/1.KubernetesBasic/README.md 파일을 참고 하여 문제를 푸세요.
*  답은 Solution.md 파일에 있습니다.
## 1. 모든 Pods를 출력하시오.
```
kubectl get pods
```

## 2. 아래의 조건을 만족하는 pods를 만드시오.
|pod Name|image Name|
|--------|----------|
|n1      |nginx     |
|u1      |ubuntu    |
|h1      |httpd     |

```
root@vm01:~# kubectl run n1 --image=nginx
pod/n1 created
root@vm01:~# kubectl run u1 --image=ubuntu
pod/u1 created
root@vm01:~# kubectl run h1 --image=httpd
pod/h1 created
root@vm01:~# k get pods

```

## 3. 만들어진 pods를 확인하시오.
```
root@vm01:~# k get pods -o wide
NAME                     READY   STATUS             RESTARTS      AGE     IP               NODE   NOMINATED NODE   READINESS GATES
h1                       1/1     Running            0             44s     10.233.115.220   vm03   <none>           <none>
n1                       1/1     Running            0             61s     10.233.79.66     vm04   <none>           <none>
nginx-7854ff8877-4wvvr   1/1     Running            0             54m     10.233.115.218   vm03   <none>           <none>
nginx-7854ff8877-68z9w   1/1     Running            0             68m     10.233.109.20    vm02   <none>           <none>
nginx-7854ff8877-crxxh   1/1     Running            1             61m     10.233.109.94    vm01   <none>           <none>
nginx-7854ff8877-jgqjz   1/1     Running            0             61m     10.233.109.21    vm02   <none>           <none>
nginx-7854ff8877-n42kn   1/1     Running            1 (60m ago)   68m     10.233.109.90    vm01   <none>           <none>
nginx-7854ff8877-pgjq4   1/1     Running            0             68m     10.233.109.19    vm02   <none>           <none>
nginx-7854ff8877-phv6t   1/1     Running            0             61m     10.233.109.22    vm02   <none>           <none>
nginx-7854ff8877-pjxhh   1/1     Running            0             54m     10.233.115.217   vm03   <none>           <none>
nginx-7854ff8877-sdsbt   1/1     Running            0             3h41m   10.233.109.3     vm02   <none>           <none>
nginx-7854ff8877-slkfr   1/1     Running            0             61m     10.233.109.95    vm01   <none>           <none>
nginx-7854ff8877-xw9c7   1/1     Running            1 (60m ago)   3h41m   10.233.109.97    vm01   <none>           <none>
nginx-7854ff8877-ztr4m   1/1     Running            1 (60m ago)   68m     10.233.109.96    vm01   <none>           <none>
u1                       0/1     CrashLoopBackOff   2 (22s ago)   52s     10.233.79.67     vm04   <none>           <none>
```

## 4. ubuntu이미지가 잘 못 만들어진 원인은?
* kubernetes에서 사용하는 pod들은 기본적으로 detach(-d)방식으로 동작.
```
root@vm01:~# k get pods -o wide | grep u1
u1                       0/1     CrashLoopBackOff   4 (83s ago)   3m2s    10.233.79.67     vm04   <none>           <none>
```

## 5. 아래의 yml파일을 가지고 다시 생성하시오.
```
root@vm01:~# kubectl delete pod u1
pod "u1" deleted


cat <<EOF>u1.yml
apiVersion: v1
kind: Pod
metadata:
  name: u1
  labels:
    server: ubuntu
spec:
  containers:
  - image: ubuntu
    command:
      - "sleep"
      - "604800"
    imagePullPolicy: IfNotPresent
    name: u1
  restartPolicy: Always
EOF
root@vm01:~# kubectl create -f ~/u1.yml
```


## 6. pods의 Label이 server=ubuntu인 모든 파드를 출력하시오.
```

root@vm01:~# kubectl get pods -l server=ubuntu
NAME   READY   STATUS    RESTARTS   AGE
u1     1/1     Running   0          68s
```

## 7. n1,h1 pods의 label을 server=ubuntu, app=web  이라고 지정하시오.
```
root@vm01:~# kubectl label po/n1 app=web server=ubuntu
pod/n1 labeled
root@vm01:~# kubectl label po/h1 app=web server=ubuntu
pod/h1 labeled

```
## 8. 모든 pods의 label과 함께 출력하시오.
```
kubectl get pods --show-labels
```

## 9. app=web인 Pods만 출력하시오.
```
kubectl get pods -l app=web

```

## 10. Label이 app=web인 모든 Pods를 삭제 하시오.
```
kubectl delete po -l app=web

```

## 11. 모든 파드를 지우시오.
```
 kubectl get pods

 kubectl delete po/u1

```
* cf)
```
kubectl get pods |awk 'NR>1{print $1}'|xargs -i{} kubectl delete po/{}
# or
kubectl get pods -o json|jq .items[].metadata.name |xargs -i{} kubectl delete po/{}

```
