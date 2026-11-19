# Plano de implementação do projeto SEMMA

## 1. Definir o problema e os dados

- Usar como referência o notebook inicial em
  [`task/semma_aluno.ipynb`](../../task/semma_aluno.ipynb).
- Utilizar a base [`task/abt_churn.csv`](../../task/abt_churn.csv).
- Confirmar:
  - a variável-alvo `flagChurn`;
  - a variável temporal `dtRef`;
  - os identificadores que não devem entrar no modelo;
  - o período reservado para a base OOT (Out of Time).

## 2. Criar o notebook completo

Criar `results/20261119/semma_aluno.ipynb`, organizado nas cinco fases:

### Sample

- Carregar os dados localmente e no Google Colab/Drive.
- Validar dimensões, tipos e período temporal.
- Separar os períodos históricos da base OOT.

### Explore

- Calcular estatísticas descritivas.
- Analisar a distribuição da variável-alvo.
- Verificar valores ausentes e duplicidades.
- Analisar a evolução temporal do churn.
- Visualizar correlações e as principais variáveis.

### Modify

- Remover identificadores e colunas com risco de vazamento.
- Converter `dtRef` em variáveis temporais.
- Tratar valores ausentes.
- Codificar variáveis categóricas, se existirem.
- Separar atributos e alvo usando um pipeline reproduzível.

### Model

- Dividir os dados históricos em treino e validação, preservando a ordem temporal quando aplicável.
- Treinar pelo menos dois modelos:
  - regressão logística como baseline;
  - árvore de decisão ou Random Forest.
- Usar `random_state` fixo e registrar os parâmetros.

### Assess

- Avaliar accuracy, precision, recall, F1 e ROC-AUC.
- Exibir matriz de confusão e curva ROC.
- Avaliar o modelo final na base OOT.
- Comparar o desempenho histórico com o desempenho OOT.
- Registrar limitações, desbalanceamento e possíveis sinais de overfitting.

## 3. Documentar a entrega

Atualizar o [`README.md`](./README.md) com:

- objetivo do projeto;
- descrição das cinco fases SEMMA;
- origem e estrutura dos dados;
- regra de separação temporal;
- modelos utilizados;
- métricas obtidas;
- limitações;
- instruções para execução no Google Colab e localmente.

## 4. Validar o projeto

- Executar todas as células do notebook em ordem.
- Confirmar que o notebook funciona localmente e no Colab.
- Verificar que a base OOT não é usada no treinamento.
- Confirmar a ausência de vazamento de dados.
- Validar que as métricas e os arquivos gerados são reproduzíveis.
- Conferir o diff final e manter apenas arquivos relacionados ao projeto.
