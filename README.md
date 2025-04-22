# Zabbix + Grafana + Prometheus Docker Compose

Este projeto usa Docker Compose para configurar uma stack de monitoramento composta por **Zabbix**, **Grafana**, **Prometheus**, e outros componentes. A seguir está a descrição de cada serviço, como configurá-los, e a explicação do arquivo `.env`.

## Serviços

### 1. **MySQL**
- **Imagem**: `mysql:8.0`
- **Container Name**: `zabbix-mysql`
- **Função**: Serve como o banco de dados para o **Zabbix Server**, armazenando todas as informações de configuração e monitoramento.
- **Variáveis de Ambiente**:
  - `MYSQL_ROOT_PASSWORD`: Senha do usuário root do MySQL.
  - `MYSQL_DATABASE`: Nome do banco de dados a ser criado para o Zabbix.
  - `MYSQL_USER`: Nome do usuário do MySQL.
  - `MYSQL_PASSWORD`: Senha do usuário do MySQL.
- **Links**:
  - [Documentação do MySQL](https://hub.docker.com/_/mysql)

### 2. **Zabbix Server**
- **Imagem**: `zabbix/zabbix-server-mysql:alpine-6.4-latest`
- **Container Name**: `zabbix-server`
- **Função**: A aplicação central de monitoramento que coleta, armazena e processa métricas de servidores e dispositivos monitorados.
- **Dependências**: Depende do serviço **MySQL** para armazenar os dados.
- **Variáveis de Ambiente**:
  - `DB_SERVER_HOST`: Endereço do servidor MySQL.
  - `MYSQL_ROOT_PASSWORD`: Senha do usuário root do MySQL.
- **Links**:
  - [Documentação do Zabbix](https://www.zabbix.com/documentation/current/manual)

### 3. **Zabbix Web**
- **Imagem**: `zabbix/zabbix-web-apache-mysql:alpine-6.4-latest`
- **Container Name**: `zabbix-web`
- **Função**: Interface web do Zabbix, permitindo que os usuários visualizem os dados de monitoramento, configurem alertas e explorem as métricas coletadas.
- **Dependências**: Depende do **Zabbix Server** para funcionar corretamente.
- **Variáveis de Ambiente**:
  - `DB_SERVER_HOST`: Endereço do servidor MySQL.
  - `MYSQL_DATABASE`: Nome do banco de dados do Zabbix.
  - `MYSQL_USER`: Nome do usuário do MySQL.
  - `MYSQL_PASSWORD`: Senha do usuário do MySQL.
  - `ZBX_SERVER_HOST`: Endereço do servidor Zabbix.
  - `PHP_TZ`: Fuso horário (por exemplo, `America/Sao_Paulo`).
- **Links**:
  - [Documentação do Zabbix Web](https://www.zabbix.com/documentation/current/manual/web_interface)

### 4. **Zabbix Agent**
- **Imagem**: `zabbix/zabbix-agent:alpine-6.4-latest`
- **Container Name**: `zabbix-agent`
- **Função**: O agente Zabbix é instalado nos servidores ou dispositivos a serem monitorados, coletando métricas e enviando-as ao **Zabbix Server**.
- **Dependências**: Depende do **Zabbix Server** para enviar as métricas coletadas.
- **Variáveis de Ambiente**:
  - `ZBX_SERVER_HOST`: Endereço do servidor Zabbix.
  - `ZBX_HOSTNAME`: Nome do host do agente.
  - `ZBX_LISTENIP`: IP de escuta do agente.
- **Links**:
  - [Documentação do Zabbix Agent](https://www.zabbix.com/documentation/current/manual/agents)

### 5. **Grafana**
- **Imagem**: `grafana/grafana`
- **Container Name**: `grafana`
- **Função**: Ferramenta de visualização de dados que se conecta ao Zabbix ou outros bancos de dados para criar dashboards interativos.
- **Portas**:
  - `3333:3000`: Porta do Grafana (acesso via `localhost:3333`).
- **Links**:
  - [Documentação do Grafana](https://grafana.com/docs/grafana/latest/)

### 6. **Prometheus**
- **Imagem**: `prom/prometheus`
- **Container Name**: `prometheus`
- **Função**: Sistema de monitoramento e alerta. Coleta métricas de serviços e armazena essas métricas para consulta.
- **Portas**:
  - `9090:9090`: Porta do Prometheus (acesso via `localhost:9090`).
- **Links**:
  - [Documentação do Prometheus](https://prometheus.io/docs/introduction/overview/)

### 7. **Apache Exporter**
- **Imagem**: `bitnami/apache-exporter:latest`
- **Container Name**: `apache-exporter`
- **Função**: Exportador de métricas para o Prometheus, coletando dados do Apache HTTP Server.
- **Variáveis de Ambiente**:
  - `SCRAPE_URI`: URI para obter as métricas do Apache (ex: `http://zabbix-web:8080/server-status?auto`).
- **Links**:
  - [Documentação do Apache Exporter](https://github.com/prometheus-community/apache_exporter)

## Configuração do `.env`

O arquivo `.env` é usado para armazenar variáveis de ambiente sensíveis e configurar os valores de acesso aos serviços no Docker Compose. Ao usar variáveis de ambiente, você pode facilmente configurar e reutilizar seu projeto sem precisar editar diretamente o `docker-compose.yml`.

### Exemplo de `.env`

```env
MYSQL_ROOT_PASSWORD=zabbix_root_password
MYSQL_DATABASE=zabbix
MYSQL_USER=zabbix_user
MYSQL_PASSWORD=zabbix_password
```

### Como Funciona

- As variáveis definidas no arquivo `.env` são referenciadas no arquivo `docker-compose.yml` com a sintaxe `${VAR_NAME}`.
- Isso permite que você mantenha valores sensíveis fora do arquivo de configuração principal, facilitando a troca de credenciais sem alterar o código do serviço.
  
Certifique-se de **nunca** incluir o arquivo `.env` em um repositório público. Para isso, adicione o `.env` ao seu `.gitignore`.

## Instruções para Execução

1. **Certifique-se de ter o Docker e o Docker Compose instalados**. Caso não tenha, siga a documentação oficial:
   - [Instalar Docker](https://docs.docker.com/get-docker/)
   - [Instalar Docker Compose](https://docs.docker.com/compose/install/)

2. **Configure o arquivo `.env`** com as variáveis apropriadas para o seu ambiente.

3. **Execute o Docker Compose** com o seguinte comando:
   ```bash
   docker-compose up -d
   ```

4. **Acesse as interfaces**:
   - **Zabbix Web**: [http://localhost:8080](http://localhost:8080)
   - **Grafana**: [http://localhost:3333](http://localhost:3333)
   - **Prometheus**: [http://localhost:9090](http://localhost:9090)