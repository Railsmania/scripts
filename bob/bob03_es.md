#Omniauth y la estrategia DigitalOcean
En esta lección vamos a requerir que los usuarios se autentiquen antes de poder ver el panel central que estamos creando.

Para ello, vamos a utilizar una estrategia de autenticación abierta ("Oauth"), para registrar a los usuarios en nuestra aplicación. Si no estás familiarizado con Oauth, revisa las notas en la lección para una explicación más detallada.

Vamos a utilizar dos gemas: la gema OmniAuth y una gema con una estrategia Oauth especifica para permitir que los usuarios se autentiquen a través del servicio de Oauth de DigitalOcean. En la parte inferior de nuestro Gemfile, añadiremos las gemas "omniauth" y "omniauth-digitalocean", ambas fijadas a versiones específicas.

```
gem 'omniauth', '1.2.2'
gem 'omniauth-digitalocean', '0.1.0'
```
Y ejecutamos el comando 'bundle install'

# BDD: crear un test

Antes de empezar a escribir código, recuerda que en nuestra última lección añadimos Cucumber a nuestro proyecto para que pudiéramos crear tests antes que código usando una metodología llamada Behavior Driven Development o BDD.

Vamos a escribir el escenario que necesitamos para cubrir el requisito más básico de autenticación.

En el directorio "features/", crea un archivo llamado "authentication.feature". En este fichero, escribe lo siguiente:

```
Feature: Authentication
  In order to protect sensitive information
  As a user
  I should login before accessing my dashboard

  Scenario: Unauthenticated request
  to a dashboard
    Given I am not logged in
    When I visit the root url
    Then the response should be the login page
```

Ejecuta "bundle exec cucumber"

Cucumber nos provee de las definiciones recomendadas para los pasos. Cópialas y ponlas en un archivo llamado authentication.rb dentro del directorio /features/step_definitions

¿Qué significa que un usuario no se ha logeado? Para nuestra aplicación, un usuario no está logeado si no tiene un identificador de sesión activa.

Debido a que por defecto un usuario no tiene identificador de sesión activa(session id), la primera definición puede quedar en blanco.

La segunda definición es simple - sólo necesitamos una pagina para logearse. Nuestro tema Boostrap vino con una página de inicio de sesión, así que vamos a utilizarla.

Esta página de inicio de session es sencilla y tiene muy poco texto. El texto "Please Sign In" parece suficientemente único para que podamos utilizarlo en nuestro test.

Eso significa que en este paso podemos escribir:

expect
the page
to
have content
'Please Sign In'

Ejecutamos los escenarios con 'bundle exec cucumber'
Un escenario falla, como se esperaba, porque no tenemos una página de inicio de sesión. Vamos a añadir una!

# El controlador de sesiones y la página de inicio de sesión

Crea un archivo sessions_controller.rb en "/app/controllers/", hereda de ApplicationController y escribe un único método: "new".

Guarda el fichero, y crea un directorio llamado "sessions" en "/app/views/".

En ese directorio, vamos a crear un archivo "new.html.erb" y copiamos en el todo el contenido del cuerpo del fichero login.html del tema Bootstrap. Asegúrate de no copiar las referencias a Javascript, que ya se incluyeron en una lección anterior.

Vamos a editar esta plantilla. Dado que vamos a usar OAuth de DigitalOcean, necesitamos un bonito logo que la gente puede hacer clic para logearse. Este logo está disponible en las notas de la lección, así que sólo tienes que añadir el archivo a su directorio /app/assets/images.

Justo encima de la etiqueta de formulario HTML, insertamos un enlace al logo.

```
<div class="text-center">
                      <a href="/auth/digitalocean"><%= image_tag 'do_logo.png', width: 250 %><br />Autenticate usando Digital Ocean</a>
                    </div>
```

El href de este enlace apunta a una ruta que la gema OmniAuth ofrece a nuestra aplicación Rails. Añade el logo, comenta el formulario normal de inicio de sesión y guarda ese archivo.


# Añadir rutas y conectarlas a nuestro controlador

Después de leer la documentación OmniAuth, sabemos que necesitamos estas rutas específicas.

```
  get '/auth/new'
  get '/auth/:provider/callback'
  get '/auth/logout'
  get '/auth/failure'
```

Todas estas rutas van a nuestro controlador "sessions"

"Auth/new" irá al controlador "sessions", método "new".
"auth/provider/callback" irá al método "create" del controlador "sessions".

Mientras que la ruta "auth/logout" apunta a "sessions#destroy" y "auth/failure" a "sessions#failure":

```
  get '/auth/new', to: 'sessions#new'
  get '/auth/:provider/callback', to: 'sessions#create'
  get '/auth/logout', to: 'sessions#destroy'
  get '/auth/failure', to: 'sessions#failure'
```

Vamos a agregar los metodos "create", "failure" y "destroy" a nuestro controlador.

En "app/controllers/sessions_controller" escribimos estos 3 métodos en blanco

```
class SessionsController < ApplicationController
  def new
  end

  def create
  end

  def destroy
  end

  def failure
  end
end
```

Asegúrate de guardar tanto el controlador como el archivo de rutas.

# Pasar nuestro test

Vamos a hacer lo mínimo necesario para pasar nuestro escenario. Todo lo que tenemos que hacer es redirigir a todo el mundo a la página de inicio de sesión para pasar el escenario. Vamos a hacer uso de un "before filter" en ApplicationController.

En  "app/controllers/application_controller" añade un método llamado 'verify session' y un before_filter para este metodo. Queremos que este método redirija a la página de inicio de sesión.
Podemos encontrar la ruta a la página de inicio de sesión al abrir una terminal y escribiendo:

bundle exec rake routes

Vemos que la ruta a "Sessions#new" es "auth_new_path"

Asi que este método debe sólo redirigir a "auth_new_path".

```
before_filter :verify_session

def verify_session
  redirect_to auth_new_path
end
```

Guarda el fichero ApplicationController.

Como añadimos este "before_filter" al ApplicationController, se aplicará a todos los controladores que heredan de él. Para evitar un bucle de redireccionamiento infinito, tenemos que añadir un salto antes de filtro en el SessionsController.

Ahora vamos a ejecutar nuestros escenarios.

La salida nos muestra que nuestro nuevo escenario está pasando. Pero el escenario que escribimos en la lección anterior está fallando. Como queremos que sólo los usuarios autenticados puedan acceder al tablero de instrumentos, esto tiene sentido.

# Autenticación de usuarios a través de DigitalOcean
Pero ahora tenemos que escribir el código para que un usuario conectado pueda acceder a la página de panel.

En primer lugar, vamos a necesitar un inicializador para OmniAuth. En el directorio "config/initializers" crea un archivo "01_omniauth.rb" archivo y añade:

```
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :digitalocean, 'CLIENT_ID', 'SECRET_TOKEN', scope: "read write"
end
```

Substituye 'CLIENT_ID' y 'SECRET_TOKEN' por las credenciales que recibiste de DigitalOcean. En las notas explicamos como conseguir éstas, asegúrate de echarles un vistazo si no sabes como conseguirlas.

Voy a añadir mi ID de cliente y símbolo secreto y guardar este archivo.

Ahora es el momento de conectar todo. Queremos que un usuario haga click en el logo de inicio de sesión, autentique con Digital Ocean, y rediriga a nuestra aplicación. Cuando se redirigen, los usuarios aterrizarán en el método "SessionsController#create". En este método encontraremos una sesiòn existente previa o crearemos una nueva basada en un hash de autenticación.

```
if auth_hash
  session_instance = Session.find_or_create_by(content: auth_hash.to_json)
  session[:active] = session_instance.id
end
redirect_to root_path
```
Y añadimos el siguiente método protegido:

```
protected
def auth_hash
  request.env['omniauth.auth']
end
```
Hay 3 cosas que tenemos que explicar de este código.

En primer lugar, el hash especial session[] de Rails. Este hash especial persiste información entre peticiones. Todo lo que ponemos en él en esta solicitud estará aún ahi en la siguiente solicitud asumiendo que no lo borramos intencionalmente y suponiendo que el usuario no borra sus cookies.

En segundo lugar, "request.env" es otro hash especial de Rails. Contiene una gran cantidad de información sobre el entorno de la solicitud actual. OmniAuth ha colocado información sobre el usuario que se acaba de autenticar a través de nuestro proveedor de autenticación abierta - Digital Ocean.

Y en tercer lugar, todavía no hemos hablado del objeto "Session", pero en este método encontramos o creamos una instancia de "Session". El objeto Session es un modelo que tenemos que crear. Lo usaremos para guardar la información de sesión en la base de datos para facilitar la búsqueda.

Vamos a crear ese modelo abriendo una terminal y escribiendo

```
bundle exec rails generate model Session content:text
```
y
```
bundle exec rake db:migrate
```

Eso crea nuestro modelo de sesión, la migración de la base de datos, la tabla en la base de datos cuando se ejecutan las migraciones.


# Limpiando código antiguo
A continuación, vamos a volver a nuestro ApplicationController. Ese código rápido que escribimos antes de pasar nuestro escenario necesita ser actualizado. Lo cambiamos para que sea:

```
redirect_to auth_new_path unless Session.exists?(id: session[:active])
```

Ahora sólo vamos a redirigir a la página de inicio de sesión si no hay una instancia de Session con id igual al valor en la sesión [activa].

# Prueba real
Es hora de probarlo todo. Abre un terminal, asegúrate de que está en el directorio del proyecto, y inicia el servidor:
```
bundle exec rails server
```

Abre la URL de tu servidor en el navegador y ...

Bravo! Nos redirige a la página de inicio de sesión. Al hacer clic en el enlace nos lleva a DigitalOcean donde podemos autenticar. DigitalOcean entonces nos remite a nuestra aplicación - y estamos conectados, Éxito!


# Dos cosas mas
Dos detalles mas:

En primer lugar, vamos a hacer funcionar el enlace "logout". Editamos "/app/views/dashboards/index.html.erb" para añadir la ruta de cierre de sesión de autenticación a ese enlace. También actualizamos el metodo "Sessions destroy"  para destruir adecuadamente nuestra sesión cuando hacemos clic en cierre de sesión:

```
Session.destroy(session[:active])
reset_session
redirect_to auth_new_path
```

"reset_session" también es un método proporcionado por Railsq que destruye la sesión por lo que a Rails se refiere. Destruimos nuestro propio registro en la tabla "session" en la base de datos y redirigimos a la página de inicio de sesión. Podemos probar esto simplemente haciendo clic en logout y luego tratar de acceder al panel central.

# Pasar ese escenario!

Y finalmente, cuando comprobamos nuestros escenarios:

```
bundle exec cucumber
```

vemos que todavía uno está fallando. Tenemos que conseguir que pase. He aquí cómo.

Añadimos un nuevo paso en dashboard.feature

```
Given I am logged in
```

y añadimos esta definición a nuestro archivo authentication.rb en features/step_definitions:
```
Given(/^I am logged in$/) do
  OmniAuth.config.test_mode = true
  visit '/auth/digitalocean'
end
```

Ejecutamos los escenarios, y ahora pasan como se esperaba.

# fin
Muy bien, eso es todo lo que podemos meter en una lección. Si tienes alguna pregunta o comentario, por favor contáctenos en RailsMania.com. Gracias por su atención!
