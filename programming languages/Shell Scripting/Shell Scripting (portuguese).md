```
 _____  _            _  _ 
/  ___|| |          | || | 
\ `--. | |__    ___ | || | 
 `--. \| '_ \  / _ \| || | 
/\__/ /| | | ||  __/| || | 
\____/ |_| |_| \___||_||_| 
 _____              _         _    _               
/  ___|            (_)       | |  (_)              
\ `--.   ___  _ __  _  _ __  | |_  _  _ __    __ _ 
 `--. \ / __|| '__|| || '_ \ | __|| || '_ \  / _` |
/\__/ /| (__ | |   | || |_) || |_ | || | | || (_| |
\____/  \___||_|   |_|| .__/  \__||_||_| |_| \__, |
                      | |                     __/ |
                      |_|                    |___/   
```

**Autor: Kayky Moreira Praxedes**

# 0. Conceitos Básicos

> Este resumo utiliza **Bash em GNU/*Linux***. Os comandos de sistema podem variar conforme a distribuição; recursos próprios do Bash não estão necessariamente disponíveis em outras *shells* ou ao executar um arquivo com `sh`. 

> Nos modelos de sintaxe, **elementos entre chaves indicam conteúdo a ser substituído** (como comandos, caminhos, valores, *flags*, etc.). As chaves devem ser retiradas no uso real, salvo quando forem explicitamente apresentadas como parte da sintaxe da *shell*.

## *Shell*:

A *shell* é um programa que funciona como **interpretador de comandos** e também como uma **linguagem de programação**, permitindo controlar e combinar outros programas do sistema. O **terminal** fornece o meio de entrada e saída usado para interagir com a *shell*, enquanto o ***kernel*** é a parte do sistema operacional responsável por gerenciar recursos como processos, arquivos e dispositivos.

Ao abrir um terminal em uma interface gráfica, normalmente é criado um **pseudoterminal** e iniciada uma *shell* (como o `bash`) conectada a ele. Os comandos digitados são então interpretados pela *shell*, que pode executá-los internamente ou iniciar outros programas.

### Programas:

Os comandos executados pela *shell* podem ser **internos** (*builtins*, implementados pela própria *shell*) ou **programas externos**, armazenados no sistema de arquivos. Por exemplo, `cd` é um *builtin*, enquanto `ls` normalmente corresponde a um programa externo.

Quando um programa externo é chamado apenas pelo nome, sem indicar seu caminho, o `bash` procura por um executável correspondente nos diretórios definidos pela variável `$PATH`. Por isso é possível executar simplesmente `ls` em vez de escrever seu caminho completo, como `/usr/bin/ls`.

### *Status* de Saída:

Além das informações que um comando pode imprimir, sua execução termina produzindo um ***status*** **de saída** (*exit status*), representado por um valor de `0` a `255`. Por convenção, `0` representa uma execução bem-sucedida, enquanto valores diferentes de `0` indicam algum tipo de falha ou condição diferente do sucesso.

O *status* do último comando executado fica disponível em `$?`. Esse valor é importante em *scripts*, pois permite tomar decisões com base no sucesso ou falha de comandos anteriores.

## Manuais:

Como o *Linux* recebe atualizações recorrentes, manuais impressos podem ficar desatualizados. A maior parte das informações necessárias, desde comandos de usuário até interfaces internas do *kernel* e da biblioteca C, pode ser consultada no próprio sistema operacional (SO).

### Hierarquia do Sistema:

Por uma questão de organização, os manuais foram divididos em grupos representados por números, seguindo a seguinte hierarquia:

1. Comandos de usuário;
2. Interfaces de chamadas de sistema do *kernel*;
3. Interfaces da biblioteca C;
4. Arquivos especiais (dispositivos, *drivers*, etc.);
5. Formatos de arquivos;
6. Jogos e divertimentos;
7. Assuntos variados (*miscellaneous*);
8. Comandos de administração do sistema;
9. Comandos internos do *kernel* (pouco usado no *Linux*, que mantém essa documentação no site oficial);
10. *New* (seção adicional para documentação nova ou em elaboração, quando utilizada).

> As duas últimas entradas variam conforme a distribuição e o ambiente *Linux*. A seção 9 não é padronizada; *New* pode aparecer identificada por `n`, não sendo uma seção 10 universal.

- `apropos {palavra}` - Mostra um resumo de uma linha sobre a palavra pesquisada (para que serve o comando, qual é o tipo de arquivo etc.). Retorna resultados parciais encontrados no título e no resumo, inclusive quando a busca corresponde a parte de uma palavra.

```
apropos grep
bzegrep (1)          - search possibly bzip2 compressed files for a regular expression
grep (1)             - print lines that match patterns
...

apropos line
proc_pid_cmdline (5) - command line
HEAD (1)             - Simple command line user agent
...
```

- `whatis {palavra}` - Resumo de uma linha sobre a palavra pesquisada (apenas resultado direto).

```
whatis grep
grep (1)             - print lines that match patterns

whatis line
line: nothing appropriate.
```

> O número ao lado do nome retornado no `apropos` e `whatis` corresponde à seção do manual no sistema.

- `man {hierarquia} {palavra}` - Manual completo da palavra (apenas resultado direto do comando). A hierarquia pode ser ocultada, mas pode mostrar resultados diferentes do esperado caso a palavra apareça em múltiplas hierarquias (escolhe a primeira referenciada).

- `info {palavra}` - Manual organizado em tópicos navegáveis; pode ser pensado como um `man` ampliado para programas que oferecem documentação nesse formato, sem ser necessariamente mais atualizado.

- `{palavra} --help` - Manual intermediário sobre a palavra pesquisada (apenas resultado direto).

- `help {comando}` - Consulta a ajuda dos comandos internos do Bash, como `cd` e `read`.

## Comandos Básicos:

### Informações sobre o Sistema:

> Para obter informações mais aprofundadas, use as ferramentas de consulta sobre comandos.

- `who {opções}` - Lista os usuários logados.

- `pwd {opções}` - Mostra o diretório de trabalho atual.

- `history {opções}` - Mostra os comandos executados recentemente.

- `ls {opções} {diretório}` - Lista o conteúdo de um diretório.

- `tree {opções} {diretório}` - Mostra o conteúdo de um diretório em um formato de árvore hierárquica.

- `find {diretório inicial} {opções} {critérios}` - Procura arquivos e diretórios por nome, permissões e outros critérios.

- `locate {opções} {critérios}` - Busca rápida de caminhos em uma base de dados previamente construída, funcionando como uma alternativa ao `find` para procurar nomes. Não utiliza um diretório inicial.

> A base pode estar desatualizada. Sua atualização é feita por `updatedb` (normalmente com `sudo`) ou por uma tarefa agendada do sistema.

- `whereis {opções} {comando/arquivo}` - Mostra a localização de comandos, páginas de manual e binários.

- `which {opções} {comando}` - Localiza executáveis nos diretórios de `$PATH`, enquanto `whereis` também procura outros arquivos relacionados, como manuais.

- `type {comando}` - Mostra como o Bash interpreta um nome: comando interno, função, *alias* ou programa externo. `command -v {comando}` fornece uma identificação curta, útil em *scripts*.

- `file {opções} {arquivo}` - Mostra o tipo do arquivo especificado.

- `stat {opções} {arquivo}` - Mostra informações detalhadas sobre arquivos e diretórios.

- `date {opções} {data}` - Mostra ou define o relógio do sistema.

- `cal {opções} {data}` - Mostra um calendário em linha de comando.

### Ações no Terminal:

- `clear` - Limpa o conteúdo da tela atual.

- `exit {status}` - Encerra a *shell* com o *status* informado (opcional). Em uma sessão interativa, isso normalmente também fecha o terminal associado.

- `logout` - Encerra uma *shell* de *login*; não encerra todas as sessões do usuário no sistema.

## Automatização de Processos:

A *shell* pode ser utilizada diretamente no terminal, um comando por vez, ou de maneira automatizada por meio de *scripts*, o que permite maior velocidade, versatilidade e tarefas mais complexas. Para criar o *script*, é necessário:

1. Criar um arquivo e inserir a sequência de comandos desejada. Na primeira linha, indicar o interpretador com `#!/caminho/interpretador` (por exemplo, `#!/bin/bash`).
2. Dar permissão de execução com `chmod +x script.sh` e executá-lo por `./script.sh`. O `./` informa o caminho, pois o diretório atual normalmente não pertence a `$PATH`.

> O `#!` (*shebang*) lembra o `#include` de C na ideia de indicar algo externo necessário ao código. A diferença é que ele aponta para o programa que interpretará o arquivo, sem incluir código ou bibliotecas.

Também é possível executar um *script* escolhendo o interpretador diretamente no comando (como `bash script.sh`). Nesse caso a execução não exige permissão de execução nem depende do `#!` (o arquivo ainda precisa ser legível).

---

# 1. Processamento de Texto

## Editores de Texto no Terminal:

> As principais diferenças estão no número de ferramentas presentes no editor e na forma como editam.

- `nano {opções} {arquivo}` - Editor simples, com os principais atalhos visíveis na tela.

- `vim {opções} {arquivo}` - Editor mais completo, com modos separados para inserir texto e executar comandos.

- `emacs {opções} {arquivo}` - Editor robusto e extensível, com ferramentas integradas para edição e programação.

> `nano` e `pico`, assim como `vim` e `vi`, são relacionados, mas não necessariamente o mesmo programa. Algumas distribuições usam *aliases* ou *links* para que um nome execute a implementação do outro.

## Visualização de Textos:

- `echo {argumentos}` - Imprime os argumentos como texto, normalmente seguidos por uma quebra de linha.

- `printf {formato} {argumentos}` - Imprime conforme um formato: em `printf '%s\n' "$texto"`, `%s` recebe o texto e `\n` representa a quebra de linha (sintaxe quase idêntica à do C).

- `cat {opções} {arquivos}` - Concatena arquivos e imprime seu conteúdo. Se usado com um único arquivo, apenas o imprime.

> O comando `tac` inverte a ordem das linhas de cada arquivo, mantendo a ordem dos arquivos recebidos.

- `zcat {opções} {arquivo}` - Lê o conteúdo de um arquivo compactado com `gzip`. Existem variações a depender do tipo de compressão.

- `less {opções} {arquivo}` - Mostra a saída de um comando ou arquivo de texto e permite navegar para trás e para frente.

> O comando `more` faz basicamente a mesma coisa, mas oferece navegação mais limitada, principalmente ao receber dados de outro comando.

- `head {opções} {arquivo}` - Mostra as primeiras linhas de um arquivo (dez por padrão; `-n` altera a quantidade).

- `tail {opções} {arquivo}` - Mostra as últimas linhas de um arquivo (dez por padrão; `-n` altera a quantidade).

## Extrair Dados de Textos:

- `strings {opções} {arquivo}` - Extrai caracteres legíveis de arquivos (pode extrair dados de binários como `.jpg`, `.mp3`, etc.).

- `wc {opções} {arquivo}` - Conta o número de linhas, palavras, caracteres, etc. (depende da opção escolhida) de um arquivo.

- `grep {opções} {padrão} {arquivo}` - Filtra as linhas que correspondem a um padrão (por padrão, uma expressão regular). Pode ler arquivos ou a entrada padrão. `-i` ignora diferenças entre maiúsculas e minúsculas, `-n` mostra o número da linha, `-v` seleciona as que não correspondem e `-F` trata o padrão como texto literal.

```bash
grep -in "erro" registro.txt   # Linhas com "erro", "ERRO" etc., indicando a posição.
grep -F "[OK]" registro.txt    # Procura o texto literal [OK].
```

- `cut {opções} {arquivo}` - Extrai partes de cada linha, como campos delimitados (`cut -d ":" -f 1 /etc/passwd` extrai os nomes dos usuários).

- `diff {opções} {arquivo_1} {arquivo_2}` - Compara o conteúdo de arquivos.

## Operações sobre Textos:

- `sort {opções} {arquivo}` - Ordena o conteúdo de um fluxo de dados de entrada ou arquivo.

- `uniq {opções} {arquivo}` - Remove linhas repetidas **adjacentes**. É comum usá-lo após `sort`; com `-c`, também conta as repetições.

- `tr {opções} {conjunto_1} {conjunto_2}` - Substitui ou remove caracteres da entrada padrão. Por exemplo, `tr '[:lower:]' '[:upper:]' < arquivo.txt` converte letras minúsculas em maiúsculas.

- `tee {opções} {arquivos}` - Mostra a saída de um comando e a escreve em um ou mais arquivos.

> Os seguintes comandos possuem maior complexidade (este é apenas um uso básico):

- `sed {opções} {expressão} {arquivo}` - Imprime o conteúdo de um arquivo (como o `cat`) com a opção de substituir palavras.

- `awk {opções} {programa} {arquivo}` - Formata o resultado de um comando.

# 2. Sistema de Arquivos

Antes de qualquer coisa, é importante entender como a *shell* identifica o caminho para um arquivo (e/ou diretório):

- `/caminho/absoluto/arquivo` - Iniciar o caminho com uma barra (`/`) indica que está sendo usado o caminho absoluto, a partir da raiz do sistema.
- `~/caminho_de_user/arquivo` - Iniciar o caminho com `~/` indica que está sendo usado o caminho a partir do diretório pessoal do usuário atual (**também é considerado um caminho absoluto**). Para usar o diretório de outro usuário sem informar o caminho absoluto, pode-se escrever `~user_alvo/caminho/arquivo`.
- `caminho/relativo/arquivo` - O sistema considera o diretório atual e interpreta o caminho a partir dele.

Além disso, `.` e `..` funcionam como atalhos para o próprio diretório e para seu diretório pai. Podem ser imaginados como *aliases*, embora sejam componentes especiais dos caminhos, e não *aliases* definidos na *shell*. Dessa maneira, estando no diretório `C/`, cujo caminho absoluto é `/A/B/C`, os comandos `cd D` e `cd ./D` produzem o mesmo resultado para chegar a `D/`. De maneira semelhante, para chegar a `B/`, tanto `cd /A/B` quanto `cd ..` produzem o mesmo resultado.

Em sistemas de arquivos *Linux* comuns, nomes normalmente diferenciam letras maiúsculas e minúsculas (*case-sensitive*), portanto `Arquivo.txt` e `arquivo.txt` podem representar arquivos diferentes. Além disso, arquivos e diretórios cujo nome começa com `.` são convencionalmente tratados como **ocultos** e, por padrão, não são exibidos pelo `ls` (podem ser mostrados com `ls -a`).

## Estrutura de Pastas do *Linux*:

> A estrutura e a função das pastas podem variar conforme a distribuição. Os *links* mostrados abaixo ilustram uma organização com diretórios integrados a `/usr`.

```text
/                         # raiz: ancestral de todos os arquivos do sistema
├── bin -> usr/bin        # comandos binários essenciais p/ inicializar e executar o sistema
├── boot                  # arquivos necessários para inicializar o sistema
├── dev                   # arquivos de dispositivos (discos, terminais, impressoras etc.)
├── etc                   # configurações locais do sistema (ex: /etc/passwd = lista de usuários)
│   ├── opt               # configs de pacotes opcionais instalados em /opt
│   ├── X11               # configs do sistema de janelas X
├── home                  # diretórios pessoais dos usuários (ex: /home/zach)
│   └── user              # diretório pessoal de um usuário específico
├── lib -> usr/lib        # bibliotecas compartilhadas
│   └── modules           # módulos do kernel carregáveis
├── mnt                   # pontos de montagem p/ sistemas de arquivos temporários
├── opt                   # pacotes de software opcionais
├── proc                  # sistema de arquivos virtual c/ infos do kernel e processos
├── root                  # diretório pessoal do administrador (não é o mesmo que /)
├── run                   # dados voláteis de execução, recriados a cada inicialização
├── sbin -> usr/sbin      # binários essenciais de administração do sistema
├── sys                   # pseudo sistema de arquivos p/ dispositivos
├── tmp                   # arquivos temporários
├── usr                   # programas e dados compartilháveis, normalmente estáticos
│   ├── bin               # maioria dos comandos de usuário
│   ├── games             # jogos e programas educacionais
│   ├── include           # cabeçalhos (headers) usados por programas C
│   ├── lib               # bibliotecas
│   ├── local             # hierarquia local: bin, games, lib, share, src etc.
│   ├── sbin -> bin       # comandos de administração; pode estar integrado a bin
│   └── share             # dados independentes de arquitetura
│       ├── doc           # documentação
│       ├── info          # diretório principal do sistema GNU info
│       └── man           # manuais online
└── var                   # dados variáveis: mudam durante a execução do sistema
    ├── log               # arquivos de logs (lastlog, messages, wtmp etc.)
    └── spool             # dados de aplicação spool (cron, at, lpd etc.)
```

## Usuários:

O *Linux* é um sistema multiusuário no qual os direitos de acesso são definidos para:

- `u` (*user*): Dono do arquivo.
- `g` (*group*): Grupo associado ao arquivo, reunindo usuários que compartilham permissões. Permite dar acesso a várias pessoas específicas a um arquivo ou diretório **sem precisar liberá-lo para o restante do sistema** (*others*).
- `o` (*others*): Demais usuários, que não são o dono nem pertencem ao grupo associado ao arquivo.

As classes não são somadas: no controle tradicional de permissões, verifica-se primeiro se o usuário é o dono; caso contrário, se pertence ao grupo; por fim, aplicam-se as permissões de outros.

Ao se pesquisar informações detalhadas acerca de um arquivo específico (seja com o `stat`, com o `ls -l`, etc.), é possível ver o dono do arquivo e o grupo.

## Informações:

- `id {opções} {usuário}` - Mostra informações de identidade do usuário. Se nenhum usuário for informado, considera o usuário atual.

- `basename {opções} {caminho/arquivo_ou_dir}` - Retorna apenas `arquivo_ou_dir`.

- `dirname {opções} {caminho/arquivo_ou_dir}` - Retorna `caminho`.

- `lsof {opções}` - Lista os arquivos abertos.

- `fuser {opções} {arquivo}` - Identifica os processos que utilizam um arquivo ou sistema de arquivos.

## Operações sobre Arquivos e Diretórios:

> Usar esses comandos com caminho absoluto (`/` e `~/` expandido pela *shell* para o diretório pessoal) e com caminhos relativos pode gerar resultados diferentes.

- `cd {opções} {caminho/diretório}` - Muda o diretório de trabalho atual.

- `mkdir {opções} {caminho/nome}` - Cria um diretório no caminho especificado.

- `rmdir {opções} {caminho/nome}` - Apaga um diretório vazio; se ele contiver elementos, não será apagado.

- `rm {opções} {caminho/nome}` - Apaga um arquivo ou diretório. É muito usado com a *flag* `-r` (*recursive*), com a qual apaga todos os arquivos de um diretório e o próprio diretório.

- `mv {opções} {origem/nome_antigo} {destino/nome_novo}` - Move ou renomeia um arquivo ou diretório. Se o destino for um diretório existente, mantém o nome e move o item para dentro dele; caso contrário, o destino indica o novo caminho e nome.

- `cp {opções} {origem/nome_antigo} {destino/nome_novo}` - Copia um arquivo, interpretando o destino da mesma maneira que o `mv`. Para copiar diretórios, é necessário usá-lo com a *flag* `-r`, que copia recursivamente os arquivos e subdiretórios da pasta-alvo.

- `ln {opções} {origem/nome_arquivo} {destino/nome_link}` - Cria *links* (caminhos alternativos para acessar um dado).

    - `ln` (*hard link*): funciona como um novo **ponteiro independente para o dado do arquivo original** (funciona mesmo se o arquivo original for apagado). **Não é usado com diretórios e exige o mesmo sistema de arquivos**.

      > Nas informações do arquivo (acessadas pelo `ls -l`, por exemplo), é possível ver se existem outros *hard links* para aquele espaço no disco e quantos (por padrão, é apenas 1).
 
    - `ln -s` (*link* simbólico ou *soft link*): funciona como um **ponteiro para o arquivo ou diretório original** (é ligado ao seu nome). Dessa maneira, o *link* simbólico deixa de funcionar se o arquivo original for apagado, mas volta a funcionar se um arquivo com o mesmo nome e caminho for criado no lugar. **Funciona com diretórios e pode atravessar sistemas de arquivos**.

      > Em suas informações, ele indica o caminho para o qual aponta (`nome_link -> /caminho/arquivo`).

- `gzip {opções} {arquivo}` - Comprime arquivos.

- `gunzip {opções} {arquivo}` - Descomprime arquivos.

- `tar {opções} {arquivos}` - Agrupa múltiplos arquivos/diretórios em um único arquivo.

- `split {opções} {arquivo}` - Separa um arquivo em pedaços menores.

- `shred {opções} {arquivo}` - Sobrescreve os dados do arquivo para dificultar sua recuperação; `-u` também remove o arquivo. A eficácia depende do armazenamento: SSDs, cópias e sistemas que gravam dados em novos locais podem preservar versões anteriores.

## Permissões:

Cada arquivo ou diretório pode ter as seguintes permissões:

- `r` (*read*): Permite que **o arquivo seja aberto e lido**. Para diretórios, que **o conteúdo seja listado**.
- `w` (*write*): Permite que **o arquivo seja escrito ou truncado**. Para diretórios, permite **criar, remover e renomear entradas**, normalmente em conjunto com `x`.
- `x` (*execute*): Permite que **o arquivo seja tratado como um programa executável**. Para diretórios, permite **acessá-los diretamente e suas sub-pastas** (como ao usar `cd` ou abrir um arquivo pelo caminho).

**Formato de Permissão:** `-rwxrw-r--`

O primeiro caractere (`-` no exemplo acima) representa o tipo de arquivo. Os principais são:

- `-` - Arquivos comuns;
- `d` - Diretórios;
- `l` - *Links* simbólicos;
- `c` (*character device*) - Dispositivos de caractere, geralmente acessados como fluxos de dados, como terminais;
- `b` (*block device*) - Dispositivos de bloco, como discos, que permitem acesso a regiões de armazenamento;
- `p` e `s` - Respectivamente, *pipe* nomeado e *socket*.

> De maneira simplificada, o sistema representa dispositivos por arquivos especiais. Isso permite usar operações de leitura e escrita para se comunicar com eles, embora cada dispositivo tenha comportamentos próprios.

Nas demais posições, os elementos, agrupados de três em três, representam as permissões do **dono**, do **grupo** e dos **demais usuários** (`-` indica a ausência daquela permissão).

- `chown {opções} {dono}:{grupo} {arquivo/diretório}` - Altera o dono e/ou grupo associado a um arquivo ou diretório.

- `chgrp {opções} {grupo} {arquivo/diretório}` - Altera apenas o grupo associado a um arquivo ou diretório.

- `chmod {opções} {permissões} {arquivo/diretório}` - Altera as permissões de acesso.

> Apenas o dono do arquivo ou o superusuário podem alterar o modo (permissão).

Suporta duas formas para especificar mudanças:

- **Representação Octal:** Usa três dígitos octais para definir as permissões absolutas do dono, do grupo e dos demais usuários, respectivamente. As permissões são calculadas pela soma de `r`=4, `w`=2 e `x`=1 (`chmod 751 arquivo` resulta em `-rwxr-x--x` para um arquivo comum).
- **Representação Simbólica:** Usa as letras `u` (dono), `g` (grupo), `o` (outros), `a` (todos) acompanhadas das operações `+` (adiciona), `-` (remove) ou `=` (define especificamente) (`chmod u+x arquivo` adiciona execução ao dono, por exemplo).

> Ao omitir a classe (`u`, `g`, `o` ou `a`), o comando considera todas, mas respeita a restrição de `umask`; por isso, `chmod +x` e `chmod a+x` nem sempre são equivalentes.

### Permissões Especiais:

Na representação octal, pode ser acrescentado um quarto dígito à esquerda para definir permissões especiais:

- **SUID** (*Set User ID*, octal `4nnn`): Ao executar o arquivo, o processo assume o identificador efetivo do dono (`-rwsr-xr-x`).
- **SGID** (*Set Group ID*, octal `2nnn`): Ao executar o arquivo, o processo assume o grupo efetivo do arquivo (`-rwxr-sr-x`). Em diretórios, novos arquivos recebem o grupo do diretório.
- ***Sticky bit*** (octal `1nnn`): Em diretórios, restringe remoção e renomeação ao dono do arquivo, dono do diretório ou superusuário (`drwxrwxrwt`).

As letras `s` e `t` ocupam a posição de uma permissão de execução. Se essa permissão não estiver habilitada, aparecem em maiúsculas (`S` e `T`). No *Linux*, SUID e SGID não produzem esse efeito de execução em *scripts* interpretados.

- `umask {opções} {máscara}` - Define os *bits* removidos das permissões solicitadas ao criar arquivos e diretórios. A máscara é herdada pelos processos filhos, mas não altera arquivos existentes.

Com `umask 022`, uma criação que solicita `666` resulta em `644` (`rw-r--r--`); para um diretório que solicita `777`, resulta em `755` (`rwxr-xr-x`). A máscara remove permissões, sem acrescentar execução a arquivos que não a solicitaram.

---

# 3. Processos

Todo programa em execução no *Linux* é tratado pelo *kernel* como um **processo**. Como os sistemas operacionais modernos são multitarefa (*multitasking*), o *kernel* alterna rapidamente qual processo tem acesso à CPU (*Central Processing Unit*), criando a impressão de simultaneidade mesmo em um único núcleo. Com vários núcleos, também pode haver execução realmente paralela.

A própria *shell* também é um **processo**. Em uma execução comum a partir de uma *shell* interativa, quando ela inicia um programa externo, esse programa normalmente passa a executar em outro processo, relacionado à *shell* como seu processo filho. Já comandos internos (*builtins*) podem ser executados diretamente pelo próprio processo da *shell*.

## Como Funcionam Processos:

Um programa pode lançar (executar) outros programas, formando uma relação de parentesco entre processos:

```text
processo_pai
 └── processo_filho
```

- Um **processo pai** (*parent process*) é aquele que dá origem a um **processo filho** (*child process*).
- Cada processo recebe um PID (*process ID*), que pode ser reutilizado depois que o processo termina, além de manter informações como memória alocada, estado de execução e dono/usuário associado (assim como arquivos).
- Muitos serviços do sistema são executados como ***daemons***: processos em segundo plano, sem interface de usuário.

> Conceitualmente, o PID 0 pode ser associado ao lado do *kernel* nessa árvore: corresponde à tarefa inicial, ligada à tarefa ociosa (*swapper/idle*), e não ao *kernel* inteiro como um programa comum. Dela se origina o PID 1 (`init`, frequentemente implementado por `systemd`), responsável pela inicialização dos serviços.

Os processos podem ter diferentes estados indicando como está sua execução, com os principais sendo:

- `R` (*running/runnable*) - Executando ou pronto para executar;
- `S` (*sleeping*) - Esperando algum evento, em uma espera interrompível;
- `D` - Espera não interrompível, frequentemente associada a operações de entrada e saída;
- `T` (*stopped*) - O processo foi instruído a parar;
- `Z` (*zombie*) - Processo filho finalizado mas não coletado pelo pai;

> `<` (*high priority*) não é diretamente um estado, mas um indicador de prioridade elevada. Sua influência depende da política de escalonamento, não de uma quantidade fixa de tempo de CPU.

### Informações sobre os Processos:

O *Linux* expõe informações sobre os processos em execução por meio do sistema de arquivos virtual `/proc`. Cada processo em execução possui uma pasta própria dentro de `/proc`, identificada pelo seu PID (`/proc/{PID}`). É dentro dela que ficam, entre outras coisas, os descritores de arquivo do processo (`/proc/{PID}/fd`), consultáveis com `ls -l`.

## Listagem:

- `ps {opções}` - Mostra uma foto **instantânea** dos processos.

    - O comando `ps x` exibe todos os processos do usuário, adicionando a coluna `STAT` (estado do processo).
    - Já o `ps aux` exibe os processos de **todos os usuários**, com colunas extras de uso de CPU e memória.

    > Opções sem traço (`x`, `aux`) invocam o `ps` no estilo BSD, que o *Linux* consegue emular.

- `pstree {opções}` - Mostra os processos em formato de árvore, evidenciando a relação de pai/filho.

- `top {opções}` - Visão **dinâmica** e continuamente atualizada dos processos, ordenados por atividade. Também permite navegação e filtragem de informações em tempo de execução.

> O `btop` é uma alternativa ao `top` com interface mais visual: reúne gráficos de CPU, memória, discos e rede, além de oferecer suporte ao mouse, visualização de processos em árvore e filtros interativos.

- `jobs {opções}` - Mostra os trabalhos ativos que **aquela *shell* lançou**.

> **Um *job* pode ser composto por mais de um processo**. Ao executar `sort arquivo.txt | uniq &`, isso é um *job* (aparece como `[1]` no `jobs`), mas são dois processos (`sort` e `uniq`, cada um com seu próprio PID).

- `vmstat {opções} {atraso}` - Mostra a utilização instantânea do sistema (memória, *swap*, disco); com um tempo em segundos, atualiza continuamente.

- `xload {opções}` - Gráfico da carga do sistema ao longo do tempo (interface gráfica).

- `tload {opções}` - Semelhante ao `xload`, mas desenhado no terminal.

## Controlando Processos:

Na sessão interativa, os programas podem executar em dois modos: **primeiro plano** (*foreground*, controlando o terminal) ou **segundo plano** (*background*, liberando o *prompt*). Para já iniciar em plano de fundo, basta seguir o comando com `&`:

```console
$ xlogo &
[1] 3486
```

- `fg {%N}` - Traz um *job* ao primeiro plano (*foreground*), identificado pelo número do *job*, `%N` (opcional se houver apenas um).

- `bg {%N}` - Retoma um *job* parado em segundo plano (*background*).

> Esse comando só funciona no terminal de origem do *job*, exigindo que ele esteja parado antes (o processo é retomado, e vai para o plano de fundo).

### Sinais:

- `kill {opções} {-sinal} {PID ou %job}` - Envia um sinal a um processo (identificado por seu PID ou pelo número do *job* `%N`). Sem sinal especificado, envia `TERM` por padrão, pedindo para o programa terminar.

O `kill` não "mata" processos diretamente; ele envia **sinais**, notificações que o sistema e outros processos podem entregar a um programa. A lista pode ser consultada com `kill -l`, e seus significados, em `man 7 signal`.

- `Ctrl+C` envia `SIGINT` ao grupo de processos em primeiro plano, normalmente interrompendo sua execução.
- `Ctrl+Z` envia `SIGTSTP`, normalmente suspendendo esse grupo (marcado como *stopped* em `jobs`).

Um programa pode tratar ou ignorar vários sinais, mas não `SIGKILL` (`kill -KILL PID`), que força o encerramento, nem `SIGSTOP` (`kill -STOP PID`), que força a suspensão. Encerramento e suspensão são ações diferentes.

> Um programa também pode implementar seus próprios comandos de teclado: `q`, por exemplo, pode simular uma ação de saída. Nesse caso, ele interpreta uma entrada; não cria um novo sinal do sistema.

- `killall {opções} {-sinal} {nome}` - Mesma ideia, mas envia o sinal a todos os processos que correspondam a um **nome** de programa.

- `nohup {comando}` - Executa um comando ignorando `SIGHUP`, ajudando a mantê-lo após o fechamento do terminal. Não o coloca em segundo plano sozinho; para isso, usa-se `nohup comando &`.

- `wait {PID ou %job}` - Aguarda o término de um processo filho ou trabalho e retorna seu *status*. Sem argumento, aguarda todos os trabalhos em segundo plano da *shell*.

---

# 4. Entradas, Saídas e Redirecionamentos

## Entrada e Saída Padrão:

Os programas normalmente utilizam três canais para lidar com dados: **entrada padrão** (*stdin*, descritor **0**), **saída padrão** (*stdout*, descritor **1**) e **saída de erro padrão** (*stderr*, descritor **2**). Em uma sessão interativa, eles costumam estar ligados ao terminal: a entrada recebe o que é digitado, enquanto as duas saídas mostram texto na tela.

Esses destinos podem ser alterados individualmente. Por exemplo, um programa pode ler de um arquivo, escrever o resultado em outro e continuar mostrando os erros no terminal.

### TTY (Terminal):

**TTY** (*teletype*) é o nome dado ao dispositivo que representa um terminal no sistema. Historicamente se referia a terminais físicos; ao abrir um terminal em uma interface gráfica, normalmente é criado um **pseudoterminal**, cujo lado conectado à *shell* aparece em `/dev/pts/{N}` no *Linux*.

Os descritores podem ser imaginados como **apontadores** para os destinos de entrada e saída, mas não são *links* do sistema de arquivos. Cada processo possui sua própria tabela de descritores, que pode conter outros além de `0`, `1` e `2` (arquivos abertos, *pipes*, etc.). Redirecionar um deles não redireciona automaticamente os de outro processo.

## Redirecionamento para Arquivos:

Nos operadores abaixo, o número do descritor deve ficar junto do operador. Se omitido, utiliza-se `0` para entrada e `1` para saída.

- `{comando} {n}< {arquivo}` - Faz o descritor `n` ler do arquivo. Por exemplo, `sort < nomes.txt` recebe o conteúdo de `nomes.txt` pela entrada padrão.
- `{comando} {n}> {arquivo}` - Direciona o descritor `n` para o arquivo, criando-o ou truncando seu conteúdo anterior. Por exemplo, `comando 2> erros.txt` salva apenas os erros.
- `{comando} {n}>> {arquivo}` - Acrescenta a saída ao final do arquivo, criando-o se necessário.
- `{comando} &> {arquivo}` - Direciona tanto a saída padrão quanto a saída de erro para o mesmo arquivo.

O redirecionamento é preparado **antes da execução do comando**. Assim, `cat arquivo > arquivo` esvazia o arquivo antes que `cat` possa lê-lo.

> `set -o noclobber` ativa a proteção contra sobrescrita com `>` na *shell* atual; `set +o noclobber` a desativa. O operador `>|` permite forçar a sobrescrita mesmo com essa proteção.

> `/dev/null` descarta os dados escritos nele. Por exemplo, `comando > /dev/null` descarta a saída padrão, enquanto `comando 2> /dev/null` descarta os erros.

## Duplicação de Descritores:

- `{comando} {n}>&{alvo}` - Faz o descritor `n` (por padrão, `1`) usar o mesmo destino que `alvo` possui naquele momento. `2>&1`, por exemplo, direciona os erros para o destino atual da saída padrão.

A ordem dos redirecionamentos importa, pois são processados da esquerda para a direita. Supondo que o arquivo `y` exista e o `x` não:

```bash
cat x y > output.txt 2>&1  # 1 passa a ser output.txt; 2 recebe o mesmo destino de 1 (output.txt).
cat x y 2>&1 > output.txt  # 2 recebe o destino atual de 1 (terminal); depois, só 1 vai para output.txt.
```

## Comunicação e Entrada em Bloco:

- `{comando_1} | {comando_2}` - ***Pipes***. Conecta a saída padrão do primeiro comando à entrada padrão do segundo (evitando a obrigatoriedade de criação de arquivos intermediários). `sort nomes.txt | uniq`, por exemplo, ordena as linhas e remove repetições adjacentes. A saída de erro mantém seu destino original, a menos que seja redirecionada.

> Por padrão, o *status* dessa sequência (*pipeline*) é o do último comando. Com `set -o pipefail`, passa a ser o do comando mais à direita que falhou, ou `0` se todos tiveram sucesso; `set +o pipefail` restaura o comportamento padrão.

- `{comando} << {delimitador}` - Envia as linhas seguintes como entrada até encontrar uma linha contendo apenas o delimitador (normalmente `EOF` ou `END`). Esse bloco é chamado de *here-document* e dispensa uma entrada interativa encerrada com `Ctrl+D`.

```bash
cat << EOF
> Diretório pessoal: $HOME
> Resultado: $((2 + 3))
> EOF
```

Sem proteção no delimitador, o bloco permite expansão de parâmetros, substituição de comandos e expansão aritmética. Com o delimitador protegido, como em `<< 'EOF'`, o conteúdo é literal; a linha final continua sendo apenas `EOF`, sem aspas. Não ocorre expansão de nomes de arquivos nesse texto.

> `<<-` realiza a mesma função, mas remove tabulações do início das linhas do bloco e do delimitador final (não remove espaços comuns).

---

# 5. Expansões

Antes de executar um comando, a *shell* reconhece a estrutura da linha e realiza **expansões**, substituindo certas expressões por valores, resultados ou nomes de arquivos. Só depois entrega os argumentos já “mastigados” ao programa.

As expansões seguem uma ordem: 

1. **Chaves;** 
2. **Til;**
3. **Parâmetros;**
4. **Aritmética e Comandos;** 
5. **Separação de Palavras;**
6. **Nomes de Arquivos;** 
 
Ao final, as aspas usadas para proteger a sintaxe são removidas. 

> Nem todo contexto aplica todas essas etapas (aspas e atribuições, por exemplo, alteram esse comportamento).

## Tipos de Expansão:

### Expansão de Arquivos:

Nomes de arquivos podem ser representados por **caracteres curinga**. A *shell* procura nomes existentes que correspondam ao padrão e o substitui pelos resultados antes da execução do comando.

- `?` - Representa um único caractere obrigatório. `ls doc?.txt` encontra nomes como `doc1.txt` e `docA.txt`, mas não `doc12.txt`.
- `*` - Representa qualquer sequência de caracteres, inclusive vazia. `rm *.tmp` apaga arquivos terminados em `.tmp`; `cat memo*` lê nomes iniciados por `memo`; `echo *__*` lista todos os arquivos que possuem os caracteres `__` em alguma parte do seu nome (inicio, meio e/ou fim).
- `[caracteres]` - Representa **um caractere do conjunto**. `ls relatorio_[123].txt` encontra os relatórios 1, 2 e 3; `[a-d]` indica uma faixa.
- `[!caracteres]` ou `[^caracteres]` - Representa um caractere que não pertence ao conjunto. `ls arquivo_v[^3].txt` ignora `arquivo_v3.txt`, mas pode encontrar `arquivo_v1.txt` e `arquivo_vA.txt`.
- `[[:classe:]]` - Representa um caractere de uma classe predefinida. `ls doc_[[:digit:]].txt` encontra `doc_1.txt`, mas não `doc_A.txt`.

| Classe | Caracteres Correspondentes |
| --- | --- |
| `alnum` / `alpha` | Letras e dígitos / apenas letras. |
| `digit` / `xdigit` | Dígitos decimais / hexadecimais (`0`–`9`, `A`–`F`, `a`–`f`). |
| `lower` / `upper` | Letras minúsculas / maiúsculas. |
| `blank` / `space` | Espaço e tabulação / caracteres de espaçamento, incluindo quebra de linha. |
| `cntrl` | Caracteres de controle. |
| `graph` / `print` | Caracteres visíveis, sem espaço / imprimíveis, incluindo espaço. |
| `punct` | Caracteres de pontuação. |

Por padrão, os curingas não incluem o ponto inicial de nomes ocultos: ele precisa aparecer explicitamente, como em `.*`. Se nenhum nome corresponder, o padrão permanece literal e é passado assim ao comando. Classes e faixas de letras podem depender da configuração de idioma e região.

> Esses padrões (*globs*) não são expressões regulares. Em um padrão de arquivo, `*` representa qualquer sequência; em uma expressão regular, como as usadas por `grep`, ele repete o elemento anterior.

### Expansão de Til (`~`):

O til funciona como um atalho para diretórios pessoais: `~` expande para o diretório do usuário atual, enquanto `~usuario` indica o de outro usuário existente. Ele deve aparecer em uma posição reconhecida pela *shell*, normalmente no início da palavra: `~/Documentos`. Entre aspas (`"~/Documentos"`), permanece literal.

### Expansão Aritmética:

Executa contas com **números inteiros** e insere o resultado no comando, usando `$((expressão))`. São suportadas adição (`+`), subtração (`-`), multiplicação (`*`), divisão (`/`), resto (`%`) e exponenciação (`**`), além de operadores de comparação e lógicos.

> Espaços internos são ignorados, e parênteses podem organizar as operações: `$(((5 ** 2)   * 3))` resulta em `75`. A divisão inteira descarta a parte fracionária: `$((5 / 2))` resulta em `2`.

### Expansão de Chaves:

Gera múltiplas palavras a partir de um padrão entre chaves. Diferentemente dos curingas, **não exige arquivos existentes**.

- **Valores específicos:** `mkdir pasta_{txt,pdf,img}` cria três pastas: `pasta_txt`, `pasta_pdf` e `pasta_img`.
- **Faixa de valores:** `touch teste_v{1..5}` gera nomes de `teste_v1` a `teste_v5`. São usados dois pontos finais (`..`), não um hífen.

É possível aninhar chaves para combinar padrões. Espaços dentro dos elementos precisam ser protegidos: `{"A A",B}` gera duas palavras, enquanto `{A, B}` não forma a expansão pretendida.

> Como as chaves são expandidas antes das variáveis, `{1..$n}` não gera uma sequência usando o valor de `n`.

### Expansão de Parâmetros:

Permite recuperar valores armazenados na *shell*. A forma `${variavel}` delimita explicitamente o nome; `$variavel` é sua forma simplificada, suficiente quando o nome pode ser identificado sem ambiguidade. `${USER}`, por exemplo, recupera o nome de usuário registrado nessa variável.

As chaves permitem separar o nome de textos adjacentes e aplicar operações adicionais, apresentadas no capítulo de variáveis. Elas **não substituem as aspas** para preservar espaços: `${variavel}` e `"${variavel}"` podem produzir argumentos diferentes.

> `printenv` lista variáveis de ambiente. No Bash, `set` sem argumentos também mostra variáveis da própria *shell* e definições de funções; portanto, as duas consultas não são equivalentes.

### Substituição de Comandos:

A forma `$(comando)` captura a **saída padrão** de um comando e a utiliza como texto. As quebras de linha finais são removidas; mensagens enviadas à saída de erro não são capturadas, salvo se forem redirecionadas.

Por exemplo, `printf 'Data: %s\n' "$(date +%F)"` executa `date`, captura uma data como `2026-10-13` e a entrega como argumento a `printf`. As aspas preservam o resultado como um único argumento.

## Separação de Palavras:

Quando expansões de parâmetros ou comandos aparecem sem aspas, seus resultados podem ser separados em palavras (*word splitting*) pelos caracteres de `IFS` (por padrão, espaço, tabulação e quebra de linha). Depois disso, os padrões de arquivos também podem ser expandidos.

```bash
texto="duas palavras"
printf '<%s>\n' $texto    # Imprime <duas> e <palavras>, em linhas separadas.
printf '<%s>\n' "$texto"  # Imprime <duas palavras>, em uma única linha.
```

Em argumentos de comandos, `"$variavel"` preserva o valor como um argumento, inclusive quando está vazio. Sem aspas, um valor vazio pode desaparecer da lista de argumentos. Nas atribuições simples (`copia=$texto`), não ocorre essa separação nem expansão de arquivos.

## Aspas e *Escapes*:

Aspas e *escapes* controlam quais caracteres conservam seu significado especial e quais devem ser tratados literalmente.

- **Aspas Duplas (`"..."`):** Preservam espaços e impedem a separação de palavras e a expansão de arquivos. Continuam permitindo expansões como `$variavel`, `$(comando)` e `$((expressão))`. `echo "O usuário $USER acessou o arquivo"` mantém o texto unido e substitui a variável.
- **Aspas Simples (`'...'`):** Preservam literalmente tudo entre elas. `echo 'O usuário $USER'` imprime o próprio texto `$USER`; uma aspa simples não pode ser inserida diretamente dentro desse mesmo par de aspas.
- **Barra Invertida (`\`):** Protege o próximo caractere fora de aspas simples. Dentro de aspas duplas, atua apenas em casos específicos, como `\$`, `\"`, `\\`, crase e quebra de linha. `echo "O saldo é \$5.00"` imprime o cifrão literalmente.

> Uma barra invertida imediatamente antes de uma quebra de linha permite continuar o comando na linha seguinte, fora de aspas simples. Já sequências como `\n` dependem de quem as interpreta: `printf 'Linha 1\nLinha 2\n'` produz duas linhas. No Bash, `echo -e` também interpreta essas sequências, mas o comportamento de `echo` varia com opções e implementação.

---

# 6. Variáveis e Parâmetros

Variáveis associam um nome a um valor, que pode ser recuperado com `${nome}` ou `$nome`. As chaves delimitam onde o nome termina: `${nome}_copia` utiliza a variável `nome`, enquanto `$nome_copia` procura uma variável chamada `nome_copia`.

```bash
primeira="a vida"
segunda="${primeira}eh bela"
printf '%s\n' "$segunda"  # "a vidaeh bela"
```

## Nomes, Atribuição e Valores:

O nome começa com uma letra ou sublinhado (`_`) e pode continuar com letras, números e sublinhados. Caracteres como `/`, `-` e `*` não são permitidos. Convencionalmente, nomes próprios dos *scripts* usam minúsculas, evitando conflitos com variáveis conhecidas do ambiente.

- `{variavel}={valor}` - Atribui um valor, **sem espaços ao redor de `=`**. Para escrever um texto com espaços diretamente, usam-se aspas ou escapes: `nome="Ana Silva"`.
- `{variavel}=` - Define um texto vazio; a variável continua definida.
- `unset {variavel}` - Remove a variável, salvo se ela estiver protegida contra isso.

Variáveis comuns armazenam texto; contextos aritméticos e atributos específicos permitem interpretá-lo como números. Por isso, `x=1`, `x="1"` e `x='1'` produzem o mesmo valor para uma variável comum.

```bash
var_1='script.*'
printf '%s\n' $var_1    # Pode expandir para script.c, script.cpp etc., se existirem.
printf '%s\n' "$var_1"  # Imprime literalmente script.*.
var_2=ls
"$var_2"                # Executa ls, pois a expansão ocupa o lugar do nome do comando. Também funcionaria sem as aspas duplas
```

> O conteúdo de uma variável não é reinterpretado como uma linha completa de código: `|`, `>` e aspas armazenadas não se tornam automaticamente operadores ou delimitadores. O exemplo anterior funciona por conter apenas o nome do comando.

## Ambiente, Exportação e Herança:

Uma variável criada na *shell* não é automaticamente enviada aos programas executados por ela. **Exportar** significa incluir seu nome e valor no ambiente desses programas, e não compartilhar uma variável única entre processos.

- `export {variavel}={valor}` - Cria ou atualiza uma variável e a marca para exportação. `export variavel` exporta uma variável já existente.
- `{variavel}={valor} {comando}` - Fornece uma variável ao ambiente daquele comando, sem alterar seu valor na *shell* que o chamou: `VAR="teste" ./meu_script.sh`.

### Isolamento e Contexto de Execução:

| Forma | Comportamento das Variáveis |
| --- | --- |
| `bash script.sh` ou `./script.sh` | Inicia outro interpretador; recebe pelo ambiente as variáveis exportadas. Alterações não voltam à *shell* original. |
| `( comandos )` | Cria uma *subshell*, copiando o contexto, inclusive variáveis não exportadas e atributos. Alterações ficam isoladas. |
| `source arquivo.sh` ou `. arquivo.sh` | Executa no contexto atual. As alterações podem permanecer depois que o arquivo termina. |

Quando uma variável é passada pelo **ambiente** a um novo programa, transfere-se seu nome e valor, não atributos de controle como somente leitura. Isso difere da cópia do contexto em uma *subshell*. Em ambos os casos isolados, alterar a cópia não modifica o valor no processo original.

## Variáveis da *Shell*:

A *shell* mantém diversas variáveis com funções específicas, convencionalmente em maiúsculas. Algumas fazem parte do ambiente, mas outras, como as de configuração dos *prompts*, podem existir apenas na própria *shell*.

| Variável | Descrição |
| --- | --- |
| `$HOME` | Caminho do diretório pessoal do usuário atual, normalmente usado na expansão de `~`. |
| `$PATH` | Diretórios, separados por `:`, nos quais são procurados programas executáveis. |
| `$PS1` / `$PS2` | *Prompt* principal / secundário, usado quando a *shell* espera a continuação de um comando. |
| `$IFS` | Separador interno de campos (*Internal Field Separator*), usado na separação de resultados de expansões e pelo comando `read`. |
| `$PWD` | Caminho do diretório de trabalho atual, mantido pela *shell*. |

> Alterar manualmente `$PWD` não muda o diretório de trabalho do processo (como a *shell* identifica o caminho não tem relação com essa variável).

## Atributos e Escopo:

- `declare -{atributo} {variavel}={valor}` - Atribui valor e propriedades à variável.

- `declare +{atributo} {variavel}` - Remove um atributo quando isso é permitido.

Alguns atributos importantes são:

| Atributo | Função                                                                                                           |
| -------- | ---------------------------------------------------------------------------------------------------------------- |
| `-x`     | Marca para exportação, como `export`.                                                                            |
| `-r`     | Torna somente leitura, como `readonly`: impede atribuição e remoção com `unset`. Não pode ser desfeito com `+r`. |
| `-i`     | Avalia atribuições como expressões inteiras: `declare -i total=2+3` armazena `5`.                                |

> Os atributos podem ser combinados: uma variável pode ser exportada e somente leitura ao mesmo tempo. No Bash, o comando `typeset` é equivalente a `declare` (*alias*).

### Localidade em Funções:

Por padrão, funções usam as variáveis da *shell* em que executam. Uma variável global nesse contexto é visível às funções, mas **não precisa ser exportada**; exportação e escopo são propriedades diferentes.

- `local {variavel}={valor}` - Dentro de uma função, cria uma variável local que oculta outra de mesmo nome enquanto a função executa. Ao retornar, a variável externa volta a ficar visível.

O escopo é dinâmico: outras funções chamadas por essa função também podem enxergar a variável local. Dentro de funções, `declare` também cria variáveis locais por padrão; fora delas, não substitui `local`. A sintaxe de funções fica reservada para seu próprio capítulo.

## *Arrays*:

- `{array}=(valor1 valor2 valor3)` - Cria um vetor indexado. Quando os índices não são especificados, os elementos recebem posições consecutivas a partir de zero.
- `{array}[{indice}]={valor}` - Atribui ou altera um elemento. O acesso utiliza `${array[indice]}`.
- `${#array[@]}` - Retorna a quantidade de elementos; `${!array[@]}` permite obter seus índices.

```bash
arquivos=("relatorio final.txt" "dados.csv")
arquivos[1]="dados novos.csv"
printf '%s\n' "${arquivos[0]}"  # relatorio final.txt
printf '%s\n' "${arquivos[@]}"  # Um elemento por linha, preservando seus espaços.
printf '%s\n' "${#arquivos[@]}" # 2
```

`"${array[@]}"` preserva **cada elemento como um argumento independente**; `"${array[*]}"` reúne todos em um único argumento, separados pelo primeiro caractere de `IFS` (espaço por padrão). Sem aspas, podem ocorrer separação de palavras e expansão de arquivos, perdendo os limites originais dos elementos.

> Os índices não precisam ser contínuos. Por isso, a quantidade de elementos não é necessariamente o maior índice mais um.

## Variáveis e Parâmetros Especiais:

Fornecem informações sobre a execução. Parâmetros especiais, como `$?` e `$$`, não são nomes comuns aos quais se atribui um valor com `=`.

| Forma     | Descrição                                                                                                                                                          |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `$RANDOM` | Produz um inteiro pseudoaleatório de `0` a `32767` (15 *bits*).                                                                                                    |
| `$$`      | PID da *shell* original; mantém esse valor em contextos de *subshell*, como `( ... )`. `$BASHPID` identifica o processo Bash que está executando naquele contexto. |
| `$!`      | PID associado ao comando ou trabalho mais recente iniciado em segundo plano.                                                                                       |
| `$?`      | *Status* de saída do último comando ou *pipeline*, conforme as regras de redirecionamento e execução.                                                              |

> `$?` é atualizado após cada comando. Depois de `echo $?`, passa a indicar o resultado desse próprio `echo`; para guardar um resultado, a atribuição `resultado=$?` deve vir imediatamente após o comando de interesse.

## Leitura de Entrada:

- `read {opções} {variaveis}` - Lê uma linha da entrada padrão e distribui seus campos entre as variáveis. A última recebe o restante dos campos e seus separadores internos; com uma variável, recebe o texto todo após o tratamento de `IFS`.

Por padrão, espaços, tabulações e quebras de linha funcionam como separadores, e os espaços nas extremidades são removidos. A opção `-r` impede que barras invertidas sejam interpretadas como escapes. Para preservar também os espaços, usa-se `IFS= read -r linha`.

```bash
printf 'Nome completo: '
IFS= read -r nome
printf 'Nome recebido: <%s>\n' "$nome"
```

> Se não forem passadas variáveis para `read`, ele armazena a linha em na variável `REPLY` (implicitamente), sem separá-la em campos. Seu *status* indica sucesso ou uma condição como fim da entrada, o que permite usá-lo em testes e, posteriormente, em repetições.

## Parâmetros Posicionais:

Representam os argumentos passados a um *script* ou função, de maneira semelhante a `argc` e `argv` de C. Eles são diferentes dos dados lidos da entrada padrão por `read`.

| Forma | Descrição |
| --- | --- |
| `$#` | Quantidade de argumentos posicionais; não inclui `$0`. |
| `$0` | Nome usado para chamar a *shell* ou o *script*. Não passa a ser o nome de uma função quando ela é chamada. |
| `$1` a `${n}` | Argumentos pela posição. A partir do décimo, as chaves são obrigatórias: `${10}`, `${11}` etc. |
| `"$@"` | Todos os argumentos, cada um preservado separadamente, como em `"${array[@]}"`. |
| `"$*"` | Todos reunidos em um argumento, separados pelo primeiro caractere de `IFS`. |

- `shift {n}` - Descarta os primeiros `n` argumentos e desloca os restantes: com `shift` (equivalente a `shift 1`), `$2` vira `$1`, `$3` vira `$2`, etc. Não altera `$0`.
- `set -- {valores}` - Redefine os parâmetros posicionais; `--` encerra as opções de `set`. Sem valores depois de `--`, remove todos os argumentos posicionais.

```bash
set -- "Ana Silva" "relatorio final.txt"
printf '%s\n' "$#"  # 2
printf '<%s>\n' "$@" # Dois argumentos completos, um por linha.
shift
printf '%s\n' "$1"  # relatorio final.txt
```

## Expansão e Manipulação de Valores:

As formas `${...}` permitem aplicar valores padrão, testes e operações sobre o texto durante a expansão. Em geral, modificam **o resultado expandido**, sem alterar a variável original; a atribuição com `:=` é uma exceção.

### Valores Padrão e Condicionais:

Uma variável **não definida** não possui valor atribuído; uma variável **vazia** está definida, mas contém zero caracteres. Nos operadores abaixo, `:` faz a verificação incluir as duas situações. Sem ele, testa-se apenas se a variável não está definida.

- `${variavel:-padrao}` - Retorna `padrao` se a variável não estiver definida ou estiver vazia; caso contrário, retorna seu valor.
- `${variavel:=padrao}` - Além de retornar, atribui `padrao` se a variável não estiver definida ou estiver vazia.
- `${variavel:?mensagem}` - Escreve um erro na saída de erro padrão se a variável não estiver definida ou estiver vazia. Nesse caso, uma *shell* não interativa encerra a execução com *status* de erro. Se a variável estiver definida e não vazia, a expansão retorna seu valor.
- `${variavel:+alternativa}` - Retorna `alternativa` apenas se a variável estiver definida e não vazia; caso contrário, produz texto vazio.

```bash
nome=
printf '<%s>\n' "${nome-padrao}"  # <>: definida, embora vazia.
printf '<%s>\n' "${nome:-padrao}" # <padrao>: o teste também considera vazio.
: "${nome:=padrao}"               # Atribui o padrão sem tentar executá-lo.
```

> **Comando nulo (`:`):** Não realiza uma ação e retorna sucesso. Na última linha, ele recebe o resultado da expansão como argumento; se `${nome:=padrao}` fosse escrito sozinho na posição de comando, a *shell* tentaria executar o valor resultante.

### Manipulação de Texto:

- `${#variavel}` - Quantidade de caracteres do texto. Para vetores, `${#vetor[@]}` conta elementos.
- `${!variavel}` - Expansão indireta: usa o valor de uma variável como nome de outra e recupera seu conteúdo, lembrando uma desreferenciação de ponteiro.
- `${variavel:inicio:tamanho}` - Extrai uma parte do texto, a partir do índice `inicio` (começando em zero). Sem `tamanho`, segue até o final.
- `${variavel/padrao/novo}` - Substitui a primeira correspondência de um padrão; `${variavel//padrao/novo}` substitui todas.
- `${variavel#padrao}` / `${variavel##padrao}` - Remove a menor / maior correspondência do padrão no início do texto.
- `${variavel%padrao}` / `${variavel%%padrao}` - Remove a menor / maior correspondência do padrão no final do texto.

Os padrões seguem as regras de casamento da *shell*, não de expressões regulares. As formas duplicadas fazem diferença quando um curinga permite mais de uma correspondência:

```bash
arquivo="relatorio.final.txt"
printf '%s\n' "${arquivo%.*}"  # relatorio.final
printf '%s\n' "${arquivo%%.*}" # relatorio
printf '%s\n' "${arquivo#*.}"  # final.txt
printf '%s\n' "${arquivo##*.}" # txt
```

---

# 7. Estruturas Condicionais

Na *shell*, condições avaliam o ***status* de saída** de comandos: `0` significa sucesso e permite executar o ramo verdadeiro; valores diferentes de zero indicam uma condição falsa ou erro. Isso lembra o retorno de sucesso de um programa C, mas difere de uma expressão lógica em C, na qual zero é falso.

> Os comandos internos `true` e `false` produzem diretamente os *status* `0` e `1`, respectivamente. Não é necessário usar um testador para todo comando: `if grep -q 'erro' registro.txt; then ...` já pode decidir pelo resultado de `grep`.

## Testes de Expressões:

- `test {expressão}` ou `[ {expressão} ]` - Testa arquivos, textos e inteiros, produzindo um *status*. 

> `[` é um comando interno do Bash (também existem versões externas), e `]` é seu argumento final; por isso, **os espaços são obrigatórios**.

- `[[ {expressão} ]]` - Construção do Bash que acrescenta testes de padrões e expressões regulares. Não realiza separação de palavras nem expansão de arquivos nos valores; sua sintaxe e alguns operadores diferem de `[ ... ]`.

- `(( expressão ))` - Avalia uma expressão inteira. Se o resultado numérico for diferente de zero, retorna *status* `0`; se for zero, retorna *status* `1`. Permite operadores como `>`, `>=`, `&&`, `||` e `!`, sem exigir espaços junto dos parênteses.

**Testes Mais Usados:**

| Finalidade | Expressão com `test` ou `[` |
| --- | --- |
| Caminho existente | `[ -e "$caminho" ]` |
| Arquivo comum / diretório | `[ -f "$caminho" ]` / `[ -d "$caminho" ]` |
| Leitura / escrita / execução permitida | `[ -r "$caminho" ]` / `[ -w "$caminho" ]` / `[ -x "$caminho" ]` |
| Texto vazio / não vazio | `[ -z "$texto" ]` / `[ -n "$texto" ]` |
| Textos iguais / diferentes | `[ "$a" = "$b" ]` / `[ "$a" != "$b" ]` |
| Inteiros iguais / diferentes | `[ "$a" -eq "$b" ]` / `[ "$a" -ne "$b" ]` |
| Inteiro menor / menor ou igual | `[ "$a" -lt "$b" ]` / `[ "$a" -le "$b" ]` |
| Inteiro maior / maior ou igual | `[ "$a" -gt "$b" ]` / `[ "$a" -ge "$b" ]` |

Esses testes também podem ser utilizados em `[[ ... ]]`. Em `[ ... ]`, as aspas são importantes para manter valores vazios ou com espaços como argumentos completos. Um teste falso normalmente retorna `1`; uma expressão inválida pode retornar outro *status* de erro.

### Texto, Padrões e Aritmética:

Em `[[ ... ]]`, `<` e `>` comparam **textos**, conforme a ordenação do idioma e região; para inteiros, utilizam-se `-lt`, `-gt` etc. ou `(( ... ))`. Não existem operadores `<=` e `>=` para comparação textual nessa construção.

Com `==` e `!=`, o lado direito pode ser um padrão da *shell*: `[[ $arquivo == *.txt ]]` testa a terminação `.txt`, enquanto `[[ $arquivo == "*.txt" ]]` compara com o texto literal `*.txt`. O operador `=~` permite testar uma expressão regular, como em `[[ $numero =~ ^[0-9]+$ ]]`.

> `$((expressão))` **expande um valor**, enquanto `((expressão))` **executa uma avaliação e fornece um *status***. Por exemplo, `$((2 > 1))` produz `1`, mas `((2 > 1))` termina com *status* `0`, indicando que a condição é verdadeira.

## Estruturas de Decisão:

> Para fechar uma estrutura de decisão (delimitação), utiliza-se o nome da estrutura ao contário. `if` usa `fi`, e `case` usa `esac`.  

### `if`, `elif` e `else`:

Executa o primeiro bloco cuja condição termine com sucesso. `elif` permite testar outra condição; `else` cobre o caso em que nenhuma teve sucesso. Ambos são opcionais.

```text
if {comando_testado}; then
    {comandos_caso_sucesso}
elif {outro_comando}; then
    {comandos_caso_sucesso_alternativo}
else
    {comandos_caso_falha}
fi
```

```bash
#!/bin/bash

arquivo="relatorio.txt"

if [ ! -e "$arquivo" ]; then                    # Testa se o arquivo não existe.
    printf 'Arquivo inexistente.\n'
elif [[ -f "$arquivo" && -r "$arquivo" ]]; then # Testa se é um arquivo comum e se tem permissão de leitura.
    if test ! -s "$arquivo"; then               # Testa se o arquivo está vazio
        printf 'O arquivo está vazio.\n'
    else
        cat "$arquivo"
    fi
else
    printf 'O caminho não é um arquivo comum legível.\n' >&2
fi
```

O `!` nega o resultado do teste, e `&&` exige sucesso dos dois comandos. O ponto e vírgula antes de `then` separa comandos na mesma linha; ele pode ser substituído por uma quebra de linha.

### `case`:

Compara um texto com padrões, de cima para baixo, executando o primeiro que corresponder. Os padrões seguem as regras da *shell*, permitindo `*`, `?` e conjuntos entre colchetes; `|` separa alternativas para o mesmo bloco.

```bash
arquivo="relatorio.txt"
case "$arquivo" in
    *.txt | *.md)
        printf 'Arquivo de texto.\n'
        ;;
    *.csv)
        printf 'Tabela de dados.\n'
        ;;
    *)
        printf 'Tipo não identificado.\n'
        ;;
esac
```

`;;` encerra a seleção depois do bloco correspondente. O padrão `*` aceita qualquer texto e funciona como alternativa padrão quando colocado por último; no início, capturaria todos os casos.

> O Bash também oferece `;&`, que continua no bloco seguinte sem testar seu padrão, e `;;&`, que continua testando os padrões seguintes. Portanto, há formas de cascata, embora `;;` seja suficiente para o uso básico.

## Operadores de Curto-Circuito:

Encadeiam comandos a partir de seus resultados, evitando execuções desnecessárias:

- `{comando_1} && {comando_2}` - Lógica *AND*: executa o segundo apenas se o primeiro terminar com sucesso.
- `{comando_1} || {comando_2}` - Lógica *OR*: executa o segundo apenas se o primeiro falhar.
- `! {comando}` - Inverte o resultado lógico: sucesso passa a falha e falha passa a sucesso.

O *status* de uma lista com `&&` ou `||` é o do último comando efetivamente executado. Esses operadores têm a mesma precedência entre si e são avaliados da esquerda para a direita.

> `a && b || c` não equivale a um `if/else` geral: `c` também executa quando `a` tem sucesso, mas `b` falha. Quando a intenção é escolher entre dois ramos, `if/else` torna essa decisão explícita.

---

> REVISADO ATÉ AQUI!

# 8. Estruturas de Repetição

# Fontes

- ALVARES, Andrei Rimsa. Disciplina: Tópicos Especiais em Fundamentos da Computação: *Shell Scripting*. Curso de graduação em Engenharia de Computação - Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2026.
- FREE SOFTWARE FOUNDATION. *GNU Bash Reference Manual*. Edição 5.3, para Bash 5.3. [S. l.], 2025. Disponível em: [https://www.gnu.org/software/bash/manual/bash.html](https://www.gnu.org/software/bash/manual/bash.html). Acesso em: 18 ago. 2026.
- FREE SOFTWARE FOUNDATION. *GNU Coreutils*. Versão 9.11. [S. l.], 2026. Disponível em: [https://www.gnu.org/software/coreutils/manual/coreutils.html](https://www.gnu.org/software/coreutils/manual/coreutils.html). Acesso em: 4 set. 2026.
- THE LINUX MAN-PAGES PROJECT. *Linux man-pages*. Versão 6.19. [S. l.], 2026. Disponível em: [https://www.kernel.org/pub/linux/docs/man-pages/man-pages-6.19.tar.xz](https://www.kernel.org/pub/linux/docs/man-pages/man-pages-6.19.tar.xz). Acesso em: 4 set. 2026.
- THE KERNEL DEVELOPMENT COMMUNITY. *The Linux kernel documentation*. [S. l.], [s. d.]. Disponível em: [https://docs.kernel.org/](https://docs.kernel.org/). Acesso em: 4 set. 2026.
