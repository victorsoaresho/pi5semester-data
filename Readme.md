# P.I - 5º Semestre - Sistema de Classificação de Score de Crédito (Aprendizado de Máquina) 💳

Este projeto faz parte do **Projeto Integrador (P.I)** do 5º Semestre e tem como objetivo a construção de um modelo de **Aprendizado de Máquina** para prever o risco de crédito de clientes.

---

## 🎯 Objetivo do Projeto

O objetivo principal é classificar um cliente como sendo de **bom** ou **mau** score (mau pagador).

A aplicação deste modelo visa:
* Reduzir os riscos de crédito para a instituição de consórcios.
* Otimizar os processos de aprovação de crédito.

---

## 👨‍💻 Equipe

O projeto foi desenvolvido pelos seguintes alunos do 5º DSM:

* Victor Hugo Ferreira Soares
* Pedro Afonso Acacio da Silva
* Samuel Ribeiro Filho
* Paulo Henrique Borges de Andrade Filho

---

## 📊 Conjunto de Dados

O projeto utilizou um conjunto de dados bancários abrangente, contendo informações cadastrais, demográficas e comportamentais de clientes.

| Característica | Detalhe |
| :--- | :--- |
| **Registros Iniciais** | 16.650 |
| **Registros Finais (Tratados)** | 6.770 (Disponível em `data/trusted_dataset.csv`) |
| **Variável Alvo** | `mau` (Indicadora de mau pagador: `True` = mau, `False` = bom) |
| **Exemplos de Variáveis** | `sexo`, `posse_de_veiculo`, `tipo_renda`, `educacao`, `idade`, `tempo_de_emprego` |

---

## ⚙️ Pré-processamento e Limpeza de Dados

As etapas de pré-processamento foram conduzidas para garantir qualidade e coerência antes da etapa de modelagem.

1.  **Tratamento de Nulos:** Colunas categóricas (`sexo`, `tipo_renda`, `tipo_residencia`) tiveram valores ausentes imputados pela **moda** (categoria mais frequente).
2.  **Remoção de Duplicatas:** Remoção aplicada com `df.drop_duplicates()`, resultando na redução de 16.650 para **6.770 amostras**.
3.  **Padronização de Variáveis:**
    * `idade` e `tempo_emprego` passaram por limpeza de caracteres, conversão de tipos e validação de faixa/remoção de outliers.
4.  **Tratamento de Outliers:** Utilização de **boxplots** e método **IQR** ($1.5 \times IQR$) para identificar e remover outliers, principalmente em `tempo_emprego`.
5.  **Escalonamento:** Aplicação de **StandardScaler** e/ou **MinMaxScaler** conforme o modelo, devido à forte assimetria em variáveis como `qtd_filhos` e `tempo_emprego`.

---

## 🧪 Modelagem e Avaliação

O projeto abordou o **desbalanceamento** da classe alvo (`mau` minoritária) para melhorar a detecção.

### Modelos Testados

Modelos avaliados antes do balanceamento:
* Regressão Logística
* Decision Tree (Árvore de Decisão)
* Random Forest
* K-Nearest Neighbors (KNN)
* Gaussian Naive Bayes (GNB)

### Balanceamento e Otimização

* **Técnica de Balanceamento:** **SMOTE** (`Synthetic Minority Over-sampling Technique`) foi aplicado no **conjunto de treino** (`X_train`, `y_train`).
* **Melhor Modelo:** O **Random Forest** destacou-se após o SMOTE.
* **Ajuste de Hiperparâmetros:** Foi realizado ajuste fino com **RandomizedSearchCV** com validação cruzada estratificada, focado em métricas F1/Recall.

### Resultados Finais do Modelo (Random Forest Ajustado)

O **Random Forest ajustado** obteve as seguintes métricas no conjunto de teste:

| Métrica | Valor |
| :--- | :--- |
| **Accuracy** | 0.9027 |
| **Precision** | 0.8813 |
| **Recall (Sensibilidade)** | **0.9331** |
| **F1-Score** | **0.9064** |

**Conclusão:** O **Recall de 93.31%** indica que a grande maioria dos clientes com mau score é identificada, e o **Precision de 88.13%** evita um excesso de falsos positivos. O Random Forest ajustado apresenta o melhor *trade-off* para o objetivo de gestão de risco.

---

## 📦 Artefato e Deploy

O modelo final foi serializado para deployment.

* **Serialização:** O artefato (pipeline completo - pré-processador + Random Forest tunado) foi exportado usando `joblib.dump(csf, "mymodel.joblib")`.
* **Validação:** A validação demonstrou que o artefato é carregável e produz predições coerentes, com mecanismos de *fallback* para alinhamento seguro das *features* (ex: reindexação após one-hot encoding).

### 💡 Boas Práticas Recomendadas

1.  **Salvar o Pipeline Completo:** Garante que as transformações aplicadas no treino também ocorram na inferência.
2.  **Monitoramento:** Implementar monitoramento em produção para *data drift* e performance.
3.  **Robustez:** Usar `OneHotEncoder` com `handle_unknown='ignore'` dentro do pipeline para robustez contra categorias novas.