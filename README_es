🌍 **API de Predicción de Temperatura y Asistente Gemini sobre AEMET** 

Bienvenido al repositorio de la API de Predicción de Temperatura y Asistente Gemini. Este proyecto es un servicio web basado en FastAPI que combina un modelo  hibrido de Machine Learning para predecir la temperatura y un asistente virtual integrado para consultas de datos históricos.

La API se despliega en una instancia de AWS EC2 y utiliza PostgreSQL como base de datos para almacenar datos meteorológicos históricos. Los modelos de ML se cargan desde un bucket de S3 para una gestión de recursos eficiente.

**🚀 Características Principales**

    Predicción de Temperatura: Utiliza un modelo  híbrido que combina una pila de capas LSTM bidireccionales con una pila de bloques Transformer entrenado con pytorch para generar pronósticos de temperatura a futuro.

    Asistente Inteligente: Integra un asistente conversacional (basado en Gemini) que puede responder a preguntas sobre los datos históricos disponibles en la base de datos.

    Arquitectura Robusta: Los modelos de ML se cargan al inicio de la aplicación desde AWS S3, garantizando que el servicio esté siempre listo para responder sin demoras.

    API RESTful: Ofrece endpoints RESTful claros para la predicción de pronósticos y para interactuar con el asistente.


**🛠️ Prerrequisitos**

Para ejecutar este proyecto, necesitas tener instalados los siguientes componentes:

    -Python 3.9+

    -PostgreSQL (con una base de datos configurada y datos históricos)

 	-EC2( con clave .pem para su conexión SSH , grupo de seguridad con permisos de entrada y salida de trafico acorde a los puertos a utilizar)

  	-RDS(configurada para tráfico necesario)

    -Acceso a AWS (con credenciales configuradas para S3)

 	-Crear Lambdas con los archivos LambdaS3(para extraer datos de la API y guardarlos en S3) y lambdaBD(para guardar los datos extraidos en RDS)
  		<recuerde establecer las variables de entorno en la configuracion de las lambdas y las capas necesarias para las librerias>
  	

**⚙️Instalación y Configuración**

Sigue estos pasos para poner el proyecto en marcha en tu entorno local o en una instancia de EC2.


**1. Clonar el Repositorio**
   
	git clone <url_repositorio>
 
	cd <nombre_de_tu_repositorio>

**2. Otorgar permisos a tu clave .pem para permitir la conexion ssh a EC2**
   
   chmod 400 /ruta_a_su_clave_pem/nombre_clave.pem
   
**3. Conexion SSH a su instancia EC2**
   
   ssh -v -i '/ruta_a_su_clave_pem/nombre_clave.pem' ec2-user@<ip_publica_ec2>
   
   <recomiendo establecer su ip elástica para que su ip no sea volatil>
   
   <ec2-user cambia según la AMI (Amazon Machine Image) que uses para lanzar la instancia EC2>

**4. Configurar el Entorno Virtual y dependencias**
   
    sudo yum update -y
   
	sudo yum install -y python3 python3-pip git nginx awscli

	python3 -m venv venv
	source venv/bin/activate	


**5. Creación directorio**
   
    mkdir /home/ec2-user/fastapi_app
   
	cd /home/ec2-user/fastapi_app

**6. Descargar contenido desde Github o S3  e instalar dependencias**
   
	 -A. Copie los archivos necesarios directamente desde su repositorio de Github
   
   		<Creamos un repositorio vacio para la descarga selectiva>
   		git clone --no-checkout <URL_del_repositorio>
		cd <nombre_del_repositorio>
		git sparse-checkout init --cone

		<Descargamos el contenido de API>

		git sparse-checkout set API    
		git sparse-checkout set API/Main_production.py API/model_architecture.py API/model_production.py API/qa_production.py API/requirements.txt

   		<Limpiamos la configuración actual de sparse-checkout>

   		git sparse-checkout set --no-cone

   		<Descargamos el modelo y sus pesos desde el directorio streamlit>

   		git sparse-checkout set streamlit    
		git sparse-checkout set streamlit/production_weather_model.pth streamlit/scaler_X_production.joblib streamlit/scaler_y_production.joblib

		<Para evitar errores de descargas volvemos a resetear sparse-checkout>

		git sparse-checkout set --no-cone		


	    <Instalación de dependencias>
		 pip install -r requirements.txt
  
   
    -B. Cree un Bucket S3 en su consola de AWS y  suba el contenido de la carpeta API además de los archivos 
    production_weather_model.pth, scaler_X_production.joblib y scaler_y_production.joblib que se encuentran
    en la carpeta de streamlit.
   			
			<Asegúrese de que su instancia EC2 tenga los permisos necesarios para acceder a S3>

   				<Para ello, acceda a IAM en AWS y cree un nuevo rol con la siguiente politica>
   					{
					    "Version": "2012-10-17",
					    "Statement": [
					        {
					            "Effect": "Allow",
					            "Action": [
					   			"s3:GetObject",
					   
					   			 "s3:ListBucket"
					            ],
					            "Resource": [
					                "arn:aws:s3:::<nombre_de_tu_bucket>",
					                "arn:aws:s3:::<nombre_de_tu_bucket>/*"
					            ]
					        }
					    ]
					}
				
   				<Tras crear el rol, asignelo a su instancia EC2>


		    <Descarge archivos desde S3 a EC2>
		    aws s3 sync s3://<ruta_a_su_archivo>/nombre_archivo/ .
		   	 
		    <Instalación de dependencias>
			 pip install -r requirements.txt

      
  

**7. Configurar las Variables de Entorno**
   
	El proyecto usa variables de entorno para conectarse a la base de datos PostgreSQL.
    Debes definirlas en tu terminal antes de iniciar la aplicación.
   
	Reemplaza los valores de ejemplo con tus credenciales reales:

		export GOOGLE_API_KEY=<api_key>
		export PG_HOST="<tu_host_de_postgresql>"
		export PG_PORT="5432" # o el puerto que uses
		export PG_USER="<tu_usuario_de_postgresql>"
		export PG_PASSWORD="<tu_contraseña_de_postgresql>"
		export PG_DATABASE="<tu_base_de_datos_de_postgresql>"
  		

**8. Iniciar la Aplicación**

	Una vez que las variables de entorno están configuradas, puedes iniciar el servidor Uvicorn. Es importante usar el flag --workers 1 para la carga de modelos.
	
	uvicorn Main_production:app --host 0.0.0.0 --port 8000 --workers 1
	
	#Si todo está configurado correctamente, verás en la consola que los modelos se cargan desde S3 y que la aplicación se inicia sin errores.

🧪 **Uso de la API**

Una vez que la API está funcionando, puedes interactuar con ella a través de dos endpoints principales: Forecast(para predicciones) y Ask(para recibir información de históricos)

	Desde Streamlit APP:
		https://proyecto-final-hack-a-bossgit-wx2inpdg5kukgh6xymcwkh.streamlit.app/
	
	Desde Consola:
		Endpoint 1: Predicción de Temperatura
	
		Este endpoint toma una ubicación y un número de días para predecir la temperatura.
	
	   			Ruta: /forecast
	
	    		Método: POST
	
	    		Payload: JSON con los campos ubicacion (string) y dias (integer).
	
		Ejemplo de uso con curl:
	
		curl -X POST "http://<IP_PUBLICA_EC2>:8000/forecast" \
		-H "Content-Type: application/json" \
		-d '{"ubicacion": "Madrid", "dias": 5}'
	
		Respuesta esperada (JSON):
	
		{
	  	"ubicacion": "Madrid",
	  	"pronostico": [
	   	 { "fecha": "2025-08-08", "temperatura": 25.5 },
	   	 { "fecha": "2025-08-09", "temperatura": 26.1 },
	   	 { "fecha": "2025-08-10", "temperatura": 25.9 },
	    	 { "fecha": "2025-08-11", "temperatura": 26.3 },
	         { "fecha": "2025-08-12", "temperatura": 26.8 }
	  							]
									}
	
		Endpoint 2: Asistente de Consultas
	
		Este endpoint te permite hacer preguntas sobre los datos históricos.
	
	   		Ruta: /ask
	
	    		Método: POST
	
	   		Payload: JSON con el campo question (string).
	
		Ejemplo de uso con curl:
	
		curl -X POST "http://<IP_PUBLICA_EC2>:8000/ask" \
		-H "Content-Type: application/json" \
		-d '{"pregunta": "Cuál fue la temperatura media en Madrid en mayo de 2024?"}'
	
		Respuesta esperada (JSON):
	
		{
	 	 "respuesta": "La temperatura media en Madrid en mayo de 2024 fue de 21.5 grados Celsius.",
	  	"sql_generada": "SELECT AVG(tmed) FROM datos_clima WHERE nombre = 'Madrid' AND fecha BETWEEN '2024-05-01' AND '2024-05-31'"
		}


## 🤝 Contribuciones

Las contribuciones son bienvenidas. Si tienes ideas para mejorar, abre un *issue* o crea un *pull request*.

---

## 📖 Documentación
	Puedes acceder a la documentación interactiva de la API en tu navegador visitando la siguiente dirección:
	<http://localhost:8000/docs>

	Esto te llevará directamente a la interfaz de Swagger UI, donde podrás explorar y probar todos los endpoints de tu API.

