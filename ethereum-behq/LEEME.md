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

En caso de entrar en conflicto con algún recurso ya ocupado se aceptan los siguiente parámetros:

| **Nombre** | **Valor por defecto** | **Descripción** | 
|------------|-----------------------|-----------------|
| **HOSTNAME** | 0.0.0.0 | Dirección ip |
| **PORT**   | 30303 | Puerto de escucha |
| **RPCPORT** | 8545 | Puerto de escucha del servidor HTTP-RPC |
| **RPCCORSDOMAIN** | * | Listado de dominios separados por comas que acepta peticiones de origen cruzados |
| **WSPORT**  | 8546 | Puerto de escucha del servidor WS-RPC |
| **NETWORKID** | 201710 | NetworkID Personalizado (integer, 1=Frontier, 3=Ropsten) |
| **RPCAPI** | admin,db,eth,debug,miner,net,shh,txpool,personal,web3|APIs servidas via interfaz HTTP-RPC |
| **VERBOSITY** | 3 | Nivel de log: 0=silent, 1=error, 2=warn, 3=info, 4=core, 5=debug, 6=detail |
| **MINERTHREADS** | 1 | Nº de hilos para minado |
    

La forma más sencilla es ejecutarlo con variables de entorno:

```
PORT=40404 RPCPORT=18545 docker-compose -f docker-compose.yml up 
```

Para cualquier cambio que necesitemos es necesario regenerar la imagen de docker:

```
docker rm `docker ps -a| grep ethereum-behq | awk {'print $1'}`
docker-compose -f docker-compose-init.yml up
VAR1=VALOR1 VAR2=VALOR2 docker-compose -f docker-compose.yml up 
```

## Comandos básicos


### Arranque imagen docker (una vez creada)

```
docker start ethereum-behq
```

### Parada imagen docker (una vez creada)

```
docker stop ethereum-behq
```

### Consulta de ip de la imagen docker

```
docker inspect ethereum-behq | grep IPAddress | cut -d '"' -f 4 | grep -v ^$
```

### Logs de la imagen docker

```
docker logs -f `docker ps -a| grep ethereum-behq| awk {'print $1'}`
```

### Versión de software

```
docker exec -ti ethereum-behq geth version
```

### Creación de una cuenta

```
docker exec -ti ethereum-behq geth account new
```

### Attach de consola Javascript

```
docker exec -ti ethereum-behq geth attach
```

### Conexión a través de [Mist](https://github.com/ethereum/mist/releases)

```
mist --rpc http://0.0.0.0:8545 --swarmurl "null"
```

## Operaciones básicas dentro de la consola Javascript:

```
docker exec -ti ethereum-behq geth attach

Welcome to the Geth JavaScript console!

instance: Geth/ethereum-behq/v1.7.1-unstable/linux-amd64/go1.9
coinbase: 0x6a45ab210b8ad40b34167cd49be056ce2ac2f864
at block: 3349 (Sat, 28 Oct 2017 00:38:22 UTC)
 datadir: /root/.ethereum
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

# Para de minar
> miner.stop()
true

# Inicio minar con 1 hilo
> miner.start(1)
null

# Obtener dirección de coinbase
> eth.coinbase
"0x6a45ab210b8ad40b34167cd49be056ce2ac2f864"

# Obtener balance
> web3.fromWei(eth.getBalance(eth.coinbase), "ether");
15465

# Obtener bloque actual
> eth.blockNumber
3349

# Listado de cuentas:
> personal.listAccounts
["0x6a45ab210b8ad40b34167cd49be056ce2ac2f864"]

> remitente = personal.listAccounts[0]     # También es `eth.coinbase`
> destinatario_nuevo = personal.newAccount()
Passphrase: ****         # demo
Repeat passphrase: ****  # demo
"0xf3ae33adfc3e42abe054fc77a6691063d9218cf3"

# Listado de cuentas:
> personal.listAccounts

["0x6a45ab210b8ad40b34167cd49be056ce2ac2f864", "0xf3ae33adfc3e42abe054fc77a6691063d9218cf3"]

# Enviar transacción de `remitente` a `destinatario_nuevo`
> personal.unlockAccount(remitente)
Unlock account 0x6a45ab210b8ad40b34167cd49be056ce2ac2f864
Passphrase ****   # behq
true
> personal.unlockAccount(destinatario_nuevo, "demo")
true
> eth.sendTransaction({from: remitente, to: destinatario_nuevo, value: web3.toWei(1000, "ether")})
"0x773d6aaf1f130514e5f8844c6e830b377c4fec58521a25b462c51facce3ad613"

> web3.fromWei(eth.getBalance(remitente), "ether")
15465

> web3.fromWei(eth.getBalance(destinatario_nuevo), "ether")
0

# Listado transacciones pendientes
> eth.pendingTransactions
[{
    blockHash: null,
    blockNumber: null,
    from: "0x6a45ab210b8ad40b34167cd49be056ce2ac2f864",
    gas: 90000,
    gasPrice: 18000000000,
    hash: "0x773d6aaf1f130514e5f8844c6e830b377c4fec58521a25b462c51facce3ad613",
    input: "0x",
    nonce: 2,
    r: "0xbc3b866265e48cd1b04b0bd4e6398b0a916c3ac5d2b6fc559684c3ea97155511",
    s: "0x20270f35a795d1f4ac39be5db730d764021143ff1ff7e5ffdd81a947c26517cb",
    to: "0xf3ae33adfc3e42abe054fc77a6691063d9218cf3",
    transactionIndex: 0,
    v: "0x2a",
    value: 1e+21
}]

# ... Esperamos a que se realice la transacción ...
> eth.pendingTransactions
[]
> eth.blockNumber
3380
> web3.fromWei(eth.getBalance(remitente), "ether")
14675

> web3.fromWei(eth.getBalance(destinatario_nuevo), "ether")
1000
```

# Referencias

* https://github.com/eduadiez/private_chain_ethereum_docker
* https://hub.docker.com/r/ethereum/client-go/
* https://github.com/ethereum/go-ethereum
* https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options
