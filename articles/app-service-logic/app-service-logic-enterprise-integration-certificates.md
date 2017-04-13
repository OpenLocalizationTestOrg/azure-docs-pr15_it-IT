
<properties
    pageTitle="Utilizzo dei certificati con Service Pack integrazione Enterprise | Microsoft Azure"
    description="Informazioni su come usare i certificati con la logica App integrazione di portatile Enterprise"
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="msftman"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="deonhe"/>

# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>Informazioni sui certificati ed Enterprise Integration Pack

## <a name="overview"></a>Panoramica
Integrazione dell'organizzazione vengono utilizzati i certificati per proteggere le comunicazioni B2B. È possibile utilizzare due tipi di certificati nelle applicazioni integrazione enterprise:

- Certificati pubblici, che devono essere acquistati da un'autorità di certificazione (CA).
- Certificati personali, è possibile inviare a se stessi. I certificati sono definiti talvolta certificati.


## <a name="what-are-certificates"></a>Quali sono i certificati?
I certificati sono documenti digitali che consentono di verificare l'identità dei partecipanti comunicazioni elettroniche e che proteggere anche le comunicazioni elettroniche.

## <a name="why-use-certificates"></a>Perché usare i certificati?
In alcuni casi B2B comunicazioni devono essere considerate riservate. Integrazione di Enterprise utilizza certificati per proteggere queste comunicazioni in due modi:

- Crittografando il contenuto dei messaggi
- Inserendo una firma digitale dei messaggi  

## <a name="how-do-you-upload-certificates"></a>Come si carica certificati?

### <a name="public-certificates"></a>Certificati pubblica
Per usare un *certificato pubblico* nelle applicazioni logica con funzionalità B2B, è innanzitutto necessario caricare il certificato in un account di integrazione. Per usare un *certificato autofirmato*, invece, è necessario innanzitutto caricarlo [Azure chiave archivio](../key-vault/key-vault-get-started.md "informazioni chiave archivio").

Dopo aver caricato un certificato, è disponibile per proteggere i messaggi B2B quando si definiscono le relative proprietà in [contratti](./app-service-logic-enterprise-integration-agreements.md) creati.  

Ecco la procedura dettagliata per il caricamento dei certificati pubblici all'account di integrazione dopo l'accesso al portale di Azure:

1. Selezionare **Sfoglia**.  
    ![Selezionare Sfoglia](./media/app-service-logic-enterprise-integration-overview/overview-1.png)  

2. Immettere **l'integrazione** nella casella filtro di ricerca e quindi selezionare **Gli account di integrazione** dall'elenco dei risultati.     
    ![Selezionare gli account di integrazione](./media/app-service-logic-enterprise-integration-overview/overview-2.png)

3. Selezionare l'account di integrazione a cui si desidera aggiungere il certificato.  
    ![Selezionare l'account di integrazione a cui si desidera aggiungere il certificato](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  

4.  Selezionare il riquadro **dei certificati** .  
    ![Selezionare il riquadro dei certificati](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)

5. In e il **certificati** visualizzata, selezionare il pulsante **Aggiungi** .
    ![Selezionare il pulsante Aggiungi](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Immettere un **nome** per il certificato e quindi selezionare il tipo di certificato. (In questo esempio è stato usato il tipo di certificato pubblico.) Quindi selezionare l'icona di cartella sul lato destro della casella di testo **certificato** .

7. Quando si apre il selettore file, individuare e selezionare il file di certificato che si desidera caricare al proprio account di integrazione.

8. Selezionare il certificato e quindi selezionare **OK** nella finestra selezione dei file. La convalida e carica il certificato al proprio account di integrazione.

8. Infine, riattivarla e il **certificato di Aggiungi** , selezionare il pulsante **OK** .  
    ![Selezionare il pulsante OK](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)  

9. In circa un minuto, verrà visualizzato una notifica che indica che il caricamento di certificato è stato completo.

10. Selezionare il riquadro **dei certificati** . Verrà visualizzato il certificato appena aggiunto.  
    ![Visualizzare il nuovo certificato](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)  

### <a name="private-certificates"></a>Certificati personali
È possibile caricare i certificati privati nel proprio account di integrazione eseguendo la procedura seguente:  

1. [Caricare la chiave privata all'archivio di chiave] (../key-vault/key-vault-get-started.md "Informazioni sulle chiave archivio").  

    > [AZURE.TIP] È necessario autorizzare la caratteristica di logica App del servizio di App Azure per eseguire operazioni di archivio di chiave. È possibile concedere l'accesso a capitale servizio App logica utilizzando il comando PowerShell seguente:`Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  

2. Creare un certificato privato.  

3. Caricare il certificato privato al proprio account di integrazione.

Dopo avere adottato i passaggi precedenti, è possibile utilizzare il certificato privato per creare i contratti.

Di seguito sono indicati i passaggi per caricare i certificati privati nel proprio account di integrazione dopo l'accesso al portale di Azure:  

1. Selezionare **Sfoglia**.  
    ![Caricare i certificati privati nel proprio account di integrazione](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    

2. Immettere **l'integrazione** nella casella filtro di ricerca e quindi selezionare **Gli account di integrazione** dall'elenco dei risultati.     
    ![Selezionare gli account di integrazione](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  

3. Selezionare l'account di integrazione a cui si desidera aggiungere il certificato.  
    ![Selezionare l'account di integrazione a cui si desidera aggiungere il certificato](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  

4. Selezionare il riquadro **dei certificati** .  
    ![Selezionare il riquadro dei certificati](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)  

5. In e il **certificati** visualizzata, selezionare il pulsante **Aggiungi** .
    ![Selezionare il pulsante Aggiungi](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Immettere un **nome** per il certificato e quindi selezionare il tipo di certificato. (In questo esempio è stato usato il tipo di certificato pubblico.) Quindi selezionare l'icona di cartella sul lato destro della casella di testo **certificato** .

7. Quando si apre il selettore file, individuare e selezionare il file di certificato che si desidera caricare al proprio account di integrazione.

8. Dopo aver selezionato il certificato, selezionare **OK** nella selezione file. Questa azione di convalida il certificato e carica al proprio account di integrazione.

9. Infine, riattivarla e il **certificato di Aggiungi** , selezionare il pulsante **OK** .  
    ![Aggiungere il certificato](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)  

10. In circa un minuto, verrà visualizzato una notifica che indica che il caricamento di certificato è stato completo.

11. Selezionare il riquadro **dei certificati** . Verrà visualizzato il certificato appena aggiunto.
    ![Visualizzare il nuovo certificato](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)  

Dopo aver caricato un certificato, è disponibile per proteggere i messaggi B2B quando si definiscono le relative proprietà in [contratti](./app-service-logic-enterprise-integration-agreements.md).  

## <a name="next-steps"></a>Passaggi successivi
- [Creare un'app di logica che utilizza le funzionalità B2B](./app-service-logic-enterprise-integration-b2b.md)  
- [Creare un contratto B2B](./app-service-logic-enterprise-integration-agreements.md)  
- [Altre informazioni sull'archivio di chiave] (../key-vault/key-vault-get-started.md "Informazioni sulle chiave archivio")  
