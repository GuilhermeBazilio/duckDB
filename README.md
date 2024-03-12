# Repositório de Estudos: Análise de Dados com duckDB

Este repositório é dedicado aos estudos e práticas relacionados ao duckDB, uma ferramenta poderosa para análise de dados. O duckDB se destaca como um banco de dados local altamente eficiente, caracterizado por sua performance excepcional, ausência de persistência de dados e dispensa de complexas instalações e configurações de usuários e schemas.

## Principais Características do duckDB

- **Eficiência Notável:** O duckDB oferece uma experiência de banco de dados local extremamente eficiente.
- **Colunar:** Armazena dados de forma colunar, otimizando consultas analíticas.
- **Sem Persistência:** Não realiza persistência de dados, proporcionando uma abordagem ágil e temporária para análises.
- **Instalação Simplificada:** Dispensa configurações complexas, tornando-se uma escolha prática para diversas aplicações.
- **Suporte Multilinguagem:** Utilizável em Python, R, Java e NodeJs, proporcionando versatilidade na integração com diferentes ambientes de desenvolvimento.

## Projeto e Utilização

O projeto inclui um destaque especial para a integração do duckDB com a linguagem Python, evidenciado no arquivo "Campeonato_brasileiro.ipynb". Para incorporar o duckDB em seu projeto Python, basta realizar a instalação do pacote com o comando:

```bash
pip install duckdb
```
Em seguida, importe o duckDB no código-fonte da sua aplicação. Para obter informações detalhadas sobre o duckDB, consulte a documentação oficial em [https://duckdb.org/docs/](https://duckdb.org/docs/).

## Análise de Dados - Campeonato Brasileiro 2023

Este código Python apresenta uma análise de jogos do campeonato brasileiro, focando nas performances do Grêmio. Os dados são provenientes do dataset "campeonato-brasileiro-full.csv", disponível no Kaggle através do seguinte link: [https://www.kaggle.com/datasets/adaoduque/campeonato-brasileiro-de-futebol/data](https://www.kaggle.com/datasets/adaoduque/campeonato-brasileiro-de-futebol/data).

A análise concentra-se na identificação das formações táticas do time do Grêmio mais bem-sucedidas durante o campeonato brasileiro da edição de 2023. Para esta análise, foi utilizado uma única consulta diretamente do próprio arquivo ".csv". Com poucas linhas de código pode-se efetuar uma análise com boa performance em um conjunto de dados.

Esta análise nos mostra que a formação tática com mais sucesso em casa ou fora de casa é a 4-2-3-1. 
Importante salientar que, em 2023, o time tinha Luis Suárez no ataque. Agora é torcer ainda mais para o meu time obter melhores resultados este ano :)

Código Python:
```python
import duckdb

# Conectando ao DuckDB
conn = duckdb.connect()

# Consulta usando o DuckDB lendo o arquivos do diretório com a extensão CSV
result = conn.query("""
    WITH VITORIAS_MANDANTE AS (
        SELECT
            TRIM(FORMACAO_MANDANTE) AS FORMACAO,
            COUNT(*) AS VITORIAS,
            RANK() OVER (ORDER BY COUNT(*) DESC) AS FORMACAO_RANK,
            'DENTRO DE CASA' AS LOCAL
        FROM
            '*.csv'
        WHERE
            (upper(vencedor) = 'GREMIO' and upper(mandante)='GREMIO') and (data between '2023-01-01' and '2023-12-31')
        GROUP BY
            FORMACAO_MANDANTE
    ),
    
    VITORIAS_VISITANTE AS (
        SELECT
            TRIM(FORMACAO_VISITANTE) AS FORMACAO,
            COUNT(*) AS VITORIAS,
            RANK() OVER (ORDER BY COUNT(*) DESC) AS FORMACAO_RANK,
            'FORA DE CASA' AS LOCAL
        FROM
            '*.csv'
        WHERE
            (upper(vencedor) = 'GREMIO' and upper(visitante)='GREMIO') and (data between '2023-01-01' and '2023-12-31')
        GROUP BY
            FORMACAO_VISITANTE
    )    
    
    SELECT
        FORMACAO,
        VITORIAS,
        LOCAL
    FROM
        VITORIAS_MANDANTE
    WHERE
        FORMACAO_RANK = 1
    UNION ALL
    SELECT
        FORMACAO,
        VITORIAS,
        LOCAL
    FROM
        VITORIAS_VISITANTE
    WHERE
        FORMACAO_RANK = 1    
;
""")

print(result)

# Fecha a conexão
conn.close()
```

Print do código com o resultado:

![image](https://github.com/GuilhermeBazilio/duckDB/assets/69533575/496232ba-c8fc-4a84-9be1-6340ad99f891)



Para contribuições ou sugestões, sinta-se à vontade para abrir uma *issue* ou enviar um *pull request*. Obrigado por explorar este repositório de estudos sobre duckDB!
