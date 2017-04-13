<properties
    pageTitle="Sicurezza per gli hub di notifica"
    description="In questo argomento viene sicurezza per gli hub di Azure notifica."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="security"></a>Sicurezza

##<a name="overview"></a>Panoramica

In questo argomento viene descritto il modello di sicurezza di Azure notifica hub. Poiché gli hub di notifica sono un'entità Bus di servizio, vengono implementare lo stesso modello di sicurezza di Bus di servizio. Per ulteriori informazioni, vedere gli argomenti [Bus dei servizi di autenticazione](https://msdn.microsoft.com/library/azure/dn155925.aspx) .

##<a name="shared-access-signature-security-sas"></a>Protezione delle firme accesso condiviso (SA) 

Notifica hub implementa uno schema di sicurezza a livello di entità denominata SA (condiviso accesso firma). Questa combinazione attiva messaggistica entità dichiarare un massimo di 12 regole di autorizzazione la relativa descrizione che concedono autorizzazioni a tale entità.

Ogni regola contiene un nome, il valore di chiave (segreto condiviso) e un insieme di diritti, come illustrato nella sezione "Attestazioni di sicurezza". Quando si crea un Hub di notifica, vengono automaticamente create due regole: una con diritti di ascolto (che usa l'app client) e una con pieni diritti (che usa back-end app).

Durante l'esecuzione di gestione di registrazione da applicazioni client, se le informazioni inviate tramite le notifiche non sono riservati (ad esempio gli aggiornamenti meteo), più raramente per accedere a un Hub di notifica per assegnare il valore della chiave della regola di accesso solo ascolto all'applicazione client e per assegnare il valore della chiave dell'accesso completo alla regola di back-end app.

Non è consigliabile incorporare il valore di chiave nelle applicazioni client di Windows Store. Un modo per evitare di incorporare il valore di chiave è che l'applicazione client recuperarla da back-end app all'avvio.

È importante tenere presente che il tasto con access ascolto consente di un'applicazione client per eseguire la registrazione per un tag. Se l'app è necessario limitare le registrazioni a tag specifici ai client specifiche (ad esempio, quando tag rappresentano gli ID utente), il back-end app deve eseguire le registrazioni. Per ulteriori informazioni, vedere Gestione di registrazione. Si noti che in questo modo, l'applicazione client non avranno accesso diretto a un hub di notifica.

##<a name="security-claims"></a>Attestazioni di sicurezza

Analogamente alle altre entità, notifica Hub operazioni consentite per tre attestazioni di sicurezza: ascoltare, inviare e gestire.

| Richiesta di rimborso | Descrizione | Operazioni consentite |
|-------|-------------|--------------------|
| Ascolto | Creare o aggiornare, leggere ed eliminare le registrazioni singole | Creare o aggiornare la registrazione<br><br>Registrazione di lettura<br><br>Leggere tutte le registrazioni per un punto<br><br>Eliminare la registrazione |
| Invia | Inviare messaggi all'hub di notifica | Invia messaggio |
| Gestire | CRUDs gli hub di notifica (compreso l'aggiornamento delle credenziali PNS e chiavi di protezione) e lettura le registrazioni in base ai tag | Hub di notifica di creazione/aggiornamento/lettura/eliminazione<br><br>Leggere le registrazioni con tag |


Notifica hub accetta sulle attestazioni abbia concesso o meno i token di controllo di accesso di Microsoft Azure o token di firma generato con chiavi condivise configurate direttamente su Hub notifica.