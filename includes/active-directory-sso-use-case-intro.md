Organizzazioni usano altre applicazioni [Software come un servizio](https://azure.microsoft.com/overview/what-is-saas/) per la produttività perché tecnologia cloud e strumenti sono sempre più disponibili. Man mano che aumenta il numero di App SaaS, diventa difficile per gli amministratori per gestire gli account e diritti di accesso e per gli utenti a ricordare le password diverse. La gestione di queste applicazioni singolarmente crea operazioni aggiuntive e meno sicuro.


- I dipendenti con per tenere traccia delle password molti comandi sono particolarmente utili utilizzare meno sicuro metodi per ricordare, annotare le password o utilizzando le stesse password tra molti account.

- Quando arriva un nuovo dipendente o uno cambi, ognuno dei loro account deve essere singolarmente viene completato il provisioning o deprovisioning.

- Inoltre, i dipendenti possono iniziare a utilizzare SaaS App per il proprio lavoro senza passano attraverso il reparto IT, vale a dire creano un proprio account su sistemi che gli amministratori IT non approvato e non il monitoraggio.  

Una soluzione per tutti questi problemi è single sign-on (SSO). È il modo più semplice per gestire più applicazioni e offrire agli utenti un'esperienza coerenza sign-on. Azure Active Directory (Azure Active Directory) offre una soluzione Single Sign-on efficace e sono disponibili già integrate applicazioni, con esercitazioni per gli amministratori configurare una nuova app e avviare il provisioning di utenti rapidamente.


## <a name="how-does-azure-active-directory-integrate-apps"></a>In che modo Azure Active Directory integrare App?  

Azure Active Directory consente l'integrazione delle applicazioni e gli account di provisioning. Può essere gestito tramite uno dei due modi.

- Se l'app non è già integrato nell'applicazione di raccolta, è possibile accedere tramite il portale per configurare le App e configurare le impostazioni per consentire di Single Sign-on. Per un'app qualsiasi raccolta, è possibile iniziare da seguire le istruzioni di dettagliate semplice presentate nella raccolta di app e nel portale di Azure per abilitare il single sign-on.

- Se l'app non è presente nella raccolta, è possibile impostare ancora la maggior parte delle applicazioni in Azure Active Directory come un'app personalizzata. È necessario un po' più tecnico competenze necessarie per configurare. È possibile aggiungere qualsiasi applicazione che supporta SAML 2.0 come app federati o qualsiasi applicazione che è basate su HTML accesso pagina come un'app di Single Sign-on password.

Nel caso in cui gli utenti hanno creato il loro account per le applicazioni di SaaS che non siano gestite da IT, lo strumento di [Individuazione di App Cloud](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) offre una soluzione. Questo strumento controlla il traffico web per identificare quali App sono utilizzati in tutta l'organizzazione e il numero di utenti che usano ciascuno di essi. IT può usare queste informazioni per scoprire quali App per gli utenti preferiscono e decidere quale integrare Azure Active Directory per Single Sign-on.  

Quando si integra un'app in Azure Active Directory, è possibile eseguire il mapping di identità di applicazione stabiliti degli utenti alle rispettive identità di Azure Active Directory.  
