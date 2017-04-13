<properties
    pageTitle="Endpoint del servizio di Azure"
    description="Descrive le impostazioni della Endpoint del servizio di Azure il Toolkit di Azure per Eclisse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# <a name="azure-service-endpoints"></a>Endpoint del servizio di Azure #

Endpoint del servizio Azure per determinare che se l'applicazione è distribuito in e gestito dalla piattaforma Azure globale, Azure gestito da 21Vianet in Cina o private piattaforma Azure. Finestra di dialogo **Endpoint del servizio** consente di specificare quali endpoint servizio che si desidera utilizzare. Per aprire la finestra di dialogo **Endpoint del servizio** , all'interno di Eclisse, fare clic su **finestra**, fare clic su **Preferenze**, espandere **Azure**e quindi fare clic su **Endpoint del servizio**. Il campo **Set attivo** consente di stabilire quali endpoint servizio Azure verrà utilizzata per i progetti Azure nell'area di lavoro corrente.

Di seguito viene illustrata la finestra di dialogo **Endpoint del servizio** .

![][ic719493]

## <a name="to-set-the-service-endpoints"></a>Per impostare gli endpoint del servizio ##

Nella finestra di dialogo **Endpoint del servizio** , eseguire una delle operazioni seguenti:

* Se si desidera utilizzare la piattaforma Azure globale, nell'elenco a discesa **Impostazione attiva** , selezionare **windowsazure.com** e fare clic su **OK**.
* Se si desidera utilizzare Azure gestito da 21Vianet in Cina, nell'elenco a discesa **Impostazione attiva** , selezionare **windowsazure.cn (Cina)** e fare clic su **OK**.
* Se si desidera utilizzare una piattaforma Azure privata:
    1. Fare clic su **Modifica**.
    2. Verrà visualizzata una finestra di dialogo che notifica che verrà chiuso la finestra di dialogo **Endpoint del servizio** , e verrà aperto il file di set di preferenza. Fare clic su **OK**.
    3. Creare un nuovo file preferencesets.xml `preferenceset` elemento. Per il nuovo elemento, creare `name`, `blob`, `management`, `portalURL` e `publishsettings` attributi e aggiungere i valori per poterli che corrispondono alla piattaforma Azure privata. È possibile utilizzare i valori previsti esistente `preferenceset` elementi come modelli. **Nota**: il valore utilizzato per la `blob` attributo deve contenere il testo "blob" nell'URL.
    4. Salvare e chiudere preferencesets.xml.
    5. Riaprire la finestra di dialogo **Endpoint del servizio** .
    6. Nell'elenco a discesa **Impostazione attiva** , selezionare il set attivo che è stato creato e fare clic su **OK**.
    7. Dopo aver creato la piattaforma Azure privata `preferenceset` elemento, è possibile modificare i valori assegnati a tale facendo clic sul pulsante **Modifica** nella finestra di dialogo **Endpoint dei servizi** . È anche possibile creare più piattaforma Azure privata `preferenceset` elementi, se lo si desidera.

## <a name="see-also"></a>Vedere anche ##

[Azure Toolkit per Eclisse][]

[Installare il Toolkit di Azure per Eclisse][] 

[Creazione di un'applicazione Hello World per Azure in Eclisse][]

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure][].

<!-- URL List -->

[Centro per sviluppatori di Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit per Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creazione di un'applicazione Hello World per Azure in Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installare il Toolkit di Azure per Eclisse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png
