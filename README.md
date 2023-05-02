<h1>Analise do Churn</h1>

<h2>Objetivo:</h2 >

<p>
Este projeto possui como objetivo a analise dos dados do arquivo data-test-analytics.csv e a obtencao de insights para as resolucoes dos possiveis problemas que podem ser encontrados durante a analise.
</p>

#

<h2>Tecnologias:</h2>

  <table>
    <tr>
      <td>Python</td>
    </tr>
      <tr>
      <td>3.10</td>
    </tr>
  </table>


#

<h2>Pre-requisitos:</h2>
<p>

- Python
  - E necessario que a versao esteja entre a 3.7 - 3.10, a versao pode ser baixada nesse site : https://www.python.org/downloads/ ou se voce ja tiver o python instalado, you can check the version passing this codebasta checar a versao com o comando:

  ```
  python -V
  ```


</p>



#


<h2>Criando o ambiente do Python:</h2>
<p>Crie o ambiente do projeto usando o codigo abaixo.</h3>

```
python -m venv .venv
```
<h3>2 - Ativando o ambiente do projeto.</h3>

```
.venv/bin/activate
```
<h3>3 - instale os pacotes usando o codigo abaixo ou apenas olhe o arquivo e baixe manualmente.</h3>

```
pip install -r requirements.txt
```
</p>

#

<h2>Rodando o notebook</h2 >

<p>
  <h3>
  Depois de organizar o ambiente, voce pode entrar o no arquivo analise_completa.ipynb e rodar tudo.
  </h3>
</p>


#

<h2>Detalahes do projeto:</h2>
<h3>
O projeto foi divido em 3 etapas:

- Formatacao do Dataset (data-test-analytics.csv)
- Analise Exploratoria
- Proximos Passos
</h3>

#

<p>
<h2>Formatando o Dataset</h2>
Nesta etapa observamos que os dados do tipo data poderiam possuir uma melhor formatacao, e tambem, optei por criar 2 colunas novas, uma de churn (pois e este o objetivo do projeto) e uma de regiao (pois acredito ser sempre uma boa ideia olhar para o macro). Apos isto, resolvi analisar de forma mais ampla o dataset utilizando os seguintes comandos:

```
print('Dados Temporais e Numericos:')
display(df1.describe().round(2))

print('Dados de Objetos e Categoricos:')
display(df1.describe(include=['object','category']))
```
A utilizacao do describe nos permitiu tirar algumas observacoes ja antes mesmo da analise focada no churn:

- Pode-se notar que 505 pessoas cancelaram a sua assinatura, logo, o churn atual e de 505 cliente ou 5.05% dos clientes totais

- A media de recency(Tempo desde a última compra do cliente) pode estar errada por conta de possiveis outliers

- De fato a algumas colunas que nao valem a pena serem analisadas, pois possuem muitos dados distintos

- A maior quantidade de clientes esta ativa

- A maior quantidade de clientes foi adquirida por organic_search

- A maior quantidade de clientes esta no Nordeste

Alem disso, e possivel notar que a coluna status indica a situacao da conta do cliente se esta ativa, cancelada ou pausada. E tambem que ao olharmos para a coluna all_orders, ha clientes que nunca compraram.

Por fim, acabei identificando algo bem curioso e decidi checar com este comando

```
query = '''
    SELECT
        status,
        COUNT(status) as qtd_clientes
    FROM df1
    WHERE all_orders == 0 OR status == 'paused' OR status == 'canceled'
    GROUP BY 1
'''

ps.sqldf(query)
```
Este comando me permitiu ver que nesses dados podemos ter 2 analises distintas, uma para o churn real, que seriam os clientes que ja cancelaram, e outra para o churn estimado, que seriam os clientes cancelados + clientes pausados + clientes ativos sem ordens, pois estes estao a 1 passo de cancelarem.
</p>

#

<h2>Analise Exploratoria.</h2>
<h3>
  <p>Durante a analise obtive diversos insights que irei apresentar a seguir

  imagem

  A partir deste grafico baseado na matriz de correlacao, podemos entender que as variaveis recency, status e deleted_at possuem um alto grau de correlacao com o churn. Sendo assim, iremos analisar essas 3 variaveis prioritariamente.

  imagem

  Churn real que sera melhor analisado a seguir

  imagem
  imagem

  Com base nos graficos, notas-se que a maior quantidade de churn provem dos clientes mais recentes em comparacao com todo o historico do churn

  imagem

  Analisando o grafico acima, podemos entender entao que os servicos ativos sao renovados no periodo de 27 a 42 dias.

  imagem

  Podemos notar que mesmo que o canal none possua uma porcentagem de churn maior do que os outros, o canal telegram_whatsapp que de fato recebe algum investimento, possui o churn mais problematico.

  imagem

  Podemos entender em qual regiao podemos dar um foco maior para a resolucao do problema

  imagem

  Percebesse entao que o churn e diretamente proporcional a quantidade de clientes em uma regiao , quanto mais clientes em uma regiao maior sera o churn nela.

  imagem

  A partir deste simples grafico podemos considerar que o churn possui um grande potencial de mais que quadruplicar (um aumento de 443.76%) em relacao ao atual, indo de 5.05% para 22.41%.
  </p>
<h3>
<h2>Proximos Passos.</h2>

<h3> Sugestão 1: Adotar o NPS </h3>
<p>
Uma possível sugestão para minimizar o churn seria adotar o Net Promoter Score (NPS). Essa metodologia é utilizada para avaliar o grau de satisfação e fidelidade dos clientes em relação a determinada empresa ou produto. Por meio do NPS, é possível coletar feedbacks dos clientes, identificar deficiências no produto ou serviço e até mesmo encontrar as razões para a insatisfação dos clientes.

No caso da Petlove, o NPS pode ser uma forma de entender melhor as necessidades e desejos dos clientes, o que pode ajudar a desenvolver estratégias mais eficazes para manter a fidelidade dos mesmos, evitando o churn dos clientes de longa data. Além disso, é possível utilizar o NPS para converter clientes que cancelaram seus serviços, incentivando-os a retornar e tornar-se novamente assinantes.
</p>
<h3>Sugestão 2: Oferecer incentivos nos primeiros meses</h3>
<p>
Outra sugestão para minimizar o churn seria oferecer incentivos nos primeiros meses de assinatura, a fim de aumentar a fidelização dos clientes tendo em vista que grande parte do churn provem de clientes que cancelaram suas assinaturas em menos de 1 ano.

Uma estratégia interessante seria oferecer um cupom de desconto para a primeira compra, juntamente com uma oferta do serviço de assinatura, destacando as vantagens de se tornar um assinante. Além disso, a empresa pode desenvolver um programa de recompensas para os clientes mais fiéis, que realizam compras frequentemente e mantém-se ativos por um período prolongado. Isso pode incentivar os clientes a permanecerem na plataforma por mais tempo.
</p>
<h3> Sugestão 3: Reativar clientes ativos sem compras</h3>
<p>
Ha clientes ativos que não realizam compras na Petlove desde 2017 e que so aumenta ao longo dos anos. Para minimizar o possivel churn, a empresa pode adotar estratégias para reativar esses clientes e incentivá-los a realizar novas compras.

Uma possível solução seria entrar em contato com esses clientes por e-mail ou por mensagens via whatsapp e telegram, oferecendo cupons de desconto com prazo de validade limitado (com cerca de 1 mes de validade), para incentivar a realização de novas compras. Além disso, é importante que na mensagem esteja destacado as vantagens de se tornar um assinante.

Esta solucao nao so aumentaria os clientes adquiridos pelo canal de marketing whatsapp_telegram, como tambem teria grandes chances de diminuir o churn dessa plataforma, alem de valorizar o investimento nesse canal de marketing.
</p>



