---
layout: post
title: Controlador#1
---

Buenas,

Hoy hablaremos del controlador del juego, sobre la funcion que cumple en el juego
y por que es una en el desarrollo de este.

Este `GameObject` se encargara de que solo podamos utilizar un piloto a la vez, tambien
comprobara si este esta trabajando con alguna consola o simplemente esta esperando alguna
instruccion. En caso de que este esperando alguna instruccion los controles habituales
moveran al piloto, en caso de que este trabajando el Jugador interactuara con el objeto
ligado al punto de anclaje.

<h2>Controlador - GameObject:</h2>

Crearemos un `GameObject` vacio.
Sera hijo de la `Scene`.
Solo contendra dos componentes:

1. Transform
2. Seleccion.cs (Script)

El `Script` hara lo que explicamos una lineas mas arriba. En esta entrada solo hablaremos
de lo fundamental, y no agregaremos nada sobre la UI.

{% highlight c# %}
private int parteNum;
private bool notrabaja;

public Transform[] piloto;
private Piloto[] p00;

void Start () {

	parteNum = 1;
	notrabaja = false;

	p00 = new Piloto[piloto.Length];
	for(int i=0; i<piloto.Length; i++)
		p00[i] = piloto[i].GetComponent<Piloto>();
}
{% endhighlight %}

La variable parteNum, dira que piloto el Jugador ha seleccionado. Por defecto empezara con el 1.
La variable notrabaja, nos dira si el piloto esta operando algun punto de anclaje.

La variable public piloto guardara las refencias a los pilotos.
la variable p00, sera un acceso rapido a la Componenete piloto de la variable piloto.

Este `Script` va estar constantemente operativo ultilizando el metodo FixedUpdate().

{% highlight c# %}
void FixedUpdate () {

	if(Input.GetKey(KeyCode.Alpha1)) {
		parteNum = 1;
		pilotoElegido();
	}
	else if(Input.GetKey(KeyCode.Alpha2)) {
		parteNum = 2;
		pilotoElegido();
	}


	verificaPilotoTrabaja();
}
{% endhighlight %}

Este metodo verificara si se apreto la tecla 1 o la tecla 2 y ademas comprobara
si el piloto esta trabajando o no. Esto es necesario porque si el piloto no trabaja
el `Script` de movimiento tiene que estar activo, caso contrario desacivado.

{% highlight c# %}
private void verificaPilotoTrabaja() {

	// Parte 1:
	if(parteNum == 1 && notrabaja && p00[parteNum-1].getQueTrabajo() != null) {
		piloto[parteNum-1].GetComponent<Movimiento>().enabled = false;
		notrabaja = false;
	}
	else if(parteNum == 2 && notrabaja && p00[parteNum-1].getQueTrabajo() != null) {
		piloto[parteNum-1].GetComponent<Movimiento>().enabled = false;
		notrabaja = false;
	}

	// Parte 2:
	if(parteNum == 1 && !notrabaja && p00[parteNum-1].getQueTrabajo() == null) {
		piloto[parteNum-1].GetComponent<Movimiento>().enabled = true;
		notrabaja = true;

		pilotoElegidoReserva();
	}
	else if(parteNum == 2 && !notrabaja && p00[parteNum-1].getQueTrabajo() == null) {
		piloto[parteNum-1].GetComponent<Movimiento>().enabled = true;
		notrabaja = true;

		pilotoElegidoReserva();
	}

}
{% endhighlight %}

Recordemos que estamos trabajando para el caso especial 2 pilotos. En la parte 1
comprobamos si el piloto seleccionado trabaja. En la parte 2 comprobamos si el
piloto seleccionado no trabaja.

{% highlight c# %}
private void pilotoElegidoReserva() {

	for(int i=0; i<p00.Length; i++) {
		if(i == parteNum-1) {
			p00[i].setElegidoPorJugador(true);
		}
		else {
			p00[i].setElegidoPorJugador(false);
		}
	}

}
{% endhighlight %}

Por ultimo actualizamos el estado elegido por Jugador de cada piloto.
Continuando con el `Script`.

{% highlight c# %}
private void pilotoElegido() {
	for(int i=0; i<piloto.Length; i++) {
		if(i == parteNum - 1) {
			activarConfiguracionTrabajo(p00[i], piloto[i]);
			p00[i].setElegidoPorJugador(true);
		}
		else {
			desactivarConfiguracionTrabajo(p00[i], piloto[i]);
			p00[i].setElegidoPorJugador(false);
		}
	}
}
{% endhighlight %}

En el metodo pilotoElegido() activaremos de la configuracion trabajo del piloto seleccionado y para el resto de los pilotos dejaremos todo desactivado.

{% highlight c# %}
private void activarConfiguracionTrabajo(Piloto p_script, Transform piloto) {

	if(p_script.getEstadoTrabajo() != null) {
		p_script.getEstadoTrabajo().SetActive(true);
		piloto.GetComponent<Movimiento>().enabled = false;
	}

	else{
		piloto.GetComponent<Movimiento>().enabled = true;
		notrabaja = true;
	}

}

private void desactivarConfiguracionTrabajo(Piloto p_script, Transform piloto) {

	if(p_script.getEstadoTrabajo() != null)
		p_script.getEstadoTrabajo().SetActive(false);
	piloto.GetComponent<Movimiento>().enabled = false;

}
{% endhighlight %}

En los metodos de `configuracionTrabajo` simplemente verificamos si el piloto esta inactivo u operando algun punto de anclaje.
Con el `GameObject` Controlador ya creado ahora podemos operar dos pilotos a la vez.
Este `Script` es una de la piedras fundamentales del juego y ahora ya establecido estamos en condicion de
empezar a crear a nuestros enemigos, UI y nuestro primer corto del juego.

Desde ya saludos.-
