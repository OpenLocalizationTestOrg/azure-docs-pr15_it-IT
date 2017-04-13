Script di distribuzione ignorerà la creazione dell'ambiente virtuale in Azure se vengono rilevati che esiste già un ambiente virtuale compatibile.  Questo può velocizzare notevolmente la distribuzione.  Pacchetti che sono già installati verranno ignorati da pip.

In alcuni casi può essere necessario forzare eliminare tale ambiente virtuale.  Si verrà utile se si decide di includere un ambiente virtuale come parte del repository.  È anche consigliabile eseguire questa operazione è necessario eliminare determinati pacchetti o verificare le modifiche apportate a requirements.txt.

Esistono alcune opzioni per gestire l'ambiente virtuale esistente in Azure:

### <a name="option-1-use-ftp"></a>Opzione 1: Utilizzare FTP

Con un client FTP la connessione al server e sarà possibile eliminare la cartella env.  Si noti che alcuni client FTP (ad esempio browser web) sia di sola lettura e non consente di eliminare le cartelle, in modo che sarà necessario assicurarsi di usare un client FTP con tale funzionalità.  Il nome host FTP e l'utente vengono visualizzati in blade dell'applicazione web nel [Portale di Azure](https://portal.azure.com).

### <a name="option-2-toggle-runtime"></a>Opzione 2: Interruttore runtime

Ecco un'alternativa che consente di sfruttare il fatto che lo script di distribuzione eliminerà la cartella env quando non corrispondono a versione desiderata di Python.  Verrà in modo efficace eliminare l'ambiente esistente e crearne uno nuovo.

1. Passare a una versione diversa di Python (tramite runtime.txt o e **l'Impostazioni applicazione** nel portale di Azure)
1. operazioni push alcune modifiche (ignorare gli eventuali errori di installazione pip se presente)
1. Tornare alla versione iniziale di Python
1. operazioni push alcune modifiche nuovamente

### <a name="option-3-customize-deployment-script"></a>Opzione 3: Personalizzare script di distribuzione

Se è stato personalizzato lo script di distribuzione, è possibile modificare il codice di Deploy per forzare l'eliminazione della cartella env.
