# Guia de Uso Completo: tmux 🚀

O `tmux` é um multiplexador de terminal que permite alternar facilmente entre vários programas em um único terminal, desanexar e reanexar sessões, e dividir a tela em vários painéis.

## 📌 Conceitos Fundamentais

Antes de começar, é essencial entender a hierarquia do tmux:
1. **Sessão (Session):** O ambiente global. Pode conter várias janelas. Se mantém viva mesmo se você fechar o terminal.
2. **Janela (Window):** Equivale a uma "aba" no seu navegador. Ocupa a tela inteira.
3. **Painel (Pane):** Divisões dentro de uma mesma janela (telas divididas).

> ⚠️ **A Tecla de Prefixo:** Quase todos os comandos do tmux exigem que você pressione uma combinação de teclas de ativação antes. Por padrão, essa combinação é **`Ctrl + b`** (abreviado nesta doc como `Prefix`).

---

## 🛠️ Comandos de Inicialização (No Terminal Principal)

Esses comandos são executados diretamente no seu terminal padrão, antes de entrar ou para gerenciar o tmux por fora.

| Comando | Descrição |
| :--- | :--- |
| `tmux` | Inicia uma nova sessão com ID numérico |
| `tmux new -s <nome_da_sessao>` | Cria uma nova sessão com um nome personalizado |
| `tmux ls` | Lista todas as sessões ativas |
| `tmux a` ou `tmux attach` | Conecta à última sessão ativa |
| `tmux a -t <nome_da_sessao>` | Conecta a uma sessão específica pelo nome |
| `tmux kill-session -t <nome_da_sessao>` | Fecha/destrói uma sessão específica |
| `tmux kill-server` | Fecha todas as sessões e encerra o tmux |

---

## 🎹 Atalhos Dentro do tmux (Usando o Prefixo)

Uma vez dentro de uma sessão do tmux, use o prefixo **`Ctrl + b`**, solte as teclas, e depois pressione o atalho correspondente.

### 1. Gerenciamento de Sessões
| Atalho | Ação |
| :--- | :--- |
| `Prefix` + `d` | **Detach:** Desanexa da sessão atual (deixa ela rodando em background) |
| `Prefix` + `s` | Lista as sessões interativamente para alternar entre elas |
| `Prefix` + `$` | Renomeia a sessão atual |

### 2. Gerenciamento de Janelas (Abas)
| Atalho | Ação |
| :--- | :--- |
| `Prefix` + `c` | Cria uma nova janela |
| `Prefix` + `,` | Renomeia a janela atual |
| `Prefix` + `p` | Vai para a janela anterior (*Previous*) |
| `Prefix` + `n` | Vai para a próxima janela (*Next*) |
| `Prefix` + `<Número>` | Vai para a janela correspondente ao número (ex: `Prefix` + `1`) |
| `Prefix` + `&` | Fecha a janela atual (pede confirmação) |

### 3. Gerenciamento de Painéis (Divisões de Tela)
| Atalho | Ação |
| :--- | :--- |
| `Prefix` + `%` | Divide a tela **verticalmente** (lado a lado) |
| `Prefix` + `"` | Divide a tela **horizontalmente** (cima e baixo) |
| `Prefix` + `Setas direcionais` | Move o cursor entre os painéis |
| `Prefix` + `z` | Dá **Zoom** no painel atual (ocupa a tela toda). Use de novo para voltar |
| `Prefix` + `x` | Fecha o painel atual (pede confirmação) |
| `Prefix` + `Espaço` | Alterna entre layouts automáticos de painéis |
| `Prefix` + `q` | Mostra temporariamente os números dos painéis (digite o número para ir até ele) |

---

## 💡 Dicas Extras de Sobrevivência

* **Como rolar a tela (Scroll):** Por padrão, a roda do mouse não funciona. Pressione `Prefix` + `[` para entrar no modo de cópia. Agora você pode usar as setas ou as teclas `PageUp`/`PageDown` para navegar pelo histórico. Para sair desse modo, pressione `q`.
* **Personalização:** Você pode criar um arquivo chamado `~/.tmux.conf` na sua pasta de usuário para mudar o prefixo padrão, ativar o suporte ao mouse e mudar as cores do terminal.
