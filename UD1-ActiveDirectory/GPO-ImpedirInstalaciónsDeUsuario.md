# Configuración que impida a instalación de novas aplicacións en usuarios/as ou equipos da OU especificada, evitando instalacións de programas sen permisos específicos.

Para impedi-la instalación de novas aplicacións mediante **Política de Grupo (GPO)**, pódense seguir estes pasos. Esta configuración aplicarase ós/ás usuarios/as ou equipos dunha Unidade Organizativa específica:

### 1. Configurar a GPO para restrinxir instalacións de software
   - Abrir **Group Policy Management Console (GPMC)** en **Windows Server**.
   - Navegar ata a Unidade Organizativa (OU) onde se atopan os/as usuarios/as ou equipos ós que se queira aplica-la restrición.
   - Facer clic dereito sobre a OU e seleccionar **Create a GPO in this domain, and Link it here...**. Darlle un nome descritivo, como "Restrinxir instalacións de software".
   - Facer clic dereito na nova GPO e seleccionar **Edit**.

### 2. Configuración da restrición de instalación de software
   #### Opción 1: Utilizar **Directiva de restrición de software**
   - Navega a **Computer Configuration > Policies > Windows Settings > Security Settings > Software Restriction Policies**.
   - Se as políticas de restrición de software non están configuradas, fai clic dereito en **Software Restriction Policies** e selecciona **New Software Restriction Policies**.
   - En **Enforcement**, selecciona que as políticas se apliquen a todos os programas, incluíndo os que son executados polos administradores.
   - En **Additional Rules**, podes configurar regras específicas para bloquear programas ou rutas de instalación comúns para evitar a instalación de aplicacións.

   #### Opción 2 (eu descartaríaa por parecerme DEMASIADO COMPLEXA): Configurar mediante **AppLocker** (en versións de Windows compatibles)
   - Navega a **Computer Configuration > Policies > Windows Settings > Security Settings > Application Control Policies > AppLocker**.
   - Expande **AppLocker** e verás catro tipos de regras: **Executable Rules**, **Windows Installer Rules**, **Script Rules**, e **Packaged app Rules**.
   - Activa as regras de AppLocker para **Executable Rules** e establece políticas que restrinxan a execución de instalacións de programas para usuarios/as non administradores/as..
   - Fai clic dereito en **Executable Rules** e selecciona **"Create New Rule"** para comeza-la configuración dunha nova regra.
   - Fai clic en **Create New Rule** e segue o asistente para bloquea-la instalación de software non permitido.
   - O asistente guiarate para definir permisos de acceso, seleccionar usuarios ou grupos, e establecer os criterios de restrición (por exemplo, por hash de ficheiro, ruta ou certificado de editor).

### 3. Usar a configuración de políticas para impedir instalacións de MSI
   - Navega a **Computer Configuration > Policies > Administrative Templates > Windows Components > Windows Installer**.
   - Configura as seguintes opcións:
      - **Disable Windows Installer**: Activa esta opción e selecciona **Always** para impedir a instalación de ficheiros MSI.
      - **Prohibit User Installs**: Activa esta opción para bloquear instalacións de software a nivel de usuario.

### 4. Aplicar e verificar a GPO
   - Garda e pecha a configuración da GPO.
   - Aplica a política aos equipos mediante o comando `gpupdate /force` en cada equipo ou agarda a que a GPO se aplique automaticamente.
   - Verifica que os usuarios non poidan instalar novas aplicacións probando unha instalación cunha conta de usuario ordinaria na OU afectada.

![Petición de credenciais ante unha tentativa de instalación de software por unha conta sen privilexios](/UD1-ActiveDirectory/imaxes/tentativaInstalacionSenPrivilexios.png "Petición de credenciais ante unha tentativa de instalación de software por unha conta sen privilexios")

---
*Unha alternativa máis sinxela, dende o meu punto de vista,* que ó estar a nivel de Windows Installer, non require un sistema tan complexo como AppLocker e, para moitos casos, resulta suficiente para bloquear instalacións é a seguinte.

A GPO **“Prohibir instalacións de usuario”** é unha alternativa efectiva e máis sinxela se só queres impedir a instalación de aplicacións en certos equipos ou usuarios sen necesidade de AppLocker. 

Esta GPO en **Configuración do equipo > Directivas > Plantillas administrativas > Componentes de Windows > Windows Installer > Prohibir instalacións de usuario** permite controlar o comportamento de instalación para os usuarios que non sexan administradores. Podes configuralo para restrinxir instalacións por completo ou permitir só a instalación de aplicacións específicas mediante MSI (Microsoft Installer) se precisas algún grao de flexibilidade.

### Opciones de configuración de "Prohibir instalacións de usuario"
Esta política permite varias opcións de configuración:
   - **Habilitada**: Impide todas as instalacións de Windows Installer.
   - **Non configurada**: permite todas as instalacións.
   - **Deshabilitada**: non aplica restricións específicas.

Tras configura-la GPO, lembra actualiza-las políticas de grupo nos equipos obxectivo executando **`gpupdate /force`**.
