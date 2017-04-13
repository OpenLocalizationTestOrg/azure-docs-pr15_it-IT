<properties
    pageTitle="Risoluzione dei problemi di stato su Azure il traffico Manager ridotto"
    description="Come risolvere i problemi di profili Manager il traffico quando viene illustrato come ridotto stato."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Risoluzione dei problemi di stato su Azure il traffico Manager ridotto

In questo articolo viene descritto come risolvere i problemi di un profilo di Azure il traffico Manager che viene visualizzato uno stato peggiore. Per questo scenario, è consigliabile che sia stato configurato un profilo di gestore del traffico verso ad alcuni dei servizi cloudapp.net ospitato. Quando si seleziona lo stato del manager il traffico, viene visualizzato che lo stato è ridotto.

![stato peggiore](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Passare alla scheda endpoint del profilo, vedere gli endpoint il cui stato è non in linea:

![non in linea](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## <a name="understanding-traffic-manager-probes"></a>Informazioni sul traffico gestione ricerche

- Gestore del traffico considera un endpoint a essere ONLINE solo quando il sondaggio riceve una risposta HTTP 200 dal percorso di sondaggio. Un'altra risposta 200 non è un errore.
- Reindirizzamento 30 x non riesce, anche se l'URL reindirizzato restituisce un 200.
- Per le ricerche HTTPs, gli errori di certificato vengono ignorati.
- Il contenuto effettivo del percorso di sondaggio non ha importanza, come viene restituito un 200. Sondaggio un URL in contenuto statico come "/ favicon.ico" è una tecnica comune. Aggiungere contenuto dinamico, ad esempio pagine ASP non vengano sempre restituite 200, anche quando l'applicazione è integro.
- Procedura consigliata consiste nel configurare il percorso di sondaggio a un elemento è sufficiente logica per determinare che il sito sia verso l'alto o verso il basso. Nell'esempio precedente, impostando il percorso su "/ favicon.ico", sono solo test tale w3wp.exe risponde. La ricerca non è possibile che l'applicazione web sia integro. Un'opzione migliore, è possibile impostare un percorso per un qualcosa, ad esempio "/ Probe.aspx" con la logica per determinare l'integrità del sito. Ad esempio, si potrebbe utilizzare contatori delle prestazioni per l'utilizzo della CPU o misurare il numero di richieste non riuscite. Oppure è possibile tentare di accedere a risorse di database o lo stato della sessione per assicurarsi che l'applicazione web funzioni.
- Se tutti gli endpoint di un profilo sono inferiori, quindi il traffico Manager considera tutti gli endpoint come integro e indirizza il traffico a tutti gli endpoint. Questo comportamento garantisce che i problemi con il meccanismo di sondaggio non vengano un'interruzione completa del servizio.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per risolvere un errore di verifica, è necessario uno strumento che mostra il codice di stato HTTP restituito dall'URL di verifica. Sono disponibili numerosi strumenti che mostrano la risposta HTTP non elaborata.

* [Fiddler](http://www.telerik.com/fiddler)
* [curvatura](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Inoltre, è possibile utilizzare la scheda di rete degli strumenti Debug F12 in Internet Explorer per visualizzare le risposte HTTP.

In questo esempio si desidera visualizzare la risposta da URL sondaggio: http://watestsdp2008r2.cloudapp.net:80/sondaggio. Nell'esempio di PowerShell riportato di seguito viene illustrato il problema.

```powershell
    Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Output di esempio:

```text
    StatusCode StatusDescription
    ---------- -----------------
            301 Moved Permanently
```

Si noti che è ricevuta una risposta di reindirizzamento. Come descritto in precedenza, qualsiasi StatusCode diverso da 200 è considerato un errore. Gestore del traffico viene modificato lo stato dell'endpoint offline. Per risolvere il problema, verificare la configurazione del sito Web per assicurarsi che StatusCode corretto può essere restituito dal percorso di sondaggio. Riconfigurare la ricerca di gestore del traffico in modo che puntino a un percorso che restituisce un 200.

Se il sondaggio Usa il protocollo HTTPS, potrebbe essere necessario disattivare certificato controllo per evitare errori SSL/TLS durante il test. Le seguenti istruzioni di PowerShell disabilitare la convalida dei certificati per la sessione di PowerShell corrente:

```powershell
    add-type @"
    using System.Net;
    using System.Security.Cryptography.X509Certificates;
    public class TrustAllCertsPolicy : ICertificatePolicy {
        public bool CheckValidationResult(
        ServicePoint srvPoint, X509Certificate certificate,
        WebRequest request, int certificateProblem) {
        return true;
        }
    }
    "@
    [System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui metodi di routing il traffico di gestore del traffico](traffic-manager-routing-methods.md)

[Che cos'è il traffico Manager](traffic-manager-overview.md)

[Servizi cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web App](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operazioni su Manager il traffico (riferimento all'API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlet di gestore del traffico Azure][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
