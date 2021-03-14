#!/usr/bin/env bash

date

BW_DIR="[REPLACEME]"
LE_DIR="[REPLACEME]"
DOMAIN="[REPLACEME]"
EMAIL="[REPLACEME]"

function newCertLetsEncrypt() {
        docker pull certbot/dns-cloudflare
        docker run -i --rm --name certbot -p 443:443 -p 80:80 \
            -v $LE_DIR/letsencrypt:/etc/letsencrypt/ certbot/dns-cloudflare \
            certonly --noninteractive --email $EMAIL -d $DOMAIN  \
            --dns-cloudflare --dns-cloudflare-credentials /etc/letsencrypt/dns-api-key.ini \
            --preferred-challenges dns-01 --agree-tos --logs-dir /etc/letsencrypt/logs
}

function forceUpdateLetsEncrypt() {
        docker pull certbot/dns-cloudflare
        docker run -i --rm --name certbot -p 443:443 -p 80:80 \
            -v $LE_DIR:/etc/letsencrypt/ certbot/dns-cloudflare \
            renew --dns-cloudflare --dns-cloudflare-credentials /etc/letsencrypt/dns-api-key.ini \
            --preferred-challenges dns-01 --agree-tos --logs-dir /etc/letsencrypt/logs --force-renew
}

$BW_DIR/../bitwarden.sh stop

if [ "$1" == "new" ]
then
    newCertLetsEncrypt
elif [ "$1" == "renew" ]
then
    forceUpdateLetsEncrypt
fi

CERTS=$( ls $LE_DIR/live/$DOMAIN/ )

for f in $CERTS
do
    echo copying $f
    cp $LE_DIR/live/$DOMAIN/$f $BW_DIR/ssl/$DOMAIN/$f
 done

$BW_DIR/../bitwarden.sh start

