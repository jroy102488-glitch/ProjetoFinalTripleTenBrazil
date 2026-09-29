# Dicionário de Dados

## Projeto: Previsão de Risco de Incêndios no Brasil

Este documento descreve as principais variáveis utilizadas no projeto, incluindo dados de queimadas, informações meteorológicas do INMET, dados das estações meteorológicas, cobertura e uso da terra do MapBiomas e variáveis criadas durante o processamento.

---

## 1. Dados de Queimadas

| Variável | Descrição |
|---|---|
| `ano` | Ano do registro do foco de queimadas. |
| `mes` | Mês do registro. |
| `data_hora` | Data e hora da detecção do foco. |
| `bioma` | Bioma associado à localização do foco. |
| `sigla_uf` | Sigla da Unidade Federativa. |
| `sigla_uf_nome` | Nome da Unidade Federativa. |
| `id_municipio` | Código identificador do município. |
| `id_municipio_nome` | Nome do município. |
| `latitude` | Latitude geográfica do foco. |
| `longitude` | Longitude geográfica do foco. |
| `satelite` | Satélite responsável pela detecção do foco. |
| `dias_sem_chuva` | Número informado de dias sem chuva. |
| `precipitacao` | Precipitação associada ao registro de queimadas. |
| `risco_fogo` | Indicador numérico de risco de fogo, variando de 0 a 1 após o tratamento dos valores inválidos. |
| `potencia_radiativa_fogo` | Potência radiativa associada ao foco de fogo. |

---

## 2. Dados Meteorológicos — INMET

| Variável | Descrição |
|---|---|
| `ano` | Ano da observação meteorológica. |
| `data` | Data da observação meteorológica. |
| `hora` | Hora da observação. |
| `id_estacao` | Código identificador da estação meteorológica. |
| `precipitacao_total` | Precipitação total registrada. |
| `pressao_atm_hora` | Pressão atmosférica observada no horário. |
| `pressao_atm_max` | Pressão atmosférica máxima registrada. |
| `pressao_atm_min` | Pressão atmosférica mínima registrada. |
| `radiacao_global` | Radiação global registrada. |
| `temperatura_bulbo_hora` | Temperatura do ar observada no horário. |
| `temperatura_orvalho_hora` | Temperatura do ponto de orvalho observada no horário. |
| `temperatura_max` | Temperatura máxima registrada. |
| `temperatura_min` | Temperatura mínima registrada. |
| `temperatura_orvalho_max` | Temperatura máxima do ponto de orvalho. |
| `temperatura_orvalho_min` | Temperatura mínima do ponto de orvalho. |
| `umidade_rel_max` | Umidade relativa máxima. |
| `umidade_rel_min` | Umidade relativa mínima. |
| `umidade_rel_hora` | Umidade relativa observada no horário. |
| `vento_direcao` | Direção do vento. |
| `vento_rajada_max` | Velocidade máxima da rajada de vento. |
| `vento_velocidade` | Velocidade do vento. |

Os registros horários do INMET foram agregados por estação e dia antes da integração com a base principal.

---

## 3. Cadastro das Estações Meteorológicas

| Variável | Descrição |
|---|---|
| `id_estacao` | Código identificador da estação meteorológica. |
| `estacao` | Nome da estação meteorológica. |
| `id_municipio` | Código do município onde a estação está localizada. |
| `id_municipio_nome` | Nome do município da estação. |
| `data_fundacao` | Data de fundação ou início de operação registrada para a estação. |
| `latitude` | Latitude da estação. |
| `longitude` | Longitude da estação. |
| `altitude` | Altitude da estação. |

Para ampliar a cobertura meteorológica, cada município presente na base de queimadas foi associado à estação meteorológica ativa mais próxima por distância geográfica.

---

## 4. Dados de Cobertura e Uso da Terra — MapBiomas

| Variável | Descrição |
|---|---|
| `ano` | Ano de referência do dado de cobertura e uso da terra. |
| `sigla_uf` | Sigla da Unidade Federativa. |
| `sigla_uf_nome` | Nome da Unidade Federativa. |
| `id_municipio` | Código identificador do município. |
| `id_municipio_nome` | Nome do município. |
| `id_classe` | Código da classe de cobertura ou uso da terra. |
| `area` | Área correspondente à classe de cobertura ou uso da terra. |

Para a modelagem, foi utilizado o ano de 2021, o mais recente disponível na base utilizada. As áreas de cada classe foram transformadas em proporções da área total registrada para cada município.

---

## 5. Variáveis Criadas Durante o Processamento

| Variável | Descrição |
|---|---|
| `numero_focos` | Quantidade de focos registrados em determinado município e dia. |
| `risco_fogo_medio` | Média diária do indicador de risco de fogo por município. |
| `risco_fogo_max` | Maior valor diário do indicador de risco de fogo por município. |
| `potencia_radiativa_media` | Média diária da potência radiativa dos focos. |
| `potencia_radiativa_max` | Maior potência radiativa registrada no município-dia. |
| `pressao_atm_media` | Média diária da pressão atmosférica da estação meteorológica associada. |
| `radiacao_global_media` | Média diária da radiação global. |
| `temperatura_media` | Temperatura média diária. |
| `umidade_rel_media` | Umidade relativa média diária. |
| `vento_velocidade_media` | Velocidade média diária do vento. |
| `distancia_estacao_km` | Distância aproximada, em quilômetros, entre a localização representativa do município na base e a estação meteorológica ativa mais próxima. |
| `dia_do_ano` | Número do dia dentro do ano, utilizado para representar a sazonalidade. |
| `mapbiomas_classe_*` | Proporção da área municipal correspondente a cada classe de cobertura ou uso da terra do MapBiomas. |
| `classe_risco` | Variável alvo final de classificação: Baixo, Médio ou Alto. |

---

## 6. Variável Alvo

A variável alvo do modelo é `classe_risco`.

Ela foi construída a partir de `risco_fogo_max`, que representa o maior valor válido do indicador de risco de fogo observado para cada combinação de município e dia.

| Classe | Intervalo de `risco_fogo_max` |
|---|---|
| `Baixo` | 0,00 a 0,40 |
| `Médio` | > 0,40 a 0,70 |
| `Alto` | > 0,70 a 1,00 |

Após a preparação dos dados, 308.991 observações apresentaram uma classe de risco válida para a modelagem.

Distribuição final da variável alvo:

| Classe | Observações | Proporção |
|---|---:|---:|
| Baixo | 80.447 | 26,04% |
| Médio | 22.918 | 7,42% |
| Alto | 205.626 | 66,55% |

A distribuição mostra um desbalanceamento importante entre as classes. Por esse motivo, métricas como F1-score macro e balanced accuracy foram consideradas juntamente com a acurácia.

---

## 7. Variáveis Excluídas da Modelagem

Algumas variáveis foram mantidas durante a análise, mas deliberadamente excluídas das features utilizadas pelos modelos.

### Variáveis diretamente relacionadas ao target

- `risco_fogo_medio`
- `risco_fogo_max`

Essas variáveis foram excluídas porque estão diretamente relacionadas à construção da variável alvo e sua utilização causaria vazamento de informação.

### Informações observadas após ou durante o evento de fogo

- `numero_focos`
- `potencia_radiativa_media`
- `potencia_radiativa_max`

Essas informações descrevem focos de fogo já observados e, portanto, não são adequadas como preditores para uma estimativa de risco anterior ao evento.

### Variáveis meteorológicas da própria base de queimadas

- `dias_sem_chuva`
- `precipitacao_queimadas`

Essas variáveis também foram excluídas das features do modelo. A modelagem utilizou dados meteorológicos independentes provenientes do INMET.

### Identificadores

Identificadores e nomes de municípios e estações não foram utilizados diretamente como variáveis preditoras.

---

## 8. Observações e Limitações dos Dados

- A unidade de análise final é a combinação município-dia.
- A base analisada contém dias e municípios associados a registros de focos de fogo; portanto, o modelo não deve ser interpretado como um modelo de previsão da ocorrência ou não ocorrência de incêndios.
- O objetivo da classificação é estimar a categoria do indicador de risco de fogo dentro desse universo de observações.
- Os dados meteorológicos foram associados utilizando a estação ativa mais próxima disponível.
- Em regiões com baixa densidade de estações, a distância entre o município e a estação pode ser elevada.
- Valores meteorológicos ausentes utilizados na modelagem foram tratados dentro do pipeline, com parâmetros aprendidos somente a partir dos dados de treinamento.
- Os dados do MapBiomas utilizados como referência são de 2021 e foram tratados como características territoriais estáticas para o período analisado.
- A importância das variáveis produzida pelo Random Forest deve ser interpretada como importância para o modelo, e não como evidência de relação causal.