<div align="center">

# 🌐 Hotspot Salutta — Portal Cautivo MikroTik

**Plantilla moderna, ultra-ligera y responsiva para portales cautivos en routers MikroTik RouterOS v6.x y v7.x.**  
Diseñada con estética *Glassmorphism*, integración corporativa para **Salutta**, autenticación segura MD5 CHAP y acceso para invitados con 1 solo clic.

[![MikroTik RouterOS](https://img.shields.io/badge/MikroTik-RouterOS%20v6%20%7C%20v7-006699?style=for-the-badge&logo=mikrotik&logoColor=white)](https://mikrotik.com)
[![HTML5 & CSS3](https://img.shields.io/badge/UI-HTML5%20%7C%20CSS3%20Glassmorphism-E34F26?style=for-the-badge&logo=html5&logoColor=white)](https://developer.mozilla.org/)
[![Seguridad CHAP](https://img.shields.io/badge/Seguridad-MD5%20CHAP%20%2F%20PAP-059669?style=for-the-badge&logo=shield&logoColor=white)](#-arquitectura-y-flujo-de-autenticación)
[![Responsive](https://img.shields.io/badge/Diseño-100%25%20Responsive-3A96A9?style=for-the-badge&logo=visualstudiocode&logoColor=white)](#-vista-previa)
[![Estado](https://img.shields.io/badge/Estado-Producción%20Ready-brightgreen?style=for-the-badge)](#)

[Vista Previa](#-vista-previa) • [Características](#-características-principales) • [Flujo de Autenticación](#-arquitectura-y-flujo-de-autenticación) • [Instalación](#-instalación-y-despliegue) • [Terminal CLI](#opción-b-despliegue-por-terminal-cli-de-mikrotik) • [Personalización](#-guía-de-personalización) • [Troubleshooting](#-diagnóstico-y-solución-de-problemas)

---

</div>

## 📸 Vista Previa

<div align="center">
  <table>
    <tr>
      <td align="center" style="padding: 20px;">
        <img src="salutta.png" alt="Portal Cautivo Salutta - Pantalla de Login Móvil" width="380" style="border-radius: 20px; box-shadow: 0 15px 35px rgba(0,0,0,0.25);" />
        <br />
        <em><b>Figura 1:</b> Pantalla principal de autenticación en dispositivos móviles con efecto translúcido y acceso rápido.</em>
      </td>
    </tr>
  </table>
</div>

---

## 📑 Tabla de Contenidos

1. [Características Principales](#-características-principales)
2. [Arquitectura y Flujo de Autenticación](#-arquitectura-y-flujo-de-autenticación)
3. [Estructura del Proyecto y Variables RouterOS](#-estructura-del-proyecto-y-variables-routeros)
4. [Instalación y Despliegue](#-instalación-y-despliegue)
   - [Opción A: Despliegue con Winbox (GUI)](#opción-a-despliegue-con-winbox-gui)
   - [Opción B: Despliegue por Terminal CLI de MikroTik](#opción-b-despliegue-por-terminal-cli-de-mikrotik)
   - [Opción C: Carga vía FTP / SFTP](#opción-c-carga-vía-ftp--sftp)
5. [Configuración de Walled Garden (Bypass de Redes)](#-configuración-de-walled-garden)
6. [Guía de Personalización](#-guía-de-personalización)
   - [Paleta de Colores y Tokens CSS](#paleta-de-colores-y-tokens-css)
   - [Sustitución de Logotipo y Fondo](#sustitución-de-logotipo-y-fondo)
   - [Redirección Post-Login](#personalizar-redirección-post-login)
7. [Diagnóstico y Solución de Problemas (Troubleshooting)](#-diagnóstico-y-solución-de-problemas)
8. [Buenas Prácticas de Seguridad](#-buenas-prácticas-de-seguridad)
9. [Licencia y Créditos](#-licencia-y-créditos)

---

## ✨ Características Principales

- 💎 **Estética Glassmorphism Premium**: Filtros de desenfoque translúcido (`backdrop-filter: blur`), bordes sutiles y profundidad con sombras multinivel adaptadas a la paleta médica/salud corporativa (`#3A96A9`).
- ⚡ **Doble Modalidad de Acceso**:
  - **Credenciales Administradas**: Login mediante usuario y clave con soporte para base de datos local de RouterOS, RADIUS o User Manager.
  - **Acceso Clientes con 1 Clic (Trial)**: Conexión inmediata para visitantes temporales mediante el mecanismo `Trial` nativo de MikroTik (`T-$(mac-esc)`).
- 🛡️ **Autenticación Criptográfica Robusta**:
  - Cifrado en el navegador con algoritmo **MD5 CHAP** (`md5.js`), garantizando que la contraseña nunca viaje en texto plano sobre redes Wi-Fi abiertas.
- 📊 **Panel de Monitoreo de Sesión en Tiempo Real (`status.html`)**:
  - Muestra dirección IP asignada, volumen de datos transferidos (subida/bajada formateado en bytes legibles), tiempo total conectado, tiempo restante de sesión y botón de desconexión inmediata.
- 🔄 **Flujo de Redirección Inteligente (`alogin.html`)**:
  - Animación de carga moderna y redirección fluida tras la autenticación hacia las redes sociales oficiales ([@saluttave en Instagram](https://www.instagram.com/saluttave/?hl=es)).
- 📱 **100% Responsivo y Mobile-First**:
  - Total compatibilidad con los asistentes de portal cautivo (Captive Network Assistant - CNA) de **iOS (Apple)**, **Android**, **macOS** y **Windows**.
- 🚀 **Cero Dependencias Críticas Externas**:
  - La tipografía `Osande` y los estilos están integrados localmente; los iconos SVG están vectorizados inline para garantizar renderizado instantáneo incluso antes de conceder salida a Internet.

---

## 📐 Arquitectura y Flujo de Autenticación

El siguiente diagrama ilustra el ciclo de vida de la conexión desde que el usuario ingresa a la red Wi-Fi hasta la navegación abierta:

```mermaid
flowchart TD
    A([Dispositivo se conecta a la Wi-Fi]) --> B[RouterOS detecta tráfico no autenticado]
    B --> C[Redirección HTTP a login.html]
    
    C --> D{¿Método de Acceso?}
    
    D -- "Botón: ACCESO CLIENTES" --> E[Solicitud Trial: link-login-only?username=T-MAC]
    D -- "Formulario Usuario / Clave" --> F[Encriptación MD5 CHAP en cliente: md5.js]
    
    F --> G[Envío POST de credenciales a RouterOS]
    E --> H[Validación de perfil en MikroTik]
    G --> H
    
    H -- "Credenciales Inválidas / Error" --> I[Recarga login.html con mensaje $(error)]
    
    H -- "Autenticación Exitosa" --> J[Redirección a alogin.html]
    J --> K[Apertura de ventana de estado status.html]
    J --> L[Redirección final a Instagram @saluttave]
    
    K --> M[Monitoreo en vivo: IP, Tráfico, Uptime]
    M --> N([Navegación Autorizada a Internet])
```

---

## 📁 Estructura del Proyecto y Variables RouterOS

### Árbol de Archivos

```text
hostpot-salutta/
├── login.html               # Interfaz principal de autenticación (Formulario + Trial)
├── status.html              # Panel interactivo de estadísticas de consumo y sesión
├── logout.html              # Pantalla de confirmación de fin de sesión y reconexión
├── alogin.html              # Pantalla de transición post-login con loader animado
├── error.html               # Plantilla de captura para contingencias del sistema
├── errors.txt               # Diccionario de traducción de errores de RouterOS
├── radvert.html             # Soporte para publicidad / banners programados
├── redirect.html            # Manejador auxiliar de saltos de URL
├── rlogin.html              # Punto de enlace para redirecciones WISP / XML
├── login.css                # Sistema central de diseño y tokens responsive
├── md5.js                   # Módulo de hash criptográfico CHAP de MikroTik
├── log.jpg                  # Isotipo oficial de Salutta (circular)
├── backsalut.jpg            # Fondo visual corporativo con textura médica
├── Osande - Medium DEMO.ttf # Fuente tipográfica corporativa
├── salutta.png              # Captura de pantalla de la interfaz para documentación
└── README.md                # Documentación oficial del repositorio
```

### Principales Variables de Sustitución de RouterOS Empleadas

| Variable | Archivo | Función |
|---|---|---|
| `$(link-login-only)` | `login.html` | URL interna del router que procesa la autenticación POST. |
| `$(link-orig)` | `login.html`, `alogin.html` | URL original que solicitó el cliente antes de ser interceptado. |
| `$(chap-id)` / `$(chap-challenge)` | `login.html` | Desafío criptográfico dinámico entregado por MikroTik para CHAP. |
| `$(trial)` | `login.html` | Bandera booleana (`yes`/`no`) que habilita el botón de acceso libre. |
| `$(mac-esc)` | `login.html` | Dirección MAC del cliente escapada para URL (`T-XX:XX:XX:XX:XX:XX`). |
| `$(username)` | `login.html`, `status.html` | Nombre del usuario autenticado en la sesión activa. |
| `$(ip)` | `status.html`, `logout.html` | Dirección IP arrendada al cliente en la subred Hotspot. |
| `$(bytes-in-nice)` / `$(bytes-out-nice)` | `status.html`, `logout.html` | Tráfico transferido formateado automáticamente (KB, MB, GB). |
| `$(uptime)` | `status.html`, `logout.html` | Tiempo transcurrido de la conexión actual. |
| `$(session-time-left)` | `status.html` | Cuenta regresiva del tiempo límite asignado al perfil. |
| `$(error)` | `login.html`, `error.html` | Mensaje descriptivo traducido de error según `errors.txt`. |

---

## 🚀 Instalación y Despliegue

### Opción A: Despliegue con Winbox (GUI)

1. **Conexión al Router**:
   - Inicia sesión en tu router MikroTik mediante **Winbox**.
2. **Cargar los Archivos**:
   - En el menú principal, haz clic en **Files**.
   - Crea un nuevo directorio o arrastra la carpeta completa del proyecto.
   - *Ruta recomendada en equipos con memoria flash*: `flash/hotspot-salutta` (o simplemente `hotspot-salutta` en almacenamiento raíz).
3. **Asignar la Plantilla al Hotspot**:
   - Dirígete a **IP** > **Hotspot** > Pestaña **Server Profiles**.
   - Abre tu perfil de servidor activo (generalmente `hsprof1` o el configurado en tu interfaz Wi-Fi).
   - En la pestaña **General**, establece el campo **HTML Directory** apuntando a tu carpeta:
     ```text
     hotspot-salutta
     ```
     *(O `flash/hotspot-salutta` según corresponda)*.
4. **Habilitar Métodos de Autenticación**:
   - En la pestaña **Login** del mismo perfil, activa las siguientes casillas:
     - [x] **HTTP PAP**
     - [x] **HTTP CHAP**
     - [x] **Trial** *(Requerido si deseas usar el botón "ACCESO CLIENTES")*
     - [ ] **Cookie** *(Opcional: Desmarca si requieres que los usuarios vean el portal cada vez que expira su sesión)*.
   - Si habilitas **Trial**, define el **Trial Uptime Limit** (ej. `01:00:00` para 1 hora) y el **Trial Uptime Reset** (ej. `1d 00:00:00` para renovar cada 24h).
5. **Aplicar Cambios**:
   - Presiona **Apply** y luego **OK**.

---

### Opción B: Despliegue por Terminal CLI de MikroTik

Puedes configurar el perfil de Hotspot ejecutando los siguientes comandos en la **Terminal de MikroTik RouterOS**:

```routeros
# 1. Definir el directorio HTML de la plantilla en el perfil del Hotspot
/ip hotspot profile
set [ find default=yes ] html-directory=hotspot-salutta

# Si utilizas un perfil específico (ejemplo: hsprof1):
# set [ find name="hsprof1" ] html-directory=hotspot-salutta

# 2. Configurar métodos de login (CHAP, PAP y Trial)
/ip hotspot profile
set [ find html-directory="hotspot-salutta" ] \
    login-by=http-chap,http-pap,trial \
    trial-uptime-limit=1h \
    trial-uptime-reset=1d \
    trial-user-profile=default

# 3. Validar la configuración resultante
/ip hotspot profile print detail
```

---

### Opción C: Carga vía FTP / SFTP

Si prefieres automatizar el despliegue mediante un cliente FTP (como FileZilla, WinSCP o scripts `lftp`):

```bash
# Conectar al router MikroTik
ftp 192.168.88.1

# Introducir credenciales de administrador de MikroTik
# Crear carpeta y transferir archivos:
mkdir hotspot-salutta
cd hotspot-salutta
mput *.html *.css *.js *.jpg *.png *.ttf errors.txt
bye
```

---

## 🌐 Configuración de Walled Garden

Para asegurar que los clientes puedan ser redirigidos correctamente a **Instagram** (`https://www.instagram.com/saluttave/?hl=es`) o cargar recursos externos antes de completar el inicio de sesión, se recomienda agregar las siguientes reglas de Walled Garden:

### Comandos CLI para Walled Garden:

```routeros
# Permitir navegación previa hacia el perfil de Instagram y sus dominios CDN
/ip hotspot walled-garden
add dst-host=*.instagram.com comment="Acceso a Instagram Salutta"
add dst-host=*.cdninstagram.com comment="CDN Multimedia de Instagram"
add dst-host=*.facebook.com comment="Infraestructura Meta"
add dst-host=*.fbcdn.net comment="CDN Meta"

# Si requieres permitir FontAwesome u otros CDNs externos:
add dst-host=cdnjs.cloudflare.com comment="Cloudflare CDN FontAwesome"
```

---

## 🎨 Guía de Personalización

### Paleta de Colores y Tokens CSS

La identidad visual se gestiona centralizadamente mediante variables CSS en [login.css](file:///c:/Users/Pagina-web1/Videos/hostpot-salutta-/login.css):

```css
:root {
    --primary: #3A96A9;          /* Color de marca principal (Botones, acentos) */
    --primary-hover: #2D7A8A;    /* Tono oscuro para hover y botones activos */
    --primary-light: #EBF5F7;    /* Fondos suaves y realces de campos */
    --color-dark: #111827;       /* Fondo del botón "ACCESO CLIENTES" */
    --color-error: #EF4444;      /* Notificaciones de error y campos inválidos */
    --bg-card: rgba(255, 255, 255, 0.94); /* Tarjeta Glassmorphism */
    --radius-lg: 24px;           /* Curvatura de bordes de la tarjeta */
    --radius-sm: 12px;           /* Curvatura de campos de entrada y botones */
}
```

### Sustitución de Logotipo y Fondo

- **Logo**: Reemplaza el archivo `log.jpg` con tu logotipo en formato cuadrado (resolución recomendada: `300x300 px`, PNG o JPG).
- **Fondo de Pantalla**: Reemplaza el archivo `backsalut.jpg` con una imagen optimizada para web (resolución recomendada: `1920x1080 px`, peso menor a 150 KB para carga instantánea).

### Personalizar Redirección Post-Login

Para cambiar el destino de redirección una vez autenticado, edita la línea 6 y la línea 66 de [alogin.html](file:///c:/Users/Pagina-web1/Videos/hostpot-salutta-/alogin.html):

```html
<!-- Línea 6: Meta Refresh de respaldo -->
<meta http-equiv="refresh" content="2; url=https://tu-sitio-web.com">

<!-- Línea 66: Redirección inmediata por JavaScript -->
location.href = 'https://tu-sitio-web.com';
```

---

## 🛠️ Diagnóstico y Solución de Problemas

### 1. El portal cautivo no se abre automáticamente en iPhone / Android
- **Causa**: MikroTik no intercepta peticiones HTTP si el dispositivo prueba directamente contra HTTPS con HSTS, o el DNS del router no está capturando las consultas de detección de portal (`captive.apple.com`, `connectivitycheck.gstatic.com`).
- **Solución**:
  - Verifica que el Hotspot tenga configurado el servicio DNS nativo de MikroTik (`/ip dns set allow-remote-requests=yes`).
  - En el perfil de Hotspot, asegúrate de que **Transparent Proxy** esté habilitado o que las reglas de NAT del Hotspot estén al inicio de `/ip firewall nat`.

### 2. "Wrong username or password" al intentar iniciar sesión
- **Causa**: Discrepancia entre la codificación de caracteres o el archivo `md5.js` no se transfirió correctamente en modo texto/binario.
- **Solución**:
  - Verifica en `/ip hotspot user print` que el usuario exista y la clave coincida.
  - Verifica que `login-by` incluya `http-chap`.

### 3. El botón "ACCESO CLIENTES" no inicia la sesión (Trial)
- **Causa**: El método `trial` no está activado en el Server Profile, o el usuario ya agotó su tiempo de prueba y la MAC sigue bloqueada por el `uptime-reset`.
- **Solución**:
  - Revisa la configuración con:
    ```routeros
    /ip hotspot profile print detail
    ```
  - Si necesitas reiniciar la prueba para una MAC durante el desarrollo:
    ```routeros
    /ip hotspot active remove [find user~"T-"]
    /ip hotspot user remove [find name~"T-"]
    ```

---

## 🔒 Buenas Prácticas de Seguridad

1. **Aislamiento de Clientes en Wi-Fi (Client Isolation)**:
   - Desactiva el reenvío directo entre dispositivos inalámbricos para prevenir ataques ARP Spoofing o escaneo local de red:
     ```routeros
     /interface wireless set [ find ] default-forwarding=no
     ```
2. **Prevenir Envío de Claves en Texto Plano**:
   - Mantén habilitado **HTTP-CHAP** en lugar de PAP exclusivo, para que las claves viajen protegidas mediante challenge MD5.
3. **Límite de Sesiones Concurrentes**:
   - Configura `shared-users=1` en los perfiles de usuario que requieran uso individual para evitar la reventa no autorizada de credenciales.

---

## 📄 Licencia y Créditos

- **Diseño e Implementación**: Plantilla adaptada para la red Wi-Fi y experiencia de usuario de **Salutta**.
- **Compatibilidad**: Compatible con **MikroTik RouterOS v6.48+** y **RouterOS v7.x**.
- **Derechos de Marca**: Todos los logotipos, isotipos y activos de marca `Salutta` pertenecen a sus respectivos titulares.

<div align="center">
  <sub>Desarrollado con altos estándares de rendimiento y seguridad para redes MikroTik.</sub>
</div>
