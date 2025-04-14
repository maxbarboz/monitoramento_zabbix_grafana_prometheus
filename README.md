# Estudo sobre Zabbix

## 🧠 Resumo:

- O MySQL armazena os dados do Zabbix.
- O Zabbix Server é o núcleo que processa os dados e se comunica com os agentes.
- O Zabbix Web fornece a interface gráfica para configuração e visualização.
- O Zabbix Agent coleta dados dos hosts monitorados.

Cada serviço é interdependente e a comunicação entre eles é feita por variáveis de ambiente, como DB_SERVER_HOST e ZBX_SERVER_HOST.

# INFORMAÇÕES IMPORTANTES

Utilizando container, podemos ter alguns erros a respeito do IP que o server utiliza no container, com isso podemos ter erros como:

- `Get value from agent failed: cannot connect to [[127.0.0.1]:10050]: [111] Connection refused`

Para resolver, é necessário alterar o IP no client web (Zabbix Web), seguir os passos a seguir:

![alt text](/img/image.png)

```shell
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' zabbix-agent
```