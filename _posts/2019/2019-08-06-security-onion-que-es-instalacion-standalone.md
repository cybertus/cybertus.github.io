---
layout: post
title:	"Security Onion: Qué es e instalación Standalone"
date:	2019-08-06 13:00:00
categories:
    - blog
tags:
    - BlueTeam
    - Monitoring
    - SecurityOnion
---

El mes pasado di una charla en la Hack & Beers de Vigo y estuve hablando sobre Security Onion y como nos puede ayudar a las pequeñas empresas sin presupuesto a ganar visibilidad en la red. Inicialmente, un despliegue de Security Onion es muy sencillo y no requiere apenas esfuerzo ni tiempo, pero el tunning de todas las reglas/servicios sí que nos consumirá hasta el alma en ocasiones. Esto me animó a crear una serie de posts sobre el tema y comenzar de una vez por todas con este blog.

[Security Onion][SecurityOnion] es una distribución de linux gratuita, que ofrece múltiples herramientas de monitorización de red. A lo largo de esta serie de posts me estaré apoyando en la [documentación oficial][so-doc] de Security Onion y, en lo restante, en la información encontrada en sus [foros oficiales][so-foro].

El siguiente gráfico muestra, a alto nivel, cuáles son los servicios que nos ayudarán a monitorizar nuestra red.

<a href="https://securityonion.readthedocs.io/en/latest/architecture.html#high-level-architecture-diagram">![Screenshot](https://securityonion.readthedocs.io/en/latest/_images/elastic-architecture.png)</a>

Otro gráfico más detallado se puede visualizar en [este enlace][so-arch-detailed].

<br/>

Con esta distribución tenemos solucionados bastantes aspectos que nos ofrecen una enorme visibilidad en la red:

    - Integración de activos mediante syslog
	- Parseo e indexación de los logs (Logstash y Elasticsearch)
	- Alertas de intrusiones a nivel de red (Snort/Suricata)
	- Alertas de intrusiones a nivel de host (OSSEC, ahora Wazuh)
	- Configuración de políticas de red (Bro)
	- Informes para los managers (Kibana)
	- Análisis de tráfico (Wireshark, NetworkMiner, CapME!)

A lo largo de esta serie iré profundizando sobre estas herramientas.
<br/><br/>

Security Onion nos ofrece dos tipos de arquitecturas:
	
- `Standalone`: una única máquina en la que se procesan y se almacenan todos los logs. 
- `Distribuida`: se compone por un <span class="reserved">Master server</span>, <span class="reserved">Forward node</span> y <span class="reserved">Storage node</span>. Alternativamente, el Forward node y Storage node se pueden unificar en un <span class="reserved">Heavy node</span>. De esta arquitectura hablaré en otros posts más adelante.

La arquitectura standalone no es más que una instalación en una única máquina virtual o servidor y que realiza todas las funciones de redirección, almacenamiento y de servidor. Esto es, todos los servicios mostrados en la imagen sobre la <span class="reserved">arquitectura a alto nivel</span> serán instalados en una única máquina. No tiene mayor complicación. Esta es la arquitectura en la que nos centraremos en este post.

<br/>

# Instalación

### Requisitos

Una máquina virtual con las siguientes características:  

    - 4 núcleos CPU
    - 8GB de RAM
    - 50GB HDD
    - 2x NIC

Siguiendo la siguiente arquitectura:

<img src="{{ site.baseurl }}/images/posts/2019/07/arch.png" height="700"/> 


Desde nuestra aplicación de virtualización, podemos configurar el rango de red que queremos utilizar. Voy a configurar mi VMWare para utilizar una red personalizada <span class="reserved">vmnet 192.168.100.0/24</span> 


<b>VMWare Fusion </b>

VMWare Fusion > Preferencias... > Red > + 

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/vmfusionred.png)
<br/>
<br/>

<b>VirtualBox</b>

Archivo > Preferencias... > Red > Agregar nueva red

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/vboxred.png)

<br/>

Ya con la máquina virtual configurada, insertamos la ISO de Security Onion, arrancamos y lo instalamos. La instalación de la ISO la voy a omitir porque asumo que todos ya estamos hartos de instalar distribuciones de linux, y esto no tiene nada de especial.

### Setup - Primera fase

Una vez tengamos Security Onion instalado (os recomiendo hacer un snapshot), comenzamos la configuración. Esta distribución trae su propio wizard, que no es más que un conjunto de scripts que se ejecutarán a medida que le vayamos indicando qué clase de entorno queremos configurar. El setup de Security Onion tiene dos fases:

    - Fase 1. Configuración de las interfaces de red
    - Fase 2. Configuración de roles y servicios

Ejecutamos el setup del escritorio.

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon1-1.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon1-2.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon1-3.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon1-4.png)

Aquí introducimos los siguientes datos:

    - 192.168.100.10
    - 255.255.255.0
    - 192.168.100.1
    - 8.8.8.8 \(o cualquier otro DNS\)
    - local.domain

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon1-5.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon1-6.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon1-7.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon1-8.png)

### Setup - Segunda fase

Las interfaces de red ya se han configurado y ya podemos comenzar a instalar los servicios de Security Onion. En esta fase vamos a especificar si queremos que nuestro Security Onion sea un Master Server, Forward Node, si va a ser una arquitectura standalone o distribuida, etc.

Pulsamos en el botón Setup del escritorio de nuevo y se nos mostrará el siguiente mensaje:

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-1.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-2.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-3.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-4.png)

En la siguiente imagen especificamos el usuario y contraseña que usaremos en Kibana, Squert y Sguil.

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-5.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-6.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-7.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-8.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-9.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-10.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-11.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-12.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-13.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-14.png)

![Screenshot]({{ site.baseurl }}/images/posts/2019/07/secon2-15.png)

A partir de aqui solo queda esperar a que se configuren los servicios y arranque logstash, que tarda como unos 10-15 minutos. Con esto ya tenemos una instalación Standalone de Security Onion. No tiene ninguna complicación y los mensajes que se van mostrando en el setup son bastante descriptivos y sencillos de entender. 



[SecurityOnion]: https://securityonion.net/
[so-doc]: https://securityonion.readthedocs.io/en/latest/
[so-foro]: https://groups.google.com/d/forum/security-onion
[so-arch-detailed]: https://securityonion.readthedocs.io/en/latest/architecture.html#detailed-data-flow-diagram