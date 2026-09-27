# Temperatura em Salvador: pipeline com Databricks

Projeto de estudo para praticar ingestão de dados de uma API pública, transformações com PySpark e organização em camadas Bronze, Silver e Gold no Databricks.

O notebook consulta a temperatura média diária do ar a 2 metros de altura (`T2M`) para um ponto próximo a Salvador, BA, durante **janeiro de 2025**.

## Fonte dos dados

- **Fonte:** [NASA POWER — API de dados diários](https://power.larc.nasa.gov/docs/services/api/temporal/daily/).
- **Consulta:** [temperatura diária no ponto escolhido](https://power.larc.nasa.gov/api/temporal/daily/point?parameters=T2M&community=RE&longitude=-38.51&latitude=-12.97&start=20250101&end=20250131&format=JSON).
- **Coordenadas utilizadas:** latitude `-12.97`, longitude `-38.51`.
- **Período:** 01/01/2025 a 31/01/2025.
- **Unidade:** graus Celsius (°C).

Os dados representam um ponto da grade meteorológica da NASA POWER. Não são medições de uma estação instalada em Salvador.

## Etapas do projeto

| Camada | O que o notebook faz | Tabela Delta |
| --- | --- | --- |
| Bronze | Extrai os valores diários do JSON da API e mantém a data no formato original `yyyyMMdd`. | `bronze_temperatura_mensal` |
| Silver | Converte `data` para o tipo `date` e mantém `temperatura_c` como número (`double`). | `silver_temperatura_mensal` |
| Gold | Agrupa os dias por ano e mês para calcular indicadores do período. | `gold_temperatura_mensal` |

Nesta versão, cada camada é salva como tabela Delta, e as transformações seguintes usam os DataFrames criados na mesma execução do notebook.

## Resultado

Resultado exibido no notebook para janeiro de 2025:

| Mês | Média das temperaturas médias diárias | Maior média diária | Menor média diária | Dias com dados |
| --- | ---: | ---: | ---: | ---: |
| Janeiro/2025 | 27,72 °C | 29,07 °C | 26,15 °C | 31 |

**Observação:** a maior e a menor média diária não são a temperatura máxima e a temperatura mínima registradas no mês. O parâmetro consultado é `T2M`, temperatura *média* diária.

## Como executar

1. Importe o arquivo [`testando conhecimentos.ipynb`](./testando%20conhecimentos.ipynb) para um workspace Databricks.
2. Conecte o notebook a um ambiente com Spark e Delta Lake e execute as células em ordem.
3. Confira as tabelas `bronze_temperatura_mensal`, `silver_temperatura_mensal` e `gold_temperatura_mensal` no esquema de dados em uso.

O período está definido diretamente na URL. As gravações usam `mode("overwrite")`, então uma nova execução substitui o conteúdo das tabelas.

## Próximos passos

- Fazer a Silver ler a tabela Bronze salva e a Gold ler a tabela Silver salva.
- Tratar falhas da requisição e validar eventuais valores ausentes da API.
- Permitir escolher outro mês sem editar a URL no código.
