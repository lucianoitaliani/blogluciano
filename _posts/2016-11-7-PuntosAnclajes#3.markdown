---
layout: post
title: Nave#3 - Puntos de Anclajes
---
Buenas,

Hoy hablaremos de los Puntos de Anclajes en el caso General.
La primer pregunta es, ¿Qué es un punto de anclaje?
Bien, esto funciona como un modulo donde el piloto
puede interactuar y a traves de ellos puede realizar una accion
como mover la nave o disparar una torreta.

Tres casos de punto de anclajes.
1. La consola de mando. (Dar movimiento a la Nave)
2. Consola de proyectil. (Dispara una clase de proyectil)
3. Consola de misil. (Dispara misiles)

Este desarrollo es creado bajo Unity 5.4.1 en c#.

`Importante`: Desde el punto de vista de la jerarquia, el `GameObject` es hijo
del `GameObject` Nave.

<h2>Caso General:</h2>

El caso general es un `Prefab`, el nombre es consola y tiene cuatro componenetes:
1. <em>Transform</em>
2. <em>BoxCollider</em>
3. <em>SpriteRenderer</em>
4. <em>Script(ActivarZona.cs)</em>

<h3>Layer:</h3>
No necesita de ningun `Layer` especial, por lo tanto usaremos el mismo que el del
`GameObject` padre.

<h3>Transform:</h3>
Lo modificaremos para darle una posicion relativa algun lugar arbitrario de la Nave.

<h3>SpriteRenderer:</h3>
Utilizaremos la imagen de una consola con teclado. No es necesario darle ningun otro
valor.

<h3>BoxCollider:</h3>
Mediante la UI de Unity definiremos los limites del `Sprite`. El unico proposito de esto
es que el jugador nos traspase la consola como un fantasma.

<h3>Script(ActivarZona.cs):</h3>
Lo primero que tenemos que hacer es aclarar que va hacer el `Script`. La idea es que detecte cuando
un piloto quiere interectuar con una consola.

Por lo tanto tenemos que definir el campo de deteccion, que tan grande es.
Saber cuando el piloto esta en ese campo.
Comprobar si el piloto quiere o no interactuar con la consola.
Y por ultimo hacer este sondeo solo con el piloto que el Jugador eligio.
Primero definimos las variables:

{% highlight c# %}
private bool estadoActivacion;
public string objetoActivar;

public float radio;
public LayerMask mascara;
public Transform ubicacion;

public Transform objetoInteraccion;
public Transform[] piloto;

private Seleccion controladorjuego;

void Start() {
	estadoActivacion = false;
	controladorjuego = GameObject.Find("Controlador").GetComponent<Seleccion>();
}
{% endhighlight %}

El `estadoActivacion` nos dice si la consola esta activada por algun piloto o no y el `objetoActivar`
nos dice que objeto.

El segundo grupo de variables definiremos el radio de deteccion, el tipo de objetos que queremos detectar
mediante una `Layer` (En este caso serian pilotos) y por ultimo la posicion del centro del radio.

En el tercer grupo tenemos el arreglo de pilotos, donde guardaremos sus referencias y tendremos la Transform
del `objetoInteraccion`. El `objetoInteraccion` tiene como estado inicial desactivado y consta de un `Script` donde se habilitara cuando el piloto interactue con la consola.

La cuarta variable la omitiremos porque es el controlador de los multiples pilotos que tendremos.
En entradas futuras nos centraremos en esto.

`IMPORTANTE`: Este `GameObject` es un `Prefab` que al darle como atributo public a las variables nos permite modificar (Desde de la UI de Unity) posiciones, mascaras, cantidad de pilotos y no re escribir una y otra vez lo mismo.

{% highlight c# %}
private void activar() {
	estadoActivacion = true;
	objetoInteraccion.gameObject.SetActive(true);

	piloto[controladorjuego.getParteNum()-1].GetComponent<Piloto>().setQueTrabajo(objetoActivar);
}

private void desactivar() {
	estadoActivacion = false;
	objetoInteraccion.gameObject.SetActive(false);

	piloto[controladorjuego.getParteNum()-1].GetComponent<Piloto>().setQueTrabajo(null);
}
{% endhighlight %}

Los metodos de arriba activan o desactivan el uso de una consola. La ultima linea de cada metodo define el trabajo del piloto activo.
El significa de: `controladorjuego.getParteNum()-1` , se basa en que para elegir un piloto el Jugador debera
apretar un numero 1,2,3,4,.. En el arreglo de piloto nosotros ponemos en el indice 0 al piloto llamado por
el numero 1 y asi sucesivamente. Ahi el motivo de restar uno.
Una solucion simple si el -1 molesta, es dejar el indice 0 sin referencia y empezar por el numero 1.
Con lo que la linea quedaria:

{% highlight c# %}
piloto[controladorjuego.getParteNum()].GetComponent<Piloto>().setQueTrabajo(null);
{% endhighlight %}

Bien, seguimos analizando el `Script`:

{% highlight c# %}
void FixedUpdate () {

	if(!estadoActivacion && Physics2D.OverlapCircle(ubicacion.position, radio, mascara) && Input.GetKey("e")) {

		if(controladorjuego.getParteNum() == 1 &&
			Physics2D.OverlapCircle(ubicacion.position, radio, mascara).name == "Piloto01")
			activar();
		else if(controladorjuego.getParteNum() == 2 &&
				 Physics2D.OverlapCircle(ubicacion.position, radio, mascara).name == "Piloto02")
			activar();

	}
	else if(estadoActivacion && Input.GetKey("f"))
		desactivar();

}
{% endhighlight %}

En el primer if evaluaremos tres condiciones:
	Que el `estadoActivacion` sea falso el piloto no esta interactuando.
	En la segunda condicion comprobamos si hay un piloto en el area de interaccion.
	En la tercera condicion comprobamos si el Jugador apreto la tecla "e", para habilitar la intereccion.

En el else correpondiente al if anterior:
	Evaluaremos si estadoActivacion es true y por lo tanto el piloto esta operando.
	La segunda condicion comprobamos, si la tecla f es pulsada, para deshabilitar la interaccion.

En el if anidado buscamos que piloto está activo, mediante el nombre y el que controla el Jugador.
Esta parte la suplantaremos por un for, pero por el momento la dejaremos con el caso especial para
dos Pilotos.

En la siguiente entrada hablaremos:
1. La consola de mando. (Dar movimiento a la Nave)
2. Consola de proyectil. (Dispara una clase de proyectil)
3. Consola de misil. (Dispara misiles)

Debe entenderse que el GameObject de esas 3 consola será el de esta misma entrada pero con `objetosInteraccion`
distintos. por lo tanto este `Prefab` sera la piedra angular de los modulos.

Saludos.-
