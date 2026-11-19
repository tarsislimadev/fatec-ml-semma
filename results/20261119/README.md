# Projeto SEMMA: previsão de churn

Esta entrega implementa as cinco etapas do método SEMMA sobre a base de
clientes do projeto: Sample, Explore, Modify, Model e
Assess.

## Arquivos

- [`semma_aluno.ipynb`](./semma_aluno.ipynb): notebook executável com o
  projeto completo.
- [`churn_sample_train.csv`](./churn_sample_train.csv): amostra histórica
  usada no desenvolvimento.
- [`churn_sample_oot.csv`](./churn_sample_oot.csv): último período reservado
  para avaliação Out of Time.
- [`PLANS.md`](./PLANS.md): plano original de implementação.

## Dados e amostragem

A entrada é [`task/abt_churn.csv`](../../task/abt_churn.csv), com 5.496 linhas,
43 colunas e 14 competências mensais entre março de 2024 e abril de 2025.
`flagChurn` é a variável-alvo e `dtRef` é a referência temporal.

O notebook aplica uma divisão temporal:

- Treino: março de 2024 a fevereiro de 2025, 4.742 linhas;
- Validação: março de 2025, 451 linhas;
- OOT: abril de 2025, 303 linhas.

`idUsuario`, `dtRef` e `flagChurn` não entram como variáveis explicativas.
Os dados numéricos passam por imputação da mediana e padronização dentro de
pipelines do scikit-learn. A base OOT não participa do treinamento.

## Etapas SEMMA

### Sample

Carrega a base, valida as colunas obrigatórias, converte `dtRef`, separa os
períodos temporais e exporta as amostras histórica e OOT.

### Explore

Verifica tipos, valores ausentes, duplicidades, distribuição do churn,
evolução mensal e correlação das variáveis numéricas com o alvo. A base não
possui valores ausentes nem duplicidades completas.

### Modify

Remove identificadores e a referência temporal do conjunto de atributos,
separa o alvo e constrói o pré-processamento reproduzível para os modelos.

### Model

Treina uma regressão logística como baseline e um Random Forest. Ambos usam
`random_state=42` e tratamento de desbalanceamento com `class_weight='balanced'`.

### Assess

As métricas observadas na execução do notebook foram:

| Modelo | Amostra | Accuracy | Precision | Recall | F1 | ROC-AUC |
| --- | --- | ---: | ---: | ---: | ---: | ---: |
| Random Forest | Validação | 0,7761 | 0,7639 | 0,8893 | 0,8219 | 0,8386 |
| Random Forest | OOT | 0,7855 | 0,7619 | 0,8366 | 0,7975 | 0,8410 |
| Regressão logística | Validação | 0,7738 | 0,7667 | 0,8779 | 0,8185 | 0,8346 |
| Regressão logística | OOT | 0,7822 | 0,7636 | 0,8235 | 0,7925 | 0,8389 |

O Random Forest foi selecionado por apresentar o maior ROC-AUC na validação.
Na OOT, sua matriz de confusão foi:

```text
              previsto_0  previsto_1
real_0              110          40
real_1               25         128
```

## Como executar

### Localmente

A partir da raiz do repositório, abra o notebook e execute as células em
ordem. As dependências são `pandas`, `scikit-learn`, `matplotlib`,
`nbformat` e `nbconvert`.

### Google Colab

Envie a pasta do projeto para o Google Drive, abra o notebook no Colab e
execute as células. A primeira célula tenta montar o Drive; a busca de
arquivos aceita tanto `task/abt_churn.csv` quanto o caminho usado no notebook
original.

## Limitações

- A avaliação OOT contém apenas um mês, portanto não mede estabilidade em
  vários períodos futuros.
- As métricas dependem da definição de `flagChurn` e da disponibilidade das
  variáveis no momento real da previsão.
- Correlação não implica causalidade; as variáveis mais correlacionadas devem
  ser interpretadas no contexto do negócio.
