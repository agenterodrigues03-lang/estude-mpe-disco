# 🛠️ Guia de Configuração do Claude Code

> Guia prático para usar o Claude Code como ferramenta de estudo e de
> desenvolvimento do painel MPE. Leia uma vez com calma; depois use como consulta.

---

## 1. Os três níveis de configuração

Entender a hierarquia evita 90% da frustração com a ferramenta.

| Nível | Arquivo | Vale para | Use para |
|---|---|---|---|
| **Pessoal** | `~/.claude/CLAUDE.md` | Todos os seus projetos | Seu estilo, seu idioma, como você quer ser tratado |
| **Projeto** | `CLAUDE.md` (raiz do repo) | Este projeto, para qualquer pessoa | Regras do conteúdo, convenções, o que não fazer |
| **Local** | `.claude/settings.local.json` | Só você, neste projeto | Permissões e ajustes que não vão para o Git |

Regra prática: **se a instrução vale para amanhã também, ela vai para um arquivo.**
Se vale só para agora, vai no chat.

---

## 2. `CLAUDE.md` — o arquivo que mais importa

É lido automaticamente no início de toda sessão. É o que impede você de repetir
"responda em português", "siga o template", "não invente súmula" toda vez.

O que já está configurado neste repositório:

- Contexto do concurso e papel de mentor
- Idioma pt-BR obrigatório
- **Regra crítica antialucinação**: nunca inventar súmula, tema de repercussão
  geral ou artigo de lei — o que for duvidoso vem marcado com `⚠️ CONFERIR`
- Padrão de anotação por tópico
- Convenções de pasta e de commit

**Como manter:** durante uma conversa, digite `#` seguido da instrução e ela é
gravada no `CLAUDE.md` automaticamente. Ex.: `# sempre incluir a posição do MPF
quando divergir do STJ`.

⚠️ Cuidado: um `CLAUDE.md` gigante perde eficácia. Mantenha enxuto e específico.
Instrução vaga ("seja bom") não muda nada; instrução concreta ("marque
⚠️ CONFERIR quando não tiver certeza do número da súmula") muda tudo.

---

## 3. `.claude/settings.json` — parar de autorizar a mesma coisa

Sem isso, o Claude pede permissão a cada comando e o trabalho trava.
Já está configurado aqui com:

- **Permitido**: leitura de arquivos, busca, edição, operações normais de Git,
  e consulta web restrita a `planalto.gov.br`, `stf.jus.br`, `stj.jus.br` e
  Dizer o Direito — as fontes que importam para conferir jurisprudência.
- **Bloqueado**: `rm -rf`, `git push --force`, `git reset --hard` e leitura de
  arquivos de segredo (`.env`, `.key`).

Essa lista de bloqueio não é decoração. Ela é a sua rede de segurança quando você
estiver cansado às 23h e aprovar algo sem ler.

Comando útil: `/permissions` mostra e edita as permissões pela interface.

---

## 4. Comandos personalizados (`/`)

Arquivos em `.claude/commands/`. Cada `.md` vira um comando. Já criados:

| Comando | O que faz |
|---|---|
| `/topico penal dolo eventual` | Cria a anotação no padrão do caderno, com pegadinhas de banca |
| `/questao [cola a questão]` | Comenta alternativa por alternativa e registra o erro no caderno de erros, com contador de recorrência |
| `/revisar controle de constitucionalidade` | Faz 10 perguntas **antes** de mostrar resposta — revisão ativa, não releitura |
| `/lacunas constitucional` | Diagnóstico honesto do que falta, priorizado por incidência em prova e por erro recorrente |

Para criar outro: basta um novo `.md` na pasta. `$ARGUMENTS` recebe o que você
digitar depois do comando.

---

## 5. Skills

Você já possui skills instaladas que se encaixam neste projeto:

- `organizar-caderno` — reorganiza e padroniza uma disciplina inteira
- `matriz-skills-mp` — diagnóstico de nível e priorização, gera planilha
- `portugues-brasil` — padroniza a escrita em pt-BR

Diferença para comandos: a **skill** é acionada sozinha quando o assunto aparece;
o **comando** você dispara manualmente. Use skill para processos longos e
repetíveis, comando para ações rápidas do dia a dia.

---

## 6. Como conversar (isto vale mais que qualquer arquivo)

1. **Modo plano** (`Shift+Tab`) antes de tarefa grande. O Claude planeja, você
   aprova, só então ele executa. Evita retrabalho caro.
2. **`/clear` entre assuntos.** Contexto sujo é a principal causa de resposta ruim.
   Terminou Penal e vai para Constitucional? `/clear`.
3. **Dê critério de aceite.** Não peça "melhora o painel". Peça: "na tela Meu Dia,
   as tarefas concluídas devem sumir da lista principal e aparecer num contador
   no rodapé".
4. **Aponte o arquivo.** "Ajuste `03-direito-constitucional/04-controle.../adi.md`"
   rende muito mais que "ajuste o resumo de ADI".
5. **Peça revisão do próprio trabalho**: "releia o que você escreveu e marque o que
   não tem certeza". Funciona bem e pega erro de jurisprudência.

---

## 7. Fluxo de trabalho para o painel MPE

O Claude Code edita **arquivos**. Para ele melhorar o painel, o **código-fonte do
painel precisa estar em um repositório Git** acessível.

Enquanto o painel estiver apenas publicado numa plataforma fechada, o ciclo é
manual e lento (o Claude gera, você copia e cola). Com o código no GitHub, o ciclo
vira: pedir → ele altera → você revisa o diff → publica.

**Trazer o código para o Git é o passo de maior retorno deste projeto.**
Ver `SITE.md` para o estado atual e as rotas possíveis.

---

## 8. Erros comuns de quem está começando

- ❌ Sessão eterna sem `/clear` → respostas pioram progressivamente.
- ❌ Pedir coisas gigantes de uma vez → prefira etapas verificáveis.
- ❌ Aceitar jurisprudência sem conferir → confira todo `⚠️ CONFERIR` antes de decorar.
- ❌ Trabalhar direto na `main` → sempre em branch, sempre revisando o diff.
- ❌ Colocar tudo no `CLAUDE.md` → instrução demais dilui as que importam.
