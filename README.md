# Hospital Mortality Prediction

Projeto acadêmico de classificação binária para estimar a probabilidade de óbito em pacientes hospitalizados, seguindo o regulamento do Code Challenge 2026.

## Objetivo e princípio de trabalho

O objetivo é comparar modelos de forma reproduzível, com foco na métrica principal da competição, AUC-ROC. A análise dos atributos será descrita como associação com as predições, sem inferências causais ou afirmações clínicas. O projeto será desenvolvido por etapas: primeiro entender e validar os dados; depois estabelecer uma validação e um baseline; em seguida comparar modelos e limiares; por fim interpretar erros e preparar a submissão.

Não há garantia de que um algoritmo específico seja o melhor. Modelos baseados em árvores e boosting são candidatos naturais para comparação em dados tabulares; a escolha dependerá da validação definida antes de olhar o conjunto de teste da competição. As variáveis têm nomes categorizados no material da competição, então seus significados clínicos não serão presumidos.

## Estrutura

```text
.
├── data/
│   └── raw/                 # Dados locais, ignorados pelo Git
├── notebooks/
│   └── 01_auditoria_inicial.ipynb
├── .gitignore
├── README.md
└── requirements.txt
```

## Dados

Coloque uma cópia local de `final_treino_val.csv` em `data/raw/final_treino_val.csv`. Os arquivos de dados tabulares são ignorados pelo Git; não remova essa proteção nem force a inclusão dos dados no repositório público. O CSV original permanece fora do repositório.

No arquivo recebido, os nomes das colunas são `ID` e `DESFECHO` em maiúsculas. O notebook padroniza os nomes em memória para facilitar a análise. `desfecho` usa `0` para cura e `1` para óbito; `id` é identificador e não deve ser usado como preditor. Conforme o regulamento, AUC-ROC é a métrica principal. LR− e F1 são critérios de desempate baseados nos rótulos, logo a escolha do limiar será tratada separadamente da avaliação da AUC.

## Ambiente

Com Python instalado, crie e ative um ambiente virtual e instale as dependências:

```bash
python -m venv .venv
```

No Windows:

```powershell
.venv\Scripts\Activate.ps1
```

No macOS/Linux:

```bash
source .venv/bin/activate
```

Depois:

```bash
python -m pip install -r requirements.txt
jupyter notebook
```

Abra `notebooks/01_auditoria_inicial.ipynb` e execute as células em ordem.

## Organização metodológica

O notebook segue o fluxo abaixo. Nesta etapa estão preparados o carregamento, a auditoria e o protocolo; spot-checking e resultados virão em uma etapa posterior.

1. **Carregamento inicial:** importação de bibliotecas e leitura dos dados.
2. **Preparação dos dados:** tratamento de inconsistências após investigação; sem criar atributos derivados até haver justificativa; preparação de pipelines para codificação e tratamento de ausências dentro dos folds.
3. **Protocolo de avaliação:** divisão estratificada de 80% para desenvolvimento e 20% para holdout local, com semente 42; validação cruzada estratificada de 5 folds no desenvolvimento; ROC-AUC para seleção e comparação principal. O holdout será consultado uma vez após as decisões de modelo.
4. **Spot-checking:** próxima etapa; comparar um conjunto pequeno de algoritmos e configurações iniciais.
5. **Resultados:** próxima etapa; tabela e gráfico com resultados de validação e, após seleção, avaliação no holdout.
6. **Interpretação:** próxima etapa; discutir desempenho, diferenças, limitações e importância de atributos como associações, sem afirmações causais.

O `id` fica fora de `X`. Transformações aprendidas, como imputação, codificação e escala, devem ser ajustadas dentro de `Pipeline`/`ColumnTransformer` para cada fold. LR− e F1 dependem do limiar; o limiar será escolhido usando predições out-of-fold dos dados de desenvolvimento e seus efeitos serão reportados separadamente da AUC. Após avaliação local e definição das escolhas, o modelo final poderá ser reajustado com todos os dados rotulados. Nenhuma decisão será baseada nos rótulos do teste final da competição.
