# Guía de entorno — WorkshopAgent

Fork personal del repo del taller "Hands On Lab: Creando nuestro primer agente de IA"
(original: https://github.com/fcnatra/WorkshopAgent). Este archivo documenta cómo está
preparado el equipo local para hacer la práctica. El contenido del taller está en `README.md`.

## Remotos de git

| Remoto     | URL                                                | Uso                              |
|------------|----------------------------------------------------|----------------------------------|
| `origin`   | https://github.com/batura-endika/WorkshopAgent.git | Nuestro fork. Aquí hacemos push. |
| `upstream` | https://github.com/fcnatra/WorkshopAgent.git       | Repo del profesor. Solo lectura. |

Traer novedades del profesor:

```bash
git fetch upstream
git merge upstream/main
```

## Entorno Python

- Python 3.14 del sistema (Homebrew). El taller pide 3.10+.
- Entorno virtual en `.venv/` (ignorado por git) con estos paquetes:
  - `google-genai` — SDK de Gemini, usado por `Refinador.py`, `Refinador_con_bucle_agentico.py` y `Chat.py`.
  - `requests` — usado por `Refinador_agentico_API_REST.py`.
  - `anthropic` — solo para `Chat.claude.py`.

Activar el entorno (carga también la API key, ver abajo):

```bash
cd /Users/endika/Sandbox/agile-norte/WorkshopAgent
source .venv/bin/activate
```

Desactivar y limpiar la clave de la sesión:

```bash
deactivate
unset GEMINI_API_KEY
```

Recrear el entorno desde cero si hiciera falta:

```bash
python3 -m venv .venv
.venv/bin/pip install --upgrade pip
.venv/bin/pip install google-genai requests anthropic
```

## API key de Gemini

- La clave está en el archivo `.env` de la raíz del repo, con el formato `GEMINI_API_KEY=...`.
- `.env` está en `.gitignore` y tiene permisos `600`. Nunca se sube al repo ni se pega en el código.
- El script `.venv/bin/activate` tiene un bloque añadido al final que hace `source` del `.env`,
  así que al activar el entorno la variable queda definida en esa terminal.
- Comprobar que está cargada sin mostrarla entera:

```bash
echo ${GEMINI_API_KEY:0:6}
```

- Si se regenera la clave en https://aistudio.google.com, basta con editar `.env` y volver a activar el entorno.
- Para `Chat.claude.py` haría falta añadir `ANTHROPIC_API_KEY=...` al mismo `.env`.

## Carpeta de trabajo del agente

- `especificaciones/` es la carpeta que vigila el agente. Está en `.gitignore`.
- Contiene copias de los requisitos de ejemplo `R-001.txt`, `R-002.txt` y `R-003.txt`.
- Los originales están en `plantillas especificaciones/`. Para reiniciar la práctica:

```bash
rm -f especificaciones/*.txt
cp "plantillas especificaciones"/R-00*.txt especificaciones/
```

## Ejecutar el agente

```bash
source .venv/bin/activate
python Refinador.py
```

## Problemas conocidos

- **503 UNAVAILABLE "high demand"**: error del lado de Google con el modelo gratuito en horas de carga.
  La clave y el modelo son correctos, solo hay que reintentar al cabo de un rato.
  Una clave inválida daría 400/403 y un modelo inexistente 404.
- Otros errores (certificados, recursión con SSL) están documentados al final del `README.md`.
