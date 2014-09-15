# Conceptos básicos y Hola Mundo en Rails.

En esta lección vamos a revisar los fundamentos de trabajar con un projecto Ruby on Rails.

# Nuevo proyecto
Vamos a empezar por la creación de un nuevo proyecto. Crear un nuevo proyecto es muy sencillo, basta con ejecutar el comando "rails new" en la terminal:

rails new myproject

Esto creará un directorio con un proyecto myproject.
Este proyecto ya contiene todo lo necesario para hacer funcionar tu aplicación web.
Vamos a echar un vistazo a su contenido.
Al más alto nivel, podemos ver varios archivos y directorios. En este
video esplicaremos sólo los directorios más importantes.

# Gemfile
El primer archivo que queremos abordar es el Gemfile.

En el Gemfile definimos todo el software del que depende nuestra aplicación.

El software en el universo Ruby generalmente se presenta en gemas (gems). El Gemfile es
una lista de todas las gemas utilizadas por nuestro proyecto Rails.
Podemos ver, por ejemplo, que incluso definimos la versión de Rails en que nuestro
proyecto depende.
Hay muchas otras gemas en este archivo. La mayor parte de la potencia de
Rails viene de instalar gemas útiles que proporcionan funcionalidad para
tu aplicación. Nosotros te enseñaremos cómo encontrar, instalar y usar gemas en
tus proyectos.


# Bundler
Cuando modificamos el Gemfile, debemos ejecutar el comando
'Bundle install'

Bundle descargará e instalará todo el software
definido en el Gemfile y éste estará disponible en nuestro proyecto.

# WEBrick
A pesar de que simplemente auto-generamos nuestro proyecto, ya podemos lanzar un servidor web con nuestra aplicación. En la terminal, asegúrate de estar en el directorio del proyecto, a continuación ejecuta:

bundle exec rails server

Este comando inicia un servidor web que servirá la página por defecto de Rails. La página de inicio por defecto no es muy interesante, pero cuando la vemos comprobamos que nuestro proyecto funciona correctamente.

# Config /routes

El siguiente directorio importante es config/  Todos los ficheros de configuración del
proyecto están aquí. Por ahora podemos usar los valores predeterminados. Explicaremos cada archivo en lecciones futuras, cuando tengamos que modificarlo.

Pero un archivo que lo más seguro tendrás que modificar en todos tus proyectos es config/routes.rb

Este archivo por defecto está lleno de ejemplos comentados de su uso y define
las rutas del proyecto. Las rutas se usan para asignar el código en los controladores del proyecto que se ejecutará cuando el usuario visita una URL en su navegador.

Como ejemplo, vamos a descomentar esta línea de la ruta raíz.
root 'wellcome#index'

'root' significa la URL de tu web cuando el usuario no indica ningún path.
El formato 'welcome#index' indica el controlador 'welcome' y la acción 'index'. Significando que cuando el usuario llega la direccion raiz de tu sitio web, se ejecutará la acción index del controlador welcome.

# Controladores y acciones (controllers and actions)
Entonces, ¿qué es una acción? ¿Qué es un controlador?

Volviendo al sistema de archivos que podemos ver un directorio 'app'. La mayor parte de lo que es
identificado con una aplicación Rails está dentro de este directorio.
Rails sigue una arquitectura llamada MVC, Modelo-Vista-Controlador. La aplicación tiene subdirectorios para modelos, vistas y controladores.

Cuando un usuario visita nuestra aplicación en cualquier URL dada, Rails
buscará primero en el archivo config/routes.rb para determinar qué controlador controlará la solicitud  del usuario.

Como onfiguramos config/routes.rb para usar el controlador 'welcome', vamos a
crear ese archivo.

Todos los controladores heredan del controlador llamado ApplicationController. Así se crea un controlador llamado WelcomeController:

class WelcomeController <ApplicationController
end


En config/routes.rb escribimos "welcome#index", significa la accion 'index' del controlador 'welcome'.

Para crear una nueva acción, creamos un nuevo método en Ruby:

`` `
class WelcomeController <ApplicationController
def index
end
end
`` `

Ahora el controlador está listo. Se llamará a este método, pero en este punto no devolverá algo útil. Todavía necesitamos crear una vista (view) para enviarla al navegador del usuario.

# Vistas (views)

Tenemos un directorio app/views donde se guardan todas las vistas. Por convención un
controlador renderiza las vistas del directorio con el mismo nombre que el controlador. Así que necesitamos crear un directorio llamado 'welcome' en app/views.

Ahora tenemos que crear una vista para 'index'. Basta con crear un archivo index.html.erb. La extensión erb indica que este archivo será ejecutado por rails antes de convertirse en HTML. Esto nos permite crear contenido dinámico. Por ejemplo podemos mezclar de Ruby y HTML:
<H1> Hello World </ h1>
<% = "Hola desde Ruby '%>

Vamos a reiniciar nuestro servidor rails, vamos a la terminal y ejecutamos:
bundle exec rails server

Ahora podemos volver a abrir en una pestaña del navegador y podemos ver nuestra primera página web Hello World a través de Ruby on Rails!


Eso es todo por esta lección, te animamos a explorar los otros archivos en tu nuevo proyecto. Como crear nuevos projectos es tan fácil, si se rompe algo, puedes simplemente borrar el projecto y crear uno nuevo!

Feliz programación!