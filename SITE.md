# 🌐 Painel MPE — arquitetura, riscos e decisão de migração

## 1. Identificação

- **URL:** https://painel.rodriguesmodafeminina.com/painel-mpe/meu-dia
- **Hospedagem:** Hostinger (confirmado por DNS — CNAME `connect.hostinger.com`)
- **Construído em:** Hostinger Horizons (construtor com IA)
- **Finalidade:** painel pessoal de estudos, acesso restrito por login

---

## 2. Stack real (diagnóstico do próprio Horizons)

| Camada | Tecnologia |
|---|---|
| Front-end | React 18.3.1 (JavaScript/JSX) |
| Build | Vite 7.3.6 · `npm run build` → `dist/apps/web` |
| Estilos | Tailwind CSS |
| Componentes | Radix UI · lucide-react · framer-motion |
| Formulários | react-hook-form + zod |
| Datas / gráficos | date-fns · Recharts |
| **Backend** | **PocketBase** (gerenciado pela plataforma) |

### Estrutura

```
apps/
├── web/src/
│   ├── App.jsx
│   └── pages/painel-mpe/
│       ├── pages/          ← PlanejamentoIntegradoPage.jsx, MeuDiaPage.jsx
│       ├── componentes/
│       └── layout e estilos
└── pocketbase/
    ├── pb_migrations/
    └── hooks/
```

### Rotas

Mais de 30 rotas internas. As relevantes por ora:

- `/painel-mpe/planejamento-integrado` → `PlanejamentoIntegradoPage.jsx`
- `/painel-mpe/meu-dia` → `MeuDiaPage.jsx`
- `/` → login; autenticado, redireciona para `/painel-mpe/meu-dia`

Outras áreas: centro-de-comando, agenda-semanal, ciclo-de-estudos, estudo-ativo,
biblioteca-juridica, mentora-mpe, desempenho, simulados, discursiva, prova-oral,
relatorios, roi, configuracoes.

### Dados e autenticação

- Tudo no **PocketBase**: usuários, agenda, planos e blocos, questões, discursivas,
  interações de IA, documentos.
- `localStorage` apenas para o estado do estudo em andamento (`mpe_em_andamento`).
- Login por coleção `users` do PocketBase, sessão persistente, rotas protegidas,
  acesso restrito a perfil administrativo.

> Observação: o diagnóstico veio com ruído de tradução automática — "recálculos"
> é Recharts, "data-fns" é date-fns, "CEP de exportação" é o ZIP.

---

## 3. O que a exportação traz — e o que não traz

| Vai no ZIP | **Não** vai no ZIP |
|---|---|
| Código da aplicação web | Arquivos `.env` |
| Páginas, componentes, estilos, utilitários | Senhas e chaves |
| Configurações do projeto | Registros de usuários |
| Migrações do PocketBase | **Dados do PocketBase** |
| | Uploads e arquivos |
| | Banco em produção e backups |

**Consequência direta:** o código exportado sobe vazio. Todo o seu histórico de
estudo — agenda, erros, questões, discursivas — fica para trás sem um backup
separado do PocketBase.

---

## 4. Os dois bloqueios reais

### Bloqueio 1 — o PocketBase é um servidor, não um arquivo

A hospedagem compartilhada da Hostinger serve arquivos e não roda build. Ela dá
conta do front-end compilado (`dist/apps/web`), mas **não** roda o PocketBase, que
é um processo de servidor. Hospedar por conta própria exige VPS ou equivalente.

Hoje quem roda esse servidor é a plataforma. Ao exportar, essa responsabilidade
passa a ser sua.

### Bloqueio 2 — acesso ao backup dos dados

É preciso confirmar se há acesso ao painel administrativo do PocketBase para baixar
um backup completo. Sem isso, os dados ficam presos na plataforma.

---

## 5. A decisão

A exportação do Horizons é de mão única: não há sincronização e não se importa
código de volta. Portanto não existe cenário em que o Horizons e o Claude Code
trabalhem no mesmo projeto.

| | Ficar no Horizons | Migrar para o GitHub |
|---|---|---|
| Quem altera o código | Horizons, por prompt | Claude Code, com diff e histórico |
| Infraestrutura | Da plataforma | Sua (VPS, backups, atualizações) |
| Custo de operação | Nenhum | Real e contínuo |
| Escala do projeto | Piora conforme cresce | Melhora conforme cresce |

---

## 6. Sequência recomendada

**1. Backup do PocketBase — fazer agora, independe de qualquer decisão.**
Hoje os dados existem em um lugar só. Se a plataforma falhar, o histórico de estudo
vai junto. O backup não compromete com nada e elimina o risco maior.

**2. Registrar as variáveis de ambiente em gerenciador de senhas.**
`MPE_ADMIN_PASSWORD`, `MPE_DEMO_PASSWORD`, `PB_ENCRYPTION_KEY`, `PB_SUPERUSER_*`,
`BUILDER_MAILER_*`. Sem `PB_ENCRYPTION_KEY` o backup pode ficar inutilizável.
**Nunca commitar esses valores. `.env` sempre no `.gitignore`.**

**3. Aplicar os lotes de layout no Horizons.**
Não dependem da migração e o painel fica melhor de imediato.

**4. Migrar quando houver tempo dedicado.**
Exportar, subir ao GitHub, montar o VPS, apontar o domínio. Não é tarefa de
intervalo entre um estudo e outro.

---

## 7. Publicação depois da migração

- Front-end: `npm run build` → `dist/apps/web`. A Hostinger **não roda build**;
  o build sai do GitHub Actions ou vai versionado.
- PocketBase: processo de servidor, exige VPS.
- Deploy via GitHub: hPanel → Avançado → Git (hospedagem) ou GitHub Actions (VPS).

---

## Fontes

- [Hostinger — Deploy de repositório Git](https://www.hostinger.com/support/1583302-how-to-deploy-a-git-repository-in-hostinger/)
- [Hostinger Docs — Git](https://docs.hostinger.com/websites/git)
- [Hostinger — Deploy em VPS com GitHub Actions](https://www.hostinger.com/support/deploy-to-hostinger-vps-using-github-actions/)
- [Hostinger — Como exportar o código do Horizons](https://www.hostinger.com/support/10771345-hostinger-horizons-how-to-export-code/)
