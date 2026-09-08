# 🚀 Como Estruturar seu Repositório Git - Guia Prático

---

## 1️⃣ PRIMEIROS PASSOS (Na sua máquina)

### Instalar Git
```bash
# Windows: baixe em https://git-scm.com/download/win
# Mac: brew install git
# Linux: sudo apt-get install git
```

### Configurar Git (primeira vez)
```bash
git config --global user.name "Seu Nome"
git config --global user.email "seu.email@gmail.com"
```

---

## 2️⃣ CLONAR O REPOSITÓRIO

Após criar no GitHub, faça:

```bash
# Clone o repositório
git clone https://github.com/seu-usuario/estude-mpe-disco.git

# Entre na pasta
cd estude-mpe-disco

# Veja a estrutura
ls -la
```

---

## 3️⃣ ESTRUTURA BÁSICA (Copie e cole)

```bash
# Copie os 3 arquivos que criei para sua pasta
# README.md
# CRONOGRAMA.md
# TEMPLATE-ANOTACOES.md

# Crie as pastas principais (na ordem do README)
mkdir -p 01-direito-penal/{01-parte-geral,02-parte-especial,03-jurisprudencia,04-questoes-comentadas}
mkdir -p 02-direito-processual-penal/{01-principios,02-procedimentos,03-recursos,04-questoes-comentadas}
mkdir -p 03-direito-constitucional/{01-principios-fundamentais,02-direitos-fundamentais,03-constituicao-economica,04-controle-constitucionalidade,05-questoes-comentadas}
mkdir -p 04-direito-administrativo/{01-principios,02-atos-administrativos,03-procedimento-administrativo,04-funcao-publica,05-questoes-comentadas}
mkdir -p 05-direito-civil/{01-pessoas-familia,02-obrigacoes-contratos,03-direitos-reais,04-responsabilidade-civil,05-questoes-comentadas}
mkdir -p 06-direito-processual-civil/{01-principios-procedimento,02-partes-representacao,03-recursos,04-questoes-comentadas}
mkdir -p 07-direito-do-consumidor/{01-principios-direitos,02-responsabilidade,03-questoes-comentadas}
mkdir -p 08-direito-ambiental/{01-principios-protecao,02-bens-protegidos,03-questoes-comentadas}
mkdir -p 09-direito-coletivo/{01-acoes-coletivas,02-legitimidade-ativa,03-questoes-comentadas}
mkdir -p 10-legislacao-especial/{01-lei-drogas,02-lei-racismo,03-lei-tortura,04-lei-crimes-hediondos,05-outras-leis}
mkdir -p 11-portugues-redacao/{01-gramatica,02-interpretar-texto,03-redacao-tecnica,04-modelos-pareceres}
mkdir -p 12-raciocinio-logico/{01-logica-basica,02-questoes-comentadas}
mkdir -p 13-jurisprudencia/{01-stf-temas,02-stj-jurisprudencia,03-tribunais-estaduais}
mkdir -p 14-questoes-recursos/{01-ultimas-provas,02-concursos-realizados,03-resumo-erros}
mkdir -p 15-legislacao/{01-constituicao-federal,02-codigos,03-leis-especiais}
mkdir -p 16-planejamento-estudo
mkdir -p 17-recursos-externos
```

---

## 4️⃣ ADICIONAR ARQUIVOS E FAZER COMMIT

### Primeiro commit (estrutura)
```bash
# Adicione tudo que criou
git add .

# Faça o commit
git commit -m "docs: estrutura inicial do repositório de estudo MPE"

# Envie para GitHub
git push origin main
```

### Mensagens de commit boas
```
# ✅ BOM
git commit -m "docs: adiciona anotações sobre Princípios Penais"
git commit -m "docs: cria template de jurisprudência"
git commit -m "feat: organiza questões de Direito Processual Civil"

# ❌ RUIM
git commit -m "atualizou"
git commit -m "xyz"
git commit -m "teste"
```

---

## 5️⃣ ROTINA DE ESTUDOS COM GIT

### Antes de estudar
```bash
# Atualize o repositório (se trabalha em múltiplos computadores)
git pull origin main
```

### Após estudar
```bash
# Veja o que mudou
git status

# Adicione os arquivos
git add 01-direito-penal/01-parte-geral/principios-penais.md

# Faça o commit com mensagem descritiva
git commit -m "docs: estuda princípios penais - parte geral"

# Envie para GitHub
git push origin main
```

---

## 6️⃣ PADRÃO DE NOMES DE ARQUIVO

### Para Anotações Teóricas
```
01-direito-penal/01-parte-geral/principios-penais.md
01-direito-penal/01-parte-geral/crime-elementos.md
01-direito-penal/02-parte-especial/homicidio.md
```

### Para Questões
```
01-direito-penal/04-questoes-comentadas/q001-stf-2022.md
01-direito-penal/04-questoes-comentadas/q002-oab-2023.md
01-direito-penal/04-questoes-comentadas/q003-fgv-cespe.md
```

### Para Jurisprudência
```
13-jurisprudencia/01-stf-temas/tema-001-legalidade.md
13-jurisprudencia/02-stj-jurisprudencia/sumula-123.md
```

---

## 7️⃣ ESTRUTURA DE UM ARQUIVO MARKDOWN

```markdown
# Homicídio Simples (Art. 121, CP)

## 📌 Conceito
Definição clara...

## 🎯 Elementos
- Elemento 1
- Elemento 2

## ⚖️ Jurisprudência
- **STF**: [Decisão]
- **STJ**: [Súmula]

## 📊 Questões
[Links ou descrição]

## 💭 Observações Pessoais
[Seu resumo]
```

---

## 8️⃣ COMANDOS GIT ESSENCIAIS

### Ver histórico
```bash
# Veja os últimos commits
git log --oneline -10

# Veja o que você mudou
git diff
```

### Desfazer coisas
```bash
# Desfaça um commit (mantendo os arquivos)
git reset --soft HEAD~1

# Desfaça tudo (cuidado!)
git reset --hard HEAD~1
```

### Criar branches (opcional, mas útil)
```bash
# Crie um branch para uma disciplina
git checkout -b estudo-direito-penal

# Mude de branch
git checkout main

# Junte o branch
git merge estudo-direito-penal
```

---

## 9️⃣ BOAS PRÁTICAS

✅ **Commit pequeno e frequente**  
- Não guarde 10 arquivos para fazer commit uma vez por mês
- Faça commit a cada tópico estudado

✅ **Mensagens descritivas**
```bash
git commit -m "docs: aprende sobre crime doloso e culposo"  # ✅
git commit -m "atualizou"                                    # ❌
```

✅ **Organize com pastas**
- Siga a estrutura do README.md
- Não coloque tudo solto na raiz

✅ **Use o TEMPLATE-ANOTACOES.md**
- Mantenha consistência nos seus resumos

✅ **Revise antes de fazer push**
```bash
git diff HEAD~1  # Veja o que vai enviar
```

---

## 🔟 AUTOMAÇÃO (Avançado)

### Script de commit diário
Crie um arquivo `commit-diario.sh`:

```bash
#!/bin/bash

# Adiciona todos os arquivos
git add .

# Pega a data de hoje
DATA=$(date +"%d/%m/%Y")

# Faz commit com data
git commit -m "docs: estudo do dia $DATA"

# Envia para GitHub
git push origin main

echo "✅ Commit do dia enviado!"
```

Execute com:
```bash
chmod +x commit-diario.sh
./commit-diario.sh
```

---

## 📊 EXEMPLO DE REPOSITÓRIO APÓS 1 MÊS

```
estude-mpe-disco/
├── README.md
├── CRONOGRAMA.md
├── TEMPLATE-ANOTACOES.md
├── COMO-USAR-GIT.md
├── 01-direito-penal/
│   ├── 01-parte-geral/
│   │   ├── principios-penais.md
│   │   ├── crime-elementos.md
│   │   └── culpabilidade.md
│   └── 04-questoes-comentadas/
│       ├── q001-stf-2022.md
│       ├── q002-oab-2023.md
│       └── [+40 questões]
├── 02-direito-processual-penal/
│   ├── 01-principios/
│   │   └── principios-dpp.md
│   └── 04-questoes-comentadas/
│       └── [+35 questões]
└── [demais disciplinas...]
```

---

## 🆘 TROUBLESHOOTING

### Erro: "Permission denied"
```bash
# Gere uma chave SSH no GitHub
# Settings > Developer settings > Personal access tokens
# Use o token como senha
```

### Erro: "Could not read Username"
```bash
git config --global credential.helper store
# Próxima vez, salva sua senha
```

### Erro: Arquivo grande demais
```bash
# Git rejeita arquivos > 100MB
# Use arquivos menores ou comprima
```

---

## 🎯 PRÓXIMOS PASSOS

1. ✅ Clone o repositório
2. ✅ Crie as 17 pastas principais
3. ✅ Faça o primeiro commit
4. ✅ Comece a estudar e fazer commits diários
5. ✅ Revise o repositório a cada 2 semanas

---

**Sucesso na sua jornada! ⚖️📚🚀**
