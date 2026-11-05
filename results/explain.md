# Explicação do notebook `semma_aluno.ipynb`

O arquivo `semma_aluno.ipynb` é um notebook Jupyter compatível com o Google
Colab. Ele organiza o projeto de análise preditiva seguindo a etapa S
(Sample) do método SEMMA.

## Estrutura do arquivo

Um arquivo `.ipynb` é um documento JSON composto por uma lista de células. O
notebook contém células de código e células Markdown:

1. Configuração do ambiente: monta o Google Drive no caminho
   `/content/drive`.
2. Importações: carrega o `pandas` e os recursos de modelagem do
   `scikit-learn`.
3. Leitura dos dados: localiza e lê o arquivo CSV com
   `pandas.read_csv`.
4. Preparação: converte a data de observação, cria a variável resposta
   binária `entregue` a partir do resultado do pedido e cria atributos
   derivados da data.
5. Amostragem temporal: separa o último mês como base OOT (Out-of-Time) e
   utiliza os meses anteriores para desenvolvimento.
6. Divisão treino/teste: separa os dados de desenvolvimento de forma
   estratificada e reproduzível.
7. Modelagem: treina um Random Forest dentro de um `Pipeline`, com
   imputação da mediana para valores ausentes.
8. Avaliação: calcula ROC-AUC, acurácia, precisão, recall e F1 no teste e
   na base OOT.
9. Conclusão: orienta a comparar o desempenho temporal do modelo.

## Como executar no Google Colab

1. Faça upload de `semma_aluno.ipynb` para o Google Colab.
2. Coloque `olist_orders_dataset.csv` no Google Drive.
3. Execute a célula de montagem do Drive e autorize o acesso.
4. Confirme que o CSV está em uma das pastas configuradas na célula de
   leitura.
5. Execute as células em ordem, de cima para baixo.

## Base de dados e variável resposta

O projeto utiliza o Brazilian E-Commerce Public Dataset by Olist, obtido no
Kaggle. A coluna `order_purchase_timestamp` representa a data de observação.
A coluna `order_status` fornece o resultado observado; o notebook cria
`entregue`, com valor `1` quando o pedido foi entregue e `0` nos demais casos.

As informações de entrega real e o próprio resultado não são usadas como
atributos preditivos. Assim, a avaliação OOT representa uma verificação
temporal do comportamento do modelo em pedidos posteriores ao período de
desenvolvimento.
