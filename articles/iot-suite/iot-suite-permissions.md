<properties
  pageTitle="Famiglia di prodotti IoT Azure e Azure Active Directory | Microsoft Azure"
  description="Descrive come Azure IoT Suite usi Azure Active Directory per gestire le autorizzazioni."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="10/24/2016"
  ms.author="araguila"/>
  
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Autorizzazioni per il sito azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Cosa accade quando si accede

Quando accede alla [azureiotsuite.com][lnk-azureiotsuite], il sito determina i livelli di autorizzazione basato sul tenant di Azure Active Directory (AAD) attualmente selezionato e Azure abbonamento.

1.  Il sito prima di tutto ne comuni quali tenant AAD a cui si appartiene per popolare l'elenco dei tenant visualizzato accanto al nome dell'utente ha effettuato l'accesso. Al momento è possibile che il sito ottenere solo i token utente per un tenant alla volta. Di conseguenza, quando si passa a un tenant diversi utilizzando l'elenco a discesa nell'angolo superiore destro, il sito nuovamente consente l'accesso al tenant per ottenere i token per tenant.

2.  Successivamente, il sito ne da Azure le sottoscrizioni che sono stati associati a tenant selezionato. Risulta abbonamenti disponibili quando si crea una nuova soluzione preconfigurata.

3.  Infine, il sito recupera tutte le risorse nelle sottoscrizioni e i gruppi di risorse contrassegnate come soluzioni preconfigurate e compila i riquadri nella home page.

Nelle sezioni seguenti vengono descrivano i ruoli che controllano l'accesso alle soluzioni preconfigurate.

## <a name="aad-roles"></a>Ruoli AAD

Ruoli AAD le soluzioni di provisioning preconfigurato possibilità di controllare e gestire gli utenti in una soluzione preconfigurato.

È possibile trovare ulteriori informazioni sui ruoli di amministratore in AAD in [assegnazione di ruoli di amministratore in Azure Active Directory][lnk-aad-admin], ma questo articolo analizza principalmente l' **Amministratore globale** e i ruoli di **Utente dominio** usato per le soluzioni preconfigurate.

**Amministratore globale:** Possono essere presenti molti amministratori globali per ogni tenant AAD. Quando si crea un tenant AAD, l'utente per impostazione predefinita amministratore globale del tenant. L'amministratore globale può effettuare il provisioning di una soluzione preconfigurata e viene assegnato un ruolo di **amministratore** per l'applicazione all'interno di tenant di AAD. Tuttavia, se un altro utente nello stesso tenant AAD crea un'applicazione, il ruolo predefinito che abbia concesso o meno l'amministratore globale è **Solo leggere IMPLICITI**. Gli amministratori globali possono assegnare ruoli per applicazioni tramite il [portale classica Azure][lnk-classic-portal].

**Utente dominio:** Possono essere presenti molti utenti/membri del dominio per ogni tenant AAD. Un utente del dominio può effettuare il provisioning di una soluzione preconfigurata attraverso [azureiotsuite.com] [ lnk-azureiotsuite] sito. Il ruolo predefinito che sono concesse per l'applicazione che la preparazione è **amministratore**. Possono creare un'applicazione [azure iot-remota monitoraggio] mediante lo script build.cmd[ lnk-rm-github-repo] o [azure iot-stima-manutenzione] [ lnk-pm-github-repo] repository, ma il ruolo predefinito dispongono di è **Implicito sola lettura**, se non dispone dell'autorizzazione per assegnare ruoli. Se un altro utente nel tenant di AAD crea un'applicazione, vengono assegnati al ruolo di **Sola lettura implicito** per impostazione predefinita per tale applicazione. Che non hanno la possibilità di assegnare i ruoli per applicazioni. pertanto non è possibile aggiungere utenti o ruoli per gli utenti per un'applicazione anche se è stato effettuato il provisioning.

**Utente Guest/Guest:** Possono essere presenti molti utenti/ospiti guest per ogni tenant AAD. Gli utenti guest hanno un numero limitato di diritti nel tenant di AAD. Di conseguenza, gli utenti guest non è possibile effettuare il provisioning di una soluzione preconfigurata nel tenant di AAD.

Per ulteriori informazioni, vedere le risorse seguenti:

- [Creare o modificare utenti in Azure Active Directory][lnk-create-edit-users]
- [Assegnare i ruoli di App in AAD][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Ruoli di amministratore di abbonamento Azure

I ruoli di amministratore Azure controllano la possibilità di eseguire il mapping di un abbonamento a Azure a un tenant di Active Directory.

È possibile trovare altre informazioni sui ruoli CO-amministratore Azure, amministratore del servizio e amministratore dell'Account vedere l'articolo [su come aggiungere o modificare CO-amministratore Azure, amministratore del servizio e amministratore dell'Account][lnk-admin-roles].

## <a name="application-roles"></a>Ruoli applicazione

Ruoli applicazione controllano l'accesso ai dispositivi in soluzione preconfigurato.

Esistono due definite e uno dei ruoli implicito definiti nell'applicazione creata durante il provisioning di una soluzione preconfigurata.

-   **Amministratore:** Controllo completo per aggiungere, gestire e rimuovere i dispositivi

-   **Di sola lettura:** È possibile visualizzare i dispositivi

-   **Implicito solo lettura:** È diverso da quello di sola lettura, ma viene concesso a tutti gli utenti del tenant di AAD. Questa operazione è stata eseguita per comodità in fase di sviluppo. È possibile rimuovere questo ruolo modificando [RolePermissions.cs] [ lnk-resource-cs] file di origine.

### <a name="changing-application-roles-for-a-user"></a>Modificare i ruoli di applicazione di un utente

È possibile utilizzare la procedura seguente per rendere un utente in Active Directory un amministratore della soluzione preconfigurato.

È necessario essere un amministratore globale AAD per modificare i ruoli per un utente:

1. Passare al [portale classica Azure][lnk-classic-portal].

2. Selezionare **Active Directory**.

3. Fare clic sul nome del tenant di AAD (si tratta della directory che si sceglie azureiotsuite.com quando viene completato il provisioning della soluzione).

4. Fare clic su **applicazioni**.

5. Fare clic sul nome dell'applicazione che corrisponde al nome del soluzione preconfigurato. Se non è visibile l'applicazione nell'elenco, passare all'elenco a discesa **Mostra** fino a **applicazioni appartiene alla società** e fare clic sul segno di spunta.

7. Fare clic su **utenti**.

8. Selezionare l'utente che si desidera cambiare ruoli.

9. Fare clic su **Assegna** e selezionare il ruolo (ad esempio **amministratore**) che si desidera assegnare all'utente, fare clic sul segno di spunta.

## <a name="faq"></a>Domande frequenti

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-do-this"></a>Si è amministratori del servizio e desidera modificare il mapping di directory tra l'abbonamento e un tenant AAD specifico. Come effettuare questa operazione?

1. Passare al [portale classica Azure][lnk-classic-portal], fare clic su **Impostazioni** nell'elenco dei servizi sul lato sinistro.

2. Selezionare l'abbonamento a cui a che si desidera modificare il mapping di directory.

3. Fare clic su **Modifica Directory**.

4. Selezionare la **Directory** si desidera utilizzare nell'elenco a discesa. Fare clic sulla freccia avanti.

5. Confermare il mapping di directory e interessate coamministratori. Si noti che, se si spostano da un'altra directory, vengono rimossi tutti gli CO-amministratori dalla directory originale.

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Si è un utente/membro di dominio nel tenant AAD e ho creato una soluzione preconfigurata. Come ottenere assegnato un ruolo per l'applicazione?

Contattare l'amministratore globale per assegnare all'amministratore globale nel tenant AAD per ottenere le autorizzazioni per assegnare ruoli agli utenti se stessi oppure contattare l'amministratore globale per assegnare un ruolo. Se si desidera modificare la soluzione preconfigurata tenant AAD è stata distribuita per vedere la domanda successiva.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Come si passa il tenant AAD che la soluzione di preconfigurato monitoraggio remoto e applicazione vengono assegnate?

È possibile eseguire una distribuzione cloud da <https://github.com/Azure/azure-iot-remote-monitoring> e ridistribuire con un tenant AAD appena creato. Poiché si sta per impostazione predefinita un amministratore globale quando si crea un nuovo tenant AAD, si avrà accesso per l'aggiunta di utenti e assegnazione di ruoli a tali utenti.

1. Creare una nuova directory AAD nel [portale di gestione di Azure][lnk-classic-portal].

2. Passare a <https://github.com/Azure/azure-iot-remote-monitoring>.

3. Eseguire `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (ad esempio `build.cmd cloud debug myRMSolution`)

4. Quando richiesto, è possibile impostare **tenantid** per il nuovo tenant creato anziché il tenant precedente.


### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Desidera modificare un servizio amministratore o CO-durante l'accesso con un account organizzativo

Vedere l'articolo del supporto tecnico [Modifica amministratore di servizio e CO-durante l'accesso con un account organizzative][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Perché viene visualizzato questo errore? "L'account non dispone delle autorizzazioni appropriate per creare una soluzione. Rivolgersi all'amministratore dell'account o provare con un altro account."

Esaminiamo il diagramma seguente:

![][img-flowchart]

> [AZURE.NOTE] Se si continua a essere visualizzato l'errore dopo la convalida si sono un amministratore globale nel tenant AAD e un amministratore di creazione dell'abbonamento, all'amministratore dell'account rimuovere l'utente e riassegnare le autorizzazioni necessarie in questo ordine: aggiungere l'utente come amministratore globale e quindi aggiungere l'utente come co-amministratore dell'abbonamento Azure. Se i problemi persistono, contattare [Guida e supporto tecnico][lnk-help-support].

**Perché viene visualizzato questo messaggio di errore quando si ha un abbonamento Azure?** *Per creare soluzioni preconfigurate è necessario un abbonamento a Azure. È possibile creare un account di valutazione gratuito in pochi minuti.*

Se si è certi di che disporre di un abbonamento Azure, convalidare il tenant mapping per l'abbonamento e verificare che il corretto tenant sia selezionato nella casella di riepilogo. Se è stato convalidato tenant desiderato sia corretto, seguire il diagramma precedente e convalidare il mapping di abbonamento e il tenant AAD.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come IoT famiglia di prodotti, vedere come è possibile [personalizzare una soluzione preconfigurata][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://azure.microsoft.com/documentation/articles/active-directory-application-manifest/
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
