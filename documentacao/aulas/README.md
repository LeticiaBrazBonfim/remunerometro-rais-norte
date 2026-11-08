# Fontes de aula — slides e laboratórios do professor

> **Decisão registrada em 26/10/2026: estes arquivos são as fontes principais do projeto.** São os slides e os notebooks produzidos **pelo professor em sala**. Vamos **espelhar o professor**: mesma sequência de passos, mesmos arquivos, mesma base teórica e mesma lógica. Nenhum arquivo desta pasta deve ser alterado — são material de origem; as adaptações (caminho, base de dados, semente) acontecem **nos nossos notebooks**, ficando registradas nesta página.
>
> Autoria dos slides e laboratórios: Prof. Dr. Aléssio Tony Cavalcanti de Almeida — Ciência de Dados para Negócios, UFPB/CCSA, MLS 2026.2.
>
> **Atualização de 06/11/2026:** os arquivos de origem do professor permanecem intactos. Caminho real da base: `dados/RAIS_VINC_PUB_NORTE.COMT.gz` (sem subpasta `bruto`). O fluxo vigente em `remunerometro.ipynb` adapta os laboratórios 4/6 para essa base, alvo média mensal nominal positiva, matrícula 011900 e comparação baseline/MQO/Ridge/Lasso. O prazo atual é 08/11/2026; datas anteriores são históricas.

## Estrutura

```
docs/aulas/
├── README.md                    ← este mapa
├── slides/                      ← decks das aulas 1 a 5
│   └── slides_aula_01.pdf … slides_aula_05.pdf
└── laboratorios/                ← notebooks do professor usados em sala
    ├── lab_aula_02.ipynb
    ├── lab_aula_03.ipynb
    ├── lab_aula_04.ipynb
    ├── lab_aula_06.ipynb
    └── dados_pof_pb_simulados.csv   (dado de ensino lido pelo lab da aula 4)
```

O CSV fica **dentro de `laboratorios/`** porque o `lab_aula_04.ipynb` o abre por caminho relativo (`Path('dados_pof_pb_simulados.csv')`): mover um sem o outro quebra a execução.

## Mapa aula a aula

| Aula | Data | Slides | Laboratório | Núcleo da aula |
|---:|---|---|---|---|
| 1 | 03/09/2026 | `slides/slides_aula_01.pdf` | — | Aprendizagem supervisionada começa pela decisão; unidade, população, alvo, horizonte; Canvas |
| 2 | 10/09/2026 | `slides/slides_aula_02.pdf` | `laboratorios/lab_aula_02.ipynb` | Da necessidade ao problema preditivo; prevalência, baseline, capacidade, lift, curva de ganho, limiar sob custos |
| 3 | — | `slides/slides_aula_03.pdf` | `laboratorios/lab_aula_03.ipynb` | Clínica de projetos; capacidade, custos e decisão (`TP_k`, `FP_k`, `precision@k`, `V_k`) |
| 4 | — | `slides/slides_aula_04.pdf` | `laboratorios/lab_aula_04.ipynb` | Da pergunta aos dados admissíveis; contrato, qualidade, relações, **leakage**, partição, baseline, preparo só no treino |
| 5 | — | `slides/slides_aula_05.pdf` | — | Regressão linear simples; MQO, resíduos, baseline × modelo, MAE/RMSE, extrapolação |
| 6 | 22/10/2026 | **não adicionado** | `laboratorios/lab_aula_06.ipynb` | Quando a reta precisa de freio, e quando o alvo é um sim/não: ridge, lasso, caminhos de coeficientes, **Remunerômetro múltiplo na RAIS**, logística, limiar pelo custo |

**Lacunas do material:** slides da Aula 6; laboratórios das Aulas 1 e 5.

## Laboratórios — inventário

| Arquivo | Células (código / markdown) | Execução salva | Semente do professor | Bibliotecas |
|---|---:|---|---|---|
| `lab_aula_02.ipynb` | 23 (9 / 14) | 9 de 9 executadas, 0 erros | `20260910` | numpy, pandas, plotly |
| `lab_aula_03.ipynb` | 11 (6 / 5) | **nenhuma** (0 executadas) | `20260917` | numpy, pandas, plotly |
| `lab_aula_04.ipynb` | 19 (8 / 11) | **nenhuma** (0 executadas) | `20261001` | numpy, pandas, matplotlib, sklearn |
| `lab_aula_06.ipynb` | 40 (22 / 18) | 22 de 22 executadas, 0 erros | `20261022` | numpy, pandas, matplotlib, sklearn, statsmodels |

### Seções de cada laboratório (ordem a espelhar)

- **`lab_aula_02.ipynb`** — 1. A base · 2. Prevalência e o baseline que engana · 3. A restrição de capacidade · 4. Curva de ganho · 5. O limiar ótimo sob custos assimétricos · 6. Quando as duas coisas colidem · 7. Exercício para levar ao Canvas · Transferência para outros setores.
- **`lab_aula_03.ipynb`** — Métricas da fila · Curva de capacidade · Sensibilidade ao custo · Entrega da dupla.
- **`lab_aula_04.ipynb`** — 1. Configuração e contrato · 2. Leitura e validação da unidade · 3. Qualidade, cobertura e distribuição do alvo · 4. Relações com o alvo · 5. Relógio da decisão e leakage · 6. Partição que representa o uso · 7. Baseline fora da amostra · 8. Pré-processamento ajustado somente no treino · Saídas da equipe.
- **`lab_aula_06.ipynb`** — **Parte A — Extensões da regressão linear:** A0 critério · A1 ridge à mão · A2 ridge × `scikit-learn` · A3 por que o lasso zera coeficientes · A4 quando regularizar vale a pena · A5 caminhos de coeficientes · **A6 Remunerômetro múltiplo na RAIS**. **Parte B — Classificação:** B1 logística à mão e separação perfeita · **B2 RAIS: o vínculo paga mais de 2 salários mínimos?** · por que não regressão linear no 0/1 · logit com poucos atributos · pipeline completo e avaliação no teste · escolhendo o limiar pelo custo · "Para entregar (individual) — junto com a Ficha do Modelo 2".

O `lab_aula_04.ipynb` é o **espelho direto** do que este repositório chama de auditoria de dados (contrato, qualidade, vazamento, partição e baseline), e o `lab_aula_06.ipynb` é o **espelho direto** da parte de modelagem exigida pelo enunciado (ridge, lasso, pipeline, teste e limiar).

## Dados de ensino usados nos laboratórios

- **`laboratorios/dados_pof_pb_simulados.csv`** — 96 famílias, 12 municípios, **11 colunas** (`id_familia`, `municipio`, `estado`, `zona`, `renda_mensal`, `consumo_mensal`, `idade_chefe`, `sexo_chefe`, `estado_civil_chefe`, `escolaridade_chefe`, `pessoas_familia`), todos os registros com `estado = PB`.
- É **amostra sintética de ensino** (apresentada na Aula 4 como "Amostra POF simulada da Paraíba"); **não** é a base do projeto e **não** serve como evidência empírica.
- A base do projeto continua sendo **apenas** `dados/RAIS_VINC_PUB_NORTE.COMT.gz` (base única Norte, conforme o recorte confirmado).

## O que espelhar e o que adaptar

| Aspecto | Como o professor fez | Nosso projeto | Regra |
|---|---|---|---|
| Sequência de passos | Seções 1→8 (aula 4) e A0→A6, B1→B2 (aula 6) | igual | **Espelhar na mesma ordem**, com as mesmas verificações |
| Arquivos e lógica | Notebooks próprios, dados sintéticos nas aulas 2/3/4 | mesmo raciocínio aplicado à RAIS Norte | Manter a mesma estrutura de contrato, asserções, gráficos e conclusões |
| Base de dados | Aulas 2/3/4: dados simulados; **aula 6**: `../../data/rais_pb_2025_analitica.parquet` | `RAIS_VINC_PUB_NORTE.COMT.gz` (única base) | **Arquivos do professor intactos**; trocar caminho/base só nos nossos notebooks, registrando a diferença |
| Semente | Datas (`20260910`, `20260917`, `20261001`, `20261022`) | `SEMENTE = "011900"` (matrícula) | A regra do enunciado prevalece sobre a do laboratório |
| Salário mínimo | `SALARIO_MINIMO_2025 = 1518.00` (lab 6) | confirmar antes de usar | Registrar a fonte do valor adotado |
| Teste | lab 6 avalia no teste dentro do laboratório | teste aberto **uma única vez**, depois de congelar | A regra do enunciado prevalece |

## Pendência relacionada

**Registro histórico de 26/10:** o arquivo separado `projeto_remunerometro_template.ipynb`, citado na p. 3 do enunciado, não havia sido localizado.

**Esclarecimento registrado em 06/11:** o professor informou que a referência está em algum material de aula, slide ou notebook de laboratório, sem especificar qual. A orientação vigente é usar sempre esse material como base principal. O notebook integrado segue o enunciado e os laboratórios 4/6. Não se afirma que o arquivo separado com o nome literal foi encontrado; a conformidade de apresentação continua sujeita à revisão acadêmica final.
