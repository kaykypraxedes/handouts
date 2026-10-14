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
# 0. Conceitos Básicos

## *Shell*:

A *shell* é um programa que funciona como **interpretador de comandos** e também como uma **linguagem de programação**, permitindo controlar e combinar outros programas do sistema. O **terminal** fornece o meio de entrada e saída usado para interagir com a *shell*, enquanto o ***kernel*** é a parte do sistema operacional responsável por gerenciar recursos como processos, arquivos e dispositivos.

Ao abrir um terminal em uma interface gráfica, normalmente é criado um ***pseudo-terminal*** e iniciado um *shell* (como o `bash`) conectado a ele. Os comandos digitados são então interpretados pela *shell*, que pode executá-los internamente ou iniciar outros programas.

### Programas:

Os comandos executados pela *shell* podem ser **internos** (*builtins*, implementados pela própria *shell*) ou **programas externos**, armazenados no sistema de arquivos. Por exemplo, `cd` é um *builtin*, enquanto `ls` normalmente corresponde a um programa externo.

Quando um programa externo é chamado apenas pelo nome, sem indicar seu caminho, o `bash` procura por um executável correspondente nos diretórios definidos pela variável `$PATH`. Por isso é possível executar simplesmente `ls` em vez de escrever seu caminho completo, como `/usr/bin/ls`.

### *Status* de Saída:

Além das informações que um comando pode imprimir, sua execução termina produzindo um ***status*** **de saída** (*exit status*), representado por um valor de `0` a `255`. Por convenção, `0` representa uma execução bem-sucedida, enquanto valores diferentes de `0` indicam algum tipo de falha ou condição diferente do sucesso.

O *status* do último comando executado fica disponível em `$?`. Esse valor é importante em *scripts*, pois permite tomar decisões com base no sucesso ou falha de comandos anteriores.

## Manuais:

Como o *Linux* recebe atualizações recorrentes, manuais impressos podem ficar desatualizados. A maior parte das informações necessárias, desde comandos de usuário até interfaces internas do *kernel* e da biblioteca *C*, pode ser consultada no próprio sistema operacional (SO).

### Hierarquia do Sistema:

Por uma questão de organização, os manuais foram divididos em grupos representados por números, seguindo a seguinte hierarquia:

1. Comandos de usuário;
2. Interfaces de chamadas de sistema do *kernel*;
3. Interfaces da biblioteca *C*;
4. Arquivos especiais (dispositivos, *drivers*, etc.);
5. Formatos de arquivos;
6. Jogos e divertimentos;
7. Assuntos variados (*miscellaneous*);
8. Comandos de administração do sistema;
9. Comandos internos do *kernel* (pouco usado no *Linux*, que mantém essa documentação no site oficial);
10. *New* (*buffer* para seções em elaboração, antes de serem formalmente adicionadas às respectivas seções);

> Nos comandos a seguir, o número ao lado do nome da *palavra* retornada corresponde à seção do manual no sistema.
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
line: nada apropriado.
```

- `{palavra} --help` - Manual intermediário sobre a palavra pesquisada (apenas resultado direto).

- `man {hierarquia} {palavra}` - Manual completo da palavra (apenas resultado direto do comando). A hierarquia pode ser ocultada, mas pode mostrar resultados diferentes do esperado caso a palavra apareça em múltiplas hierarquias.

- `info {palavra}` - `man` melhorado (mais atualizado).

## Comandos básicos:

### Informações Sobre o Sistema:

> Para obter informações mais aprofundadas, use as ferramentas de consulta sobre comandos.

- `who {opções}` - Lista os usuários logados.

- `pwd {opções}` - Mostra o diretório de trabalho atual.

- `history {opções}` - Mostra os comandos executados recentemente.

- `ls {opções} {diretório}` - Lista o conteúdo de um diretório.

- `tree {opções} {diretório}` - Mostra o conteúdo de um diretório em um formato de árvore hierárquica.

- `find {diretório inicial} {opções} {critérios}` - Procura arquivos e diretórios por nome, permissões e outros critérios.

- `locate {opções} {critérios}` - `find` mais rápido (tem *buffer*). Ele não utiliza um diretório inicial.
> Para atualizar o *buffer* é preciso executar o comando `sudo updatedb` ou reiniciar o sistema.

- `whereis {opções} {comando/arquivo}` - Mostra a localização de comandos, páginas de manual e binários.

- `which {opções} {comando/arquivo}` - `whereis` apenas para binários e *scripts*.

- `file {opções} {arquivo}` - Mostra o tipo do arquivo especificado.

- `stat {opções} {arquivo}` - Mostra informações detalhadas sobre arquivos e diretórios.

- `date {opções} {data}` - Mostra ou define o relógio do sistema.

- `cal {opções} {data}` - Mostra um calendário em linha de comando.

### Ações no Terminal:

- `clear` - Limpa o conteúdo da tela atual.

- `exit` - Sai do terminal.

- `logout` - Desconecta do usuário atual.

## Automatização de Processos:

A *shell* pode ser utilizado diretamente no terminal, um comando por vez, ou de maneira automatizada por meio de *scripts*, o que permite maior velocidade, versatilidade e tarefas mais complexas. Para criar o *script*, é necessário:

1. Criar um arquivo e inserir a sequência de comandos desejada. Para que o *kernel* saiba como interpretar os comandos, é preciso adicionar o interpretador: `#! /caminho/interpretador` (o `#!` funciona quase como o `#include` de `C`, mas adiciona um interpretador em vez de uma biblioteca).
> Para utilizar o *bash* (por exemplo): `#! /bin/bash`.
2. Dar permissão de execução para o arquivo (`chmod +x {arquivo}`) para executá-lo diretamente.

---

# 1. Processamento de Texto

## Editores de Texto no Terminal:

> As principais diferenças estão no número de ferramentas presentes no editor e na forma como editam.

- `nano {opções} {arquivo}` - Editor simples (em muitos sistemas, é também um *alias* para o editor `pico`).

- `vim {opções} {arquivo}` - Editor de texto mais completo (em muitos sistemas, é também um *alias* para o editor `vi`).

- `emacs {opções} {arquivo}` - Editor de texto mais robusto.

## Visualização de Textos:

- `cat {opções} {arquivos}` - Concatena arquivos e imprime seu conteúdo. Se usado com um único arquivo, apenas o imprime.
> O comando `tac` concatena os arquivos em ordem reversa (inverte o conteúdo, mas mantém a ordem de arquivos).

- `zcat {opções} {arquivo}` - Lê o conteúdo de um arquivo compactado com ***gzip***. Existem variações a depender do tipo de compressão.

- `less {opções} {arquivo}` - Mostra a saída de um comando ou arquivo de texto e permite navegar para trás e para frente.
> O comando `more` faz basicamente a mesma coisa, mas de maneira mais limitada (apenas avança).

- `head {opções} {arquivo}` - Mostra as primeiras partes de um arquivo.

- `tail {opções} {arquivo}` - Mostra as últimas partes de um arquivo.

## Extrair Dados de Textos:

- `strings {opções} {arquivo}` - Extrai caracteres legíveis de arquivos (pode extrair dados de binários como `.jpg`, `.mp3`, etc.).

- `wc {opções} {arquivo}` - Conta o número de linhas, palavras, caracteres, etc. (depende da opção escolhida) de um arquivo.

- `grep {opções} {padrão} {arquivo}` - Filtra dados de um texto.

- `diff {opções} {arquivo_1} {arquivo_2}` - Compara o conteúdo de arquivos.

## Operações Sobre Textos:

- `sort {opções} {arquivo}` - Ordena o conteúdo de um fluxo de dados de entrada ou arquivo.

- `tee {opções} {arquivos}` - Mostra a saída de um comando e a escreve em um ou mais arquivos.

> Os seguintes comandos possuem maior complexidade (este é apenas um uso básico):
- `sed {opções} {expressão} {arquivo}` - Imprime o conteúdo de um arquivo (como o `cat`) com a opção de substituir palavras.

- `awk {opções} {programa} {arquivo}` - Formata o resultado de um comando.

# 2. Sistema de Arquivos

Antes de qualquer coisa, é importante entender como a *shell* identifica o caminho para um arquivo (e/ou diretório):

1. `/caminho/absoluto/arquivo` - Iniciar o caminho com uma barra (`/`) indica que está sendo usado o caminho absoluto, a partir da raiz do sistema.
2. `~/caminho_de_user/arquivo` - Iniciar o caminho com `~/` indica que está sendo usado o caminho a partir do diretório pessoal do usuário atual. Para usar o diretório de outro usuário sem informar o caminho absoluto, pode-se escrever `~user_alvo/caminho/arquivo`.
3. `caminho/relativo/arquivo` - O sistema considera o diretório atual e interpreta o caminho a partir dele, o que pode causar algumas confusões.

Além disso, a *shell* interpreta `.` como um *alias* para o próprio diretório e `..` como um *alias* para seu diretório pai.
Dessa maneira, estando no diretório `C/`, cujo caminho absoluto é `/A/B/C`, os comandos `cd D` e `cd ./D` produzem o mesmo resultado para chegar a `D/`. De maneira semelhante, para chegar a `B/`, tanto `cd /A/B` quanto `cd ..` produzem o mesmo resultado.

Em sistemas de arquivos *Linux* comuns, nomes normalmente diferenciam letras maiúsculas e minúsculas (*case-sensitive*), portanto `Arquivo.txt` e `arquivo.txt` podem representar arquivos diferentes. Além disso, arquivos e diretórios cujo nome começa com `.` são convencionalmente tratados como **ocultos** e, por padrão, não são exibidos pelo `ls` (podem ser mostrados com `ls -a`).

## Estrutura de Pastas do *Linux*:

> A estrutura e a função das pastas podem variar um pouco conforme a distribuição.

```
/                         # raiz: ancestral de todos os arquivos do sistema
├── bin -> usr/bin        # comandos binários essenciais p/ inicializar e executar o sistema
├── boot                  # arquivos estáticos do boot loader, necessários pra dar boot
├── dev                   # arquivos de dispositivos (discos, terminais, impressoras etc.)
├── etc                   # configurações locais do sistema (ex: /etc/passwd = lista de usuários)
│   ├── opt               # configs de pacotes opcionais instalados em /opt
│   ├── X11               # configs do sistema de janelas X
├── home                  # diretórios pessoais dos usuários (ex: /home/zach)
│   └── user              # * diretório pessoal de um usuário específico (mesmo padrão do /home)
├── lib -> usr/lib        # bibliotecas compartilhadas
│   └── modules           # módulos do kernel carregáveis
├── mnt                   # pontos de montagem p/ sistemas de arquivos temporários
├── opt                   # pacotes de software opcionais
├── proc                  # sistema de arquivos virtual c/ infos do kernel e processos
├── root                  # * home do usuário root (não é o mesmo que /)
├── run                   # * dados voláteis de runtime (PIDs, sockets), recriados a cada boot
├── sbin -> usr/sbin      # binários essenciais de administração do sistema
├── sys                   # pseudo sistema de arquivos p/ dispositivos
├── tmp                   # arquivos temporários
├── usr                   # 2ª maior hierarquia: dados compartilháveis, não mudam com frequência
│   ├── bin               # maioria dos comandos de usuário
│   ├── games             # jogos e programas educacionais
│   ├── include           # cabeçalhos (headers) usados por programas C
│   ├── lib               # bibliotecas
│   ├── local             # hierarquia local: bin, games, lib, share, src etc.
│   ├── sbin -> bin       # binários de administração não vitais
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

1. `u` (*user*): Dono do arquivo.
2. `g` (*group*): agrupamento de usuários que compartilham permissões. Permite dar acesso a várias pessoas específicas a um arquivo ou diretório **sem precisar liberá-lo para o restante do sistema** (*others*).
3. `o` (*others*): Outros usuários **além** do dono (permissões não se aplicam a ele).

Ao se pesquisar informações detalhadas acerca de um arquivo específico (seja com o `stat`, com o `ls -l`, etc.), é possível ver o dono do arquivo e o grupo.

## Informações:

- `id {opções} {usuário}` - Mostra informações de identidade do usuário. Se nenhum usuário for informado, considera o usuário atual.

- `basename {opções} {caminho/arquivo_ou_dir}` - Retorna apenas `arquivo_ou_dir`.

- `dirname {opções} {caminho/arquivo_ou_dir}` - Retorna `caminho`.

- `lsof {opções}` - Lista os arquivos abertos.

- `fuser {opções} {arquivo}` - Mostra informações sobre os arquivos abertos.

## Operações Sobre Arquivos e Diretórios:

> Nos comandos a seguir, se não for passado o caminho absoluto (começando a partir de `/` ou de `~/`), é considerado o caminho relativo (a partir da pasta atual).
- `cd {opções} {caminho/diretório}` - Entra em diretório pelo terminal.

- `mkdir {opções} {caminho/nome}` - Cria um diretório no caminho especificado.

- `rmdir {opções} {caminho/nome}` - Apaga um diretório vazio; se ele contiver elementos, não será apagado.

- `rm {opções} {caminho/nome}` - Apaga um arquivo ou diretório. É muito usado com a *flag* `-r` (*recursive*), com a qual apaga todos os arquivos de um diretório e o próprio diretório.

- `mv {opções} {origem/nome_antigo} {destino/nome_novo}` - Move um arquivo ou diretório. Se o nome novo não for informado, o comando mantém o nome existente; se `origem/` e `destino/` forem iguais, o arquivo é apenas renomeado.

- `cp {opções} {origem/nome_antigo} {destino/nome_novo}` - Copia um arquivo. Infere o nome novo se ele não for informado, da mesma maneira que o `mv`. Para copiar diretórios, é necessário usá-lo com a *flag* `-r`, que copia recursivamente os arquivos e subdiretórios da pasta-alvo.

- `ln {opções} {origem/nome_arquivo} {destino/nome_link}` - Cria *links* (caminhos alternativos para acessar um dado).

    1. `ln` (*hard-link*): funciona como um novo **ponteiro independente para o dado do arquivo original** (funciona mesmo se o arquivo original for apagado). **Não funciona com diretórios**.
    > Nas informações do arquivo (acessadas pelo `ls -l`, por exemplo), é possível ver se existem outros *hard-links* para aquele espaço no disco e quantos (por padrão, é apenas 1).
    2. `ln -s` (*soft-link*): funciona como um **ponteiro para o arquivo ou diretório original** (é ligado ao seu nome). Dessa maneira, o *soft-link* deixa de funcionar se o arquivo original for apagado, mas volta a funcionar se um arquivo com o mesmo nome e caminho for criado no lugar. **Funciona com diretórios** (sua maior vantagem).
    > Em suas informações, ele indica o caminho para o qual aponta (`nome_link -> /caminho/arquivo`).

- `gzip {opções} {arquivo}` - Comprime arquivos.

- `gunzip {opções} {arquivo}` - Descomprime arquivos.

- `tar {opções} {arquivos}` - Agrupa múltiplos arquivos/diretórios em um único arquivo.

- `split {opções} {arquivo}` - Separa um arquivo em pedaços menores.

- `shred {opções} {arquivo}` - Apaga arquivos de forma segura (sobrescreve sua posição de memória).

## Permissões:

Cada arquivo ou diretório pode ter as seguintes permissões:

1. `r` (*read*): Permite que **o arquivo seja aberto e lido**. Para diretórios, que **o conteúdo seja listado**.
2. `w` (*write*): Permite que **o arquivo seja escrito ou truncado**. Para diretórios, que **arquivos internos sejam criados, removidos e renomeados**.
3. `x` (*execute*): Permite que **o arquivo seja tratado como um programa executável**. Para diretórios, **entrar nele** (ex: usar `cd`).

**Formato de permissão:** `-rwxrw-r--`

1. O primeiro caractere da permissão (`-` no exemplo acima) representa o tipo de arquivo:

    - `-` - Arquivos comuns;
    - `d` - Diretórios;
    - `l` - *soft-links*;
    - `c` (*Character Device*) - São comumente peças de *hardware* nas quais o sistema lê ou escreve "uma letra por vez" em um fluxo contínuo, sem poder avançar ou retroceder (como **dispositivos periféricos**: teclado, mouse, placa de som etc.);
    - `b` (*Block Device*) - São comumente peças de *hardware* nas quais o sistema lê e grava dados em "pacotes" grandes (blocos); o sistema consegue ir diretamente à parte que deseja ler (como **dispositivos de armazenamento**: *HDs*, *SSDs*, pendrives etc.);
    > De maneira simplificada, o sistema trata **dispositivos** (componentes físicos, ou *hardware*) **como se fossem arquivos**. Isso permite que programas se comuniquem com um teclado ou um pendrive usando as mesmas regras e permissões de um arquivo de texto normal.

2. Nas demais posições, os elementos, agrupados de três em três, representam as permissões do **dono**, do **grupo** e dos **demais usuários** (`-` indica a ausência daquela permissão).

- `chown {opções} {dono}:{grupo} {arquivo/diretório}` - Altera o dono e/ou grupo associado a um arquivo ou diretório.

- `chgrp {opções} {grupo} {arquivo/diretório}` - Altera apenas o grupo associado a um arquivo ou diretório.

- `chmod {opções} {permissões} {arquivo/diretório}` - Altera as permissões de acesso.
> Apenas o dono do arquivo ou o superusuário podem alterar o modo (permissão).

Suporta duas formas para especificar mudanças:

1. **Representação Octal:** Usa três dígitos octais para definir as permissões absolutas do dono, do grupo e dos demais usuários, respectivamente. As permissões são calculadas pela soma de `r`=4, `w`=2 e `x`=1 (`chmod 751` -> -rwxr-x--x).
2. **Representação Simbólica:** Usa as letras `u` (dono), `g` (grupo), `o` (outros), `a` (todos) acompanhadas das operações `+` (adiciona), `-` (remove) ou `=` (define especificamente) (`chmod u+x` adiciona execução ao dono, por exemplo).
> Caso não seja definido nenhum usuário no comando, implicitamente ele considera todos (`a`).

### Permissões Especiais:

Na representação octal, podem ser utilizadas a representação de 4 valores, com o primeiro sendo uma permissão especial:

- ***SUID*** (***Set User ID***, octal `4nnn`): Quando o arquivo é executado, o processo assume o identificador do dono do arquivo (`-rw *s* rwxrwx`).
- ***SGID*** (***Set Group ID***, octal `2nnn`): Quando executado, o processo assume o grupo do arquivo. Quando aplicado a um diretório, novos arquivos criados dentro dele recebem o grupo do diretório, e não o grupo do usuário que criou o arquivo (`-rwxrw *s* rwx`).
- ***Sticky bit*** (octal 1000): Em diretórios, impede que um usuário remova ou renomeie um arquivo caso não seja o dono do diretório, o dono do arquivo ou o superusuário (`drwxrwxrw *t* `).

- `umask {opções} {máscara}` - Controla as permissões padrão dadas a um arquivo no momento em que é criado (qualquer arquivo daquela sessão da *shell*). Funciona como uma máscara de *bits* (usando notação octal) que serão removidos dos atributos de modo do arquivo.

---

# 3. Processos

Todo programa em execução no *Linux* é tratado pelo *kernel* como um **processo**. Como os sistemas operacionais modernos são multitarefa (*multitasking*), o *kernel* cria a ilusão de fazer várias coisas ao mesmo tempo, alternando rapidamente qual processo tem acesso à *CPU*.

O própria *shell* também é um **processo**. Em uma execução comum a partir de um *shell* interativo, quando ele inicia um programa externo, esse programa normalmente passa a executar em outro processo, relacionado à *shell* como seu processo filho. Já comandos internos (*builtins*) podem ser executados diretamente pelo próprio processo da *shell*.

## Como Funcionam Processos:

Um programa pode lançar (executar) outros programas, formando uma relação de parentesco entre processos:

```
processo_pai
 └── processo_filho
```

- Um **processo pai** (*parent process*) é aquele que dá origem a um **processo filho** (*child process*).
- Cada processo recebe um ***PID*** (*process ID*), atribuído em ordem crescente, além de manter informações como memória alocada, estado de execução e dono/usuário associado (assim como arquivos).
- Muitos serviços do sistema são executados como ***daemons***: processos em segundo plano, sem interface de usuário.
> O próprio *kernel* é convencionalmente tratado como *PID* 0; o primeiro processo real que ele lança é o `init` (*PID* 1), responsável por inicializar os demais serviços do sistema.

Os processos podem ter diferentes estados indicando como está sua execução, com os principais sendo:

- `R` (*Running*) - Executando;
- `S` (*Sleeping*) - Esperando algum evento (como uma tecla);
- `T` (*sTopped*) - O processo foi instruído a parar;
- `Z` (*Zombie*) - Processo filho finalizado mas não coletado pelo pai;
> `<` (*High priority*) - Não é diretamente um estado, mas um indicador de alta prioridade. O processo recebe mais tempo de *CPU*.

### Informações Sobre os Processos:

O *Linux* expõe informações sobre os processos em execução por meio do sistema de arquivos virtual `/proc`. Cada processo em execução possui uma pasta própria dentro de `/proc`, identificada pelo seu ***PID*** (`/proc/{PID}`). É dentro dela que ficam, entre outras coisas, os descritores de arquivo do processo (`/proc/{PID}/fd`), consultáveis com `ls -l`.

## Listagem:

- `ps {opções}` - Mostra uma foto **instantânea** dos processos.

    - O comando `ps x` exibe todos os processos do usuário, adicionando a coluna `STAT` (estado do processo).
    - Já o `ps aux` exibe os processos de **todos os usuários**, com colunas extras de uso de *CPU* e memória.
    > Opções sem traço (`x`, `aux`) invocam o `ps` no estilo *BSD*, que o *Linux* consegue emular.

- `pstree {opções}` - Mostra os processos em formato de árvore, evidenciando a relação de pai/filho.

- `top {opções}` - Visão **dinâmica** e continuamente atualizada dos processos, ordenados por atividade.

- `jobs {opções}` - Mostra trabalhos (processos que **aquele terminal** lançou) ativos.
> **Um trabalho pode ser composto por mais de um processo**. Ao executar `sort arquivo.txt | uniq &`, isso é um trabalho (aparece como `[1]` no `jobs`), mas são dois processos (`sort` e `uniq`, cada um com seu próprio *PID*).

- `vmstat {opções} {atraso}` - Mostra a utilização instantânea do sistema (memória, *swap*, disco); com um tempo em segundos, atualiza continuamente.

- `xload {opções}` - Gráfico da carga do sistema ao longo do tempo (interface gráfica).

- `tload {opções}` - Semelhante ao `xload`, mas desenhado no terminal.

## Controlando Processos:

O terminal executa programas em dois modos: **primeiro plano** (*foreground*, controlando o terminal) ou **segundo plano** (*background*, liberando o *prompt*). Para já iniciar em plano de fundo, basta seguir o comando com `&`:

```
$ xlogo &
[1] 3486
```

- `fg {job}` - Traz um *job* ao primeiro plano (*foreground*), identificado pelo número do *job*, `%N` (opcional se houver apenas um).

- `bg {job}` - Retoma um *job* parado em segundo plano (*background*).
> Esse comando só funciona no terminal de origem do *job*, exigindo que ele esteja parado antes (o processo é retomado, e vai para o plano de fundo).

### Sinais:

- `kill {opções} {-sinal} {PID ou %job}` - Envia um sinal a um processo (identificado por seu *PID* ou pelo número do *job* `%N`). Sem sinal especificado, envia `TERM` por padrão, pedindo para o programa terminar.

    - O `kill` não "mata" processos diretamente; ele envia **sinais** (a forma como o sistema operacional se comunica com programas em execução). A lista completa de sinais suportados pode ser vista com `kill -l`, e o que cada sinal faz pode ser consultado, em algumas distribuições, no manual `man 7 signal`.
    > Dois atalhos de teclado permitem alternar entre esses estados enviando sinais ao processo em primeiro plano (um processo em plano de fundo é imune a entradas de teclado): `CTRL-C` o interrompe, `CTRL-Z` o suspende (marcado como *stopped* em `jobs`).
    - Vale lembrar que nem todo programa reage aos sinais da maneira convencional (o programa pode ser implementado para interpretar esses sinais de outra maneira, criar seus "próprios sinais", ou mesmo ignorar qualquer sinal).
    > Os únicos sinais que não podem ser ignorados são o `SIGKILL` (comumente `kill -9`), que encerra o processo imediatamente, e o `SIGSTOP` (comumente `kill -19`), que apenas interrompe o processo.

- `killall {opções} {-sinal} {nome}` - Mesma ideia, mas envia o sinal a todos os processos que correspondam a um **nome** de programa.

- `nohup {comando}` - Torna um processo imune ao sinal `HUP` (que fecharia junto com o terminal).

---

# 4. Entradas, Saídas e Redirecionamentos

## Entrada e Saída Padrão:

Todo programa em execução possui estruturas definidas para lidar com dados, sendo elas a **entrada** (*input*), a **saída** (*output*) e a **saída de erro** (*error output*). Por padrão, essas estruturas são direcionadas da seguinte forma:

- **Entrada padrão** (*stdin*): O teclado (descritor de arquivo - **0**).
- **Saída padrão** (*stdout*): A tela (descritor de arquivo - **1**).
- **Saída de erro** (*stderr*): Também a tela (descritor de arquivo - **2**).

### *TTY* (Terminal):

***TTY*** (*teletype*) é o nome dado ao dispositivo que representa um terminal no sistema. Historicamente se referia a terminais físicos, mas ao abrir um terminal dentro de uma interface gráfica, o sistema cria um ***pseudo-terminal*** (*pts* - *pseudo terminal slave*), que se comporta da mesma forma para fins de entrada e saída.

- Por padrão, a **entrada padrão**, **saída padrão** e **saída de erro padrão** de um processo são apenas **apontadores** (*links*) para esse dispositivo de terminal (`/dev/pts/{N}` no *Linux*), sendo `N` um número que pode variar de acordo com o terminal aberto e o sistema operacional usado.
- A quantidade de descritores de um processo também pode variar além dos três padrões (`0`, `1`, `2`), a depender de como o programa foi construído (uso de *pipes*, arquivos abertos, etc.).
> Cada um desses apontadores é individual por processo. Alterá-lo em um processo não interfere em outro, mesmo que ambos apontem para o mesmo dispositivo. Ao final da execução do processo, esses apontadores deixam de existir.

## Ferramentas de Redirecionamento:

- `{comando} {num_entrada}< {arquivo}` - `num_entrada` implícito é 0 (**entrada padrão**). **Define a entrada padrão** para aquela operação como sendo `arquivo`.

- `{comando} {num_saída}> {arquivo}` - `num_saída` implícito é 1 (**saída padrão**). **Define a saída padrão** para aquela operação como sendo `arquivo`.
> Se o arquivo não existir, ele o cria, e, se existir e possuir conteúdo, o conteúdo é sobrescrito.

> Se o arquivo estiver com bloqueio de sobrescrita (`set -o noclobber`, que funciona para a sessão atual da *shell* e pode ser desativado com `set +o noclobber`), pode-se usar o operador `>|` para fazer uma sobrescrita forçada.

   - É possível redirecionar tanto a saída padrão quanto a saída de erro padrão ao mesmo tempo com `&>`.
   - `{comando} {num_saida}>&{num_alvo}` - Duplica o descritor `num_saida` (implícito: saída padrão), fazendo-o apontar para o mesmo destino do descritor `num_alvo`, em vez de um arquivo.
   > A ordem dos redirecionamentos importa, já que são processados da esquerda para a direita. Supondo que o arquivo `y` exista e o `x` não:

   ```bash
   $ cat x y 1>output 2>&1  # Saída padrão (1 - tela) passa a ser "output". Saída de erro padrão (2 - tela) passa a ser a mesma de 1 (output).
   $ cat x y 2>&1 1>output  # Saída de erro padrão (2 - tela) passa a ser a mesma de 1 (tela). Saída padrão (1 - tela) passa a ser "output" (2 continua sendo a tela).
   ```

- `{comando} {num_saída}>> {arquivo}` - Concatena a saída de um comando ao final de um arquivo. Da mesma maneira que o operador `>`, se o arquivo não existir, o cria.

- `{comando_1} | {comando_2}` - Redireciona a saída do `comando_1` como entrada do `comando_2`. Com ele, evita-se a necessidade de criação de arquivos intermediários para relacionar suas saídas e entradas, como por exemplo:

```bash
$ comando_a [argumentos] > temp
$ comando_b [argumentos] < temp
$ rm temp
```

> Por padrão, o *pipe* conecta apenas a saída padrão (`stdout` - `1`) do comando à esquerda à entrada padrão (`stdin` - `0`) do comando à direita. A saída de erro (`stderr` - `2`) mantém seu destino original, a menos que seja explicitamente redirecionada.

- `{comando} << {pattern}` - Envia o conteúdo digitado (texto) nas linhas seguintes como entrada para o comando, até encontrar uma linha contendo apenas o `pattern` definido (pode ser qualquer palavra, sendo comumente usados `EOF`, `END` etc.). É útil em *scripts*, pois dispensa o `CTRL-D` usado para encerrar uma entrada indefinida de forma interativa.
> Por padrão, o texto passa pela expansão de variáveis e símbolos especiais da *shell* (ex: `$VARIAVEL`). Para usá-lo de forma crua (sem expansão), basta envolver o `PATTERN` (ou parte dele) em aspas simples.

   - O operador `<<-` realiza a mesma função, mas remove tabulações (apenas `TAB` - não funciona com espaços simples) do início de cada linha do bloco.

# 5. Expansões

Antes de executar o comando, a *shell* analisa a linha em busca de **metacaracteres** (caracteres especiais) e os substitui automaticamente por dados reais (**expansão**). Somente depois ele entrega a instrução completa (já "mastigada") para o programa executar.

## Tipos de expansão:

### Expansão de Arquivos:

Nomes de arquivos podem ser abreviados utilizando **caracteres coringa**. a *shell* busca arquivos reais (existentes no sistema) que correspondam ao padrão e substitui esse padrão pelos nomes encontrados antes da execução. Os caracteres coringa são:

- `?`: Arquivo possui um caractere único e obrigatório no lugar do curinga. Por exemplo, `ls doc?.txt` lista arquivos como "doc1.txt" ou "docA.txt", mas ignora "doc12.txt". Já um `cat ?` tentaria ler o conteúdo de todos os arquivos do diretório cujo nome tenha apenas um único caractere.
- `*`: O arquivo possui qualquer sequência de caracteres (ou nenhum caractere). Por exemplo, `rm *.tmp` apaga todos os arquivos que terminam com ".tmp". Um `cat memo*` mostra o conteúdo de todos os arquivos que começam com "memo" (independentemente de como terminem), enquanto `ls *20*` lista arquivos que possuem "20" em qualquer parte do nome.
> Tanto o `?` quanto o `*` ignoram arquivos ocultos (que começam com um ponto `.`) por padrão. Para listá-los, é preciso colocar o ponto de forma explícita, como em `ls .*`.
- `[caracteres]`: Substitui um único caractere que faça parte do conjunto delimitado nos colchetes. Pode-se usar hifens para definir faixas, como `[0-9]` ou `[a-z]`, ou indicar apenas caracteres específicos, como `[159]`. Por exemplo, `less relatorio_[123].pdf` abre para leitura apenas os relatórios 1, 2 ou 3. Já `ls img_[a-d].png` lista imagens de "img_a.png" até "img_d.png".
> `[!caracteres]` ou `[^caracteres]`: Atua como uma negação do conjunto. Substitui um caractere único que **não** esteja listado nos colchetes. Por exemplo, `rm arquivo_v[^3].doc` apaga "arquivo_v1.doc", "arquivo_v2.doc", "arquivo_v4.doc", "arquivo_vl.doc", "arquivo_v$.doc", mas ignora o "arquivo_v3.doc".
- `[[:class:]]`: Substitui um único caractere que seja membro de uma classe pré-definida pelo sistema. Para usar, ele precisa estar dentro de um conjunto próprio de colchetes, resultando em algo como `ls doc_[[:digit:]].txt` (que lista "doc_1.txt", mas não "doc_A.txt"). As principais classes suportadas são:
  - ***alnum:*** Caracteres alfanuméricos (letras e dígitos).
  - ***alpha:*** Apenas letras do alfabeto.
  - ***blank:*** Caracteres em branco (espaços e tabulações).
  - ***cntrl:*** Caracteres de controle do terminal.
  - ***digit:*** Apenas números (dígitos).
  - ***graph:*** Caracteres gráficos (alfanuméricos e pontuações).
  - ***lower:*** Apenas letras minúsculas.
  - ***print:*** Caracteres imprimíveis (visíveis).
  - ***space:*** Caracteres de espaçamento (espaço, tabulação, quebra de linha, etc.).
  - ***upper:*** Apenas letras maiúsculas.
  - ***xdigit:*** Dígitos hexadecimais (0 a 9, A a F).

### Expansão Til (~):

O caractere til (`~`) atua como um atalho direto para os diretórios pessoais (`home`) dos usuários do sistema. Como comentado anteriormente, o til sozinho expande para o diretório pessoal do usuário atual (independentemente de quão fundo ele esteja no sistema), enquanto o til acompanhado do nome de um usuário existente (`~{usuario}`) expande para o diretório pessoal do usuário especificado.

### Expansão Aritmética:

Executa contas matemáticas (**exclusivamente com números inteiros**), retornando apenas o resultado final antes da execução do comando. A sintaxe consiste em colocar a operação dentro de dois parênteses precedidos por um cifrão, no formato `$(({expressão}))`.

As operações básicas suportadas são: Adição (`+`), Subtração (`-`), Multiplicação (`*`), Divisão (`/`), Módulo/Resto de divisão (`%`) e Exponenciação (`**`).
> Os espaços em branco dentro da expressão são ignorados e você pode usar parênteses simples internamente para organizar a ordem das operações (`$(((5**2)  *3))` vira 75).

### Expansão de Chaves:

Gera múltiplas *strings* (textos) a partir de um padrão definido entre chaves `{}`. Diferentemente dos caracteres coringa (que procuram arquivos que *já existem*), as chaves geram palavras do zero.

- **Valores específicos:** Você define exatamente os itens que deseja gerar. `mkdir pasta_{txt,pdf,img}` cria três pastas distintas chamadas "pasta_txt", "pasta_pdf" e "pasta_img".
> O padrão dentro das chaves **não pode conter espaços em branco** (`{A, B, C}` está errado).
- **Faixa de valores:** Diferentemente da expansão de arquivos (que usa o hífen), aqui são usados dois pontos finais (`..`) para criar sequências automáticas de números ou letras. `touch teste_v{1..5}` cria cinco arquivos de uma vez (de "teste_v1" até "teste_v5").

É possível **aninhar expansões** colocando chaves dentro de outras chaves para criar combinações complexas.

### Expansão de Parâmetros:

A *shell* é capaz de armazenar e operar **variáveis** (as do sistema convencionalmente são maiúsculas) que podem ser expandidas com o símbolo de cifrão (`$`) imediatamente antes do nome da variável. O `$USER` expande o nome do usuário atual, `$BASH` retorna o caminho do interpretador em uso, `$PWD` retorna o diretório atual, etc.
> Para ver uma lista completa das variáveis disponíveis na *shell*, basta executar os comandos `printenv` ou `set`.

## Substituição de Comandos:

A substituição de comandos permite capturar a saída (o resultado) de um comando inteiro e utilizá-la como texto diretamente na linha de comando de outra instrução (como parâmetros ou argumentos), envolvendo-o no formato `$({comando})`. Ao executar `ls -l $(which cp)`, a *shell* resolve primeiro o que está entre parênteses (o caminho do programa `cp`), captura esse resultado (`/bin/cp`), substitui-o na linha original e só então executa o comando externo (que se torna `ls -l /bin/cp`).

## *Escapes*:

Como dito, a *shell* interpreta as expansões automaticamente e separa argumentos (espaços extras são ignorados e cada palavra é um argumento). Para que a *shell* ignore essas regras e leia o texto exatamente como foi digitado (como um nome de arquivo que possui espaços ou um texto que contenha um cifrão literal), são utilizados os ***escapes***.

- **Aspas Duplas (`" "`):** Desativam a maioria das expansões (como as de arquivos, chaves e til) e impedem que os espaços separem as palavras, tratando tudo dentro delas como um bloco único. Porém, elas **ainda permitem** a expansão do cifrão (`$`), da crase (`` ` ``) e da barra invertida (`\`). Em `echo "O usuário $USER acessou o arquivo"`, os espaços extras são mantidos intactos, e a variável `$USER` ainda é traduzida para o nome do usuário.
- **Aspas Simples (`' '`):** É o bloqueio total. Desativam **todas** as expansões e substituições (tudo vira texto puro). `echo 'O usuário $USER'` vai imprimir "O usuário $USER".
> **Barra Invertida (`\`):** Não é propriamente um *escape*, mas um operador que isola **um único caractere** imediatamente posterior e indica que ele deve ser impresso na íntegra. `echo "O saldo é \$5.00"` informa à *shell* que o cifrão é apenas texto normal.

> A barra invertida também é usada com letras específicas para representar formatações de texto (como o `\n`, inspirado no `C`), mas, para sua interpretação correta, é necessário sinalizar com uma *flag* no comando (`echo -e`, por exemplo; o `echo` normal não interpreta).

> REVISADO ATÉ AQUI!

# 6. Variáveis

Como visto nas expansões, a *shell* permite armazenar valores em palavras e recuperar esse conteúdo através da expansão de parâmetros (usando o símbolo `$`). Esse mecanismo é a base para o uso de **variáveis**, que podem ser tanto criadas pelo usuário quanto nativas do próprio sistema.

> A expansão de variáveis pode também ser feita por `${variavel_entre_chaves}`, com essa sintaxe trazendo algumas vantagens, como a possibilidade de concatenar *strings*.

```bash
$ first=primeira
$ second=${first}palavra
$ echo $second           #primeirapalavra
```

## Variáveis do Usuário:

Para declarar uma variável, assim como na maioria das linguagens de programação, o nome nunca pode começar com um número (permitido começar com *under-line*). Após o primeiro caractere, o nome pode conter letras, sublinhados e números (demais caracteres como barras (`/`) ou traços (`-`), asteriscos (*), etc. não são permitidos).

### Atribuição e Valores:

- `{variavel}={valor}` - Atribui `valor` à variável. Na atribuição, **não pode haver espaços** antes ou depois do sinal de igual (`X = 1` daria erro). Para armazenar *strings* com espaços, é necessário envolvê-la em aspas ou então utilizar o escape de espaço (`\ `).

- `{variavel}=` - Limpa o valor da variável (ela passa a armazenar um texto vazio, mas continua existindo).

- `unset {variavel}` - Remove a variável completamente da memória da *shell*.

> Na *shell*, **tudo é armazenado como *string*** (recursos internos permitem interpretar esses valores e realizar operações aritméticas, por exemplo, mas é tudo texto). Por conta disso, declarar uma variável como `X=1`, `X="1"` ou `X='1'` não faz diferença. Devido à interpretação da *shell* antes de executar o comando, caracteres especiais armazenados sofrem expansão e comandos armazenados podem ser acessados diretamente por variáveis.

```bash
$ var_1=script.*
$ echo $var_1   # script.c script.cpp script.py ...
$ echo "$var_1" # script.*
$ var_2=ls
$ $var2         # a.txt script.c script.cpp ...
```

### Escopo de Execução:

É possível definir uma variável que existirá apenas durante a execução de um comando ou *script* específico, sem criá-la ou alterá-la no *shell* atual. Para isso, basta declarar a atribuição seguida de um espaço e do comando.

- `{variavel}={valor} {comando}` - Passa a variável apenas para o ambiente do comando executado.

```bash
$ VAR="teste" ./meu_script.sh  # VAR só existe durante a execução do script
$ echo $VAR                    # Retorna vazio na shell atual
```

## Variáveis da Shell:

A *shell* possui diversas variáveis pré-definidas pelo sistema. Convencionalmente, elas são escritas com letras maiúsculas. 

Essas variáveis costumam atuar de forma global (também chamadas de variáveis de ambiente). Para que uma variável criada pelo usuário tenha esse mesmo comportamento (tornando-se visível para outros programas e *subshells* executados a partir dali) utiliza-se o comando `export`:

- `export {variavel}={valor}` - Cria (ou atualiza) uma variável, definindo-a como global. Para simplesmente fazer uma concatenação: `export variavel=${variavel_entre_chaves}valor_concatenado`

**Principais Variáveis Padrão:**

| Variável | Descrição |
| :--- | :--- |
| `$HOME` | Caminho do diretório pessoal do usuário atual (equivalente ao uso do `~`). |
| `$PATH` | Lista de diretórios (separados por `:`) onde a *shell* procura os programas executáveis. Ao digitar um comando como `ls`, ela verifica pasta por pasta até encontrá-lo (como em `/usr/bin`). |
| `$PS1` | Define o texto e a aparência do *prompt* principal do terminal (o que aparece antes de você digitar). |
| `$PS2` | Define o *prompt* secundário (normalmente um `>`, exibido quando a *shell* aguarda que você termine de digitar um comando na linha de baixo). |
| `$IFS` | Separador Interno de Campos (*Internal Field Separator*). Define os caracteres que a *shell* usa para separar palavras e itens (por padrão: espaço, tabulação e quebra de linha). |
| `$PWD` | Armazena o caminho absoluto do diretório de trabalho atual. |

> Se o valor de `$PWD` for manualmente alterado, a pasta de trabalho não mudará. Além disso, ao executar o comando `pwd`, o sistema consultará sua localização real e retornará o diretório correto, ignorando a alteração feita na variável (essa parte não é definida pela variável).

## Atributos de Variáveis:

- `declare -{atributo} {variavel}={valor}` - Atribui o valor com a propriedade especificada.

- `declare +{atributo} {variavel}` - Remove o atributo da variável (mantendo seu valor armazenado).

Existem vários atributos, mas as mais utilizadas são:
- **`-x` (Global):** Análogo ao uso do comando `export {variavel}`.
- **`-r` (Somente Leitura):** Protege a variável, impedindo mudanças nela após a declaração. Análogo ao uso do comando `readonly {variavel}`.

> O `-r` é irreversível durante a execução (uma vez aplicada, não é possível alterá-la, retirar o atributo com `+r` e nem apagar a variável com `unset`). Ela só deixará de existir quando a sessão atual da *shell* for encerrada.

Esses atributos não são exclusivos (um atributo pode ser global e *read only* ao mesmo tempo, por exemplo). 

> Os comando `declare` e `typeset` são equivalentes - permitem definir propriedades específicas para as variáveis.

### Isolamento e Herança de Variáveis:

Cada *shell* em execução atua como um processo independente na memória do sistema. Por isso, alterar ou criar uma variável em um terminal não afeta nem gera conflitos com outras *shells* abertas simultaneamente. 

Além disso, quando uma variável global é passada para uma nova *shell*, o sistema transfere apenas o seu nome e o seu valor. **Atributos de controle**, como o somente Leitura (`-r`), **pertencem apenas à *shell* onde foram criados e não são herdados**. Assim, a nova *shell* apenas recebe uma cópia da variável (modificá-la não altera a restrição ou o valor na *shell* original).

## Escopo e Localidade:

No entanto, as funções na *shell* não possuem isolamento padrão: elas compartilham as mesmas variáveis do restante do código. Isso significa que modificar uma variável dentro de uma função alterará seu valor globalmente, o que pode sobrescrever dados e causar conflitos indesejados.

Para restringir o escopo exclusivamente ao ambiente interno de uma função, utiliza-se o comando `local` (ou seu equivalente `typeset`) no momento da declaração. 

- `local {variavel}={valor}` - Cria uma variável que existirá apenas durante a execução da função atual. Isso protege as variáveis externas, garantindo que alterações internas não afetem o resto do *script*.

## *Arrays*:

- `{array}=(valor1 valor2 valor3)` - Cria um vetor unidimensional com índices iniciados em zero. O acesso aos índices é feito com uso de chaves junto ao expansor de parâmetro (`$`) (`${array[0]}`, por exemplo).

Para recuperar todos os itens de uma vez, utilizam-se os símbolos `@` ou `*`. Eles só apresentam diferença de comportamento quando envoltos em aspas duplas: `"${array[@]}"` expande cada elemento como uma palavra (argumento) independente, enquanto `"${array[*]}"` expande todos agrupados em uma única *string*, separados pelo primeiro caractere definido em `$IFS` (por padrão, o espaço). 

> Se utilizados sem aspas duplas (`${vetor[@]}` ou `${vetor[*]}`), possuem exatamente o mesmo efeito (elementos separados).

## Variáveis Especiais:

Informações dinâmicas sobre a execução dos comandos e do próprio terminal.

| Variável | Descrição |
| :--- | :--- |
|`$RANDOM` | Gera um número inteiro aleatório entre 0 e 32767 (15 *bits*). |
| `$$` | Retorna o *PID* (identificador) do processo atual. É útil para identificar exatamente qual *shell* ou *script* está rodando no momento. |
| `$!` | Retorna o *PID* do último processo colocado em plano de fundo (*background*). |
| `$?` | Armazena o *status* de saída (*exit status*) do último comando executado. |

> O valor de `$?` é atualizado após **cada** comando executado. Se um programa falhar e você rodar `echo $?` para verificar o erro, ele mostrará o código da falha. Porém, se você rodar `echo $?` uma segunda vez logo em seguida, o valor retornado será `0`, pois agora ele estará mostrando o *status* de sucesso do próprio comando `echo` anterior.

## Parâmetros:

### *Input*:

- `read {opções} {variaveis}` - Lê a entrada e atribui as palavras digitadas às variáveis informadas em sequência.

>  Por padrão, o *shell* usa os espaços em branco para separar a entrada. O `read` mais **uma variável** armazena a linha inteira (mesmo com os espaços). Com **múltiplas variáveis**, cada variável recebe uma palavra (se houver mais palavras do que variáveis, a **última variável da lista** armazenará todo o restante do texto, incluindo os espaços finais). Com **nenhuma variável**, toda a entrada do usuário será salva automaticamente na variável padrão `$REPLY`.

### Parâmetros Posicionais:

Parâmetros posicionais permitem referenciar os argumentos passados para um *script* ou função no momento de sua execução (comportamento análogo ao `argc` e `argv` da linguagem C).

| Variável | Descrição |
| :--- | :--- |
| `$#` | Retorna o número total de parâmetros passados na chamada do programa. |
| `$0` | Retorna o nome do programa ou *script* em execução. |
| `$1` a `${n}` | Retornam os argumentos específicos correspondentes à sua posição (primeiro argumento, segundo argumento, etc.). **A partir do décimo parâmetro, é obrigatório o uso de chaves para a expansão** (`${10}`, `${11}`, etc.). |
| `$@` e `$*` | Representam todos os argumentos de uma só vez. Seguem a mesma regra de expansão dos arranjos: entre aspas duplas, `"$@"` trata cada argumento de forma independente, enquanto `"$*"` os une em uma única *string*. |

### Manipulação de Parâmetros:
- `shift {n}` - Descarta os `n` primeiros parâmetros e desloca os restantes para a esquerda. Ao utilizar apenas `shift` (equivalente a `shift 1`), o antigo `$2` passa a ser o `$1`, o `$3` passa a ser o `$2`, e assim por diante. Pode ser chamado sucessivamente.
- `set {valores}` - Inicializa ou redefine os parâmetros posicionais de dentro do próprio *script*. É útil para atribuir valores padrão de forma automática caso o usuário não tenha passado nenhum argumento na execução, permitindo que o resto do código utilize as variáveis posicionais normalmente.

### Expansão de Variáveis:

O *shell* permite modificar o resultado de uma variável no próprio momento em que ela é chamada, aplicando valores padrão, testes condicionais e formatações diretamente no texto.

**Valores Padrão e Condicionais:**
Servem para precaver erros caso uma variável não tenha sido inicializada pelo usuário ou pelo sistema.
- `${variavel:-padrao}` - Retorna o `padrao` caso a variável esteja nula ou não definida. O valor da variável não é alterado.
- `${variavel:=padrao}` - Retorna e **atribui** o `padrao` à variável caso ela esteja nula ou vazia.
> **Comando Nulo (`:`):** Ao usar `:=`, a *shell* tentará executar o resultado como um comando. Para fazer uma atribuição silenciosa e evitar erros, utiliza-se o comando nulo `:` (que não faz nada e retorna sucesso) antes da expansão: `: ${variavel:=/tmp/padrao}`.
- `${variavel:?mensagem}` - Imprime a `mensagem` de erro na *shell* e encerra a execução (retornando *status* 1) caso a variável esteja nula.
- `${variavel:+padrao}` - Retorna o `padrao` **apenas se** a variável estiver definida e possuir algum valor. Muito usado para adicionar *flags* ou prefixos condicionais apenas quando o dado existe.

**Manipulação de Texto:**
Permite fatiar, substituir e medir *strings* sem a necessidade de recorrer a programas externos complexos.
- `${#variavel}` - Retorna o tamanho (número de caracteres) da *string* armazenada. Se usada com um arranjo (`${#vetor[@]}`), retorna o número de elementos contidos nele.
- `${!variavel}` - Expansão indireta. Trata o valor armazenado na primeira variável como se fosse o nome de outra variável, retornando o conteúdo desta última (funciona como uma desreferenciação de ponteiro).
- `${variavel:offset:tamanho}` - Retorna um pedaço do texto (fatiamento). Começa a leitura a partir da posição `offset` (iniciada em zero) e captura a quantidade de caracteres definida em `tamanho` (se o tamanho for omitido, vai até o final da *string*).
- `${variavel/antigo/novo}` - Substitui a primeira ocorrência do texto `antigo` pelo texto `novo`. (Para substituir todas as ocorrências de uma vez, utiliza-se duas barras: `${variavel//antigo/novo}`).
- `${variavel#prefixo}` - Remove o padrão especificado (`prefixo`) do **início** do texto.
- `${variavel%sufixo}` - Remove o padrão especificado (`sufixo`) do **final** do texto (muito útil para remover extensões de arquivos, como em `${arquivo%.txt}`).

# 7. Estruturas de Fluxo Condicional

Na *shell*, as estruturas condicionais avaliam o ***status* de saída** (*exit status*) dos comandos executados. Como convenção (semelhante à linguagem C), o valor `0` indica sucesso, enquanto qualquer valor de `1` a `255` representa uma falha ou condição falsa.

> A *shell* possui dois comandos nativos criados exclusivamente para gerar esses retornos: o `true` (que sempre retorna o *status* `0`) e o `false` (que sempre retorna `1`).

## Testadores de expressões:

- `test {expressão}` - Avalia expressões lógicas e gera um *status* de saída correspondente. Esses comandos suportam diversos operadores (facilmente consultáveis nos manuais da *shell*) para realizar três categorias principais de testes:

  - **Arquivos e Diretórios:** Verifica a existência e o tipo do arquivo (diretório, bloco, caractere, *pipe* nomeado), a presença de atributos especiais (como o *sticky bit*), ou compara dois arquivos (qual é mais novo, ou se apontam para o mesmo *inode*).
  - **Textos (*Strings*):** Verifica se uma variável está nula/vazia, se duas *strings* são exatamente iguais, ou compara textos caractere a caractere (para saber qual é maior ou menor na ordem alfabética).
  - **Números Inteiros:** Realiza comparações matemáticas tradicionais (igualdade, maior, menor, etc.).

- `[ {expressão} ]` - Funcionamento idêntico ao `test`.

> O colchete de abertura (`[`) não é apenas um delimitador sintático, mas sim **um programa real** do sistema. Por ser um comando, ele exige que a expressão e o argumento final (o colchete de fechamento `]`) sejam passados separadamente. Portanto, é obrigatório haver espaços ao redor da expressão. Escrever `[{expressão}]` colado causará um erro de comando não encontrado.

- `[[ {expressão} ]]` - Atua de forma análoga ao colchete simples (sendo retrocompatível com ele), mas é uma palavra reservada da própria *shell* que adiciona recursos poderosos e simplifica a sintaxe, como por exemplo, o uso do operador `==` para casamento de padrões (utilizando as mesmas regras de expansão de arquivos da *shell*) e o uso de expressões regulares diretas na *string* com `=~`, comparações numéricas utilizando operadores como `>`, `>=`, etc.

> Por convenção e compatibilidade, ele exige o mesmo padrão de espaçamento interno do colchete simples.

- `(( {expressão_com_números_inteiros} ))` - Utilizado exclusivamente para avaliações e testes matemáticos com números inteiros. Ele resolve o problema do teste de textos (onde a *string* "10" seria considerada menor que "9" em uma verificação caractere a caractere). Além disso, suporta a sintaxe tradicional de operadores lógicos de linguagens como C e C++: `&&` (AND), `||` (OR) e `!` (NOT).

> Não exige espaços internos obrigatórios (diferente dos colchetes).

## Operadores Condicionais:

### `if`, `elif` e `else`:

Avalia o *status* de saída de um comando principal para decidir qual bloco de código será executado.

```bash
if {comando_testado}; then
    {comandos_caso_sucesso}
elif {outro_comando}; then
    {comandos_caso_sucesso_alternativo}
else
    {comandos_caso_falha}
fi
```

### `case`:

Testa uma *string* comparando-a com uma série de padrões até encontrar uma correspondência exata. Por suportar o casamento de padrões, é possível usar a barra vertical (`|`) para definir alternativas, o ponto de interrogação (`?`) para identificar um caractere único qualquer ou até mesmo colchetes (`[ ]`) para classes de caracteres.

```bash
case {string} in
    padrao_1 | padrao_2)
        {comandos}
        ;;
    padrao_3)
        {comandos}
        ;;
    *)  # Caso default - Representa qualquer sequência de caracteres, por isso funciona como default.
        {comandos_padrao}
        ;;
esac
```

O `case` não suporta cascata (como é possível fazer em C), onde, se dois casos geram o mesmo resultado, eles tem que ser definidos com os casamentos de padrões expecificados.

> A avaliação ocorre sempre de cima para baixo. O asterisco (`*`), se colocado como a primeira opção no topo do `case`, a *shell* sempre entrará nele e nunca testará as alternativas abaixo.

## Operadores de Curto-Circuito:

Atuam como controladores de fluxo diretos para encadear comandos, baseando-se no fato de que a *shell* não faz avaliações desnecessárias.

- `{comando_1} && {comando_2}` - Lógica *AND*. O `comando_2` só é executado se o `comando_1` terminar com sucesso (status 0). Se o primeiro falhar, a lógica *AND* inteira já é considerada falsa, e o segundo comando é ignorado.

- `{comando_1} || {comando_2}` - Lógica *OR*. O `comando_2` só é executado se o `comando_1` falhar (status diferente de 0). Se o primeiro der sucesso, a lógica *OR* já está satisfeita na primeira tentativa, encerrando a execução ali mesmo.

---

# Fontes

- ALVARES, Andrei Rimsa. Disciplina: Tópicos Especiais em Fundamentos da Computação: *Shell Scripting*. Curso de graduação em Engenharia de Computação - Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2026.
- FREE SOFTWARE FOUNDATION. *GNU Bash Reference Manual*. Edição 5.3, para Bash 5.3. [S. l.], 2025. Disponível em: [https://www.gnu.org/software/bash/manual/bash.html](https://www.gnu.org/software/bash/manual/bash.html). Acesso em: 18 ago. 2026.
- FREE SOFTWARE FOUNDATION. *GNU Coreutils*. Versão 9.11. [S. l.], 2026. Disponível em: [https://www.gnu.org/software/coreutils/manual/coreutils.html](https://www.gnu.org/software/coreutils/manual/coreutils.html). Acesso em: 4 set. 2026.
- THE LINUX MAN-PAGES PROJECT. *Linux man-pages*. Versão 6.19. [S. l.], 2026. Disponível em: [https://www.kernel.org/pub/linux/docs/man-pages/man-pages-6.19.tar.xz](https://www.kernel.org/pub/linux/docs/man-pages/man-pages-6.19.tar.xz). Acesso em: 4 set. 2026.
- THE KERNEL DEVELOPMENT COMMUNITY. *The Linux kernel documentation*. [S. l.], [s. d.]. Disponível em: [https://docs.kernel.org/](https://docs.kernel.org/). Acesso em: 4 set. 2026.
