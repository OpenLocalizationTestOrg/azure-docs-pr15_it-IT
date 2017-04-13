<properties 
    pageTitle="Autenticazione RADIUS e Azure Server di autenticazione a più fattori"
    description="Questa è la pagina di autenticazione a più fattori Azure utili nella distribuzione di autenticazione RADIUS e Server di autenticazione a più fattori Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>



# <a name="radius-authentication-and-azure-multi-factor-authentication-server"></a>Autenticazione RADIUS e Azure Server di autenticazione a più fattori

La sezione autenticazione RADIUS consente di abilitare e configurare l'autenticazione RADIUS per il Server di autenticazione a più fattori Azure. RADIUS è un protocollo standard per accettare le richieste di autenticazione e per elaborare le richieste. Il Server di autenticazione a più fattori Azure funge da un server RADIUS e viene inserito tra il client RADIUS (ad esempio accessorio VPN) e la destinazione di autenticazione, che può essere Active Directory (AD), una directory LDAP o un altro server RADIUS, per aggiungere l'autenticazione a più fattori Azure. Per l'autenticazione a più fattori Azure alla funzione, è necessario configurare il Server di autenticazione a più fattori Azure in modo che possano comunicare con il server di client e la destinazione di autenticazione. Il Server di autenticazione a più fattori Azure accetta richieste da un client RADIUS convalida le credenziali rispetto al target di autenticazione, aggiunge l'autenticazione a più fattori Azure e invia una risposta al client RADIUS. Autenticazione intera avrà esito positivo solo se l'autenticazione principale e l'autenticazione a più fattori Azure esito positivo.

>[AZURE.NOTE]
>Il Server MFA supporta solo PAP (autenticazione tramite password) e MSCHAPv2 (Microsoft verifica Handshake Authentication Protocol) RADIUS protocolli quando funge da server RADIUS.  Quando il server MFA funge da proxy RADIUS a un altro server RADIUS che supporta il protocollo, ad esempio Microsoft NPS, è possono utilizzare altri protocolli, ad esempio EAP (extensible autenticazione).
></br>
>Quando si usano altri protocolli in questa configurazione, i token SMS e GIURAMENTO unidirezionali non funzionerà dal momento che il Server MFA non è in grado di avviare una risposta corretta verifica RADIUS utilizza tale protocollo.


![Autenticazione RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="radius-authentication-configuration"></a>Configurazione di autenticazione RADIUS

Per configurare l'autenticazione RADIUS, installare il Server di autenticazione a più fattori Azure su un server di Windows. Se si dispone di un ambiente Active Directory, il server deve essere aggiunti al dominio all'interno della rete. Utilizzare la procedura seguente per configurare il Server di autenticazione a più fattori Azure:

1. All'interno del Server di autenticazione a più fattori Azure fare clic sull'icona di autenticazione RADIUS nel menu a sinistra.
2. Selezionare la casella di controllo Abilita RADIUS autenticazione.
3. Nella scheda client modificare le porte di autenticazione e Accounting porte se il servizio RADIUS di autenticazione a più fattori Azure deve essere associato alle porte non standard per ascoltare le richieste RADIUS dai client che verrà configurato.
4. Fare clic su Aggiungi... pulsante.
5. Nella finestra di dialogo Aggiungi Client RADIUS immettere l'indirizzo IP del dispositivo/server per l'autenticazione per il Server di autenticazione a più fattori di Azure, il nome di un'applicazione (facoltativo) e un segreto condiviso. È necessario che il segreto condiviso corrispondere sul Server di autenticazione a più fattori Azure sia accessorio/server. Il nome dell'applicazione all'interno dei report di autenticazione a più fattori Azure e che venga visualizzato all'interno di SMS o App Mobile messaggi di autenticazione.
6. Selezionare la casella di corrispondenza utente richiede l'autenticazione a più fattori se tutti gli utenti sono stati o verranno importati nel Server e soggetto a autenticazione a più fattori. Se un numero significativo di utenti non sono stati ancora importato nel Server e/o sarà escluso dall'operazione di autenticazione a più fattori, lasciare selezionata la casella. Vedere la Guida per ulteriori informazioni su questa caratteristica.
7. La casella Abilita fallback GIURAMENTO token se gli utenti utilizzeranno l'autenticazione di app per dispositivi mobili di autenticazione a più fattori Azure e si desidera utilizzare GIURAMENTO passcode come un fallback autenticazione per la notifica di chiamata, SMS o push telefono fuori banda.
8. Fare clic sul pulsante OK.
9. Puoi ripetere i passaggi da 4 a 8 per aggiungere altri client RADIUS.
10. Fare clic sulla scheda di destinazione.
11. Se il Server di autenticazione a più fattori Azure è installato in un server di dominio in un ambiente di Active Directory, selezionare il dominio di Windows.
12. Se gli utenti devono essere autenticati rispetto a una directory LDAP, selezionare associazione LDAP. Quando si usa binding LDAP, è necessario fare clic sull'icona di integrazione della Directory e modificare la configurazione LDAP nella scheda impostazioni in modo che il Server è possibile associare alla directory. Istruzioni per la configurazione LDAP sono disponibili nella Guida alla configurazione del Proxy LDAP.
13. Se gli utenti devono essere autenticati rispetto a un altro server RADIUS, selezionare server RADIUS.
14. Configurare il server che il Server di proxy le richieste RADIUS a facendo clic su Aggiungi... pulsante.
15. Nella finestra di dialogo Aggiungi Server RADIUS immettere l'indirizzo IP del server RADIUS e un segreto condiviso. Il segreto condiviso dovranno essere lo stesso nel server RADIUS sia il Server di autenticazione a più fattori Azure. Modificare la porta di autenticazione e la porta di Accounting se porte diversi vengono utilizzate dal server RADIUS.
16. Fare clic sul pulsante OK.
17. È necessario aggiungere il Server di autenticazione a più fattori Azure come client RADIUS in altro server RADIUS in modo che elaborerà le richieste di accesso inviate dal Server di autenticazione a più fattori Azure. È necessario utilizzare lo stesso segreto condiviso configurato nel Server di autenticazione a più fattori Azure.
18. È possibile ripetere questo passaggio per aggiungere altri server RADIUS e configurare l'ordine in cui il Server chiami loro con i pulsanti Sposta su e Sposta giù. È stata completata la configurazione del Server di autenticazione a più fattori Azure. Il Server è ora in attesa le porte configurate per le richieste di accesso RADIUS dai client configurato.   


## <a name="radius-client-configuration"></a>Configurazione di Client RADIUS

Per configurare il client RADIUS, attenersi alle linee guida:

- Configurare il server o un dispositivo per eseguire l'autenticazione tramite RADIUS all'indirizzo IP del Server di Azure a più fattori autenticazione, che verrà utilizzato come server RADIUS.
- Usare lo stesso segreto condiviso è stato configurato in precedenza.
- Configurare il timeout RADIUS da 30-60 secondi in modo che di tempo per convalidare le credenziali dell'utente, eseguire l'autenticazione a più fattori, ricevere la risposta e quindi rispondere alla richiesta di accesso RADIUS.
