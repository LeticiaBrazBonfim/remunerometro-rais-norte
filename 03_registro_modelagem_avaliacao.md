# Registro de modelagem, validação e escolha — Remunerômetro

> **Atualização vigente em 08/11/2026:** o notebook de entrega foi executado novamente após a instalação das dependências. As 26 células têm saídas salvas sem erros, com as mesmas contagens, escolha e métricas da rodada congelada. Somente notebook e HTML estão em `entrega/`. Os caminhos antigos de `resultados/`, scripts, testes e outras saídas citados abaixo são **históricos**; os arquivos foram removidos na limpeza do repositório e não são necessários para executar o notebook atual.

Rodada original executada em 06/11/2026 e reproduzida em 08/11/2026. Prazo vigente: 08/11/2026. Os números abaixo vêm das execuções registradas e das saídas preservadas no notebook, não de preenchimento estimado.

A explicação da seção 4 do notebook foi simplificada em 08/11/2026: baseline, modelos, cinco dobras, MAE, RMSE, log e `alpha` agora aparecem em uma sequência curta e sem fórmulas longas. Código, modelos, grades e resultados permaneceram iguais.

## 1. Dados, alvo e amostra

- **Origem:** base local RAIS 2025 Norte, `dados/RAIS_VINC_PUB_NORTE.COMT.gz`; cache Parquet conferido por esquema e metadados.
- **Alvo:** `vl_rem_media_nom`, média mensal nominal positiva e finita; `ln(y)` no ajuste, `exp` na inversão. Sem correção de retransposição estimada no teste.
- **Base:** 5.645.037 vínculos, 62 colunas; 4.893.994 elegíveis. Os 751.043 zeros (13,30%) limitam a cobertura. Não foram classificados como erros nem como censura sem evidência.
- **Partição:** 80/20, semente pessoal 011900 (11900). Treino antes da amostra: 3.915.195; teste: 978.799.
- **Dtreino efetivo:** exatamente 2.000 vínculos sorteados apenas do treino; as posições reproduzem as saídas originais. O restante do treino não é incorporado ao ajuste final.
- **Dimensão:** p=209; p/n=0,1045. Dobras: 187, 183, 175, 182 e 177 colunas, com dimensões iguais entre treino/validação de cada dobra.
- **Qualidade:** média sem NaN/negativos; dezembro tem 41,43% de ausências. Idades/jornadas zero, jornada 99 e município de trabalho 999999 estão registrados em `auditoria_base.json`, sem recodificação arbitrária.
- **Unidade:** vínculo, não pessoa; sem identificador individual não foi possível controlar pessoas com vários vínculos entre partições.

## 2. Validação e proteção do teste

`KFold(5, shuffle=True, random_state=11900)` igual para todas as alternativas. Escala e one-hot são ajustados no treino de cada dobra dentro do Pipeline. A imputação saiu da versão atual porque não há NaN nos oito preditores da base auditada; a reprodução confirmou os mesmos resultados. CBO4 é transformação determinística; frequência mínima 5 é aprendida por dobra. Sexo e raça/cor ficam fora de X; escolaridade/sexo ficam disponíveis para análise de erro.

Baseline: mediana do alvo em log com inversão exp. MQO, Ridge e Lasso são ajustados em log e selecionados por **MAE CV em reais**, com RMSE log complementar. MQO usa tolerância 1e-8; Ridge usa LSQR, tolerância 1e-8; Lasso usa tolerância 1e-6 e até 100.000 iterações. Avisos de não convergência interrompem a execução. [Documentação oficial do Ridge](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.Ridge.html).

O teste não foi usado para selecionar atributos, alphas, transformações ou vencedor. As análises usadas para definir os atributos vieram da amostra de treino. A versão atual confere, antes da célula de teste, a contagem de elegíveis, os 2.000 vínculos, o tamanho do teste, Ridge e os alphas originais. Em seguida reproduz as previsões em blocos. Os JSON auxiliares da rodada antiga foram removidos; o notebook é autossuficiente.

## 3. Grades e parâmetros

| Modelo | Grade inicial | Selecionado | Critério | Borda / ação |
|---|---|---|---|---|
| Baseline | Sem busca | Mediana do log | Referência | Não se aplica |
| MQO | Especificação fixa | Sem penalização | MAE CV para comparação | Não se aplica |
| Ridge | 16 pontos log de 0,01 a 1.000 | alpha=4,6415888336 | Menor MAE CV em reais | Interior; não exigiu expansão |
| Lasso | 13 pontos log de 0,00001 a 0,1 | alpha=0,0004641589 | Menor MAE CV em reais | Interior; não exigiu expansão |

A expansão automática, se necessária, preserva os pontos prévios e amplia por fator 100 na direção da borda, limitada a quatro ampliações. Toda busca penalizada deve sair da borda antes do teste. A barreira foi testada também com um caso sintético em que apenas Lasso fica na borda.

## 4. Comparação treino × CV × teste

DP é o desvio entre cinco dobras, não intervalo de confiança.

**RMSE em log**

| Modelo | Treino | CV média | CV DP | Teste |
|---|---|---|---|---|
| Baseline | 0,6894 | 0,6882 | 0,0403 | 0,7030 |
| MQO | 0,4145 | 0,4669 | 0,0307 | 0,4687 |
| Ridge | 0,4202 | 0,4604 | 0,0258 | 0,4653 |
| Lasso | 0,4213 | 0,4607 | 0,0269 | 0,4663 |

**MAE em R$**

| Modelo | Treino | CV média | CV DP | Teste |
|---|---|---|---|---|
| Baseline | 1.808,96 | 1.809,19 | 190,86 | 1.865,52 |
| MQO | 1.253,13 | 1.386,99 | 140,29 | 1.374,01 |
| Ridge | 1.269,46 | 1.369,17 | 127,92 | 1.374,79 |
| Lasso | 1.268,62 | 1.371,74 | 132,75 | 1.373,30 |

## 5. Curvas e diagnóstico de ajuste

As duas curvas estão em `resultados/curvas_validacao.png` e incorporadas ao notebook/HTML. Em Ridge, alpha=0,01 tem MAE CV R$ 1.386,82 e treino nas dobras R$ 1.251,86: diferença de aproximadamente R$ 134,97. No alpha escolhido, a diferença diminui para cerca de R$ 98,11. Alpha=1.000 aumenta o erro de treino para R$ 1.601,16 e de CV para R$ 1.611,37, coerente com contração excessiva/subajuste.

Em Lasso, alpha=0,00001 tem MAE CV R$ 1.389,05; alpha=0,1 chega a R$ 1.719,20, com erro de treino R$ 1.716,83. A região interior selecionada evita esses extremos. Penalização pequena permite maior ajuste às categorias da amostra e maior distância treino/CV; p/n=0,1045 e categorias raras tornam a regularização relevante. Isso não permite afirmar significância estatística da diferença entre os dois modelos penalizados.

## 6. Modelo pré-selecionado e teste

**Ridge**, por menor MAE CV (R$ 1.369,17). O MAE CV de Lasso foi R$ 1.371,74, diferença pequena diante dos DPs entre dobras. O teste não foi usado para trocar o vencedor.

No teste, Ridge teve MAE **R$ 1.374,79**, RMSE log **0,4653** e redução de **26,31%** no MAE frente ao baseline (R$ 1.865,52). Lasso teve MAE R$ 1.373,30 e MQO R$ 1.374,01; esses números não alteram a seleção anterior.

O viés médio de Ridge foi **−R$ 589,00** (previsto menos observado). Média observada do teste: R$ 3.479,09; média prevista: R$ 2.890,09. O ganho sobre a constante é útil para comparação exploratória, mas o erro e a subestimação impedem tratar a estimativa como promessa salarial. O resultado não comprova superioridade sobre uma referência salarial agrupada por ocupação/município, que não foi avaliada como concorrente independente no teste. Na etapa 5, a referência agrupada foi examinada apenas no treino/CV, como exploração posterior.

### Critério matemático

Defina z=ln(y). MQO minimiza soma((z−b0−Xb)²); Ridge acrescenta alpha×||b||² à soma; Lasso minimiza soma((z−b0−Xb)²)/(2n)+alpha×||b||₁. O intercepto não é penalizado. A seleção minimiza a média do MAE em reais na CV após inverter exp. A inversão não é automaticamente a média aritmética condicional; não foi introduzida uma correção após olhar o teste.

## 7. Erro do modelo escolhido por grupo

Os códigos são os observados, sem rótulos atuais não confirmados. A última coluna é MAE dividido pela **média observada do grupo**, não MAPE individual.

| Variável | Código | n | MAE R$ | RMSE log | Viés R$ | MAE / média (%) |
|---|---|---|---|---|---|---|
| Escolaridade | 1 | 5.510 | 976,61 | 0,4169 | -97,70 | 34,00 |
| Escolaridade | 2 | 15.932 | 666,92 | 0,3727 | 107,40 | 30,35 |
| Escolaridade | 3 | 8.884 | 702,31 | 0,3825 | -205,44 | 29,89 |
| Escolaridade | 4 | 30.696 | 683,23 | 0,3926 | -123,84 | 29,27 |
| Escolaridade | 5 | 56.429 | 725,60 | 0,3965 | -93,43 | 29,94 |
| Escolaridade | 6 | 43.713 | 575,73 | 0,3907 | -161,33 | 28,97 |
| Escolaridade | 7 | 546.442 | 759,25 | 0,3874 | -187,63 | 30,84 |
| Escolaridade | 8 | 21.542 | 1.073,66 | 0,4672 | -467,20 | 38,07 |
| Escolaridade | 9 | 240.368 | 3.096,23 | 0,6287 | -1.687,70 | 48,52 |
| Escolaridade | 10 | 4.782 | 5.929,55 | 0,8077 | -5.002,20 | 57,52 |
| Escolaridade | 11 | 4.501 | 5.711,60 | 0,7207 | -3.899,17 | 52,27 |
| Sexo | 1 | 554.965 | 1.414,48 | 0,4784 | -728,46 | 40,37 |
| Sexo | 2 | 423.834 | 1.322,83 | 0,4475 | -406,38 | 38,38 |

Os códigos de escolaridade 10 e 11 têm MAE de aproximadamente R$ 5,9 mil e R$ 5,7 mil e subestimação forte. Também têm remuneração média maior; ainda assim, o erro relativo à média do grupo é de 57,52% e 52,27%, contra cerca de 29–34% em vários outros grupos. Isso exige cautela especial nesses perfis. Os grupos têm 4.782 e 4.501 vínculos no teste, respectivamente; isso não corrige a menor cobertura de tais perfis nos 2.000 registros de treino.

Sexo código 1: MAE R$ 1.414,48, n=554.965; código 2: R$ 1.322,83, n=423.834. Não se atribui causalidade à diferença. Excluir sexo/raça de X não garante ausência de desigualdade ou de atributos que funcionem como proxies. Não foi feita uma auditoria completa de equidade.

## 8. Execução, reprodução e evidências da etapa 4

- Execução integral: 12 células de código, todas aprovadas, 43.9s na primeira execução completa. Sem erros ou stderr salvos. O tempo pode variar entre máquinas/rodadas.
- Congelamento anterior ao teste (UTC): `2026-11-07T01:11:44.518244+00:00`.
- Identificação: `0d714609f49d692485a8ceca8456a2e22d7d2cbe49b00142a2ea9b652700d06c`.
- `resultados/avaliacao_teste.json`: contagens, métricas e grupos; 978.799 vínculos em cada modelo e em cada total por variável de grupo.
- `resultados/historia_grades.json` e `busca_*.csv`: buscas completas.
- `resultados/comparacao_modelos.csv`, `erros_por_grupo.csv`: tabelas originais.
- `resultados/auditoria_base.json`, `diagnostico_preparo.json`: qualidade e preparação.
- `resultados/execucao_notebook.json`: execução integral.
- `resultados/remunerometro.html`: exportação com saídas e figuras.
- `notas.md`: reprodução; `tests/test_fluxo.py`: equivalência e proteções.

Na etapa 4, a reprodução em processo novo também concluiu as 12 células, em 25,9s, reutilizando o teste já avaliado. O SHA-256 de `avaliacao_teste.json` permaneceu idêntico byte a byte. A conferência daquela versão aprovou 34 verificações, incluindo cinco testes automatizados, dependências sem conflitos, correspondência CSV/JSON, preservação dos dados/cache/notebook histórico e integridade dos três gráficos incorporados ao HTML. Os gráficos de distribuição, curvas e grupos foram também examinados visualmente. A evidência atual, com os complementos da etapa 5, está descrita abaixo e substitui os hashes de apresentação da versão anterior.

Bruto e cache originais foram preservados. Materiais de origem do professor e notebook de apoio histórico não foram alterados. Não houve reinstalação arbitrária para o erro de DLL histórico, que não se repetiu. Requisitos passaram a UTF-8 e listam as dependências diretas efetivamente usadas.

## 9. Declaração de IA e revisão

Codex/OpenAI auxiliou na leitura de documentação, auditoria, integração/refatoração, barreiras de teste, execução e redação dos registros. Todas as seções da versão integrada foram influenciadas por IA. A validação técnica executada neste ambiente não equivale à revisão acadêmica final.

Foram confirmados o recorte Norte, o uso da base local e o material do professor como fonte principal. A revisão acadêmica final permanece pendente. O arquivo separado com nome literal de template não foi encontrado; a organização segue o enunciado e os laboratórios 4/6, com a limitação registrada.

## 10. Estado da entrega e limites remanescentes

- [x] Semente na primeira célula; 2.000 vínculos apenas do treino.
- [x] Notebook executado do início ao fim; caminhos relativos; leitura limitada em memória.
- [x] Pipeline/CV; quatro alternativas; duas métricas; grades interiores.
- [x] Escolha congelada antes do teste; tabela final e erro por escolaridade/sexo.
- [x] Curvas examinadas e interpretadas; critério matemático escrito.
- [x] Limitações e IA; HTML gerado sem microdados.
- [ ] Revisão acadêmica manual e eventual conferência do formato esperado pelo professor.

Limites semânticos do dicionário atual e PUB, dependência entre vínculos da mesma pessoa, validade externa/temporal, incerteza individual e ausência de avaliação confirmatória da referência agrupada no teste permanecem explícitos. Não são ocultados por uma execução sem erro. Nenhuma mudança de modelo foi feita em resposta ao desempenho no teste.


## 11. Consolidação e simplificação vigente

A semente foi mantida em **011900**. Os arquivos públicos são `REM_Leticia.ipynb` e `REM_Leticia.html`. A versão vigente contém as seções 0–7 do enunciado e todo o processamento no notebook.

Uma versão intermediária acrescentou uma referência agrupada e uma descrição de coeficientes depois da avaliação final. Como não eram exigidas pelo professor e aumentavam o volume do projeto, saíram da apresentação principal. Os arquivos históricos foram removidos na limpeza do repositório; nunca foram usados para trocar Ridge nem para reabrir o teste.

A versão final preserva o mesmo problema, os oito atributos, as grades e os quatro modelos. O notebook executou **26 células de código**, em **45,2 s**, sem erros. O HTML contém **13 tabelas e três gráficos**. Foram reproduzidos os MAEs de teste dos quatro modelos, o RMSE em log e os 13 grupos, com diferenças inferiores a R$ 0,000001 nos valores exibidos. O teste foi recalculado apenas para reproduzir o procedimento já fixado; não houve nova seleção. A pasta `entrega/` contém somente notebook e HTML. Depois da execução, mudaram apenas explicações em texto e o layout do HTML, sem alteração do processamento.

A revisão acadêmica final e a submissão continuam pendentes. Nenhuma mudança de modelo foi feita em resposta ao desempenho no teste.
