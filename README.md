# Previsão de Risco de Incêndios no Brasil

## Visão geral

Este projeto desenvolve uma pipeline de Machine Learning para classificação do risco de incêndio no Brasil, utilizando dados de queimadas, condições meteorológicas, localização geográfica e cobertura e uso da terra.

O objetivo é classificar o risco em três categorias:

- **Baixo**
- **Médio**
- **Alto**

O projeto foi desenvolvido como parte do projeto final do programa de Data Science da TripleTen Brasil.


## Objetivo de negócio e KPIs

### Objetivo de negócio

O projeto busca apoiar a identificação e priorização de situações de maior risco de incêndio no Brasil por meio de Machine Learning. A proposta é combinar informações meteorológicas, geográficas, temporais e de cobertura e uso da terra para classificar o nível de risco em **Baixo, Médio ou Alto**.

Em um cenário operacional, essa classificação pode servir como ferramenta de apoio para equipes de monitoramento e gestão de risco, ajudando na priorização de municípios e períodos que merecem maior atenção.

O modelo não prevê diretamente a ocorrência de um incêndio. Ele estima a categoria do indicador de risco de fogo dentro do universo de município-dias associados aos registros analisados.

### Ganho prático e aplicação do modelo

O principal ganho prático do modelo é transformar diferentes fontes de informação — meteorologia, localização, sazonalidade e cobertura e uso da terra — em uma classificação única de risco que pode auxiliar na priorização do monitoramento.

Em uma aplicação operacional, o modelo poderia ser utilizado como uma ferramenta de apoio à decisão. Em vez de analisar manualmente todas as combinações de município e dia com a mesma prioridade, equipes responsáveis pelo monitoramento poderiam utilizar a classificação produzida pelo modelo para organizar uma fila de atenção, dando prioridade inicial às observações classificadas como **Alto risco**.

Essa priorização pode contribuir para:

- direcionar a atenção das equipes para situações de maior risco;
- apoiar a definição de prioridades para inspeção e monitoramento;
- organizar recursos operacionais limitados de forma mais eficiente;
- identificar municípios e períodos que merecem análise adicional;
- complementar, e não substituir, a avaliação realizada por especialistas.

O ganho esperado, portanto, não deve ser interpretado como a prevenção automática de incêndios. O modelo funciona como um mecanismo de **triagem e priorização**, reduzindo o universo de situações que precisam receber a mesma atenção inicial.

Além disso, uma simulação de impacto financeiro foi incluída no notebook para demonstrar como o valor operacional do modelo poderia ser estimado a partir de custos de ações preventivas e de falhas na identificação de situações de alto risco. Como o projeto não possui dados reais de custos operacionais ou prejuízos evitados, os valores utilizados nessa simulação são apenas parâmetros hipotéticos e não representam economia financeira comprovada.

Antes de uma utilização em produção, o modelo deveria ser validado em um ambiente operacional real, com acompanhamento contínuo do desempenho global e por região, especialmente porque a análise por UF e bioma mostrou diferenças relevantes na capacidade de identificação das classes.

Como parte da avaliação de possíveis diferenças de desempenho entre grupos, também foram calculadas **Precision e Recall por bioma e por UF** utilizando as previsões do Random Forest otimizado com Optuna. Os resultados mostraram que o desempenho não é homogêneo entre as regiões e que a classe Médio permanece como a principal dificuldade do modelo. Também foram observadas variações no recall da classe Alto entre alguns estados e biomas. Essas diferenças não demonstram, por si só, a existência de viés causal, mas indicam a necessidade de monitorar o desempenho regional antes de uma aplicação operacional.

Também foi proposto no notebook um **plano de teste A/B para validação operacional** antes de uma eventual adoção do modelo. Para reduzir o risco de contaminação entre os grupos, a unidade experimental proposta é o **município**, mantendo todas as observações de um mesmo município no mesmo grupo durante o experimento. O Grupo Controle utilizaria o procedimento operacional atual, enquanto o Grupo Tratamento utilizaria as classificações do modelo como apoio à priorização. O plano inclui hipóteses H0/H1, métricas operacionais, randomização dos municípios e comparação estatística entre os grupos. O teste não foi executado neste projeto por não haver acesso a uma operação real na qual intervenções controladas possam ser aplicadas.

### KPIs técnicos

Devido ao desbalanceamento das classes, a avaliação do modelo não depende apenas da acurácia. As principais métricas utilizadas são:

- **F1-score Macro:** avalia o desempenho considerando igualmente as três classes.
- **Balanced Accuracy:** avalia o desempenho de forma balanceada entre as classes.
- **Recall por classe:** permite analisar a capacidade do modelo de identificar cada nível de risco.

O **Random Forest otimizado com Optuna** foi selecionado como principal modelo de referência. A avaliação principal foi realizada com divisão de treino e teste por município, evitando que observações do mesmo município aparecessem simultaneamente nos dois conjuntos.

O modelo apresentou:

- **Accuracy:** 0,8329
- **Balanced Accuracy:** 0,7171
- **F1-score Macro:** 0,6908

A escolha do modelo de referência priorizou Balanced Accuracy e F1 Macro, pois essas métricas representam melhor o desempenho entre as três classes em um problema com distribuição desbalanceada.

### KPI operacional

Como indicador operacional, considera-se especialmente o **Recall da classe Alto**, que representa a proporção dos casos realmente classificados como Alto que são corretamente identificados pelo modelo.

Como indicador operacional, considera-se especialmente o **Recall da classe Alto**, que representa a proporção dos casos realmente pertencentes à classe Alto que são corretamente identificados pelo modelo.

Na avaliação principal, realizada com separação de treino e teste **por município**, o desempenho da classe Alto é utilizado como um dos indicadores operacionais relevantes, pois permite avaliar a capacidade do modelo de identificar situações classificadas no nível mais elevado de risco.

Esse indicador é relevante para um cenário de apoio ao monitoramento, pois permite avaliar a capacidade do modelo de sinalizar situações que apresentam o nível mais elevado de risco dentro das categorias definidas.


---

## Fontes de dados

O projeto integra dados provenientes de diferentes fontes públicas.

### Queimadas

Registros de focos de queimadas utilizados como base principal da análise, incluindo informações como:

- município;
- data e hora;
- latitude e longitude;
- dias sem chuva;
- precipitação;
- risco de fogo;
- potência radiativa do fogo.

Período utilizado: **2024 a fevereiro de 2025**.

### INMET

Dados meteorológicos provenientes de estações automáticas do Instituto Nacional de Meteorologia.

Entre as variáveis utilizadas estão:

- precipitação;
- temperatura;
- umidade relativa;
- pressão atmosférica;
- radiação global;
- velocidade do vento;
- rajadas de vento.

Foram utilizados dados de **2024 e 2025**.

#### Download dos dados históricos do INMET

Os dados meteorológicos de 2024 utilizados neste projeto foram obtidos no portal oficial do Instituto Nacional de Meteorologia (INMET), na seção **Dados Históricos Anuais**:

https://portal.inmet.gov.br/dadoshistoricos/

Para reproduzir o processamento dos dados de 2024:

1. Acesse a página de Dados Históricos Anuais do INMET.
2. Selecione **ANO 2024 (AUTOMÁTICA)**.
3. O portal realizará o download de um arquivo ZIP contendo os arquivos das estações meteorológicas automáticas de 2024.
4. Extraia o conteúdo do arquivo ZIP.
5. Coloque os arquivos CSV extraídos no diretório de dados brutos utilizado pelo projeto antes de executar o notebook de pré-processamento.

Os arquivos brutos do INMET 2024 não são armazenados diretamente neste repositório devido ao volume de dados. No processamento realizado para este projeto, o conjunto baixado continha **565 arquivos de estações meteorológicas**.

O notebook `notebooks/02_data_preprocessing.ipynb` realiza a leitura e o tratamento desses arquivos, incluindo a padronização das colunas, tratamento de problemas de leitura identificados em algumas estações e agregação dos registros horários para o nível diário.

### MapBiomas

Dados de cobertura e uso da terra por município.

Como os dados disponíveis utilizados neste projeto vão até **2021**, as proporções das classes de cobertura do solo de 2021 foram utilizadas como características ambientais estáticas para o período analisado.

---

## Preparação dos dados

As diferentes bases apresentavam granularidades e coberturas temporais distintas.

Os registros de queimadas foram agregados por:

**município + dia**

Para cada município e dia foram calculadas diferentes estatísticas, incluindo o maior valor diário de risco de fogo.

Os dados meteorológicos horários do INMET foram transformados em informações diárias.

Como nem todos os municípios possuem uma estação meteorológica, cada município presente na base de queimadas foi associado à **estação meteorológica disponível mais próxima**, utilizando distância geográfica calculada com Haversine.

Os valores meteorológicos ausentes são tratados dentro da pipeline de Machine Learning utilizando imputação pela mediana calculada exclusivamente sobre os dados de treinamento. Além disso, são adicionados indicadores de ausência (`add_indicator=True`), permitindo que o modelo diferencie valores originalmente observados daqueles que precisaram ser imputados.

---

## Variável alvo

A variável alvo foi construída a partir do maior valor de risco de fogo registrado para cada combinação município-dia.

As categorias utilizadas foram:

| Categoria | Intervalo |
|---|---|
| Baixo | 0,00 – 0,40 |
| Médio | > 0,40 – 0,70 |
| Alto | > 0,70 – 1,00 |

Após a remoção das observações sem variável alvo válida, a base destinada à modelagem possui **308.991 observações**.

Distribuição das classes:

| Classe | Observações | Percentual |
|---|---:|---:|
| Baixo | 80.447 | 26,04% |
| Médio | 22.918 | 7,42% |
| Alto | 205.626 | 66,55% |

A distribuição mostra um problema de classificação desbalanceado, principalmente devido à menor representação da classe Médio.

---
### Análise exploratória e teste de hipótese

A análise exploratória foi ampliada para incluir distribuições gráficas das principais variáveis, investigação de outliers e análise da matriz de correlação entre variáveis numéricas.

Também foi realizado um **teste formal de hipótese** para investigar a relação entre precipitação e o indicador de risco de fogo. Foram definidas explicitamente as hipóteses **H0 e H1** e aplicado o teste não paramétrico de **Mann–Whitney U**, comparando observações sem precipitação com observações que apresentaram chuva.

Os resultados completos, incluindo gráficos, estatísticas descritivas, análise de outliers, matriz de correlação, formulação das hipóteses e interpretação do teste estatístico, estão apresentados no notebook `01_data_understanding.ipynb`.

## Modelos avaliados

Foram avaliados os seguintes modelos:

- Dummy Classifier, utilizado como baseline;
- Regressão Logística;
- Random Forest;
- Random Forest Ajustado, com hiperparâmetros definidos manualmente;
- Random Forest + Optuna, com busca sistemática de hiperparâmetros utilizando 20 trials;
- XGBoost, incluído como algoritmo de boosting para ampliar a comparação entre modelos.

A avaliação principal foi realizada com divisão de treino e teste por município, evitando que observações do mesmo município aparecessem simultaneamente nos dois conjuntos.

Devido ao desbalanceamento das classes, a avaliação considera não apenas Accuracy, mas principalmente:

- **Balanced Accuracy**
- **F1 Macro**
- métricas individuais de Precision, Recall e F1-score por classe.

A otimização com Optuna utilizou **F1 Macro como métrica objetivo** e uma validação interna também separada por município, mantendo o conjunto de teste principal fora do processo de seleção dos hiperparâmetros.

## Resultados

A avaliação principal utiliza divisão de treino e teste por município, garantindo que um mesmo município não esteja presente simultaneamente nos dois conjuntos.

| Modelo | Accuracy | Balanced Accuracy | F1 Macro |
|---|---:|---:|---:|
| Dummy Classifier | 0,6536 | 0,3333 | 0,2635 |
| Regressão Logística | 0,6717 | 0,5917 | 0,5530 |
| Random Forest | 0,8229 | 0,7013 | 0,6767 |
| Random Forest Ajustado | 0,8317 | 0,6967 | 0,6784 |
| **Random Forest + Optuna** | **0,8329** | **0,7171** | **0,6908** |
| XGBoost | 0,8694 | 0,6324 | 0,6233 |
| Random Forest — Validação Temporal | 0,6989 | 0,5541 | 0,5515 |

O XGBoost apresentou a maior Accuracy global, com 0,8694. Entretanto, seu desempenho foi menos equilibrado entre as classes, especialmente para a classe Médio.

O **Random Forest + Optuna** apresentou a maior Balanced Accuracy e o maior F1 Macro entre os modelos avaliados na divisão principal por município. Por esse motivo, foi selecionado como modelo de referência do projeto.

A comparação reforça que Accuracy isoladamente não é suficiente para selecionar o melhor modelo neste problema, devido ao desbalanceamento existente entre as classes Baixo, Médio e Alto.

---
### Interpretação do modelo com SHAP

Para interpretar o comportamento do modelo de referência, foi aplicada uma análise SHAP ao **Random Forest otimizado com Optuna**.

Entre as variáveis com maior contribuição para as previsões destacaram-se `dia_do_ano`, latitude, longitude, umidade relativa, temperatura e diferentes classes de cobertura e uso da terra do MapBiomas.

Os resultados indicam que o modelo utiliza uma combinação de **sazonalidade, localização geográfica, condições meteorológicas e características ambientais** para realizar suas classificações.

A análise SHAP complementa a importância tradicional das variáveis do Random Forest ao permitir avaliar a contribuição das características para as previsões do modelo. Essas contribuições devem ser interpretadas como explicações do comportamento do modelo e **não como evidência de relações causais**.

## Validação temporal

Além da avaliação principal com separação dos dados de treino e teste por município, foi realizada uma validação temporal para avaliar a capacidade de generalização do modelo para períodos futuros.

O modelo foi treinado com dados anteriores a dezembro de 2024 e testado em observações entre dezembro de 2024 e fevereiro de 2025.

Resultados:

| Métrica | Resultado |
|---|---:|
| Accuracy | 0,6989 |
| Balanced Accuracy | 0,5541 |
| F1 Macro | 0,5515 |

O desempenho inferior ao observado na avaliação principal por município mostra que a generalização para períodos futuros é mais difícil.

Na validação temporal, as classes Alto e Baixo mantiveram desempenho relativamente melhor, com F1-score de 0,7635 e 0,7488, respectivamente, enquanto a classe Médio apresentou maior dificuldade de classificação, com F1-score de 0,1422.

Esse resultado demonstra a importância de utilizar validações temporais em problemas ambientais sujeitos a mudanças sazonais e temporais e reforça a necessidade de monitorar o desempenho do modelo ao longo do tempo.

---

## Importância das variáveis

A análise de importância do Random Forest mostrou forte participação de variáveis relacionadas à sazonalidade, localização e condições meteorológicas.

Entre as principais variáveis estão:

- dia do ano;
- mês;
- latitude;
- longitude;
- umidade relativa;
- temperatura;
- pressão atmosférica;
- velocidade do vento;
- radiação solar;
- classes de cobertura do solo do MapBiomas.

![Importância das variáveis](figures/feature_importance_random_forest.png)

As importâncias representam a contribuição das variáveis para as decisões do modelo e não devem ser interpretadas como relações causais.

---

## Limitações

Uma limitação importante deste projeto é que a base de queimadas contém registros de eventos detectados.

Consequentemente, o modelo desenvolvido **não prevê diretamente se um novo incêndio ocorrerá ou não em qualquer município brasileiro**.

A tarefa consiste em prever a **categoria de risco de fogo derivada do indicador `risco_fogo` para observações município-dia presentes na base de queimadas**.

Para desenvolver um sistema de previsão direta da ocorrência de incêndios, seria necessário construir uma base contendo também municípios e dias sem registros de focos.

Outras limitações incluem:

- utilização do MapBiomas 2021 como referência ambiental para 2024–2025;
- utilização da estação meteorológica disponível mais próxima;
- distâncias elevadas até estações meteorológicas em algumas regiões;
- presença de valores meteorológicos ausentes;
- forte desbalanceamento da variável alvo;
- redução de desempenho observada na validação temporal.

---

## Próximos passos

Possíveis evoluções do projeto incluem:

- construção de uma base completa município-dia incluindo dias sem incêndios;
- previsão direta da ocorrência de novos focos;
- inclusão de dados mais recentes de cobertura do solo;
- melhoria da identificação da classe Médio;
- incorporação de novos períodos meteorológicos;
- validação temporal contínua;
- monitoramento de data drift e desempenho;
- desenvolvimento de uma API ou aplicação para disponibilização das previsões.

---

## Estrutura do projeto

```text
ProjetoFinalTripleTenBrazil/
│
├── data/
│   ├── raw/
│   └── processed/
│
├── figures/
│   └── feature_importance_random_forest.png
│
├── models/
│   └── random_forest_risco_incendio.joblib
│
├── notebooks/
│   ├── 01_data_understanding.ipynb
│   └── 02_data_preprocessing.ipynb
│
├── reports/
├── .gitignore
├── README.md
└── requirements.txt