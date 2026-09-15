```
 _____  _       _                                 
/  ___|(_)     | |                                
\ `--.  _  ___ | |_   ___  _ __ ___    __ _  ___  
 `--. \| |/ __|| __| / _ \| '_ ` _ \  / _` |/ __| 
/\__/ /| |\__ \| |_ |  __/| | | | | || (_| |\__ \ 
\____/ |_||___/ \__| \___||_| |_| |_| \__,_||___/  
 _____                                  _                       _      
|  _  |                                (_)                     (_)
| | | | _ __    ___  _ __   __ _   ___  _   ___   _ __    __ _  _  ___ 
| | | || '_ \  / _ \| '__| / _` | / __|| | / _ \ | '_ \  / _` || |/ __|
\ \_/ /| |_) ||  __/| |   | (_| || (__ | || (_) || | | || (_| || |\__ \
 \___/ | .__/  \___||_|    \__,_| \___||_| \___/ |_| |_| \__,_||_||___/
       | |                                                             
       |_|                                                             
```

# 00. Conceitos Básicos

## O que é um Sistema Operacional:

Pode ser definido como uma camada de software que opera entre o hardware e os programas. Entre suas fuções está:

- **Abstração:** Simplifica funções complexas, provendo uma interface simples, homogênea e universal (aplicativos e dispositivos funcionam independentemente do hardware).
- **Gerência:** Gerencia os uso processador pelos programas (tempo de uso e prioridade), a alocação de memória das aplicações, e segurança das aplicações

## Tipos de Sistemas Operacionais:

- *Batch*: Executa tarefas sequenciais (transações, etc.);
- *De rede*: Acessa recursos em outros computadores;
- *Distribuído*: Acessa recursos de forma transparente;
- *Multiusuário*: Cada recurso tem um “dono” e regras de acesso;
- *Servidor*: Gestão eficiente de grandes volumes de recursos;
- *Desktop*: Interface gráfica e suporte à interatividade;
- *Móvel*: Gestão de energia, conectividade e sensores;
- *Embarcado*: Hardware com poucos recursos e energia;
- *Tempo real*: Tem comportamento temporal previsível (pode ser *soft real-time* ou *hard real-time*);
> SOs modernos não se limitam a apenas uma dessas configurações, combinando várias.

## Estrutura de um Sistema Operacional:

O sistema operacional se encontra exatamente na interseção entre o hardware (componentes físicos) e o software (aplicativos). Os principais componentes de sua estrutura são:

- Núcleo (*Kernel*): Responsável pela gerência de recursos do hardware, além de fornecer abstrações e ser utilizado ativamente pelas aplicações.
- Inicialização (*Boot*): Reconhece os dispositivos instalados na máquina e é responsável por carregar o núcleo do sistema para a memória principal.
- *Drivers*: Módulos de código específicos utilizados para acessar, comunicar e controlar dispositivos físicos externos.
- Utilitários: Funcionalidades e ferramentas complementares do sistema, englobando recursos como formatação de disco, *shell* (interpretador de comandos), interface com o usuário, entre outros.

![Estrutura de um SO](images/screenshot001.png)<br>
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 14.*

### Políticas e Mecanismos:

No desenvolvimento da estrutura do sistema operacional, é importante separar esses dois conceitos:

- **Política:** É um aspecto abstrato e de alto nível, responsável por tomar decisões (como definir a quantidade de memória alocada para as aplicações, o objetivo de um pacote, etc.). A política deve ser abstrata e totalmente independente dos mecanismos.

- **Mecanismos:** São os procedimentos de baixo nível que realmente realizam e executam as decisões tomadas pelas políticas. Os mecanismos devem ser genéricos para que possam ser reaproveitados e reutilizados pelo sistema.

### Endereço de Dispositivos:

Dispositivos como teclado, barramento de IDE e porta serial são, em sua essência, interpretações feitas através de drivers que podem ser acessadas em endereços físicos de acesso.

## Desvios:

O hardware pode desviar o fluxo de execução de um processo em algum dos três eventos:

- **Interrupção:** Desvia a execução para um evento externo gerado por um periférico.
- **Exceção:** Desvia a execução para um evento interno, erro numérico, falha na alocação de memória, etc.
- **Trap:** Desvia a execução a pedido do software.

![Exemplo de interrupção](images/screenshot002.png)
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 18.*

1. Execução de um processo;
2. Um pacote vindo da rede é recebido pela placa *Ethernet*;
3. O controlador *Ethernet* envia uma IRq ao processador;
4. O processamento é desviado para a rotina de tratamento da interrupção;
5. A rotina de tratamento transfere os dados do pacote do controlador para a memória;
6. A rotina de tratamento finaliza e o processador retorna à execução do programa.

## Nível de privilégio:

Embora os sistemas operacionais modernos possuam quatro níveis de privilégio, comumente só são utilizados dois:

- **Nível mais baixo:** Reservado às aplicações.
- **Nível mais alto:** Reservado ao núcleo (*kernel*) do sistema operacional.

## Chamadas de sistema:

Os aplicativos conseguem utilizar os recursos do hardware através de **chamadas de sistema** (*syscalls*). A partir delas, eles conseguem executar operações restritas ao SO, como ler e fechar arquivos, enviar e receber dados através da rede, ler o teclado, escrever na tela, etc.

![Exemplo de *syscall*](images/screenshot003.png)
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 23.*

---

# 01. Arquitetura de Sistemas Operacionais

As arquiteturas são classificadas com base em sua estruturação interna, na divisão de tarefas, no nível de privilégio concedido às aplicações e na disposição dos seus componentes.

## Divisão do Núcleo:

- **Sistemas monolíticos:** Todo o núcleo roda em modo privilegiado sem restrições. Todas as funções que ficam por conta do sistema operacional estão embutidas dentro do núcleo do software (ex: gerência de processos, alocador de memória, sistema de arquivos, sistema de rede, troca de contexto, drivers, etc.). Possui **alto desempenho**, apesar de ser extremamente **complexo** e **frágil** (uma rotina errada pode quebrar completamente o sistema operacional).

- **Sistema micronúcleo:** O núcleo fica responsável única e exclusivamente por funções extremamente importantes, enquanto **outras funções operam fora dele**. Dentro do núcleo ficam recursos como espaço de memória, tarefas e comunicação entre tarefas. Fora do núcleo ficam alocadas a política de escalonamento, política de memória, sistema de arquivos, protocolos de rede, etc. Embobra extremamente **estável** (fácil de manter, devido a modulação), o seu **desempenho é extremamente baixo**.

- **Sistema em camadas:** O núcleo é organizado em camadas de abstração que subdividem as responsabilidades de cada uma.

    1. **Camada inferior:** É a interface de mais baixo nível, que faz realmente a comunicação com o hardware.
    2. **Camadas intermediárias:** São focadas principalmente em abstração e gerência.
    3. **Camada superior:** Define as chamadas de sistema, que são a forma de alto nível de se comunicar com o hardware.
    
Apesar da sua organização, a **burocracia de comunicação** entre as camadas a torna ineficiente na prática.

- **Sistemas híbridos:** Misturam as características dos modelos anteriores, utilizando cada um onde é mais vantajoso. Agrupam, por exemplo, rotinas que são muito interligadas para manter a eficiência, porém mantendo um certo nível de abstração para manter a organização e a comunicação *software*/*hardware* eficiente, e a modularidade para facilitar a manutenção.

## Virtualização:

- **Máquinas Virtuais:** Consistem em uma camada de *software* ou *hardware* que constrói uma interface para que um outro sistema consiga rodar por cima. Alguns desses componentes são:

    1. **Sistema real (*Host*):** Contém os recursos reais de hardware para a utilização do sistema.
    2. **Camada de virtualização (Hipervisor):** Realmente constrói a máquina virtual a partir dos recursos do sistema host.
    
        - **Hipervisor de Aplicação:** Suporta apenas uma única aplicação de uma linguagem específica, abstraindo o sistema operacional base (**JVM** do Java, **CLR** do C#, etc.).
        - **Hipervisor de Sistemas:** Suporta a execução de um sistema operacional inteiro. Pode executar diretamente sobre o hardware, sem um sistema operacional intermediário (**Hipervisor Nativo**, como Xen, VMware ESXi), ou então executar sobre um sistema operacional hospedeir (**Hipervisor Convidado**, como VirtualBox, VMware Workstation, etc.).
        
        ![Hipervisor Nativo x Convidado](images/screenshot004.png)
        *Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 33.*
    
    3. **Sistema virtual (*Guest*):** O sistema simulado em si.

- **Containers:** Consistem em uma forma de isolar as aplicações e os sistemas operacionais, virtualizando o espaço (em nível de sistema operacional). O sistema é dividido em áreas restritas, denominadas domínios (ou *containers*).

    - **Isolamento e Recursos:** Cada domínio é isolado do outro, não existindo (por padrão) comunicação interdomínios. O hardware é dividido e limitado para cada um (como quantidade de memória, tempo de processador, espaço em disco, etc.).
    - **Vantagem em relação às Máquinas Virtuais:** Não é necessário fazer a alocação e o carregamento de um sistema operacional inteiro para cada instância. O *container* empacota apenas a aplicação e os recursos/bibliotecas estritamente necessários para o seu funcionamento, sendo, de certa forma, muito mais leve e eficiente para esse propósito.
    
## Tipos avançados de Sistemas Operacionais:

- **Sistema Exonúcleo:** Criado para tornar a comunicação entre *software* e *hardware* mais eficiente e direta, diminuindo as camadas de abstração e favorecendo o desempenho.

    - **Gerência direta:** A comunicação e os recursos tendem a ser gerenciados diretamente pela aplicação, que fica responsável por fazer a utilização e paginação de memória, o controle dos blocos do disco, a interface com a rede, etc.
    - **Indicação de uso:** É o modelo utilizado por sistemas que exigem um desempenho extremamente alto e uma adequação direta ao hardware e aos dispositivos (como servidores web de alta performance, compiladores, etc.).
    
- **Unikernels:** É um modelo focado em extrema eficiência e segurança, onde cada aplicação individual já funciona como seu sistema operacional individual, com seus *drivers*, bibliotecas, etc., sendo muito utilizado em computação em nuvem e microsserviços. Lembra muito um containers, todavia, sem um sistema operacional tradicional unificando tudo (mais parecido com um sistema de programas embarcados autossuficientes).

    - **Características:** Possui apenas um espaço de endereçamento e roda estritamente um único processo.
    - **Vantagens:** Tempo de inicialização quase instantâneo, uso mínimo de memória e altíssima segurança (por não possuir um *shell*, utilitários ou processos secundários, a superfície de ataque para invasores é drasticamente reduzida).
    
    ![Sistema Unikernel](images/screenshot005.png)
    *Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 36.*
    
---

# 02. Tarefas

## Programa x Tarefa:

- **Programa:** Trata-se de um código, uma sequência de instruções estática e sem estado interno.
- **Tarefa:** É como o programa é executado realmente pelo sistema operacional. Trata-se da sua subdivisão em partes dinâmicas que podem ser reorganizadas, reordenadas e executadas de forma paralela, sequencial, em várias *threads*, etc.

## Número de Tarefas:

O sistema pode gerenciar essas tarefas de três maneiras principais:

### 1. Sistema Monotarefa:

É o sistema mais arcaico de gerenciamento de tarefas. Executa uma tarefa de cada vez, seguindo a ordem:

1. O programa é carregado para a memória.
2. Os dados do programa são carregados.
3. O programa executa até a sua conclusão.
4. Os resultados do programa são salvos.
5. O ciclo se repete indefinidamente até o fim da execução de todos os programas.

### 2. Sistema Multitarefa:

Ao invés de esperar a execução de uma tarefa por vez, o tempo ocioso do processador (como a espera por um dado da memória ou um *input* de entrada) é utilizado para fazer a intercalação entre várias aplicações.

- **Otimização:** Entre a execução de uma tarefa e o tempo em que outra está ociosa, o sistema fica sempre alternando as execuções, tentando utilizar o máximo de tempo possível da CPU.
- **Problema:** Pode exibir mau funcionamento por conta de aplicações bloqueadas (em um laço infinito, por exemplo) ou operações interativas não muito bem otimizadas para serem responsivas. Nesses casos, pode não ocorrer a troca de tarefa, pois, na teoria, a CPU está sempre ocupada executando a instrução problemática.

### 3. Sistema de Tempo Compartilhado (*Time Sharing* / Preempção por tempo):

Cada tarefa recebe uma fatia de tempo máxima que pode executar na CPU de cada vez (normalmente entre 10 milissegundos e 200 milissegundos).

- **Troca obrigatória:** Ao atingir esse limite de tempo, a tarefa sofre **preempção**, ou seja, é obrigada a parar sua execução e ceder o processador para outra.
- **Prioridade:** O tempo de preempção que cada tarefa recebe depende do seu nível de prioridade (tarefas com maior importância recebem fatias de tempo maiores).

## Gestão de tarefas:

Para gerenciar as tarefas e garantir que a sua troca não altere de alguma maneira o funcionamento do programa, ou que os seus dados sejam corrompidos, alguns elementos são importantes:

### Contexto de execução:

Tratam-se dos elementos que são modificados durante a execução da tarefa, que marcam os seus pontos de execução para que seja fácil retomá-la durante a sua alternância. Alguns desses elementos incluem:

- **Posição atual da execução:** Regulada pelos registradores *Program Counter* (PC) e *Stack Pointer* (SP).
- **Valores das variáveis e áreas de memória.**
- **Arquivos abertos, conexões de rede, etc.**

### TCB (*Task Control Block*):

É o bloco de controle (identificador) da tarefa, responsável por fazer o gerenciamento de todo o seu contexto.

- **Informações armazenadas:** Mantém o estado da tarefa, os registradores utilizados, os recursos alocados, etc.
- **Implementação:** Nos sistemas operacionais, é implementado normalmente como uma simples `struct` na linguagem C.

### Troca de contexto:
É a ação de trocar a execução de uma tarefa por outra, sendo o mecanismo base utilizado nos sistemas multitarefa. O processo ocorre nas seguintes etapas sequenciais:

1. Salva o contexto da tarefa atualmente em execução (no seu respectivo TCB).
2. Escolhe a próxima tarefa a ser executada.
3. Restaura o contexto da próxima tarefa quando ela assumir o processador.

Existem duas entidades principais envolvidas na troca de contexto:

- **Despachante (*Dispatcher*):** Realiza efetivamente a troca de contexto em baixo nível.
- **Escalonador (*Scheduler*):** Avalia as tarefas prontas e define a ordem de execução.

## Processos:

Um processo é um *container* de recursos utilizado para executar as tarefas. 

- **Componentes:** Contém áreas de memória (códigos, dados, pilha, etc.), descritores de recursos (arquivos, *sockets*, etc.) e uma ou mais tarefas em execução.
- **Isolamento:** Os processos são isolados entre si. Esse isolamento envolve as áreas de memória (MMU) e os níveis de operação da CPU (*kernel*/*user*).

Um novo processo não surge do vazio; ele é sempre criado a partir de um processo pré-existente, formando uma hierarquia em formato de árvore.

- **Duplicação (*Fork*):** Um processo pode ser duplicado a partir de outro. O processo que solicita a criação é denominado **Processo Pai** (*Parent*), e o novo processo gerado é o **Processo Filho** (*Child*). O filho nasce como uma cópia exata do pai (compartilhando o mesmo estado inicial), mas recebe um identificador único no sistema.
- **Substituição de Código (*Exec*):** O código de um processo pode ser inteiramente substituído pelo executável de outro programa. Normalmente, logo após um processo filho ser criado por duplicação, ele utiliza essa chamada para descartar a cópia do código do pai e carregar o seu próprio código na memória.
- **Hierarquia:** Como um processo pode criar outros processos (que também podem gerar novos filhos), estabelece-se uma relação de dependência e controle (onde o pai geralmente monitora o estado de execução ou o encerramento de seus filhos).

![Implementação de um *fork*](images/screenshot006.png)
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 56.*

## *Threads*:

Trata-se do fluxo de execução dentro de um processo ou dentro do núcleo do sistema operacional. 

- **Dentro do processo:** Cada subdivisão ou linha de execução dentro de um processo pode ser uma *thread*. Isso permite que um único processo realize múltiplas tarefas simultaneamente (compartilhando as mesmas áreas de memória e recursos do processo).
- **No Sistema Operacional:** Seguindo essa lógica, a execução real que o sistema operacional gerencia e envia para o processador é, na essência, uma *thread* (lembrando que todo processo possui, no mínimo, uma *thread* principal de execução).

### Modelos de implementação de *Threads*:

- **Modelo N:1:** Cada processo possui várias *threads* internas (nível de usuário), mas apenas uma é mapeada e executada de cada vez no *kernel*. Garante uma grande **escalabilidade**, permitindo que uma aplicação tenha milhares ou milhões de *threads* sem sobrecarregar o sistema. Todavia, é um modelo **frágil e pouco confiável**. Se uma *thread* realizar uma operação bloqueante (ou cair), ela bloqueia todas as outras *threads* do processo.

- **Modelo 1:1:** Cada *thread* do processo corresponde a uma *thread* direta no *kernel* (modelo mais usado nos sistemas operacionais contemporâneos. Possui um **tratamento mais robusto** e **escalonamento independente**. As *threads* conseguem utilizar vários processadores simultaneamente, permitindo que sejam realmente executadas em paralelo. É **pouco escalável**, tendo em vista que **exige muitos recursos do sistema e do hardware** para gerenciar e manter essa simultaneidade.

- **Modelo N:M:** Existe um sistema de gerenciamento para saber quais *threads* vão realizar a ocupação do hardware dinamicamente. Um processo com três *threads* pode utilizar só um núcleo, por exemplo, enquanto outro com três *threads* pode utilizar todas as três ao mesmo tempo. Possui **gerenciamento mais dinâmico e flexível** (meio termo entre escalabilidade e paralelismo). Apesar disso, não é tão adotado por sua **maior complexidade de implementação** e por gerar um maior **custo de gerência** no mapeamento das *threads* em relação aos núcleos.

## Escalonamento de tarefas:

Responsável por definir a ordem de execução das tarefas prontas.

### Critérios de escalonamento:

- **Tempo de vida (ou *Turnaround*):** Tempo entre a criação de uma tarefa e o seu encerramento.
- **Tempo de espera:** Tempo perdido pela tarefa na fila de tarefas prontas.
- **Tempo de resposta:** Tempo entre a chegada de um evento ao sistema e a resposta a ele.
- **Justiça:** Distribuição adequada do uso do processador entre as tarefas.

### Algoritmos de escalonamento:

- **FCFS (*First-Come, First-Served*):** Executa as tarefas estritamente à medida que elas chegam (ordem de chegada).

![Escalonamento FCFS](images/screenshot007.png)
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 73.*

- ***Round Robin* (Revezamento):** Utiliza preempção de tempo. Faz a troca da execução das tarefas, intercalando-as e considerando a ordem em que foram colocadas na fila de prontas.

![Escalonamento *Round Robin*](images/screenshot008.png)
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 74.*

- **SJF (*Shortest Job First*):** Prioriza sempre a tarefa que possui o menor trabalho (tempo total de execução) a ser feito.

![Escalonamento SJF](images/screenshot009.png)
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 76.*

- **SRTF (*Shortest Remaining Time First*):** Executa a tarefa que possui o menor tempo restante para conclusão. Pode interromper (sofrer preempção) o funcionamento da tarefa atual caso chegue uma nova tarefa ainda menor na fila.

![Escalonamento SRTF](images/screenshot010.png)
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 77.*

> Na prática, esses dois últimos modelos são amplamente teóricos ou implementados através de estimativas (heurísticas). Como não é possível saber com precisão absoluta quanto tempo uma tarefa demorará para ser concluída antes dela acabar, o sistema tenta "chutar" e prever esse tempo com base no histórico de execução.

- **PRIOc (Prioridade Cooperativa):** Utiliza um sistema de prioridade entre as tarefas, onde a ordem de execução é definida pela sua importância. Por ser cooperativa, a tarefa em execução não sofre interrupção, indo até o fim ou até bloquear.

![Escalonamento PRIOc](images/screenshot011.png)
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 78.*

- **PRIOp (Prioridade Preemptiva):** Parecido com o modelo cooperativo, porém utiliza preempção. Se entra uma tarefa de maior prioridade na fila de prontas, a execução atual é pausada para realizar a execução dessa nova tarefa mais prioritária.

![Escalonamento PRIOp](images/screenshot012.png)
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 78.*

- **PRIOd (Prioridade Dinâmica):** Diferente dos outros métodos de prioridade estática, neste modelo a prioridade é dinâmica e se ajusta ao longo do tempo.

    - **Problema evitado (*Starvation* / Inanição):** Impede que tarefas de menor prioridade fiquem indefinidamente sem tempo de CPU devido à chegada constante de tarefas de maior prioridade.
    - **Mecanismo (*Aging* / Envelhecimento):** A prioridade da tarefa vai aumentando gradativamente enquanto ela aguarda na fila.
    - **Retorno ao padrão:** Quando a tarefa finalmente é executada, sua prioridade volta para o seu valor base original.
    
    ![Escalonamento PRIOd](images/screenshot013.png)
    *Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 80.*
    
**Comparação entre os algorítimos:**

![Comparação entre os algorítimos](images/screenshot014.png)
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 82.*

### Inversão de Prioridade:

Ocorre quando uma tarefa de alta prioridade é forçada a esperar por uma tarefa de menor prioridade devido ao compartilhamento de um recurso exclusivo (e é obrigatório que a tarefa de baixa prioridade termine o seu uso antes para manter a coerência dos dados).

![Inversão de prioridade](images/screenshot015.png)
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 88.*

### Protocolo de Herança de Prioridade:

Para evitar esse problema, supondo uma instrção de prioridade baixa gerando dependência em uma de prioridade alta: a prioridade da instrução detentora do recurso alvo da instrução de alta prioridade passa a ser alta também até terminar de utilizar o recurso (voltando ao normal).

**Exemplo Prático de Herança de Prioridade:**

![Protocolo de Herança de Prioridade](images/screenshot016.png)
*Fonte: MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos, 1ª ed., 2019, p. 88.*

1. **Tarefa Baixa** inicia a execução e adquire o recurso exclusivo (R).
2. **Tarefa Alta** inicia, solicita (R) e é bloqueada. 
3. **Mecanismo entra em ação:** A **Tarefa Baixa** herda temporariamente a prioridade da **Tarefa Alta**.
4. **Tarefa Média** não consegue interromper a **Tarefa Baixa** (pois esta agora está operando com prioridade "Alta").
5. **Tarefa Baixa** termina de usar (R), libera o recurso e sua prioridade volta a ser "Baixa".
6. **Tarefa Alta** imediatamente assume (R), executa até o fim e finaliza.
7. **Tarefa Média** executa e finaliza.
8. **Tarefa Baixa** retoma o controle da CPU para finalizar o restante do seu código.

---

# 03. Comunicação

A interação entre tarefas (comunicação) é uma característica essencial e extremamente benéfica quando se pensa no *design* de sistemas grandes e escaláveis, visto que permite:

- **Atendimento simultâneo:** Capacidade de atender a inúmeros usuários ao mesmo tempo.
- **Aceleração da execução:** Utilização eficiente de processadores *multicore* (com vários núcleos) para executar tarefas em paralelo.
- **Modularidade:** Permite dividir o sistema em módulos autônomos que cooperam e trabalham entre si.
- **Aplicações interativas:** Facilita a responsividade de programas complexos (como navegadores web e editores de texto) através da divisão do trabalho em várias *threads*.

A grande questão da comunicação e interação é fazer a gestão eficiente e segura das áreas compartilhadas, evitando que as tarefas interfiram negativamente umas nas outras. Os desafios envolvem gerenciar:

- **Área comum entre *threads*:** O controle do acesso simultâneo à mesma memória, já que *threads* do mesmo processo compartilham o mesmo espaço de endereçamento.
- **Área do núcleo (*Kernel*):** A comunicação entre diferentes processos (que são naturalmente isolados) utilizando áreas compartilhadas geridas pelo próprio sistema operacional.
- **Comunicação de Hardware:** A sincronização e troca de informações entre núcleos físicos de processamento diferentes.

## Tipos de comunicação:
A comunicação entre as tarefas pode ocorrer de duas maneiras principais:

- **Comunicação direta/indireta:** Na comunicação direta, o emissor (origem) envia os dados diretamente ao receptor (destino). Já na indireta, o emissor e o receptor se comunicam através de um meio ou canal. Os dados são enviados do emissor para o canal, e o receptor coleta (lê) as informações a partir desse canal. O canal de comunicação é categorizado com relação à sua capacidade (capacidade de o canal armazenar os dados em trânsito), confiabilidade (manter a integridade da mensagem) e número de participantes:

    1. **Capacidade:**

        - **Nula:** Não há armazenamento. A comunicação exige que emissor e receptor estejam prontos simultaneamente para uma transferência direta.
        - **Limitada:** O canal possui um *buffer* de tamanho finito, suportando uma quantidade limite de dados em trânsito.
        - **Ilimitada:** O *buffer* é potencialmente infinito, armazenando as mensagens continuamente enquanto o receptor não as consumir.

    2. **Confiabilidade:**
        
        - **Confiável:** O canal consegue transportar todos os dados até o destino mantendo a sua integridade e a ordem original de envio.
        - **Não confiável:** O canal não garante a entrega perfeita; os dados podem não chegar, podem chegar alterados ou em uma ordem invertida daquela em que foram enviados.
        
    3. **Número de Participantes:**
    
        - **1 para 1:** Um emissor e um receptor interagem diretamente através de um canal de comunicação.
        - **M para N:** Um ou mais emissores enviam mensagens para um ou mais receptores. Cada mensagem depositada no canal pode ser recebida/consumida por apenas um dos receptores (**modelo *mailbox***), ou cada mensagem enviada é recebida por todos os receptores conectados ao canal e filtrada (**canal de eventos**).
        
- **Comunicação síncrona/assíncrona:** As operações bloqueiam as tarefas envolvidas. Existe uma necessidade de sincronização (espera) entre as partes: o receptor precisa esperar até os dados ficarem prontos e chegarem, e o emissor precisa esperar o receptor estar pronto para efetivamente concluir o envio. Já na comunicação assíncrona emissor não é bloqueado. A partir do momento em que envia os dados (geralmente depositados em um canal ou *buffer*), o emissor fica livre para continuar sua execução. O receptor coleta essas informações no seu próprio tempo, concluindo a etapa de recepção.
- **Comunicação semi-síncrona:** As operações bloqueiam as tarefas apenas durante um prazo pré-definido (*timeout*). Funciona como um meio-termo entre o envio indiscriminado contínuo (assíncrono) e a sincronização forçada indefinida (síncrona), criando uma "janela de comunicação" com tempo limite para que a troca de dados ocorra.

# Fontes

- MAZIERO, Carlos A. Sistemas Operacionais: Conceitos e Mecanismos. 1. ed. Curitiba: Editora UFPR, 2019.
- MACHADO, Francis Berenger; MAIA, Luiz Paulo. Arquitetura de sistemas operacionais. 5. ed. Rio de Janeiro: LTC, 2013.
- ANDRADE, Michelle Hanne Soares de. Disciplina: Sistemas Operacionais. Curso de graduação em Engenharia de Computação – Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2026.
