# Documentacao do projeto SEMMA

## 1. Objetivo da atividade

O projeto adapta o notebook `semma_aluno.ipynb` para executar no Google Colab com dados armazenados no Google Drive. A etapa implementada e a etapa **S (Sample)** do SEMMA: carregar uma base publica, estruturar uma base analitica e separar uma amostra historica de uma base OOT (Out of Time).

A base escolhida e o **Brazilian E-Commerce Public Dataset by Olist**, disponibilizado no Kaggle. Ela possui datas de compra e de entrega dos pedidos, permitindo criar uma referencia temporal e avaliar o corte OOT.

## 2. Arquivos

- `semma_aluno.ipynb`: notebook executavel com a implementacao.
- `olist_orders_dataset.csv`: base original de pedidos da Olist.
- `olist_orders_sample_train.csv`: base historica criada pela etapa Sample.
- `olist_orders_sample_oot.csv`: base OOT criada com o ultimo mes observado.

Os dois arquivos de amostra sao gerados pelo notebook na mesma pasta do arquivo de entrada.

## 3. Como executar

### Google Colab e Google Drive

1. Envie a pasta do projeto para `Meu Drive/fatec-ml-semma/`.
2. Garanta que o arquivo esteja em `Meu Drive/fatec-ml-semma/results/olist_orders_dataset.csv`.
3. Abra `semma_aluno.ipynb` no Google Colab.
4. Execute as celulas em ordem.

A primeira celula instala `pandas` e `scikit-learn` e tenta montar o Google Drive. A montagem e opcional: se o notebook estiver sendo executado localmente, a ausencia do pacote `google.colab` e ignorada.

### Execucao local

Ao executar o notebook a partir da pasta `results`, o caminho local usado e:

```text
./olist_orders_dataset.csv
```

As bibliotecas necessarias sao `pandas` e `scikit-learn`.

## 4. Leitura e preparacao dos dados

O notebook carrega o CSV com `pandas.read_csv` e converte as seguintes colunas para data e hora:

- `order_purchase_timestamp`: momento da compra e data de observacao.
- `order_approved_at`: momento da aprovacao.
- `order_delivered_carrier_date`: envio para a transportadora.
- `order_delivered_customer_date`: entrega ao cliente.
- `order_estimated_delivery_date`: prazo estimado de entrega.

A base original possui 99.441 pedidos e 8 colunas.

## 5. Base analitica da etapa Sample

A base `abt` e criada em nivel de pedido. As variaveis construidas sao:

- `dtRef`: mes da compra, no formato `AAAA-MM`.
- `purchase_year`: ano da compra.
- `purchase_month`: mes da compra.
- `purchase_dayofweek`: dia da semana da compra, de 0 a 6.
- `purchase_hour`: hora da compra.
- `estimated_delivery_days`: dias entre a compra e a data estimada de entrega.
- `approval_delay_hours`: horas entre a compra e a aprovacao.
- `has_carrier_date`: indicador de existencia da data de envio para a transportadora.
- `has_customer_delivery_date`: indicador de existencia da data de entrega ao cliente.
- `flagCanceled`: target binaria, igual a 1 quando `order_status` e `canceled`.

Os identificadores (`order_id` e `customer_id`) e o status textual original nao entram nas variaveis explicativas.

## 6. Separacao temporal OOT

O notebook define:

```python
reference_month = abt['dtRef'].max()
oot = abt[abt['dtRef'] == reference_month].copy()
df_train = abt[abt['dtRef'] < reference_month].copy()
```

Essa regra reserva o ultimo mes observado para OOT e usa todos os meses anteriores como historico. Com a versao atual da base, o resultado e:

- Periodo historico: `2016-09` a `2018-09`.
- Mes OOT: `2018-10`.
- Base historica: 99.437 pedidos.
- Base OOT: 4 pedidos.

As assercoes do notebook verificam que as duas bases existem e que o mes OOT e posterior ao ultimo mes historico.

## 7. Amostra de desenvolvimento

A base historica e dividida em treino e teste usando `train_test_split`:

- `test_size=0.2`: 80% para treino e 20% para teste.
- `random_state=42`: resultado reproduzivel.
- `stratify=y`: preserva a proporcao de cancelamentos entre treino e teste.

As variaveis numericas usadas nessa divisao recebem a mediana historica quando possuem valores ausentes. A OOT nao participa dessa divisao e permanece reservada para uma avaliacao temporal posterior.

Resultado observado:

- Treino: 79.549 pedidos.
- Teste: 19.888 pedidos.
- Taxa de cancelamento historica: aproximadamente 0,625%.
- Taxa de cancelamento OOT: 100% nos 4 pedidos do ultimo mes.

## 8. Arquivos gerados

Ao final da celula Sample, o notebook executa:

```python
df_train.to_csv(train_path, index=False)
oot.to_csv(oot_path, index=False)
```

Os arquivos gerados sao:

- `olist_orders_sample_train.csv`: base historica completa, antes da divisao interna treino/teste.
- `olist_orders_sample_oot.csv`: base do ultimo mes observado, mantida fora do desenvolvimento.

## 9. Cuidados e limitacoes

1. A OOT possui apenas 4 pedidos porque o ultimo mes da base original tem poucos registros. Essa amostra e adequada para demonstrar o procedimento, mas e pequena demais para uma avaliacao estatistica confiavel.
2. `flagCanceled` e derivada de `order_status`, portanto representa cancelamento do pedido, nao churn de cliente.
3. `has_carrier_date` e `has_customer_delivery_date` dependem de eventos posteriores a compra. Elas sao uteis para demonstrar a estrutura da etapa Sample, mas devem ser removidas ou redefinidas caso o objetivo seja prever cancelamento no momento da compra.
4. O notebook implementa somente a etapa S do SEMMA. Nao ha treinamento, avaliacao ou selecao de modelo nesta entrega.
5. A base OOT deve permanecer separada ate a etapa de avaliacao final para evitar vazamento temporal.

## 10. Conclusao

A entrega atende a etapa Sample solicitada: utiliza uma base publica do Kaggle com data de observacao, cria uma referencia mensal, separa o ultimo periodo como OOT, produz uma amostra historica para desenvolvimento e exporta as duas bases para as etapas seguintes do projeto SEMMA.
