
# vpn-container

#  VPN com WireGuard (Docker)

Projeto de criação de uma VPN utilizando WireGuard em container Docker, permitindo comunicação segura entre cliente e servidor através de um túnel criptografado.

---

##  Execução

Para iniciar o servidor VPN:

```bash
docker-compose up -d
```

O serviço será iniciado utilizando a porta **51820/UDP**.

---

##  Estrutura do Projeto

* `docker-compose.yml` → Configuração do container
* `wg0-server.conf` → Configuração do servidor VPN
* `client.conf` → Configuração do cliente VPN

---

##  Arquivos de Configuração

###  docker-compose.yml

Responsável por subir o container do WireGuard:

* Utiliza a imagem `linuxserver/wireguard`
* Expõe a porta `51820/UDP`
* Habilita permissões de rede (`NET_ADMIN` e `SYS_MODULE`)
* Ativa o encaminhamento de pacotes (`ip_forward`)
* Carrega o arquivo de configuração do servidor via volume

---

###  wg0-server.conf (Servidor)

Arquivo responsável pela configuração do servidor VPN.

#### Interface

* `Address = 10.0.0.1/24` → IP do servidor na rede VPN
* `ListenPort = 51820` → Porta de escuta
* `PrivateKey` → Chave privada do servidor

#### Regras de rede

* `PostUp` → Configura regras de roteamento e NAT com iptables
* `PostDown` → Remove as regras ao encerrar o serviço

#### Peer (Cliente)

* `PublicKey` → Chave pública do cliente
* `PresharedKey` → Chave adicional de segurança
* `AllowedIPs = 10.0.0.2/32` → IP permitido para o cliente

---

###  client.conf (Cliente)

Arquivo utilizado para conectar ao servidor VPN.

#### Interface

* `Address = 10.0.0.2/24` → IP do cliente
* `PrivateKey` → Chave privada do cliente
* `DNS = 1.1.1.1` → Servidor DNS

#### Peer (Servidor)

* `PublicKey` → Chave pública do servidor
* `PresharedKey` → Chave adicional de segurança
* `Endpoint` → IP e porta do servidor (ex: 192.168.15.4:51820)
* `AllowedIPs = 10.0.0.0/24` → Tráfego roteado pela VPN
* `PersistentKeepalive = 25` → Mantém a conexão ativa

---

##  Funcionamento

* O servidor WireGuard roda em um container Docker
* O cliente se conecta utilizando chaves criptográficas
* O tráfego é encapsulado em um túnel seguro
* O NAT permite acesso à rede externa através do servidor

---

##  Objetivo

Demonstrar a implementação de uma VPN segura utilizando WireGuard e Docker, com configuração manual de servidor e cliente, garantindo privacidade e integridade dos dados.
