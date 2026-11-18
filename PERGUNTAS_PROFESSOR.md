# Perguntas prováveis do professor (com base no Laboratório 9, gabarito)

O Lab 9 mostra o que o prof. Rodrigo espera no seminário: **ferramenta = função + descrição**,
**o modelo pede e o código executa**, ciclo **pensar → agir → observar → responder**, **teste da escolha
da ferramenta** e **um caso de falha mostrado ao vivo**.

## 1. Diferenças entre o seu agente e o do Lab 9 (ele VAI perceber)

| Lab 9 | Seu projeto | Como defender |
|---|---|---|
| O **modelo** decide se usa ferramenta (`<tool_call>`) | O **código** decide a ordem: LLM extrai JSON, código confere, chama o A\* (estilo Lab 8) | "Escolhi o fluxo fixo porque só tenho uma ferramenta; com uma ferramenta só, deixar o modelo escolher só adiciona chance de erro. O código confere tudo antes de calcular." |
| Duas ferramentas | Uma: `caminho_de_ataque(origem, alvo)` | "Uma segunda seria fácil: listar vizinhas de uma máquina, igual `listar_vizinhas`." |
| Ciclo com limite de 3 passos | Um passo só | "Não há loop porque a resposta sai em uma chamada; o limite de passos serve para não girar para sempre." |
| 15 perguntas de teste | 8 pedidos, 8 de 8 | "Medi 8 casos, incluindo fora do escopo e injeção de prompt." |

## 2. Perguntas sobre o agente (Lab 9)

- **O modelo executa a ferramenta?** Não. Ele só escreve o pedido (no meu caso o JSON `{origem, alvo}`); quem executa é o código.
- **O que o modelo enxerga da ferramenta?** Só nome, descrição e parâmetros, nunca o código.
- **Por que a ferramenta confere os dados?** Porque o modelo pode inventar um nome; se a máquina não existe, recusa em vez de calcular errado.
- **O que acontece com o resultado da ferramenta?** Volta para o LLM, que explica usando só aqueles números (no Lab 9 é a mensagem com papel `tool`).
- **O modelo pode inventar números?** Não, os números vêm do A\*; na regra do sistema: "Use SOMENTE os números do resultado".
- **Mostre um caso de falha.** Prepare: um pedido com apelido que o código não conhece, por exemplo `agente('do notebook do estagiário até o banco')` → recusa ou cai em Internet. Ou um pedido com duas máquinas ambíguas. Falha por quê: o agente só conhece 12 máquinas e os apelidos escritos à mão.
- **Qual LLM e por quê?** Databricks: Llama 3.3 70B servido, sem chave. Colab: Qwen2.5-1.5B, o mesmo do laboratório.
- **Temperatura?** Zero (no Lab, `do_sample=False`): mesma pergunta, mesma resposta.

## 3. Perguntas sobre busca (Fase 1)

- 5 componentes: estado (máquina), ações (setas), estado inicial (Internet), objetivo (Banco de dados), custo (pontos de dificuldade, `10 − cvss`).
- Heurística: saltos mínimos × menor custo de passo. Admissível: nunca superestima. No Lab é a linha reta até Bucareste; aqui não há geometria, então uso saltos.
- BFS 10,5 pontos (menos máquinas), Custo Uniforme e A\* 9,0 pontos (rota ótima).
- Por que o A\* não expandiu menos? Rede pequena; em rede grande expande bem menos.

## 4. Perguntas sobre aprendizado (Fase 2)

- 1.500 exemplos sintéticos, 80% treino, 20% teste.
- Métrica: erro médio absoluto, em pontos. Tabela 2,6 · Regressão Linear 1,7 · Árvore 0,6.
- Pesos da fórmula que gerou os dados: firewall ×1,8, MFA ×2,5, dias sem atualizar reduzem até ~73% do valor, ruído de ±15%. Foram escolhidos por mim para que proteção multiplique o esforço.
- Por que a árvore ganhou? Os efeitos se multiplicam; a regressão linear só soma.
- Árvore com profundidade máxima 8 e mínimo de 5 exemplos por folha, para não decorar os dados.

## 5. IA responsável (aula de 04/11)

Dados sintéticos, só 12 máquinas, LLM pode errar mas o código valida, sem dados pessoais (LGPD),
uso defensivo, sem chave no código, IA generativa usada como apoio.
