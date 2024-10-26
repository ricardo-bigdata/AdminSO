# AdminSO
Administración de Sistemas Operativos
Atención: debes de ter creada unha unidade organizativa, un equipo introducido nesa UO, e un grupo ou contas do dominio tamén introducidos nesa UO. É moi importante que ademais das contas, o equipo estea na UO na que se aplicará a GPO.

Para engadir usuarios/as de dominio ó grupo de administradores/as local en varios equipos mediante unha Política de Grupo (GPO), podes seguir estes pasos:

1. **Abre o Editor de Xestión de Políticas de Grupo**:
   - Vai a **Inicio** e abre **Xestión de Políticas de Grupo** (Group Policy Management).

2. **Crea ou edita unha GPO**:
   - Na árbore de consolas, navega ata o dominio ou Unidade Organizativa (OU) onde se atopan os equipos ós que queres aplica-la política.
   - Fai clic dereito e selecciona **Crear unha nova GPO neste dominio** ou selecciona unha existente.
   - Dálle un nome descritivo, por exemplo, “Engadir usuarias de dominio ó grupo Administradores local”.

3. **Configura a preferencia de grupos locais**:
   - Fai clic dereito na GPO creada e selecciona **Editar**.
   - Vai a **Configuración do equipo** > **Preferencias** > **Configuración do Panel de control** > **Grupos locais e usuarios** (Local Users and Groups).
   - Fai clic dereito en **Grupos** e selecciona **Novo** > **Grupo Local** (Local Group).

4. **Configura o grupo local “Administradores”**:
   - No campo **Acción**, selecciona **Actualizar** (Update).
   - No campo **Nome do grupo**, selecciona ou introduce **Administradores** (Administrators).
   - Na sección **Membros**, fai clic en **Agregar...**:
     - Selecciona **Usuario ou grupo**.
     - Introduce o nome de usuario/a ou grupo do dominio que queres engadir, no formato `DOMINIO\NomeUsuario-a` ou `DOMINIO\NomeGrupo`.
     - Podes engadir múltiples usuarios/as ou grupos nesta lista, se é necesario.
     - Fai clic en **Aceptar**.

5. **Aplica e vincula a GPO**:
   - Fai clic en **Aceptar** para gardar os cambios na configuración do grupo.
   - Volve a **Xestión de Políticas de Grupo** e vincula a GPO á OU onde están os equipos.

6. **Actualiza as políticas de grupo nos equipos**:
   - Os equipos afectados aplicarán automaticamente a nova configuración na seguinte actualización de políticas (normalmente en 90 minutos por defecto) ou no seguinte inicio de sesión.
   - Para actualiza-las políticas de inmediato, podes executa-lo comando `gpupdate /force` nos equipos.

Desta maneira, os/as usuarios/as ou grupos de dominio especificados terán privilexios de administrador/a local nos equipos configurados.

Se xa tiñas iniciada sesión cunha conta á que esperas que se aplique a GPO deberás reinicia-la sesión para que cargue correctamente o token cos seus novos privilexios.


Posibles problemas:

Idealmente, se un/unha usuario/a xa pertence ó grupo de **administradores locais** do equipo, debería poder realizar accións administrativas (como instalar software) coas súas propias credenciais sen necesidade de introducir outras credenciais adicionais. Non obstante, o UAC en Windows pode seguir solicitando a confirmación debido a certos controis de seguridade.

Para asegurarte de que as credenciais da conta funcionan como esperas, revisa as seguintes configuracións:

1. **Confirma a pertenza ó grupo de administradores/as locais**: Verifica que a conta está correctamente engadida ó grupo **Administradores** local. Para comprobalo, executa no equipo o comando:
   ```shell
   net localgroup Administradores
   ```
   ou en inglés:
   ```shell
   net localgroup Administrators
   ```
   A conta de usuario/a debería aparecer listada aquí.

2. **Comproba a configuración do UAC para usuarios/as administrativos**:
   - Asegúrate de que a política **Control de contas de usuario: Comportamento da elevación para administradores en Modo de aprobación de administrador** está configurada en **Solicitar consentimento só no escritorio seguro** ou **Elevar sen solicitar credenciais** se buscas eliminar esa segunda solicitude.

3. **Actualizar políticas de grupo**: Asegúrate de que todos os cambios se aplicaron con `gpupdate /force`.

Finalmente, proba de novo co usuario despois de aplicar estes cambios. Esta configuración debería permitir que un usuario do grupo de administradores locais use as súas propias credenciais sen que se lle soliciten outras adicionais ao realizar accións administrativas.
