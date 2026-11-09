# Hospital Mortality Prediction

Projeto acadêmico de classificação binária para estimar a probabilidade de óbito em pacientes hospitalizados, seguindo o regulamento do Code Challenge da Escola Regional de Aprendizado de Máquina
e Inteligência Artificial da Região Sul de 2026.

## Objetivo e princípio de trabalho

O objetivo é comparar modelos de forma reproduzível, com foco na métrica principal da competição, AUC-ROC. A análise dos atributos será descrita como associação com as predições, sem inferências causais ou afirmações clínicas. O protocolo usa validação cruzada aninhada sobre todos os dados rotulados e reserva o teste oficial somente para pontuação final.

Não há garantia de que um algoritmo específico seja o melhor. Modelos baseados em árvores e boosting são candidatos naturais para comparação em dados tabulares; a escolha dependerá da validação definida antes de olhar o conjunto de teste da competição. As variáveis têm nomes categorizados no material da competição, então seus significados clínicos não serão presumidos.

## Estrutura

```text
.
├── data/
│   └── raw/
│       └── .gitkeep         # Mantém o diretório; dados locais são ignorados
├── notebooks/
│   ├── 01_auditoria_inicial.ipynb
│   └── 02_avaliacao_teste.ipynb
├── results/                  # Artefatos locais gerados; JSON/CSV/figuras ignorados pelo Git
├── .gitattributes
├── .gitignore
├── README.md
└── requirements.txt
```

## Dados

Coloque uma cópia local de `final_treino_val.csv` em `data/raw/final_treino_val.csv`. Os arquivos de dados tabulares são ignorados pelo Git; não remova essa proteção nem force a inclusão dos dados no repositório público. O CSV original permanece fora do repositório.

No arquivo recebido, os nomes das colunas são `ID` e `DESFECHO` em maiúsculas. O notebook padroniza os nomes em memória para facilitar a análise. `desfecho` usa `0` para cura e `1` para óbito; `id` é identificador e não deve ser usado como preditor. Conforme o regulamento, AUC-ROC é a métrica principal. Regras para decisão binária e limiar dependem do formato de submissão e dos critérios oficiais.

## Instalação e execução

Com Python instalado, crie e ative um ambiente virtual:

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

Instale todas as dependências listadas em `requirements.txt` com este comando:

```shell
python -m pip install -r requirements.txt
```

O pacote `scikit-learn` fornece os imports `sklearn` usados pelo notebook. Para abrir o Jupyter:

```shell
jupyter notebook
```

Abra `notebooks/01_auditoria_inicial.ipynb` e execute as células em ordem para reproduzir auditoria e busca. Quando receber o teste oficial, use o fluxo abaixo.

## Fluxo de desenvolvimento e teste oficial

Execute `notebooks/01_auditoria_inicial.ipynb` do início ao fim com todos os registros rotulados de `final_treino_val.csv`. O protocolo usa validação estratificada aninhada: 5 folds externos para comparar famílias e 3 folds internos para selecionar parâmetros dentro de cada treino externo. A validação cruzada é uma estimativa interna de desenvolvimento; registros participam de validações ao longo dos folds, portanto ela não é um teste independente.

Após a comparação aninhada, o notebook executa uma nova busca estratificada de 5 folds em 100% dos registros rotulados. Os parâmetros selecionados são registrados em `results/stage4_model_search.json`; pré-processamento continua dentro de cada pipeline/fold. `id` não entra em `X`. Não há divisão fixa de holdout. As tabelas e gráficos da etapa 5 descrevem apenas AUC externa por fold, média e dispersão descritiva; não são métricas de um conjunto reservado.

Quando chegar o teste oficial:

1. Coloque-o em `data/raw/final_teste.csv`.
2. Reexecute primeiro o notebook 01 para refazer a validação e busca final sobre o CSV rotulado completo.
3. Execute `notebooks/02_avaliacao_teste.ipynb`. Ele seleciona a família pela maior AUC externa média já observada na validação aninhada, reajusta essa família em todos os rótulos e então gera scores para o teste.

O teste oficial não participa do ajuste de pré-processamento, treinamento, seleção de modelo ou hiperparâmetros, calibração de scores nem escolha de limiar. Se contiver `DESFECHO`, seus rótulos são usados somente depois de gerar os scores para calcular métricas finais. Não use essas métricas para voltar a escolher o modelo. Os scores individuais ficam em `results/official_test_model_scores.csv` e os agregados em `results/official_test_metrics.json`.

## Organização do projeto

1. **Auditoria:** carregar o CSV local, verificar esquema, qualidade, cardinalidade e distribuições sem usar `id` como preditor.
2. **Pré-processamento:** imputação, codificação e escala dentro de `Pipeline`/`ColumnTransformer` para evitar vazamento entre folds.
3. **Comparação aninhada:** seis famílias, AUC-ROC, 5 folds externos e 3 internos estratificados sobre todos os registros rotulados.
4. **Busca final:** nova busca aleatória com CV estratificada em todos os registros rotulados; os resultados da CV final servem à seleção de parâmetros e não são estimativa independente.
5. **Resultados e interpretação:** curvas e tabelas dos folds externos; importância por permutação nos folds externos, entendida como associação preditiva, não causal.
6. **Teste oficial:** busca refeita no conjunto rotulado completo, ajuste em 100% dele e pontuação do teste sem consultar seus rótulos durante qualquer decisão.

## Proteção de dados

Arquivos tabulares e figuras são ignorados pelo Git. Não force a inclusão de CSVs de pacientes, scores individuais ou resultados agregados que possam expor informação sensível. Os artefatos de resultados são gerados localmente ao executar os notebooks.
