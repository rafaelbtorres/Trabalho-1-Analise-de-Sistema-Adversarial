# Guia de estudo do Grupo 7 — Poker Adversarial

**Base:** versão do [relatório principal](../README.md) revisada em 06/10/2026, com pré-flop, raise e all-in. Este guia resume o trabalho inteiro para Rafael, Elton, Frederico e Diego, independentemente da divisão dos slides.

O [enunciado, seção 6](../enunciado/Apresenta%C3%A7%C3%A3o%20de%20Trabalhos.md#6-orientações-finais) exige que o grupo consiga explicar todas as decisões. A divisão dos slides organiza a fala; todos precisam dominar o conjunto. Leia, refaça as contas e ensaie as perguntas finais.

## 1. O trabalho em uma resposta de 30 segundos

> Analisamos uma mão de poker entre dois agentes de software que disputam fichas virtuais sob informação parcial. As ações públicas permitem adaptar decisões. O motor preserva regras, fichas, sigilo e progresso. Modelamos uma decisão numa matriz, quatro etapas conectadas e três ameaças, propondo controles e possíveis reações do adversário. O Trabalho 1 é planejamento; implementaremos no Trabalho 2.

**Por que é adversarial?** A e B disputam o mesmo recurso e procuram influenciar a resposta do outro, por exemplo apostando para provocar desistência. Isso envolve intenção, além do acaso das cartas.

**O motor é um terceiro jogador?** Não. Ele medeia a disputa e aplica regras. Seu sucesso é produzir uma mão válida, mesmo que um agente perca.

**Blefar é uma ameaça ao software?** É competição legítima. Ler carta proibida, adulterar fichas ou bloquear o avanço explora falhas. Perder legitimamente não comprova injustiça.

## 2. Recorte, atores e informações

O sistema é local, com **dois agentes, uma mão, fichas virtuais e até quatro etapas**: pré-flop, flop, turn e river. Não prometemos plataforma online, dinheiro real, torneios, treinamento estatístico ou execução segura de código externo arbitrário.

| Participante | Objetivo e responsabilidade | Informação disponível |
|---|---|---|
| Agente A | Maximizar seu saldo final; escolher ações e mudar sua política | Suas cartas, comunitárias já abertas, saldos, pote, posição, vez, contribuições, ações legais e histórico público |
| Agente B | Mesmo objetivo e capacidades de A | Visão equivalente, com suas próprias cartas privadas |
| Motor e executor | Aplicar regras, contabilizar fichas, controlar a execução e o encerramento | Estado completo, sem entregar esse estado inteiro aos agentes |
| Operadores do grupo | Preparar cenários e analisar a auditoria | Dados completos para análise, sem orientar um agente com cartas proibidas durante a decisão |

Os agentes **não recebem cartas do outro, comunitárias futuras nem justificativas adversárias**. O relatório mostra tudo para análise; os agentes não conhecem o resultado antes de agir.

Uma **política** é uma regra de decisão, como apostar com mão forte. Uma **heurística** é uma regra prática limitada. Usá-las e adaptar decisões pelo histórico não exige aprendizagem de máquina nem demonstra estratégia ótima.

## 3. Regras de poker necessárias para explicar o projeto

Cada agente recebe duas cartas privadas. A mesa abre três comunitárias no **flop**, mais uma no **turn** e mais uma no **river**. Na revelação, compara-se a melhor combinação de **cinco cartas entre as sete disponíveis** para cada agente. É permitido usar comunitárias nessa combinação.

A ocupa o botão/small blind e coloca **5** fichas. B coloca o big blind de **10**. **A age primeiro no pré-flop; B age primeiro nas etapas seguintes.** Os blinds já contam como contribuição ao pré-flop.

| Termo | Significado no projeto |
|---|---|
| Pote | Fichas comprometidas na mão, ainda sem dono definitivo |
| Check | Continuar sem apostar quando não há valor a pagar |
| Bet | Abrir uma aposta numa etapa sem aposta aberta |
| Call | Pagar a diferença para igualar a maior contribuição da etapa, até o limite do saldo |
| Fold | Desistir; o outro recebe o pote, sem revelação obrigatória de suas cartas |
| Raise | Igualar e aumentar a aposta, informando o **total da contribuição na etapa** |
| All-in | Comprometer todo o saldo restante; pode funcionar como bet, call ou raise |
| Showdown/revelação | Exibir as cartas e comparar as mãos |
| Runout | Abrir as comunitárias restantes após all-in pago, sem novas decisões de aposta |

Bet mínimo: 10. Raise completo: aumento pelo menos igual ao último aumento completo, inicialmente 10. Valores inteiros até o saldo, **não apenas 10 ou 20**. All-in pode ser inferior ao mínimo por esgotar saldo; aumento incompleto não reabre apostas. Não se aumenta contra oponente all-in.

**Raise para 20 não significa acrescentar sempre 20.** No pré-flop, A já colocou 5, então transfere mais 15. B já colocou 10, então paga apenas mais 10. No flop, depois de A apostar 10 e B aumentar para 20, A paga só mais 10.

A etapa fecha após check/check ou resposta à última agressão, preservando a oportunidade de agir. Se A apenas completar o big blind, **B ainda pode dar check ou raise**. Na próxima etapa, as contribuições da etapa zeram, mas o pote permanece.

All-in pago: devolver parcela não coberta e executar runout sem novas apostas. Empate: dividir o pote, atribuindo eventual ficha indivisível a B pela regra prévia. Saldo inferior ao blind: depositar o disponível e resolver o all-in com as respostas legais, mantendo referência mínima de 10.

## 4. O exemplo principal e a conservação de fichas

A tem **7♣ e 2♦**, mão inicial fraca. B tem **A♥ e A♦**, par forte. A mesa sintética será **A♠, 9♥, 4♣, K♣, 3♦**, revelada aos agentes somente nas etapas correspondentes.

B termina com trinca de ases: A♥, A♦, A♠, K♣ e 9♥. A termina com carta alta: A♠, K♣, 9♥, 7♣ e 4♣. Portanto, **B vence nessa mesa**. Isso não significa que ases venceriam qualquer mesa possível.

| Momento | Ações e transferências | Saldo A | Saldo B | Pote |
|---|---|---:|---:|---:|
| Antes dos blinds | Cada um possui 110 | 110 | 110 | 0 |
| Início do pré-flop | A coloca 5; B coloca 10 | 105 | 100 | 15 |
| Pré-flop concluído | A raise para 20: +15; B call: +10 | 90 | 90 | 40 |
| Flop concluído | B check; A bet 10; B raise para 20: +20; A call: +10 | 70 | 70 | 80 |
| Turn concluído | B check; A check | 70 | 70 | 80 |
| River concluído | B check; A bet 20; B call 20 | 50 | 50 | 120 |
| Encerramento | B recebe o pote de 120 | **50** | **170** | **0** |

A propriedade contábil é **saldo de A + saldo de B + pote = 220**. Apostar e distribuir o pote transferem fichas, sem criá-las.

A perdeu 60 e B ganhou 60 em relação às 110 iniciais. B recebe pote de 120, mas **lucra 60**, pois também contribuiu. Os [dados do cenário](../dados/cenario.json) registram as 11 ações e estados intermediários.

## 5. Onde os dois agentes se adaptam

O objetivo continua sendo ganhar fichas. O que muda é a política escolhida após observar o outro.

| Etapa | O que A observa e decide | O que B observa e decide |
|---|---|---|
| Pré-flop | B paga sem novo aumento. A mantém um teste de pressão pequena no flop | A tomou iniciativa. B paga para mantê-lo interessado, em vez de re-aumentar imediatamente com seu par forte |
| Flop | B aumenta. A paga mais 10 para continuar, mas recua: planeja check se B não apostar no turn | A insiste e paga o raise. B troca a política de apostar com mão forte no turn por **check para induzir outra aposta** |
| Turn | Após o raise anterior, A mantém cautela e dá check. A ausência de nova aposta de B sugere possível fraqueza e motiva um blefe no river | B executa o check de indução. Vê o recuo de A e mantém a tentativa no river, planejando pagar se continuar forte |
| River | B repete check. A blefa 20; call e revelação mostram que sua leitura falhou | B vê A voltar a pressionar, paga e confirma o blefe pela revelação, sem generalizar esse único caso |

**A mudança central de B:** sua política anterior apostaria no turn com mão forte. Após o call de A ao raise, escolhe check para induzir outra aposta.

**Custos:** A expõe fichas e erra sinais. B pode perder ganho ou oferecer carta gratuita. Check não prova fraqueza. O call de A com 7♣/2♦ ilustra uma heurística limitada, sem alegação de jogada ótima.

Cada decisão deverá registrar privadamente observação, política anterior, política atual, ação e custo, permitindo conferir a adaptação sem expô-la ao oponente. Uma mão não comprova perfil estatístico; a reação após o river é hipótese futura.

## 6. A matriz: o que ela mede e por que os números fazem sentido

Analisamos **um ponto do river após o check inicial de B**, com A=70, B=70 e pote=80. O analista sabe que B vence se houver revelação. A matriz é uma análise retrospectiva de informação completa de um **jogo reduzido**. Os agentes da mão continuam com informação parcial.

| Identificador | Escolha analisada |
|---|---|
| A1 | A aposta 20 como blefe |
| A2 | A dá check, fechando check/check e levando à revelação |
| B1 | B paga se A apostar; se A der check, segue para revelação |
| B2 | B desiste se A apostar; se A der check, segue para revelação |

B1/B2 são **políticas condicionais**: só há call ou fold diante de aposta. O check inicial de B é condição fixada. Raise e all-in ficam fora **somente da tabela 2×2**, que analisa uma decisão central; continuam legais no sistema.

Os ganhos abaixo contam apenas as mudanças **a partir desse ponto**, descontando novas fichas investidas. Contribuições anteriores já estão no pote.

| A / política de B | B1: pagar | B2: desistir |
|---|---:|---:|
| A1: apostar 20 | (-20, +100) | (+80, 0) |
| A2: check | (0, +80) | (0, +80) |

Com call, o pote chega a 120: B investiu 20 e recebe 120, então ganha 100 a partir desse ponto. A perde 20 adicionais. Com fold, A recebe 100, mas 20 eram sua aposta recém-colocada: seu ganho é 80. Com check, ninguém investe mais e B recebe os 80 existentes.

**Por que os ganhos somam 80?** A comparação começa com 80 já separados no pote. Desde os saldos anteriores aos blinds, o ganho de um é a perda do outro. São pontos de partida diferentes.

Convertendo ganhos em **ordem de preferência**, sempre na ordem `(A, B)`:

| A / política de B | B1 | B2 |
|---|---:|---:|
| A1 | (0, 3) | (3, 0) |
| A2 | (1, 2) | (1, 2) |

Para A: +80 é melhor que zero, que é melhor que -20. Para B: +100 é melhor que +80, que é melhor que zero. Os payoffs **não são probabilidades nem fichas**. Não precisamos usar todos os números, e sua distância não mede diferença monetária.

### Melhores respostas, dominância e equilíbrio

**Melhor resposta** é a escolha que dá maior preferência mantendo a escolha do outro fixa.

| Escolha fixada | Melhor resposta | Motivo |
|---|---|---|
| B1 | A2 | Para A, 1 é melhor que 0 |
| B2 | A1 | Para A, 3 é melhor que 1 |
| A1 | B1 | Para B, 3 é melhor que 0 |
| A2 | B1 ou B2 | Para B, ambas dão 2 |

**Dominância:** A não tem estratégia dominante, pois sua melhor escolha depende de B. B1 domina B2 **fracamente**: dá um resultado melhor diante de A1 e igual diante de A2. Dominância estrita exigiria ser melhor nas duas situações.

**Equilíbrio de Nash em estratégias puras:** ninguém melhora mudando sozinho. O único equilíbrio puro é **(A2, B1)**: A, mudando, cai de 1 para 0; B permanece em 2. (A2, B2) não é equilíbrio: A mudaria para A1, subindo de 1 para 3. Nas células de A1 também há mudança lucrativa. Não analisamos estratégias mistas, que sorteariam escolhas com probabilidades.

**Por que a mão termina em (A1, B1)?** A desconhece as cartas de B e erra a leitura do check. O equilíbrio caracteriza a tabela retrospectiva; não resolve a incerteza nem comprova convergência das heurísticas.

**O equilíbrio garante justiça?** Usa ações legais e favorece B. Justiça exige regras e autorizações equivalentes, não ganhos iguais. A matriz não comprova os controles de sigilo e contabilização.

## 7. O blefe all-in pré-flop e os saldos diferentes

O cenário alternativo começa com **A=105, B=100 e pote=15**. A, com 7♣/2♦, transfere suas 105 restantes. Sua contribuição total passa a 110, contando o small blind. B ainda só conhece suas cartas e a pressão pública.

| Resposta de B | Resultado com a mesa sintética |
|---|---|
| Fold | O pote de 120 vai para A. Final: **A=120, B=100**. A ganha 10 em relação às 110 anteriores aos blinds. B não comprova o blefe, pois A não precisa mostrar suas cartas |
| Call de 100 | O pote chega a 220. Não há novas decisões de aposta. As cartas são reveladas e a mesa restante abre. B vence: **A=0, B=220** |

Fold representa cautela hipotética, sem defender que desistir com ases seja ótimo. All-in pode ser blefe ou aposta com mão forte; a regra é a mesma.

**Saldos diferentes:** antes dos blinds, A possui 110 e B, 70, total 180. A fica com 105, B com 60 e o pote com 15. A compromete 105 e B paga 60. O pote momentaneamente chega a 180, mas as contribuições totais são 110 e 70. Devolvem-se **40 não cobertas a A**. O pote disputado fica em 140; B vence nessa mesa. Final: **A=40, B=140, pote=0**. As 40 devolvidas não são prêmio nem pote paralelo.

Dois agentes não exigem pote paralelo. Os [dados de all-in](../dados/cenario-all-in.json) registram os três ramos. O ramo pago **complementa o percurso principal; não substitui os três ciclos exigidos**, pois encerra apostas cedo.

## 8. Ativos, pressupostos, ameaças e riscos

| Conceito | Significado | Exemplo do projeto |
|---|---|---|
| Ator | Quem atua | Agente A ou B |
| Ativo/propriedade | O que precisa ser preservado | Fichas corretas, sigilo, progresso |
| Pressuposto | Condição em que o desenho confia | Visão entregue contém só dados autorizados |
| Vulnerabilidade/fraqueza | Falha que permite exploração | Campo privado sem filtro |
| Superfície de ataque | Interfaces e fluxos expostos à interação | Visão/histórico, entrada de ações e chamada do executor |
| Ameaça | Ação possível que explora uma fraqueza | Agente ler a carta indevidamente entregue |
| Impacto | Consequência da exploração | Vantagem indevida que invalida a justiça |
| Risco | Avaliação da possibilidade e do impacto | Produto qualitativo P × I |

O ativo principal é a **integridade da mão**, incluindo regras e conservação das fichas. Também preservamos **confidencialidade** das cartas e **progresso**: a mão precisa avançar ou encerrar de forma definida.

| Pressuposto | Falha possível | Ligação |
|---|---|---|
| P1: cada agente recebe somente dados autorizados | Cartas do adversário ou futuras aparecem na visão/histórico | T1 |
| P2: motor é a autoridade para validar e contabilizar | Confia numa mensagem sem conferir vez, formato, saldo e limites | T2 |
| P3: decisão termina ou é interrompida | Chamada fica esperando indefinidamente | T3 |
| P4: entradas são coerentes | Cartas duplicadas ou saldos incompatíveis | Preparação inválida, mesmo sem intenção adversarial |

Analisamos o protótipo hipotético **antes dos controles**, supondo a fraqueza presente. O módulo controla sua resposta e duração, sem acesso livre à memória do motor. Não são falhas encontradas em código implementado.

| ID | Ação, superfície e efeito | P | I | Risco |
|---|---|---:|---:|---:|
| T1 | Ler cartas proibidas na visão/histórico sem filtro, obtendo vantagem indevida e violando sigilo e justiça | 3 | 3 | **9** |
| T2 | Enviar ação fora da vez, aposta maior que o saldo ou raise abaixo do mínimo sem esgotar saldo; validação ausente aceita transição ou fichas ilegais | 3 | 3 | **9** |
| T3 | Não retornar da decisão; executor sem limite bloqueia a mão | 3 | 2 | **6** |

**Por que P=3?** Com a fraqueza presente, a exploração é direta. P indica sucesso condicionado à fraqueza, **não chance de o projeto conter a falha nem frequência medida**. Ser local limita o alcance do dano, mas não dificulta a tentativa.

**Por que I=3/3/2?** T1/T2 expõem cartas ou adulteram o resultado. T3 exige interrupção/reinício sem necessariamente afetar fichas ou sigilo. As notas são qualitativas e precisam de reavaliação futura.

Usamos **T1/T2/T3 para ameaças** e **A1/A2/B1/B2 para a matriz**, evitando confusão de identificadores.

## 9. Defesa prioritária, adaptação e resiliência

**T1/T2 empatam em 9.** Aprofundamos T1 porque cartas conhecidas não podem ser esquecidas. Recuperar a contabilização não desfaz esse conhecimento. Isso não permite adiar T2.

Defendemos T1 com visões específicas, histórico filtrado e auditoria separada. O agente percebe o campo ausente e procura no histórico. Se também estiver filtrado, pode inferir padrões públicos ou buscar outro canal. É hipótese de reação, não ataque executado.

**Efeito colateral:** filtro errado pode ocultar dado público necessário; há custo de manutenção. **Risco residual:** outro canal, registro ou exceção expor cartas. Não recalculamos o risco sem evidências.

| Controle | Ameaça tratada | Reação possível e limite |
|---|---|---|
| C1: filtrar visões e históricos | T1 | Adversário procura outra saída; filtro pode omitir dados legítimos |
| C2: validar antes de atualizar | T2 | Adversário testa valores de fronteira; erro pode rejeitar ação válida |
| C3: versão e aplicação única | T2 | Adversário repete ou reordena mensagens; cada ação precisa produzir efeito uma única vez |
| C4: orçamento, encerramento e ação padrão | T3 | Adversário responde perto do limite; um agente legítimo lento pode sofrer fold automático |
| C5: auditoria e invariantes | T1/T2/T3 | Permite conferir violações; registros incompletos dificultam análise e registros expostos podem vazar dados |

Timeout: encerrar chamada e aplicar **check quando legal ou fold diante de aposta pendente**. Rejeitar retorno atrasado. Tentativas inválidas compartilham o orçamento da decisão. Violação de invariantes interrompe a mão sem vencedor confirmado.

**Resiliência:** preservar regras, fichas, sigilo e progresso diante da adaptação adversária, controlando cada interação. Regras de aposta não mudam automaticamente durante a mão; blefe continua permitido.

Uma **corrida armamentista** exige escalada sustentada de pressão e contramedidas. Esta mão mostra escalada pontual; não demonstra corrida sustentada entre interações.

## 10. Arquitetura e o que será conferido no Trabalho 2

| Componente | Explicação para apresentar |
|---|---|
| Preparador/comparador | Valida cartas e saldos; compara a melhor mão no encerramento |
| Motor e estado | Guarda etapa, vez, versão, fichas, contribuições, valor a pagar, último aumento completo e all-ins; aplica atualizações e devoluções |
| Provedor de visões | Produz uma saída autorizada para cada agente |
| Agentes A/B | Decidem pela própria visão e memória permitida |
| Validador | Confere ação, total de raise, saldo, mínimo, vez, identidade e versão |
| Executor | Executa a decisão em processo separado sob orçamento e consegue encerrá-la |
| Registro/verificador | Separa histórico público de auditoria privada e confere invariantes |

O fluxo é **motor prepara visão → agente decide sob executor → validador confere → motor atualiza → registro confere e publica evento permitido**. A resposta alimenta a próxima decisão. O agente propõe; não altera diretamente as fichas.

Em bet/raise, a mensagem informa total desejado. O motor calcula transferências, call e all-in e controla identidade, devolução e fichas. Versão e etapa permitem rejeitar mensagem repetida, antiga ou atrasada.

Os estados são PREPARACAO, PRE_FLOP, FLOP, TURN, RIVER, RUNOUT, REVELACAO, ENCERRADA e INTERROMPIDA. Fold encerra; raise pede resposta; all-in pago conduz ao runout; incoerência interrompe.

Processo separado permite encerrar decisão bloqueada, mas **não prometemos sandbox para código externo arbitrário**. Linguagem, biblioteca, interface gráfica e duração do orçamento ainda não foram fixadas. Terminal é uma possibilidade.

Os critérios futuros são: reproduzir **50/170** no exemplo; conservar fichas; restringir visões; validar raises e calls; resolver os três ramos de all-in e a devolução de 40; preservar a opção do big blind; não pedir aposta a saldo zero; aplicar ações uma vez; controlar timeout; registrar adaptação privada; encerrar corretamente por fold, revelação, empate ou interrupção.

**Já conferimos:** dados sintéticos, fichas, cartas, matriz, riscos e arquivos. **Ainda não testamos:** motor funcional ou sua segurança. O cenário é manual, não experimento com agentes em execução.

## 11. Como explicar diagramas, fontes e participação

| Material | O que deve ser explicado |
|---|---|
| [Contexto](../diagramas/contexto.png) | Quem interage, o papel do motor, as visões autorizadas e as propriedades preservadas |
| [Ciclo adaptativo](../diagramas/ciclo-adaptativo.png) | Quatro etapas conectadas, observações que mudam políticas, custos e ramo all-in |
| [Superfície de ataque](../diagramas/superficie-de-ataque.png) | Onde T1/T2/T3 podem ocorrer e sua ligação com pressupostos, ativos e controles |

Os Mermaid e o [PPTX dos diagramas](../diagramas/diagramas-editaveis.pptx) são fontes editáveis. As imagens PNG vieram da fonte visual PPTX; o Mermaid descreve o mesmo conteúdo com outra disposição.

Fontes: **PokerStars** sustenta regras e mãos; **MIT**, melhores respostas e equilíbrio; **OWASP**, ameaças, validação e registros. As [referências completas](../fontes/referencias.md) não comprovam nossas heurísticas, payoffs ou riscos, que são escolhas didáticas. O enunciado prevalece sobre ambiguidades da transcrição.

A entrega exige relatório em Markdown, slides com versão PDF e vídeo no YouTube, com participação equilibrada e contribuições identificáveis no Git. Canva é a plataforma preferida, sem obrigatoriedade indicada. **12 slides e 11 minutos são metas do grupo, não exigências do professor.** O prazo informado é 06/10 às 23h59; o enunciado não explicita o ano.

O Codex auxiliou pesquisa, redação, dados, diagramas, slides, conferências e este guia. O grupo deve completar os registros das contribuições reais. Gravação, publicação e submissão só devem ser declaradas quando ocorrerem: [situação dos links](../apresentacao/links.md).

## 12. Resposta à pergunta final e ensaio de dúvidas

**Depois que o sistema responder, o que o outro lado aprenderá e tentará fazer em seguida?**

> A observa resistência, recua e interpreta check como fraqueza. B observa iniciativa e usa check para induzir aposta. A revelação permite rever a leitura, sem generalizar uma mão. Após defesa técnica, o agente pode buscar outro canal, testar limites ou responder perto do timeout. O motor continua preservando fichas, sigilo e progresso. Inferência pública é permitida; vazamento, adulteração e bloqueio exigem controle.

No all-in com fold, A observa a desistência e B não confirma o blefe. Com call e revelação, ambos conhecem as cartas; adaptar só afeta uma interação futura.

Para ensaiar, cada integrante deve conseguir responder **sem consultar o documento**:

1. Por que uma disputa entre agentes honestos já é adversarial? E qual a diferença entre blefe e vazamento? **Seções 1–2.**
2. Quem age primeiro em cada etapa e por que um raise para 20 transfere valores diferentes? **Seção 3.**
3. Como chegamos aos saldos 50/170, e qual é o lucro de B? **Seção 4.**
4. Qual observação mudou a política de cada agente? Onde B deixou de apostar por valor para induzir? **Seção 5.**
5. Como foram obtidos os ganhos e os payoffs? Por que a matriz é reduzida? **Seção 6.**
6. Por que B1 domina apenas fracamente e por que (A2, B2) não é equilíbrio? **Seção 6.**
7. Por que A pode jogar fora do equilíbrio calculado? **Seção 6.**
8. O que acontece no all-in pré-flop com fold, call e saldos diferentes? **Seção 7.**
9. Como ligar uma superfície a pressuposto, ameaça, ativo, controle e risco residual? **Seções 8–9.**
10. O que significam P=3 e os riscos 9/9/6? Por que aprofundar T1 sem adiar T2? **Seções 8–9.**
11. Como o adversário reage aos controles e que custo um agente legítimo pode sofrer? **Seção 9.**
12. O que o motor fará no Trabalho 2, e o que podemos afirmar que já foi verificado? **Seção 10.**
13. O que cada diagrama mostra e que fonte sustenta cada conceito? **Seção 11.**
14. Qual é a resposta à pergunta final do professor? **Seção 12.**

Ao responder, identifique a decisão, dê um exemplo e explique seu limite. Diga claramente o que ainda não foi definido, testado ou realizado.
