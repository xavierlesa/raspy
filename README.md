# Configuración para la Raspi

## Bootstrap

Actualización general

```sh
sudo apt update
sudo apt upgrade -y
sudo reboot
```

## Docker

Instalar `Docker` desde el repo

```sh
curl -sSL https://get.docker.com | sh
```

Asignar el usuario `docker` y los permisos

```sh
sudo usermod -aG docker $USER

# reboot o desconectate y volve a conectar para recargar los grupos.
sudo reboot
# exit
```

## Creando el `docker-compose`

Primero instalo [`pi-hole`](https://github.com/pi-hole/docker-pi-hole) como primer
servicio. (Ver archivo `docker-compose.yml`)

```yaml
version: "3"

# More info at https://github.com/pi-hole/docker-pi-hole/ and https://docs.pi-hole.net/
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    # For DHCP it is recommended to remove these ports and instead add: network_mode: "host"
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "67:67/udp" # Only required if you are using Pi-hole as your DHCP server
      - "8080:80/tcp"
    environment:
      TZ: 'America/Argentina/Buenos_Aires'
      WEBPASSWORD: 'PoneUnPass!' # 'set a secure password here or it will be random'
    # Volumes store your data between container upgrades
    volumes:
      - './etc-pihole:/etc/pihole'
      - './etc-dnsmasq.d:/etc/dnsmasq.d'
    #   https://github.com/pi-hole/docker-pi-hole#note-on-capabilities
    cap_add:
      - NET_ADMIN # Required if you are using Pi-hole as your DHCP server, else not needed
    restart: unless-stopped
```

> Cambia la WEBPASSWORD por algo coherente, o lo dejas así y genera una random,
> visible desde los logs.
