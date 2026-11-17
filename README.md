# Agente de caminho de invasão (versão simplificada)

Projeto Acadêmico de Inteligência Artificial (MDC050), IESB 2026/2, de Gabriel Henrique Kuhn Paz.

Um agente responde: **por onde um invasor chegaria com menos esforço da Internet até o banco de dados**
de uma rede fictícia de 12 máquinas? A resposta diz o que corrigir primeiro.

| Fase | Técnica |
|---|---|
| 1. Busca | BFS × Custo Uniforme × A\* (heurística admissível: saltos mínimos × menor custo) |
| 2. Aprendizado | Regressão Linear × Árvore de Decisão sobre 1.500 testes sintéticos; o custo aprendido alimenta o A\* |
| 3. Agente com LLM | o LLM extrai origem e alvo (JSON), o código confere, o A\* calcula e o LLM explica |

- `Agente_Rede_Simples.ipynb`: o notebook (roda no Databricks e no Colab, sem chave de API)
- `ROTEIRO.md`: roteiro do seminário e perguntas prováveis
- `build_notebook.py`: gera o notebook
