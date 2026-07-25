# Guia Completo e Definitivo do `wget`: Do Básico ao Avançado

O **GNU Wget** (ou simplesmente `wget`) é uma das ferramentas de linha de comando mais populares, robustas e versáteis para download de arquivos da web através dos protocolos **HTTP, HTTPS e FTP/FTPS**. 

Desenvolvido para operar de forma **não interativa** (sem intervenção do usuário), o `wget` pode continuar rodando em segundo plano, retomar downloads interrompidos, realizar downloads recursivos de sites inteiros e lidar com redes instáveis com extrema resiliência.

---

## Sumário
1. [Introdução e Instalação](#1-introdução-e-instalação)
2. [Sintaxe e Conceitos Básicos](#2-sintaxe-e-conceitos-básicos)
3. [Gerenciamento e Controle de Downloads](#3-gerenciamento-e-controle-de-downloads)
4. [Autenticação e Cabeçalhos HTTP](#4-autenticação-e-cabeçalhos-http)
5. [Manipulação de Cookies e Sessões](#5-manipulação-de-cookies-e-sessões)
6. [Downloads Recursivos e Espelhamento de Sites](#6-downloads-recursivos-e-espelhamento-de-sites)
7. [Filtros de Inclusão, Exclusão e Domínios](#7-filtros-de-inclusão-exclusão-e-domínios)
8. [Configuração Permanente (.wgetrc) e Scripts](#8-configuração-permanente-wgetrc-e-scripts)
9. [Comparativo: `wget` vs `curl`](#9-comparativo-wget-vs-curl)
10. [Tabela de Referência Rápida (Cheat Sheet)](#10-tabela-de-referência-rápida-cheat-sheet)

---

## 1. Introdução e Instalação

### Características Principais
- **Não Interativo:** Funciona perfeitamente em segundo plano, cron jobs ou scripts automatizados.
- **Resiliência:** Tenta reconectar automaticamente em caso de falha de rede.
- **Suporte a Resumo:** Capaz de continuar downloads interrompidos exatamente de onde pararam.
- **Espelhamento Recursivo:** Converte links HTML/CSS automaticamente para navegação offline.
- **Multi-protocolo:** Suporta HTTP, HTTPS, FTP e FTPS.

### Instalação

A maioria das distribuições Linux já vem com o `wget` pré-instalado. Se não estiver disponível:

- **Debian / Ubuntu / Linux Mint:**
  ```bash
  sudo apt update && sudo apt install wget -y
  ```
- **RHEL / CentOS / Fedora / Rocky Linux:**
  ```bash
  sudo dnf install wget -y
  ```
- **macOS (via Homebrew):**
  ```bash
  brew install wget
  ```
- **Windows:** Pode ser instalado via **Chocolatey** (`choco install wget`), **winget** (`winget install GNU.Wget`) ou executado dentro do **WSL** (Windows Subsystem for Linux).

---

## 2. Sintaxe e Conceitos Básicos

A estrutura geral de um comando `wget` é:

```bash
wget [OPÇÕES] [URL]
```

### Download Simples
Para baixar um arquivo padrão mantendo o nome original do servidor:

```bash
wget https://example.com/arquivo.zip
```

### Definindo o Nome de Saída (`-O` / `--output-document`)
Para salvar o arquivo baixado com um nome customizado:

```bash
wget -O meu_novo_nome.zip https://example.com/arquivo.zip
```
> **Atenção:** `-O -` direciona o conteúdo diretamente para a saída padrão (`stdout`), útil para *pipes*.

### Definindo o Diretório de Destino (`-P` / `--directory-prefix`)
Para salvar o arquivo em um diretório específico sem alterar o diretório de trabalho atual:

```bash
wget -P /var/www/downloads/ https://example.com/arquivo.zip
```

### Baixar Múltiplos Arquivos de uma Lista (`-i` / `--input-file`)
Se você tiver um arquivo de texto (`urls.txt`) contendo uma URL por linha:

```bash
wget -i urls.txt
```

---

## 3. Gerenciamento e Controle de Downloads

### Continuar Download Interrompido (`-c` / `--continue`)
Caso a conexão caia no meio de um download grande:

```bash
wget -c https://example.com/iso-grande.iso
```

### Executar em Segundo Plano (`-b` / `--background`)
Redireciona a execução para o *background* e grava o progresso em um arquivo de log (`wget-log` por padrão):

```bash
wget -b https://example.com/arquivo_grande.tar.gz
```
Para personalizar o arquivo de log:
```bash
wget -b -o meu_download.log https://example.com/arquivo_grande.tar.gz
```

### Limitar a Velocidade de Download (`--limit-rate`)
Essencial para evitar que o `wget` consuma toda a largura de banda da rede:

```bash
# Limita a 500 Kilobytes por segundo
wget --limit-rate=500k https://example.com/video.mp4

# Limita a 2 Megabytes por segundo
wget --limit-rate=2m https://example.com/video.mp4
```

### Definir Tentativas e Timeouts
Ajuste o comportamento do `wget` frente a redes instáveis:

```bash
# Define o número máximo de tentativas (padrão é 20)
wget -t 5 https://example.com/arquivo.zip

# Tentativas infinitas
wget -t 0 https://example.com/arquivo.zip

# Define timeout de conexão e leitura em segundos
wget -T 30 https://example.com/arquivo.zip
```

---

## 4. Autenticação e Cabeçalhos HTTP

### Autenticação HTTP Básica
Para acessar conteúdos protegidos por login e senha:

```bash
wget --http-user="usuario" --http-password="senha" https://example.com/area-restrita/
```

### Autenticação FTP
```bash
wget --ftp-user="usuario" --ftp-password="senha" ftp://ftp.example.com/dados.csv
```

### Personalizando o User-Agent (`-U` / `--user-agent`)
Muitos servidores bloqueiam requisições com o User-Agent padrão do `wget`. Você pode simular um navegador real:

```bash
wget --user-agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36" https://example.com/dados.html
```

### Enviando Cabeçalhos HTTP Customizados (`--header`)
Adicione headers extras como Tokens de Autenticação (Bearer), referers, etc.:

```bash
wget --header="Authorization: Bearer SEU_TOKEN_API"      --header="Accept: application/json"      https://api.example.com/v1/dados
```

### Ignorar Erros de Certificado SSL (`--no-check-certificate`)
Útil ao testar servidores locais com certificados autoassinados:

```bash
wget --no-check-certificate https://192.168.1.100/relatorio.pdf
```

---

## 5. Manipulação de Cookies e Sessões

Para navegar ou realizar downloads em sites que exigem login via formulário Web:

### 1. Salvar Cookies após Autenticação
```bash
wget --save-cookies cookies.txt      --keep-session-cookies      --post-data 'user=meu_usuario&pass=minha_senha'      https://example.com/login.php
```

### 2. Usar os Cookies Salvos nos Downloads Subsequentes
```bash
wget --load-cookies cookies.txt https://example.com/painel/relatorio_secreto.pdf
```

---

## 6. Downloads Recursivos e Espelhamento de Sites

O `wget` é amplamente utilizado para baixar documentações e espelhar websites completos para leitura offline.

### Opções Recursivas Fundamentais
- `-r` ou `--recursive`: Ativa o modo de busca/download recursivo.
- `-l N` ou `--level=N`: Define a profundidade máxima de links a seguir (padrão é 5; `0` ou `inf` significa infinito).
- `-k` ou `--convert-links`: Converte os links no HTML baixado para apontarem para os arquivos locais baixados.
- `-p` ou `--page-requisites`: Baixa todos os elementos necessários para exibir a página corretamente (CSS, imagens, fontes, JS).
- `-E` ou `--adjust-extension`: Adiciona extensões `.html` apropriadas em arquivos recebidos como texto sem extensão.

### Comando Clássico de Espelhamento (`-m` / `--mirror`)
A flag `-m` é um atalho equivalente a `-r -l inf -N -c` (recursivo infinito, preserva data de alteração e continua se necessário).

#### Exemplo: Baixar um site completo para visualização offline perfeita
```bash
wget --mirror --convert-links --page-requisites --no-parent https://exemplo.com/doc/
```

> **Explicação das Flags:**
> - `--mirror`: Ativa o espelhamento completo.
> - `--convert-links`: Reescreve URLs para funcionamento offline local.
> - `--page-requisites`: Garante o download de CSS/JS/Imagens.
> - `--no-parent` (`-np`): Não sobe para o diretório pai (restringe a busca a `/doc/`).

### Ignorando o `robots.txt` (`-e robots=off`)
Muitos sites bloqueiam o `wget` via `robots.txt`. Para ignorar essa restrição (use com responsabilidade):

```bash
wget -e robots=off --user-agent="Mozilla/5.0" -r -p https://example.com/
```

---

## 7. Filtros de Inclusão, Exclusão e Domínios

Em downloads recursivos, é fundamental filtrar o que deve ou não ser baixado para evitar tráfego desnecessário.

### Filtrar por Extensão de Arquivo
- `-A` (`--accept`): Lista de extensões permitidas.
- `-R` (`--reject`): Lista de extensões proibidas.

```bash
# Baixar apenas arquivos PDF e ZIP de um site
wget -r -A pdf,zip https://example.com/documentos/

# Baixar o site mas ignorar vídeos e áudios
wget -r -R mp4,mp3,avi https://example.com/
```

### Filtrar por Diretórios
- `-I` (`--include-directories`): Lista de diretórios permitidos.
- `-X` (`--exclude-directories`): Lista de diretórios ignorados.

```bash
# Baixar apenas os diretórios /images e /css
wget -r -I /images,/css https://example.com/

# Ignorar o diretório /forum
wget -r -X /forum https://example.com/
```

### Navegação Entre Domínios Diferentes
Por padrão, o `wget` não segue links que apontem para outros domínios.
- `-H` (`--span-hosts`): Permite ir para outros domínios.
- `-D` (`--domains`): Lista de domínios permitidos ao usar `-H`.

```bash
# Permitir baixar arquivos hospedados no CDN do próprio site
wget -r -H -D example.com,cdn.example.com https://example.com/
```

---

## 8. Configuração Permanente (.wgetrc) e Scripts

Em vez de repetir as mesmas flags na linha de comando, você pode configurá-las no arquivo de configuração do `wget`.

- **Global:** `/etc/wgetrc`
- **Usuário:** `~/.wgetrc`

### Exemplo de arquivo `~/.wgetrc`
```ini
# Ignorar verificação de certificado por padrão
# check_certificate = off

# Definir um User-Agent padrão
user_agent = Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36

# Definir limite de velocidade padrão
# limit_rate = 1m

# Definir número de tentativas padrão
tries = 3

# Ativar suporte a continuação de download
continue = on

# Respeitar estrutura de pastas
passive_ftp = on
```

### Exemplo prático em Script Bash
O `wget` retorna códigos de erro no `$?` que facilitam a verificação em scripts automáticos:

```bash
#!/bin/bash

URL="https://example.com/backup_diario.tar.gz"
DESTINO="/backups/"

wget -q -c -P "$DESTINO" "$URL"

if [ $? -eq 0 ]; then
    echo "[SUCESSO] Download concluído com êxito."
else
    echo "[ERRO] Falha ao realizar download via Wget." >&2
fi
```

---

## 9. Comparativo: `wget` vs `curl`

| Funcionalidade / Característica | GNU Wget | cURL |
| :--- | :--- | :--- |
| **Foco Principal** | Download de arquivos e espelhamento recursivo. | Transferência de dados em múltiplos protocolos. |
| **Download Recursivo** | **Nativo** (`-r`, `--mirror`). | **Não possui** (requer scripts externos). |
| **Resumo de Downloads** | Suporte nativo e simples (`-c`). | Suporta (`-C -`), mas menos automatizado. |
| **Execução em Background** | Suporte nativo (`-b`). | Requer suporte do shell (`&` ou `nohup`). |
| **Protocolos** | HTTP, HTTPS, FTP, FTPS. | HTTP, HTTPS, FTP, FTPS, SFTP, SCP, LDAP, MQTT, etc. |
| **Métodos HTTP (PUT/DELETE/etc)** | Limitado. | Suporte total e nativo (`-X`). |
| **Uso Ideal** | Automação de backups, downloads de sites, rotinas de download. | APIs REST, testes de infraestrutura, pipelines complexos. |

---

## 10. Tabela de Referência Rápida (Cheat Sheet)

| Flag Curta | Flag Longa | Descrição |
| :--- | :--- | :--- |
| `-O file` | `--output-document=file` | Salva o conteúdo com o nome especificado. |
| `-P dir` | `--directory-prefix=dir` | Salva os arquivos no diretório informado. |
| `-c` | `--continue` | Continua um download parcial/interrompido. |
| `-b` | `--background` | Executa o download em segundo plano. |
| `-i file` | `--input-file=file` | Lê URLs de um arquivo de texto. |
| `-r` | `--recursive` | Ativa download recursivo. |
| `-l N` | `--level=N` | Define profundidade máxima da recursão. |
| `-m` | `--mirror` | Ativa o modo de espelhamento do site. |
| `-k` | `--convert-links` | Converte links para navegação local/offline. |
| `-p` | `--page-requisites` | Baixa CSS, imagens e scripts da página. |
| `-np` | `--no-parent` | Não ascende a diretórios superiores durante recursão. |
| `-q` | `--quiet` | Desativa totalmente a saída no terminal. |
| `-nv` | `--no-verbose` | Mostra apenas informações essenciais. |
| `-A list` | `--accept=list` | Aceita apenas as extensões informadas (ex: `-A pdf,jpg`). |
| `-R list` | `--reject=list` | Rejeita as extensões informadas. |
| `-t N` | `--tries=N` | Define número máximo de tentativas. |
| `-T N` | `--timeout=N` | Definir tempo de timeout em segundos. |
| `-U agent`| `--user-agent=agent` | Define a string de User-Agent do cabeçalho HTTP. |
