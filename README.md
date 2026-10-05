# Checkpoint 5 — Bug Hunt PetFiap

> Copie este arquivo para a raiz do seu repositório com o nome **README.md**
> e preencha todas as seções.

## Identificação

**Grupo:** ___

| Integrante | RM | Turma |
|---|---|---|
| Diego Antonio Silva Mendes | 565509 | 2CCPG |
| Thiago Sobral de Alvarenga | 562695 | 2CCPG |
| Pedro Miranda Campos Riato | 562117 | 2CCPG |
| Israel Karacsony de Camargo Nunes | 563435 | 2CCPG |
| Giovanni de Lela Anjos Costa | 563066 | 2CCPG |
| Gabriel Hiro Nakamura | 562221 | 2CCPG |

| Campo | |
|---|---|
| **Total de bugs corrigidos** | 12 / 12 |
| **Total de ajustes de Clean Code** | 3 / 6 |
| **Total de testes novos escritos** |  6 / 6 |
| **Suíte final (Run As → JUnit Test)** | 26 testes, 0 falhas |

---

## Parte 1 — Bugs encontrados

> Uma linha por bug, na ordem em que você os encontrou. Use a numeração dos seus
> commits (`fix: bug01 ...`). Preencha TODAS as colunas — metade da nota está aqui.

| # | Sintoma observado (o que fiz/vi) | Causa raiz (arquivo e linha aproximada) | Correção aplicada | Conceito da disciplina |
|---|---|---|---|---|
| bug01 | O atributo `petNome` estava sem o referenciador `this` no builder de atendimento, fazendo com que o nome não seja armazenado corretamente e ser nulo sempre(Atualmente está como `petNome = petNome`) | Arquivo `AtendimentoBuilder.java` (linha 24). | Adicionado referênciador `this` ao atributo `petNome`, fazendo a lógica ser `this.petNome = petNome;` | Aula 02 (Métodos e comportamentos) - Referência a atributos |
| bug02 | Builder de atendimento não verificava se o mesmo vinha sem nome, falhando no teste de `deveRecusarMontagemSemNomeDoPet` | Arquivo `AtendimentoBuilder.java` (linha 42). | Adicionado lógica simples com uso de if para verificar se `petNome == null` ou `petNome == isBlank()`, se esse for o caso, o sistema lança uma exception de argumento invalido (`IllegalArgumentException`), se passar na validação, o sistema cria o atendimento normalmente. | Aula 11: Quando Dá Errado (Tratamento de Exceções) / Regra de negócio |
| bug03 | Builder de atendimento não verificava se o mesmo vinha sem porte, falhando no teste de `deveRecusarMontagemSemPorte` | Arquivo `AtendimentoBuilder.java` (linha 45). | Adicionado lógica simples com uso de if para verificar se `petPorte == null` ou `petPorte == isBlank()`, se esse for o caso, o sistema lança uma exception de argumento invalido (`IllegalArgumentException`), se passar na validação, o sistema cria o atendimento normalmente. | Aula 11: Quando Dá Errado (Tratamento de Exceções) / Regra de negócio |
| bug04 | Case switch implementação incorreta de tosa. Quando o sistema detectava que uma `TOSA` foi criada, ele tentava criar incorretamente um novo `BANHO`, fazendo com que uma tosa nunca fosse criada, mesmo que esse fosse o tipo de atendimento agendado e falhando no teste de `deveCriarTosaQuandoTipoForTosa`. | Arquivo `AtendimentoFactory.java` (linha 17). | Alterado implementação de case de `TOSA` para criar uma nova tosa (`new Tosa`) ao invés de banho (`new Banho`).| Regra de negócio |
| bug05 | O Singleton não mantinha uma única instância.| arquivo `GeradorProtocolo.java`, linha 19, Uma nova instância era criada e retornada sem ser armazenada em `instancia`.| Armazenar a nova instância em `instancia` antes de retorná-la. | Aula 14 - Singleton .|
| bug06 | A consulta veterinária não preenchia os dados do pet e do atendimento. | arquivo `ConsultaVeterinaria.java`, linha 17. Construtor de ConsultaVeterinaria  chamava `super()` vazio.| O `super()` foi alterado para `super(protocolo, petNome, petPorte, tutorNome, dataHora)`.| Aula 6 (Herança) - uso obrigatório de super nas classes filhas.|
| bug07 | A duração em minutos da Tosa não estava sendo definida pelo método esperado.| arquivo `Tosa.java`, linha 40. O método tinha um parâmetro e fazia uma sobrecarga em vez de sobrescrita.| Remove o parâmetro e adiciona `@Override`.| Aula 7 (Polimorfismo) - diferença entre sobrescrita (override) e sobrecarga (overload) de métodos.|
| bug08 | Os preços de banho para porte pequeno e grande estavam invertidos. | arquivo `Banho.java`, linhas 25-33. Os valores de Pequeno e Grande estavam trocados em `calcularPreco()`.|  Os valores foram colocados na ordem certa respeitando a regra de negócio, em que PEQUENO agora retorna 60.0 e GRANDE retorna 100.0.| Aulas 2 e 3 -  Métodos e regras de negócio.|
| bug09 | O agendamento não detectava conflito de horário mesmo quando o mesmo pet já tinha um atendimento AGENDADO para a mesma data/hora, falhando no teste `deveRecusarAgendamentoComHorarioJaOcupado`. | Arquivo `AgendaService.java` (linha 23). A comparação usava `==` entre `String` (petNome) e `LocalDateTime` (dataHora), comparando referência de objeto em vez de valor — `LocalDateTime` quase nunca é o mesmo objeto entre duas requisições. | Trocado `==` por `.equals()` nas duas comparações (`a.getPetNome().equals(novo.getPetNome())` e `a.getDataHora().equals(novo.getDataHora())`). | Aula 7 — comparação de objetos por valor vs referência (mesmo princípio do bug07, só aplicado a String/LocalDateTime). |
| bug10 | Buscar um atendimento por id inexistente não lançava exceção nenhuma — o método retornava `null` silenciosamente, falhando no teste `deveLancarExcecaoQuandoAtendimentoNaoExiste`. | Arquivo `AgendaService.java`, método `buscarPorId` (linhas 37-42). Um bloco try/catch capturava qualquer `Exception` — inclusive a própria `AtendimentoNaoEncontradoException` lançada pelo `orElseThrow` — e retornava `null` em vez de deixar a exceção propagar. | Removido o bloco try/catch; o método agora retorna diretamente `repository.findById(id).orElseThrow(...)`, deixando a exceção subir normalmente. | Aula 11 (Tratamento de Exceções) — catch genérico e vazio escondendo o erro em vez de tratá-lo. |
| bug11 | Era possível cancelar um atendimento que já estava CONCLUIDO (ou já CANCELADO), sem nenhum erro — a regra de negócio exige recusa nesses dois casos. | Arquivo `Atendimento.java`, método `cancelar()` (linhas 63-65). O método setava `status = "CANCELADO"` direto, sem nenhuma verificação do status atual (diferente do `concluir()`, que já fazia essa checagem). | Adicionada a mesma checagem usada em `concluir()`: se `status` não for `"AGENDADO"`, lança `StatusInvalidoException` antes de alterar o status. | Regras de transição de estado / consistência do model — mesma lógica de guarda já usada em `concluir()`, só que faltava no `cancelar()`. |
| bug12 | Era possível agendar um atendimento com data/hora no passado sem nenhum erro — o contrato exige recusa com `IllegalArgumentException`, sem nem consultar o banco. | Arquivo `AgendaService.java`, método `agendar()` (início do método, antes da linha 21). Não existia nenhuma validação comparando `novo.getDataHora()` com o momento atual. | Adicionada checagem `if (novo.getDataHora().isBefore(LocalDateTime.now()))` lançando `IllegalArgumentException` logo no início do método. | Aulas 2/3 — validação de entrada / blindagem do objeto antes de prosseguir com a regra de negócio. |

## Parte 2 — Ajustes de Clean Code

| # | Onde estava | Qual princípio/boas práticas era violado | O que eu mudei |
|---|---|---|---|
| clean01 | AtendimentoFactory.java (linhas 14-19) | Aula 2 - (Métodos e Comportamentos) - Nomes Significativos para Argumentos | Troquei o nome das variáveis para nomes claros |
| clean02 | AtendimentoController.java (linhas 105-112) | Código morto | Remove parte do código que não é utilizada no momento |
| clean03 | AtendimentoFactoryTest.java (linha 5) | Código morto/importação não utilizada | Remove um import que não é utilizado no arquivo |
| clean04 | Atendimento.java (linhas 71–90) | Aula 03 - (Encapsulamento, Getters e Setters) - Legibilidade e organização do código| Reformatei os getters e setters, separando retorno e atribuições em linhas distintas |
| clean05 | AtendimentoBuilder.java (linhas 42–47) | Aula 02 - (Métodos e comportamentos) - código duplicado para validar os campos | Extraí a validação repetida para o método validarCampoObrigatorio() |
| clean06 | AgendaService.java (linhas 27-28) | Aula 02 - (Métodos ecomportamentos) - condição complexa no `if` prejudicava a legibilidade| Extraí a condição para o método possuiConflitoDeHorario() |

## Parte 3 — Testes novos (regras que estavam sem cobertura)

> Uma linha por teste novo (`test: ...`). "Regra coberta" é o comportamento do
> contrato (seção 3 do enunciado) que o teste protege. Em "Resultado", diga se o
> teste ficou vermelho ao ser escrito (revelou bug — qual?) ou verde de cara
> (regra já estava correta).

| # | Teste escrito (classe.método) | Regra coberta | Resultado ao escrever (vermelho/verde) |
|---|---|---|---|
| teste01 | `TosaTest.deveDurar60Minutos()` | Tosa deve ter duração de 60 minutos.| Vermelho - revelou um bug em que a duração estava com parâmetro e não sobrescrevia o método esperado.|
| teste02 | ` BanhoTest.deveCalcularPrecoPorPorte() `| Banho deve custar R$ 60 para pequeno, R$ 80 para médio e R$ 100 para grande.| Vermelho - revelou um bug em que os preços de pequeno e grande estavam invertidos. |
| teste03 | `ConsultaVeterinariaTest.deveCustar150ReaisIndependenteDoPorte()`|  Consulta veterinária deve custar R$ 150 independentemente do porte.| Verde - a regra já estava correta. |
| teste04 | `AgendaServiceTest.deveCancelarAtendimentoAgendado()` | Atendimento com status AGENDADO deve virar CANCELADO ao chamar cancelar(). | Verde - a regra já estava correta (o cancelar() sempre conseguiu setar o status, só faltava a validação dos outros casos). |
| teste05 | `AgendaServiceTest.deveRecusarCancelamentoDeAtendimentoJaConcluido()` | Atendimento já CONCLUIDO (ou já CANCELADO) não pode ser cancelado novamente. | Vermelho - revelou o bug11: cancelar() não verificava o status atual antes de cancelar. |
| teste06 | `AgendaServiceTest.deveRecusarAgendamentoComDataHoraNoPassado()` | Agendamento com data/hora no passado deve ser recusado com IllegalArgumentException, sem consultar o banco. | Vermelho - revelou o bug12: agendar() não validava data/hora retroativa. |

---

## Parte 4 — Perguntas de reflexão

> Responda com suas palavras, 5 a 10 linhas cada, **usando o código real do
> projeto como exemplo**. Respostas genéricas de tutorial não pontuam.

### 1. A suíte como contrato (Aula 15)
O projeto chegou com 20 testes, 9 vermelhos. Descreva como você usou as
mensagens de falha (ex.: `expected: <Rex> but was: <null>`) para caçar os bugs.
O que a suíte de testes tem de melhor do que testar tudo na mão com curl?

### 2. Mock e injeção de dependência (Aulas 13 a 15)
No `AgendaServiceTest`, o `@Mock` cria um `AtendimentoRepository` falso e o
`@InjectMocks` o injeta no service. Explique a relação disso com o `@Autowired`
que o Spring faz em produção — quem "injeta" em cada mundo, e por que o teste
consegue rodar sem banco e sem subir o Spring?

### 3. `==` vs `.equals()` (Aula 7)
Um dos bugs fazia o agendamento duplicado passar pela verificação de conflito.
Explique por que `==` entre Strings e `LocalDateTime` falhou aqui, por que ele
"funciona por sorte" com literais como `"Rex"`, e o que a sua correção mudou.

### 4. Sobrescrita vs sobrecarga (Aula 7)
Um dos bugs compilava sem nenhum erro: um método parecia sobrescrever
`getDuracaoMinutos`, mas na verdade criava uma assinatura nova. Explique a
diferença entre override e overload nesse caso e por que a anotação `@Override`
teria impedido o bug.

### 5. Singleton manual vs bean do Spring (Aula 14)
O `GeradorProtocolo` é um Singleton escrito à mão e causou um dos bugs.
Explique o que ele garante, qual foi o bug, e por que o `AgendaService`
(`@Service`) não corre o mesmo risco no container do Spring.

### 6. Cobertura de testes: onde parar? (Aula 15)
Dos 6 testes novos que você escreveu, alguns ficaram vermelhos (revelaram
bugs) e outros verdes de cara (regras já corretas). Vale a pena manter os que
ficaram verdes? Em um projeto real com prazo, o que você priorizaria testar:
caminho feliz, caminhos de erro, ou 100% de cobertura? Justifique.

R: No cenário em que os testes ficaram verdes, vale a pena mantê-los, pois eles garantem que as regras que estavam certas continuem funcionando. Além disso, no projeto, testes como `ConsultaVeterinariaTest.deveCustar150ReaisIndependenteDoPorte()` e `AgendaServiceTest.deveCancelarAtendimentoAgendado()` são importantes, principalmente em situações em que mudanças no código possam gerar novas falhas. Nesse sentido, eles conseguem apontar quando alterações comprometem funcionalidades que antes estavam funcionando corretamente. Em um projeto real com prazo, eu daria maior prioridade aos caminhos de erro, pois problemas em regras de negócio e segurança podem causar impactos maiores no sistema. Assim, seria possível proteger principalmente as funcionalidades mais importantes e evitar problemas para os usuários.

---

## Parte 5 — Espaço livre (opcional)

Alguma dificuldade, dúvida ou comentário sobre o checkpoint?

```

```
