# Entendiendo plantillas, vistas y parciales (layouts, views, partials)

### El objetivo

Hemos progresado significativamente con nuestro projecto Ruby on Rails. En las lecciones anteriores hemos añadido un tema Bootstrap, escribimos algunas tests automatizados básicos, y proporcionamos un método para que los usuarios puedan logear en nuestro proyecto.

En esta lección, vamos a refactorizar los archivos ERB de nuestro proyecto en los que reside el código para crear HTML.
Nos gustaría que nuestro panel de control se pareciera más a este diseño. Queremos eliminar todo lo que no necesitamos y reemplazarlo con un marcador que muestre el número de servidores que se ejecutan en nuestra nube en Digital Ocean.

### Entendiendo las vistas de Rails

Hay tres tipos de archivos en el directorio "app/views":
- Plantillas, que viven bajo "app/views/layouts".
- Vistas, suelen vivir bajo un directorio con el mismo nombre que el controlador que las renderiza.
- Parciales, que viven en cualquier lugar y cuyo nombre de archivo siempre comienza con "_" (guión bajo)

Cuando el usuario visita una URL en el proyecto, Rails encuentra el controlador que responde a la solicitud, el controlador normalmente renderizará una vista y la enviará de vuelta al usuario como HTML.
Cuando se renderiza una vista, si la vista utiliza parciales, estos serán también renderizados e insertados dentro del código HTML de la vista. Además, una vista típicamente pertenece a una plantilla que también se renderizara para componer la respuesta final.

Echemos un vistazo más de cerca a cada uno de estos tipos de archivo.

### Comprendiendo las plantillas

Las plantillas proporcionan un lugar para poner el formato HTML que se repetirá en la mayor parte o la totalidad de las páginas de tu aplicación web. Así pues, este archivo debe contener el marcado que se puede aplicar a toda su aplicación. Por ejemplo enlaces a tus hojas de estilo.
Ruby on Rails fomenta el principio DRY (o "No te repitas") y este diseño sirve para hacer nuestros archivos de vista más DRY.

Nuestro proyecto tiene un archivo de diseño por defecto - lo podemos encontrar en "/app/views/layouts/index.html.erb". En este archivo sólo hay 4 métodos:
"stylesheet_link_tag" para la inclusión de hojas de estilo desde el directorio 'asserts',
"csrf_meta_tags" para la prevención de ataques de "CSRF" (Ver la notas de la lección para aprender más acerca de eso),
"yield" para la visualización de contenidos en el página (más sobre esto en un momento), y
"javascript_include_tag" para la inclusión de javascript en nuestra respuesta HTML.

El método más importante de esta página es el método "yield". Cuando se renderiza esta plantilla, este método será reemplazado por todo el código HTML de la vista asociada con el controlador y acción de la solicitud actual. Por ejemplo, cuando se envía una solicitud a la acción "index" del controlador "dashboard", este "yield" hará que la vista "index" de "/app/views/dashboards/" se renderize.

Vamos a pasar a hablar sobre el fichero de vista 'index' del controlador Dashboard.


### Refactorización de nuestra vista

El archivo "/app/views/dashboards/index.html.erb" es donde existe en la actualidad la mayoría de HTML de nuestro tema. Es un archivo bastante grande, con un montón HTML. Dividir esta vista en pequeños componentes reutilizables nos ayudará a editar y mantener nuestra aplicación.

El primer componente que podemos separar de esta vista es el de la navegación superior y en la barra lateral. Vamos a reutilizar este código HTML en muchas páginas diferentes de nuestra aplicación, así que tiene sentido separarlo.

Vamos a cortar este código HTML de la vista "índice".

A continuación, creamos un directorio llamado "application" en "/app/views/". En ese directorio, creamos un nuevo archivo llamado "_sidebar.html.erb".

### vistas parciales
Ese guión bajo es una convención importante en Rails. El guión diferencia un archivo de vista estándar de un archivo de vista parcial. Una vista parcial es un fragmento de HTML reutilizable que puede usarse una o más veces en cualquier número de vistas o plantillas. Una vista parcial es el bloque de construcción presentación más pequeña en una aplicación Rails. Una plantilla envuelve una vista, y una vista puede incluir uno o varios parciales.

OK, ahora tenemos nuestro primer parcial, vamos a pegar el HTML que hemos copiado del fichero de vista 'index' en el. Guardamos el fichero.

Luego, volvemos al archivo 'index'' del dashboard añadimos este fragmento de código:

```
<%= render partial: 'sidebar'%>
```

Guardamos. A continuación, iniciamos un servidor de desarrollo en la consola mediante el comando:

```
bundle exec rails server
```

Abrimos el navegador y revisamos el proyecto. Debe tener el mismo aspecto que la última vez que lo vimos.

Perfecto. Podemos comentar nuestra vista parcial en la vista, guardarla, volver a cargar nuestro navegador y ver como se vería la página sin el parcial.

Descomentamos esa línea, todo está renderizando completamente de nuevo. Ahora vamos a refactorizar un pedazo más grande.

Todo este contenido demo, con estos gráficos y tablas, debe ser movido a un archivo separado por ahora. Corta este contenido. A continuación, crea otro archivo en "/app/views/application/" llamado "(guion bajo)demo.html.erb". Pega ese contenido en esta parcial 'demo' . Guarda el archivo, a continuación, añade este fragmento de código en el archivo index del dashboard.

```
<%=# render partial: 'demo' %>
```

Estamos dejando esta parcial comentada por ahora. Después de guardar este archivo y actualizar nuestro navegador, vemos que todo el contenido está ahora escondido.

Ok, ahora lo último que vamos a hacer es que el código HTML restante coincida con nuestro diseño.

Vamos a comentar estos otros elementos de interfaz de usuario que no usamos.

Modificamos los bloques de comentarios para que parezcan una nube. Estamos usando Bootstrap, por lo que es tan simple como cambiar la clase CSS de este elemento HTML. También aquí vamos a cambiar el texto así:

```
                      <div class="panel-heading">
                         <div class="row">
                             <div class="col-xs-3">
-                                <i class="fa fa-comments fa-5x"></i>
+                                <i class="fa fa-cloud fa-5x"></i>
                             </div>
                             <div class="col-xs-9 text-right">
-                                <div class="huge">26</div>
-                                <div>New Comments!</div>
+                                <div class="huge">0</div>
+                                <div>Server Instances</div>
                             </div>
                         </div>
                     </div>

```

Ok, hemos aprendido acerca de la estructura de las vistas en un proyecto Rails e hicimos un poco de refactorización. Para cualquier pregunta sobre plantillas, vistas o parciales, dejanos comentarios en Railsmania.com

Gracias y hasta la proxima!
