---
description: Comentar uma questão de prova e registrar o erro no caderno de erros
argument-hint: [cole o enunciado e a alternativa que você marcou]
---

Questão: $ARGUMENTS

Faça o comentário no formato de professor corrigindo prova:

1. **Resposta correta** e por quê — fundamento legal e, se houver, jurisprudencial.
2. **Por que cada alternativa errada está errada** — uma linha por alternativa.
   É aqui que está o aprendizado real; não pule.
3. **Qual foi a armadilha** — o que a banca tentou explorar.
4. **Tópico-raiz** — qual assunto do edital precisa ser revisado para não errar de novo.

Se o usuário indicou ter errado a questão:
- Registre uma entrada em `14-questoes-recursos/03-resumo-erros/` com o tópico-raiz,
  o erro cometido e a regra correta em uma frase.
- Se já existir entrada para o mesmo tópico-raiz, **incremente o contador de recorrência**
  em vez de criar arquivo novo — erro que se repete é prioridade de revisão.

Não invente número de súmula ou de tema de repercussão geral. Na dúvida, `⚠️ CONFERIR`.
