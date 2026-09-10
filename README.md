# Sala — diario de entrenamiento

App web personal para registrar entrenamientos: ejercicios, series, repeticiones, peso, tiempo y progreso.
Un solo archivo HTML sin dependencias, pensada para el móvil dentro del gimnasio.

Los datos se guardan en el navegador y se sincronizan con un **repositorio privado aparte**,
igual que Mis Gastos.

## Archivos

| Archivo | Para qué sirve |
|---|---|
| `gym-app.html` | La app entera: interfaz, lógica y sincronización |
| `manifest.json` | Permite instalarla en la pantalla de inicio |
| `sw.js` | Caché para que funcione sin cobertura |
| `icon.svg` | Icono de la app |
| `index.html` | Redirección, para que la URL corta también funcione |

Si borras `index.html`, la app hay que abrirla siempre en `…/gym-app.html`.

## Publicar

1. Sube estos archivos a la raíz del repositorio.
2. Settings → Pages → Source: *Deploy from a branch* → rama `main`, carpeta `/ (root)`.
3. En un par de minutos está en `https://<usuario>.github.io/<repo>/`.

## Conectar con el repositorio de datos

Ver `INSTRUCCIONES.md` en el repositorio de datos, o el resumen aquí:

1. Crea un repositorio **privado**, por ejemplo `gym-datos`.
2. GitHub → Settings → Developer settings → Personal access tokens → **Fine-grained tokens**.
   - Repository access: *Only select repositories* → `gym-datos`
   - Permissions → Repository permissions → **Contents: Read and write**
3. Abre la app → **Ajustes → Sincronización** → rellena usuario, repositorio, rama y token.
4. **Sincronizar ahora**. El archivo `data/sala.json` se crea solo.
5. Repite el paso 3 en el móvil con el mismo token y los dos dispositivos quedan igualados.

## Cómo funciona por dentro

- Cada cambio se guarda al momento en `localStorage`, así que la app nunca depende de la red.
- Unos segundos después programa una subida al repositorio de datos.
- Al abrir la app compara la fecha del archivo remoto con la local y se queda con la más reciente.
- El token vive solo en ese dispositivo y únicamente viaja hacia `api.github.com`.

Si entrenas en dos dispositivos sin conexión a la vez, el último que sincronice gana.
Para esos casos está **Ajustes → Exportar JSON**.

## Estructura de los datos

```json
{
  "settings": { "neon": "#22e0ff", "smoke": 0.55, "rest": 90, "bodyweight": 75 },
  "custom":   [ { "id": "own:…", "name": "…", "muscle": "Pecho", "type": "reps" } ],
  "routines": [ { "id": "…", "name": "Empuje A",
                  "items": [ { "exId": "lib:…", "sets": 4, "reps": "8-10", "rest": 90 } ] } ],
  "sessions": [ { "id": "…", "start": "ISO", "end": "ISO", "durationSec": 3600,
                  "entries": [ { "exId": "lib:…",
                                 "sets": [ { "w": 60, "r": 10, "done": true } ] } ] } ]
}
```

Tipos de ejercicio: `reps` (peso × repeticiones), `bw` (peso corporal con lastre opcional),
`time` (segundos) y `cardio` (minutos y kilómetros).

El 1RM estimado usa Epley: `peso × (1 + reps/30)`.
