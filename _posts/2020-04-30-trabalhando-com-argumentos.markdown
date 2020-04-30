---
layout: post
title:  "Trabalhando com Argumentos em Node.js"
date:   2020-04-30 13:00:00
categories: Nodejs
---

Você já fez aquele programa que executa uma tarefa especifica e que funciona perfeitamente, dai de repente surge a necessidade de que esse programa receba um parâmetro ao iniciar.

Vamos supor que ele precise receber um CNPJ no inicio do programa, por algum motivo do destino, talvez pra buscar os dados em alguma API. O que importa agora pra gente é entender como usar os argumentos.

Naturalmente o Node.js já nos permite acessar argumentos no inicio do programa, então vamos supor que eu deseje passar dois parâmetros, sendo eles **cnpj** e **isActive**, eu faria dessa forma:
<pre><code class="language-bash">$ node index 123 true</code></pre>

E lá no código, eu precisaria acessar esses parâmetros da seguinte maneira:
<pre><code class="language-javascript">const cnpj = process.argv[2]
const isActive = Boolean(process.argv[3])
</code></pre>

**Funciona**? Sim, funciona. Mas você percebe que tanto a forma de passar esses argumentos quanto a de recebe-los ficou estranha? Talvez não agora, mas no futuro sua equipe ou até outra equipe de trabalho saberão o que significa cada argumento desse ou terão que ficar lendo o código pra identificar?

Pois é, eu passei por isso a um tempo atrás, e por isso fiz a lib **@pedroentringer/parser-command-line** que simplifica muito este processo.

Então vamos instalar esse trem:
<pre><code class="language-bash">$ yarn add @pedroentringer/parser-command-line</code></pre>

Sensacional, feito isso nós já podemos passar e receber argumento de uma forma muito mais simples.
Usando o mesmo exemplo ficaria dessa forma:
<pre><code class="language-bash">$ node index --cnpj=123 -isActive</code></pre>

Já melhorou né? Agora veja como faremos pra acessar isso no código:
<pre><code class="language-javascript">const parser = require('@pedroentringer/parser-command-line')
const args = parser.parseArgs();

console.log(args)
</code></pre>

Muito mais simples né? Agora veja como será o resultado disso:
<pre><code class="language-javascript">{
  bin: "node",
  commands: ["index"],
  flags: {
    cnpj: 123,
    isActive: true
  }
}
</code></pre>

Dessa forma fica muito melhor pra gente usar estes dados não é mesmo? E melhor ainda, você pode usar essa lib na web também, dessa forma é possível fazer o parse de um comando vindo de uma string também. Pense que ela veio via websocket do backend, ou qualquer outro cenário hehehe.

Usariamos dessa forma:
<pre><code class="language-javascript">const parser = require('@pedroentringer/parser-command-line')
const args = parser.parseString('meuPrograma show --message="Que legal, isso é uma mensagem" --time=100 -isSuccess');

console.log(args)
</code></pre>

e o resultado desse parse será:
<pre><code class="language-javascript">{
  bin: "meuPrograma",
  commands: ["show"],
  flags: {
    message: "Que legal, isso é uma mensagem",
    time: 100,
    isSuccess: true
  }
}
</code></pre>


Super bacana né? E você pode aplicar isso para diversas outras funcionalidades. Aqui não existem limites. hehehe

**Divirta-se!**