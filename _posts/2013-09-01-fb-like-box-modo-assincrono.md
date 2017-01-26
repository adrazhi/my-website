---
layout: post
title: Carregar a Like Box do Facebook em modo assíncrono
lang: pt
---


A Like Box do Facebook é um poderoso extra que permite aumentar o número de <em>likes</em> a partir de um determinado site, contudo a sua integração em modo padrão envolve um determinado custo.

Em termos práticos é adicionado ao seu site uma grande quantidade de código javascript. Mas qual é o problema do código disponibilizado pelo Facebook?

Ahh é claro! A Like Box carrega em modo síncrono.

## Mas o que é isso do modo síncrono?

Em jeito de resumo: o modo síncrono reduz a velocidade de carregamento das suas páginas. Tal facto acontece porque simplesmente a maioria dos browsers não suporta a transferência em paralelo de ficheiros .js. Assim, o carregamento das páginas pode ficar “bloqueado” enquanto essas transferências não são concluídas.

## Integração da Like Box em modo assíncrono

Primeiro é necessário obter o código da sua Like Box através do Facebook Developers. Após a escolha das opções clique em “Get Code” e na aba HTML5 irá visualizar o seguinte código:

<strong>Para introduzir antes da tag </ body >:</strong>

{% highlight html %}
<div id="fb-root"></div>

<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/pt_PT/all.js#xfbml=1&appId=XX";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>
{% endhighlight %}

<strong>Para introduzir no local onde pretende que a Like Box apareça:</strong>
{% highlight html %}
<div class="fb-like-box" data-href="http://www.fabiosantos.pt/"
data-width="292" data-show-faces="false" data-header="false"
data-stream="false" data-show-border="false"></div>
{% endhighlight %}

<strong>No primeiro código, deve inserir entre as tags < script > </ script > a seguinte linha para que o carregamento da Like Box seja efectuado em modo assíncrono:</strong>
{% highlight js %}
js.async=true;
{% endhighlight %}

<strong>Resultado final do código a introduzir antes da tag </ body >:</strong>
{% highlight html %}
<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id; js.async=true;
  js.src = "//connect.facebook.net/pt_PT/all.js#xfbml=1&appId=X";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>
{% endhighlight %}

Não se esqueça que o Google gosta de sites rápidos e optimizados. Esta é uma dica valiosa para conseguir alguns pontos extra junto dos principais motores de busca e alcançar assim melhores posições nos resultados.
