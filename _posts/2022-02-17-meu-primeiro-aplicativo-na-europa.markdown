---
layout: post
lang: pt-BR
title:  "Meu primeiro aplicativo na Europa"
date:   2022-02-17 14:12:00
categories: ReactNative
---

<img src="/assets/images/pages/covid-check-lu-banner.jpg">

**Hey Dev, tudo bem contigo?**

Hoje venho compartilhar uma experiência bem bacana que pude vivenciar em minha nova aventura.

Aqui em Luxemburgo existe um certificado de vacinação (UEDCC - Certificado Digital Covid da União Europeia) que é exigido nos estabelecimentos (restaurantes, bares, shows, etc...) e cada momento da pandemia e/ou tipo de ambiente possuem regras diferentes.

As regras são chamadas de 3G, 2G e 2G+ (Na ordem de dificuldade).

Para validar isso, o governo precisou criar um App Offline First para ler o QRCode de cada certificado e validar se o certificado tem uma assinatura digital válida e se as datas e doses da vacinação, teste de covid e/ou certificado de recuperação estão de acordo com a regra válida para aquele período e estabelecimento.

Bom, em alguns momentos eu percebi que o App do governo fecha sozinho ou trava a ponto de não fazer as validações, o que fazia que os estabelecimentos nos liberassem assim mesmo.

Dito isso, eu resolvi entender como funcionavam as validações dessas regras dentro do APP que foi feito em ionic, para tentar replicar exatamente as mesmas validações usando React Native.

**et voilá**

As validações consistem em:
- Base45
- CBOR
- COSE
- Assinatura PEM


E de quebra, adicionais algumas funcionalidades que o App oficial não possuí, sendo elas:
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