Oque é CORS ? 
Cors é um recurso de proteção que controla quais sites podem fazer requisições para outros sites
ex: https://www.uem.br/ possui a API : api.uem.br e existe o site virusite.com sem o cors o navegador iria impedir o JS do virusite acessar a resposta da API da uem, o navegador está protegendo o site

Oque é uma Origin ?
Estrutura: Protocolo + Dominio + Porta
https://www.uem.br/ é diferente de http://www.uem.br/ que é diferente de https://api.uem.br/ e tambem https://www.uem.br:25519
qualquer diferença na URL cria uma origin diferente
o CORS acontece entre o navegador e o servidor da aplicação
então o fluxo dele fica assim :
JS manda fetch para o navegador que envia para o servidor o pedido que é transformado em header http e volta para o navegador decidir se ele bloqueia o acesso ou permite, quem permite o CORS é o navegador, não o servidor, então na pratica:
Navegador faz uma requisição:
GET /api/notas
servidor recebe a requisição e monta :
Access-Control-Allow-Origin: https://www.uem.br/
se o JS bater com www.uem.br ele libera a requisição, se não, o CORS bloqueia
agora oque é o CORS Misconfiguration ?
é quando o CORS é mal configurado, permitindo vários terceiros acessarem, um exemplo é :
Access-Control-Allow-Origin:
https://qualquer-site.com ou apenas *
a universidade possui uma API: GET /api/notas
o usuario faz login normalmente e recebi um cookie JWT ai ele acessa um site com um fetch para pegar os dados do site como:

fetch("https://portal.universidade.br/api/notas",{
    credentials:"include"
})
isso puxaria tudo que está como credencial no URL, como Nome, IP, Email, CPF e por ai vai