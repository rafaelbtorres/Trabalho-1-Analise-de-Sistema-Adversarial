# Plano de produção da primeira entrega

Este documento organiza o trabalho solicitado em `enunciado/Apresentação de Trabalhos.md`. A modelagem abaixo foi incorporada ao README e aos materiais locais em 05/10/2026. Os exemplos são sintéticos e a revisão humana do grupo permanece pendente. As etapas servem também como critérios para revisar o material antes da gravação.

## Estado da produção

| Item | Situação local |
|---|---|
| Relatório, fontes e cenário numérico | Preenchidos no README, em `fontes/referencias.md` e em `dados/cenario.json` |
| Três diagramas | Fontes Mermaid e imagens PNG produzidas |
| Apresentação | 12 slides em `apresentacao/slides.pptx` e `apresentacao/slides.pdf`, com falas nas notas do PPTX |
| Roteiro | Falas, divisão equilibrada e instruções de gravação em `docs/roteiro-video.md` |
| Revisão humana e contribuições | Grupo precisa conferir decisões e registrar trabalho efetivamente realizado |
| Vídeo, links externos e submissão | Pendentes, acompanhados em `apresentacao/links.md` |

A preparação dos arquivos locais não conclui a entrega formal. É necessário revisar, gravar os quatro integrantes, publicar o vídeo e submeter os links reais.

## Resultado esperado

Até o prazo informado de **06/10 às 23h59**, entregar um repositório que permita compreender a análise pelo README, três diagramas completos com fontes editáveis, referências reais, apresentação em PDF e vídeo no YouTube. Confirmar no ambiente da disciplina os campos de submissão e eventuais orientações adicionais.

O enunciado não fixa duração do vídeo. A meta deste plano é **11 minutos**, com **2min45s por integrante**, incluindo transições e pausas. O roteiro distribui exatamente 660 segundos entre os slides; o ensaio deve confirmar o tempo real. Ajustar se houver orientação adicional do professor.

## Arquivos a produzir

| Arquivo ou artefato | Conteúdo | Critério de conclusão |
|---|---|---|
| `README.md` | Relatório completo nas seções existentes | Sem instruções de preenchimento; análise coerente e pergunta final respondida |
| `diagramas/contexto.mmd` e `.png` | Agentes, motor, dados e interações | Participantes, fronteiras e informações autorizadas identificados |
| `diagramas/ciclo-adaptativo.mmd` e `.png` | Três rodadas e dependências entre elas | Ação, resposta, observação, adaptação e custos visíveis |
| `diagramas/superficie-de-ataque.mmd` e `.png` | Interfaces, componentes, fraquezas e ativos | Os três cenários de ameaça têm pontos localizáveis |
| `fontes/referencias.md` | Fontes reais e dados sintéticos | Cada fonte usada aparece citada no relatório |
| `apresentacao/slides.pdf` | Versão final dos slides | Legível, consistente com o README e com link acessível |
| `apresentacao/links.md` | Links do PDF, fonte editável dos slides e vídeo | URLs reais, acesso testado e sem links provisórios |
| Projeto editável da apresentação | Fonte no Canva ou ferramenta escolhida | Grupo consegue editar e recuperar a versão apresentada |
| Vídeo publicado no YouTube | Apresentação com voz dos quatro integrantes | Áudio compreensível, slides legíveis e reprodução acessível |

A pasta `apresentacao/` contém os arquivos locais. Não criar links fictícios nem registrar publicação antes de realizá-la. O arquivo local de vídeo é recomendado como cópia de segurança, mas não é necessário colocá-lo no Git.

## Etapa 1 — Fechar o recorte e as regras

Preencher README 1 e 2 antes de calcular a matriz. Sugestão de recorte:

- Simulação local com agentes A e B; sem dinheiro real, contas públicas ou conexão com plataformas externas.
- Uma mão, com três etapas de decisão chamadas flop, turn e river. O estado de preparação, inclusive cartas privadas e pote inicial, é uma entrada sintética previamente definida.
- Não analisar decisões anteriores ao estado inicial. Explicar que o recorte começa após a preparação da mão.
- Agentes observam cartas próprias, cartas públicas, pote, saldos públicos, ações e histórico público. Cartas privadas do adversário ficam ocultas até a revelação final prevista pelas regras.
- A fronteira proposta é a interface de observação e ação. Agentes não recebem acesso livre ao estado interno, à memória ou aos arquivos do motor. Se o Trabalho 2 executar código externo não confiável, a arquitetura precisará de isolamento de execução adicional; filtrar um objeto não é suficiente para esse cenário.
- Motor controla ordem de ação, validade, atualização do pote e encerramento.
- Para reduzir complexidade, propor apostas de 10 ou 20 fichas, estritamente menores que o saldo disponível de cada agente, e excluir aumentos, re-aumentos, all-in e potes paralelos neste recorte. Uma etapa admite no máximo uma aposta seguida de call ou fold. Se nenhum valor for permitido, resta check quando não há aposta pendente. Se o grupo mantiver ações adicionais, especificar seus estados e custos e revisar todos os exemplos.
- Definir check quando não houver aposta a pagar, bet para iniciar aposta, call para igualar e fold para desistir. Determinar explicitamente o que acontece após fold, duas ações check e aposta seguida de call.
- Adaptação ocorre dentro da mão com base no histórico disponível. Não afirmar aprendizagem estatística confiável com apenas três observações.

Ativo principal sugerido: **integridade da mão**, incluindo aplicação das regras e conservação de fichas. Propriedades adicionais: sigilo das cartas privadas e progresso da interação.

Preencher a tabela de atores: objetivo, ações, informação e custo. Formular pelo menos dois pressupostos, preferencialmente três: isolamento das visões; validação pelo motor; resposta do agente dentro de um limite de execução.

**Concluída quando:** outra pessoa consegue explicar quem decide, quem vê o quê, quais ações são legais e quando a mão termina.

## Etapa 2 — Modelo estático com contas verificáveis

Escolher uma decisão e declarar as condições. O exemplo a seguir é um ponto de partida coerente, não uma análise geral do poker.

Estado hipotético: river, pote de 40 fichas, aposta possível de 20. Se houver revelação, B vence. A matriz é uma **análise retrospectiva de um cenário fixo**, construída com as cartas conhecidas pelo analista. Para analisar dominância e equilíbrio, tratamos a tabela de resultados como o jogo reduzido de informação completa. Durante a mão descrita no modelo dinâmico, os agentes continuam sem conhecer as cartas do outro.

Essa distinção deve aparecer antes da matriz no relatório: o equilíbrio calculado caracteriza a tabela reduzida, não a decisão ótima dos agentes sob informação parcial. Resolver esta última exigiria especificar crenças sobre mãos possíveis e avaliar seus resultados, algo que não é necessário para este recorte da primeira entrega.

- A1: apostar 20 como blefe.
- A2: dar check e seguir para revelação, supondo que B também dá check quando não enfrenta aposta.
- B1: política de pagar a aposta de A; se A der check, seguir para revelação.
- B2: política de desistir diante da aposta de A; se A der check, seguir para revelação.

As colunas são políticas condicionais. Portanto, não executar fold ou call quando não existe aposta. Essa convenção permite representar o fluxo sequencial numa matriz estática reduzida. A opção de B apostar após o check de A foi excluída apenas desta matriz; no fluxo geral da mão, B pode iniciar uma aposta após esse check. Não confundir a restrição da análise 2×2 com as regras do motor.

Ganhos líquidos **a partir deste ponto de decisão**, excluindo contribuições anteriores já incorporadas ao pote:

| A / política de B | B1: pagar se houver aposta | B2: desistir se houver aposta |
|---|---:|---:|
| A1: apostar 20 | (-20, +60) | (+40, 0) |
| A2: check | (0, +40) | (0, +40) |

Exemplo de cálculo: se B paga, o pote chega a 80. A perde as 20 fichas adicionais; B recebe 80 após investir 20, ganhando 60 a partir deste estado. A soma dos ganhos é 40, o pote já existente no início da decisão.

Convertendo cada jogador para preferências ordinais de 0 a 3:

| A / política de B | B1 | B2 |
|---|---:|---:|
| A1 | (0, 3) | (3, 0) |
| A2 | (1, 2) | (1, 2) |

Para A, ganhar 40 é melhor que ganhar 0, que é melhor que perder 20. Para B, ganhar 60 é melhor que ganhar 40, que é melhor que ganhar 0. Não é preciso usar todos os números da escala para cada jogador.

Análise incorporada ao README, a conferir na revisão humana:

- Diante de B1, A prefere A2; diante de B2, A prefere A1.
- Diante de A1, B prefere B1; diante de A2, B é indiferente entre B1 e B2.
- A não tem estratégia dominante. B1 domina **fracamente** B2 neste cenário, porque é melhor em uma linha e igual na outra; não há dominância estrita.
- O único equilíbrio em estratégias puras é (A2, B1). A não melhora mudando sozinho e B não melhora mudando sozinho.
- (A2, B2) não é equilíbrio: A melhoraria mudando para A1.
- O resultado de equilíbrio usa ações legais e conserva fichas, mas a matriz não verifica isolamento de informações nem garante justiça por si só. Essas propriedades dependem dos controles da arquitetura. Não implica que pagar seja sempre a melhor decisão quando as cartas são incertas.

No exemplo dinâmico, a combinação observada no river será (A1, B1), fora desse equilíbrio retrospectivo. Isso é intencional: A decide com informação parcial e interpreta incorretamente um sinal de B. A narrativa ilustra uma decisão possível de agentes com heurísticas limitadas, sem afirmar que ambos jogam de forma ótima ou que convergem ao equilíbrio em três rodadas.

**Concluída quando:** os quatro resultados têm explicação, as quatro melhores respostas foram conferidas e o relatório e os slides distinguem equilíbrio da tabela retrospectiva de decisões durante a mão.

## Etapa 3 — Três rodadas realmente conectadas

Exemplo proposto para ligar o modelo dinâmico ao estado da matriz:

Estado inicial: A e B possuem 100 fichas disponíveis cada um; o pote contém 20 de contribuições anteriores de 10 por jogador. Antes da preparação, cada jogador tinha 110. Total conservado: 220 fichas.

Políticas iniciais ilustrativas: A testa pressão pequena com sua mão fraca. B, com mão forte, normalmente aposta quando recebe check, mas pode mudar para uma linha de indução se observar iniciativa do adversário. São regras heurísticas propostas, não estratégias ótimas demonstradas.

| Rodada | Ação e resposta | O que A observa e muda | O que B observa e muda | Pote e saldos após as ações |
|---|---|---|---|---|
| 1 — flop | A aposta 10; B paga 10 | O call de B mostra resistência à pressão pequena. A troca a iniciativa por check na próxima etapa para observar a reação. | A abriu a aposta. B passa da intenção de apostar quando receber check para tentar induzir outra aposta de A, preservando uma aparência de cautela. | Pote 40; A 90; B 90 |
| 2 — turn | A dá check; B também dá check | A vê o check de B e o interpreta como possível fraqueza; prepara uma pressão de 20 no river. Não tem certeza sobre a mão de B. | B vê A recuar após receber call e executa a política de indução: dá check em vez de iniciar a aposta que sua política anterior escolheria. Planeja pagar uma nova aposta se sua mão continuar forte. | Pote 40; A 90; B 90 |
| 3 — river | A blefa apostando 20; B paga 20 | A testa a hipótese de fraqueza. O novo call e a revelação mostram que a leitura falhou; uma próxima interação pode motivar menos blefes nesse padrão. | B vê A voltar a apostar após o check e paga com sua mão forte. A revelação confirma um blefe neste caso, sem provar que toda sequência semelhante será blefe. | Antes da revelação: pote 80; A 70; B 70 |

Para tornar a adaptação verificável no Trabalho 2, prever em cada registro de decisão: **observação pública relevante → política anterior → política atual → ação e custo**. Esses registros são internos e não devem entregar cartas ou raciocínio privado ao oponente. O contraste central é: sem a iniciativa observada de A, a política inicial de B apostaria no turn; após observá-la, B escolhe check para tentar induzir ação futura.

Na revelação do exemplo, B vence: A termina com 70, B com 150, pote zero. Total: 220. A sugestão de mudança após a terceira rodada é uma hipótese de reação, não uma quarta rodada já executada nem ampliação automática do escopo.

Cartas sintéticas incorporadas: A recebe **7♣ e 2♦**; B recebe **A♥ e A♦**; flop **A♠, 9♥, 4♣**; turn **K♣**; river **3♦**. Não há cartas repetidas. Na revelação, B tem trinca de ases e A não forma par, sequência ou flush. Até a revelação, cada agente vê apenas suas cartas e as comunitárias já abertas. O check de B na segunda etapa é uma decisão de estratégia, não evidência de mão fraca. A classificação foi conferida com as regras citadas e por enumeração das combinações de cinco cartas; o grupo ainda deve compreender e revisar esse resultado.

Descrever custos: A expõe fichas e pode interpretar sinais de forma errada; B paga para continuar e, ao dar check com mão forte, pode perder a oportunidade de receber outra aposta ou permitir que a próxima carta favoreça A. A resposta de B ao blefe tem custo e risco mesmo sendo uma ação legítima. A escalada de 10 para 20 ilustra aumento de pressão; uma corrida armamentista sustentada seria uma possibilidade em interações repetidas, não um fenômeno demonstrado por esta única mão.

**Concluída quando:** cada mudança tem uma observação identificável, as fichas fecham e fica claro que ambos podem adaptar sua estratégia.

## Etapa 4 — Superfície, ameaças e riscos

Conectar ameaças à mesma arquitetura usada pelas três rodadas. Manter **T1/T2/T3** para ameaças, evitando confusão com as ações A1/A2 da matriz, conforme o README atual.

**Ambiente e momento da avaliação:** protótipo local hipotético, antes dos controles, com um agente deliberadamente adversarial que controla os dados retornados e o tempo de sua decisão. Cada cenário supõe a existência da fraqueza indicada; não se afirma que ela já existe em código. A mão da etapa 3 é o caminho de ações legais. As ameaças são desvios possíveis nas mesmas chamadas de observação, decisão e validação.

Nesta avaliação qualitativa, P representa a probabilidade de sucesso de uma tentativa **condicionada à fraqueza descrita estar presente**. Não representa a frequência de ataques reais nem a probabilidade de o projeto vir a conter aquela falha.

| Nota | Probabilidade qualitativa de sucesso (P) | Impacto se a tentativa funcionar (I) |
|---|---|---|
| 1 | Depende de condições adicionais difíceis de obter no ambiente proposto | Incômodo restrito, sem comprometer estado, sigilo ou conclusão da mão |
| 2 | Depende de uma sequência ou de um estado específico da interação | Interrompe o progresso e exige reinício, com estado e sigilo preservados |
| 3 | A interface permite explorar diretamente a fraqueza, sem condição adicional relevante | Expõe cartas privadas ou altera fichas/regras, invalidando a justiça do resultado |

| ID | Cenário proposto e ponto de exploração | Justificativa de P | Justificativa de I | Risco |
|---|---|---|---|---:|
| T1 | Um agente pode ler cartas privadas do oponente na visão de estado, aproveitando campos sem filtragem, obtendo vantagem indevida e violando sigilo e justiça. | **3:** se o campo privado já foi entregue, basta lê-lo. | **3:** a informação revelada muda a decisão e não pode ser tornada desconhecida durante a mão. | **9** |
| T2 | Um agente pode enviar aposta maior que seu saldo ou ação fora da vez pela entrada de ações, aproveitando falta de validação, corrompendo saldos ou ordem de jogo. | **3:** sem a verificação correspondente, basta enviar uma ação inválida. | **3:** aceitar a ação invalida as regras e a contabilização do resultado. | **9** |
| T3 | Um agente pode não retornar sua decisão na chamada de execução, aproveitando ausência de limite, impedindo o avanço da mão. | **3:** uma decisão que não termina bloqueia diretamente uma chamada sem orçamento. | **2:** a mão fica parada e exige interrupção/reinício, sem necessariamente expor cartas ou modificar saldos. | **6** |

As notas são avaliações didáticas propostas, sem medições. O fato de o sistema ser local limita o alcance do dano, mas **não torna difícil** ler um campo exposto, enviar uma ação inválida ou bloquear uma chamada sem limite. Reavaliar as notas se mudarem as capacidades, os pressupostos ou as propriedades afetadas.

**Prioridade sugerida: T1, empatada com T2 em 9.** O desempate para aprofundamento no relatório é a impossibilidade de desfazer o conhecimento das cartas já expostas naquela mão. T2 também exige controle antes de qualquer execução válida; a ordem de apresentação não justifica adiá-lo. Um reinício a partir de um estado confiável pode recuperar a contabilização, mas não faz o agente esquecer a informação privada.

Resposta a T1: entregar uma visão filtrada por agente e separar registros públicos de registros internos. Custo para agentes legítimos: um filtro incorreto pode omitir informação pública necessária, além do custo de manter e verificar as regras de acesso. Risco residual: uma segunda saída de dados pode continuar expondo informações privadas. Não atribuir redução numérica do risco sem explicitar novas premissas ou evidência.

Acrescentar uma sequência curta de reação à defesa, distinta das três rodadas de apostas: o agente tenta consultar um campo privado → recebe uma visão que não o contém → procura o mesmo dado no histórico público → encontra um histórico também filtrado → passa a explorar padrões de apostas permitidos ou procura outro canal de exposição. Essa é uma hipótese de adaptação após o redesenho, não uma execução já observada. Os controles devem cobrir visão e histórico para sustentar a sequência proposta.

Controles complementares: validação central e atualização consistente para T2; orçamento de execução e resposta padrão previamente definida para T3. Proposta de resposta padrão: check quando legal, ou fold quando houver aposta pendente. O executor deve conseguir encerrar a execução que ultrapassou o orçamento; especificar esse mecanismo no desenho do Trabalho 2. Descrever custos, como rejeitar uma ação legítima por erro ou forçar fold de um agente legítimo lento, e a adaptação possível de um adversário que passa a responder logo antes do limite.

Se o grupo preferir ameaças estratégicas, substituir por explorações concretas de políticas previsíveis e ajustar ativos, controles, diagramas e roteiro juntos. Não afirmar que bloquear blefes é a defesa da justiça do jogo.

**Concluída quando:** cada ameaça está no diagrama, tem fraqueza, ativo, consequência, justificativa do risco, controle e possibilidade de reação posterior.

## Etapa 5 — Arquitetura, diagramas e resiliência

Preencher README 6 e 7 com componentes sugeridos:

| Componente | Responsabilidade | Relação com a análise |
|---|---|---|
| Estado e motor da mão | Etapas, cartas, ordem, saldos, pote e resultado | Integridade, regras e transições |
| Provedor de visões | Filtrar informação de cada agente | T1 e sigilo |
| Agentes A e B | Decidir a partir da visão e do histórico autorizado | Conflito e adaptação |
| Validador de ações | Conferir vez, saldo, ação e limites | T2 |
| Executor de decisões | Aplicar orçamento de execução e resposta padrão | T3 |
| Registro de eventos | Permitir auditoria com separação de acesso | Observabilidade sem vazamento |

Critérios verificáveis para o Trabalho 2: executar o cenário de três rodadas; conservar 220 fichas no exemplo; impedir acesso a cartas do outro agente antes da revelação permitida; rejeitar ações inválidas sem alterar o estado; aplicar o limite de execução; registrar a observação que motivou cada decisão.

Especificar quais decisões o agente adapta e quais regras do motor permanecem invariantes. Neste recorte, o motor pode ter controles fixos; não prometer mudança automática de regras durante a mão. A adaptação estratégica está nos agentes, e mudanças futuras nos controles são propostas de redesenho.

Nos diagramas: contexto mostra atores e fronteiras; ciclo mostra a cadeia das três rodadas, incluindo a mudança de política de B; superfície mostra T1/T2/T3 nas mesmas interfaces de visão, ação e execução usadas durante a mão. Usar mesmos nomes e IDs no relatório, nos diagramas e nos slides. Exportar PNG, conferir leitura e manter os `.mmd` atualizados.

## Etapa 6 — Evidências e integração do relatório

- Buscar fontes verificáveis para regras adotadas, melhores respostas/equilíbrio e modelagem de ameaças. Preferir documentação oficial, bibliografia da disciplina e fontes primárias. Registrar título, autor/organização, URL e acesso real.
- Citar a fonte junto da afirmação sustentada, além da lista de referências.
- Identificar fichas, cartas, agentes e cenários como dados sintéticos.
- Atualizar a declaração de IA com as tarefas efetivamente realizadas, inclusive contexto, plano e roteiro, se utilizados.
- Registrar como o grupo realmente verificou conceitos e contas. Não antecipar uma validação que não ocorreu.
- Preencher contribuições individuais com trabalho realizado e commits reais.
- Responder à pergunta final relacionando sinais públicos, tentativa seguinte e propriedade preservada.
- Remover campos de preenchimento do README e das referências. Campos pendentes nos documentos de processo devem permanecer explicitamente pendentes até serem resolvidos.

## Etapa 7 — Slides, ensaio, vídeo e submissão

Usar `docs/roteiro-video.md` para produzir os slides, adaptar as falas à versão final, ensaiar, gravar, revisar, exportar e publicar. Não gravar com matriz ou riscos ainda divergentes do relatório.

Testar links do PDF e do vídeo numa sessão sem autenticação do autor. Confirmar que o professor consegue acessar o repositório. Registrar os links reais em `apresentacao/links.md` e referenciá-los no README.

## Divisão sugerida e ordem de trabalho

Esta divisão é uma proposta de organização; não representa contribuição já realizada.

| Integrante | Produção principal sugerida | Revisão cruzada | Fala sugerida |
|---|---|---|---|
| Rafael | Recorte, atores, pressupostos e contexto | Coerência da arquitetura | Slides 1–3 |
| Elton | Matriz, melhores respostas e três rodadas | Conferência de saldos e payoffs | Slides 4–6 |
| Frederico | Superfície, ameaças, avaliação e prioridade | Rastreabilidade dos controles | Slides 7–9 |
| Diego | Resiliência, arquitetura e integração dos slides | Links, referências e checklist | Slides 10–12 |

Cada integrante deve revisar a análise completa e conseguir responder perguntas além de sua parte. Fazer commits incrementais de contribuição real, evitando concentrar todo o histórico num envio final. A distribuição de referências, revisão e gravação deve envolver os quatro.

Considerando a revisão em 05/10: reservar o primeiro bloco para regras e modelo, o seguinte para ameaças e arquitetura, e o último para integrar o relatório e os diagramas. Em 06/10, reservar tempo para slides, ensaio, gravação, publicação e conferência de acesso antes das 23h59. Não deixar upload e correções para os minutos finais.

## Conferência final

Os itens marcados registram a preparação local e as conferências assistidas por IA. A aprovação humana e a entrega formal continuam pendentes.

- [x] README completo, sem campos de modelo e com recorte explícito.
- [x] Agentes de software, ativos, capacidades, informações, custos e pressupostos definidos.
- [x] Matriz justificada, quatro melhores respostas, dominância e equilíbrio conferidos; análise retrospectiva distinguida das decisões sob informação parcial.
- [x] Três rodadas conectadas; cartas e fichas consistentes; mudança de política dos dois agentes explicitada.
- [x] Três diagramas específicos, legíveis e acompanhados dos fontes editáveis.
- [x] Três pontos de exploração e três ameaças rastreáveis.
- [x] Probabilidade e impacto justificados separadamente; riscos 9/9/6 e desempate da prioridade coerentes em todos os artefatos.
- [x] Defesa prioritária, informação revelada, adaptação, custos e risco residual explicados.
- [x] Arquitetura descrita e critérios de sucesso verificáveis para o Trabalho 2.
- [x] Referências reais citadas e declaração de IA fiel ao uso.
- [x] PDF e PPTX produzidos e conferidos, com 12 slides e falas nas notas.
- [ ] Revisão humana e domínio das decisões confirmados pelos quatro integrantes.
- [ ] Contribuições registradas de acordo com o trabalho real.
- [ ] PDF e vídeo consistentes; ensaio próximo de 11 minutos, com 2min45s por integrante.
- [ ] Links e reprodução testados; entrega submetida no ambiente indicado pela disciplina.
