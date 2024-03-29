## BDD con Cucumber y Capybara

Hoy vamos a añadir una herramienta para crear test automáticos que nos permitiran verificar que estamos construyendo funcionalidad que hace exactamente lo que queremos que haga. Si no sabes lo que es el desarrollo guiado por comportamiento o el desarrollo guiado por test(BDD en inglés), puedes aprender más en las notas de esta lección.

En esta lección vamos a hacer algo muy simple para que puedas comprender lo que esta ocurriendo incluso si no estas familiarizado con estos temas. Asi que empecemos!

#### Primero, añadimos las gemas
Primero vamos a abrir el fichero Gemfile de nuestra lección anterior. Este fichero contiene una lista de todo el software que tu proyecto esta utilizando. Como puedes ver, incluso Rails esta en esta lista de dependencias.

Al final del fichero vamos a crear un grupo de gemas que solo están disponibles para desarrollo y tests.

Las tres gemas que vamos a incluir son:
- rspec rails
- cucumber rails
- database_cleaner

El número que escribimos detrás de cada gema indica la version que queremos utilizar.
Para asegurar que tienes exáctamente la misma experiencia que te enseñamos en este video, asegurare de utilizar exáctamente las mismas versiones que tenemos aquí.
```
group :development, :test do
  gem 'rspec-rails', '3.0.2'
  gem 'cucumber-rails', '1.4.1'
  gem 'database_cleaner', '1.3.0'
end
```

Ahora, guardamos el fichero.

Después de modificar el Gemfile tenemos que ejecutar un comando para comprobar y descargar las nuevas dependencias de nuestro proyecto.

Así que abrimos una terminal y ejecutamos:
```
bundle install
```

Bundler descargará las 3 nuevas dependencias que hemos indicado y cualquier otra dependencia que estas necesiten y las instalará para que nuestra aplicación las pueda utilizar.

#### Ahora instala Cucumber - esto generarà los ficheros necesarios para ejecutar los escenarios.

Una vez que bundler haya terminado de instalar nuestras gemas, tenemos que completar un solo paso especial que Cucumber requiere para su instalación.
En la terminal escribe:
```
rails generate cucumber:install
```

Y pulsa enter.

Cucumber crea los archivos y carpetas necesarios para nuestro proyecto y ahora podemos verificar que todo está instalado correctamente.

Para ello, vamos a ejecutar

#### Asegurate de que funciona
```
bundle exec rake cucumber
```

No vemos ningún error. La salida de la terminal menciona que hay cero escenarios con cero pasos, y eso es completamente cierto porque no hemos creado ninguno todavía.
```
Using the default profile...
0 scenarios
0 steps
0m0.000s
```

#### Escribir nuestro primer escenario declarativo
Ahora es el momento de escribir nuestro primer escenario.

Crea un fichero en el directorio /features / llamado dashboards.feature

En ese fichero, vamos a empezar con la palabra clave Feature.  Y escribimos el nombre de esta funcionalidad.
En la siguiente linea, identamos el texto y escribimos una pequeña historia explicando lo que queremos hacer.

"Para ver rápidamente la actividad actual, como usuario accedo a un panel de control central"
```
In order to quickly see current activity
A user
Should access a central dashboard
```

Esta es la funcionalidad que queremos comprobar en este fichero. Ahora, tenemos que escribir un escenario que pone a prueba esta funcionalidad.

Escribimos un escenario simple cubriendo el requisito de que el usuario puede ver el dashboard.

Cuando visito la url raíz
Entonces la respuesta debe ser el dashboard central.

Eso es todo.
```
Feature: Dashboard
  In order to quickly see current activity
  A user
  Should access a central dashboard

  Scenario: Access the dashboard
    When I visit the root url
    Then the response should be the central dashboard
```

Nuestro siguiente paso es ejecutar otra vez el comando cucumber. En la terminal, ejecuta:
```
bundle exec rake cucumber
```

#### Añadiendo ficheros de pasos (steps files)

Esta vez, el resultado es diferente. Cucumber intentó ejecutar el escenario, pero no sabía como ejecutar los pasos que escribimos.

Afortunadamente, Cucumber ha sugerido código que podemos utilizar para definir estos pasos.

Copia los pasos sugeridos en la terminal, crea un fichero llamado dashboard.rb en nuestro directorio "features/step_definitions/", y pega el contenido sugerido allí.

Guarda el fichero, y  ejecuta cucumber de nuevo para ver la diferencia:
```
bundle exec rake cucumber
```

#### Modificando los pasos

Esta vez, cucumber encuentra las definiciones de los pasos pero estas están vacias porque no las hemos personalidado para comprobar exactamente lo que queremos que hagan.

Ahora todo lo que tenemos que hacer es escribir un poco de código dentro de las definiciones.


En nuestro primer paso, simplemente queremos visitar la url raíz de nuestra aplicación.

En el segundo paso, queremos verificar que la respuesta HTML tiene un cierto contenido que podamos identificar como el panel(dashboard).
Por ahora vamos a decir que esperamos que la página tenga el contenido 'Dashboard'
```
When(/^I visit the root url$/) do
  visit root_path
end

Then(/^the response should be the central dashboard$/) do
  expect(page).to have_content 'Dashboard'
end
```

Vamos a ejecutar cucumber de nuevo.

bien!, pasan.  Pero vamos a asegurarnos de que no estamos siendo engañados -
Vamos a ver si realmente está poniendo a prueba el contenido de la página
Podemos hacer esto cambiando 'Dashboard' a algo que sabemos que no está en esa página como 'RailsMania '

Ejecutamos cucumber otra vez y vemos que el escenario falla, incluso vemos que se esperaba ‘railsmania’ pero que no se ha encontrado en todo el HTML.

Perfecto, hemos dejado listo nuestro proyecto para empezar a desarrollar guiados por comportamiento. En la próxima lección vamos a mostrar el escenario con la funcionalidad antes de escribir el código que la implementa.

Si quieres aprender más sobre cómo funciona Cucumber y sus muchas características poco conocidas, echa un vistazo a la lección Cucumber en profundidad en RailsMania.com!

Gracias por tu atención y hasta la próxima
