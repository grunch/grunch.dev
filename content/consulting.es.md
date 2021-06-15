+++
title = "Consultoría Lightning Network"
date = 2021-06-14
+++
A menudo recibo solicitudes de asesoría sobre servicios relacionados con bitcoin/Lightning, básicamente son los siguientes:

**Servicios lightning custodiados:**

* Permitir a los clientes utilizar mis nodos para implementar Lightning Network
* Implementar una API lightning network que permita la conectividad entre el cliente y mis nodos, la API tendría endpoints básicos y otros endpoints personalizados:
	- Invoice creation
	- Lookup payments
	- Withdraw
	- Get invoices
	- Get user balance
	- Build lnurl
	- Get lnurl orderId
	- Get lnurl withdraw
	- Check lnurl by orderId
* Implementar el frontend que trabajará con la API

**Servicios lightning no custodiados:**

* Crear una infraestructura Bitcoin/Lightning para clientes con la finalidad de permitirles tener custodia total de sus fondos.
	- Lightning node on demand, es recomendado tener solo un nodo lightning
	- Mantenimiento del nodo lightning:
		+ Abrir canales
		+ Cerrar canales que no respondan
		+ Mantener canales balanceados dependiendo de las necesidades del cliente, por ejemplo, si el cliente tiene un e-commerce, solamente necesita mantener capacidad de entrada (`inbound capacity`), esto debe estar automatizado
		+ Loop In/Out

Incluye los mismos servicios de API mencionados en la sección **custodiada**.

Seguridad:
Aunque este campo es muy nuevo y es difícil considerar a alguien un experto, llevo 3 años trabajando con este tipo de aplicaciones lightning, en los que he aprendido buenas prácticas en materia de seguridad.

Los nodos Lightning se ejecutan en Linux, sistema operativo que he estado usando desde hace más de 20 años, y la gestión de la seguridad de Linux es algo que estoy familiarizado.
