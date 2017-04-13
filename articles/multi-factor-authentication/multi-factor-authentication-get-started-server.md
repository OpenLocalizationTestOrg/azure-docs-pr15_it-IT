<properties 
    pageTitle="Guida introduttiva con il Server di autenticazione a più fattori Azure"
    description="Questa è la pagina di autenticazione a più fattori Azure che descrive come iniziare a utilizzare Azure MFA Server."
    services="multi-factor-authentication"
    keywords="autenticazione server azure con più fattori autenticazione app attivazione pagina download di autenticazione server"
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

# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Guida introduttiva con il Server di autenticazione a più fattori Azure




<center>![Cloud](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Ora che è stato rilevato se usare locale autenticazione a più fattori, è possibile iniziare più. Questa pagina illustra una nuova installazione del server e ottenere il programma di installazione con Active Directory locale. Se è già installato il server PhoneFactor e ci stiamo per eseguire l'aggiornamento, vedere [aggiornamento al Server a più fattori Azure](multi-factor-authentication-get-started-server-upgrade.md) o se si sta cercando informazioni sull'installazione esclusivamente al servizio web vedere [distribuzione di Azure a più fattori autenticazione Server Mobile App Web servizio](multi-factor-authentication-get-started-server-webservice.md).


## <a name="download-the-azure-multi-factor-authentication-server"></a>Scarica il Server Azure autenticazione a più fattori



Esistono diversi modi che è possibile scaricare il Server di autenticazione a più fattori Azure. Entrambi vengono eseguite tramite il portale Azure. Il primo è gestendo direttamente il Provider di autenticazione a più fattori. Il secondo consiste nell'utilizzare le impostazioni del servizio. La seconda opzione richiede un Provider di autenticazione a più fattori o una licenza di Azure MFA, Azure Active Directory Premium o Enterprise mobilità Suite.


### <a name="to-download-the-azure-multi-factor-authentication-server-from-the-azure-portal"></a>Per scaricare il server di autenticazione a più fattori Azure dal portale di Azure
--------------------------------------------------------------------------------

1. Accedere al portale di Azure come amministratore.
2. A sinistra, selezionare Active Directory.
3. Nella pagina Active Directory, fare clic su **Provider di autenticazione a più fattori** nella parte superiore
4. Nella parte inferiore fare clic su **Gestisci**
5. Viene visualizzata una nuova pagina.  Fare clic su **Downloads.** 
 ![Download](./media/multi-factor-authentication-sdk/download.png)
6. Sopra **Generare le credenziali dell'attivazione**, fare clic su **scaricare.** 
 ![Download](./media/multi-factor-authentication-get-started-server/download4.png)
7. Salvare il download.



### <a name="to-download-the-azure-multi-factor-authentication-server-via-the-service-settings"></a>Per scaricare il Server di autenticazione a più fattori Azure tramite le impostazioni del servizio


1. Accedere al portale di Azure come amministratore.
2. A sinistra, selezionare Active Directory.
3. Fare doppio clic sull'istanza di Azure Active Directory.
4. Nella parte superiore fare clic su **Configura**
![Download](./media/multi-factor-authentication-sdk/download2.png)
5. Selezionare **le impostazioni del servizio di gestione** autenticazione a più fattori
6. Nella pagina Impostazioni servizi, nella parte inferiore dello schermo fare clic su **Vai al portale**.
![Download](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Viene visualizzata una nuova pagina. Fare clic su **Downloads.**
8. Sopra **Generare le credenziali dell'attivazione**, fare clic su **scaricare.**
9. Salvare il download.




## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>Installare e configurare il Server Azure autenticazione a più fattori
Ora che è stato scaricato il server è possibile installare e configurarla.  Assicurarsi che il server in che si sta installando soddisfi i requisiti seguenti:



Requisiti del Server Azure autenticazione a più fattori|Descrizione|
:------------- | :------------- |
Hardware|<li>200 MB di spazio su disco rigido</li><li>processore dotati x32 o x64</li><li>1 GB di RAM maggiore</li>
Software|<li>Windows Server 2008 o versione successiva se l'host è un server del sistema operativo</li><li>Windows 7 o versione successiva se l'host è un sistema operativo del client</li><li>4.0 Microsoft .NET Framework</li><li>IIS 7.0 o versione successiva se l'installazione del servizio web o portale utente SDK</li>

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Requisiti di firewall Server di autenticazione a più fattori Azure
--------------------------------------------------------------------------------
Ogni server MFA devono avere la possibilità di comunicare su porta 443 in uscita per le operazioni seguenti:

- https://PFD.phonefactor.NET
- https://pfd2.phonefactor.NET
- https://CSS.phonefactor.NET

Se per la porta 443 sono limitate firewall in uscita, è necessario aprire gli intervalli di indirizzi IP seguenti:

Subnet IP|Maschera|Intervalli IP
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1-134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1-134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129-70.37.154.254

Se non si siano utilizzando le funzionalità di conferma dell'evento di autenticazione a più fattori Azure e se gli utenti non sono autenticazione con l'App per dispositivi mobili autenticazione a più fattori da dispositivi della rete aziendale pi intervalli possono essere ridotte per le operazioni seguenti:


Subnet IP|Maschera|Intervalli IP
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72-134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72-134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201-70.37.154.206


### <a name="to-install-and-configure-the-azure-multi-factor-authentication-server"></a>Installare e configurare il server di autenticazione a più fattori Azure
--------------------------------------------------------------------------------


1. Fare doppio clic sull'eseguibile. Verrà avviato l'installazione.
2. Nella schermata di selezione cartella di installazione, verificare che la cartella sia corretta e fare clic su Avanti.
3. Una volta completato l'installazione, fare clic su Fine.  Verrà avviata la configurazione guidata.
4. Nella procedura guidata configurazione schermata iniziale, posizionare un controllo **Salta mediante la configurazione guidata di autenticazione** e fare clic su **Avanti**.  Verrà chiudere la procedura guidata e avviare il server.
    ![Cloud](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Tornare nella pagina vengono scaricati server da fare clic sul pulsante **Genera le credenziali di attivazione** .  Copiare le informazioni nel Server Azure MFA nelle caselle disponibili e fare clic su **Attiva**.


La procedura riportata sopra mostra un'installazione express con la configurazione guidata.  È possibile eseguire nuovamente la configurazione guidata autenticazione selezionandolo dal menu Strumenti nel server.



##<a name="import-users-from-active-directory"></a>Importare gli utenti da Active Directory

Ora che il server sia installato e configurato è possibile importare rapidamente gli utenti nel Server Azure MFA.

### <a name="to-import-users-from-active-directory"></a>Per importare gli utenti da Active Directory
--------------------------------------------------------------------------------


1. Nel Server MFA Azure, a sinistra, selezionare **gli utenti**.
2. Nella parte inferiore, selezionare **Importa da Active Directory**.
3. A questo punto è possibile eseguire una ricerca per singoli utenti o eseguire una ricerca nella directory Active Directory per unità organizzative con utenti che.  In questo caso, si verranno specificare gli utenti unità Organizzativa.
4. Evidenziare tutti gli utenti a destra e fare clic su **Importa**.  Verrà visualizzato un menu a comparsa che indica che è stato completato.  Chiudere la finestra di importazione.

![Cloud](./media/multi-factor-authentication-get-started-server/import2.png)

## <a name="send-users-an-email"></a>Inviare un messaggio di posta elettronica agli utenti
Ora che sono stati importati gli utenti nei server di autenticazione a più fattori Azure, si consiglia di inviare agli utenti un messaggio di posta elettronica che li informa che è state registrate in autenticazione a più fattori.

Con il Server di autenticazione a più fattori Azure esistono diversi modi per configurare gli utenti per l'uso di autenticazione a più fattori.  Ad esempio, se si conosce i numeri di telefono degli utenti o erano in grado di importare i numeri di telefono nel Server di autenticazione a più fattori Azure dalla directory della propria azienda, messaggio di posta elettronica verrà informare gli utenti che sono stati configurati per utilizzare l'autenticazione a più fattori Azure, alcune istruzioni sull'uso di autenticazione a più fattori Azure e informare l'utente del numero di telefono in che quest'ultimo riceverà loro l'autenticazione.  

Il contenuto del messaggio di posta elettronica variano a seconda del metodo di autenticazione che è stata impostata per l'utente (ad esempio, telefonata, SMS, app per dispositivi mobili).  Ad esempio, se l'utente è necessario usare un PIN quando l'autenticazione, messaggio di posta elettronica che indica le quali il proprio PIN iniziale è stata impostata su.  In genere è necessario che gli utenti modificare il proprio PIN durante la prima autenticazione.

Se i numeri di telefono degli utenti non configurati o importati nel Server di autenticazione a più fattori Azure o gli utenti sono preconfigurati per usare l'app per dispositivi mobili per l'autenticazione, è possibile inviare loro un messaggio di posta elettronica che consente di sapere che sono stati configurati per l'utilizzo di autenticazione a più fattori Azure e darà per completare la registrazione di account tramite il portale di Azure a più fattori autenticazione utente.  Un collegamento ipertestuale sarà incluso l'utente fa clic su per accedere al portale di utente. Quando l'utente fa clic sul collegamento ipertestuale, il proprio browser verrà aperto e terzi Azure a più fattori autenticazione utente portale della propria azienda.   


### <a name="configuring-email-and-email-templates"></a>Configurazione di posta elettronica e modelli di posta elettronica

Facendo clic sull'icona della posta elettronica sul lato sinistro è possibile configurare le impostazioni per l'invio di questi messaggi di posta elettronica.  Si tratta in cui è possibile immettere informazioni SMTP del server di posta elettronica e consente di inviare un messaggio di posta di larghezza programmato mediante l'aggiunta di un controllo Invia messaggi alla casella di controllo.

![Impostazioni di posta elettronica](./media/multi-factor-authentication-get-started-server/email1.png)

Nella scheda posta elettronica contenuti, verranno visualizzati tutti i modelli di posta elettronica diversi disponibili tra cui scegliere.  Per consentire a seconda di come è stata configurata gli utenti per utilizzare l'autenticazione a più fattori, è possibile scegliere il modello che meglio si preferisce.

![Modelli di posta elettronica](./media/multi-factor-authentication-get-started-server/email2.png)

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Il modo in cui il Server di autenticazione a più fattori Azure gestisce i dati degli utenti

Quando si utilizza l'autenticazione a più fattori (MFA) il Server in locale, un utente vengono memorizzati in server locale. Dati utente persistente non vengono archiviati nel cloud. Quando si esegue un'autenticazione a due fattori, il Server MFA invia dati al servizio cloud di Azure MFA per eseguire l'autenticazione. Quando le richieste di autenticazione vengono inviate al servizio cloud, i campi seguenti vengono inviati nella richiesta e registri in modo che siano disponibili nei rapporti di autenticazione / l'uso del cliente. Alcuni campi sono facoltativi in modo che può essere abilitate o disabilitate all'interno del Server di autenticazione a più fattori. La comunicazione dal Server MFA al servizio cloud MFA Usa SSL/TLS sulla porta 443 in uscita. Questi campi sono:

- ID univoco - nome utente o ID server MFA interno
- Prima e il cognome - facoltativo
- Indirizzo di posta elettronica - facoltativo
- Numero di telefono, quando si effettua una chiamata vocale o l'autenticazione di SMS
- Token di dispositivo - quando si esegue l'autenticazione di app per dispositivi mobili
- Modalità di autenticazione
- Risultato dell'autenticazione
- Nome del Server MFA
- Server MFA IP
- Client IP, se disponibile



Oltre ai campi sopra, il risultato di autenticazione (successo/negazione) e il motivo per qualsiasi rifiuti è memorizzata con i dati di autenticazione e sono disponibili nei report di autenticazione/utilizzo.


## <a name="advanced-azure-multi-factor-authentication-server-configurations"></a>Autenticazione a più fattori Azure Server configurazioni avanzate
Per ulteriori informazioni sulla configurazione avanzata e informazioni di configurazione per l'uso nella tabella seguente.

Metodo|Descrizione
:------------- | :------------- |
[Portale utente](multi-factor-authentication-get-started-portal.md)|  Informazioni sull'installazione e configurazione del portale utente la distribuzione e utente modalità self-service.
[Active Directory Federation Services](multi-factor-authentication-get-started-adfs.md)|Informazioni sulle impostazioni di autenticazione a più fattori Azure con ADFS.
[Autenticazione RADIUS](multi-factor-authentication-get-started-server-radius.md)|  Informazioni sull'installazione e configurazione del Server di MFA Azure con raggio.
[Autenticazione IIS](multi-factor-authentication-get-started-server-iis.md)|Informazioni sull'installazione e configurazione del Server di MFA Azure con IIS.
[Autenticazione di Windows](multi-factor-authentication-get-started-server-windows.md)|  Informazioni sull'installazione e configurazione del Server di MFA Azure con l'autenticazione di Windows.
[Autenticazione LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informazioni sull'installazione e configurazione del Server di MFA Azure con autenticazione LDAP.
[Remote Desktop Gateway e Server di autenticazione a più fattori Azure utilizzando raggio](multi-factor-authentication-get-started-server-rdg.md)|  Informazioni sull'installazione e configurazione del Server di MFA Azure con Gateway Desktop remoto utilizzando raggio.
[Eseguire la sincronizzazione con Active Directory di Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Informazioni sull'installazione e configurazione della sincronizzazione tra Active Directory e il Server di MFA Azure.
[La distribuzione del servizio di autenticazione a più fattori Azure Server Web App per dispositivi mobili](multi-factor-authentication-get-started-server-webservice.md)|Informazioni sull'installazione e configurazione del servizio web di server Azure MFA.
