Azure consente di verificare che l'applicazione utilizza Python **Se entrambe le condizioni sono vere**:

- file Requirements.txt nella cartella radice
- qualsiasi file .py nella cartella radice o runtime.txt che specifica python

Una volta che le maiuscole/minuscole, utilizza uno script di distribuzione specifica Python, che consente di eseguire la sincronizzazione standard dei file, nonché ulteriori operazioni di Python quali:

- Gestione automatica dell'ambiente virtuale
- Installazione di pacchetti elencati in requirements.txt utilizzando pip
- Creazione di config appropriate in base alla versione Python selezionata.
- Raccogliere file statici per le applicazioni Django

È possibile controllare alcuni aspetti dei passaggi di distribuzione predefinito senza dover personalizzare lo script.

Se si vuole ignorare tutti i passaggi di distribuzione specifica Python, è possibile creare questo file vuoto:

    \.skipPythonDeployment

Se si vuole ignorare la raccolta di file statici per l'applicazione Django:

    \.skipDjango 

Per un maggiore controllo distribuzione, è possibile ignorare lo script di distribuzione predefinito tramite la creazione di file seguenti:

    \.deployment
    \deploy.cmd

È possibile utilizzare l' [interfaccia di Azure riga di comando][] per creare i file.  Utilizzare questo comando dalla cartella del progetto:

    azure site deploymentscript --python

Se non sono presenti questi file, Azure consente di creare uno script di distribuzione temporaneo e viene eseguito.  È identica a quella creata con il comando precedente.

[Azure interfaccia della riga di comando]: http://azure.microsoft.com/downloads/
