# nginx-reverse-proxy

## V1: Simple Proxy

A simple proxy is to just map 80 to whatever localhost port there is. 

```bash
docker compose -f docker-compose-simple.yml up -d
```

## V2: Full Proxy With Certbot SSL