```
 _____  _  _   
|  __ \(_)| |  
| |  \/ _ | |_ 
| | __ | || __|
| |_\ \| || |_ 
 \____/|_| \__|
```

**Autor: Kayky Moreira Praxedes**

# 0. Conceitos Básicos

## Git e Controle de Versões:

O **Git** é um sistema de controle de versões: registra estados de um projeto ao longo do tempo, permitindo consultar o que mudou, comparar versões e recuperar conteúdos anteriores. Também permite desenvolver alterações em linhas independentes e reuni-las posteriormente.

O histórico fica no próprio computador. Não é necessário ter uma conta em um serviço ou uma conexão com a internet para registrar alterações localmente.

**GitHub**, **GitLab** e **Bitbucket** são serviços que hospedam repositórios Git e oferecem recursos de colaboração. Eles utilizam o Git, mas não são o Git; o uso de repositórios remotos é explicado no capítulo 3.

## Repositório e *Commits*:

Um **repositório** reúne os arquivos do projeto e os dados que o Git utiliza para acompanhar seu histórico. Em um repositório local comum, esses dados ficam na pasta oculta `.git`. Apagá-la remove o histórico e as configurações locais do Git, mesmo que os arquivos do projeto permaneçam.

Um ***commit*** é um registro de uma versão do projeto. Ele associa uma fotografia lógica dos arquivos (*snapshot*) a informações como autor, data, mensagem e versões anteriores. A mensagem descreve o que foi feito; o identificador, também chamado de *hash*, permite localizar esse registro.

O identificador pode aparecer abreviado, como `a1b2c3d`. Alterar o conteúdo ou a mensagem de um *commit* produz outro *commit*, com outro identificador.

## Diretório de Trabalho, Preparação e Histórico:

O Git separa os arquivos em edição do conteúdo que será registrado:

| Local | Função |
| --- | --- |
| **Diretório de trabalho** | Arquivos do projeto que você abre e modifica. |
| **Área de preparação** (*staging area* ou *index*) | Estado selecionado para formar o próximo *commit*. |
| **Histórico** | Versões já registradas por meio dos *commits*. |

O fluxo comum consiste em editar os arquivos, selecionar as alterações com `git add` e registrá-las com `git commit`. A preparação permite escolher o que entra em cada registro, sem precisar incluir todas as alterações de uma vez.

Um arquivo novo é **não rastreado** (*untracked*) até ser adicionado ao Git. Depois disso, suas alterações podem estar **modificadas** (*modified*, ainda não preparadas) ou **preparadas** (*staged*, selecionadas para o próximo registro). Quando o arquivo corresponde ao estado registrado, ele está **não modificado** (*unmodified*).

Um mesmo arquivo pode ter alterações preparadas e outras não preparadas. Por exemplo, se você executar `git add` e depois editar novamente o arquivo, o próximo *commit* incluirá a versão preparada antes dessa última edição. É necessário outro `git add` para atualizar a preparação.

## *Branches* e `HEAD`:

Uma ***branch*** representa uma linha de desenvolvimento. Ela aponta para um *commit* e avança quando novos registros são feitos naquela linha. É possível criar outra *branch* para trabalhar em uma mudança sem alterar imediatamente a linha principal.

Normalmente, **`HEAD`** indica a *branch* selecionada; quando usado como referência a uma versão, representa o *commit* atual dessa *branch*. `HEAD~1` indica seu antecessor e `HEAD~2`, dois passos para trás. Em uma mesclagem, essa contagem acompanha a linha do primeiro pai, e não a ordem das datas dos *commits*.

Nos exemplos, `main` é o nome da *branch* principal. Esse nome é uma convenção e pode ser diferente em outros projetos.

## Instalação:

Para informações de instalação, consulte a [página oficial](https://git-scm.com/install/).

Depois da instalação, abra um terminal e execute `git --version` para conferir se o comando está disponível.

## Configuração Inicial:

O Git precisa de um nome e um e-mail para identificar a autoria dos *commits*. Essa identificação **não realiza login** em nenhum serviço remoto.

```bash
git config --global user.name "Seu Nome"
git config --global user.email "email@exemplo.com"
git config --global init.defaultBranch main
```

A opção `--global` aplica a configuração ao usuário atual. Para usar outra identidade apenas em um projeto, execute o comando dentro de seu repositório com `--local` no lugar de `--global`. A configuração local prevalece sobre a global naquele projeto.

`init.defaultBranch` define o nome inicial para novos repositórios; não renomeia as *branches* de projetos existentes.

- `git config --get {chave}` - Consulta uma configuração, como `user.email`.

- `git config --list` - Lista as configurações aplicáveis. `--show-origin` também mostra de qual arquivo cada uma veio.

---

# 1. Comandos Básicos

## Estrutura dos Comandos:

A forma básica é `git {comando} {opções} {argumentos}`. O comando escolhe a operação; as opções, também chamadas de *flags*, ajustam seu comportamento; os argumentos indicam sobre o que ela atua.

> Nos modelos de sintaxe, elementos entre `{chaves}` representam conteúdo a ser substituído, sem manter as chaves. `{opções}` indica modificadores opcionais; a descrição principal apresenta o comportamento sem eles, considerando os argumentos indicados. Quando um argumento também puder ser omitido, isso será explicado no texto. Os exemplos concretos já apresentam a forma usada no terminal.

> Variações de uso que mudam significativamente a operação aparecem em listas subordinadas, com o comando completo. Ajustes simples são explicados na descrição principal. Formas específicas também podem aparecer separadamente na seção correspondente à sua finalidade.

Em `git commit -m "Corrige a leitura do botão"`, `commit` é a operação, `-m` indica que a mensagem será fornecida no comando e o texto entre aspas é essa mensagem.

## Manuais:

- `git --help` - Mostra os comandos mais comuns e as opções globais.

- `git {comando} -h` - Mostra um resumo da sintaxe e das opções, como `git restore -h`.

- `git help {opções} {comando}` - Abre o manual completo. A documentação também está disponível no [site do Git](https://git-scm.com/docs).
  
    - `git help --all` - Lista todos os comandos disponíveis.
    - `git help --guides` - Lista os guias conceituais instalados.

## Iniciar um Repositório:

- `git init {opções} {diretório}` - Inicia um repositório. Sem o diretório, utiliza a pasta atual.

> O comando prepara o repositório, mas ainda não registra os arquivos: isso é feito com `add` e `commit`.

## Preparar e Registrar Alterações:

- `git add {opções} {caminhos}` - Prepara o estado atual dos arquivos ou diretórios informados.

    - `git add .` - Seleciona a pasta atual (inclui as alterações a partir dela).
    - `git add -A` - Inclui alterações de todo o repositório, inclusive exclusões.

- `git commit {opções}` - Registra o conteúdo preparado e abre o editor configurado para escrever a mensagem.

    - `git commit -m "{mensagem}"` - Registra o conteúdo preparado, fornecendo a mensagem diretamente, sem abrir o editor.
    - `git commit -a -m "{mensagem}"` - Prepara automaticamente modificações e exclusões de arquivos já rastreados antes de registrar, sem adicionar arquivos novos ainda não rastreados.

    > Um arquivo novo passa a ser rastreado ao ser adicionado com `git add`, mesmo antes de seu primeiro *commit*. Diferentemente de `commit -a`, `git add -A` também prepara arquivos novos ainda não rastreados.

```bash
git add arquivo_1.txt diretorio_1/arquivo_2.md diretorio_2/
git commit -m "Atualiza a documentação."
```

Se outros arquivos já estiverem preparados (além desse último `add`), eles também entrarão no *commit*.

## Ignorar Arquivos:

O arquivo **`.gitignore`**, normalmente colocado na raiz do projeto, define padrões de arquivos que não devem ser incluídos no versionamento. É útil para arquivos de compilação, dependências baixadas e configurações locais.

```gitignore
# Diretório de compilação
build/

# Arquivos com extensão .o
*.o

# Configuração local
.env
```

O `.gitignore` não interrompe o rastreamento de arquivos já adicionados. Para deixar de rastrear um deles, mantendo a cópia local:

- `git rm --cached {arquivo}` - Prepara a remoção do arquivo do versionamento, sem apagar a cópia local. `-r` permite aplicar a operação a um diretório.

Depois, inclua o caminho no `.gitignore` e registre ambas as alterações em um *commit*. Isso não remove o arquivo dos *commits* anteriores. Se uma credencial já foi publicada, removê-la do projeto não basta: ela deve ser revogada no serviço correspondente.

## Restaurar Arquivos e Retirar da Preparação:

- `git restore {opções} {arquivo}` - Substitui o conteúdo do arquivo pela versão da área de preparação, descartando suas alterações ainda não preparadas.

    - `git restore --staged {arquivo}` - Retira as alterações da preparação, mantendo o conteúdo do arquivo no diretório de trabalho. É a forma comum de desfazer um `add` depois que o repositório já possui um *commit*.
    - `git restore --source={versão} {arquivo}` - Recupera no diretório de trabalho o conteúdo que o arquivo tinha na versão indicada. Não remove *commits* nem altera a posição da *branch*.

```bash
git restore --staged main.cpp          # Desfaz a preparação, preservando a edição.
git restore --source=HEAD~1 main.cpp   # Recupera o arquivo da versão anterior.
```

As duas linhas mostram operações independentes. Após recuperar uma versão antiga, use `add` e `commit` se quiser registrar esse conteúdo como uma nova alteração.

**Restaurar o conteúdo descarta a edição substituída.** A origem padrão de `restore` é a preparação; com `--staged`, é `HEAD`. Por isso, um `restore` comum não necessariamente recupera o último *commit*: ele pode recuperar uma edição que você já havia preparado.

## Corrigir o Último *Commit*:

- `git commit --amend {opções}` - Substitui o último *commit* por outro, incorporando o estado preparado e permitindo editar a mensagem.

    - `git commit --amend --no-edit` - Mantém a mensagem anterior.
    - `git commit --amend -m "{mensagem}"` - Fornece uma nova mensagem diretamente.

Para incluir uma correção esquecida no último registro:

```bash
git add main.cpp
git commit --amend --no-edit
```

Para alterar apenas a mensagem, use `git commit --amend -m "Nova mensagem"` sem outras alterações preparadas. Caso existam, elas também serão incluídas.

O identificador do *commit* muda. Utilize essa correção preferencialmente antes de publicar o registro; substituir *commits* que outras pessoas já utilizam exige coordenação.

## Desfazer *Commits*:

Há duas situações distintas: voltar a *branch* para uma versão anterior ou criar um novo registro que desfaça uma alteração mantendo o histórico existente.

### Voltar a *Branch* com `reset`:

- `git reset {opções} {versão}` - Move a *branch* atual para a versão indicada. Sem opção de modo, utiliza `--mixed`: redefine a preparação para essa versão, preservando os arquivos do diretório de trabalho. Os *commits* posteriores deixam de fazer parte do histórico dessa *branch*. Se a versão for omitida, utiliza `HEAD`.

| Opção | Área de Preparação | Diretório de Trabalho |
| --- | --- | --- |
| `--soft` | Mantém o estado preparado. | Mantém os arquivos e suas edições. |
| `--mixed` | Volta ao estado da versão indicada. | Mantém os arquivos e suas edições. |
| `--hard` | Volta ao estado da versão indicada. | Restaura os arquivos rastreados para essa versão, descartando as alterações afetadas. |

Para desfazer o último *commit* mantendo seu conteúdo preparado para um novo registro:

```bash
git reset --soft HEAD~1
```

Se preferir escolher novamente o que será preparado, use `git reset --mixed HEAD~1`. Esses comandos exigem que exista um *commit* anterior; não servem para remover o primeiro registro do repositório.

**`--hard` pode eliminar trabalho ainda não registrado**, inclusive alterações anteriores à tentativa de desfazer o *commit*. Também pode remover arquivos não rastreados que estejam no caminho da restauração. Não é uma alternativa para quem deseja apenas reorganizar os registros.

### Desfazer uma Alteração com `revert`:

- `git revert {opções} {commit}` - Cria um novo *commit* que desfaz as alterações introduzidas pelo registro indicado, preservando o histórico existente. `git revert HEAD` reverte o último registro comum, sem mesclagem.

É a opção usual para desfazer alterações já compartilhadas. Reverter um *commit* antigo não restaura necessariamente o projeto inteiro àquela época: desfaz as mudanças daquele registro, podendo gerar conflitos com alterações posteriores.

Se houver conflitos, edite os arquivos, prepare a resolução com `git add` e prossiga com `git revert --continue`. Para cancelar a reversão ainda em andamento, utilize `git revert --abort`.

> `reset` e `--amend` reorganizam o histórico local; não atualizam automaticamente o servidor. Os exemplos de `revert` tratam de *commits* comuns: reverter um *commit* de mesclagem exige escolher a linha de referência e não faz parte deste fluxo básico.

## Recuperar um *Commit* Perdido:

- `git reflog {opções}` - Lista movimentos recentes de `HEAD`, como trocas de *branch* e operações de `reset`. Ajuda a localizar um *commit* que deixou de aparecer no histórico atual.

- `git branch {nome} {commit}` - Cria uma *branch* apontando para o registro encontrado. A operação preserva esse ponto sem substituir os arquivos atuais; as operações com *branches* são detalhadas no próximo capítulo.

Procure no `reflog` o identificador do estado desejado e utilize-o no segundo comando. As entradas são locais e expiram: o `reflog` não é uma cópia de segurança permanente nem recupera, de forma geral, edições que nunca foram registradas.

## Consultar Alterações e Histórico:

- `git status {opções}` - Mostra a *branch* atual, arquivos novos e alterações preparadas ou ainda não preparadas. `--short` apresenta uma saída compacta.

- `git diff {opções}` - Mostra as alterações dos arquivos rastreados que ainda não foram preparadas.

    - `git diff --staged` - Mostra o que foi preparado em relação ao último *commit*.
    - `git diff {versão_1} {versão_2}` - Compara os arquivos entre duas versões. Para limitar a comparação a um arquivo, acrescente `-- {arquivo}`.

- `git log {opções}` - Mostra os *commits* do histórico da *branch* atual, incluindo identificador, autoria e mensagem. `--oneline` resume cada registro em uma linha; `--graph` desenha as relações entre os *commits*.

    - `git log --all` - Inclui os históricos das demais *branches* e de outras referências do repositório, em vez de partir apenas da *branch* atual.

```bash
git diff --staged                  # Confere o conteúdo preparado.
git diff HEAD~1 HEAD -- main.cpp   # Compara o arquivo entre as duas versões.
git log --oneline --graph --all    # Visualiza o histórico das branches.
```

`status` informa **quais arquivos** mudaram; `diff` mostra **o que mudou**; `log` mostra **o que já foi registrado**. Arquivos novos ainda não rastreados aparecem no `status`, mas seu conteúdo não aparece no `git diff` comum.

> Se a saída abrir em um visualizador de páginas, como `less`, pressione `q` para sair.

---

# 2. *Branches* e Mesclagem

## Funcionamento das *Branches*:

Criar uma *branch* não cria outra pasta do projeto: cria uma referência para um ponto do histórico. Inicialmente, duas *branches* podem apontar para o mesmo *commit*. Conforme cada uma recebe novos registros, suas linhas de desenvolvimento se separam.

Ao trocar de *branch*, o Git atualiza os arquivos do diretório de trabalho para a versão selecionada. As alterações ainda não registradas não pertencem automaticamente a uma *branch*: podem acompanhar a troca quando compatíveis, ou impedir a operação quando seriam sobrescritas.

## Gerenciar *Branches*:

- `git branch {opções}` - Lista as *branches* locais e marca a atual com `*`.

    - `git branch -a` - Inclui as referências de *branches* remotas (capítulo 3).
    - `git branch -vv` - Mostra o último *commit* e a associação remota de cada *branch*, quando existir.
    - `git branch {nome} {versão}` - Cria uma *branch* a partir da versão indicada. Se não for passada versão, cria a partir de `HEAD`. Não troca para ela (continua na atual).
    - `git branch -m {nome_antigo} {novo_nome}` - Renomeia uma *branch*. Se não for passada uma *branch* específica (sem `nome_antigo`), ela renomeia a *branch* atual.
    - `git branch -d {branch}` - Exclui uma *branch* após verificar se seus *commits* estão integrados à linha que ela acompanha ou, sem essa associação, à linha atual.
    - `git branch -D {branch}` - Força a exclusão mesmo sem essa integração.

- `git switch {opções} {branch}` - Troca para uma *branch* existente.

    - `git switch -c {nome}` - Cria uma *branch* no ponto atual e já troca para ela.

Troque para outra *branch* antes de excluir a atual. A exclusão remove o nome que apontava para o histórico; não desfaz alterações já mescladas em outra *branch*. Com `-D`, trabalhos não integrados podem ficar difíceis de localizar.

> Em tutoriais antigos, `checkout` aparece tanto para trocar de *branch* quanto para restaurar arquivos. Aqui são utilizados `switch` e `restore`, que separam essas finalidades.

## Guardar Alterações Temporariamente:

- `git stash {opções}` - Guarda alterações ainda não registradas para permitir uma pausa no trabalho, como trocar de *branch* para resolver outro problema, sem criar um *commit* provisório. Sem um subcomando, executa a operação de `stash push`.

    - `git stash push {opções}` - Guarda as alterações de arquivos rastreados e retira essas edições do diretório de trabalho e da preparação. `-m "{descrição}"` identifica o conteúdo guardado.
    - `git stash push -u` - Inclui também arquivos não rastreados, além das alterações rastreadas.
    - `git stash list` - Lista as entradas disponíveis.
    - `git stash apply` - Reaplica a entrada mais recente sem removê-la da lista.
    - `git stash pop` - Reaplica a entrada mais recente e a remove se a aplicação terminar sem conflitos.
    - `git stash drop` - Exclui a entrada mais recente sem reaplicá-la.

`apply`, `pop` e `drop` aceitam uma entrada específica, como `git stash apply 'stash@{1}'`. Nesse caso, as chaves fazem parte do identificador mostrado por `stash list`.

```bash
git stash push -u -m "Leitura do botão em andamento"
git switch main
# Depois de terminar o outro trabalho, volte à branch original.
git switch leitura-botao
git stash pop
```

O exemplo pressupõe que a *branch* original se chama `leitura-botao`. A reaplicação pode gerar conflitos se os arquivos tiverem mudado; nesse caso, a entrada é mantida. Arquivos ignorados pelo `.gitignore` não são incluídos por `-u`.

## Mesclar *Branches*:

A **mesclagem** (*merge*) integra o trabalho de outra *branch* na *branch* atual. Antes de iniciá-la, registre ou guarde as alterações pendentes para facilitar a conferência e um eventual cancelamento.

- `git merge {opções} {branch}` - Integra a *branch* indicada na atual.

    - `git merge --ff-only {branch}` - Aceita apenas um avanço direto; recusa a integração se os históricos divergiram.
    - `git merge --no-ff {branch}` - Cria um *commit* de mesclagem mesmo quando um avanço direto seria possível, desde que haja algo a integrar.

```bash
git switch main
git merge leitura-botao
```

Nesse exemplo, `main` recebe o trabalho de `leitura-botao`. O comando não troca para a *branch* indicada nem a exclui.

Se `main` não recebeu *commits* próprios depois da separação, pode apenas avançar até o ponto de `leitura-botao`: é o ***fast-forward***. Se ambas receberam registros, o Git normalmente cria um *commit* de mesclagem para unir os dois históricos.

**Históricos divergentes não significam necessariamente conflito.** O Git pode combinar as alterações automaticamente quando elas são compatíveis.

## Resolver Conflitos:

Um **conflito** ocorre quando o Git não consegue combinar as alterações sozinho, como quando duas *branches* modificam o mesmo trecho de maneiras diferentes. A mesclagem fica pendente até que o conteúdo final seja definido.

### Escolher o Conteúdo e Concluir:

Em um conflito textual, o arquivo pode apresentar algo assim:

```text
<<<<<<< HEAD
intervalo = 500;
=======
intervalo = 1000;
>>>>>>> leitura-botao
```

Nesse *merge*, a parte acima de `=======` veio da *branch* atual; a de baixo, da *branch* que está sendo incorporada. Os marcadores delimitam o conflito e precisam ser removidos junto do conteúdo que não será mantido.

1. Execute `git status` para localizar os arquivos conflitantes.
2. Edite cada arquivo, escolhendo um dos conteúdos ou combinando as alterações. No exemplo, se o intervalo desejado for 1000, deixe apenas `intervalo = 1000;` naquele trecho.
3. Salve e confira o resultado. Execute `git add {arquivo}` para marcar a resolução.
4. Depois de resolver todos os conflitos, execute `git commit` para concluir a mesclagem.

O `add` informa que você terminou a resolução; ele não verifica se a lógica do arquivo está correta.

### Manter um dos Lados de um Arquivo:

Durante um *merge* com conflito de conteúdo, também é possível escolher a versão inteira de um arquivo:

- `git restore --ours {arquivo}` - Mantém a versão da *branch* atual.

- `git restore --theirs {arquivo}` - Mantém a versão da *branch* que está sendo incorporada.

São alternativas: escolha apenas uma para o arquivo, confira e execute `git add {arquivo}`. Elas selecionam o arquivo inteiro daquele lado, não apenas um trecho conflitante, e não descartam toda a outra *branch*.

### Cancelar a Mesclagem:

- `git merge --abort` - Cancela uma mesclagem ainda em andamento e tenta restaurar o estado anterior. Não serve para desfazer uma mesclagem já concluída.

O cancelamento pode não reconstruir edições que já estavam pendentes antes do início; por isso, a preparação anterior ao *merge* é importante.

---

# 3. Repositórios Remotos

## Repositório Local e Remoto:

Um **repositório remoto** é outra cópia do repositório com a qual o Git pode trocar histórico, normalmente hospedada em um servidor. O repositório local continua independente: registrar um *commit* não o envia automaticamente ao remoto.

GitHub, GitLab e Bitbucket utilizam os mesmos comandos Git para essa troca. Mudam o endereço, as permissões e os métodos de autenticação oferecidos por cada serviço.

Um remoto é identificado localmente por um nome curto e uma URL. **`origin`** é o nome convencional utilizado na clonagem, mas poderia ser outro.

## Clonar um Repositório:

- `git clone {opções} {URL} {diretório}` - Cria uma cópia local de um repositório existente, trazendo seu histórico e os arquivos de uma *branch* inicial. Também configura o remoto `origin`. Se o diretório for omitido, cria uma subpasta no diretório atual, normalmente com o nome do repositório.

```bash
git clone https://github.com/usuario/projeto.git
cd projeto
```

> Depois da clonagem, não é necessário executar `git init` ou cadastrar novamente o mesmo remoto.

## Conectar e Gerenciar Remotos:

Se o projeto começou localmente, crie um repositório no serviço escolhido e cadastre sua URL. Para publicar um histórico local existente, um remoto inicialmente vazio evita criar dois históricos independentes; não acrescente um README pelo serviço nessa etapa.

- `git remote {opções}` - Lista os nomes dos remotos configurados. `-v` também mostra seus endereços.

    - `git remote add {nome} {URL}` - Cadastra um remoto; não envia os arquivos ainda.
    - `git remote set-url {nome} {nova_URL}` - Altera o endereço de um remoto existente.
    - `git remote remove {nome}` - Remove a configuração local do remoto; não apaga o repositório hospedado.

```bash
git remote add origin https://github.com/usuario/projeto.git
```

## Autenticação e Credenciais:

O nome e o e-mail configurados em `user.name` e `user.email` identificam a autoria dos *commits*. A **autenticação** determina qual conta acessa o servidor e quais operações ela pode realizar. Alterar o e-mail local não troca a conta autenticada.

Repositórios públicos normalmente permitem leitura sem autenticação. O envio de alterações e o acesso a projetos privados exigem credenciais e permissão. A URL escolhida determina o método utilizado, principalmente HTTPS ou SSH.

### HTTPS e Armazenamento de Credenciais:

Uma URL HTTPS tem a forma `https://servidor/usuario/projeto.git`. O serviço pode autenticar por navegador ou solicitar uma credencial, como um **token**: um código que autoriza operações dentro das permissões concedidas.

No GitHub, a senha comum da conta não autentica operações Git por HTTPS. Ao inserir as credenciais manualmente, informe o usuário e utilize um *Personal Access Token* no campo de senha. Outros serviços possuem seus próprios procedimentos.

Um ***credential helper*** guarda ou recupera credenciais para evitar digitá-las a cada operação. Os mecanismos abaixo são alternativas, não etapas para executar em sequência.

| Mecanismo | Utilidade e Uso Básico |
| --- | --- |
| `cache` | Guarda temporariamente em memória, em sistemas com suporte, como Linux. Configure com `git config --global credential.helper 'cache --timeout=3600'` para manter por até uma hora. |
| `store` | Grava em arquivo sem criptografia. É habilitado com `git config --global credential.helper store`, mas expõe o token a quem puder ler o arquivo; prefira um cofre de credenciais. |
| Git Credential Manager | Gerencia autenticação HTTPS e integração com armazenamento de credenciais. Depois de instalado, `git credential-manager configure` configura seu uso pelo Git. |
| GitHub CLI | Oferece autenticação pelo navegador e pode fornecer a credencial ao Git. É específico para GitHub, incluindo suas instalações empresariais. |

No Git Credential Manager, Windows e macOS possuem cofres padrão; no Linux é necessário selecionar e configurar um armazenamento compatível. Consulte as instruções oficiais de [instalação](https://github.com/git-ecosystem/git-credential-manager#download-and-install), [uso](https://github.com/git-ecosystem/git-credential-manager/blob/main/docs/usage.md) e [armazenamento](https://github.com/git-ecosystem/git-credential-manager/blob/main/docs/credstores.md).

Com o [GitHub CLI](https://cli.github.com/) instalado:

- `gh auth login` - Inicia a autenticação; escolha HTTPS e o fluxo pelo navegador para esse uso.

- `gh auth setup-git` - Configura o Git para aproveitar a autenticação do GitHub CLI.

- `gh auth status` - Confere a conta e a autenticação utilizadas pela ferramenta.

O GitHub CLI tenta usar o cofre do sistema, mas pode recorrer a arquivo sem criptografia se ele não estiver disponível. Confira os avisos de autenticação. Tokens não devem ser incluídos no projeto nem na URL do remoto.

### SSH:

No SSH, a autenticação utiliza um par de chaves: a **pública** é cadastrada no serviço; a **privada** permanece no computador. Uma senha de proteção (*passphrase*) pode proteger o arquivo da chave privada.

Para criar e carregar uma chave, em Bash ou Git Bash:

```bash
ssh-keygen -t ed25519 -C "email@exemplo.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

Os dois últimos comandos pressupõem que você aceitou o caminho padrão na criação. Se já existir uma chave nesse caminho, não a sobrescreva sem verificar seu uso. Cadastre no serviço o conteúdo de `~/.ssh/id_ed25519.pub`; nunca envie o arquivo sem `.pub`.

Copie a URL SSH fornecida pelo serviço e utilize-a na clonagem ou na configuração do remoto. Por exemplo, no GitHub:

```bash
ssh -T git@github.com
git remote set-url origin git@github.com:usuario/projeto.git
```

Na primeira conexão, confira a identificação do servidor com a documentação oficial antes de aceitá-la. O primeiro comando testa a autenticação; o segundo altera um remoto já existente. O `ssh-agent` permite reutilizar a chave carregada durante a sessão.

## Enviar e Receber Alterações:

- `git push {opções} {remoto} {branch}` - Envia os *commits* e tenta atualizar a *branch* correspondente no remoto.

    - `git push -u {remoto} {branch}` - Envia os *commits* e configura o acompanhamento da *branch* remota (*upstream*), permitindo normalmente omitir os nomes nos próximos `push` e `pull` dessa *branch*.

```bash
git push -u origin main
# Depois de criar novos commits, permanecendo na branch main:
git push # No fluxo deste exemplo, equivale a "git push origin main".
```

Esse primeiro envio pressupõe que existe ao menos um *commit* e uma *branch* local chamada `main`. Ele não inclui arquivos ou edições que ainda não foram registrados.

**`main` e `origin/main` não são a mesma referência.** `main` acompanha o trabalho local; `origin/main` registra a posição conhecida da *branch* no servidor. Essa informação local precisa ser atualizada por comunicação com o remoto.

- `git fetch {opções} {remoto}` - Obtém as novidades e atualiza as referências remotas locais, sem mesclar automaticamente na *branch* de trabalho. Se o remoto for omitido, utiliza o associado à *branch* atual ou, na ausência dessa associação, `origin`.

    - `git fetch --prune {remoto}` - Também limpa referências de *branches* que já foram excluídas no servidor; não exclui as *branches* locais de trabalho.

- `git pull {opções} {remoto} {branch}` - Obtém as novidades e tenta integrá-las à *branch* atual. Sem opções de integração, segue o comportamento configurado no ambiente. Com o acompanhamento remoto configurado, os nomes podem ser omitidos.

    - `git pull --ff-only {remoto} {branch}` - Aceita apenas avanço direto; recusa a integração se os históricos divergiram.
    - `git pull --no-rebase {remoto} {branch}` - Seleciona mesclagem, como no capítulo 2.

Para conferir as novidades antes de incorporá-las, estando em `main`:

```bash
git fetch origin
git log --oneline HEAD..origin/main
git diff HEAD origin/main
git merge origin/main
```

Nesse uso de `log`, `HEAD..origin/main` mostra os *commits* conhecidos no remoto que ainda não fazem parte da linha atual. O `diff` compara os conteúdos. A integração acontece apenas no último comando.

Se quiser obter e integrar em uma única operação, use `git pull --no-rebase origin main`, também estando na *branch* que deve receber essas mudanças. A opção explícita evita depender da configuração de integração daquele ambiente.

## Problemas de Sincronização:

### Envio Recusado ou Históricos Divergentes:

Um envio recusado com indicação de *non-fast-forward* ou necessidade de buscar alterações costuma significar que o servidor possui *commits* que sua linha local ainda não incorpora. Não resolva automaticamente com envio forçado, pois ele pode descartar trabalho compartilhado.

Estando na *branch* que deve ser sincronizada, com alterações pendentes registradas ou guardadas, obtenha e integre o histórico remoto:

```bash
git pull --no-rebase origin main
# Se houver conflitos, resolva-os e conclua o commit de mesclagem.
git push origin main
```

Substitua `main` pela *branch* correspondente. Se o Git informar que precisa de uma estratégia para reconciliar linhas divergentes, `--no-rebase` escolhe a mesclagem. Se a tentativa anterior usou `--ff-only`, a recusa indica que o avanço direto não é suficiente.

Em caso de conflito, siga [Resolver Conflitos](#resolver-conflitos). Uma recusa causada por falta de permissão ou proteção da *branch* exige corrigir o acesso ou seguir o fluxo de colaboração do projeto; um `pull` não resolve essas restrições.

### Remoto, *Branch* ou Autenticação Incorretos:

| Situação | O que Conferir |
| --- | --- |
| `remote origin already exists` | Use `git remote -v`. Para corrigir o endereço, utilize `remote set-url`, em vez de cadastrar o mesmo nome novamente. |
| Ausência de *upstream* | Confira a *branch* e, no primeiro envio, utilize `git push -u origin {branch}`. |
| `src refspec main does not match any` | Verifique se existe um *commit* e se a *branch* se chama realmente `main`. |
| Falha de autenticação ou `Permission denied (publickey)` | Confira a URL, o método de acesso e a credencial ou chave cadastrada na conta autorizada. |
| Alterações locais seriam sobrescritas | Use `git status` e registre ou guarde o trabalho com `stash` antes de atualizar. |

---

# Fontes

- CHACON, Scott; STRAUB, Ben. *Pro Git*. 2. ed. Apress, 2014. [Edição on-line em português](https://git-scm.com/book/pt-br/v2).
- GIT PROJECT. *Git Reference Documentation*. [Referência dos comandos](https://git-scm.com/docs). Consultas de revisão: 17 nov. 2026.
- GIT PROJECT. *Install*. [Windows](https://git-scm.com/install/win), [Linux e outros sistemas Unix](https://git-scm.com/install/linux) e [macOS](https://git-scm.com/install/mac). Acesso em: 17 nov. 2026.
- GIT PROJECT. *git-credential-cache* e *git-credential-store*. [Armazenamento temporário](https://git-scm.com/docs/git-credential-cache) e [armazenamento em arquivo](https://git-scm.com/docs/git-credential-store). Acesso em: 17 nov. 2026.
- GIT ECOSYSTEM. *Git Credential Manager*. [Uso](https://github.com/git-ecosystem/git-credential-manager/blob/main/docs/usage.md) e [cofres de credenciais](https://github.com/git-ecosystem/git-credential-manager/blob/main/docs/credstores.md). Acesso em: 17 nov. 2026.
- GITHUB. *GitHub CLI Manual*. [Autenticação](https://cli.github.com/manual/gh_auth_login) e [integração com Git](https://cli.github.com/manual/gh_auth_setup-git). Acesso em: 17 nov. 2026.
- GITHUB. *Managing your personal access tokens* e *Generating a new SSH key and adding it to the ssh-agent*. [Tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) e [chaves SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent). Acesso em: 17 nov. 2026.
