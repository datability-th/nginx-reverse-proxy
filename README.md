# nginx-reverse-proxy

## V1: Simple Proxy

A simple proxy is to just map 80 to whatever localhost port there is. 

```bash
docker compose -f docker-compose-simple.yml up -d
docker compose -f docker-compose-simple.yml down
```

## V2: Full Proxy With Certbot SSL



# Dev Notes

This is setup from https://mindsers.blog/post/https-using-nginx-certbot-docker/ .

Simple reverse proxy is from here https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/How-to-setup-Nginx-reverse-proxy-servers-by-example.


For networking, docker we use the default `bridge` network as documented here: https://faun.pub/run-the-containers-in-default-bridge-network-with-docker-compose-yml-f8e57590722 .

We use the variables from `envsubst` https://serverfault.com/questions/577370/how-can-i-use-environment-variables-in-nginx-conf . 

>nginx docker image already runs envsubst through /docker-entrypoint.d/20-envsubst-on-templates.sh. You only need to place a template file in the right place: /etc/nginx/templates/my-file.conf.template.

>Using environment variables in nginx configuration:
>
>Out-of-the-box, Nginx doesn't support using environment variables inside most configuration blocks.
>
>But envsubst may be used as a workaround if you need to generate your nginx configuration dynamically before nginx starts.

Check the network by `docker inspect {container_name}`

You can reach containers by name if the `docker network` is the same. However, I think that the default `bridge` network doesn't work?   
  If it doesn't work, need to link by IP???
 https://stackoverflow.com/questions/31149501/how-to-reach-docker-containers-by-name-instead-of-ip-address