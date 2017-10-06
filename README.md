# spamkiller
Ciertos cuestiones para mejorar Spamassassin en Zimbra en español

## Instroducción
En los siguientes ficheros se definen reglas personalizadas para que ampliar la capacidad de Spamassassin para identificar phishing por medio de su contenido, sobre todo cuando este sea en español.

La idea es separar todo en diferentes ficheros para que sea más sencillo navegar en ellos. Considere dos cosas:
* El orden es importante. De allí que tengan un número correlativo al principio de su nombre
* El orden en importante, entre otras cosas, porque algunas son reglas compuestas (reglas tipo *meta* en la terminología de Spamassassin) de reglas que están en otros ficheros

Sobre el puntuaje de las reglas, considere:
* Las reglas de contenido suelen escribirse para buscar contenido en expresiones regulares. Es mala idea buscar una palabra, hará que muchos correos válidos se marquen como spam.
* Las reglas de contenido suelen tener puntuajes bajos. A veces pueden no tenerlos
* Existen ciertas reglas con un sufijo *_ADV*. Estas pueden expresar contenido especialmente pernicioso o que se considere definitivamente SPAM. 

Sobre la forma de escribir reglas, considere:
* Las reglas de contenido en forma de expresiones regulares necesitan escribirse de la forma */contenido/i*, donde el modificar *i* permite que se evalúe tanto mayúsculas como minúsculas

Por ahora, el contenido de los ficheros va de la siguiente forma:

### 01\_minsal.cf
Cargas los plugins personalizados
Tiene reglas compuestas con algunas reglas que nativas de Spamassassin

### 021\_minsal\_phishing.cf
Reglas que verifican el contenido de algunas cabeceras del correo. Suceden en dos fases: 
* Reglas de tipo header que precisamente buscan por cierto contenido, sobre todo en *subject*. Suelen mantener un puntaje bajo: 0.1 es lo recomendable
* Reglas de tipo meta que combinan contenido anterior. Estas suelen tener mayores puntajes, nunca mayor a 1.0

### 022\_minsal\_phishing.cf
Reglas que verifican el contenido en el cuerpo del correo.
Contenido que se refiera a RAZONES, es decir, el motivo por el cual están enviando este correo. Suele tratarse de algo técnico relacionado a la administración del servicio de correo que asusta al usuario: *"Superado el límite de almacenamiento"*, *"Haciendo un mantenimiento a nuestros servidores"*

### 023\_minsal\_phishing.cf
Reglas que verifican el contenido en el cuerpo del correo.
Contenido que se refiera a DETALLES, es decir, pequeñas frases que refuerzan la idea de RAZONES, causando mayor alarma (A veces de hecho sólo terminan de darle sentido a todo, so): *"definido por administrador"*, *"mensajes pendientes"*

### 024\_minsal\_phishing.cf
Reglas que verifican el contenido en el cuerpo del correo.
Contenido que se refiere a OBJETIVOS, es decir, la acción que el usuario debe hacer para resolver su problema: *"Aumentar su capacidad de almacenamiento"*, *"Actualizar la versión de Zimbra"*

### 025\_minsal\_phishing.cf
Reglas que verifican el contenido en el cuerpo del correo.
Contenido que se refiere a AMENAZAS, es decir, una sentencia fatídica para la cuenta en cuestión: *"No podrá enviar o recibir correo"*, *"Error cuando se envía el mensaje"*

### 026\_minsal\_phishing.cf
Reglas que verifican el contenido en el cuerpo del correo.
Contenido que se refiere a MEDIANTES, es decir, acciones por las que el usuario logra el objetivo de resolver su problema: *"Haga click en el siguiente enlace"*, *"Copie y pegue en su navegador"*

### 027\_minsal\_phishing.cf
Reglas que verifican el contenido en el cuerpo del correo.
Son distintos tipos de frases que llamamos CONTENIDO, es decir, dan más sentido y cuerpo al mensaje: El hecho que el texto sea más extenso suele dar una mejor impresión. 
Difiere de DETALLES en que este refuerza la idea de RAZONES, mientras que CONTENIDO es casi siempre palabrería, que sin embargo, suele ser tanto o más importante a la hora de configurar una estafa

### 028\_minsal\_phishing.cf
Son reglas de tipo *meta*, es decir, reglas compuestas
Estas reglas son llamadas COMPORTAMENTALES y se supone que son capaces de describir una estafa en un correo. Por ejemplo, cuando una correo es marcado como ZT\_COMPORTAMENTAL\_\*,básicamente estamos ante un spam. La naturaleza de las reglas permite que ningún correo válido entre a esta categoría. 

## Instalación
git clone git@github.com:VTacius/spamkiller.git /opt/zimbra/conf/sa

## ¿Modificarlas? 
Para agregar reglas y verificar su funcionamiento, es recomendable hacerlo contra correo conocido como spam.
```bash
/opt/zimbra/zimbramon/bin/spamassassin -D -L -C /opt/zimbra/conf/spamassassin/ -C /opt/zimbra/conf/sa < /opt/zimbra/tareas/mejora\_antispam/94-advertencia\_de\_cuenta
```

En realidad, pueden usarse contra cualquier instalación de spamassassin que tenga las reglas básicas que vengan con él
