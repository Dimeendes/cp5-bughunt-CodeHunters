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
| **Total de bugs corrigidos** | 08 / 12 |
| **Total de ajustes de Clean Code** | ___ / 6 |
| **Total de testes novos escritos** | 3 / 6 |
| **Suíte final (Run As → JUnit Test)** | ___ testes, ___ falhas |

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
| bug09 | | | | |
| bug10 | | | | |
| bug11 | | | | |
| bug12 | | | | |

## Parte 2 — Ajustes de Clean Code

| # | Onde estava | Qual princípio/boas práticas era violado | O que eu mudei |
|---|---|---|---|
| clean01 | | | |
| clean02 | | | |
| clean03 | | | |
| clean04 | | | |
| clean05 | | | |
| clean06 | | | |

## Parte 3 — Testes novos (regras que estavam sem cobertura)

> Uma linha por teste novo (`test: ...`). "Regra coberta" é o comportamento do
> contrato (seção 3 do enunciado) que o teste protege. Em "Resultado", diga se o
> teste ficou vermelho ao ser escrito (revelou bug — qual?) ou verde de cara
> (regra já estava correta).

| # | Teste escrito (classe.método) | Regra coberta | Resultado ao escrever (vermelho/verde) |
|---|---|---|---|
| teste01 | `TosaTest.deveDurar60Minutos()` | Tosa deve ter duração de 60 minutos.| Vermelho - revelou um bug em que a duração estava com parâmetro e não sobrescrevia o método esperado.|
| teste02 | | | |
| teste03 | | | |
| teste04 | | | |
| teste05 | | | |
| teste06 | | | |

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

---

## Parte 5 — Espaço livre (opcional)

Alguma dificuldade, dúvida ou comentário sobre o checkpoint?

```

```
