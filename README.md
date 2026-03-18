# Implementación de VPN Client-To-Site en FortiGate y Router Cisco sobre GNS3

## Video demostrativo

[![Ver video en YouTube](https://img.youtube.com/vi/AloOvnEUH-4/maxresdefault.jpg)](https://youtu.be/AloOvnEUH-4)

---

## I-) Introducción y Objetivo de la Solución
El presente informe técnico documenta el diseño, implementación y validación de una topología de red basada en una VPN Client-to-Site IPsec entre un FortiGate-VM y un router Cisco, desarrollada en un entorno virtual sobre GNS3. La práctica fue diseñada con el objetivo de interconectar dos redes LAN ubicadas en extremos distintos de la topología, permitiendo comunicación segura a través de un túnel IPsec.
La solución implementada está compuesta por un FortiGate, un router Cisco, dos redes LAN y dos hosts de prueba. En este escenario, el FortiGate actúa como servidor VPN y el router Cisco actúa como cliente VPN, permitiendo el intercambio de tráfico entre la red LAN del FortiGate y la red LAN del router.
El direccionamiento IP fue elaborado tomando como referencia la matrícula 2024-0918, con el propósito de mantener coherencia con los requerimientos de la práctica. La validación final se realizó mediante pruebas de ping, estado del túnel IPsec y traceroute, confirmando el correcto funcionamiento de la conectividad entre ambas redes.

<img width="1059" height="353" alt="Screenshot 2026-03-18 190938" src="https://github.com/user-attachments/assets/e1da3dbc-ba54-48cd-b14b-1219ea77a1e4" />

---

## II-) Topología Implementada, Interfaces y Direccionamiento IP

La topología implementada se compone de un FortiGate, un router Cisco y dos hosts finales, uno en cada red LAN. La red LAN del lado FortiGate fue conectada a la interfaz port2, mientras que la red de transporte hacia el router fue conectada a port1. Del lado del router, la interfaz GigabitEthernet0/0 fue utilizada como enlace WAN hacia el FortiGate, y la interfaz GigabitEthernet0/1 como puerta de enlace de la segunda red LAN.
La red local del FortiGate fue definida como 192.168.24.0/24, utilizando la dirección 192.168.24.1/24 en la interfaz port2 y asignando al host PC1 la dirección 192.168.24.10/24. La red local del router fue definida como 192.168.18.0/24, utilizando la dirección 192.168.18.1/24 en la interfaz GigabitEthernet0/1 y asignando al host PC2 la dirección 192.168.18.10/24.

<img width="369" height="396" alt="Screenshot 2026-03-18 191259" src="https://github.com/user-attachments/assets/16be9fc6-4f8e-4d88-ae48-8695e37875ae" />

<img width="612" height="132" alt="Screenshot 2026-03-18 191349" src="https://github.com/user-attachments/assets/237fe152-8ce5-41ed-885b-db4d0608439c" />

Para el enlace de transporte entre FortiGate y router se utilizó la subred 10.24.9.0/30, configurando la dirección 10.24.9.1/30 en la interfaz port1 del FortiGate y la dirección 10.24.9.2/30 en la interfaz GigabitEthernet0/0 del router Cisco.

<img width="237" height="181" alt="Screenshot 2026-03-18 191626" src="https://github.com/user-attachments/assets/199f89b0-9f38-454f-a430-3967beb60632" />

<img width="234" height="195" alt="Screenshot 2026-03-18 191643" src="https://github.com/user-attachments/assets/19e3d276-debb-43f8-9e3d-c523262bf9f4" />

---

## III-) Configuración de Conectividad Base y Enrutamiento
Antes de establecer la VPN, fue necesario garantizar la conectividad básica entre el FortiGate y el router Cisco a través del enlace WAN. Para ello, se configuró la interfaz port1 del FortiGate con la dirección 10.24.9.1/30, mientras que la interfaz GigabitEthernet0/0 del router Cisco fue configurada con la dirección 10.24.9.2/30. Esta conectividad permitió que ambos peers pudieran intercambiar tráfico IP antes de negociar el túnel.

<img width="413" height="196" alt="Screenshot 2026-03-18 192320" src="https://github.com/user-attachments/assets/4c3db898-5759-44be-bea4-e153c92d2a3a" />

<img width="489" height="104" alt="Screenshot 2026-03-18 192352" src="https://github.com/user-attachments/assets/f6116a50-6c12-422f-8f9c-cc0958774a3c" />

Adicionalmente, se configuró una ruta por defecto en el FortiGate apuntando hacia 10.24.9.2, y en el router Cisco se añadió una ruta estática hacia la red 192.168.24.0/24 a través de 10.24.9.1. Estas rutas permitieron el conocimiento de la red remota y prepararon la topología para el enrutamiento a través del túnel IPsec.

<img width="558" height="328" alt="Screenshot 2026-03-18 192435" src="https://github.com/user-attachments/assets/e6dc17d2-6c3a-47f3-a050-9c389d9e1a19" />

<img width="305" height="94" alt="Screenshot 2026-03-18 192506" src="https://github.com/user-attachments/assets/d538da4a-50fc-4103-b448-47d1e560007f" />

La verificación de esta etapa se realizó mediante pruebas de ping entre el FortiGate y el router, así como pruebas de conectividad desde cada host hacia su respectiva puerta de enlace. Los resultados confirmaron que la infraestructura base se encontraba correctamente operativa antes de proceder con la configuración VPN.

<img width="435" height="135" alt="Screenshot 2026-03-18 192534" src="https://github.com/user-attachments/assets/189c8d76-d648-47af-989f-46d8f9572096" />

<img width="423" height="140" alt="Screenshot 2026-03-18 192550" src="https://github.com/user-attachments/assets/29ad67b6-398b-4fb0-aad1-00b46bf5840f" />

---

## IV-) Implementación de la VPN Client-to-Site IPsec
Una vez validada la conectividad base, se procedió a la implementación del túnel VPN Client-to-Site IPsec. En esta topología, el FortiGate fue utilizado como extremo principal o servidor VPN, mientras que el router Cisco fue configurado como cliente VPN. La interfaz lógica del túnel fue creada en el FortiGate con el nombre VPN-C2S-0918.

<img width="254" height="120" alt="Screenshot 2026-03-18 192933" src="https://github.com/user-attachments/assets/9652108b-5939-4d62-ac4d-e5046fdae1b7" />

Durante la configuración del laboratorio se identificó una limitación de compatibilidad en la imagen del FortiGate utilizada, ya que no aceptó propuestas criptográficas avanzadas como AES256/SHA256 desde CLI. Por esta razón, fue necesario adaptar la configuración del router Cisco a los parámetros compatibles con el FortiGate. Finalmente, la VPN fue establecida utilizando IKEv1, propuesta DES/MD5 en Fase 1, y ESP-DES/ESP-MD5-HMAC en Fase 2, con DH Group 14 y PFS deshabilitado.

<img width="879" height="246" alt="Screenshot 2026-03-18 192953" src="https://github.com/user-attachments/assets/9bad63ae-e8a4-4693-8b61-ef6c9d92026a" />

<img width="418" height="175" alt="Screenshot 2026-03-18 193025" src="https://github.com/user-attachments/assets/ce29873f-5cfc-4899-ab40-1cf61de491b4" />

<img width="667" height="433" alt="Screenshot 2026-03-18 193103" src="https://github.com/user-attachments/assets/929bef21-bfcb-451e-9296-29425e36e386" />

Adicionalmente, en el FortiGate se configuró una ruta estática hacia la red remota por la interfaz lógica del túnel, y se añadieron políticas de firewall en ambos sentidos: desde la LAN local hacia la VPN y desde la VPN hacia la LAN local, con NAT deshabilitado. Estas configuraciones permitieron que el tráfico entre ambas redes fuera encapsulado y transportado a través del túnel IPsec.

<img width="420" height="598" alt="Screenshot 2026-03-18 193143" src="https://github.com/user-attachments/assets/4bce0c86-a8e0-4285-8e62-1a8ebbeb6693" />

---

## V-) Validación del Estado del Túnel VPN
La validación del túnel IPsec se realizó tanto desde el FortiGate como desde el router Cisco. En el router, el comando show crypto isakmp sa mostró el estado QM_IDLE, lo que confirma que la negociación IKE fue completada exitosamente. Asimismo, el comando show crypto ipsec sa mostró asociaciones de seguridad activas y contadores de paquetes en incremento, lo que demostró el intercambio real de tráfico cifrado entre ambos extremos.

<img width="435" height="105" alt="Screenshot 2026-03-18 193355" src="https://github.com/user-attachments/assets/82f19ca8-5237-4323-9750-3dbd8facf98e" />

<img width="518" height="183" alt="Screenshot 2026-03-18 193507" src="https://github.com/user-attachments/assets/68e3e0d7-547b-43ba-8b87-70c5d514b661" />

En el FortiGate, la salida de get vpn ipsec tunnel summary mostró el valor selectors(total,up): 1/1, confirmando que el selector de tráfico se encontraba activo. Por su parte, diagnose vpn tunnel list permitió observar el detalle de la asociación de seguridad, incluyendo los selectores entre las redes 192.168.24.0/24 y 192.168.18.0/24, así como los contadores de recepción y transmisión de paquetes.

<img width="634" height="56" alt="Screenshot 2026-03-18 193532" src="https://github.com/user-attachments/assets/0c1303b4-480b-42b8-906b-5e829f8db455" />

<img width="897" height="458" alt="Screenshot 2026-03-18 193625" src="https://github.com/user-attachments/assets/8c9625c9-3683-4652-96d3-89b749500df7" />

En conjunto, estas evidencias confirmaron que el túnel VPN Client-to-Site se encontraba operativo y transportando tráfico de forma correcta entre ambas redes LAN.

---

## VI-) Pruebas de Conectividad y Traceroute

Como validación funcional final, se realizaron pruebas de ping entre ambos hosts. Desde PC2 hacia 192.168.24.10 se obtuvo respuesta satisfactoria, observándose únicamente un primer timeout correspondiente al establecimiento inicial del túnel. Del mismo modo, desde PC1 hacia 192.168.18.10 se obtuvo conectividad exitosa, confirmando el intercambio bidireccional de tráfico entre ambas redes LAN.

<img width="441" height="143" alt="Screenshot 2026-03-18 194020" src="https://github.com/user-attachments/assets/cfb01edf-27fe-412f-84e5-3b9714a39b59" />

<img width="424" height="145" alt="Screenshot 2026-03-18 194043" src="https://github.com/user-attachments/assets/f6e46975-7098-413c-a9b1-005fb94e3f87" />

Adicionalmente, se realizaron pruebas de traceroute desde ambos extremos. Desde PC1 hacia 192.168.18.10, el recorrido mostró como primer salto la puerta de enlace local 192.168.24.1, como segundo salto la dirección WAN del router 10.24.9.2, y finalmente el host remoto 192.168.18.10. Desde PC2 hacia 192.168.24.10, se observó como primer salto la puerta de enlace 192.168.18.1, como segundo salto la IP WAN del FortiGate 10.24.9.1, y finalmente el host remoto 192.168.24.10.

<img width="577" height="118" alt="Screenshot 2026-03-18 194110" src="https://github.com/user-attachments/assets/7ae017d3-44c8-4189-aaf6-f02525298124" />

<img width="589" height="114" alt="Screenshot 2026-03-18 194147" src="https://github.com/user-attachments/assets/7d9c9db8-a7a6-4392-b6db-567803f812ea" />

El mensaje ICMP type 3 code 3 (Destination port unreachable) observado al final del traceroute corresponde al comportamiento esperado de la utilidad trace en VPCS, por lo que no representa un error. Por el contrario, confirma que el tráfico alcanzó correctamente el host final.

---

## VII-) Conclusión

La implementación desarrollada en GNS3 permitió construir exitosamente una topología de VPN Client-to-Site IPsec entre un FortiGate y un router Cisco, cumpliendo con los requerimientos de la práctica. La solución integró correctamente direccionamiento IP basado en la matrícula, conectividad base, rutas estáticas, políticas de firewall y establecimiento del túnel VPN entre ambas redes LAN.
Desde el punto de vista técnico, la validación confirmó que el router Cisco estableció una asociación IKE en estado QM_IDLE, que el FortiGate mantuvo el selector de tráfico activo 1/1, y que ambas redes intercambiaron tráfico cifrado exitosamente. Las pruebas de ping y traceroute confirmaron además la comunicación extremo a extremo entre los hosts de ambas LAN.
En conclusión, la práctica demostró una implementación funcional y estable de conectividad segura entre redes remotas, utilizando un modelo Client-to-Site adaptado a las capacidades de interoperabilidad entre el FortiGate y el router Cisco en el entorno virtual empleado.
