<properties
   pageTitle="Guida introduttiva a modelli privati | Microsoft Azure"
   description="Aggiungere, gestire e condividere i modelli personali tramite il portale di Azure, CLI Azure o PowerShell."
   services="marketplace-customer"
   documentationCenter=""
   authors="VybavaRamadoss"
   manager="asimm"
   editor=""
   tags="marketplace, azure-resource-manager"
   keywords=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="vybavar"/>

# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Guida introduttiva a modelli personali nel portale di Azure

Un modello di [Gestione risorse di Azure](../resource-group-authoring-templates.md) è un modello dichiarativo utilizzato per definire la distribuzione. È possibile definire le risorse per la distribuzione per una soluzione e specificare i parametri e variabili che consentono di valori di input per ambienti diversi. Il modello è composto da JSON ed espressioni che è possibile utilizzare per creare i valori per la distribuzione.

È possibile utilizzare la nuova funzionalità di **modelli** nel [Portale di Azure](https://portal.azure.com) insieme al provider di risorse **Microsoft.Gallery** come estensione di [Azure Marketplace](https://azure.microsoft.com/marketplace/) per consentire agli utenti di creare, gestire e distribuire modelli personali da una raccolta personale.

In questo documento viene descritto come aggiungere, gestire e condividere un privato **modello** tramite il portale di Azure.

## <a name="guidance"></a>Guida

I suggerimenti seguenti consentono di sfruttare al meglio **i modelli** quando si lavora con le soluzioni:

- Un **modello** è una risorsa incapsulante che contiene un modello di Manager delle risorse e metadati aggiuntivi. Comportamento molto simile a un elemento di Marketplace. La differenza fondamentale è che si tratti di un elemento privato anziché gli elementi Marketplace pubblici.
- La raccolta di **modelli** è utile per gli utenti che hanno l'esigenza di personalizzare le distribuzioni.
- **Modelli** è adatto per gli utenti che hanno l'esigenza di un repository semplice all'interno di Azure.
- Iniziare con un modello di gestione risorse esistente. Modelli disponibili nella [github](https://github.com/Azure/azure-quickstart-templates) o [Esporta modello](../resource-manager-export-template.md) da un gruppo di risorse esistente.
- **Modelli** sono collegati all'utente che li pubblica. Il nome dell'autore è visibile a chiunque abbia accesso in lettura.
- **Modelli** sono risorse Manager delle risorse e non è possibile rinominare una volta pubblicato.

## <a name="add-a-template-resource"></a>Aggiungere una risorsa modello

Esistono due modi per creare un **modello** della risorsa nel portale di Azure.

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>Metodo 1: Creare una nuova risorsa modello da un gruppo di risorse in esecuzione

1. Passare a un gruppo di risorse esistente nel portale di Azure. In **Impostazioni**, scegliere **Esporta modello** .
2. Dopo aver esportato il modello di Manager delle risorse, utilizzare il pulsante **Salva modello** salvarlo all'archivio **modelli** . Trovare tutti i dettagli per Esporta modello [qui](../resource-manager-export-template.md).
<br /><br />
![Esportazione di gruppo di risorse](media/rg-export-portal1.PNG)  <br />

3. Selezionare il pulsante di comando **Salva come modello** .
<br /><br />

4. Immettere le informazioni seguenti:

    - Nome: nome dell'oggetto modello (Nota: questo è il nome di un Manager delle risorse Azure in base a. Tutte le restrizioni di denominazione valide e non può essere modificato dopo averlo creato).
    - Descrizione: breve riepilogo sul modello.

    ![Salva modello](media/save-template-portal1.PNG)  <br />

5. Fare clic su **Salva**.

    > [AZURE.NOTE] E il modello Esporta Mostra notifiche quando il modello di gestione risorse esportato è presenti errori, ma sarà comunque possibile salvare il modello di Manager delle risorse per i modelli. Assicurarsi di selezionare e risolvere i problemi di modello qualsiasi Manager delle risorse prima di ridistribuire il modello di gestione risorse esportato.

### <a name="b-method-2--add-a-new-template-resource-from-browse"></a>B. Metodo 2: Aggiungere una nuova risorsa modello di Sfoglia

È anche possibile aggiungere un nuovo **modello** da zero utilizzando la + Aggiungi pulsante di comando in **Sfoglia > modelli di**. È necessario specificare un nome, descrizione e il modello di gestione risorse JSON.

![Aggiungi modello](media/add-template-portal1.PNG)  <br />

> [AZURE.NOTE] Microsoft.Gallery è che provider di Azure risorse in base a un Tenant. Il modello della risorsa è collegata all'utente che ha creato. Non è associato ad alcuna sottoscrizione specifico. Una sottoscrizione deve essere scelto solo quando si distribuisce un modello.

## <a name="view-template-resources"></a>Risorse di modelli di visualizzazione

È possibile visualizzare tutti i **modelli** disponibili **Sfoglia > modelli di**. Sono inclusi **i modelli** creati e quelli condivisi con l'utente con diversi livelli di autorizzazioni. Ulteriori dettagli nella sezione [controllo di accesso](#access-control-for-a-tenant-resource-provider) .

![Modello di visualizzazione](media/view-template-portal1.PNG)  <br />

È possibile visualizzare i dettagli di un **modello** , fare clic su un elemento nell'elenco.

![Modello di visualizzazione](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>Modificare una risorsa modello

È possibile avviare il flusso di modifica per un **modello** da destra facendo clic sulla voce nell'elenco o facendo clic sul pulsante di comando di modifica.

![Modifica modello](media/edit-template-portal1a.PNG)  <br />

È possibile modificare la descrizione o testo modello Manager delle risorse. È possibile modificare il nome, poiché si tratta di un nome di risorsa Manager delle risorse. Quando si modifica il Manager delle risorse modello JSON si eseguirà la convalida per assicurarsi che sia JSON valido. Scegliere **OK** e quindi **Salva** per salvare il modello aggiornato.

![Modifica modello](media/edit-template-portal2a.PNG)  <br />

Dopo aver salvato il **modello** verrà visualizzata una notifica di conferma.

![Modifica modello](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>Distribuire un modello della risorsa

È possibile distribuire un **modello** che si dispone delle autorizzazioni di **lettura** in. Il flusso di distribuzione avvia standard e di distribuzione il modello di Azure. Compilare i valori per i parametri del modello Manager delle risorse procedere con la distribuzione.

![Distribuire modello](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>Condividere una risorsa modello

Una risorsa **modello** può essere condivisi con i colleghi. La condivisione è simile alla [assegnazione di ruolo per tutte le risorse in Azure](../active-directory/role-based-access-control-configure.md). Il proprietario del **modello** fornisce le autorizzazioni agli altri utenti che possono interagire con una risorsa modello. La persona o un gruppo di utenti che condividono il **modello** con saranno in grado di visualizzare il modello di Manager delle risorse e le proprietà della raccolta.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Controllo di accesso per le risorse Microsoft.Gallery

Ruolo | Autorizzazioni
---|----
Proprietario | Consente di controllo completo per il modello della risorsa inclusi Condividi
Utilità per la lettura | Consente di lettura ed Execute(Deploy) sulla risorsa modello
Per i collaboratori | Consente l'autorizzazione di modifica ed Elimina sul modello della risorsa. Utente non è possibile condividere il modello con altri utenti

Selezionare **condivisione** sull'elemento Sfoglia facendo clic su a destra o su e il visualizzazione di un elemento specifico. Consente di avviare un'esperienza di condivisione.

![Modello di condivisione](media/share-template-portal1a.png)  <br />

 È ora possibile scegliere un ruolo e un utente o gruppo per consentire l'accesso a un **modello**specifico. Ruoli disponibili sono proprietario, utilità per la lettura e collaboratori. Ulteriori dettagli nella sezione [controllo di accesso](#access-control-for-a-tenant-resource-provider) .

![Modello di condivisione](media/share-template-portal2b.png)  <br />

![Modello di condivisione](media/share-template-portal3b.png)  <br />

Fare clic su **Seleziona** , quindi **Ok**. È ora possibile visualizzare gli utenti o gruppi che aggiunti alla risorsa.

![Modello di condivisione](media/share-template-portal4b.png)  <br />

> [AZURE.NOTE] Un modello può essere condivisa solo con utenti e gruppi nello stesso tenant di Azure Active Directory. Se si condivide un modello con un indirizzo di posta elettronica che non si trova il tenant, un invito verrà inviato la richiesta di aggiungersi al tenant come guest.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla creazione di modelli di Manager delle risorse, vedere [modelli di creazione condivisa](../resource-group-authoring-templates.md)
- Per comprendere le funzioni che è possibile usare in un modello di Manager delle risorse, vedere [funzioni Template](../resource-group-template-functions.md)
- Per ulteriori informazioni sulla creazione di modelli, vedere [procedure consigliate per la progettazione di modelli di gestione risorse di Azure](../best-practices-resource-manager-design-templates.md)
