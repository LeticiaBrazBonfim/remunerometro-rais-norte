# Registro de atributos e codificação — Remunerômetro

Decisão sobre os atributos em 06/11/2026: os mesmos oito preditores foram escolhidos antes do teste. Em 08/11/2026, o código do preparo ficou mais simples: não há preenchimento de ausências porque a auditoria da base completa não encontrou NaN nessas colunas. Escala e one-hot continuam dentro do Pipeline e são aprendidos em cada dobra. A execução confirmou as mesmas 209 colunas e os mesmos resultados. A tabela de nulos agora aparece também no notebook de entrega para os 11 campos lidos na amostra de treino (oito preditores originais, alvo, sexo e raça/cor); nessa amostra todos têm zero nulos. A tabela da amostra não substitui a auditoria histórica da base completa.

| Atributo | Tipo e disponibilidade na consulta | Associação esperada, sem interpretação causal | Codificação no Pipeline | Risco e decisão |
|---|---|---|---|---|
| `idade` | Numérico; perfil informado | Experiência pode acompanhar remuneração, mas a relação pode ser não linear | StandardScaler | Manter linear nesta versão; não acrescentar idade² após olhar o teste. Idade zero não foi recodificada sem fundamento. |
| `qtd_hora_contr` | Numérico; jornada pretendida informada | Maior jornada pode acompanhar maior remuneração mensal | StandardScaler | Manter. Zero/99 são questões semânticas registradas; não tratados automaticamente como ausentes. |
| `escolaridade_apos_2005_codigo` | Categórico; perfil informado | Formação pode se associar à remuneração; não impor distância entre códigos | One-hot | Manter e avaliar erro por grupo; associação não é retorno causal da educação. |
| `cbo_4_digitos` | Categórico; ocupação pretendida informada | Ocupações diferem em qualificação e remuneração | Prefixo de 4 dígitos do código de 6 posições; one-hot | Manter CBO4 para reduzir fragmentação. Não é ocupação individual de seis dígitos. |
| `municipio_codigo` | Categórico; município pretendido do estabelecimento | Mercado local e composição produtiva podem diferir | Código como categoria; one-hot | Manter município do estabelecimento. Pode diferir do lugar efetivo de trabalho. |
| `tipo_vinculo_codigo` | Categórico; natureza do cenário informada | Formas de vínculo podem se associar a estruturas salariais distintas | Código como categoria; one-hot | Manter sem supor rótulos atuais não confirmados. Não é informação extraída de contratação futura. |
| `categoria_trabalhador_codigo` | Categórico; categoria do cenário informada | Classificação pode distinguir regimes/arranjos do vínculo | Código como categoria; one-hot | Manter como já previsto. Sobreposição com tipo de vínculo pode gerar colinearidade; regularização reduz instabilidade, sem garantir interpretação causal. |
| `ibge_subsetor_codigo` | Categórico; subsetor do cenário informado | Estruturas produtivas e salários associados diferem | Código como categoria; one-hot | Manter. Sem informação do cenário, a consulta fica incompleta; não inferir do alvo. |

O encoder usa `min_frequency=5`, `handle_unknown="ignore"` e matriz esparsa. Escala e categorias são aprendidas dentro de cada dobra da CV; não são ajustadas no teste. Os atributos selecionados não têm NaN na base auditada; códigos especiais não são automaticamente sinônimos de NaN.

## Transformações e dimensionalidade

- CBO de 2/4/6 dígitos na amostra: 45/271/455 categorias; vínculos em categorias com n<5: 0,95% / 16,30% / 28,05%.
- A representação CBO4 foi mantida por equilibrar detalhamento e fragmentação, conforme a análise anterior do treino.
- Idade linear foi mantida como especificação inicial parcimoniosa e coerente com a preparação existente. Uma relação quadrática é plausível, mas não foi comparada nesta rodada; não se afirma que a forma linear tenha vencido uma busca contra idade². Jornada tem associação positiva esperada com remuneração mensal, sem impor esse sinal ao ajuste. Para códigos categóricos não há um único sinal numérico global: cada contraste depende da categoria e do contexto.
- Ajuste final nos 2.000 vínculos: **209 colunas**, p/n = **0,1045**.
- Nas dobras: p = 187, 183, 175, 182, 177. É esperado variar entre dobras; dentro de cada dobra treino/validação têm a mesma dimensão.
- One-hot completo e intercepto podem produzir dependências lineares. MQO usa solução numérica para predição; seus coeficientes não devem ser lidos como efeitos identificados de cada código.

## Candidatas excluídas e relógio da decisão

| Campo | Momento/uso | Decisão e justificativa |
|---|---|---|
| `vl_rem_media_nom`, `log_rem` | Resultado histórico a prever | Proibidos em X: alvo e transformação. |
| Demais `vl_rem_*`, `rem_*`, salários e derivados | Remuneração realizada | Proibidos em X: revelam informação do resultado. O notebook escolhe explicitamente as oito colunas de X, sem remuneração. |
| `sexo_codigo` | Característica pessoal; conhecida na consulta | Fora de X; preservado para avaliação por grupo. Ausência em X não garante equidade. |
| `raca_cor_codigo` | Característica pessoal; conhecida na consulta | Fora de X; distribuição de treino auditada. Não se alegou avaliação de equidade completa por raça nesta rodada. |
| `tempo_emprego_meses` | Duração do vínculo já realizado | Excluído: não está definido na consulta de uma oportunidade pretendida. |
| `municipio_trab_codigo` | Local de prestação do serviço | Excluído: 999999 em 5.625.908 linhas (99,66%), com semântica atual incompleta; não substitui o município do estabelecimento. |
| Natureza jurídica e tamanho do estabelecimento | Características do cenário que poderiam existir | Não adicionados: preservada a especificação anterior, sem ampliar atributos após o teste. |
| Indicadores de ativo/desligamento/abandono | Estado posterior ou administrativo do vínculo | Não são preditores nem filtros extras; não transferir a situação histórica para uma contratação futura. |
| Partição, `_posicao_fonte`, `_posicao_elegivel` | Controle de processamento | Proibidos em X; apenas rastreabilidade/partição. |

## Auditoria e pendências semânticas

Foram observadas 27 idades zero, 2.392 jornadas zero e 73.464 jornadas 99 na base completa. Não se concluiu que sejam erros. Permaneceram como registrados. O significado de todos os códigos atuais, o sufixo PUB e as duas colunas extras em relação ao leiaute de 2020 continuam sem definição integral confirmada.

As tabelas históricas de códigos não foram tomadas como prova de vigência atual. O erro por escolaridade/sexo é apresentado com os códigos efetivamente observados. O modelo precisa receber as características do cenário; não há interface de consulta implementada nem garantia de cobertura de qualquer combinação informada.

## Análise posterior — sobreposição entre tipo de vínculo e categoria

Análise realizada em 08/11/2026, depois do congelamento e da avaliação final. Não alterou o notebook, os atributos da rodada, o vencedor ou o teste.

O [Manual da RAIS 2024](https://www.rais.gov.br/sitio/rais_ftp/ManualRAIS2024.pdf) trata os campos separadamente, mas informa que `categoria` foi incluída para padronizar RAIS e eSocial e que seu preenchimento era opcional na declaração RAIS. O tipo de vínculo já descreve a relação de emprego. A [Tabela 01 do eSocial](https://www.gov.br/esocial/pt-br/documentacao-tecnica/leiautes-esocial-v-s-1-3-cons-nt-05-2025/tabelas.html) confirma categorias como empregado geral, aprendiz, temporário, agente público, avulso e contribuinte individual. Portanto, existe sobreposição conceitual real; categoria não deve ser apresentada como informação independente do tipo de vínculo.

Na população elegível de 4.893.994 vínculos, foram observados 20 códigos de tipo, 18 de categoria e somente 39 pares, entre 360 combinações possíveis. Usando para cada tipo a categoria mais frequente, a categoria é antecipada corretamente em **98,57%** dos vínculos. No sentido inverso, a melhor regra por categoria acerta o tipo em **82,83%**. A associação é forte (V de Cramér **0,688**; informação mútua normalizada **0,778**), mas não perfeita. Exemplos exatos na base: tipo 55 com categoria 103 em 65.201 vínculos; tipo 50 com categoria 106 em 41.373; tipo 90 com categoria 105 em 20.682. O tipo preserva distinções que uma mesma categoria agrega.

Foi feita uma ablação exploratória somente nos mesmos 2.000 vínculos de treino e nas mesmas cinco dobras. Cada variante reajustou o alpha do Ridge pela grade original, sem abrir ou reutilizar o teste:

| Preditores de vínculo | MAE CV em R$ | Diferença para ambos |
|---|---:|---:|
| Tipo + categoria | 1.369,17 | — |
| Sem categoria | 1.374,81 | +5,64 (+0,41%) |
| Sem tipo | 1.391,78 | +22,61 (+1,65%) |
| Sem ambos | 1.473,96 | +104,79 (+7,65%) |

Retirar categoria piorou o MAE nas cinco dobras, entre R$ 3,70 e R$ 7,73, mas o ganho de mantê-la é pequeno. O resultado indica que tipo de vínculo contém a maior parte da informação compartilhada e categoria acrescenta um sinal preditivo residual.

A literatura consultada não estabelece que as duas classificações devam ser usadas juntas. O [TCC da UFPR com CAGED e predição salarial](https://acervodigital.ufpr.br/xmlui/handle/1884/85738) usa ocupação, escolaridade, idade, localização, atividade econômica e porte, destacando CBO, escolaridade e idade; não combina duas codificações jurídicas quase equivalentes. O estudo publicado na *Research Policy* com [RAIS e salários](https://doi.org/10.1016/j.respol.2022.104645) usa tipo de contrato, ocupação, setor/região e características do trabalhador e da firma; seu período 2014–2017 antecede a incorporação da categoria do eSocial à declaração RAIS. Metodologicamente, o artigo original do [Ridge de Hoerl e Kennard](https://doi.org/10.1080/00401706.1970.10488634) justifica regularização para preditores não ortogonais, mas isso não torna coeficientes redundantes interpretáveis como efeitos separados.

**Status:** evidência registrada; a decisão acadêmica final permanece pendente. Para a rodada congelada, manter ambos preserva o protocolo e o menor MAE de CV observado. Em uma futura versão orientada à simplicidade de uso, remover categoria e pedir apenas tipo de vínculo é uma alternativa defensável pelo custo preditivo pequeno, mas exigiria um novo protocolo definido antes de qualquer novo teste.

## Complemento didático da etapa 5

O notebook traz uma tabela curta com os oito atributos; esta página guarda os motivos e riscos em detalhe. Uma versão ampliada descreveu coeficientes, mas esse apêndice não integra a apresentação final e o arquivo foi removido durante a limpeza do repositório. Não foi alterada a seleção dos oito atributos. A diferença entre código categórico, one-hot, coeficiente e efeito causal pode ser estudada com o notebook e as aulas do professor.
