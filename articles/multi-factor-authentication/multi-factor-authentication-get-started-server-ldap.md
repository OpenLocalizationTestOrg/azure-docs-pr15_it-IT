<properties 
    pageTitle="Autenticazione LDAP e Azure Server di autenticazione a più fattori"
    description="Questa è la pagina di autenticazione a più fattori Azure utili nella distribuzione di autenticazione LDAP e il Server di autenticazione a più fattori Azure."
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
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Autenticazione LDAP e Azure Server di autenticazione a più fattori


Per impostazione predefinita, il Server di autenticazione a più fattori Azure è configurato per importare o sincronizzare gli utenti da Active Directory. Tuttavia, può essere configurato per binding alla directory LDAP diverse, ad esempio una directory di ADAM o controller di dominio Active Directory specifico. Quando è configurato per connettersi a una directory tramite LDAP, il Server di autenticazione a più fattori Azure configurabili per agire come un proxy LDAP per eseguire l'autenticazione. Consente inoltre per l'utilizzo di binding LDAP come destinazione RADIUS, per pre-autenticazione degli utenti quando si utilizza l'autenticazione di IIS o per l'autenticazione principale nel portale di Azure a più fattori autenticazione utente.

Quando si utilizza l'autenticazione a più fattori Azure come un proxy LDAP, il Server di autenticazione a più fattori Azure viene inserito tra il client LDAP (ad esempio accessorio VPN, applicazione) e il server di directory LDAP per aggiungere l'autenticazione a più fattori. Per l'autenticazione a più fattori Azure alla funzione, il Server di autenticazione a più fattori Azure deve essere configurato per comunicare con il server di client e la directory LDAP. In questa configurazione, il Server di autenticazione a più fattori Azure accetta le richieste LDAP dal server di client e le applicazioni e li inoltra al server di directory LDAP di destinazione per convalidare le credenziali principale. Se la risposta dalla directory LDAP mostra che le credenziali primarie sono validi, autenticazione a più fattori Azure esegue l'autenticazione fattore di secondo e invia una risposta al client LDAP. Autenticazione intera avrà esito positivo solo se l'autenticazione al server LDAP e l'autenticazione a più fattori hanno esito positivo.





## <a name="ldap-authentication-configuration"></a>Configurazione di autenticazione LDAP


Per configurare l'autenticazione LDAP, installare il Server di autenticazione a più fattori Azure su un server di Windows. Utilizzare la procedura seguente:

1. All'interno del Server di autenticazione a più fattori Azure fare clic sull'icona di autenticazione LDAP nel menu a sinistra.
2. Selezionare la casella di controllo Abilita autenticazione LDAP.![Autenticazione LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. Nella scheda client modificare le porte TCP e porta SSL se il servizio LDAP di autenticazione a più fattori Azure deve essere associato alle porte non standard per ascoltare le richieste LDAP dai client che verrà configurato.
4. Se si prevede di utilizzare LDAPS dal client al Server di autenticazione a più fattori Azure, è necessario installare un certificato SSL nel server che il Server è in esecuzione su. Fare clic su Sfoglia. pulsante accanto alla casella certificato SSL e selezionare il certificato installato che verrà utilizzato per la connessione protetta.
5. Fare clic su Aggiungi... pulsante.
6. Nella finestra di dialogo Aggiungi Client LDAP immettere l'indirizzo IP del dispositivo, server o dell'applicazione per l'autenticazione del Server e il nome di un'applicazione (facoltativo). Il nome dell'applicazione all'interno dei report di autenticazione a più fattori Azure e che venga visualizzato all'interno di SMS o App Mobile messaggi di autenticazione.
7. Selezionare la casella di corrispondenza utente richiede l'autenticazione a più fattori di Azure se tutti gli utenti sono stati o verranno importati nel Server e soggetto a autenticazione mutli fattori. Se un numero significativo di utenti non sono stati ancora importato nel Server e/o sarà escluso dall'operazione di autenticazione mutli fattori, lasciare la casella deselezionata. Vedere la Guida per ulteriori informazioni su questa caratteristica.
8. È possibile ripetere i passaggi da 5 a 7 per aggiungere altri client LDAP.
9. Quando l'autenticazione a più fattori Azure è configurato per ricevere l'autenticazione LDAP, deve essere proxy tali l'autenticazione alla directory LDAP. Di conseguenza, la destinazione nella scheda viene visualizzata sola una singola, grigio opzione per utilizzare una destinazione LDAP. Per configurare la connessione di directory LDAP, fare clic sull'icona di integrazione della Directory.
10. Nella scheda Impostazioni, selezionare il pulsante di opzione Usa specifico configurazione LDAP.
11. Fare clic su Modifica. pulsante.
12. Nella finestra di dialogo Modifica configurazione LDAP compilare i campi con le informazioni necessarie per connettersi alla directory LDAP. Descrizioni dei campi sono inclusi nella tabella seguente. Nota: Queste informazioni sono incluse anche nel file della Guida Server di autenticazione a più fattori Azure.![Integrazione della directory](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. Verificare la connessione LDAP facendo clic sul pulsante Test.
14. Se il test di connessione LDAP è stato completato, fare clic sul pulsante OK.
15. Fare clic sulla scheda filtri. Il Server è preconfigurato per caricare i contenitori, utenti e gruppi di sicurezza da Active Directory. Se l'associazione a un'altra directory LDAP, sarà necessario modificare i filtri di visualizzazione. Fare clic sul collegamento Guida per altre informazioni sui filtri.
16. Fare clic sulla scheda attributi. Il Server è preconfigurato per mappare gli attributi da Active Directory.
17. Se l'associazione di una directory LDAP diversa oppure modificare il mapping degli attributi preconfigurato, fare clic su Modifica. pulsante.
18. Nella finestra di dialogo Modifica attributi modificare i mapping di attributo LDAP per la directory. I nomi degli attributi può essere digitati o selezionati, fare clic su di... pulsante accanto a ogni campo.
19. Fare clic sul collegamento Guida per ulteriori informazioni sugli attributi.
20. Fare clic sul pulsante OK.
21. Fare clic sull'icona impostazioni della società e selezionare la scheda Risoluzione nome utente.
22. Se si connette a Active Directory da un server di dominio, dovrebbe essere possibile lasciare l'Usa Windows sicurezza gli identificatori per abbinare i nomi utente pulsante di opzione selezionato. In caso contrario, selezionare l'attributo identificatore univoco LDAP di utilizzo del pulsante di opzione corrispondente nomi utente. Quando è selezionata, il Server di autenticazione a più fattori Azure tenterà di risolvere ogni nome utente in un identificatore univoco nella directory LDAP. Verrà eseguita una ricerca LDAP al nome utente attributi definiti nell'integrazione Directory -> scheda attributi. Quando un utente viene autenticato, verrà risolto il nome utente per l'identificatore univoco nella directory LDAP e l'identificatore univoco verrà utilizzato per la corrispondenza dell'utente nel file di dati di autenticazione a più fattori Azure. In questo modo per i confronti senza maiuscole/minuscole, come nome utente e come lungo e breve formati questo completa la configurazione del Server di autenticazione a più fattori Azure. Il Server è ora in attesa le porte configurate le richieste di accesso LDAP da client configurati e impostato su proxy le richieste alla directory LDAP per l'autenticazione.


## <a name="ldap-client-configuration"></a>Configurazione di Client LDAP

Per configurare il client LDAP, attenersi alle linee guida:

- Configurare il dispositivo, server o un'applicazione di eseguire l'autenticazione tramite LDAP al Server di autenticazione a più fattori Azure come se fosse directory LDAP. È necessario utilizzare le stesse impostazioni che utilizzerebbero normalmente per connettersi direttamente alla directory LDAP, fatta eccezione per il nome del server o l'indirizzo IP che sarà quello del Server di autenticazione a più fattori di Azure.
- Configurare il timeout LDAP da 30-60 secondi in modo che di tempo per convalidare le credenziali dell'utente con la directory LDAP, eseguire l'autenticazione secondo fattore, ricevere la risposta e quindi rispondere alla richiesta di accesso LDAP.
- Se Usa LDAPS, il dispositivo o il server eseguendo la query LDAP deve attendibile il certificato SSL installato sul Server di autenticazione a più fattori Azure.
