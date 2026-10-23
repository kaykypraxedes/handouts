## Mapeamento de Endereços:

Para que a comunicação ocorra, não basta conhecer apenas o endereço IP do destino final. A camada de rede precisa repassar a informação para a camada de enlace, que, por sua vez, exige o endereço físico (MAC) para realizar a entrega local do quadro. O processo de descobrir qual MAC corresponde a qual IP é chamado de mapeamento.

Existem duas abordagens para realizar esse vínculo:

### Mapeamento Estático:

Consiste em manter uma tabela fixa contendo todos os endereços IP da rede e seus respectivos endereços MAC. Na prática, esse modelo é inviável por dois motivos principais:

- **Ineficiência:** Pesquisar e manter uma tabela gigantesca com todos os dispositivos consumiria muitos recursos e geraria um alto tempo de processamento (*overhead* de busca).
- **Rigidez:** Como a maioria dos endereços IP atuais é distribuída de forma dinâmica, a tabela estática ficaria desatualizada rapidamente a cada mudança de IP, quebrando a comunicação da rede.

### Mapeamento Dinâmico (Protocolo ARP):

É a solução eficiente e adotada como padrão nas redes atuais. Quando um dispositivo precisa enviar uma mensagem, mas não conhece o endereço físico do destinatário, ele descobre essa informação sob demanda utilizando o protocolo **ARP** (*Address Resolution Protocol*)[cite: 2]. 

O funcionamento depende do local onde o destinatário se encontra:

- **Na mesma rede local:** O computador de origem envia um quadro em *broadcast* (um "grito" para todos os dispositivos conectados àquela rede) perguntando: *"Qual endereço de enlace corresponde ao IP 'X'?"*. Todos recebem a pergunta, mas apenas a máquina que possui aquele IP responde diretamente, enviando o seu endereço MAC de volta.
- **Em redes diferentes (*Internet*):** Como a mensagem de *broadcast* do ARP "morre" no roteador e não atravessa para outras redes, o computador de origem percebe que o IP é externo e pergunta apenas: *"Qual é o MAC do meu Roteador (*Gateway*)?"*. Ao receber a resposta, ele entrega a mensagem ao roteador local, que se encarrega de encaminhá-la pelo resto do trajeto.

> Os dispositivos guardam as respostas do ARP temporariamente em uma tabela na memória (cache) para não precisarem perguntar a mesma coisa o tempo todo. É possível visualiza isso no terminal linux com o comando `ip neigh` (No Windows é o comando `arp -a`).

## IPs Públicos e Privados:

O modelo IPv4 foi projetado com uma capacidade de aproximadamente 4 bilhões de endereços ($2^{32}$). Considerando que a população mundial já ultrapassa a marca de 8 bilhões de pessoas e que cada indivíduo costuma utilizar múltiplos aparelhos conectados simultaneamente, tornou-se matematicamente impossível atribuir um endereço IP único e global para cada dispositivo existente.

Para contornar o esgotamento iminente dos endereços, foi elaborado o protocolo **NAT** (*Network Address Translation*). A sua solução consistiu em dividir os endereços IP em duas categorias de alcance:

- **IPs Públicos:** São endereços globais e exclusivos. Eles são os IPs que efetivamente aparecem e trafegam na *Internet* externa.
- **IPs Privados:** São endereços de uso exclusivamente local. Como eles não existem na *Internet* pública, podem ser repetidos livremente dentro de diferentes casas e empresas pelo mundo (desde que não se repitam dentro da mesma rede local).

Para organizar o endereçamento interno das redes, foram reservados três blocos de IPs privados, baseados nas antigas divisões por classes:

| Classe | Intervalo de Endereçamento |
| :--- | :--- |
| **A** | `10.0.0.0` até `10.255.255.255` |
| **B** | `172.16.0.0` até `172.31.255.255` |
| **C** | `192.168.0.0` até `192.168.255.255` |

### O Mecanismo de Tradução (Portas):

Quando um dispositivo local faz uma requisição para acessar a *Internet*, a comunicação não ocorre de forma direta. O dispositivo envia o pedido para o roteador, que atua como um representante da rede. O roteador substitui o IP privado original pelo seu próprio IP público e encaminha a requisição para fora.

Para saber exatamente a quem devolver a informação quando a resposta voltar da *Internet*, o roteador anota essa tradução em uma tabela interna. Ele utiliza as **portas de rede** (um recurso da Camada de Transporte que será explorado no próximo capítulo) para marcar e identificar qual aparelho específico da rede interna fez aquela solicitação.

## Endereçamento Multicast:

O endereçamento *Multicast* (referente à Classe D) é utilizado quando um emissor precisa enviar a mesma informação para um **grupo específico de usuários** simultaneamente. Ele resolve um grande problema: evita o esgotamento da rede (que ocorreria se o servidor enviasse uma cópia individual para cada usuário) e evita o tráfego desnecessário (que ocorreria se enviasse para todos da rede, como no *broadcast*).

Os endereços *Multicast* operam no intervalo de `224.0.0.0` até `239.255.255.255` ($2^{28}$ endereços) e possuem uma mecânica de funcionamento muito peculiar:

- Um dispositivo comum **não recebe** um IP *Multicast* como sua identidade (ele continua usando seu IP normal da Classe A, B ou C). Em vez disso, o IP *Multicast* atua como uma "frequência de rádio". O servidor transmite os dados para essa frequência (ex: `224.1.1.1`), e o computador do usuário utiliza um protocolo de controle (como o IGMP - *Internet Group Management Protocol*) para avisar ao roteador local.

> É como um rádio, onde se sintoniza a frequência, com os pacotes que chegarem nela sendo repassadas para o usuário.

A associação a essas "frequências" pode ocorrer de duas formas:

- **Grupos Temporários:** São criados dinamicamente no momento em que o primeiro dispositivo solicita a associação ("sintoniza") e são completamente eliminados e liberados quando o último dispositivo se desconecta.
- **Grupos Permanentes:** São fixos e reservados. Eles existem de forma contínua na rede, independentemente de haver dispositivos associados ("ouvindo") naquele exato momento ou não.

> Matematicamente, a Classe D possui cerca de 268 milhões de endereços. Se cada vídeo, *stream* na Twitch ou partida de jogo online global ganhasse um IP exclusivo, esses endereços se esgotariam rapidamente. Além disso, rotear mensagens *Multicast* na *Internet* pública provou ser complexo e instável demais. Por conta disso, serviços globais (como Netflix, YouTube, etc.) utilizam conexões *Unicast* (um-para-um) combinadas com milhares de servidores espalhados pelo mundo (CDNs). O *Multicast* verdadeiro brilha e é amplamente utilizado hoje em **redes fechadas e controladas**, como na transmissão de TV a cabo da sua operadora (*IPTV*), em sistemas internos de bolsas de valores ou em videoconferências corporativas.

## Roteamento:

O roteamento é a inteligência por trás da camada de rede. Trata-se da capacidade de encaminhar um pacote da sua origem até o seu destino final, saltando através de dispositivos intermediários (os **roteadores**).

Para que o pacote chegue ao destino corretamente, o roteamento depende de dois componentes essenciais:

- **Algoritmo de Roteamento:** É o "cérebro" matemático que calcula e define qual é o melhor caminho possível na topologia da rede.
- **Tabela de Roteamento:** É o "mapa" armazenado na memória do roteador. Ela lista as rotas conhecidas e indica por qual "porta de saída" (interface de rede) o pacote deve ser ejetado para seguir viagem.

> **Atualização Dinâmica:** Na *Internet* real, os roteadores "conversam" entre si periodicamente através de protocolos de roteamento. Eles trocam informações para atualizar suas tabelas em tempo real, descobrindo rotas mais rápidas ou desviando o tráfego caso um roteador vizinho seja desconectado ou sofra uma falha.

### Interfaces e a Rota Padrão (*Default Route*):

Um roteador possui múltiplas **interfaces de rede** (conexões físicas ou lógicas) para interligar redes distintas. Imagine uma linha conectando as redes `20`, `30`, `40` e `50` através de roteadores intermediários. 

Um roteador posicionado no meio desse caminho possui uma interface `1` (que aponta para a esquerda, em direção à rede `20`) e uma interface `2` (que aponta para a direita, em direção às demais redes). Uma tabela completa e exaustiva desse roteador precisaria mapear cada destino individualmente:

- Para a rede `20` ➔ Sair pela interface `1`
- Para a rede `30` ➔ Sair pela interface `2`
- Para a rede `40` ➔ Sair pela interface `2`
- Para a rede `50` ➔ Sair pela interface `2`

Em redes reais, que formam teias complexas com bilhões de caminhos possíveis (e não apenas linhas retas), possuir uma tabela listando cada rede existente no mundo exauriria a memória e o processamento do equipamento. A solução para otimizar isso é a utilização da **Rota Padrão** (*Default Route*).

**A Otimização na Prática:**
Em vez de decorar o mapa do mundo inteiro, o roteador memoriza apenas as redes vizinhas que ele conhece de forma específica e cria uma regra geral para todo o resto. A tabela do exemplo acima seria drasticamente reduzida para:

- Para a rede `20` ➔ Sair pela interface `1`
- **Para qualquer outra rede** (representado na tabela por `0.0.0.0`) ➔ Sair pela interface `2` (Rota Padrão)

Essa rota padrão atua como uma rota de escape. Se o roteador não conhece o destino específico de um pacote, ele simplesmente encaminha esse pacote para um roteador maior ou mais central (o seu *Gateway* Padrão), delegando a ele a responsabilidade de consultar uma tabela mais completa e achar o caminho final.

### Funções Básicas e Características dos Roteadores:

Os roteadores são os principais equipamentos de interconexão da Camada de Rede. Na prática, a sua operação diária se divide em três diretrizes fundamentais:

- **Manutenção de Informações de Roteamento:** É a inteligência do equipamento. Consiste na construção, manutenção e atualização constante de suas tabelas de rotas, conversando com outros roteadores para mapear a rede e descobrir os melhores caminhos disponíveis.
- **Encaminhamento de Pacotes (Comutação):** É a ação mecânica. O roteador recebe um pacote por uma interface de entrada, processa o IP de destino cruzando-o com a sua tabela de rotas e, por fim, despacha o pacote através da interface de saída adequada.
- **Serviços Adicionais:** Roteadores modernos acumulam funções extras vitais para a saúde da rede. Os principais exemplos são a execução do **NAT** (traduzindo IPs privados em públicos), a operação como **DHCP** (distribuindo IPs dinâmicos automaticamente) e a atuação como um **Firewall** básico (filtrando e bloqueando pacotes maliciosos).

> **A Contenção do Domínio de *Broadcast*:** Uma característica histórica e estrutural muito importante do roteador é a sua capacidade de barrar mensagens de *broadcast* (aquelas enviadas simultaneamente para todos os dispositivos). Antes da popularização dos *switches* capazes de criar redes virtuais (*VLANs*), os roteadores eram a principal ferramenta utilizada para separar fisicamente os departamentos de uma empresa. Como o "grito" de *broadcast* morre ao atingir a interface do roteador, ele impede que redes vizinhas sejam afogadas por tráfego irrelevante.

### Tipos de Roteadores:

Os dispositivos responsáveis por realizar o roteamento e o encaminhamento de pacotes podem ser divididos em duas categorias principais, dependendo da necessidade de desempenho e do cenário de aplicação:

- **Roteadores Especializados:** São equipamentos concebidos e construídos exclusivamente para a tarefa de roteamento, buscando o máximo de performance possível. Geralmente, possuem uma arquitetura interna e um sistema operacional proprietários. Seu grande diferencial é que as principais funções de decisão e encaminhamento são gravadas diretamente no *hardware* (chips dedicados), o que permite processar milhões de pacotes por segundo em altíssima velocidade.
- **Roteadores Não Especializados:** É basicamente um computador comum (de uso geral) equipado com pelo menos duas placas de rede e configurado para realizar o repasse de informações. Utilizam sistemas operacionais tradicionais, como Linux ou Windows, e realizam o roteamento puramente via *software*. Embora o equipamento possa contar com algumas ferramentas de auxílio, o *hardware* não é otimizado exclusivamente para redes, entregando um desempenho inferior aos modelos especializados.

> **Na prática:** Os **roteadores especializados** são a espinha dorsal da *Internet* (utilizados por provedores e grandes data centers). Já os **roteadores não especializados** são muito úteis em laboratórios acadêmicos, pequenas empresas ou projetos de baixo custo, onde um computador antigo pode ser reaproveitado para gerenciar a rede usando sistemas como o Linux.

## Algoritmos de Roteamento:

O algoritmo é o "cérebro matemático" do roteador. É a lógica computacional responsável por analisar a rede, preencher a tabela de roteamento e garantir que os dados cheguem ao destino. Para que uma rede funcione de forma estável e confiável, um bom algoritmo precisa atender a cinco características fundamentais:

- **Seleção do Melhor Caminho:** Capacidade de escolher a rota mais eficiente da origem ao destino, baseando-se em uma métrica predefinida (como o caminho mais curto, o mais rápido ou o de menor custo).
- **Convergência Rápida:** Agilidade para se adaptar a mudanças. Se um cabo se romper ou um roteador vizinho for desligado, o algoritmo deve recalcular a nova rota e atualizar a tabela quase instantaneamente.
- **Robustez:** Continuar operando de forma correta e estável mesmo sob condições adversas, como falhas de *hardware*, gargalos em equipamentos intermediários ou picos extremos de tráfego.
- **Escalabilidade:** Manter a eficiência de desempenho independentemente do tamanho da topologia. Ele precisa ser funcional tanto em pequenas redes locais quanto em arquiteturas gigantescas que estão em constante crescimento.
- **Baixo Consumo de Recursos:** Ser construído de forma enxuta para exigir o mínimo de processamento, memória e banda do roteador, garantindo que o próprio cálculo da rota não acabe atrasando a comunicação da rede.

### Classificação dos Algoritmos de Roteamento:

Os algoritmos podem ser classificados de diversas formas, dependendo de como enxergam a rede e de como tomam suas decisões. Para não deixar o estudo cansativo, podemos agrupar essas categorias de forma direta:

- **Por Tipo de Entrega:**
  - ***Unicast*:** Encaminha o pacote de um emissor para um único receptor final.
  - ***Multicast*:** Encaminha de um emissor para um grupo específico de receptores.
  - ***Broadcast*:** Encaminha para todos os dispositivos da rede.

- **Por Atualização (Estático vs. Dinâmico):** O roteamento **estático** utiliza tabelas fixas criadas manualmente pelo administrador. Já o **dinâmico** é adaptativo; os roteadores se comunicam periodicamente para atualizar as tabelas e contornar falhas de forma automática.

- **Por Estrutura (Plano vs. Hierárquico):** No modelo **plano**, todos os roteadores operam no mesmo nível de igualdade. No modelo **hierárquico**, a rede é subdividida logicamente em áreas ou regiões menores para facilitar o gerenciamento.
  - *Escopo:* Dessa divisão estrutural surgem os algoritmos de roteamento **Interno** (que operam apenas dentro de uma região delimitada) e os de roteamento **Externo** (que fazem a conexão entre áreas diferentes).

- **Por Visão da Rede (Local vs. Global):** No modelo **local**, o roteador baseia suas decisões consultando apenas as informações de seus vizinhos diretos. No modelo **global**, o roteador possui conhecimento do mapa topológico completo de toda a rede.

- **Por Tomada de Decisão (Centralizado vs. Distribuído):**
  - **Centralizado:** O caminho completo é calculado de ponta a ponta previamente à transmissão. Isso exige que a rede seja muito estável, pois qualquer instabilidade ou queda de um equipamento invalida a rota, obrigando o recálculo total.
  - **Distribuído:** O cálculo é dinâmico. À medida que o pacote viaja, cada roteador pelo caminho toma a decisão imediata de qual será o próximo equipamento a recebê-lo.

## Métricas de Roteamento:

As métricas são os parâmetros e informações armazenadas nas tabelas de roteamento. É a partir delas que os algoritmos conseguem avaliar matematicamente e decidir qual é o melhor caminho para enviar um pacote. 

Um algoritmo não utiliza obrigatoriamente todas as métricas existentes; ele escolhe apenas as que fazem sentido para o seu modo de operação. As principais são:

- **Número de Saltos (*Hop Count*):** Representa a quantidade de dispositivos intermediários (roteadores) que o pacote precisará atravessar da origem até o destino. Para algoritmos baseados apenas nessa métrica, o melhor caminho é sempre o que tiver o menor número de saltos.
- **Taxa de Transmissão e Carga:** Avalia a capacidade de tráfego do canal de comunicação (em *bits* por segundo) e o seu congestionamento atual. Canais engarrafados entregam menos velocidade, portanto, o algoritmo sempre priorizará as rotas com a maior taxa de transmissão disponível.
- **Atraso (Latência):** É o tempo que um pacote leva para ser efetivamente transferido para o próximo roteador adjacente. Quanto menor o atraso, melhor e mais responsiva é a rota.
- **Taxa de Erro:** Mede a confiabilidade do canal. Como visto no estudo das camadas Física e de Enlace, meios de transmissão podem sofrer interferências. O roteamento prioriza caminhos que apresentem a menor quantidade de erros e pacotes corrompidos.
- **Disponibilidade:** Avalia a estabilidade da rota, medindo a quantidade de tempo que aquele canal de comunicação consegue operar de forma ininterrupta, sem sofrer quedas.
- **Custo:** Apesar do nome, na maioria das vezes não se refere a um valor financeiro. Na prática, o "custo" é uma métrica genérica criada pelo administrador ou pelo próprio algoritmo. Ele funciona combinando várias das métricas acima (como saltos, atraso e taxa de erro) através de pesos matemáticos arbitrários para criar uma nota única de avaliação para a rota.

### Roteamento por Vetor de Distância:

O algoritmo de **Vetor de Distância** (cujo protocolo mais famoso é o *RIP*) é um método dinâmico, descentralizado e muito popular pela sua simplicidade. A sua lógica de funcionamento baseia-se em roteadores que compartilham suas tabelas inteiras apenas com seus vizinhos diretos (uma verdadeira "fofoca" de rede).

Neste modelo, o roteador não possui um mapa completo da rede; ele age como uma pessoa em um cruzamento que confia cegamente nas placas apontadas pelos roteadores vizinhos.

O processo de construção e atualização das rotas ocorre em quatro etapas lógicas:

1. **Estado Inicial:** Quando ligado, o roteador conhece apenas a si mesmo (com custo ou distância igual a `0`) e os roteadores com os quais está conectado por um cabo direto.
2. **A Troca (O Envio do Vetor):** Periodicamente, ele envia uma cópia da sua tabela para os seus vizinhos diretos. Ao mesmo tempo, ele recebe as tabelas deles.
3. **O Incremento (O Cálculo):** Quando o roteador "A" recebe a tabela do roteador "B", ele imediatamente **incrementa** o custo. Por exemplo, se a métrica for número de saltos, "A" soma `+1` a todos os valores recebidos da tabela de "B", pois ele sabe que precisará dar um salto até "B" antes de seguir o resto da rota.
4. **A Decisão (Atualizar ou Descartar):** Após fazer a soma, o roteador compara as rotas novas com a sua própria tabela:
   - Se for um destino novo, ele adiciona à tabela.
   - Se a rota recebida tiver um custo **menor/melhor** que a dele, ele substitui a sua rota antiga por essa nova.
   - Se a rota for **pior** ou se referir a ele mesmo, o dado é simplesmente **descartado** por ineficiência.

**Dinamismo e Resiliência (Tratamento de Falhas):**
O que torna este algoritmo essencialmente dinâmico é a sua capacidade de recalcular a rede automaticamente em caso de problemas. Se um cabo for rompido ou um roteador vizinho "morrer", o equipamento adjacente que detectar a queda altera imediatamente o custo daquele caminho para **infinito** (um valor limite que significa "destino inatingível"). 

Na próxima rodada de trocas, esse valor infinito é repassado aos vizinhos. Ao receberem essa informação, eles entendem que o caminho caiu, apagam a rota original de suas tabelas e passam a procurar na própria tabela qual é a segunda melhor rota disponível para contornar o problema, restabelecendo o fluxo da rede.

> **Ponto de Atenção (A lentidão da "Fofoca"):**
> A grande vantagem deste modelo é a sua extrema simplicidade e o baixo *overhead* computacional (o roteador só faz somas básicas). A sua desvantagem, porém, é a **convergência lenta**. Como a informação viaja apenas de vizinho em vizinho, a notícia de que uma rede distante caiu com "custo infinito" pode demorar bastante tempo para atravessar todos os equipamentos e chegar ao outro lado do mapa.

## Roteamento Hierárquico

Em redes de proporções globais como a *Internet*, manter uma tabela de roteamento detalhando cada dispositivo existente no mundo é tecnicamente inviável. Uma tabela desse porte esgotaria a memória dos roteadores, atrasaria o processamento e geraria um tráfego de atualização massivo na rede. 

Para resolver o problema de escalabilidade, utiliza-se o **Roteamento Hierárquico**, que divide a infraestrutura agrupando os roteadores em regiões lógicas chamadas de **Sistemas Autônomos (AS)**.

O funcionamento dessa organização baseia-se na divisão de papéis e na abstração de rotas:

- **Roteamento Interno:** Dentro de um mesmo Sistema Autônomo, os roteadores conhecem a topologia detalhada e o caminho exato para qualquer dispositivo pertencente àquela própria região.
- **Roteadores de Borda:** São os equipamentos posicionados nas extremidades da região. Eles atuam como "portões de saída" e são os únicos responsáveis por fazer a ponte de comunicação e troca de rotas com outros Sistemas Autônomos externos.
- **Ocultamento de Topologia (Abstração):** Um roteador interno não conhece os equipamentos de redes externas. Se o destino de um pacote estiver fora do seu Sistema Autônomo, o roteador não precisa calcular a rota até lá; ele simplesmente encaminha a mensagem para o roteador de borda da sua região.

> **O Grande Benefício:** Ao aplicar essa hierarquia, o tamanho das tabelas de roteamento é reduzido drasticamente. O equipamento substitui milhões de rotas individuais por rotas resumidas apontando para grandes regiões externas. Isso economiza processamento, memória e viabiliza o funcionamento da rede global.

## Roteamento de Broadcast:

Quando uma mesma informação precisa ser entregue a todos os dispositivos de uma rede remota (como um aviso para um departamento com 1000 máquinas), enviar 1000 pacotes individuais (*unicast*) a partir da origem geraria um tráfego insustentável. 

O **roteamento de *broadcast*** otimiza esse processo enviando apenas **uma única cópia** do pacote através da rede externa. A difusão (multiplicação) do pacote é delegada apenas ao roteador final, que está diretamente conectado ao departamento alvo.

### Identificação do Pacote:
Para que o roteador saiba que aquela mensagem não é para um único computador, mas sim para todos, o sistema utiliza o **Endereço IP de Destino**. O envio pode ocorrer de duas formas:

- **Broadcast Limitado (`255.255.255.255`):** Utilizado para falar com todos os dispositivos da **própria rede local**. O roteador inunda a rede com a mensagem, mas bloqueia a sua passagem para redes externas.
- **Broadcast Direcionado:** Utilizado para enviar uma mensagem para todos os dispositivos de uma **rede remota**. O emissor coloca como destino o IP de *broadcast* daquela sub-rede específica (onde os *bits* de *host* são todos `1`, como `192.168.10.255`). A mensagem viaja pela *Internet* como um pacote único. Ao chegar no roteador final, o equipamento identifica que se trata do IP reservado de difusão e multiplica o pacote localmente, entregando-o a todas as máquinas do setor.

### Algoritmos de Roteamento Broadcast:

Para que a mensagem de *broadcast* atinja todos os nós da rede, os equipamentos podem utilizar diferentes estratégias algorítmicas. As duas abordagens principais são:

#### 1. Roteamento por Inundação (*Flooding*):

A regra deste algoritmo é direta e primária: quando um roteador recebe um pacote, ele faz cópias e o retransmite por **todas as suas interfaces de saída**, com exceção daquela por onde o pacote acabou de entrar.

Apesar de garantir de forma robusta que a mensagem alcance todos os cantos da topologia, a inundação sem restrições gera dois problemas críticos:
- **Loops de Roteamento:** Embora o roteador não devolva o pacote diretamente de onde veio, ele repassa para vizinhos que podem, eventualmente, acabar devolvendo o pacote pelo outro lado do caminho.
- **Esgotamento de Banda:** A multiplicação contínua e exponencial de pacotes viajando em *loop* gera uma tempestade de pacotes que pode consumir toda a capacidade de transmissão da rede.

**Mecanismos de Contenção:**
Para viabilizar o uso da inundação sem causar o colapso da rede, aplicam-se duas estratégias de controle em conjunto:

- **Identificador Único (Controle de *Cache*):** O pacote recebe um número de identificação exclusivo. Os roteadores mantêm uma memória temporária registrando os IDs processados recentemente. Se um pacote chegar com um ID já conhecido, ele é imediatamente descartado.
  > *O problema da memória volátil:* Como o *cache* é pequeno, informações antigas são sobrescritas. Se um pacote trafegar por dezenas de roteadores e só retornar à origem muito tempo depois, o roteador já terá "esquecido" o seu ID, retransmitindo a mensagem e recriando o *loop*.
- **Tempo de Vida (TTL - *Time to Live*):** Atua como a barreira de segurança final. O pacote recebe um limite máximo de saltos. A cada roteador que ele atravessa, esse valor é reduzido em 1. Se o pacote entrar em um grande *loop* e conseguir burlar o controle do *cache*, o seu TTL inevitavelmente chegará a zero, fazendo com que o roteador "mate" e descarte o pacote antes que ele se torne eterno.

#### 2. Roteamento por Árvore de Cobertura (*Spanning Tree*):

Nesta abordagem, o algoritmo mapeia a rede física (que geralmente é uma teia com vários caminhos redundantes) e sobrepõe a ela uma estrutura lógica em formato de árvore. Nessa árvore, é estabelecido **apenas um caminho único e definitivo** interligando todos os nós da rede.

Essa restrição é puramente lógica: os cabos físicos alternativos continuam conectados aos equipamentos, mas o algoritmo bloqueia o repasse de informações por eles.

- **A Grande Vantagem:** Como a estrutura dita que a informação só pode fluir por uma única rota pré-aprovada, a ocorrência de *loops* torna-se matematicamente impossível. O roteador copia e encaminha a mensagem apenas nas "bifurcações" da árvore, eliminando o desperdício de banda causado pela inundação.

**Desvantagens e Desafios:**
Apesar da elegância e da segurança na prevenção de *loops*, essa arquitetura traz dois problemas práticos importantes:
- **Gargalos e Subutilização:** Como todo o tráfego da rede é afunilado para um único caminho lógico, esses enlaces podem ficar superutilizados e congestionados, diminuindo a velocidade geral de comunicação. Enquanto isso, caminhos alternativos que poderiam aliviar a carga ficam ociosos por estarem bloqueados.
- **Sensibilidade a Falhas:** Se ocorrer qualquer defeito físico (cabo rompido ou roteador queimado) que faça parte da árvore principal, a comunicação é "quebrada". O envio é interrompido até que o algoritmo detecte a falha e recalcule a árvore inteira do zero para encontrar uma nova rota lógica.


### A Base do Roteamento Multicast: Árvores Geradoras

Para que a transmissão seja eficiente e não inunde a rede com cópias desnecessárias, os algoritmos de roteamento *multicast* baseiam-se na construção de **árvores geradoras** (*spanning trees*). Em redes amplamente interligadas, transmitir pacotes indiscriminadamente geraria redundâncias e *loops* que esgotariam a banda. 

A árvore geradora resolve esse problema abstraindo a topologia física para criar uma estrutura lógica **sem ciclos**. Ela traça caminhos únicos entre o transmissor e os receptores. Dessa forma, o pacote viaja por um percurso único e só é duplicado pelo roteador no exato ponto de bifurcação onde os caminhos para os membros do grupo se separam.

### Encaminhamento por Caminho Reverso (RPF)

O **Encaminhamento por Caminho Reverso** (RPF - *Reverse Path Forwarding*) é o mecanismo prático utilizado pelos roteadores para construir essas árvores e garantir a ausência de *loops* na transmissão. Ele reaproveita a inteligência da tabela de roteamento convencional (*unicast*) para testar a validade dos pacotes recebidos.

O funcionamento do teste ocorre da seguinte forma:

- **Análise da Origem:** Quando um roteador recebe um pacote *multicast*, ele analisa o IP do transmissor original e a interface física por onde a mensagem acabou de entrar.
- **O Teste do Caminho Ideal:** O roteador consulta sua tabela de rotas e faz uma verificação lógica: *"Se eu precisasse enviar um pacote de volta para esse transmissor pelo melhor caminho possível, eu usaria a mesma interface por onde este pacote chegou?"*
- **Aprovação e Encaminhamento:** Se a resposta for sim, o pacote chegou pela rota ideal (mais curta). O roteador o aceita, faz as cópias necessárias e o encaminha pelas demais interfaces ligadas ao grupo.
- **Descarte (Contenção de *Loop*):** Se o pacote chegar por uma interface diferente daquela que seria usada para alcançar a origem, significa que ele veio por um caminho secundário ou redundante. O roteador identifica a anomalia e descarta a mensagem.

> O RPF força o fluxo da informação a seguir sempre em frente, afastando-se continuamente da origem. Ao rejeitar mensagens vindas de portas incorretas, ele "corta" os caminhos secundários da topologia da rede, revelando naturalmente a estrutura limpa da árvore geradora.

### Roteamento Multicast: Caminho Inverso vs. Baseado em Núcleo:

Os algoritmos de roteamento *multicast* diferem principalmente na forma como constroem a árvore geradora para evitar *loops* e entregar os pacotes. Existem duas abordagens principais:

#### Multicast por Caminho Inverso (Árvores Baseadas na Origem):

Utiliza o mecanismo de RPF (*Reverse Path Forwarding*) para criar uma árvore exclusiva para cada transmissor do grupo. Segue uma abordagem de "empurrar" os dados (*Push*), operando em duas etapas:

- **Inundação (*Flood*):** O transmissor envia o tráfego para toda a rede. O mecanismo de RPF atua na entrada de cada roteador para evitar *loops*, garantindo que essa inundação forme uma árvore lógica sem ciclos. Inicialmente, o tráfego alcança até mesmo os roteadores que não possuem receptores interessados.
- **Poda (*Prune*):** Quando um roteador de extremidade recebe o pacote e constata que nenhum dispositivo na sua rede local solicitou aquele tráfego, ele envia uma mensagem de "poda" de volta para o roteador anterior, cortando aquele segmento do caminho.

> A árvore gerada por RPF começa abrangendo toda a rede e vai "encolhendo" à medida que os galhos ociosos são podados e removidos do roteamento.

#### Multicast Baseado em Núcleo (Árvores Compartilhadas):

Em vez de criar uma árvore independente para cada transmissor, a rede elege um roteador central para atuar como o **núcleo** (*Core* ou *Rendezvous Point*). Uma única árvore é construída e compartilhada por todos os membros do grupo.

- **Construção Sob Demanda (*Pull*):** A árvore é construída "de baixo para cima". Apenas os roteadores que possuem receptores interessados enviam uma solicitação de junção em direção ao roteador núcleo. 
- **Otimização de Caminhos:** O transmissor não inunda a rede; ele encaminha seus pacotes diretamente para o núcleo via rotas *unicast*. Ao chegar no núcleo, o pacote é duplicado e distribuído apenas pelas ramificações da árvore já estabelecidas.
- **Ausência de Tráfego Desnecessário:** Como a árvore só cresce para os locais que ativamente pediram para se juntar ao grupo, os pacotes nunca trafegam por caminhos que levam a não receptores, eliminando a necessidade de testes de descarte por poda durante a transmissão.

> Enquanto a árvore baseada na origem gasta recursos iniciais inundando a rede para mapear os interessados, a árvore baseada em núcleo é mais eficiente em redes esparsas, pois o caminho só existe onde há demanda explícita.
