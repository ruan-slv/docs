# Guia Completo e Definitivo de cURL: Do Básico ao Avançado

Este documento foi estruturado como um guia de estudos prático e aprofundado sobre o **cURL**, cobrindo desde seus conceitos elementares até técnicas avançadas de depuração, automação e análise de desempenho em redes.

---

## Sumário
1. [Introdução ao cURL](#1-introdução-ao-curl)
2. [Sintaxe e Estrutura Básica](#2-sintaxe-e-estrutura-básica)
3. [Dicionário de Flags e Opções Principais](#3-dicionário-de-flags-e-opções-principais)
4. [Métodos HTTP e Manipulação de Dados](#4-métodos-http-e-manipulação-de-dados)
5. [Trabalhando com Headers e Autenticação](#5-trabalhando-com-headers-e-autenticação)
6. [Gerenciamento de Cookies e Sessões](#6-gerenciamento-de-cookies-e-sessões)
7. [Upload e Download de Arquivos](#7-upload-e-download-de-arquivos)
8. [SSL/TLS e Segurança](#8-ssltls-e-segurança)
9. [Debugging e Métricas de Performance](#9-debugging-e-métricas-de-performance)
10. [Recursos Avançados (HTTP/2, HTTP/3, Paralelismo e Proxies)](#10-recursos-avançados)
11. [Automação em Shell e Parsing com `jq`](#11-automação-em-shell-e-parsing-com-jq)
12. [Cheat Sheet / Tabela de Referência Rápida](#12-cheat-sheet)

---

## 1. Introdução ao cURL

O **cURL** (Client for URLs) é uma ferramenta de linha de comando e uma biblioteca (`libcurl`) de código aberto utilizada para transferir dados por meio de diversos protocolos de rede, como **HTTP, HTTPS, FTP, FTPS, SFTP, SMTP, POP3, IMAP, TELNET, MQTT**, entre outros.

---

## 2. Sintaxe e Estrutura Básica

A sintaxe geral do cURL é:

```bash
curl [opções] [URL]
```

### Exemplo Simples (Requisição GET Padrão):
```bash
curl https://api.github.com/zen
```
> Por padrão, o cURL envia uma requisição `GET` e imprime o corpo da resposta (response body) diretamente no stdout (terminal).

---

## 3. Dicionário de Flags e Opções Principais

Abaixo estão as flags mais utilizadas, organizadas por categoria:

| Flag / Opção | Nome Longo | Descrição |
| :--- | :--- | :--- |
| `-X` | `--request` | Especifica o método de requisição HTTP (GET, POST, PUT, DELETE, etc.). |
| `-H` | `--header` | Adiciona um cabeçalho (header) personalizado à requisição. |
| `-d` | `--data` | Envia dados no corpo da requisição (HTTP POST/PUT). |
| `--data-raw` | `--data-raw` | Envia dados brutos (útil se o dado começar com `@`). |
| `--data-urlencode` | `--data-urlencode` | Codifica automaticamente os dados para URL (URL encoding). |
| `-i` | `--include` | Exibe os cabeçalhos da **resposta** junto com o corpo. |
| `-I` | `--head` | Envia uma requisição `HEAD` (retorna apenas os cabeçalhos da resposta). |
| `-v` | `--verbose` | Modo detalhado: exibe todo o handshaking, cabeçalhos enviados e recebidos. |
| `-s` | `--silent` | Modo silencioso: esconde barra de progresso e mensagens de erro. |
| `-S` | `--show-error` | Usado junto com `-s` para exibir erros caso a requisição falhe. |
| `-o` | `--output` | Salva a resposta em um arquivo local com nome especificado. |
| `-O` | `--remote-name` | Salva o arquivo localmente mantendo o nome do arquivo remoto. |
| `-L` | `--location` | Segue redirecionamentos HTTP (códigos 301, 302, 307, 308). |
| `-u` | `--user` | Informa usuário e senha para autenticação (Basic/Digest). |
| `-F` | `--form` | Envia dados multipart/form-data (usado para upload de arquivos). |
| `-b` | `--cookie` | Envia cookies na requisição (String ou caminho de arquivo). |
| `-c` | `--cookie-jar` | Salva os cookies recebidos da resposta em um arquivo. |
| `-k` | `--insecure` | Permite conexões HTTPS "inseguras" (ignora verificação de certificado SSL). |
| `-x` | `--proxy` | Define um servidor proxy para intermediar a requisição. |
| `-m` | `--max-time` | Tempo máximo em segundos permitido para toda a operação. |
| `--connect-timeout` | `--connect-timeout` | Tempo máximo em segundos para estabelecer a conexão inicial. |
| `-w` | `--write-out` | Exibe variáveis de métricas formatadas após a execução. |

---

## 4. Métodos HTTP e Manipulação de Dados

### 4.1. GET (Consultar Recursos)
Por padrão, o cURL executa requisições GET.

```bash
# Requisição básica
curl https://jsonplaceholder.typicode.com/posts/1

# GET passando query parameters na URL
curl "https://jsonplaceholder.typicode.com/posts?userId=1"
```

### 4.2. POST (Criar Recursos)
Para enviar dados via `POST`, utilize `-X POST` e a flag `-d` para o payload.

#### Envio com JSON:
```bash
curl -X POST https://jsonplaceholder.typicode.com/posts   -H "Content-Type: application/json"   -d '{"title": "Novo Post", "body": "Conteúdo do artigo", "userId": 1}'
```

#### Envio Form-Urlencoded:
```bash
curl -X POST https://example.com/api/login   -d "username=admin&password=123456&grant_type=password"
```

#### Envio usando arquivo JSON externo (`@`):
```bash
curl -X POST https://jsonplaceholder.typicode.com/posts   -H "Content-Type: application/json"   -d @payload.json
```

### 4.3. PUT e PATCH (Atualizar Recursos)
* `PUT`: Substituição completa do recurso.
* `PATCH`: Atualização parcial do recurso.

```bash
# Atualização via PUT
curl -X PUT https://jsonplaceholder.typicode.com/posts/1   -H "Content-Type: application/json"   -d '{"title": "Título Atualizado", "body": "Novo conteúdo", "userId": 1}'

# Atualização via PATCH
curl -X PATCH https://jsonplaceholder.typicode.com/posts/1   -H "Content-Type: application/json"   -d '{"title": "Apenas o Título Mudou"}'
```

### 4.4. DELETE (Remover Recursos)
```bash
curl -X DELETE https://jsonplaceholder.typicode.com/posts/1
```

---

## 5. Trabalhando com Headers e Autenticação

### 5.1. Custom Headers
Você pode enviar múltiplos cabeçalhos adicionando várias flags `-H`.

```bash
curl https://api.example.com/data   -H "Accept: application/json"   -H "X-Custom-Header: ValorAtributo"   -H "User-Agent: MeuAppCliente/1.0"
```

### 5.2. Autenticação Basic Auth
A flag `-u` constrói automaticamente o cabeçalho `Authorization: Basic <base64>`:

```bash
curl -u "usuario:senha" https://api.example.com/protected
```

### 5.3. Autenticação Bearer Token (JWT / OAuth2)
Padrão muito comum em APIs modernas:

```bash
curl https://api.example.com/v1/user/profile   -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

---

## 6. Gerenciamento de Cookies e Sessões

O cURL possui suporte nativo para persistir e reenviar cookies.

### 6.1. Salvar Cookies da Resposta (`-c` / `--cookie-jar`)
```bash
curl -c cookies.txt -X POST https://example.com/login   -d "username=user&password=pass"
```

### 6.2. Enviar Cookies em Requisições Futuras (`-b` / `--cookie`)
```bash
# Lendo cookies de um arquivo gerado
curl -b cookies.txt https://example.com/dashboard

# Enviando cookies diretamente na linha de comando
curl -b "session_id=xyz123; theme=dark" https://example.com/dashboard
```

---

## 7. Upload e Download de Arquivos

### 7.1. Download de Arquivos
```bash
# Salvar com um nome personalizado (-o)
curl -o imagem.jpg https://example.com/foto_HD.jpg

# Salvar mantendo o nome do arquivo remoto (-O)
curl -O https://example.com/downloads/relatorio.pdf

# Retomar um download interrompido (-C -)
curl -C - -O https://example.com/downloads/arquivo_grande.iso
```

### 7.2. Upload de Arquivos via Multipart Form Data (`-F`)
A flag `-F` envia os dados como `multipart/form-data`, simulando um `<form enctype="multipart/form-data">`. Use o prefixo `@` para especificar o caminho do arquivo.

```bash
curl -X POST https://example.com/api/upload   -H "Authorization: Bearer <token>"   -F "file=@/caminho/para/documento.pdf"   -F "description=Relatório Financeiro 2026"
```

---

## 8. SSL/TLS e Segurança

 Ao trabalhar com ambientes de desenvolvimento local, certificados autoassinados ou redes corporativas, ajustes SSL podem ser necessários:

```bash
# Ignorar validação de certificado SSL (USAR COM CAUTELA)
curl -k https://dev.local-api.internal

# Especificar um certificado de Autoridade Certificadora (CA) customizado
curl --cacert /path/to/ca-bundle.crt https://secure.api.com

# Usar certificado de cliente (mTLS - Mutual TLS)
curl --cert client.crt --key client.key https://mtls.example.com
```

---

## 9. Debugging e Métricas de Performance

### 9.1. Inspecionando Cabeçalhos da Resposta (`-i` e `-I`)
```bash
# Exibe cabeçalhos da resposta + corpo
curl -i https://httpbin.org/get

# Exibe APENAS os cabeçalhos da resposta (Requisição HEAD)
curl -I https://httpbin.org/get
```

### 9.2. Modo Verboso e Trace (`-v` e `--trace`)
```bash
# Exibe requisição (>), resposta (<) e informações da conexão (*)
curl -v https://httpbin.org/get

# Trace completo incluindo dump hexadecimal (útil para protocolos de baixo nível)
curl --trace-ascii trace.txt https://httpbin.org/get
```

### 9.3. Análise de Desempenho HTTP com `-w` (`--write-out`)
O cURL permite extrair métricas exatas de tempo para diagnosticar gargalos de rede (DNS, Handshake TLS, TTFB, etc.).

Crie um arquivo de formato `curl-format.txt`:
```text
    time_namelookup:  %{time_namelookup}s

       time_connect:  %{time_connect}s

    time_appconnect:  %{time_appconnect}s

   time_pretransfer:  %{time_pretransfer}s

      time_redirect:  %{time_redirect}s

 time_starttransfer:  %{time_starttransfer}s

                    ----------

         time_total:  %{time_total}s

        http_code:  %{http_code}

```

Execute a requisição formatada:
```bash
curl -w "@curl-format.txt" -o /dev/null -s https://google.com
```

---

## 10. Recursos Avançados

### 10.1. Requisições Paralelas (`--parallel` ou `-Z`)
Disponível em versões recentes do cURL para efetuar múltiplos downloads/requests concorrentes na mesma execução.

```bash
curl -Z -O https://example.com/file1.zip -O https://example.com/file2.zip
```

### 10.2. Forçando Versão do Protocolo (HTTP/1.1, HTTP/2, HTTP/3)
```bash
curl --http1.1 https://example.com
curl --http2 https://example.com
curl --http3 https://cloudflare.com
```

### 10.3. Uso de Proxies
```bash
# Proxy HTTP/HTTPS
curl -x http://proxy.empresa.com:8080 https://api.example.com

# Proxy SOCKS5 com autenticação
curl -x socks5://user:pass@proxy.local:1080 https://api.example.com
```

### 10.4. Limitação de Banda (`--limit-rate`)
```bash
# Limita o download a no máximo 500 Kilobytes por segundo
curl --limit-rate 500k -O https://example.com/grande_arquivo.iso
```

---

## 11. Automação em Shell e Parsing com `jq`

Em scripts de automação, é comum capturar a resposta, o código de status HTTP e tratar erros.

### 11.1. Capturando Status Code e Corpo Separadamente
```bash
#!/usr/bin/env bash

RESPONSE=$(curl -s -w "
%{http_code}" https://jsonplaceholder.typicode.com/posts/1)
HTTP_BODY=$(echo "$RESPONSE" | sed '$d')
HTTP_STATUS=$(echo "$RESPONSE" | tail -n1)

if [ "$HTTP_STATUS" -eq 200 ]; then
  echo "Sucesso! Resposta:"
  echo "$HTTP_BODY" | jq '.title'
else
  echo "Erro na requisição. Código Status: $HTTP_STATUS"
fi
```

### 11.2. Processando JSON na Linha de Comando com `jq`
```bash
# Filtrar campos específicos
curl -s https://jsonplaceholder.typicode.com/users/1 | jq '{nome: .name, email: .email}'

# Iterar sobre arrays em respostas JSON
curl -s https://jsonplaceholder.typicode.com/posts | jq '.[0:3] | .[] | .title'
```

---

## 12. Cheat Sheet / Tabela de Referência Rápida

| Tarefa | Comando cURL |
| :--- | :--- |
| **GET Simples** | `curl https://example.com` |
| **Salvar Arquivo** | `curl -o saida.html https://example.com` |
| **POST JSON** | `curl -X POST -H "Content-Type: application/json" -d '{"a":1}' https://example.com` |
| **POST Form** | `curl -d "campo1=valor1&campo2=valor2" https://example.com` |
| **Upload Arquivo** | `curl -F "file=@/path/to/file" https://example.com/upload` |
| **Passar Token Bearer**| `curl -H "Authorization: Bearer <TOKEN>" https://example.com` |
| **Seguir Redirecionamento** | `curl -L https://example.com` |
| **Inspecionar Headers** | `curl -I https://example.com` |
| **Modo Verboso (Debug)**| `curl -v https://example.com` |
| **Ignorar SSL Erro** | `curl -k https://example.com` |
| **Usar Proxy** | `curl -x http://127.0.0.1:8080 https://example.com` |
| **Obter apenas Status Code** | `curl -s -o /dev/null -w "%{http_code}
" https://example.com` |

---
*Documentação gerada para fins de estudo e referência técnica de cURL.*
