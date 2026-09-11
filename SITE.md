# 🌐 Painel MPE — situação, plataforma e plano de migração

## 1. Identificação da plataforma ✅

- **URL:** https://painel.rodriguesmodafeminina.com/painel-mpe/meu-dia
- **Hospedagem: Hostinger** — confirmado por DNS:
  - `www.rodriguesmodafeminina.com` → CNAME → `connect.hostinger.com` → `connect.hstgr.net`
  - `painel.rodriguesmodafeminina.com` → `195.35.60.235`, `191.101.104.25`
    (mesmos blocos `195.35.60.0/24` e `191.101.104.0/24` do domínio principal)

Ou seja: **não** é Lovable, Bolt, v0 ou Replit — essas ferramentas servem a partir
da infraestrutura própria delas. Está tudo na Hostinger.

> Não foi possível inspecionar o HTML da página: o domínio é bloqueado pela política
> de rede desta sessão (403 no proxy de egresso). A identificação acima veio de DNS.

### Falta confirmar: como o painel foi construído

| Hipótese | Como confirmar no hPanel | Como extrair o código |
|---|---|---|
| **Hostinger Horizons** (construtor com IA) | O projeto aparece em *Horizons / AI Builder* | Exportar código → ZIP (React + Vite) |
| **Upload manual** (arquivos estáticos/PHP) | Só aparece em *Hospedagem* | Gerenciador de Arquivos → compactar `public_html` → baixar |

A rota `/painel-mpe/meu-dia` sugere uma SPA com roteamento — compatível com as duas.

---

## 2. Limitações conhecidas do Hostinger Horizons

Se o painel foi feito no Horizons, é preciso saber **antes** de exportar:

- A exportação é **via de mão única**: o código editado **não volta** para o Horizons.
  Depois da migração, o desenvolvimento continua no GitHub + Claude Code — o modo
  "pedir por prompt dentro do Horizons" deixa de valer para esse projeto.
- O Horizons **não** importa código do GitHub e **não** tem sincronização bidirecional.
- A exportação exige plano Hobbyist ou superior.

Para o objetivo declarado (usar o Claude Code para melhorar o painel), essa troca
é vantajosa: perde-se o prompt dentro do Horizons e ganha-se histórico, revisão de
diff, possibilidade de desfazer e um agente que enxerga o projeto inteiro.

---

## 3. Publicação depois da migração

A Hostinger tem deploy a partir do GitHub: **hPanel → Avançado → Git →
"Connect with GitHub"** (OAuth, instala o GitHub App), escolhe repositório e branch.
Cada `push` no branch conectado dispara deploy automático via webhook.

### ⚠️ Armadilha técnica que precisa ser resolvida

**O deploy da Hostinger não roda build.** Ele serve exatamente o que está no
repositório. Um projeto React/Vite exportado do Horizons **não funciona** se for
publicado como está — ele precisa de `npm run build`, que gera a pasta `dist/`.

Duas saídas:

- **Simples:** versionar a pasta `dist/` já compilada e apontar o deploy para ela.
- **Correta:** GitHub Actions roda o build a cada push e publica o resultado em um
  branch dedicado (ex.: `deploy`); a Hostinger observa esse branch.

Recomendação: começar pela simples para fechar o ciclo rápido e migrar para a
correta depois que o fluxo estiver funcionando.

---

## 4. Plano de migração

| # | Passo | Quem faz |
|---|---|---|
| 1 | Confirmar no hPanel se o painel é Horizons ou upload manual | **Você** |
| 2 | Exportar/baixar o código em ZIP | **Você** |
| 3 | Enviar o ZIP para o Google Drive (já conectado a esta sessão) | **Você** |
| 4 | Baixar o ZIP, criar o repositório `painel-mpe`, commitar o código | Claude Code |
| 5 | Analisar a stack e documentar em `CLAUDE.md` do novo repositório | Claude Code |
| 6 | Configurar o build e o deploy automático | Claude Code + você no hPanel |
| 7 | Ciclo aberto: você pede → eu altero → você revisa o diff → publica | Ambos |

---

## Fontes

- [Hostinger — Deploy de repositório Git](https://www.hostinger.com/support/1583302-how-to-deploy-a-git-repository-in-hostinger/)
- [Hostinger Docs — Git](https://docs.hostinger.com/websites/git)
- [Hostinger — Como exportar o código do Horizons](https://www.hostinger.com/support/10771345-hostinger-horizons-how-to-export-code/)
- [Hostinger Horizons — Perguntas frequentes](https://www.hostinger.com/support/10673155-hostinger-horizons-frequently-asked-questions/)
