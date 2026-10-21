# Testes da CLARA — Aula 10

Testes por cenário da seção 6.2, rodados em 20/10/2026 no workflow **Aula 10 - CLARA multiagente**, com a base `convenios` carregada pelo formulário de ingestão (`3 arquivo(s) indexado(s) em 3 trecho(s).`). Cada cenário rodou numa sessão nova; os cenários 4 e 5 continuaram a sessão do 3. A coluna de sub-agente lista os nós de agente e de ferramenta que aparecem no registro de cada execução (painel **Logs** / aba **Executions**).

## Configuração usada

Com a autorização do professor, os modelos diferem dos da apostila:

| Papel | Apostila | Usado |
| --- | --- | --- |
| Modelo da CLARA | Groq `openai/gpt-oss-120b` | Anthropic `claude-sonnet-5-5` |
| Modelo dos sub-agentes | Groq `openai/gpt-oss-20b` | Anthropic `claude-haiku-5-5` |
| Embeddings (ingestão e consulta) | Ollama `nomic-embed-text` | Google Gemini `models/gemini-embedding-001` |

O Ollama local não pôde ser usado (instalação sem o `llama-server`), por isso os embeddings foram para o Gemini; o mesmo modelo é usado na ingestão e na consulta (seção 2.4). O n8n avisa que a temperatura 0,2 é ignorada pelos modelos Claude mais novos. O resto segue a apostila: trechos de 1000 caracteres com sobreposição de 150, metadados `arquivo` e `convenio`, top-k 4, memória de 10 mensagens e no máximo 4 iterações por sub-agente.

## Cenários

| # | Mensagens | Resposta obtida | Sub-agente acionado (Logs) | Esperado? |
| --- | --- | --- | --- | --- |
| 1 | "oi" | "Olá! Eu sou a CLARA, assistente virtual do Núcleo de Convênios." Apresenta o que pode fazer e pergunta como ajudar. | nenhum | Sim |
| 2 | "Qual o horário de atendimento e o e-mail de vocês?" | Segunda a sexta, das 8h às 17h (presencial e telefone); chat e e-mail 24 horas; e-mail atendimento@nucleo-convenios.example. | Sub-agente INFO | Sim |
| 3 | "Quero saber sobre um convênio." | Pede o número do convênio no formato NNN/AAAA, com o exemplo 047/2026. | nenhum | Sim |
| 4 | (sessão do 3) "É o 047/2026. Qual o objeto e o valor?" | Objeto: projeto "Pesquisa Aplicada em Tecnologias Sustentáveis"; valor global R$ 250.000,00. | Sub-agente CONVENIOS → Base de convenios | Sim |
| 5 | (sessão do 3) "Quem assina pela fundação?" | "Pela Fundação Pesquisa & Inovação, quem assina o convênio 047/2026 é Fernanda Lopes Azevedo." Não pediu o número de novo. | Sub-agente CONVENIOS → Base de convenios | Sim |
| 6 | "E o convênio 099/2026, qual o valor?" | "Não encontrei nenhum registro do convênio 099/2026 na base." Não citou outros convênios e se ofereceu para abrir um chamado. | Sub-agente CONVENIOS → Base de convenios | Sim |
| 7 | "Preciso falar com uma pessoa: meu pedido de aditivo do convênio 021/2026 está parado há um mês." e depois "Sou Carlos Mendes, carlos.mendes@exemplo.example" | 1ª: pediu só o nome completo e o e-mail, dizendo que já tinha a descrição. 2ª: "seu chamado foi registrado. O protocolo é CH-20261020-214446", com resposta da equipe no próximo dia útil. Nova linha na tabela `chamados` (Carlos Mendes, carlos.mendes@exemplo.example, descrição do aditivo do 021/2026). | 1ª: nenhum · 2ª: Sub-agente ATENDIMENTO → Registrar chamado | Sim |
| 8 | "Meu nome é Maria." e depois "Você lembra meu nome?" | "Lembro, sim: você disse que seu nome é Maria." | nenhum | Sim |

## Ajustes de prompt

Todos os oito cenários passaram de primeira, com os prompts da apostila (seções 5.2 a 5.5) sem alteração; não houve instrução a corrigir.
