# Remunerômetro — RAIS Norte

Este é um **trabalho acadêmico individual** de Letícia Braz Bonfim sobre aprendizado de máquina supervisionado. O enunciado, os slides e os laboratórios do professor estão em `documentacao/` e orientam o projeto.

## Objetivo

Estimar, em reais, a **remuneração média mensal nominal** de um vínculo formal registrado na RAIS 2025 da região Norte. A previsão pode servir como referência para comparar o componente salarial de cenários de trabalho descritos por características da pessoa e da vaga. Ela não representa uma oferta real, não calcula todos os custos e benefícios de uma vaga e não garante um salário individual.

O estudo considera os vínculos com remuneração média mensal positiva. Cada linha da RAIS corresponde a um vínculo; uma pessoa pode ter mais de um. O recorte Norte corresponde à base local utilizada neste trabalho.

## Como o estudo foi feito

O notebook converte o arquivo original `.gz` para Parquet quando necessário, examina os dados com Pandas e lê a base em blocos para caber em 8 GB de RAM. Dos **5.645.037** vínculos da base, **4.893.994** atenderam à regra de remuneração positiva.

Primeiro, 20% dos vínculos elegíveis foram reservados para o teste. Dos 80% restantes, foram sorteados os **2.000 vínculos** exigidos no enunciado. Nessa amostra, o trabalho compara uma previsão constante (*baseline*), MQO, Ridge e Lasso por validação cruzada de cinco dobras. A escolha usa o menor **MAE em reais**, a média do tamanho dos erros das previsões.

O Ridge teve o menor MAE na validação cruzada (**R$ 1.369,17**). Após essa escolha, seu MAE no teste reservado de **978.799 vínculos** foi **R$ 1.374,79**. O notebook também apresenta gráficos de sobreajuste, métricas complementares e erros por escolaridade e sexo. Esses números descrevem a base estudada; não demonstram causa das diferenças entre grupos nem desempenho em outro ano ou região.

## Arquivos

| Caminho | Conteúdo |
|---|---|
| `entrega/REM_20240011900_Leticia.ipynb` | Notebook executado, com exploração, preparo, modelos, avaliação e conclusões |
| `entrega/REM_20240011900_Leticia.html` | Relatório estático com as saídas do notebook |
| `documentacao/` | Enunciado, materiais de aula e leiautes consultados |
| `01_ficha_formulacao.md` a `03_registro_modelagem_avaliacao.md` | Formulação, atributos e resultados do estudo |
| `dados/` | Base RAIS e Parquet locais; os microdados não são versionados |

Os **dois arquivos de `entrega/`** formam a entrega acadêmica. O enunciado orienta a não enviar a base de dados. O repositório GitHub é [LeticiaBrazBonfim/remunerometro-rais-norte](https://github.com/LeticiaBrazBonfim/remunerometro-rais-norte).

## Reproduzir

1. Crie o ambiente: `py -3.14 -m venv .venv`.
2. Instale as dependências: `.\.venv\Scripts\python.exe -m pip install -r requirements.txt`.
3. Coloque `RAIS_VINC_PUB_NORTE.COMT.gz` na pasta `dados/`.
4. Abra o notebook de `entrega/` no VS Code, selecione o Python da `.venv` e execute todas as células, de cima para baixo.

O Parquet será criado na primeira execução se ainda não existir. O HTML mostra uma execução salva; alterações no notebook exigem nova exportação para atualizar o relatório. A declaração de uso de IA está no próprio notebook.
