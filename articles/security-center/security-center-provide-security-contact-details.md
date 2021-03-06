<properties
   pageTitle="Proporcionar detalles de contacto de seguridad en Azure Security Center | Microsoft Azure"
   description="En este documento se muestra cómo proporcionar detalles de contacto de seguridad en Azure Security Center."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="terrylan"/>

# Proporcionar detalles de contacto de seguridad en Azure Security Center

Azure Security Center recomendará que proporcione los detalles de contacto de seguridad para su suscripción de Azure si no lo ha hecho ya. Esta información la utilizará Microsoft para ponerse en contacto con usted si Microsoft Security Response Center (MSRC) detecta que un tercero no autorizado o ilegal ha accedido a los datos de clientes. MSRC lleva a cabo una selecta supervisión de seguridad de la red e infraestructura de Azure y recibe información sobre amenazas y quejas sobre abusos de terceros.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## Implementación de la recomendación

1. En la hoja **Recomendaciones**, seleccione **Proporcionar detalles de contacto de seguridad**. ![Proporcionar contacto de seguridad][1]

2. Se abre la hoja **Proporcionar detalles de contacto de seguridad**. Seleccione la suscripción de Azure sobre la que desea proporcionar información de contacto. ![Proporcionar datos de los contactos de seguridad][2]

3. Se abre una segunda hoja **Proporcionar datos de los contactos de seguridad**. Escriba la dirección de correo electrónico del contacto de seguridad o direcciones separadas por comas. No hay un límite en el número de direcciones de correo electrónico que se pueden escribir. Escriba un número de teléfono internacional de seguridad.

4. Seleccione **Aceptar** para aplicar la información de contacto de seguridad a su suscripción.

## Pasos siguientes

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
- [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que le ayudan a proteger los recursos de Azure.
- [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]: ./media/security-center-provide-security-contacts/provide-contact-details.png

<!---HONumber=AcomDC_0713_2016-->