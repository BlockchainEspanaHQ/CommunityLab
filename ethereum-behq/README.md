# Instalación

## Prerequisitos

[Instalación Docker](https://docs.docker.com/engine/installation/)

## Creación de imagen docker

El caso más simple es generarla con los parámetros por defecto:

```
docker-compose -f docker-compose-init.yml up
docker-compose -f docker-compose.yml up
```

### Personalización de imagen docker

En caso de entrar en conflicto con algún recuso ya ocupado se aceptan los siguiente parámetros:

| **Nombre** | **Valor por defecto** | **Descripción** | 
|------------|-----------------------|-----------------|
| **HOSTNAME** | 0.0.0.0 | Dirección ip |
| **PORT**   | 30303 | Puerto de escucha |
| **RCPPORT** | 8545 | Puerto de escucha del servidor HTTP-RPC |
| **WSPORT**  | 8546 | Puerto de escucha del servidor WS-RPC |
| **NETWORKID** | 201710 | Identificador de red |
| **RCPAPI** | admin,db,eth,debug,miner,net,shh,txpool,personal,web3|APIs servidas via interfaz HTTP-RPC |
| **VERBOSITY** | 3 | Nivel de detalle |
| **MINERTHREADS** | 1 | Nº de hilos para minado |
    

La forma más sencilla es ejecutarlo con variables de entorno:

```
PORT=40404 RCPPORT=18545 docker-compose -f docker-compose.yml up 
```

Para cualquier cambio que necesitemos es necesario regenerar la imagen de docker:

```
docker rm `docker ps -a| grep ethereum-behq | awk {'print $1'}`
docker-compose -f docker-compose-init.yml up
VAR1=VALOR1 VAR2=VALOR2 docker-compose -f docker-compose.yml up 
```

## Operaciones básicas

### Arranque imagen

```
docker start ethereum-behq
```

### Parada imagen

```
docker stop ethereum-behq
```

### Consulta de ip:

```
docker inspect ethereum-behq | grep IPAddress | cut -d '"' -f 4 | grep -v ^$
```

## Conexión a la blockchain

```
mist --rpc http://0.0.0.0:8545 --swarmurl "null"
```