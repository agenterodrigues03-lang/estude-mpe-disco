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
- [x] O projeto **já tem tabelas** no Supabase (quais, ainda não sabemos).

Por isso, todas as tabelas novas usam o prefixo `mpe_`: elimina qualquer risco de colisão de nome
com o que já existe, sem precisar auditar o banco antes.

### Caminho B — eu mesmo escrevo o código
Aí preciso de:

1. **O projeto Horizons conectado a um repositório GitHub** (Horizons → menu ☰ → *GitHub / Exportar*)
   e o repositório liberado para esta sessão. Sem isso não alcanço o código do site.
2. As credenciais **não** — nunca me envie senha, `service_role key` ou `.env`. Só o repositório.
3. O texto completo das outras abas do artefato. Nas imagens só consigo ler a aba **Hoje** e os
   indicadores (50% → 55%, Fase 1/5, horizonte 18–30m). O conteúdo de **Ciclo de estudos**,
   **Semana 1 · bloco a bloco** e **Rotina** eu reconstruí a partir da lógica visível — está no
   Anexo A e você revisa antes de publicar.

### O que já sei do ambiente
- Hostinger **Horizons** gera app **React + Vite + TailwindCSS + shadcn/ui**, com **Supabase** para
  banco e autenticação. Os prompts abaixo já assumem essa stack.
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
(páginas, tabelas no Supabase e se o login já está funcional).
```

---

### PROMPT 1 — Estrutura de dados (Supabase)

```
IMPORTANTE — leia antes de executar:
- NÃO altere, renomeie nem apague nenhuma tabela, coluna ou política que já exista no projeto.
- O login já funciona: use a autenticação existente, não crie outra.
- Crie apenas tabelas NOVAS, todas com o prefixo mpe_, usando CREATE TABLE IF NOT EXISTS.
- Se alguma dessas tabelas já existir, pare e me avise em vez de alterar.

Crie no Supabase as tabelas abaixo, com RLS (Row Level Security) ativada e políticas que permitam
a cada usuário ler e escrever APENAS as próprias linhas (comparando user_id com auth.uid()).

1) mpe_planos
   - id (uuid, pk)
   - user_id (uuid, referência a auth.users)
   - titulo (text)
   - ciclo_atual (int, default 1)
   - semana_atual (int, default 1)
   - fase_atual (int, default 1)
   - total_fases (int, default 5)
   - nivel_inicial (numeric, default 50)     -- % de acertos do diagnóstico
   - meta_ciclo (numeric, default 55)        -- % alvo
   - meta_semana (int, default 8)
   - horizonte (text, default '18-30m')
   - created_at (timestamptz, default now())

2) mpe_blocos
   - id (uuid, pk)
   - user_id (uuid)
   - plano_id (uuid, referência a mpe_planos)
   - ciclo (int), semana (int)
   - dia_semana (int)          -- 1 = segunda ... 6 = sábado
   - ordem (int)               -- posição do bloco dentro do dia
   - duracao_horas (numeric)   -- 1 ou 2
   - tipo (text)               -- 'lei_seca' | 'questoes' | 'caderno' | 'g7_pos' | 'simulado' | 'revisao'
   - disciplina (text)
   - descricao (text)
   - concluido (boolean, default false)
   - concluido_em (timestamptz, null)

3) mpe_sessoes_questoes
   - id (uuid, pk)
   - user_id (uuid)
   - data (date)
   - disciplina (text)
   - banca (text)
   - total (int)
   - acertos (int)
   - observacoes (text)

4) mpe_caderno_erros
   - id (uuid, pk)
   - user_id (uuid)
   - data (date, default hoje)
   - disciplina (text)
   - tema (text)
   - erro (text)              -- o que eu errei
   - correcao (text)          -- o entendimento correto
   - fonte (text)             -- lei, súmula, informativo
   - revisado_em (date, null)

Não crie nenhuma tela ainda. Só o banco e as políticas de segurança.
Depois me mostre o SQL que foi executado.
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

Os blocos devem vir da tabela "mpe_blocos" do Supabase, filtrando pelo ciclo e semana atuais do
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
Crie uma rotina de carga inicial (seed) que insira na tabela "mpe_blocos" os blocos do Ciclo 1,
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
- Marcar ou desmarcar a caixa deve gravar imediatamente no Supabase (campos "concluido" e
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

Tudo gravado no Supabase, na tabela mpe_caderno_erros, apenas para o usuário logado.
```

---

### PROMPT 9 — Indicadores vivos

```
Agora torne o rodapé de indicadores dinâmico:

- "NÍVEL ATUAL" deixa de ser fixo: passa a mostrar o percentual de acertos das últimas 10 sessões
  registradas na tabela mpe_sessoes_questoes (soma de acertos dividida pela soma de total).
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
2. Estados de carregamento: enquanto os dados do Supabase carregam, mostre esqueletos de conteúdo,
   nunca a tela em branco.
3. Mensagens de erro amigáveis se o Supabase falhar.
4. A página deve funcionar bem com a tela do celular no modo escuro.
5. Adicione o título da aba do navegador como "Plano de estudos · Painel MPE".

Depois disso, liste para mim tudo que mudou e me diga o que devo testar antes de publicar.
```

---

## 4. Anexo A — Conteúdo da Semana 1, bloco a bloco

> **Segunda-feira** é exatamente o que está no artefato original.
> **Terça a sábado** foram construídos por mim seguindo a mesma lógica — revise e ajuste ao seu
> edital antes de carregar.

### SEGUNDA-FEIRA · PRIMEIRO DIA (8h)
| Ordem | Duração | Tipo | Conteúdo |
|---|---|---|---|
| 1 | 1h | Lei seca | CF, arts. 127 a 130-A: o Ministério Público. Leitura limpa, grifo só no artigo-chave. É o tema de maior retorno do plano inteiro. |
| 2 | 1h | Questões | 20 a 30 de Penal, teoria do crime, só de bancas de MP. É diagnóstico: o objetivo é medir e alimentar o caderno de erros, não acertar. |
| 3 | 2h | Caderno | Tutela Coletiva: abrir a estrutura e fechar a trindade (difusos, coletivos, individuais homogêneos) com as Súmulas 601 e 594 do STJ. |
| 4 | 2h | Caderno | Civil: abrir a estrutura e fechar a parte geral (LINDB, pessoa natural, personalidade, capacidade). |
| 5 | 2h | G7/Pós | Processo Penal: sistema acusatório, inquérito e ação penal. Anotar as três dúvidas que sobrarem. |

### TERÇA-FEIRA · SEGUNDO DIA (8h)
| Ordem | Duração | Tipo | Conteúdo |
|---|---|---|---|
| 1 | 1h | Lei seca | Lei 7.347/85 (LACP), arts. 1º a 5º e 11 a 13. É a espinha dorsal da atuação coletiva. |
| 2 | 1h | Questões | 20 a 30 de Constitucional, funções institucionais do MP, bancas de MP. Fechar o ciclo com a lei seca de ontem. |
| 3 | 2h | Caderno | Tutela Coletiva: legitimidade ativa, litispendência e conexão nas ações coletivas. |
| 4 | 2h | Caderno | Civil: negócio jurídico — planos de existência, validade e eficácia; defeitos e invalidade. |
| 5 | 2h | G7/Pós | Penal: teoria do crime — conduta, tipicidade, nexo causal e imputação objetiva. Três dúvidas. |

### QUARTA-FEIRA · TERCEIRO DIA (8h)
| Ordem | Duração | Tipo | Conteúdo |
|---|---|---|---|
| 1 | 1h | Lei seca | CPP, arts. 4º a 23 (inquérito) e 24 a 42 (ação penal). Grifar prazos e titularidade. |
| 2 | 1h | Questões | 20 a 30 de Tutela Coletiva, bancas de MP. Primeira medição da matéria-eixo da Fase 1. |
| 3 | 2h | Caderno | Constitucional: controle de constitucionalidade — estrutura geral, ADI, ADC, ADPF e legitimados. |
| 4 | 2h | Caderno | Administrativo: princípios expressos e implícitos; atos administrativos (atributos e elementos). |
| 5 | 2h | G7/Pós | Civil: LINDB aplicada e prescrição e decadência. Três dúvidas. |

### QUINTA-FEIRA · QUARTO DIA (8h)
| Ordem | Duração | Tipo | Conteúdo |
|---|---|---|---|
| 1 | 1h | Lei seca | CDC, arts. 1º a 7º, 12 a 17 e 81 a 104. A parte processual coletiva do CDC é a mais cobrada. |
| 2 | 1h | Questões | 20 a 30 de Processo Penal, inquérito e ação penal. Fechar com a lei seca de ontem. |
| 3 | 2h | Caderno | Inquérito civil e compromisso de ajustamento de conduta: Resolução 23 do CNMP, prazos e arquivamento. |
| 4 | 2h | Caderno | Penal: culpabilidade, erro de tipo e de proibição, concurso de pessoas. |
| 5 | 2h | G7/Pós | Constitucional: controle concentrado — efeitos da decisão, modulação e reclamação. Três dúvidas. |

### SEXTA-FEIRA · QUINTO DIA (8h)
| Ordem | Duração | Tipo | Conteúdo |
|---|---|---|---|
| 1 | 1h | Lei seca | Lei 8.429/92 com a redação da Lei 14.230/21: arts. 9º, 10, 11, 12, 17 e 17-B. Atenção ao dolo específico. |
| 2 | 1h | Questões | 20 a 30 de Administrativo e Improbidade, bancas de MP. |
| 3 | 2h | Caderno | Ambiental: princípios, competências, licenciamento e responsabilidade civil ambiental (Súmula 613 do STJ). |
| 4 | 2h | Caderno | Infância e Juventude: medidas protetivas, medidas socioeducativas e a atuação do MP no ECA. |
| 5 | 2h | G7/Pós | Tutela Coletiva: liquidação, execução e coisa julgada (art. 16 da LACP e Tema 1075 do STF). Três dúvidas. |

### SÁBADO · SEXTO DIA (8h) — Aferição
| Ordem | Duração | Tipo | Conteúdo |
|---|---|---|---|
| 1 | 2h | Simulado | 60 questões multibanca, cronometradas, misturando as cinco disciplinas da semana. |
| 2 | 1h | Revisão | Correção do simulado sem consultar material: por que a errada está errada. |
| 3 | 2h | Revisão | Caderno de erros da semana inteira: reescrever cada erro como uma frase de tese correta. |
| 4 | 1h | Lei seca | Releitura rápida dos cinco textos legais da semana, só nos artigos grifados. |
| 5 | 2h | G7/Pós | Uma peça ou discursiva de MP com espelho de correção. Comparar e anotar o que faltou. |

### DOMINGO — Descanso
Sem blocos. No máximo, 30 minutos de releitura passiva do caderno de erros, se der vontade.
Se não der, não faça.

---

## 5. Anexo B — Checklist antes de publicar

- [ ] Consigo entrar com meu e-mail e senha e a página /plano abre.
- [ ] A aba "Hoje" mostra o dia correto da semana.
- [ ] Marco um bloco como concluído, fecho o app, abro de novo e ele continua marcado.
- [ ] Um segundo usuário de teste não vê nada dos meus dados (teste a RLS).
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

Publique ao final de cada sessão, não no meio.
