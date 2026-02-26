# kuberenete-3Tier
쿠버네티스에 배포할 3Tier 구축


## Docker build
```
docker build -t was:local ./was
docker build -t web:local ./web

docker tag was:local rainhyeon/three-tier-was:v1
docker tag web:local rainhyeon/three-tier-web:v1
```

## Docker Push
```
docker push rainhyeon/three-tier-was:v1
docker push rainhyeon/three-tier-web:v1
```

## 베포
```
kubectl apply -f k8s/
```

- 배포 결과
```
controlplane:~/kuberenete-3Tier$ k get all -n three-tier -o wide
NAME                       READY   STATUS         RESTARTS   AGE     IP            NODE     NOMINATED NODE   READINESS GATES
pod/db-79d697f7fb-7r7fs    1/1     Running        0          2m43s   192.168.1.4   node01   <none>           <none>
pod/was-5b4f7fb55b-npxfs   0/1     ErrImagePull   0          2m43s   192.168.1.7   node01   <none>           <none>
pod/was-5b4f7fb55b-t2nr4   0/1     ErrImagePull   0          2m43s   192.168.1.6   node01   <none>           <none>
pod/web-5bc4d547cd-5rkdk   1/1     Running        0          2m42s   192.168.1.5   node01   <none>           <none>

NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE     SELECTOR
service/db    ClusterIP   10.105.2.92     <none>        5432/TCP       2m43s   app=db
service/was   ClusterIP   10.97.185.161   <none>        80/TCP         2m43s   app=was
service/web   NodePort    10.96.27.219    <none>        80:30817/TCP   2m42s   app=web

NAME                  READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES                        SELECTOR
deployment.apps/db    1/1     1            1           2m43s   postgres     postgres:16                   app=db
deployment.apps/was   0/2     2            0           2m43s   was          rainhyeon/three-tier-was:v1   app=was
deployment.apps/web   1/1     1            1           2m43s   web          rainhyeon/three-tier-web:v1   app=web

NAME                             DESIRED   CURRENT   READY   AGE     CONTAINERS   IMAGES                        SELECTOR
replicaset.apps/db-79d697f7fb    1         1         1       2m43s   postgres     postgres:16                   app=db,pod-template-hash=79d697f7fb
replicaset.apps/was-5b4f7fb55b   2         2         0       2m43s   was          rainhyeon/three-tier-was:v1   app=was,pod-template-hash=5b4f7fb55b
replicaset.apps/web-5bc4d547cd   1         1         1       2m42s   web          rainhyeon/three-tier-web:v1   app=web,pod-template-hash=5bc4d547cd
```


## 통신 테스트
### [web-> was -> db] API 직접 검증
```
controlplane:~/kuberenete-3Tier$ kubectl -n three-tier port-forward svc/web 8080:80
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
```






