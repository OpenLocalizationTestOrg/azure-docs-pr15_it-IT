<properties 
    pageTitle="Remote Desktop Gateway e Server di autenticazione a più fattori Azure utilizzando raggio"
    description="Questa è la pagina di autenticazione a più fattori Azure utili nella distribuzione di Gateway Desktop remoto (RD) e Server di autenticazione a più fattori Azure utilizzando raggio."
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

# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Remote Desktop Gateway e Server di autenticazione a più fattori Azure utilizzando raggio

In molti casi Gateway Desktop remoto utilizza dei criteri di rete locale per l'autenticazione degli utenti. In questo documento viene descritto come indirizzare una richiesta di RADIUS fuori dal Gateway Desktop remoto (tramite criteri di rete locale) al Server di autenticazione a più fattori.

Il Server di autenticazione a più fattori deve essere installato in un server diverso, che sarà quindi proxy la richiesta RADIUS dei criteri di rete sul Server Gateway Desktop remoto. Dopo avere dei criteri di rete convalidato il nome utente e la password, verrà restituito una risposta al Server di autenticazione a più fattori che esegue il secondo fattore di autenticazione prima di restituire un risultato per il gateway.





## <a name="configure-the-rd-gateway"></a>Configurare il Gateway Desktop remoto

Il Gateway Desktop remoto deve essere configurato per l'invio di autenticazione RADIUS a un Server di autenticazione a più fattori Azure. Dopo l'installazione di Gateway Desktop remoto, configurato e non lavorativi, passare alla proprietà Gateway Desktop remoto. Passare alla scheda archivio RD e sostituirla con utilizza un server centrale dei criteri di rete anziché server locale. Aggiungere uno o più server di autenticazione a più fattori di Azure come server RADIUS e specificare un segreto condiviso per ogni server.





## <a name="configure-nps"></a>Configurare i criteri di rete

Il Gateway Desktop remoto utilizza dei criteri di rete per inviare la richiesta RADIUS per l'autenticazione a più fattori Azure. Per evitare che il Gateway Desktop remoto da timeout prima del completamento autenticazione a più fattori, è necessario impostare un timeout. Utilizzare la procedura seguente per configurare i criteri di rete.

1. In Criteri di rete, espandere il menu RADIUS client e Server nella colonna a sinistra e fare clic su gruppi di Server RADIUS remoti. Accedere alle proprietà del gruppo di SERVER GATEWAY TS. Modificare i server RADIUS visualizzata e fare clic sulla scheda bilanciamento del carico. Modificare il "numero di secondi senza risposta prima che la richiesta venga eliminato" e "Numero di secondi tra le richieste quando server viene identificato come non disponibile" per 30-60 secondi. Fare clic sulla scheda/Account di autenticazione e assicurarsi che le porte raggio specificate corrispondano le porte che il Server di autenticazione a più fattori verrà visualizzato in ascolto.
2. Criteri di rete deve essere configurato anche per ricevere l'autenticazione RADIUS dal Server di autenticazione a più fattori Azure. Fare clic su client RADIUS nel menu a sinistra. Aggiungere il Server di autenticazione a più fattori Azure come client RADIUS. Scegliere un nome descrittivo e specificare un segreto condiviso.
3. Espandere la sezione criteri nel riquadro di spostamento a sinistra e fare clic su criteri richiesta di connessione. Deve contenere un criterio di richiesta di connessione denominata criteri di autorizzazione di GATEWAY Servizi terminal creato al momento Gateway Desktop remoto è stata configurata. Questo criterio inoltra richieste RADIUS al Server di autenticazione a più fattori.
4. Copiare il criterio per crearne uno nuovo. In nuovo criterio, aggiungere una condizione che corrisponde al nome descrittivo Client con il nome descrittivo impostata nel passaggio 2 per il client Azure a più fattori autenticazione Server RADIUS. Modificare il Provider di autenticazione al Computer locale. Questo criterio assicura che quando si riceve una richiesta RADIUS dal Server di autenticazione a più fattori Azure, viene effettuata l'autenticazione in locale invece di inviare una richiesta al Server di autenticazione a più fattori Azure che comporterebbe una condizione di ciclo. Per evitare che la condizione di ciclo, il nuovo criterio deve essere ordinato sopra il criterio originale inoltra al Server di autenticazione a più fattori.

## <a name="configure-azure-multi-factor-authentication"></a>Configurare l'autenticazione a più fattori Azure


--------------------------------------------------------------------------------



Il Server di autenticazione a più fattori Azure è configurato come proxy RADIUS tra Gateway Desktop remoto e dei criteri di rete.  Deve essere installato su un server di dominio che è distinto dal server Gateway Desktop remoto. Utilizzare la procedura seguente per configurare il Server di autenticazione a più fattori Azure.

1. Aprire il Server di autenticazione a più fattori Azure e fare clic sull'icona di autenticazione RADIUS. Selezionare la casella di controllo Abilita RADIUS autenticazione.
2. Nella scheda client assicurarsi che le porte corrispondono a che cosa è configurato in Criteri di rete e fare clic su Aggiungi. pulsante. Aggiungere l'indirizzo IP del server Gateway Desktop remoto, nome dell'applicazione (facoltativo) e un segreto condiviso. Il segreto condiviso sarà necessario che il Server di autenticazione a più fattori Azure e Gateway Desktop remoto.
3. Fare clic sulla scheda di destinazione e scegliere il pulsante di opzione server RADIUS.
4. Fare clic su Aggiungi. pulsante. Immettere l'indirizzo IP, segreto condiviso e le porte del server. A meno che non usa una rete centrale, il client RADIUS e destinazione RADIUS saranno gli stessi. Il segreto condiviso deve corrispondere una configurazione nella sezione client RADIUS del server.

![Autenticazione RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)
