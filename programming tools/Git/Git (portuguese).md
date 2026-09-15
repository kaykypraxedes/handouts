```
 _____  _  _   
|  __ \(_)| |  
| |  \/ _ | |_ 
| | __ | || __|
| |_\ \| || |_ 
 \____/|_| \__|
```

# 00. Conceitos Básicos

## Git e GitHub:

O **Git** é um sistema distribuído de controle de versão. Ele registra versões de um projeto, permite comparar alterações, recuperar estados anteriores e desenvolver funcionalidades em linhas independentes. Como cada cópia comum de um repositório contém seu próprio histórico, a maior parte das operações do Git é local e não depende de conexão com a internet.

O **GitHub** é um serviço que hospeda repositórios Git e acrescenta recursos de colaboração, como *issues*, *pull requests* e controle de acesso. Git e GitHub não são a mesma ferramenta: é possível utilizar Git sem GitHub e hospedar repositórios Git em outros serviços, como GitLab e Bitbucket.

## Repositório:

Um **repositório** é o conjunto formado pelo projeto e pelos dados usados pelo Git para manter seu histórico. Em um repositório comum, esses dados ficam no diretório oculto `.git`, que armazena objetos, referências e configurações locais. Apagar `.git` não apaga os arquivos do projeto, mas remove dele o histórico e a configuração do Git.

O Git registra cada versão como um **snapshot** (uma fotografia lógica do estado preparado do projeto). Um **commit** referencia um desses *snapshots* e contém metadados como autor, data, mensagem e o commit anterior. Seu identificador é calculado a partir do conteúdo e dos metadados armazenados, portanto a alteração de um commit produz outro identificador.

## Áreas do Git:

O fluxo básico do Git envolve três áreas:

- **Diretório de trabalho** (*Working Tree*): versão dos arquivos presente no sistema de arquivos, na qual o usuário realiza alterações.
- **Área de preparação** (*Staging Area* ou *Index*): estado escolhido para formar o próximo commit.
- **Repositório local**: banco de dados dentro de `.git`, no qual os commits e demais objetos são armazenados.

O fluxo mais comum é:

```text
Working Tree -- git add --> Staging Area -- git commit --> Repositório local
```

`git add` copia para o *Index* o estado atual do conteúdo selecionado. Portanto, se um arquivo for modificado novamente depois de `git add`, a primeira alteração continuará preparada e a alteração mais recente permanecerá apenas no diretório de trabalho até um novo `git add`.

## Estados dos arquivos:

Um arquivo pode estar nos seguintes estados principais:

- **Não rastreado** (*untracked*): existe no diretório de trabalho, mas ainda não participa do histórico do Git.
- **Não modificado** (*unmodified*): é rastreado e corresponde à versão registrada.
- **Modificado** (*modified*): é rastreado, mas seu conteúdo no diretório de trabalho difere do estado preparado ou registrado.
- **Preparado** (*staged*): seu estado atual foi copiado para o *Index* e será incluído no próximo commit.
- **Registrado** (*committed*): seu estado está armazenado em um commit do repositório local.

## Referências e `HEAD`:

Uma **branch** é uma referência móvel para um commit. Normalmente, `HEAD` aponta para a branch atualmente selecionada, e essa branch aponta para seu commit mais recente. Conforme novos commits são criados, a referência da branch avança.

`HEAD~1` representa o primeiro pai do commit indicado por `HEAD`; `HEAD~2`, o primeiro pai dele, e assim sucessivamente. Em históricos com *merges*, essa notação segue repetidamente o primeiro pai, não necessariamente o segundo commit mais recente por data.

Quando `HEAD` aponta diretamente para um commit, em vez de uma branch, o repositório está em estado de **`detached HEAD`**. É possível inspecionar e até criar commits nesse estado, mas deve-se criar uma branch para conservar facilmente essa nova linha de desenvolvimento.

---

# 01. Configuração e Identidade

## Escopos de configuração:

As configurações do Git podem ser aplicadas em diferentes escopos. Uma configuração mais específica normalmente sobrescreve uma configuração equivalente de escopo mais amplo.

- **Sistema** (`--system`): aplica-se aos usuários daquela instalação do sistema.
- **Global** (`--global`): aplica-se ao usuário atual e normalmente fica em `~/.gitconfig` ou `~/.config/git/config`.
- **Local** (`--local`): aplica-se somente ao repositório atual e fica em `.git/config`; este é o escopo padrão quando nenhum é informado dentro de um repositório.

> Nos comandos a seguir, o `{localização}` representa a abrangência daquela configuração. `--global` marca aquela configuração para todas as operações do usuário, enquanto `--local` define apenas para o repositório atual. 
- `git config {localização} user.name {nome}` - Define o nome normalmente gravado nos commits do usuário.

- `git config {localização} user.email {email@exemplo.com}` - Define o e-mail normalmente gravado nos commits do usuário.

- `git config {localização} init.defaultBranch main` - Define `main` como nome inicial dos novos repositórios.

- `git config --list` - Lista as configurações aplicáveis ao contexto atual.

- `git config --show-origin --list` - Lista as configurações e os arquivos dos quais elas foram lidas.

- `git config --get user.email` - Mostra o valor efetivo de uma configuração específica.

`user.name` e `user.email` definem a **identidade registrada no commit**; eles não autenticam o usuário no GitHub. A conta usada para enviar o commit a um servidor pode ser diferente do nome e do e-mail gravados nele.

---

# 02. Versionamento e Arquivos

## Inicialização:

- `git init {pasta}` - Cria a pasta, se necessário, e inicia nela um novo repositório Git.
> O comando `git init` sem pasta inicia um repositório no diretório atual, criando `.git` dentro dele.

- `git -C {pasta} {comando}` - Executa um comando como se o Git tivesse sido iniciado naquela pasta.

A maioria dos comandos procura `.git` no diretório atual e em seus diretórios-pai. Por isso, normalmente eles podem ser executados em uma subpasta do projeto, sem que o terminal esteja exatamente na raiz do repositório.

## Inspeção do estado:

- `git status {opções}` - Resume as diferenças entre `HEAD`, *Index* e diretório de trabalho, além de indicar arquivos não rastreados e a branch atual.
> `git status --short` - Mostra o mesmo estado em formato compacto.

- `git diff {opções} {commit_1} {commit_2}` - Compara dois commits.
> Utilizando a opção `--staged`, é mostrado as alterações já preparadas para o próximo commit em relação a `HEAD`.
> Se não for passado nenhum commit, ele simplesmente mostra as alterações do diretório de trabalho que ainda não estão preparadas.

## Preparação e registro:

- `git add {arquivo/pasta}` - Prepara o estado atual de um arquivo ou todos os arquivos, recursivamente, de um diretório.
> É possível selecionar todos os arquivos, a partir do diretório atual, com o `git add .` (respeitando as regras de exclusão).

- `git commit {opções} -m "{mensagem}"` - Cria um commit com o estado presente no *Index*.

- `git log {opções}` - Mostra o histórico.
> Para mostrar de forma mais legível, pode ser utilizado o comando `git log --oneline --graph --decorate --all`.

Um commit inclui apenas o estado preparado. Arquivos não rastreados ou alterações feitas depois do último `git add` não entram automaticamente no commit.
 - Mostra, em formato compacto, o histórico e a relação entre branches e outras referências.
## Exclusão de arquivos:

O arquivo `.gitignore` contém padrões de arquivos **não rastreados** que o Git deve ignorar, como dependências baixadas, arquivos de compilação e credenciais locais. Exemplos:

```gitignore
# Diretório em qualquer nível
node_modules/

# Arquivos com esta extensão
*.o

# Arquivos locais de ambiente
*.env

# Exceção a um padrão anterior
!config.example.env
```

O `.gitignore` não deixa de rastrear um arquivo que já foi incluído em commits. Para mantê-lo no diretório de trabalho, mas removê-lo do *Index*, pode-se usar `git rm --cached {arquivo}` e então registrar essa remoção em um commit. Segredos já publicados continuam existindo no histórico e devem ser revogados, mesmo depois da remoção do arquivo.

## Restauração:

- `git restore {opções} {arquivo}` - Restaura o arquivo no diretório de trabalho a partir do *Index*, descartando alterações ainda não preparadas.
>`git restore --staged {arquivo}` - Restaura o arquivo no *Index* a partir de `HEAD`, retirando suas alterações da área de preparação sem descartá-las do diretório de trabalho.
>`git restore --source={commit} {arquivo}` - Restaura no diretório de trabalho a versão do arquivo existente no commit indicado.

Como o *Index* costuma corresponder a `HEAD` antes de `git add`, `git restore {arquivo}` frequentemente parece restaurar o último commit. A origem padrão, porém, é o *Index*. Essas operações podem descartar conteúdo; antes de executá-las, deve-se conferir `git status` e `git diff`.

---

# 03. Histórico e Recuperação

## Alteração do último commit:

- `git commit --amend -m "nova mensagem"` - Substitui o último commit, usando o conteúdo atualmente preparado e a nova mensagem.

`--amend` não edita o commit existente: ele cria outro commit e move a branch para ele. Se houver alterações no *Index*, elas também serão incorporadas ao novo commit. Para apenas abrir o editor e alterar a mensagem, pode-se usar `git commit --amend` sem `-m`.

## `reset`:

- `git reset {opções} {commit}` - Move `HEAD` e, normalmente, a branch atual para outro commit. 

O modo escolhido determina o que também acontece com o *Index* e o diretório de trabalho:

- `git reset --soft HEAD~1` - Move a branch, mas mantém no *Index* e no diretório de trabalho o conteúdo do commit desfeito.

- `git reset --mixed HEAD~1` - Move a branch e redefine o *Index*, mas mantém as alterações no diretório de trabalho. `--mixed` é o modo padrão.

- `git reset --hard HEAD~1` - Move a branch e torna o *Index* e os arquivos rastreados do diretório de trabalho iguais ao commit indicado, descartando as alterações rastreadas afetadas.

`reset --hard` normalmente não remove arquivos não rastreados que não interfiram na restauração, mas pode apagar arquivos ou diretórios não rastreados que estejam no caminho de arquivos rastreados que precisam ser escritos. Por isso, deve ser usado somente depois de conferir o alvo e o estado do repositório.

## Reversão:

- `git revert {commit}` - Cria um novo commit que aplica o inverso das alterações introduzidas pelo commit indicado.

`revert` preserva o histórico existente e, por isso, costuma ser a alternativa mais segura para desfazer alterações já publicadas. O resultado pode gerar conflitos se o projeto tiver mudado desde o commit revertido.

## Recuperação com `reflog`:

- `git reflog` - Mostra o registro local dos movimentos recentes de `HEAD` e de outras referências.

- `git branch {nova_branch} {commit}` - Cria uma branch apontando para um commit recuperado pelo `reflog`.

O `reflog` pode ajudar a localizar commits que deixaram de ser alcançáveis depois de `reset`, `rebase` ou exclusão de uma branch. Ele é local e suas entradas expiram, portanto não deve ser tratado como cópia de segurança permanente.

> `commit --amend`, `reset` e `rebase` podem reescrever a linha de histórico e alterar identificadores. Evite aplicá-los a commits já compartilhados sem coordenar com os demais colaboradores. `revert` cria um novo commit e não reescreve os anteriores.

---

# 04. Branches e Integração

## Branches:

Ao inicializar um repositório, o Git define o nome de uma branch inicial ainda sem commits, chamada de **unborn branch**. A branch passa a apontar para um commit quando o primeiro é criado; antes disso, diversos comandos que precisam de um commit como referência ainda não podem operar normalmente.

- `git branch {opções}` - Lista as branches locais; `*` indica a branch atual.

Essa é uma das seções mais importantes para o versionamento, sendo usadas muitas configurações de branch, sendo as principais:

- `git branch -a` - Lista branches locais e referências de branches remotas.

- `git branch -m {novo_nome}` - Renomeia a branch atual.

- `git branch -m {nome_antigo} {nome_novo}` - Renomeia uma branch específica.

- `git branch -d {branch}` - Exclui uma branch já integrada à sua *upstream* ou, na ausência dela, ao histórico alcançado por `HEAD`.

- `git branch -D {branch}` - Força a exclusão da referência, mesmo sem integração.

- `git switch {opções} {branch}` - Troca para a branch indicada e atualiza o diretório de trabalho.

- `git switch -c {nova_branch}` - Cria uma branch a partir da posição atual e troca para ela.

Excluir uma branch remove sua referência, não necessariamente seus commits de imediato. Ainda assim, trabalhos não integrados podem se tornar difíceis de localizar; confira o conteúdo antes de usar `-D`.

## Merge:

- `git merge {outra_branch}` - Integra na branch atual o histórico alcançável pela outra branch.

Quando a branch atual pode apenas avançar até o mesmo ponto da outra, ocorre um **fast-forward**. Quando as linhas de desenvolvimento divergiram, o Git normalmente cria um commit de *merge* que possui mais de um pai, desde que consiga combinar as alterações.

## Conflitos:

Um **conflito** ocorre quando o Git não consegue decidir automaticamente como combinar alterações. Ele marca os trechos conflitantes nos arquivos e interrompe a integração para que o usuário resolva o conteúdo.

O fluxo básico para concluir um *merge* com conflitos é:

1. Executar `git status` para identificar os arquivos conflitantes.
2. Editar os arquivos e remover os marcadores de conflito, mantendo o conteúdo correto.
3. Executar `git add {arquivo}` para marcar cada conflito como resolvido.
4. Executar `git commit` para concluir o *merge*, quando o Git não o concluir automaticamente.

- `git merge --abort` - Tenta retornar ao estado anterior ao início do *merge*.

---

# 05. Repositórios Remotos

## Conceitos:

Um **remoto** é um repositório acessível por uma URL e registrado localmente com um nome curto. `origin` é apenas o nome convencional atribuído ao remoto criado por `git clone`; não é uma palavra obrigatória nem representa necessariamente o repositório original do projeto.

Uma referência como `origin/main` é uma **remote-tracking branch**: um registro local da posição observada da branch `main` no remoto `origin` durante a última comunicação. Ela não é a mesma referência que a branch local `main`.

Uma branch local pode ter uma **upstream branch** configurada, por exemplo, `main` seguindo `origin/main`. Essa relação permite que comandos como `git pull` e `git push` determinem seus alvos padrão e que `git status` compare as duas branches.

## Configuração de remotos:

- `git remote {opções}` - Lista os nomes dos remotos configurados.

Algumas das opções mais importantes desse comando são:

- `git remote -v` - Lista os remotos e suas URLs de busca e envio.

- `git remote add {nome} {URL}` - Registra um repositório remoto com o nome escolhido.

- `git remote set-url {nome} {nova_URL}` - Altera a URL de um remoto.

- `git remote rename {nome_antigo} {nome_novo}` - Renomeia um remoto.

- `git remote remove {nome}` - Remove sua configuração local e as referências de rastreamento associadas; não apaga o repositório hospedado.

- `git branch -vv` - Mostra as branches locais, seus últimos commits e suas *upstreams*, quando configuradas.

- `git branch --set-upstream-to={remoto}/{branch_remota} {branch_local}` - Define explicitamente a *upstream* de uma branch local.

## Transferência e integração:

- `git clone {URL} {pasta}` - Cria uma cópia local do repositório, incluindo seu histórico, configura um remoto normalmente chamado `origin` e obtém uma versão de trabalho.

- `git fetch {remoto}` - Baixa os objetos e atualiza as referências de rastreamento do remoto sem integrar automaticamente as alterações à branch local.

- `git fetch {remoto} {branch}` - Busca a branch ou referência indicada no remoto.

- `git pull` - Executa primeiro um `fetch` e depois integra a *upstream* à branch atual, conforme as opções e configurações de *merge*, *rebase* ou avanço direto.

- `git pull {remoto} {branch}` - Busca a branch informada e a integra à branch atual; o segundo argumento não é necessariamente o nome da branch local.

- `git push {remoto} {branch}` - Tenta atualizar no remoto a branch indicada com os commits locais correspondentes.

- `git push -u {remoto} {branch}` - Realiza o envio e, se bem-sucedido, configura a branch remota como *upstream* da branch local.

`fetch` permite inspecionar as alterações antes da integração, por exemplo, com `git log HEAD..origin/main` e `git diff HEAD..origin/main`. Ele não solicita confirmação para mesclar porque não realiza a mesclagem. Já `pull` pode alterar imediatamente a branch e o diretório de trabalho.

Um `push` pode ser recusado quando o remoto contém commits que a atualização local descartaria. Nesse caso, normalmente é necessário obter e integrar o trabalho remoto antes de tentar novamente. O envio forçado reescreve o histórico remoto e não deve ser usado como solução automática.

---

# 06. Autenticação e Credenciais

## Identidade e autenticação:

Existem dois mecanismos independentes:

- **Identidade do commit**: `user.name` e `user.email` são metadados gravados no histórico.
- **Autenticação remota**: prova ao GitHub ou a outro servidor qual conta está realizando uma operação e quais recursos ela pode acessar.

Operações puramente locais, como `add`, `commit`, `branch` e `log`, não exigem login no GitHub. A leitura de repositórios públicos também pode não exigir autenticação, mas o envio de alterações e o acesso a repositórios privados normalmente exigem uma credencial autorizada. O método utilizado depende do protocolo da URL do remoto, principalmente **HTTPS** ou **SSH**.

## HTTPS e token:

Nas operações Git por HTTPS, o GitHub não aceita a senha comum da conta como credencial. Quando a autenticação é inserida manualmente, utiliza-se o nome de usuário e um **Personal Access Token (PAT)** no campo da senha. O token é a parte que efetivamente autentica a operação.

Um PAT autoriza ações em nome do usuário, limitado tanto pelo acesso que a conta já possui quanto pelas permissões concedidas ao token. O GitHub recomenda tokens **fine-grained** quando eles atendem ao caso de uso, pois podem ser limitados a um proprietário, repositórios específicos, permissões específicas e um prazo de validade. O token deve ser tratado como uma senha: não deve ser colocado no repositório, na URL do remoto, em comandos que fiquem no histórico ou em mensagens.

Ao utilizar um PAT manualmente:

1. Criar o token nas configurações de desenvolvedor da conta do GitHub.
2. Selecionar somente os repositórios e permissões necessários e definir uma expiração apropriada.
3. Executar a operação Git utilizando uma URL HTTPS.
4. Informar o usuário quando solicitado e inserir o PAT no campo de senha.

Não é necessário gerar um token a cada `push`. Um **credential helper** pode recuperar uma credencial já autorizada:

- `git config --global credential.helper cache` - Mantém a credencial temporariamente em memória; ela volta a ser solicitada depois da expiração ou do encerramento do serviço de cache.

- `git config --global credential.helper store` - Grava a credencial persistentemente em arquivo **sem criptografia**, normalmente em `~/.git-credentials`. Evita novas solicitações, mas não é recomendado para tokens importantes ou computadores compartilhados.

Para armazenamento persistente, prefira o GitHub CLI, o Git Credential Manager ou um *helper* integrado ao cofre de credenciais do sistema, como GNOME Keyring ou KDE Wallet quando houver integração disponível. O armazenamento seguro depende das ferramentas instaladas e da sessão do sistema.

## GitHub CLI:

O GitHub CLI oferece um fluxo prático de autenticação pelo navegador e pode configurar o Git para reutilizar a credencial:

- `gh auth login` - Autentica o GitHub CLI. No fluxo interativo, pode-se escolher `HTTPS`, autenticação pelo navegador e permitir que a ferramenta configure as credenciais para o Git.

- `gh auth status` - Mostra o estado da autenticação e a conta ativa.

- `gh auth setup-git` - Configura o GitHub CLI como *credential helper* para os hosts nos quais ele está autenticado.

- `gh auth logout` - Remove do GitHub CLI a autenticação armazenada localmente para a conta escolhida; não revoga necessariamente o token no servidor.

Quando há um armazenamento seguro disponível, o GitHub CLI pode guardar nele o token obtido no fluxo web. Se não houver, a própria ferramenta informa que poderá recorrer a armazenamento menos seguro; por isso, os avisos exibidos durante o login devem ser lidos.

## SSH:

No protocolo SSH, a autenticação utiliza um par de chaves. A **chave pública** é cadastrada no GitHub, enquanto a **chave privada** permanece no computador e nunca deve ser enviada a terceiros. O servidor verifica se o cliente possui a chave privada correspondente sem precisar recebê-la.

Um fluxo comum é:

```bash
$ ssh-keygen -t ed25519 -C "email@exemplo.com"
$ eval "$(ssh-agent -s)"
$ ssh-add ~/.ssh/id_ed25519
```

Depois, o conteúdo de `~/.ssh/id_ed25519.pub` deve ser cadastrado nas configurações de chaves SSH da conta. A conexão pode ser testada com:

```bash
$ ssh -T git@github.com
```

Para usar SSH em um remoto já configurado por HTTPS:

```bash
$ git remote set-url origin git@github.com:usuario/repositorio.git
```

Uma *passphrase* protege a chave privada caso o arquivo seja copiado. O `ssh-agent` mantém a chave desbloqueada durante uma sessão, e a integração do ambiente gráfico com o cofre do sistema pode persistir esse desbloqueio de forma segura entre sessões, conforme a configuração do sistema.

---

# 07. Ferramentas Adicionais

## `stash`:

O **stash** armazena temporariamente alterações ainda não commitadas e restaura um diretório de trabalho mais limpo, sendo útil para trocar de contexto sem criar um commit provisório.

- `git stash push -m "descrição"` - Guarda alterações rastreadas com uma descrição.

- `git stash push -u -m "descrição"` - Inclui também arquivos não rastreados.

- `git stash list` - Lista os *stashes* existentes.

- `git stash apply {stash}` - Reaplica um *stash* sem removê-lo da lista.

- `git stash pop` - Reaplica o *stash* mais recente e tenta removê-lo da lista.

- `git stash drop {stash}` - Remove uma entrada específica.

## Tags:

Uma **tag** atribui um nome estável a um ponto do histórico, sendo frequentemente usada para marcar versões como `v1.0.0`. Diferentemente de uma branch, ela não avança automaticamente com novos commits.

- `git tag` - Lista as tags locais.

- `git tag -a v1.0.0 -m "Versão 1.0.0"` - Cria uma tag anotada no commit atual.

- `git tag -a v1.0.0 {commit} -m "Versão 1.0.0"` - Cria uma tag anotada no commit indicado.

- `git push {remoto} v1.0.0` - Envia uma tag específica ao remoto; tags não são necessariamente enviadas por um `push` comum.

## Rebase:

O **rebase** reaplica uma sequência de commits sobre uma nova base. Por exemplo, estando em uma branch de funcionalidade, `git rebase main` reaplica os commits exclusivos dessa branch sobre a posição atual de `main`, produzindo uma história linear.

Como os commits reaplicados recebem novos identificadores, o *rebase* reescreve essa parte do histórico. Ele é útil para organizar trabalho local, mas não deve ser aplicado sem coordenação a commits publicados que outras pessoas já utilizam.

- `git rebase {nova_base}` - Reaplica os commits da branch atual sobre a base indicada.

- `git rebase --continue` - Continua o processo depois da resolução dos conflitos e da preparação dos arquivos.

- `git rebase --abort` - Cancela o processo e tenta restaurar o estado anterior ao *rebase*.

---

# Fontes:

- CHACON, Scott; STRAUB, Ben. *Pro Git*. 2. ed. New York: Apress, 2014. Disponível em: <https://git-scm.com/book/en/v2>. Acesso em: 15 set. 2026.

- GIT PROJECT. *Git Reference Documentation*. Versão 2.55.0. [S. l.]: Software Freedom Conservancy, 2026. Disponível em: <https://git-scm.com/docs>. Acesso em: 15 set. 2026.

- GITHUB. *About authentication to GitHub*. [S. l.]: GitHub, [s. d.]. Disponível em: <https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-authentication-to-github>. Acesso em: 15 set. 2026.

- GITHUB. *Caching your GitHub credentials in Git*. [S. l.]: GitHub, [s. d.]. Disponível em: <https://docs.github.com/en/get-started/git-basics/caching-your-github-credentials-in-git>. Acesso em: 15 set. 2026.

- GITHUB. *Managing your personal access tokens*. [S. l.]: GitHub, [s. d.]. Disponível em: <https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens>. Acesso em: 15 set. 2026.

- GITHUB. *Generating a new SSH key and adding it to the ssh-agent*. [S. l.]: GitHub, [s. d.]. Disponível em: <https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent>. Acesso em: 15 set. 2026.

- GITHUB. *Testing your SSH connection*. [S. l.]: GitHub, [s. d.]. Disponível em: <https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection>. Acesso em: 15 set. 2026.

- GITHUB. *GitHub CLI Manual: gh auth*. [S. l.]: GitHub, [s. d.]. Disponível em: <https://cli.github.com/manual/gh_auth>. Acesso em: 15 set. 2026.
