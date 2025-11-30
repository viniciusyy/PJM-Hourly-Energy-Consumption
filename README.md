# 📈 Demanda de Energia – PJM Hourly Energy Consumption

Este repositório contém a análise de uma série temporal de demanda horária de energia da **PJM Interconnection**, combinando:

- Análise exploratória da série temporal (EDA),
- Modelagem preditiva com **ARIMA**,
- Uso de **Redis** com estruturas de dados probabilísticas
  (Bloom Filter e Count-Min Sketch) para análise em fluxo.

Trabalho desenvolvido como parte da disciplina de Ciência de Dados / Séries Temporais.

---

## 🗂 Estrutura do Repositório

```text
.
├── data/
│   └── PJM_Load_hourly.csv      # dataset utilizado (uma das regiões)
├── notebooks/
│   ├── 01_eda_pjm.ipynb         # EDA + médias móveis + estacionaridade
│   ├── 02_arima_pjm.ipynb       # modelagem ARIMA + previsão + RMSE
│   └── 03_redis_pjm.ipynb       # Redis + Bloom Filter + Count-Min Sketch
└── README.md
```

## 📊 Descrição do Dataset
- Origem: Kaggle – Hourly Energy Consumption
   - Página oficial:
     - https://www.kaggle.com/datasets/robikscube/hourly-energy-consumption
- Organização responsável:
  - PJM Interconnection, que coordena o sistema de transmissão e o mercado de energia em várias regiões dos EUA.
- Forma de coleta:
  - Medidas horárias de demanda de energia (em MW), obtidas por sistemas de monitoramento da PJM e disponibilizadas como séries históricas.
- Tipo de dado coletado:
   - Timestamp (data/hora)
   - Demanda de energia elétrica em MW para uma região específica

 Neste projeto foi utilizada apenas uma das séries disponíveis (PJM_Load_hourly.csv), representando a carga total do sistema PJM.


## 📊 Análise do Dataset

### 1. Descrição completa do dataset

- **Origem da base de dados**  
  O conjunto de dados utilizado é o **“Hourly Energy Consumption”** disponível no Kaggle, originalmente publicado por Rob Mulla a partir de dados históricos da **PJM Interconnection**. A PJM é um *Regional Transmission Organization (RTO)* que coordena o sistema de transmissão de energia elétrica e o mercado atacadista de energia em diversas regiões dos Estados Unidos.  
  Trata-se, portanto, de uma fonte **oficial e confiável**, amplamente utilizada em estudos acadêmicos e exemplos de séries temporais.

- **Como o dataset foi coletado**  
  Os valores de demanda são medidos por sistemas operacionais da PJM (por exemplo, sistemas SCADA e medidores instalados em subestações e pontos de medição do sistema elétrico). Esses dados são agregados e disponibilizados como séries históricas de **demanda horária**, sendo posteriormente organizados e publicados em formato tabular (CSV) no Kaggle.

- **Tipo de dado coletado**  
  O dataset contém, em essência:
  - Um carimbo de **data e hora** (`timestamp`), com granularidade de **1 hora**;
  - A **demanda de energia elétrica** em **MW (megawatts)** para uma determinada região ou para o sistema como um todo.  

  No repositório original existem vários arquivos, cada um representando uma área/região (AEP, COMED, DAYTON, PJM total etc.).  
  Neste projeto foi utilizado especificamente o arquivo:

  - `PJM_Load_hourly.csv` – demanda horária total do sistema PJM.

---

### 2. Redução / Recorte do dataset

O conjunto original contém séries extensas, com vários anos de dados e múltiplas regiões.  
Para tornar a análise mais manejável e focada, foram feitas **duas reduções principais**:

1. **Seleção de apenas uma série/região**  
   Entre todos os arquivos disponibilizados, foi escolhido apenas:
   - `PJM_Load_hourly.csv`, que representa a **carga total do sistema PJM**.  

   Isso simplifica a análise (sem múltiplas regiões) e mantém um sinal forte e representativo do comportamento global da demanda.

2. **Recorte temporal da série**  
   Em vez de utilizar todo o histórico disponível, o projeto trabalhou com a janela:

   > **de 1998 até 2002**

   Esse recorte foi adotado pelos seguintes motivos:

   - Garante a presença de **vários ciclos sazonais completos** (aproximadamente 4 anos de dados);
   - Mantém um **volume de dados suficiente** para análise de tendência, sazonalidade e ajuste de modelos (ARIMA, médias móveis, etc.), sem tornar o tempo de processamento excessivo;
   - Facilita o uso em ambientes interativos (como Google Colab), onde memória e tempo de execução são limitados.

Em resumo:

> “Escolhemos a janela de **1998–2002** pois apresenta padrões sazonais completos, comportamento representativo da demanda e um volume de dados adequado para experimentação, modelagem e integração com Redis, mantendo o projeto leve e reprodutível.”


## 🧪 Notebooks e Análises

  01_eda_pjm.ipynb – Análise Exploratória (EDA)

  - Leitura do dataset e ajuste do índice temporal.
  - Verificação de datas mínimas / máximas e número de observações.
  - Visualização da série horária completa.
  - Cálculo de médias móveis:
    - Janela de 24h (1 dia),
    - Janela de 7 dias.
  - Discussão de:
    - tendência de longo prazo,
    - sazonalidade anual e semanal,
    - comportamento geral da série.

  Também são feitas análises básicas de estacionaridade (como preparação para o ARIMA).

  02_arima_pjm.ipynb – Modelagem Preditiva com ARIMA

  - Agregação da série horária em série diária (média por dia).
  - Divisão em treino e teste (por exemplo, últimos 365 dias para teste).
  - Ajuste de vários modelos ARIMA(p, 1, q):
    - Ex.: ARIMA(1,1,0), ARIMA(1,1,1), ARIMA(2,1,1), ARIMA(2,1,2), etc.
  - Seleção do melhor modelo com base em:
    - RMSE (erro quadrático médio da previsão no conjunto de teste),
    - AIC (quando necessário).
   
Também há comparação com um modelo ingênuo (Naive),
que prevê que “amanhã = hoje”.
Isso mostra que modelos mais complexos nem sempre superam baselines simples e reforça a importância de sempre comparar com referenciais.

03_redis_pjm.ipynb – Análise com Redis e Estruturas Probabilísticas

Este notebook demonstra como usar Redis como backend para estruturas probabilísticas aplicadas à série temporal:

Bloom Filter (dias críticos)
- Reamostragem diária para obter o pico diário (max).
- Definição de um limiar de dia crítico (ex.: max > 35000 MW).
- Para cada dia crítico, é gerado um padrão textual com:
  - estação do ano (season),
  - flag de fim de semana (weekend=True/False),
  - indicação de pico acima do limiar.
- Esses padrões são inseridos em um Bloom Filter implementado manualmente com bitmaps (SETBIT / GETBIT).

Isso permite responder rapidamente perguntas como:

“Já vimos um fim de semana de verão com pico acima de 35.000 MW?”

Aceitamos falsos positivos, mas nunca falsos negativos, com uso de memória fixo.

Count-Min Sketch (faixas de consumo)
- Discretização do consumo horário em faixas de 500 MW (ex.: 20000–20499, 30000–30499).
- Implementação de um Count-Min Sketch manual usando hashes e HINCRBY em Redis.
- Alimentação do CMS com todas as observações horárias.
- Consulta da frequência aproximada de faixas específicas, com comparação com as contagens exatas obtidas em pandas.

O CMS fornece contagens aproximadas com:
- baixo uso de memória,
- atualizações muito rápidas,
- e sempre uma leve superestimação (nunca subestima).

## ⚙️ Como Executar os Notebooks

1. Clonar o repositório
  ```text
  git clone https://github.com/viniciusyy/PJM-Hourly-Energy-Consumption.git
  cd PJM-Hourly-Energy-Consumption
  ```
2. Garantir a pasta data/ com o dataset
     Coloque o arquivo PJM_Load_hourly.csv dentro da pasta data/.
     
   No GitHub ele já está versionado, mas se rodar localmente, só confira o caminho.

3. Dependências de Python
  As análises utilizam principalmente:
   - pandas
   - numpy
   - matplotlib
   - statsmodels
   - scikit-learn
   - redis

Instalação (exemplo):
  ```text
  pip install pandas numpy matplotlib statsmodels scikit-learn redis
 ```

## 🔴 Redis no Notebook 03

Para executar o 03_redis_pjm.ipynb:
- É necessário ter um servidor Redis disponível.
- No Google Colab, o próprio notebook mostra como:
  - instalar o Redis via apt-get,
  - iniciar o servidor,
  - conectar usando o cliente redis.

Obs.: o Redis usado é a versão “pura”, sem RedisBloom.

As estruturas Bloom Filter e Count-Min Sketch são implementadas manualmente sobre:
- bits (SETBIT / GETBIT),
- hashes (HINCRBY).



