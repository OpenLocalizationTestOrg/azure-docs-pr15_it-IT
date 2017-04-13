<properties
   pageTitle="Utilizzo del codice VS con i modelli di gestione risorse | Microsoft Azure"
   description="Viene illustrato come configurare codice Visual Studio per creare un modello di gestione di risorse Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="cmatskas"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="chmatsk;tomfitz"/>

# <a name="working-with-azure-resource-manager-templates-in-visual-studio-code"></a>Utilizzo dei modelli di gestione risorse Azure nel codice Visual Studio

Modelli di gestione risorse Azure sono file JSON che descrivono una risorsa e le relative dipendenze. Questi file in alcuni casi possono essere grandi e complicato utensili supporto è importante. Codice di Visual Studio è un editor di codice nuovo, leggere e Apri origine, multipiattaforma. Supporta la creazione e modifica dei modelli di Manager delle risorse tramite una [nuova estensione](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Codice di Visual Studio viene eseguito ovunque e non richiede l'accesso a Internet a meno che non si desidera distribuire i modelli di Manager delle risorse.

Se non si dispone già di codice Visual Studio, è possibile installarlo in [https://code.visualstudio.com/](https://code.visualstudio.com/).

## <a name="install-the-resource-manager-extension"></a>Installare l'estensione di Manager delle risorse

Per lavorare con i modelli di JSON nel codice Visual Studio, è necessario installare un'estensione. La procedura seguente Scarica e installa il supporto delle lingue per i modelli di JSON Manager delle risorse:

1. Barra di avvio VS codice 
2. Aprire Apri rapida (Ctrl + P) 
3. Eseguire il comando seguente: 

        ext install azurerm-vscode-tools

4. Riavviare codice VS quando viene richiesto di attivare l'estensione. 

 Processo di fatto!

## <a name="set-up-resource-manager-snippets"></a>Configurare la gestione risorse frammenti

I passaggi precedenti installato il supporto degli strumenti, ma a questo punto è necessario configurare il codice di Visual Studio per utilizzare i frammenti di modello JSON.

1. Copiare il contenuto del file di archivio di [azure-xplat-arm-utensili](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) negli Appunti.
2. Avviare Visual Studio codice 
3. Nel codice Visual Studio, è possibile aprire il file di frammenti JSON, individuarlo **File** -> **Preferenze** -> **Utente frammenti** -> **JSON**, oppure selezionando **F1** e digitando **Preferenze** fino a quando non è possibile selezionare **Preferenze: frammenti**.

    ![frammenti di preferenza](./media/resource-manager-vs-code/preferences-snippets.png)

    In Opzioni, selezionare **JSON**.

    ![Selezionare json](./media/resource-manager-vs-code/select-json.png)

4. Incollare il contenuto del file nel passaggio 1 in un file di frammenti utente prima dell'istruzione "}" 
5. Verificare che la JSON sia corretto e sono non disponibili in un punto qualsiasi zigzag. 
6. Salvare e chiudere il file di frammenti utente.

Questo è tutto ciò che serve per iniziare a usare i frammenti di Manager delle risorse. Successivamente, è necessario posizionare il programma di installazione al test.

## <a name="work-with-template-in-vs-code"></a>Usare modello nel codice Visual Studio

Il modo più semplice per iniziare a lavorare con un modello consiste nel trascinare uno dei modelli avvio rapido disponibile in [Github](https://github.com/Azure/azure-quickstart-templates) o usare uno dei propri. È possibile [esportare un modello](resource-manager-export-template.md) per uno dei gruppi di risorse tramite il portale. 

1. Se è stata esportata un modello da un gruppo di risorse, aprire i file estratti nel codice VS.

    ![visualizzare i file](./media/resource-manager-vs-code/show-files.png)

2. Aprire il file template.json in modo che è possibile modificarlo e aggiungere alcune risorse aggiuntive. Dopo la **"risorse": [** premere INVIO per iniziare una nuova riga. Se si digita **arm**, verrà visualizzato un elenco di opzioni. Queste opzioni sono i frammenti di modello che è stato installato. Dovrebbe risultare analoga alla seguente: 

    ![Mostra frammenti](./media/resource-manager-vs-code/type-snippets.png)

3. Scegliere il frammento di codice desiderate. In questo articolo sta scegliendo **arm ip** per creare un nuovo indirizzo IP pubblico. Inserire un punto e virgola dopo la parentesi di chiusura "}" della risorsa per verificare che il modello appena creata la sintassi è valida.

     ![aggiungere virgola](./media/resource-manager-vs-code/add-comma.png)

4. IntelliSense predefiniti codice VS. Quando si modificano i modelli, codice VS suggerisce valori disponibili. Ad esempio, per aggiungere una sezione di variabili per il modello, aggiungere **""** (due virgolette) e selezionare **Ctrl + barra spaziatrice** tra le virgolette. Verrà visualizzata con le opzioni incluse **le variabili**.

    ![aggiungere variabili](./media/resource-manager-vs-code/add-variables.png)

5. IntelliSense può suggerire valori disponibili o le funzioni. Per impostare una proprietà su un valore di parametro, creare un'espressione con **"[]"** e **Ctrl + barra spaziatrice**. È possibile iniziare a digitare il nome di una funzione. Selezionare **scheda** dopo aver trovato la funzione desiderata.

    ![aggiungere parametri](./media/resource-manager-vs-code/select-parameters.png)

6. Selezionare di nuovo **Ctrl + barra spaziatrice** all'interno della funzione per visualizzare un elenco dei parametri disponibili all'interno del modello.

    ![aggiungere parametri](./media/resource-manager-vs-code/select-avail-parameters.png)

7. Se si verificano problemi convalida schema nel modello scelto, si noterà zigzag familiarità nell'editor. È possibile visualizzare l'elenco di errori e avvisi digitando **Ctrl + MAIUSC + M** o selezionando le icone nella barra di stato sinistro inferiore.

    ![errori](./media/resource-manager-vs-code/errors.png)

    Convalida del modello consente di rilevare i problemi di sintassi; Tuttavia, è anche possibile riscontrare errori che è possibile ignorare. In alcuni casi, l'editor confronta il modello con lo schema che non è aggiornato e pertanto viene segnalato un errore anche se si conosce che sia corretto. Si supponga ad esempio una funzione recente è stato aggiunto a Gestione risorse, ma lo schema non è stato aggiornato. L'editor viene segnalato un errore nonostante che la funzione funziona correttamente durante la distribuzione.

    ![messaggio di errore](./media/resource-manager-vs-code/unrecognized-function.png)

## <a name="deploy-your-new-resources"></a>Distribuire le nuove risorse

Quando il modello è pronto, è possibile distribuire le nuove risorse seguendo le istruzioni seguenti: 

### <a name="windows"></a>Windows

1. Aprire un prompt dei comandi di PowerShell 
2. Tipo di accesso: 

        Login-AzureRmAccount 

3. Se si hanno più abbonamenti, ottenere un elenco delle sottoscrizioni con:

        Get-AzureRmSubscription

    Selezionare l'abbonamento da usare.
   
        Select-AzureRmSubscription -SubscriptionId <Subscription Id>

4. Aggiornare i parametri del file parameters.json
5. Eseguire Deploy.ps1 per distribuire il modello di Azure

### <a name="osxlinux"></a>OSX/Linux

1. Aprire una finestra del terminale 
2. Tipo di accesso:

        azure login 

3. Se si hanno più abbonamenti, selezionare l'abbonamento a destra con:

        azure account set <subscriptionNameOrId> 

4. Aggiornare i parametri del file parameters.json.
5. Per distribuire il modello, eseguire:

        azure group deployment create -f <PathToTemplate> 

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sui modelli, vedere [modelli di creazione condivisa Manager delle risorse di Azure](resource-group-authoring-templates.md).
- Per informazioni sulle funzioni di modello, vedere [gestione di risorse di Azure modello funzioni](resource-group-template-functions.md).
- Per altri esempi di utilizzo del codice di Visual Studio, vedere [compilazione di applicazioni basate su cloud con codice Visual Studio](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code) dalla connessione [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Per altre guide rapide dal demo HealthClinic.biz, vedere [Guide rapide strumenti di sviluppo di Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
