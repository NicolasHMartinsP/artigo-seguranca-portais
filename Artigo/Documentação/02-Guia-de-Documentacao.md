---
tags: [tcc, seguranca, documentacao]
---

# Guia de Documentação

Como escrever cada parte do artigo. Para as tarefas técnicas que alimentam este guia, ver [[01-Tarefas-de-Desenvolvimento]]. Para o cronograma completo, ver [[00-Indice]].

## Como é o seu primeiro artigo científico

- Não escreva na ordem de leitura. Escreva a Metodologia e os capítulos técnicos primeiro — você sabe exatamente o que fez; deixe a Introdução para o fim, quando já souber o que o artigo realmente entrega.
- Mantenha um "diário de decisões" desde o Mês 1 — toda escolha técnica (por que C++, por que proxy reverso, por que Opção B no banco) anotada na hora, com o motivo. No Mês 6 você vai precisar dessas justificativas e não vai lembrar os detalhes sozinho, sem ter registrado.
- A primeira versão de um parágrafo não precisa ser boa, precisa existir. Revisão de texto é sempre a última etapa de cada mês, nunca a primeira.
- Peça para o orientador ler capítulo por capítulo, não só o artigo inteiro no fim — erros estruturais pegos cedo custam bem menos para corrigir do que no Mês 6. Trabalhando sozinho, o orientador é sua principal fonte de segunda opinião — use as orientações com frequência.

## Como documentar — diretrizes gerais

- Linguagem técnica e impessoal (evite "eu fiz"; prefira "foi implementado" ou "implementou-se").
- Cada capítulo deve responder três perguntas: o que foi feito, por que, e o que isso significa.
- Nunca cole um trecho de código sem explicar o que ele faz e por que é relevante ali.
- Sempre que existir versão vulnerável e versão corrigida, compare lado a lado — essa comparação é o centro do artigo.
- Cite fontes na norma exigida pelo curso (ABNT é o padrão mais comum no Brasil) — confirme com o orientador.
- Legendas de figuras e tabelas conforme a norma (ex.: ABNT usa "Figura 1 –" acima da imagem, "Tabela 1 –" acima da tabela, fonte abaixo).
- Nomeie vulnerabilidades sempre pela categoria oficial do OWASP Top 10:2025, além do nome comum (ex.: "SQL Injection, enquadrada na categoria A05 — Injection — do OWASP Top 10:2025").

---

## Introdução do Artigo (fechada no Mês 6)

### O que documentar
- Contextualização: importância da segurança em sistemas acadêmicos
- Motivação: caso anonimizado como gancho — sem nome de instituição, sem detalhes reprodutíveis
- Problema de pesquisa, objetivo geral, objetivos específicos
- Justificativa: por que esse estudo importa
- Estrutura do artigo (roteiro dos capítulos)

### O que analisar
- O problema de pesquisa está delimitado o bastante pra ser respondido em um artigo?
- Os objetivos específicos são verificáveis — dá pra apontar, no fim, exatamente onde cada um foi cumprido?
- O caso motivador está anonimizado a ponto de não permitir identificar a instituição real?

> 🔄 Mudança em relação à proposta original: a Introdução não existia como seção formal no esboço inicial. Rascunhem no Mês 1, fechem de verdade só no Mês 6.

---

## Metodologia e Considerações Éticas (Mês 1)

### O que documentar
- Declaração explícita de que nenhum sistema de produção real foi testado
- Enquadramento legal (Lei nº 12.737/2012 — invasão de dispositivo informático) e por que o estudo permanece dentro da lei
- Tratamento de dados sob a LGPD, caso o honeypot seja exposto publicamente
- Critérios de comparação que serão usados no Capítulo 6
- Descrição do ambiente de testes (isolado, local, sem dados reais de terceiros)

### O que analisar
- O escopo ético cobre todos os riscos reais do projeto, inclusive exposição eventual do honeypot?
- Os critérios de comparação definidos aqui são os mesmos usados de fato no Capítulo 6?

> 🔄 Mudança: capítulo novo, não existia no esboço original. Te protege juridicamente e dá credibilidade acadêmica ao trabalho.

---

## Capítulo 1 — Fundamentação Teórica (Mês 1)

### O que documentar
- OWASP Top 10:2025 — categorias e principais mudanças em relação à versão de 2021, com foco nas três exploradas no projeto
- Fundamentos de segurança em aplicações web
- Sistemas acadêmicos: características e superfície de ataque típica
- HTTP: requisição/resposta, métodos, códigos de status
- Cookies: atributos de segurança (HttpOnly, Secure, SameSite)
- Sessões: criação, tempo de vida, invalidação
- Trabalhos relacionados (TCCs, artigos, projetos de referência como DVWA e WebGoat)

### O que analisar
/- Cada decisão arquitetural está justificada tecnicamente (não apenas "porque foi mais simples")?
- O diagrama e a descrição são suficientes para alguém de fora reproduzir o ambiente?

> 🔄 Mudanças: (1) TLS passou a ser feito por reverse proxy — nginx/Caddy —, não implementado do zero em C++; evita um projeto à parte dentro do projeto. (2) SQLite não tem privilégios nativos como MySQL/PostgreSQL; decidiu-se pela **Opção B** — implementar o menor privilégio na camada de aplicação, com conexões de leitura e escrita separadas, em vez de trocar de banco. Isso vira um achado técnico do artigo: mostra que dá pra aplicar o princípio mesmo sem suporte nativo do BD.

---

## Capítulo 3 — Vulnerabilidades Implementadas (Mês 3)

### 3.1 Cross-Site Scripting (XSS)
**Documentar:** tipo de XSS (armazenado ou refletido); fluxo de exploração passo a passo; impacto (roubo de cookie de sessão, defacement).

### 3.2 Broken Access Control
**Documentar:** diferença entre "esconder botão na interface" e "checar permissão no servidor"; fluxo de exploração — aluno autenticado chamando diretamente o endpoint de professor.

### 3.3 Injection (SQL Injection)
**Documentar:** exemplos de query antes e depois, lado a lado; explicação técnica de `sqlite3_prepare_v2` / `sqlite3_bind_text` / `sqlite3_step`.

### O que analisar (capítulo todo)
- O impacto de cada vulnerabilidade está conectado ao caso motivador da introdução (nota alterada)?
- Os prints/logs de exploração são suficientes como evidência, sem expor dados sensíveis desnecessários?

> 🔄 Mudança: no esboço original, "alteração de notas" aparecia só como impacto do BAC, mas o exemplo de `UPDATE` malicioso é, na verdade, Injection. Agora os dois ficam separados: BAC é falha de **autorização** (endpoint sem checar papel), Injection é falha de **sanitização** (entrada maliciosa). Mesmo resultado final, mecanismos diferentes — deixem isso explícito, é um dos pontos mais interessantes do trabalho.

---

## Capítulo 4 — Mitigações (Mês 4)

### O que documentar
- XSS: output encoding, cabeçalho CSP, sanitização — com comparação de código antes/depois
- BAC: modelo RBAC adotado e onde exatamente a checagem acontece no fluxo da requisição
- Injection: prepared statements com exemplos de código; estratégia de menor privilégio (Opção B, já implementada no Capítulo 2) revisitada aqui como parte da defesa contra Injection

### O que analisar
- Cada mitigação foi de fato testada contra o ataque correspondente do Capítulo 3, ou ficou só na teoria?
- As limitações de cada mitigação foram discutidas?

> 🔄 Mudança: o item "ORM (se comentar)" saiu da lista de desenvolvimento. Como o projeto é C++ puro com `sqlite3_prepare_v2`, não há ORM real implementado — vira nota teórica curta ("em stacks com ORM, a parametrização normalmente vem embutida"), não uma tarefa.

---

## Capítulo 5 — Defesa Ativa (Mês 5)

### O que documentar
- Arquitetura da defesa ativa (diagrama)
- Resultados capturados, se houver exposição controlada (tipos de payload mais frequentes, volume de tentativas)
- Estatísticas de acesso ao honeytoken

### O que analisar
- Os dados capturados foram anonimizados corretamente antes de entrar no texto?
- O honeypot ajuda a detectar algo que as mitigações do Capítulo 4 não cobrem sozinhas?

> 🔄 Mudança: cuidado ético explícito adicionado. Se o honeypot for exposto além da rede local: nunca usar dados reais, anonimizar qualquer IP capturado (LGPD). Alternativa mais segura: exercício fechado com colegas/orientador em vez de exposição pública.

---

## Capítulo 6 — Avaliação (Mês 6)

### O que documentar
- Tabela comparativa antes/depois (vulnerabilidade × explorável/corrigida)
- Parágrafo de discussão logo após a tabela
- Gráficos, se possível (ex.: tempo até exploração)

### O que analisar
- A comparação antes/depois foi feita em condições justas (mesmo ambiente, mesmas ferramentas, mesmo número de tentativas)?
- Os números têm significância mínima, ou é preciso deixar claro que é um estudo de caso único (limitação, não defeito)?

> 🔄 Mudança: a tabela sozinha ficava rasa. Adicionou-se uma camada quantitativa por trás dela — tempo até exploração, nº de tentativas, resultado de scan automatizado (OWASP ZAP) antes/depois, dados agregados do honeypot.

---

## Considerações Finais (Mês 6)

### O que documentar
- Retomada dos objetivos: o que foi respondido
- Síntese dos resultados do Capítulo 6
- Contribuições do trabalho
- Limitações do estudo
- Sugestões de trabalhos futuros

### O que analisar
- As conclusões decorrem diretamente dos dados apresentados no Capítulo 6, ou soam genéricas demais?
- As limitações listadas são honestas — todo projeto acadêmico tem, esconder isso pesa mais na banca do que assumir?

---

## Checklist de revisão final

- [ ] Todas as citações têm referência completa na lista final
- [ ] Nenhum trecho identifica a instituição real do caso motivador
- [ ] Todas as tabelas e figuras têm legenda e são citadas no texto antes de aparecerem
- [ ] Termos técnicos (XSS, BAC, Injection, RBAC etc.) são definidos na primeira aparição
- [ ] Introdução e Considerações Finais "conversam" entre si
- [ ] As 8 mudanças listadas em [[00-Indice]] estão de fato refletidas no texto final
