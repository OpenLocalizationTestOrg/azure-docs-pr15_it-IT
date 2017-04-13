<properties 
    pageTitle="Nome autorità che emette e chiave in servizi BizTalk | Microsoft Azure" 
    description="Informazioni su come recuperare nome autorità che emette e chiave per Bus di servizio o controllo di accesso (ACS) in servizi BizTalk. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>




# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: Nome autorità che emette e chiave

Azure BizTalk Services utilizza il nome emittente Bus di servizio e chiave e il nome di accesso controllo emittente e chiave. In particolare:

Attività | Quali nome autorità che emette e chiave
--- | ---
Distribuzione dell'applicazione da Visual Studio | Nome emittente controllo di accesso e la chiave
Configurazione del portale di servizi BizTalk Azure | Nome emittente controllo di accesso e la chiave
Creazione di inoltro line con i servizi di adattatore BizTalk in Visual Studio | Nome autorità che emette Bus di servizio e chiave

In questo argomento viene illustrata la procedura per recuperare il nome autorità che emette e chiave. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nome emittente controllo di accesso e la chiave
Il nome di accesso controllo emittente e chiave vengono usate per le operazioni seguenti:

- L'applicazione di servizio BizTalk Azure creato in Visual Studio: per distribuire correttamente l'applicazione BizTalk Service in Visual Studio in Azure, immettere il nome emittente controllo di accesso e chiave. 
- Il portale di servizi BizTalk Azure: Quando si crea un BizTalk Service e aprire il portale di servizi BizTalk, il nome di emittente controllo di accesso e la chiave vengono automaticamente registrati per le distribuzioni con gli stessi valori di controllo di accesso.

### <a name="to-copy-and-paste-the-access-control-issuer-name-and-issuer-key"></a>Per copiare e incollare il nome di accesso controllo emittente e chiave

1. Accedere al [portale classica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel riquadro di spostamento sinistra, selezionare **Servizi BizTalk**.
3. Selezionare il servizio BizTalk. 
4. Selezionare **Le informazioni di connessione** nella barra delle applicazioni. Namespace controllo accesso, emittente predefinito (nome emittente) e come chiave predefinita (chiave) sono elencati e può essere copiata e incollata.  

Per riepilogare:  
Nome autorità che emette = emittente predefinito  
Chiave = chiave predefinita


È inoltre possibile selezionare **Apri portale di gestione di ACS** per ottenere i valori di controllo dell'accesso:

1. Accedere al [portale classica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel riquadro di spostamento sinistra, selezionare **Servizi BizTalk**.
3. Selezionare il servizio BizTalk.
4. Selezionare il pulsante informazioni di connessione e selezionare **Apri portale di gestione di ACS**.
5. Nel portale in **Impostazioni del servizio**, selezionare **Identità di servizio**. Verrà visualizzata l'identità del servizio, vale a dire il valore di nome emittente controllo di accesso. Selezionare il collegamento di identità del servizio per visualizzare la Password, vale a dire il valore di chiave. È possibile copiare i rispettivi valori.<br/><br/>
In **Servizio identità**, ad esempio, viene visualizzato "proprietario". "Proprietario" è il nome di emittente controllo di accesso. Quando si fa clic sul collegamento "proprietario", viene visualizzata la **Password**. Quando si fa clic sul collegamento "Password", viene visualizzato il valore. Questo valore Password è la chiave controllo l'accesso.  

Per riepilogare:  
Nome autorità che emette = nome identità del servizio  
Chiave = valore Password

Nel riquadro di spostamento sinistro, è anche possibile selezionare **Active Directory** per recuperare i valori di controllo di accesso. 

> [AZURE.IMPORTANT]Quando viene creato un Namespace di controllo accesso tramite **Active Directory**, un'identità del servizio **non** viene creata automaticamente. Durante il provisioning di un BizTalk Service, Namespace un controllo di accesso, l'identità del servizio denominato "proprietario" (nome emittente), Password (chiave), e chiave simmetrico vengono creati automaticamente.<br /> 
[Come: servizio di gestione ACS usare per configurare le identità servizio](http://go.microsoft.com/fwlink/p/?LinkID=303942) vengono fornite ulteriori informazioni sull'identità di servizio controllo di accesso.


## <a name="service-bus-issuer-name-and-issuer-key"></a>Nome autorità che emette Bus di servizio e chiave
Nome autorità che emette Bus di servizio e chiave vengono utilizzati da BizTalk scheda servizi. In un progetto di servizi BizTalk in Visual Studio, utilizzare BizTalk scheda Services per connettersi a un sistema Line-of-Business (Line) locale. Per connettersi, creare inoltro LOB e immettere i dettagli di sistema line. In tal caso, è inoltre possibile immettere il nome di servizio Bus autorità che emette e chiave.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Per recuperare il nome di servizio Bus autorità che emette e chiave

1. Accedere al [portale classica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel riquadro di spostamento sinistra selezionare **Bus di servizio**.
3. Selezionare lo spazio dei nomi. Nella barra delle applicazioni, selezionare **Le informazioni di connessione**. Verrà visualizzato il **Predefinito emittente** (nome emittente) e la **Chiave predefinita** (chiave). È possibile copiare i rispettivi valori.  

Per riepilogare:  
Nome autorità che emette = emittente predefinito  
Chiave = chiave predefinita

## <a name="next"></a>Successivo
Altri argomenti relativi ai servizi BizTalk Azure:

-  [Installazione dei servizi di BizTalk Azure SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Esercitazioni: Servizi di Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Come posso iniziare a usare Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Servizi BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## <a name="see-also"></a>Vedere anche
-  [Procedura: utilizzare il servizio di gestione di ACS per configurare l'identità di servizio](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [Servizi di BizTalk: Sviluppo, Basic, Standard e Premium edizioni grafico](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servizi BizTalk: Portale classica di Azure utilizzando il Provisioning](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk servizi: Grafico di stato di Provisioning](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk Services: Schede del Dashboard, monitorare e scala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servizi di BizTalk: Eseguire il Backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 
