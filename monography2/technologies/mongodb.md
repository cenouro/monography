# Database
MongoDB é uma base de dados NoSQL baseada em documentos. 
O termo NoSQL ainda não possui uma definição consolidada; a
definição mais difundida é de "Not Only SQL". 

A dificuldade de definir o termo NoSQL ocorre pois 
existe uma diversidade de databases não relacionais 
mas baseadas em conceitos diferentes. Por exemplo: documment based (Mongo), 
column based (Cassandra), graph based (Neo4J), key-value based (Reddis), 
entre outras.
Apesar de haver tal diversidade de conceitos e implementações,
o objetivo principal de base de dados não-relacionais é reduzir a
"impedância de representação".

Em sistemas que utilizam uma base de dados relacional, existe uma severa diferença
entre o modelo de dados definido na base de dados e o modelo de
dados definido no software em si. No software, dados são
comumente representados por objetos. Na base de dados relacional,
um objeto no software pode ser particionado em diversas tabelas.
Nesse caso, para recuperar o objeto, são necessários joins.
Dependendo da complexidade do objeto em si (e, consequentemente,
da complexidade de seu particionamento), queries em SQL podem
rapidamente se tornar muito complexas, reduzindo a
manutenibilidade do código. Além disso, joins invariavelmente
causam impactos na performance da base de dados.

Algumas estratégias podem ser utilizadas para lidar com a
impedância de representação de dados e/ou
performance/complexidade de joins. Views podem ser utilizadas
para reduzir complexidade de queries, e desnormalização de
tabelas reduz a impedância. Obviamente, desnormalização viola o
conceito de base de dados relacionais e views tornam o código
mais obscuro pois devem ser programadas no SGDB ao invés de serem
implementadas na codebase do software em si.

Outra estratégia consiste na utilização de ORMs. ORMs melhoram
consideravelmente a qualidade do código, pois almejam ser
independentes do SGBD utilizado. Outra vantagem é o
encapsulamento da tradução do objeto em ambas as direções. Porém,
ORMs podem ter um grande impacto na performance do software já
que necessitam implementar uma série de funcionalidades para
manter coerência de objetos em memória e na base de dados,
tradução de objetos e validações. A boa utilização de ORM requer
uma ORM com implementação madura, de desenvolvedores
experientes, boas ferramentas de instrumentação de código e meios
de reproduzir/analisar cenários reais da utilização do software.
Somente assim os desenvolvedores podem decidir quais trechos
devem delegar a ORM e quais trechos necessitam ser implementados
em SQL puro para melhor performance.

Mesmo com as grandes vantagens das ORMs, base de dados
relacionais ainda possuem uma grande desvantagem que tem levado
novos sistemas a serem implementados com base de dados
não-relacionais. Tal desvantagem é a escalabilidade. Base de
dados NoSQL foram projetadas para serem facilmente divididas em
diferentes servidores. Esse não é o caso com base de dados
relacionais. É possível escalar SGBDs horizontalmente, mas para
isso é necessário abrir mão da maior vantagem de SGBDs: ACID
compliance.

Após tal análise, optei por usar Mongo no desenvolvimento pois,
no melhor dos casos, SGBDs seriam "tão bons quanto" um sistema
NoSQL.

## MongoDB
MongoDB é uma base de dados baseada em documentos. Documentos
podem conter outros documentos. Isso permite grande flexibilidade
em como dados são representados; com relação a performance,
documentos permitem que dados relacionados (geralmente documentos
aninhados) sejam recuperados com apenas uma query, evitando
joins.

Documentos são representados externamente como objetos JSON. Por
esse motivo, a impedância de representação é quase nula.

Por ser baseado em documentos, Mongo é extremamente flexível. Não
há schema, e diferentes documentos de uma mesma coleção podem
conter documentos extremamente diferentes, tanto em relação aos
tipos de dados como em relação à estrutura dos documentos em si.
Isso evita que o desenvolvedor tenha que lidar com as
impertinências de um schema formal. O código pode ser modificado
apenas no software; a base de dados simplesmente continua
funcionando para que o desenvolvedor execute os testes.
Obviamente, a ausência de schemas possui grandes desvantagens, e
é considerada uma boa prática definir um schema formal através de uma
ORM antes que o código seja colocado no ambiente de produção.

Como Mongo é extremamente flexível, o workflow mais recomendado é
investir mais tempo de desenvolvimento no software em si do que
na modelagem de dados. Após estágios iniciais de desenvolvimento,
os diferentes objetos que devem ser persistidos na base
de dados possuem estruturas mais definidas; com isso, coleções,
documentos e índices podem ser implementados para representar da
melhor forma dados e queries utilizadas pelo software. Isso
proporciona melhor desempenho e reduz a impedância de
representação.

Mongo possibilita sharding de coleções. Sharding faz com que uma
coleção de documentos seja distribuida entre diferentes processos
Mongo. Shards podem residir em diferentes servidores, permitindo
escalabilidade horizontal. Shards também podem ser realizados em
uma mesma máquina, proporcionando aumento de desempenho, uma vez
que o bloqueamento para escrita é realizado no contexto de
processo, e não de máquina.
