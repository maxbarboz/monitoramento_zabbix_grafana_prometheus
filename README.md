🧠 Resumo:

- O MySQL armazena os dados do Zabbix.
- O Zabbix Server é o núcleo que processa os dados e se comunica com os agentes.
- O Zabbix Web fornece a interface gráfica para configuração e visualização.
- O Zabbix Agent coleta dados dos hosts monitorados.

Cada serviço é interdependente e a comunicação entre eles é feita por variáveis de ambiente, como DB_SERVER_HOST e ZBX_SERVER_HOST.