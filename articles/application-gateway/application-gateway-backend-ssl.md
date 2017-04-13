<properties
   pageTitle="Attivazione dei criteri SSL e to end SSL su Gateway applicazione | Microsoft Azure"
   description="Questa pagina offre una panoramica del Gateway applicazione supporto to end SSL."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>

# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Abilitazione dei criteri SSL e to end SSL su Gateway di applicazioni

## <a name="overview"></a>Panoramica

Applicazione gateway supporta terminazione SSL gateway, dopo il tipo di traffico scorra in genere non crittografato ai server di back-end. In questo modo unburdened dal sovraccarico di costi crittografia/decrittografia ai server web. Tuttavia per alcuni clienti comunicazioni non crittografate ai server di back-end non sono un'opzione accettabile. È possibile a causa di requisiti di sicurezza/conformità o dell'applicazione può accettare solo connessione protetta. Per tali applicazioni gateway applicazione supporta la crittografia SSL to end.

Fine a fine SSL consente di sicuro trasmettere dati riservati per il back-end crittografata fissa sfruttando i vantaggi della funzionalità di bilanciamento del carico di livello 7 quali gateway applicazione fornisce, ad esempio affinità cookie, basato su URL routing, il supporto per il routing basato su siti o possibilità di inserire X-inoltrati-* intestazioni.

Quando è configurato con la modalità di comunicazione SSL to end, gateway applicazione termina sessioni SSL nel gateway e decrittografa il traffico utente. Quindi, si applica regole configurate per selezionare un'istanza di pool back-end appropriati per instradare il traffico a. Gateway di applicazioni quindi avvia una nuova connessione SSL al server di back-end e crittografa nuovamente i dati utilizzando certificato di chiave pubblica del server di back-end prima la trasmissione di richiesta di back-end. Termina per terminare una SSL è abilitata l'impostazione protocollo in BackendHTTPSetting su Https, quindi viene applicato a un pool di back-end. Ogni server di back-end del pool di back-end con SSL to end abilitato deve essere configurato con un certificato per consentire la comunicazione sicura.

![scenario to end ssl][1]

In questo esempio, le richieste utilizzano TLS1.2 vengono indirizzate ai server di back-end Pool1 tramite SSL to end.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Termina per terminare una SSL e whitelist dei certificati

Gateway di applicazioni comunica solo con le varianti di back-end noti che hanno whitelisted il proprio certificato con il gateway di applicazione. Per abilitare whitelist dei certificati, è necessario caricare la chiave pubblica dei certificati server di back-end per il gateway di applicazione (non il certificato radice). Solo le connessioni a back-end noti e whitelisted quindi consentite. I risultati di back-end rimanente in un errore di gateway. I certificati autofirmati sono a scopo di testing e non consigliati per carichi di lavoro di produzione. Questi certificati devono essere whitelisted con il gateway di applicazione come descritto nei passaggi precedenti prima che possono essere utilizzati.

## <a name="application-gateway-ssl-policy"></a>Applicazione dei criteri di SSL Gateway

Applicazione gateway supporta configurabili SSL stato criteri utente, che consentono ai clienti un maggiore controllo connessioni SSL gateway dell'applicazione.

1. SSL 2.0 e 3.0 disabilitate per impostazione predefinita per tutti i gateway di applicazione. Non sono tutto configurabili.
2. Consente di definizione dei criteri SSL opzione per disabilitare i protocolli seguenti 3 - TLSv1\_0, TLSv1\_1, TLSv1\_2.
3. Se non è stata definita alcun criterio SSL tutti e tre (TLSv1\_0, TLSv1\_1, TLSv1_2) sono abilitate.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso sui fine a fine SSL e criteri di SSL, passare a [attivare SSL to end su gateway di applicazioni](application-gateway-end-to-end-ssl-powershell.md) creare un gateway di applicazioni con possibilità di inviare il traffico back-end in formato crittografato.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png