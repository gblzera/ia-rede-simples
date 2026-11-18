# Perguntas prováveis do professor (com base no Laboratório 9, gabarito)

O Lab 9 mostra o que o prof. Rodrigo espera no seminário: **ferramenta = função + descrição**,
**o modelo pede e o código executa**, ciclo **pensar → agir → observar → responder**, **teste da escolha
da ferramenta** e **um caso de falha mostrado ao vivo**.

## 1. Agora o projeto segue o Lab 9

Modelo decide a ferramenta, duas ferramentas (`caminho_de_ataque` e `listar_vizinhas`), ciclo de até
3 passos, 15 perguntas de teste (14 de 15 com o Llama em 18/11). Falhas reais para mostrar:
injeção de prompt (respondeu sem ferramenta) e "notebook do estagiário" (trocou pelo PC RH).
Por que o limite de 3 passos? Para o agente não girar para sempre se ficar pedindo ferramenta.

## 2. Perguntas sobre o agente (Lab 9)

- **O modelo executa a ferramenta?** Não. Ele só escreve o pedido (nome da ferramenta + argumentos em JSON); quem executa é o código.
- **O que o modelo enxerga da ferramenta?** Só nome, descrição e parâmetros, nunca o código.
- **Por que a ferramenta confere os dados?** Porque o modelo pode inventar um nome; se a máquina não existe, recusa em vez de calcular errado.
- **O que acontece com o resultado da ferramenta?** Volta para o LLM, que explica usando só aqueles números (no Lab 9 é a mensagem com papel `tool`).
- **O modelo pode inventar números?** Não, os números vêm do A\*; na regra do sistema: "Use SOMENTE os números do resultado".
- **Mostre um caso de falha.** `agente('Qual o caminho da internet até o notebook do estagiário?')`: o modelo trocou a máquina inexistente pelo PC RH antes de a ferramenta conferir. Correção: regra mandando perguntar quando a máquina não estiver na lista.
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

## 6. Acrescentado em 18/11, 17h (itens do enunciado que faltavam)

- **Três redes de tamanhos diferentes** (seção 2, experimento): 12, 150 e 800 máquinas. Nós expandidos: média UCS 119 × A\* 88; grande UCS 689 × A\* 627. Custo igual (ótimo), BFS sempre mais cara (36,4 e 85 pontos contra 30,1 e 68).
- **Pré-processamento:** 5% de `dias_sem_patch` faltando → mediana (SimpleImputer); normalização com StandardScaler na regressão; firewall e MFA já são 0/1.
- **Validação cruzada (5 partes):** regressão 1,90 ± 0,15; árvore 0,76 ± 0,05.
- **Onde erra mais:** passos com MFA e cvss baixo (erro ~1,1 ponto), porque são os mais difíceis e o ruído é proporcional.
- **Tempo por pergunta do agente:** coluna `segundos` na avaliação.
