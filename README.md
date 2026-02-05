<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Voltea tu Carta y Gana</title>

  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(120deg, #0f172a, #1e293b);
      color: white;
      text-align: center;
      padding: 20px;
    }

    h1 {
      font-size: 2.5em;
      color: gold;
      text-shadow: 0 0 15px orange;
    }

    .info {
      background: rgba(255,255,255,0.1);
      padding: 15px;
      border-radius: 15px;
      margin: 15px auto;
      max-width: 600px;
      font-size: 1.1em;
    }

    .admin-box {
      background: rgba(0,0,0,0.4);
      padding: 15px;
      border-radius: 15px;
      max-width: 500px;
      margin: 20px auto;
    }

    input {
      padding: 10px;
      border-radius: 10px;
      border: none;
      margin: 5px;
      width: 80%;
      max-width: 300px;
      text-align: center;
    }

    button {
      padding: 12px 20px;
      border: none;
      border-radius: 15px;
      background: gold;
      font-weight: bold;
      cursor: pointer;
      margin-top: 10px;
    }

    button:hover {
      background: orange;
    }

    .players {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
      gap: 15px;
      max-width: 700px;
      margin: auto;
      margin-top: 20px;
    }

    .player-card {
      background: rgba(255,255,255,0.1);
      padding: 15px;
      border-radius: 15px;
    }

    .flip-card {
      width: 100px;
      height: 140px;
      margin: auto;
      perspective: 1000px;
    }

    .flip-inner {
      width: 100%;
      height: 100%;
      transition: transform 1s;
      transform-style: preserve-3d;
      position: relative;
    }

    .flipped .flip-inner {
      transform: rotateY(180deg);
    }

    .flip-front, .flip-back {
      width: 100%;
      height: 100%;
      position: absolute;
      border-radius: 15px;
      backface-visibility: hidden;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 2em;
    }

    .flip-front {
      background: crimson;
    }

    .flip-back {
      background: white;
      color: black;
      transform: rotateY(180deg);
    }

    #resultado {
      margin-top: 25px;
      font-size: 1.5em;
      color: lime;
      font-weight: bold;
    }
  </style>
</head>

<body>

  <h1>🎴 VOLTEA TU CARTA Y GANA 🎴</h1>

  <div class="info">
    <p>Bienvenido al sorteo interactivo 🎰</p>
    <p>📌 Cada participante se registra en una casilla.</p>
    <p>Cuando el administrador lance el juego, todos recibirán una carta aleatoria.</p>
    <p>🏆 ¡El que obtenga la carta más alta gana el premio en USDT!</p>
  </div>

  <div class="admin-box">
    <h2>⚙️ Configuración del Administrador</h2>
    <input type="number" id="apuesta" placeholder="Valor de la apuesta (ej: 2 USDT)">
    <input type="number" id="premio" placeholder="Premio ganador (ej: 200 USDT)">
    <br>
    <button onclick="modoAdmin()">👑 Entrar como Admin</button>
    <button id="btnLanzar" style="display:none;" onclick="lanzarCartas()">🎰 Lanzar Sorteo</button>
  </div>

  <h2>👥 Participantes (6 cupos)</h2>

  <div class="players" id="players"></div>

  <div id="resultado"></div>

  <script>
    const passwordAdmin = "allen123"; // CAMBIA TU CONTRASEÑA AQUÍ
    let jugadores = Array(6).fill(null);

    const cartas = ["2","3","4","5","6","7","8","9","10","J","Q","K","A"];

    function crearJugadores() {
      const contenedor = document.getElementById("players");
      contenedor.innerHTML = "";

      jugadores.forEach((jugador, index) => {
        contenedor.innerHTML += `
          <div class="player-card">
            <h3>Jugador ${index+1}</h3>
            <p>${jugador ? "✅ " + jugador.nombre : "Disponible"}</p>
            <button onclick="registrar(${index})" ${jugador ? "disabled" : ""}>
              ${jugador ? "Ocupado" : "Unirse"}
            </button>

            <div class="flip-card ${jugador && jugador.carta ? "flipped" : ""}">
              <div class="flip-inner">
                <div class="flip-front">🂠</div>
                <div class="flip-back">${jugador && jugador.carta ? jugador.carta : ""}</div>
              </div>
            </div>
          </div>
        `;
      });
    }

    function registrar(i) {
      let nombre = prompt("Escribe tu nombre o alias:");
      if(nombre) {
        jugadores[i] = { nombre: nombre, carta: null };
        crearJugadores();
      }
    }

    function modoAdmin() {
      let clave = prompt("Contraseña de administrador:");
      if(clave === passwordAdmin) {
        alert("✅ Acceso concedido");
        document.getElementById("btnLanzar").style.display = "inline-block";
      } else {
        alert("❌ Contraseña incorrecta");
      }
    }

    function lanzarCartas() {
      let apuesta = document.getElementById("apuesta").value;
      let premio = document.getElementById("premio").value;

      if(!apuesta || !premio) {
        alert("⚠️ Admin debe poner apuesta y premio antes.");
        return;
      }

      let activos = jugadores.filter(j => j !== null);

      if(activos.length < 2) {
        alert("⚠️ Se necesitan mínimo 2 jugadores.");
        return;
      }

      // Asignar cartas aleatorias
      jugadores.forEach(j => {
        if(j) {
          j.carta = cartas[Math.floor(Math.random() * cartas.length)];
        }
      });

      crearJugadores();

      // Buscar ganador
      let ganador = activos.reduce((max, j) =>
        cartas.indexOf(j.carta) > cartas.indexOf(max.carta) ? j : max
      );

      document.getElementById("resultado").innerHTML =
        `🏆 GANADOR: ${ganador.nombre} <br>
         🎉 Premio: ${premio} USDT <br>
         💰 Cada participante apostó: ${apuesta} USDT`;
    }

    crearJugadores();
  </script>

</body>
</html>
