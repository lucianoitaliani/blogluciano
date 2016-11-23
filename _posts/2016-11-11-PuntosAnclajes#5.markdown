---
layout: post
title: Nave#5 - Punto de Anclajes
---
Buenas,

En la entrada de hoy hablaremos de los ultimos dos puntos de anclajes que utilizaremos por ahora
en la Nave: `consolaProyectiles` y `consolaMisiles`.
Por tal nombre podemos determinar que tratara de los sistemas de ataque de la nave.

Este desarrollo es creado bajo Unity 5.4.1 en c#.

<h2>consolaProyectil - GameObject:</h2>

Creamos un nuevo `Prefab` Punto de Anclaje. El nombre que le pondremos es consolaProyectil.
Este `GameObject` sera hijo de Nave.

El `Sprite` que ulizaremos es el de una consola.
El `BoxCollider` definira los limites del `Sprite`.

<h3>Script: ActivarZona.cs</h3>
1. Las variables públicas del Script: (Esto debe hacerse en la UI de Unity)
2. radio = 0.2f;
3. mascara = pilotos;
4. ubicacion = Es la Transform del GameObject.
5. pilotos: (Estamos usando por ahora el caso especial 2) size = 2

Y en cada elemento debemos colocar la Transform del Piloto.
La ultima variable a establecer es `objetoInteraccion`.
Para estar completar esta variable primero debemos crear un `GameObject` vacio.
Lo nombraremos torreta.
Este `GameObject` se compondra por tres Componentes:

1. Transform
2. SpriteRenderer
3. Script

La `Transform` es la ubicacion. En el `Sprite` ira la imagen de la torreta.
Con el `Script` nos encargaremos de simular el disparo cuando el Jugador
aprete el boton izquierdo del mouse o alguna tecla especial.

{% highlight c# %}
Cañon.cs:(Script)

    public Transform arma;
	public Transform[] bala;

	private float angulo_inf;
	private float angulo_sup;

	void Start () {
		angulo_inf = -360f;
		angulo_sup = 360;
	}
{% endhighlight %}

El primer grupo de variables es el mas importante, en arma tendremos
la `Transform` de la imagen de la torreta. La otra variable es bala que
es un arreglo, el motivo es que puede que haya armas que tiren mas de
un proyectil por tiro.

    `IMPORTANTE`: Es necesario crear un Sprite2D como hijo de consolaProyectil
	y nombrarlo imagen_torreta. Ademas es necesario crear un GameObject vacio
	como hijo de imagen_torreta.
	Este ultimo GameObject se llamará bala y tendra dos componentes

	Transform
	Sprite

	En Sprite pondremos la imagen de esta.
	IMPORTANTE: La estructura de la municion va ir variando pero empezaremos por esta.
	IMPORTANTE: Este GameObject esta por defecto desactivado.

El motivo de que sean publico es para poder reutilizar el `Script`.

El segundo grupo de variable restringe el barrido angular de la torreta. Por el momento
este es de 360. Puede cambiarse en el futuro.

Seguimos con el analisis del `Script`.

{% highlight c# %}
void Update () {

	Vector3 mouse = Input.mousePosition;
	Vector3 direccion = Camera.main.ScreenToWorldPoint(mouse) - arma.position;
	float angulo = Mathf.Atan2(direccion.y, direccion.x) * Mathf.Rad2Deg;
{% endhighlight %}

Con la variable mouse vamos a seguir el rastro del punto y ver donde este esta, cuando el
Jugador hace click.
En la segunda variable tenemos una diferencia entre vectores que nos queda el vector director entre
la posicion del mouse y el arma.

{% highlight c# %}
    Camera.main.ScreenToWorldPoint(mouse)
{% endhighlight %}

El metodo `ScreenToWorldPoint` nos sirve para hacer una tranforma del vector posicion del mouse respecto de
la pantalla al eje coordenado del juego. La pantalla tiene como vector(0,0) el esquinero inferior izquierdo.

Con la ultima variable calculamos el angulo.

{% highlight c# %}
	Mathf.Atan2(direccion.y, direccion.x) * Mathf.Rad2Deg;
{% endhighlight %}

Atan2(..) calcula la tangente(y/x) (¿Motivo? Sor Car Toa) el resultado del metodo es retornado en
radianes y lo que hacemos por ultimo es convertirlo en grados.

{% highlight c# %}
	if(Input.GetMouseButtonDown(0))
		for(int i=0; i<bala.Length; i++) {
			Transform go = Instantiate(bala[i]);
			go.position = new Vector2(bala[i].position.x, bala[i].position.y);

			go.gameObject.SetActive(true);
			go.rotation = Quaternion.AngleAxis(angulo, Vector3.forward);
			go.GetComponent<Rigidbody2D>().velocity = new Vector2(direccion.x,direccion.y);
		}
{% endhighlight %}

En el condicional if comprobamos si el jugador apreto el boton izquierdo.
En el caso de que sea verdadero el condicional instanciaremos una nueva variable
con la informacion en cada indice de bala.

Una vez instanciado, lo activaremos, porque la original estara desactivada.
Rotaremos el proyectil hasta el angulo obtenido anteriormente y por ultimo
le daremos la velocidad.

{% highlight c# %}
		arma.rotation = Quaternion.AngleAxis(angulo, Vector3.forward);
		for(int i=0; i<bala.Length; i++)
			bala[i].rotation = Quaternion.AngleAxis(angulo, Vector3.forward);

	}
{% endhighlight %}

Por ultimo rotaremos tambien la torreta para seguir el curso del puntero.

<h2>consolaMisiles - GameObject:</h2>

Este `GameObject` es exactamente igual al anterior solo que los `Sprite` deben ser distintos.
En vez de una torreta una alnzadera de misiles; En vez de proyectiles, misiles.

Es importante tener en cuenta que las balas todavia no colisionan. Evite hacerlo en esta entrada
para no extenderla demasiado.

Resumiendo. Tenemos a los pilotos, la nave, la consola del motor para movimiento y la consola de sistema de
ataques. Con todo esto solo nos queda hablar del controlador de pilotos y ya estaremos listo para nuestra primera mision.

Saludos.-


<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-87908822-1', 'auto');
  ga('send', 'pageview');

</script>
