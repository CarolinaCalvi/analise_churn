<h1>Análise do Churn</h1>


<h2>Objetivo:</h2 >


<p>
Este projeto possui como objetivo a análise dos dados do arquivo data-test-analytics.csv e a obtenção de insights para as resoluções dos possíveis problemas que podem ser encontrados durante a análise.
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


<h2>Pré-requisitos:</h2>
<p>


- Python
 - É necessário que a versão esteja entre a 3.7 - 3.10, a versão pode ser baixada nesse site : https://www.python.org/downloads/ ou se você já tiver o python instalado, you can check the version passing this code basta checar a versão com o comando:


 ```
 python -V
 ```




</p>






#




<h2>Criando o ambiente do Python:</h2>
<p>Crie o ambiente do projeto usando o código abaixo.</h3>


```
python -m venc .venc
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
 Depois de organizar o ambiente, você pode entrar na pasta notebooks e rodar o arquivo análise completa.ipynb.
 </h3>
</p>


#
<h2>Arquivos:</h2>


- **dados/data-test-analytics.csv:** Dados utilizado para a análise
- **notebooks/análise completa.ipynb:** Jupyter Notenook que se encontra toda a análise referente aos dados
- **gráficos:** Pasta que contém as imagens geradas pelos códigos no notebook analise completa.ipynb




#


<h2>Detalhes do projeto:</h2>
<h3>
O projeto foi divido em 3 etapas:


- Formatação do Dataset (data-test-analytics.csv)
- Análise Exploratória
- Próximos Passos
</h3>


#


<p>
<h2>Formatando o Dataset</h2>
Nesta etapa observamos que os dados do tipo data poderiam possuir uma melhor formatação, e também, optei por criar 2 colunas novas, uma de churn (pois é este o objetivo do projeto) e uma de região (pois acredito ser sempre uma boa ideia olhar para o macro). Após isto, resolvi analisar de forma mais ampla o dataset utilizando os seguintes comandos:


```
print('Dados Temporais e Numéricos:')
display(df1.describe().round(2))


print('Dados de Objetos e Categóricos:')
display(df1.describe(include=['object','category']))
```
A utilização do describe nos permitiu tirar algumas observações já antes mesmo da análise focada no churn:


- Pode-se notar que 505 pessoas cancelaram a sua assinatura, logo, o churn atual é de 505 cliente ou 5.05% dos clientes totais


- A média de recency(Tempo desde a última compra do cliente) pode estar errada por conta de possíveis outliers


- De fato a algumas colunas que não valem a pena serem analisadas, pois possuem muitos dados distintos


- A maior quantidade de clientes está ativa


- A maior quantidade de clientes foi adquirida por organic search


- A maior quantidade de clientes está no Nordeste


Além disso, é possível notar que a coluna status indica a situação da conta do cliente se está ativa, cancelada ou pausada. E também que ao olharmos para a coluna all orders, há clientes que nunca compraram.


Por fim, acabei supondo algo e decidi checar com este comando


```
query = '''
   SELECT
       status,
       COUNT(status) as qtd clientes
   FROM df1
   WHERE all_orders == 0 OR status == 'paused' OR status == 'canceled'
   GROUP BY 1
'''


ps.sqldf(query)
```
Este comando me permitiu ver que nesses dados podemos ter 2 análises distintas, uma para o churn atual, que seriam os clientes que já cancelaram o serviço, e outra para o churn estimado, que seriam os serviços cancelados + serviços pausados + serviços ativos sem ordens, pois os pausados e os sem ordens estão a 1 passo de cancelarem.
</p>


#


<h2>Análise Exploratória.</h2>
<h3>
<p>Durante a análise obtive diversos insights que irei apresentar a seguir




![correlacao_churn](https://user-images.githubusercontent.com/128322539/235804901-4074529f-444d-4105-b8ee-ec991c06a732.png)




A partir deste gráfico baseado na matriz de correlação, podemos entender que as variáveis recency, status e deletead possuem um alto grau de correlação com o churn.
<p></p>


- Churn Atual:


 ![churn_atual](https://user-images.githubusercontent.com/128322539/235805496-25d65733-ff09-427a-b53f-c5d984899c6c.png)






<p>
 Com base no gráfico acima, foi necessária uma análise baseada nos dias para entender em que situação ocorria o churn
</p>


![churn_trimestral](https://user-images.githubusercontent.com/128322539/235805872-82c09a1d-5384-456e-93e8-d4cce2c6f98f.png)




Percebe-se que os clientes mais recentes são os que mais se tornam churn.


Por esse motivo, decidi comparar com os assinantes ativos para identificar o perfil dos assinantes em relação a compras
![frequencia_compras](https://user-images.githubusercontent.com/128322539/235805879-0dfab553-38f6-45ce-b171-9d02e0a75ebd.png)



Analisando o gráfico acima, podemos entender então que o perfil do assinante ativo, seriam aqueles que fazem compras entre os períodos de 18 e 51 dias, logo, o perfil dos assinantes com potência de se tornarem churn, seriam aquele que compram em períodos após 60 dias


Após isto optei por entender onde o churn estava mais presente nas regiões e nos canais de marketing(marketing_source)


- Canais de Marketing
![churn_porcentagem_canais_marketing](https://user-images.githubusercontent.com/128322539/235805885-24b8f617-c010-4dc9-8ab9-dd98222c57a7.png)
Podemos notar que embora o canal none possua uma porcentagem de churn maior do que os outros, o canal telegram whatsapp que de fato recebe algum investimento, possui o churn mais problemático.


- Regiões
![churn_porcentagem_regiao](https://user-images.githubusercontent.com/128322539/235805883-8237f380-1bab-4e18-8fb6-888f2df5f10a.png)


Nota-se que o churn não varia muito entre as regiões


- Churn estimado
![churn_estimado_divido](https://user-images.githubusercontent.com/128322539/235805875-f151efdf-7ca5-4f29-beb6-3a4a027a0857.png)
o Gráfico acima mostra o possível churn
![churn_estimado](https://user-images.githubusercontent.com/128322539/235805876-aec28080-c327-46ff-8559-fffd36b826a0.png)


A partir deste gráfico podemos considerar que o churn possui um grande potencial de aumentar caso nada seja feito
</p>
<h3>
<h2>Conclusão:</h2>


Observou-se que os clientes que mais se tornam churn são clientes que possuem uma taxa de compra inferior a 18 dias e superior a 60 dias e clientes que são atraídos pelo canal de marketing telegram e whatsapp.











