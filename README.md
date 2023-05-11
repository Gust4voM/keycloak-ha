# Keycloak sobre docker-compose 
## Nodos Keycloak

### Destacados del manifiesto:

En command le pasamos 3 parametros: - -b - 0.0.0.0 y - -Djgroups.bind_addr=[IP_del_host]

el -b 0.0.0.0 bindea las direcciones de la aplicacion al comodin, y el -Djgroups.bind_addr=[IP_del_host] bindea las ips de trabajo de los JGroups a nuestra IP, esto es escencial para que el cluster funcione -> ver: https://docs.jboss.org/jbossclustering/cluster_guide/5.1/html/jgroups.chapt.html#jgroups-transport para mas detalle

Protocolos de discovery -> JDBC_PING y TCPPING ver: https://www.keycloak.org/2019/05/keycloak-cluster-setup.html

JGROUPS_DISCOVERY_PROPERTIES -> como indica el comentario tienen una mezcla de lo que necesitamos para correr cualquiera de los dos protocolos de ping, las propiedades que no correspondan simplemente son ignoradas no rompen el servicio.

network_mode: host -> nos ahorra bindear todos los puertos explicitamente.

Documentacion oficial de KC wildfly: https://www.keycloak.org/docs/18.0/server_installation/#_standalone-ha-mode

## Nodo Postgress:

En este caso no hay mucho que destacar las variables de entorno se explican solas, le damos usuario, pass y un nombre a la base de datos para que Keycloak popule.
Persiste la info en un volumen para no perder configuraciones, sesiones o usuarios federados localmente en caso de que se apague.

## Levantando el servicio:

### Pre-requisitos:

#### Instalacion de Docker y plugins

```bash 
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine \
                  podman \
                  runc
```

```bash 
sudo yum install -y yum-utils
```

```bash 
#los repos de centos si funcionan
sudo dnf config-manager \
--add-repo=https://download.docker.com/linux/centos/docker-ce.repo 
```

```bash
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

```bash
sudo systemctl start docker
sudo systemctl enable docker
sudo reboot
```

#### Instalacion de Git 

```bash
sudo yum install git
```
### Despliegue:

#### Crear un usuario para el servicio y agregarlo al grupo docker

```bash
mkdir /home/keycloak
```

```bash
useradd -d /home/keycloak -s /bin/bash keycloak
usermod -aG docker keycloak
sudo reboot # para aplicar cambio de grupo
```
#### Crear una llave para el deploy

```bash
su - keycloak
ssh-keygen -t rsa -b 4096
cat .ssh/id_rsa.pub > authorized_keys
```
##### Agregar la llave al repo con permisos

#### Clono repo
```bash
cd /home/keycloak
git clone git@github.com:sandbox-trp/keycloak-ha.git
```
# PASOS PARA PARAMETRIZAR (WIP)

