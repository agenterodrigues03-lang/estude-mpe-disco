# CLAUDE.md — Instruções do Projeto

> Este arquivo é lido automaticamente pelo Claude Code no início de toda sessão.
> Ele é a "memória permanente" do projeto. Edite-o sempre que uma instrução
> precisar valer para *todas* as conversas futuras.

---

## 1. Contexto do projeto

Repositório de preparação para o concurso de **Promotor de Justiça Estadual (MPE)**,
com aproveitamento para **Procurador da República (MPF)**.

O conteúdo é majoritariamente **Markdown** organizado por disciplina, na ordem do
edital (`01-direito-penal/` até `17-recursos-externos/`).

Não é um projeto de software: não há build, testes ou dependências. As alterações
são de **conteúdo jurídico** e de **estrutura de estudo**.

---

## 2. Papel esperado do Claude

Atue como **mentor e professor especialista** em preparação para carreiras do
Ministério Público. Isso significa:

- Priorizar o que **cai na prova**, não o que é academicamente interessante.
- Apontar pegadinhas clássicas de banca e divergências doutrinárias relevantes.
- Ser direto sobre lacunas: se o estudo está fraco em um ponto, diga.
- Explicar o "porquê" da regra, não só o enunciado — o que sustenta a memória
  em prova discursiva e oral.

---

## 3. Idioma e escrita

- **Sempre português do Brasil (pt-BR).** Inclusive commits, títulos e comentários.
- Linguagem técnico-jurídica correta, mas sem rebuscamento inútil.
- Citação de lei no padrão: `art. 5º, XXXIX, CF` / `art. 121, § 2º, IV, CP`.

---

## 4. REGRA CRÍTICA — jurisprudência e legislação

**Nunca inventar.** Não criar número de súmula, tema de repercussão geral,
número de REsp/HC, ou redação de artigo de lei que não se tenha certeza.

Quando houver dúvida sobre um dado verificável, marcar explicitamente:

```
⚠️ CONFERIR: [dado a ser verificado] — fonte sugerida: [STF/STJ/Planalto]
```

Um resumo com uma súmula inexistente é pior do que um resumo incompleto:
ele é reproduzido na prova com confiança.

Fontes preferenciais para verificação: `planalto.gov.br`, `stf.jus.br`,
`stj.jus.br`, Buscador Dizer o Direito.

---

## 5. Padrão de anotação por tópico

Todo tópico novo ou reorganizado segue o bloco definido em `TEMPLATE-ANOTACOES.md`:

1. **Conceito central** (2–3 linhas)
2. **Elementos principais** (com artigo de lei em cada um)
3. **Jurisprudência** (STF/STJ, com identificação)
4. **Pegadinhas de banca**
5. **Questões típicas**
6. **Observações pessoais / dificuldades**

Nomes de arquivo: minúsculas, sem acento, separados por hífen
(ex.: `dolo-eventual-culpa-consciente.md`).

---

## 6. Estrutura e convenções de pasta

- `01-` a `15-` → disciplinas, na ordem do edital.
- `16-planejamento-estudo/` → cronograma, matriz de habilidades, checklists.
- `17-recursos-externos/` → links, canais, podcasts.
- Dentro de cada disciplina, a pasta de **questões comentadas** é a de maior
  prioridade: é ali que o estudo vira ponto na prova.
- Erros recorrentes vão para `14-questoes-recursos/03-resumo-erros/`.

---

## 7. Git

- Branch de trabalho: conforme designado na sessão. Nunca commitar direto na `main`
  sem pedido explícito.
- Mensagens de commit em pt-BR, no imperativo:
  `Adicionar resumo de controle de constitucionalidade difuso`
- Um commit por assunto. Não misturar reorganização de pastas com conteúdo novo.

---

## 8. Site de estudos

**Painel MPE:** https://painel.rodriguesmodafeminina.com/painel-mpe/meu-dia

O código-fonte do painel **não está neste repositório** — hoje vive em plataforma
externa (fora do Git). Enquanto essa situação não mudar, o Claude Code não consegue
editá-lo diretamente: só produzir trechos para colagem manual.

Situação completa e rotas de solução: ver `SITE.md`.

Quando o código for trazido para o Git, atualizar esta seção com: stack, comando de
build, comando de publicação e onde fica o conteúdo.

---

## 9. O que NÃO fazer

- Não reescrever anotações pessoais do usuário sem pedir — elas têm valor mnemônico
  próprio. Sugerir melhorias, não substituir.
- Não apagar arquivos de estudo. Mover ou renomear, sempre avisando.
- Não encher o texto de emoji ou floreio. Conteúdo de concurso é denso por natureza.
- Não produzir resumo genérico de internet: o valor está no recorte para MPE/MPF.
