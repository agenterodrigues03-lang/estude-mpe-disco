# Prompts para replicar o "Plano de estudo de Vinicius" no Painel MPE (Hostinger Horizons)

> Objetivo: reproduzir, dentro do site **Painel MPE — Central de Estudos**, a página do plano de
> estudos (Ciclo 1 · Semana 1 · seis dias de seis blocos), com as abas **Hoje**, **Ciclo de estudos**,
> **Semana 1 · bloco a bloco** e **Rotina**, e transformá-la em algo *vivo* (marcação de blocos
> concluídos, caderno de erros e evolução do percentual de acertos).

---

## 1. O que eu preciso de você

### Caminho A — você cola os prompts no Horizons (é o caminho deste arquivo)
Não preciso de nada. Os prompts abaixo estão prontos para copiar e colar, **um de cada vez**,
no chat do Horizons.

Situação confirmada com o usuário:

- [x] O login ("Entrar / Criar agora") **já está funcionando**.
- [x] O banco do projeto **não é Supabase: é PocketBase**. Não há SQL — há *coleções*, e o
      controle de acesso se faz por **API rules**, não por RLS.
- [x] As quatro coleções novas já foram criadas: `mpe_planos`, `mpe_blocos`,
      `mpe_sessoes_questoes`, `mpe_caderno_erros`. O projeto passou a ter 12 coleções.

O prefixo `mpe_` foi mantido: elimina qualquer risco de colisão com as 8 coleções que já existiam,
sem precisar auditar o banco antes.

### Caminho B — eu mesmo escrevo o código
Aí preciso de:

1. **O projeto Horizons conectado a um repositório GitHub** (Horizons → menu ☰ → *GitHub / Exportar*)
   e o repositório liberado para esta sessão. Sem isso não alcanço o código do site.
2. As credenciais **não** — nunca me envie senha, `service_role key` ou `.env`. Só o repositório.
3. O texto das abas do artefato que ainda não vi. Já tenho a aba **Hoje**, os indicadores
   (50% → 55%, Fase 1/5, horizonte 18–30m) e a grade completa da aba **Ciclo de estudos**
   (Anexo A). Falta o conteúdo das abas **Semana 1 · bloco a bloco** e **Rotina**.

### O que já sei do ambiente
- Hostinger **Horizons** gera app **React + Vite + TailwindCSS + shadcn/ui**. O banco e a
  autenticação deste projeto rodam em **PocketBase** (não Supabase). Os prompts abaixo assumem
  essa stack.
- Em PocketBase: *coleção* no lugar de tabela, *record* no lugar de linha, **API rules** no lugar
  de RLS, e os campos `id`, `created` e `updated` são gerados automaticamente.
- O botão **Publicar** (canto superior direito) é o que joga a versão para o domínio.

---

## 2. Regras de ouro para conversar com o Horizons

Isto vale mais que os prompts em si — é o que separa um site que fica pronto de um que quebra:

1. **Um prompt = uma entrega.** Nunca peça "crie a página inteira com banco, login e gráficos".
   A IA de builder erra em pedidos largos e depois é difícil desfazer.
2. **Peça um checkpoint antes de mudanças grandes** ("crie um checkpoint antes desta alteração").
3. **Sempre diga o que NÃO mudar**: "não altere a tela de login nem o layout existente".
4. **Depois de cada prompt**, abra a pré-visualização no celular. É onde você vai usar.
5. **Publique só ao final de cada bloco** (não a cada micro-ajuste).
6. Se a IA "viajar", não tente consertar com mais texto: volte ao checkpoint anterior.

---

## 3. Os prompts

### PROMPT 0 — Contexto do projeto (cole primeiro, sempre)

```
Contexto do projeto, guarde para todas as próximas instruções:

Este é o "Painel MPE — Central de Estudos", um app privado de estudo para concurso de
Promotor de Justiça Estadual. Usuário único no início (eu), com login por e-mail e senha.

Identidade visual atual que deve ser preservada: fundo creme claro (#F7F3EC), tipografia serifada
nos títulos, botões e destaques em dourado/ocre (#9A7B3F), texto em quase-preto.

Vou pedir agora uma nova área do app: a página do PLANO DE ESTUDOS. Ela terá tema ESCURO
(fundo #17141A, cartões #221C22, texto claro, acento em rosa-queimado #E8879B e detalhes em
vermelho-vinho), contrastando com o resto do painel, que continua claro.

Regras para todas as suas próximas respostas:
- Não altere a tela de login nem as páginas já existentes, a menos que eu peça explicitamente.
- Faça uma mudança por vez, exatamente a que eu pedir, sem inventar funcionalidades extras.
- Todo texto da interface em português do Brasil.
- Mobile-first: eu uso este app quase sempre pelo celular.

Não altere nada ainda. Apenas confirme que entendeu e me diga o que já existe hoje no projeto
(páginas, coleções no banco e se o login já está funcional).
```

---

### PROMPT 1 — Estrutura de dados (PocketBase)

```
IMPORTANTE — leia antes de executar:
- NÃO altere, renomeie nem apague nenhuma coleção, campo ou regra que já exista no projeto.
- O login já funciona: use a coleção de autenticação existente, não crie outra.
- Crie apenas coleções NOVAS, todas com o prefixo mpe_.
- Se alguma dessas coleções já existir, pare e me avise em vez de alterar.

Crie as coleções abaixo. Em todas elas, o campo user_id é uma RELAÇÃO (relation) para a coleção
de autenticação do projeto, obrigatória, com maxSelect 1 e cascadeDelete ativado.
Em todas as quatro, as cinco API rules (list, view, create, update, delete) devem ser
exatamente: user_id = @request.auth.id
Isso garante que cada usuário só enxerga e altera os próprios registros.
Não repita os campos id, created e updated: o PocketBase já os cria sozinho.

1) mpe_planos
   - user_id (relation -> coleção de autenticação, obrigatório)
   - titulo (text)
   - ciclo_atual (number, padrão 1)
   - semana_atual (number, padrão 1)
   - fase_atual (number, padrão 1)
   - total_fases (number, padrão 5)
   - nivel_inicial (number, padrão 50)     // % de acertos do diagnóstico
   - meta_ciclo (number, padrão 55)        // % alvo
   - meta_semana (number, padrão 8)
   - horizonte (text, padrão "18-30m")

2) mpe_blocos
   - user_id (relation -> coleção de autenticação, obrigatório)
   - plano_id (relation -> mpe_planos, maxSelect 1)
   - ciclo (number), semana (number)
   - dia_semana (number)        // 1 = segunda ... 6 = sábado
   - ordem (number)             // posição do bloco dentro do dia
   - duracao_horas (number)     // 1 ou 2
   - tipo (select, valores: lei_seca, questoes, caderno, g7_pos, simulado, revisao)
   - disciplina (text)
   - descricao (editor ou text)
   - concluido (bool, padrão falso)
   - concluido_em (date, opcional)

3) mpe_sessoes_questoes
   - user_id (relation -> coleção de autenticação, obrigatório)
   - data (date)
   - disciplina (text)
   - banca (text)
   - total (number)
   - acertos (number)
   - observacoes (text, opcional)

4) mpe_caderno_erros
   - user_id (relation -> coleção de autenticação, obrigatório)
   - data (date)
   - disciplina (text)
   - tema (text)
   - erro (editor)              // o que eu errei
   - correcao (editor)          // o entendimento correto
   - fonte (text)               // lei, súmula, informativo
   - revisado_em (date, opcional)

Não crie nenhuma tela ainda. Só o banco e as políticas de segurança.
Depois me mostre, para cada coleção criada, a lista de campos com seus tipos e as cinco API rules.
```

---

### PROMPT 1-B — Conferência das regras de acesso (não pule)

> Por que existe: o construtor confirmou que criou as coleções "com regras de acesso restrito ao
> próprio usuário", mas não mostrou as regras. Em PocketBase, uma coleção com API rule vazia
> (`null`) fica acessível só ao admin, e uma com rule `""` fica **aberta a qualquer um**. A
> diferença entre as duas é invisível na conversa e decisiva no ar. Confira antes de seguir.

```
Antes de criarmos qualquer tela, preciso conferir a segurança do que você acabou de criar.

Para cada uma das quatro coleções (mpe_planos, mpe_blocos, mpe_sessoes_questoes,
mpe_caderno_erros), me mostre em uma tabela:

1. o nome de cada campo e seu tipo;
2. o valor EXATO das cinco API rules (listRule, viewRule, createRule, updateRule, deleteRule),
   copiado literalmente — se alguma estiver vazia ou nula, diga "vazia" ou "nula", não escreva
   uma descrição do que ela deveria fazer;
3. para qual coleção o campo user_id aponta.

Se alguma das cinco regras de alguma coleção NÃO for exatamente `user_id = @request.auth.id`,
corrija agora para esse valor e me diga o que estava antes.

Não crie nenhuma tela ainda.
```

---

### PROMPT 2 — A página e a navegação

```
Crie uma nova página protegida por login na rota /plano, chamada "Plano de estudos", e um link
para ela no menu principal do painel.

Layout da página (tema escuro, conforme o contexto que passei):

1. CABEÇALHO
   - Linha superior em letras espaçadas, pequenas, cinza:
     "MINISTÉRIO PÚBLICO ESTADUAL · PROMOTOR DE JUSTIÇA"
   - Título grande, serifado: "Plano de estudo de Vinicius"
   - Subtítulo em cinza claro: "Ciclo 1 · semana 1 · seis dias de seis blocos. Construído sobre o
     diagnóstico de 50% de acertos e os raios-X de SP, MG, FGV e do agregado das últimas provas."
   - Uma barra vertical rosa (#E8879B) de 4px na borda esquerda do cabeçalho inteiro.

2. NAVEGAÇÃO POR ABAS
   - Rótulo acima: "SEÇÕES DO PLANO · DESLIZE PARA VER TODAS", em letras espaçadas e pequenas.
   - Abas em formato de pílula, roláveis horizontalmente no celular:
     "Hoje" | "Ciclo de estudos" | "Semana 1 · bloco a bloco" | "Rotina e regras" | "Caderno de erros"
   - A aba ativa tem fundo rosa (#E8879B) e texto escuro; as inativas têm apenas contorno cinza.
   - A aba "Hoje" é a padrão ao abrir a página.

3. CONTEÚDO DA ABA (por enquanto, deixe cada aba com um espaço reservado vazio)

4. RODAPÉ DE INDICADORES — uma grade de 2 colunas no celular e 4 no computador, com bordas finas:
   - "NÍVEL ATUAL" → 50% → legenda "diagnóstico inicial"
   - "META DO CICLO" → 55% → legenda "até a semana 8"
   - "FASE" → 1/5 → legenda "coletivo + constitucional"
   - "HORIZONTE" → 18–30m → legenda "quatro ciclos"
   Os números grandes, serifados; os rótulos pequenos e espaçados.

Por enquanto os valores dos indicadores podem ficar fixos no código. Só a estrutura visual.
```

---

### PROMPT 2-A — Menu lateral + Ciclo de Estudos como primeira tela

> Pedido do usuário: "Ciclo de Estudos" passa a ser o **primeiro item** do menu lateral e abre a
> grade semanal completa.

```
Duas alterações no menu lateral, sem mexer em mais nada:

1. Crie um novo item de menu chamado "Ciclo de Estudos", com ícone de calendário/grade, e coloque-o
   como o PRIMEIRO item da seção PRINCIPAL, acima de "Centro de Comando". Rota: /ciclo-de-estudos.
   Os demais itens (Centro de Comando, Meu Dia, Estudo Ativo, Desempenho, Biblioteca Jurídica,
   Discursiva, Simulados) permanecem exatamente na ordem atual, logo abaixo.

2. Crie a página /ciclo-de-estudos, protegida por login, com o conteúdo abaixo.

CABEÇALHO
- Linha pequena em letras espaçadas, cinza: "CICLO 1 · SEMANA 1 · SEIS DIAS DE SEIS BLOCOS"
- Título grande serifado: "Ciclo de estudos"
- Parágrafo de apoio: "Semana dedicada 100% ao Bloco 1 (núcleo duro), com a carga de cada
  disciplina proporcional ao consolidado de 2.060 questões. Sábado reserva 3 blocos de conteúdo
  mais a revisão dos erros e a peça discursiva. As disciplinas giram para nunca repetir no mesmo
  dia — o cérebro faz força para resgatar a informação, mesclando matérias. Domingo é descanso e
  planejamento, sem exceção."

A GRADE SEMANAL — este é o coração da tela
No computador: uma tabela de seis colunas, dentro de um cartão escuro com borda fina cinza.
Cabeçalhos em letras maiúsculas espaçadas e pequenas, com a duração em uma segunda linha menor:
DIA | LEI SECA (1h) | QUESTÕES (1h) | CADERNO (2h) | CADERNO (2h) | G7 / PÓS (2h)

Linhas separadas por uma linha fina; o rótulo do dia ("1º DIA") em rosa #E8879B, em versalete.

1º DIA | Constitucional | Penal | Tutela Difusos e Coletivos | Civil | Processo Penal
2º DIA | Legislação Penal Especial | Processo Penal | Administrativo | Constitucional | Processo Civil
3º DIA | Tutela Difusos e Coletivos | Civil | Processo Penal | Penal | Legislação Penal Especial
4º DIA | Penal | Constitucional | Civil | Tutela Difusos e Coletivos | Administrativo
5º DIA | Processo Penal | Tutela Difusos e Coletivos | Penal | Constitucional | Processo Civil
6º DIA | Constitucional | Revisão dos erros | Tutela Difusos e Coletivos | Legislação Penal Especial | Jurisprudência + discursiva
7º DIA | em itálico, cinza, ocupando a largura toda: "Descanso — reler os cadernos, anotar o que travou e pedir o plano da semana 2"

No CELULAR não use tabela com rolagem lateral: transforme cada dia em um cartão empilhado, com o
rótulo do dia no topo em rosa e, dentro, cinco linhas no formato "LEI SECA · 1h — Constitucional",
uma embaixo da outra. O 7º dia vira um cartão só com a frase de descanso.

Destaque o cartão (ou a linha) do dia de hoje com borda rosa à esquerda, calculando pela data real:
segunda = 1º dia, ..., sábado = 6º dia, domingo = 7º dia.

CARGA DA SEMANA — abaixo da grade, uma grade de indicadores pequenos, dois por linha no celular:
Tutela Difusos e Coletivos 8h · Constitucional 7h · Processo Penal 6h · Penal 6h ·
Legislação Penal Especial 5h · Civil 5h · Processo Civil 4h · Administrativo 4h ·
Revisão e discursiva 3h.
Acima deles, o rótulo "HORAS POR DISCIPLINA NA SEMANA · TOTAL 48H".

NOTA DE RODAPÉ, em texto menor e cinza:
"As 3h de Infância e Juventude foram redistribuídas dentro do Bloco 1 (1h para Tutela Coletiva, 1h
para Constitucional, 1h devolvida a Civil), porque o Bloco 1 foi definido sem ela."

Por enquanto os dados da grade podem ficar fixos no código, em um único arquivo de dados separado
do componente visual, para eu poder trocar a semana depois sem mexer no layout.
```

---

### PROMPT 3 — Aba "Hoje"

```
Preencha a aba "Hoje" da página /plano.

Ela mostra um cartão com fundo vinho escuro (#2E1B20), borda esquerda rosa de 4px e cantos
levemente arredondados, contendo:

- Título em letras espaçadas, pequenas, rosa: o dia da semana atual em maiúsculas seguido da
  posição no ciclo. Ex.: "SEGUNDA-FEIRA · PRIMEIRO DIA".
  Esse título deve ser calculado a partir da data real de hoje (segunda = primeiro dia,
  ... sábado = sexto dia). No domingo, mostre "DOMINGO · DESCANSO E REVISÃO LEVE".

- Lista com marcadores dos blocos do dia. Cada item começa com a duração e o tipo em negrito,
  seguidos de travessão e a descrição. Exemplo do formato exato:
  "1h · Lei seca — CF, arts. 127 a 130-A: o Ministério Público. Leitura limpa, grifo só no
  artigo-chave. É o tema de maior retorno do plano inteiro."

- Ao final do cartão, em texto menor e cinza:
  "A semana inteira, bloco a bloco, está na aba Semana 1 · bloco a bloco." (com "Semana 1 ·
  bloco a bloco" em negrito).

Os blocos devem vir da coleção "mpe_blocos" do PocketBase, filtrando pelo ciclo e semana atuais do
plano do usuário e pelo dia da semana de hoje, ordenados pelo campo "ordem".
Se não houver blocos cadastrados para hoje, mostre uma mensagem discreta:
"Nenhum bloco cadastrado para hoje. Cadastre a semana na aba Semana 1 · bloco a bloco."
```

---

### PROMPT 4 — Carregar o conteúdo da Semana 1

> Antes de colar, revise o **Anexo A** deste arquivo: é o conteúdo que vai para dentro do prompt.
> Só a segunda-feira veio do artefato original; os outros cinco dias eu montei seguindo a mesma
> lógica (lei seca → questões → dois cadernos → G7/Pós), com o peso que MP costuma cobrar.

```
Crie uma rotina de carga inicial (seed) que crie na coleção "mpe_blocos" os registros do Ciclo 1,
Semana 1, para o usuário logado — e que não duplique se já existirem blocos daquela semana.

Cole aqui o conteúdo do Anexo A (os seis dias, bloco a bloco).

Adicione também um botão discreto "Recarregar semana 1" dentro da aba "Semana 1 · bloco a bloco",
visível só para mim, que executa essa carga.
```

---

### PROMPT 5 — Aba "Semana 1 · bloco a bloco"

```
Preencha a aba "Semana 1 · bloco a bloco".

Mostre os seis dias (segunda a sábado) como uma lista vertical de seções. Para cada dia:

- Cabeçalho da seção: nome do dia em maiúsculas, letras espaçadas, rosa, seguido do total de
  horas do dia. Ex.: "TERÇA-FEIRA · 8H".
- Abaixo, os blocos daquele dia, cada um em uma linha com:
  - uma caixa de seleção (checkbox) à esquerda, em rosa quando marcada;
  - a duração e o tipo em negrito;
  - a descrição;
  - quando marcado, o texto fica cinza e riscado.
- Marcar ou desmarcar a caixa deve gravar imediatamente no PocketBase (campos "concluido" e
  "concluido_em") e refletir na aba "Hoje", sem recarregar a página.

No topo da aba, uma barra de progresso fina mostrando "X de Y blocos concluídos nesta semana"
e o percentual.

O dia de hoje deve aparecer destacado com a mesma borda rosa do cartão da aba "Hoje".
```

---

### PROMPT 6 — Aba "Ciclo de estudos"

```
Preencha a aba "Ciclo de estudos" com uma visão das cinco fases do ciclo de 8 semanas.
Use cartões empilhados, cada um com: número da fase, nome, semanas que ocupa, disciplinas-eixo
e o resultado esperado. Conteúdo:

FASE 1 · Semanas 1–2 · Base institucional e coletiva
Eixos: Constitucional (MP, arts. 127 a 130-A), Tutela Coletiva, Civil (parte geral),
Processo Penal (sistema acusatório e ação penal).
Resultado esperado: dominar o que cai em toda prova de MP e não depende de nenhuma outra matéria.

FASE 2 · Semanas 3–4 · Instrumentos de atuação
Eixos: Inquérito civil e TAC, Improbidade (Lei 8.429 com a redação da Lei 14.230/21),
Administrativo (princípios e atos), Penal (teoria do crime).
Resultado esperado: saber manejar os instrumentos próprios do promotor, não só descrevê-los.

FASE 3 · Semanas 5–6 · Direitos difusos setoriais
Eixos: Consumidor, Ambiental, Infância e Juventude (ECA), Saúde e Educação como direitos
prestacionais.
Resultado esperado: fechar as matérias de alto peso relativo e baixa concorrência de estudo.

FASE 4 · Semana 7 · Controle e jurisprudência
Eixos: Controle de constitucionalidade, súmulas e informativos recentes de STF e STJ,
Processo Civil aplicado ao coletivo.
Resultado esperado: transformar conhecimento em resposta de prova.

FASE 5 · Semana 8 · Aferição
Eixos: simulado completo multibanca, revisão do caderno de erros, recalibragem do percentual.
Resultado esperado: medir se o nível saiu de 50% e definir o Ciclo 2.

Acima dos cartões, uma linha do tempo horizontal simples com as 8 semanas, destacando a semana
atual do plano (vinda do banco).
```

---

### PROMPT 7 — Aba "Rotina e regras"

```
Preencha a aba "Rotina e regras" com uma lista de princípios do plano, cada um em um cartão
pequeno com um título curto em negrito e uma explicação de uma ou duas linhas:

- Lei seca todo dia. Uma hora, sempre no primeiro bloco, com a cabeça descansada.
- Questões antes do resumo. A questão mostra o recorte que a banca usa; o resumo vem depois.
- Errar é o dado. O objetivo do bloco de questões é alimentar o caderno de erros, não acertar.
- Dois cadernos por dia. Um coletivo/institucional e um de disciplina clássica, nunca dois iguais.
- Três dúvidas por bloco de G7/Pós. Anotar exatamente três e resolver no dia seguinte.
- Revisão em espiral. O que entrou hoje volta em 24h, em 7 dias e em 30 dias.
- Sábado é aferição. Simulado, correção e caderno de erros. Nada de matéria nova.
- Domingo é descanso. Descanso planejado também é técnica de estudo.

Ao final, um bloco de destaque com borda rosa:
"Regra de ouro do ciclo: nenhum bloco é pulado por falta de vontade — apenas remarcado no mesmo
dia. Bloco perdido é bloco reagendado, nunca bloco cancelado."
```

---

### PROMPT 8 — Aba "Caderno de erros"

```
Preencha a aba "Caderno de erros".

1) Um formulário curto para registrar um erro: disciplina (lista suspensa com Constitucional,
   Coletivo, Penal, Processo Penal, Civil, Processo Civil, Administrativo, Improbidade,
   Consumidor, Ambiental, ECA, Legislação especial), tema, "o que eu errei", "o entendimento
   correto" e "fonte (lei, súmula, informativo)". Botão "Registrar erro".

2) Abaixo, a lista dos erros registrados, mais recentes primeiro, agrupados por disciplina,
   cada um em um cartão recolhível. Cada cartão tem um botão "Revisado hoje" que grava a data
   no campo revisado_em.

3) No topo, três contadores: total de erros registrados, erros nunca revisados e a disciplina
   com mais erros no período — esta última com o rótulo "Sua maior fraqueza agora".

Tudo gravado no PocketBase, na coleção mpe_caderno_erros, apenas para o usuário logado.
```

---

### PROMPT 9 — Indicadores vivos

```
Agora torne o rodapé de indicadores dinâmico:

- "NÍVEL ATUAL" deixa de ser fixo: passa a mostrar o percentual de acertos das últimas 10 sessões
  registradas na coleção mpe_sessoes_questoes (soma de acertos dividida pela soma de total).
  Se não houver nenhuma sessão registrada, mostre o nivel_inicial do plano (50%) e mantenha a
  legenda "diagnóstico inicial"; havendo sessões, a legenda passa a ser "últimas 10 sessões".
- "META DO CICLO", "FASE" e "HORIZONTE" vêm dos campos do plano no banco.
- Quando o nível atual já tiver alcançado a meta do ciclo, o número fica verde.

Adicione também, na aba "Hoje", abaixo do cartão do dia, um formulário de uma linha para
registrar a sessão de questões do dia: disciplina, banca, total de questões e acertos.
```

---

### PROMPT 10 — Acabamento e publicação

```
Faça o acabamento da página /plano, sem alterar o conteúdo:

1. Verifique que tudo funciona em tela de 390px de largura: as abas rolam horizontalmente sem
   quebrar o layout, nenhum texto vaza e nada exige rolagem lateral da página inteira.
2. Estados de carregamento: enquanto os dados do PocketBase carregam, mostre esqueletos de conteúdo,
   nunca a tela em branco.
3. Mensagens de erro amigáveis se o PocketBase falhar ou o usuário perder a sessão.
4. A página deve funcionar bem com a tela do celular no modo escuro.
5. Adicione o título da aba do navegador como "Plano de estudos · Painel MPE".

Depois disso, liste para mim tudo que mudou e me diga o que devo testar antes de publicar.
```

---

### PROMPT 11 — Auditoria: listar todos os erros do projeto

> Use sempre que sentir que "tem coisa quebrada" e antes de qualquer publicação importante.
> A instrução de **não corrigir nada** é a parte mais importante do prompt: um construtor de IA
> solto para "arrumar os erros" costuma quebrar três telas para consertar uma.

```
Não altere NENHUM arquivo agora. Esta tarefa é apenas de diagnóstico.

Faça uma auditoria completa do projeto e me devolva a lista de TODOS os erros e riscos
encontrados, em uma tabela com estas colunas:

GRAVIDADE (Crítico / Alto / Médio / Baixo) | ONDE (arquivo, tela ou coleção) | O QUE ACONTECE |
O QUE QUEBRA PARA O USUÁRIO | CORREÇÃO PROPOSTA (uma linha)

Verifique, no mínimo:

1. Erros de compilação e de build, e erros de tipo (TypeScript), com o arquivo e a linha.
2. Erros e avisos que aparecem no console do navegador ao abrir CADA rota do app, uma por uma.
   Liste as rotas que você testou.
3. Itens do menu lateral que apontam para rota inexistente, página em branco ou tela que
   não carrega.
4. Chamadas ao PocketBase que falham (401, 403, 404), coleções referenciadas no código que não
   existem no banco, e campos usados no código que não existem na coleção correspondente.
5. Coleções cujas API rules permitem acesso a dados de outro usuário, ou que estão abertas
   sem autenticação. Mostre a regra literal de cada uma que estiver irregular.
6. Telas que quebram quando NÃO há dados: usuário novo, lista vazia, registro ausente.
   Diga quais telas você testou nesse cenário.
7. Layout: qualquer tela que estoure a largura em 390px e force rolagem lateral da página.
8. Textos da interface em inglês ou fora do português do Brasil.
9. Dependências quebradas, importações não resolvidas e arquivos órfãos que ninguém usa.
10. Qualquer chave, token ou credencial exposta no código do lado do cliente.

Ao final, separe em três listas:
A) o que você pode corrigir sozinho, com segurança, sem afetar nenhuma outra tela;
B) o que exige uma decisão minha antes;
C) o que é apenas aviso cosmético e pode ficar como está.

Ordene tudo da maior para a menor gravidade. Repito: não corrija nada ainda.
```

**Depois que ele responder:** corrija **um item por vez**, do mais grave para o menos grave,
pedindo um checkpoint antes de cada correção que toque em mais de um arquivo. Nunca mande
"corrija todos os itens da lista A" de uma vez.

---

## 4. Anexo A — A grade da Semana 1 (fonte: o artefato)

> Esta grade foi lida diretamente do artefato, aba **Ciclo de estudos**. Substitui integralmente a
> versão anterior deste anexo, que era uma reconstrução minha e **estava errada** na rotação das
> disciplinas.

**Lógica da semana**, no texto do próprio artefato: semana dedicada 100% ao Bloco 1 (núcleo duro),
com a carga de cada disciplina proporcional ao consolidado de 2.060 questões. Sábado reserva 3
blocos de conteúdo mais a revisão dos erros e a peça discursiva. As disciplinas giram para nunca
repetir no mesmo dia — o cérebro faz força para resgatar a informação, mesclando matérias. Domingo
é descanso e planejamento, sem exceção.

### A grade

| DIA | LEI SECA · 1h | QUESTÕES · 1h | CADERNO · 2h | CADERNO · 2h | G7/PÓS · 2h |
|---|---|---|---|---|---|
| **1º DIA** | Constitucional | Penal | Tutela Difusos e Coletivos | Civil | Processo Penal |
| **2º DIA** | Legislação Penal Especial | Processo Penal | Administrativo | Constitucional | Processo Civil |
| **3º DIA** | Tutela Difusos e Coletivos | Civil | Processo Penal | Penal | Legislação Penal Especial |
| **4º DIA** | Penal | Constitucional | Civil | Tutela Difusos e Coletivos | Administrativo |
| **5º DIA** | Processo Penal | Tutela Difusos e Coletivos | Penal | Constitucional | Processo Civil |
| **6º DIA** | Constitucional | Revisão dos erros | Tutela Difusos e Coletivos | Legislação Penal Especial | Jurisprudência + discursiva |
| **7º DIA** | *Descanso — reler os cadernos, anotar o que travou e pedir o plano da semana 2* | | | | |

8h por dia · 48h na semana.

### Horas por disciplina na semana

| Disciplina | Horas |
|---|---|
| Tutela Difusos e Coletivos | 8h |
| Constitucional | 7h |
| Processo Penal | 6h |
| Penal | 6h |
| Legislação Penal Especial | 5h |
| Civil | 5h |
| Processo Civil | 4h |
| Administrativo | 4h |
| Revisão dos erros + jurisprudência e discursiva | 3h |

*As 3h de Infância e Juventude foram redistribuídas dentro do Bloco 1 (1h para Tutela Coletiva,
1h para Constitucional, 1h devolvida a Civil), porque o Bloco 1 foi definido sem ela.*

Confere: 8+7+6+6+5+5+4+4+3 = 48h.

### Detalhamento do 1º dia (do artefato, aba "Hoje")

| Ordem | Duração | Tipo | Conteúdo |
|---|---|---|---|
| 1 | 1h | Lei seca | CF, arts. 127 a 130-A: o Ministério Público. Leitura limpa, grifo só no artigo-chave. É o tema de maior retorno do plano inteiro. |
| 2 | 1h | Questões | 20 a 30 de Penal, teoria do crime, só de bancas de MP. É diagnóstico: o objetivo é medir e alimentar o caderno de erros, não acertar. |
| 3 | 2h | Caderno | Tutela Coletiva: abrir a estrutura e fechar a trindade (difusos, coletivos, individuais homogêneos) com as Súmulas 601 e 594 do STJ. |
| 4 | 2h | Caderno | Civil: abrir a estrutura e fechar a parte geral (LINDB, pessoa natural, personalidade, capacidade). |
| 5 | 2h | G7/Pós | Processo Penal: sistema acusatório, inquérito e ação penal. Anotar as três dúvidas que sobrarem. |

### Temas sugeridos para os dias 2 a 6

> A **disciplina** de cada bloco é a da grade acima e não se mexe. O **tema** abaixo é sugestão
> minha, coerente com a Fase 1 — ajuste ao seu edital.

**2º DIA** — Lei seca: Lei 11.343/06, arts. 28, 33 a 35 e 40 · Questões: Processo Penal, inquérito
e ação penal · Caderno: Administrativo, princípios e atos administrativos · Caderno:
Constitucional, funções institucionais do MP e garantias · G7/Pós: Processo Civil, tutela
provisória e competência.

**3º DIA** — Lei seca: Lei 7.347/85 (LACP), arts. 1º a 5º e 11 a 13 · Questões: Civil, parte geral
· Caderno: Processo Penal, prisões e medidas cautelares · Caderno: Penal, teoria do crime
(conduta, tipicidade, nexo) · G7/Pós: Legislação Penal Especial, Lei de Drogas e Lei de Tortura.

**4º DIA** — Lei seca: CP, arts. 13 a 25 · Questões: Constitucional, MP e direitos fundamentais ·
Caderno: Civil, negócio jurídico (existência, validade, eficácia) · Caderno: Tutela Coletiva,
inquérito civil e TAC (Resolução 23 do CNMP) · G7/Pós: Administrativo, improbidade (Lei 8.429 com
a redação da Lei 14.230/21).

**5º DIA** — Lei seca: CPP, arts. 4º a 23 · Questões: Tutela Difusos e Coletivos · Caderno: Penal,
culpabilidade e concurso de pessoas · Caderno: Constitucional, controle de constitucionalidade ·
G7/Pós: Processo Civil, aplicação subsidiária ao processo coletivo.

**6º DIA** — Lei seca: releitura dos artigos grifados na semana · Questões: revisão dos erros da
semana, reescrevendo cada erro como tese correta · Caderno: Tutela Coletiva, liquidação, execução
e coisa julgada (art. 16 da LACP e Tema 1075 do STF) · Caderno: Legislação Penal Especial, Lei
8.072/90 e Lei 9.455/97 · G7/Pós: jurisprudência recente de STF e STJ + uma peça ou discursiva de
MP com espelho.

---

## 5. Anexo B — Checklist antes de publicar

- [ ] Consigo entrar com meu e-mail e senha e a página /plano abre.
- [ ] A aba "Hoje" mostra o dia correto da semana.
- [ ] Marco um bloco como concluído, fecho o app, abro de novo e ele continua marcado.
- [ ] Um segundo usuário de teste não vê nada dos meus dados (teste as API rules).
- [ ] A página inteira funciona no celular sem rolagem lateral.
- [ ] O registro de sessão de questões altera o indicador "NÍVEL ATUAL".
- [ ] O caderno de erros grava e lista corretamente.
- [ ] O restante do painel (login, páginas antigas) continua igual.

---

## 6. Ordem de execução sugerida

| Sessão | Prompts | Tempo estimado |
|---|---|---|
| 1ª | 0, 1, 2 | ~40 min |
| 2ª | 3, 4, 5 | ~50 min |
| 3ª | 6, 7 | ~30 min |
| 4ª | 8, 9, 10 | ~50 min |
| Sempre que precisar | 11 (auditoria de erros) | ~15 min |

Publique ao final de cada sessão, não no meio.

---

## 7. Log de correções pós-auditoria (Prompt 11)

A auditoria devolveu 9 itens. A gravidade atribuída pelo próprio construtor foi **corrigida**:
ele classificou a senha de administrador exposta como "Baixo" e um link quebrado como "Crítico".
É o inverso.

### Ordem de correção (uma por vez, nesta sequência)

| # | Item | Gravidade real | Por quê |
|---|---|---|---|
| 1 | Senha de administrador real dentro de uma migração | **Crítico** | Admin do PocketBase = todos os dados de todos os usuários. Credencial em migração fica no histórico do projeto. |
| 2 | `mpe_planos` e `mpe_blocos` sem migração no ambiente | **Crítico** | Contradiz o que foi informado antes. Sem migração, as coleções somem numa republicação em ambiente limpo. |
| 3 | `agregarPorDisciplina` ignora a disciplina `geral` | **Alto** | Não quebra a tela: **mente**. Simulados gerais somem do Desempenho e do ROI, e a decisão de estudo passa a se apoiar em número errado. |
| 4 | Bloqueios de autenticação antigos em 9 páginas + `IaModule` | **Alto** | Dois caminhos de login convivendo é como se entra por engano onde não devia. |
| 5 | Link quebrado para `/painel-mpe/ia/corretor-discursivas` | Médio | Uma tela em branco. Incômodo, não risco. |
| 6 | `carregarDados` sem tratamento de erro | Médio | Falha silenciosa parece tela vazia; o usuário acha que perdeu dados. |
| 7 | 4 avisos de dependência de `useEffect` (ESLint) | Baixo | Pode gerar dado velho em tela; corrigir com cuidado, mexer em dependência causa laço infinito. |
| 8 | Coleção `contact_form` e gancho de e-mail órfãos | Baixo | Só limpeza. |
| 9 | Dados semeados da conta demo removida | **Não é erro** | Decisão: o painel deve começar vazio e ser preenchido com dado real. Dado de demonstração num painel de medição é ruído. |

### O que a auditoria NÃO fez

Foi inspeção estática (ESLint, busca no código, esquema do PocketBase). **Não** houve teste em
navegador, console real por rota, teste de tela vazia com usuário novo, nem verificação visual a
390px. Os itens 2, 6 e 7 do Prompt 11 continuam abertos e exigem uma rodada de teste real antes
da publicação.

### Ajuste: o painel é de usuário único

O usuário confirmou que **só ele** acessa o site, que foi feito exclusivamente para ele.
Isso muda três itens — e não muda os dois primeiros.

**O que muda**

- O item 9 (dados da conta demo) fica decidido: painel começa vazio, sem semear nada.
- O risco de "um usuário ver dados de outro" deixa de ser cenário real. As API rules por dono
  continuam corretas e ficam como estão, mas saem da lista de prioridades.
- Surge um item novo, que a auditoria não levantou: se existe opção **"Criar conta"** aberta na
  tela de login, o painel não é exclusivo — qualquer pessoa que chegue ao endereço pode se
  cadastrar. Fechar o cadastro público passa a ser gravidade **Alta**, e resolve junto com o
  item 4 (bloqueios de autenticação antigos).

**O que não muda**

- Item 1 (senha de admin em migração) continua **Crítico**. Usuário único descreve quem
  *deveria* entrar, não quem *consegue*. O site está na internet aberta, o endereço do painel
  de administração do PocketBase é previsível, e uma credencial que vazou para um arquivo
  publicado serve para qualquer pessoa que a encontre. Intenção não é controle de acesso.
- Item 2 (coleções sem migração) continua **Crítico** e fica até pior: com um único usuário,
  os dados perdidos numa republicação são exatamente os dele, sem nenhuma outra cópia.
- Item 3 (`agregarPorDisciplina` ignorando `geral`) continua o mais insidioso: o número errado
  é o dele, e é sobre ele que a decisão de estudo será tomada.

### Ordem revisada

1. Senha de admin em migração (**Crítico**) — e trocar a senha depois, fora do chat.
2. `mpe_planos` e `mpe_blocos` sem migração (**Crítico**).
3. Fechar o cadastro público de novas contas (**Alto** — item novo).
4. `agregarPorDisciplina` ignora `geral` (**Alto**).
5. Bloqueios de autenticação antigos em 9 páginas + `IaModule` (**Alto**).
6. Link quebrado do corretor de discursivas (Médio).
7. `carregarDados` sem tratamento de erro (Médio).
8. Avisos de `useEffect` (Baixo).
9. `contact_form` órfã (Baixo).

### Andamento

**Item 1 — senha de admin em migração: RESOLVIDO.**
- Arquivo `apps/pocketbase/pb_migrations/1789065011_create_definitive_admin.js`, linhas 20 e 63:
  senha removida e substituída por `MPE_ADMIN_PASSWORD` e `MPE_DEMO_PASSWORD`, com interrupção
  da execução se as variáveis não estiverem definidas.
- Descoberta relevante: a credencial exposta era a da **conta de login do próprio painel**, não a
  do superusuário do PocketBase. São contas diferentes.
- O painel não tinha tela para trocar a senha da própria conta. Foi criada em
  Configurações → Perfil → "Alterar senha", exigindo a senha atual.
- Senha trocada pelo usuário em 10/09/2026.

**Pendente de confirmação:** as credenciais nas outras três migrações
(`1789047573_seed_biblioteca_test_docs.js`, `1789050400_create_mpe_dados_estudo.js`,
`1789056600_create_mpe_discursivas.js`) e na documentação foram limpas? A varredura final do
projeto não foi reportada. Cobrar antes de publicar.

**Também sem resposta:** a conta de demonstração de `MPE_DEMO_PASSWORD` ainda existe e consegue
fazer login? Entra junto com o item 3 (fechar o cadastro público).

---

## 8. Integração com o OneDrive (somente leitura)

**Objetivo definido pelo usuário:** o painel lê os arquivos de uma pasta específica do OneDrive
pessoal (`01-ESTUDOS PARA MPE-2026`) e os exibe dentro da Biblioteca Jurídica. Nada é gravado de
volta. O trabalho de conteúdo continua no OneDrive e no ChatGPT; o painel passa a ser a vitrine e
o medidor.

### Por que isso não é "só um prompt"

Ler o OneDrive de outra pessoa exige autorização da Microsoft. O caminho é:

1. **Registro do aplicativo** no Microsoft Entra ID — só o usuário pode fazer, com a própria conta.
2. **OAuth 2.0** (fluxo de código de autorização) para o usuário conceder acesso uma vez.
3. **Microsoft Graph** para listar e baixar os arquivos da pasta.
4. **Um componente no servidor** — hook do PocketBase — porque o segredo do cliente e o token de
   atualização **não podem** ficar no navegador. Este é o ponto que mais gente erra.

### Passo 1 — o que só o usuário pode fazer (antes de qualquer prompt)

No portal do Azure (`portal.azure.com`), com a conta Microsoft do OneDrive:

1. **Microsoft Entra ID → Registros de aplicativo → Novo registro**
   - Nome: `Painel MPE`
   - Tipos de conta com suporte: *Contas em qualquer diretório organizacional e contas pessoais
     da Microsoft* (o OneDrive pessoal exige a opção que inclui contas pessoais).
   - URI de Redirecionamento: tipo **Web**, valor `https://SEU-DOMINIO/onedrive/callback`
     (substituir pelo domínio real do painel publicado).
2. Anotar, na tela de visão geral: **ID do aplicativo (cliente)** e **ID do diretório (locatário)**.
3. **Certificados e segredos → Novo segredo do cliente**. Copiar o **Valor** na hora — ele só
   aparece uma vez.
4. **Permissões de API → Adicionar → Microsoft Graph → Permissões delegadas**:
   `Files.Read`, `offline_access`, `User.Read`. Conceder consentimento.

### O que trazer de volta

- ID do aplicativo (cliente) — pode ser dito em texto, não é secreto.
- ID do diretório (locatário).
- A URL exata do painel publicado, para conferir o URI de redirecionamento.
- O **segredo do cliente NÃO** deve ser colado em nenhuma conversa. Ele vai direto para a
  variável de ambiente do projeto, no painel da Hostinger.

### Regras da implementação (para o prompt, depois)

- Segredo do cliente e token de atualização vivem **apenas** no servidor (hook do PocketBase),
  nunca no código do navegador, nunca em `localStorage`.
- Token de atualização guardado em coleção própria, com API rules restritas ao dono.
- Escopo `Files.Read` apenas. Nada de `Files.ReadWrite`, que não é necessário para ler.
- A pasta é fixada por configuração, não por busca no drive inteiro.
- Uma tela de "Conectar ao OneDrive" em Configurações, com botão de desconectar que apaga o token.

### Quando fazer

**Depois** de fechar os itens 1 a 5 da auditoria — em especial o item 5 (bloqueios de autenticação
antigos). Construir uma integração de autenticação nova em cima de dois caminhos de login
convivendo é a receita para um defeito que ninguém acha depois.

**Item 2 — coleções sem migração: RESOLVIDO.**
- As quatro coleções `mpe_` não tinham migração. Foram criadas:
  `1789076843_create_mpe_planos.js`, `1789076844_create_mpe_blocos.js`,
  `1789076845_create_mpe_sessoes_questoes.js`, `1789076846_create_mpe_caderno_erros.js`.
- Todas idempotentes, sem apagar coleção ou registro e sem semear dados.
- Nenhum registro foi perdido: as quatro estavam com 0 registros.

**Descoberta operacional mais importante até aqui:**
**visualização e publicação usam bancos de dados SEPARADOS.** O que é digitado na
pré-visualização do Horizons não vai para o site publicado. As migrações rodam na inicialização
do ambiente publicado e criam as coleções vazias lá.

> **Regra permanente: dado real de estudo só se lança no site PUBLICADO.**
> A pré-visualização é para testar a tela, nunca para registrar questões, erros ou blocos
> concluídos. O que for digitado lá se perde.

**Contradição a observar:** na criação das coleções, o construtor afirmou tê-las criado e
informou que o projeto passara a ter 12 coleções. O diagnóstico do item 2 mostrou que não havia
migração nenhuma. Nada se perdeu porque não havia dados — mas serve de lembrete: confirmar o que
o construtor afirma ter feito, sempre.
