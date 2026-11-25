# Laboratorio: Windows Server 2022, Active Directory y Servidor de Archivos

Guía paso a paso para montar, sobre VirtualBox, un servidor **Windows Server 2022** con un dominio de **Active Directory** y un **servidor de archivos** accesible desde un cliente **Windows 11**.

## Índice

1. [Requisitos]
2. [Crear la máquina virtual]
3. [Instalar Windows Server 2022]
4. [Instalar Active Directory y crear el dominio]
5. [Crear el servidor de archivos]
6. [Conectar un cliente Windows 11]

---

## 1. Requisitos

- [VirtualBox](https://www.virtualbox.org/) instalado.
- Imagen ISO de Windows Server 2022 (descargada desde el sitio de Microsoft, eligiendo el idioma).
- Un segundo disco virtual para el servidor de archivos (ver [sección 5](#5-crear-el-servidor-de-archivos)).
- Un equipo cliente con Windows 11.

## 2. Crear la máquina virtual

En VirtualBox, hacer clic en **Nuevo** y completar:

| Parámetro | Valor |
| Nombre | `Wn Server 2022` |
| Versión | Windows 2022 (64 bits) |
| Memoria RAM | 3 GB |
| Procesadores | 4 núcleos |
| Disco duro | 90 GB |
| Disquete (Sistema) | Desactivado |
| Adaptador de red | **Adaptador puente** |

Para cargar la ISO: en el almacenamiento, clic en el disco óptico **Vacío** → seleccionar el archivo ISO descargado → **Aceptar**.


## 3. Instalar Windows Server 2022

1. Iniciar la máquina virtual.
2. Elegir idioma y región: **Español (Argentina)**.
3. Clic en **Instalar ahora**.
4. Seleccionar la edición **Windows Server 2022 Standard (Desktop Experience)**.
5. Aceptar los términos de licencia.
6. Elegir **Instalación personalizada (nueva instalación)**.
7. No particionar el disco y continuar. Windows se instala solo.
8. Cuando termine, **quitar la ISO** desde VirtualBox (expulsar el disco virtual).
9. Definir la contraseña del usuario **Administrador**.
10. Iniciar sesión con `Ctrl + Alt + Supr` (en VirtualBox: menú *Entrada → Teclado → Insertar Ctrl-Alt-Supr*).

> ⚠️ Usar una contraseña propia y segura.

## 4. Instalar Active Directory y crear el dominio

### 4.1 Instalar el rol

1. Abrir el **Administrador del servidor**.
2. **Administrar → Agregar roles y características**.
3. Elegir **Servicios de dominio de Active Directory**.
4. Clic en **Agregar características** cuando lo pida.
5. Avanzar con **Siguiente** hasta llegar a **Instalar**.

### 4.2 Promover el servidor a controlador de dominio

1. En el Administrador del servidor, clic en **Promover este servidor a controlador de dominio**.
2. Seleccionar **Agregar un nuevo bosque**.
3. Nombre del bosque: `Labeze.com`.
4. Definir la contraseña del modo de restauración de servicios de directorio (**DSRM**).
5. **No** crear la delegación del servidor DNS.
6. Nombre NetBIOS: `LABEZE`.
7. **Siguiente → Instalar**. El servidor se reinicia al finalizar.

## 5. Crear el servidor de archivos

### 5.1 Instalar los roles de archivos

1. **Administrar → Agregar roles y características**.
2. Desplegar **Servicios de archivo y almacenamiento** y seleccionar:
   - Administrador de recursos del servidor de archivos
   - Espacios de nombres DFS
   - Replicación DFS
   - Servidor de destino iSCSI
3. **Siguiente → Instalar**. Al terminar, cerrar el asistente.

![Roles de servicios de archivo]

### 5.2 Crear el recurso compartido

> 💡 **Importante:** el servidor de archivos debe estar en un **disco distinto** al de la instalación de Windows Server 2022. Agregar un segundo disco virtual a la VM si hace falta. Esto es recomendable para evitar posibles problemas con el disco donde se instalo el windows server. 

1. En el Administrador del servidor: **Panel → Servicios de archivos y de almacenamiento → Recursos compartidos**.

   ![Recursos compartidos]

2. **Tareas → Nuevo recurso compartido**.
3. Elegir **Recurso compartido SMB: rápido**.
4. Seleccionar el servidor y luego la unidad (el disco secundario).
5. Nombre del recurso compartido: `File server`, más una descripción opcional.

   ![Nombre del recurso compartido]

6. Marcar **Habilitar la enumeración basada en el acceso**.
7. Revisar los permisos. Se pueden modificar; en este laboratorio se dejaron por defecto.
8. **Siguiente → Crear**.

## 6. Conectar un cliente Windows 11

1. En el equipo con Windows 11, abrir el recurso `File server` desde la red.
2. Clic derecho sobre **File server → Conectar a unidad de red**.
3. Marcar **Conectar de nuevo al iniciar sesión**.
4. Finalizar. El recurso queda disponible como una unidad de red.

---

## Resumen de la configuración

| Elemento | Valor |
| Sistema operativo | Windows Server 2022 Standard (Desktop Experience) |
| Hipervisor | VirtualBox |
| Dominio / Bosque | `Labeze.com` |
| NetBIOS | `LABEZE` |
| Recurso compartido | `File server` (SMB) |
| Cliente de prueba | Windows 11 |
