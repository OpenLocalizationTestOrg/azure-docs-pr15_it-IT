<properties
    pageTitle="Tipi di eventi di rischio rilevati da Azure Active Directory identità protezione | Microsoft Azure"
    description="In questo argomento viene offerta una panoramica dettagliata di tipi di eventi di rischio disponibili in protezione di Azure Active Directory dell'identità"
    services="active-directory"
    keywords="protezione dell'identità di Azure active directory, individuazione app cloud, la gestione applicazioni, sicurezza, rischio, il livello di rischio, vulnerabilità, criteri di sicurezza"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="markvi"/>

#<a name="types-of-risk-events-detected-by-azure-active-directory-identity-protection"></a>Tipi di eventi di rischio rilevati da Azure Active Directory identità protezione 

In protezione dell'identità di Azure Active Directory, rischio sono eventi che:

- sono state contrassegnato come sospetto

- indicare che un'identità è compromessa. 

In questo argomento viene offerta una panoramica dettagliata dei tipi disponibili di eventi di rischio.


## <a name="leaked-credentials"></a>Credenziali persa

Le credenziali persa vengono trovate pubblicato nel web scuro da ricercatori sulla protezione di Microsoft. Queste credenziali si trovano in genere in formato testo normale. Vengono confrontati con le credenziali di Azure Active Directory e se viene trovata una corrispondenza, vengono segnalati come "Leaked credenziale" in protezione dell'identità.

Perdita credenziali eventi di rischio sono classificati come un evento di rischio gravità "Elevato", in quanto forniscono un'indicazione Cancella che il nome utente e la password siano disponibili agli utenti malintenzionati.

## <a name="impossible-travel-to-atypical-locations"></a>Impossibile viaggi in posizioni insolite

Questo tipo di evento di rischio identifica due accessi provenienti da aree geografiche distante posizioni, in cui almeno uno dei percorsi potrebbe anche essere insolito per l'utente, assegnato comportamento precedente. Inoltre, il tempo tra due accessi è inferiore al tempo che avrebbe richiesto all'utente di viaggio dalla posizione prima alla seconda, che indica che un altro utente utilizza le stesse credenziali. 

Questo algoritmo di apprendimento computer che ignora più evidenti "*falsi*" contribuire alla condizione Impossibile viaggio, ad esempio VPN e posizioni regolarmente utilizzati da altri utenti nell'organizzazione.  Il sistema ha un periodo di apprendimento iniziale di 14 giorni durante il quale apprende comportamento di accesso del nuovo utente.

Impossibile viaggio è in genere un buon indicatore un hacker ha potuto correttamente accesso aggiuntivo. Tuttavia, falsi positivi possono verificarsi quando un utente è in viaggio utilizzando un nuovo dispositivo o una connessione VPN che in genere non viene utilizzata da altri utenti nell'organizzazione. Un'altra origine di falsi positivi è applicazioni che in modo non corretto passano server IP come client IP, che può fornire l'aspetto dei componenti aggiuntivi di accesso è ospitato in atto dall'interfaccia di dati in cui l'applicazione di back-end (si tratta in genere Microsoft Data Center, che può fornire l'aspetto di prendere gli accessi posizionare Microsoft proprietà indirizzi IP). In seguito a tali falsi positivi, il livello di rischio per l'evento di rischio è "**medio**".

##<a name="sign-ins-from-infected-devices"></a>Componenti aggiuntivi di accesso da dispositivi virus

Questo tipo di evento di rischio identifica i componenti aggiuntivi di accesso da dispositivi contenere malware, noti in attivamente comunicare con un server bot. Ciò è dovuto correlazione indirizzi IP del dispositivo dell'utente rispetto a indirizzi IP che sono state in contatto con un server bot. 

L'evento di rischio identifica indirizzi IP non dispositivi utente. Se più dispositivi sono dietro a un solo indirizzo IP e solo alcuni sono controllate da una rete bot, gli accessi da altri dispositivi i trigger questo evento inutilmente, ovvero il motivo per la classificazione di questo evento di rischio come "**bassa**".  

Si consiglia di contattare l'utente e analizzare i dispositivi dell'utente. È inoltre possibile che dispositivo personali dell'utente è presente, o come indicato in precedenza, da un altro utente è stato usando un dispositivo virus dallo stesso indirizzo IP dell'utente. Dispositivi virus spesso contengono virus da malware che non sono ancora stati identificati da parte di software antivirus e può inoltre indicare come abitudini utente errati che potrebbero avere causato il dispositivo di infezioni.

Per ulteriori informazioni su come infezioni malware indirizzo, vedere il [Centro protezione da Malware](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


## <a name="sign-ins-from-anonymous-ip-addresses"></a>Componenti aggiuntivi di accesso da indirizzi IP anonimi

Questo tipo di evento di rischio identifica gli utenti che hanno effettuato da un indirizzo IP che è stato identificato come indirizzo IP proxy anonimo. Vengono utilizzati dagli utenti per nascondere l'indirizzo IP del dispositivo in uso e possono essere utilizzati per azione di malintenzionati.

Si consiglia di contattare immediatamente l'utente per verificare se utenti vi accedessero anonimi indirizzi IP. Il livello di rischio per questo tipo di evento di rischio è "**medio**" poiché sé IP anonimo non è un'indicazione sicura di una violazione dell'account.

## <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Componenti aggiuntivi di accesso da indirizzi IP con attività sospetti

Questo tipo di evento di rischio identifica gli indirizzi IP da cui un numero elevato di tentativi di accesso non vengono rilevato, tra più account utente, per un breve periodo di tempo. Corrisponde a modelli di traffico di indirizzi IP utilizzati da utenti malintenzionati e un indicatore fondamentale che gli account sono già o stanno per essere compromessa. Si tratta di un algoritmo che ignora più evidenti "*falsi positivi*", ad esempio gli indirizzi IP utilizzati regolarmente da altri utenti nell'organizzazione di apprendimento.  Il sistema ha un periodo di apprendimento iniziale di 14 giorni in cui apprende il comportamento di accesso di un nuovo utente e il nuovo tenant.

Si consiglia di contattare l'utente per verificare se sono effettivamente effettuato l'accesso con un indirizzo IP che è stato contrassegnato come sospetto. Il livello di rischio per questo tipo di evento è "**medio**" perché più dispositivi possono essere dietro lo stesso indirizzo IP, mentre solo alcuni può essere responsabile dell'attività sospetto. 


## <a name="sign-in-from-unfamiliar-locations"></a>Accesso aggiuntivo da con percorsi

Questo tipo di evento di rischio è un meccanismo di valutazione di accesso in tempo reale che vengono tenuti in considerazione oltre percorsi di accesso (IP, latitudine / longitudine e ASN) per determinare nuovi / con posizioni. Il sistema le informazioni sulle posizioni precedente utilizzato da un utente e vengono tenuti in considerazione questi percorsi "familiari". Il rischio anche quando viene visualizzato il segno viene eseguita da un percorso che non è presente nell'elenco di percorsi familiarità. Il sistema ha un periodo di apprendimento iniziale di 14 giorni, in cui non contrassegna le nuove posizioni con percorsi. Il sistema ignora inoltre i componenti aggiuntivi di accesso da dispositivi familiari e posizioni di limitazioni geografiche vicino familiare. <br>
Con percorsi possono indicare sicuro che un pirata informatico è in grado si tenta di utilizzare un furto di identità. Falsi positivi possono verificarsi quando un utente è in viaggio, provare una nuova periferica o utilizza una connessione VPN nuova. In seguito a tali falsi positivi, il livello di rischio per questo tipo di evento è "**medio**".


## <a name="azure-ad-anomalous-activity-reports"></a>Report attività anomala di Azure Active Directory

Alcuni di questi eventi di rischio sono state disponibili tramite i report di attività anomala di Azure Active Directory nel portale di Azure. Nella tabella riportata di seguito sono elencati i diversi tipi di evento di rischio e report di **Attività anomala di Azure Active Directory** corrispondente. Microsoft continua a investire in questo spazio e piani per migliorare la precisione di rilevamento degli eventi di rischio esistenti e aggiungere nuovi tipi di evento di rischio in modo continuativo continuamente. 



| Tipo di evento di rischio protezione identità | Corrispondente Azure AD attività anomala Report |
| :-- | :-- |
| Credenziali persa    | Utenti con credenziali persa |
| Impossibile viaggio in posizioni insolite | Attività di accesso irregolare |
| Componenti aggiuntivi di accesso da dispositivi virus    | Componenti aggiuntivi di accesso da dispositivi eventualmente virus |
| Componenti aggiuntivi di accesso da indirizzi IP anonimi  | Componenti aggiuntivi di accesso da origini sconosciute |
| Componenti aggiuntivi di accesso da indirizzi IP con attività sospetti | Componenti aggiuntivi di accesso da indirizzi IP con attività sospetti |
| Segni di da con percorsi    | - |
| Eventi di blocco    | - |

Report attività anomala di Azure Active Directory seguenti non sono inclusi come eventi di rischio in protezione dell'identità di Azure Active Directory e pertanto non sono disponibili tramite la protezione dell'identità. Questi report sono ancora disponibili nel portale di Azure tuttavia verrà eliminati in un determinato momento in futuro come vengono verranno sostituite da eventi di rischio in protezione dell'identità.

- Accessi dopo più errori
- Componenti aggiuntivi di accesso da più aree geografiche


## <a name="see-also"></a>Vedere anche

- [Protezione dell'identità di Azure Active Directory](active-directory-identityprotection.md)


