## Permanent Redirect[¶](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#permanent-redirect)

This annotation allows to return a permanent redirect (Return Code 301) instead of sending data to the upstream. For example `nginx.ingress.kubernetes.io/permanent-redirect: https://www.google.com` would redirect everything to Google.

```
nginx.ingress.kubernetes.io/permanent-redirect: https://www.google.com
```



## Permanent Redirect Code[¶](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#permanent-redirect-code)

This annotation allows you to modify the status code used for permanent redirects. For example `nginx.ingress.kubernetes.io/permanent-redirect-code: '308'` would return your permanent-redirect with a 308.

```
nginx.ingress.kubernetes.io/permanent-redirect-code: '308'
```





## rewrite 配置

> 下面 rewrite 规则意思是 访问 [www.example.com/hello/(.*)](http://www.example.com/hello/(.*)) 跳转到 [www.example.com/(.*)](http://www.example.com/(.*))

```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: "nginx" # 绑定ingress-class
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: "/$1"
spec:
  rules:
  - host: www.example.com
    http:
      paths:
      - path: /hello/(.*)$
        backend:
          serviceName: demo-svc
          servicePort: 8080
```

或者

```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: "nginx" # 绑定ingress-class
    nginx.ingress.kubernetes.io/configuration-snippet: |
      rewrite ^/hello/(.*)$ /$1 redirect;
spec:
  rules:
  - host: www.example.com
    http:
      paths:
      - path: /hello/(.*)$
        backend:
          serviceName: demo-svc
          servicePort: 8080
```



下面 rewrite 规则意思是 访问 [www.example.com/](http://www.example.com/hello/(.*)) 跳转到 [www.example.com/app1](http://www.example.com/(.*))

```
创建一个带有 app-root 注释的 Ingress 规则：

$ echo "
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/app-root: /app1
  name: approot
  namespace: default
spec:
  rules:
  - host: approot.bar.com
    http:
      paths:
      - backend:
          serviceName: http-svc
          servicePort: 80
        path: /
" | kubectl create -f -
检查重写是否有效

$ curl -I -k http://approot.bar.com/
HTTP/1.1 302 Moved Temporarily
Server: nginx/1.11.10
Date: Mon, 13 Mar 2017 14:57:15 GMT
Content-Type: text/html
Content-Length: 162
Location: http://stickyingress.example.com/app1
Connection: keep-alive
```





## 限速

> 设置 [www.example.com/login](http://www.example.com/login) 登陆页为每秒100个连接数，10.0.0.0/24,172.10.0.1 IP段不在限速范围

```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: "nginx" # 绑定ingress-class
    nginx.ingress.kubernetes.io/limit-rps: '100'
    nginx.ingress.kubernetes.io/limit-whitelist: 10.0.0.0/24,172.10.0.1
spec:
  rules:
  - host: www.example.com
    http:
      paths:
      - path: /login
        backend:
          serviceName: demo-svc
          servicePort: 8080
```

"# ingress" 
