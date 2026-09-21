```
 _____  _  _   
|  __ \(_)| |  
| |  \/ _ | |_ 
| | __ | || __|
| |_\ \| || |_ 
 \____/|_| \__|
```

# 00. Conceitos Básicos

## *Git* e *GitHub*:

O ***Git*** é um sistema de controle de versão que permite comparar alterações, recuperar estados anteriores e desenvolver funcionalidades em linhas independentes. Cada cópia comum de um repositório contém seu próprio histórico e é mantida localmente.

O ***GitHub*** é um serviço que hospeda repositórios *Git* e acrescenta recursos de colaboração, como *issues*, *pull requests* e controle de acesso. *Git* e *GitHub* não são a mesma ferramenta: é possível utilizar *Git* sem *GitHub* e hospedar repositórios *Git* em outros serviços, como *GitLab* e *Bitbucket*.

## Repositório:

Um **repositório** é o conjunto formado pelo projeto e pelos dados usados pelo *Git* para manter seu histórico. Esses dados ficam no diretório oculto `.git`, que armazena objetos, referências e configurações locais. Apagar `.git` não apaga os arquivos do projeto, mas sim o histórico e a configuração do *Git*.

O *Git* registra cada versão como um ***snapshot*** (uma fotografia lógica do estado preparado do projeto). Um ***commit*** referencia um desses *snapshots* e contém metadados como autor, data, mensagem e o *commit* anterior. Seu identificador é calculado a partir do conteúdo e dos metadados armazenados.
> A alteração de um *commit* produz outro identificador.

## Estados dos arquivos:

Um arquivo pode estar nos seguintes estados principais:

- **Não rastreado** (*untracked*): existe no diretório de trabalho, mas ainda não participa do histórico do *Git*.
- **Não modificado** (*unmodified*): é rastreado e corresponde à versão registrada.
- **Modificado** (*modified*): é rastreado, mas seu conteúdo no diretório de trabalho difere do estado preparado ou registrado.
- **Preparado** (*staged*): seu estado atual foi copiado para a área de preparação e será incluído no próximo *commit*.
- **Registrado** (*committed*): seu estado está armazenado em um *commit* do repositório local.

## Referências e `HEAD`:

Uma ***branch*** é uma referência móvel para um *commit*. Normalmente, `HEAD` aponta para a *branch* atualmente selecionada, e essa *branch* aponta para seu *commit* mais recente. Conforme novos *commits* são criados, a referência da *branch* avança.

`HEAD~1` representa o primeiro pai do *commit* indicado por `HEAD`; `HEAD~2`, o primeiro pai dele, e assim sucessivamente. Em históricos com *merges*, essa notação segue repetidamente o primeiro pai, não necessariamente o segundo *commit* mais recente por data.

Quando `HEAD` aponta diretamente para um *commit*, em vez de uma *branch*, o repositório está em estado de **`detached HEAD`**. É possível inspecionar e até criar *commits* nesse estado, mas deve-se criar uma *branch* para conservar facilmente essa nova linha de desenvolvimento.

## Estrutura dos comandos:

A forma geral da interface é `git {opções_globais} {comando} {opções} {argumentos}` (a notação é didática e simplificada; não é totalmente padronizada).

O *Git* também permite criar *aliases* personalizados com `git config set --global alias.{nome} "{expansão}"`. A expansão é escrita sem o `git` inicial.
> Esses *aliases* são apenas abreviações configuradas pelo usuário e não devem ser confundidos com comandos distintos que possuem funcionalidades parcialmente semelhantes.

## Manuais:

- `git --help` - Mostra os comandos mais comuns e as opções globais.

- `git {comando} -h` - Mostra um resumo curto da sintaxe e das opções do comando.

- `git help {comando}` - Abre o manual completo do comando. `git {comando} --help` é uma forma equivalente.

- `git help --all` - Lista todos os comandos disponíveis.

- `git help --guides` - Lista os guias conceituais instalados.

Também podem ser consultadas a [Git Reference Documentation](https://git-scm.com/docs) e a obra [Pro Git](https://git-scm.com/book/en/v2).

---

# 01. Configuração e Identidade

## Escopos de configuração:

As configurações do *Git* podem ser aplicadas em diferentes escopos. Uma configuração mais específica normalmente sobrescreve uma configuração equivalente de escopo mais amplo.

- **Sistema** (`--system`): aplica-se aos usuários daquela instalação do sistema.
- **Global** (`--global`): aplica-se ao usuário atual e normalmente fica em `~/.gitconfig` ou `~/.config/git/config`.
- **Local** (`--local`): aplica-se somente ao repositório atual e fica em `.git/config`; este é o escopo padrão quando nenhum é informado dentro de um repositório.

- `git config set {escopo} {nome} {valor}` - Define uma configuração. Algumas das chaves mais importantes são:

  - `user.name` - Nome normalmente gravado nos *commits* do usuário.
  - `user.email` - E-mail normalmente gravado nos *commits* do usuário.
  - `init.defaultBranch` - Nome inicial das *branches* de novos repositórios, como `main`.

- `git config get {opções} {nome}` - Mostra o valor efetivo de uma configuração específica (as configurações como `user.name` definidas pelo `set`).

- `git config list {opções}` - Lista as configurações aplicáveis ao contexto atual. A opção `--show-origin` também mostra os arquivos dos quais elas foram lidas.

`user.name` e `user.email` definem a **identidade registrada no** ***commit***; eles não autenticam o usuário no *GitHub*. A conta usada para enviar o *commit* a um servidor pode ser diferente do nome e do e-mail gravados nele.

---

# 02. Versionamento e Arquivos

## Inicialização:

- `git init {opções} [{diretório}]` - Inicia um novo repositório *Git*. Quando um diretório é omitido, o repositório é iniciado no diretório atual.

- `git -C {diretório} {comando}` - Executa um comando como se o *Git* tivesse sido iniciado naquele diretório.

A maioria dos comandos procura `.git` no diretório atual e em seus diretórios-pai. Por isso, normalmente eles podem ser executados em uma subpasta do projeto, sem que o terminal esteja exatamente na raiz do repositório.

## Inspeção do estado:

- `git status {opções}` - Resume as diferenças entre `HEAD`, *index* (área de preparação) e diretório de trabalho, além de indicar arquivos não rastreados e a *branch* atual. A opção `--short` mostra o estado em formato compacto.

- `git diff {opções} [{referências}] [--] [{caminhos}]` - Mostra diferenças entre estados do projeto. Sem referências, compara o diretório de trabalho com o *index*; `--staged` compara o *index* com `HEAD`; uma referência compara o diretório de trabalho com ela; e duas referências comparam os estados indicados. `--` pode separar referências de caminhos.

## Preparação e registro:

- `git add {opções} {caminhos}` - Prepara no *index* o estado atual dos caminhos selecionados. `.` seleciona as alterações a partir do diretório atual.

- `git commit {opções}` - Cria um *commit* com o estado presente no *index*. `-m "{mensagem}"` fornece a mensagem pela linha de comando, enquanto `-a` prepara automaticamente modificações e exclusões de arquivos já rastreados, mas não inclui arquivos novos.

- `git log {opções} [{referências}] [--] [{caminhos}]` - Mostra o histórico. As opções `--oneline`, `--graph`, `--decorate` e `--all` produzem uma visualização compacta das relações entre *branches* e outras referências.

Um *commit* inclui apenas o estado preparado. Arquivos não rastreados ou alterações feitas depois do último `git add` não entram automaticamente no *commit*.

## Exclusão de arquivos:

O arquivo `.gitignore` contém padrões de arquivos **não rastreados** que o *Git* deve ignorar, como dependências baixadas, arquivos de compilação e credenciais locais. Exemplos:

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

O `.gitignore` não deixa de rastrear um arquivo que já foi incluído em *commits*. Para mantê-lo no diretório de trabalho, mas removê-lo do *index*, pode-se usar `git rm --cached {arquivo}` e então registrar essa remoção em um *commit*.
> Segredos já publicados continuam existindo no histórico e devem ser revogados, mesmo depois da remoção do arquivo.

## Restauração:

- `git restore {opções} {caminhos}` - Restaura os caminhos selecionados. Por padrão, atualiza o diretório de trabalho a partir do *index*, descartando alterações ainda não preparadas.

  - `--staged` - Atualiza o *index* a partir de `HEAD`, retirando as alterações da área de preparação sem descartá-las do diretório de trabalho.
  - `--source={commit}` - Escolhe outra referência como origem da restauração.

Como o *index* costuma corresponder a `HEAD` antes de `git add`, `git restore {arquivo}` frequentemente parece restaurar o último *commit*. A origem padrão, porém, é o *index*.

---

# 03. Histórico e Recuperação

## Alteração do último *commit*:

A opção `--amend` de `git commit` substitui o último *commit* usando o conteúdo atualmente preparado. Ela não edita o *commit* existente: cria outro *commit* (ainda precisa de `-m "{mensagem}"` para fornecer uma nova mensagem) e move a *branch* para ele.
> Se houver alterações no *index*, elas também serão incorporadas.

## `reset`:

- `git reset {opções} {commit}` - Move `HEAD` e, normalmente, a *branch* atual para outro *commit*. O modo escolhido determina o que também acontece com o *index* e o diretório de trabalho:

  - `--soft` - Move a *branch*, mas mantém no *index* e no diretório de trabalho o conteúdo dos *commits* desfeitos.
  - `--mixed` - Move a *branch* e redefine o *index*, mas mantém as alterações no diretório de trabalho. É o modo padrão.
  - `--hard` - Move a *branch* e torna o *index* e os arquivos rastreados do diretório de trabalho iguais ao *commit* indicado, descartando as alterações rastreadas afetadas.
  > `reset --hard` normalmente não remove arquivos não rastreados que não interfiram na restauração, mas pode apagar arquivos ou diretórios não rastreados que estejam no caminho de arquivos rastreados que precisam ser escritos.

## Reversão:

- `git revert {opções} {commits}` - Cria novos *commits* que aplicam o inverso das alterações introduzidas pelos *commits* indicados.
> A opção `--no-commit` aplica as reversões ao *index* e ao diretório de trabalho sem registrá-las imediatamente.

`revert` preserva o histórico existente e, por isso, costuma ser a alternativa mais segura para desfazer alterações já publicadas. O resultado pode gerar conflitos se o projeto tiver mudado desde o *commit* revertido.

## Recuperação com `reflog`:

- `git reflog [{subcomando}] {opções} [{referência}]` - Consulta ou gerencia os registros locais dos movimentos de referências. Sem subcomando, mostra o histórico de `HEAD`; `show` consulta uma referência específica.

- `git branch {nova_branch} {commit}` - Cria uma *branch* apontando para um *commit* recuperado pelo `reflog`.

O `reflog` pode ajudar a localizar *commits* que deixaram de ser alcançáveis depois de `reset`, `rebase` ou exclusão de uma *branch*. Ele é local e suas entradas expiram, portanto não deve ser tratado como cópia de segurança permanente.

---

# 04. *Branches* e Integração

## *Branches*:

Ao inicializar um repositório, o *Git* define o nome de uma *branch* inicial ainda sem *commits*, chamada de ***unborn branch***. A *branch* passa a apontar para um *commit* quando o primeiro é criado; antes disso, diversos comandos que precisam de um *commit* como referência ainda não podem operar normalmente.

- `git branch {opções} [{nome} [{ponto_inicial}]]` - Lista, cria, renomeia ou exclui *branches*, conforme os argumentos e opções. Sem argumentos, lista as *branches* locais e utiliza `*` para indicar a atual. Algumas das opções mais importantes são:

  - `-a` - Inclui as referências de *branches* remotas na listagem.
  - `-vv` - Mostra também o último *commit* e a *upstream* de cada *branch*, quando configurada.
  - `-m [{nome_antigo}] {nome_novo}` - Renomeia a *branch* atual ou a *branch* especificada.
  - `-d {branches}` - Exclui *branches* já integradas à sua *upstream* ou, na ausência dela, ao histórico alcançado por `HEAD`.
  - `-D {branches}` - Força a exclusão das referências, mesmo sem integração.

- `git switch {opções} {branch}` - Troca para a *branch* indicada e atualiza o diretório de trabalho. A opção `-c {nova_branch}` cria uma *branch* a partir da posição atual e já troca para ela.

Excluir uma *branch* remove sua referência, não necessariamente seus *commits* de imediato. Ainda assim, trabalhos não integrados podem se tornar difíceis de localizar.

## *Merge*:

- `git merge {opções} {commits}` - Integra na *branch* atual os históricos alcançáveis pelos *commits* ou *branches* indicados. `--ff-only` aceita apenas avanço direto, enquanto `--no-ff` força a criação de um *commit* de *merge* quando a integração for possível.

Quando a *branch* atual pode apenas avançar até o mesmo ponto da outra, ocorre um ***fast-forward***. Quando as linhas de desenvolvimento divergiram, o *Git* normalmente cria um *commit* de *merge* que possui mais de um pai, desde que consiga combinar as alterações.

## Conflitos:

Um **conflito** ocorre quando o *Git* não consegue decidir automaticamente como combinar alterações. Ele marca os trechos conflitantes nos arquivos e interrompe a integração para que o usuário resolva o conteúdo.

O fluxo básico para concluir um *merge* com conflitos é:

1. Executar `git status` para identificar os arquivos conflitantes.
2. Editar os arquivos e remover os marcadores de conflito, mantendo o conteúdo correto.
3. Executar `git add {arquivo}` para marcar cada conflito como resolvido.
4. Executar `git commit` para concluir o *merge*, quando o *Git* não o concluir automaticamente.

A opção `--abort` de `git merge` tenta retornar ao estado anterior ao início da integração.

---

# 05. Repositórios Remotos

## Conceitos:

Um **remoto** é um repositório acessível por uma *URL* e registrado localmente com um nome curto. `origin` é apenas o nome convencional atribuído ao remoto criado por `git clone`; não é uma palavra obrigatória nem representa necessariamente o repositório original do projeto.

Uma referência como `origin/main` é uma ***remote-tracking branch***: um registro local da posição observada da *branch* `main` no remoto `origin` durante a última comunicação. Ela não é a mesma referência que a *branch* local `main`.

Uma *branch* local pode ter uma ***upstream branch*** configurada, por exemplo, `main` seguindo `origin/main`. Essa relação permite que comandos como `git pull` e `git push` determinem seus alvos padrão e que `git status` compare as duas *branches*.

## Configuração de remotos:

- `git remote [{subcomando}] {opções} {argumentos}` - Consulta e gerencia os remotos configurados. Sem subcomando, lista seus nomes. As principais operações são:

  - `-v` - Inclui as *URLs* de busca e envio na listagem.
  - `add {nome} {URL}` - Registra um repositório remoto com o nome escolhido.
  - `set-url {nome} {nova_URL}` - Altera a *URL* de um remoto.
  - `rename {nome_antigo} {nome_novo}` - Renomeia um remoto.
  - `remove {nome}` - Remove sua configuração local e as referências de rastreamento associadas; não apaga o repositório hospedado.

A opção `--set-upstream-to={remoto}/{branch_remota}` de `git branch` define explicitamente a *upstream* da *branch* atual ou de uma *branch* local informada como argumento. A opção `-vv`, apresentada anteriormente, permite conferir essa associação.

## Transferência e integração:

- `git clone {opções} {URL} [{diretório}]` - Cria uma cópia local do repositório, incluindo seu histórico, configura um remoto normalmente chamado `origin` e obtém uma versão de trabalho. Opções importantes: `--branch {branch}` seleciona a *branch* inicial, `--depth {n}` limita a profundidade do histórico obtido e `--recurse-submodules` inicializa os submódulos configurados.

- `git fetch {opções} [{remoto} [{refspecs}]]` - Baixa objetos e atualiza referências sem integrar automaticamente as alterações à *branch* local. `--all` consulta todos os remotos, `--prune` remove referências de rastreamento que deixaram de existir no remoto e `--tags` busca todas as *tags*.

- `git pull {opções} [{remoto} [{refspecs}]]` - Executa primeiro um `fetch` e depois integra o conteúdo obtido à *branch* atual. `--rebase` utiliza *rebase*, `--no-rebase` utiliza *merge* e `--ff-only` aceita somente um avanço direto.
O `fetch` permite inspecionar as alterações antes da integração, por exemplo, com `git log HEAD..origin/main` e `git diff HEAD..origin/main`. Ele não solicita confirmação para mesclar porque não realiza a mesclagem. Já `pull` pode alterar imediatamente a *branch* e o diretório de trabalho.

- `git push {opções} [{remoto} [{refspecs}]]` - Tenta atualizar referências no remoto com o conteúdo local. `-u` ou `--set-upstream` também configura a *upstream*, `--tags` envia todas as *tags* e `--force-with-lease` condiciona uma atualização forçada ao estado remoto esperado.
> Pode ser recusado quando o remoto contém *commits* que a atualização local descartaria. Nesse caso, normalmente é necessário obter e integrar o trabalho remoto antes de tentar novamente. O envio forçado reescreve o histórico remoto e não deve ser usado como solução automática. `--force-with-lease` adiciona uma verificação de segurança, mas ainda exige cuidado e coordenação.

---

# 06. Autenticação e Credenciais

## Identidade e autenticação:

Existem dois mecanismos independentes:

- **Identidade do** ***commit***: `user.name` e `user.email` são metadados gravados no histórico.
- **Autenticação remota**: prova ao *GitHub* ou a outro servidor qual conta está realizando uma operação e quais recursos ela pode acessar.

Operações puramente locais, como `add`, `commit`, `branch` e `log`, não exigem *login* no *GitHub*. A leitura de repositórios públicos também pode não exigir autenticação, mas o envio de alterações e o acesso a repositórios privados normalmente exigem uma credencial autorizada. O método utilizado depende do protocolo da *URL* do remoto, principalmente ***HTTPS*** ou ***SSH***.

## *HTTPS* e *token*:

Nas operações *Git* por *HTTPS*, o *GitHub* não aceita a senha comum da conta como credencial. Quando a autenticação é inserida manualmente, utiliza-se o nome de usuário e um ***Personal Access Token (PAT)*** no campo da senha. O *token* é a parte que efetivamente autentica a operação.

Um *PAT* autoriza ações em nome do usuário, limitado tanto pelo acesso que a conta já possui quanto pelas permissões concedidas ao *token*. O *GitHub* recomenda *tokens* ***fine-grained*** quando eles atendem ao caso de uso, pois podem ser limitados a um proprietário, repositórios específicos, permissões específicas e um prazo de validade. O *token* deve ser tratado como uma senha: não deve ser colocado no repositório, na *URL* do remoto, em comandos que fiquem no histórico ou em mensagens.

Ao utilizar um *PAT* manualmente:

1. Criar o *token* nas configurações de desenvolvedor da conta do *GitHub*.
2. Selecionar somente os repositórios e permissões necessários e definir uma expiração apropriada.
3. Executar a operação *Git* utilizando uma *URL* *HTTPS*.
4. Informar o usuário quando solicitado e inserir o *PAT* no campo de senha.

Não é necessário gerar um *token* a cada `push`. Um ***credential helper*** pode recuperar uma credencial já autorizada. A configuração geral é `git config set --global credential.helper {helper}`. Entre os *helpers* básicos estão:

- `cache` - Mantém a credencial temporariamente em memória; ela volta a ser solicitada depois da expiração ou do encerramento do serviço de *cache*.
- `store` - Grava a credencial persistentemente em arquivo **sem criptografia**, normalmente em `~/.git-credentials`. Evita novas solicitações, mas não é recomendado para *tokens* importantes ou computadores compartilhados.

Para armazenamento persistente, prefira o *GitHub CLI*, o *Git Credential Manager* ou um *helper* integrado ao cofre de credenciais do sistema, como *GNOME Keyring* ou *KDE Wallet* quando houver integração disponível. O armazenamento seguro depende das ferramentas instaladas e da sessão do sistema.

## *GitHub CLI*:

O *GitHub CLI* oferece um fluxo prático de autenticação pelo navegador e pode configurar o *Git* para reutilizar a credencial:

- `gh auth login` - Autentica o *GitHub CLI*. No fluxo interativo, pode-se escolher `HTTPS`, autenticação pelo navegador e permitir que a ferramenta configure as credenciais para o *Git*.

- `gh auth status` - Mostra o estado da autenticação e a conta ativa.

- `gh auth setup-git` - Configura o *GitHub CLI* como *credential helper* para os *hosts* nos quais ele está autenticado.

- `gh auth logout` - Remove do *GitHub CLI* a autenticação armazenada localmente para a conta escolhida; não revoga necessariamente o *token* no servidor.

Quando há um armazenamento seguro disponível, o *GitHub CLI* pode guardar nele o *token* obtido no fluxo *web*. Se não houver, a própria ferramenta informa que poderá recorrer a armazenamento menos seguro; por isso, os avisos exibidos durante o *login* devem ser lidos.

## *SSH*:

No protocolo *SSH*, a autenticação utiliza um par de chaves. A **chave pública** é cadastrada no *GitHub*, enquanto a **chave privada** permanece no computador e nunca deve ser enviada a terceiros. O servidor verifica se o cliente possui a chave privada correspondente sem precisar recebê-la.

Um fluxo comum é:

```bash
$ ssh-keygen -t ed25519 -C "email@exemplo.com"
$ eval "$(ssh-agent -s)"
$ ssh-add ~/.ssh/id_ed25519
```

Depois, o conteúdo de `~/.ssh/id_ed25519.pub` deve ser cadastrado nas configurações de chaves *SSH* da conta. A conexão pode ser testada com:

```bash
$ ssh -T git@github.com
```

Para usar *SSH* em um remoto já configurado por *HTTPS*:

```bash
$ git remote set-url origin git@github.com:usuario/repositorio.git
```

Uma *passphrase* protege a chave privada caso o arquivo seja copiado. O `ssh-agent` mantém a chave desbloqueada durante uma sessão, e a integração do ambiente gráfico com o cofre do sistema pode persistir esse desbloqueio de forma segura entre sessões, conforme a configuração do sistema.

---

# 07. Ferramentas Adicionais

## `stash`:

O ***stash*** armazena temporariamente alterações ainda não registradas em um *commit* e restaura um diretório de trabalho mais limpo, sendo útil para trocar de contexto sem criar um *commit* provisório.

- `git stash push {opções}` - Guarda temporariamente alterações rastreadas. `-m "{descrição}"` atribui uma mensagem e `-u` inclui também arquivos não rastreados.

- `git stash list` - Lista os *stashes* existentes.

- `git stash apply {stash}` - Reaplica um *stash* sem removê-lo da lista.

- `git stash pop` - Reaplica o *stash* mais recente e tenta removê-lo da lista.

- `git stash drop {stash}` - Remove uma entrada específica.

## *Tags*:

Uma ***tag*** atribui um nome estável a um ponto do histórico, sendo frequentemente usada para marcar versões como `v1.0.0`. Diferentemente de uma *branch*, ela não avança automaticamente com novos *commits*.

- `git tag {opções} [{nome} [{commit}]]` - Lista ou cria *tags*. Sem argumentos, lista as *tags* locais; `-a` cria uma *tag* anotada e `-m "{mensagem}"` registra sua mensagem. Quando o *commit* é omitido, a *tag* aponta para `HEAD`.

*Tags* não são necessariamente enviadas por um `push` comum. Uma *tag* específica pode ser enviada como *refspec*, por exemplo, `git push {remoto} {tag}`; a opção `--tags` envia todas as *tags* locais.

## *Rebase*:

O ***rebase*** reaplica uma sequência de *commits* sobre uma nova base. Por exemplo, estando em uma *branch* de funcionalidade, `git rebase main` reaplica os *commits* exclusivos dessa *branch* sobre a posição atual de `main`, produzindo um histórico linear.

Como os *commits* reaplicados recebem novos identificadores, o *rebase* reescreve essa parte do histórico. Ele é útil para organizar trabalho local, mas não deve ser aplicado sem coordenação a *commits* publicados que outras pessoas já utilizam.

- `git rebase {opções} [{nova_base}]` - Reaplica os *commits* da *branch* atual sobre a base indicada. Durante uma interrupção por conflitos, `--continue` prossegue depois da resolução e preparação dos arquivos, enquanto `--abort` cancela o processo e tenta restaurar o estado anterior.

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
