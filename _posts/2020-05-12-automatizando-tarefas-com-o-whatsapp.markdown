---
layout: post
title:  "Automatizando tarefas com o WhatsApp"
date:   2020-05-12 22:50:00
categories: Nodejs
---

Heeeey, tudo bem contigo? Estou muito animado com essa dica de hoje, porque eu amo esse tipo de projeto. Espero que você curta essa **experiência**.

<div class="splash projects">
    <div class="content">
        <span class="title">Já manja de Node.js?</span>
        <span class="description">Então talvez você prefira ir direto para o código do projeto.</span>
    </div>
    <div class="btn">
        <a href='https://github.com/pedroentringer/bot-whatsapp-services' target="_blank">Ver o Projeto</a>
    </div>
</div>

<img src="/assets/images/posts/f2c98102-23ed-45b9-a60a-00f89b8dfe0c.jpeg" style="width: 200px">

**Então vamos direto ao que interessa!!**

Recentemente uma turma lançou uma biblioteca simplesmente **sensacional** que nos permite explorar **demais** as funcionalidades do WhatsApp Web usando seu WebPack. A lib ficou incrivelmente profissional e simples de usar.

O pacote chama-se **whatsapp-web.js** e você pode conferir toda documentação [clicando aqui](https://waguide.pedroslopez.me/ "whatsapp-web.js"), e o github [clicando aqui](https://github.com/pedroslopez/whatsapp-web.js "whatsapp-web.js").

##### Bora Codar
Já crie um projeto por ai, com essa estrutura:

![Estrutura de Pastas](/assets/images/posts/6f208174-0592-4142-a5f1-360e2f4b2554.png)

###### Pacotes
Neste projeto iremos utilizar os seguintes pacotes, então inicie seu projeto com a estrutura acima e instale os pacotes:

- sucrase
- whatsapp-web.js
- @pedroentringer/parser-command-line

<pre><code class="language-bash">$ yarn init -y
$ yarn add sucrase whatsapp-web.js @pedroentringer/parser-command-line
</code></pre>

Como iremos utilizar o **sucrase** aqui para poder escrever com import/export vamos criar um script de start no ```package.json```

<pre><code class="language-json">{
  "name": "bot-whatsapp",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "start": "sucrase-node src/index.js"
  },
  "dependencies": {
    "@pedroentringer/parser-command-line": "^1.0.1",
    "sucrase": "^3.15.0",
    "whatsapp-web.js": "^1.6.0"
  }
}
</code></pre>

Show, feito isso vamos para o nosso arquivo ```index.js``` dentro da pasta ```src``` e já iniciar nosso bot.

<pre><code class="language-javascript">//importo o Client do pacote
import { Client } from 'whatsapp-web.js'

/**
 * Crio o client, e é aqui que ele irá abrir o navegador de forma oculta usando puppeteer
 *
 * Porem aqui eu quero poder ler o QRCode pelo navegador sempre que for a primeira execução
 * para que assim eu não precise fazer isso pelo terminal então eu passo o headless como false
 */
const client = new Client({
  puppeteer: {
    headless: false
  }
})

//Escuto o evento ready que é disparo quando o navegador abre o whatsapp corretamente
client.on('ready', () => console.log('Client is ready!'))

//Escuto o evento message que é disparado quando o whatsapp recebe uma nova mensagem
client.on('message', message => console.log(message))

//inicializo o client para que tudo comece a rodar
client.initialize()
</code></pre>

Show de bola, se você rodar isso já vai conseguir ver seu navegador abrindo. Dai você vai precisar scanear o QRCode para acessar o WhatsApp.
Feito isso, sempre que receber uma nova mensagem ela ira aparecer no seu terminal.

**E olha que irado**, você já consegue receber todas as mensagens direto no código, sensacional né? Faça você mesmo, rode esse comando no seu terminal: 

<pre><code class="language-bash">$ yarn start</code></pre>

### Executando comandos
Esse sisteminha vai rodar em um Windows Server, então todos os meus comandos Shell aqui serão para Win. **Mas não se preocupe**, você poderá usar outros comandos de acordo com seu servidor.

O Node.js tem por padrão um carinha chamado ```exec``` que você pode importar do pacote ```child_process``` e é exatamente ele que iremos usar para rodar nossos comandos. Nele você consegue executar scripts como se estivesse no terminal/cmd da sua máquina.

Pra fazer isso vamos criar um arquivo chamado ```Shell.js``` na pasta ```src/components``` dessa forma:

<pre><code class="language-javascript">//importo o exec do node
import { exec } from 'child_process'

//crio uma classe pra controlar todos os comandos
export default class Shell {

  /** 
   * Defino uma function chamada restart que irá reiniciar alguns serviços
   * Perceba que ela recebe os parametros que são tratados pela lib @pedroentringer/parser-command-line
   */
  async restart ({ commands, flags }) {
    for (const command of commands) {
      
      /**
        * Se o comando for igual a "cte" vou matar o serviço que está rodando atualmente e rodar ele novamente
        */
      if (command === 'cte') {
        this.executeCommands([
          'taskkill /f /im cmd.exe /fi "windowtitle eq CTE"',
          'taskkill /f /im apagt_cte_nfe.exe',
          'start "" "C:\\Users\\pedro.iconex\\Desktop\\CTE.bat"'
        ])
      }

      /**
        * Se o comando for igual a "email" vou parart as services responsáveis por isso e iniciá-las novamente
        */
      if (command === 'email') {
        await this.executeCommands([
          'net stop apiintegracaoemailxml.exe',
          'net start apiintegracaoemailxml.exe',
          'net stop apiintegracaoemailedi.exe',
          'net start apiintegracaoemailedi.exe'
        ])
      }
    }
  }

  async run ({ commands, flags }) {
    for (const command of commands) {
      /**
        * Se o comando for igual a "cte" eu executo um start "C:\\Users\\pedro.iconex\\Desktop\\CTE.bat" em meu CMD.
        */
      if (command === 'cte') {
        this.executeCommand('start "" "C:\\Users\\pedro.iconex\\Desktop\\CTE.bat"')
      }

      /**
        * Se o comando for igual a "cte" eu executo um start "D:\\API_NODE\\util\\ajustes\\corrigeIBGE.js" em meu CMD.
        */
      if (command === 'ibge') {
        await this.executeCommand('node "D:\\API_NODE\\util\\ajustes\\corrigeIBGE.js"')
      }

    }
  }

  /**
   * Essa função vai receber um array de comandos e executar um de cada vez.
   */
  async executeCommands (commands) {
    for (const command of commands) {
      await this.executeCommand(command)
    }
  }

  /**
   * Essa função recebe a string do comando e executa usando o "exec" do node
   */
  executeCommand (command) {
    return new Promise((resolve, reject) => {
      exec(command, (error, stdout, stderr) => {
        if (error) reject(error)
        if (stderr) reject(stderr)
        resolve('Success')
      })
    })
  }
}

</code></pre>

Uma outra coisa interessante desse ```exec``` é que a variável ```stdout``` retorna exatamente tudo que for imprimido no terminal, dai você pode tratar isso de alguma forma se for necessário.

Show de bola, até aqui nós já recebemos as mensagem e temos a classe responsável por executar os comandos. 

Mas antes de utilizar, vamos criar alguns alertas pop-up em nosso bot para que fique mais fácil de entender o que está acontecendo quando ele estiver em segundo plano.

### Criando Alertas
Eu gosto de pensar que esses scripts precisam ser ocultos, então costumo exibir um alerta na tela quando o programa inicia com sucesso ou apresenta algum erro.

Pra fazer isso vamos criar um arquivo chamado ```Alert.js``` na pasta ```src/components``` dessa forma:

<pre><code class="language-javascript">import Shell from './Shell'
const shell = new Shell()

export default class Alert {
  static showAlert (title, message) {
    const { platform } = process
    if (platform === 'darwin') this.showAlertDarwin(title, message)
    if (platform === 'win32') this.showAlertWin(title, message)
  }

  static showAlertDarwin (title, message) {
    /**
     * Repare que iremos usar nossa classe Shell para executar um script no terminal
     * Esse script exibe um pop-up no MacOs
     */
    shell.executeCommand(`osascript -e 'display notification "${message}" with title "${title}"'`)
  }

  static showAlertWin (title, message) {
    /**
     * Repare que iremos usar nossa classe Shell para executar um script no terminal
     * Esse script exibe um pop-up no Windows
     */
    shell.executeCommand(`msg %username% "${title} ${message}"`)
  }
}
</code></pre>

Show feito isso, nós já receberemos os alertas em pop-up sempre que o bot iniciar. Aqui eu só consegui testar com Mac e Win. Então provavelmente para o linux você precisará adaptar esse comando.

![Estrutura de Pastas](/assets/images/posts/6daa59c1-25a5-40b6-b157-f5d22e252a76.png)

### Gravando Sessão do WhatsApp
O problema que temos agora é que sempre que você inicia o bot é necessário ficando lendo aquele QRCode novamente. Isso é bem chato né? Imagine um **shutdown** não programado do servidor, seu bot iria parar de funcionar e você nem saberia. 

O pessoal da lib pensou nisso, e nos permitem salvar a sessão gerada pelo WhatsApp, maravilhoso né?

Pra fazer isso, vamos criar um arquivo chamado ```Session.js``` na pasta ```src/components```, esse arquivo será uma classe que controlar essa sessão.

<pre><code class="language-javascript">//importo a lib "fs" do node que me permitira ler e gravar arquivos
import * as fs from 'fs'

//importo a função "resolve" da lib "path" pra poder resolver um diretório
import { resolve } from 'path'

//importo nosso alerta, pra disparar as mensagems de sucesso ou erro
import Alert from './Alert'

//resolvo o caminho que irei salvar a sessão
const sessionPath = resolve(__dirname, '..', 'config', 'session.json')

//crio a classe Session
export default class Session {

  //crio uma função para salvar o conteudo da sessão em JSON
  static save (session) {
    fs.writeFile(
      sessionPath,
      JSON.stringify(session),
      async (error) => error && Alert.showAlert('[BOT] Pedro Entringer', error.message)
    )
  }

  //crio uma função para ler o JSON da sessão
  static get () {
    if (fs.existsSync(sessionPath)) {
      return require(sessionPath)
    }

    return undefined
  }
}

</code></pre>

Pronto! Assim que vincularmos isso ao bot você vera que será criado um arquivo ```session.json``` na pasta ```src/config```. 

**Lembre-se de criar essa pasta**, se não o código não irá funcionar.

### Criando o Bot de Fato
Já temos tudo que é preciso para executar os comandos em nosso servidor, certo? **Sim.**

Já estamos recebendo as mensagens do WhatsApp, certo? **Sim.**

##### Então o que falta agora?
Hehehe, agora precisamos entender a mensagem que está chegando do WhatsApp para saber qual comando vamos executar.

E vamos fazer isso usando a lib ```@pedroentringer/parser-command-line``` de forma bastante simples.

Crie um arquivo chamado ```Bot.js``` na pasta ```src/components```.

<pre><code class="language-javascript">//importo a lib para fazer um parse nas mensagens
import { parseString } from '@pedroentringer/parser-command-line'

//import nosso shell
import Shell from './Shell'

//inicio o shell
const shell = new Shell()

//crio uma classe bot
export default class Bot {

  /**
   * Defino seu construtor recebendo o client do WhatsApp 
   * para que eu possa responder as mensagems por aqui.
   */
  constructor (client) {
    this.client = client

    /**
     * Defino um contato, pois eu desejo que apenas ele possa me enviar mensagems.
     * Alias, este é o meu contato, mude para o seu aqui.
     */
    this.contact = '5527981670051@c.us'
  }

  //crio uma função para validar se quem está mandando a mensagem é o contato que definimos
  isAdmin (contact) {
    if (!this.client) throw new Error('Client is not ready!')

    if (contact !== this.contact) throw new Error('Client is not ready!')
  }

  /**
   * Essa função recebe a mensagem e quem enviou
   * body => Mensagem
   * from => Quem enviou
   */
  async parseMessage ({ body, from }) {
    
    //valido se quem enviou a mensagem é o nosso contato
    this.isAdmin(from)

    //faço o parse da mensagem
    const { bin, commands, flags } = parseString(body)

    //defino quais são os "bins" aceitos pelo bot
    const availlableBins = ['restart', 'run', 'commands']

    //valido os bins
    if (availlableBins.find(availlableBin => availlableBin === bin)) {
      try {

        //direciono os comandos e as flags baseado no bin recebido
        if (bin === 'commands') await this.showCommands(from)
        if (bin === 'restart') await shell.restart({ commands, flags })
        if (bin === 'run') await shell.run({ commands, flags })

        //se tudo ocorrer corretamente, respondo com sucesso.
        if (bin !== 'commands') await this.client.sendMessage(from, '✅ Command successfully executed')
      } catch (err) {

        //se houver erro respondo com o detalhe do erro.
        await this.client.sendMessage(from, `⚠️ ${err.message}`)
      }
    } else {

      //Se não for um comando válido
      await this.client.sendMessage(from, '🙁 This is not a valid command.')
    }
  }

  //lista os comandos válidos
  async showCommands (from) {
    await this.client.sendMessage(from, '🚀 List of commands')
    await this.client.sendMessage(from, '*run [{programs}]*\nUsed to run some system services.\nAvailable: cte, sefaz, ibge and ie')
    await this.client.sendMessage(from, '*restart [{programs}]*\nUsed to restart some system services.\nAvailable: cte and email')
    await this.client.sendMessage(from, '*commands*\nUsed to list all accepted commands.')
  }
}

</code></pre>

Sensacional! Isso já é suficiente para nosso Bot funcionar **perfeitamente**, vamos liga-lo?

### Toque final
Criamos todas nossas classes principais, só precisamos agora é vincular elas de fato com nosso ```client``` do bot.

Para fazer isso, vamos modificar nosso arquivo ```index.js```, e faremos da seguinte forma:
<pre><code class="language-javascript">import { Client } from 'whatsapp-web.js'

//importo todas as noosas classes
import Bot from './components/Bot'
import Alert from './components/Alert'
import Session from './components/Session'

//crio o client
const client = new Client({
  puppeteer: {
    headless: !!Session.get() //aqui eu valido se existe uma sessao, para saber se eu devo exibir o navegado ou não
  },
  session: Session.get() //busco a sessão existente
})

//inicio o bot
const bot = new Bot(client)

//exibo o alerta quando o bot estiver pronto para usi
client.on('ready', () => Alert.showAlert('[BOT] Pedro Entringer', 'Client is ready!'))

//salvo a sessão quando fizermos a primeira leitura do QRCode
client.on('authenticated', (session) => Session.save(session))

//mando as mensagens recebidas para o nosso Bot
client.on('message', message => bot.parseMessage(message))

//inicializo o client do WhatsApp Web
client.initialize()
</code></pre>

Super bacana né? E você pode aplicar isso em diversas outras funcionalidades. Aqui não existem limites. hehehe

**Divirta-se!**