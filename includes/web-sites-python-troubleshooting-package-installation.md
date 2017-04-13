Alcuni pacchetti non venga installato utilizzando pip quando viene eseguito su Azure.  È possibile semplicemente che il pacchetto non sono disponibile sull'indice Python pacchetto.  È possibile che un compilatore è necessario (un compilatore non è disponibile nei computer che esegue l'applicazione web nel servizio di Azure App).

In questa sezione vengono illustrate in modi per risolvere questo problema.

### <a name="request-wheels"></a>Richiedere delle rotelline

Se l'installazione del pacchetto richiede un compilatore, è necessario cercare contattare il proprietario del pacchetto per richiedere che delle rotelline venga reso disponibile per il pacchetto.

Con la disponibilità recente di [Microsoft Visual C++ compilatore per Python 2.7][], è stata semplificata per la creazione di pacchetti contenenti codice nativo per Python 2.7.

### <a name="build-wheels-requires-windows"></a>Creare delle rotelline (richiede Windows)

Nota: Quando si utilizza questa opzione, assicurarsi di compilare il pacchetto con un ambiente Python corrispondente la piattaforma, una architettura/una versione utilizzata in web app in Azure App servizio (Windows/32-bit/2.7 o 3.4).

Se non viene installato il pacchetto perché è necessario un compilatore, è possibile installare il compilatore sul computer locale e creare una ruota per il pacchetto, quindi verranno inclusi nel repository.

Utenti Mac o Linux: Se non si dispone di accesso a un computer Windows, vedere [creare una macchina virtuale che esegue Windows][] per la creazione di una macchina virtuale in Azure.  È possibile utilizzare per creare le rotelle, aggiungerle all'archivio ed eliminare la macchina virtuale se si preferisce. 

Per Python 2.7, è possibile installare [Microsoft Visual C++ compilatore per Python 2.7][].

Per Python 3.4, è possibile installare [Microsoft Visual C++ 2010 Express][].

Per creare delle rotelline, è necessario il pacchetto di rotellina:

    env\scripts\pip install wheel

In cui si userà `pip wheel` per la compilazione una dipendenza:

    env\scripts\pip wheel azure==0.8.4

Viene creato un file .whl nella cartella \wheelhouse.  Aggiungere i file di cartella e ruota \wheelhouse per l'archivio.

Modificare il requirements.txt per aggiungere il `--find-links` opzione nella parte superiore. In questo modo pip per cercare una corrispondenza esatta nella cartella locale prima di passare all'indice di pacchetto python.

    --find-links wheelhouse
    azure==0.8.4

Se si desidera includere tutte le dipendenze nella cartella \wheelhouse e non utilizza l'indice di pacchetto python, è possibile forzare pip per ignorare l'indice di pacchetto mediante l'aggiunta di `--no-index` nella parte superiore del requirements.txt.

    --no-index

### <a name="customize-installation"></a>Personalizzare l'installazione

È possibile personalizzare lo script di distribuzione per installare un pacchetto nell'ambiente virtuale utilizzando un programma di installazione alternativo, ad esempio facile\_installare.  Vedere Deploy per un esempio di commento.  Assicurarsi che tali pacchetti non sono elencati in requirements.txt, per impedire ai pip di installarli.

Aggiungere lo script di distribuzione:

    env\scripts\easy_install somepackage

È anche possibile usare facile\_installare da un programma di installazione exe (alcune sono zip in modo semplice e rapido compatibile\_installa le supporta).  Aggiungere il programma di installazione per l'archivio e richiamare facile\_installare passando il percorso del file eseguibile.

Aggiungere lo script di distribuzione:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Includere l'ambiente virtuale nell'archivio (richiede Windows)

Nota: Quando si utilizza questa opzione, assicurarsi di utilizzare un ambiente virtuale corrispondente la piattaforma, una architettura/una versione utilizzata in web app in Azure App servizio (Windows/32-bit/2.7 o 3.4).

Se si include l'ambiente virtuale nel repository, è possibile impedire lo script di distribuzione di eseguire la gestione dell'ambiente virtuale in Azure mediante la creazione di un file vuoto:

    .skipPythonDeployment

È consigliabile eliminare l'ambiente virtuale esistente App, per evitare che i file rimasti da quando l'ambiente virtuale è stato gestito automaticamente.


[Creare una macchina virtuale che esegue Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ compilatore per Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
