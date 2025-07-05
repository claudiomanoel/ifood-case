# Case Técnico Data Architect - iFood
## Objetivo
Esse desafio permitirá que você demonstre suas habilidades em
Engenharia de Dados/Software, Análise e Modelagem de Dados.
Neste case técnico, você deverá fazer a ingestão de alguns dados em
nosso Data Lake e pensar em uma forma de disponibilizá-los para os
consumidores. Para finalizar, você deverá realizar análises sobre os dados
disponibilizados.
Você deverá:
- Desenvolver uma solução para fazer a ingestão de dados referentes
às corridas de táxis de NY em nosso Data Lake;
- Disponibilizar os dados para os usuários consumirem (através de SQL,
por exemplo);
- Realizar algumas análises dos dados e mostrar os resultados;
## Dados Disponíveis
Os dados estão disponíveis no site da agência responsável por licenciar e
regular os táxis na cidade de NY: https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page
Num primeiro momento, precisamos que sejam armazenados e
disponibilizados os dados de Janeiro a Maio de 2023.
## Considerações
- Você pode considerar inicialmente armazenar todos os arquivos
originais em uma landing zone (que pode ser um bucket do S3, por
exemplo, ou qualquer outra tecnologia de sua escolha);
- Você pode considerar armazenar os dados estruturados/
transformados em uma camada de consumo (que pode ser um bucket
do S3, por exemplo, ou qualquer outra tecnologia de sua escolha);
- Você pode considerar manipular/limpar os dados que julgar
necessário;
- Você precisa garantir que as colunas **VendorID**,
**passenger\_count**, **total\_amount**,
**tpep\_pickup\_datetime** e **tpep\_dropoff\_datetime**
estejam presentes na camada de consumo. As outras colunas podem
ser ignoradas;
- Você pode considerar que no Data Lake não existe nenhuma tabela
criada, portanto, precisam ser modeladas e criadas.
## O Desafio
Você deverá entregar:
1. **Solução para ler os dados originais, fazer a ingestão no Data
Lake e disponibilizar para os usuários finais:**
2. Deve utilizar PySpark em alguma etapa;
3. Recomendamos usar Databricks Community Edition (https://
community.cloud.databricks.com/);
4. A escolha da tecnologia de metadados fica a seu critério;
5. A escolha da linguagem de consulta (SQL, PySpark e etc) para os
usuários finais fica a seu critério;
2. **Código SQL ou PySpark estruturado da forma que preferir com
as respostas para as seguintes perguntas:**
2. Qual a média de valor total (total\_amount) recebido em um mês
considerando todos os yellow táxis da frota?
3. Qual a média de passageiros (passenger\_count) por cada hora do dia
que pegaram táxi no mês de maio considerando todos os táxis da
frota?