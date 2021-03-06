---
type: wordpress
title: PM2 - Gerenciador de aplicações Node.js em ambientes de produção
summary: |
  PM2 é um gerenciador de aplicações para Node.js para servidor de produção com funcionalidades como monitoramento e criação de cluster, veja nesse tutorial como utilizar suas funcionalidades.
date: 2016-01-27 12:12:29
authors:
  - jaswdr
slug: pm2
images:
  - /images/wp-content/uploads/2016/01/logo_pm2.png
categories:
  - desenvolvimento
  - tutoriais
tags:
  - javascript
  - node-js
  - pm2
---

O Node.js, famoso interpretador javascript do lado servidor, trouxe uma nova gama de possibilidades para a linguagem javascript. Entre suas maiores vantagens posso citar o uso da mesma linguagem de programação no lado cliente e servidor e usar programação assíncrona por padrão. Contudo em algum momento da vida de seu projeto ou da sua aplicação, este deve ser publicado, e é neste ponto que o PM2 aparece.

O PM2 basicamente serve para manter e monitorar sua aplicação rodando, usar cluster para alta disponibilidade com balanceamento de carga, logs, recarregar a aplicação automaticamente quando há alterações nos arquivos ou reinicio do servidor, entre diversas outras atividades que a principio podem parecer simples, porém tenha em mente que o PM2 foi projetado para gerenciar diversas aplicações rodando de uma só vez, em um único console.

<!--more-->

Para ilustrar melhor vamos fazer um exemplo, para isso antes de mais nada devemos fazer a instalação do mesmo, através do seguinte comando:
<p style="text-align: center;"><strong>npm install -g pm2</strong></p>
Isto fará a instalação global do PM2, vou pressupor que você já possuí o interpretador Node.js instalado, já adianto que por se tratar de um pacote apenas para produção não há versões para Windows, e apesar da instalação até ser feita, pois o NPM não permite bloquear a instalação por plataforma, o programa não irá rodar. Segundo a equipe do projeto no Github não há planos para fazer essa compatibilidade.

Após a instalação temos acesso ao comando "pm2", e a partir desse faremos todas as operações necessárias. Para exemplificar, usarei um projeto bem simples que basicamente retorna algumas informações do servidor rodando, como <em>uptime</em>, número de processadores, quantidade de memória total, entre outras informações úteis.

O projeto está disponível neste <a href="http://github.com/jaschweder/ping.js" target="_blank">link</a>. O código é bem simples e basicamente só usa o modulo <em>http</em> do node para iniciar um servidor e o modulo <em>os</em> para gerar as informações. Para criarmos nossa primeira instância da aplicação com o pm2 execute o seguinte comando na raiz do projeto:
<p style="text-align: center;"><strong>pm2 start ping.js</strong></p>
Logo em seguida podemos verificar se a aplicação está rodando corretamente com o seguinte comando:
<p style="text-align: center;"><strong>pm2 list</strong></p>
Algo como a imagem abaixo deve ser exibido.

<img class="aligncenter size-full wp-image-4629" src="/images/wp-content/uploads/2016/01/Captura-de-tela-de-2016-01-25-202321.png" alt="pm2 list" width="770" height="113" />

Agora conforme está colocado no arquivo <strong><em>ping.js</em></strong>, se acessarmos no navegador o endereço <strong>"http://localhost:3000"</strong> será retornado um objeto JSON com as informações que comentei anteriormente.

Contudo, até aqui, o pm2 não se diferencia  dos outros gerenciadores ou até mesmo de executar apenas o próprio node com o comando <strong>"node ping.js"</strong>, o resultado é o mesmo e veremos agora alguns diferenciais dele. O primeiro é a possibilidade criar um cluster. O conceito básico de um cluster é criar varias instâncias da aplicação e fazer o balanceamento da requisições do cliente entre elas, otimizando o uso de recursos. Para criarmos um cluster no PM2 inicialmente precisamos remover a aplicação que adicionamos anteriormente executando o comando abaixo para remover :
<p style="text-align: center;"><strong>pm2 delete 0</strong></p>
Agora se rodarmos <strong>"pm2 list"</strong> novamente não será mais exibido a aplicação <strong>"ping.js"</strong>.

A criação de um cluster no PM2 é muito simples. Após criado, o pm2 irá equilibrar automaticamente as requisições entre essas instâncias, melhorando assim o uso de CPU, atento que o ideal é ter o número de instâncias igual ao número de processadores. Do contrário pode inclusive acontecer de sua aplicação ficar mais lenta e o tempo de resposta aumentar consideravelmente.
<p class="editor editor-colors" style="text-align: center;"><strong><span class="text">pm2 start index.js -i 4
</span></strong></p>
<code class="highlight bash"></code>Este comando escala a aplicação para 4 instâncias, para visualizar estas instâncias use o comando <strong>"pm2 list"</strong> e você verá uma saída parecida com a imagem abaixo:

<img class="aligncenter wp-image-4630" src="/images/wp-content/uploads/2016/01/Captura-de-tela-de-2016-01-25-210344.png" alt="pm2 + 4 instâncias" width="620" height="172" />

Talvez você se pergunte porque fazer isso se o Node.js é <strong>event-driven</strong>,  ou seja, trabalha com I/O de forma assíncrona. Para entender isso você precisa entender a forma como Node.js trabalha, que faz basicamente a otimização do uso de I/O, não aguardando que uma leitura ou escrita acabe e fazendo o uso de callbacks. Resumidamente quando o Node.js precisa fazer algum I/O, como chamada a um banco de dados, API ou escrita e leitura de arquivo em disco, você sempre irá informar uma função de callback, dizendo ao Node.js que quando acabar de fazer a ação ele precisa executar a função designada. Isso otimiza a questão de leitura e escrita mas não o de CPU, que continua sendo usado sincronicamente. Por essa razão o próprio site oficial está bem claro quais tipos de aplicações que o Node.js é eficiente, que são as chamadas "Data Drive Applications", ou aplicações que utilizam na maioria ou principalmente funções de leitura e escrita do tipo que citei anteriormente.

Segue alguns outros comandos úteis do PM2 que você pode se interessar eu listo abaixo:
<table>
<tbody>
<tr>
<td><strong>pm2 monit</strong></td>
<td>mostra a quantidade/percentual de CPU e memória usada pelas aplicações rodando</td>
</tr>
<tr>
<td><strong>pm2 show [app-name]</strong></td>
<td>mostra informações sobre a aplicação</td>
</tr>
<tr>
<td><strong>pm2 logs ou pm2 logs [app-name]</strong></td>
<td>mostra o log da aplicação ou de todas as aplicações</td>
</tr>
<tr>
<td><strong>pm2 reload all</strong></td>
<td>recarrega os arquivos de todas as aplicações rodando</td>
</tr>
<tr>
<td><strong>pm2 restart all</strong></td>
<td>reinicia todas as aplicações rodando</td>
</tr>
<tr>
<td><strong>pm2 startup</strong></td>
<td>configura o pm2 para inicializar com o sistema operacional</td>
</tr>
</tbody>
</table>
Mais informações sobre o PM2 você pode encontrar neste <a href="https://www.npmjs.com/package/pm2" target="_blank">link</a> do site npmjs.org e neste <a href="https://github.com/Unitech/pm2" target="_blank">link</a> com o repositório no Github.

Bom pessoal espero que tenham gostado desse breve tutorial de como usar o PM2 em servidores de produção para usar melhor os recursos de CPU e memória. Até a próxima o/
<div id="_booktextmark_tab_id_" style="visibility: hidden;" title="1453638638259"></div>
