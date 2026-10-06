# Contexto para IAs e colaboradores

## Objetivo do repositório

Este é o Trabalho 1 da disciplina Engenharia de Software Adversarial, do Grupo 7. A entrega é uma análise de um sistema adversarial, com planejamento e desenho arquitetural para uma implementação posterior no Trabalho 2.

O tema já escolhido é **Poker Adversarial**. O README delimita dois jogadores, uma mão desde o pré-flop com até quatro etapas, raise e all-in, fichas virtuais e informações parcialmente ocultas. O recorte foi ampliado em 06/10/2026 por solicitação do usuário após as sugestões do Elton. Preserve esse tema e recorte, salvo orientação posterior do grupo.

Integrantes: Rafael Barboza Torres, Elton Henrique Lunardi Gimenes, Frederico Marques da Silva Barcelos e Diego Santos de Araujo.

## Fontes e ordem de leitura

1. `enunciado/Apresentação de Trabalhos.md`: requisitos formais e rubrica.
2. `enunciado/trancricao_video_enunciado.md`: explicações complementares do professor. A transcrição contém erros de reconhecimento; use o enunciado formal para resolver ambiguidades de redação.
3. `README.md`: relatório principal e decisões efetivamente incorporadas ao projeto.
4. `docs/plano-primeira-entrega.md`: sequência de produção, proposta de modelagem e critérios de conclusão.
5. `docs/roteiro-video.md`: conteúdo dos slides, falas sugeridas e processo de gravação.
6. `diagramas/*.mmd`, `diagramas/diagramas-editaveis.pptx` e `fontes/referencias.md`: diagramas editáveis e evidências. Os PNGs foram exportados da fonte visual PPTX; os Mermaid representam o mesmo conteúdo, com disposição própria.

As instruções do usuário prevalecem sobre este arquivo. O README contém a modelagem atual, preparada com assistência de IA e ainda sujeita à revisão humana do grupo. A arquitetura é especificação para o Trabalho 2, não resultado de implementação.

## Requisitos da primeira entrega

- Prazo informado pelo enunciado: **06/10 às 23h59**. O arquivo não explicita o ano; o contexto desta revisão é 05/10/2026.
- Relatório principal em Markdown no repositório Git.
- Apresentação em slides, com link para a versão PDF.
- Vídeo gravado com slides, disponibilizado no YouTube; Canva é a plataforma preferida pelo enunciado, sem obrigatoriedade indicada.
- Participação equilibrada na apresentação e contribuições individuais identificáveis no Git.
- Não é exigida implementação de código nesta etapa. A arquitetura deve ser concreta e viável para o Trabalho 2.

Conteúdo obrigatório: interação delimitada; atores, objetivos, capacidades, informações e restrições; ativo ou propriedade preservada; pelo menos dois pressupostos e suas falhas; matriz de dois jogadores com duas ações cada; justificativa dos payoffs; melhores respostas, dominância e equilíbrio; pelo menos três rodadas conectadas de ação → resposta → observação → adaptação; três diagramas; pelo menos três pontos de exploração e três cenários de ameaça; probabilidade, impacto e risco; resposta prioritária, informação revelada, adaptação seguinte, efeitos colaterais e risco residual; redesenho e resiliência; referências; declaração de IA e contribuições.

A pergunta final é: **Depois que o sistema responder, o que o outro lado aprenderá e tentará fazer em seguida?**

Rubrica: delimitação 15; modelo estático 20; modelo dinâmico 20; superfície e ameaças 25; redesenho e resiliência 15; clareza, evidências e organização 5. Total: 100 pontos.

## Estado atual em 05/10/2026

- README preenchido com recorte, matriz, três rodadas, ameaças, controles e arquitetura.
- Três diagramas em Mermaid e PNG, fontes bibliográficas reais e cenário sintético em `dados/cenario.json`.
- Apresentação local de 12 slides em `apresentacao/slides.pptx` e `apresentacao/slides.pdf`; falas nas notas do PPTX e em `docs/roteiro-video.md`.
- Conferência automática do exemplo numérico e dos artefatos não equivale a testar um motor de jogo implementado.
- Ainda faltam revisão humana, confirmação das contribuições efetivas, gravação dos quatro integrantes, publicação do vídeo, acesso externo aos arquivos e submissão. `apresentacao/links.md` registra essas pendências.
- Nenhuma publicação, push, submissão ou autoria individual foi fabricada. Antes de continuar, confira o Git e os arquivos atuais.

## Revisão de escopo em 06/10/2026

Pré-flop, raise e all-in foram incorporados por solicitação do usuário. O exemplo principal tem quatro etapas e 11 ações, conserva 220 fichas e termina em A=50/B=170. A matriz no river parte de pote 80 após check de B. O all-in alternativo pode encerrar cedo e não substitui os três ciclos exigidos. Não registrar aprovação humana coletiva apenas por essa revisão assistida.

## Fotografia da revisão inicial

- O README tem identificação, resumo, escopo e fluxo inicial parcialmente preenchidos. A maior parte das seções seguintes ainda contém campos de modelo.
- Os três arquivos Mermaid existem, mas seus conteúdos ainda são genéricos.
- As referências ainda são exemplos, sem fontes bibliográficas reais.
- Não foram encontrados slides, diagramas PNG ou vídeo na revisão inicial.
- O histórico local consultado tinha três commits, de Rafael e Elton. Isso não comprova ausência de trabalho dos demais fora desse histórico.
- A pasta `enunciado/` aparecia como não rastreada no Git. Verifique novamente antes da entrega.
- Os arquivos de planejamento foram criados para orientar a produção; não substituem o relatório e os entregáveis.

Este estado é uma fotografia inicial. Antes de executar tarefas, confira os arquivos e o Git atuais e atualize o planejamento conforme o trabalho avançar.

## Modelagem incorporada, ainda sujeita à revisão do grupo

Descrever o sistema como uma simulação local com **dois agentes de software** que tomam decisões e adaptam suas estratégias, mediados por um motor de jogo. A transcrição enfatiza agentes de software e aceita jogos, inclusive poker, como contexto adversarial.

Separar três responsabilidades:

- Agentes: maximizar o resultado em fichas sob informação parcial.
- Motor de jogo: aplicar regras, validar ações e contabilizar fichas.
- Visão de cada agente: disponibilizar apenas suas cartas e informações públicas autorizadas.

O adversarial surge do conflito entre os agentes; o motor não precisa ter uma estratégia de jogador. O blefe permitido faz parte do jogo. Uma ameaça estratégica pode explorar uma política previsível de um agente; uma ameaça ao software pode violar sigilo, contabilização ou progresso da mão. Identifique explicitamente qual caso está sendo discutido.

## Cuidados de modelagem

- Não confundir ganhar uma mão com preservar a justiça do sistema. Uma derrota legítima não comprova falha do motor.
- Não prometer poker completo. Declare as simplificações e o ponto inicial das três rodadas.
- Manter pré-flop, blinds 5/10, ordem A antes do flop/B depois, raise e all-in consistentes em relatório, dados, diagramas e slides. Raise indica total por etapa e call transfere apenas a diferença. Após all-in pago, devolver excesso e executar runout sem novas apostas. O cenário principal termina em 50/170 (total 220); ramos alternativos em `dados/cenario-all-in.json`.
- A matriz proposta é uma análise retrospectiva de um cenário fixo, com informação completa para analisar a tabela reduzida. Os agentes da mão dinâmica têm informação parcial. Não apresentar o equilíbrio da tabela como decisão ótima durante a mão nem exigir que a sequência observada termine nele.
- Não usar “apostar por valor” versus “blefar” como ações livremente intercambiáveis sem explicar a mão e a informação de cada jogador.
- Payoffs de preferência não são probabilidades nem automaticamente ganhos monetários. Explicar como os resultados geram a ordem de preferência.
- Em cada rodada, indicar qual observação anterior levou à mudança seguinte de **cada agente**. Para B, explicitar a passagem da política de apostar com mão forte para check visando induzir uma nova aposta após observar iniciativa de A. Registrar observação, política anterior, política atual e custo sem revelar esse registro privado ao oponente.
- Manter saldos, pote e histórico coerentes. Quando houver exemplo numérico, conferir conservação de fichas e transições legais.
- Rastrear componentes → pontos de exploração → pressupostos → ameaças → ativos → controles → adaptação → risco residual.
- Usar T1/T2/T3 para ameaças e A1/A2/B1/B2 para ações da matriz, conforme o README atual.
- Probabilidade e impacto são avaliações qualitativas propostas, justificadas separadamente. No plano revisado, P é condicionada à presença da fraqueza e à capacidade do agente de explorá-la. As notas 9/9/6 correspondem a T1/T2/T3; T1 e T2 empatam, e o sigilo já perdido justifica aprofundar T1. Escopo local não é justificativa de baixa probabilidade.
- Não inventar referências, aprovação docente específica do projeto, experimentos, publicação, validação do grupo, contribuições ou autoria de commits.
- Declarar uso de IA de acordo com o que ocorreu e registrar a verificação humana somente após ela ocorrer.

## Como trabalhar neste repositório

Escrever em português claro. Manter o README autossuficiente, com links para as fontes e os artefatos. Documentos em `docs/` apoiam o processo, mas não devem esconder elementos exigidos do relatório principal.

Atualizar os diagramas Mermaid, a fonte visual PPTX e os blocos do README em conjunto. Exportar imagens para acompanhar os fontes editáveis, conforme a estrutura sugerida no enunciado. `.build/` e `.qa/` contêm intermediários locais ignorados pelo Git; os entregáveis estão nas pastas públicas do projeto.

Fazer primeiro a modelagem coerente; depois produzir slides e gravar o vídeo. As falas do roteiro devem refletir a versão final do README. A meta sugerida é 11 minutos, com 2min45s por integrante; não é duração exigida pelo enunciado. Ao revisar, verificar conceitos, cálculos, links, nomes, tempo real no ensaio e ausência de campos de modelo nos entregáveis.

Respeitar alterações existentes. Não fabricar histórico de participação. Atribuir contribuição apenas à pessoa que efetivamente realizou ou revisou o trabalho. Não publicar, fazer push ou enviar mensagens a terceiros sem autorização aplicável do usuário.
