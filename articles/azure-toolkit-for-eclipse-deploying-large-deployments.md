<properties
    pageTitle="Distribuzione di distribuzioni di grandi dimensioni"
    description="Informazioni su come distribuire utilizzando il Toolkit di Azure per Eclisse distribuzioni di grandi dimensioni."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# <a name="deploying-large-deployments"></a>Distribuzione di distribuzioni di grandi dimensioni #

Se la distribuzione è troppo grande per essere contenuti nella cartella approot predefinita, è possibile utilizzare una risorsa di archiviazione locale come cartella radice di distribuzione per il JDK e applicazione server.

## <a name="to-use-a-local-storage-resource-as-the-deployment-root-folder-for-large-deployments"></a>Usare una risorsa di archiviazione locale come cartella radice di distribuzione per distribuzioni di grandi dimensioni ##

1. Creare una nuova risorsa di archiviazione locale. Il nome della risorsa non ha importanza. Risorse di archiviazione sono definite a livello di ruolo. Il modo più rapido per accedere la finestra di dialogo Configurazione di archiviazione locale, da cui è possibile creare una nuova risorsa di archiviazione locale, è tramite la procedura seguente: pulsante destro del mouse sul ruolo nella visualizzazione **Gestione progetti** (espandere il nodo del progetto Azure se non è presente il ruolo), fare clic su **Azure**e quindi fare clic su **Archivio locale**. Nella finestra di dialogo **Archivio locale** , fare clic su **Aggiungi** per creare una nuova risorsa di archiviazione locale.
1. Impostare le dimensioni desiderate per almeno 2048 MB (nulla meno potrebbe causare lo stesso file di dimensioni problemi durante mentre incontrare il approot).
1. Verificare che sia selezionata **pulire il contenuto quando l'istanza di ruolo è inserimento Cestino** ; Questo consente di impedire l'esecuzione in conflitto con file esistenti nella risorsa quando l'istanza di ruolo è inserimento Cestino di logica di avvio della distribuzione.
1. Assicurarsi che il valore di **variabile di ambiente archiviazione percorso della directory della risorsa dopo la distribuzione** è impostato per la stringa **DEPLOYROOT**. La finestra di dialogo di archiviazione locale risorsa un aspetto simile al seguente.
    ![][ic667943]

In alternativa, se si utilizza **DEPLOYROOT** come il *nome* della risorsa locale e non modificare il nome della variabile ambiente generato automaticamente (che verrà impostato su **DEPLOYROOT_PATH** in questo caso), che da usare per l'applicazione.

Ulteriori informazioni sulla creazione di una risorsa di archiviazione locali disponibili proprietà [archivio locale][].

## <a name="see-also"></a>Vedere anche ##

[Azure Toolkit per Eclisse][]

[Creazione di un'applicazione Hello World per Azure in Eclisse][]

[Installare il Toolkit di Azure per Eclisse][] 

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure][].

<!-- URL List -->

[Centro per sviluppatori di Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit per Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creazione di un'applicazione Hello World per Azure in Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installare il Toolkit di Azure per Eclisse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Proprietà archivio locale]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png
