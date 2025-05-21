# Requisitos previos
Para ejecutar el proyecto, es requisito tener instalada la última versión de Docker y Docker Desktop

# Paso a paso para levantarlo
## Imagen de Jenkins y SonarCube
Para iniciar el proyecto, es necesario levantar Jenkins y SonarCube usando la siguiente línea de comandos en la terminal:

```
docker-compose up -d
```

## Exposición de localhost con Ngrok
Cuando termine de levantar la imagen, será necesario exponer el puerto local usando ngrok. En este caso, primero se configura 
la terminal con el token que arroja la página oficial de ngrok usando:
```
ngrok config <token de autenticación>
```

Y luego se exporta el puerto de Jenkins (8090):
```
ngrok http 8090
```

Ahora, la terminal arrojará una url que se va a utilizar más adelante. No deberá cerrar la terminal hasta que deje de usar el
proyecto. Si desea dejarlo desplegado, deberá buscar opciones de pago y utilizar ese dominio en lugar de esta URL temporal.

## Configuración de webhooks de Github
En este caso, al ser un proyecto en local, se deberá configurar el webhook cada vez que se reinicie el entorno. En la versión 
gratuita de ngrok, la URL cambia cada vez que se ejecuta el comando anterior.
Para hacer esta configuración, se deben seguir estos pasos:
1. Click en la pestaña de **Seguridad** del repositorio
2. Busca **Webhooks** y click en **Add webhook**
3. En el apartado de URL, se coloca la que generó Ngrok en la consola ```https://<URL de Ngrok>/github-webhook/```
4. Deberá colocar como **Tipo de contenido** la opción de *application/json*
5. Click en el botón de **Add webhook**, en la parte inferior de la configuración

## Configuración de Jenkins
### Configuración inicial
Ahora para esta parte, será necesario primero crear una cuenta de administrador, recuerde que es puerto está protegido por que
es el que se va a encargar de desplegar a producción nuestra página o producto web. Se elige la opción recomendada para la
instalación inicial de los plugins.

### Plugins adicionales
Para instalar los plugins adicionales deberá seguir esta lista de pasos:
1. En panel de control, click en **Administrar Jenkins**
2. Buscar el apartado de **Plugins** y click
3. En la parte de **Available Plugins** deberá buscar los siguientes plugins (si no aparece, probablemente ya lo tenga instalado):
   - Docker pipeline
   - Docker
   - docker-build-step
   - Eclipse Temurin installer
   - Github Integration
   - Maven Integration
   - openJDK-native
   - OWASP Dependency-Check
   - Pipeline
   - Slack Notification Plugin
   - SonarQube Scanner for Jenkins
4. Una vez seleccionados todos los plugins, click en el botón de la parte superior derecha que dice **Instalar**
5. Al darle click, serás redireccionado al log de instalación, marca la casilla **Reiniciar Jenkins cuando termine la instalación y
   no queden trabajos en ejecución**
**Nota:** Si el paso 5 genera fallos (se queda colgada la interfaz), deberá reiniciar la imagen del docker en su lugar usando en la terminal:
    ```
    docker-compose restart
    ```
### Configurar los Plugins
Ahora, para usar los plugins que acabamos de instalar, se deberá entrar a la opción de **Tools**, en el panel de control (Dentro de *Administrar 
Jenkins*). Ahí deberá seguir la siguiente configuración (si ya tiene esa configuración omita el ítem):

- Intalaciones JDK
  1. Click en *Añadir JDK*
  2. Colocar un nombre y activar la instalación automática
  3. Click en *Añadir un instalador*
  4. Seleccionar *Install from adoptium.net* la versión *jdk-11.0.19+7*
- SonarCube Scanner
  1. Click en *Añadir SonarQube Scanner*
  2. Colocar un nombre y activar la instalación automática
  3. Click en *Añadir un instalador*
  4. Seleccionar *Install from Maven Central* y la versión *SonarQube Scanner 4.8.0.2856*
- Git installations
  1. Click en *Añadir Git*
  2. Colocar un nombre y como path del Git ejecutable s colocar *git*
- Maven
  1. Click en *Añadir Maven*
  2. Colocar un nombre y activar la instalación automática
  3. Click en *Anadir un instalador*
  4. Seleccionar *Instalar desde Apache* y la versión *3.6.0*
- Instalaciones de Dependency-Check
  1. Click en *Añadir Dependency-Check*
  2. Colocar un nombre y activar la instalación automática
  3. Click en *Añadir un instalador*
  4. Seleccionar *Install from github.com* y dependency-check 6.5.1
- Instalaciones de Docker
  1. Click en *Añadir Docker*
  2. Colocar un nombre y activar la instalación automática
  3. Click en *Anadir un instalador*
  4. Seleccionar *Download from docker.com* y la version *latest*

### Crear los pipelines
