# Papers de base — Texto-para-SQL em PT-BR

> Coloque em `docs/PAPERS.md`. Não precisa ler tudo de uma vez: leia cada paper na fase indicada.
> **Como ler um paper (iniciante):** resumo → introdução → figuras e tabelas → conclusão. Leia a seção de método só se precisar. Peça ao Claude: "explique este paper usando o meu projeto como exemplo".
> Prioridade: ⭐⭐⭐ essencial · ⭐⭐ importante · ⭐ complementar

---

## 1. Texto-para-SQL (Fases 2 e 3)

| ⭐ | Paper | Por que ler | Link |
|---|---|---|---|
| ⭐⭐⭐ | **BIRD**: Li et al., 2023 (NeurIPS). *Can LLM Already Serve as A Database Interface? A BIg Bench for Large-Scale Database Grounded Text-to-SQLs* | Define a **execution accuracy**, a métrica central do seu harness. Mostra que dados sujos e conhecimento de negócio derrubam os modelos, que é a mesma ideia do seu banco de marketing. | https://arxiv.org/abs/2305.03111 |
| ⭐⭐⭐ | **mRAT-SQL+GAP**: José & Cozman, 2021 (BRACIS). *A Portuguese Text-to-SQL Transformer* | **Trabalho brasileiro (C4AI/USP) sobre texto-para-SQL em português.** É o seu trabalho relacionado direto: eles traduziram o Spider para PT. Você vai mais longe com perguntas nativas, domínio de negócio e LLMs atuais. Cite no README. | https://arxiv.org/abs/2110.03546 |
| ⭐⭐ | **Spider**: Yu et al., 2018 (EMNLP). *A Large-Scale Human-Labeled Dataset for Complex and Cross-Domain Semantic Parsing and Text-to-SQL Task* | O benchmark clássico. Bom para ver como categorizar dificuldade (fácil, média, difícil, extra). | https://arxiv.org/abs/1809.08887 |
| ⭐⭐ | **DAIL-SQL**: Gao et al., 2023. *Text-to-SQL Empowered by Large Language Models: A Benchmark Evaluation* | Compara formas de montar o prompt (como representar o esquema, exemplos few-shot). Ajuda no `run_eval.py`. | https://arxiv.org/abs/2308.15363 |
| ⭐ | **DIN-SQL**: Pourreza & Rafiei, 2023 (NeurIPS). *Decomposed In-Context Learning of Text-to-SQL with Self-Correction* | Decompõe perguntas complexas em partes. É uma ideia para melhorar a baseline sem treino. | https://arxiv.org/abs/2304.11015 |

## 2. Avaliação e estatística (Fase 3)

| ⭐ | Paper | Por que ler | Link |
|---|---|---|---|
| ⭐⭐⭐ | **Miller, 2024 (Anthropic).** *Adding Error Bars to Evals: A Statistical Approach to Language Model Evaluations* | A base de todo o `stats.py`: erro padrão, erros agrupados, comparação pareada entre modelos e cálculo de tamanho de amostra. **É o que mais te diferencia de um avaliador comum.** | https://arxiv.org/abs/2411.00640 |
| ⭐⭐ | **Zheng et al., 2023 (NeurIPS).** *Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena* | Os vieses de usar LLM como juiz (posição, verbosidade, autopreferência). Útil se você avaliar a qualidade das perguntas sintéticas com LLM. | https://arxiv.org/abs/2306.05685 |
| ⭐ | **Golchin & Surdeanu, 2023.** *Time Travel in LLMs: Tracing Data Contamination in Large Language Models* | Contaminação de dados: por que o teste não pode vazar. Sustenta a sua regra de vazamento. | https://arxiv.org/abs/2308.08493 |

## 3. RLHF e alinhamento (Fases 4 e 5): a teoria por trás do que você já faz como anotador

| ⭐ | Paper | Por que ler | Link |
|---|---|---|---|
| ⭐⭐⭐ | **InstructGPT**: Ouyang et al., 2022 (NeurIPS). *Training language models to follow instructions with human feedback* | O paper que popularizou o pipeline SFT → modelo de recompensa → RL. Explica **para que servem** as comparações que você faz nas plataformas. | https://arxiv.org/abs/2203.02155 |
| ⭐⭐⭐ | **DPO**: Rafailov et al., 2023 (NeurIPS). *Direct Preference Optimization: Your Language Model is Secretly a Reward Model* | O método que você vai usar na Fase 5: treina direto nos pares bom/ruim, sem modelo de recompensa separado. | https://arxiv.org/abs/2305.18290 |
| ⭐⭐ | **Christiano et al., 2017 (NeurIPS).** *Deep Reinforcement Learning from Human Preferences* | A origem do aprendizado por preferências humanas. É curto e histórico. | https://arxiv.org/abs/1706.03741 |
| ⭐ | **Constitutional AI**: Bai et al., 2022 (Anthropic). *Constitutional AI: Harmlessness from AI Feedback* | Feedback gerado por IA em vez de humano (RLAIF). É contexto para entender para onde a área foi. | https://arxiv.org/abs/2212.08073 |

## 4. Fine-tuning eficiente (Fase 5)

| ⭐ | Paper | Por que ler | Link |
|---|---|---|---|
| ⭐⭐⭐ | **LoRA**: Hu et al., 2021 (ICLR 2022). *Low-Rank Adaptation of Large Language Models* | Por que dá para treinar só uma pequena fração dos pesos. É o que faz o projeto caber no Colab. | https://arxiv.org/abs/2106.09685 |
| ⭐⭐ | **QLoRA**: Dettmers et al., 2023 (NeurIPS). *Efficient Finetuning of Quantized LLMs* | LoRA sobre modelo quantizado em 4 bits. É o que o Unsloth usa por baixo. | https://arxiv.org/abs/2305.14314 |

## 5. Dados sintéticos (Fase 4)

| ⭐ | Paper | Por que ler | Link |
|---|---|---|---|
| ⭐⭐ | **Self-Instruct**: Wang et al., 2022 (ACL 2023). *Aligning Language Models with Self-Generated Instructions* | Como gerar dados de treino com o próprio LLM e filtrar. É a base do seu gerador com filtro por execução. | https://arxiv.org/abs/2212.10560 |

## 6. Documentação de dataset e modelo (Fase 6)

| ⭐ | Paper | Por que ler | Link |
|---|---|---|---|
| ⭐⭐ | **Datasheets for Datasets**: Gebru et al., 2018 (CACM 2021) | Roteiro de perguntas para documentar o seu dataset (origem, anotação, limitações). Use como template do dataset card. | https://arxiv.org/abs/1803.09010 |
| ⭐ | **Model Cards for Model Reporting**: Mitchell et al., 2018 (FAccT 2019) | O mesmo para o modelo publicado no Hugging Face. | https://arxiv.org/abs/1810.03993 |

---

## Ordem de leitura sugerida (mínimo essencial)
1. BIRD → 2. mRAT-SQL+GAP → 3. Miller (error bars) → *(termina o Marco 1)* → 4. InstructGPT → 5. LoRA → 6. DPO

São 6 papers. Com 1 ou 2 por semana, acompanha o ritmo do projeto.

## Dica para o README e as entrevistas
Ter uma seção "Trabalhos relacionados" no README citando BIRD, Spider e mRAT-SQL+GAP, com uma frase sobre o que o seu projeto faz de diferente, já mostra maturidade de pesquisa. Exemplo: *"Diferente do mRAT-SQL+GAP, que traduziu o Spider, este benchmark usa perguntas escritas nativamente em PT-BR sobre um domínio de negócio (marketing digital) e avalia LLMs atuais com intervalos de confiança."*
