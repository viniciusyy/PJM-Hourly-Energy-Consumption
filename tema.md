# 📊 Demanda de Eletricidade – PJM Hourly Energy Consumption

## 📘 Descrição Detalhada do Dataset
A PJM Interconnection é uma organização que opera mercados de energia em várias regiões dos Estados Unidos. A demanda horária é medida por sistemas de monitoramento em tempo real e consolidada em séries históricas. Um extrato dessa base foi organizado e publicado em formato de dataset no Kaggle.

### 🔍 Quem extraiu os dados e como foram coletados?
A PJM Interconnection é uma organização que opera mercados de energia em várias regiões dos Estados Unidos. A demanda horária é medida por sistemas de monitoramento em tempo real e consolidada em séries históricas. Um extrato dessa base foi organizado e publicado em formato de dataset no Kaggle.

## 📁 Tipo de Dados Coletados
Demanda de energia elétrica (MW) por região e por hora, com marca temporal precisa.

## 🕒 Período da Série Temporal
📆 **Início:** 2002-01-01  
📆 **Fim:** 2018-01-01

## 🔢 Tamanho Aproximado
➡️ **Centenas de milhares de registros horários amostras**

## 🔗 Links Oficiais
- 👉 **Página oficial do dataset:** https://www.kaggle.com/datasets/robikscube/hourly-energy-consumption  
- 📥 **Download direto:** https://www.kaggle.com/datasets/robikscube/hourly-energy-consumption/data

---

## 🧠 Análise de Séries Temporais com Redis

Além das análises clássicas de séries temporais (como visualização, decomposição, médias móveis, análise de autocorrelação e modelagem preditiva com ARIMA/Prophet/LSTM), este trabalho deverá incluir **análises adicionais utilizando estruturas de dados probabilísticas e algoritmos de processamento massivo implementados em Redis**.

O aluno (ou grupo) deve escolher **pelo menos 2 (duas)** das técnicas abaixo para aplicar ao dataset deste tema:

1. **Bloom Filter**  
   - Exemplo de uso: verificar se determinados eventos ou padrões da série já ocorreram em uma janela de tempo (por exemplo, picos acima de um limiar, combinações específicas de variáveis, dias com condições críticas).  
   - Objetivo: permitir consultas do tipo “já vimos algo parecido antes?” de forma rápida e com baixo uso de memória, aceitando pequenos falsos positivos.

2. **HyperLogLog**  
   - Exemplo de uso: estimar o número de valores distintos ou categorias observadas ao longo do tempo (por exemplo, diferentes faixas de consumo, classes de poluição, perfis de uso ao longo dos dias).  
   - Objetivo: obter estimativas de **cardinalidade** (número de elementos distintos) em janelas temporais, utilizando pouquíssima memória e permitindo acompanhar a diversidade de estados da série.

3. **MinHash**  
   - Exemplo de uso: medir a **similaridade** entre janelas de tempo (semanas, meses, anos), tratando cada janela como um conjunto de eventos ou categorias e comparando padrões de comportamento.  
   - Objetivo: identificar períodos com comportamento semelhante, o que ajuda a detectar recorrências, mudanças de regime ou sazonalidades não triviais.

4. **Count-Min Sketch**  
   - Exemplo de uso: estimar com boa aproximação a **frequência** de determinados valores, intervalos ou categorias (por exemplo, quantas vezes a série ficou em uma certa faixa de consumo, ou quantos eventos de alta magnitude ocorreram).  
   - Objetivo: rastrear “os mais frequentes” ao longo do tempo em fluxo de dados, sem precisar armazenar todo o histórico detalhado.

### 🎯 Requisitos específicos desta parte

- Utilizar **Redis** (e, se desejado, módulos como RedisBloom) para implementar as estruturas escolhidas.  
- No notebook, documentar claramente:
  - Como os dados da série temporal foram pré-processados e transformados para serem inseridos no Redis (por exemplo, discretização em faixas, definição de janelas temporais, mapeamento para chaves).  
  - Como cada estrutura de dados foi populada (operações de inserção) e consultada (consultas, estimativas, verificações).  
  - Que tipo de **insight adicional** cada técnica trouxe em relação à análise estatística/tradicional da série temporal.
- Comparar brevemente o **custo/benefício** dessas abordagens probabilísticas em relação a soluções simples (armazenar tudo em tabelas convencionais, fazer contagens exatas em memória, etc.).  
- Apresentar exemplos concretos de consultas e resultados (saídas textuais, tabelas-resumo ou gráficos que ilustrem as estimativas obtidas a partir do Redis).
