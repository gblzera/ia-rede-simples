# Roteiro do seminário (18/11), versão simplificada

Notebook: `Agente_Rede_Simples` no Databricks, no Git folder `Users/gabriel.kpaz@gmail.com/ia-rede-simples-final` (a pasta antiga `ia-rede-simples` ficou com a versão anterior)
(repositório privado `gblzera/ia-rede-simples`). **Antes de entrar na sala**: abra o notebook, escolha
**Serverless** e clique em **Run all** (cerca de 2 min). O LLM é o Llama 3.3 70B servido pelo
workspace, sem chave no código. Se o modelo servido falhar, o agente usa palavras-chave sozinho.
Backup se o Databricks cair: `Agente_Rede_Simples_executado.html` (execução de 17/11, tudo certo).
O mesmo notebook também roda no Colab, e lá ele usa o Qwen2.5-1.5B.

---

## Abertura (30 s)

> "Meu agente responde uma pergunta de defesa: **por onde um invasor chegaria com menos esforço
> da Internet até o banco de dados da empresa?** Com isso, a empresa sabe o que corrigir primeiro.
> A rede é inventada, com 12 máquinas. São três fases, e no fim tudo vira uma peça só."

## Fase 1: Busca (2 min), seções 1 e 2

- Mostre o desenho da rede: "cada bolinha é uma máquina, cada seta é um passo possível do invasor".
- Os 5 componentes: **estado** = máquina onde o invasor está; **ação** = seguir uma seta;
  **objetivo** = banco de dados; **custo** = pontos de dificuldade (quanto maior, mais difícil), na Fase 1 `10 − cvss`.
- Heurística: **saltos mínimos até o alvo × menor custo de passo**. É admissível porque nenhum
  caminho tem menos saltos que isso e nenhum passo custa menos que o menor.
- Tabela: "a BFS acha 3 máquinas, mas custa 10,5 pontos. O A\* acha 5 máquinas e 9 pontos: mais passos,
  porém mais fáceis."
- Se perguntarem por que o A\* expandiu os mesmos nós: "a rede é pequena; em rede grande, no
  Marco 1, ele expandiu até 7,5 vezes menos".

## Fase 2: Aprendizado (2,5 min), seção 3

- "A tabela `10 − cvss` ignora firewall, autenticação em dois fatores e atualização. Então eu
  aprendo o custo real com um histórico de 1.500 testes de invasão, que são dados sintéticos."
- 4 entradas (cvss, firewall, mfa, dias sem atualizar) → saída: pontos de dificuldade.
- Dois modelos, 80% treino e 20% teste, métrica **erro médio absoluto**:
  tabela erra 2,6 pontos · **regressão linear 1,7 pontos** · **árvore de decisão 0,6 pontos**.
- Por que a árvore ganha: "firewall e MFA **multiplicam** o esforço; a regressão linear só soma.
  A árvore aprende regras do tipo *se tem MFA, então…*".
- Desenho final: "com o custo aprendido, o A\* muda de rota. A azul (tabela) custa 12,7 pontos de
  verdade; a vermelha (aprendida) custa 5,8 pontos. A vermelha, pelo Wi-Fi de visitantes e pela
  impressora, é **o furo real** que a tabela não via."

## Fase 3: Agente com ferramentas (2,5 min), seção 4

- Igual ao Laboratório 9: **o modelo decide** se usa ferramenta, qual e com quais dados; **o código executa**.
- Duas ferramentas: `caminho_de_ataque(origem, alvo)` (A\* com custo aprendido) e `listar_vizinhas(maquina)`.
- Ciclo **pensar → agir → observar → responder**, com **limite de 3 passos**.
- Mostre a ficha (JSON): "o modelo nunca vê o código, só nome, descrição e parâmetros".
- Demo: a pergunta do wi-fi usa **as duas ferramentas em dois passos**.
- Pergunta "Oi, o que você sabe fazer?": responde **sem ferramenta**.
- **Caso de falha:** "notebook do estagiário": o Llama trocou sozinho pelo PC RH (errou antes da conferência).
- Avaliação: **15 perguntas, 14 de 15**. O erro foi a injeção de prompt: respondeu sem chamar a ferramenta.

## IA responsável (1 min), seção 5

Dados sintéticos (o modelo aprende as nossas suposições) · agente limitado a 12 máquinas · o LLM
pode errar, então o código valida · custo zero · sem dados pessoais (LGPD) · uso defensivo · IA
generativa usada como apoio no desenvolvimento.

---

## Perguntas prováveis da arguição

| Pergunta | Resposta curta |
|---|---|
| O que é heurística admissível? | Nunca superestima o custo real até o objetivo; garante que o A\* ache a rota ótima. |
| Diferença entre BFS, Custo Uniforme e A\*? | BFS: menos passos. Custo Uniforme: menor custo acumulado `g`. A\*: `g + h`, usa a estimativa para ir direto ao alvo. |
| Por que o grafo é dirigido? | Chegar de A em B não significa chegar de B em A. |
| Por que dividir em treino e teste? | Para medir o erro em dados que o modelo nunca viu; senão o resultado engana. |
| O que é MAE? | Erro médio absoluto: em média, quantos pontos a previsão erra. |
| Por que a árvore ganhou? | Os efeitos se multiplicam; a regressão linear só representa soma. |
| Como o custo aprendido entra na busca? | A mesma função `busca` recebe `custo_aprendido` no lugar de `custo_tabela`. |
| O LLM pode inventar números? | Não: ele só extrai origem e alvo e explica; os números vêm da ferramenta. |
| O que é saída estruturada? | O LLM responde em JSON `{origem, alvo}`, que o código consegue ler e validar. |
| E se o pedido não tiver nada a ver? | A guarda vê que não há nenhuma máquina citada e recusa sem chamar a ferramenta. |
