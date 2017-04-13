<properties
   pageTitle="Gestione dei ruoli di Azure cloud services progetti con Visual Studio | Microsoft Azure"
   description="Informazioni su come aggiungere nuovi ruoli per il progetto di servizio cloud Azure o rimuovere i ruoli esistenti utilizzando Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="managing-roles-in-the-azure-cloud-services-projects-with-visual-studio"></a>Gestione dei ruoli nei progetti di servizi cloud Azure con Visual Studio

Dopo aver creato il progetto di servizio cloud Azure, è possibile aggiungere nuovi ruoli o rimuovere ruoli esistenti. È anche possibile importare un progetto esistente e convertirla in un ruolo. Ad esempio, è possibile importare un'applicazione web ASP.NET e impostarlo come un ruolo web.

## <a name="adding-or-removing-roles"></a>Aggiunta o rimozione di ruoli

**Per aggiungere un ruolo**

In **Esplora soluzioni**, aprire il menu di scelta rapida per il nodo **ruoli** di un progetto di servizio cloud e scegliere **Aggiungi**. È possibile selezionare un ruolo web esistente o lavoro dalla soluzione corrente o creare un nuovo progetto di ruolo web o lavoro. Oppure selezionare un progetto appropriato, ad esempio un progetto di applicazione web ASP.NET e associare a un progetto di ruolo.

**Per rimuovere un'associazione ruolo**

Il nodo **ruoli** del progetto di servizio cloud in Esplora soluzioni, aprire il menu di scelta rapida del ruolo da rimuovere e scegliere **Rimuovi**.

## <a name="removing-and-adding-roles-in-your-cloud-service"></a>Rimuovere e aggiungere ruoli del servizio cloud

Se si decide di aggiungere il ruolo al progetto rimuovere un ruolo dal progetto di servizio cloud, vengono aggiunti solo la dichiarazione di ruolo e attributi di base, ad esempio le informazioni di diagnostica e i punti finali. Nessun risorse aggiuntive o riferimenti vengono aggiunti al file ServiceDefinition.csdef o il file ServiceConfiguration. cscfg. Se si desidera aggiungere queste informazioni, sarà necessario aggiungere manualmente in questi file.

Ad esempio, è possibile rimuovere un ruolo di servizio web e in seguito si decide di aggiungere questo ruolo in una soluzione. Se si esegue questa operazione, si verificherà un errore. Per evitare questo errore, è necessario aggiungere il `<LocalResources>` elemento visualizzato nel seguente XML nel file ServiceDefinition.csdef. Utilizzare il nome del ruolo di servizio web aggiunti al progetto come parte dell'attributo nome per il **<LocalStorage>** elemento. In questo esempio il nome del ruolo di servizio web è **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare i ruoli in Visual Studio leggendo [Configura i ruoli per un servizio Cloud di Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
