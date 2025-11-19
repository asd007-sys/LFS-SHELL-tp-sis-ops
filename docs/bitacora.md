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
**Problema 1:** Comandos `gcc`, `g++` y `make` no encontrados
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

## Sesión 3: 18 de Noviembre - 21:06 a 22:09 - Configuración del Entorno LFS y Descarga de Paquetes

**Duración:** 1 hora aprox.
**Participantes:** Marcelo Avalos
**Objetivo:** Configurar entorno LFS, crear estructura de directorios y descargar todos los paquetes fuente
### Tareas Realizadas

- [x] Configurar variable de entorno LFS
- [x] Establecer umask para permisos seguros
- [x] Montar partición LFS en el punto de montaje
- [x] Configurar permisos de ownership y acceso
- [x] Crear directorio sources con permisos adecuados
- [x] Descargar paquetes fuente usando wget-list-systemd
- [x] Completar descargas faltantes desde mirror alternativo
- [x] Verificar integridad con md5sums

### Comandos Ejecutados
```bash

# Configurar variables de entorno críticas
export LFS=/mnt/lfs
umask 022

# Montar partición LFS
mkdir -pv $LFS
mount -v -t ext4 /dev/sda6 $LFS

# Configurar permisos de ownership y acceso
chown root:root $LFS
chmod 755 $LFS

# Crear directorio sources y configurar permisos
mkdir -v $LFS/sources
chmod -v a+wt $LFS/sources

# Descargar paquetes usando la lista oficial
wget --input-file=wget-list-systemd --continue --directory-prefix=$LFS/sources

# Descargar paquetes faltantes desde mirror alternativo
wget --continue --directory-prefix=$LFS/sources https://lfs.gnlug.org/pub/lfs/lfs-packages/12.4/[nombre-paquete-faltante]

# Verificar integridad de los paquetes
pushd $LFS/sources
  md5sum -c md5sums
popd
```

### Problemas Encontrados

**Problema 1:** Algunos paquetes fallaron en la descarga con la lista oficial

- **Causa:** Problemas de conectividad con los servidores oficiales

- **Solución:** Usar mirror alternativo https://lfs.gnlug.org/pub/lfs/lfs-packages/12.4/ para completar las descargas

**Problema 2**: Necesidad de verificar integridad de paquetes descargados

- **Causa**: Descargas desde múltiples fuentes

- **Solución**: Usar md5sum -c md5sums y  md5sum -c md5sums | grep -v "OK$", regex con grep para validar todos los archivos y verificar si alguna linea no termina con OK.

**Problema 3**: Se apagó inesperadamente la PC

- **Causa**: Corte de electricidad inesperada

- **Solución**: Montar $LFS de nuevo y asegurarse de que todo tenga los privilegios necesarios ejecutando los comandos de permisos nuevamente

### Resultados Obtenidos

    ✅ Variables de entorno LFS configuradas correctamente

    ✅ Partición LFS montada y con permisos adecuados

    ✅ Directorio sources creado con permisos a+wt

    ✅ Todos los paquetes fuente de LFS descargados

    ✅ Integridad de paquetes verificada con md5sums

    ✅ Entorno listo para comenzar la construcción de LFS

### Reflexión Técnica

"La descarga de paquetes desde múltiples mirrors es una práctica común en LFS debido a la disponibilidad variable de los servidores. La verificación con md5sums es crucial para asegurar que no haya corrupción en los archivos fuente. El directorio sources con permisos a+wt permite que múltiples usuarios puedan escribir y acceder temporalmente a los archivos durante el proceso de construcción."


### Evidencias

![Privilegio configurado $LFS](../imagenes/sesion3/privilegio-lfs.png)
*Figura 1: Privilegio configurado $LFS*

![Checkeo de los archivos descargados con md5](../imagenes/sesion3/md5check.png)
*Figura 2: Checkeo de los archivos descargados con md5*

### Apuntes

Utilicé inteligencia artificial para ayudar a mejorar el formato Markdown y la gramática de la bitácora, así como para consultar comandos desconocidos como hacer el regex para el md5sum check .

```
```

## Sesión 4: 19 de Noviembre - 11:53 a 12:45- Configuración Completa del Usuario LFS y Estructura de Directorios

**Duración:** 1 hora aprox.  
**Participantes:** Marcelo Avalos  
**Objetivo:** Crear estructura de directorios LFS, usuario lfs, configurar entorno de construcción

### Tareas Realizadas
- [x] Crear estructura básica de directorios LFS
- [x] Crear enlaces simbólicos para compatibilidad
- [x] Crear directorio lib64 para arquitectura x86_64
- [x] Crear directorio tools para toolchain temporal
- [x] Crear grupo y usuario lfs
- [x] Establecer password para el usuario lfs
- [x] Transferir ownership de directorios LFS al usuario lfs
- [x] Configurar archivos de entorno (.bash_profile y .bashrc)
- [x] Verificar carga correcta de variables de entorno

### Comandos Ejecutados
```bash
# Crear estructura básica de directorios LFS
mkdir -pv $LFS/{etc,var} $LFS/usr/{bin,lib,sbin}

# Crear enlaces simbólicos para compatibilidad
for i in bin lib sbin; do
  ln -sv usr/$i $LFS/$i
done

# Crear lib64 para arquitecturas x86_64
case $(uname -m) in
  x86_64) mkdir -pv $LFS/lib64 ;;
esac

# Crear directorio tools para toolchain temporal cruzada
mkdir -pv $LFS/tools

# Crear grupo y usuario lfs para construcción
groupadd lfs
useradd -s /bin/bash -g lfs -m -k /dev/null lfs
passwd lfs

# Transferir ownership de directorios críticos al usuario lfs
chown -v lfs $LFS/{usr{,/*},var,etc,tools}
case $(uname -m) in
  x86_64) chown -v lFS/lib64 ;;
esac

# Configurar archivos de entorno del usuario lfs
cat > ~/.bash_profile << "EOF"
...
EOF

cat > ~/.bashrc << "EOF"
....
EOF

#Instalar tree
como root
dnf install tree
tree /mnt/lfs 

### Verificar configuración completa
ls -la $LFS/
ls -la $LFS/tools
id lfs
source ~/.bash_profile
echo "LFS: $LFS"
echo "LFS_TGT: $LFS_TGT"
```
### Problemas Encontrados

No se encontraron problemas significativos - toda la configuración se completó exitosamente.
Resultados Obtenidos

    ✅ Estructura de directorios LFS creada correctamente

    ✅ Enlaces simbólicos configurados para compatibilidad

    ✅ Directorio tools creado para toolchain temporal cruzada

    ✅ Usuario lfs creado y configurado

    ✅ Dueño de directorios transferido al usuario lfs

    ✅ Archivos de entorno configurados y verificados

    ✅ Entorno completamente preparado para comenzar toolchain temporal

### Reflexión Técnica

"La estructura de directorios LFS sigue el Filesystem Hierarchy Standard moderno donde /bin, /lib y /sbin son enlaces simbólicos a /usr. El directorio /tools es crucial para alojar la toolchain temporal cruzada, separándola del sistema host. La creación del usuario lfs dedicado asegura un entorno limpio y aislado para la construcción."
### Evidencias

![Tree de LFS 1](../imagenes/sesion4/tree_de_lfs_1.png)
*Figura 1: Tree de LFS*

![Tree de LFS 2](../imagenes/sesion4/tree_lfs_part_2.png)
*Figura 2: Tree de LFS parte 2*

![Privilegios](../imagenes/sesion4/privilegios_y_simbolic-links.png)
*Figura 3: Privilegios*


### Apuntes

Utilicé inteligencia artificial para ayudar a mejorar el formato Markdown y la gramática de la bitácora, así como para consultar comandos como tree para hacer la imagen de la estructura del lfs.


```
```

