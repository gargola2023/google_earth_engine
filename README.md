# Comparador multitemporal GEE (Swipe)

App web estática para comparar dos imágenes satelitales con un deslizador (swipe), usando **Google Earth Engine** directamente desde el navegador.

- Dibuja un polígono o rectángulo como área de interés, o carga un GeoJSON.
- Cada lado del swipe tiene su propio **sensor, rango de fechas, método y visualización**.
- Sensores: Landsat 5 TM, Landsat 7 ETM+, Landsat 8 OLI, Landsat 9 OLI-2 y Sentinel-2 L2A.
- Métodos: compuesto por mediana con máscara de nubes, o la escena menos nubosa del período.
- Visualizaciones: color real, falso color SWIR1/NIR/Rojo, infrarrojo color, NDWI y NDVI.

Tecnologías: Leaflet, Leaflet.draw y la API JavaScript de Earth Engine. No requiere servidor.

---

## Cómo funciona la autenticación

La app no guarda ninguna credencial. Cada visitante inicia sesión con **su propia cuenta Google** y usa **un proyecto de Google Cloud registrado en Earth Engine**. Por eso cada persona que use la app necesita acceso a Earth Engine.

---

## Configuración (una sola vez)

### 1. Proyecto de Google Cloud con Earth Engine
1. Entra a <https://console.cloud.google.com> y crea o elige un proyecto.
2. Regístralo para Earth Engine en <https://code.earthengine.google.com/register> (uso no comercial o comercial, según corresponda).
3. En **APIs y servicios → Biblioteca**, habilita la **Google Earth Engine API**.

### 2. Pantalla de consentimiento OAuth
1. **APIs y servicios → Pantalla de consentimiento de OAuth**.
2. Tipo de usuario **Externo**, estado **En prueba**.
3. En **Usuarios de prueba**, agrega los correos de quienes usarán la app (incluido el tuyo).

> Mientras la app esté "En prueba", solo los usuarios de prueba pueden iniciar sesión (hasta 100). Para abrirla a cualquiera, Google exige verificar la app porque el permiso de Earth Engine es sensible.

### 3. Client ID OAuth
1. **APIs y servicios → Credenciales → Crear credenciales → ID de cliente de OAuth**.
2. Tipo: **Aplicación web**.
3. En **Orígenes de JavaScript autorizados** agrega:
   - `https://TU_USUARIO.github.io`
   - `http://localhost:8000` (para pruebas locales)
4. Copia el Client ID (termina en `.apps.googleusercontent.com`).

### 4. Pon el Client ID en la app
En `index.html`, al inicio del script:

```js
const CONFIG = {
  CLIENT_ID: 'xxxxxxxx.apps.googleusercontent.com',
  PROJECT: 'tu-proyecto-gee'   // opcional: proyecto por defecto
};
```

El Client ID **no es secreto**; puede ir en un repositorio público. Si lo dejas vacío, la app lo pide en el panel.

---

## Publicar en GitHub Pages

1. Crea un repositorio y sube `index.html` (y este README).
2. **Settings → Pages → Source: Deploy from a branch**, rama `main`, carpeta `/ (root)`.
3. La app quedará en `https://TU_USUARIO.github.io/NOMBRE_REPO/`.

## Probar en local

```bash
python -m http.server 8000
```

Abre <http://localhost:8000>. No funciona abriendo el archivo con doble clic (`file://`), porque OAuth exige un origen http(s).

---

## Uso

1. Escribe el ID de tu proyecto Earth Engine y pulsa **Conectar**. La primera vez aparece **Iniciar sesión con Google**.
2. Dibuja el área de interés con las herramientas del mapa (por defecto viene la Laguna de Aculeo).
3. Configura cada lado: sensor, fechas, método, nubosidad máxima y visualización.
4. Pulsa **Generar comparación** y arrastra la manija central.

### Notas
- La escena menos nubosa no lleva máscara de nubes, para no dejar huecos; el compuesto por mediana sí.
- Landsat 7 tiene franjas sin datos (SLC-off) desde junio de 2003.
- Si un lado no encuentra escenas, revisa el rango de fechas o sube el porcentaje de nubes permitido.
- Las teselas de Earth Engine son temporales: si la página queda abierta muchas horas, vuelve a pulsar **Generar comparación**.

---

Autor: Daniel González Baez — Ingeniero de Ejecución en Geomensura | Candidato a Magíster en Teledetección
