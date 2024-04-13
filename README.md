# Instrucciones de instalación
- - - 
## Índice
1. [Introducción](#Introducción)
2. [Requisitos previos](#Requisitos-previos)
3. [nova up api](#novaup-api)
    - [Instrucciones de instalación](#instrucciones-de-instalación)
    - [Variables de entorno](#variables-de-entorno)
4. [Nova Up](#nova-up)
    - [Instrucciones de instalación](#instrucciones-de-instalación)
    - [Variables de entorno](#variables-de-entorno)
5. [API KEYS](#api-keys) 
6. [Créditos](#creditos)

---
## Introducción
El repositorio actual consta de 2 proyectos debidamente clasificados en distintos directorios:
 - nova-up, donde se encuentra el front end de la aplicación
 - nova-up-api, donde se encuentra el backend de la aplicación.

 El presente documento aborda el proceso de instalación y configuración del proyecto en los servidores institucionales de la Universidad Politécnica de Quintana Roo, cada parte del proyecto se explicará detalladamente en su sección correspondiente.
- - - 
## Requisitos previos del servidor
- [Node.js](https://nodejs.org/en)
- [Yarn](https://yarnpkg.com/)
- Base de datos [MySQL](https://www.mysql.com/)/[MariaDB](https://mariadb.org/)

(Nota, a fecha de 12/04/2025, todos los requisitos previos estén instalados y configurados en los servidores institucionales, de todos modos, es recomendable verificarlo)

---
## NovaUp API
### Instrucciones de instalación
1. **Preparar tu aplicación Nest.js**:  - Clona o copia el directorio `nova-up-api` en el directorio de tu preferencia en el servidor.
   - Abre una terminal en el directorio recién creado.
   - Instala las dependencias del proyecto utilizando Yarn.
   ```bash
   yarn install
   ```
   -Configura las [variables de entorno](#variables-de-entorno) de la aplicación
2. **Instalar PM2**: PM2 es un gestor de procesos para aplicaciones Node.js. Instálalo globalmente usando npm.

    ```bash
    npm install pm2 -g
    ```

3. **Compilar tu aplicación Nest.js**: Compila tu código a JavaScript utilizando el siguiente comando.

    ```bash
    npm run build
    ```
4. **Crear y correr migraciones de la base de datos**: Si tu aplicación Nest.js utiliza una base de datos que requiere migraciones (como PostgreSQL con TypeORM), necesitarás crear y correr las migraciones antes de iniciar tu aplicación en producción.

   - Para crear una migración, ejecuta el siguiente comando en la terminal:

     ```bash
     npm run typeorm:migrate <nombre_de_la_migración>
     ```

     Reemplaza `<nombre_de_la_migración>` con un nombre descriptivo para tu migración.

   - Una vez que la migración se haya creado con éxito, ejecuta el siguiente comando para aplicar la migración a tu base de datos:

     ```bash
     npm run typeorm:run
     ```

   Asegúrate de que todas las migraciones se hayan ejecutado correctamente antes de iniciar tu aplicación Nest.js en producción.

4. **Iniciar tu aplicación Nest.js con PM2**: Usa PM2 para iniciar tu aplicación Nest.js en segundo plano.

    ```bash
    pm2 start dist/main.js --name nova-up
    ```

5. **Configurar Apache como proxy inverso**: Configura Apache para redirigir las solicitudes a tu aplicación Nest.js.

    ```apache
    <VirtualHost *:80>
        ServerName example.com
        ProxyPreserveHost On
        ProxyPass / http://localhost:3000/
        ProxyPassReverse / http://localhost:3000/
    </VirtualHost>
    ```

    Reemplaza `example.com` con el dominio o dirección IP del servidor.

6. **Reiniciar Apache**: Reinicia el servicio de Apache para aplicar los cambios.

    ```bash
    sudo systemctl restart apache2
    ```
Ahora la api Nest.js debería ser accesible a través de Apache el servidor.

---
### Variables de entorno
## Configuración de variables de entorno

Para configurar las variables de entorno necesarias para la aplicación, sigue estos pasos:

1. Crea un archivo `.env` en el directorio raíz de tu proyecto.

2. Configura las siguientes variables de entorno dentro de el archivo `.env`:

```plaintext
- `DB_HOST`: La dirección del host donde se encuentra alojada la base de datos.
  
- `DB_PORT`: El puerto utilizado para la conexión con la base de datos.

- `DB_USERNAME`: El nombre de usuario utilizado para acceder a la base de datos.

- `DB_PASSWORD`: La contraseña utilizada para acceder a la base de datos.

- `DB_NAME`: El nombre de la base de datos utilizada por la aplicación.

- `PORT`: El puerto en el que se ejecutará la aplicación.
(NOTA; CREAR LA BASE DE DATOS CON ANTERIORIRDAD EN EL SERVIDOR)

- `JWT_SECRET`: La clave secreta utilizada para firmar y verificar tokens JWT (JSON Web Tokens). Esta clave debe ser generada de forma segura utilizando el método más conveniente disponible en tu entorno de desarrollo. Se recomienda encarecidamente utilizar un generador de contraseñas seguras o métodos criptográficos sólidos para generar este valor. El uso de una clave segura aumenta la seguridad de la aplicación al dificultar la posibilidad de que un atacante adivine o fuerce la clave.

- `GOOGLE_CLIENT_ID`: El ID del cliente proporcionado por Google para la autenticación OAuth, se explica con mas detalle en la sección de API KEYS
```

---

## NovaUp
### Instruciones de instalación

1. **Preparar tu aplicación Nest.js**:  - Clona o copia el directorio `nova-up` en el directorio de tu preferencia en el servidor.
   - Abre una terminal en el directorio recién creado.
   - Instala las dependencias del proyecto utilizando Yarn.
   ```bash
   yarn install
   ```
   -Configura las [variables de entorno](#variables-de-entorno) de la aplicación
2. **Construye tu aplicación Next.js**: Dirígete al directorio de tu aplicación Next.js y ejecuta `npm run build`. Esto generará una carpeta `.next` con los archivos optimizados para producción.

3. **Configura Apache**: Agrega un bloque de configuración para tu aplicación Next.js en el archivo de configuración de Apache (`httpd.conf`):

```apache
<VirtualHost *:80>
    ServerName tu-dominio.com

    DocumentRoot /ruta del proyecto/build
    <Directory /ruta del proyectouild>
        Options -Indexes +FollowSymLinks +MultiViews
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```
6. **Reiniciar Apache**: Reinicia el servicio de Apache para aplicar los cambios.

    ```bash
    sudo systemctl restart apache2
    ```
Ahora el frontend Next.js debería ser accesible a través de Apache.

### Variables de entorno
## Configuración de variables de entorno

Para configurar las variables de entorno necesarias para la aplicación, sigue estos pasos:

1. Crea un archivo `.env.local` en el directorio raíz de tu proyecto.

2. Configura las siguientes variables de entorno dentro de el archivo `.env`:

```plaintext
# Claves de google auth api
- `NEXT_PUBLIC_GOOGLE_ID`: La ID pública de tu aplicación de Google.
- `NEXT_PUBLIC_GOOGLE_SECRET`: La clave secreta pública de tu aplicación de Google.

- `NEXT_PUBLIC_URL_BASE`: La URL base de tu aplicación Next.js.
- `NEXT_PUBLIC_URL_API`: La URL de tu API.

#Youtube API
- `NEXT_PUBLIC_YOUTUBE_API_KEY`: La clave de API de YouTube.
- `NEXTAUTH_URL`: Este valor es la URL base de tu aplicación donde NextAuth está configurado para manejar la autenticación.
Por lo general, esta URL es la dirección a la que los usuarios accederán para autenticarse en tu aplicación.
Por ejemplo, si tu aplicación está alojada en https://mi-aplicacion.com, entonces NEXTAUTH_URL sería https://mi-aplicacion.com.
Debes asegurarte de configurar correctamente esta URL en tu entorno de desarrollo y producción para que NextAuth pueda funcionar correctamente.

- `NEXTAUTH_SECRET`: Esta es una clave secreta utilizada por NextAuth para firmar y cifrar las sesiones de usuario y otros datos sensibles.
Debes generar una cadena aleatoria y segura para esta clave.
```

### API KEYS
Para integrar servicios de Google en tu aplicación Next.js, necesitarás obtener las API Keys correspondientes. Sigue estos pasos para obtener tus propias API Keys de Google y configurarlas como variables de entorno en tu proyecto Next.js:

1. **Inicia sesión en la Consola de Desarrolladores de Google**:
   - Accede a la [Consola de Desarrolladores de Google](https://console.developers.google.com/) e inicia sesión con tu cuenta de Google o crea una nueva si aún no tienes una.

2. **Crea un nuevo proyecto**:
   - Si es la primera vez que accedes, es posible que debas crear un nuevo proyecto. Haz clic en "Seleccionar proyecto" y luego en "Nuevo proyecto". Sigue las instrucciones para nombrar y crear el proyecto.

3. **Habilita las API necesarias**:
   - Dirígete al panel de control y haz clic en "Habilitar APIs y servicios".
   - Busca y habilita las API que necesitas para tu aplicación Next.js, como "YouTube Data API", 

4. **Crea credenciales de API**:
   - En la pestaña "Credenciales", haz clic en "Crear credenciales" y selecciona "Clave de API".
   - Se generará una nueva clave de API que podrás utilizar en tu aplicación.

5. **Obtén tus API Keys**:
   - Copia la clave de API generada para cada servicio que habilitaste. Por ejemplo, copia la API Key de YouTube para `NEXT_PUBLIC_YOUTUBE_API_KEY`.

6. **Guarda tus API Keys como variables de entorno**:
   - Abre el archivo `.env.local` en la raíz de tu proyecto Next.js.
   - Agrega las API Keys como variables de entorno por ejemplo:

     ```
     NEXT_PUBLIC_YOUTUBE_API_KEY=TU_API_KEY_DE_YOUTUBE
     ```

## Creditos
Los créditos del desarrollo de la aplicación van a la generación 2020-2023 de la carrera de Ingeniería en Software. Agradecemos su arduo trabajo y dedicación en la creación de esta aplicación, en especial a los usuarios[DiegoG28](DiegoG28) [itzramn](https://github.com/itzramn) [Alexandergv2117](https://github.com/Alexandergv2117), [RomanshkVolkov](https://github.com/RomanshkVolkov) [gloverProgramando](https://github.com/gloverProgramando), [LangelXoBl](https://github.com/LangelXoBl), [azulcolor](https://github.com/azulcolor), [peekaboo212](https://github.com/peekaboo212)