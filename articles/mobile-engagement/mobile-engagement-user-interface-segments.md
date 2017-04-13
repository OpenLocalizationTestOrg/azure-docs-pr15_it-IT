<properties 
   pageTitle="Interfaccia utente di Azure impegno per dispositivi mobili - segmenti" 
   description="Informazioni su come creare e gestire segmenti di utenti per identificare i modelli di utilizzo con coinvolgimento di Azure Mobile" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Come creare e gestire segmenti di utenti per identificare i modelli di utilizzo

In questo articolo descrive la scheda **segmenti** del portale di **Impegno Mobile** . Utilizzare il portale **Mobile impegno** per monitorare e gestire le App per dispositivi mobili.

La sezione segmenti dell'interfaccia utente consente di utilizzare segmenti gli utenti in base al comportamento diverso e analitica che è possibile leggere dall'applicazione e possono accedere anche tramite l'API segmenti. Segmenti vengono calcolate prima di tutto 24 ore successive vengono create e vengono ricalcolate ogni 24 ore in base alle informazioni analitica più recente. Una volta che viene calcolato un segmento, viene visualizzato un grafico "Day alla cronologia giorno" ogni giorno.


>[AZURE.NOTE] Numero di sezioni del portale di **Impegno Mobile** dell'interfaccia utente è disponibile il pulsante **Visualizza Guida** . Premere questo tasto per ottenere maggiori informazioni contestuali su una sezione.

## <a name="create-segments"></a>Creare segmenti
È possibile creare un segmento in base a fino a 10 criteri in un periodo specifico alto per 60 giorni in passato dalla sezione analitica. Ad esempio, è possibile creare un segmento in base alle persone che hanno visualizzato determinate pagine o cercare contenuto specifico all'interno dell'applicazione negli ultimi 10 giorni. Queste informazioni sono disponibili nella sezione analitica. Pertanto, è possibile utilizzare per creare un segmento e quindi impostare una notifica push di destinazione questo sottoinsieme di utenti ottenerli torni all'applicazione. 
 
> Nota: Dopo aver calcolato un segmento, non può essere modificato; solo può essere duplicato (copiato) o eliminato (eliminato). È possibile duplicare un segmento della stessa applicazione (con la stessa AppID) e possono anche essere duplicato in altre applicazioni (con un diverso AppID). 
 
 ![segments1][35] 

## <a name="examples-segments"></a>Esempi segmenti
 ![segments2][36]

Segmenti consentono di segmento di utenti finali dell'applicazione.
Segmenti agli utenti è una strategia di marketing importante. Azure coinvolgimento Mobile consente di ottenere dati cronologici e creare segmenti personalizzati. Questo potente strumento consente di ottenere informazioni sull'esperienza dei clienti dell'applicazione. È possibile facilmente analizzare i segmenti e utilizzare i segmenti come destinazioni push.
Un caso di utilizzo comune è che si desidera inviare un push una notifica per incoraggiare gli utenti finali per valutare l'applicazione nell'archivio. Invece di inviare una notifica a tutti gli utenti finali, è possibile creare un segmento specificati solo gli utenti che utilizzano l'applicazione ogni giorno per l'ultimo mese e hanno un utente l'esperienza utente. Quando si inviano notifiche push di un numero minore di altamente destinazione, viene visualizzato un ROI migliore.
 
 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>È possibile creare segmenti in base ai principali elementi di Azure Mobile impegno:
- Evento: creare un segmento di tale destinazioni uno specifico degli eventi dell'applicazione che si sono verificati più di due volte una settimana. 
- Sessione: creare un segmento di utenti che hanno implementato l'applicazione di più di 5 per ultima settimana.
- Attività: creare un segmento di utenti che utilizzano una pagina o contenuto più o meno di 10 ora ultimo mese.
- Processo: creare un segmento di utenti che hanno completato un processo più di due volte un giorno.
- Arresto anomalo: creare un segmento di tutti gli utenti che hanno un arresto anomalo più di 10 volte la settimana scorsa. (Impossibile push questo segmento con un scusa o persino tagliando!)
- Errore: creare un segmento di tutti gli utenti che hanno un messaggio di errore più di 100 volte ultimi 3 giorni.
- Informazioni di App: creare un segmento destinato a un informazioni App personalizzato che si sono verificati durante l'ultimi 25 giorni.
 
 ![segments4][38]

Per utilizzare segmento in modo ottimale, deve lavorare un'integrazione personalizzata di SDK nell'applicazione con un piano di tagging del tag "Informazioni App".
Quindi, passare alla home page dell'interfaccia, selezionare l'applicazione desiderata e fare clic sulla sezione "Segmenti".

1. Selezionare la sezione "Segmenti".
2. Fare clic su "nuovo segmento" pulsante per creare un nuovo segmento.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Esempio di vita reale: Creare un segmento semplice in base alle informazioni "Sessione"
Creare un segmento di tutti gli utenti finali che utilizzano l'app almeno 50 volte nell'ultima settimana. A questo punto, trovare solo gli utenti finali dedicato almeno 30 secondi nell'app per la sessione. Questa operazione verrà visualizzati tutti gli utenti finali che hanno un'esperienza positiva nell'app. Quindi, il segmento creato potrebbe essere utilizzato per inserire una notifica a questi utenti finali di chiedere di valutare l'app nello store.
 
 ![segments5][39]

1. Specificare un nome per il segmento per trovare rapidamente nell'elenco "Segmento".
2. Fare clic sul pulsante "Crea".
 
 ![segments6][40]

Scegliere sessione.
 
 ![segments7][41]

1. Selezionare il periodo di "Ultima settimana".
2. Fare clic su Avanti.
 
 ![segments8][42]

1. Selezionare l'operatore è rilevante nell'elenco: =; ≥, ≤.
2. Immettere il numero desiderato.
3. Selezionare quella desiderata. 
4. Fare clic su Avanti.
In questo esempio viene impostato in modo che nell'ultima settimana, corrispondono agli utenti che hanno apportato almeno 50 sessioni.
 
 ![segments9][43]

Per segmentazione sessione, è possibile scegliere la lunghezza per sessione come criterio.

1. Selezionare l'operatore dall'elenco.
2. Specificare la lunghezza per la sessione.
3. Fare clic su Avanti.
In questo esempio, un messaggio informa che che su tutte le sessioni che sono state segmentato nella sezione ricorrenza, selezionare solo gli utenti che hanno dedicato più di 30 secondi per la sessione.
 
 ![segments10][44]

Assegnare un nome il criterio per recuperare nell'imbuto completa e fare clic su Fine.
 
 ![segments11][45]

Al termine dell'impostazione del criterio, verrà visualizzato nell'imbuto segmento.
Perché un segmento basato sui dati analitica, segmenti vengono calcolate una volta al giorno.
In questo esempio, 47,7 il criterio di corrispondenza % del totali finali. Dovrebbero essere gli utenti che hanno avuto luogo un'esperienza soddisfacente e possibilità di esprimere una valutazione successiva se si inserisce in una notifica chiedendo loro di valutare l'app nello store.


## <a name="see-also"></a>Vedere anche

- [Concetti][Link 6]
- [Risoluzione dei problemi del servizio di Guida][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 
