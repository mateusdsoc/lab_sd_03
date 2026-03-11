# Reflexao 

## T5

**A coluna "Melhor para" sugere que gRPC é preferível para microserviços internos enquanto REST domina APIs públicas. Por que a **tipagem forte do .proto** é vantagem em microserviços internos mas potencial barreira em APIs públicas?**

    A tipagem forte do .proto é vantagem para microsserviços uma vez que, quando as aplicações são distribuídas em microsserviços, diversas equipes podem estar focadas em seu próprio projeto sem estarem atentas ao que os outros microsserviços estão utilizando, dessa forma, para não ocorrer nenhum problema de comunicação e nãoe existir o risco de dispersão tecnológica a tipagem forte do .proto é utilizada. Assim se alguém mudar uma variável de int para string o código não chega em produção porque nem vai compilar. Entretanto, essa abordagem é uma barreira de entrada muito grande para APIs públicas, uma vez que quem quiser utilizar o código terá que usar binário e um .proto, desse modo, é mais fácil utilizar o REST porque utiliza JSON sendo mais amigável e requisições, qualquer um com navegador utiliza.

**Por que o REST é chamado de "orientado a recursos" e o RPC de "orientado a ações"? Dê um exemplo concreto de uma operação (ex: "cancelar um pedido") modelada das duas formas e discuta as implicações de cada escolha.**

    O REST é "orientado a recursos" porque por mais que a existe o conjunto de verbos (GET, POST, UPDATE, DELETE) o foco principal está no que o usuário chama, e normalmente o método foca em um substantivo, a URL é modelada ao longo de substantivos, por exemplo: "PUT /pedidos/12345/status". Nesse exemplo a url se baseia em torno dos status de um pedido. Já o RPC tem suas funções e procedimentos focados em torno dos verbos "proxy.cancelar_pedido(12345)", o foco é cancelar o pedido.

**Stubs e skeletons: Explique, com base no que você observou na Tarefa 2, o papel do stub (cliente) e do skeleton (servidor) em qualquer sistema RPC. Por que esses componentes existem — o que aconteceria sem eles?**

    Stubs são uma representação para o cliente, de uma função que não existe localmente, mas em um servidor. O skeleton é seu equivalente para o servidor. O stub percebe que o usuário fez a chamada de um método, empacota os dados (marshalling), envia o pacote pela rede, quando o pacote chega quem recebe é skeleton, que fica ouvindo a rede e desempacota os dados, invoca a função real existente o servidor, e manda o resultado de volta, o skub recebe a resposta, faz o unmarshalling e converte eles para o objeto da resposta (no códiogo Python). Ees são essenciais porque, sem eles não seria possível chamar o método e receber a resposta.

**REST não é RPC: Fielding (2000) critica explicitamente o uso de RPC sobre HTTP por violar a restrição de interface uniforme do REST. Com base nas Tarefas 1 e 3, descreva uma diferença fundamental de modelagem entre as duas abordagens, usando um exemplo concreto da sua implementação.**

    O fielding critica o RPC por utilizar o HTTP apenas como "túnel" para enviar mensagens, enquanto a ideia principal do HTTP é utilizar os métodos (POST, PUT...), não ignorando a semântica nativa do protocolo. Enquanto o RPC ignora fazendo: proxy.calcular("soma", 10.0, 3.0), o REST respeita focando na modelagem por recurso com requests.post("http://localhost:5050/produtos").

**O .proto da Tarefa 4 define o campo resultado como double. Se você precisasse adicionar um novo campo unidade: string ao RespostaCalculo sem quebrar clientes existentes, como o Protobuf lida com isso? E como o REST (sem schema) lidaria com a mesma mudança?**

    Caso o novo campo for adicionado ao código, o servidor passa a empacotar e enviar o valor. Se o cliente ainda usa o contrato antigo (sem a nova unidade), nesse caso o gRPC ignora todos os novos campos e não dá erro, ele só não vai reconhecer, portanto não vai mostrar ao cliente. O REST se comportaria da mesma forma, o servidor vai enviar mas o cliente antigo não vai ter ciência daquele valor.

**Considere o seguinte cenário: uma startup precisa expor uma API de pagamentos tanto para parceiros externos (apps de terceiros) quanto para comunicação interna entre 10 microsserviços. Que tecnologia você recomendaria para cada caso e por quê? Baseie-se nos critérios do comparativo da Tarefa 5.**

    Para a API pública eu recomendaria o REST com JSON porque é tecnologia com menor barreira de entrada, se destacando por ser mais intuitiva com os métodos HTTP, além da legibilidade por utilizar JSON. Nos microsserviços por outro lado, a recomendação adequada é o gRPC. Por mais que a distribuição dos arquivos .proto deve ser feita, ela se torna fácil porque o tráfego de informações ocorre dentro da empresa somente, acompanhada pela forte tipagem que torna o desevolvimento entre mais de uma equipe seguro e a rápida serialização binária do Protobuf.

**O Lab 04 mostrou que transparência excessiva pode ser prejudicial. Como isso se aplica ao RPC? Em que situação a transparência do RPC — que faz uma chamada remota parecer local — pode levar um desenvolvedor a tomar uma decisão de design errada?**

    O RPC tem a intenção de tornar a rede invisível aos olhos do cliente, fazendo uma chamada remota parecer local. Entretanto, essa ilusão pode acarretar em barreiras e falhas. Se um desenvolvedor acha que chamda que ele está fazendo é local, ele pode criar um loop de requisições, executando-as uma por uma utilizando o prox.somar() por exemplo. Entretanto, as chamadas de vários métodos um por um nesse caso não são eficazes porque o método não está local, então passa por diversas etapas de comunicação com a rede somente para executar uma soma, não seria portanto eficaz, podendo causar até um try.except.TimeoutError. Caso o desevolvedor tivesse ciência que a chamada é remota (se a transparência fosse menor) ele poderia adaptar o código para fazer todas as requisições de uma vez, tornando o código eficiente.
