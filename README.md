# Como eu escalaria IA num time de engenharia

*Bruno de Oliveira. As seções "O que aconteceu" e "Resultados" vêm de experiência vivida e medida; "Como eu escalaria" é um método proposto a partir dela, ainda não aplicado em escala. Nenhum dado de empresa, cliente, produto ou sistema interno aparece aqui.*

---

## O ponto de partida

A triagem de problemas em produção era lenta e cara. O Product Owner relatava o problema, no máximo procurava uma task parecida no Jira e escrevia a tarefa (problema, cliente, data, prioridade, critérios de aceitação). Ele não analisava logs. O engenheiro só começava o debug depois.

Eu tinha background de desenvolvimento e sabia o caminho: ver o problema, procurar recorrência, abrir os logs no Datadog, ir ao código e debugar as funções Lambda. A pergunta foi: **esse caminho pode ser ensinado a uma ferramenta?**

## O que eu construí

Uma skill de IA, usada junto com uma extensão do GitNexus. Fluxo:

1. Eu descrevia o problema.
2. A skill buscava no Jira se havia recorrência e já separava as tasks relacionadas.
3. Analisava o código e fazia um pré-debug.
4. Lia os logs no Datadog, tirava prints e deixava as evidências.
5. Criava a task no backlog, no escopo pré-definido, com critérios de aceitação, prints do trecho de código e do log e, às vezes, uma proposta de solução.
6. Quando o problema já tinha ocorrido antes, vinculava e sinalizava as tasks.

## Resultados

- **Mais de 200 problemas** passaram pela skill em 9 meses de uso, vindos de **9 produtos diferentes do mesmo nicho (ecossistema veicular)**.
- Preparar uma task passou de **cerca de 45 minutos** (fora o debug do engenheiro) para **cerca de 10 minutos** de revisão e aprovação.
- O PO ganhou tempo para priorização, trade-offs e reordenação de sprint.
- A escrita das tasks ficou padronizada e com mais contexto, o que acelerou o debug do desenvolvedor.
- O PO passou a conhecer os erros do produto numa camada mais técnica (logs).

## Tipos de problema que ela encontrou

- **Um falso positivo antigo:** uma validação tratava um array vazio como "sem resultado" porque o tratamento de erro não devolvia `null`. O serviço checava que o valor era diferente de `null`, via o array vazio e concluía que estava tudo bem, em vez de tratar como erro.
- **Quebra de contrato em deploy:** o corpo da resposta de uma rota foi alterado sem notar que um consumidor não tolerava qualquer mudança. O erro aparecia no webhook. A skill pegou ao debugar a rota e ler os logs em massa.
- **Redução de custo:** numa melhoria de entrega, a análise apontou 3 chamadas desnecessárias a um serviço de proxy.
- **Escala:** para suportar um volume de milhões de requisições por mês, avaliou a estrutura e apontou aos engenheiros os pontos de reforço.

## Como eu escalaria isso para um time inteiro *(proposta, não experiência)*

1. **Comece por uma dor repetitiva e mensurável.** A triagem funcionou porque tinha um processo claro, um responsável e um tempo de partida medível.
2. **Capture o processo de quem já o faz bem antes de automatizar.** Sem isso, a automação reproduz um processo ruim mais rápido.
3. **A IA prepara, o humano decide.** O PO revisava e aprovava em ~10 minutos. Manter a decisão humana protege a qualidade e constrói confiança.
4. **Padronize a saída.** Uma estrutura fixa de task (problema, evidência, critérios) facilita a leitura e o treinamento de quem chega.
5. **Dê evidência, não só conclusão.** Prints de código e de log permitem conferir rápido se a hipótese faz sentido.
6. **Meça antes e depois.** Tempo por task, volume e recorrência dão a base. Eu acrescentaria a taxa de hipóteses corretas, que não medi.
7. **Transforme em aprendizado.** O PO aprendeu com os logs. O mesmo vale para júnior e para quem está entrando no time.

## Limites e riscos que eu veria

- A skill **depende de acesso e de qualidade dos logs**. Sem logs úteis, o pré-debug perde valor.
- **Hipótese não é causa.** A proposta de solução apareceu só às vezes e sempre passou por revisão.
- **Segurança e custo:** dados de clientes em logs e uso de ferramentas externas exigem cuidado e passam pelo time de segurança.
- Eu **não medi** a taxa de acerto das hipóteses nem a redução de reincidência. São os dois números que eu mediria primeiro num novo time.

## Em uma frase

*Peguei um processo que eu dominava, ensinei a uma ferramenta e devolvi tempo ao time: de ~45 para ~10 minutos por problema, em mais de 200 casos, espalhados por 9 produtos do mesmo nicho.*
