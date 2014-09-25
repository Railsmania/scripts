## Bootstrap
Hoy vamos a crear una nueva aplicación Ruby on Rails y añadir este panel HTML gratuito que hemos encontrado. Además del hecho de que tiene un montón de gráficos interesantes y otras palafernarias, este tema se basa en Bootstrap por lo que esta preparado para dispositivos móviles.

Es muy simple añadir un tema Bootstrap a un projecto Rails, así que empecemos!

En primer lugar, vamos a abrir una ventana con línea de comandos para crear nuestro proyecto Rails. Vamos a nombrar nuestro proyecto "bob". Podemos crear nuestro proyecto mediante la ejecución del comando ```rails new bob``` en el directorio en el que queremos crear nuestro proyecto.

"rails new" necesita unos momentos para generar todos los archivos base del proyecto. Una vez hecho, vamos a verificar que todo funciona correctamente iniciando un servidor Rails en modo de desarrollo en Localhost.

En la línea de comandos executamos "bundle exec rails server". Ahora abrimos una nueva pestaña del navegador y vamos a la URL del servidor.

Podemos ver la página que Rails genera para nosotros. Muestra información sobre el entorno donde nuestra aplicación se está ejecutando. Todo está funcionando correctamente bien si podemos ver esta página.

Nuestra primera pregunta: ¿cómo cambiamos esta página de inicio por defecto que ofrece Rails?

La respuesta es que tenemos que añadir una ruta a nuestro fichero "routes.rb". Todas las URLs a las que responde nuestro project Rails tienen que estar definidas en este fichero de rutas. Esto es lo que haremos:

Abre el fichero routes.rb. Descomenta la directiva "root". Sabemos que queremos un panel de administración, así que vamos a renombrar esta ruta para apuntar a a la ruta índice (index) de nuestro panel y guardamos el fichero.

Vamos a refrescar la pestaña del navegador de nuestra aplicación. Observa cómo la página de destino ha sido reemplazada por esta página con un precioso mensaje de error. Señala que tenemos un error de enrutamiento. El problema específico es que el no hemos creado un DashboardsController.

Por lo tanto, vamos a crear nuestro controlador para el "dashboard" y la acción "index".

En el directorio "app/controllers" queremos crear un archivo llamado "dashboards_controller.rb" y en ese archivo pondremos el siguiente código:

```
class DashboardsController < ApplicationController
  def index
  end
end
```

El DashboardsController hereda del ApplicationController que Rails ha generado automáticamente. Nuestra acción index no tiene ninguna lógica especial, así que vamos a dejarla en blanco.

Ahora tenemos que crear una vista (view) para esta acción. En el directorio "app/views" creamos una carpeta llamada "dashboards", version plural del nombre del controlador y la ruta. Allí, agregamos un fichero "index.html.erb". A continuación, escribimos este mensaje:

```
<h1>In Dashboards Index</h1>
```

Si guardamos y reiniciamos nuestro servidor Rails ahora, cuando visitamos nuestro projecto en el navegador, veremos nuestra vista del panel con nuestro pequeño mensaje.

Genial, ahora es el momento de añadir el tema Bootstrap. La mayoría de los temas Bootstrap vienen con un ejemplo HTML y todo el CSS y Javascript en subcarpetas siguiendo diferentes convenciones de nombres. Eso no es un problema para nosotros.

Todo lo que tenemos que hacer es mover nuestro CSS al directorio "assets/stylesheets". Vamos a descartar los ficheros CSS minimizados siempre que tengamos una versión del mismo fichero sin minimizar.

A continuación, movemos el Javascript a "assets/javascripts/". Al igual que con las hojas de estilo, vamos a descartar los ficheros minimizados siempre que tengamos una versión del mismo fichero sin minimizar.

Movemos todas las imagenes a "assets/images/".

Por último, si hay ficheros de fuentes (fonts), creamos un directorio dentro de "assets/" llamado "fonts" y las movemos alli. Simple!

Vale, ahora tenemos que decidir dónde colocar el fichero principal del tema. Prefiero mantener el fichero de diseño (layout) de la aplicación original tan sencillo y limpio como sea posible. Así será más fácil intercambiar temas en diferentes partes de la aplicación. Lo único que queremos hacer en el fichero "app/views/layouts/application.html.erb" es mover las referencias a Javascript a la parte inferior de la página, justo encima de la etiqueta del cerrando "body".

Ahora copiamos todos el contenido dentro de "body" del fichero demostración del tema, sin incluir las etiquetas Javascript del final, y lo pegamos el fichero /app/views/dashboards/index.html.erb.

Guárdalo, volver a cargar nuestra aplicación en el navegador. Wow, se ve muy bien, pero faltan algunas cosas. Usando las herramientas de desarrollo de nuestro navegador podemos ver lo que falla. Sabiendo cómo solucionarlo es un poco más complicado, pero aquí está el resumen de qué hacer:

El fichero CSS de FontAwesome necesita ser convertido a un fichero ERB, para que podamos arreglar algunas rutas usando funciones de Rails:

```
  src: url('<%= asset_path("fontawesome-webfont.eot") %>?v=4.1.0');
  src: url('<%= asset_path("fontawesome-webfont.eot") %>?#iefix&v=4.1.0') format('embedded-opentype'), url('<%= asset_path("fontawesome-webfont.woff") %>?v=4.1.0') format('woff'), url('<%= asset_path("fontawesome-webfont.ttf") %>?v=4.1.0') format('truetype'), url('<%= asset_path("fontawesome-webfont.svg") %>?v=4.1.0#fontawesomeregular') format('svg');

````

A continuación, tenemos que arreglar algunos de los javascript. Vamos a no usar dos ficheros Javascript que están mostrando los errores en las herramientas de desarrollado en la consola de nuestro navegador. Estos dos ficheros son ficheros de demostración para el tema que descargamos, vamos a deactivarlos usando la palabra clave "stub".

```
//= stub plugins/flot/flot-data
//= stub plugins/dataTables/dataTables.bootstrap
```

Eso dejará los ficheros en el proyecto para futuro uso pero desactivados para que Rails los ignore.

Ahora, refrescamos nuestro proyecto en nuestro navegador y no muestra ningún error y todo parece estar aquí.

Eso es todo por esta lección, asegúrese de ver el contenido relacionado adicional y temas avanzados que estamos compartiendo en RailsMania.com.

--------
### El huevo de Pascua