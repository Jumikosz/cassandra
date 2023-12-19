
### Trabalho Pós Graduação Engenharia de dados | Infnet | Infraestrutura Cassandra ###

# 1. Explique, com suas palavras, as diferenças entre bases de dados SQL e NoSQL, apresentando exemplos de uso para cada uma delas.

Bancos de Dados SQL:

Bancos de dados SQL usam um formato de tabela para organizar os dados, como se fosse uma planilha e as tabelas se relacionam umas com as outras;
Possuem um esquema estruturado e fixo, ou seja, a estrutura das tabelas é definida antes;
Transações ACID: Garantem propriedades ACID (Atomicidade, Consistência, Isolamento e Durabilidade) para garantir a integridade dos dados.
Aumentar a capacidade de um banco de dados SQL geralmente envolve a adição de mais recursos a um único servidor pois sua escalabilidade é vertical.
Exemplo de Banco SQL:MySQL, PostgreSQL, Microsoft SQL Server.

Bancos de Dados NoSQL:

Os bancos de dados NoSQL não seguem o modelo relacional. Eles podem ter modelos de dados flexíveis, como documentos, grafos, pares chave-valor ou famílias de colunas.
Possuem esquema dinâmico, permitindo a adição de novos campos sem a necessidade de modificar esquemas existentes, proporcionando mais flexibilidade.
Em alguns casos, eles podem sacrificar a consistência imediata em troca de uma melhor disponibilidade e tolerância a falhas.
É fácil de escalar, necessita somente adicionar mais servidores à medida que a carga aumenta (escalabilidade horizontal).

MongoDB (documento), Redis (chave-valor), Neo4j (grafo), Cassandra (Banco de Dados de Coluna)
Resumindo, SQL é como uma planilha organizada, ótima para estruturas de dados fixas, enquanto NoSQL é mais flexível, adequado para situações em que a estrutura dos dados pode mudar frequentemente, como em aplicativos web dinâmicos.


# 2.Escolha uma base de dados pública brasileira, que será o alvo para condução de seu Projeto de Disciplina. Esta base será carregada para uma infraestrutura Cassandra, para dar suporte à geração de um processo de análise de dados.

A base escolhida é de avaliações de filmes da Netflix, ela é composta por dois arquivos CSV, em um deles temos os nomes dos filmes e ano de gravação, na outra têm os usuários que avaliaram e a nota de cada avaliação. 
Para a execução desse trabalho foi feita a junção das duas bases construindo o dataset avaliacoes_por_filme.
A transformação dos datasets é feita nesse notebook:

Incluir link notebook 1


# 3.Defina pelo menos uma pergunta de negócio que será o objetivo de seu processo de análise a ser desenvolvido a partir dos dados armazenados na infraestrutura Cassandra.

Algumas perguntas que devem ser respondidas neste trabalho:

“Qual a média de avaliação de cada filme?”
“Quais os filmes que possuem a média de avaliação (rating) maior que 4,5?”
“Qual a quantidade de avaliações que cada filme possui?”
“Qual a quantidade de avaliações que cada usuário fez?”

Apresente um planejamento básico da infraestrutura necessária para comportar os dados que deseja importar.
Criação de Rede Docker:
Criar Docker para facilitar a comunicação entre os containers do Cassandra. Isso pode ser feito usando o seguinte comando:

# $docker network create --driver bridge cassandra-network

Containers Cassandra:
Criar containers Docker para o Cassandra utilizando a imagem oficial do Apache Cassandra disponível no Docker Hub. Exposição da porta 9042, necessária para comunicação, e o mapeamento de pasta local para uma determinada pasta do container, utilizei a pasta /tmp.

   docker run --name filmes: Criando o container “filmes”
 	--network cassandra-network: Especifica a rede que será utilizada pelo container
	-p 9042:9042: Mapeamento das portas
	/Users/julia/Downloads/Cassandra:/tmp: Mapeamento de diretórios
	cassandra:4.1.3: Imagem que será utilizada.

$docker run --name filmes --network cassandra-network -p 9042:9042 -v /Users/julia/Downloads/Cassandra:/tmp cassandra:4.1.3

Verificação do Status do Container:
# $docker ps

Teste de Conexão:
Conectando ao container do Cassandra usando um cliente CQLSH para verificar se o banco de dados está acessível:
# $docker exec -it filmes cqlsh

Conferência se o diretório foi mapeado corretamente:

# $cd tmp/ - para entrar na pasta mapeada do container

Criação do Keyspace, como era somente para a execução do trabalho foi feito com SimpleStrategy e fator de replicação 1:

# $CREATE KEYSPACE filmes WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1};


Criação da tabela avaliacoes_por_filme, primary key sendo Movie_id e chave de clusterização User_id:

# $CREATE TABLE avaliacoes_por_filme (
#    Movie_ID INT,
#    User_ID INT,
#    Name TEXT,
#    Rating SMALLINT,
#    PRIMARY KEY ((Movie_ID), User_ID)
# );

Cópia do arquivo avaliacoes_por_filme.csv para a tabela criada e comprovação de carga:

# $COPY avaliacoes_por_filme FROM '/tmp/avaliacoes_por_filme.csv' WITH DELIMITER =',' AND HEADER = TRUE;

Após a carga de dados foi utilizado o DataSpell para fazer a conexão do Cassandra com Spark para serem feitas análises mais aprofundadas. Todas essas configurações estão no notebook abaixo:
















