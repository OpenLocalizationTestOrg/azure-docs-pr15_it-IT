<properties
    pageTitle="Azure AD Connect: Domande frequenti | Microsoft Azure"
    description="Questa pagina è domande frequenti su Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-faq"></a>Azure AD Connect domande frequenti

## <a name="general-installation"></a>Installazione generale
**D: installazione funziona se l'amministratore globale di Active Directory Azure ha 2FA abilitato?**  
Con le build febbraio 2016, questa operazione è supportata.

**D: è presente un modo per installare Azure AD Connect automatica?**  
Questa caratteristica è supportata solo per installare Azure AD Connect usando la procedura guidata installazione. Un'installazione automatica e invisibile all'utente non è supportata.

**D: si dispone di un insieme di strutture in cui non è possibile contattare un dominio. Modalità di installazione di Azure AD Connect**  
Con le build febbraio 2016, questa operazione è supportata.

**D: l'agente di integrità di dominio Active Directory lavorare su server core?**  
Sì. Dopo aver installato l'agente, è possibile completare il processo di registrazione utilizzando il seguente cmdlet di PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>Rete
**D: è necessario un firewall, il dispositivo di rete o qualcosa che limita le connessioni di tempo massimo potersi aprire della rete. Quanto tempo deve la soglia di timeout sul lato client essere quando si usa Azure AD Connect?**  
Tutto il software di rete, dispositivi fisici o qualsiasi altro elemento che limita il tempo massimo connessioni possono restare aperte utilizzi una soglia di almeno 5 minuti (300 secondi) per la connessione tra il server in cui è installato il client di Azure AD Connect e Azure Active Directory. Si applica anche a tutti gli strumenti di sincronizzazione Microsoft Identity precedenti.

**D: sono domini (singola etichetta domini) supportati?**  
No, Azure AD Connect non supporta locale insiemi/domini utilizzando domini.

**Q: vengono "tratteggiata" NetBios denominato supportati?**  
No, Azure AD Connect non supporta i locale insiemi/domini in cui il nome NetBios contiene un periodo "." nel nome.

## <a name="federation"></a>Federazione
**D: cosa fare se viene visualizzato un messaggio di posta elettronica che richiedere per rinnovare il certificato di Office 365**  
Utilizzare le indicazioni descritto nell'argomento [rinnovare i certificati](active-directory-aadconnect-o365-certs.md) su come rinnovare il certificato.

**D: ho "Aggiornare automaticamente componente" impostare per il componente di Office 365 applicazione. È necessario eseguire alcuna azione quando attraversa i token certificato di firma automaticamente?**  
Utilizzare le indicazioni delineata l' articolo [rinnovare i certificati](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Ambiente
**D: supportato per rinominare il server dopo l'installazione di Azure AD Connect?**  
No. Modificare il nome del server che il motore di sincronizzazione per non essere in grado di connettersi al database SQL e il servizio non sarà possibile avviare.

## <a name="identity-data"></a>Dati sulle identità
**Q: l'attributo UPN (userPrincipalName) in Azure Active Directory non corrisponde a UPN locale - perché?**  
Vedere gli articoli seguenti:

- [I nomi utente in Office 365, Azure o Intune non corrispondono l'UPN locale o l'ID di accesso alternativo](https://support.microsoft.com/en-us/kb/2523192)
- [Le modifiche non sincronizzate tramite lo strumento di sincronizzazione di Azure Active Directory dopo aver modificato l'UPN di un account utente per usare un dominio federato diverso](https://support.microsoft.com/en-us/kb/2669550)

È inoltre possibile configurare Azure Active Directory per consentire al motore di sincronizzazione di aggiornare l'attributo userPrincipalName come descritto in [termini di caratteristiche di Azure AD Connect sincronizzazione servizio](active-directory-aadconnectsyncservice-features.md).

## <a name="custom-configuration"></a>Configurazione personalizzata
**D: dove sono descritti i cmdlet di PowerShell per Azure AD Connect**  
Fatta eccezione per i cmdlet descritti in questo sito, altri cmdlet di PowerShell disponibili nella Azure AD Connect non sono supportati per gli utenti.

* *D: è possibile utilizzare "server di esportazione/Server importare" disponibili nella *gestione del servizio di sincronizzazione* per spostare configurazione tra servers? * *  
No. Questa opzione non verrà recuperato tutte le impostazioni di configurazione e non deve essere utilizzata. Utilizzare invece la procedura guidata per creare la configurazione di base sul secondo server e utilizzare l'editor di regole di sincronizzazione per generare gli script di PowerShell per spostare una regola personalizzata tra server. Vedere [spostare configurazione personalizzata da attivo a server di gestione temporanea](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server).

## <a name="troubleshooting"></a>Risoluzione dei problemi
**D: come è possibile ottenere assistenza con Azure AD Connect?**

[Ricerca della Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Effettuare una ricerca Microsoft Knowledge Base () per le soluzioni tecniche comuni interruzione Correggi i problemi sul supporto per Azure AD Connect.

[Forum di Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

- È possibile eseguire una ricerca e cercare tecnici le domande e risposte dalla community oppure richiedere assistenza la propria domanda facendo clic [qui](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Azure AD Connect il supporto tecnico](https://manage.windowsazure.com/?getsupport=true)

- Usare questo collegamento per ottenere il supporto tramite il portale di Azure.
