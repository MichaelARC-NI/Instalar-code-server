# 🚀 Code-Server en Termux (VS Code en Android)

Guía paso a paso para instalar, configurar y ejecutar **VS Code (Code-Server)** en Android utilizando Termux. Esta guía te permite transformar tu dispositivo móvil o tablet en un entorno de desarrollo web completo accesible localmente o desde cualquier equipo dentro de tu red Wi-Fi.

---

## 📋 Contenido

- [Requisitos Previos](#-requisitos-previos)
- [Guía de Instalación Paso a Paso](#-guía-de-instalación-paso-a-paso)
- [Configuración para Acceso en Red Wi-Fi](#-configuración-para-acceso-en-red-wi-fi)
- [Cómo Conectarse desde otro Dispositivo](#-cómo-conectarse-desde-otro-dispositivo)
- [🛠️ Solución de Errores Frecuentes](#%EF%B8%8F-solución-de-errores-frecuentes)
- [📦 Respaldo y Restauración](#-respaldo-y-restauración)
- [🗑️ Desinstalación y Limpieza](#%EF%B8%8F-desinstalación-y-limpieza)

---

## 🛠️ Requisitos Previos

1. **Termux instalado desde F-Droid o GitHub** (No uses la versión obsoleta de la Play Store).
2. Conexión a Internet activa.
3. Ambos dispositivos (móvil y PC/Tablet) conectados a la **misma red Wi-Fi**.

---

## 🚀 Guía de Instalación Paso a Paso

Sigue estos comandos en orden dentro de tu terminal Termux:

### 1. Actualizar el sistema
Asegúrate de tener los paquetes y dependencias actualizadas:
```bash
pkg update && pkg upgrade -y
```

### 2. Otorgar permisos de almacenamiento
Permite que Termux acceda al almacenamiento de tu Android:
```bash
termux-setup-storage
```
> *(Acepta la ventana emergente de permisos en tu pantalla).*

### 3. Evitar que Android suspenda el servidor (Wake Lock)
Previene que el sistema operativo cierre Termux cuando apagues la pantalla o cambies de aplicación:
```bash
termux-wake-lock
```

### 4. Instalar el repositorio comunitario (TUR)
Habilita el *Termux User Repository* para obtener la build oficial de code-server:
```bash
pkg install tur-repo -y
```

### 5. Instalar Code-Server
Descarga e instala el paquete principal de VS Code Server:
```bash
pkg install code-server -y
```

### 6. Generar la configuración inicial
Ejecuta `code-server` una vez para crear las carpetas de configuración:
```bash
code-server
```
> *Una vez que veas que el servidor inicia y muestra logs en pantalla, presiona `Ctrl + C` para detenerlo.*

---

## 🌐 Configuración para Acceso en Red Wi-Fi

Por defecto, `code-server` solo es accesible desde el mismo dispositivo (`127.0.0.1`). Para acceder desde tu PC o tablet en la misma red Wi-Fi, debes cambiar la dirección de escucha a `0.0.0.0`.

### 1. Modificar la IP de acceso (Permanente)
Ejecuta el siguiente comando para actualizar el archivo de configuración:
```bash
sed -i 's/127.0.0.1:8080/0.0.0.0:8080/g' ~/.config/code-server/config.yaml
```

### 2. Obtener tu contraseña de acceso
Obtén la clave secreta generada por el servidor:
```bash
cat ~/.config/code-server/config.yaml
```
> *Copia el texto que aparece al lado de `password:`.*

### 3. Obtener tu dirección IP local
Encuentra la IP local asignada a tu dispositivo Android:
```bash
ifconfig
```
> *Busca la sección `wlan0` y anota la dirección IP junto a `inet` (Ejemplo: `192.168.1.8`).*

### 4. Iniciar el servidor
Inicia `code-server` normalmente:
```bash
code-server
```

---

## 💻 Cómo Conectarse desde otro Dispositivo

1. Asegúrate de que tu PC/Tablet esté conectada al **mismo Wi-Fi** que el teléfono.
2. Abre cualquier navegador web (Chrome, Edge, Firefox, Safari).
3. Escribe la dirección IP seguida del puerto `:8080` en la barra de navegación:
   ```text
   http://[TU_DIRECCION_IP]:8080
   ```
   *Ejemplo real:* `http://192.168.1.8:8080` *(Usa siempre `http://`, no `https://`)*.
4. Ingresa la contraseña obtenida en el archivo `config.yaml`.

---

## 🛠️ Solución de Errores Frecuentes

### ❌ Error 1: `ENOENT: process.cwd failed with error no such file or directory`
**Causa:** Termux perdió la ruta de la carpeta actual (por ejemplo, si borraste una carpeta donde estabas parado).  
**Solución:** Regresa al directorio principal de usuario ejecutando:
```bash
cd ~
```
Y luego vuelve a ejecutar `code-server`.

---

### ❌ Error 2: `error listen EADDRINUSE: address already in use 0.0.0.0:8080`
**Causa:** El puerto `8080` ya está siendo utilizado por una instancia previa de `code-server` colgada en segundo plano.  
**Solución 1:** Finaliza los procesos activos de Node.js:
```bash
pkill -f node
```
**Solución 2:** Forzar el cierre de cualquier servicio en el puerto `8080`:
```bash
fuser -k 8080/tcp
```
**Solución 3:** Desliza desde el borde izquierdo de Termux para abrir el menú lateral y cierra cualquier otra sesión o pestaña abierta antes de volver a intentar.

---

### ❌ Error 3: El servidor se apaga solo al bloquear el teléfono
**Causa:** Android pone la aplicación Termux en modo de ahorro de energía.  
**Solución:** 
1. Ejecuta `termux-wake-lock`.
2. En la configuración de tu teléfono (Ajustes de Android -> Aplicaciones -> Termux -> Batería), desactiva la optimización de batería y permite el inicio automático / segundo plano.

---

## 📦 Respaldo y Restauración

### Crear una copia de seguridad completa
Guarda todo tu entorno de Termux (incluyendo configuraciones, proyectos y extensiones de VS Code) en tu memoria interna:
```bash
termux-setup-storage && cd /data/data/com.termux/files && tar -zcvf /sdcard/termux-backup.tar.gz usr home && echo -e "\n\033[1;32m[+] Respaldo completado en /sdcard/termux-backup.tar.gz\033[0m"
```

### Restaurar copia de seguridad
Restaura tu entorno previamente guardado:
```bash
termux-setup-storage && cd /data/data/com.termux/files && tar -zxvf /sdcard/termux-backup.tar.gz --recursive-unlink --preserve-permissions && echo -e "\n\033[1;32m[+] Restauración finalizada. Reinicia Termux ejecutando exit.\033[0m"
```

---

## 🗑️ Desinstalación y Limpieza

- **Desinstalar la aplicación:**
  ```bash
  pkg uninstall code-server -y
  ```
- **Borrar todos los datos de configuración y extensiones:**
  ```bash
  rm -rf ~/.config/code-server ~/.local/share/code-server
  ```

---

## 👤 Autor

Desarrollado y mantenido por **[MichaelARC-NI](https://github.com/MichaelARC-NI)**.
- 🌐 [Página Web Oficial del Proyecto](https://michaelarc-ni.github.io/Instalar-code-server/)
- 🐙 [Repositorio en GitHub](https://github.com/MichaelARC-NI/Instalar-code-server.git)
