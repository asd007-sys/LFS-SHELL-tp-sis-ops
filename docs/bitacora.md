# Bitácora del Proyecto LFS

**Integrantes:** Marcelo Avalos  
**Fecha de inicio:** 17 de Noviembre

---

## Sesión 1: 17 de Noviembre - Instalación Rocky Linux 10 Host

**Duración:** 45 mins aprox. (11:30 - 12:15)  
**Participantes:** Marcelo Avalos  
**Objetivo:** Instalar Rocky Linux 10 como host para LFS con particiones optimizadas

### Tareas Realizadas
- [x] Descargar Rocky Linux 10 ISO
- [x] Crear VM (4 CPU, 11.5GB RAM, 60GB disco)
- [x] Configurar esquema de particiones personalizado
- [x] Completar instalación básica


### Comandos ejecutados post-instalación:
lsblk
free -h


### Resultados:

    Particiones: / (20G), /home (5G), swap (12G), libre (22G)

    RAM: 11.5GB confirmada

    Versión: Rocky Linux 10.0 

### Problemas Encontrados

Problema: Tamaño de /home posiblemente pequeño para desarrollo extenso

    Solución: Como la mayoría de LFS se construye en la partición dedicada, el host no precisa de un /home grande

    Aprendizaje: El espacio crítico está en la partición LFS, no en /home del host

### Resultados Obtenidos

    ✅ Rocky Linux 10 instalado y funcionando

    ✅ 22GB espacio libre reservado para LFS

    ✅ Sistema listo para configuración LFS

### Reflexión Técnica

"El esquema de particiones prioriza espacio para LFS. Los 22GB libres deberían ser suficientes para sistema base según el manual LFS. El swap generoso (12GB) ayudará en compilaciones intensivas. La partición /home de 5GB es adecuada para documentación y configuraciones del proyecto."
Evidencias

![Verificación de particiones con lsblk](../imagenes/sesion1/lsblk.png)
*Figura 1: Salida del comando lsblk mostrando el esquema de particiones*

![Pantalla de instalación exitosa](../imagenes/sesion1/host_instalado.png)
*Figura 2: Instalación de Rocky Linux 10 completada*

![Verificación de RAM y swap](../imagenes/sesion1/free-h.png)
*Figura 3: Salida de free -h mostrando memoria disponible*


---
---

## Sesión 2: 18 de Noviembre, 19:40 a 20:52 - Preparación Completa del Entorno LFS

**Duración:** 1 hora aprox.
**Participantes:** Marcelo Avalos  
**Objetivo:** Verificar requisitos del host, crear partición LFS y preparar entorno completo

### Tareas Realizadas
- [x] Ejecutar `version-check.sh` del manual LFS
- [x] Identificar paquetes faltantes en el sistema host
- [x] Instalar herramientas de desarrollo necesarias
- [x] Configurar enlace simbólico yacc → bison
- [x] Crear partición LFS en espacio libre de 21GB
- [x] Formatear partición LFS como ext4
- [x] Verificar todas las herramientas requeridas

### Comandos Ejecutados
```bash


# Verificación inicial
bash version-check.sh

# Instalación de paquetes faltantes
dnf install bison m4 patch perl 
dnf install gcc gcc-c++ make


# Configuración de yacc
ln -sv /usr/bin/bison /usr/bin/yacc

# Creación de partición LFS con parted
parted /dev/sda
print free          # Ver espacio libre
parted /dev/sda mkpart primary ext4 40.8GB 63.8GB
quit

# Formatear partición
mkfs -t ext4 /dev/sda6

# Verificación final
bison --version
yacc --version
bash version-check.sh
```

### Problemas Encontrados
**Problema 1:** Comandos `gcc` y `g++` no encontrados
- **Causa:** Paquetes de desarrollo no instalados
- **Solución:** `dnf install gcc gcc-c++ make`

**Problema 2:** `yacc` no encontrado pero `bison` sí estaba disponible
- **Causa:** Rocky Linux usa bison como reemplazo de yacc
- **Solución:** `ln -sv /usr/bin/bison /usr/bin/yacc`

**Problema 3:** `texinfo` no disponible en repositorios base
- **Causa:** Repositorio CRB deshabilitado por defecto
- **Solución:** 
```bash
dnf config-manager --set-enabled crb
dnf install texinfo
```

**Problema 4:** Faltaban los paquetes perl, m4 y patch
- **Causa:** No incluidos en el grupo de desarrollo
- **Solución:** `dnf install m4 patch perl`

**Problema 5:** Error de symlink - creé `/usr/bin/yac` por error
- **Causa:** Mal tipeado el comando
- **Solución:** `rm -v /usr/bin/yac` 

### Resultados Obtenidos
- ✅ Todas las herramientas de verificación instaladas
- ✅ `version-check.sh` ejecutado exitosamente
- ✅ GCC confirmado (versión superior a la mínima requerida)
- ✅ Partición LFS creada (/dev/sda6 de ~21GB)
- ✅ Partición formateada como ext4 
- ✅ Entorno host completamente preparado para LFS

### Reflexión Técnica
"La preparación del entorno LFS mostró la importancia de verificar meticulosamente cada herramienta. El habilitar el repositorio CRB fue crucial para obtener texinfo. Los problemas con yacc se resolvieron fácilmente con symlinks, pero hay que tener cuidado con los errores de tipeo en los comandos."

### Evidencias
![Requisitos fallado](../imagenes/sesion2/requisitos-host-fallado.png)
*Figura 1: Requisitos necesarios a completar*

![Requisitos cumplidos](../imagenes/sesion2/requisitos-host.png)
*Figura 21: Requisitos necesarios completados*

![Partición LFS creada](../imagenes/sesion2/particion-lfs-sda6.png)
*Figura 3: Particion LFS creada*

### Apuntes

Utilicé inteligencia artificial para ayudar a mejorar el formato Markdown y la gramática de la bitácora, así como para consultar comandos desconocidos como parted y el repositorio alternativo crb.

```
```
