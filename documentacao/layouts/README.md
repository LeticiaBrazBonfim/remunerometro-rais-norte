# Documentação oficial da RAIS — inventário

Documentação reunida em **26/10/2026** para tentar resolver a pendência do leiaute das 62 colunas. Este README registra o que cada arquivo **efetivamente cobre** e o que ele **não** cobre.

> **Atualização de 06/11/2026:** foram obtidos dois leiautes de microdados diretamente do FTP oficial do MTE e adicionados a esta pasta. A pesquisa foi reaberta com autorização. O registro de 26/10 foi preservado como histórico; a situação atual está descrita na seção abaixo.

## Inventário

| Arquivo | Origem declarada | Conteúdo | SHA-256 | Tamanho |
|---|---|---|---|---:|
| `Apresentação - RAIS 2025.pdf` | MTE — deck "RAIS, Ano-base 2025" | 38 páginas: guia da divulgação dos dados (apresentação, sumário executivo, tabelas, microdados, painel), cronograma eSocial e resultados do ano-base | `4f72f5489038fec3c1eb3532836d3b182c0934f0c19b21d7794c6f830bbced0d` | 1.262.908 |
| `Apresentação do PowerPoint.pdf` | (sem identificação) | **Mesmo texto** do arquivo anterior — a extração das 38 páginas é idêntica byte a byte; os bytes do PDF diferem (provável exportação duplicada do mesmo deck) | `fe9ba425ebd45b2a8615cc22bc104d2f156ed1138097d7e5d67d94247ed254a7` | 1.007.439 |
| `Manual de Orientação.pdf` | MTE, Brasília, 2026 (CIP: "Manual de Orientação da RAIS: anos-anteriores", 47 p.) | 46 páginas: instruções de preenchimento e **protocolo de entrega da declaração** da RAIS (CREA, CNPJ, quantidades transmitidas) | `33858aee5681707a19aa6895b075a9df2d9c97c1d3b2b8b45db269248fdf0c77` | 740.369 |
| `LayoutGenerico1976-2022.pdf` | MTE | 4 páginas: "**LAYOUT PARA GERAÇÃO DO ARQUIVO DE DECLARAÇÃO GDRAIS** — Genérico 1976-2022", em largura fixa por posição (`001 a 006`, `007 a 020`...) do arquivo de declaração | `87cc24ece0a07df2cc8c71d41fa81b69355d71636325a5cc1933df41846b0bf2` | 87.884 |
| `LAYOUT_BASE_HARMONIZADA.xlsx` | Portal gov.br/MJ (Obmigra) | 10 abas com **esquema harmonizado** (`cod_uf`, `cod_municipio`, `val_salario_med`...), não as 62 colunas da RAIS | `6eb5a97b193d680b8993bcf1e282080f403c21a5502e18576fbdf00e25898f58` | 359.336 |
| [RAIS_vinculos_layout.xls](RAIS_vinculos_layout.xls) | FTP oficial do MTE/PDET, pasta `RAIS/Layouts/vínculos/`; obtido em 06/11/2026 | Leiaute histórico sem ano no nome: 13 abas; 45 campos na aba `RAISD - layout` | `e0063ea3651d58a8be7f083839576a54eb8d4b492e23912c80b8c0e4db546e08` | 1.031.680 |
| [RAIS_vinculos_layout2020.xls](RAIS_vinculos_layout2020.xls) | FTP oficial do MTE/PDET, pasta `RAIS/Layouts/vínculos/`; obtido em 06/11/2026 | Leiaute de 2020: 13 abas; 60 campos na aba `RAISD - layout`, com descrições, categorias e tabelas auxiliares | `6fe18ceaf8cc3ce6c87046876711a839a00b5293259cd46eaa5ea595f3e10c49` | 1.055.744 |

## Situação atual — verificação de 06/11/2026

### Origem e preservação dos leiautes

- A [página de microdados do MTE](https://www.gov.br/trabalho-e-emprego/pt-br/assuntos/estatisticas-trabalho/microdados-rais-e-caged) indica o acesso ao servidor FTP do PDET.
- Os dois arquivos foram baixados de `ftp://ftp.mtps.gov.br/pdet/microdados/RAIS/Layouts/vínculos/`. Essa pasta é separada das pastas anuais dos microdados.
- A pasta consultada listava os leiautes sem ano, 1985–1993, 2015, 2016, 2017, 2018–2019 e 2020. **Não foi encontrado nessa pasta um leiaute específico de 2025.**
- Os arquivos foram copiados integralmente para `documentacao/layouts/`. Os tamanhos e hashes SHA-256 foram conferidos na origem local do download e nas cópias do projeto, sem diferenças. Esses hashes foram calculados localmente para rastreabilidade; não são checksums publicados pelo MTE.

### O que a fonte histórica esclarece

Na aba `RAISD - layout` de `RAIS_vinculos_layout2020.xls`:

| Campo histórico | Definição registrada | Linha da planilha |
|---|---|---:|
| `MUN TRAB` | Município onde o empregado trabalha ou presta serviço | 106 |
| `MUNICIPIO` | Município de localização do estabelecimento | 107 |
| `REM DEZ (R$)` | Remuneração do trabalhador em dezembro, em valor nominal | 245 |
| `REM MED (R$)` | Remuneração média do trabalhador, em valor nominal | 247 |
| `TEMP EMPR` | Tempo de emprego em meses | 264 |

Na revisão de 06/11, a correspondência usada foi explicitada: `MUNICIPIO` → `municipio_codigo`, `MUN TRAB` → `municipio_trab_codigo`, `REM DEZ (R$)` → `vl_rem_dezembro_nom`, `REM MED (R$)` → `vl_rem_media_nom`. Rótulos e tipos atuais foram conferidos no cabeçalho/Parquet; ambos os alvos foram auditados em todos os vínculos. É uma correspondência semântica apoiada no leiaute histórico, não a confirmação de um esquema oficial específico de 2025. O material do professor continua sendo a base principal da metodologia; os leiautes apoiam a interpretação das variáveis.

O modelo vigente usa a média mensal nominal positiva, município do estabelecimento e base Norte. A auditoria necessária está no notebook de `entrega/`; regras e limites, nos registros 01 a 03 da raiz. Os antigos arquivos auxiliares de resultados foram removidos na limpeza final.

### Limites que permanecem

1. **Formato atual:** o cabeçalho de `dados/RAIS_VINC_PUB_NORTE.COMT.gz` foi conferido e tem 62 colunas, delimitadas por vírgula. O leiaute de 2020 descreve 60 campos e menciona ponto e vírgula. Os nomes também diferem; não usar a planilha como esquema literal de leitura do arquivo atual.
2. **Colunas adicionais:** `Ind Vínculo Abandonado - Código` e `Categoria Trabalhador - Código`, presentes no arquivo local, não estão descritas no leiaute de 2020.
3. **Código `999999`:** aparece no leiaute histórico como exemplo do formato de município. As linhas 108 e 109 registram `IGNORADO <UF>` / `<UF>9999` e `IGNORADO` / `-1`. Isso, isoladamente, não confirma o significado de `999999` no arquivo atual. A planilha harmonizada é outra fonte e usa outro esquema; sua regra não deve ser transferida automaticamente.
4. **Sufixo `PUB`:** não foi localizada uma definição literal nos materiais consultados. Não interpretar o sufixo como filtro de setor público sem evidência.
5. **Vigência dos códigos:** tabelas históricas de municípios, ocupações e outras classificações não comprovam que todos os códigos e domínios permaneceram iguais em 2025. Conferir a cobertura na auditoria da base antes de recodificar ou excluir registros.

## O que foi verificado em 26/10/2026

Extração de texto dos quatro PDFs e comparação com o cabeçalho real do arquivo (`RAIS_VINC_PUB_NORTE.COMT.gz`, 62 rótulos):

- **Nenhum dos 62 rótulos aparece em nenhum dos documentos.** A verificação foi feita procurando cada rótulo literal (`Vl Rem Média Nom`, `Município - Código`, `Escolaridade Após 2005 - Código`, `CBO 2002 Ocupação - Código`, ...) no texto extraído dos quatro PDFs: **0 ocorrências**.
- O deck trata da **divulgação** dos dados, não do dicionário de variáveis. Ele aponta a página de microdados como fonte das variáveis: *"Microdados: microdados anuais públicos, contendo todas as variáveis disponíveis em arquivo formato .txt"* — Material em `https://www.gov.br/trabalho-e-emprego/pt-br/assuntos/estatisticas-trabalho`.
- O `LayoutGenerico1976-2022.pdf` é o layout do **arquivo de declaração** (registro fixo, por posição de byte), que é outro objeto: nosso arquivo é o microdados público em CSV.
- O `Manual de Orientação.pdf` é manual de **preenchimento da declaração**; os `999999` que aparecem nele são **máscaras de formulário** (ex.: contadores `9.999`), não o código `999999` do nosso arquivo.
- Os dois PDFs de apresentação têm **texto idêntico** e bytes diferentes. Mantidos os dois por precaução; com uma confirmação futura, um deles pode ser removido.

## Pendências registradas em 26/10/2026 — histórico

O registro abaixo corresponde ao material disponível naquela data. A pesquisa de 06/11/2026 resolveu parcialmente essas lacunas, conforme a seção de situação atual. As referências a `docs/...` pertencem à organização anterior do projeto.

1. **Leiaute das 62 colunas: não obtido.** Nenhum material em `docs/layouts/` (nem os slides em `docs/aulas/`) traz rótulos, tipos, domínios ou unidades das variáveis.
2. **Semântica de `Município - Código` / `Município Trab - Código` e significado de `999999`: não obtida** (D-f em `docs/decisoes_metodologicas.md`).
3. **Universo `PUB`: não obtido** — nenhum dos arquivos explica o sufixo.
4. **Decisão registrada em 26/10/2026: não perseguir mais essa informação por enquanto.** O que os documentos cobrem está resumido acima; o que falta segue marcado como descrição medida, não oficial.

## Pontos de busca registrados em 26/10/2026 — histórico

Caso volte a ser necessário, os pontos de partida mais prováveis — todos citados nos próprios documentos deste repositório — são, em ordem de prioridade:

1. **Página de microdados do MTE:** `https://www.gov.br/trabalho-e-emprego/pt-br/assuntos/estatisticas-trabalho`. É a fonte indicada **no próprio deck** (`Apresentação - RAIS 2025.pdf`, item "4. Microdados": *"microdados anuais públicos, contendo todas as variáveis disponíveis em arquivo formato .txt"*). Se houver dicionário/leiaute junto da publicação dos microdados, é ali que ele deve estar.
2. **PDET — Plano Diretor de Estatísticas e Trabalho:** `pdet.mte.gov.br`, endereço citado na capa do mesmo deck (`Apresentação - RAIS 2025.pdf`, página 2). É o portal técnico do MTE, naturalmente o lugar onde documentação de variáveis é publicada.
3. **Pacote de microdados no FTP:** `ftp://ftp.mtps.gov.br/pdet/microdados/RAIS/2025/` — verificado em 25/10/2026, contém **apenas os oito `.7z` regionais**, sem arquivo de leiaute, dicionário ou checksum. Verificar de novo só se o diretório mudar.
4. **Painel de informações da RAIS** (item 5 do mesmo deck) — consulta online de dados; pode exibir rótulos das variáveis.
5. **Direto com o professor** (coordenação da disciplina) — a fonte mais curta, e a mesma por onde vieram os slides e os laboratórios em `docs/aulas/`.

Na verificação de 26/10/2026, esses pontos não foram abertos, conforme a decisão de adiar a busca naquela data. Essa decisão foi revista em 06/11/2026; as fontes efetivamente consultadas e os arquivos obtidos estão registrados na seção de situação atual.
