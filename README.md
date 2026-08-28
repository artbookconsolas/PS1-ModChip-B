# PS1 ModChip (B)
PCB para modchips basados en el ATtiny25/45/85 con [PsNee](https://github.com/kalymos/psnee), para consolas Sony PS1 Fat y Slim. Este repositorio indicará compatibilidades, esquemas de instalación (gracias a Quade nuevamente), y el archivo Gerber de la PCB listo para fabricación.

## 📌 Prefacio

Los modchips en la PS1 se utilizan para poder leer juegos de distintas regiones (originales y alternativos), y para lanzar copias de seguridad de juegos que ya tienes (¿cierto?). Como tal, no hacen nada más.

Al ser un accesorio para juegos físicos mediante CD-ROM, el láser de la consola debe estar completamente funcional. De hecho, al usar un chip es cuando más se nota si el láser está en buenas condiciones o si ya está mostrando sus años.

A diferencia de los modchips clásicos, esta versión utiliza el firmware **PsNee**, el cual es inteligente y *Stealth*: escucha la lente de la consola e inyecta la señal solo cuando es requerida, evitando los bloqueos Anti-Modchip (como LibCrypt) presentes en los juegos más tardíos de la PS1.

---

## ¿Cómo funciona técnicamente PsNee?
La protección original de la consola se basa en leer un código regional que viene "grabado" físicamente en la base de los discos originales (conocido como wobble groove). Las grabadoras de CD comunes de PC no pueden replicar esta pista física. Los códigos son:

SCEA: América (NTSC-U)
SCEE: Europa (PAL)
SCEI: Japón (NTSC-J)

A diferencia de los chips clásicos (como el MM3) que inyectan o "spamean" continuamente este código hacia el controlador del lector (Mechacon), **PsNee es un modchip inteligente con monitoreo en tiempo real**.

PsNee se conecta a las líneas de subcódigo del lector: `SUBQ` (Subcode Q) y `SQCK` (Subcode Clock). A través de estas líneas, el microcontrolador "escucha" la posición exacta del láser. Cuando la consola arranca y el lector entra en la zona inicial del disco (el *Lead-in*), PsNee detecta la petición de la consola e inyecta la cadena de región (`SCEx`) justo en el milisegundo exacto en que es requerida.

Una vez entregado el código, PsNee apaga inmediatamente sus salidas y pasa a un estado pasivo (alta impedancia). Para la consola y el juego, el chip deja de existir por completo en el circuito. Esto garantiza un $100\%$ de efectividad contra las protecciones **Anti-Modchip** y **LibCrypt** de juegos tardíos (como *Spyro*, *Dino Crisis* o *MediEvil 2* por ejemplo), evitando la famosa pantalla negra de "SOFTWARE TERMINATED".

> [!Note]
> Al ser un chip 100% Stealth (sigiloso), PsNee no interferirá jamás con tus juegos originales ni activará falsos positivos de protección antipiratería, algo que sí ocurre con chips antiguos que se quedan inyectando datos mientras juegas.

⚠️ Advertencia sobre consolas Japonesas (NTSC-J)
Es importante destacar que las consolas japonesas (desde la serie SCPH-3000 en adelante) tienen una capa de seguridad adicional. Además de la verificación del lector de CD, tienen un bloqueo de región directamente integrado en la BIOS de la placa madre.

Si instalas este modchip en una consola japonesa:

* Podrás jugar copias de seguridad de juegos japoneses sin problemas.
* NO podrás arrancar juegos americanos (NTSC-U) ni europeos (PAL) directamente. El chip engaña al lector de CD, pero la BIOS detectará que el juego no es de su región y bloqueará el arranque.

Para jugar títulos de otras regiones en una consola japonesa con modchip, necesitarás usar un disco de arranque (como Import Player), utilizar FreePSXBoot con un juego japonés original o directamente reemplazar el chip BIOS de la placa por uno americano.

📺 El problema del color en juegos de otras regiones (PAL vs NTSC)
El modchip te permite saltar el bloqueo del CD y arrancar un juego europeo (PAL) en una consola americana (NTSC) o viceversa, pero no modifica el hardware de video de la consola.

La placa madre de la PS1 tiene un único oscilador de cristal (el reloj de cuarzo principal) soldado de fábrica, el cual dicta la frecuencia exacta de la subportadora de color para su región nativa. Si fuerzas el arranque de un juego europeo (que corre a 50Hz) en una consola americana (que es de 60Hz), el codificador de video no logra sincronizar la frecuencia cromática correcta.

¿El resultado? La imagen se verá en blanco y negro o rodando verticalmente si utilizas cables de video compuesto tradicionales (los clásicos RCA Amarillo, Blanco y Rojo) o S-Video.

¿Cómo se soluciona esto?
Vía Hardware (Avanzado): Instalar un mod adicional conocido como DFO (Dual Frequency Oscillator) o MFO. Esto implica desoldar el reloj original de la placa madre y reemplazarlo por una pequeña placa inteligente que cambia la frecuencia de reloj al vuelo dependiendo de la región del juego insertado.

---

## Archivos y Compatibilidad

A diferencia de los chips PIC, donde debías elegir entre distintas variantes de código (MM3, Mayumi, OneChip), **PsNee utiliza un único código unificado e inteligente** compatible con prácticamente todas las revisiones de placa base de PS1:

* **PU-7, PU-8 y PU-18:** Altamente recomendado. En estas placas antiguas es donde PsNee brilla especialmente por su monitoreo `SUBQ/SQCK`, logrando un funcionamiento stealth perfecto donde los chips PIC solían dar problemas.
* **PU-20, PU-22 y PU-23:** 100% compatible. Ofrece arranque rápido y estable sin requerir cables de reloj externos adicionales.
* **PM-41 y PM-45 (PSone Slim):** Compatible con todas las regiones. 
  * Para la **PSone Europea (SCPH-102)**, se recomienda quedarse con ONEChip.

> [!Note]
> Si prefieres utilizar microcontroladores PIC tradicionales con códigos clásicos como MM3 o Mayumi v4, consulta nuestro repositorio hermano: **[PS1 ModChip (A)](https://github.com/artbookconsolas/PS1-ModChip-A)**.

Crédito y agradecimientos a **kalymos** (desarrollador original de [PsNee](https://github.com/kalymos/psnee)), a **[Quade](https://quade.co/ps1-modchip-guide/psnee/)** por la documentación de los diagramas de instalación, y a la comunidad de preservación retro.

Placa de montaje (Hardware)

En la carpeta de "PCB" se agrega el archivo Gerber (PS1.ModChip.B.170726.zip) de la PCB custom para montar el chip en su versión superficial (SOIC-8) junto con un espacio para su condensador de desacoplo de 100nF (o 0.1uF).

---

## Esquemas de Instalación y Agradecimientos Finales

A continuación se detallan los diagramas de instalación para los diferentes modelos de placa base de PlayStation 1. Todos estos esquemas son fruto del excelente trabajo de documentación/recopilación de Quade. Créditos y agradecimientos para él.

Para mantener este documento organizado y no convertirlo en una galería de imágenes interminable, he colocado todos los diagramas de soldadura en la carpeta "Esquemas".
