<properties
    pageTitle="La visualizzazione di contenuto Javadoc in Eclisse per il pacchetto di Azure raccolte per Java"
    description="Informazioni su come visualizzare il contenuto Javadoc relative alle librerie di Azure Eclisse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>La visualizzazione di contenuto Javadoc in Eclisse per il pacchetto di Azure raccolte per Java #

Il contenuto Javadoc relative alle librerie di Azure per Java può essere visualizzato all'interno dell'ambiente Eclisse associando contenuto Javadoc alle librerie di Azure per Java. La procedura seguente viene illustrato come utilizzare questa funzionalità di Eclisse.

Questa procedura si presuppone che la raccolta di Azure per Java è già stato aggiunto al percorso di compilazione.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Per visualizzare il contenuto Javadoc Eclisse relative alle librerie di Azure per Java ##

* All'interno di gestione di progetti di Eclisse, nella sezione **Raccolte fare riferimento** del progetto, aprire il menu di scelta rapida per la raccolta di Azure per Java JAR. Ad esempio **microsoft-windowsazure-api-0.1.0.jar** (il numero di versione potrebbe essere diversi, dipende dalla versione installata).
* Fare clic su **proprietà**.
* Nella finestra di dialogo **proprietà** , nel riquadro sinistro fare clic su **Posizione Javadoc**. Verrà visualizzata la finestra di dialogo **Percorso Javadoc** .
* È possibile specificare un **URL Javadoc**o un **Javadoc nell'archivio**.
    * Se si sceglie di specificare un **URL Javadoc**, utilizzare gli URL, ad esempio **http://dl.windowsazure.com/javadoc** o **http://dl.windowsazure.com/storage/javadoc**.
    * Se si sceglie di usare **Javadoc nell'archivio**, è possibile specificare un file esterno o un file di area di lavoro.
    Specificare il numero di copie e Sfoglia/convalida in base alle esigenze. Nell'esempio seguente la JAR Javadoc corrispondente scaricati in locale in una cartella denominata **c:\MyAzureJARs**associa le librerie di Azure per Java.
    ![][ic553487]
* *Facoltativo*: fare clic su **convalida**. Problemi con la Javadoc JAR possono essere visualizzate qui.
* Fare clic su **OK**.

Una volta associato la raccolta, il contenuto Javadoc deve essere visualizzato l'IDE Eclisse. Ad esempio, se `blob` definiti di tipo `CloudBlockBlob` all'interno del codice seguente è illustrato un esempio del contenuto Javadoc che viene visualizzato quando si digita `blob.acquireLease` nel codice:

![][ic553488]

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

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png