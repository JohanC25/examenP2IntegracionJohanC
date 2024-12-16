1. Requisitos Previos:
   1.1. Antes de comenzar, asegúrate de tener instalado lo siguiente:
        - Node.js (Versión 18.x o superior).
        - npm (Incluido con Node.js).
        - PostgreSQL (Versión 12 o superior).
        - Un cliente para probar los servicios, como Postman o curl.

2. Configuración de la Base de Datos:
   2.1. Crear las bases de datos necesarias ejecutando los siguientes comandos en PostgreSQL:
        CREATE DATABASE soap_service;
        CREATE DATABASE rest_api;
        CREATE DATABASE inventory_service;

   2.2. Esquema de las tablas:
        - Base de Datos `soap_service`:
          CREATE TABLE availability (
              room_id SERIAL PRIMARY KEY,
              room_type VARCHAR(50) NOT NULL,
              available_date DATE NOT NULL,
              status VARCHAR(20) NOT NULL
          );
          INSERT INTO availability (room_type, available_date, status) VALUES
          ('Single', '2024-06-01', 'available'),
          ('Single', '2024-06-02', 'available'),
          ('Double', '2024-06-01', 'available'),
          ('Suite', '2024-06-03', 'unavailable');

        - Base de Datos `rest_api`:
          CREATE TABLE reservations (
              reservation_id SERIAL PRIMARY KEY,
              room_number INT,
              customer_name VARCHAR(100),
              start_date DATE,
              end_date DATE,
              status VARCHAR(20)
          );

        - Base de Datos `inventory_service`:
          CREATE TABLE rooms (
              room_id SERIAL PRIMARY KEY,
              room_number INT NOT NULL UNIQUE,
              room_type VARCHAR(50) NOT NULL,
              status VARCHAR(20) NOT NULL
          );

3. Configuración y Ejecución de los Servicios:
   3.1. Servicio SOAP (Gestión de Disponibilidad):
        3.1.1. Navega al directorio `soap-service`:
               cd soap-service
        3.1.2. Instala las dependencias:
               npm install
        3.1.3. Inicia el servidor SOAP:
               node index.js
        3.1.4. Verifica que el servicio esté corriendo en:
               http://localhost:3001/wsdl

   3.2. API REST (Gestión de Reservas):
        3.2.1. Navega al directorio `rest-api`:
               cd rest-api
        3.2.2. Instala las dependencias:
               npm install
        3.2.3. Inicia el servidor REST:
               node index.js
        3.2.4. Abre Swagger en tu navegador:
               http://localhost:3002/api-docs
        3.2.5. Prueba los endpoints disponibles:
               - POST /reservations: Crear una nueva reserva.
               - GET /reservations/{id}: Consultar una reserva.
               - DELETE /reservations/{id}: Cancelar una reserva.

   3.3. Microservicio de Inventario:
        3.3.1. Navega al directorio `inventory-service`:
               cd inventory-service
        3.3.2. Instala las dependencias:
               npm install
        3.3.3. Inicia el servidor de inventario:
               node index.js
        3.3.4. Abre Swagger en tu navegador:
               http://localhost:3003/api-docs
        3.3.5. Prueba los endpoints disponibles:
               - POST /rooms: Registrar una habitación.
               - GET /rooms: Listar todas las habitaciones.
               - PATCH /rooms/{id}: Actualizar el estado de una habitación.
               - DELETE /rooms/{id}: Eliminar una habitación.

4. Pruebas de los Servicios:
   4.1. Servicio SOAP:
        4.1.1. Usa un cliente como Postman para enviar una solicitud POST a:
               http://localhost:3001/wsdl
        4.1.2. Utiliza este XML en el cuerpo de la solicitud:
               <soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:avail="http://example.com/availability">
                   <soapenv:Header/>
                   <soapenv:Body>
                       <avail:checkAvailability>
                           <avail:startDate>2024-06-01</avail:startDate>
                           <avail:endDate>2024-06-03</avail:endDate>
                           <avail:roomType>Single</avail:roomType>
                       </avail:checkAvailability>
                   </soapenv:Body>
               </soapenv:Envelope>

   4.2. API REST:
        4.2.1. Prueba los siguientes endpoints desde Swagger o Postman:
               - POST /reservations: 
                 {
                     "roomType": "Single",
                     "startDate": "2024-06-01",
                     "endDate": "2024-06-03",
                     "customerName": "John Doe"
                 }
               - GET /reservations/{id}: Reemplaza `{id}` con el ID de una reserva existente.
               - DELETE /reservations/{id}: Reemplaza `{id}` con el ID de una reserva existente.

   4.3. Microservicio de Inventario:
        4.3.1. Prueba los siguientes endpoints desde Swagger o Postman:
               - POST /rooms:
                 {
                     "roomNumber": 101,
                     "roomType": "Single",
                     "status": "available"
                 }
               - PATCH /rooms/{id}:
                 {
                     "status": "maintenance"
                 }
               - DELETE /rooms/{id}: Reemplaza `{id}` con el ID de una habitación existente.

5. Notas Adicionales:
   5.1. Asegúrate de que las bases de datos estén corriendo antes de iniciar los servicios.
   5.2. Si un servicio no se conecta a PostgreSQL, revisa las credenciales y verifica que la tabla correspondiente exista.
   5.3. Puedes usar herramientas como pgAdmin o DBeaver para administrar las bases de datos.
