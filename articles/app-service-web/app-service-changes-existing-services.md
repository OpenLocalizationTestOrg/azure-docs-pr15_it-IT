<properties
    pageTitle="Servizio App Azure e l'impatto sulla servizi Azure esistenti"
    description="Viene illustrato l'impatto sul nuovo servizio App Azure e delle relative caratteristiche servizi esistenti in Azure."
    services="app-service"
    documentationCenter=""
    authors="yochay"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="yochayk"/>


# <a name="azure-app-service-and-existing-azure-services"></a>Azure servizio App e servizi Azure esistenti

Questo articolo illustra le modifiche ai servizi Azure esistenti nell'ambito di modifica per raggruppare diversi servizi di Azure nel [Servizio App Azure](https://azure.microsoft.com/services/app-service/), una nuova offerta integrata.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="overview"></a>Panoramica

[Azure App](https://azure.microsoft.com/services/app-service/) è un servizio cloud nuove e univoco che consente agli sviluppatori di creare web e App per dispositivi mobili per qualsiasi piattaforma e qualsiasi dispositivo. Servizio App è una soluzione integrata progettata per semplificare funzioni codifica ripetute, integrare sistemi SaaS e dell'organizzazione e automatizzare processi aziendali durante la riunione alle proprie esigenze di sicurezza, l'affidabilità e scalabilità.

Servizio di App riunisce tutti i seguenti esistenti Azure servizi - [siti Web](https://azure.microsoft.com/services/websites/), [Servizi mobili](https://azure.microsoft.com/services/mobile-services/)e [Servizi Biztalk](https://azure.microsoft.com/services/biztalk-services/) in un unico servizio combinato, durante l'aggiunta di nuove funzionalità.  Servizio App consente di ospitare i tipi di app seguenti:

-   Web App
-   App per dispositivi mobili
-   API App
-   App logica

La tabella seguente illustra i mapping di servizi Azure al servizio di App e i tipi di app disponibili all'interno di essa.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Servizio di Azure esistente</th>
<th align="left", style="width:10%">Servizio App Azure</th>
<th align="left", style="width:80%">Che cosa è cambiato</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Siti Web Azure</td>
<td align="left">Web App</td>
<td align="left"><li>Per i siti Web di Azure, App è strettamente limitata a modificare il nome di siti Web in Web Apps.
<p><li>Tutte le istanze esistenti dei siti Web sono ora Web Apps nel servizio di App.</p>
<p><li>È possibile accedere ai tuoi siti Web esistente tramite il <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Portale di Azure</a>, in cui si possono trovare tutti i siti esistenti in <em>Applicazioni Web</em>.</p>
<p><li><em>Web Hosting pianificare</em> è ora <em>App piano di servizio</em>. Un <em>Piano di servizio App</em> può contenere qualsiasi tipo di applicazione del servizio di App, ad esempio le applicazioni Web, Mobile, logica o API.</p>
<p><li>Azure App servizio Web Apps è in genere disponibilità.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">Ulteriori informazioni sulle applicazioni Web</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Servizi di telefonia Mobile Azure</td>
<td align="left">App per dispositivi mobili</td>
<td align="left"><p><li>Servizi mobili continuano a essere disponibile come servizio autonomo e rimangono completamente supportato.</p>
<p><li>App mobile è un tipo di app nel servizio di App che si integra tutte le funzionalità di servizi mobili e altro ancora.</p>
<p><li>È facile eseguire <a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">la migrazione da servizi mobili alle App Mobile</a>.</p>
<p><li>Come parte del servizio di App, App Mobile è possibile ottenere nuove funzionalità oltre ai servizi mobili, ad esempio l'integrazione con locale e sistemi SaaS, bande orarie, WebJobs, le opzioni di ridimensionamento meglio e più di gestione temporanea.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">Ulteriori informazioni sulle App Mobile</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API App</td>
<td align="left">
<p><li>API App è un nuovo tipo di app nel servizio App che consente di creare facilmente e utilizzare API nel cloud.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">Maggiori informazioni sulle App API</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">App logica</td>
<td align="left">
<p><li>Logica App è un nuovo tipo di app nel servizio App che consente di automatizzare facilmente processo aziendale.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">Maggiori informazioni sulle App logica</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Servizi BizTalk Azure</td>
<td align="left">App BizTalk API</td>
<td align="left">
<li><p>Servizi BizTalk continuano a essere disponibile come servizio autonomo e rimangono completamente supportato.</p>
<li><p>Tutte le funzionalità di BizTalk Services sono integrate servizio App come consentire agli utenti API App eseguire scenari di integrazione B2B con uno dei tipi di app e integrazione di applicazioni aziendali nel servizio di App</p>
<li><p>Con le applicazioni di logica a questo punto è possibile automatizzare processi aziendali tramite un'esperienza di progettazione per creare flussi di lavoro</p></td>
</tr>
</tbody>
</table>

Per ulteriori informazioni, visitare [documentazione del servizio di App](https://azure.microsoft.com/documentation/services/app-service/).
