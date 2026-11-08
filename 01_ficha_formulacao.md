# Ficha de formulação do problema — Remunerômetro

## 1. Identificação e decisão

- **Autoria:** Letícia Braz Bonfim.
- **Semente:** matrícula `011900`, valor numérico `11900`, na primeira célula.
- **Usuário:** trabalhador(a) que compara cenários profissionais.
- **Decisão:** quais combinações de ocupação, jornada, município e características do vínculo vale a pena investigar.
- **Quando:** ao consultar um cenário, antes de se candidatar. Os atributos do cenário são informados pelo usuário; o sistema não acessa informações de um contrato futuro desconhecido.
- **Unidade histórica:** um vínculo formal na base local RAIS 2025 **Norte**. Um vínculo não equivale necessariamente a uma pessoa única.
- **Fonte única:** `dados/RAIS_VINC_PUB_NORTE.COMT.gz`, com cache Parquet local. Não se interpreta `PUB` como setor público sem comprovação.

## 2. População, alvo e referência temporal

- **População modelada:** vínculos da base local com `vl_rem_media_nom` finita e positiva. Dos 5.645.037 vínculos, 4.893.994 atendem à regra; 751.043 (13,30%) têm média zero e ficam fora. Não foi aplicado filtro adicional por ativo em dezembro, vínculo abandonado ou códigos especiais sem significado confirmado.
- **Tipo:** regressão.
- **Alvo:** remuneração **média mensal nominal do vínculo ao longo do ano**, em reais, campo `vl_rem_media_nom`. Não é a soma anual dos rendimentos.
- **Ajuste:** `ln(vl_rem_media_nom)`; inversão `exp(pred_log)`, sem fator de correção estimado no teste. A inversão não é automaticamente a média aritmética condicional em reais.
- **Referência:** vínculos históricos de 2025; não se trata de previsão longitudinal nem de cotação em tempo real.
- **Por que manter a média:** combina com a comparação de referências mensais e tem maior cobertura que dezembro. A decisão não decorre apenas de menos NaN. Dezembro tem 41,43% de ausências, concentradas nos inativos (89,81%; ativos: 19,99%). As duas opções medem populações/referências distintas.

## 3. Regra de decisão, baseline e métricas

Superestimar pode gerar expectativa salarial irreal; subestimar pode levar a descartar cenários. Os custos relativos não foram quantificados. Usamos MAE como erro absoluto compreensível em reais, sem afirmar que mede toda a utilidade da decisão.

- **Baseline:** `DummyRegressor(strategy="median")` no alvo em log, invertido por `exp`; uma mesma previsão constante nas duas métricas. Calculado somente nos dados de ajuste de cada dobra.
- **Alternativas:** baseline, MQO, Ridge e Lasso.
- **Métrica principal:** MAE em reais na CV de cinco dobras; menor é melhor.
- **Complementar:** RMSE em log. O critério interno de MQO/Ridge/Lasso é quadrático no alvo em log, distinto da métrica usada para selecionar a alternativa.
- **Partição:** 80/20 com semente 11900; amostra de exatamente 2.000 do treino; mesmas dobras para todos os modelos.
- **Teste:** a rodada original congelou a escolha antes da avaliação. A versão atual confere contagens, vencedor e alphas registrados antes de reproduzir o teste. Nenhuma troca de vencedor pelo resultado no teste.
- **Referência descritiva por ocupação/município:** completada na etapa 5 como comparação exploratória posterior, somente no treino/CV. Mediana CBO4+município, com recuos por suporte mínimo. Não foi avaliada como novo concorrente no teste; o ganho final reportado no teste continua sendo frente à constante.

## 4. Atributos disponíveis e usos vedados

Perfil: idade e escolaridade. Cenário: jornada, CBO4, município do estabelecimento, tipo de vínculo, categoria do trabalhador e subsetor. Consulte o registro de atributos para a justificativa e o contrato de disponibilidade.

Sexo e raça/cor ficam fora dos preditores. Sexo e escolaridade são usados na análise de erro por grupo. Tempo no emprego fica fora por se referir a um vínculo já realizado. Qualquer remuneração, derivado salarial, partição e identificadores de posição são proibidos em X.

O produto não seleciona vagas reais, não estima chance de contratação e não identifica o efeito causal de mudar de ocupação/localidade. Não usar a previsão como garantia de remuneração nem como critério automatizado de contratação.

## 5. Resultado e limites

Ridge foi escolhido por CV. A avaliação completa está em `03_registro_modelagem_avaliacao.md` e no notebook executado. Houve ganho sobre a constante, com erro ainda expressivo e subestimação média. O resultado apoia investigação de cenários; não sustenta uma promessa salarial individual.

Cobertura restrita ao trabalho formal, ao Norte e a remuneração positiva observada; categorias raras/inéditas; códigos atuais parcialmente documentados; ausência de validação temporal/geográfica independente e de intervalos individuais calibrados. Sem identificador de pessoa não foi possível garantir separação de pessoas com múltiplos vínculos entre treino/teste.

O material do professor é a referência metodológica principal. Os leiautes oficiais históricos foram preservados em `documentacao/layouts/`, com origem e limitações registradas. Não foi encontrado um leiaute específico das 62 colunas atuais.

## 6. Aprendizagem e entrega acadêmica

As seções 0–7 do notebook seguem o enunciado; as explicações necessárias estão no próprio arquivo. A semente continua sendo 011900. Foi criado um ambiente virtual, as dependências foram instaladas e o notebook completo foi executado novamente em 08/11/2026.
