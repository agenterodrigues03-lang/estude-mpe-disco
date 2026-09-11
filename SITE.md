# 🌐 Painel MPE — situação e rotas

## Estado atual

- **URL:** https://painel.rodriguesmodafeminina.com/painel-mpe/meu-dia
- **Finalidade:** painel pessoal de estudos (uso individual, não público)
- **Código-fonte:** ❌ não está neste repositório e não está em nenhum repositório
  GitHub liberado para esta sessão
- **Plataforma/stack:** ⚠️ A DEFINIR — não foi possível inspecionar o site a partir
  do ambiente remoto (o domínio é bloqueado pela política de rede da sessão)

> O domínio-raiz (`rodriguesmodafeminina.com`) pertence a outro projeto; o painel
> vive em um subdomínio reaproveitado.

---

## Por que isso importa

O Claude Code trabalha editando arquivos versionados em Git. Sem acesso ao
código-fonte do painel, ele não consegue alterá-lo — só produzir trechos para
você colar manualmente.

---

## Três rotas possíveis

### Rota A — Exportar o painel para o GitHub ✅ recomendada

Se o painel foi construído em ferramenta de geração de app (Lovable, Bolt, v0,
Replit, Base44 e similares), quase todas oferecem **conexão/exportação direta para
o GitHub**. Feito isso:

1. O repositório é liberado para a sessão.
2. O Claude Code passa a ler e alterar o painel de verdade.
3. Cada alteração vira commit revisável, com histórico e possibilidade de desfazer.
4. A publicação continua funcionando como já funciona hoje.

**Ganho:** ciclo completo de melhoria, sem copiar e colar.

### Rota B — Reconstruir o painel dentro deste repositório

Criar o painel como aplicação neste repositório, alimentado pelo próprio caderno
de estudos em Markdown, e publicar (GitHub Pages ou similar).

**Ganho:** caderno e painel no mesmo lugar — o que você estuda alimenta o que o
painel mostra, sem duplicação.
**Custo:** refazer o que já existe.

### Rota C — Manter como está, com apoio manual

O Claude Code gera o código ou o conteúdo e você cola na plataforma.

**Ganho:** nenhum esforço de migração.
**Custo:** lento, sem histórico, sem revisão de diff, erro humano na cópia.

---

## Próximo passo

Identificar **em que ferramenta o painel foi construído**. Essa única informação
define se a Rota A está disponível — e ela é, de longe, a de melhor retorno.
