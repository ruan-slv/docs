# 📜 Documentação Básica para Utilização do Assembly x86_64

Este guia serve como referência e manual de sobrevivência para o desenvolvimento em baixo nível na arquitetura x86_64.

---

## 🏗️ 1. Estrutura Básica de um Programa

Todo programa em Assembly x86_64 é estruturado em seções fundamentais que delimitam onde ficam as variáveis e onde fica o código executável:

* **.data**: Destinada a variáveis inicializadas. Espaço para constantes e textos (strings) fixos que não mudam de tamanho.
* **.rodata**: Área destinada a constantes imutáveis e strings fixas que não devem ser modificadas em tempo de execução. É usada para garantir que certos dados permaneçam somente leitura, evitando alterações acidentais.
* **.bss**: Destinada a variáveis não inicializadas. Reserva espaço na memória RAM para dados dinâmicos que serão inseridos durante a execução.
* **.text**: Onde o código propriamente dito reside. É obrigatório conter o ponto de entrada global (geralmente a etiqueta `_start`).

---

## 💾 2. Memória e Registradores

Os registradores são pequenos espaços de armazenamento ultravelozes localizados dentro do próprio processador. Na arquitetura de 64 bits, podemos "fatiar" esses registradores para manipular pedaços menores de dados (32, 16 ou 8 bits), economizando memória.

### 📊 Tabela Unificada de Registradores

| 64 bits | 32 bits | 16 bits | 8 bits | Nome Completo | Convenção de Uso Geral |
| :-- | :-- | :-- | :-- | :-- | :-- |
| **RAX** | EAX | AX | AL / AH | Accumulator | Operações matemáticas, ID de syscalls e retorno de funções. |
| **RBX** | EBX | BX | BL / BH | Base | Aponta para dados na memória (ponteiro base). Preservado. |
| **RCX** | ECX | CX | CL / CH | Counter | Contador para loops e 4º argumento de funções (Windows). |
| **RDX** | EDX | DX | DL / DH | Data | Divisões/multiplicações e E/S. 3º argumento de funções. |
| **RSI** | ESI | SI | SIL | Source Index | Ponteiro de origem para strings/arrays. 2º argumento (Linux). |
| **RDI** | EDI | DI | DIL | Destination Index | Ponteiro de destino para strings/arrays. 1º argumento (Linux). |
| **RBP** | EBP | BP | BPL | Base Pointer | Aponta para o início da base da pilha (*stack frame*) atual. |
| **RSP** | ESP | SP | SPL | Stack Pointer | Aponta para o topo atual da pilha (muda dinamicamente). |
| **R8** | R8D | R8W | R8B | Registrador 8 | Passagem de argumentos (5º no Linux / 3º no Windows). |
| **R9** | R9D | R9W | R9B | Registrador 9 | Passagem de argumentos (6º no Linux / 4º no Windows). |
| **R10** | R10D | R10W | R10B | Registrador 10 | Uso temporário (*Scratch*). Usado em syscalls no Linux (4º arg). |
| **R11** | R11D | R11W | R11B | Registrador 11 | Uso temporário (*Scratch*). Modificado por syscalls (salva RFLAGS). |
| **R12** | R12D | R12W | R12B | Registrador 12 | Registro preservado (deve ser restaurado se usado pela função). |
| **R13** | R13D | R13W | R13B | Registrador 13 | Registro preservado (deve ser restaurado se usado pela função). |
| **R14** | R14D | R14W | R14B | Registrador 14 | Registro preservado (deve ser restaurado se usado pela função). |
| **R15** | R15D | R15W | R15B | Registrador 15 | Registro preservado (deve ser restaurado se usado pela função). |

> 💡 **Nota sobre os sub-registradores:** Mudar a parte baixa (ex: `EAX`) afeta diretamente o registrador pai (`RAX`). Para RAX, RBX, RCX e RDX, o sufixo **L** indica os 8 bits inferiores (*Low*) e **H** os 8 bits superiores (*High*) do primeiro bloco de 16 bits.

---

## 🔍 3. Detalhes dos Registradores Protegidos e Voláteis (R8 a R15)

* **R8 e R9 (Argumentos):** Substituem o uso lento da pilha enviando parâmetros diretamente ao processador. No Linux, representam o 5º e 6º argumento; no Windows, o 3º e 4º.
* **R10 e R11 (Voláteis/Scratch):** Podem ter seus valores apagados ou alterados pelo sistema operacional a qualquer momento. O `R10` assume o papel do `RCX` em syscalls Linux, e o `R11` é destruído pelo próprio hardware para salvaguardar os sinalizadores (*RFLAGS*).
* **R12 a R15 (Não-Voláteis/Callee-Saved):** Guardam variáveis estáveis de longa duração. Se sua função alterá-los, você deve aplicar `push` no início da função e `pop` antes do `ret` para devolver o valor intacto ao sistema.

---

## 🚚 4. Instruções Fundamentais

### A Instrução MOV (Cópia de Dados)
O `MOV` não move fisicamente um dado (não apaga a origem); ele funciona estritamente como um **Copiar e Colar**.
```assembly
mov destino, origem
```

#### 1 - Imediato para Registrador: `mov rax, 60` (coloca o número 60 direto em `RAX`).

#### 2 - Registrador para Registrador: `mov rbx, rax` (duplica o valor de `RAX` dentro de `RBX`).

#### 3 - Memória para Registrador: `mov rax, [rbx]` (o uso de colchetes indica acesso ao endereço mapeado na `RAM`).

### Observação: É fisicamente impossível copiar dados de um ponto da memória RAM diretamente para outro `mov [mem1], [mem2]`. Um registrador deve sempre servir de intermediário.

## 🎛️ 5. Entendendo as Syscalls (Chamadas de Sistema)
Seu programa roda em uma zona segura e limitada chamada User Space (Espaço do Usuário) e não tem permissão para acessar o hardware diretamente. Para interagir com a tela, teclado ou disco, ele precisa invocar o Kernel Space (Espaço do Kernel) através de uma `syscall`.

### Fluxo de Execução de uma Syscall:

#### 1 - O ID do serviço desejado é carregado no registrador `RAX`.
#### 2 - Os argumentos necessários para esse serviço são colocados nos registradores definidos pela arquitetura (`RDI`, `RSI`, `RDX`, ...).
#### 3 - A instrução `syscall` é disparada. O processador pausa seu programa, eleva o nível de privilégio e transfere o controle para o Kernel. O Kernel processa o pedido e deixa a resposta/status de volta no `RAX`.

### Exemplo: Encerrar programa com sucesso

```
section .text
    global _start

_start:
    mov rax, 60 ; ID 60 = exit
    mov rdi, 0  ; Primeiro argumento (0 = código de saída sem erros)
    syscall     ; Transfere o controle do kernel para fechar o processo
```

## 🟰 6. Controle de Fluxo: Condições (If / Else)
Tomadas de decisão ocorrem em dois passos: primeiro realizamos uma comparação através do comando `cmp` (que avalia a diferença matemática entre dois valores) e, em seguida, executamos um salto condicional se o critério for correspondido.

* `cmp op1, op2`: Compara as duas entradas.
* `je` (Jump if Equal): Pula se forem iguais.
* `jne` (Jump if Not Equal): Pula se forem diferentes.
* `jg` (Jump if Greater): Pula se o primeiro operando for maior.
* `jl` (Jump if Less): Pula se o primeiro operando for menor.
* `jmp`: Salto incondicional (pula sempre, sem fazer perguntas).

### Exemplo de Estrutura Condicional:
```
    cmp rax, rbx            ; Compara RAX com RBX
    je valores_iguais       ; Se forem iguais, pula para a etiqueta correspondente

    ; --- Bloco ELSE (Se forem diferentes) ---
    mov rcx, 0              ; Executa lógica caso sejam diferentes
    jmp fim_condicao        ; Salta o bloco do "IF" para evitar execução mútua

valores_iguais:             ; --- Bloco IF ---
    mov rcx, 1              ; Executa lógica caso sejam iguais

fim_condicao:
    ; O fluxo do programa continua unificado aqui...
```

## 🔁 7. Laços de Repetição (Loops)
Um laço de repetição aproveita as etiquetas e saltos condicionais. Definimos um marcador inicial, decrementamos um registrador que funciona como contador de voltas e saltamos de volta ao topo enquanto a verificação for válida.

### Exemplo Prático: Loop de 5 iterações:
```
    mov rcx, 5              ; Inicializa o contador com 5 voltas

topo_do_loop:
    ; ... [O código que precisa ser repetido fica nesta região] ...

    sub rcx, 1              ; Decrementa 1 unidade do contador (RCX = RCX - 1)
    cmp rcx, 0              ; Compara se o contador chegou ao fim (zero)
    jg topo_do_loop         ; Se RCX ainda for MAIOR (Greater) que zero, pula para o topo

fim_do_loop:
    ; O critério falhou (RCX = 0), o programa sai do laço e segue em frente
```

## 🛠️ 8. Compilar e Executar um Arquivo Assembly
Para transformar o seu código fonte .asm em um programa executável binário, o computador passa por duas etapas principais: Montagem (Compilation) e Linkagem (Linking).

#### 1 - Instalação das Ferramentas
##### APT
```bash
sudo apt update
sudo apt install nasm build-essential -y
```

##### DNF
```bash
sudo dnf check-update
sudo dnf groupinstall "Development Tools" -y
sudo dnf install nasm -y
```

#### 2 - O Processo de Build (Passo a Passo)
##### Passo A: Montar o arquivo objeto (.o)
O NASM vai ler o seu código de texto e traduzi-lo para instruções binárias de máquina. Ele gera um arquivo intermediário chamado "objeto".
```
nasm -f elf64 programa.asm -o programa.o
```
* `-f elf64`: Especifica o formato do arquivo de saída como ELF de 64 bits (padrão do Linux).
* `-o programa.o`: Define o nome do arquivo objeto que será gerado.

##### Passo B: Linkar o arquivo objeto
O montador gera o código, mas o sistema operacional ainda não sabe onde o programa começa de verdade ou como interagir com as bibliotecas do sistema. O `ld` (Linker) junta tudo e cria o executável final.
```
ld programa.o -o programa
```
* `-o programa`: Define o nome do arquivo executável final (você pode dar o nome que quiser).

##### Passo C: Executar o programa
Agora seu programa é um executável nativo do sistema. Para rodá-lo:
```
./programa
```

## 📋 9. Consultar a Tabela de Syscalls
* [Tabela Syscalls](https://www.chromium.org/chromium-os/developer-library/reference/linux-constants/syscalls/)
* [Tabela ASCII](https://www.ascii-code.com/)
