Azure Marketplace rende disponibile un'ampia gamma di applicazioni web popolari sviluppata da Microsoft, società di terze parti e iniziative software Apri origine. App Web create da Azure Marketplace non richiedono l'installazione del software ad eccezione di browser utilizzato per connettersi al [Portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=529715). 

In questa esercitazione si apprenderanno:

- Come creare una nuova app web tramite Azure Marketplace.

- Informazioni su come distribuire l'applicazione web tramite il portale di anteprima di Azure.
 
È necessario creare un blog WordPress che utilizza un modello predefinito. L'illustrazione seguente mostra l'applicazione completata:


![Blog WordPress][13]

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="create-a-web-app-in-the-portal"></a>Creare un'app web nel portale

1. Accedere al portale di Azure anteprima.

2. Aprire uno facendo clic sull'icona **Marketplace** Azure Marketplace:

    ![Icona del Marketplace][marketplace]

    Oppure facendo clic sull'icona di **Nuovo** nella parte superiore destra del dashboard e seleziona **Marketplace** in bottow dell'elenco.
    
    ![Crea nuova][5]
    
3. Selezionare **Web + Mobile**. Cercare **WordPress** e fare clic sull'icona **WordPress** .

    ![WordPress dall'elenco][7]
    
5. Dopo aver letto la descrizione dell'app WordPress, selezionare **Crea**.

6. Fare clic su app **Web**e fornire i valori necessari per la configurazione dell'applicazione web.
    
    ![configurare l'app][8]

7. Fare clic sul **Database**e fornire i valori necessari per la configurazione del database MySQL. 

    ![configurare il database][database]

8. Specificare un nome per un nuovo gruppo di risorse.

    ![Gruppo di set di risorse][groupname]

8. Se necessario, fare clic su **SOTTOSCRIZIONE**e specificare l'abbonamento da usare. 

7. Dopo aver che definisce il web app, fare clic su **Crea**e attendere la nuova app web viene creata.

   Quando l'app è stata creata, verrà visualizzato il gruppo di risorse contenente database e web app.

   ![gruppo Mostra][resourcegroup]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Avviare e gestire applicazioni web WordPress
    
1. Fare clic sulla nuova app web per visualizzare i dettagli dell'applicazione.

    ![barra di avvio del dashboard][10]

2. Nella pagina **Essentials** , fare clic su **Sfoglia** o il collegamento all' **Url** per aprire una pagina di benvenuto dell'applicazione web.

    ![URL del sito][browse]

3. Se non è installato WordPress, immettere le informazioni di configurazione appropriato necessari affinché WordPress e fare clic su **Installa WordPress** per completare la configurazione e aprire la pagina di accesso dell'applicazione web.

4. Fare clic su **Accedi** e immettere le credenziali.  

5. È necessario una nuova app web WordPress simile all'app web riportata di seguito.    

    ![il sito WordPress][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png
