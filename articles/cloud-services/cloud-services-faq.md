<properties
    pageTitle="Domande frequenti sui servizi cloud | Microsoft Azure"
    description="Domande frequenti sui servizi Cloud."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="adegeo"/>

# <a name="cloud-services-faq"></a>Domande frequenti sui servizi cloud
In questo articolo fornisce le risposte ad alcune domande frequenti sui servizi Cloud di Microsoft Azure. È inoltre possibile visitare le [Domande frequenti su supporto di Azure](http://go.microsoft.com/fwlink/?LinkID=185083) per informazioni generali su Azure prezzi e supporto tecnico. È anche possibile vedere la [pagina dimensione memoria virtuale di servizi Cloud](cloud-services-sizes-specs.md) per informazioni sulle dimensioni.

## <a name="certificates"></a>Certificati

### <a name="where-should-i-install-my-certificate"></a>Nel punto in cui è necessario installare il certificato?

- **Personale**  
Certificato di applicazione con la chiave privata (\*. pfx, \*p12).

- **AUTORITÀ DI CERTIFICAZIONE**  
Tutti i certificati intermedi passare nell'archivio (criteri e Sub CA).

- **RADICE**  
Archiviare all'autorità di certificazione, in modo che il certificato CA principale devono essere inseriti qui.

### <a name="i-cant-remove-expired-certificate"></a>Non è possibile rimuovere certificato scaduto

Azure impedisce la rimozione di un certificato mentre è in uso. È necessario eliminare la distribuzione che utilizza il certificato o aggiornare la distribuzione con un certificato diverso o rinnovato.

### <a name="delete-an-expired-certificate"></a>Eliminare un certificato scaduto

A condizione che il certificato non sia in uso, è possibile utilizzare il cmdlet di PowerShell [AzureCertificate Rimuovi](https://msdn.microsoft.com/library/azure/mt589145.aspx) per rimuovere un certificato.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>I certificati denominati Windows Azure servizio Management per le estensioni ho è scaduto.

I certificati vengono creati ogni volta che l'estensione viene aggiunta al servizio cloud, ad esempio l'estensione Desktop remoto. I certificati vengono utilizzati solo per crittografare e decrittografare la configurazione dell'estensione privata. Non è importante se questi certificati scadono. La data di scadenza non sia selezionata.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>I certificati eliminato continuano

Questi continuano probabilmente a causa di uno strumento in uso, ad esempio Visual Studio. Ogni volta che si riconnette con uno strumento che utilizza un certificato, nuovamente essere caricato in Azure.

### <a name="my-certificates-keep-disappearing"></a>Mantenere scomparire certificati personali

Quando viene riutilizzato l'istanza di macchina virtuale, vengono perse tutte le modifiche locali. Utilizzare un' [attività di avvio](cloud-services-startup-tasks.md) per installare certificati alla macchina virtuale ogni volta che viene avviato il ruolo.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>Non è possibile trovare i certificati di gestione del portale

Sono disponibili nel portale classica di Azure solo [i certificati di gestione](..\azure-api-management-certs.md) . Portale di Azure corrente non vengono utilizzati i certificati di gestione. 

### <a name="how-can-i-disable-a-management-certificate"></a>Come è possibile disattivare un certificato di gestione?

[I certificati di gestione](..\azure-api-management-certs.md) non può essere disabilitata. Si eliminarli tramite il portale classica Azure quando non si desidera che vengano utilizzati più.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>Creazione di un certificato SSL per uno specifico indirizzo IP

Seguire le istruzioni in [creare un'esercitazione certificato](cloud-services-certs-create.md). Usare l'indirizzo IP come nome DNS.

## <a name="security"></a>Sicurezza

### <a name="disable-ssl-30"></a>Disabilitare SSL 3.0

Per disabilitare SSL 3.0 e utilizzare la protezione TLS, creare un'attività di avvio descritte in questo post di blog: https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

## <a name="scale-a-cloud-service"></a>Ridimensionare un servizio cloud

### <a name="i-cannot-scale-beyond-x-instances"></a>Impossibile ridimensionare oltre X istanze

L'abbonamento di Azure ha un limite al numero di core che è possibile utilizzare. Ridimensionamento non funziona se è stata utilizzata in tutti i core disponibili. Ad esempio, se si dispone di un limite di 100 core, significa che è possibile avere 100 istanze di macchina virtuale A1 dimensioni per il servizio cloud o A2 50 dimensioni istanze macchina virtuale.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Impossibile prenotare un indirizzo IP in un servizio cloud multi-VIP

Assicurarsi che l'istanza di macchina virtuale che si sta tentando di prenotare l'indirizzo IP per sia attivato. In secondo luogo, verificare che si utilizza IP riservati per preoccuparsi distribuzioni di prova e di produzione. **Non** modificare le impostazioni durante l'aggiornamento di distribuzione.

