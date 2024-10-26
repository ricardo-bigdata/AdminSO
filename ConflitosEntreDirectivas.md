# Conflitos entre directivas

Cando se aplican directivas en conflito, como a que prohibe instalacións e a que outorga permisos de administrador/a local, o comportamento adoita depender das prioridades específicas de cada directiva:

1. **Directiva "Impedir instalacións de usuario/a"**: esta directiva normalmente reside en **Configuración do usuario > Políticas > Plantillas administrativas > Sistema > Instalación de paquetes** e está deseñada para impedir que os usuarios poidan instalar software independentemente dos seus permisos administrativos. Esta é unha política restritiva que bloquea instalacións de forma explícita e adoita prevalecer sobre os permisos locais.

2. **Directiva "Engadir privilexios de administrador/a local"**: a configuración mediante **Configuración do equipo > Preferencias > Configuración de control do panel > Grupos locais e usuarios** engade os/as usuarios/as ó grupo de Administradores locais, dándolles permisos administrativos no equipo. Este acceso outórgalles a capacidade de instalar software, pero non pode anular unha política explícita que prohibe instalacións a nivel de dominio.

### En caso de conflito

En xeral, **prevalece a directiva que prohibe instalacións**. En escenarios de políticas en conflito, Windows aplica o principio de "negar antes de permitir", polo que calquera restrición específica (como prohibir instalacións) prevalece sobre as configuracións de permisos amplos (como a pertenza ao grupo de Administradores locais).

### Recomendacións

Se queres que os/as usuarios/as poidan instalar programas pero con restricións, podes modifica-las políticas de instalación de software para permitir instalacións asinadas ou xestionadas en lugar de bloquear completamente a capacidade de instalar. Outra opción é **dividi-las directivas en diferentes OUs se queres aplicar configuracións distintas segundo os grupos de usuarios/as**.

![Directivas en OUs distintas para evitar conflitos](/conflitosGPO.png "Importante a orde da aplicación das directivas e o seu reparto en OUs")
