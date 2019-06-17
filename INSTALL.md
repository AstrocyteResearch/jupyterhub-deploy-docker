Install instructions for Astrocyte
==================================


## Mount external drive

Add a drive to `/data` so that docker and the images have access to more storage
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html#ebs-mount-after-reboot

## Change where docker stores files

Configure docker settings via https://blog.adriel.co.nz/2018/01/25/change-docker-data-directory-in-debian-jessie/

    sudo service docker stop
    sudo rsync -axPS /var/lib/docker/ /data/docker
    sudo service docker start

## Create certs

      
    cd ~/jupyterhub-deploy-docker
      mkdir -p secrets
      docker volume create --name jupyterhub-secrets

      cd ./examples/letsencrypt/
     ./letsencrypt.sh --domain cortex.astrocyte.io --email skruzel@astrocyte.io --volume jupyterhub-secrets



      make build
      make notebook_image

