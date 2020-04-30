---
layout: post
title:  "Manipulando dados de um site com Web Scraping"
date:   2020-04-29 15:30:00
categories: Nodejs
---

É impressionante a ideia de conseguir manipular os dados de um site de outra pessoa não é mesmo? Eu adoro isso, logo quando comecei a faculdade de programação fiquei intrigado por não ser a primeira coisa a aprender (que sonho).

Mas vamos direto ao ponto, aqui vamos aplicar o Web Scraping em um site simples e em outro um pouco mais complexo. **Let`s go to code**.

#### Bibliotecas utilizadas:
* Cheerio
* @pedroentringer/cheerio-table-parser **(Mágica kkk)**
* Axios
* Puppeteer

#### Manipulando dados de um site simples
Pra começar vamos criar nosso projeto bem rapidinho

{% prism bash %}
$ yarn init -y
$ yarn add cheerio axios puppeteer @pedroentringer/cheerio-table-parser
{% endprism %}

Isso pode demorar alguns minutinhos para terminar, enquanto isso vamos falar um pouco sobre cada uma dessas dependências.

##### Axios
O Axios é muuuio usado para fazer requisições HTTP, quando você quer buscar ou enviar dados para algum site.
Por aqui vamos usar ele para buscar a estrutura do meu proprio blog hehehe

##### Cheerio
O Cheerio é uma lib que nos permite manipular a DOM do site com mais facilidade, muito similar com a forma do JQuery.

##### @pedroentringer/cheerio-table-parser
Essa é uma lib que eu mesmo fiz pra resolver uma problema que eu tive recentemente. Ela basicamente recebe qualquer tabela em HTML e converte para JSON, assim fica mais fácil de manipular os dados né? hehehe

##### Puppeteer
O Puppeteer é incrivel, com ele você consegue mapear ações para que um "bot" replique no site usando um navegador, como se fosse uma pessoa de verdade, **top né?**

##### Let's go to code
Depois que tudo tiver instalado certinho, bora começar de uma forma simples.
O desafio vai ser buscar o titulo do primeiro post desse nosso blog aqui.

Pra fazer isso vamos criar nosso primeiro arquivo **index.js** e já importar o _axios_ e o _cheerio_ nele

{% prism javascript %}
const axios = require('axios')
const cheerio = require('cheerio')
{% endprism %}

Show, feito isso já podemos usar o axios para fazer uma busca na home do nosso blog, e vamos fazer isso da seguinte forma:
{% prism javascript %}
const webscraping = async () => {

  const { data } = await axios.get('https://pedroentringer.dev')

  const $ = cheerio.load(data)

  const posts = $('.post-title a')

  const firstPost = posts.first()

  console.log(firstPost.text())

}
webscraping()
{% endprism %}

E o resultado disso será:
{% prism javascript %}
Manipulando dados de um site com Web Scraping
{% endprism %}

Simples né? Agora vamos complicar um pouco mais.

#### Manipulando dados de um site com autenticação e navegação
Agora imagine um cenário que você está fazendo um projeto freelancer pra uma empresa que possui um ecommerce em Magento 1.9, eles precisam visualizar facilmente os 5 pedidos mais populares do site. 

Nesse caso, a cada pagina do painel é gerado um token diferente, e seria bem chato ficar adivinhando como ele gera esse token para que possamos reproduzir da mesma forma, né? Maaas, para resolver isso nós podemos acessar esses dados usando um navegador, e é ai que entra o puppeteer.

Vamos lá, importando todas as libs:
{% prism javascript %}
import cheerio from 'cheerio'
import parseTable from '@pedroentringer/cheerio-table-parser'
import puppeteer from 'puppeteer'
{% endprism %}

Repare que substituimos o axios pelo puppeteer.
E agora é só abrir o navegador, acessar o site, e **daaaale**.
{% prism javascript %}

//Com o puppeteer eu chamo um navegador
const browser = await puppeteer.launch({
  headless: true //Aqui eu defino se quero que ele apareça ou não => true (não aparece) | false (aparece)
})

try {

  //Crio uma nova aba no navegador
  const page = await browser.newPage()

  //Acesso a pagina de login do painel
  await page.goto('meu_site/admin')

  //Insiro meu email no input de login
  await page.type('input[id=username]', 'oi@pedroentringer.dev')

  //Insiro minha senha no input do login
  await page.type('input[id=login]', 'minha_senha')

  //Clico no botão de Entrar
  await page.click('input[type=submit]')

  /**
   *  Agora eu preciso aguardar o site carregar
   *  e o elemento com id #grid_tab_reviewed_products aparecer na tela
   *  
   *  No painel do Magento 1.9 esse elemento representa um botão que 
   *  irá carregar na tela uma tabela com os 5 produtos mais visitados no site
   *  
   *  Assim que ele aparecer eu clico nele
   */
  await page.waitForSelector('#grid_tab_reviewed_products', { visible: true, timeout: 0 })
  await page.click('#grid_tab_reviewed_products')

  /**
   * Apos o clique, preciso esperar nossa tabela aparecer, pois o Magento irá fazer um request e buscar esses dados
   */
  await page.waitForSelector('#productsReviewedGrid_table', { visible: true, timeout: 0 })

  /**
   * Perfeito, agora que chegamos aqui, é como se estivessemos naquele exemplo simples
   * Basta pegar o html da pagina e manipular com o cheerio
   */
  const html = await page.content()

  //Lembre-se sempre de fechar o navegador kkk
  await browser.close()

  //Importo o HTML do site atual para o cheerio
  const $ = cheerio.load(html)
  
  //Ativo o modulo de converter a tabela
  parseTable($)

  //converto a tabela para JSON
  const table = $('#productsReviewedGrid_table').parseTable()

  console.log(table)

} catch (err) {
  await browser.close()
  console.error(err)
}
{% endprism %}

O mais legal é o resultado super fácil de manipular, veja:

{% prism javascript %}
[
  {
    nome: 'Camiseta Infantil Ac/Dc Preta',
    preco: 'R$ 42,90',
    visitas: 185
  },
  {
    nome: 'Body Beatles Yellow Submarine Manga Curta Branco',
    preco: 'R$ 39,90',
    visitas: 178
  },
  {
    nome: 'Body Iron Maiden Manga Curta Preto Algodão',
    preco: 'R$ 39,90',
    visitas: 170
  },
  {
    nome: 'Body Manga Curta Chega De Nana Neném Preto',
    preco: 'R$ 39,90',
    visitas: 144
  },
  {
    nome: 'Body Ac/Dc Manga Curta Preto',
    preco: 'R$ 39,90',
    visitas: 141
  }
]
{% endprism %}

Super bacana né? E você pode aplicar isso para diversas outras funcionalidades. Aqui não existem limites. hehehe

**Divirta-se!**