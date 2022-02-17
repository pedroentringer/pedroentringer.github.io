---
layout: post
lang: pt-BR
title:  "Meu primeiro aplicativo na Europa"
date:   2022-02-17 13:12:00
categories: ReactNative
---


**Hey Dev, tudo bem contigo?** Hoje venho compartilhar uma experiência bem bacana que pude vivenciar em minha nova aventura.

<img src="/assets/images/pages/covid-check-lu-banner.jpg">

Recentemente me mudei do Brasil para Luxemburgo, e tenho vivido ótimas experiências e quero compartilhar contigo uma que me ajudou como portfólio que resolve um problema real.

Aqui em Luxemburgo existe um certificado de vacinação **(UEDCC - Certificado Digital Covid da União Europeia)** que é exigido nos estabelecimentos (restaurantes, bares, shows, etc...) e cada momento da pandemia e/ou tipo de ambiente possuem regras diferentes.

As regras são chamadas de 3G, 2G e 2G+ (Na ordem de dificuldade), veja com detalhes as regras nesses links:
- https://guichet.public.lu/en/actualites/2021/decembre/28-app-covidcheck.html
- https://covid19.public.lu/en/covidcheck.html#:~:text=The%202G%2B%20system,CoV%2D2%20rapid%20antigen%20test

Para validar isso, o governo precisou criar um **App Offline First** para ler o QRCode de cada certificado e validar se:
- O Certificado possuí uma assinatura digital válida
- O Certificado não foi revogado (Cancelado)
- O Certificado não foi adulterado
- Está dentro da regra vigente

Bom, em alguns momentos eu percebi que o App do governo fecha sozinho ou trava a ponto de não fazer as validações, o que fazia que os estabelecimentos nos liberassem sem fazer as validações necessárias.

Dito isso, eu resolvi entender como funcionavam as validações dessas regras dentro do APP que foi feito em ionic, para tentar replicar exatamente as mesmas validações usando React Native.

**et voilá**

O Primeiro passo foi entender como o App funcionava, e com um pouco de tempo pude perceber que:
- O App foi escrito em ionic, o que me ajudou, pois já usei bastante ionic no passado.
- Lendo o código JS gerado, consegui identificar as validações que são feitas em:
    - Base45
    - CBOR
    - COSE
    - Assinatura PEM
- Existe uma API oficial que só é acessível com uma chave .p12 que nos fornece:
    - Certificados com chave publica para validar a assinatura dos certificados de vacinação
    - Lista de certificados revogados
        - Encryptado em SHA256

Com todas essas informações, eu comecei a criar o layout do novo Aplicativo e escrever todas essas validações obrigatórias. Por fim, conseguimos chegar ao resultado desejado.

E de quebra, adicionei algumas funcionalidades que o App oficial não possuí, sendo elas:
- 5 idiomas disponíveis
- Salvar os meus certificados offline

Veja o resultado final em: https://pedroentringer.dev/app/covid-check-lu/
Layout Inicial: https://www.figma.com/file/FgWcvLqgUQyMF77909GxQM/CovidCheck---Lu?node-id=0%3A1

<div class="splash projects">
    <div class="content">
        <span class="title">Publicação</span>
        <span class="description">Este aplicativo não pode ser publicado por usar dados sensíveis relacionados a saúde, segundo a Apple e Google.</span>
    </div>
</div>