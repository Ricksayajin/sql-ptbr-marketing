# CLAUDE.md — Projeto: Texto-para-SQL em PT-BR (dados de marketing)

## Quem sou eu
- Ricardo, ex-estagiário de análise de dados na Monks (campanhas Google, Meta e TikTok).
- Faço freelas de RLHF e avaliação de modelos há cerca de 7 meses (Appen, Labelbox, Label Studio, SRT da Meta).
- Estudo na UNIVESP. Uso Windows.
- **Este é o meu primeiro projeto desse tamanho.** Sei SQL e o básico de Python. Estou aprendendo avaliação automatizada, estatística para avaliações e fine-tuning.

## Objetivo do projeto
Mostrar que eu **construo o sistema** de RLHF e avaliação, e não só executo tarefas de avaliador.
Ciclo completo: **dataset → avaliação → treino → medição**.

1. Banco sintético de marketing (campanhas, anúncios, métricas diárias e conversões) em DuckDB.
2. Dataset de perguntas em PT-BR com o SQL correto ("SQL ouro"), com guia de anotação.
3. Harness de avaliação: roda modelos, executa o SQL gerado e compara resultados (execution accuracy), com intervalo de confiança e análise de erros.
4. Dados de treino: exemplos para SFT e pares de preferência para DPO.
5. Fine-tuning de um modelo aberto pequeno (1 a 3 bilhões de parâmetros) com LoRA no Colab: primeiro SFT, depois DPO.
6. Publicação: GitHub, Hugging Face (dataset e modelo) e um post técnico.

Roteiro detalhado, fases, materiais e prompts: `docs/ROADMAP.md`.
Papers de base por fase: `docs/PAPERS.md`.
Fase atual: **Fase 0 (setup)**. Atualize esta linha a cada fase concluída.

## Como trabalhar comigo
- **Modo professor:** antes de implementar algo novo, explique em 3 a 5 linhas o que vai fazer e por quê. Depois implemente em passos pequenos.
- Mostre o plano antes de qualquer mudança que crie mais de 2 arquivos ou mais de 100 linhas.
- Quando aparecer um conceito novo (LoRA, DPO, erro padrão etc.), explique com um exemplo concreto do próprio projeto.
- Deixe para mim as partes em que eu aprendo mais: escrever as perguntas e o SQL ouro manualmente e interpretar os resultados. Sugira, revise e aponte erros, mas não faça por mim.
- Responda em português. Nomes de código (variáveis, funções, arquivos) em inglês.
- Se eu pedir algo que vá contra as regras abaixo, avise antes de fazer.

## Regras invioláveis
- **O conjunto de teste nunca entra no treino.** Nenhum exemplo do teste (nem parecido, nem reescrito) pode ir para os dados de SFT ou DPO. Verifique se há vazamento sempre que gerar dados.
- **Nada de inventar resultados.** Todo número em README, relatório ou post precisa vir de um arquivo em `results/` gerado por script.
- Toda métrica é reportada com intervalo de confiança: Wilson para proporções, ou bootstrap.
- Seeds fixas em tudo que for aleatório (geração de dados, splits, treino).
- Exemplos gerados por LLM só entram no dataset se o SQL executar sem erro e eu tiver revisado uma amostra.
- Nunca faça commit de chaves de API. Elas ficam no `.env`, que está no `.gitignore`.

## Stack
- Python 3.11+, ambiente com `uv`.
- DuckDB (banco local, arquivo único) e pandas.
- Avaliação: scripts próprios (o objetivo é eu entender o processo). Talvez `inspect-ai` mais tarde.
- Treino: Google Colab com `transformers`, `trl`, `peft` e Unsloth.
- Testes: `pytest` (testes pequenos, principalmente da comparação de resultados SQL).
- Lint: `ruff`.

## Estrutura do repositório (alvo)
```
data/
  db/            # banco DuckDB sintético (gerado por generate_db, fora do Git)
  eval/          # dataset de teste (perguntas + SQL ouro), versionado
  train/         # dados de SFT e pares de DPO
docs/
  ROADMAP.md
  guia_anotacao.md
  dicionario_dados.md
src/
  sql_ptbr_marketing/   # pacote Python (layout criado pelo uv init)
    __init__.py
    generate_db.py
    run_eval.py
    compare.py          # execução + comparação de resultados
    stats.py            # intervalos de confiança
notebooks/       # Colab de SFT e DPO
results/         # saídas das avaliações (JSON/CSV com data no nome)
tests/           # importam o código com `from sql_ptbr_marketing.<módulo> import ...`
```

## Comandos
- Instalar: `uv sync`
- Gerar banco: `uv run python -m sql_ptbr_marketing.generate_db`
- Avaliar: `uv run python -m sql_ptbr_marketing.run_eval --model <nome>`
- Testes: `uv run pytest`
(Atualizar conforme os scripts forem criados.)

## Contexto da conversa que originou o projeto
- Eu me sentia limitado no uso do Claude e queria projetos de RLHF que me destacassem além do papel de avaliador.
- Decidimos por texto-para-SQL em PT-BR porque junta o que eu já tenho (SQL, dados de marketing, experiência em RLHF) com o que o mercado valoriza (avaliação automatizada, estatística e pós-treino).
- Uso duplo: portfólio para vagas (AI Evaluation, dados para pós-treino, ML) e credencial para vagas de RLHF especialista, que pagam mais.
- Estratégia: entregas em fases que já valem sozinhas. A Fase 3 (dataset + avaliação) já é publicável antes do fine-tuning.
