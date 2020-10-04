Role Name
=========

El role descarga e instala la paquetería necesaria para utilizar ProxySQL.
Se han creado el código para 3 cluster, aunque los dos últimos se encuentra comentados.

El role contiene tasks, handlers y templates.
- Tasks: Descarga e instala la versión de ProxySQL indica en las variables. Por último copia el fichero .j2 a /etc/proxysql.cnf con las variables correspondientes.
- Handlers: Elimina el fichero proxysql.db y reinicia el servicio cuando hay cambios en el archivo de configuración.
- Templates: Fichero de configuración, que contiene bucles mediante los cuales se incluye código para componer el fichero.

- Variables: las variables se encuentran a nivel de inventario, y pueden ser distintas para los entornos de Pre y Pro.


Role Variables
--------------

Las variables se dividen en ficheros para su mejor gestión:

| vars_file                    | variable                  | default_value                          |
|------------------------------|---------------------------|----------------------------------------|
| global                       | proxysql_version          | 2.0.8                                  |
| global                       | proxysql_user             | admin                                  |
| global                       | proxysql_password         | vault_file                             |
| global                       | proxysql_exporter_user    | admin                                  |
| global                       | proxysql_exporter_password| vault_file                             |
| global                       | proxysql_monitor_username | root                                   |
| global                       | proxysql_monitor_password | vault_file                             |
| global                       | cluster0_hostgroup_ro     | 10                                     |
| global                       | cluster0_hostgroup_rw     | 20                                     |
| global                       | cluster1_hostgroup_ro     | 30                                     |
| global                       | cluster1_hostgroup_rw     | 40                                     |
| global                       | cluster2_hostgroup_ro     | 50                                     |
| global                       | cluster2_hostgroup_rw     | 60                                     |
| mysql_query_rules_reader     | match_digest              | "^SELECT"                              |
| mysql_query_rules_reader     | hostgroup                 | "{{ clusterX_hostgroup_ro }}"          |
| mysql_query_rules_writer     | match_digest              | "^SELECT.*FOR UPDATE$"                 |
| mysql_query_rules_writer     | hostgroup                 | "{{ clusterX_hostgroup_rw }}"          |
| mysql_replication_hostgroup  | writer_hostgroup          | "{{ clusterX_hostgroup_rw }}"          |
| mysql_replication_hostgroup  | reader_hostgroup          | "{{ clusterX_hostgroup_ro }}"          |
| mysql_replication_hostgroup  | comment                   | aws-rds-instances                      |
| mysql_replication_hostgroup  | check_type                | innodb_read_only                       |
| mysql_server_reader          | address                   | endpoint                               |
| mysql_server_reader          | hostgroup                 | "{{ clusterX_hostgroup_ro }}"          |
| mysql_server_reader          | max_connections           | 150                                    |
| mysql_server_writer          | address                   | endpoint                               |
| mysql_server_writer          | hostgroup                 | "{{ clusterX_hostgroup_rw }}"          |
| mysql_server_writer          | max_connections           | 150                                    |
| mysql_special_rules          | match_pattern             | "/[*] keep_multiplexing_enabled [*]/$" |
| mysql_special_rules          | multiplex                 | 2                                      |
| mysql_users                  | username                  | root                                   |
| mysql_users                  | password                  | "{{ vault_proxysql_mysql_users }}"     |
| mysql_users                  | default_hostgroup         | "{{ cluster0_hostgroup_rw }}"          |
| mysql_users                  | max_connections           | 150                                    |
| mysql_users                  | default_schema            | default_X                              |


Dependencies
------------
Any.

Example Playbook
----------------

ansible-playbook -i inventory proxysql.yml --private-key path_private_key

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
