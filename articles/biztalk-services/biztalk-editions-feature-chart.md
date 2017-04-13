<properties
    pageTitle="Informazioni sulle funzionalità delle edizioni di servizi BizTalk | Microsoft Azure"
    description="Confrontare le funzionalità delle edizioni servizi BizTalk: sviluppo, base, Standard e Premium gratuiti. MABS, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="biztalk-services-editions-chart"></a>BizTalk Services: Grafico edizioni

Azure BizTalk Services offre diverse versioni. In questo articolo consente di determinare quale edizione è appropriato in base alle proprie esigenze scenario e business.


## <a name="compare-the-editions"></a>Confronto delle edizioni

**Liberare (Preview)**

Creare e gestire le connessioni ibrida. Una connessione ibrida è un modo semplice per connettere un sito Web di Azure a un sistema locale, ad esempio SQL Server.

**Per sviluppatori**

Include connessioni ibrida, EAI ed EDI elaborazione dei messaggi con un portale di gestione partner commerciali da usare e il supporto per schemi EDI comuni e RTF elaborazione EDI su X12 e AS2. Creare scenari comuni di indirizzi EAI la connessione di servizi nel cloud con qualsiasi protocolli HTTP/S, resto, FTP, WCF e SFTP per leggere e scrivere i messaggi.  Utilizzare la connettività con sistemi line locale con schede SAP, eBusiness Oracle, DB Oracle, Siebel e SQL Server pronte per l'uso. Utilizzare un ambiente basato sui sviluppo con gli strumenti di Visual Studio per la distribuzione e sviluppo semplificata. Limitazione per motivi di sviluppo e test solo con alcun livello contratto di servizio.

**Base**

Include la maggior parte delle funzionalità di sviluppo con aumenti connessioni connessioni ibrido EAI ponti, EDI contratti e BizTalk scheda Pack. Offre disponibilità e l'opzione di scalare con un livello di contratto di servizio.

**Standard**

Include tutte le funzionalità di base con aumenti connessioni connessioni ibrido EAI ponti, EDI contratti e BizTalk scheda Pack. Offre disponibilità e l'opzione di scalare con un livello di contratto di servizio.

**Premium**

Include tutte le funzionalità Standard con aumenti connessioni connessioni ibrido EAI ponti, accordi EDI e BizTalk scheda Pack. Include anche informazioni sull'archiviazione, disponibilità e l'opzione di scalare con un livello di contratto di servizio.


## <a name="editions-chart"></a>Grafico edizioni
Nella tabella seguente vengono elencate le differenze.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Liberare (Preview)</th>
        <th>Per sviluppatori</th>
        <th>Base</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Prezzo di partenza</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Servizi di Azure BizTalk prezzi</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Azure prezzi calcolatrice</a></td>
</tr>
<tr>
<td><strong>Configurazione minima predefinita</strong></td>
<td>1 unità gratuito</td>
<td>Unità di 1 per sviluppatori</td>
<td>1 unità di base</td>
<td>1 unità standard</td>
<td>Unità di 1 premio</td>
</tr>
<tr>
<td><strong>Scala</strong></td>
<td>Nessuna scala</td>
<td>Nessuna scala</td>
<td>Sì, in incrementi di 1 unità di base</td>
<td>Sì, in incrementi di 1 unità Standard</td>
<td>Sì, in incrementi di 1 unità Premium</td>
</tr>
<tr>
<td><strong>Massimo consentito scalabilità</strong></td>
<td>Nessuna scala</td>
<td>Nessuna scala</td>
<td>Fino a 8 unità</td>
<td>Fino a 8 unità</td>
<td>Fino a 8 unità</td>
</tr>
<tr>
<td><strong>Ponti EAI unitario</strong></td>
<td>Non è incluso</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Include i contratti TPM</td>
<td>Non è incluso</td>
<td>Inclusi. 10 accordi unitario.</td>
<td>Inclusi. 50 accordi unitario.</td>
<td>Inclusi. 250 accordi unitario.</td>
<td>Inclusi. contratti di 1000 unitario.</td>
</tr>
<tr>
<td><strong>Connessioni ibrido unitario</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Ibrido connessione i trasferimenti di dati (GB) per unità</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Connessioni BizTalk scheda Servizi per sistemi di line locale</strong></td>
<td>Non è incluso</td>
<td>1 connessione</td>
<td>2 connessioni</td>
<td>5 connessioni</td>
<td>25 connessioni</td>
</tr>
<tr>
<td align="left"><strong>Sistemi/protocolli supportati:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Bus di servizio (SB)</li>
<li>Archivio Blob Azure</li>
<li>API REST</li>
</ul>
</td>
<td>Non è incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Disponibilità</strong>
<br/><br/>
Per livello contratto di servizio, vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk Services prezzi</a>.
</td>
<td>Non è incluso</td>
<td>Non è incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Eseguire il backup e ripristino</strong></td>
<td>Non è incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Rilevamento modifiche</strong></td>
<td>Non è incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Archiviazione</strong><br/><br/>
Include Non ripudio di conferma (NRR) e il download dei messaggi rilevati</td>
<td>Non è incluso</td>
<td>Incluso</td>
<td>Non è incluso</td>
<td>Non è incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Utilizzo del codice personalizzato</strong></td>
<td>Non è incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Uso delle trasformazioni, tra cui XSLT personalizzato</strong></td>
<td>Non è incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
</table>

> [AZURE.NOTE] Per adattabilità dagli errori hardware, disponibilità implica con più macchine virtuali all'interno di una sola unità BizTalk.


## <a name="faqs"></a>Domande frequenti

#### <a name="what-is-a-biztalk-unit"></a>Che cos'è un'unità BizTalk?
"Unità" è il livello atomico di una distribuzione di Azure BizTalk Services. Ogni edizione viene fornito con un'unità che dispone di memoria e capacità di elaborazione diversi. Ad esempio un'unità di base ha più elaborazione di sviluppo, Standard ha più elaborazione di base e così via. Durante il ridimensionamento BizTalk Service, ridimensionate in termini di unità.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>Che cos'è la differenza tra i servizi BizTalk e Azure BizTalk SV?
Servizi BizTalk offre un'architettura di piattaforma-come-a-Service (PaaS) true per la creazione di soluzioni di integrazione nel cloud. Con il modello PaaS analisi completamente la logica dell'applicazione e lasciare tutta la gestione dell'infrastruttura a Microsoft, tra cui:

- Non è necessario gestire o patch macchine virtuali.
- Microsoft garantisce la disponibilità.
- È possibile controllare scala richiesta richiedendo semplicemente più o meno capacità tramite il portale di Azure.

BizTalk Server in macchine virtuali di Azure fornisce un'architettura di infrastruttura-come-a-Service (IaaS). Creare macchine virtuali e configurarli esattamente come l'ambiente locale, rendendo più semplice per l'esecuzione di applicazioni esistenti nel cloud con alcuna modifica del codice. Con la tecnologia IaaS, si è ancora responsabile della configurazione delle macchine virtuali, gestione macchine virtuali (ad esempio, installare software e patch del sistema operativo) e architettura dell'applicazione per la disponibilità elevata.

Se si desidera creare nuove soluzioni di integrazione di ridurre al minimo l'attività di gestione dell'infrastruttura, utilizzare i servizi BizTalk. Se si desiderano per migrare rapidamente le soluzioni BizTalk esistenti o cercando un ambiente su richiesta sviluppare e testare le applicazioni di BizTalk Server, utilizzare una macchina virtuale Azure BizTalk Server.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>Qual è la differenza tra le connessioni ibrida e servizio scheda BizTalk?
Il servizio scheda BizTalk viene utilizzato da un servizio di BizTalk Azure. Il servizio scheda BizTalk utilizza BizTalk scheda Pack per connettersi a un sistema di linee di Business (Line) locale. Una connessione ibrido offre un modo semplice e comodo per connettere le applicazioni di Azure, ad esempio la caratteristica Web Apps nel servizio di Azure App e servizi mobili di Azure, a una risorsa locale.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>Che cosa significa "Ibrido connessione trasferimenti di dati (GB) per unità"? Si tratta al minuto/ora/giorno/settimana/mese? Cosa accade quando viene raggiunto il limite?

I costi della connessione ibrido unitario dipende l'edizione BizTalk Services. In poche parole, i costi dipendono quantità di dati è trasferire. Ad esempio il trasferimento di dati di 10 GB giornaliero costa meno di trasferimento giornaliero 100 GB. Utilizzare la [Calcolatrice prezzi](https://azure.microsoft.com/pricing/calculator/?scenario=full) per i servizi BizTalk per determinare costi specifici. In genere i limiti vengono applicati a ogni giorno. Se si supera il limite, eventuale sovradosaggio verrà addebitato pari a $1 per GB.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Quando si crea un contratto di servizi BizTalk, perché il numero di ponti salire da due anziché solo uno?

Ogni contratto è composto da due diversi ponti: un bridge di comunicazione invia lato e un bridge di comunicazione lato ricezione.

####  <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>Cosa accade quando raggiunto il limite di quota al numero di ponti o accordi?

il non utente è possibile distribuire qualsiasi nuovi ponti o creare eventuali accordi. Per distribuire informazioni, è necessario passare a più unità del servizio BizTalk o l'aggiornamento a un'edizione superiore.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>Come la migrazione da un livello di BizTalk Services a altra?

La versione gratuita non può essere eseguita la migrazione o 'scalabilità' a un altro livello e non può essere eseguito il backup e ripristinati a un altro livello. Se è necessario un altro livello, creare un nuovo BizTalk Service usando il nuovo livello. Tutti gli elementi creati con l'edizione gratuita, incluse le connessioni ibrida, è necessario ricrearle in nuovo BizTalk Service. 

Per le edizioni rimanente, utilizzare il backup e ripristino per la migrazione del elementi da un livello a un altro. Eseguire il backup, ad esempio, gli elementi nel livello Standard e quindi ripristinarli al livello Premium. [Servizi BizTalk: Backup e ripristino](biztalk-backup-restore.md) descrive i percorsi di migrazione supportati e gli elenchi quali elementi vengono sottoposti a backup. Si noti che le connessioni ibrido non eseguito il backup. Dopo il backup e ripristino di un nuovo livello, quindi ricreare le connessioni ibrida.  


#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>È il servizio scheda BizTalk incluso nel servizio? Come si riceve il software?

Sì, il servizio di scheda BizTalk con BizTalk scheda Pack sono inclusi i SDK dei servizi BizTalk Azure [scaricare](http://www.microsoft.com/download/details.aspx?id=39087).

## <a name="next-steps"></a>Passaggi successivi

Per creare servizi BizTalk Azure nel portale di Azure, passare a [servizi BizTalk: Provisioning tramite il portale di Azure](biztalk-provision-services.md). Per avviare la creazione di applicazioni, passare a [Servizi BizTalk di Windows Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="additional-resources"></a>Risorse aggiuntive
- [Servizi di BizTalk: Il Provisioning nel portale di Azure](biztalk-provision-services.md)<br/>
- [BizTalk Services: Grafico di stato di Provisioning](biztalk-service-state-chart.md)<br/>
- [BizTalk Services: Schede del Dashboard, monitorare e scala](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [BizTalk Services: Eseguire il Backup e ripristino](biztalk-backup-restore.md)<br/>
- [Servizi BizTalk: limitazione](biztalk-throttling-thresholds.md)<br/>
- [Servizi di BizTalk: Nome autorità che emette e chiave](biztalk-issuer-name-issuer-key.md)<br/>
- [Come posso iniziare a usare Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
