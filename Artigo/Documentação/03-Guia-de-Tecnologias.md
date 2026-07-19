---
tags: [tcc, seguranca, tecnologias]
---

# Guia de Tecnologias

O que estudar de cada tecnologia, onde aprender e em que mês/capítulo ela entra. Cronograma completo em [[00-Indice]].

## C++ — Servidor HTTP (Mês 2 · Capítulo 2) #cap2

- **O que aprender:** sockets (POSIX ou Boost.Asio), parsing manual de requisição HTTP, roteamento básico, gerenciamento de memória
- **Onde estudar:** cppreference.com · documentação do Boost.Asio · RFC 9110 (para entender o protocolo que está sendo implementado)
- **Por que importa:** é a base de todo o ambiente — bugs aqui se propagam para todos os capítulos seguintes
- **Trabalhando sozinho:** se o cronograma apertar, considere usar uma biblioteca HTTP mínima (ex.: [cpp-httplib](https://github.com/yhirose/cpp-httplib), header-only) para o parsing e roteamento básico, e concentre seu tempo próprio em cookies, sessão e nas rotas que efetivamente entram nos Capítulos 3-6. Documente essa escolha no Capítulo 2 como decisão de escopo — reimplementar parsing HTTP do zero não agrega nada ao argumento de segurança do artigo.

## SQLite — Banco de Dados e Menor Privilégio (Opção B) (Mês 2 · Capítulos 2 e 4) #cap2 #cap4

Esta é a peça técnica mais nova do projeto — vale um estudo mais cuidadoso do que as outras.

- **O problema:** diferente de MySQL/PostgreSQL, o SQLite não tem `GRANT`/`REVOKE` nem usuários — é um arquivo único. O princípio do menor privilégio não pode ser aplicado dentro do banco em si.
- **A solução adotada (Opção B):** aplicar o menor privilégio na camada da aplicação, abrindo conexões separadas conforme a necessidade de cada função:
  - Conexão de leitura: `sqlite3_open_v2(caminho, &db, SQLITE_OPEN_READONLY, NULL)` — usada por funções como login e listagem de notas
  - Conexão de escrita: aberta normalmente (`SQLITE_OPEN_READWRITE`), usada só pelas funções que realmente gravam dados (ex.: lançar nota, criar usuário)
- **O que aprender:** SQLite C API (`sqlite3_open_v2`, `sqlite3_prepare_v2`, `sqlite3_bind_*`, `sqlite3_step`), as flags de abertura de conexão, e por que separar conexões por responsabilidade é boa prática mesmo fora de bancos com privilégios nativos
- **Onde estudar:** sqlite.org/cintro.html (documentação oficial da C API) · sqlite.org/c3ref/open.html (flags de abertura de conexão)
- **Entregável para o artigo:** um teste demonstrando que uma tentativa de escrita pela conexão de leitura falha — essa é a evidência de que o menor privilégio está de fato em vigor, não só declarado no texto

## JavaScript — Frontend (Mês 2 · Capítulo 2; explorado no Mês 3 · Capítulo 3) #cap2 #cap3

- **O que aprender:** manipulação de DOM, `fetch` para chamar a API do servidor, por que inserir input não confiável no DOM sem tratamento vira XSS
- **Onde estudar:** MDN Web Docs (developer.mozilla.org)
- **Por que importa:** o mesmo código do frontend será, no Mês 3, o ponto de entrada do ataque XSS

## HTTP, Cookies e Sessões (Mês 1 · Capítulo 1) #cap1

- **O que aprender:** métodos e códigos de status HTTP; atributos de cookie (HttpOnly, Secure, SameSite); ciclo de vida de uma sessão (criação, expiração, invalidação)
- **Onde estudar:** RFC 9110 (HTTP Semantics) · RFC 6265 (Cookies) · OWASP Session Management Cheat Sheet
- **Por que importa:** pré-requisito para entender tanto o Capítulo 2 (implementação) quanto o Capítulo 3 (exploração de sessão via XSS)

## nginx / Caddy — Reverse Proxy e TLS (Mês 2 · Capítulo 2) #cap2

- **O que aprender:** configuração básica de proxy reverso, terminação TLS, por que separar TLS do servidor de aplicação é prática comum em produção
- **Onde estudar:** nginx.org/docs · caddyserver.com/docs · mkcert (github.com/FiloSottile/mkcert) para gerar certificado local
- **Por que importa:** entrega HTTPS de ponta a ponta sem o custo de implementar TLS na mão em C++

## Docker (Mês 1–2 · Metodologia e Capítulo 2) #cap2 #metodologia

- **O que aprender:** Dockerfile básico, `docker-compose` para orquestrar proxy + servidor + banco, isolamento de rede entre containers
- **Onde estudar:** docs.docker.com/get-started
- **Por que importa:** garante, na prática, o isolamento declarado na Metodologia

## OWASP ZAP (Mês 6 · Capítulo 6) #cap6

- **O que aprender:** scan passivo x scan ativo, como interpretar o relatório de vulnerabilidades gerado
- **Onde estudar:** zaproxy.org/docs
- **Por que importa:** gera as métricas comparativas antes/depois que sustentam o Capítulo 6

## OWASP Top 10:2025 — Referência Central (Mês 1; citado nos Capítulos 3 e 4) #cap1 #cap3 #cap4

- **O que aprender:** as 10 categorias da versão 2025, com foco em Broken Access Control, Injection e a categoria que cobre XSS
- **Onde estudar:** owasp.org/Top10/2025 · OWASP Cheat Sheet Series
- **Por que importa:** é o referencial teórico que dá peso acadêmico à escolha das três vulnerabilidades do projeto

## Logging, Rate Limiting e Honeypots (Mês 5 · Capítulo 5) #cap5

- **O que aprender:** formato de log estruturado (JSON); estratégias simples de rate limiting (contador por IP/janela de tempo); conceito de honeypot e honeytoken
- **Onde estudar:** OWASP Logging Cheat Sheet · artigos introdutórios do Honeynet Project
- **Por que importa:** é a parte mais autoral do projeto — poucos TCCs de segurança chegam a implementar defesa ativa, não só passiva

## Legislação Aplicável (Mês 1 · Metodologia) #metodologia

Não é tecnologia, mas é pré-requisito para a Metodologia.

- **O que aprender:** noções gerais da Lei nº 12.737/2012 (invasão de dispositivo informático) e da LGPD (Lei nº 13.709/2018) relevantes ao projeto
- **Onde estudar:** planalto.gov.br (texto oficial das leis)
- **Por que importa:** sustenta o enquadramento legal do estudo — ver Metodologia em [[02-Guia-de-Documentacao]]
