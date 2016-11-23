---
layout: post
title: Analytics en la pagina.
---

Buenas,

Hoy estoy escribiendo una entrada corta. Dado que en la anterior armamos una pagina de
cero en un host nuevo, una de las cosas que nos falto fue implementar las analytics.
Para ello es necesario crear una cuenta en google analytics o usar una cuenta de google
ya existente.
Una vez completado el formulario para hacer un track a la pagina, google nos dara un
script creado en javascript.

{% highlight html %}
  <script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-XXXXXXXX-X', 'auto');
  ga('send', 'pageview');
  </script>
{% endhighlight %}

Una vez con el codigo, vamos a nuestra carpeta donde se encuentra el repositorio de
jekyll y creamos una carpeta.

    $ mkdir _includes
    $ cd _includes

Una vez adentro creamos un archivo para guardar el codigo.

    $ touch analytics.html
    $ gedit analytics.html

En mi caso use gedit, pero es algo totalmente arbitrario. Y por ultimo vamos por cada
post y tambien en la pagina principal agregando la siguiente linea:

{% highlight html %}
    { % include analytics.html % }
{% endhighlight %}

Y listo con eso ya tenemos todo el poder del analytics para monitorear nuestro trafico.<br>
Saludos.-

{% include analytics.html %}
