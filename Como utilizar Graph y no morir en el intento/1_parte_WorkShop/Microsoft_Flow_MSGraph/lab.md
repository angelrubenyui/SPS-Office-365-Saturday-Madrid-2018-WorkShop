# Como utilizar Graph y no morir en el intento

En este laboratorio crearemos un conector personalizado que haga llamadas a Microsoft Graph API y que luego podamos utilizar en Flow y Power Apps

## Prerequisitos

1. Debes tener un tenant Office 365 para completar este laboratorio. 
1. Visual Studio 2017

## Registrar la aplicación en Azure AD

1. Ir al portal de Azure https://portal.azure.com y loguearte con un usuario con permisos para registrar aplicaciones
1. En el menú accede a Azure Active Directory
1. Pulsar sobre Registro de aplicaciones

![Screenshot](media/registroAplicaciones.png)

1. Pulsar sobre Nuevo registro de aplicaciones

![Screenshot](media/nuevoRegistroAplicaciones.png)

1. Indicar:
- Nombre: Nombre de la aplicación
- Tipo de aplicación: Aplicación web o API
- Url de inicio de sesión: una url con formato válido, no es necesario que sea accesible

![Screenshot](media/crearRegistroAplicacion.png)

1. Pulsa el botón Crear para registrar la aplicación
1. Una vez registrada la aplicación, apunta el Id de aplicación, lo necesitaremos más adelante

![Screenshot](media/idAplicacion.png)

1. Una vez registrada la aplicación, debemos darle permisos para acceder a Microsoft Graph, para ello, pulsa en Configuración y accede a Permisos necesarios

 ![Screenshot](media/permisosNecesarios.png)

1. En la pantalla de Permisos necesarios pulsa sobre Agregar

 ![Screenshot](media/guardarPermisosNecesarios.png)

1. Pulsa sobre Seleccionar una API y en la siguiente pantalla seleccionamos Microsoft Graph y pulsamos sobre Seleccionar

![Screenshot](media/seleccionarAPI.png)

1. Una vez seleccionado Microsoft Graph debemos especificar que permisos necesitamos, para ello pulsamos sobre Seleccionar permisos y nos mostrará los permisos sobre los que podemos habilitar el acceso

![Screenshot](media/seleccionarPermisosAPI.png)

1. Para nuestro ejemplo seleccionaremos
-	[TODO] Read user tasks
1. Una vez hayamos seleccionado los permisos pulsamos sobre Listo para guardar los cambios.
1. Ahora necesitamos generar una clave para acceder al API, para en la pantalla de configuración de la aplicación pulsamos sobre Claves

![Screenshot](media/seleccionarClaves.png)

1. En la sección contraseñas añadimos una descripción para la contraseña y el tiempo de expiración

![Screenshot](media/anadirClaves.png)

1. La clave se generará una vez guardemos los cambios. En este momento apunta la clave que se genera porque es el único momento que podrás verla.

![Screenshot](media/guardarClaves.png)

## Crear Conector personalizado

1. Accede a Microsoft Flow e inicia sesión 
1. Pulsa sobre el icono ![image](media/iconoConfiguracion.png)  y en el menú selecciona Conectores personalizados, en esta pantalla se mostrará todos los conectores personalizados que hayas creado

![Screenshot](media/crearConector.png)

1. Pulsa sobre Crear conector personalizado y Crear desde cero

 ![Screenshot](media/crearDesdeCero.png)

1. En la ventana que aparece introduce el titulo del conector y pulsa sobre Continuar

 ![Screenshot](media/tituloConector.png)

1. En la siguiente pantalla podremos cargar el icono que queremos para el conector, color de fondo, etc. En la sección Esquema deberemos seleccionar HTTPS, como host indicaremos graph.microsoft.com y la URL Base ‘/’

 ![Screenshot](media/informacionGeneralConector.png)

1. Una vez hayamos introducido los datos del host pulsamos sobre Seguridad para pasar a la siguiente pantalla
1. En la siguiente pantalla nos solicita el Tipo de autenticación, en el desplegable seleccionaremos OAuth 2.0
1. Una vez hayamos seleccionado OAuth 2.0 nos muestra los datos de configuración de la autenticación. Deberemos introducir:
- Proveedor de identidades: Azure Active Directory
- Client id: id de la aplicación que hemos registrado en Azure AD
- Client secret: clave que hemos generado al registrar la aplicación en Azure AD
- Login url: https://login.windows.net
- Tenant ID: common 
- Resource URL: https://graph.microsoft.com
- Ámbito (opcional)

![Screenshot](media/autenticacionConector.png)
 
1. Pulsamos sobre Crear conector para generar la url de redireccionamiento, que necesitamos para la aplicación que hemos registrado en Azure AD

 ![Screenshot](media/urlRedireccionamiento.png)

1. Una vez generada la url de redireccionamiento, la copiamos y volvemos al portal de Azure. No cierres esta ventana ya que volveremos para terminar de configurar el conector
1. En la configuración de la aplicación que hemos registrado en Azure AD pulsamos sobre URL de respuesta

 ![Screenshot](media/urlRespuesta.png)

1. En la pantalla que se muestra añadimos la url que hemos obtenido del conector y pulsamos sobre guardar

 ![Screenshot](media/anadirUrlRespuesta.png)

1. Con esto ya tenemos registrada nuestra aplicación en Azure AD y creado nuestro conector. Lo único que nos queda es añadir las peraciones que necesitamos a nuestro conector.

## Añadir operaciones al conector

1.Dentro de la configuración del conector pulsamos sobre Definición y se muestra la pantalla para poder añadir Acciones y Desencadenadores a nuestro conector

 ![Screenshot](media/anadirAcciones.png)

1. Pulsamos sobre Nueva acción en el panel de la derecha.
1. En la siguiente pantalla nos solicita un nombre, descripción e identificador para la acción. En nuestro ejemplo obtendremos las tareas de un usuario.

 ![Screenshot](media/generalAcciones.png)

1. Hacemos scroll hacia abajo y vemos que nos solicita detalles sobre la consulta que tiene que realizar. Pulsaremos sobre Importar desde ejemplo

 ![Screenshot](media/importarSolicitud.png)

1. En el panel que se muestra introducir como verbo GET y la url https://graph.microsoft.com/v1.0/me/planner/tasks. Podéis probar las consultas en el explorador de Graph https://developer.microsoft.com/en-us/graph/graph-explorer, además es bueno ya que necesitaremos el cuerpo de la respuesta en el siguiente paso. Una vez que hayamos introducido la url de la consulta pulsar sobre Importar

 ![Screenshot](media/importarEjemplo.png)

1. Ahora debemos añadir agregar el formato de resupuesta. Para ello pulsamos sobre Agregar respuesta prederteminada en la sección Respuesta 

 ![Screenshot](media/agregarRespuesta.png)

1. Se nos muestra una pantalla para introducir el formato de la respuesta, podemos ejecutar la consulta en Graph Explorer y obtener la respuesta, o copiar 

````json
{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#Collection(microsoft.graph.plannerTask)",
    "@odata.count": 1,
    "@odata.nextLink": "https://graph.microsoft.com/v1.0/me/planner/tasks?$skiptoken=1%2523A%2b%2b8w28w4BhAUydElN2sKAo9ckAH4Cx",
    "value": [
        {
            "@odata.etag": "W/\"JzEtVGFzayAgQEBAQEBAQEBAQEBAQEBASCc=\"",
            "planId": "izWQ0k_J_U2sWsnatQ2cuMkACIZZ",
            "bucketId": "pkClhA3f50m83z_ZJG7LtMkANxhA",
            "title": "Disfrutar el SharePoint Saturda Madrid",
            "orderHint": "8586740023894722922",
            "assigneePriority": "8586740022099644192",
            "percentComplete": 0,
            "startDateTime": null,
            "createdDateTime": "2018-05-29T14:34:56.0052885Z",
            "dueDateTime": null,
            "hasDescription": false,
            "previewType": "automatic",
            "completedDateTime": null,
            "completedBy": null,
            "referenceCount": 0,
            "checklistItemCount": 0,
            "activeChecklistItemCount": 0,
            "conversationThreadId": null,
            "id": "8w28w4BhAUydElN2sKAo9ckAH4Cx",
            "createdBy": {
                "user": {
                    "displayName": null,
                    "id": "07f8ff51-0d1a-4c7f-adfa-1376a1047ee6"
                }
            },
            "appliedCategories": {},
            "assignments": {
                "07f8ff51-0d1a-4c7f-adfa-1376a1047ee6": {
                    "@odata.type": "#microsoft.graph.plannerAssignment",
                    "assignedDateTime": "2018-05-29T14:37:55.5131615Z",
                    "orderHint": "",
                    "assignedBy": {
                        "user": {
                            "displayName": null,
                            "id": "07f8ff51-0d1a-4c7f-adfa-1376a1047ee6"
                        }
                    }
                }
            }
        }
    ]
}
````

1. Una vez pulsemos sobre Importar se mostrará todos los elementos de salida  de nuestra consulta y que podremos utilizar en Flow

 ![Screenshot](media/importarRespuesta.png)

1. Pulsamos sobre Actualizar conector para guardar la acción que hemos creado

 ![Screenshot](media/actualizarConector.png)

1. Con esto ya tenemos creada la operación. Sólo nos queda crear un Flow para probar nuestro conector

## Probar el conector

1. Ir a Planner y añadir varias tareas asignándolas a nuestro usuario

 ![Screenshot](media/agregarTareas.png)

1. Vamos a la página de Flow y creamos un Flow nuevo, para ello vamos a Mis flujos y pulsamos sobre Crear desde cero

 ![Screenshot](media/nuevoFlow.png)

1. En la siguiente pantalla pulsamos sobre Crear desde cero

 ![Screenshot](media/crearFlow.png)

1. Lo que haremos es crear una tarea periódica que nos devuelva nuestras tareas, para ello, como desencadenador añadimos Programación – Periocidad e introducimos cada cuanto tiempo queremos que nos avise, si pulsamos sobre opciones avanzadas, podemos configurarlo con más precisión

 ![Screenshot](media/timmer.png)

1. Una vez configurado el timer pulsamos sobre Nuevo Paso y Agregar una acción

 ![Screenshot](media/agregarAccionFlow.png)

1. En la caja de texto buscamos el nombre que hemos puesto a nuestro conector

 ![Screenshot](media/agregarConectorFlow.png)

1. Una vez seleccionado el conector, seleccionamos la acción que queremos, en nuestro caso, Get my tasks

 ![Screenshot](media/agregadoConectorFlow.png)
