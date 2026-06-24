# Processamento e Modelagem Preditiva de Gaps de Carteira com Apache Spark

Este repositório apresenta um pipeline completo de engenharia de dados e machine learning desenvolvido em PySpark para analisar, qualificar e recomendar campanhas de investimentos para clientes de uma assessoria financeira com base em seus gaps de alocação de ativos.

O projeto utiliza uma base de dados estruturada hospedada no Supabase (PostgreSQL) e realiza o processamento localmente com Apache Spark, abordando tanto a classificação preditiva (machine learning supervisionado) quanto a recuperação de informações textuais relevantes.

---

## Estrutura do Projeto

O repositório está organizado de forma a separar as responsabilidades de análise, modelagem e busca:

```text
ml-mercado-financeiro/
├── .env.example            # Modelo de variaveis de ambiente de conexao
├── .gitignore              # Regras de exclusao de arquivos locais no Git
├── requirements.txt        # Dependencias e bibliotecas necessarias
├── artifacts/
│   └── profiling_summary.json  # Relatorio estatistico gerado no Notebook 1
└── notebook/
    ├── 01_ingestion_profiling.ipynb   # Ingestao e analise de cobertura de dados
    ├── 02_ml_contas_gaps.ipynb        # Pipeline de classificacao (ML)
    └── 03_retrieval_embeddings.ipynb  # Motor de busca textual por TF-IDF
```

Descrição das Etapas
1. Ingestão e Diagnóstico de Cobertura (Profiling)

O primeiro notebook realiza a conexao segura via JDBC com o Supabase, extraindo as tabelas de gaps de contas e campanhas de marketing. O Spark avalia a integridade estrutural das colunas, calculando o percentual de valores nulos (NULL%) para justificar tecnicamente a selecao de features nas proximas etapas, exportando um relatorio consolidado em formato JSON.
2. Classificação de Tendência de Alocação (ML Supervisionado)

O segundo notebook constroi um pipeline de classificacao para prever a coluna tipo_gap (OVER/UNDER) com base nas variáveis estruturais classe_ativo, custodia_faixa e aderencia_percentual.

- Algoritmo: Regressao Logistica (LogisticRegression) no Spark MLlib.
- Pipeline: Aplicacao de StringIndexer, OneHotEncoder para variaveis categoricas e VectorAssembler para montagem do vetor de entrada.
- Avaliacao: O modelo e avaliado na base de teste (20% dos dados) com base em Acuracia, F1-Score e matriz de confusao.

3. Mecanismo de Busca por Relevância Textual (TF-IDF Retrieval)

O terceiro notebook implementa um sistema de recomendacao de campanhas. Ele une os campos de texto das campanhas em um unico documento e o vetoriza utilizando o algoritmo TF-IDF. Dada uma frase de busca do usuario, o sistema calcula a Similaridade de Cosseno entre a query e o corpus de campanhas, retornando as sugestoes mais relevantes (Top-K) com o respectivo score de proximidade semantica.
Requisitos de Infraestrutura Local

O ambiente de execucao requer a instalacao das seguintes dependencias:

- Python 3.11 ou 3.12 (venv ativa)
- Java JDK 17 (Eclipse Temurin) para o funcionamento do compilador JVM do Spark
- Utilitario winutils.exe e hadoop.dll configurados localmente em C:/hadoop/bin para manipulacao do sistema de arquivos no Windows
- Driver JDBC do PostgreSQL registrado na sessao Spark

## Como Executar o Projeto

Clone o repositorio:
```
 git clone <[URL_DO_SEU_REPOSITORIO](https://github.com/mneves0988/ml-mercado-financeiro)>
 cd ml-mercado-financeiro
```
Crie e ative a venv:
```
    py -3.12 -m venv venv
    .\venv\Scripts\activate
```

Instale os requisitos:
```
    pip install -r requirements.txt
```

Configure as credenciais no arquivo .env a partir do modelo .env.example.

Execute o Jupyter Lab para rodar os notebooks em sequencia (01, 02 e 03):
```
    jupyter lab
