author: Alejandro Seoane Martínez
summary: Proyecto2
id: docs
categories: codelab,markdown
environments: Web
status: Published


# Bastionamiento del arranque de un sistema Debian

## Introducción y preparación del proyecto

**Realizado por:** Alejandro Seoane Martínez  
**Asigantura:** Bastionado de redes y sistemas  
**Curso:** 2024/25  
**Grado:** G.E.C.E.G.S.

Configurar un arranque seguro en Debian es fundamental para mejorar la seguridad del propio sistema operativo. Esto nos protegerá contra modificaciones no autorizadas en el firmware y los cargadores de arranque, lo que es importante para mantener la integridad del sistema.  
Con esto conseguiremos garantizar un entorno de ejecución seguro desde el inicio, lo que minimiza los riesgos asociados a ciberataques.   
También garantizaremos que solo se carguen y ejecuten componentes de software firmados y confiables, evitando que se pueda ejecutar código malicioso en el arranque del dispositivo.

![Portada](/docs/img/image1.png)


## Ocultación del arranque

Para ocultar el menú de arranque (GRUB) seguiremos los siguientes pasos:
- Tendremos que tener en cuenta que muchas configuraciones habrá que hacerlas con permisos de superusuario. 

```bash
sudo nano /etc/default/grub
```

![image2](image2.png)

Buscaremos las líneas GRUB_TIMEOUT y GRUB_TIMEOUT_STYLE. Si alguna de las 2 no están las tendremos que añadir y les pondremos el valor 0 a las 2. 

![image3](image3.png)

Una vez acabado le daremos a Ctrl+O para guardar el archivo y actualizaremos el grub con el siguiente comando: 
```bash
sudo update-grub
```

Y nos saldrá lo siguiente: 
![image4](image4.png)

## Contraseña de arranque

Lo primero que tendremos que hacer es generar una contraseña encriptada. Esto lo conseguiremos con el comando: 

```bash
grub-mkpasswd-pbkdf2
```

Nos pedirá la contraseña para poder encriptarla.

![alt text](image6.png)

Una vez tenemos la contraseña encriptada la copiaremos y abriremos el siguiente archivo "40_custom": 
```bash
sudo nano /etc/grub.d/40_custom
```

Al final del documento añadiremos las siguientes líneas con nuestros datos: 
```
set superusers="nombre-usuario"
password_pbkdf2 nombre-usuario hash-password
```

![alt text](image5.png)

Por último actualizaremos de nuevo el grub: 
```bash
sudo update-grub
```
![alt text](image7.png)

## Copia de seguridad

Para la copia de seguridad será un proceso fácil. Primero tendremos que crear la carpeta donde queremos hacer la copia de seguridad del GRUB: 

```bash
mkdir /home/alejandro/backup
```

Una vez creada la carpeta copiaremos el archivo de configuración que crea el GRUB: 

```bash
sudo cp /boot/grub/grub.cfg /home/alejandro/backup/
```

![alt text](image8.png)

Podríamos ver el contenido de la misma. 
![alt text](image9.png)
Ya habríamos hecho la copia de seguridad. Es importante recalcar que esta copia de seguridad habría que tenerla cifrada y protegida como pudiese ser en un disco externo cifrado.

Si ocurriese cualquier problema con el GRUB, podríamos volcar el archivo backup que hemos copiado para poder recuperar la configuración. 

## Otras opciones de seguridad

Como hemos mencionado en el apartado anterior lo suyo sería tenerlo cifrado de alguna manera para darle un nivel más en cuanto a seguridad. 

Hemos escogido la herramienta **gocryptfs** la cuál nos permitirá realizar acciones de encriptamiento. 

Lo primero de todo sería instalar la herramienta para ello ejecutaremos en la consola: 
```bash
sudo apt-get install gocryptfs
```

Procederemos a crear la encriptación de la carpeta que hemos indicado anteriormente donde íbamos a guardar el backup. 
```bash
gocryptfs -init /home/alejandro/backup
```
Nos pedirá una contraseña para poder descifrarlo después si queremos. 

![alt text](image11.png)


