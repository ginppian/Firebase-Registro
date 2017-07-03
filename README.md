Firebase Registro Asistentes
=============

## Descripción

<p align="justify">
	El presente módulo se realizo contar el tiempo de los estudiantes en los congresos de la <i>Facultad de Estomatología BUAP</i>. El sistema obtiene la hora de <b>entrada</b>, la almacena, obtiene la hora de <b>salida</b>, es capaz de obtener la <i>diferencia</i> de horas y <i>acumularlas</i>.
</p>

<p align="center">
	<img src="https://github.com/ginppian/Firebase-Registro/blob/master/imgs/img5.png" width="1280" height="640">
</p>

## Técnico

<p align="justify">
	El sistema recibe una matricula de 6 dígitos y hace uso de la <i>API Google Firebase</i> para almacenar la información a través de <i>Web Sockets</i> en una Base de Datos <i>NO Relacional</i>. La API ya ofrece TODO a través de tu cuenta personal de <i>Google</i> de manera gratuita y no hay que preocuparse por <i>configurar</i> nada, sólo programar tu <i>APP</i>. El lenguaje con el que construimos la aplicación es <i>Javascript</i>
</p>

<p align="justify">
<i>PS:</i> La app <b>no necesitar estar alojada en un servidor</b> se puede correr desde local en <i>index.html</i> 
</p>

## Código

* <i>Existe Usuario</i>

```Javascript
function existeUsuario(id){
  var rootRef = firebase.database().ref();
  var usersRef = rootRef.child("users");
  usersRef.child(id).once('value', function(snapshot) {
    var exist = (snapshot.val() !== null);


  });
}
```

* <i>Escribir Información Usuario</i>

```Javascript
function writeUserData(id) {
    //alert("writeUserData");
    var now = new Date(Date.now());
    var formatted = now.getHours() + ":" + now.getMinutes() + ":" + now.getSeconds();

    $('#result').html('<p class="status_blue">Registrado...<br>Matricula: '+id+'<br> Hra. Entrada:'+formatted+'</p>');

    firebase.database().ref('users/'+id).set({
    horaEntrada: formatted,
    matricula: id,
    totalHoras: "00:00:00"
  });
}
```

* <i>Actualiza Información Usuario y Obtiene la DIFERENCIA de Hrs</i>

```Javascript
function updateUserData(id){
  //alert(firebase.database().ref('users/'+id));

  var adaNameRef = firebase.database().ref('users/'+id);
  adaNameRef.once('value').then(function(snapshot) {

    var bandera = snapshot.val().horaEntrada;
    if(bandera == "-1"){
      //alert("es -1 Entrando...");
      //Obtenemos la hora actual
      var now = new Date(Date.now());
      var formatted = now.getHours() + ":" + now.getMinutes() + ":" + now.getSeconds();

      $('#result').html('<p class="status_green">Entrando...'+'<br>'+'Hra: '+formatted+'<br> Matricula: '+id+'</p>');

      //Updateamos
      adaNameRef.update({
        horaEntrada: formatted
      });

    }else {
      //alert("Salidendo...");

      //Obtenemos la hora de entrada
      var horaEntrada = snapshot.val().horaEntrada;
      //alert("horaEntrada: "+ horaEntrada);

      //Obtenemos la hora actual
      var now = new Date(Date.now());
      var formatted = now.getHours() + ":" + now.getMinutes() + ":" + now.getSeconds();
      console.log(formatted);

      var hora1 = (formatted).split(":"),
          hora2 = (horaEntrada).split(":"),
          t1 = new Date(),
          t2 = new Date();

      console.log(hora1);
      console.log(hora2);

      t1.setHours(hora1[0], hora1[1], hora1[2]);
      t2.setHours(hora2[0], hora2[1], hora2[2]);

      //Hacemos la diferencia
      t1.setHours(t1.getHours() - t2.getHours(), t1.getMinutes() - t2.getMinutes(), t1.getSeconds() - t2.getSeconds());

      //Guardamos el resultado
      var horasAcumuladas = t1.getHours() + ":" + t1.getMinutes() + ":" + t1.getSeconds();
      //alert("horasAcumuladas: "+horasAcumuladas);

      //Obtenemos las horas acumuladas
      var totalHoras = snapshot.val().totalHoras;
      //alert("totalHoras: "+ totalHoras);

      var hora3 = (totalHoras).split(":"),
          hora4 = (horasAcumuladas).split(":"),
          t3 = new Date(),
          t4 = new Date();

      console.log(hora3);
      console.log(hora4);

      t3.setHours(hora3[0], hora3[1], hora3[2]);
      t4.setHours(hora4[0], hora4[1], hora4[2]);

      //Hacemos la suma
      t3.setHours(t3.getHours() + t4.getHours(), t3.getMinutes() + t4.getMinutes(), t3.getSeconds() + t4.getSeconds());

      //Guardamos el resultado
      var newTotalHoras = t3.getHours() + ":" + t3.getMinutes() + ":" + t3.getSeconds();
      //alert("newTotalHoras: "+newTotalHoras);

      $('#result').html('<p class="status_red">Saliendo...'+'<br>'+'Hra: '+formatted+'<br> Matricula: '+id+'<br> Total: '+newTotalHoras+'</p>');

      //Updateamos
      adaNameRef.update({
        horaEntrada: "-1",
        totalHoras: newTotalHoras
      });
    }
  });
}
```

* <i>Guarda Información Usuario</i>

```
function enviarDatos(){
  var caja = document.getElementById("cajaTexto");
  var matricula = $('#cajaTexto').val();
  $('#cajaTexto').val('');
  console.log(matricula);

  //1.- Obtenemos el valor de la caja de texto
  //matricula = matricula.substr(1, 9);
  //console.log("matricula: " + matricula);
  //2.- Aseguramos que la MATRICULA tenga la LONGITUD correcta
  if(matricula.length == 6)
  {
    //existeUsuario(matricula);
    var rootRef = firebase.database().ref();
    var usersRef = rootRef.child("users");
    usersRef.child(matricula).once('value', function(snapshot) {
      var exist = (snapshot.val() !== null);
      //alert("exist"+exist);
      if(exist){
        updateUserData(matricula);
      } else {
        writeUserData(matricula);
      }
    });

  } else {

    $('#cajaTexto').val('');
    $('#result').html('<p class="warning">Ingrese matricula válida de 6 digitos.</p>');

    $('#cajaTexto').focus();
  }
}
```

<p align="center">
	<img src="https://github.com/ginppian/Firebase-Registro/blob/master/imgs/img1.png" width="1280" height="740">
</p>

<p align="center">
	<img src="https://github.com/ginppian/Firebase-Registro/blob/master/imgs/img2.png" width="1280" height="740">
</p>

<p align="center">
	<img src="https://github.com/ginppian/Firebase-Registro/blob/master/imgs/img3.png" width="1280" height="740">
</p>

<p align="center">
	<img src="https://github.com/ginppian/Firebase-Registro/blob/master/imgs/img4.png" width="1280" height="740">
</p>

<p align="center">
	<img src="https://github.com/ginppian/Firebase-Registro/blob/master/imgs/img5.png" width="1280" height="740">
</p>

<p align="center">
	<img src="https://github.com/ginppian/Firebase-Registro/blob/master/imgs/img6.png" width="1280" height="740">
</p>

## Conclusión

La plataforma de <i>Firebase</i> es realmente muy sencilla de implementar. Muy útil para proyectos <i>simples</i> que requieran procesos de tiempo real como: un <i>registro</i>, un <i>chat</i>, consultar información dónde sabemos de andemos sus  <i>IDs</i> etc.

## Contacto

Twitter: @ginppian