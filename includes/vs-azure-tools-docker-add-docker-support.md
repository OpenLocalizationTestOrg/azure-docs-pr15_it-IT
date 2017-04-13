1. In Visual Studio **Esplora soluzioni**fare clic sul progetto e selezionare **Aggiungi > supporto Docker** dal menu di scelta rapida.

    ![Aggiungere il menu di scelta rapida Docker supporto](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)

1. Aggiunta del supporto Docker a un Core ASP.NET progetto web comporta l'aggiunta di diversi file correlati Docker aggiunti al progetto, inclusi file Docker scrive, script di Windows PowerShell di distribuzione e file di proprietà Docker. 

    ![File docker aggiunti al progetto](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)
    
> [AZURE.NOTE]Se Usa [Docker per versione Beta di Windows](https://beta.docker.com), aprire Properties\Docker.props, rimuovere il valore predefinito e riavviare Visual Studio per il valore per rendere effettive.
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```
