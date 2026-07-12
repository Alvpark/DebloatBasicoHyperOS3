# DEBLOAT · ADB MIUI / HYPEROS

## Guía de Optimización y Desinstalación de Paquetes mediante ADB

Procedimiento para reducir aplicaciones preinstaladas, servicios opcionales y componentes publicitarios en dispositivos Xiaomi y Poco con MIUI o HyperOS, utilizando las herramientas de depuración de Android.

---

## ⚠️ Consideraciones Importantes

### Sobre el método utilizado

Los comandos de esta guía utilizan:

```
pm uninstall -k --user 0
```

Este método **no elimina físicamente** los archivos del sistema ni modifica la partición del sistema operativo. Las aplicaciones se desinstalan únicamente para el usuario actual y pueden restaurarse posteriormente mediante ADB. No requiere acceso root.

- No todos los paquetes están presentes en todas las versiones de MIUI o HyperOS.
- Algunos comandos pueden devolver errores sin que ello represente un problema.

### ⚠️ Advertencia de riesgos

Aunque los paquetes incluidos en esta guía han sido seleccionados por su bajo impacto en el funcionamiento general del sistema, la desinstalación de componentes del fabricante puede ocasionar comportamientos inesperados en determinadas versiones de firmware. **Se recomienda realizar una copia de seguridad antes de comenzar.**

### Comportamiento de las desinstalaciones

Las aplicaciones eliminadas mediante `pm uninstall -k --user 0` pueden reaparecer después de:

- Restablecimiento de fábrica.
- Actualizaciones mayores del sistema (ej. MIUI → HyperOS).
- Creación de un nuevo perfil de usuario.

Esto no indica un error; es el comportamiento esperado del método.

---

## 🛠️ Requisitos Previos y Configuración Inicial

Antes de ejecutar los comandos, es necesario preparar el entorno en la computadora y habilitar el acceso en el teléfono.

### Paso 1 – Configurar las Platform Tools en la PC

Para que la computadora pueda comunicarse con el teléfono, se necesitan las herramientas oficiales de depuración:

1. Descargar el archivo comprimido de **SDK Platform-Tools** para Windows desde el [sitio web de Android Developers](https://developer.android.com/studio/releases/platform-tools).
2. Extraer el contenido del archivo `.zip` en una ruta accesible (por ejemplo: `C:\Users\TuUsuario\Desktop\Programas\platform-tools`).
3. Abrir la carpeta `platform-tools`. Hacer clic en la barra de direcciones de Windows, escribir `cmd` y presionar `Enter`. Se abrirá la terminal de comandos en esa ubicación.

### Paso 2 – Activar la Depuración USB en el Celular

Este paso habilita al teléfono para aceptar comandos ADB desde la computadora:

1. En el dispositivo, ingresar a **Ajustes → Sobre el teléfono** y presionar 7 veces sobre **Versión de OS** (o versión de MIUI/HyperOS) hasta que aparezca el mensaje: *«Ahora eres desarrollador»*.
2. Volver al menú principal de Ajustes y navegar hasta **Ajustes adicionales → Opciones de desarrollador**.
3. Activar el interruptor de **Depuración USB (USB Debugging)**.
4. En dispositivos Xiaomi/Poco, activar también **Depuración USB (Ajustes de seguridad)**. El sistema mostrará advertencias; aceptar para confirmar.

### Paso 3 – Verificación de Conexión

1. Conectar el teléfono a la computadora con un cable USB.
2. En la terminal, ejecutar:

   ```
   C:\platform-tools>adb devices
   ```

3. En el teléfono, aparecerá un diálogo solicitando permitir la depuración USB. Seleccionar **"Permitir siempre desde esta computadora"** y presionar Aceptar.
4. Ejecutar nuevamente `adb devices`. Si la conexión es correcta, se mostrará el número de serie del dispositivo seguido de `device`.

Para acceder al entorno del teléfono, ejecutar:

```
C:\platform-tools>adb shell
```

El indicador cambiará al nombre en clave del procesador (ej: `klee:/ $`), confirmando que se está operando dentro del sistema del dispositivo.

---

## Gestión de Aplicaciones del Sistema mediante ADB

**Dispositivo de Referencia**
- **Modelo:** Xiaomi Poco X8 Pro (2511FPC34G)
- **Firmware:** HyperOS v3.0.303.0 (Placa: `klee`)
- **Objetivo:** Reducir aplicaciones preinstaladas, componentes publicitarios y servicios de telemetría sin modificar la partición del sistema ni requerir acceso root.

---

### 1. Ajuste de Animaciones del Sistema

Los siguientes comandos reducen la escala de las animaciones del sistema a 0.5x, lo que puede disminuir el tiempo de renderizado de las transiciones:

```bash
klee:/ $ settings put global window_animation_scale 0.5
klee:/ $ settings put global transition_animation_scale 0.5
klee:/ $ settings put global animator_duration_scale 0.5
```

✔ Animaciones configuradas a 0.5x

---

### 2. Componentes de Publicidad y Telemetría

Paquetes utilizados por Xiaomi para mostrar contenido promocional y recopilar datos de uso:

```bash
klee:/ $ pm uninstall -k --user 0 com.miui.msa.global
klee:/ $ pm uninstall -k --user 0 com.miui.analytics
klee:/ $ pm uninstall -k --user 0 com.xiaomi.mipicks
```

- **`msa.global`** → Servicio utilizado para mostrar recomendaciones y contenido promocional.
- **`analytics`** → Módulo de recopilación de datos de uso y telemetría.
- **`mipicks`** → Tienda de aplicaciones alternativa que puede mostrar notificaciones y recomendaciones.

---

### 3. Aplicaciones Multimedia Opcionales

Aplicaciones multimedia preinstaladas por el fabricante:

```bash
klee:/ $ pm uninstall -k --user 0 com.miui.videoplayer
klee:/ $ pm uninstall -k --user 0 com.miui.player
klee:/ $ pm uninstall -k --user 0 com.miui.weather.global
```

- **`videoplayer`** → Reproductor Mi Video.
- **`player`** → Reproductor Mi Música.
- **`weather.global`** → Aplicación de clima (puede no estar presente en todos los firmwares).

---

### 4. Servicios de Facebook Preinstalados

Componentes de Facebook que pueden ejecutarse en segundo plano y consumir recursos del sistema, incluso si las aplicaciones principales de Facebook no están instaladas:

```bash
klee:/ $ pm uninstall -k --user 0 com.facebook.appmanager
klee:/ $ pm uninstall -k --user 0 com.facebook.services
klee:/ $ pm uninstall -k --user 0 com.facebook.system
```

✔ Puede reducir la cantidad de procesos en segundo plano.

---

### 5. Servicios de Soporte Opcionales

Componentes del sistema que no son esenciales para el funcionamiento diario:

```bash
klee:/ $ pm uninstall -k --user 0 com.miui.extraphoto
klee:/ $ pm uninstall -k --user 0 com.miui.bugreport
klee:/ $ pm uninstall -k --user 0 com.miui.miservice
klee:/ $ pm uninstall -k --user 0 com.miui.hybrid
klee:/ $ pm uninstall -k --user 0 com.android.browser
```

- **`extraphoto`** → Procesamiento adicional de imágenes por IA en la galería.
- **`bugreport`** → Herramienta de reporte de errores.
- **`miservice`** → Servicio de feedback y soporte de Xiaomi.
- **`hybrid`** → Navegador híbrido (puede no estar presente en todos los firmwares).
- **`android.browser`** → Navegador web (puede no estar presente en todos los firmwares).

---

### 6. Desactivación de Componentes de Búsqueda

Para deshabilitar la barra de búsqueda en el cajón de aplicaciones y la pantalla lateral izquierda:

- **Buscador del cajón de aplicaciones:**

```bash
klee:/ $ pm uninstall -k --user 0 com.mi.appfinder
```

- **Pantalla lateral izquierda (App Vault):**

```bash
klee:/ $ pm uninstall -k --user 0 com.mi.globalminusscreen
```

---

## Evaluación de Riesgos por Paquete

Los siguientes niveles de riesgo se basan en la experiencia de usuarios y en la función esperada de cada componente. El impacto real puede variar según la versión de firmware y la región.

| Paquete | Riesgo | Descripción |
|---------|--------|-------------|
| `com.miui.msa.global` | Bajo | Servicio de publicidad; su ausencia no afecta funciones básicas. |
| `com.miui.analytics` | Bajo | Telemetría; su ausencia no afecta el rendimiento general. |
| `com.xiaomi.mipicks` | Bajo | Tienda alternativa; su ausencia no afecta la tienda principal. |
| `com.miui.videoplayer` | Bajo | Reproductor de video; puede reemplazarse por otra app. |
| `com.miui.player` | Bajo | Reproductor de música; puede reemplazarse. |
| `com.facebook.*` | Bajo | Servicios de Facebook; no afectan funciones del sistema. |
| `com.miui.extraphoto` | Medio | Puede desactivar algunas funciones de IA en la galería. |
| `com.mi.globalminusscreen` | Medio | Puede eliminar widgets y acceso rápido en la pantalla lateral. |
| `com.android.browser` | Medio | Puede afectar enlaces abiertos por defecto; se puede reemplazar. |
| `com.miui.bugreport` | Bajo | Herramienta de reporte de errores; su ausencia no es crítica. |
| `com.miui.miservice` | Bajo | Servicio de soporte; no afecta funciones diarias. |
| `com.mi.appfinder` | Medio | Elimina la búsqueda en el cajón de apps; puede afectar la experiencia de algunos usuarios. |

---

## Mecanismo de Restauración

Restaurar un paquete desinstalado sin necesidad de reiniciar el teléfono de fábrica:

```bash
cmd package install-existing NOMBRE_DE_LA_APP
```

**Ejemplo:**

```bash
cmd package install-existing com.miui.msa.global
```

---

## Solución de Problemas Comunes

### Mensaje "Failure [not installed for 0]"

Es normal que algunos paquetes devuelvan este mensaje. Puede deberse a:

- El paquete no está presente en el firmware regional.
- Fue desinstalado previamente.
- No está activo en el perfil de usuario por defecto.

✔ El proceso continúa sin afectar el resto de la desinstalación.

---

### No aparece el diálogo de depuración USB

- Verificar que ambas opciones de depuración USB estén activadas.
- Probar con otro cable USB (preferentemente original).
- Reiniciar el servicio ADB:
  ```bash
  adb kill-server
  adb start-server
  ```

---

### `adb devices` no muestra el dispositivo

- Verificar la conexión física del cable.
- Probar con otro puerto USB en la computadora.
- En Opciones de desarrollador, desactivar y reactivar Depuración USB.
- Reiniciar el teléfono y la computadora.

---

### Restaurar todas las aplicaciones desinstaladas

- El método más sencillo es realizar un **restablecimiento de fábrica** desde los ajustes del dispositivo.
- Alternativamente, se pueden restaurar paquetes individuales con el comando mencionado en la sección de [Mecanismo de Restauración](#mecanismo-de-restauración).

---

**Guía de Debloat · ADB MIUI / HyperOS**  
⚠️ Bajo responsabilidad del usuario · Realizar copia de seguridad.
