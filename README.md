# AWS-DATA-ENGINEER

O presente projeto tem por finalidade desenvolver uma infraestrutura na AWS para uma empresa fictícia que produz bicicletas. As seguintes tecnologias foram utilizadas:

- AWS S3 para armazenamento de dados;
- AWS Glue para análise e tratamento de dados com o Spark;
- AWS RDS para criação de um banco relacional PostgreSQL;
- DBeaver para manipular o SGBD e enviar arquivos do S3 para o PostgreSQL por meio da extensão aws_s3.

## ETL

O Amazon S3 (Simple Storage Service) foi escolhido para armazenar os arquivos do projeto devido à sua ampla compatibilidade com os serviços da AWS, integração suave e eficiente com o ecossistema da plataforma. A localização na região Norte da Virgínia foi selecionada devido ao baixo custo dos serviços em comparação com outras regiões. O S3 oferece confiabilidade, durabilidade e escalabilidade, garantindo alta disponibilidade dos dados, proteção contra falhas e facilidade de expansão conforme as necessidades do projeto crescem. Todos os scripts do projeto estão disponíveis no topo desta página para fácil acesso e utilização.

<p align="center">
    <img src="https://imgur.com/jqXSuk7.png" alt="codigo2">
</p>

Em seguida, a ferramenta Glue foi utilizada para realizar uma análise exploratória dos dados, bem como o tratamento dos mesmos. Todo o processo foi feito com o Spark, uma poderosa estrutura de processamento distribuído, conhecida por sua capacidade de lidar com grandes volumes de dados de maneira eficiente e escalável. A análise exploratória dos dados envolveu:

- Exibição dos esquemas dos dataframes;
- Contagem de linhas de cada dataframe;
- Exibição das 5 primeiras linhas de cada dataframe;
- Verificação de valores nulos;
- Substituição da palavra "NULL" por uma string vazia;
- Substituição de "," por ".";
- Alocação dos arquivos tratados em uma nova pasta no S3.

<p align="center">
    <img src="https://imgur.com/RAQ79bU.png" alt="codigo2">
</p>

<p align="center">
    <img src="https://imgur.com/Rufrio0.png" alt="codigo2">
</p>

## SGDB

Após o processo de ETL, foi criado um banco de dados PostgreSQL no RDS (Amazon Relational Database Service), que é um serviço gerenciado de banco de dados relacional oferecido pela AWS. O RDS simplifica tarefas de administração de banco de dados, como provisionamento, backup e escalabilidade automática, ao mesmo tempo em que fornece alto desempenho e confiabilidade. O PostgreSQL, como escolha para o RDS, apresenta várias vantagens. Ele é um sistema de gerenciamento de banco de dados relacional de código aberto amplamente adotado e conhecido por sua robustez, confiabilidade e recursos avançados.

<p align="center">
    <img src="https://imgur.com/DeTOMuE.png" alt="codigo2">
</p>

O DBeaver foi utilizado para fazer uma conexão ao SGDB, enviar os dados do S3 para o PostgreSQL por meio da extensão aws_s3, criar tabelas e realizar consultas.

<p align="center">
    <img src="https://imgur.com/93NFuJz.png" alt="codigo2">
</p>

<p align="center">
    <img src="https://imgur.com/jy5uUQR.png" alt="codigo2">
</p>

O RDS obedeceu os critérios do seguinte fluxograma de banco de dados:

<p align="center">
    <img src="https://imgur.com/rzlln1o.png" alt="codigo2">
</p>

## Consultas no banco de dados

1) Escreva uma query que retorna a quantidade de linhas na tabela Sales.SalesOrderDetail pelo campo SalesOrderID, desde que tenham pelo menos três linhas de detalhes.

Query:
SELECT 
	SalesOrderID as id, 
	COUNT(*) AS qtd 
FROM testeRox.SalesOrderDetail as sod
GROUP BY SalesOrderID
HAVING qtd >= 3

<p align="center">
    <img src="https://imgur.com/zrmxWpF.png" alt="codigo2">
</p>

2) Escreva uma query que ligue as tabelas Sales.SalesOrderDetail, Sales.SpecialOfferProduct e Production.Product e retorne os 3 produtos (Name) mais vendidos (pela soma de OrderQty), agrupados pelo número de dias para manufatura (DaysToManufacture).

Query:
SELECT * 
FROM(
  SELECT 
  	ROW_NUMBER() OVER(PARTITION BY p.DaysToManufacture ORDER BY sum(sod.OrderQty) DESC) as pos,
  	p.DaysToManufacture AS dtm,
    	p.Name as nome,
    	SUM(sod.OrderQty) AS qtd
  FROM testeRox.SpecialOfferProduct sop 
  JOIN testeRox.Product p ON sop.ProductID = p.ProductID
  JOIN testeRox.SalesOrderDetail sod ON sop.SpecialOfferID = sod.SalesOrderDetailID
  GROUP BY nome, p.DaysToManufacture
  ) as posicao
WHERE pos <= 3

<p align="center">
    <img src="https://imgur.com/ptUMM5I.png" alt="codigo2">
</p>

3) Escreva uma query ligando as tabelas Person.Person, Sales.Customer e Sales.SalesOrderHeader de forma a obter uma lista de nomes de clientes e uma contagem de pedidos efetuados.

Query:
SELECT
	c.CustomerID AS id,
	CONCAT(p.FirstName, ' ', p.LastName) AS nome, 
	COUNT(*) AS qtd
FROM testeRox.SalesOrderHeader soh
JOIN testeRox.Customer c ON soh.CustomerID = c.CustomerID
JOIN testeRox.Person p ON c.PersonID = p.BusinessEntityID 
GROUP BY c.CustomerID, p.FirstName, p.LastName
ORDER BY qtd DESC;

<p align="center">
    <img src="https://imgur.com/aUDbbhx.png" alt="codigo2">
</p>


4) Escreva uma query usando as tabelas Sales.SalesOrderHeader, Sales.SalesOrderDetail e Production.Product, de forma a obter a soma total de produtos (OrderQty) por ProductID e OrderDate.

Query:
SELECT
    sod.ProductID AS id,
    p.Name AS nome,
    SUM(sod.OrderQty) AS qtd,
    CAST(soh.OrderDate AS DATE) AS data_pedido
FROM testeRox.SalesOrderDetail sod
JOIN testeRox.SalesOrderHeader soh ON sod.SalesOrderID = soh.SalesOrderID
JOIN testeRox.Product p ON sod.ProductID = p.ProductID
GROUP BY id, nome, data_pedido
ORDER BY data_pedido, qtd DESC;

<p align="center">
    <img src="https://imgur.com/U1sEXsA.png" alt="codigo2">
</p>


5) Escreva uma query mostrando os campos SalesOrderID, OrderDate e TotalDue da tabela Sales.SalesOrderHeader. Obtenha apenas as linhas onde a ordem tenha sido feita durante o mês de setembro/2011 e o total devido esteja acima de 1.000. Ordene pelo total devido decrescente.

Query:
SELECT 
	SalesOrderID as id,
	CAST(OrderDate AS DATE) AS data, 
	TotalDue AS total_devido
FROM testeRox.SalesOrderHeader
WHERE OrderDate BETWEEN '2011-09-01' AND '2011-09-30' AND TotalDue > 1000
ORDER BY total_devido;

<p align="center">
    <img src="https://imgur.com/OUGchY8.png" alt="codigo2">
</p>

