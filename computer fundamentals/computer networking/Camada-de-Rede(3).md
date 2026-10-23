# 04. Camada de Rede

## Função da Camada de Rede:

Enquanto a camada de enlace realiza a comunicação local entre dispositivos adjacentes, a camada de rede permite que a informação trafegue da origem até o destino através de redes interligadas e dispositivos intermediários. Esse processo envolve duas funções principais:

- **Roteamento:** Define os caminhos até os destinos e organiza as informações sobre as rotas disponíveis, conforme os critérios utilizados pela rede.
- **Encaminhamento (Comutação):** É a ação prática: o equipamento recebe um pacote, consulta as informações de encaminhamento e o repassa pela saída correspondente ao **próximo salto**, o próximo equipamento que o receberá no trajeto.

> No modelo Internet, o **IP** (*Internet Protocol*) oferece esse serviço sem garantir a entrega ou a ordem de chegada dos pacotes.

## Comutação:

Permite que dispositivos se comuniquem através de equipamentos intermediários, sem exigir uma conexão direta entre as extremidades.

![Rede de Interconexão](images/screenshot039.png)<br>
*Fonte: MAIA, Luiz Paulo. Arquitetura de Redes de Computadores, 2ª ed., 2013, p. 261.*

![Roteadores na Rede de Interconexão](images/screenshot040.png)<br>
*Fonte: MAIA, Luiz Paulo. Arquitetura de Redes de Computadores, 2ª ed., 2013, p. 262.*

Nos diagramas de camadas, os roteadores executam apenas até a Camada de Rede porque esse é o nível necessário ao encaminhamento IP básico. É como um centro de distribuição dos correios: basta consultar o endereço no envelope, sem interpretar o conteúdo da carta.

> O equipamento pode executar outras funções de camadas superiores, mas elas não são necessárias para esse encaminhamento.

### Comutação por Circuito:

Estabelece um caminho entre a origem e o destino antes da transmissão. Esse **circuito** mantém recursos reservados para a comunicação até seu encerramento. O processo ocorre em três etapas:

1. **Alocação do circuito:** Estabelece-se o caminho e reservam-se os recursos necessários.
2. **Troca de dados:** A informação é transmitida pelo circuito estabelecido.
3. **Liberação do circuito:** A conexão é encerrada e os recursos ficam disponíveis para outras comunicações.

> Funciona como uma ligação telefônica tradicional: primeiro disca-se e estabelece-se a chamada; depois ocorre a conversa; ao desligar, o circuito é liberado.

![Comutação por Circuito](images/screenshot041.png)<br>
*Fonte: MAIA, Luiz Paulo. Arquitetura de Redes de Computadores, 2ª ed., 2013, p. 263.*

- **Caminho predefinido:** Os dados seguem pelo circuito estabelecido, simplificando seu encaminhamento durante a transmissão.
- **Reserva e ociosidade:** A capacidade reservada fica indisponível para outras comunicações mesmo nos períodos de silêncio. Um enlace físico pode atender vários circuitos por multiplexação, cada um com sua parcela reservada.
- **Dependência do circuito:** Uma falha no caminho pode interromper a comunicação e exigir seu restabelecimento, sem necessariamente perder os dados já recebidos.

### Comutação por Pacotes:

Organiza os dados em pequenos blocos chamados **pacotes**, encaminhados pelos equipamentos intermediários até o destino. Uma mensagem maior pode ser dividida em vários pacotes, e os enlaces são compartilhados por diferentes comunicações.

![Comutação por Pacotes](images/screenshot042.png)<br>
*Fonte: MAIA, Luiz Paulo. Arquitetura de Redes de Computadores, 2ª ed., 2013, p. 265.*

- **Compartilhamento sob demanda:** Pacotes de várias comunicações utilizam a infraestrutura conforme há dados para enviar, aproveitando os intervalos de ociosidade. Esse funcionamento não exige reserva fixa de capacidade, embora determinados serviços possam reservar recursos.
- **Recuperação por partes:** A divisão permite implementar mecanismos que retransmitam partes perdidas, sem necessariamente reenviar toda a mensagem. A divisão em pacotes, sozinha, não realiza essa recuperação.
- **Processamento por pacote:** Cada equipamento interpreta informações de controle para decidir o encaminhamento. O compartilhamento melhora o aproveitamento da rede, mas também pode provocar espera quando vários pacotes disputam uma saída.

## Implementações da Comutação por Pacotes:

### Datagrama (Serviço Não Orientado a Conexão):

**Cada pacote é tratado de forma independente**, sem estabelecer previamente uma conexão na camada de rede. O equipamento consulta o endereço de destino e suas informações de encaminhamento para decidir o próximo salto. É o modelo utilizado pelo IP na Internet.

- **Caminhos independentes:** Pacotes da mesma comunicação podem seguir rotas diferentes, sem exigir mudança de caminho a cada envio. O encaminhamento consulta informações já disponíveis, em vez de calcular uma rota completa por pacote.
- **Adaptação a falhas:** Havendo caminhos alternativos e atualização das rotas, novos pacotes podem contornar um equipamento ou enlace defeituoso. A recuperação não é necessariamente imediata.
- **Entrega e ordem:** No serviço IP, pacotes podem se perder ou chegar fora de ordem. Retransmissão e reorganização dependem de outros mecanismos, como os oferecidos pelo TCP, apresentado no modelo de camadas.

### Circuito Virtual (Serviço Orientado a Conexão):

Estabelece um caminho lógico antes do envio dos dados. Os pacotes seguem esse caminho, e os equipamentos intermediários utilizam um **identificador do circuito** para consultar o encaminhamento correspondente. Esse modelo é aplicado em redes ATM (*Asynchronous Transfer Mode*), por exemplo.

- **Caminho estabelecido:** O percurso é escolhido previamente. Ainda existe processamento por pacote, mas o encaminhamento utiliza as associações do circuito.
- **Compartilhamento:** Diferentes circuitos virtuais podem compartilhar os enlaces físicos. A existência de reserva de capacidade depende do serviço, não apenas de ser um circuito virtual.
- **Entrega e ordem:** O caminho comum favorece a manutenção da ordem, mas as garantias de entrega e ordenação dependem do serviço implementado.
- **Dependência do caminho:** Uma falha pode interromper os circuitos afetados e exigir novos caminhos, sem necessariamente reiniciar toda a transferência.

## Endereçamento:

Permite identificar as interfaces de rede e orientar o encaminhamento até o destino. No modelo Internet, utiliza-se um identificador lógico chamado **endereço IP**. Um dispositivo com várias interfaces pode ter endereços distintos associados a elas.

O cabeçalho IP contém os **endereços de origem e destino**. O destino orienta o encaminhamento; a origem identifica de onde o pacote partiu e permite endereçar uma resposta. A identificação vale dentro do seu contexto: endereços privados, por exemplo, podem ser reutilizados em redes independentes.

- **IPv4:** Possui **32 *bits***, permitindo `2^32` combinações. Sua representação decimal utiliza quatro blocos de 8 *bits*, separados por pontos, com valores entre 0 e 255 (como `192.168.0.1`).
- **IPv6:** Possui **128 *bits***, permitindo `2^128` combinações. A ampliação do espaço de endereçamento combate diretamente a limitação de endereços do IPv4. Sua representação utiliza oito grupos hexadecimais, separados por dois-pontos.

> Um IPv6 pode aparecer resumido, como `fe80::70db:aa5b:82c0:622c`. O `::` substitui grupos zerados: o endereço completo é `fe80:0000:0000:0000:70db:aa5b:82c0:622c`. Essa substituição pode aparecer uma única vez no endereço.

Os cálculos de máscaras, sub-redes e faixas de endereços apresentados a seguir se concentram no **IPv4**. Mecanismos específicos dessa versão, como ARP e endereçamento de *broadcast*, não devem ser estendidos automaticamente ao IPv6.

### Endereço Físico (MAC) e Endereço Lógico (IP):

- **MAC:** Identifica interfaces na comunicação pelo enlace local. Como visto na camada de enlace, o endereço utilizado pode ser alterado por configuração ou aleatorização do sistema.
- **IP:** Identifica interfaces no contexto lógico da rede e orienta o encaminhamento entre redes. Sua configuração deve corresponder à rede de conexão, podendo ser manual ou automática.

> Ao levar um notebook de casa para uma cafeteria, a configuração IP é adequada à nova rede. O MAC permanece igual (ou pode mudar por mecanismos de privacidade). O valor numérico do IP também pode coincidir, pois as duas redes podem utilizar a mesma faixa privada.

### Endereçamento Estático e Dinâmico:

- **Estático:** O endereço permanece configurado na interface até ser alterado, normalmente por configuração manual. Exige controle dos endereços utilizados: atribuir o mesmo IP a interfaces que precisam ser distintas na mesma rede pode causar um conflito.
- **Dinâmico:** No IPv4, normalmente utiliza **DHCP** (*Dynamic Host Configuration Protocol*). O cliente solicita uma configuração e recebe um endereço por um período de concessão, que pode ser renovado. O endereço pode mudar em outra conexão, mas isso não é obrigatório.

O endereçamento dinâmico facilita a administração e a mobilidade, permitindo receber uma configuração compatível ao mudar de rede. Endereços com concessões encerradas podem ser reutilizados pelo servidor.

> Servidores, impressoras, câmeras e roteadores podem precisar de um endereço estável para facilitar seu acesso. Além da configuração estática, uma **reserva no DHCP** permite entregar o mesmo endereço a um cliente identificado.

> Ao executar `ip a` no terminal Linux (no Windows, `ipconfig /all`), é possível consultar os endereços IPv4 e IPv6 e suas informações de máscara ou prefixo, explicadas a seguir.

## Endereçamento Hierárquico:

Se fosse necessário manter uma rota individual para cada interface da Internet, as tabelas seriam muito maiores e mais difíceis de atualizar. O endereçamento hierárquico permite agrupar destinos, dividindo o endereço em duas partes:

- **Identificador de Rede (*NetID*):** Identifica a rede à qual a interface pertence.
- **Identificador de Host (*HostID*):** Distingue a interface dentro dessa rede.

> É semelhante a um endereço postal: a informação sobre uma cidade permite encaminhar correspondências para muitos endereços sem conhecer cada casa individualmente. Na rede, um prefixo pode representar vários destinos em uma única entrada de encaminhamento.

### Máscara e Prefixo de Rede:

O endereço IPv4 identifica a interface, mas, sozinho, não informa onde termina a parte de rede e começa a parte de *host*. Essa divisão é indicada pela **máscara de rede**, formada por *bits* 1 consecutivos para a rede, seguidos de *bits* 0 para o *host*.

Por exemplo, a máscara `255.255.255.0` corresponde a 24 *bits* 1 e 8 *bits* 0. No endereço `146.164.2.70`, ela indica que `146.164.2` identifica a rede e o último bloco identifica o *host* dentro dela.

A mesma divisão pode ser escrita como **comprimento do prefixo**: `146.164.2.70/24`. O número após a barra informa quantos *bits* pertencem à rede. Em IPv4, ele pode variar de 0 a 32.

- **Na máquina:** O endereço IP e a máscara são configurados na interface, manualmente ou por mecanismos como DHCP. Essa configuração permite identificar a rede diretamente conectada.
- **No roteador:** Além da configuração de suas interfaces, existem rotas com prefixos e indicações de encaminhamento. Cada rota pode representar um bloco de tamanho diferente.

> A máscara não é enviada junto do endereço no cabeçalho de cada pacote IPv4. O pacote contém o IP de destino; os equipamentos utilizam as configurações e rotas que conhecem para decidir como encaminhá-lo.

Para obter o endereço da rede, aplica-se **AND *bit* a *bit*** entre o IP e a máscara:

- **Bit 1 na máscara:** Preserva o *bit* correspondente do IP.
- **Bit 0 na máscara:** Zera o *bit* correspondente do IP.

Assim, `146.164.2.70 AND 255.255.255.0 = 146.164.2.0`, identificando a rede correspondente àquela máscara.

### Endereços de Rede, de Host e de *Broadcast*:

Definida a divisão entre rede e *host*, os valores da parte de *host* determinam a função de cada endereço. Nas sub-redes IPv4 convencionais com *broadcast*:

- **Todos os *bits* de *host* em zero:** Identificam a própria rede.
- **Todos os *bits* de *host* em um:** Formam o endereço de *broadcast*, utilizado para destinar uma transmissão a todas as interfaces daquela sub-rede.
- **Valores intermediários:** Podem ser atribuídos às interfaces, respeitando outras reservas aplicáveis.

Na rede `146.164.2.0/24`, o endereço da rede é `146.164.2.0`, o de *broadcast* é `146.164.2.255` e o intervalo utilizável vai de `146.164.2.1` a `146.164.2.254`.

Os finais `.0` e `.255` resultam dessa divisão específica. **A máscara determina a parte de *host***, que pode ocupar mais ou menos que os últimos oito *bits*.

> Em enlaces ponto a ponto com prefixo `/31`, os dois endereços são utilizados pelas extremidades. A reserva de rede e *broadcast* descrita acima se aplica às sub-redes convencionais com *broadcast*.

### Sub-redes:

O **subendereçamento** divide um bloco em blocos menores. Para isso, amplia-se o prefixo usando parte dos *bits* antes destinados aos *hosts*. Esses *bits* passam a distinguir as sub-redes, reduzindo a quantidade de endereços dentro de cada uma.

Por exemplo, o bloco `146.164.2.0/24` possui oito *bits* para *hosts*. Ao utilizar dois deles para subdivisão, formam-se `2^2 = 4` sub-redes `/26`, cada uma com seis *bits* para *hosts*.

Cada sub-rede terá `2^6 = 64` endereços. Descontando rede e *broadcast*, restam **62 endereços para interfaces**:

| Sub-rede | Intervalo Utilizável | *Broadcast* |
| --- | --- | --- |
| `146.164.2.0/26` | `146.164.2.1` - `146.164.2.62` | `146.164.2.63` |
| `146.164.2.64/26` | `146.164.2.65` - `146.164.2.126` | `146.164.2.127` |
| `146.164.2.128/26` | `146.164.2.129` - `146.164.2.190` | `146.164.2.191` |
| `146.164.2.192/26` | `146.164.2.193` - `146.164.2.254` | `146.164.2.255` |

A máscara `/26` corresponde a `255.255.255.192`. Aplicando-a ao endereço `146.164.2.70`, obtém-se a sub-rede `146.164.2.64`. No último bloco, a operação é:

```text
IP:       01000110  (70)
Máscara:  11000000  (192)
AND:      01000000  (64)
```

Os dois primeiros *bits* desse bloco distinguem as quatro sub-redes; os seis restantes identificam os *hosts*. Quanto maior o prefixo, menor será o bloco de endereços.

A divisão permite organizar setores e distribuir melhor os endereços. Quando as sub-redes correspondem a segmentos de enlace separados, como VLANs diferentes, também há separação dos seus domínios de *broadcast*.

> Apenas configurar faixas IP diferentes no mesmo enlace não separa os domínios de *broadcast*. Essa separação depende também da organização da camada de enlace, apresentada no apêndice de redes locais.

## Endereçamento por Classes (*Classful*):

Nesse modelo histórico, os primeiros *bits* do endereço determinavam a divisão básica entre rede e *host*. As classes A, B e C ofereciam três tamanhos de rede; D e E tinham outras finalidades.

| Classe | Bits Iniciais | Faixa de Endereços | Divisão ou Finalidade |
| --- | --- | --- | --- |
| **A** | `0` | `0.0.0.0` a `127.255.255.255` | 8 *bits* para rede e 24 para *host*. |
| **B** | `10` | `128.0.0.0` a `191.255.255.255` | 16 *bits* para rede e 16 para *host*. |
| **C** | `110` | `192.0.0.0` a `223.255.255.255` | 24 *bits* para rede e 8 para *host*. |
| **D** | `1110` | `224.0.0.0` a `239.255.255.255` | Endereçamento de grupos (*multicast*). |
| **E** | `1111` | `240.0.0.0` a `255.255.255.255` | Faixa historicamente reservada para usos futuros e experimentais, com endereços de uso especial. |

> Os *bits* iniciais das classes A, B e C já estão incluídos nos tamanhos da parte de rede. As faixas também contêm endereços especiais que não podem ser atribuídos normalmente às interfaces.

O endereço `146.164.2.70`, por exemplo, pertence à antiga classe B. Sua rede básica seria `146.164.0.0`, com máscara `255.255.0.0` (`/16`). A instituição poderia subdividir esse bloco usando máscaras mais longas, como `/24` e `/26`.

No esquema histórico, as redes externas podiam encaminhar para a rede classe B como um todo, enquanto a instituição utilizava as máscaras de sub-rede para distribuir o tráfego internamente. As máscaras permitiam subdividir a rede recebida, mas **não eliminavam a rigidez dos tamanhos atribuídos pelas classes**.

Uma organização com cerca de 500 interfaces ilustra o problema: uma classe C, com 254 endereços utilizáveis, seria insuficiente; uma classe B, com 65.534, reservaria muito mais endereços que o necessário. Subdividir a classe B organizaria o espaço, mas não reduziria o bloco originalmente reservado à organização.

Outra possibilidade seria receber vários blocos classe C. Isso reduziria a sobra, mas aumentaria a quantidade de redes representadas nas tabelas de roteamento. O modelo enfrentava, assim, **desperdício de endereços e crescimento das tabelas**.

## Endereçamento Sem Classes (CIDR):

O **CIDR** (*Classless Inter-Domain Routing*) permite distribuir e anunciar blocos com comprimentos de prefixo explícitos, sem limitar a divisão aos tamanhos das classes A, B e C. A identificação da rede depende do prefixo informado, em vez de ser deduzida da classe do endereço.

Uma rede convencional `/23`, por exemplo, possui nove *bits* para *hosts*: são `2^9 = 512` endereços, dos quais 510 são utilizáveis. Esse tamanho pode atender ao exemplo das 500 interfaces sem reservar uma classe B inteira.

> O CIDR não criou as máscaras nem o subendereçamento. Ele retirou a obrigatoriedade das classes na alocação e no roteamento entre redes. Subdividir blocos continua sendo possível nesse modelo.

### VLSM e Subdivisões Sucessivas:

O **VLSM** (*Variable Length Subnet Masking*) permite utilizar máscaras de comprimentos diferentes nas subdivisões de um bloco. Assim, setores com necessidades distintas podem receber quantidades diferentes de endereços.

No exemplo de `146.164.2.0/24`, em vez de criar quatro sub-redes iguais, seria possível manter `146.164.2.0/26` e `146.164.2.64/26`, com 62 endereços utilizáveis cada, e destinar a metade restante a `146.164.2.128/25`, com 126.

Também é possível subdividir um bloco que já resultou de outra divisão: uma instituição pode organizar `146.164.0.0/16` em setores `/24` e subdividir um desses setores em redes `/26`. A máscara identifica o prefixo completo utilizado, sem registrar separadamente as funções administrativas de cada divisão.

> “Comprimento variável” significa que as sub-redes podem ter tamanhos diferentes; a máscara não muda automaticamente a cada pacote. O **VLSM** organiza os tamanhos dentro de um bloco; o **CIDR** permite distribuir e anunciar blocos sem depender das classes, inclusive agregando rotas.

### Agregação de Rotas:

Blocos contíguos e corretamente alinhados podem ser representados por um prefixo que abrange todos eles, quando o encaminhamento permitir. Assim, uma rota externa pode representar várias redes internas, reduzindo a necessidade de divulgar cada divisão separadamente.

Para um pacote destinado à máquina `146.164.2.70/26`, uma organização possível seria:

| Equipamento | Rota Conhecida no Exemplo | Encaminhamento |
| --- | --- | --- |
| Roteador externo | `146.164.0.0/16` | Em direção à instituição. |
| Roteador da instituição | `146.164.2.0/24` | Em direção ao setor. |
| Roteador que atende a subdivisão | `146.164.2.64/26` | Para a rede local que contém a máquina `.70`. |

O destino continua sendo `146.164.2.70`; varia o detalhamento das rotas utilizadas. Um mesmo roteador pode conhecer vários desses blocos. A representação externa depende das rotas divulgadas, não apenas de existir uma subdivisão.

![Roteamento CIDR](images/screenshot043.png)<br>
*Fonte: Elaborado pelo autor com auxílio do ChatGPT, 2026.*

## Encaminhamento de Pacotes:

Uma máquina utiliza suas informações de roteamento para distinguir os destinos acessíveis diretamente daqueles que exigem um roteador. Uma **interface de rede** é a conexão física ou lógica pela qual ela envia e recebe os dados.

### Dentro e Fora da Sub-rede:

Considere uma máquina configurada como `146.164.2.70/26`, pertencente à sub-rede `146.164.2.64/26`. Na configuração comum:

- **Destino `146.164.2.100`:** Está na mesma sub-rede. A máquina pode enviar pelo enlace local, sem passar por um roteador.
- **Destino `146.164.2.130`:** Está em outra sub-rede, `146.164.2.128/26`. A máquina entrega o pacote a um roteador acessível pela rede local, conforme sua tabela de rotas.

Essa distinção pode ser obtida aplicando a máscara ao IP da própria máquina e ao destino: resultados iguais indicam a mesma sub-rede. A decisão efetiva considera as rotas configuradas, inclusive rotas mais específicas quando existirem.

Cada roteador consulta sua própria tabela para determinar a saída seguinte, até que o pacote alcance a rede do destino e possa ser entregue localmente.

## Mapeamento de Endereços:

Depois de escolher a saída e o próximo salto, é necessário entregar o pacote à camada de enlace. Em uma rede *Ethernet*, essa entrega utiliza um quadro com endereços MAC. O **mapeamento de endereços** permite descobrir qual MAC corresponde ao IP do próximo salto local.

### Mapeamento Estático:

Mantém associações entre IP e MAC configuradas manualmente, apenas para as entradas necessárias. Permite controlar associações específicas, mas exige acompanhar mudanças de endereços ou equipamentos. O trabalho cresce com a quantidade de entradas, e uma associação desatualizada pode impedir a entrega correta.

### Mapeamento Dinâmico - ARP:

O **ARP** (*Address Resolution Protocol*) descobre essas associações sob demanda no IPv4 em enlaces como *Ethernet*. Antes de perguntar, o dispositivo consulta sua tabela de vizinhos; se já houver uma associação válida, pode utilizá-la.

Quando precisa resolver um endereço, o funcionamento depende do próximo salto definido pelo encaminhamento:

- **Destino na mesma rede local:** O computador envia uma solicitação ARP em *broadcast*, perguntando “Qual MAC corresponde ao IP X?”. É como um grito para os dispositivos daquele enlace. Na situação comum, o dispositivo com aquele IP responde diretamente ao solicitante, informando seu MAC.
- **Destino em outra rede:** O computador resolve o MAC do roteador escolhido como próximo salto, não o MAC do destino remoto. A solicitação ARP fica restrita ao enlace local; o roteador recebe o pacote e realiza o encaminhamento seguinte.

O **quadro** é endereçado ao próximo salto local, enquanto o **pacote IP** identifica o destino final. Em um encaminhamento comum, sem tradução de endereços, o IP de destino é preservado, mas o quadro é substituído conforme o pacote passa de um enlace para outro.

> As associações aprendidas ficam temporariamente em uma tabela de vizinhos (*cache*), evitando repetir a pergunta a cada pacote. No Linux, `ip neigh` permite consultá-la; no Windows, `arp -a` mostra as entradas ARP. O IPv6 utiliza outro mecanismo de descoberta de vizinhos, não o ARP.

## NAT:

O IPv4 possui aproximadamente 4,3 bilhões de combinações (`2^32`), das quais nem todas estão disponíveis para atribuição comum. O crescimento das redes e a quantidade de dispositivos conectados pressionaram esse espaço limitado.

Endereços privados e tradução podem reduzir a necessidade de endereços públicos. **A reserva dessas faixas e o NAT são mecanismos distintos**, utilizados em conjunto nesse cenário.

### Endereços Públicos e Privados:

- **Públicos:** São globalmente únicos no contexto de sua atribuição e utilizáveis no roteamento da Internet. O acesso ao dispositivo ainda depende das rotas e das regras de filtragem.
- **Privados:** Destinam-se ao uso interno e não são encaminhados como destinos globais pela Internet pública. Podem ser reutilizados em redes independentes, mas precisam ser coordenados entre redes interligadas para evitar conflitos.

As faixas privadas do IPv4 são:

| Bloco | Intervalo de Endereços |
| --- | --- |
| `10.0.0.0/8` | `10.0.0.0` a `10.255.255.255` |
| `172.16.0.0/12` | `172.16.0.0` a `172.31.255.255` |
| `192.168.0.0/16` | `192.168.0.0` a `192.168.255.255` |

Os prefixos indicam o tamanho desses blocos, que podem ser subdivididos; eles não equivalem a uma única rede das antigas classes A, B e C.

### Tradução de Endereços e Portas:

O **NAT** (*Network Address Translation*) altera endereços IP na passagem entre redes. No caso comum de acesso à Internet, o equipamento substitui o endereço privado de origem por um endereço público e mantém as associações necessárias para traduzir o tráfego de retorno.

Quando vários dispositivos compartilham um mesmo IPv4 público utilizando também as portas TCP ou UDP, o mecanismo é chamado **NAPT** (*Network Address Port Translation*), também conhecido como **PAT** (*Port Address Translation*).

As **portas**, apresentadas no modelo de camadas, distinguem comunicações entre aplicações. O tradutor associa endereços, portas e protocolo, podendo trocar a porta de origem para diferenciar comunicações. Na resposta, essa associação recupera o endereço e a porta internos; a porta não identifica permanentemente um aparelho.

> O NAT básico traduz endereços; o NAPT/PAT também utiliza portas. No uso cotidiano, “NAT” frequentemente designa esse segundo funcionamento. A tradução não substitui um *firewall*: traduzir endereços e decidir quais comunicações são permitidas são funções diferentes.

### Tabela de Rotas e Prefixo Mais Longo:

Cada entrada associa um **prefixo de destino** a uma interface de saída e, quando necessário, a um próximo roteador. Se várias entradas corresponderem ao destino, utiliza-se a de **prefixo mais longo**, pois ela representa o grupo mais específico.

Uma rota `/26` é mais específica que uma `/24` que a contém. Para o destino `146.164.2.70`, uma entrada para `146.164.2.64/26` tem precedência sobre uma para `146.164.2.0/24`, se ambas estiverem disponíveis para o encaminhamento.

> Essa seleção usa os prefixos das entradas da tabela, não uma máscara enviada pela máquina de origem. O cálculo das melhores rotas alimenta a tabela; a consulta por pacote utiliza o resultado desse trabalho.

### Rota Padrão (*Default Route*):

É utilizada quando não existe uma rota mais específica para o destino. No IPv4, é representada por **`0.0.0.0/0`**: como seu prefixo tem comprimento zero, corresponde a qualquer endereço, mas tem a menor especificidade possível.

Considere redes identificadas como `20`, `30`, `40` e `50`: a rede `20` é alcançada pela interface `1`; as demais, pela interface `2`. Se esta também for a saída adequada para outros destinos, a tabela pode utilizar:

| Destino | Encaminhamento |
| --- | --- |
| Rede `20` | Interface `1`. |
| Demais destinos, sem rota mais específica | Interface `2`, pela rota padrão. |

Os números são rótulos; em uma tabela IPv4 real, utilizam-se prefixos. O ***gateway* padrão** é o próximo roteador associado à rota padrão, quando necessário. Essa configuração evita listar todos os destinos que utilizam a mesma saída, sem exigir que o próximo equipamento seja “maior”.

Nem todo roteador possui rota padrão. **Sem uma rota aplicável, o pacote é descartado.**

> No Linux, `ip -4 route` e `ip -6 route` mostram as rotas IPv4 e IPv6: quais redes são alcançadas por cada interface e próximo salto. A entrada `default` identifica a rota padrão; quando presentes, `via` indica o próximo roteador e `dev`, a interface de saída.
## Roteamento:

O **algoritmo de roteamento** define como avaliar caminhos; o **protocolo de roteamento** estabelece a troca de informações entre equipamentos. “Melhor caminho” significa o mais adequado aos critérios adotados, como custo ou políticas administrativas, não necessariamente o menor percurso físico.

### Funções e Tipos de Roteadores:

O roteador interliga redes e utiliza rotas configuradas manualmente ou aprendidas por protocolos. Além das funções centrais apresentadas na abertura, pode oferecer NAT, DHCP e ***firewall*** (regras para permitir ou bloquear tráfego), sem que esses serviços sejam obrigatórios.

> Roteadores separam domínios de *broadcast* de enlace: um quadro de difusão recebido não é simplesmente repetido nas outras redes. O *broadcast* IPv4 direcionado será distinguido adiante.

- **Roteadores especializados:** Projetados para redes, podem acelerar o encaminhamento com chips dedicados. O cálculo das rotas e outras funções de controle podem continuar em *software*.
- **Roteadores de uso geral:** Computadores configurados para encaminhar pacotes, utilizando sistemas como Linux. Suas interfaces não precisam corresponder a duas placas físicas distintas.

> Equipamentos especializados são comuns em provedores; computadores de uso geral também atendem laboratórios e pequenas redes. O desempenho depende dos recursos e das funções executadas.

### Algoritmos e Características Desejáveis:

Um algoritmo precisa equilibrar a qualidade dos caminhos com o trabalho para mantê-los:

- **Seleção de caminhos:** Escolher rotas adequadas às métricas e às políticas utilizadas.
- **Convergência:** Alcançar informações consistentes após mudanças, como a queda de um enlace, reduzindo o período de adaptação.
- **Robustez:** Lidar com falhas e informações temporariamente desatualizadas, evitando instabilidade persistente.
- **Escalabilidade:** Continuar viável conforme aumenta o número de redes e equipamentos.
- **Consumo de recursos:** Limitar o processamento, a memória e o tráfego de controle necessários à manutenção das rotas.

### Classificações dos Algoritmos:

As classificações descrevem aspectos diferentes e podem ser combinadas. Um algoritmo distribuído, por exemplo, pode trabalhar com informações globais sobre a topologia do domínio em que opera.

| Critério | Alternativas |
| --- | --- |
| **Atualização** | **Estático:** rotas configuradas pelo administrador. **Dinâmico:** rotas aprendidas e atualizadas por protocolos, conforme as mudanças detectadas. |
| **Estrutura** | **Plano:** organização sem uma divisão hierárquica do domínio de roteamento. **Hierárquico:** organização em domínios ou áreas, limitando o detalhamento necessário em cada parte. |
| **Informação disponível** | **Local ou parcial:** utiliza informações recebidas dos vizinhos, sem precisar conhecer toda a topologia. **Global:** utiliza um mapa da topologia do domínio considerado. |
| **Local do cálculo** | **Centralizado:** uma entidade concentra o cálculo das rotas. **Distribuído:** os roteadores participam desse cálculo e trocam informações entre si. |

Atualizações dinâmicas podem ser periódicas ou motivadas por mudanças. A centralização também permite atualizar rotas; não exige uma rede imutável.

Quanto ao **tipo de entrega**, distinguem-se *unicast* (um destinatário), *broadcast* (todos dentro de um âmbito definido) e *multicast* (um grupo).

> Endereçamento estático/dinâmico descreve a configuração dos endereços; roteamento estático/dinâmico descreve a origem e a atualização das rotas.

### Métricas de Roteamento:

São medidas utilizadas para comparar caminhos. Um algoritmo utiliza as métricas previstas em seu funcionamento; não precisa avaliar todas, nem medir continuamente as condições reais do tráfego.

- **Número de saltos (*Hop Count*):** Quantidade de saltos ao longo do caminho. Quando esse é o único critério, prefere-se o menor número, mesmo que outro caminho tenha enlaces mais rápidos.
- **Taxa de transmissão e carga:** A taxa indica a capacidade do enlace; a carga representa sua utilização. São fatores diferentes: um enlace de grande capacidade também pode estar congestionado.
- **Atraso (latência):** Tempo associado à passagem dos dados por um enlace ou caminho, conforme a medida utilizada.
- **Taxa de erro:** Indica a ocorrência de erros na transmissão e pode ser considerada na avaliação da qualidade de um enlace.
- **Disponibilidade:** Indica a continuidade de funcionamento de um enlace ou caminho, considerando suas interrupções.
- **Custo:** Valor atribuído ao enlace ou à rota para permitir comparação. Pode ser configurado, derivado de uma característica ou calculado pela combinação de fatores; não precisa representar dinheiro nem reunir todas as métricas anteriores.

A maior capacidade ou menor taxa de erro só influencia a escolha se participar do critério utilizado.

### Roteamento por Vetor de Distância:

Cada roteador informa aos vizinhos os destinos conhecidos e seus custos. Sem precisar do mapa completo, combina a distância anunciada pelo vizinho com o custo para alcançá-lo. O **RIP** (*Routing Information Protocol*) é um protocolo dessa família.

> É como consultar vizinhos sobre destinos e distâncias, sem receber a descrição de todo o percurso.

1. **Conhecimento inicial:** O roteador identifica suas redes diretamente conectadas e as rotas já configuradas. Em uma representação abstrata do algoritmo, a distância de um nó até ele mesmo é zero.
2. **Troca de informações:** Recebe dos vizinhos anúncios de destinos e custos, e divulga suas próprias informações. A periodicidade e a existência de atualizações parciais dependem do protocolo.
3. **Cálculo:** Soma o custo de alcançar o vizinho ao custo que ele anunciou. Se cada salto tiver custo `1`, alcançar um destino pelo vizinho acrescenta `+1` à distância informada por ele.
4. **Atualização:** Inclui novos destinos alcançáveis e adota alternativas de menor custo. Se a informação vier do vizinho utilizado pela rota atual, também considera pioras, inclusive a indicação de que o destino ficou inalcançável.

**Tratamento de falhas:** Ao detectar a perda de um caminho, o roteador invalida a rota correspondente. A falha pode ser percebida diretamente ou pela ausência de atualizações durante um período. O protocolo pode anunciar um custo chamado **infinito**, que representa um destino inalcançável.

Os vizinhos atualizam as rotas que dependiam daquele caminho e podem adotar alternativas, se existirem. Não há garantia de uma segunda rota já disponível. No RIP, o valor `16` representa infinito; as distâncias utilizáveis vão até 15 saltos.

A vantagem é a simplicidade do cálculo. A convergência pode ser lenta: informações antigas levam vizinhos a anunciar caminhos que já não funcionam, inclusive formando *loops* temporários.

### Roteamento Hierárquico e Sistemas Autônomos:

Além da agregação de destinos apresentada no CIDR, o **roteamento hierárquico** organiza a troca de informações em domínios, limitando o detalhamento necessário em cada parte.

Um **Sistema Autônomo (AS)** reúne redes e roteadores sob uma administração com política de roteamento definida. Não equivale a qualquer sub-rede e pode ter divisões internas.

- **Roteamento interno:** Estabelece caminhos dentro de um AS. O conhecimento da topologia depende do protocolo e de sua organização em áreas; nem todo roteador precisa conhecer todos os detalhes internos.
- **Roteamento externo:** Permite trocar informações de alcance entre sistemas autônomos, considerando também suas políticas de interconexão.
- **Roteadores de borda:** Interligam o AS a outros sistemas autônomos. Rotas externas, ou uma rota padrão quando suficiente, permitem orientar o tráfego interno até uma saída adequada.

Essa organização controla o alcance das informações divulgadas. **Um AS não corresponde necessariamente a uma única rota**, e seus roteadores internos podem conhecer rotas externas.

## Distribuição por *Broadcast*:

Uma transmissão em *broadcast* destina a mesma informação a todos os participantes de um âmbito definido. A difusão IPv4 em uma sub-rede deve ser distinguida da distribuição por uma topologia de roteadores.

### *Broadcast* Limitado e Direcionado:

- **Limitado (`255.255.255.255`):** Destina-se à rede local. Em *Ethernet*, o emissor utiliza um quadro de difusão, que pode ser distribuído pelos *switches* daquele domínio. O roteador não encaminha esse pacote para outras redes.
- **Direcionado:** Utiliza o endereço de *broadcast* de uma sub-rede, obtido com os *bits* de *host* em um. Por exemplo, `192.168.10.255` é o *broadcast* de `192.168.10.0/24`. Quando recebido de outra rede e explicitamente permitido, pode ser encaminhado até a sub-rede e então transmitido por difusão no enlace final.

O recebimento e o encaminhamento de *broadcast* direcionado devem estar **bloqueados por padrão nos roteadores**, devido à possibilidade de abuso. Portanto, essa entrega remota não deve ser presumida como disponível pela Internet.

Para distribuir informação por uma topologia, existem modelos como inundação e árvores de cobertura. Eles não representam o funcionamento automático de um *broadcast* IPv4 pela Internet.

### Inundação (*Flooding*):

Cada roteador que recebe a mensagem a replica pelas saídas participantes da distribuição, com exceção da interface de chegada. Isso permite alcançar os nós conectados, mas pode produzir várias cópias e *loops*: um pacote pode retornar por outro caminho mesmo sem ser devolvido diretamente ao vizinho que o enviou.

Para limitar essa circulação, podem ser empregados mecanismos complementares:

- **Identificação e *cache*:** O equipamento registra as mensagens processadas recentemente e descarta cópias repetidas. A identificação precisa distinguir as mensagens dentro do mecanismo utilizado, por exemplo, associando origem e número de sequência.
- **Limite de saltos:** Restringe a distância que uma cópia pode percorrer. No encaminhamento IPv4, o **TTL** (*Time to Live*) é reduzido pelos roteadores; quando se esgota, o pacote é descartado.

O *cache* reduz repetições; o limite de saltos impede circulação indefinida, inclusive após a remoção de registros antigos. Ainda pode haver desperdício com cópias descartadas nos equipamentos seguintes.

> O identificador usado no controle de duplicatas depende do algoritmo ou protocolo. Ele não deve ser confundido automaticamente com o campo de identificação do cabeçalho IPv4.

### Árvore de Cobertura (*Spanning Tree*):

Seleciona um conjunto de ligações que conecta todos os nós participantes **sem formar ciclos**. Nessa árvore, existe um único caminho entre cada par de nós. Uma mensagem pode ser replicada nas bifurcações para alcançar os diferentes ramos, evitando a circulação repetida da inundação sem controle.

Os enlaces excluídos da árvore continuam existindo fisicamente e podem ser utilizados por outras distribuições ou comunicações. Essa seleção não significa concentrar todo o tráfego da rede em um único trajeto.

- **Vantagem:** Reduz cópias redundantes ao organizar o percurso da distribuição.
- **Limitações:** Os caminhos escolhidos podem não ser os melhores para todos os emissores e podem concentrar tráfego em determinados enlaces. Uma falha pode exigir atualização da árvore, interrompendo temporariamente parte da distribuição; não é obrigatório recalcular tudo do zero.

> A ideia de uma estrutura sem ciclos também aparece no STP, apresentado no apêndice de redes locais. Aqui, porém, trata-se da seleção de caminhos para distribuir pacotes, não necessariamente do bloqueio de portas de *switches*.

## *Multicast*:

Envia a mesma informação para um **grupo de destinatários**. Os roteadores replicam o pacote onde os caminhos se separam, evitando cópias independentes desde a origem e entrega indiscriminada a todos.

### Endereçamento de Grupo e IGMP:

No IPv4, o *multicast* utiliza a faixa `224.0.0.0/4`, de `224.0.0.0` a `239.255.255.255`, historicamente chamada classe D. O endereço identifica um **grupo**, não substitui o endereço individual da interface que participa dele.

> É como uma frequência de rádio: o emissor transmite para um endereço de grupo, como `224.1.1.1`, e os receptores interessados “sintonizam” essa transmissão. O endereço individual continua existindo para as demais comunicações.

O **IGMP** (*Internet Group Management Protocol*) informa aos roteadores locais o interesse dos dispositivos em grupos IPv4. Já o roteamento *multicast* organiza os caminhos entre roteadores até as redes com receptores.

- **Grupos permanentes:** Possuem endereços atribuídos de forma permanente. Podem ter qualquer quantidade de membros, inclusive nenhum; o que permanece é a atribuição do endereço, não uma transmissão contínua.
- **Grupos temporários:** Existem enquanto houver participantes e utilizam endereços atribuídos para esse uso. O IGMP comunica a participação, mas não funciona como um serviço global que distribui e libera esses endereços.

> O *multicast* é útil em redes administradas, como IPTV e sistemas internos de informação. A comunicação entre redes exige suporte e políticas compatíveis; escolher um endereço de grupo não garante alcance pela Internet.

### Árvores de Distribuição:

A árvore precisa alcançar **os receptores interessados**, não todos os nós da rede. Roteadores sem receptores locais podem participar do caminho até receptores mais adiante. Há árvores por origem e árvores compartilhadas por vários emissores; sua construção depende do protocolo.

### Encaminhamento por Caminho Reverso (RPF):

O **RPF** (*Reverse Path Forwarding*) testa a interface de chegada. Na árvore baseada na origem:

1. **Identificação:** O roteador verifica a origem do pacote e a interface pela qual ele chegou.
2. **Consulta:** Determina qual interface utilizaria para alcançar essa origem, a partir das informações de roteamento usadas pelo mecanismo, normalmente derivadas das rotas *unicast*.
3. **Decisão:** Se as interfaces coincidirem, o pacote passa no teste e pode ser replicado pelas saídas previstas para a distribuição. Caso contrário, é descartado nesse modelo de encaminhamento.

É a pergunta: “Para voltar até esse transmissor, eu usaria a interface por onde o pacote chegou?”. Passar no teste indica conformidade com as rotas utilizadas, não o menor percurso físico. O protocolo ainda precisa determinar as saídas participantes da distribuição.

> Em árvores compartilhadas, o teste pode usar como referência o ponto central da distribuição, em vez do transmissor original.

### Árvore por Origem com Inundação e Poda:

Uma estratégia para construir árvores por origem começa distribuindo o tráfego pelo domínio e depois remove ramificações sem interessados:

1. **Inundação (*Flood*):** O tráfego é distribuído inicialmente, utilizando verificações como RPF para controlar o encaminhamento. Nessa fase, pode alcançar redes sem receptores interessados.
2. **Poda (*Prune*):** Um roteador informa ao anterior que determinado ramo não precisa receber o tráfego quando não há receptores locais nem interesse nos ramos seguintes. A informação permite interromper o envio naquele sentido.

> A árvore começa abrangente e vai “encolhendo” à medida que os galhos sem interessados são podados. Mudanças na participação exigem atualizar essa distribuição.

Essa estratégia aproveita o conhecimento dos caminhos até a origem, mas pode desperdiçar tráfego na fase inicial. **Nem toda árvore baseada na origem precisa começar com inundação**; essa é uma forma específica de construí-la.

### Árvores Compartilhadas Baseadas em Núcleo:

Nesse modelo, um roteador atua como **núcleo** da árvore compartilhada pelo grupo. Conforme o protocolo, existe um ponto de encontro (*Rendezvous Point*, RP) com função correspondente na organização inicial da distribuição.

Os roteadores com receptores interessados enviam solicitações de associação em direção a esse ponto. A árvore cresce conforme há demanda, em vez de começar inundando todo o domínio para depois procurar os interessados.

Na distribuição pela árvore compartilhada, os dados alcançam essa estrutura e seguem por seus ramos até os receptores. A forma de levar o tráfego do emissor até a árvore depende do protocolo; não exige que todo emissor envie diretamente um pacote *unicast* ao núcleo.

- **Vantagem:** Evita a inundação inicial de todo o domínio e permite compartilhar parte das informações de distribuição entre diferentes emissores.
- **Limitações:** Os caminhos pela árvore compartilhada podem ser mais longos que os caminhos diretos entre uma origem e seus receptores. O núcleo também exige atenção quanto à disponibilidade e à concentração de tráfego.

A árvore precisa ser atualizada quando participantes entram ou saem. Conforme o protocolo, também pode haver mudança para caminhos baseados na origem.

## Fontes:

- MAIA, Luiz Paulo. *Arquitetura de Redes de Computadores*. 2. ed. Rio de Janeiro: LTC, 2013.
- BRADEN, R. (ed.). *Requirements for Internet Hosts — Communication Layers*. RFC 1122. [S. l.]: RFC Editor, 1989. Disponível em: https://www.rfc-editor.org/rfc/rfc1122.html. Acesso em: 21 out. 2026.
- PLUMMER, D. C. *An Ethernet Address Resolution Protocol*. RFC 826. [S. l.]: RFC Editor, 1982. Disponível em: https://www.rfc-editor.org/rfc/rfc826.html. Acesso em: 21 out. 2026.
- REKHTER, Y. et al. *Address Allocation for Private Internets*. RFC 1918. [S. l.]: RFC Editor, 1996. Disponível em: https://www.rfc-editor.org/rfc/rfc1918.html. Acesso em: 21 out. 2026.
- SRISURESH, P.; EGEVANG, K. *Traditional IP Network Address Translator (Traditional NAT)*. RFC 3022. [S. l.]: RFC Editor, 2001. Disponível em: https://www.rfc-editor.org/rfc/rfc3022.html. Acesso em: 21 out. 2026.
- MALKIN, G. *RIP Version 2*. RFC 2453. [S. l.]: RFC Editor, 1998. Disponível em: https://datatracker.ietf.org/doc/rfc2453/. Acesso em: 21 out. 2026.
- HAWKINSON, J.; BATES, T. *Guidelines for Creation, Selection, and Registration of an Autonomous System (AS)*. RFC 1930. [S. l.]: RFC Editor, 1996. Disponível em: https://www.rfc-editor.org/rfc/rfc1930.html. Acesso em: 21 out. 2026.
- SENIE, D. *Changing the Default for Directed Broadcasts in Routers*. RFC 2644. [S. l.]: RFC Editor, 1999. Disponível em: https://www.rfc-editor.org/rfc/rfc2644.html. Acesso em: 21 out. 2026.
- DEERING, S. *Host Extensions for IP Multicasting*. RFC 1112. [S. l.]: RFC Editor, 1989. Disponível em: https://www.rfc-editor.org/rfc/rfc1112.html. Acesso em: 21 out. 2026.
- CISCO. *IP Multicast Technology Overview*. In: *IP Multicast Configuration Guide, Cisco IOS XE 17.x*. [S. l.]: Cisco, [s. d.]. Disponível em: https://www.cisco.com/c/en/us/td/docs/routers/ios/config/17-x/ip-multicast/b-ip-multicast/m_imc_tech_oview-0.html. Acesso em: 21 out. 2026.
