# ROADMAP — Texto-para-SQL em PT-BR

> Para iniciante em projetos grandes: cada fase tem **objetivo, o que aprender, entregável, critério de pronto, prompts para o Claude Code e materiais**.
> Ritmo considerado: cerca de 10 a 15 h por semana. Total estimado: **10 a 14 semanas**. Os prazos são de referência, não metas rígidas.
> Coloque este arquivo em `docs/ROADMAP.md` dentro do repositório.

---

## Visão geral

| Fase | O quê | Tempo | Já dá para publicar? |
|---|---|---|---|
| 0 | Setup (Git, Python, Claude Code, contas) | 3 a 5 dias | — |
| 1 | Banco sintético de marketing | 1 semana | — |
| 2 | Dataset de avaliação + guia de anotação | 2 semanas | parcial |
| 3 | Harness de avaliação + estatística | 2 semanas | ✅ **MARCO 1** |
| 4 | Dados de treino (SFT + pares de preferência) | 1 a 2 semanas | — |
| 5 | Fine-tuning (SFT → DPO) no Colab | 2 a 3 semanas | ✅ **MARCO 2** |
| 6 | Publicação e divulgação | 1 semana | ✅ final |

**Regra de ouro:** termine o Marco 1 antes de estudar fine-tuning a fundo. Um projeto pela metade bem documentado vale mais que um projeto completo que nunca foi publicado.

---

## Fase 0 — Setup (3 a 5 dias)

**Objetivo:** ter o ambiente funcionando e o primeiro commit no GitHub.

**O que aprender**
- Git básico: `init`, `add`, `commit`, `push`, branches.
- Ambiente Python com `uv` (funciona bem no Windows).
- Claude Code: CLAUDE.md, modo de planejamento (Shift+Tab) e modos de permissão.

**Tarefas**
1. Criar o repositório no GitHub (sugestão de nome: `sql-ptbr-marketing`).
2. Instalar `uv`, criar o projeto, adicionar `duckdb`, `pandas`, `pytest` e `ruff`.
3. Copiar `CLAUDE.md` para a raiz e este arquivo para `docs/`.
4. Criar `.gitignore` (incluindo `.env`, `*.duckdb` grandes e `__pycache__`).
5. Criar contas: Hugging Face e Google Colab.

**Pronto quando:** `uv run python -c "import duckdb; print('ok')"` funciona e o repositório está no GitHub.

**Prompt para o Claude Code**
> Leia o CLAUDE.md. Estou na Fase 0. Me guie passo a passo para configurar o projeto com uv no Windows: estrutura de pastas, pyproject, .gitignore e primeiro commit. Explique cada comando antes de eu rodar.

**Materiais**
- GitHub Skills (cursos interativos de Git e GitHub): https://skills.github.com
- Documentação do Claude Code: https://code.claude.com/docs
- Curso Claude Code in Action, módulos 1 e 2: https://anthropic.skilljar.com/claude-code-in-action

---

## Fase 1 — Banco sintético de marketing (1 semana)

**Objetivo:** um banco DuckDB realista, parecido com os dados que você via na Monks.

**Esquema sugerido** (ajuste com a sua experiência)
- `canais` (Google, Meta, TikTok)
- `campanhas` (id, canal, nome, objetivo, data_inicio, data_fim, orcamento)
- `anuncios` (id, campanha_id, formato, criativo)
- `metricas_diarias` (data, anuncio_id, impressoes, cliques, custo)
- `conversoes` (data, anuncio_id, tipo, quantidade, receita)

**O que aprender**
- Modelagem relacional (chaves, granularidade diária).
- Gerar dados com padrões realistas: sazonalidade, fim de semana, CTR diferente por canal e alguns valores sujos de propósito (nulos, campanha pausada).

**Entregáveis:** `src/generate_db.py` com seed fixa, `data/db/marketing.duckdb` e `docs/dicionario_dados.md`.

**Pronto quando:** você consegue responder 5 perguntas de negócio com SQL e os números fazem sentido (CTR entre 0,5% e 5%, CPA plausível etc.).

**Prompt**
> Fase 1. Quero desenhar o esquema do banco de marketing junto com você. Primeiro me pergunte sobre como os dados eram na prática (granularidade, métricas, problemas comuns). Depois proponha o esquema, e só então o gerador com seed fixa.

**Materiais**
- Documentação do DuckDB: https://duckdb.org/docs

---

## Fase 2 — Dataset de avaliação (2 semanas)

**Objetivo:** de 150 a 200 perguntas em PT-BR com SQL ouro. **Esta é a parte em que a sua experiência de anotador vira diferencial.**

**Estrutura de cada exemplo**
```json
{"id": "q001", "pergunta": "Qual canal teve o menor CPA em março?",
 "sql_ouro": "SELECT ...", "dificuldade": "media",
 "categorias": ["agregacao", "filtro_data"], "autor": "humano"}
```

**Distribuição sugerida**
- Dificuldade: cerca de 40% fácil, 40% média e 20% difícil.
- Categorias: agregação, join, filtro de datas, funções de janela (ranking, acumulado), métricas derivadas (CTR, CPA, ROAS), perguntas ambíguas e linguagem informal ("quanto gastei com o TikTok mês passado?").

**Processo**
1. Escrever o `docs/guia_anotacao.md` **antes** das perguntas. Ele define o que é uma pergunta válida, como tratar ambiguidade e o formato de datas e de arredondamento. Você já conhece guias desse tipo das plataformas.
2. Escrever de 60 a 80 perguntas **à mão**.
3. Gerar o restante com um LLM, com filtro: o SQL precisa executar, o resultado não pode ser vazio, e você revisa 100% das perguntas sintéticas que vão para o teste.
4. Marcar `autor: humano` ou `autor: sintetico`, para depois comparar os dois grupos.

**Pronto quando:** 150 ou mais exemplos, todos executam, o guia está escrito e há um script que valida o arquivo.

**Prompt**
> Fase 2. Vou escrever o guia de anotação primeiro. Revise meu rascunho como se você fosse o QA de uma plataforma de RLHF: aponte ambiguidades e casos não cobertos. Não escreva o guia por mim.

> Escrevi 20 perguntas com SQL ouro em data/eval/. Rode todas, mostre as que falham e critique a qualidade: perguntas ambíguas, SQL que dá a resposta certa por acaso, categorias desbalanceadas.

**Materiais**
- Artigo do BIRD (como um benchmark de texto-para-SQL é construído e avaliado): https://arxiv.org/abs/2305.03111
- BIRD Mini-Dev (exemplo de dataset pequeno e harness): https://github.com/oneconvergence/bird-bench

---

## Fase 3 — Harness de avaliação + estatística (2 semanas) → MARCO 1

**Objetivo:** um comando que avalia qualquer modelo no seu dataset e gera uma tabela com intervalo de confiança.

**Componentes**
1. `run_eval.py`: monta o prompt (pergunta + esquema), chama o modelo, extrai o SQL da resposta e salva as saídas brutas.
2. `compare.py`: executa o SQL gerado e o ouro, e compara **os resultados** (não o texto do SQL). Atenção a ordem das linhas, arredondamento de decimais e nomes de colunas. **Escreva testes para isso**, porque é a parte mais sujeita a erro.
3. `stats.py`: acurácia de execução com **intervalo de Wilson**, e comparação pareada entre modelos (bootstrap pareado ou teste de McNemar).
4. Análise de erros: categorize as falhas (SQL inválido, join errado, filtro de data errado, interpretação errada da pergunta) e mostre a taxa por categoria e por dificuldade.

**Modelos a comparar:** 1 modelo grande via API (custo baixo para cerca de 200 perguntas) e 1 ou 2 modelos abertos pequenos, que serão a base do fine-tuning.

**O que aprender**
- Erro padrão, intervalo de confiança e por que "71% contra 69%" muitas vezes é só ruído.
- Diferença entre comparar intervalos separados e fazer um teste pareado.

**Entregáveis:** `results/AAAA-MM-DD_<modelo>.json`, uma tabela comparativa e `docs/analise_erros.md`.

**Pronto quando:** você consegue dizer com números se o modelo A é melhor que o B, e explicar em que tipo de pergunta cada um erra.

**Marco 1:** atualize o README e faça o post nº 1 no LinkedIn: "Construí um benchmark de texto-para-SQL em português com dados de marketing. O que descobri sobre onde os modelos erram".

**Prompt**
> Fase 3. Antes de codar, me explique a diferença entre exact match e execution accuracy, e quais armadilhas existem ao comparar resultados de SQL. Depois vamos escrever primeiro os testes do compare.py.

> Me ensine o intervalo de Wilson usando os meus próprios resultados. Calcule à mão comigo para um modelo e depois implemente em stats.py.

**Materiais**
- Evan Miller (Anthropic), *Adding Error Bars to Evals* (a referência de estatística para avaliações): https://arxiv.org/abs/2411.00640
- Blog do Hamel Husain sobre evals (gratuito; o curso pago dele é opcional e caro): https://hamel.dev
- Inspect (framework de evals do UK AISI), para conhecer depois de fazer o seu à mão: https://inspect.aisi.org.uk

---

## Fase 4 — Dados de treino (1 a 2 semanas)

**Objetivo:** dados para treinar, **sem vazamento do teste**.

**Tarefas**
1. **SFT:** de 1.000 a 2.000 pares (pergunta, SQL) gerados de forma sintética a partir de templates e de um LLM, filtrados por execução.
2. **Verificação de vazamento:** checar similaridade entre perguntas de treino e de teste (n-gramas ou embeddings) e remover as muito parecidas.
3. **Pares de preferência para DPO:** para cada pergunta de treino, `chosen` = SQL correto e `rejected` = SQL que o modelo base gerou e que **falhou**. Esse é exatamente o formato de dado do RLHF que você já produz como anotador, agora gerado por você.
4. Amostra de revisão humana: você revisa cerca de 100 exemplos e mede a taxa de erro do gerador.

**Pronto quando:** `data/train/sft.jsonl`, `data/train/dpo.jsonl`, relatório de vazamento e taxa de qualidade da amostra revisada.

**Prompt**
> Fase 4. Me explique o formato de dados esperado pelo SFTTrainer e pelo DPOTrainer do TRL. Depois vamos montar o gerador com filtro por execução e o checador de vazamento. Quero entender por que vazamento invalida o projeto.

**Materiais**
- smol-course da Hugging Face, unidade de dados sintéticos: https://github.com/huggingface/smol-course

---

## Fase 5 — Fine-tuning (2 a 3 semanas) → MARCO 2

**Objetivo:** um modelo pequeno que melhora de forma mensurável no seu benchmark.

**Ordem**
1. **Estude antes de treinar:** faça as unidades de SFT e de DPO do smol-course. Leva cerca de 1 semana.
2. **SFT com LoRA** no Colab (GPU gratuita ou Colab Pro), usando Unsloth para caber na memória.
3. Avaliar com o **mesmo harness** da Fase 3.
4. **DPO** em cima do modelo SFT, com os pares da Fase 4. Avaliar de novo.
5. Tabela final: modelo base → SFT → SFT+DPO → modelo grande de referência, todos com intervalo de confiança e teste pareado.

**Cuidados**
- Comece com poucos dados e poucas épocas só para ver o pipeline funcionar de ponta a ponta. Depois aumente.
- Salve checkpoints no Google Drive ou no Hugging Face, porque o Colab desconecta.
- Se o DPO não melhorar, isso também é resultado. Investigue e documente o motivo, porque uma análise honesta de um resultado negativo também impressiona.

**Entregáveis:** notebooks em `notebooks/`, modelo publicado no Hugging Face com model card e tabela final em `results/`.

**Marco 2:** post nº 2: "Treinei um modelo pequeno para gerar SQL em português: de X% para Y%".

**Prompt**
> Fase 5. Explique LoRA e DPO usando o meu projeto como exemplo, sem fórmulas primeiro. Depois adapte o notebook de SFT do Unsloth para o meu dataset. Quero rodar uma versão mínima (100 exemplos, 1 época) antes da completa.

**Materiais**
- smol-course (SFT, LoRA, DPO, avaliação; feito para GPU modesta): https://github.com/huggingface/smol-course
- Documentação do TRL (SFTTrainer, DPOTrainer): https://huggingface.co/docs/trl
- Unsloth, guia de DPO com notebooks de Colab: https://unsloth.ai/docs/get-started/reinforcement-learning-rl-guide/preference-dpo-orpo-and-kto
- Artigo do DPO (leia a introdução e a seção de método): https://arxiv.org/abs/2305.18290

---

## Fase 6 — Publicação (1 semana)

**Checklist**
- [ ] README como case: problema → abordagem → resultados (tabela + gráfico) → aprendizados → limitações.
- [ ] Dataset no Hugging Face com dataset card (origem, processo de anotação, limitações): https://huggingface.co/docs/hub/datasets-cards
- [ ] Modelo no Hugging Face com model card.
- [ ] Post técnico (Medium, LinkedIn ou dev.to), em português **e** em inglês. O inglês amplia muito o alcance para vagas internacionais.
- [ ] Atualizar currículo e LinkedIn. Exemplo de linha: "Construí benchmark e pipeline de pós-treino (SFT+DPO) para texto-para-SQL em PT-BR; modelo de 1,5B foi de X% para Y% de acurácia de execução".
- [ ] Usar o projeto nas candidaturas para vagas de RLHF especialista (Mercor, Micro1, Outlier etc.) e de AI Evaluation, dados e ML.

---

## Papers acadêmicos
Lista completa, com ordem de leitura por fase: `docs/PAPERS.md`.

## Materiais de base (paralelos ao projeto)
- **Livro:** Chip Huyen, *AI Engineering* (O'Reilly, 2025). Os capítulos de avaliação e de fine-tuning são os mais úteis aqui.
- **Estatística:** a disciplina da UNIVESP ajuda. Revise intervalo de confiança e teste de hipótese.
- **Vídeos (YouTube):** buscar `smol course hugging face`, `DPO explained`, `LoRA explained`, `LLM evals Hamel Husain`.

## Se travar
1. Diminua o escopo: menos perguntas, um modelo só, menos categorias.
2. Volte para a última versão que funcionava (`git log`, `git checkout`).
3. Peça ao Claude Code: "estou travado em X; me explique o problema antes de propor solução".
4. Registre o problema e a solução em `docs/diario.md`. Isso vira material para o post e para a entrevista.
