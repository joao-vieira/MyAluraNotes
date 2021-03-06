# Elasticsearch: introdução à buscas inteligentes

## Aula 01 - Introdução ao Elasticsearch

### Atividade 01 - Introdução

- Tudo começa pela busca
  - Essa busca, em muitos casos, é fundamental para a empresa (Marketplaces, Netflix ou mesmo o Google)
  - Além disso, ela pode ir de um simples campo de texto até uma interface mais sofisticada como o Google Maps (buscas são realizadas enquanto o usuário *scrolla* a tela procurando o ponto de destino)
- A busca pode resolver os mais diversos fins:
  - Desde encontrar um restaurante até analisar/corrigir um erro dentro de milhares de linhas de *logs* de uma aplicação

> Tudo isso é a parte visível da busca. A partir disso, é possível pensar: o que está por trás da busca?

- Com o passar do tempo, o volume e a complexidade dos dados analisados aumentou tanto que realizar buscas tornou-se uma dor de cabeça para os profissionais de T.I.
  - Elasticsearch veio para resolver isso e nos ajuda diariamente (aplicativos de navegação, recomendação ou mesmo de encontros)
- O Elasticsearch funciona em cima do Java e faz uso de outra aplicação: [Lucene](https://lucene.apache.org/)
  - Um software de busca que contém uma API para a indexação de documentos, também escrito em Java.
  - Porém, não necessariamente precisa se utilizar Java para usar o Elasticsearch. 
    - Enquanto que em um banco de dados relacional, geralmente utiliza-se a linguagem SQL, no Elasticsearch utiliza-se **comandos HTTP REST**.
- Alguns dos conceitos do Elasticsearch: [Índices](https://www.elastic.co/pt/blog/what-is-an-elasticsearch-index), [Tipos](https://logz.io/blog/elasticsearch-mapping/), [Shards](https://forum.casadocodigo.com.br/t/elasticsearch-o-que-sao-shards/173) e [Réplicas](https://codingexplained.com/coding/elasticsearch/understanding-replication-in-elasticsearch).

## Atividade 02 - Preparando o ambiente

- **Ferramentas que serão utilizadas no curso:** [Elasticsearch](https://www.elastic.co/pt/), [Kibana](https://www.elastic.co/pt/kibana) e [cURL](https://curl.haxx.se/).
- Instalações no Ubuntu:
  - **Referências:** [Referência](https://tecadmin.net/setup-elasticsearch-on-ubuntu/) - [Referência 2](https://www.digitalocean.com/community/tutorials/como-instalar-elasticsearch-logstash-e-kibana-elastic-stack-no-ubuntu-18-04-pt) - [Referência 3](https://linuxize.com/post/how-to-install-elasticsearch-on-ubuntu-18-04/)

> Elasticsearch 

- Confirme que os comandos a seguir retornam resultados (ou instale as ferramentas):
  - `java -version`
  - `echo $JAVA_HOME`
- Instalando:
  - `sudo apt-get install apt-transport-https`
  - `wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -`
- Agora configure o repositório apt do seu sistema:
  - `sudo add-apt-repository "deb https://artifacts.elastic.co/packages/7.x/apt stable main"`
- Finalize:
  - `sudo apt-get update`
  - `sudo apt-get install elasticsearch`
- Customize seu Elasticsearch depois de instalado (opcional):
  - `sudo nano /etc/elasticsearch/elasticsearch.yml`
  - Altere estes valores:

```plain
network.host: localhost
cluster.name: myCluster1
node.name: "myNode1"
```

- Para iniciar o Elasticsearch, existem duas opções:
  - Configurá-lo para iniciar assim que o sistema iniciar:
    - `sudo /bin/systemctl enable elasticsearch.service`
  - Ou rodar estes comandos:
    - `sudo systemctl start elasticsearch.service`
    - `sudo systemctl stop elasticsearch.service`
- Teste se tudo ficou ok:
  - `curl -X GET "http://localhost:9200/?pretty"`


> Kibana

- Instale:
  - `sudo apt install kibana`
- Habilite e inicie:
  - `sudo systemctl enable kibana`
  - `sudo systemctl start kibana`

### Atividade 04 - Como Funciona o Elasticsearch

- Após iniciar o serviço, o Elasticsearch usará as seguintes portas:
  - **9200**: fornece o web service REST
  - **9300**: comunicação entre os nós (nodes)
- Acesso ao Elasticsearch (comparado a um banco relacional):
  - *POST* x insert
  - *GET* x select
  - *PUT* x update ou insert (s/ duplicar)
  - *DELETE* x delete

### Atividade 06 -  O Comando HTTP Post

- A partir de agora, já é possível utilizar o console do Kibana para executar comandos no Elasticsearch.
  - Para isso, basta acessar o menu *Dev Tools*.

### Inserindo dados no Elastic

- Obs: no contexto do ElasticSearch, `índice` significa **banco de dados**.
  - E os **"registros"** são chamados de `documentos`.
- A sintaxe dos POST consiste no seguinte:

```json
POST [índice]/_doc
{JSON_COM_DOCUMENTO}

-- Nesse curso:
POST catalogo/_doc
{
  "nome": "João Silva",
  "interesses": ["futebol", "música", "literatura"],
  "cidade": "São Paulo",
  "formação": "Letras",
  "estado": "SP",
  "país": "Brasil",
}
```

### Atividade 07 - Inserindo Documentos com ID Explícito

#### Sintaxe do GET

```json
GET [índice]/_count

-- Nesse curso:
GET catalogo/_count
```

#### Criando um documento com o ID explícito

```json
POST [índice]/_doc/{id}
{JSON_COM_DOCUMENTO}

-- Nesse curso:
POST catalogo/_doc/1
{
    "nome": "João Silva",
    "interesses": ["futebol", "música", "literatura"],
    "cidade": "São Paulo",
    "formação": "Letras",
    "estado": "SP",
    "país": "Brasil"
}
```

### Atividade 08 - Obtendo e Pesquisando Documentos

#### Busca Geral

```json
GET [índice]/_search

-- Nesse curso:
GET catalogo/_count
```

```json
GET [índice]/_search/?q=[termo]

-- Nesse curso:
GET catalogo/_search/?q=futebol
```

### Atividade 12 - Resumo

- Nessa aula, aprendemos:
  - O que é e para que serve o ElasticSearch.
  - Como fazer uma instalação básica do ElasticSeach a ser usada em uma única máquina.
  - Como instalar o Kibana e o cURL.
  - Como utilizar um cliente REST para criar e localizar documentos por identificador, listar todos os documentos ou aplicar um valor simples para consulta.

## Aula 02 - Índices, Tipos, Shards e Réplicas

### Atividade 01 - Os Comandos HTTP HEAD e PUT

| Banco Relacional | Elasticsearch |
|:----------------:|:-------------:|
|     Instância    |     Index     |
|      Tabela      |      Type     |
|      Esquema     |    Mapping    |
|       Tupla      |    Document   |
|      Coluna      |   Attribute   |

- O HTTP `HEAD`
  - Retorna apenas cabeçalhos HTTP
  - Não retorna o documento
  - Serve para saber se existe o documento

#### Descobrindo se um documento existe

- No SQL seria (provavelmente):
  - `select 1 from pessoas where id = N`
- No Elastic é:
  - `HEAD catalogo/_doc/1`

### Atividade 02 - O Comando HTTP POST

- O comando `GET` sempre retorna a última versão do documento.
  - Inclusive, isso significa que o Elastic armazena seus documentos de forma imutável (estamos sempre substituindo o documento atual por um outro documento - versionamento).
- Já o comando `POST`, nesse caso, acaba sendo um pouco diferente que normalmente é utilizado. Nesse caso, ele é útil para **atualizar** os documentos (ou apenas um *attribute* dele).

### Atividade 03 - Shards

> Os "pedaços" do índice.

- **Shards** nada mais são do que as partições de um banco de dados. São pedaços do índice original que, por serem "independentes", permitem que um índice seja dividido em várias máquinas e limitações físicas sejam superadas.
  - Inclusive, esse é um dos pontos (se não o mais) principais do Elastic, tendo em vista que o *Apache Lucene* não suporta esse particionamento.
- Cada *shard* pode ser copiado, replicado ou feito backup. Daí as características do Elastic de ser confiável e escalável.
- Existem dois tipos de *shard*:
  - *Primária*: onde as escritas são realizadas.
  - *Secundárias (ou réplicas*: recebem as atualizações, conforme as primárias vão sendo moficadas.

> No Kibana, abrir o menu **Stack Monitoring**.

- Além do *Overview*, nos menus *Nodes* e *Indices*, é possível visualizar a sua "base de dados".
  - Uma dica é que uma shard **nunca** tenha 50GB ou mais.

### Atividade 07 - Resumo

- Nessa aula, aprendemos:
  - O que é um índice, tipo e documento.
  - Como utilizar a API Rest para operações de criação, atualização, remoção e verificação de existência de documentos no ElasticSearch.
  - A analogia entre ElasticSearch e um banco de dados relacionais.
  - O que são shards, réplicas e qual a sua importância.

## Aula 03 - Iniciando com Buscas

### Atividade 01 - Busca Livre e Busca Específica

> Busca Livre

- O Elastic "cria" um campo `_all`. É por isso que, mesmo sem referenciar nenhum atributo, ele encontra todos os documentos que possuem o valor procurado.
  - Ex.: o documento `{"nome": "João Vitor", "sobrenome": "Veronese Vieira"}` possui o camplo **_all** `"João Vitor Veronese Vieira"`

> Busca Específica

- Para fazer uma busca específica, a estrutura do comando `GET` muda um pouco:
  - `GET <documento>/_search/?q=atributo:valor`
  - Ex: `GET catalogo/_search/?q=estado:SP`

### Atividade 02 - O Cabeçalho da Busca

> Interpretando o cabeçalho de resposta

- `took`: tempo (em *ms*) que a busca demorou pra executar
  - Útil para identificar gargalos ou buscas que estão demorando mais do que o esperado
- `timed_out`: excedeu (ou não) o tempo de resposta
- `shards`: indica quantos fragmentos do Elastic foram afetados pela busca
- `hits`: é o resultado, propriamente dito
  - `total`: indica a quantia de resultados retornados
  - `max_score`: vai de 0 a 1 e indica o grau de relevância do resultado

### Atividade 03 - Paginação

- A paginação é muito útil para evitar qualquer tipo de gargalo ou sobrecarga de dados em qualquer um dos atores envolvidos no processo de busca: rede, Elastic, cliente, entre outros...
- Para fazê-la em uma busca do Elastic, possivelmente uma estrutura semelhante à essa será utilizada:
  - Ex.: `GET catalogo/_search/?q=futebol&size=10&from=0`
  - Onde:
    - **SIZE:** tamanho da "página"
    - **FROM:** deslocamento (indica o intervalo)

### Atividade 06 - Para saber Mais: API Scroll

- Quando utilizamos paginação simples, temos que tomar cuidado para não causarmos instabilidade no cluster. Caso solicitemos 10.000 registros, todas as shards participantes na consulta podem retornar esta quantidade.
  - Por exemplo, caso tenhamos 3 shards com 100.000 documentos em cada e pedimos os primeiros 10.000 resultados, o nó coordenador terá de processar 30.000 documentos, executar a ordenação e pegar os primeiros 10.000.

### Atividade 08 - Resumo

- Nessa aula, aprendemos:
  - Que podemos especificar o campo na busca usando `campo:termo`.
  - ElasticSearch retorna 10 resultados por padrão.
  - A entender as estatísticas que são parte da resposta de requisições de busca, como tempo de resposta e hits.

## Aula 04 - Mapeamento de Tipos

### Atividade 01 - Mapeamento

- O Elasticsearch define **automaticamente** o índice a partir do documento!
- Para obter o *mapeamento* dos atributos e índices do Elasticsearch, basta utilizar a função `_mapping`.
  - Ex.: `GET catalogo/_mapping`
- O Elasticsearch divide os dados em duas categorias:
  - *Os tipos de dados "core":*
    - strings
    - números
    - e datas
  - *Os tipos "complexos":*
    - arrays
    - IPv4
    - IPv6
    - informação geoespacial

### Atividade 06 - Resumo

- Nessa aula, aprendemos:
  - Como verificar os mappings para nossos documentos.
  - Tipos de dados existentes do ElasticSearch.
  - Inferência de tipos pelo ElasticSearch.

## Aula 05 - Quebrando Textos com Analyzers

### Atividade 01 - Índice Invertido e Analisadores

- Basicamente, todo o poder do Elasticsearch e suas funcionalidades são possíveis graças ao armazenamento de **índice invertido**.
  - Essa técnica, basicamente, é semelhante aos glossários de um livro: as palavras (`tokens`) são referenciadas pelas páginas (`documentos`) em que se encontram.
  - Dessa forma, o Elasticsearch consegue encontrar os termos buscados mesmo sem nenhum atributo ser especificado diretamente.
- Para que essa busca ocorra, um *Analyzer* é necessário. Os mais comuns são:
  - *Espaço em branco*
  - *Padrão (standard)*
  - *Simples*
  - *Idioma (Portuguese, English, etc)*

> Para testar o analyzer **espaço em branco**:

```
GET catalogo/_analyze
{
  "analyzer": "whitespace",
  "text": "Eu nasci há 10 mil (sim, isso mesmo, 10 mil) anos atrás"
}
```

> Para testar o analyzer **standard**:

```
GET catalogo/_analyze
{
  "text": "Eu nasci há 10 mil (sim, isso mesmo, 10 mil) anos atrás"
}
```

> Para testar o analyzer **simples**:

```
GET catalogo/_analyze
{
  "analyzer": "simple",
  "text": "Eu nasci há 10 mil (sim, isso mesmo, 10 mil) anos atrás"
}
```

### Atividade 04 - Encontrando quem é nascido no Brasil e gosta de música

- Por padrão, o Elasticsearch é buscar no documento um termo **ou** (*OR*) outro. Para buscar documentos que contenham obrigatoriamente dois termos, é preciso utilizar o **e** (*AND*).
  - Atenção para a caixa alta, se for em caixa baixa, o Elasticsearch interpretará como mais um termo a ser buscado.

### Atividade 08 - Resumo

- Nessa aula, aprendemos:
  - Como a busca exata e a busca de texto cheio são diferentes.
  - O que são e como usar os analyzers para incrementar nossas buscas.
  - A verificar como um analyzer vai indexar um determinado texto.
  - Como alterar o analyzer padrão para português e melhorar nossas buscas.
  