# Oracle Cloud Setup

## Configuração do Ubuntu Server
1. Atualização da lista de pacotes:
```bash
sudo apt update
```

2. Atualização dos pacotes instalados:
```bash
sudo apt upgrade
```

3. Instalação de um editor de texto:
```bash
sudo apt install nano
```

### Firewall
1. Instalação do ufw:
```bash
sudo apt install ufw
```

2. Permitir conexão as portas SSH, WireGuard e MySQL:
```bash
sudo ufw allow 22/tcp
sudo ufw allow 51820/udp
sudo ufw allow 3306/tcp

sudo ufw enable
```

### Configuração do iptables
1. Verificar as regras atuais no iptables:
```bash
sudo iptables -L --line-numbers
```

2. Adicionar as regras na posição anterior à regra que rejeita tudo (WireGuard, MySQL):
```bash
sudo iptables -I INPUT 6 -p udp --dport 51820 -j ACCEPT
sudo iptables -I INPUT 7 -p tcp --dport 3306 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
```

3. Salvar as regras:
```bash
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

### Oracle Cloud
Adicionar as regras de entrada para o WireGuard e MySQL no painel da OCI:
![alt text](image-2.png)


## MySQL
1. Instalação do MySQL Server:
```bash
sudo apt install mysql-server
```

2. Configuração inicial segura:
```bash
sudo mysql_secure_installation
```

3. Abrir o terminal do MySQL:
```bash
sudo mysql
```

4. Criar um usuário para acesso remoto:
```sql
CREATE USER 'ryanr'@'%' IDENTIFIED BY 'password';
```

5. Alterar o plugin de autenticação para `mysql_native_password`, que é o mais comum e possui maior compatibilidade:
```sql
ALTER USER 'ryanr'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
```

6. Criar um banco de dados para teste:
```sql
CREATE SCHEMA test;
```

7. Garantindo as permissões do usuário para o banco:
```sql
GRANT ALL PRIVILEGES ON test.* TO 'ryanr'@'%';
FLUSH PRIVILEGES;
```

8. Modificar a linha `bind-address = 127.0.0.1` do arquivo `/etc/mysql/mysql.conf.d/mysqld.cnf` para `bind-address = 0.0.0.0`.

9. Reinicie o MySQL para aplicar as alterações:
```bash
sudo systemctl restart mysql
```

## Node-RED
<div align="justify">
Na documentação do Node-RED, encontramos um script para instalação e atualização do Node.js, npm e Node-RED em qualquer sistema operacional baseado em Debian. O comando abaixo fará o download e executará o script:
</div>

```bash
bash <(curl -sL https://raw.githubusercontent.com/node-red/linux-installers/master/deb/update-nodejs-and-nodered)
```
<div align="justify">
Depois de instalado, podemos iniciar o Node-RED com o comando:
</div>

```bash
node-red-start
```

