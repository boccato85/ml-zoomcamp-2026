# Titanic: análise exploratória e classificação

Projeto de estudo do **ML Zoomcamp 2026**, desenvolvido em 11 e 12 de outubro. Foram realizadas a leitura do CSV, a investigação de hipóteses sobre os passageiros e o treinamento de uma regressão logística para prever sobrevivência.

O foco foi compreender o percurso: explorar os dados, preparar as features, comparar alternativas e interpretar os erros. O estudo teve apoio de IA para tutoria e revisão. A execução e a interpretação dos resultados foram realizadas localmente no VS Code com Jupyter.

## Dados e objetivo

Fonte: [Titanic — Machine Learning from Disaster, Kaggle](https://www.kaggle.com/competitions/titanic/data).

Foi utilizado o `train.csv`, com 891 registros e 12 colunas. O alvo é `Survived`: 0 para não sobrevivente e 1 para sobrevivente. Foi realizada uma divisão estratificada própria para o projeto: **534 passageiros no treino, 178 na validação e 179 no teste**, com `random_state=42`.

O `test.csv` da competição não foi usado: ele não tem o alvo divulgado e é diferente do teste local deste projeto. `gender_submission.csv` também não participa da análise. Não houve envio à competição.

## Como executar

1. Baixe os dados na página oficial do Kaggle e extraia `train.csv` na mesma pasta do notebook.
2. Em um ambiente Python, instale as dependências:

   ```bash
   python -m pip install -r requirements.txt
   ```

3. Abra [titanic_exploracao.ipynb](titanic_exploracao.ipynb) no VS Code/Jupyter e selecione o kernel do ambiente preparado.
4. Reinicie o kernel e execute as células em ordem, a partir do início, usando esta pasta como diretório de trabalho.

As saídas da execução original estão salvas no notebook. A revisão verificou sintaxe, consistência dos cálculos e preservação dessas saídas; o notebook inteiro ainda precisa ser reexecutado após a revisão no ambiente local. Dependências não estão fixadas por versão; pequenas diferenças entre versões podem ocorrer. A execução original registra Python 3.14.6 nos metadados, mas não registra todas as versões das bibliotecas.

## O que foi feito

- Inspeção de tipos, ausentes e distribuição do alvo.
- EDA no treino: sexo, classe, idade, tarifa, porto e composição familiar.
- Investigação da diferença entre quantidades e percentuais por porto/classe.
- Criação de `FamilySize` e `IsAlone`; apenas FamilySize foi testada como alternativa no modelo.
- Preenchimento de Age com mediana 29 e Embarked com moda S, calculadas no treino.
- Codificação com DictVectorizer e padronização com StandardScaler, ajustados somente no treino.
- Regressão logística com configuração padrão de regularização e limite de 1.000 iterações.
- Comparação de limiares, retirada de porto e substituição das contagens familiares.
- Escolha pela acurácia da validação e avaliação final no teste reservado.

O modelo original usa Pclass, Sex, Age, SibSp, Parch, Fare e Embarked. São sete features e dez colunas após codificação. **Pclass foi mantida numérica.** O padronizador foi aplicado a todas as colunas, incluindo indicadores. Não houve transformação logarítmica ou retreinamento com treino e validação juntos.

## Resultados na validação

| Configuração | Acurácia | Acertos |
|---|---:|---:|
| Baseline: sempre prever 0 | 61,80% | 110/178 |
| Modelo original, limiar 50% | **79,78%** | **142/178** |
| Modelo original, limiar 30% | 78,09% | 139/178 |
| Sem Embarked, limiar 50% | 78,09% | 139/178 |
| FamilySize no lugar de SibSp/Parch, limiar 50% | 79,21% | 141/178 |

Baixar o limiar de 50% para 30% elevou o recall de **69,12% para 83,82%**, mas reduziu a precisão de **75,81% para 67,06%**. Foram identificados dez sobreviventes adicionais e gerados treze falsos positivos adicionais. Nenhum limiar é melhor para todos os objetivos.

As diferenças entre conjuntos de features foram pequenas: retirar porto reduziu três acertos e resumir a família reduziu um. Foram mantidos o modelo original e o limiar de 50% para o teste, seguindo a acurácia como critério deste exercício.

## Avaliação final no teste

| Métrica | Resultado |
|---|---:|
| Acurácia | **79,89%** — 143/179 |
| Recall da classe sobrevivente | **69,57%** — 48/69 |
| Precisão da classe sobrevivente | **76,19%** — 48/63 |

| Real / Previsto | 0 | 1 |
|---|---:|---:|
| 0 | 95 | 15 |
| 1 | 21 | 48 |

São 143 acertos e 36 erros. O teste recebeu os parâmetros de preparação e o modelo já aprendidos no treino; não participou da escolha das features ou do limiar.

## Aprendizados e limites

Um dos principais aprendizados foi transformar hipóteses em comparações. O porto com maior proporção de primeira classe não era o porto com mais passageiros dessa classe em números absolutos. Idades medianas iguais não significavam distribuições iguais. A matriz de confusão também mostrou por que acurácia, recall e precisão respondem a perguntas diferentes.

As associações observadas não demonstram causas. A base é pequena e histórica; foi utilizada uma única divisão, sem validação cruzada. Não foram realizados ajustes de regularização, modelagem explícita de interações ou comparação com outros algoritmos. Estes resultados representam um exercício de aprendizado, sem avaliação de uso em produção ou generalização para outra população.

## Referências

- [ML Zoomcamp — Classification](https://github.com/DataTalksClub/machine-learning-zoomcamp/tree/main/03-classification)
- [Prática complementar — Explore more](https://github.com/DataTalksClub/machine-learning-zoomcamp/blob/main/03-classification/14-explore-more.md)
- [Dataset e dicionário de variáveis — Kaggle](https://www.kaggle.com/competitions/titanic/data)
