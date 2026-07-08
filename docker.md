# AMBIENTES DOCKERIZADOS
Este documento tem como objetivo explicar e fornecer modelos de container para ambientes de desenvolvimento com docker, evitando ouvir a famosa frase: "Na minha máquina funciona".

Será fornecido algumas ferramentas ao longo desta ferramenta, sendo elas: 
* Java     
* Postgres
* Mysql
* MariaDB
* MongoDB 

---
## COMANDOS GLOBAIS DE MONITORAMENTO
| Ação            | Comando                               | Descrição                                              |
| :---            | :---                                  | :---                                                   |
| Status Global   | `docker ps -a`                        | Lista todos os containers e seus estados (Up, Exited)  |
| Uso de Recursos | `docker stats`                        | Monitora consumo de CPU e Memória em tempo real        |
| Ver Imagens     | `docker images`                       | Lista todas as imagens baixadas localmente             |
| Ver Logs        | `docker logs -f <nome_do_container>`  | Mostra todos os logs do container em execução          |
---
## COMANDOS DE CONTROLE
| AÇÃO            | COMANDO                               | DESCRIÇÃO                                              |
| :---            | :---                                  | :---                                                   |
| Parar           | `docker stop <id_ou_nome>`            | Para o container salvando oque precisa                 |
| Iniciar         | `docker start <id_ou_nome>`           | Inicia um container que foi parado                     |
| Reiniciar       | `docker restart <id_ou_nome>`         | Reinicia o container                                   |
| Matar           | `docker kill <id_ou_nome>`            | Mata a execução do container                           |
| Reinicia Docker | `sudo systemctl restart docker`       | Reinicia o Docker da máquina                           |  
---
## COMANDOS DE LIMPEZA
| Ação            | Comando                               | Descrição                                              |
| :---            | :---                                  | :---                                                   |
| Remover         | `docker rm <id_ou_nome>`              | Remove um container parado                             |
| Deletar         | `docker rmi <id_ou_nome_da_imagem>`   | Deleta uma imagem da sua máquina                       |
r Limpeza Geral   | `docker system prune`                 | Apaga todos os containers, redes e cache não utilizados|
---
## COMANDOS DE ACESSO AO BASH
| Ação             | Comando                                    | Descrição                                         |
| :---             | :---                                       | :---                                              |
| Acessar Bash     | `docker exec -it <nome_do_container> bash` | Abre terminal interativo bash do container        |
| Acessar SH       | `docker exec -it <nome_do_container> sh`   | Abre um terminal simples                          |
| Sair do Terminal | `exit`                                     | Sai do modo interativo e volta para sua máquina   |

## GERENCIAMENTO DO DOCKER - COMPOSE
| Comando                        | Ação                                                      |
| :---                           | :---                                                      |
| `docker compose up --build -d` | Cria e inicia os contêiners                               |
| `docker compose build`         | Realiza a etapa de build das imagens que serão utilizadas |
| `docker compose logs -f app`   | Visualiza os logs dos contêiners                          |
| `docker compose restart`       | Reinicia os contêiners                                    |
| `docker compose ps`            | Lista os contêiners                                       |
| `docker compose scale`         | Permite aumentar o número de réplicas de um contêiner     |
| `docker compose start`         | Inicia os contêiners                                      |
| `docker compose stop`          | Paralisa os contêiners                                    |
| `docker compose down -v`       | Paralisa e remove todos os contêiners (tudo)              |

<br><br><br>

# Exemplo de Containers docker

## Java
O Java é uma linguagem de programação multiplataforma e orientada a objetos que opera sob o famoso princípio "escreva uma vez, rode em qualquer lugar" (write once, run anywhere). Por conta de sua alta segurança e escalabilidade, ela é amplamente utilizada no desenvolvimento de sistemas corporativos robustos, aplicativos Android e APIs de grande porte no backend.

```bash
FROM docker.io/library/eclipse-temurin:25-jdk-alpine AS builder
WORKDIR /build
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN chmod +x mvnw
RUN ./mvnw dependency:go-offline
COPY src ./src
RUN ./mvnw clean package -DskipTests

FROM docker.io/library/eclipse-temurin:25-jre-alpine
WORKDIR /app
COPY --from=builder /build/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

## Postgresql
É um sistema de gerenciamento de banco de dados relacional objeto (SGBD), tendo amplo espaço no mercado pela sua robustez, confiabilidade, desempenho avançado e conformidade com os padrões SQL.

### Comando para utilizar o Postgresql via container

```bash
docker run --name postgres-db \
-e POSTGRES_PASSWORD=minhasenha \
-e POSTGRES_USER=meuusuario \
-e POSTGRES_DB=meubanco \
-p 5432:5432 \
-d postgres:latest
```

#### Explicação de cada comando
* `docker run`: Executa um novo container a partir de uma imagem.
* `--name postgres-db`: Define o nome do container.
* `-e POSTGRES_PASSWORD=minhasenha`: Define a senha do usuário.
* `-e POSTGRES_USER=meuusuario`: Define usuário padrão do banco.
* `-e POSTGRES_DB=meubanco`: Define o nome do banco de dados criado automaticamente.
* `-p 5432:5432`: Mapeia a porta do host para a porta do container.
* `-d postgres:latest`: Define o container em segundo placo e a imagem que será usada mais sua versão.

## Mysql
É um sistema de gerenciamento de banco de dados relacional (SGBDR), sendo muito popular por conta da sua compatibilidade, desempenho e escalabilidade.

### Comando para utilizar o Mysql via container

```bash
docker run --name mysql-db \
-e MYSQL_ROOT_PASSWORD=minhasenha \
-e MYSQL_USER=seuusuario \
-e MYSQL_DATABASE=meubanco \
-p 3333:3306 \
-d mysql:latest
```

#### Explicação de cada comando
* `docker run`: Executa um novo container a partir de uma imagem.
* `--name mysql-db`: Define o nome do container.
* `-e MYSQL_ROOT_PASSWORD=minhasenha`: Define a senha do usuário.
* `-e MYSQL_USER=meuusuario`: Define usuário padrão do banco.
* `-e MYSQL_DATABASE=meubanco`: Define o nome do banco de dados criado automaticamente.
* `-p 3305:3306`: Mapeia a porta do host para a porta do container.
* `-d mysql:latest`: Define o container em segundo placo e a imagem que será usada mais sua versão.

## Mariadb
O MariaDB é um sistema de gerenciamento de banco de dados relacional de código aberto, desenvolvido pelos criadores originais do MySQL como uma alternativa robusta, rápida e totalmente compatível com ele. Ele é amplamente utilizado no mercado devido ao seu alto desempenho, recursos avançados de segurança e uma comunidade ativa que garante constantes inovações.

### Comandos para utilizar o Mariadb via container
```bash
docker run --name maria-db \
-e MARIADB_ROOT_PASSWORD=suasenha \
-e MARIADB_DATABASE=meubanco \
-e MARIADB_USER=seuusuario \
-e MARIADB_PASSWORD=suasenha \
-p 3306:3306 \
-d mariadb:latest
```

### Explicação de cada comando

* `docker run`: Executa um novo container a partir de uma imagem.
* `--name maria-db`: Define o nome do container.
* `-e MARIADB_ROOT_PASSWORD=suasenha`: Define a senha de administrador (root) do banco de dados.
* `-e MARIADB_DATABASE=meubanco`: Define o nome do banco de dados criado automaticamente.
*` -e MARIADB_USER=seuusuario`: Define o usuário padrão adicional criado no banco.
* `-e MARIADB_PASSWORD=suasenha`: Define a senha para o usuário padrão adicional criado.
* `-p 3306:3306`: Mapeia a porta do host para a porta do container.
* `-d mariadb:latest`: Define o container em segundo plano e a imagem que será usada mais sua versão.

## MongoDB
O MongoDB é um banco de dados NoSQL orientado a documentos que armazena informações em formatos flexíveis semelhantes ao JSON, em vez de tabelas e linhas tradicionais. Ele é altamente escalável e ideal para lidar com grandes volumes de dados não estruturados, permitindo modificações rápidas na estrutura dos dados sem a necessidade de migrações complexas.

```bash
docker run -d \
--name mongo-db \ 
-p 27017:27017 \ 
-v mongo_data:/data/db \
mongo:latest
```

### Explicação de cada comando

* `docker run`: Executa um novo container a partir de uma imagem.
* `-d`: Define o container em segundo plano (detached mode).
* `--name mongo-db`: Define o nome do container.
* `-p 27017:27017`: Mapeia a porta do host para a porta do container.
* `-v mongo_data:/data/db`: Cria e mapeia um volume nomeado para persistir os dados do banco fora do ciclo de vida do container.
* `mongo:latest`: Define a imagem que será usada mais sua versão.