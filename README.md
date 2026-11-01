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

## Etapas planejadas

1. Auditoria inicial de esquema, qualidade, tipos e distribuição do alvo.
2. Definição de validação reproduzível e baseline simples.
3. Comparação de modelos com pipelines e seleção de hiperparâmetros sem consultar o teste final.
4. Escolha do limiar conforme os critérios da competição e análise dos erros.
5. Interpretação de importância de atributos quando aplicável e preparação da submissão reproduzível.

As próximas etapas serão implementadas depois de revisar os achados desta auditoria, preservando as decisões e resultados experimentais.
