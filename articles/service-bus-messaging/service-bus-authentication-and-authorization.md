<properties 
    pageTitle="Servizio di autenticazione Bus e l'autorizzazione | Microsoft Azure"
    description="Panoramica di autenticazione condiviso accesso firma (SA)."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="service-bus-authentication-and-authorization"></a>Autenticazione Bus di servizio e le autorizzazioni

Applicazioni possono eseguire l'autenticazione a Bus di servizio Azure mediante l'autenticazione di uno dei due condiviso accesso firma (SA) o tramite Azure Active Directory controllo di accesso (noto anche come servizio controllo di accesso o ACS). Condiviso autenticazione firma Access consente alle applicazioni di eseguire l'autenticazione al servizio Bus utilizza un tasto configurato lo spazio dei nomi, o su entità a cui sono associati diritti specifici. È quindi possibile utilizzare questo tasto per generare un token di firma di Access condiviso utilizzabile dai client per eseguire l'autenticazione a Bus di servizio.

>AZURE. SA importante è consigliabile utilizzare ACS, poiché fornisce una combinazione di autenticazione semplice, flessibile e facile da usare per Bus di servizio. Le applicazioni possono utilizzare SA in scenari in cui non necessarie per gestire il concetto di "utente autorizzato."

## <a name="shared-access-signature-authentication"></a>Autenticazione di accesso firma condivisa

[Autenticazione SA](service-bus-sas-overview.md) consente di concessione dell'accesso utente alle risorse di servizio Bus con diritti specifici. Autenticazione SA nel servizio Bus implica la configurazione di una chiave di crittografia con diritti associati a una risorsa Bus di servizio. Client di accedere a quella risorsa presentando token sa che include la risorsa URI viene eseguito l'accesso e la scadenza di cui è stato effettuato l'accesso con la chiave configurata.

È possibile configurare le chiavi per SA in uno spazio dei nomi Bus di servizio. Il tasto si applica a tutte le entità messaggistica nello spazio dei nomi. È inoltre possibile configurare le chiavi sugli argomenti e le code Bus di servizio. È anche possibile SA sul servizio Bus inoltra.

Per utilizzare SA, è possibile configurare un oggetto [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) in un spazio dei nomi, una coda o un argomento che include le seguenti:

- *Nome* che identifica la regola.

- *PrimaryKey* è una chiave di crittografia utilizzata per accesso/convalidare i token SA.

- *Chiave secondaria* è una chiave di crittografia utilizzata per accesso/convalidare i token SA.

- *Diritti* che rappresenta l'insieme dei diritti ascoltare, invia o Gestisci abbia concesso o meno.

Regole di autorizzazione configurate a livello di spazio dei nomi possono concedere l'accesso a tutte le entità in uno spazio dei nomi per i client con i token effettuato l'accesso con il tasto corrispondente. Fino a 12 tale autorizzazione è possono configurare le regole in un spazio dei nomi, una coda o un argomento di Bus di servizio. Per impostazione predefinita, una [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) con pieni diritti viene configurata per ogni spazio dei nomi quando si è effettuato il provisioning.

Per accedere a un'entità, il client richiede un token di SA generato utilizzando una specifica [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Token SA viene generato utilizzando il SHA256 HMAC di una stringa di risorsa è costituito da URI risorsa a cui viene chiesto l'accesso e la scadenza con una chiave di crittografia associata alla regola di autorizzazione.

Supporto per l'autenticazione SA per Bus di servizio è incluso nelle versioni di Azure .NET SDK 2.0 e versioni successive. SA include il supporto per un [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Tutte le API che accettano una stringa di connessione come parametro includono il supporto per le stringhe di connessione SA.

## <a name="acs-authentication"></a>Autenticazione di ACS

L'autenticazione di servizio Bus tramite ACS viene gestito tramite correlato "-sb" ACS dello spazio dei nomi. Se si vuole uno spazio dei nomi ACS guidata per creare uno spazio dei nomi Bus di servizio, non è possibile creare spazio dei nomi Bus di servizio tramite il portale classico Azure; è necessario creare lo spazio dei nomi utilizzando il cmdlet di PowerShell [AzureSBNamespace di nuovo](https://msdn.microsoft.com/library/azure/dn495165.aspx) . Per esempio:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Per evitare la creazione di uno spazio dei nomi ACS, eseguire il comando seguente:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Ad esempio, se si crea un spazio dei nomi di servizio Bus denominato **contoso.servicebus.windows.net**, uno spazio dei nomi ACS complementare denominata **contoso sb.accesscontrol.windows.net** viene eseguito il provisioning automaticamente. Per tutti gli spazi dei nomi sono stati creati prima di agosto 2014, uno spazio dei nomi ACS accompagnamento inoltre è stato creato.

Viene eseguito il provisioning di un'identità di servizio predefinita "proprietario", con pieni diritti, per impostazione predefinita questo spazio dei nomi ACS correlata. È possibile ottenere un controllo preciso a qualsiasi entità Bus di servizio tramite ACS configurando le relazioni di trust appropriato. È possibile configurare identità servizio aggiuntivo per la gestione dell'accesso alle entità Bus di servizio.

Per accedere a un'entità, il client richiede un token SWT ACS con attestazioni appropriate presentando le proprie credenziali. Token SWT devono essere inviate quindi come parte della richiesta a Bus di servizio per attivare l'autorizzazione del client per l'accesso all'entità.

Supporto per l'autenticazione ACS per Bus di servizio è incluso nelle versioni di Azure .NET SDK 2.0 e versioni successive. Questa autenticazione include il supporto per un [SharedSecretTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedsecrettokenprovider.aspx). Tutte le API che accettano una stringa di connessione come parametro includono il supporto per le stringhe di connessione ACS.

## <a name="next-steps"></a>Passaggi successivi

Continuare a leggere [l'autenticazione di firma di Access condiviso con Bus di servizio](service-bus-shared-access-signature-authentication.md) per altri dettagli sui SA.

Per una panoramica delle associazioni di protezione in Bus di servizio, vedere [Firme accesso condiviso](service-bus-sas-overview.md).

È possibile trovare ulteriori informazioni sui token ACS in [come: richiedere un Token ACS mediante il protocollo a capo OAuth](https://msdn.microsoft.com/library/hh674475.aspx).



