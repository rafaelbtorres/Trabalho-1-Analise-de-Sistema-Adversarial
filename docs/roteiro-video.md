# Roteiro dos slides e do vídeo

Roteiro de produção para a primeira entrega do Grupo 7. Base: `enunciado/Apresentação de Trabalhos.md`, `enunciado/trancricao_video_enunciado.md` e `docs/plano-primeira-entrega.md`.

As falas abaixo correspondem à modelagem incorporada ao README e aos 12 slides em [PPTX editável](../apresentacao/slides.pptx) e [PDF](../apresentacao/slides.pdf). As falas também estão nas notas do PPTX. O ensaio e a gravação permanecem como etapas seguintes. Se alterar o modelo, atualizar relatório, dados, diagramas, slides e falas juntos. O cenário é uma sequência sintética manual, não uma execução de software pronto.

## Formato e distribuição

- Sugestão: 12 slides, vídeo com meta de **11 minutos**. O enunciado consultado não define duração nem quantidade de slides.
- Rafael: slides 1–3; Elton: 4–6; Frederico: 7–9; Diego: 10–12.
- Meta: cada integrante dispõe de **2min45s**, incluindo pausas e transição. Os tempos abaixo totalizam 660 segundos; confirmar a duração real no ensaio, sem acelerar artificialmente a fala.
- Usar apresentação horizontal 16:9, fundo simples, contraste alto e texto curto. Preferir fonte de corpo de pelo menos 24 pontos; ampliar tabelas se necessário.
- Uma ideia central por slide. O texto completo deste roteiro fica nas notas, não projetado na tela.
- Usar os diagramas finais do repositório; não desenhar versões contraditórias para a apresentação.
- A fala deve explicar as escolhas. Ensaiar até conseguir apresentá-las com naturalidade, sem apenas ler parágrafos.

| Bloco | Slides | Tempos por slide | Intervalo no vídeo | Total |
|---|---|---|---|---|
| Rafael | 1–3 | 40s + 55s + 70s | 00:00–02:45 | 2min45s |
| Elton | 4–6 | 55s + 50s + 60s | 02:45–05:30 | 2min45s |
| Frederico | 7–9 | 45s + 60s + 60s | 05:30–08:15 | 2min45s |
| Diego | 10–12 | 55s + 55s + 55s | 08:15–11:00 | 2min45s |

## Slide 1 — Tema, grupo e pergunta central

**Responsável:** Rafael. **Tempo-alvo:** 40 segundos.

**Na tela:** título “Poker Adversarial”; Grupo 7; nomes dos quatro integrantes; pergunta “Como os agentes decidem e se adaptam com informação parcial?”.

**Fala sugerida:**

> Somos o Grupo 7, formado por Rafael, Elton, Frederico e Diego. Nosso trabalho analisa uma interação de poker entre dois agentes de software. Cada agente busca melhorar seu resultado em fichas e precisa tomar decisões sem conhecer as cartas privadas do adversário. A pergunta que orienta a análise é como as escolhas de um lado produzem informação e mudam a decisão seguinte do outro. Nesta primeira entrega, apresentamos o planejamento, os modelos estratégicos e os riscos. A implementação funcional fica para o Trabalho 2.

## Slide 2 — Recorte e regras da interação

**Responsável:** Rafael. **Tempo-alvo:** 55 segundos.

**Na tela:** dois agentes; uma mão; pré-flop → flop → turn → river; fichas virtuais; estado inicial sintético; ações incluídas e principais exclusões.

**Fala sugerida:**

> A mão começa antes do flop, com duas cartas privadas para cada agente e nenhuma comunitária aberta. A coloca small blind de 5 e B, big blind de 10. A age primeiro no pré-flop e B nas etapas seguintes. Permitimos check, bet, call, fold, raise e all-in, com fichas inteiras até o saldo. Um raise indica o total da etapa; call paga apenas a diferença. O mínimo de aposta é 10 e o de aumento acompanha o último aumento completo. All-in pago encerra as decisões de aposta: devolvemos excesso não coberto e revelamos as cartas restantes. O relatório também traz um blefe all-in pré-flop, além do percurso principal com quatro etapas.

**Conferência:** posição, blinds, aumentos e all-in correspondem ao README 1.1. Mostrar que all-in pago conduz à revelação sem novas apostas.

## Slide 3 — Atores, ativo, pressupostos e contexto

**Responsável:** Rafael. **Tempo-alvo:** 70 segundos.

**Na tela:** diagrama de contexto; A e B buscam fichas; motor preserva regras; visão limitada; pressupostos de isolamento, validação e progresso.

**Fala sugerida:**

> Os agentes A e B têm objetivos conflitantes porque disputam o mesmo pote. Suas decisões são intencionais: podem pagar para obter mais informação, desistir para limitar perdas ou apostar para pressionar o adversário. O motor tem outra responsabilidade: preservar a integridade da mão, aplicar as regras e conservar as fichas. Também precisamos preservar o sigilo das cartas privadas e permitir que a interação avance. O diagrama mostra quais informações circulam entre os agentes e o motor. Nossa proposta depende de três pressupostos: cada agente recebe apenas sua visão autorizada, o motor valida as ações e cada decisão termina dentro de um limite de execução. Se esses pressupostos falharem, surgem os riscos que discutiremos. Isso caracteriza uma situação adversarial porque as decisões procuram influenciar e explorar a reação do outro, além de responder ao acaso das cartas.

**Transição:** “O Elton vai mostrar como representamos uma dessas decisões no modelo estático.”

## Slide 4 — Decisão estática e payoffs

**Responsável:** Elton. **Tempo-alvo:** 55 segundos.

**Na tela:** pote 80, aposta 20, hipótese “B vence na revelação”; matriz ordinal 2×2; legenda `(A, B)`; nota “análise retrospectiva: cartas fixadas pelo analista”.

| A / política de B | Pagar se houver aposta | Desistir se houver aposta |
|---|---:|---:|
| Apostar 20 | (0, 3) | (3, 0) |
| Check | (1, 2) | (1, 2) |

**Fala sugerida:**

> Esta matriz analisa o river após o check inicial de B, com pote de 80, aposta de 20 e cartas com as quais B vence. O analista conhece os resultados; os agentes não conhecem as cartas do outro. A aposta ou dá check. B paga ou desiste diante da aposta; check de A encerra a etapa. Raises e all-in continuam permitidos no sistema, mas estão fora desta tabela reduzida. Com call, A perde 20 adicionais e B ganha 100 desse ponto em diante. Com fold, A ganha 80. Com check, A ganha zero e B recebe 80. A tabela converte esses ganhos em preferências. Seu equilíbrio não resolve a incerteza durante a mão.

**Conferência visual:** as contribuições anteriores estão excluídas do ganho líquido desta decisão. Se necessário, revelar as contas numa animação simples ou nas notas; não projetar duas tabelas minúsculas.

## Slide 5 — Melhores respostas, dominância e equilíbrio

**Responsável:** Elton. **Tempo-alvo:** 50 segundos.

**Na tela:** matriz com melhores respostas destacadas; “A: sem dominante”; “B: pagar domina fracamente”; equilíbrio “check / pagar se houver aposta”; ressalva “equilíbrio da tabela retrospectiva”.

**Fala sugerida:**

> Se B paga, A prefere check; se B desiste, A prefere apostar. A não tem estratégia dominante. Para B, pagar é melhor diante da aposta e empata com a outra política diante do check. Assim, pagar domina fracamente: é melhor numa linha e igual na outra. O único equilíbrio puro da tabela é check com a política de pagar. Nenhum jogador melhora mudando sozinho. Check com a política de desistir não é equilíbrio, pois A melhoraria apostando. Esse resultado conserva fichas e usa ações legais. As garantias de sigilo e justiça dependem também dos controles do motor; a matriz sozinha não as comprova.

## Slide 6 — Quatro etapas, observação e adaptação

**Responsável:** Elton. **Tempo-alvo:** 60 segundos.

**Na tela:** quatro etapas desde o pré-flop; raises para 20; B muda de valor para indução; pote 15 → 40 → 80 → 80 → 120; saldos finais A 50 e B 170; all-in alternativo: fold 120/100, call 0/220.

**Fala sugerida:**

> No pré-flop, A aumenta para 20 e B paga, levando o pote a 40. A mantém pressão pequena. No flop, B dá check, A aposta 10 e B aumenta para 20. A paga só mais 10. O pote chega a 80. O raise leva A à cautela; o call leva B a trocar aposta por valor por check visando indução. No turn, B dá check e A também. A interpreta a ausência de nova aposta como fraqueza. No river, B repete check, A blefa 20 e B paga. B vence com trinca: A termina com 50 e B com 170. No ramo all-in pré-flop, fold dá 120 a A; call leva à revelação sem novas apostas e, nesta mesa, B termina com 220.

**Conferência:** mostrar as cartas sintéticas do plano: A = 7♣/2♦; B = A♥/A♦; mesa = A♠/9♥/4♣/K♣/3♦. Indicar que a apresentação mostra a visão do analista, enquanto os agentes não veem as cartas um do outro antes da revelação. Não dizer que foi feita uma simulação executável se o cenário foi apenas calculado manualmente.

**Transição:** “O Frederico vai relacionar essa interação às superfícies de exploração e aos riscos da arquitetura.”

## Slide 7 — Superfície de ataque e natureza das ameaças

**Responsável:** Frederico. **Tempo-alvo:** 45 segundos.

**Na tela:** diagrama de superfície; marcações T1 na visão de estado, T2 na entrada de ações e T3 no executor. Reservar A1/A2 para as ações da matriz.

**Fala sugerida:**

> As quatro etapas usam chamadas de observação, decisão e validação. Nessas mesmas interfaces, analisamos três desvios: T1 expõe cartas na visão de estado; T2 aceita uma ação inválida; T3 deixa uma decisão bloquear a execução. São cenários hipotéticos anteriores aos controles, supondo um agente deliberadamente adversarial e a fraqueza indicada. O diagrama liga cada ponto ao ativo afetado. As etapas apresentadas seguem ações legais; os riscos mostram o que poderia falhar nessas chamadas. Blefar continua permitido. Perder legitimamente é diferente de perder porque o adversário recebeu informação privada ou alterou as regras.

## Slide 8 — Três cenários e avaliação do risco

**Responsável:** Frederico. **Tempo-alvo:** 60 segundos.

**Na tela:** tabela curta; uma frase por cenário; P, I e produto. Manter cenários completos no README.

| ID | Cenário | P | I | Risco |
|---|---|---:|---:|---:|
| T1 | Acesso indevido às cartas do adversário | 3 | 3 | 9 |
| T2 | Ação inválida altera o estado | 3 | 3 | 9 |
| T3 | Decisão não termina e bloqueia a mão | 3 | 2 | 6 |

**Fala sugerida:**

> Avaliamos a chance de a tentativa funcionar supondo que a fraqueza descrita existe. A probabilidade qualitativa é três nos três casos: basta ler o campo exposto, enviar uma ação sem a validação correspondente ou não retornar de uma chamada sem limite. O ambiente local não dificulta essas ações. T1 e T2 recebem impacto três porque expõem cartas ou invalidam a contabilização e as regras. T3 recebe dois porque interrompe a mão, sem necessariamente alterar saldos ou revelar dados. Multiplicando probabilidade por impacto, temos nove, nove e seis. T1 e T2 empatam. As notas são avaliações didáticas condicionais, não medições da frequência de ataques nem prova de que essas falhas já existem no projeto.

**Conferência:** se o grupo alterar riscos ou ameaças, substituir tabela e fala. Não tratar esses números como evidência experimental.

## Slide 9 — Ameaça prioritária e próxima adaptação

**Responsável:** Frederico. **Tempo-alvo:** 60 segundos.

**Na tela:** empate T1/T2 = 9; desempate: sigilo perdido não pode ser recuperado na mão; campo privado → visão filtrada → tentativa pelo histórico → histórico filtrado → sinais públicos/outros canais.

**Fala sugerida:**

> Aprofundamos T1, empatada com T2, porque não é possível fazer o agente esquecer cartas já expostas. Isso não dispensa corrigir T2. Para T1, propomos visões específicas e separação dos registros públicos e internos. Uma reação possível é o agente consultar um campo privado, perceber sua ausência e tentar obtê-lo pelo histórico. O histórico também precisa estar filtrado. Depois, ele pode explorar sinais públicos permitidos ou procurar outro canal de exposição. Essa sequência é uma hipótese de adaptação, não um teste realizado. O controle custa manutenção e pode omitir informação pública por erro, prejudicando agentes legítimos. O risco residual é outro caminho de vazamento; ainda não temos evidência para atribuir uma redução numérica ao risco.

**Transição:** “O Diego vai explicar os demais controles e como essa análise orienta a implementação futura.”

## Slide 10 — Redesenho, custos e resiliência

**Responsável:** Diego. **Tempo-alvo:** 55 segundos.

**Na tela:** três controles ligados aos IDs; uma coluna de custo e outra de risco residual.

**Fala sugerida:**

> O redesenho propõe três controles. Para T1, filtramos visões e históricos. Para T2, validamos vez, ação, saldo e limites antes de atualizar o estado. Para T3, encerramos decisões que excedem um orçamento e aplicamos check quando legal ou fold quando existe aposta pendente. Um adversário pode procurar outra saída de dados ou passar a responder logo antes do limite. A defesa também custa: um erro pode rejeitar uma ação válida, e um agente legítimo lento pode sofrer fold automático. Por isso, precisamos registrar o motivo de cada resposta e verificar seus efeitos. A resiliência consiste em continuar preservando as propriedades da mão diante dessas adaptações; não pressupõe eliminar todo comportamento adversarial.

## Slide 11 — Arquitetura e critérios para o Trabalho 2

**Responsável:** Diego. **Tempo-alvo:** 55 segundos.

**Na tela:** motor e estado → visões → agentes → validação → atualização e registro; executor envolvendo a chamada; quatro critérios verificáveis.

**Fala sugerida:**

> A arquitetura possui motor e estado, provedor de visões, agentes, validador, executor e registro. Além de vez e saldo, o estado guarda contribuição por etapa, valor a pagar e último aumento completo. O motor calcula quanto transferir em raise, call e all-in. All-in pago devolve excesso e entra em runout sem pedir novas apostas. No Trabalho 2, verificaremos o percurso até 50 e 170, os ramos de all-in, conservação de fichas, sigilo e rejeição de ações inválidas. Os registros privados ligam observação, política anterior, política atual e custo, sem entregar o raciocínio ao oponente. São critérios planejados para implementação futura.

## Slide 12 — Pergunta final, fontes e participação

**Responsável:** Diego. **Tempo-alvo:** 55 segundos.

**Na tela:** pergunta final; resposta em uma frase; duas ou três referências efetivamente usadas; declaração curta de IA; link do repositório e indicação das contribuições reais.

**Fala sugerida:**

> Depois da resposta, o outro lado aprende com as ações e os limites observáveis. Pode mudar seus blefes, suas decisões de pagar ou o caminho pelo qual procura informação. Apesar disso, o sistema precisa preservar sigilo, fichas e progresso da mão. As fontes que sustentam regras e conceitos ficam no relatório, junto da declaração das tarefas em que usamos IA. A adaptação dos dois agentes, os limites da matriz e os critérios dos riscos fazem parte da análise. As contribuições registradas estão no relatório e no Git. Essa análise será a base da implementação no Trabalho 2.

## Como produzir os slides

1. Finalizar primeiro o README, as contas, os dados sintéticos e os diagramas. Revisar em conjunto as escolhas que diferem da base original.
2. Criar uma apresentação com o formato e os 12 slides acima. Canva é a preferência indicada pelo enunciado; outra ferramenta pode ser usada se atender aos entregáveis.
3. Inserir títulos e elementos visuais; deixar as falas nas notas. Evitar capturas pequenas do README.
4. Apresentar a matriz com destaque visual das melhores respostas e mostrar a ordem dos pares. Não usar cores como única forma de distinguir A e B.
5. Mostrar as quatro etapas numa sequência, com os saldos e o sinal observado. Não depender apenas de animações: a versão PDF também precisa ser compreensível.
6. Dividir o diagrama de superfície e a tabela de risco em slides distintos para manter legibilidade.
7. Colocar citações curtas junto aos conceitos e às regras apoiadas por fontes. Manter referências completas no README e em `fontes/referencias.md`.
8. Exportar PDF, abrir o arquivo e conferir todos os slides, inclusive acentos, tabelas, cortes e links. Guardar em `apresentacao/slides.pdf`.
9. Registrar o link da apresentação editável e o link acessível do PDF quando existirem.

## Ensaio antes de gravar

Fazer uma passagem completa com cronômetro e registrar o tempo de cada bloco. A distribuição planejada soma 11 minutos, mas a duração real depende de fala, pausas e transições. Buscar 2min45s por integrante. Se ultrapassar, cortar repetições antes de aumentar a velocidade. Preservar a limitação da matriz, a adaptação dos dois agentes, as justificativas dos riscos e o desempate da prioridade.

Cada integrante deve conseguir explicar, sem ler:

- Por que o caso é adversarial e por que os agentes são de software.
- Qual informação é privada e o que o motor preserva.
- Como calcular cada payoff e identificar as melhores respostas.
- Por que a dominância de B é fraca e pertence à matriz retrospectiva, sem definir a decisão ótima durante a mão.
- Qual observação muda a política de cada agente e por que B dá check após observar a iniciativa de A.
- A diferença entre blefe permitido e violação das propriedades do sistema.
- Como P e I foram justificadas separadamente, por que T1 e T2 empatam e qual critério desempata a análise aprofundada.
- O que será implementado no Trabalho 2 e o que ainda é apenas proposta.

Se alguém não consegue explicar uma escolha, revisar o modelo antes de decorar a fala. A transcrição destaca domínio do conteúdo e participação equilibrada, além da presença no vídeo.

## Como gravar

1. **Preparar o ambiente.** Usar local silencioso, microfone próximo e posição estável. Fechar notificações e outras janelas que possam aparecer sobre os slides. Confirmar que a versão aberta é a final.
2. **Testar som e imagem.** Gravar um trecho de 20 a 30 segundos com uma fala e a matriz na tela. Reproduzir para conferir volume, ruído e legibilidade. Buscar 1080p se a ferramenta permitir; isso é uma sugestão de qualidade, não requisito do enunciado.
3. **Escolher a forma de gravação.** Preferir a gravação da apresentação no Canva, conforme orientação do enunciado. Usar os recursos disponíveis da conta para narrar e apresentar os slides. Se a gravação conjunta não estiver disponível, cada integrante grava seu bloco, e o grupo reúne os quatro na ordem definida.
4. **Manter padrão entre blocos.** Usar a mesma apresentação, proporção, resolução e volume aproximado. Deixar uma pequena pausa entre integrantes para facilitar os cortes. Se usar câmera, posicioná-la sem cobrir tabelas ou diagramas; o enunciado consultado não exige câmera.
5. **Gravar as transições.** Rafael apresenta Elton; Elton apresenta Frederico; Frederico apresenta Diego. Não deixar repetidas apresentações do grupo entre os blocos.
6. **Corrigir erros relevantes.** Se houver erro de conceito, número ou nome, repetir o trecho e retirar a versão incorreta na edição. Cortar pausas excessivas sem acelerar artificialmente as falas.
7. **Exportar e guardar.** Gerar um arquivo de vídeo, por exemplo MP4, e manter uma cópia local antes de publicar. Evitar adicionar um vídeo grande ao repositório Git.
8. **Revisar o arquivo exportado inteiro.** Conferir início, transições, áudio dos quatro, leitura da matriz, valores do risco, encerramento e duração. Não confiar apenas na prévia do editor.

Se a gravação do Canva não funcionar, usar uma ferramenta de gravação de tela disponível e manter slides, narração e vídeo final. O entregável é o vídeo com slides acessível pelo YouTube; a preferência de ferramenta não deve impedir sua produção.

## Publicação e submissão

1. Publicar o vídeo no YouTube com um título claro, como “Grupo 7 — Poker Adversarial — Trabalho 1”. Não marcar como privado se isso impedir acesso dos professores. Confirmar se a disciplina determina uma visibilidade específica; sem orientação adicional, considerar link não listado acessível a quem o possui.
2. Na descrição, incluir identificação do trabalho e links reais do repositório e do PDF. Não inventar resultados ou afirmar que o sistema já está implementado.
3. Esperar o processamento necessário e testar a reprodução, o áudio e a legibilidade dos slides.
4. Abrir o vídeo e o PDF em uma sessão sem autenticação do autor. Conferir acesso ao repositório conforme o formato exigido pela disciplina.
5. Registrar os links reais em `apresentacao/links.md` e no README. Links do projeto editável servem como fonte; o link de PDF é um entregável distinto.
6. Submeter os links no ambiente indicado pelos professores antes de 06/10 às 23h59. O campo exato de submissão deve ser conferido no ambiente da disciplina.

## Perguntas prováveis para preparar respostas

| Pergunta | Elementos que a resposta deve explicar |
|---|---|
| Por que isso é um sistema de software adversarial? | Dois agentes decidem, disputam fichas e adaptam políticas; o motor medeia a interação. |
| O motor precisa vencer um jogador? | Não; sua responsabilidade é preservar regras, estado e propriedades da mão. |
| Por que usar quatro etapas de uma mão? | O pré-flop mostra a decisão sem comunitárias. Flop, turn e river conectam observação e adaptação, atendendo a pelo menos três ciclos. |
| O all-in pré-flop não elimina as rodadas seguintes? | Se houver call, elimina novas decisões de aposta. Por isso, é um ramo alternativo ao percurso principal de quatro etapas. |
| Raise para 20 transfere sempre 20? | Não. O total desejado menos a contribuição já feita determina a transferência. A transfere 15 no pré-flop; no flop, paga apenas mais 10 ao raise de B. |
| E quando os saldos são diferentes? | Devolver a parte não coberta antes da comparação. No ramo 110/70 antes dos blinds, devolver 40 e disputar apenas 140. |
| Como B sabe que vence na matriz? | Não recebe cartas de A; a vitória de B é condição do exemplo usada na análise. O modelo não resolve toda a informação incompleta. |
| Por que o river observado não termina no equilíbrio da matriz? | O equilíbrio pertence à análise retrospectiva de um cenário fixo; A usa uma heurística sob informação parcial e interpreta o check de B incorretamente. |
| Onde B realmente se adapta? | Depois da iniciativa de A, troca a intenção de apostar com mão forte por check para tentar induzir outra aposta; pode perder ganho ou permitir melhora da mão adversária. |
| Por que pagar domina apenas fracamente? | É melhor diante da aposta e igual diante do check nas condições definidas. |
| Check com a política de desistir é equilíbrio? | Não: A melhora mudando sozinho para apostar. |
| Por que A perdeu e o sistema ainda está correto? | A derrota é resultado permitido; regras, sigilo e conservação podem continuar preservados. |
| Os riscos foram medidos? | Não; são avaliações qualitativas justificadas de um protótipo hipotético. |
| Por que a probabilidade de bloqueio não é baixa num teste local? | Sem limite, não retornar da decisão bloqueia diretamente a chamada; o alcance local não dificulta a exploração. |
| Por que aprofundar T1 se T2 tem a mesma pontuação? | As duas exigem controle; T1 é escolhida para aprofundamento porque a informação privada exposta não pode ser esquecida durante a mão. |
| Por que não bloquear blefes? | O blefe pertence às regras; o controle trata acesso indevido, ações inválidas e bloqueio do progresso. |
| O que resta após filtrar a visão? | Inferência legítima por dados públicos e possível exposição indevida em outros canais. |
| Como provar participação de todos? | Fala equilibrada, domínio do conjunto, contribuições reais e histórico individual identificável. |

## Checklist do vídeo

- [ ] Roteiro atualizado de acordo com o README final, sem decisões divergentes.
- [ ] Cartas sintéticas, resultado, saldos e contas conferidos.
- [ ] Referências reais e declaração de IA fiel ao uso.
- [ ] Ensaio próximo de 11 minutos; quatro blocos próximos de 2min45s, com pausas e transições.
- [ ] Matriz retrospectiva distinguida da mão sob informação parcial; adaptação de ambos os agentes explicada.
- [ ] Três ameaças T1/T2/T3, notas 9/9/6, justificativas separadas e desempate apresentados conforme o plano adotado.
- [ ] Defesa, tentativa seguinte, custo e risco residual apresentados sem prometer eficácia já medida.
- [ ] Arquitetura futura e pergunta final respondidas.
- [ ] Nenhuma proposta apresentada como teste ou implementação concluída.
- [ ] PDF legível e vídeo completo revisado após a exportação.
- [ ] Links acessíveis e submissão concluída dentro do prazo.
