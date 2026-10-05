# Trabalho 1 - Poker Adversarial

## Identificação e materiais da entrega

- **Disciplina:** Engenharia de Software Adversarial.
- **Grupo:** Grupo 7.
- **Prazo informado:** 06/10 às 23h59, conforme o [enunciado](enunciado/Apresenta%C3%A7%C3%A3o%20de%20Trabalhos.md).
- **Integrantes:** Rafael Barboza Torres (rafaelbarbozarafaelbarboza.aluno@unipampa.edu.br), Elton Henrique Lunardi Gimenes (eltongimenes.aluno@unipampa.edu.br), Frederico Marques da Silva Barcelos (fredericobarcelos.aluno@unipampa.edu.br) e Diego Santos de Araujo (diegoaraujo.aluno@unipampa.edu.br).
- **Apresentação:** [PDF](apresentacao/slides.pdf) e [PowerPoint editável com notas de fala](apresentacao/slides.pptx).
- **Diagramas:** imagens nas seções do relatório, fontes Mermaid em `diagramas/` e [fonte visual editável](diagramas/diagramas-editaveis.pptx) usada para exportar os PNGs.
- **Gravação e publicação:** [roteiro de 11 minutos](docs/roteiro-video.md) e [situação dos links e da submissão](apresentacao/links.md).

Este README é o relatório principal. Os arquivos de [contexto para IAs](AGENTS.md) e [planejamento](docs/plano-primeira-entrega.md) apoiam a manutenção. Os materiais locais estão preparados para revisão humana. A gravação pelos quatro integrantes, a publicação e a comprovação das contribuições individuais ainda dependem do grupo.

## Resumo do sistema

O Poker Adversarial é uma simulação local de uma mão entre dois agentes de software. Eles disputam fichas virtuais, observam respostas públicas e modificam sua política de decisão. Um motor aplica as regras, contabiliza o pote e fornece a cada agente somente a informação autorizada. A interação começa no flop e percorre turn e river, com três etapas de apostas.

Os agentes podem blefar e explorar padrões públicos. O motor deve preservar a integridade da mão, o sigilo das cartas privadas e o progresso da execução. Uma derrota legítima não indica falha dessas propriedades.

## 1. Proposta e delimitação

### 1.1 Interação analisada e regras

Usamos uma variante didática inspirada em Texas Hold'em: duas cartas privadas por agente, cinco comunitárias reveladas em etapas e comparação da melhor combinação de cinco cartas no encerramento. A organização das cartas e etapas se apoia nas regras da [PokerStars](https://www.pokerstars.com/poker/games/texas-holdem/). Os limites de apostas e a ordem fixa abaixo são simplificações próprias do protótipo.

O estado inicial já contém cartas privadas, três comunitárias do flop, saldos e pote. A preparação anterior é contabilizada, mas suas decisões não integram a análise. A age primeiro em cada etapa.

| Situação | Ações permitidas e resultado |
|---|---|
| Não há aposta pendente | Check ou bet de 10 ou 20 fichas. A aposta deve ser estritamente menor que o saldo disponível de ambos. Se nenhum valor for permitido, resta check. |
| A dá check | B pode dar check ou iniciar uma aposta sob os mesmos limites. Se B aposta, A responde. |
| Existe aposta pendente | O outro agente escolhe call, transferindo o mesmo valor ao pote, ou fold. Aumentos não são permitidos. |
| Duas ações check ou bet seguida de call | A etapa termina. Após flop revela-se o turn; após turn, o river. |
| Fold | O agente restante recebe o pote. Não há revelação obrigatória de suas cartas. A mão termina. |
| River concluído sem fold | Revelam-se as cartas, compara-se a melhor mão e entrega-se o pote ao vencedor. Em empate, divide-se igualmente. |

Há no máximo uma aposta por etapa, sem aumentos, all-in ou potes paralelos. Os valores inteiros e pares mantêm divisível o pote em caso de empate. O motor rejeita ações incompatíveis com a vez, a etapa ou o saldo, sem alterar fichas.

O fluxo é: preparar estado sintético, fornecer visão de A, receber e validar ação, atualizar e fornecer a visão do outro, receber resposta e concluir a etapa ou a mão. Cada atualização pública alimenta a decisão seguinte.

Antes da revelação, a visão contém cartas próprias, comunitárias abertas, saldos, pote, vez, ações legais e histórico público. Não contém cartas futuras, cartas privadas do oponente ou sua justificativa interna.

### 1.2 Por que é adversarial?

Os objetivos são conflitantes: os agentes disputam o mesmo recurso e preferem aumentar seu saldo final. A aposta é um sinal intencional que pode tentar provocar uma desistência. Ao observar call ou check, o oponente adapta sua próxima decisão. A incerteza das cartas não elimina a intenção de influenciar o adversário.

Uma entrada inválida acidental é erro; enviar deliberadamente aposta ilegal para explorar validação ausente é ação adversarial. Um blefe válido é competição permitida. Conhecer cartas proibidas por vazamento viola a justiça. O motor medeia a disputa e não precisa derrotar um jogador.

### 1.3 Escopo para o Trabalho 2

**Incluído:** execução local, dois agentes configuráveis, uma mão com três etapas, dados sintéticos, visões filtradas, validação central, contabilização, encerramento por fold ou revelação, limite de execução e auditoria.

**Excluído:** torneios, dinheiro real, contas, plataforma online, agentes externos arbitrários, aprendizagem estatística, pré-flop estratégico, aumentos, all-in e potes paralelos.

Os agentes são módulos do próprio protótipo. O motor é a autoridade sobre cartas e fichas. Para controlar uma decisão que não termina, o executor deverá rodar o módulo em processo separado, recebendo apenas a visão serializada. Isso permite encerrar a chamada sem travar o motor, mas não constitui sandbox para código externo não confiável. Essa modalidade exigiria proteção adicional e fica fora do recorte.

## 2. Descrição do sistema adversarial

### 2.1 Atores, objetivos e capacidades

| Ator | Objetivo | Ações e capacidades | Informação observável | Restrições e custos |
|---|---|---|---|---|
| Agente A | Maximizar fichas ao final da mão | Check, bet, call, fold e mudança de política de pressão | Cartas próprias, comunitárias abertas, pote, saldos, vez e ações públicas de B | Saldo finito, apostas limitadas, informação parcial e apostas perdidas |
| Agente B | Maximizar fichas ao final da mão | Mesmas ações; mudar de apostar por valor para induzir apostas | Visão autorizada equivalente e ações públicas de A | Pagar para observar, deixar de ganhar por cautela e errar sinais |
| Motor e executor | Preservar regras, fichas, sigilo e progresso | Preparar, filtrar, validar, atualizar, encerrar chamadas e distribuir pote | Estado completo, ações recebidas e eventos | Não entregar sua visão completa ao agente; controles têm custo e podem falhar |
| Operadores do grupo | Configurar e analisar cenários | Definir entradas e revisar auditoria após a mão | Visão completa para análise, separada das entradas dos agentes | Não orientar um agente com dados privados do outro durante a decisão |

### 2.2 Ativos e propriedades

O ativo principal é a **integridade da mão**, incluindo transições legais e conservação de fichas. Cartas privadas precisam de **confidencialidade**, e a interação precisa de **progresso**. Justiça significa aplicar regras e autorizações equivalentes aos agentes, sem garantir que ambos ganhem.

A verificação planejada confere, após cada evento, que saldo de A + saldo de B + pote = total inicial; compara ações aceitas com a vez e a lista legal; procura cartas proibidas em todas as visões e históricos públicos; e confere encerramento dentro do orçamento. O exemplo possui 220 fichas.

### 2.3 Pressupostos e possíveis falhas

| ID | Pressuposto | Como pode falhar | Consequência |
|---|---|---|---|
| P1 | Cada agente recebe somente dados autorizados | Serialização inclui cartas do outro, futuras ou justificativas privadas na visão/histórico | Vantagem informacional e violação de sigilo, ligada a T1 |
| P2 | Motor é a única autoridade para aceitar e contabilizar | Confia na mensagem sem conferir vez, formato, saldo ou limites | Ação ilegal altera fichas ou sequência, ligada a T2 |
| P3 | Decisão termina ou é interrompida | Executor espera indefinidamente um módulo sem retorno | Mão bloqueada, ligada a T3 |
| P4 | Entradas têm cartas únicas e estado coerente | Cartas duplicadas ou saldos incompatíveis entram no cenário | Comparação ou contabilização inválida, mesmo sem intenção adversarial |

### 2.4 Diagrama de contexto

![Contexto: agentes, motor, visões e ativos](diagramas/contexto.png)

Fonte editável: [Mermaid](diagramas/contexto.mmd). Os agentes cruzam a fronteira apenas por visões autorizadas e propostas de ação. O operador analisa a auditoria sem transformá-la em entrada indevida durante a decisão.

## 3. Modelo estratégico estático

### 3.1 Decisão central e limites

Analisamos retrospectivamente uma decisão no river: pote de 40 e aposta de 20, com as cartas fixadas no exemplo da seção 4. Se houver revelação, B vence. O analista conhece os resultados possíveis e constrói um jogo reduzido de informação completa. Os agentes da interação continuam sem conhecer as cartas do outro.

O equilíbrio pertence à tabela. Não determina a decisão ótima sob informação parcial, que exigiria crenças sobre mãos possíveis e outra modelagem. As colunas são políticas condicionais, evitando call ou fold sem aposta:

- **A1:** bet de 20 como blefe.
- **A2:** check, seguido de check de B e revelação.
- **B1:** pagar se A apostar; seguir para revelação após check.
- **B2:** desistir se A apostar; seguir para revelação após check.

A possibilidade de B apostar após check foi excluída apenas desta matriz 2×2. Continua nas regras gerais. Procurar melhores respostas e seus cruzamentos segue o método apresentado na [aula 2 de Teoria dos Jogos do MIT](https://ocw.mit.edu/courses/17-810-game-theory-spring-2021/mit17_810s21_lec2.pdf).

### 3.2 Fichas e preferências

O ganho líquido considera este ponto de decisão, excluindo contribuições anteriores já colocadas no pote:

| A / política de B | B1: pagar se houver aposta | B2: desistir se houver aposta |
|---|---:|---:|
| A1: apostar 20 | (-20, +60) | (+40, 0) |
| A2: check | (0, +40) | (0, +40) |

Com call, o pote chega a 80. B recebe 80 após investir 20, ganhando 60 desse ponto em diante; A perde 20 adicionais. Com fold, A recupera sua aposta e recebe o pote prévio de 40. A soma dos ganhos dessa comparação é 40, recurso já existente no início da decisão. Considerando toda a mão, ganhos relativos ao saldo anterior de 110 são de soma zero.

Convertendo para preferências ordinais, na ordem **(payoff de A, payoff de B)**:

| A / política de B | B1 | B2 |
|---|---:|---:|
| **A1** | **(0, 3)** | **(3, 0)** |
| **A2** | **(1, 2)** | **(1, 2)** |

Para A, +40 é melhor que zero, que é melhor que -20. Para B, +60 é melhor que +40, que é melhor que zero. Valores maiores indicam preferência, não probabilidade. Não é necessário usar todos os números da escala para cada jogador.

### 3.3 Resultados, respostas e equilíbrio

| Resultado | Justificativa |
|---|---|
| (A1, B1) | O blefe recebe call e perde. Pior resultado de A e melhor de B entre as opções. |
| (A1, B2) | A provoca fold e conquista o pote. Melhor de A e pior de B. |
| (A2, B1) | B vence sem custo adicional. A evita perder mais, mas não ganha. |
| (A2, B2) | A política de desistir só se aplica diante de aposta; com check, há a mesma revelação. |

| Escolha fixada | Melhor resposta | Comparação |
|---|---|---|
| B escolhe B1 | A2 | 1 > 0 para A |
| B escolhe B2 | A1 | 3 > 1 para A |
| A escolhe A1 | B1 | 3 > 0 para B |
| A escolhe A2 | B1 e B2 | Ambas dão 2 para B |

A não possui estratégia dominante. B1 domina **fracamente** B2: é melhor diante de A1 e igual diante de A2. Não há dominância estrita.

O único equilíbrio em estratégias puras é **(A2, B1)**. A perde preferência se muda sozinho para A1 e B não melhora mudando para B2. (A2, B2) não é equilíbrio porque A melhora apostando. Nas outras células também existe desvio unilateral lucrativo.

Esse resultado usa ações legais e conserva fichas. Justiça e sigilo dependem da arquitetura. O equilíbrio favorece B no cenário, sem garantir benefício igual aos participantes. Não analisamos estratégias mistas. No modelo dinâmico, A escolhe fora desse equilíbrio retrospectivo por interpretar incorretamente um sinal sob informação parcial.

## 4. Modelo estratégico dinâmico

### 4.1 Dados sintéticos e rodadas

Antes da preparação, cada agente dispõe de 110 fichas e contribui com 10 para o pote. O recorte inicia com A=100, B=100 e pote=20. Total: 220 fichas. As cartas são sintéticas e não se repetem:

| Elemento | Cartas |
|---|---|
| Privadas de A | 7♣ e 2♦ |
| Privadas de B | A♥ e A♦ |
| Flop | A♠, 9♥, 4♣ |
| Turn | K♣ |
| River | 3♦ |

O analista vê toda a tabela; os agentes recebem apenas as cartas permitidas em cada etapa. A melhor mão de B é A♥, A♦, A♠, K♣, 9♥, trinca de ases. A possui A♠, K♣, 9♥, 7♣, 4♣, carta alta. A classificação favorece B conforme a [hierarquia de mãos](https://www.pokerstars.com/poker/games/rules/hand-rankings/).

Política inicial de A: testar pressão pequena com mão fraca e ajustar pela resposta. Política inicial de B: apostar com mão forte quando recebe check; após observar iniciativa de A, pode trocar para induzir outra aposta. São heurísticas limitadas, sem demonstração de ótimo ou aprendizagem estatística.

| Rodada | Ação e resposta | Observação e adaptação de A | Observação e adaptação de B | Estado |
|---|---|---|---|---|
| 1 - flop | A aposta 10; B paga 10. Motor valida e publica. | Call indica resistência. A planeja check no turn para observar. | A iniciou a aposta. B muda da intenção de apostar diante de check para indução, mantendo A interessado. | A=90, B=90, pote=40 |
| 2 - turn | A dá check; B dá check. Motor encerra etapa e abre river. | Interpreta check como possível fraqueza e planeja pressão de 20. Leitura incerta. | Executa a mudança: check em vez de apostar. Planeja pagar nova aposta se continuar forte. | A=90, B=90, pote=40 |
| 3 - river | A blefa 20; B paga 20. Motor conduz à revelação. | Testa sua hipótese. Call e revelação mostram falha; pode reduzir esse blefe numa interação futura. | Observa retomada da pressão e paga. Revelação confirma um blefe neste caso, sem generalizar. | A=70, B=70, pote=80 |

B recebe 80 no encerramento: **A=70, B=150 e pote=0**. A soma é sempre 220. Os dados estruturados estão em [cenario.json](dados/cenario.json).

A sequência é uma hipótese manual coerente, não resultado de simulador implementado. No river ocorre (A1, B1), fora do equilíbrio da tabela: A desconhece a mão de B e sua heurística erra a leitura do check.

### 4.2 Ciclo e custos

![Três rodadas e adaptação dos agentes](diagramas/ciclo-adaptativo.png)

Fonte editável: [Mermaid](diagramas/ciclo-adaptativo.mmd).

B explicita adaptação: sem a iniciativa anterior de A, apostaria no turn com mão forte; depois de observá-la, escolhe check para tentar induzir aposta. Seu custo é perder oportunidade de ganho ou permitir que a próxima carta favoreça A. A expõe fichas ao interpretar um sinal ambíguo.

No Trabalho 2, cada decisão deverá registrar internamente observação relevante, política anterior, política atual, ação e custo. O oponente não recebe esse registro. Controles técnicos também custam: interromper decisão legítima lenta pode provocar fold automático.

### 4.3 Perguntas de fechamento

- **Quem observa quem?** A e B observam ações públicas mútuas. Motor observa ações e execução. Operadores analisam auditoria após a mão.
- **O que muda?** A muda pressão e cautela. B muda de aposta por valor para indução. Regras e cartas não mudam por vontade do agente.
- **O que dispara adaptação?** Call, check, valor, novas comunitárias e revelação. São sinais, não acesso garantido à força privada.
- **Qual é o custo?** Fichas comprometidas, oportunidade perdida, cálculo adicional e leitura errada. Defesa técnica pode interromper agente legítimo.
- **Onde surge corrida armamentista?** Mais pressão pode levar a mais pagamentos e exposição. A passagem de 10 para 20 é escalada pontual. Corrida sustentada exigiria interações repetidas e não foi demonstrada nesta mão.

## 5. Ameaças e riscos

### 5.1 Superfície e capacidades

Avaliamos um protótipo hipotético **antes dos controles**. Um módulo adversarial pode inspecionar sua visão, enviar dados inválidos e não terminar a decisão. Não supomos acesso livre à memória do motor ou a sistemas externos. A fraqueza é precondição de cada cenário, não falha verificada em código.

Os pontos são as mesmas interfaces das rodadas: saída de visão/histórico, entrada de ações e chamada do executor. Relacionar componentes, ameaças e respostas durante o desenho se apoia na [modelagem de ameaças da OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html).

![Superfície de ataque: T1, T2 e T3](diagramas/superficie-de-ataque.png)

Fonte editável: [Mermaid](diagramas/superficie-de-ataque.mmd). T1/T2/T3 são ameaças e não se confundem com ações A1/A2.

### 5.2 Escala e cenários

P é a probabilidade qualitativa de sucesso da tentativa **se a fraqueza existir**. Não é frequência medida nem probabilidade de o projeto conter uma falha. Escopo local limita o dano, mas não dificulta exploração direta. A escala 1 a 3 e o produto P×I são a convenção do enunciado.

| Nota | Probabilidade condicionada à fraqueza | Impacto no recorte |
|---|---|---|
| 1 | Exige condições adicionais difíceis | Incômodo sem comprometer estado, sigilo ou conclusão |
| 2 | Exige sequência ou estado específico | Interrupção e reinício, com sigilo e estado preservados |
| 3 | Exploração direta pela interface | Cartas privadas expostas ou regras/fichas adulteradas, invalidando justiça |

| ID | Cenário de ameaça | Ponto e fraqueza | Ativo | P e justificativa | I e justificativa | Risco |
|---|---|---|---|---|---|---:|
| T1 | Um agente pode ler cartas do oponente pela visão ou histórico, aproveitando filtro ausente, obtendo vantagem indevida sobre a justiça da mão. | Provedor de visões/histórico; falha de P1 | Sigilo e justiça | 3: basta ler campo já entregue | 3: informação proibida muda decisões e não pode ser esquecida | 9 |
| T2 | Um agente pode enviar aposta maior que seu saldo ou ação fora da vez pela entrada de ações, aproveitando validação ausente, causando contabilização ou transição ilegal. | Validador/motor; falha de P2 | Integridade e fichas | 3: basta enviar ação sem conferência correspondente | 3: resultado desrespeita regras e saldos | 9 |
| T3 | Um agente pode nunca retornar pela chamada de decisão, aproveitando orçamento ausente, bloqueando o progresso da mão. | Executor; falha de P3 | Progresso | 3: chamada sem limite fica bloqueada diretamente | 2: exige interrupção/reinício sem necessariamente alterar saldo ou sigilo | 6 |

Os valores devem ser revistos com evidências da implementação. T1 e T2 empatam e ambas exigem controles antes do uso.

### 5.3 Prioridade, resposta e adaptação

**Aprofundamos T1**, empatada com T2 em 9, porque o agente não pode esquecer cartas já expostas. Reiniciar a partir de estado confiável recupera contabilização, mas não elimina conhecimento. O desempate organiza a análise e não autoriza adiar T2.

A resposta é fornecer visão específica por agente e filtrar histórico público, separando registros internos. O adversário observa ausência dos campos privados. Uma sequência possível é:

1. Consulta carta do oponente na visão.
2. Percebe campo indisponível e procura o dado no histórico.
3. Encontra histórico filtrado e passa a inferir padrões permitidos ou procurar outro canal.

Essa adaptação após redesenho é hipotética, distinta das três etapas de apostas e sem alegação de teste realizado. Inferência por ações públicas permanece legítima. O risco residual é uma saída secundária, exceção ou registro que exponha dados.

Filtro incorreto pode omitir informação pública e prejudicar agente legítimo. Desenvolver e conferir autorizações tem custo. Não atribuímos redução numérica ao risco após defesa sem evidências. Monitoramos ausência de cartas proibidas em todas as saídas, preservando dados públicos necessários.

## 6. Redesenho e resiliência

| Controle | Ameaça | Incentivo e observação | Reação possível | Custo e risco residual |
|---|---|---|---|---|
| C1: filtrar visões e históricos | T1 | Remove vantagem da leitura direta; campos ficam indisponíveis | Procura outra saída ou sinais públicos | Pode omitir dado legítimo; outro canal pode vazar |
| C2: validar antes de atualizar | T2 | Ação ilegal não rende fichas; rejeição é observável | Testa valores de fronteira | Erro pode rejeitar ação válida; outra transição pode falhar |
| C3: versão e aplicação única | T2 | Repetição não duplica ganho; versão antiga é rejeitada | Tenta outra ordem ou formato | Maior complexidade; conferência incorreta ainda pode aceitar repetição |
| C4: orçamento, término e ação padrão | T3 | Ausência de resposta não bloqueia indefinidamente; timeout é observável | Retorna perto do limite | Pode interromper agente legítimo lento; consome recursos até o limite |
| C5: auditoria e invariantes | T1/T2/T3 | Permite detectar violação e reconstruir sequência | Procura evento não coberto | Registros custam recursos e podem vazar se publicados indevidamente |

O validador deverá conferir tipo, valor permitido e significado da ação para o estado, em linha com a validação sintática e semântica da [OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html). Eventos deverão registrar ator, ação, etapa, versão e resultado, com separação entre informação pública e privada, conforme orientação de [registros de aplicação](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html).

Para timeout, a ação padrão é check quando legal ou fold quando há aposta pendente. O executor encerra o processo antes de aplicar a resposta e rejeita retorno atrasado. Se detectar violação de invariantes, o motor interrompe a mão para análise, sem declarar vencedor a partir de estado inconsistente.

A resiliência depende de aplicar regras e propriedades mesmo quando o adversário muda de caminho. Visões e ações continuam verificadas a cada interação. Mudanças de controles são redesenho posterior; regras de aposta não mudam automaticamente no meio da mão. Blefe permanece permitido e nenhuma defesa é solução definitiva.

## 7. Arquitetura inicial para o Trabalho 2

### 7.1 Componentes e contratos

| Componente | Responsabilidade | Entradas | Saídas | Relação |
|---|---|---|---|---|
| Preparador/comparador | Validar cenário e melhor mão | Cartas e saldos sintéticos | Estado válido e resultado | P4, integridade |
| Motor e estado | Etapas, vez, versão, fichas e encerramento | Ação validada e estado anterior | Novo estado e evento | T2 |
| Provedor de visões | Serializar dados autorizados | Estado interno e identidade | Visão própria e dados públicos | T1 |
| Agentes A/B | Escolher ação e adaptar política | Visão e memória autorizadas | Ação e registro privado | Estratégia, T2/T3 |
| Validador | Conferir ação, valor, vez e versão | Mensagem e estado | Aceitação única ou rejeição | T2 |
| Executor | Rodar módulo em processo sob orçamento | Visão e configuração | Ação para inspeção ou timeout | T3 |
| Registro/verificador | Separar histórico e conferir invariantes | Eventos e decisões | Histórico filtrado e auditoria | T1/T2/T3 |

A mensagem contém agente, etapa, versão do estado, tipo de ação e valor somente em bet/call. O motor fixa a identidade e não permite troca pelo módulo. Resposta repetida, atrasada ou de outra versão é rejeitada. Rejeições não mudam fichas; novas tentativas compartilham o orçamento daquela decisão para evitar laço infinito.

Memória do agente contém somente sua visão anterior e inferências. Justificativas pertencem à auditoria e não ao histórico público.

### 7.2 Fluxo e estados

PREPARACAO valida dados. FLOP, TURN e RIVER alternam vez e resposta conforme a seção 1. Cada ação validada incrementa versão, atualiza fichas uma única vez e registra evento. Check/check ou bet/call fecha a etapa. Fold leva a ENCERRADA. River concluído leva a REVELACAO e depois ENCERRADA. Inconsistência leva a INTERROMPIDA, sem resultado competitivo confirmado.

A implementação pode começar por terminal e relatório de eventos. Interface gráfica, linguagem e biblioteca não são exigidas nesta entrega. [cenario.json](dados/cenario.json) define cartas, ações ilustrativas e estados esperados.

### 7.3 Critérios de sucesso

1. Executar três etapas e obter saldos 70/150 com pote zero no exemplo.
2. Conservar 220 fichas após cada ação, sem saldo negativo.
3. Não expor cartas adversárias/futuras em visões e históricos antes da revelação permitida.
4. Rejeitar ação fora da vez, valor fora do conjunto ou saldo insuficiente, sem alterar fichas.
5. Aplicar cada ação uma única vez e rejeitar versões antigas/retornos atrasados.
6. Interromper decisão sem retorno dentro do orçamento e aplicar ação padrão legal.
7. Registrar mudança de política dos dois agentes sem expor raciocínio privado ao outro.
8. Encerrar por fold, showdown, empate ou interrupção conforme os estados definidos.

São especificações para o Trabalho 2, não testes já executados de um motor funcional.

## 8. Referências

Fontes consultadas em 05/10/2026; metadados e rastreabilidade estão em [fontes/referencias.md](fontes/referencias.md):

1. [PokerStars - Texas Hold'em](https://www.pokerstars.com/poker/games/texas-holdem/): estrutura de cartas e etapas.
2. [PokerStars - Poker Hand Rankings](https://www.pokerstars.com/poker/games/rules/hand-rankings/): classificação das mãos.
3. [MIT OpenCourseWare - Games in Strategic Form and Nash Equilibrium, aula 2, 2021](https://ocw.mit.edu/courses/17-810-game-theory-spring-2021/mit17_810s21_lec2.pdf): melhores respostas e equilíbrio.
4. [OWASP - Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html): desenho, ameaças e respostas.
5. [OWASP - Input Validation Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html): conferência de ações.
6. [OWASP - Logging Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html): eventos e proteção de dados.

O [enunciado](enunciado/Apresenta%C3%A7%C3%A3o%20de%20Trabalhos.md) define entregáveis e escala. A [transcrição](enunciado/trancricao_video_enunciado.md) complementa a interpretação. Apostas, cartas, políticas, payoffs e riscos são decisões do cenário didático, não resultados atribuídos às fontes.

## 9. Declaração de IA generativa

O Codex foi usado para analisar o enunciado e a base, preparar contexto, plano e roteiro, redigir este relatório, buscar fontes, organizar dados sintéticos, produzir diagramas e slides e conferir coerência entre materiais. A verificação automatizada abrange fichas, respostas da matriz, classificação das cartas, riscos, duração planejada e estrutura de arquivos. Isso não equivale a testar o sistema futuro.

A revisão humana e o domínio das decisões pelos quatro integrantes ainda precisam ocorrer antes da submissão. O grupo deve registrar as verificações que efetivamente fizer, corrigir erros e assumir a versão apresentada. IA não substitui contribuições humanas nem apresentação pelos integrantes.

## 10. Contribuições individuais

O histórico local consultado em 05/10/2026 permite confirmar apenas o abaixo. Ausência de registro local não comprova ausência de trabalho em outro ambiente. A preparação assistida por IA não foi atribuída artificialmente a pessoas nem distribuída por commits com autoria fabricada.

| Integrante | Evidência local | Participação planejada para concluir |
|---|---|---|
| Rafael Barboza Torres | 549e76e: esqueleto; bd5171c: identificação, resumo, escopo e fluxo | Revisar recorte e atores; apresentar slides 1 a 3 |
| Elton Henrique Lunardi Gimenes | 88fef65: primeira revisão | Revisar matriz e rodadas; apresentar slides 4 a 6 |
| Frederico Marques da Silva Barcelos | Contribuição individual ainda não identificável no histórico local consultado | Revisar riscos, registrar trabalho real; apresentar slides 7 a 9 |
| Diego Santos de Araujo | Contribuição individual ainda não identificável no histórico local consultado | Revisar arquitetura, registrar trabalho real; apresentar slides 10 a 12 |

A divisão é proposta, não declaração de trabalho concluído. Todos devem revisar o conjunto, gravar sua parte e registrar contribuições reais no Git.

## 11. Checklist

- [x] Interação e regras delimitadas.
- [x] Atores, ativos, capacidades, dados, custos e pressupostos descritos.
- [x] Matriz, contas, respostas, dominância e equilíbrio analisados.
- [x] Três rodadas e adaptação dos dois agentes.
- [x] Três diagramas com fontes editáveis.
- [x] Três ameaças, riscos 9/9/6 e desempate.
- [x] Controles, próxima reação, custos e risco residual.
- [x] Arquitetura e critérios para o Trabalho 2.
- [x] Referências e declaração de IA.
- [x] Apresentação local em PDF/PPTX e roteiro.
- [ ] Revisão humana, domínio e contribuições reais de todos confirmados.
- [ ] Vídeo dos quatro gravado e publicado no YouTube.
- [ ] Links externos acessíveis e submissão concluída.

## Pergunta final

**Depois que o sistema responder, o que o outro lado aprenderá e tentará fazer em seguida?**

A aprende que call resiste à pressão e pode interpretar check como fraqueza. B observa iniciativa e pode usar check para induzir aposta. A revelação corrige a hipótese de A neste caso, sem produzir conhecimento universal sobre o oponente.

Após defesa técnica, o agente aprende quais campos e ações são permitidos. Pode buscar informação pelo histórico, testar limites ou retornar perto do orçamento. O motor deve continuar filtrando saídas, validando ações e preservando fichas e progresso. Inferência legítima permanece no jogo; exposição indevida, adulteração e bloqueio exigem controles e revisão.
