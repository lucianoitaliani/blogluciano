---
layout: post
title: Nave#4 - Puntos de Anclajes
---

Buenas,

En la entrada de hoy pondremos el motor a funcionar.
Para esta entrada necesitaremos el Prefab del Punto de anclaje. (Nave - Punto de Anclaje # 3)

Este desarrollo es creado bajo Unity 5.4.1 en c#.

Crearemos un nuevo `GameObject` con el `Prefab` del Punto de Anclaje.
El nombre que le pondre al `GameObject` es timon(arbitrario).

El `Sprite` que usaré es simplemente el de una consola. No debe ser grande.
El `BoxCollider` definirá los límites del `Sprite`.

Las variables publicas del `Script`:

1. objetoActivar = motor; (String)
2. radio = 0.2f;
3. mascara = pilotos; (En caso de que no exista crearlo y aplicarlo a los pilotos)
4. ubicacion = timon(Transform);
5. Piloto[] = Pondremos logitud 2 y ligaremos los GameObject de los dos pilotos;

Solo resta una variable que es `objetoInteraccion`. Esta variable hace referencia
al objeto que crearemos en esta entrada.

1. objetoInteraccion = motor;

<h2>Motor - GameObject:</h2>

Crearemos un GameObject vacio.
Este `GameObject` solo contendra dos componentes. Y sera hijo de el
`GameObject` Nave. Por defecto este `GameObject` estará desactivado.

1. Transform
2. Comportamiento.cs (Script)

La `Transform` no es importante, por lo que solo nos ocuparemos del
`Script`. Este `Script` se comportara de manera analoga a Movimiento.cs

{% highlight c# %}
private float velX;
private float velY;

public Transform proa;
private GameObject nave;

void Start () {
	velY = 0f;
	velX = 0f;

	nave = GameObject.Find("Nave");
}
{% endhighlight %}

Las dos primeras variable se usaran para detectar si hay movimiento
de parte del jugador por medio del teclado. (Ya visto en Movimiento)

Luego desde la UI de Unity estableceremos la `Transform` del `GameObject` proa en la variable
proa.

Y por ultimo buscaremos el `GameObject` Nave que en este caso es el padre. Estas
dos variables nos permetiran crear el vector director.
Segundo paso será crear un método para que haga el calculo del vector director.

{% highlight c# %}
private Vector3 calculoDireccion() {
	return popa.position - nave.GetComponent<Transform>().position;
}
{% endhighlight %}

y por ultimo usaremos el FixedUpdate():

{% highlight c# %}
void FixedUpdate () {

	// Parte 1:
	velX = Input.GetAxis("Horizontal");
	velY = Input.GetAxis("Vertical");

	// Parte 2:
	if(velY > 0 || 0 > velY) {
		Vector3 direccion = calculoDireccion();
		nave.GetComponent<Rigidbody2D>().velocity =
			new Vector2(velY*direccion.x, velY*direccion.y);
	}

	// Parte 3:
	if(velX > 0 || velX < 0) {
		nave.transform.Rotate(Vector3.back * velX);
	}

	// Parte 4:
	nave.GetComponent<Nave>().
		setVelCruceroX(nave.GetComponent<Rigidbody2D>().velocity.x);
	nave.GetComponent<Nave>().
		setVelCruceroY(nave.GetComponent<Rigidbody2D>().velocity.y);

}
{% endhighlight %}

1. Chequeamos si el Jugador apretó alguna flecha.<br>
2. En caso que el valor absoluto de vely sea mayor a cero, la nave se moverá según el vector director.<br>
3. Si el valor absoluto de velx es mayor a cero, la nave girará.<br>
4. Definiremos la nueva velocidad crucero de la nave.<br>

Con esta nueva entrada ya estamos piloteando la nave, solo nos queda habilitar los mecanismo de disparos
y estaremos listo para marchar al combate.

Saludos.-
