## Bitwarden Let's Encrypt DNS-01 Challenge Automation Script

This script provides self-hosting Bitwarden users with an automated renewal of Let's Encrypt certificates using the DNS-01 challenge type, circumventing the HTTP-01 challenge type's requirement for the server to have a port open to the public internet.  This allows you to keep remote access to the Bitwarden server restricted to VPN access and removes the need to manually renew the certificate, ensuring your Bitwarden instance is always accessible when you need it.  It can also be used to generate new certificates if you are changing your hostname or deploying a new Bitwarden instance.

### Usage 

Customize the following variables in the script:

- BW_DIR: the location of your ```bwdata``` directory 
- LE_DIR: the directory where you'd like to stage your LE certificates
- DOMAIN: the domain you'd like to generate/renew a cert for  
- EMAIL: the email address registered with your DNS provider (only required for new cert generation)

The script will first download the certificates to the LE staging directory and then copy the required files to the ```bwdata```'s ssl directory.  This keeps things cleaner in the ```bwdata/ssl``` directory and provides you with a standard certbot directory for storage/review of old certs and logs if needed.  This script also assumes your ```bitwarden.sh``` file resides in the same directory as your ```bwdata``` directory.

The script currently only uses the following two arguments:

- ```renew```: renew the certs for an existing domain
- ```new```: generate certificates for a new domain   which are fairly self explanatory.  

The script will simply copy the latest certs it generates into the ```bwdata/ssl``` directory and those will go live once it starts Bitwarden again.  If you want to generate a new domain you will need to manually edit ```bwdata/config.yml``` to change the hostname and then run ```bitwarden.sh rebuild```.  The ```new``` argument is just intended to set things up so that the cert you generate can easily be auto-renewed by this script.

### API Keys/Security

Generate an API token from your DNS provider that is restricted to only the zone you intend to use for this certificate.  I would *STRONGLY* recommend against using a global API token from your DNS provider. Save the API key in a '''dns-api-key.ini''' file in your LE staging directory with the following format:

```
dns_cloudflare_email = [email address registered with your DNS provider]
dns_cloudflare_api_key = [API key for the zone you're using]
```

Make sure the above file has permissions locked down tight, along with the permissions for both your ```LE_DIR``` and ```BW_DIR/ssl``` directories.  If you're taking the time to self-host your own Bitwarden instance you're probably pretty security conscious so you probably know that it's worth it to take the time to double check everything, and please do bring any holes you see in my implementation to my attention.

### Scheduling/Logging

I would recommend running this as a cronjob every two months with the output of the script set to append to a log file in case it's ever needed for review.

### Other DNS Providers

At the moment this script has only been tested with Cloudflare but [certbot](https://hub.docker.com/u/certbot) has various docker images to perform DNS-01 challenges for other DNS providers.  I plan to update the script to allow the DNS provider as an argument, please let me know if you have success dropping in a different DNS provider and what, if any, changes were required.


