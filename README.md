# 🎮 Chuhan Análisis 

**Chuhan Análisis** es una aplicación flotante (*overlay*) ligera para Windows desarrollada en Python. Su objetivo es proporcionar asistencia táctica, información de *matchups* y recomendaciones de *builds* optimizadas en tiempo real a los jugadores de League of Legends mientras se encuentran en la Fase de Selección de Campeones o dentro de la partida.

---

## 🚀 ¿Qué hace la aplicación?

* **Análisis de Matchup en Early Game:** Identifica a tu rival directo de línea y proporciona consejos de juego táctico para los niveles 1 a 3 (intercambios, cuidado con habilidades clave, control de oleadas).
* **Build Meta Dinámica:** Consulta las métricas y recomendaciones actuales para armar los mejores objetos según el campeón seleccionado.
* **Información de Partida en Vivo:** Muestra el rendimiento en tiempo real (Súbditos/CS por minuto, oro actual) y calcula el tiempo de aparición de objetivos neutrales (Dragón, Heraldo, Barón).
* **Ruta de Jungla (Jungle Pathing):** Ofrece la ruta óptima de *farmeo* inicial según el campeón utilizado en el rol de Jungla.
* **Temporizadores de Enfriamiento:** Herramienta integrada para rastrear los *Flashes* e invocadores del equipo enemigo.
* **Interfaz Adaptable:** Modo compacto flotante (*Always-on-Top*) diseñado para no obstaculizar la visión del juego.

---

## 🛠️ ¿Cómo toma la información en tiempo real?

La aplicación obtiene los datos del juego de forma totalmente automatizada combinando dos fuentes oficiales que proporciona el propio cliente de League of Legends:

### 1. Lectura en Selección de Campeones (LCU API via WebSockets)
A través de la librería `lcu-driver`, la aplicación se conecta localmente al puerto de la **League Client Update (LCU) API** usando credenciales del cliente en ejecución.
* Mantiene un **WebSocket en escucha constante** registrando los eventos de la fase de selección (`/lol-champ-select/v1/session`).
* Identifica en tiempo real tu rol, el campeón que seleccionaste y determina quién es tu rival directo de línea.
* Consulta el rango soloq del invocador rival en tiempo real.

### 2. Monitoreo en Partida Activa (Live Client Data API)
Una vez iniciada la partida, un hilo secundario (`threading`) consulta en un bucle ligero a la API local de Riot (`https://127.0.0.1:2999/liveclientdata/`):
* **`/gamestats`**: Extrae el tiempo transcurrido de la partida para calcular contadores de objetivos y ritmo del juego.
* **`/playerlist`**: Captura los puntajes de súbditos (CS), hechizos de invocador (para detectar automáticamente si juegas Jungla mediante el *Smite*) y la estructura de equipos
* **`/activeplayer`**: Obtiene el oro actual acumulado y estadísticas del jugador.

### 3. Procesamiento y Asignación de Contenido
Con los nombres e identificadores capturados, el script realiza peticiones asíncronas a las APIs públicas de **DataDragon (Riot Games)** y fuentes externas para descargar iconos de objetos, atributos y generar de forma adaptativa la guía de compra e instrucciones para la fase de líneas.
