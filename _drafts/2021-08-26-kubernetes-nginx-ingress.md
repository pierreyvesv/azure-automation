

<https://itnext.io/kubernetes-ingress-controllers-how-to-choose-the-right-one-part-1-41d3554978d2>

<https://danielfm.me/post/painless-nginx-ingress/>


the annotation `kubernetes.io/ingress.class: "nginx"` will update the nginx-ingress-controller



```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: ingress-octave-mobi8
  namespace: octave-mobi8
  annotations:
    # use the shared ingress-nginx
    kubernetes.io/ingress.class: "nginx"
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
  - hosts:
    - website.fr
  rules:
  - host: website.fr
    http:
      paths:
      - path: /
        pathType: ImplementationSpecific # default
        backend:
          serviceName: website-deployment
          servicePort: 443
```

`k exec -it nginx-master-ingress-nginx-controller-5765757d89-4sn4n -n ingress-nginx -- sh`


The nginx controler nginx.conf will have information about this ingress.

````txt
## start server website-fr
        server {
                server_name website-fr ;

                listen 80  ;
                listen [::]:80  ;
                listen 442 proxy_protocol  ssl http2 ;
                listen [::]:442 proxy_protocol  ssl http2 ;

                set $proxy_upstream_name "-";

                ssl_certificate_by_lua_block {
                        certificate.call()
                }

                location / {

                        set $namespace      "octave-mobi8";
                        set $ingress_name   "ingress-octave-mobi8";
                        set $service_name   "octave-mobi8-deployment";
                        set $service_port   "443";
                        set $location_path  "/";
                        set $global_rate_limit_exceeding n;

                        rewrite_by_lua_block {
                                lua_ingress.rewrite({
                                        force_ssl_redirect = false,
                                        ssl_redirect = true,
                                        force_no_ssl_redirect = false,
                                        preserve_trailing_slash = false,
                                        use_port_in_redirects = false,
                                global_throttle = { namespace = "", limit = 0, window_size = 0, key = { }, ignored_cidrs = { } },
                                })
                                balancer.rewrite()
                                plugins.run()
                        }
```


### ssl path trough

if your nginx helm charts has been installed with ssl-passthrough = "", then you should add     `nginx.ingress.kubernetes.io/ssl-passthrough: "true"` to your yaml file
