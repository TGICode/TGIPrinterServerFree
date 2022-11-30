# TGIPrinterServerFree (beta)

TGIPrinterServer es un proyecto para crear un servidor de impresión que correrá sobre Windows y que permitirá imprimir directamente desde una página web.

Este servidor no está pensado para webs de propósito general, el propósito de este proyecto es el de proveer un mecanismo para aquellas aplicaciones más profesionales, como aplicaciones de facturación o de gestión, en las que se requiere la impresión de informes, facturas, listados, etc.

El programa es una aplicación que se instalará en el área de notificación o bandeja del sistema y permitirá configurar la o las impresoras que deseemos utilizar en nuestra aplicación. Por otro lado, actuará como un servidor que recibirá comandos desde nuestra app para realizar la impresión.

El primer paso consistirá en descargar el servidor y copiarlo en nuestro ordenador (aún no hemos implementado un instalador).

Una vez copiado deberemos ejecutarlo. La app aparecerá en la bandeja del sistema. Haciendo doble click sobre el icono se abrirá la UI del programa y podremos configurar las impresoras, el puerto de escucha y si deseamos que se ejecute automáticamente al entrar en Windows.

Se podrá configurar una impresora por defecto y tantas impresoras como se desee y que estén presentes en el sistema. A cada impresora se le asociará un alias por el que será identificada en nuestra app web.

Cuando nuestro servidor esté configurado y en ejecución, ya podremos enviar comandos desde nuestra aplicación web.

## Ejemplo 1. Recuperar lista de impresoras
### Javascript
```
<html>
  <script type="text/javascript">
    function Enviar(){
      let Tarea = {
        command: "getPrintersList"
      }

      fetch('http://127.0.0.1:7500', {
        method: "POST",
        body: encodeURI(JSON.stringify(Tarea))
      })
      .then(response => response.json()) 
      .then(json => console.log(json))
      .catch(err => console.log(err));
    }
  </script>
<body>
  <button onclick="Enviar()"> Prueba javascript</button>
</body>
</html>
```

### jQuery
```
<html>
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.1/jquery.min.js"></script>
  <script type="text/javascript">
    function Enviar(){
      let Tarea = {
        command: "getPrintersList"
      }

        $.ajax({
            url: "http://127.0.0.1:7500",
            method: "POST",
            data: encodeURI(JSON.stringify(Tarea)),
            success: function (result) {
              console.log(JSON.parse(result));
            },
            error: function(jqXHR, textStatus, errorThrown) {
               console.log(textStatus, errorThrown);
            }
          });
    }
  </script>
<body>
  <button onclick="Enviar()"> Prueba javascript</button>
</body>
</html>
```

### Angular
```
Enviar(){
  let Tarea = {
    command: "getPrintersList"
  }

  this._http.post('http://127.0.0.1:7500', encodeURI(JSON.stringify(Tarea)))
  .subscribe({
    next:  (data:any) => {
      if(data.res != 'OK'){
        alert("Error en los datos");
      }
      console.log(data);
    },
    error: (error: HttpErrorResponse) =>{
      alert("Ha ocurrido un error al comunicar con el servidor de impresión");
      console.log(error)
      return null;
    }
  })
}
```

## Ejemplo 2. Impresión de un texto y una línea
### Javascript
```
function Enviar(){
  Tarea = {
          command: "printJob",
          data: {
            "name":"Trabajo de impresión",
            "commands": [
              {
                command: "setFont",
                name: "Times New Roman",
                height: 3,
                color: "FF0000",
                bold: true
              },
              {
                command: "writeText",
                text: "Hola mundo",
                x: 10,
                y: 10
              },
              {
                command: "setPen",
                widthPx: 1,
                color: "0000FF"
              },
              {
                command: "moveTo",
                x: 10,
                y: 14
              },
              {
                command: "lineTo",
                x: Ancho-10,
                y: 14
              }
            ]
          }
        }
    fetch('http://127.0.0.1:7500', {
    method: "POST",
    body: encodeURI(JSON.stringify(Tarea))
  })
  .then(response => response.json()) 
  .then(json => console.log(json))
  .catch(err => console.log(err));
}
```

Puede descargar la lista de comandos para la versión free en el link siguiente
[Especificaciones del servidor de impresión TGIPrinterServer (Free).pdf](https://github.com/TGICode/TGIPrinterServerFree/files/10123190/Especificaciones.del.servidor.de.impresion.TGIPrinterServer.Free.pdf)
