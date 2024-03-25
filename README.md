# AWS-DATA-ENGINEER

O presente projeto tem por finalidade desenvolver uma infraestrutura na AWS para uma empresa fictícia que produz bicicletas. As seguintes tecnologias foram utilizadas:

- AWS S3 para armazenamento de dados;
- AWS Glue para análise e tratamento de dados com o Spark;
- AWS Lambda para carregar os dados tratados para o banco de dados;
- AWS RDS para criação de um banco relacional postgresql;
- DBeaver para manipular o SGBD.

## ETL

OS asquivos do projeto foram alocados no S3 devido a enorme compatibilidade deste serviço com as demais ferramentas da AWS.

<p align="center">
    <img src="https://imgur.com/jqXSuk7.png" alt="codigo2">
</p>

  Em seguida a ferramenta Glue foi utilizada para realizar uma análise exploratória dos dados assim como o tratamendo dos mesmo. Todo o processo foi feito com o Spark,  uma poderosa estrutura de processamento distribuído, conhecida por sua capacidade de lidar com grandes volumes de dados de maneira eficiente e escalável. A análise  exploratória dos dados envolveu:

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

## SGBD

Após o processo de ETL, foi criado um banco de dados PostgreSQL no RDS (Amazon Relational Database Service), que é um serviço gerenciado de banco de dados relacional oferecido pela AWS. O RDS simplifica tarefas de administração de banco de dados, como provisionamento, backup e escalabilidade automática, ao mesmo tempo em que fornece alto desempenho e confiabilidade. O PostgreSQL, como escolha para o RDS, apresenta várias vantagens. Ele é um sistema de gerenciamento de banco de dados relacional de código aberto amplamente adotado e conhecido por sua robustez, confiabilidade e recursos avançados.

<p align="center">
    <img src="https://imgur.com/DeTOMuE.png" alt="codigo2">
</p>

O DBeaver foi utilizado para fazer uma conexão ao SGBD, enviar os dados do S3 para o Postgre por meio da extensão aws_s3, criar tabelas e realizar consultas.  

<p align="center">
    <img src="https://imgur.com/93NFuJz.png" alt="codigo2">
</p>


<p align="center">
    <img src="https://imgur.com/jy5uUQR.png" alt="codigo2">
</p>

