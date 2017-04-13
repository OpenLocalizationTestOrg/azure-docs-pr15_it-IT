<properties
    pageTitle="Azure playbook la protezione dell'identità di Active Directory | Microsoft Azure"
    description="Informazioni su come la protezione dell'identità di Azure Active Directory consente di limitare la possibilità degli utenti malintenzionati per sfruttare un'identità compromesso o dispositivo e proteggere un'identità o un dispositivo che in precedenza è stato sospetto o noto per essere compromessa."
    services="active-directory"
    keywords="protezione dell'identità di Azure active directory, individuazione app cloud, la gestione applicazioni, sicurezza, rischio, il livello di rischio, vulnerabilità, criteri di sicurezza"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-playbook"></a>Azure playbook la protezione dell'identità di Active Directory 

Questo playbook consente di:

- Compilare i dati nell'ambiente di protezione dell'identità vulnerabilità e simulare eventi di rischio
- Configurare i criteri di accesso condizionale basati sui rischi e testare l'impatto di questi criteri


## <a name="simulating-risk-events"></a>Simulare eventi di rischio

In questa sezione vengono fornite procedura per simulare i tipi di evento di rischio seguenti:

- Componenti aggiuntivi di accesso da indirizzi IP anonimi (semplici)
- Componenti aggiuntivi di accesso da postazioni familiarità (stampa)
- Impossibile viaggio in posizioni insolite (difficile)

Non è possibile simulare altri eventi di rischio in modo sicuro.


### <a name="sign-ins-from-anonymous-ip-addresses"></a>Componenti aggiuntivi di accesso da indirizzi IP anonimi

Questo tipo di evento di rischio identifica gli utenti che hanno effettuato da un indirizzo IP che è stato identificato come indirizzo IP proxy anonimo. Vengono utilizzati dagli utenti per nascondere l'indirizzo IP del dispositivo in uso e possono essere utilizzati per azione di malintenzionati.

**Per simulare un'accessohttp da un indirizzo IP anonimo, eseguire le operazioni seguenti**:

1.  Scaricare il [Browser TR](https://www.torproject.org/projects/torbrowser.html.en).
2.  Tramite il Tor Browser, passare a [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3.  Immettere le credenziali dell'account che si desidera visualizzare nel report **gli accessi ricevuta da indirizzi IP anonimi** .

Accesso aggiuntivo verrà visualizzato nel dashboard di protezione dell'identità all'interno di 5 minuti. 


###<a name="sign-ins-from-unfamiliar-locations"></a>Componenti aggiuntivi di accesso da postazioni con

Il rischio di percorsi è un meccanismo di valutazione di accesso in tempo reale che vengono tenuti in considerazione oltre percorsi di accesso (IP, latitudine / longitudine e ASN) per determinare nuovi / con posizioni. Il sistema memorizza IP precedente, latitudine / longitudine e ASN di un utente e vengono tenuti in considerazione per essere posizioni familiari. Un percorso di accesso viene considerato con se il percorso di accesso non corrisponde a uno dei percorsi di familiari esistenti.

Protezione dell'identità di Azure Active Directory:  

 - ha un periodo di apprendimento iniziale di 14 giorni in cui non contrassegna le nuove posizioni con percorsi.
 - Ignora i componenti aggiuntivi di accesso da dispositivi familiari e posizioni di limitazioni geografiche vicino una posizione familiare esistente.

Per simulare con posizioni, è necessario accedere da un percorso e un dispositivo che l'account non è firmato dalla prima. 


**Per simulare un'accessohttp da una posizione con, eseguire le operazioni seguenti**:

1.  Scegliere un account dotato di almeno una cronologia di accesso di 14 giorni. 

2.  Eseguire una:
    
    un. Durante l'uso di una rete VPN, passare a [https://myapps.microsoft.com](https://myapps.microsoft.com) e immettere le credenziali dell'account che si desidera simulare l'evento di rischio per.

    b. Richiedere un collega in un percorso diverso per accedere con le credenziali dell'account (scelta non consigliate).

Accesso aggiuntivo verrà visualizzato nel dashboard di protezione dell'identità all'interno di 5 minuti.
 
### <a name="impossible-travel-to-atypical-location"></a>Impossibile viaggio insolita posizione
Simulare la condizione di viaggio Impossibile risulta difficile perché l'algoritmo utilizza apprendimento eliminare falsi positivi, ad esempio Impossibile viaggio da dispositivi familiari o accessi da VPN utilizzati da altri utenti nella directory. Inoltre, l'algoritmo richiede una cronologia di accesso di 3-14 giorni per l'utente prima di iniziare la generazione di eventi di rischio.

**Per simulare un viaggio Impossibile in posizione insolita, eseguire le operazioni seguenti**:

1.  Tramite il browser standard, passare a [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2.  Immettere le credenziali dell'account che si desidera generare un evento di rischio viaggio Impossibile per.

3.  Modificare l'agente utente. È possibile modificare agente utente in Internet Explorer dagli strumenti di sviluppo o modificare l'agente utente in Firefox o Chrome in un componente aggiuntivo riquadro di selezione tipo di agente utente.

4.  Modificare l'indirizzo IP. È possibile modificare l'indirizzo IP tramite una rete VPN, un componente aggiuntivo TR, o che gira su un nuovo computer Azure in un centro di dati diversi.

5.  Effettuare l'accesso a [https://myapps.microsoft.com](https://myapps.microsoft.com) utilizzando le stesse credenziali come prima e all'interno di alcuni minuti dopo il segno di precedente.

Accesso appariranno nel dashboard di protezione dell'identità all'interno di 2 a 4 ore.<br>
A causa di macchina complessa formazione modelli in questione, è probabile che verrà non da selezionare.<br> È consigliabile replicare questa procedura per più account Azure Active Directory.


## <a name="simulating-vulnerabilities"></a>La simulazione vulnerabilità 
Le vulnerabilità sono weaknesses in un ambiente di Azure Active Directory che possono essere utilizzati da un attore non valido. Attualmente 3 tipi di vulnerabilità vengono resi disponibili in protezione di Azure Active Directory identità che offre altre funzionalità di Azure Active Directory. Queste vulnerabilità verranno visualizzate nel dashboard di protezione dell'identità automaticamente una volta configurate queste caratteristiche.

-   Azure AD [autenticazione a più fattori?](../multi-factor-authentication/multi-factor-authentication.md)
-   Azure AD [Individuazione App Cloud](active-directory-cloudappdiscovery-whatis.md).
-   Azure Active Directory [di gestione delle identità privilegi](active-directory-privileged-identity-management-configure.md). 



##<a name="user-compromise-risk"></a>Rischio di violazione di utente

**Per testare il rischio di violazione di utente, eseguire le operazioni seguenti**:

1.  Effettuare l'accesso a [https://portal.azure.com](https://portal.azure.com) con le credenziali di amministratore globale per il tenant.

2.  Passare alla **protezione dell'identità**. 

3.  Nella finestra principale e **protezione dell'identità di Azure Active Directory** , fare clic su **Impostazioni**. 

4.  E il **Portale di impostazioni** in **regole di protezione**, scegliere **utente compromettere rischio**. 

5.  Scegliere e il **rischio l'accesso** , disattivare **Abilita regola** e quindi fare clic su **Salva** impostazioni.

6.  Per un account utente specificato, simulare una con percorsi o un evento di rischio IP anonimo. Eleva il livello di rischio utente per l'utente a **Media**.

7.  Attendere alcuni minuti e quindi verificare che il livello di utente per l'utente **medio**.

8.  Passare a e il **Portale di impostazioni** .

9.  **In e il **Rischio di violazione di utente** , in **Attiva regola**, selezionare.** 

10. Selezionare una delle opzioni seguenti:

    un. Per bloccare, selezionare **medio** in **Blocca l'accesso**.

    b. Per applicare la modifica della password di protezione, selezionare **medio** in **richiedono l'autenticazione a più fattori**.

13. Fare clic su **Salva**.

14. È ora possibile testare basati sui rischi accesso condizionato effettuando l'accesso con un utente con un livello di rischio con privilegi elevati. Se il rischio di utente è Media, a seconda della configurazione del criterio, l'accesso aggiuntivo non sia bloccato o è necessario modificare la password. 
<br><br>
![Playbook](./media/active-directory-identityprotection-playbook/201.png "Playbook")
<br>

 
##<a name="sign-in-risk"></a>Rischio di accesso

 
**Per provare un segno di rischio, procedere come segue:**

1.  Effettuare l'accesso a [https://portal.azure.com](https://portal.azure.com) con le credenziali di amministratore globale per il tenant.

2.  Passare alla **protezione dell'identità**.

3.  Nella finestra principale e **protezione dell'identità di Azure Active Directory** , fare clic su **Impostazioni**. 

4.  E il **Portale di impostazioni** in **regole di protezione**, scegliere **Accedi rischio**.

5.  E il **rischio l'accesso **, selezionare **** in **Attiva regola**. 

7.  Selezionare una delle opzioni seguenti:

    un. Per bloccare, selezionare **medio** in **Blocca l'accesso**

    b. Per applicare la modifica della password di protezione, selezionare **medio** in **richiedono l'autenticazione a più fattori**.

8.  Per bloccare, seleziona medio in blocco effettuare l'accesso.

9.  Per applicare l'autenticazione a più fattori, selezionare **medio** in **richiedono l'autenticazione a più fattori**.

10. Fare clic su **Salva**.

11. È ora possibile testare basati sui rischi condizionale accesso tramite la simulazione le posizioni con o anonimi IP rischio gli eventi in quanto sono entrambi gli eventi rischio **medio** .

<br>
![Playbook](./media/active-directory-identityprotection-playbook/200.png "Playbook")
<br>


## <a name="see-also"></a>Vedere anche

 - [Protezione dell'identità di Azure Active Directory](active-directory-identityprotection.md)
