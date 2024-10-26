# Conflitos entre directivas

Cando se aplican directivas en conflito, como a que prohibe instalacións e a que outorga permisos de administrador/a local, o comportamento adoita depender das prioridades específicas de cada directiva:

1. **Directiva "Impedir instalacións de usuario/a"**: esta directiva normalmente reside en **Configuración do usuario > Políticas > Plantillas administrativas > Sistema > Instalación de paquetes** e está deseñada para impedir que os usuarios poidan instalar software independentemente dos seus permisos administrativos. Esta é unha política restritiva que bloquea instalacións de forma explícita e adoita prevalecer sobre os permisos locais.

2. **Directiva "Engadir privilexios de administrador/a local"**: a configuración mediante **Configuración do equipo > Preferencias > Configuración de control do panel > Grupos locais e usuarios** engade os/as usuarios/as ó grupo de Administradores locais, dándolles permisos administrativos no equipo. Este acceso outórgalles a capacidade de instalar software, pero non pode anular unha política explícita que prohibe instalacións a nivel de dominio.

### En caso de conflito

En xeral, **prevalece a directiva que prohibe instalacións**. En escenarios de políticas en conflito, Windows aplica o principio de "negar antes de permitir", polo que calquera restrición específica (como prohibir instalacións) prevalece sobre as configuracións de permisos amplos (como a pertenza ao grupo de Administradores locais).

### Recomendacións

Se queres que os/as usuarios/as poidan instalar programas pero con restricións, podes modifica-las políticas de instalación de software para permitir instalacións asinadas ou xestionadas en lugar de bloquear completamente a capacidade de instalar. Outra opción é **dividi-las directivas en diferentes OUs se queres aplicar configuracións distintas segundo os grupos de usuarios/as**.

![Directivas en OUs distintas para evitar conflitos](/UD1-ActiveDirectory/imaxes/conflitosGPO.png "Importante a orde da aplicación das directivas e o seu reparto en OUs")

---
Hai unha serie de comprobacións que poden axudar a determinar se realmente non hai políticas aplicadas ou se hai un problema que impide a súa aplicación correcta.

En caso de conflito entre dúas directivas de grupo (GPOs) que afectan ó/á mesmo/a usuario/a ou equipo e teñen configuracións contraditorias, existen algúns principios xerais sobre cal delas prevalece:

1. **Orde de aplicación das directivas**
As políticas de grupo aplícanse nesta orde:
   - Local
   - De sitio
   - De dominio
   - De unidade organizativa (OU), sendo as sub-OU aplicadas despois das OU superiores.

   **A última política aplicada é a que ten prioridade**
Por exemplo, se unha política de nivel de dominio lle outorga privilexios de administrador/a e unha política de OU lle prohibe instalar software, a política da OU (máis próxima ao obxecto) sería a que prevalecería.

3. **Prioridade entre configuracións de “Computadora” e “Usuario”**
   - Se unha directiva de computador prohíbe instalar software e outra directiva de usuario/a permite a instalación, normalmente prevalece a directiva de computador. **As políticas de configuración de computador aplícanse antes que as de usuario/a e son máis restritivas** en cuestións de seguridade.

4. **Configuracións explícitas de permisos de seguridade**
Dentro das GPOs, pódese definir permisos de seguridade para permitir ou denegar o acceso a certas accións (como instalar software). As directivas que conteñen permisos de **Denegación explícita prevalecen sobre as que Permiten acceso**.

6. **Herdanza e bloqueos**
As GPOs herdadas de OU superiores aplícanse a non ser que se bloqueen, ou que unha OU inferior aplique unha configuración que sobrepase as herdadas.

Para determinar cal de dúas directivas prevalecería nun caso concreto, pódese:

   - **Usar a ferramenta `gpresult`** para ver as políticas efectivas sobre o/a usuario/a ou equipo:
     ```shell
     gpresult /r
     ```
   - **Revisar conflitos específicos** usando o Editor de Políticas de Grupo ou a Consola de Administración de Políticas de Grupo (GPMC) en Windows Server.

---
Ademais de `gpresult /r`, que amosa obxectos de directiva de grupo (GPOs) aplicados, existen outras formas de verificar cales realmente se aplicaron ou se hai un problema coa aplicación das políticas:

1. **Revisar o Visor de Eventos**:
   - Abri-lo **Visor de Eventos** (`eventvwr.msc`).
   - Navegar a **Registros de aplicacións e servizos > Microsoft > Windows > GroupPolicy > Operational**.
   - Aquí pódese ver eventos relacionados coa aplicación de políticas de grupo, incluídos erros ou avisos que indiquen problemas na aplicación das GPOs.

2. **Forza-la actualización de políticas**:
   - Forza-la actualización das políticas usando o comando:
     ```shell
     gpupdate /force
     ```
   - Despois de executa-lo comando, revisar de novo con `gpresult /r` para ver se aparecen as políticas aplicadas. 

3. **Verifica-la conectividade co Controlador de Dominio**:
   - A aplicación das GPOs require conectividade co controlador de dominio. Pódese comproba-la conexión executando:
     ```shell
     nltest /dsgetdc:<dominio>
     ```
   - Se hai problemas de conectividade, quizais sexa necesario resolver estes problemas para que as políticas se apliquen correctamente.

4. **Revisar permisos de seguridade**:
   - Hai que asegurarse de que o/a usuario/a ou equipo teñen permisos de lectura e aplicación na GPO. Na consola de **Group Policy Management** (GPMC), pódense revisa-las configuracións de seguridade de cada GPO en cuestión para comprobar que os permisos son correctos.

5. **Comprobar filtrado de GPOs mediante WMI**:
   - Se a GPO está filtrada mediante WMI (instrumentación de administración de Windows), pode que non se aplique en función dos resultados da consulta WMI. Pódese revisar isto na GPMC, baixo a sección de **Scope** da GPO.

---
Ademais do comando `gpresult` e o Visor de eventos, hai un par de localizacións onde pódese verificar que políticas de grupo se aplicaron efectivamente. Verificar estes rexistros e a caché local pode axudar a entender mellor que GPOs se aplicaron e se algunha delas puido ser ignorada debido a erros ou conflitos.

1. **Directorio local de políticas**
En cada equipo, hai unha copia en caché das políticas de grupo aplicadas, o que permite verifica-la configuración que se aplicou.
   - **Localización**: `C:\Windows\System32\GroupPolicy`
   - Este directorio contén subdirectorios de configuración para políticas de **usuario** e de **equipo**. O ficheiro `Registry.pol` dentro deste directorio almacena as políticas efectivas.

3. **Rexistro de políticas de grupo aplicadas**
   - **Rexistro**: Accede ao Editor do Rexistro con `regedit` e revisa:
     - Para políticas de equipo: `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\History`
     - Para políticas de usuario: `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Group Policy\History`

   Aquí haberá unha lista de GUIDs que corresponden aos GPO aplicados. Pódese identificar cada GPO co GUID correspondente revisándoo en **GPMC** (Group Policy Management Console).

4. **Visor de eventos (Outras entradas)** 
   - Ademais de revisa-la categoría **Aplicación** e **Sistema**, pódese buscar en **Microsoft > Windows > GroupPolicy > Operational** para ver detalles sobre a aplicación de políticas de grupo en tempo real.

