
## set browser cache

pour les gros sites: stockage des images dans le browser des clients:

```bash
set brower cache
location ~*  \.(jpg|jpeg|png|gif|ico|css|js|pdf)$ {
expires 7d;
}
```

<https://webdock.io/en/docs/optimizing-performance/setting-cache-control-headers-common-content-types-nginx-and-apache>

## max chunk

dans le block http positionner l’option: `sendfile_max_chunk 512k;`

The last directive can reduce the maximum time spent in blocking sendfile() calls, since NGINX won’t try to send the whole file at once, but will do it in 512 KB chunks.

<http://nginx.org/en/docs/http/ngx_http_core_module.html#sendfile_max_chunk>
<https://www.nginx.com/blog/thread-pools-boost-performance-9x>

## TCP_NOTSENT_LOWAT and BBR reduces the amount of buffering

 kernel 4.9.0 already have module for "TCP BBR" to manage congestion, they only needs to be activated.

The combination of TCP_NOTSENT_LOWAT and BBR reduces the amount of buffering on the network to the absolute minimum and is CRITICAL for good end-user performance with HTTP/2. This is particularly true for NGINX and other HTTP/2 servers that don’t implement their own buffer throttling.

<https://cloud.google.com/blog/products/gcp/tcp-bbr-congestion-control-comes-to-gcp-your-internet-just-got-faster>
<https://blog.cloudflare.com/http-2-prioritization-with-nginx/>
<https://www.cyberciti.biz/cloud-computing/increase-your-linux-server-internet-speed-with-tcp-bbr-congestion-control/>