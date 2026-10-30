# Registro de móviles · El Pino Ltda. (versión en la nube)

Aplicación web para el registro de móviles y conductores. Los datos y las fotos
se guardan en **Supabase**, y se entra con **correo y contraseña**. Todos los
equipos (computador, tablet, celular) ven la misma información, y los cambios
de una persona aparecen en las demás al instante.

Archivos:

| Archivo | Para qué sirve |
|---|---|
| `index.html` | La aplicación. Se publica en GitHub Pages. |
| `supabase_setup.sql` | Crea la tabla, los permisos y la carpeta de fotos en Supabase. Se ejecuta una sola vez. |
| `README.md` | Esta guía. |
| `.gitignore` | Evita subir por error respaldos `.json` o la base `.accdb`. |

---

## Antes de empezar: descarga un respaldo actualizado

Si alguien siguió usando la versión anterior después del primer respaldo,
abre esa versión **antes de cambiar el `index.html`** y descarga un respaldo nuevo
desde **Exportar → Respaldo completo (.json)**. Ese archivo es el que vas a subir a la nube.

> Si se te olvida, no pasa nada grave: los datos de la versión anterior siguen
> guardados en ese navegador. Cuando un editor entre a la versión nueva desde
> ese mismo equipo, la app le preguntará si quiere subirlos a la nube.

---

## Paso 1. Crear el proyecto en Supabase

1. Entra a **supabase.com** y crea una cuenta. Puedes entrar con tu cuenta de GitHub.
2. Pulsa **New project**:
   - **Name:** `registro-el-pino`
   - **Database password:** pulsa *Generate* y guárdala en un lugar seguro. La app no la usa, pero Supabase te la puede pedir más adelante.
   - **Region:** **South America (São Paulo)**, la más cercana a Chile.
3. Espera uno o dos minutos a que el proyecto quede listo.

## Paso 2. Crear la tabla, los permisos y la carpeta de fotos

1. En el menú izquierdo, entra a **SQL Editor** → **New query**.
2. Abre `supabase_setup.sql`, copia **todo** el contenido, pégalo y pulsa **Run**.
   Debe terminar con *Success*. Si lo ejecutas dos veces no pasa nada.

Esto deja configurado lo siguiente:
- Sin sesión iniciada no se puede leer ni modificar nada.
- Solo entran los correos que tú autorices, aunque alguien logre crearse una cuenta.
- Hay dos permisos: **editor** (crea, modifica, elimina e importa) y **lector** (solo ve e imprime).
- Las fotos van a una carpeta **privada**. Se muestran con enlaces temporales que vencen en una hora.
- La fecha y el autor de cada cambio los registra la base de datos.

## Paso 3. Cerrar el registro público

En **Authentication → Sign In / Providers**, desactiva **Allow new users to sign up**
y guarda. Así solo tú puedes crear cuentas desde el panel.

## Paso 4. Crear tu usuario (y los demás)

Para **cada persona** que va a usar el registro hay que hacer dos cosas.

**a) Crear la cuenta:** en **Authentication → Users** → **Add user** → **Create new user**.
Escribe su correo y una contraseña inicial, y marca **Auto Confirm User**.

**b) Darle permiso:** en **Table Editor** → tabla **usuarios_permitidos** → **Insert row**:
- `email`: el mismo correo, **en minúsculas**
- `rol`: `editor` o `lector`
- `nombre`: cómo quieres que aparezca en la app, por ejemplo `Tuto`

Sin este segundo paso, la persona puede iniciar sesión pero ve el mensaje
"Tu cuenta no tiene acceso".

> Para quitarle el acceso a alguien, borra su fila en `usuarios_permitidos`.
> Si además quieres eliminar la cuenta, hazlo en **Authentication → Users**.

## Paso 5. Permitir la recuperación de contraseña

En **Authentication → URL Configuration**:
- **Site URL:** la dirección de tu app, por ejemplo `https://TU-USUARIO.github.io/registro-el-pino/`
- **Redirect URLs:** agrega esa misma dirección.

Así funciona el enlace "¿Olvidaste tu contraseña?". El plan gratuito envía pocos
correos por hora; si alguien no recibe el suyo, puedes cambiarle la contraseña tú
desde **Authentication → Users**.

## Paso 6. Conectar la app con tu proyecto

1. En Supabase, pulsa **Connect** (arriba) o entra a **Project Settings → API Keys**. Copia:
   - la **Project URL**, del tipo `https://abcdxyz.supabase.co`
   - la clave **pública**: *Publishable key* (`sb_publishable_…`) o, en la sección *Legacy*, la **anon key** (`eyJ…`)
2. Abre `index.html` con un editor de texto (Bloc de notas sirve) y busca estas líneas, casi al comienzo del código:
   ```js
   const SUPABASE_URL = '';
   const SUPABASE_ANON_KEY = '';
   ```
3. Pega tus datos entre las comillas:
   ```js
   const SUPABASE_URL = 'https://abcdxyz.supabase.co';
   const SUPABASE_ANON_KEY = 'sb_publishable_xxxxxxxx';
   ```
4. Guarda el archivo.

> ⚠️ **Nunca** pegues la clave **secret** (`sb_secret_…`) ni la **service_role**.
> Esas claves saltan todos los permisos. La app las rechaza si las detecta.
> La clave pública sí puede ir en la página y en GitHub: sin usuario autorizado no da acceso a nada.

## Paso 7. Publicar en GitHub

En tu repositorio: **Add file → Upload files**, arrastra el nuevo `index.html`,
`README.md` y `.gitignore`, y pulsa **Commit changes**. En uno o dos minutos la app
se actualiza en la misma dirección de siempre. Recárgala con `Ctrl + F5`.

No subas `supabase_setup.sql` si no quieres; no tiene datos secretos, pero tampoco hace falta en GitHub.
**Nunca** subas respaldos `.json` ni el `.accdb`.

## Paso 8. Subir los datos a la nube

1. Abre la app y entra con tu correo y contraseña.
2. Pulsa **Respaldo ▾ → Importar respaldo (.json)** y elige el respaldo del paso inicial.
3. Espera a que termine la barra de progreso. Quedan los móviles con todos sus datos y fotos.

Listo. Desde ahora todos trabajan sobre los mismos datos.

---

## Uso diario

- **Entrar:** correo y contraseña. La sesión queda abierta en ese equipo hasta que pulses 👤 → **Cerrar sesión**.
- **Cambiar la contraseña:** 👤 → **Cambiar mi contraseña**.
- **Si dos personas editan el mismo móvil:** cuando la segunda guarda, la app avisa que hubo un cambio entretanto y deja elegir entre ver la versión de la otra persona o guardar la propia encima. Nada se pierde sin aviso.
- **Respaldos:** aunque los datos estén en la nube, descarga de vez en cuando
  **Respaldo ▾ → Descargar respaldo completo** y guárdalo en un lugar seguro, como un pendrive o un Drive privado.
- **Imprimir:** igual que antes, una hoja oficio por ficha con datos y fotos. Los lectores también pueden imprimir.

## Límites del plan gratuito de Supabase

- 500 MB de base de datos y 1 GB para fotos. Un móvil con sus 9 fotos ocupa unos 2 MB, así que caben varios cientos.
- **Si el proyecto pasa 7 días sin uso, Supabase lo pausa.** Los datos no se borran: entra a supabase.com
  y pulsa **Restore project**. Si el registro se usa todas las semanas, esto no ocurre.
- Si algún día necesitan más espacio o que nunca se pause, el plan Pro cuesta US$25 al mes.

## Problemas frecuentes

| Mensaje | Qué hacer |
|---|---|
| "Falta conectar con Supabase" | Falta el paso 6: los datos de conexión no están en `index.html`. |
| "Correo o contraseña incorrectos" | Revisa el correo. Si la persona no recuerda la contraseña, cámbiala en Authentication → Users. |
| "Este usuario aún no está confirmado" | En Authentication → Users, abre el usuario y confírmalo, o vuelve a crearlo con *Auto Confirm User*. |
| "Tu cuenta no tiene acceso" | Falta su fila en `usuarios_permitidos` (paso 4b), o el correo no está en minúsculas. |
| "No se pudo revisar tu acceso" | No se ejecutó `supabase_setup.sql` (paso 2), o el proyecto está pausado. |
| "No hay conexión con el servidor" | Revisa internet, o entra a supabase.com por si el proyecto está pausado. |
