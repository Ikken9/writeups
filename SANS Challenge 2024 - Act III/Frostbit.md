# Frostbit

![[fb1.png]]

![[tls-ssl-handshake.png]]

Client: 172.17.0.3 (src)
Server: 34.173.241.47 (dst)
Ipv4, HTTPS, TLSv1.3

Client hello: api.frostbit.app
TLS handshake


core dump file
![[file2.png]]
gdb

![[fb2.png]]

No stripped
no-executable bit enabled

r2 -AA <bin> perform analysis over the binary

Basado en los contenidos del decompile y el pcap, el programa establece una conexion tls con un servidor en 34.173.241.47,