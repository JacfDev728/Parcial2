Jorge A Cortes 2185745
Santiago Miranda 2210257

---//---

Punto 1: FTPS con NAT en Modo Pasivo (Puerto 21)

Se implementó el Firewall (servidor1) para canalizar el tráfico seguro de FTP al servidor interno (cliente1) y permitir las conexiones de datos pasivas mediante el módulo nf_conntrack_ftp.
Configuración Clave (Evidencia en GitHub)

          Archivo	                  |      Máquina	        |  Directiva que Valida
servidor1_config/before.rules	      |      servidor1	      |  Regla DNAT para redirigir el tráfico del puerto 21 a cliente1.
servidor1_config/modules	          |      servidor1	      |  Carga del módulo nf_conntrack_ftp para canales de datos pasivos.
cliente1_config/vsftpd.conf	        |      cliente1	        |  Configuración pasv_address=192.168.56.20 (dirección pública del Firewall).
cliente1_config/vsftpd.pem	        |      cliente1	        |  Archivos de Certificado y Clave SSL/TLS para el cifrado.

---//---

Punto 2: DNS Autoridad con AXFR Canalizado (Puerto 53 TCP)

Se implementó una arquitectura DNS Maestro/Esclavo (servidor1 Maestro) con transferencia de zona (AXFR) permitida, la cual debe ser obligatoriamente canalizada a través del Firewall.
Configuración Clave (Evidencia en GitHub)

          Archivo	                  |      Máquina	        |  Directiva que Valida
servidor1_config/before.rules	      |      servidor1	      |  Regla DNAT para redirigir el tráfico TCP del puerto 53 al DNS Maestro.
servidor1_config/named.conf.local	  |      servidor1	      |  Configuración de zona type master con allow-transfer { any; };.
cliente1_config/named.conf.local	  |      cliente1	        |  Configuración de zona type slave apuntando al Maestro.

---//---

Punto 3: Cliente Linux con DNS sobre TLS (Puerto 853)

Se configuró el cliente Linux (cliente1) para que todas sus consultas salientes se realicen utilizando el puerto 853, asegurando el cifrado mediante TLS.
Configuración Clave (Evidencia en GitHub)

          Archivo	                  |       Máquina	        |  Directiva que Valida
cliente1_config/resolved.conf	      |       cliente1	      |  Directiva DNSOverTLS=yes y servidores externos configurados.
