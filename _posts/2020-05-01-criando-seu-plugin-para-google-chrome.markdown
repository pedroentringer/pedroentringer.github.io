---
layout: post
title:  "Criando seu plugin para Google Chrome"
date:   2020-05-01 07:20:00
categories: Nodejs
---

Heeeey, já vimos que na internet tem vaaaarios tutoriais sobre este tema, não é mesmo?
Vamos pra mais um, só que dessa vez, sem **pop-up** hahaha.

Ah, e esse plugin vai funcionar no Google Chrome, Opera e no novo Edge.

O que nós vamos fazer por aqui é explorar aquele menu que aparece nos sites quando clicamos com o botão direito do mouse. Iremos criar uma opção para compartilhar um texto selecionado com algum contato do **WhatsApp** que for definido no script.

![Menu do navegador](/assets/images/posts/5d73b650-3a82-4487-b133-a66217e0a7a7.png)

### Let's go to code
Vamos iniciar nosso projeto com uma pasta em branco, e nela criaremos o primeiro arquivo chamado **manifest.json**. Este arquivo contem todos os detalhes de nossa extensão e eu vou explicar os mais importantes agora.

Vamos colocar nele essas configurações:

<pre><code class="language-json">{
  "background": {
    "persistent": false,
    "scripts": ["src/index.js"]
  },
  "browser_action": {
    "default_title": "Compartilhamento do Pedro",
    "default_icon": "icon.png"
  },
  "icons": {
      "128": "icon.png",
      "16": "icon.png",
      "32": "icon.png",
      "48": "icon.png"
  },
  "description": "Compartilhar frases e sites",
  "manifest_version": 2,
  "name": "Compartilhamento do Pedro",
  "permissions": ["contextMenus"],
  "short_name": "Compartilhamento do Pedro",
  "version": "0.1"
}
</code></pre>

Vamos ver melhor o que significa isso:
- **background**: aqui ficam as configurações de segundo plano, ou seja, aquilo que vai acontecer por tras da tela.
  - **persist**: definimos que não vamos salvar nada, ou seja, não iremos persistir nenhum dado.
  - **scripts**: e aqui vem o principal, serão os nossos arquivos de javascript que irão manipular o menu.
- **browser_action**: aqui ficam as configurações mais visuais como icone e titulo que irão aparecer no menu.
- **permissions**: Este passo é muito importante, pois sempre precisaremos deixar claro as permissões que desejamos usar. E para o nosso tutorial precisaremos apenas do **contextMenus**, mas você pode ver as outras [clicando aqui](https://support.google.com/chrome/a/answer/7515036?hl=pt-BR "Permissões de Aplicativos e Extensões").

Feito isso vamos colocar um icone na extensão né, e pode ser na raiz do projeto mesmo, no meu caso vou por minha foto de perfil hahahah.

**Lembre-se** de colocar o arquivo com o mesmo nome e extensão que foi inserido no **manifest.json**

#### Cadê o código?
kkkk eu sei, eu sei. Falei demais. **bora codar**. 

Crie uma pasta ```src``` e um ```index.js``` nela e vamos entender duas coisas. A instalação e as funcionalidades.

##### Instalação 
Sempre que você instala uma extensão o chrome vai executar uma função chamada ```chrome.runtime.onInstalled```. E é essa função que iremos usar para criarmos os nossos menus.

Pra começar vamos definir quais serão os nossos menus, dessa forma:
<pre><code class="language-javascript">const menus = [
  {
    id: 'numero01',
    title: 'Compartilhar com a pessoa 01'
  },
  {
    id: 'numero02',
    title: 'Compartilhar com a pessoa 02'
  }
];
</code></pre>

Show, agora vamos entender como vai funcionar esse onInstalled do chrome.
<pre><code class="language-javascript">const onInstalled = () => {
  //essa função vai executar sempre que a extensão for instalada
}

chrome.runtime.onInstalled.addListener(onInstalled)
</code></pre>

Bom, se já temos uma função que vai ser executada logo de inicio, é nela que iremos criar nossos menus. E vamos fazer isto usando uma outra função do chrome que é a ```chrome.contextMenus.create```, então vamos percorrer todos os nossos menus e cria-los.

<pre><code class="language-javascript">const menus = [
  {
    id: 'numero01',
    title: 'Compartilhar com a pessoa 01'
  },
  {
    id: 'numero02',
    title: 'Compartilhar com a pessoa 02'
  }
];

const onInstalled = () => {

  for (const menu of menus) {

    chrome.contextMenus.create({ 
      ...menu, 
      contexts: ['selection'], 
    })
        
  }

}

chrome.runtime.onInstalled.addListener(onInstalled)
</code></pre>

Reparou que o ```chrome.contextMenus.create``` recebe um ```Object``` como parâmetro? É isso que são os menus do chrome hehehe. 

Nós ainda passamos um array de **contexts** pra essa função, e pra que isso server? É aqui onde definimos quando o menu aparece. Usando o context **selection** estamos dizendo que o menu vai aparecer sempre que selecionarmos um texto em algum site e clicarmos com o botão direito. 

Você pode ver todos os contexts permitidos [clicando aqui](https://developer.chrome.com/apps/contextMenus#type-ContextType "Contexts").

Se testarmos essa extensão agora, já vamos conseguir ver nossos menus por lá:
![Menu do navegador com as nossas opções](/assets/images/posts/12028790-eb93-4015-aaee-252a8bf74dc8.png)

##### Funcionalidade
Shoooow, já chegamos até aqui! Agora é mole demais!

Pra adicionar funcionalidade ao clique em nosso menu iremos usar a outra função do chrome que é a ```chrome.contextMenus.onClicked``` e o nosso código vai ficar mais ou menos assim:

<pre><code class="language-javascript">const onClicked = (data, tab) => {
    const { menuItemId, selectionText} = data
    alert(`Selecionou o texto "${selectionText}" para compartilhar com "${menuItemId}"`)
}

chrome.contextMenus.onClicked.addListener(onClicked)
</code></pre>

Agora sempre que selecionarmos um texto e usarmos nosso compartilhamento o navegador vai mostrar isso em um alerta hehehe
![Menu do navegador com as nossas opções](/assets/images/posts/e7e74506-a182-4dc7-9983-96078f8cb539.png)
 
**Mas é só isso?** kkkkkk Calma. Vamos de fato compartilhar o texto! 

Então agora é simples, já temos o texto e o numero da pessoa, então é só fazer o redirect pra url do **WhatsApp** usando outro recurso do chrome chamado ```chrome.tabs.create``` que irá criar uma aba no navegador com a url que passarmos.

<pre><code class="language-javascript">const onClicked = (data, tab) => {
    
    const { menuItemId, selectionText} = data
    
    const resource = new URL("https://api.whatsapp.com/send")
    resource.searchParams.append('phone', menuItemId)
    resource.searchParams.append('text', selectionText)

    chrome.tabs.create({ url: resource.href })

}
</code></pre>

E se você fez tudo certinho seu plugin já estará funcionando perfeitamente!!! 

Claro que esse exemplo é bastante simples, e você provavelmente não irá usar para nada, mas o que vale é entender o funcionamento de extensões do chrome.

Aqui no trabalho nós usamos uma extensão semelhante a essa pra poder consultar pedidos diretamente do e-mail sem precisar ficar abrindo o sistema toda vez. Ah, e a extensão foi criada dessa mesma forma.

O código completo ficou assim:
<pre><code class="language-javascript">const menus = [
    {
        id: 'pessoa-01',
        title: 'Compartilhar com a pessoa 01'
    },
    {
        id: 'pessoa-02',
        title: 'Compartilhar com a pessoa 02'
    }
];
  
const onInstalled = () => {

    for (const menu of menus) {

        chrome.contextMenus.create({ 
        ...menu, 
        contexts: ['selection'], 
        })
            
    }

}

const onClicked = (data, tab) => {

    const { menuItemId, selectionText} = data
    
    const resource = new URL("https://api.whatsapp.com/send")
    resource.searchParams.append('phone', menuItemId)
    resource.searchParams.append('text', selectionText)

    chrome.tabs.create({ url: resource.href })

}

chrome.contextMenus.onClicked.addListener(onClicked)
chrome.runtime.onInstalled.addListener(onInstalled)
</code></pre>

Super bacana né? E você pode aplicar isso em diversas outras funcionalidades. Aqui não existem limites. hehehe

**Divirta-se!**