<properties
    pageTitle="Azure B2C Directory attiva: Strumento di supporto Page UI personalizzazione | Microsoft Azure"
    description="Uno strumento di supporto utilizzato per illustrare la caratteristica di personalizzazione pagina dell'interfaccia utente di Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure B2C Directory attiva: Uno strumento di supporto utilizzato per illustrare la funzionalità di personalizzazione dell'interfaccia utente di pagina

In questo articolo è correlato all' [articolo principale della personalizzazione dell'interfaccia utente](active-directory-b2c-reference-ui-customization.md) di Azure Active Directory (Azure Active Directory) B2C. La procedura seguente viene descritto come verificare la funzionalità di personalizzazione dell'interfaccia utente utilizzando contenuto HTML e CSS di esempio che è disponibile.

## <a name="get-an-azure-ad-b2c-tenant"></a>Ottenere un tenant di Azure Active Directory B2C

Prima che sia possibile personalizzare qualsiasi elemento, sarà necessario [ottenere un tenant di Azure Active Directory B2C](active-directory-b2c-get-started.md) se non si dispone già uno.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Creazione di un criterio per l'abbonamento o accesso

Il contenuto di esempio troverete può essere utilizzato per customze due pagine in un [criterio per l'abbonamento o di accesso](active-directory-b2c-reference-policies.md): la [pagina di accesso unificata](active-directory-b2c-reference-ui-customization.md) e [automatico attivato pagina attributi](active-directory-b2c-reference-ui-customization.md). Quando [la creazione di criteri di iscrizione o accesso](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), è possibile aggiungere Account locale (indirizzo di posta elettronica), Facebook, Google e Microsoft come **provider di identità**. Si tratta di IDPs solo il contenuto HTML di esempio accetta.  È anche possibile aggiungere un sottoinsieme di questi IDPs se lo si desidera.

## <a name="register-an-application"></a>Registrazione di un'applicazione

Sarà necessario [registrare un'applicazione](active-directory-b2c-app-registration.md) nel tenant di B2C che può essere utilizzato per eseguire il criterio. Dopo aver registrato l'applicazione, è possibile tra alcune opzioni che è possibile utilizzare per eseguire effettivamente i criteri per l'abbonamento:

- Creare una del B2C di Azure Active Directory le applicazioni di avvio rapido elencate nella sezione "Introduzione" di [accedere verso l'alto ed effettuare l'accesso consumatori nelle applicazioni](active-directory-b2c-overview.md#getting-started).
- Usare l'applicazione di [test di Azure Active Directory B2C](https://aadb2cplayground.azurewebsites.net) predefinita. Se si sceglie di usare i test, è necessario registrare un'applicazione nel tenant di B2C mediante **reindirizzare URI** `https://aadb2cplayground.azurewebsites.net/`.
- Scegliere i criteri nel [portale di Azure](https://portal.azure.com/)pulsante **Esegui** .

## <a name="customize-your-policy"></a>Personalizzare il criterio

Per personalizzare l'aspetto del criterio, è necessario prima di tutto creare file HTML e CSS utilizzando le convenzioni specifiche di Azure Active Directory B2C. Per caricare il contenuto statico in un percorso accessibile al pubblico in modo che possano accedervi B2C di Azure Active Directory. Potrebbe trattarsi di server web dedicato, archiviazione Blob Azure, rete CDN di Azure o qualsiasi altro statico hosting risorse provider. I requisiti solo sono che il contenuto è disponibile su HTTPS ed è possibile accedervi utilizzando CORS. Dopo aver esposto il contenuto statico sul web, è possibile modificare i criteri per scegliere il percorso e presentare contenuto ai clienti. [Articolo principale della personalizzazione dell'interfaccia utente](active-directory-b2c-reference-ui-customization.md) vengono illustrati in dettaglio funzionamento delle caratteristiche di personalizzazione di Azure Active Directory B2C.

Ai fini di questa esercitazione, aver già creato alcuni contenuti di esempio e ospitati in archiviazione Blob Azure. Il contenuto di esempio è una personalizzazione di base del tema di nostro fittizia società, "". Per provare in criteri personalizzati, procedere come segue:

1. Accedere al tenant di nel [portale di Azure](https://portal.azure.com/) e passare a e il caratteristiche B2C.
2. Fare clic su **criteri per l'abbonamento o di accesso** e quindi fare clic su criteri (ad esempio, "b2c\_1\_accesso\_su\_accesso\_in").
3. Fare clic su **personalizzazione dell'interfaccia utente di pagina** e quindi **pagina di iscrizione o accesso unificato**.
4. Attivare o disattivare l'opzione di **pagina personalizzato** su **Sì**. Nel campo **pagina personalizzata URI** immettere `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Fare clic su **OK**.
5. Fare clic su **pagina iscrizione dell'account locale**. Attivare o disattivare l'opzione **Usa modello personalizzato** su **Sì**. Nel campo **pagina personalizzata URI** immettere `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
5. Ripetere il passaggio stesso per la **pagina di iscrizione account di social networking**.
 Fare clic su **OK** due volte per chiudere le pale personalizzazione dell'interfaccia utente.
6. Fare clic su **Salva**.

A questo punto è possibile provare il criterio personalizzato. Se si desidera, ma è possibile anche è sufficiente fare clic sul comando **Esegui** nel e criterio, è possibile utilizzare un'applicazione o il test di Azure Active Directory B2C. Selezionare l'applicazione nella casella di riepilogo a discesa e scegliere il reindirizzamento appropriato URI. Fare clic sul pulsante **Esegui** . Verrà aperta una nuova scheda del browser e sarà possibile eseguire mediante l'esperienza utente di iscrizione per l'applicazione con il nuovo contenuto nella posizione!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Caricare il contenuto di esempio in archiviazione Blob Azure

Se si desidera utilizzare archiviazione Blob Azure per ospitare il contenuto della pagina, è possibile creare il proprio account di archiviazione e usare lo strumento di supporto B2C per caricare i file.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+ Nuovo** > **dati + spazio di archiviazione** > **account di archiviazione**. È necessario un abbonamento Azure per creare un account di archiviazione Blob Azure. È possibile iscriversi una versione di valutazione gratuita [Azure sito Web](https://azure.microsoft.com/pricing/free-trial/).
3. Specificare un **nome** per l'account di archiviazione (ad esempio "contoso") e selezionare l'opzione desiderata per i **prezzi livelli**, **gruppo di risorse** e **sottoscrizione**. Accertarsi di avere l'opzione **Aggiungi a Startboard** selezionata. Fare clic su **Crea**.
4. Tornare al Startboard e fare clic sull'account di archiviazione appena creata.
5. Nella sezione **Sommario** , fare clic su **contenitori**e quindi fare clic su **+ Add**.
6. Specificare un **nome** per il contenitore (ad esempio, "b2c") e selezionare **Blob** come **tipo di accesso**. Fare clic su **OK**.
7. Il contenitore creato verrà visualizzato nell'elenco e il **BLOB** . Annotare l'URL del contenitore; ad esempio, dovrebbe essere simile a `https://contoso.blob.core.windows.net/b2c`. Chiudere e il **BLOB** .
8. Nella e conto dello spazio di archiviazione, fare clic **sui tasti** e annotare i valori dei campi **Chiave primaria di accesso** e **Nome dell'Account di archiviazione** .

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+ Nuovo** > **dati + spazio di archiviazione** > **account di archiviazione**. È necessario un abbonamento Azure per creare un account di archiviazione Blob Azure. È possibile iscriversi una versione di valutazione gratuita [Azure sito Web](https://azure.microsoft.com/pricing/free-trial/).
3. Selezionare **Archiviazione Blob** in **Tipo di Account**e lasciare gli altri valori come predefinito.  Se si desidera, è possibile modificare il gruppo di risorse e posizione.  Fare clic su **Crea**.
4. Tornare al Startboard e fare clic sull'account di archiviazione appena creata.
5. Nella sezione **Sommario** , fare clic su **+ contenitore**.
6. Specificare un **nome** per il contenitore (ad esempio, "b2c") e selezionare **Blob** come **tipo di accesso**. Fare clic su **OK**.
7. Aprire il contenitore **proprietà**e annotare l'URL del contenitore; ad esempio, dovrebbe essere simile a `https://contoso.blob.core.windows.net/b2c`. Chiudere e il contenitore.
8. Nella e conto dello spazio di archiviazione, fare clic sull' **Icona di chiave** e annotare i valori dei campi **Chiave primaria di accesso** e **Nome dell'Account di archiviazione** .

> [AZURE.NOTE]
    **Chiave primaria di accesso** è una credenziali di sicurezza importanti.

### <a name="download-the-helper-tool-and-sample-files"></a>Scaricare i file di esempio e strumento di supporto

È possibile scaricare i [file archiviazione Blob Azure supporto strumento e di esempio come file ZIP](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) o duplicare da GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Questo archivio contiene un `sample_templates\wingtip` directory, che contiene esempio HTML, CSS e immagini. Per questi modelli fare riferimento il proprio account di archiviazione Blob Azure, sarà necessario modificare i file HTML. Apri `unified.html` e `selfasserted.html` e sostituire tutte le istanze di `https://localhost` con l'URL del contenitore annotato nei passaggi precedenti. Poiché in questo caso, verrà visualizzato il codice HTML da Azure Active Directory, in corrispondenza del dominio, è necessario utilizzare il percorso assoluto del file HTML `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Caricare i file di esempio

Nello stesso archivio decomprimere `B2CAzureStorageClient.zip` ed eseguire il `B2CAzureStorageClient.exe` file all'interno. Questo programma è sufficiente caricare tutti i file nella cartella specificata al proprio account di archiviazione e consentire l'accesso CORS per i file. Se è stata seguita la procedura indicata sopra, i file HTML e CSS ora che punti al proprio account di archiviazione. Si noti che il nome del proprio account di archiviazione è la parte che precede `blob.core.windows.net`; ad esempio `contoso`. È possibile verificare che il contenuto è stato caricato correttamente quando si tenta di accedere a `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` in un browser. Per assicurarsi che il contenuto è ora CORS abilitato anche usare [http://test-cors.org/](http://test-cors.org/) . (Cercare "stato XHR: 200" nel risultato.)

### <a name="customize-your-policy-again"></a>Personalizzazione dei criteri di nuovo

Dopo aver caricato il contenuto di esempio per il proprio account di archiviazione, è necessario modificare i criteri di iscrizione per farvi riferimento. Ripetere i passaggi della sezione ["Personalizza il criterio"](#customize-your-policy) riportati sopra, questa volta utilizzando URL del proprio account di archiviazione. Ad esempio, la posizione del `unified.html` file `<url-of-your-container>/wingtip/unified.html`.

A questo punto è possibile utilizzare il pulsante **Esegui** o applicazione a eseguire di nuovo i criteri. Il risultato dovrebbe essere quasi esattamente la stessa - è stato utilizzato lo stesso esempio HTML e CSS in entrambi i casi. Tuttavia, i criteri fanno riferimento a questo punto l'istanza di archiviazione Blob Azure ed è attiva gratuito modificare e caricare i file di nuovo durante la. Per ulteriori informazioni su come personalizzare il formato HTML e CSS, fare riferimento all' [articolo principale della personalizzazione dell'interfaccia utente](active-directory-b2c-reference-ui-customization.md).
