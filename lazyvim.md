# 🚀 Guia Definitivo de Aprendizado: LazyVim

Este repositório foi criado para documentar e praticar o uso do **LazyVim**, uma distribuição moderna, ultra-rápida e modular para o Neovim. Aqui você encontrará a filosofia de funcionamento do editor e tabelas de referência rápida para os comandos do dia a dia.

---

## 💡 A Filosofia e a Lógica dos Atalhos

Antes de tentar decorar os comandos, entenda a regra de ouro do LazyVim:
* `<Space>` (Barra de Espaço) é a sua tecla **Leader** (Líder). Quase todos os comandos customizados começam com ela.
* A primeira letra após o espaço geralmente indica a **categoria** do comando (ex: `f` para *Find*/Buscar, `b` para *Buffer*, `c` para *Code*/Código).
* **Menu de Ajuda (*Which-Key*):** Se você esquecer um comando, pressione `<Space>` e **espere 1 segundo**. Um menu dinâmico aparecerá na parte inferior da tela mostrando todas as opções disponíveis!

---

## 🔄 Entendendo a Edição Modal (Modos do Vim)

O LazyVim utiliza o conceito de **Edição Modal**, o que torna a edição de código muito mais segura e precisa. Em vez de digitar diretamente, você alterna entre "estados" de operação:

1. **Modo Normal:** O modo padrão onde você inicia. As teclas não digitam texto, elas são comandos e movimentos (*Motions*). Evita alterações acidentais enquanto você lê o código.
2. **Modo de Inserção:** Ativado ao pressionar `i` (ou variações). O cursor muda de formato e permite a digitação de texto. Para voltar à segurança do Modo Normal, aperte `Esc`.
3. **Modo Visual:** Ativado ao pressionar `v`. Permite selecionar blocos de texto usando os movimentos do teclado para copiar, deletar ou modificar de uma só vez.

---

## 🗣️ A Gramática dos Vim Motions & Text Objects

Os comandos no Vim funcionam como uma linguagem estruturada: `[Operador] + [Modificador/Número] + [Movimento/Objeto]`.

* **Modificadores de Texto (*Text Objects*):** Permitem manipular o que está dentro ou ao redor de delimitadores estruturais (como parênteses, aspas ou chaves):
  * `i` (*inside* / dentro)
  * `a` (*around* / ao redor, incluindo o delimitador)

* **Exemplos Práticos:**
  * `diw` $\rightarrow$ **D**elete **I**nside **W**ord (Deleta a palavra sob o cursor).
  * `ci"` $\rightarrow$ **C**hange **I**nside `"` (Apaga o texto entre aspas e entra no Modo de Inserção).
  * `da(` $\rightarrow$ **D**elete **A**round `(` (Deleta os parênteses e tudo o que estiver dentro deles).

---

## 📋 Tabelas de Comandos (Divididas por Tópicos)

### 1. Gerenciamento do Ecossistema (Lazy, Mason e Sistema)

| Atalho | Comando Neovim | Descrição / Ação |
| :--- | :--- | :--- |
| `<Space> l` | `:Lazy` | Abre o painel do gerenciador de plugins (atualizar, instalar e limpar) |
| `<Space> m` | `:Mason` | Abre o gerenciador de pacotes para instalar LSPs, Linters e Formatadores |
| `<Space> g g` | *Interno* | Abre a interface interativa do **LazyGit** dentro do terminal |
| `<Space> u t` / `f t` | `:Terminal` | Abre uma instância de terminal flutuante |
| `<Space> q q` | `:qa` | Fecha todas as janelas e sai com segurança do Neovim (Quit) |

### 2. Modificadores de Entrada (Modo de Inserção Inteligente)

| Atalho | Tipo de Entrada | Descrição / Ação |
| :--- | :--- | :--- |
| `i` | Insert | Entra no modo de inserção exatamente onde o cursor está posicionado |
| `a` | Append | Entra no modo de inserção uma letra **após** a posição atual do cursor |
| `o` | Open below | Cria uma nova linha **abaixo** da linha atual e entra em modo de inserção |
| `O` | Open above | Cria uma nova linha **acima** da linha atual e entra em modo de inserção |
| `A` | Append line | Voa direto para o **final da linha atual** e entra no modo de inserção |

### 3. Navegação e Busca (Telescope / Neo-tree)

| Atalho | Descrição / Ação |
| :--- | :--- |
| `<Space> e` | Abre ou fecha a árvore lateral de arquivos (**Neo-tree**) |
| `<Space> f f` | Busca arquivos pelo nome no projeto (**F**ind **F**ile) |
| `<Space> s g` | Busca por palavras/texto dentro de todos os arquivos (**S**earch **G**rep) |
| `<Space> f r` | Abre a lista de arquivos abertos recentemente (**F**ind **R**ecent) |
| `<Space> <Space>` | Atalho global rápido para buscar arquivos por nome |
| `<Space> s b` | Procura um termo de texto especificamente dentro do arquivo atual |

### 4. Inteligência de Código e LSP (Language Server Protocol)

| Atalho | Descrição / Ação |
| :--- | :--- |
| `K` | Mostra a documentação da função ou variável sob o cursor (Hover) |
| `g d` | Vai para a definição original onde o item foi criado (**G**o to **D**efinition) |
| `g r` | Lista todas as referências e usos daquela função/variável no projeto |
| `g I` | Vai para a implementação prática de uma interface/método |
| `<Space> c r` | Renomeia uma variável/função com segurança em todos os arquivos (**R**ename) |
| `<Space> c a` | Abre o menu de sugestões e correções rápidas de código (**C**ode **A**ction) |
| `] d` / `[ d` | Pula para o **próximo** / **anterior** erro ou aviso de sintaxe do código |
| `<Space> c d` | Abre uma janela flutuante detalhando o erro exato da linha atual |
| `<Space> u f` | Ativa ou desativa a formatação automática de código ao salvar (**u**i **f**ormat) |

### 5. Manipulação de Buffers (Abas) e Janelas (Splits)

| Atalho | Descrição / Ação |
| :--- | :--- |
| `H` (`Shift + h`) | Alterna para a aba/arquivo imediatamente à **esquerda** |
| `L` (`Shift + l`) | Alterna para a aba/arquivo imediatamente à **direita** |
| `<Space> b d` | Fecha o arquivo/buffer atual sem quebrar o layout das janelas |
| `<Space> b b` | Abre a lista interativa com todos os buffers abertos em segundo plano |
| `<Space> \|` | Divide a tela **verticalmente** (adiciona uma janela ao lado) |
| `<Space> -` | Divide a tela **horizontalmente** (adiciona uma janela abaixo) |
| `<Ctrl> h / j / k / l` | Move o foco do cursor para as janelas da **esquerda / baixo / cima / direita** |

### 6. Edição Avançada e Git Integrado (Gitsigns)

| Atalho | Descrição / Ação |
| :--- | :--- |
| `gcc` | Comenta ou descomenta a linha atual de código |
| `gc` *(Modo Visual)* | Comenta ou descomenta todo o bloco de código selecionado |
| `<Alt> j` / `<Alt> k` | Move a linha ou bloco selecionado para **baixo** ou para **cima** |
| `] h` / `[ h` | Pula para a **próxima** / **anterior** alteração de linha feita no Git (*hunk*) |
| `<Space> g h p` | Mostra um popup flutuante revelando o que mudou no Git naquela linha |
| `<Space> g h r` | Reseta as alterações daquela linha específica para o estado original do Git |

---

## 🛠️ Dicas de Treino Diário
* Pratique o uso do `Esc` assim que terminar de escrever uma frase ou código. Não navegue pelas setas enquanto estiver no modo de inserção!
* Sempre que precisar procurar por atalhos enquanto estiver dentro do programa, use o comando `<Space> s k` (**S**earch **K**eymaps) para abrir a lista completa de atalhos em tempo real.
