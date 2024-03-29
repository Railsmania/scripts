# Usando APIs con Faraday

### ¿Qué es Faraday?
En esta lección vamos a utilizar una gema llamada Faraday, esta gema es un cliente HTTP. La usaremos para acceder programáticamente un API REST. Cuando terminemos esta lección, comprenderás cómo usar Faraday en tus proyectos Rails y cómo realizar peticiones HTTP básicas desde Rails para interactuar con APIs en  web remotas.

Nuestro objetivo básico de esta lección es acceder al API de Digital Ocean y mostrar algunos datos sobre los recursos que tenemos en nuestro panel de control de administración.

Así que vamos a empezar.


### Añadiendo la gema
Lo primero que tenemos que hacer es añadir la gema Faraday a nuestro Gemfile. Para asegurar que la experiencia es la misma que en este video, utiliza la misma versión que nosotros.

Una vez que hemos añadido esto al Gemfile, guarda el archivo y ejecuta:
```
bundle install
```
en la terminal.

### DropletsController
Ahora que la gema ha sido instalada vamos a añadir un controlador para interactuar con los Droplets de DigitalOcean.
Crea un archivo en "app/controllers"  llamado "droplets_controller.rb". Vamos a escribir un controlador sin nada en ese fichero:

```
class DropletsController < ApplicationController
end
```

Ahora añadimos una ruta al recurso "droplets" en el fichero config/routes.rb
```
resources :droplets
```

Puedes aprender más sobre enrutamiento de recursos en las notas de la lección,  asegúrese de revisar los enlaces y material adicional.

Ahora vamos a escribir el código del controlador para verificar que podemos conectar con el API de DigitalOcean y hacer peticiones. En el DropletsController, creamos la acción index.

```
def index
end
```

Dentro de esa acción, añadimos código copiado de un ejemplo del README de Faraday para configurar una nueva conexión.

```
conn = Faraday.new(:url => 'https://api.digitalocean.com/v2/') do |faraday|
  faraday.request  :url_encoded             # form-encode POST params
  faraday.response :logger                  # log requests to STDOUT
  faraday.adapter  Faraday.default_adapter  # make requests with Net::HTTP
end
```

A continuación vamos a configurar una cabecera para autorizar nuestra solicitud a DigitalOcean, y a asignar el resultado de la solicitud a una variable:
```
conn.headers['Authorization'] = "Bearer #{JSON.parse(Session.where(id: session[:active]).first.content)['credentials']['token']}"
@droplets = JSON.parse(conn.get('droplets').body)
```

En una lección anterior guardamos las crecenciales para autorizarnos en DigitalOcean cuando el usuario ha iniciado sesión usando autenticación abierta. Aquí, simplemente leemos la sesión activa y recuperamos las credenciales que nos proporcionó DigitalOcean.

Vamos a refactorizar este código en un metodo "helper" que podamos usar en todos nuestros controladores.

```application_controller.rb
+  def current_session_auth
+    JSON.parse(Session.where(id: session[:active]).first.content)
+  end
```
```droplets_controller.rb
conn.headers['Authorization'] = "Bearer #{current_session_auth['credentials']['token']}"
```

A continuación, vamos a crear un directorio "droplets" dentro de "app/views". Ahí creamos un fichero "index.html.erb" con HTML para poner datos luego.
El código de este archivo se encuentra en las notas de la lección, asegúrate de copiarlo de allí.

```
<div id="wrapper">
  <%= render partial: 'sidebar'%>

    <div id="page-wrapper">
        <div class="row">
            <div class="col-lg-12">
                <h1 class="page-header">Droplets</h1>
            </div>
            <!-- /.col-lg-12 -->
        </div>
        <%= @droplets %>
    </div>
</div>
```

Lanzamos nuestro servidor Rails, y echamos un vistazo a lo que tenemos hasta ahora. Podemos añadir "/droplets" a la URL para visitar la acción "index" del controlador "droplets".

Ahora estamos renderizando toda la respuesta de la API de DigitalOcean. Parece que estamos recibiendo los datos que queremos. Ahora es el momento para dar formato a cómo los mostramos para que sean más fáciles de leer.


### Modelo Ocean
Ahora, vamos a refactorizar el código faraday del controlador en una clase llamada "Ocean" que vamos a guardar en el directorio "app/models". Copia el código de las notas de la lección:

```
class Ocean
  attr_accessor :droplets, :images

  def initialize(params = {})
    @conn = Faraday.new(:url => 'https://api.digitalocean.com/v2/') do |faraday|
      faraday.request  :url_encoded             # form-encode POST params
      faraday.response :logger                  # log requests to STDOUT
      faraday.adapter  Faraday.default_adapter  # make requests with Net::HTTP
    end
    @conn.headers['Authorization'] = "Bearer #{params[:auth_token]}"
  end

  def droplets
    JSON.parse(@conn.get('droplets').body)['droplets']
  end
end
```

En el controlador de la aplicación, vamos a añadir otro método para acceder rápidamente a una instancia del modelo Ocean.

```
def ocean
  @ocean ||= Ocean.new(auth_token: current_session_auth['credentials']['token'])
end
```

Aquí te darás cuenta de algo que probablemente no ha visto antes. Después de la variable "ocean" tenemos dos barras verticales (llamados tubos) y un signo igual seguido de la creación de la instancia de una nueva clase Ocean. Esos dos barras verticales y la igualdad son un operador de asignación especial en Ruby, conocido como el operador de asignación condicional. Si la variable de instancia "ocean" es nula o falsa, entonces el lado derecho de la operación se evaluará y se asignará a "ocean". Si la variable "ocean" no es nula o falsa, entonces el lado derecho de la operación de la asignación condicional no será evaluada - y el programa mantiene el valor actual de "ocean".

Dado que queremos estos métodos en nuestras vistas, tenemos que decirle a Rails que estos dos mètodos serán considerados métodos "helper".

```
helper_method :current_session_auth, :ocean
```

Los métodos que son usados en controladores y vistas tienen que ser designados como métodos "helper" en el controlador. Explicaremos más acerca de métodos "helper" en otra lección.

### Cambios en las vistas

Inspeccionemos nuestro progreso.
Vamos a modificar el fichero "app/views/dashboards/index.html.erb" y vamos a mostrar dinámicamente el número de servidores en nuestra nube en DigitalOcean:

```
- <div class="huge">0</div>
+ <div class="huge"><%= ocean.droplets.count %></div>
```

A continuación, en el navegador refrescamos la vista para ver el nuevo contenido dinámico.

Vaya, parece que nos olvidamos de algo. Es un error que introdujimos al refactorizar el código inicial. Cuando movimos el código al controlador de la aplicación, nos olvidamos de refactorizar este trozo en nuestro modelo.

Introducimos esta dependencia a traves de un parámetro del método. Así que simplemente tenemos que referenciar este parámetro en nuestro modelo.

Arreglamos eso y guardamos el archivo, vamos a refrescar nuestra página.

Genial, nos las hemos arreglado para conectar con el API REST de DigitalOcean mediante Faraday y dinámicamente mostrar el número de servidores que tenemos.

Apenas hemos arañado la superficie de lo que es posible con esta API. Es muy fácil visualizar información, pero también lo es la interacción con tus servidores usando Faraday y la API de DigitalOcean.
Lee las notas de la lección para ver algunos ejemplos de lo que puedes hacer e  intentar trabajar con unos cuantos de sus comandos por tu mismo para ver qué eres capaz de hacer.

Gracias por tu atención!