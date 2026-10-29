# Detecção de Fraudes em Cartões de Crédito

## Sobre o Projeto

Este projeto tem como objetivo identificar transações fraudulentas em cartões de crédito utilizando técnicas de Machine Learning. O dataset utilizado contém operações reais realizadas por portadores de cartão, incluindo transações legítimas e fraudulentas.

O principal desafio deste problema é o forte desbalanceamento entre as classes. Aproximadamente 99,8% das transações são legítimas, enquanto apenas cerca de 0,17% representam fraude. Por esse motivo, métricas tradicionais como acurácia não são suficientes para avaliar o desempenho dos modelos.

---

## Objetivos

- Analisar a distribuição das transações fraudulentas.
- Preparar os dados para treinamento.
- Comparar diferentes algoritmos de classificação.
- Ajustar o limiar de decisão para melhorar a detecção de fraudes.
- Explicar as previsões utilizando SHAP.
- Avaliar os modelos com foco em Recall, Precisão e F1-Score.

---

## Dataset

O conjunto de dados contém:

- `Time`: tempo decorrido desde a primeira transação registrada.
- `Amount`: valor da transação.
- `Class`: variável alvo.
  - `0`: transação legítima.
  - `1`: transação fraudulenta.
- `V1` a `V28`: variáveis transformadas por PCA para preservar a privacidade dos clientes.

O dataset não foi incluído neste repositório. Ele deve ser obtido por meio do link disponibilizado no material da aula.

---

## Por Que o Desbalanceamento É Importante?

Em problemas extremamente desbalanceados, um modelo pode atingir mais de 99% de acurácia simplesmente classificando todas as transações como legítimas.

Exemplo:

- Total de transações: 100.000
- Fraudes: 170
- Legítimas: 99.830

Se um modelo responder sempre "não é fraude":

- Acertos: 99.830
- Erros: 170
- Acurácia: 99,83%

Apesar da acurácia elevada, o modelo não identifica nenhuma fraude e é totalmente inútil para o negócio.

Por isso, neste projeto o foco está nas métricas:

- Recall;
- Precisão;
- F1-Score;
- ROC-AUC;
- Curva Precision-Recall.

---

## Preparação dos Dados

As seguintes etapas foram realizadas:

### 1. Análise Exploratória

- Verificação de dimensões da base;
- Distribuição das classes;
- Estatísticas descritivas;
- Avaliação da proporção de fraudes.

### 2. Engenharia de Variáveis

Foi criada uma variável derivada do valor da transação:

```python
LogAmount = log(Amount + 1)
```

A transformação reduz a influência de valores extremos e melhora a estabilidade dos modelos.

### 3. Padronização

As variáveis numéricas foram escaladas com:

```python
StandardScaler
```

A padronização é especialmente importante para modelos lineares, como a Regressão Logística.

### 4. Divisão de Treino e Teste

Os dados foram divididos utilizando:

```python
train_test_split(..., stratify=y)
```

Isso garante que a proporção de fraudes seja mantida tanto no conjunto de treino quanto no conjunto de teste.

---

## Modelos Avaliados

### Regressão Logística

Utilizada como modelo baseline por ser simples, interpretável e eficiente para comparação inicial.

Principais características:

- Treinamento rápido;
- Fácil interpretação;
- Suporte a pesos de classes.

---

### Random Forest

Modelo baseado em múltiplas árvores de decisão.

Vantagens:

- Captura relações não lineares;
- Menor sensibilidade a ruídos;
- Boa capacidade de generalização.

---

### XGBoost

Modelo de Gradient Boosting amplamente utilizado em competições e aplicações reais.

Vantagens:

- Excelente desempenho em problemas tabulares;
- Capacidade de lidar com dados desbalanceados;
- Possibilidade de ajuste fino de hiperparâmetros.

---

## Estratégias para Lidar com o Desbalanceamento

Foram avaliadas diferentes abordagens:

### Class Weight

Atribuição de maior peso à classe fraudulenta.

```python
class_weight="balanced"
```

### Undersampling

Redução da quantidade de transações legítimas para equilibrar as classes.

Vantagem:

- Treinamento mais rápido.

Desvantagem:

- Perda de informações relevantes.

### Oversampling

Aumento da quantidade de exemplos da classe fraudulenta.

Vantagem:

- Preserva os exemplos legítimos.

Desvantagem:

- Pode gerar overfitting.

A escolha final foi baseada na comparação das métricas de validação.

---

## Avaliação dos Modelos

Os modelos foram comparados utilizando:

### Recall

Mede a capacidade de identificar as fraudes reais.

Fórmula:

```text
Recall = TP / (TP + FN)
```

Neste projeto, o recall da classe de fraude foi considerado a métrica mais importante.

---

### Precisão

Mede quantas das transações classificadas como fraude realmente são fraudes.

Fórmula:

```text
Precisão = TP / (TP + FP)
```

---

### F1-Score

Média harmônica entre Precisão e Recall.

Fórmula:

```text
F1 = 2 * (Precisão * Recall) / (Precisão + Recall)
```

---

### ROC-AUC

Avalia a capacidade geral de separação entre as classes.

---

### Precision-Recall Curve

Particularmente útil para problemas altamente desbalanceados.

---

## Ajuste do Limiar de Decisão

Por padrão, muitos classificadores utilizam:

```text
Threshold = 0.5
```

Entretanto, em detecção de fraude, reduzir o limiar pode aumentar significativamente o Recall.

Durante os testes foram avaliados diferentes valores de threshold para encontrar o melhor equilíbrio entre:

- Fraudes detectadas;
- Falsos positivos gerados.

A escolha final do limiar foi baseada no comportamento observado na curva Precision-Recall.

---

## Explicabilidade com SHAP

Para entender como o modelo toma decisões, foi utilizada a biblioteca SHAP.

O SHAP permite:

- Identificar as variáveis mais importantes;
- Explicar previsões individuais;
- Entender quais características aumentam ou reduzem a probabilidade de fraude.

As análises mostraram quais variáveis tiveram maior influência na classificação das transações.

---

## Comparação dos Modelos

Preencha a tabela abaixo com os resultados obtidos durante a execução do notebook.

| Modelo | Recall | Precisão | F1-Score |
|----------|----------|----------|----------|
| Regressão Logística | XX | XX | XX |
| Random Forest | XX | XX | XX |
| XGBoost | XX | XX | XX |

---

## Principais Resultados

- O recall foi priorizado sobre a acurácia.
- O desbalanceamento impactou significativamente a avaliação dos modelos.
- O ajuste do threshold permitiu aumentar a quantidade de fraudes detectadas.
- O SHAP forneceu transparência sobre as decisões do modelo.
- O melhor modelo foi selecionado considerando o desempenho da classe fraudulenta.

---

## Tecnologias Utilizadas

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-Learn
- XGBoost
- SHAP

---

## Estrutura do Projeto

```text
fraud-detection/
│
├── README.md
├── fraud_detection.ipynb
└── requirements.txt
```

---

## Como Executar

### 1. Clone o repositório

```bash
git clone https://github.com/seu-usuario/fraud-detection.git
```

### 2. Acesse o diretório

```bash
cd fraud-detection
```

### 3. Instale as dependências

```bash
pip install -r requirements.txt
```

### 4. Abra o notebook

```bash
jupyter notebook
```

### 5. Execute todas as células

Certifique-se de que o dataset esteja disponível no caminho configurado no notebook.

---

## Melhorias Implementadas

Além das etapas demonstradas pela Expert, foram realizados experimentos adicionais envolvendo:

- Ajuste de limiar de decisão;
- Comparação entre técnicas de balanceamento;
- Avaliação de múltiplos algoritmos;
- Análises de interpretabilidade utilizando SHAP.

---

## Conclusão

Este projeto demonstra um fluxo completo de Machine Learning aplicado à detecção de fraudes financeiras. Mais importante do que alcançar alta acurácia, o objetivo foi desenvolver um modelo capaz de identificar transações fraudulentas de forma eficiente, utilizando métricas adequadas para problemas altamente desbalanceados e fornecendo explicações transparentes sobre cada decisão tomada pelo modelo.
