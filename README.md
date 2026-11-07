De datos dispersos a IA útil: asistente de IA para Distribuidora Río Claro
Proyecto integrador AI-901 · Opción A (caso Distribuidora Río Claro) Autor: Anahí de Loera Figueroa Fecha de entrega: 6 de noviembre de 2026

1. Enlace del prototipo
Prototipo funcional: https://chatbot-rioclaro-73908.azurewebsites.net/

Se entra como un usuario normal: se abre el enlace y se escribe en el chat. No requiere inicio de sesión ni instalar nada. La primera respuesta puede tardar hasta un minuto porque la aplicación arranca de forma lenta al despertar.

Sugerencia de pruebas para el revisor:

"¿Cuánto cuesta el cloro de 1 litro?"
"¿Cuánto cuesta el blanqueador de 1000 ml?"
"Me llegó un producto roto, ¿qué hago?"
"¿Cuántas piezas quedan de cloro de 1 litro?"
2. El problema
Distribuidora Río Claro es una distribuidora familiar de productos de limpieza con 140 empleados y 5 sucursales (Guadalajara, Zapopan, León, Querétaro y Aguascalientes). Sin un área de TI, enfrenta estos problemas:

Perdió clientes grandes porque "nadie les contestaba a tiempo".
Sus datos están dispersos: catálogos distintos por sucursal, tres versiones de la política de devoluciones, quejas sin registro central y facturas en correos personales.
La dirección quiere un asistente que responda a clientes y empleados en minutos, pero la IA no puede aprovechar información desordenada o contradictoria.
Lo que esta entrega resuelve: la parte del asistente. Se ordenó lo indispensable (una sola versión del catálogo, de la política de devoluciones y de las preguntas frecuentes) y sobre esa base se construyó un asistente de IA con Microsoft Foundry, publicado en Azure.

Lo que no se resolvió en este prototipo (ver sección 7): extracción automática de facturas, voz, y controles de acceso por persona.

3. La solución
Una página web con un chat. El usuario escribe una pregunta, un servidor la recibe y consulta a un agente de IA de Microsoft Foundry. Ese agente responde usando únicamente tres documentos de la empresa.

Navegador  →  Azure App Service (servidor Node.js)  →  Agente en Microsoft Foundry
                                                         ├─ modelo de lenguaje
                                                         └─ búsqueda en 3 documentos

El navegador nunca habla directo con Foundry. El servidor se identifica ante Foundry con una identidad administrada, sin guardar contraseñas ni llaves en el código.

Servicios de Azure utilizados
Servicio	Para qué se usa	Justificación de costo
Microsoft Foundry (proyecto y agente)	Aloja el agente y el modelo de lenguaje gpt-5]	El consumo del modelo es por uso; con pruebas pequeñas el gasto es mínimo
Búsqueda de archivos del agente	Permite que el agente responda con los documentos de Río Claro	Incluida en el agente; evita montar un servicio de búsqueda aparte
Azure App Service (plan B1, Linux, Node.js)	Publica la página y el servidor del chat	Plan básico de bajo costo, suficiente para un piloto
Identidad administrada y control de acceso por roles (rol Foundry User)	La aplicación se autentica ante Foundry sin llaves	Sin costo adicional
Se desplegó en la región Canadá Central porque la política de la suscripción no permitió crear App Service en la región inicial (East US 2).

Cómo se ordenaron los datos
Se creó un conjunto mínimo de documentos fuente, todos ficticios:

Catálogo de productos: una sola clave por producto y sus nombres alternativos ("Cloro 1L", "CLORO LITRO" y "Blanqueador 1000 ml" son el mismo producto).
Política de devoluciones: una sola versión vigente, con plazos, casos y quién aprueba.
Preguntas frecuentes: horarios, pedidos, pagos y atención.
Esto responde al problema central del caso: si la IA aprendiera de documentos contradictorios, respondería con seguridad algo incorrecto.

Instrucciones del agente
El agente se configuró con reglas explícitas:

Se presenta como asistente de inteligencia artificial.
Responde solo con los documentos disponibles y cita de cuál proviene la información.
Si no tiene el dato, lo dice y recomienda consultar a la sucursal. No inventa precios, existencias, fechas ni contactos.
Trata igual a todos los clientes sin importar la sucursal.
No usa ni repite datos sensibles.
En decisiones con dinero aclara que su respuesta es una sugerencia y que una persona debe aprobarla.
4. Evidencias del prototipo
4.1 Saludo y transparencia
Saludo del asistente indicando que es una IA

4.2 Pregunta de precio con cita del documento
Respuesta con el precio del cloro y el documento fuente

4.3 Reconocimiento de nombres alternativos
El asistente reconoce blanqueador de 1000 ml como cloro de 1 litro

4.4 No inventa existencias
El asistente remite a la sucursal para confirmar existencias

4.5 Devoluciones con aprobación humana
Respuesta sobre devoluciones citando la política

4.6 Rechazo de datos sensibles
El asistente se niega a dar el sueldo de un gerente

4.7 Configuración en Microsoft Foundry
Agente en Foundry con instrucciones y archivos

4.8 App Service y permisos
App Service en ejecución

Asignación del rol Foundry User a la identidad de la aplicación

No incluido:

Extracción de facturas con Azure AI Document Intelligence y marca de revisión humana por baja confianza.
Entrada por voz con Azure Speech.
Autenticación por persona con Microsoft Entra ID y manejo de secretos con Azure Key Vault.
Historial central de quejas y reporte de ventas unificado.
Los documentos son ficticios y las existencias no se consultan, porque el catálogo no tiene inventario en tiempo real.
Siguientes pasos propuestos: primero facturas (el mayor ahorro de horas), luego voz para bodega y por último acceso por persona.

8. Herramientas y apoyo utilizado
Se tomó como base un tutorial de GitHub Copilot con Microsoft Foundry y Azure App Service, que se adaptó al caso (cambio de la API del agente, región de despliegue y documentos propios).
Se usó un asistente de IA como apoyo para entender los pasos, resolver errores y redactar documentos, lo cual está permitido en las condiciones del proyecto.
Los documentos de la empresa son ficticios.
