<properties
    pageTitle="Configurare un nome di dominio per l'endpoint di spazio di archiviazione Blob | Microsoft Azure"
    description="Informazioni su come eseguire il mapping di un dominio utente personalizzate per l'endpoint di spazio di archiviazione Blob per un account di archiviazione Azure nel portale classica di Azure."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurare un nome di dominio personalizzato per l'endpoint di spazio di archiviazione Blob

## <a name="overview"></a>Panoramica

È possibile configurare un dominio personalizzato per l'accesso ai dati blob nell'account di archiviazione Azure. L'endpoint predefinito per l'archiviazione Blob è `<storage-account-name>.blob.core.windows.net`. Se si eseguire il mapping di un dominio personalizzato e un sottodominio, ad esempio **www.contoso.com** all'endpoint blob per l'account di archiviazione, quindi il utenti possono anche dati blob di accesso nell'account di archiviazione con tale dominio.

>[AZURE.IMPORTANT] Spazio di archiviazione Azure non supporta ancora HTTPS con domini personalizzati. Siamo consapevoli che i clienti che sono interessati a questa caratteristica e saranno disponibile nelle versioni future.

Esistono due modi per scegliere il dominio personalizzato per l'endpoint blob per l'account di archiviazione. Il modo più semplice consiste nel creare un record CNAME mapping il dominio personalizzato e sottodominio all'endpoint blob. Un record CNAME è una funzionalità DNS che esegue il mapping di un dominio di origine di un dominio di destinazione. In questo caso, il dominio di origine è personalizzato Domino o sottodominio, tenere presente che il sottodominio è sempre necessario. Il dominio di destinazione è endpoint del servizio Blob.

Il processo di mapping di un dominio personalizzato per l'endpoint blob, tuttavia, causando un breve periodo di tempo di inattività per il dominio mentre si sta registrando il dominio nel [Portale classica Azure](https://manage.windowsazure.com). Se il dominio personalizzato è attualmente in grado di supportare un'applicazione con un contratto di servizio (contratto di servizio) che è necessario alcun tempo di inattività, è possibile utilizzare il sottodominio Azure **asverify** per fornire un passaggio intermedio registrazione in modo che gli utenti saranno in grado di accedere al dominio durante il mapping avviene sistema DNS.

Nella tabella seguente mostra gli URL di esempio per l'accesso ai dati blob in un account di archiviazione denominato **mystorageaccount**. Il dominio personalizzato registrato per l'account di archiviazione è **www.contoso.com**:

Tipo di risorsa|Formati di URL
---|---
Account di archiviazione|**Predefinito URL:** http://mystorageaccount.blob.core.windows.net<p>**URL di dominio personalizzato:** http://www.contoso.com</td>
BLOB|**Predefinito URL:** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**URL di dominio personalizzato:** http://www.contoso.com/mycontainer/myblob
Contenitore principale|**URL predefinito:** http://mystorageaccount.blob.core.windows.net/myblob o http://mystorageaccount.blob.core.windows.net/$. radice/myblob<p>**URL di dominio personalizzato:** http://www.contoso.com/myblob o http://www.contoso.com/ radice/myblob di $

## <a name="register-a-custom-domain-for-your-storage-account"></a>Registrare un dominio personalizzato per l'account di archiviazione

Utilizzare questa procedura per registrare il proprio dominio personalizzato se non si dispone di preoccupazioni che temporaneamente non disponibile per gli utenti del dominio o se il dominio personalizzato, non è attualmente che ospita un'applicazione.

Se il dominio personalizzato è attualmente supporta un'applicazione che non è possibile specificare un tempo di inattività, utilizzare la procedura descritta nel <a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">registro un dominio personalizzato per l'account di archiviazione utilizzando il sottodominio asverify intermedia</a>.

Per configurare un nome di dominio personalizzato, è necessario creare un nuovo record CNAME con il registrar. Il record CNAME specifica un alias per un nome di dominio. In questo caso associa l'indirizzo del dominio personalizzato per l'endpoint di spazio di archiviazione Blob per l'account di archiviazione.

Ogni registrar dispone di un metodo simile ma leggermente diverso per specificare un record CNAME, ma il concetto è lo stesso. Si noti che molti pacchetti di registrazione di base dominio non offrono configurazione del DNS, in modo che potrebbe essere necessario aggiornare il pacchetto di registrazione dominio prima di poter creare il record CNAME.

1.  Nel [Portale classica di Azure](https://manage.windowsazure.com), passare alla scheda **spazio di archiviazione** .

2.  Nella scheda **archiviazione** fare clic sul nome dell'account di archiviazione per il quale si desidera eseguire il mapping di dominio personalizzato.

3.  Fare clic sulla scheda **Configura** .

4.  Nella parte inferiore dello schermo fare clic su **Manage Domain** per visualizzare la finestra di dialogo **Gestione dominio personalizzato** . Nel testo nella parte superiore della finestra di dialogo, verranno visualizzate informazioni su come creare il record CNAME. Per questa procedura, ignorare il testo che si intende il sottodominio **asverify** .

5.  Accedere al sito Web del registrar DNS e passare alla pagina per la gestione DNS. È possibile in una sezione, ad esempio **Nome di dominio**, **DNS**o **Gestione di nome Server**.

6.  Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME** **Alias**o **sottodomini**.

7.  Creare un nuovo record CNAME e fornire un alias di sottodominio, ad esempio **www** o **foto**. Quindi fornire un nome host, ossia endpoint del servizio Blob nel formato **mystorageaccount.blob.core.windows.net** (dove **mystorageaccount** è il nome del proprio account di archiviazione). Il nome host da utilizzare viene fornito automaticamente nel testo della finestra di dialogo **Gestione dominio personalizzato** .

8.  Dopo aver creato il record CNAME, tornare alla finestra di dialogo **Gestione dominio personalizzato** e immettere il nome del dominio personalizzato, inclusi il sottodominio nel campo **Nome di dominio personalizzato** . Ad esempio, se il dominio è **contoso.com** e il sottodominio è **www**, immettere **www.contoso.com**; Se il sottodominio **foto**, immettere **photos.contoso.com**. Si noti che il sottodominio necessario.

9. Fare clic sul pulsante **Registra** per registrare il proprio dominio personalizzato.

    Se la registrazione ha esito positivo, si verrà visualizzato il messaggio **è attivo il dominio personalizzato**. Gli utenti possono ora visualizza blob i dati nel dominio personalizzato, in modo che devono avere le autorizzazioni appropriate.

## <a name="register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain"></a>Registrare un dominio personalizzato per l'account di archiviazione utilizzando il sottodominio asverify intermedia

Utilizzare questa procedura per registrare personalizzato dominio se il dominio personalizzato è attualmente supporta un'applicazione con un contratto di servizio che richiede al non esserci tempi di inattività. Creazione di un record CNAME che punti da asverify. &lt;sottodominio&gt;. &lt;customdomain&gt; a asverify. &lt;storageaccount&gt;. blob.core.windows.net, è possibile pre-registrare il dominio con Azure. È possibile creare un secondo record CNAME che punti da &lt;sottodominio&gt;. &lt;customdomain&gt; a &lt;storageaccount&gt;. blob.core.windows.net, quindi il traffico verso un dominio personalizzato verrà indirizzato all'endpoint blob.

Il sottodominio asverify è un sottodominio speciale riconosciuto da Azure. Anteponendo **asverify** al proprio sottodominio sia autorizzato Azure facilmente riconoscibile il dominio personalizzato senza modificare i record DNS per il dominio. Una volta che si modifica il record DNS per il dominio, verrà associato all'endpoint blob con senza tempi di inattività.

1.  Nel [Portale classica di Azure](https://manage.windowsazure.com), passare alla scheda **spazio di archiviazione** .

2.  Nella scheda **archiviazione** fare clic sul nome dell'account di archiviazione per il quale si desidera eseguire il mapping di dominio personalizzato.

3.  Fare clic sulla scheda **Configura** .

4.  Nella parte inferiore dello schermo fare clic su **Manage Domain** per visualizzare la finestra di dialogo **Gestione dominio personalizzato** . Nel testo nella parte superiore della finestra di dialogo, verranno visualizzate informazioni su come creare il record CNAME utilizzando il sottodominio **asverify** .

5.  Accedere al sito Web del registrar DNS e passare alla pagina per la gestione DNS. È possibile in una sezione, ad esempio **Nome di dominio**, **DNS**o **Gestione di nome Server**.

6.  Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME** **Alias**o **sottodomini**.

7.  Creare un nuovo record CNAME e fornire un alias di sottodominio che include il sottodominio asverify. Ad esempio, il sottodominio specificato sarà nel formato **asverify.www** o **asverify.photos**. Quindi fornire un nome host, ossia endpoint del servizio Blob nel formato **asverify.mystorageaccount.blob.core.windows.net** (dove **mystorageaccount** è il nome del proprio account di archiviazione). Il nome host da utilizzare viene fornito automaticamente nel testo della finestra di dialogo **Gestione dominio personalizzato** .

8.  Dopo aver creato il record CNAME, tornare alla finestra di dialogo **Gestione dominio personalizzato** e immettere il nome del dominio personalizzato nel campo **Nome di dominio personalizzato** . Ad esempio, se il dominio è **contoso.com** e il sottodominio è **www**, immettere **www.contoso.com**; Se il sottodominio **foto**, immettere **photos.contoso.com**. Si noti che il sottodominio necessario.

9.  Fare clic sulla casella di controllo che indica che **Avanzate: usare il sottodominio 'asverify' di "preregistrare" del dominio personalizzato**.

10. Fare clic sul pulsante **Registra** per "preregistrare" il dominio personalizzato.

    Se il preinserimento ha esito positivo, si verrà visualizzato il messaggio **è attivo il dominio personalizzato**.

11. A questo punto, il dominio personalizzato è stato verificato da Azure, ma il traffico al proprio dominio non è ancora stato instradato al proprio account di archiviazione. Per completare il processo, tornare al sito Web del registrar DNS e creare un altro record CNAME mappato sottodominio all'endpoint del servizio Blob. Ad esempio, specificare il sottodominio come **www** o **le foto**e il nome host come **mystorageaccount.blob.core.windows.net** (dove **mystorageaccount** è il nome del proprio account di archiviazione). Con questo passaggio, la registrazione del dominio personalizzato è stata completata.

12. Infine, è possibile eliminare il record CNAME è stato creato mediante **asverify**, che venisse eseguito necessaria solo di un passaggio intermedio.

Gli utenti possono ora visualizza blob i dati nel dominio personalizzato, in modo che devono avere le autorizzazioni appropriate.

## <a name="verify-that-the-custom-domain-references-your-blob-service-endpoint"></a>Verificare che il dominio personalizzato fa riferimento endpoint del servizio Blob

Per verificare che il dominio personalizzato sia effettivamente mappato a endpoint del servizio Blob, creare un blob in un contenitore pubblico all'interno di account di archiviazione. Quindi, in un web browser, utilizzare un URI nel formato seguente per accedere il blob:

-   http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

Ad esempio, è possibile utilizzare il seguente URI per accedere a una maschera web tramite un sottodominio personalizzato **photos.contoso.com** associata a un blob nel contenitore di **myforms** :

-   http://Photos.contoso.com/MyForms/ApplicationForm.htm

## <a name="unregister-a-custom-domain-from-your-storage-account"></a>Annullare la registrazione di un dominio personalizzato dal proprio account di archiviazione

Per annullare la registrazione di un dominio personalizzato, procedere come segue: 

1. Accedere al [portale classica Azure](https://manage.windowsazure.com). 

2. Nel riquadro di spostamento fare clic su **archiviazione**. 

3. Nella pagina **dello spazio di archiviazione** , fare clic sul nome dell'account di archiviazione per visualizzare il dashboard. 

5. Sulla barra multifunzione fare clic su **Manage Domain**. 

6. Nella finestra di dialogo **Gestione dominio personalizzato** , fare clic su **Annulla registrazione**. 


## <a name="additional-resources"></a>Risorse aggiuntive

-   [Come eseguire il mapping di dominio personalizzato al punto finale di contenuti rete (CDN)](../cdn/cdn-map-content-to-custom-domain.md)
