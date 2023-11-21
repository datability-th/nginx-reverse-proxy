# nginx-reverse-proxy

For servers with multiple domains, we CANT git clone it with custom folder name - because port 443 is already allocated. There will be only ONE instance of nginx, and add more `.conf` inside `./nginx/conf` folder instead!

```
nginx/
  |-- conf/
       |--  server.conf
       |--  server2.conf
       |--  ...
```


## V1: Simple Proxy

A simple proxy is to just map 80 to whatever localhost port there is. 

```bash
docker compose -f docker-compose-simple.yml up -d
docker compose -f docker-compose-simple.yml down
docker compose -f docker-compose-simple.yml start
docker compose -f docker-compose-simple.yml stop
```

## V2: Full Proxy With Certbot SSL

```bash
docker compose up -d
docker compose down
```

Prepping Dry Run [link](https://mindsers.blog/en/post/https-using-nginx-certbot-docker/). This is to test (Dry Run) create an ACME Request 

```bash
# Dry Run
docker compose run --rm  certbot certonly --webroot --webroot-path /var/www/certbot/ --dry-run -d automationpark.or.th -d www.automationpark.or.th

# Actual Run --> see results in ./certbot/conf/live/automationpark.or.th
docker compose run --rm  certbot certonly --webroot --webroot-path /var/www/certbot/ -d automationpark.or.th -d www.automationpark.or.th

# Manual Renewal
docker compose run --rm certbot renew
```

Set CertBot renewal in Cron with `crontab -e` 
[link](https://serverfault.com/questions/790772/best-practices-for-setting-a-cron-job-for-lets-encrypt-certbot-renewal)

>The certbot documentation recommends running the script twice a day


```bash
# minute	0-59  
# hour	0-23
# Day of the month	1-31
# month	1-12 or JAN-DEC
# Day of the week	0-6 or SUN-SAT
# Run everyday on 0:59 >>
59 0 * * * cd /root/nginx-reverse-proxy && docker compose run --rm certbot renew
```


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

OR `docker network inspect {network_name}`, we can see the containers inside.

You can reach containers by name if the `docker network` is the same. However, I think that the default `bridge` network doesn't work?   
  If it doesn't work, need to link by IP???
 https://stackoverflow.com/questions/31149501/how-to-reach-docker-containers-by-name-instead-of-ip-address
