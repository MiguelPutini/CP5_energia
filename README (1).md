| Nome | RM |
|---|---|
| Miguel Putini | 571624 |
| Júlia Konishi | 569506 |
| Alexandre Rizzi | 569621 |
| João Vitor Giadans | 571608 |
| João Victor Scheren | 568883 |

# Checkpoint 02 — APIs, Energias Renováveis e Machine Learning

## Objetivo

Este projeto utiliza dados de duas APIs públicas para desenvolver duas tarefas independentes de aprendizado de máquina: classificação da fonte de empreendimentos de geração de energia e regressão da radiação solar.

Foram comparados três algoritmos em cada tarefa, sempre utilizando a mesma divisão de treino e teste dentro de cada problema.

## Tarefa 1 — Classificação da fonte renovável

**Fonte dos dados:** SIGA — ANEEL (Agência Nacional de Energia Elétrica).

O arquivo `aneel_classificacao_orange.csv` contém 3.876 empreendimentos e quatro colunas. As entradas utilizadas pelo modelo são `potencia_kw`, `latitude` e `longitude`, enquanto `fonte` é a variável alvo, com as classes Solar, Eólica e Hidráulica.

Nos dados utilizados não foram encontrados valores ausentes. A distribuição é de 1.476 registros de Hidráulica, 1.200 de Solar e 1.200 de Eólica.

A divisão foi feita em 80% para treino e 20% para teste, usando `random_state=42` e `stratify=y` para manter a proporção das classes.

### Algoritmos comparados

- K-Nearest Neighbors (KNN)
- Regressão Logística
- Random Forest

KNN e Regressão Logística utilizam `StandardScaler` dentro de um `Pipeline`, evitando ajustar a padronização com os dados de teste. Para Precision, Recall e F1 foi utilizada a média **weighted**, pois o problema possui três classes e uma delas possui quantidade maior de registros.

### Resultados da classificação

| Algoritmo | Accuracy | Precision weighted | Recall weighted | F1 weighted |
|---|---:|---:|---:|---:|
| Random Forest | 0,9755 | 0,9759 | 0,9755 | 0,9755 |
| KNN | 0,9652 | 0,9655 | 0,9652 | 0,9651 |
| Regressão Logística | 0,8247 | 0,8313 | 0,8247 | 0,8233 |

O Random Forest apresentou o melhor resultado nesta configuração, com apenas 19 erros em 776 empreendimentos de teste. A classe com mais erros foi Solar (12 de 240), distribuídos entre Hidráulica (7) e Eólica (5); a Hidráulica foi a mais fácil (294 de 296 corretos). Na Regressão Logística o erro dominante foi Solar previsto como Eólica (51 de 240). As matrizes de confusão completas dos três modelos estão no notebook. Os erros restantes mostram que potência e localização ajudam a distinguir as fontes, mas não são suficientes para identificar perfeitamente a tecnologia de geração. Empreendimentos de fontes diferentes podem possuir potências parecidas ou estar em regiões próximas.

**Limitação dos dados:** 47 linhas têm latitude e longitude iguais a 0,0 (coordenada ausente preenchida com zero), todas de Eólica ou Hidráulica, o que pode dar uma pista artificial ao modelo. O CSV foi mantido como gerado pela API para preservar a reprodutibilidade.

## Tarefa 2 — Regressão da radiação solar

**Fonte dos dados:** API histórica Open-Meteo.

Foram utilizados dados horários estimados para Petrolina (PE), aproximadamente nas coordenadas -9,39 e -40,50, entre **01/04/2025 e 30/06/2025**, no fuso `America/Recife`. Foram mantidos registros das 7h às 17h.

O arquivo `meteo_regressao_orange.csv` possui 1.001 registros. As entradas são `temperatura_c`, `umidade_pct`, `nuvens_pct`, `vento_kmh` e `hora`. A variável alvo é `radiacao_w_m2`. `data_hora` é usada somente para ordenar os registros e não entra no modelo. Não foram encontrados valores ausentes no CSV utilizado.

Como são dados temporais, não foi feito embaralhamento. As primeiras 80% das horas foram usadas para treino e as últimas 20% para teste.

### Algoritmos comparados

- Regressão Linear
- Random Forest Regressor
- Árvore de Decisão para regressão

### Resultados da regressão

| Algoritmo | MAE (W/m²) | MSE ((W/m²)²) | R² |
|---|---:|---:|---:|
| Random Forest Regressor | 66,399 | 7210,084 | 0,846 |
| Árvore de Decisão | 88,791 | 15291,159 | 0,674 |
| Regressão Linear | 145,205 | 30034,201 | 0,360 |

O Random Forest Regressor apresentou o menor erro e o maior R² entre os três modelos testados. A hora do dia é uma entrada importante porque a radiação apresenta um ciclo diário relacionado à posição do Sol. As demais variáveis representam condições meteorológicas que também podem alterar a radiação recebida.

Estimar radiação solar não equivale a prever diretamente a geração elétrica de um sistema fotovoltaico. A energia elétrica produzida também depende de fatores como potência instalada, eficiência dos módulos, orientação, inclinação, temperatura dos painéis, sombreamento, perdas e disponibilidade dos equipamentos.

## Como executar

1. Baixe ou clone este repositório.
2. Abra `Checkpoint02_APIs_Energia_ML_COMPLETO.ipynb` no Google Colab ou Jupyter Notebook.
3. Execute as células na ordem, do início ao fim.
4. O notebook consulta as APIs públicas e gera os CSVs. Os dois CSVs também estão incluídos no repositório para permitir a reprodução da análise com os mesmos dados usados nos resultados acima.
5. Caso necessário, instale as bibliotecas com `pip install pandas numpy matplotlib scikit-learn`.

## Arquivos da entrega

- `Checkpoint02_APIs_Energia_ML_COMPLETO.ipynb` — notebook completo com consulta às APIs, exploração, treinamento, métricas, gráficos e interpretações.
- `aneel_classificacao_orange.csv` — dados preparados para classificação.
- `meteo_regressao_orange.csv` — dados preparados para regressão.
- `README.md` — documentação do projeto.

## Observações

As APIs utilizadas são públicas e não exigem token nas consultas propostas. Nenhuma senha, chave ou credencial deve ser adicionada ao repositório.
