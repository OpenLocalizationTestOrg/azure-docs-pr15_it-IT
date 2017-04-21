Sebbene sia possibile incollare un URI MongoLab nel codice, è consigliabile configurazione nell'ambiente per una maggiore facilità di gestione. In questo modo, se l'URI cambiano, è possibile aggiornare il tramite il portale Azure senza passare al codice.


1. Nel portale di Azure, selezionare **Web Apps**.
1. Fare clic sul nome dell'applicazione web nell'elenco Web Apps.  
![WebAppEntry][entry-website]  
Consente di visualizzare il dashboard Web App.

1. Fare clic su **Configura** nella barra dei menu.  
![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. Scorrere fino alla sezione stringhe di connessione.  
![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. Per **nome**, immettere MONGOLAB_URI.
1. Per **valore**, incollare la stringa di connessione che abbiamo ottenuto nella sezione precedente.
1. Selezionare **personalizzato** nella casella di riepilogo a discesa **tipo** (anziché **SQLAzure**impostazione predefinita).
1. Fare clic su **Salva** sulla barra degli strumenti.  
![SaveWebApp][button-website-save]

**Nota:** Azure aggiunge il **CUSTOMCONNSTR\_ ** prefisso a questa variabile, perché il codice sopra riferimenti **CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
