# Guia rápido de comandos Git

Esta documentação reúne comandos essenciais do Git para acompanhar alterações, criar commits, trabalhar com branches, sincronizar com repositórios remotos e recuperar mudanças em situações comuns. Use a tabela abaixo como referência rápida durante o fluxo de desenvolvimento.

| Categoria | Comando | Ação |
| :-- | :-- | :-- |
| Fluxo básico | `git status` | Mostra quais arquivos foram modificados, deletados ou que ainda não estão sendo rastreados. |
| Fluxo básico | `git add nome_arquivo.txt` | Adiciona arquivos para o próximo commit (Staging). |
| Fluxo básico | `git commit -m "mensagem"` | Faz o commit (salva o ponto na história). |
| Fluxo básico | `git push origin nome_da_branch` | Envia para o repositório remoto (GitHub). |
| Fluxo básico | `git log --oneline` | Lista os IDs (hashes) dos commits. |
| Fluxo básico | `git fetch` | Busca atualizações do repositório remoto sem realizar mesclagem. |
| Fluxo básico | `git pull origin nome_da_branch` | Traz as novidades do GitHub para sua máquina (Fetch + Merge). |
| Fluxo básico | `git checkout -- nome_do_arquivo.txt` | Descarta as alterações locais de um arquivo específico (antes do commit). |
| Fluxo básico | `git help nome_do_comando` | Abre o manual oficial detalhado. |
| Branches | `git branch` | Lista branches locais. |
| Branches | `git checkout -b nome_da_branch` | Cria uma nova branch e muda para ela imediatamente. |
| Branches | `git checkout nome_da_branch` | Alterna entre branches existentes. |
| Branches | `git merge nome_da_branch` | Une o código de outra branch na sua branch atual. |
| Branches | `git branch -d nome_da_branch` | Deleta uma branch local. |
| Salva vidas | `git stash` | Guarda o trabalho atual e limpa o terminal. |
| Salva vidas | `git stash list` | Lista tudo o que está guardado no stash. |
| Salva vidas | `git stash pop` | Traz as alterações de volta e as deleta da stash. |
| Salva vidas | `git stash apply` | Traz as alterações de volta, mas as mantém guardadas na stash. |
| Salva vidas | `git stash clear` | Limpa todo o stash. |
| Primeiros socorros | `git commit --amend -m "Sua nova mensagem corrigida"` | Corrige a mensagem do último commit antes de realizar push. |
| Primeiros socorros | `git add arquivo_esquecido.java` + `git commit --amend` | Inclui um arquivo esquecido no último commit. |
| Primeiros socorros | `git reset --soft HEAD~1` | Desfaz o último commit, mas mantém as alterações nos arquivos. |
| Primeiros socorros | `git reset --hard HEAD~1` | Desfaz o último commit e apaga todas as alterações. Use com cuidado. |
| Primeiros socorros | `git revert ID_DO_COMMIT` | Reverte um commit específico no histórico criando um novo commit de reversão. |
