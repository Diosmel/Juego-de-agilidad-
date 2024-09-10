# Juego-de-agilidad-
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MatchMania</title>
<style>
	body {
		font-family: 'Arial', sans-serif;
		background: linear-gradient(to right, #283048, #859398);
		color: #fff;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		height: 100vh;
		margin: 0;
	}
	h1 {
		font-size: 2.5em;
		margin-bottom: 20px;
		text-shadow: 2px 2px #000;
	}
	button {
		font-size: 1.5em;
		padding: 15px 30px;
		margin: 10px;
		cursor: pointer;
		background-color: #F67280;
		border: none;
		border-radius: 8px;
		color: white;
		box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.2);
		transition: background-color 0.3s ease;
	}
	button:hover {
		background-color: #C06C84;
	}
	#informacion p {
		margin: 5px 0;
		font-size: 1.2em;
	}
	#menuPrincipal, #tutorial, #informacion, #juego, #resultado, #reiniciar, #siguiente, #volverMenu, #cuentaRegresiva, #iniciarPantalla {
		display: none;
	}
	#juego {
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		gap: 10px;
		width: 400px;
		margin-top: 20px;
	}
	.carta {
		background-color: #3A3A3A;
		border-radius: 8px;
		box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.2);
		text-align: center;
		font-size: 40px;
		cursor: pointer;
		display: flex;
		justify-content: center;
		align-items: center;
		transition: transform 0.5s ease, background-color 0.5s ease;
	}
	#resultado {
		font-size: 2em;
		margin-top: 20px;
	}
	#cuentaRegresiva {
		font-size: 3em;
		margin-bottom: 20px;
	}
	#iniciarPantalla {
		font-size: 2.5em;
		text-align: center;
		margin-top: 50px;
		cursor: pointer;
		font-weight: bold;
		color: #FFD700;
		text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
		animation: parpadeo 1s infinite;
	}

	@keyframes parpadeo {
		0% { opacity: 1; }
		50% { opacity: 0.5; }
		100% { opacity: 1; }
	}
</style>
</head>
<body>

<h1>MatchMania</h1>

<div id="menuPrincipal">
	<button id="modo1">Iniciar Juego</button>
	<button id="tutorialBtn">Tutorial</button>
</div>

<div id="tutorial">
	<button id="tutorial1">¿Cómo jugar?</button>
	<button id="volverMenuTutorial">Volver al Menú Principal</button>
</div>

<div id="informacion">
	<p>Nivel: <span id="nivel">1</span></p>
	<p>Puntos: <span id="puntos">0</span></p>
	<p>Tiempo restante: <span id="tiempo">30</span> segundos</p>
</div>

<div id="cuentaRegresiva"></div>

<div id="juego"></div>
<p id="resultado"></p>
<button id="reiniciar">Reiniciar</button>
<button id="siguiente">Siguiente Nivel</button>
<button id="volverMenu">Volver al Menú Principal</button>

<div id="iniciarPantalla">Toca en cualquier parte de la pantalla para iniciar</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
	document.querySelector('#menuPrincipal').style.display = 'block';
});

let nivel = 1;
let puntos = 0;
let tiempoRestante = 30;
let tiempoInicial = 30;
let intervalo = null;
let juego = document.querySelector('#juego');
let primeraCarta = null;

const emojis = ['⚽️', '🎾', '🏀', '🏈', '⚾️', '🎱', '🥋', '🥊', '🏆', '🥇', '🎯', '🎮', '🎰', '🧩', '🎷', '🎤', '🎨', '🚨', '🛞', '🛟', '⚓️', '💎', '💸', '💵', '💰', '🦠', '🩸', '🪬', '⚔️', '💣', '🎁'];

document.querySelector('#modo1').addEventListener('click', mostrarPantallaInicio);
document.querySelector('#tutorialBtn').addEventListener('click', mostrarTutorial);
document.querySelector('#tutorial1').addEventListener('click', () => alert('Encuentra todos los pares de emojis antes de que se acabe el tiempo. Completa 10 niveles para ganar.'));
document.querySelector('#volverMenuTutorial').addEventListener('click', volverAlMenuPrincipal);
document.querySelector('#volverMenu').addEventListener('click', volverAlMenuPrincipal);
document.querySelector('#siguiente').addEventListener('click', siguienteNivel);
document.querySelector('#reiniciar').addEventListener('click', reiniciarJuego);
document.querySelector('#iniciarPantalla').addEventListener('click', iniciarModo1);

function mostrarTutorial() {
	ocultarTodo();
	document.querySelector('#tutorial').style.display = 'block';
}

function volverAlMenuPrincipal() {
	ocultarTodo();
	document.querySelector('#menuPrincipal').style.display = 'block';
}

function mostrarPantallaInicio() {
	ocultarTodo();
	document.querySelector('#iniciarPantalla').style.display = 'block';
}

function iniciarModo1() {
	ocultarTodo();
	document.querySelector('#cuentaRegresiva').style.display = 'block';
	let cuenta = 3;
	const cuentaRegresiva = setInterval(() => {
		document.querySelector('#cuentaRegresiva').textContent = cuenta;
		if (cuenta === 0) {
			clearInterval(cuentaRegresiva);
			iniciarJuego();
		}
		cuenta--;
	}, 1000);
}

function iniciarJuego() {
	document.querySelector('#cuentaRegresiva').style.display = 'none';
	document.querySelector('#informacion').style.display = 'block';
	document.querySelector('#juego').style.display = 'grid';
	document.querySelector('#volverMenu').style.display = 'inline-block';

	juego.innerHTML = '';
	juego.style.pointerEvents = 'auto';
	primeraCarta = null;

	let imagenes = [...emojis.slice(0, nivel * 2), ...emojis.slice(0, nivel * 2)];
	imagenes.sort(() => Math.random() - 0.5); // Asegura que las cartas se mezclen bien

	colocarCartas(imagenes);
	document.querySelector('#nivel').textContent = nivel;
	document.querySelector('#puntos').textContent = puntos;
	tiempoRestante = tiempoInicial - (nivel - 1) * 2; // Reducir 2 segundos por cada nivel
	document.querySelector('#tiempo').textContent = tiempoRestante;
	iniciarIntervaloTiempo();
}

function colocarCartas(imagenes) {
	imagenes.forEach(imagen => {
		let carta = crearCarta(imagen);
		juego.appendChild(carta);
	});
}

function crearCarta(imagen) {
	let carta = document.createElement('div');
	carta.classList.add('carta');
	carta.innerHTML = `<div class="contenido">${imagen}</div>`;
	carta.addEventListener('click', manejarCartaClick);
	return carta;
}

function manejarCartaClick(event) {
	let carta = event.currentTarget;
	if (primeraCarta && primeraCarta !== carta) {
		if (primeraCarta.innerHTML === carta.innerHTML) {
			primeraCarta.style.visibility = 'hidden';
			carta.style.visibility = 'hidden';
			puntos += 20; // Ganar 20 puntos por pareja correcta
			document.querySelector('#puntos').textContent = puntos;
			primeraCarta = null;
		} else {
			let cartaTemporal = primeraCarta;
			setTimeout(() => {
				if (cartaTemporal) {
										cartaTemporal.classList.remove('revelada');
				}
				carta.classList.remove('revelada');
				primeraCarta = null;
			}, 1000);

			// Perder 10 puntos por pareja incorrecta
			puntos -= 10;
			document.querySelector('#puntos').textContent = puntos;
		}

		// Comprobar si todas las cartas han sido descubiertas
		if ([...document.querySelectorAll('.carta')].every(carta => carta.style.visibility === 'hidden')) {
			clearInterval(intervalo);
			if (nivel < 10) {
				mostrarEmojiGanador();
				document.querySelector('#siguiente').style.display = 'inline-block';
			} else {
				mostrarMensajeGanador();
				document.querySelector('#reiniciar').style.display = 'inline-block';
			}
		}
	} else {
		carta.classList.add('revelada');
		primeraCarta = carta;
	}
}

function iniciarIntervaloTiempo() {
	intervalo = setInterval(() => {
		tiempoRestante--;
		document.querySelector('#tiempo').textContent = tiempoRestante;

		if (tiempoRestante <= 0) {
			clearInterval(intervalo);
			mostrarMensajePerdedor();
			document.querySelector('#reiniciar').style.display = 'inline-block';
			juego.style.pointerEvents = 'none';
		}
	}, 1000);
}

function mostrarEmojiGanador() {
	let emoji = document.createElement('div');
	emoji.innerHTML = '💯';
	emoji.style.fontSize = '4em';
	emoji.style.textAlign = 'center';
	document.body.appendChild(emoji);
	setTimeout(() => emoji.remove(), 1500);
}

function mostrarMensajePerdedor() {
	let mensaje;
	if (puntos >= 160) {
		mensaje = `¡Perdiste! Estuviste muy cerca del último nivel con ${puntos} puntos. ¡Sigue así!`;
	} else if (puntos >= 80) {
		mensaje = `¡Perdiste! Lograste un buen desempeño con ${puntos} puntos. ¡Con un poco más de práctica lo lograrás!`;
	} else {
		mensaje = `¡Perdiste! Solo acumulaste ${puntos} puntos. ¡No te desanimes, sigue intentando!`;
	}
	document.querySelector('#resultado').textContent = mensaje;
	document.querySelector('#resultado').style.display = 'block';
}

function mostrarMensajeGanador() {
	document.querySelector('#resultado').textContent = `¡Felicidades! Has completado los 10 niveles con ${puntos} puntos.`;
	document.querySelector('#resultado').style.display = 'block';
}

function siguienteNivel() {
	nivel++;
	document.querySelector('#siguiente').style.display = 'none';
	iniciarJuego();
}

function reiniciarJuego() {
	nivel = 1;
	puntos = 0;
	tiempoRestante = tiempoInicial;
	document.querySelector('#reiniciar').style.display = 'none';
	document.querySelector('#resultado').style.display = 'none';
	iniciarModo1();
}

function ocultarTodo() {
	document.querySelectorAll('#menuPrincipal, #tutorial, #informacion, #juego, #resultado, #reiniciar, #siguiente, #volverMenu, #cuentaRegresiva, #iniciarPantalla').forEach(el => el.style.display = 'none');
}
</script>

</body>
</html>