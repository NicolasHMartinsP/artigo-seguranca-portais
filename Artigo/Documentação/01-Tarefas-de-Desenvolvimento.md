---
tags: [tcc, seguranca, tarefas]
---

# Tarefas de Desenvolvimento

Checklist técnico organizado pelo cronograma de 6 meses (ver [[00-Indice]]). Cada mês fecha com a redação do capítulo correspondente — não deixe a escrita acumular para o final. Para o que escrever, ver [[02-Guia-de-Documentacao]]; para estudar as ferramentas, ver [[03-Guia-de-Tecnologias]].

> Ritmo sugerido por mês, trabalhando sozinho: ~2 semanas de desenvolvimento, ~1 semana de testes/ajustes, ~1 semana de escrita e revisão. Ajuste conforme sua disponibilidade real — o importante é não deixar a escrita para o fim do mês seguinte.

## Mês 1 — Metodologia + Capítulo 1 (Fundamentação Teórica) #metodologia #cap1

### Desenvolvimento
- [ ] Definir e documentar o isolamento do ambiente (containers/VMs locais, sem internet pública)
- [ ] Alinhar com o orientador antes de expor qualquer serviço fora da rede local
- [ ] Definir os critérios de comparação que serão usados no Capítulo 6 (decidir agora evita retrabalho)
- [ ] Definir plano de anonimização de dados capturados (ex.: IPs no honeypot)
- [ ] Levantar trabalhos relacionados (TCCs, artigos, DVWA, WebGoat)
- [ ] Ler o OWASP Top 10:2025 completo, com atenção às 3 categorias do projeto
- [ ] Abrir um "diário de decisões" (nota separada) para registrar toda escolha técnica com o motivo, desde já

### Escrita
- [ ] Rascunho da Introdução (motivação anonimizada, problema, objetivos — revisado no Mês 6)
- [ ] Metodologia e Considerações Éticas completa
- [ ] Capítulo 1 completo (fundamentação teórica + trabalhos relacionados)

---

## Mês 2 — Capítulo 2 (Desenvolvimento do Ambiente) #cap2

### Servidor e Frontend
- [ ] Servidor HTTP em C++: parsing de requisição e roteamento básico
- [ ] Servidor HTTP em C++: gerenciamento de cookies
- [ ] Servidor HTTP em C++: gerenciamento de sessão
- [ ] Frontend: tela de login
- [ ] Frontend: dashboard do aluno
- [ ] Frontend: painel do professor

### Banco de Dados — Opção B (menor privilégio na camada de aplicação)
- [ ] Criar schema SQLite: tabelas `usuarios`, `alunos`, `notas`, `sessions`
- [ ] Mapear quais funções da aplicação só leem dados (ex.: login, listar notas) e quais escrevem (ex.: lançar nota, criar usuário)
- [ ] Abrir uma conexão dedicada de leitura com a flag `SQLITE_OPEN_READONLY`
- [ ] Abrir uma conexão separada de escrita, usada só pelas funções que gravam dados
- [ ] Nomear as conexões de forma explícita no código (ex.: `db_read`, `db_write`) para deixar o menor privilégio visível na leitura do código
- [ ] Testar e confirmar que uma escrita tentada pela conexão de leitura falha — essa é a evidência para o artigo

### Infraestrutura
- [ ] Configurar reverse proxy (nginx ou Caddy) com terminação TLS na frente do servidor C++
- [ ] Gerar certificado local (mkcert) ou usar Let's Encrypt
- [ ] Containerizar com Docker (`docker-compose`: proxy + servidor + banco)
- [ ] Produzir diagrama de arquitetura (proxy → servidor C++ → SQLite)

### Escrita
- [ ] Capítulo 2 completo, incluindo justificativa de cada decisão técnica (proxy para TLS, Opção B para o banco)

---

## Mês 3 — Capítulo 3 (Vulnerabilidades Implementadas) #cap3

### 3.1 XSS
- [ ] Implementar campo sem output encoding (ex.: comentário/nome exibido cru)
- [ ] Criar payload de prova de conceito e registrar o resultado (prints, logs)

### 3.2 Broken Access Control
- [ ] Criar endpoint de alteração de nota que só checa sessão autenticada, sem checar papel/role
- [ ] Testar exploração: aluno autenticado chamando o endpoint de professor
- [ ] Registrar o passo a passo da exploração

### 3.3 SQL Injection
- [ ] Implementar versão vulnerável com concatenação de string
- [ ] Testar bypass de login (`' OR '1'='1`)
- [ ] Testar alteração maliciosa de nota via `UPDATE`
- [ ] Registrar o passo a passo da exploração

### Escrita
- [ ] Capítulo 3 completo — 3.1 XSS, 3.2 BAC, 3.3 Injection, com o impacto de cada uma conectado ao caso motivador da Introdução

---

## Mês 4 — Capítulo 4 (Mitigações) #cap4

### Desenvolvimento
- [ ] XSS: output encoding no ponto de renderização
- [ ] XSS: cabeçalho Content-Security-Policy
- [ ] XSS: sanitização de input como camada adicional
- [ ] BAC: checagem de papel/role no servidor em toda rota sensível
- [ ] BAC: implementar RBAC simples (aluno / professor / admin)
- [ ] Injection: reescrever 100% das queries com `sqlite3_prepare_v2` / `bind` / `step`
- [ ] Injection: validação de tipo/formato de entrada
- [ ] Injection: revisar a separação de conexões (Opção B, feita no Mês 2) e documentar como parte da mitigação — não é preciso reimplementar, só amarrar ao texto
- [ ] Reexecutar os ataques do Capítulo 3 contra a versão corrigida e confirmar que não funcionam mais

### Escrita
- [ ] Capítulo 4 completo, com comparação de código antes/depois para cada vulnerabilidade
- [ ] Nota teórica curta sobre ORM (conceito geral — não implementado no projeto)

---

## Mês 5 — Capítulo 5 (Defesa Ativa) #cap5

### Desenvolvimento
- [ ] Rota honeypot (ex.: `/admin/backup`) nunca usada legitimamente
- [ ] Honeytoken: registro isca no banco (ex.: aluno "fantasma" com nota implausível)
- [ ] Logging estruturado (JSON) de todas as requisições
- [ ] Alerta automático ao acessar o honeytoken
- [ ] Rate limiting nas rotas de login
- [ ] Decidir e documentar: honeypot fica só na rede local, ou será exposto de forma controlada (ex.: exercício fechado com colegas)? Se exposto, revisar o plano de anonimização de IPs
- [ ] (Opcional) Fail2ban para bloqueio automático

### Escrita
- [ ] Capítulo 5 completo, com estatísticas de tentativas capturadas (se houver) já anonimizadas

---

## Mês 6 — Capítulo 6 (Avaliação) + Fechamento #cap6

### Desenvolvimento
- [ ] Rodar OWASP ZAP contra a versão vulnerável
- [ ] Rodar OWASP ZAP contra a versão corrigida
- [ ] Coletar métricas: nº de vulnerabilidades, tempo até exploração, nº de tentativas
- [ ] Consolidar dados do honeypot (volume e tipo de tentativas)
- [ ] Montar tabela comparativa antes/depois
- [ ] Gerar gráficos (Python/matplotlib), se possível

### Escrita e Fechamento
- [ ] Capítulo 6 completo, com discussão logo após a tabela comparativa
- [ ] Revisar e fechar a Introdução com base nos resultados reais
- [ ] Redigir Considerações Finais
- [ ] Compilar lista de referências completa
- [ ] Revisão geral do texto (ver checklist em [[02-Guia-de-Documentacao]])
- [ ] Conferir se as 8 mudanças listadas em [[00-Indice]] estão de fato refletidas no texto final
