### 1. Definição arquitetural

Todo o projeto foi desenvolvido inteiramente no DataBricks Comunity Edition. 

Sendo assim, os arquivos de Janeiro/2023 a Maio/2023 das viagens dos Taxis Verdes e Amarelos da Cidade de Nova York foram baixados manualmente e incluídos no Volume new_york_trips 

Dessa maneira, a Landing Zone está na base **01_transient**, volume **new_york_trips** como mostra a imagem abaixo:

![Arquivos de viagens do site de New York](readme_images\01_arquivos_viagens_site_new_york.png).

Além disso, o DataBricks salva efetivamente esses arquivos/tabelas no S3 conforme exemplo abaixo:

![alt text](readme_images\02_arquivos_s3.png)

A arquitetura de dados escolhida então no Lakehouse foi a Medalhão, em que temos as camadas:

1. **01_transient**: camada de dados temporárias para apoio na importação dos dados na camada bronze, como arquivos das fontes originais e tabelas temporárias para posterior importação dos dados na camada bronze. Dessa maneira, cada arquivo criará uma tabela temporária na camada, como mostra a imagem abaixo:

![Tabelas Transients](readme_images\03_tabelas_transient.png)


2. **02_bronze**: camada de dados com tabelas sem tratamento sendo espelhos das fontes originais. Dessa forma, teremos então 02 tabelas, **green_tripdata** e **yellow_tripdata**. Ambas possuirão todas as colunas dos arquivos além da **transient_table_name**(nome do arquivo que gerou a importação dos dados) e **ingestion_datetime** (data e horário em UTC com o momento de criação do registro no LakeHouse). Com essas duas colunas, conseguiremos rastrear o arquivo gerado do registro e também a data de geração do registro na bronze. Abaixo as imagens das tabelas bronze.

Nas tabelas bronze não são importados dados considerados absurdos com base caso tenham alguma condição abaixo contemplada: 

*total_amount < 0
or passenger_count < 0 or trip_distance < 0 or fare_amount < 0 or extra < 0 or mta_tax < 0 or tip_amount < 0 or tolls_amount < 0 or improvement_surcharge < 0*


   
![Tabelas Bronze](readme_images\04_tabelas_bronze.png)

Outras colunas adicionais foram as colunas de partição dos dados por dia na coluna **lpep_pickup_date** da tabela **green_trip_data** e coluna **tpep_pickup_date** na tabela **yellow_tripdata**. Assim a busca dos dados para dias específicos é otimizada com os campos de partição.

1. **03_silver**: camada de dados com a tabela tratada new_york_taxi que unifica os dados das tabelas bronze **green_tripdata** e **yellow_tripdata** com as colunas: *vendor_id, passenger_count,total_amount, pickup_date, pickup_datetime, dropoff_datetime, color e __timestamp*. Além disso, são realizadas tratamentos de dados para a inserção dos registros.

2. **04_gold**: camada com os dados com dados mais agregados possuindo a tabela **new_york_taxi_by_hour**. Nela as informações estão agreagadas por dia, hora e cor do veículo e é utilizada para apresentação das respostas do desafio. As colunas dela são: 
*pickup_date, pickup_hour, color, quantity, total_amount e passenger_count*

### 2. Processamento dos dados

Por conta da definição de dados ser medalhão, o processamento também leva em consideração essas camadas. Assim, foi criado o pipeline "Principal Pipeline", em que na pasta **explorations** foram criados Notebooks para tratamento em que cada arquivo cria/edita as tabelas  na camada dos dados homônima como mostra a imagem abaixo:

![Notebooks de processamento dos dados](readme_images\05_processamento_dados.png)

Por conseguinte, foi criado o Job **New York Taxi Driver Job**, em que cada arquivo do Notebook é uma task como mostra a imagem:

![Job de execução](readme_images\06_job.png)

Com isso, temos simplicidade para desenvolvimento com o código direto no Notebooks facilitando inclusive depuração e a orquestração do processamento sendo realizada via Job. 

Todo o código da pasta **explorations** do pipeline está em src/pipeline.

Para acompanhamento da saúde dos dados, foram criados os alertas *"Green Tripdata with pickup and dropff date problems"* e *"Yellow Tripdata with pickup and dropff date problems"* para checar diariamente nas tabelas bronze respectivas se a data de fim da viagem é menor que a data de início com as consultas *"select count(1) from workspace.`02_bronze`.green_tripdata
where lpep_pickup_datetime > lpep_dropoff_datetime"* e "*select count(1) from workspace.`02_bronze`.yellow_tripdata as t
where tpep_pickup_datetime > tpep_dropoff_datetime*". Abaixo a listagem dos alertas no DataBricks Comunity Edition.

![Alertas para avaliação da qualidade dos dados continuamente](readme_images\10_alertas.png)

### 3. Apresentação dos resultados

Finalmente, para a apresentação dos resultados das perguntas, foram criados os scripts sql *"01_New York Taxi Yellow Cab Average Fare Early 2023.sql"* e *"02_Average Passengers Per Hour in May 2023.sql"* na pasta **answers** mostrado abaixo:

![Scripts SQL das respostas](readme_images\07_script_sql_resposta.png)

Finalmente para a apresentação dos resultados foi criado o Dashboard no Pipeline que mostra os resultados das duas consultas como nas imagens abaixo:

*Qual a média de valor total (total\_amount) recebido em um mês
considerando todos os yellow táxis da frota?* 

![Qual a média de valor total (total\_amount) recebido em um mês considerando todos os yellow táxis da frota?](readme_images\08_sql_result_01.jpg)


*Qual a média de passageiros (passenger\_count) por cada hora do dia
que pegaram táxi no mês de maio considerando todos os táxis da
frota?* 

![Qual a média de passageiros (passenger\_count) por cada hora do dia que pegaram táxi no mês de maio considerando todos os táxis da frota?](readme_images\09_sql_result_02.jpg)