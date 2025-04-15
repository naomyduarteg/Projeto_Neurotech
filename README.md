# Projeto 1: desenvolver um modelo de concessão de crédito


Este projeto utiliza técnicas de Machine Learning para prever o risco de crédito dos clientes e, a partir dos resultados, propor uma nova política de crédito que minimize as perdas financeiras. O objetivo é comparar a política atual, baseada em uma regra simples de idade, com uma nova política fundamentada num modelo de score (LGBM) que avalia a propensão de pagamento (bom pagador).

## Sumário

- [Motivação](#motivação)
- [Visão Geral do Projeto](#visão-geral-do-projeto)
- [Dados](#dados)
- [Pré-processamento](#pré-processamento)
  - [Limpeza e Imputação](#limpeza-e-imputação)
- [Modelagem](#modelagem)
  - [Comparação de Modelos](#comparação-de-modelos)
  - [Modelo Selecionado: LGBM](#modelo-selecionado-lgbm)
- [Análise Financeira](#análise-financeira)
- [Escoragem da base Out-of-time](#escoragem)
- [Como Executar](#como-executar)
- [Licença](#licença)

## Motivação

A instituição financeira possui uma política de crédito “AS-IS” que reprova automaticamente clientes com idade igual ou inferior a 28 anos. O projeto propõe que, utilizando um modelo de Machine Learning que gera um score de probabilidade de pagamento (bom pagador), seja possível definir um novo ponto de corte para negar crédito, mantendo o mesmo percentual de reprovação da política atual mas reduzindo a inadimplência.

## Visão Geral do Projeto

1. **Dados**: A base de dados é composta por informações cadastrais dos solicitantes de crédito (idade, variáveis de risco, scores, etc.), uma coluna de data (`REF_DATE`) e o rótulo (`TARGET`), onde 0 indica bom pagador e 1 inadimplente.  
2. **Pré-processamento**: As técnicas de limpeza incluem remoção de duplicatas, eliminação de colunas com mais de 50% de valores ausentes e tratamento de colunas redundantes. Valores faltantes são imputados com a mediana para variáveis numéricas e a moda para variáveis categóricas.  
3. **Modelagem**: Foram comparados diversos modelos (RandomForest, LogisticRegression, XGBoost, LGBM, CatBoost, KNN) utilizando validação cruzada e métricas como F1-score, G-Mean e AUC, que são métricas mais adequadas para dados desbalanceados. O modelo LGBM se destacou para o foco na classe de bom pagador (classe 0) e teve métricas melhores, no geral.  
4. **Análise Financeira**: Realizada apenas para os registros do mês de agosto de 2017, esta análise compara a política atual (AS-IS) — que aprova somente clientes com idade > 28 anos — com uma nova política (TO-BE) baseada no score gerado pelo modelo. São calculados:
   - Carteira Aprovada (total de crédito concedido, assumindo R\$ 1.000,00 por cliente aprovado);
   - Dívida Total (soma dos empréstimos concedidos aos inadimplentes);
   - Percentual Negado;
   - Economia gerada pela nova política, evidenciando a redução na inadimplência.

## Dados

- **Fonte dos Dados**: O arquivo `dados/test.gz` (formato gzip) contém a base de dados de teste, com colunas como `ID`, `REF_DATE`, `TARGET`, `IDADE`, além de outras variáveis utilizadas na modelagem.
- **Formato**: Os dados possuem a coluna `REF_DATE` em formato datetime (ex.: `2017-06-01 00:00:00+00:00`) e variáveis categóricas e numéricas.

## Pré-processamento

### Limpeza e Imputação

O pipeline de pré-processamento executa as seguintes etapas:

1. **Seleção de Registros**: Filtra os dados para manter apenas os registros de agosto de 2017, usando a coluna `REF_DATE`.
2. **Remoção de Colunas**:  
   - Elimina colunas com mais de 50% de valores ausentes.  
   - Remove colunas redundantes (por exemplo, `VAR70`, `VAR74`, `VAR75`, `VAR86`).
3. **Imputação**:  
   - Variáveis numéricas são preenchidas pela mediana;  
   - Variáveis categóricas são preenchidas pela moda.

Confira a parte do código no arquivo `preprocessamento.py` (ou no notebook) para detalhes de implementação.

## Modelagem

### Comparação de Modelos

Foram avaliados múltiplos modelos utilizando validação cruzada, com métricas como F1-score (foco na classe 0), G-Mean e AUC. A função `comparar_modelos()` gerava uma tabela comparativa dos melhores resultados para cada modelo.

### Modelo Selecionado: LGBM

O modelo LGBM (LGBMClassifier) foi selecionado para aplicação final, considerando que apresentou a melhor capacidade de identificar bons pagadores (classe 0) e, consequentemente, gerar um score de risco confiável.

## Análise Financeira

A análise financeira compara duas políticas:

- **Política AS-IS**: Aprova clientes com `IDADE > 28`.  
  - _Exemplo de Resultados_:  
    - Carteira Aprovada AS-IS: R\$ 5.930.000,00  
    - Dívida Total AS-IS: R\$ 1.021.000,00  
    - Percentual de Reprovação: 18,79%
- **Política TO-BE**: Utiliza o score do modelo. O ponto de corte do score é definido como o quantil que resulta na mesma taxa de reprovação da política AS-IS.  
  - _Exemplo de Resultados_:  
    - Score Cutoff: 0.5195  
    - Carteira Aprovada TO-BE: R\$ 5.930.000,00  
    - Dívida Total TO-BE: R\$ 761.000,00  
    - Economia Estimada: R\$ 260.000,00

O resultados mostram que a nova política mantém o volume de crédito concedido, porém reduz significativamente a inadimplência, gerando uma economia financeira expressiva.

## Escoragem da base Out-of-time

Ao final do projeto, geramos também previsões para uma base de dados cega (out-of-time - oot), com o resultado sendo guardado em um CSV na pasta "dados".

## Como Executar

1. **Clone o Repositório**:
    ```bash
    git clone https://github.com/seu-usuario/projeto-credito.git
    cd projeto-credito
    ```

2. **Instale as Dependências**:
    ```bash
    pip install -r requirements.txt
    ```

3. **Execute os Notebooks ou Scripts**:
    - Para modelagem: execute o notebook `projeto1.ipynb`.

## Licença

Este projeto é licenciado sob a [MIT License](LICENSE).

