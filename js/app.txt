$(document).on('deviceready',function(){
    var deviceType = (navigator.userAgent.match(/iPad/i))  == "iPad" ? "iPad" : (navigator.userAgent.match(/iPhone/i))  == "iPhone" ? "iPhone" : (navigator.userAgent.match(/Android/i)) == "Android" ? "Android" : (navigator.userAgent.match(/BlackBerry/i)) == "BlackBerry" ? "BlackBerry" : "null";
    if(deviceType=="Android"){
        $(".btnBack").hide();
    }

    $(".ui-page-active").css("min-height","95% !important;");

    $(document).on('focus','input', function() {
      setTimeout(function() {
        $(".footer").hide();
      }, 800);
    });

   $(document).on('blur','input', function() {
      setTimeout(function() {
        $(".footer").show();
      }, 0);
   });

    $.ajax({
      url: "login.html",
      success: function(result) {
          $("#contenido").html(result).trigger("create");
          $("#curp").val(localStorage.getItem("CURP"));
          footer();
      }
    });

    $("#contenido").on("click", ".btnEnviar" ,function(){
        curp = $("#curp").val();
        longitud = curp.length;
        if(curp==""){
            navigator.notification.alert('Debe llenar el campo CURP',alertDismissed,'Error', 'Ok');
            return false;
        }else{
            if(longitud!=18){
                navigator.notification.alert('El campo CURP debe contener 18 caracteres',alertDismissed,'Error', 'Ok');
                return false;
            }else{
                localStorage.setItem("CURP", curp);
                $.ajax({
                  url: "menu.html",
                  success: function(result) {
                        $("#contenido").html(result).trigger("create");
                  }
              });                
            }
        } 
    });

    $("#contenido").on("click", ".calendario" ,function(){
      $.ajax({
          url: "calendario.html", 
          success: function(result) {
                $("#contenido").html(result).trigger("create");
          }
      });
    });

    $("#contenido").on("click", ".certificado" ,function(){
      $.ajax({
          url: "certificado.html", 
          success: function(result) {
                $("#contenido").html(result).trigger("create");
                $("#txtCurp").val(localStorage.getItem("CURP"));
          }
      });
    });

    $("#contenido").on("click", ".calificaciones" ,function(){
      $.ajax({
          url: "calificaciones.html", 
          success: function(result) {
                $("#contenido").html(result).trigger("create");
                $("#txtCurp").val(localStorage.getItem("CURP"));
          }
      });
    });

    $("#contenido").on("change", "#cmbNivel" ,function(){
    	nivel = $(this).val();
    	if(nivel=="SECUNDARIA"){
    		$("#cmbGrado").html("<option value='1'>1</option><option value='2'>2</option><option value='3'>3</option>");
    	}else{
    		$("#cmbGrado").html("<option value='1'>1</option><option value='2'>2</option><option value='3'>3</option><option value='4'>4</option><option value='5'>5</option><option value='6'>6</option>");
    	}
    });

    $("#contenido").on("click", ".cCertificado" ,function(){
        curp = $("#txtCurp").val();
        ciclo = $("#cmbCiclo").val();
        longitud = curp.length;
        if(curp==""){
            navigator.notification.alert('Debe llenar el campo CURP',alertDismissed,'Error', 'Ok');
            return false;
        }else{
            if(longitud!=18){
                navigator.notification.alert('El campo CURP debe contener 18 caracteres',alertDismissed,'Error', 'Ok');
                return false;
            }else{
                $.mobile.loading( 'show', {
                  text: 'Por favor espere..',
                  textVisible: true,
                  theme: 'b'
                });
                $.ajax({
                  //url : 'http://cpte.gob.mx/peie/app/data.php',
                  url : 'http://cpte.gob.mx/calificaciones/vista/pdf/CERTIFICADO_2017.php?CURP='+curp+'&CICLO='+ciclo+'&APP=SI',
                  type : 'GET',
                  //contentType: "test/plain",
                  contentType: "application/json; charset=utf-8",
                  dataType : 'json',
                  //dataType : 'text',
                  data: {curp: curp, ciclo: ciclo},
                  success : function(data, textStatus, jqxhr){ 
                    console.log('http://cpte.gob.mx/calificaciones/vista/pdf/CERTIFICADO_2017.php?CURP='+curp+'&CICLO='+ciclo+'&APP=SI');
                    console.log(data);
                    if(data.exito==1){
                      $.mobile.loading('hide');
                      //window.open('http://cpte.gob.mx/appCalificaciones/vista/pdf/CERTIFICADO.php?CURP='+curp+'&CICLO='+ciclo, '_system', 'location=yes')
                      window.open('https://docs.google.com/gview?url='+data.url, '_blank', 'location=yes')
                    }else{ 
                    											/*CONSULTA EN EL SEER*/
                      /*$.mobile.loading('hide');
                      navigator.notification.alert('Certificado no encontrado',alertDismissed,'Error', 'Ok');*/
                      $.ajax({		                  
		                  url : 'https://seer.gob.mx/peie/alumnos/consulta_certificado.php?curp='+curp+'&ciclo='+ciclo+'&APP=SI',
		                  type : 'GET',
		                  //contentType: "test/plain",
		                  contentType: "application/json; charset=utf-8",
		                  dataType : 'json',
		                  //dataType : 'text',
		                  data: {curp: curp, ciclo: ciclo},
		                  success : function(data, textStatus, jqxhr){ 
		                    console.log('https://seer.gob.mx/peie/alumnos/consulta_certificado.php?curp='+curp+'&ciclo='+ciclo+'&APP=SI');
		                    console.log(data);
		                    if(data.exito==1){
		                      $.mobile.loading('hide');
		                      //window.open('http://cpte.gob.mx/appCalificaciones/vista/pdf/CERTIFICADO.php?CURP='+curp+'&CICLO='+ciclo, '_system', 'location=yes')
		                      window.open('https://docs.google.com/gview?url='+data.url, '_blank', 'location=yes')
		                    }else{
		                      $.mobile.loading('hide');
		                      navigator.notification.alert('Certificado no encontrado',alertDismissed,'Error', 'Ok');
		                    }                              
		                  },
		                  error : function (jqxhr, textStatus, errorMessage){ 
		                  	  console.log('https://seer.gob.mx/peie/alumnos/consulta_certificado.php?curp='+curp+'&ciclo='+ciclo+'&APP=SI');
		                      console.log(errorMessage);              
		                  }               
		              });
                    }                              
                  },
                  error : function (jqxhr, textStatus, errorMessage){ 
                      console.log(errorMessage);              
                  }               
              });                
            }
        }
    });

    $("#contenido").on("click", ".cCalificaciones" ,function(){
        curp = $("#txtCurp").val();
        nivel = $("#cmbNivel").val();
        grado = $("#cmbGrado").val();
        longitud = curp.length;
        if(curp==""){
            navigator.notification.alert('Debe llenar el campo CURP',alertDismissed,'Error', 'Ok');
            return false;
        }else{
            if(longitud!=18){
                navigator.notification.alert('El campo CURP debe contener 18 caracteres',alertDismissed,'Error', 'Ok');
                return false;
            }else{
                $.mobile.loading( 'show', {
                  text: 'Por favor espere..',
                  textVisible: true,
                  theme: 'b'
                });
                $.ajax({
                  url : 'http://cpte.gob.mx/peie/app/data.php',
                  type : 'GET',
                  contentType: "application/json; charset=utf-8",
                  //dataType : 'json',
                  data: {curp: curp, nivel: nivel, grado: grado},
                  success : function(data, textStatus, jqxhr){ 
                    console.log(data);
                    if(data==1){
                      $.mobile.loading('hide');
                      window.open('http://cpte.gob.mx/peie/MAGI/controlador/pdf.php?GRADO='+grado+'&CURP='+curp+'&NIVEL='+nivel, '_system', 'location=yes')
                    }else{
                    											/*CONSULTA AL SEER*/
                      /*$.mobile.loading('hide');
                      navigator.notification.alert('Datos y/o Boleta no encontrados',alertDismissed,'Error', 'Ok');*/
                      $.ajax({       
		                  url : 'https://seer.gob.mx/peie/alumnos/consulta_padres_2.php?curp='+curp+'&nivel='+nivel+'&grado='+grado+'&APP=SI',
		                  type : 'GET',
		                  //contentType: "test/plain",
		                  contentType: "application/json; charset=utf-8",
		                  dataType : 'json',
		                  //dataType : 'text',
		                  data: {seer:'1'},
		                  success : function(data, textStatus, jqxhr){ 
		                    console.log('https://seer.gob.mx/peie/alumnos/consulta_padres_2.php?curp='+curp+'&nivel='+nivel+'&grado='+grado+'&APP=SI');
		                    console.log(data);
		                    if(data.exito==1){
		                      $.mobile.loading('hide');
		                      //window.open('http://cpte.gob.mx/appCalificaciones/vista/pdf/CERTIFICADO.php?CURP='+curp+'&CICLO='+ciclo, '_system', 'location=yes')
		                      window.open('https://docs.google.com/gview?url='+data.url, '_blank', 'location=yes')
		                    }else{
		                      $.mobile.loading('hide');
		                      navigator.notification.alert('Certificado no encontrado',alertDismissed,'Error', 'Ok');
		                    }                              
		                  },
		                  error : function (jqxhr, textStatus, errorMessage){ 
		                  	  console.log('https://seer.gob.mx/peie/alumnos/consulta_certificado.php?curp='+curp+'&ciclo='+ciclo+'&APP=SI');
		                      console.log(errorMessage);              
		                  }               
		              });                 
                    }                              
                  },
                  error : function (jqxhr, textStatus, errorMessage){ 
                      console.log(errorMessage);              
                  }               
              });                
            }
        }
    });

    $("#contenido").on("click", ".avisos" ,function(){
      $.ajax({
          url: "avisos.html", 
          success: function(result) {                
              $("#contenido").html(result).trigger("create");
              $.mobile.loading( 'show', {
                text: 'Por favor espere..',
                textVisible: true,
                theme: 'b'
              });
              curp = localStorage.getItem("CURP");
              //alert(curp);
              //console.log(curp);
              $.ajax({
                  url : 'http://cpte.gob.mx/peie/app/data.php',
                  type : 'GET',
                  contentType: "application/json; charset=utf-8",
                  dataType : 'json',
                  data: {curp: curp},
                  success : function(data, textStatus, jqxhr){ 
                    //alert(data);
                    //console.log(data);
                    if(data!=null){
                      if(data.length > 0){
                        for (var x = 0; x < data.length; x++) {
                            fecha = data[x].fecha;
                            aviso = data[x].aviso;
                            departamento = data[x].departamento;

                            $(".ui-content").append("<div class='contenedor'><div class='head'>"+fecha+"</div><div class='dentro'>"+aviso+"</div><div class='foo'>"+departamento+"</div>");
                        }   
                        $.mobile.loading('hide'); 
                      }
                    }
                  	else{
                      $.mobile.loading('hide'); 
                  		navigator.notification.alert('No hay avisos nuevos para mostrar',alertDismissed,'Aviso', 'Ok');
                  		$.ajax({
			                url: "menu.html",
			                success: function(result) {
			                    $("#contenido").html(result).trigger("create");
			                }
			            });
                  	}                               
                  },
                  error : function (jqxhr, textStatus, errorMessage){ 
                      console.log(errorMessage);              
                  }               
              });
          }
          });
    });

    $("#contenido").on("click", ".btnBack" ,function(){
        $.ajax({
            url: "menu.html",
            success: function(result) {
                  $("#contenido").html(result).trigger("create");
            }
        });
    });

    document.addEventListener("backbutton", function(e){
        active = activePage();
        if(active==('index')){
            e.preventDefault(); 
            navigator.notification.confirm(
                "Realmente desea salir de la app?",
                function (button) {
                  if (button==2) {
                    navigator.app.exitApp();
                  }
                },"Pregunta", ["Cancelar","Ok"]
            );
        }
        if(active==('menu')){
            $.ajax({
                url: "login.html",
                success: function(result) {
                    $("#contenido").html(result).trigger("create");
                }
            });
        }
        if(active==('calendario') || active==('avisos') || active==('certificado') || active==('calificaciones')){
            $.ajax({
                url: "menu.html",
                success: function(result) {
                    $("#contenido").html(result).trigger("create");
                }
            });
        }
    }, false);
});

function alertDismissed() {
    // do something
}

function activePage(){
    var active = $("#contenido div[data-role='page']").attr("id");
    return active;
}

function footer(){
    $(".ui-page-active").append("<div data-role='footer' data-position='fixed'><img class='imgBanner' src='img/calificacionesSLP.png'></div>");
}