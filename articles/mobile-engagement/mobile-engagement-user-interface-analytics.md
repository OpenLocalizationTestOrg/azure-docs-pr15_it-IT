<properties
   pageTitle="Interfaccia utente di Azure impegno per dispositivi mobili - Analitica"
   description="Informazioni su come analizzare i dati cronologici sull'applicazione con coinvolgimento di Azure Mobile"
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

# <a name="how-to-analyze-historical-data-about-your-application"></a>Come analizzare i dati cronologici sull'applicazione

In questo articolo descrive la scheda **analisi** del portale di **Impegno Mobile** . Utilizzare il portale **Mobile impegno** per monitorare e gestire le App per dispositivi mobili. Si noti che per iniziare a usare il portale è innanzitutto necessario creare un account **Azure Mobile coinvolgimento** .


La sezione Analitica dell'interfaccia utente fornisce aggregate informazioni sull'applicazione in base a dati cronologici che vengono aggiornati ogni 24 ore. Le informazioni vengono visualizzate nel dashboard diversi composto mappe, griglie e grafici a linee/barre/torta. I dati possono essere inoltre scaricati come file con estensione csv. La maggior parte delle stesse informazioni è disponibile in tempo reale nella sezione del Monitor dell'interfaccia utente, ed è anche possibile accedervi dall'API Analitica.

>[AZURE.NOTE] Numero di sezioni del portale di **Impegno Mobile** dell'interfaccia utente è disponibile il pulsante **Visualizza Guida** . Premere questo tasto per ottenere maggiori informazioni contestuali su una sezione.

## <a name="standard-and-custom-analytics"></a>Analitica standard e personalizzati

Azure coinvolgimento Mobile fornisce un set di informazioni analitiche standard di base relative alle applicazioni che possono essere rappresentate in un grafico non appena si integra l'app SDK. Azure coinvolgimento Mobile offre la possibilità di raccogliere informazioni aggiuntive analitica personalizzato che si desidera sul comportamento degli utenti finali. È possibile eseguire questa operazione mediante la creazione di un piano di tag di "Tag personalizzati (informazioni app)", creata dalle **Impostazioni** in modo che Azure Mobile coinvolgimento può raccogliere dati aggiuntivi dell'utente.



## <a name="analytics"></a>Analitica
- Dashboard: Mostra informazioni generali su agli utenti nuovi e che attiva e le tendenze.
- Degli utenti: Identificazione gli utenti tramite l'identificatore di dispositivo: l'identificatore sia univoco per ogni dispositivo (un nuovo utente è effettivamente un nuovo dispositivo). Un utente è considerato come nuovo in un intervallo di tempo specificato, se si ha eseguito la sua prima sessione durante questo periodo. Un utente è considerato mantenuta se si è eseguito almeno una sessione durante negli ultimi sette giorni. Utenti attivi presenti utenti che sono stati resi almeno una sessione in un determinato periodo. È possibile ordinare, mensile, settimanale, giornaliera o oraria periodi di tempo. Tutti i grafici sono simili ma consentono al filtro in base a caratteristiche diverse, ad esempio la versione dell'applicazione, quindi ordina per un periodo di tempo. Le informazioni standard raccolte tramite l'integrazione di SDK includono quanto segue: utenti attivi, i nuovi utenti, numero di sessioni, lunghezza di ogni sessione, informazioni tecniche sul paese, variabili locali, posizione, vettori lingua, dispositivi, firmware, rete (WIFI), versioni di app e SDK, utilizzato dai clienti. Queste informazioni possono essere visualizzate in tempo reale dalla sezione dei monitor.

> Nota: Il periodo di tempo è in base alla data le impostazioni degli utenti dei dispositivi, in modo un utente il cui telefono impostato la data in modo non corretto può visualizzate nel periodo di tempo errato.

- Criteri di conservazione: Un utente è considerato mantenuti in un intervallo di tempo specificato, se si ha eseguito la sua prima sessione durante questo periodo. È possibile modificare gli intervalli di tempo in cui vengono contati mantenuti utenti (e nuovi utenti) in ore, giorni, settimane o mesi. Analitica di conservazione utente si basa su collaboratori. Un coorte è l'insieme di tutti i nuovi utenti rilevato per un dato periodo (ad esempio, l'insieme di utenti che eseguono la sessione prima durante questo periodo). Serve collaboratori di 1 giorno, 2 giorni, 4 giorno, 7 giorni o 1 mese. Dato un coorte, ogni giorno 1, 2 giorni, 4 giorno, 7 giorni o 1 mese, Azure coinvolgimento Mobile calcola il set di tutti gli utenti che appartengono al coorte e sono ancora attive (ad esempio, l'insieme di utenti che ha eseguito almeno una sessione durante il periodo). In questo gruppo di utenti si chiama una versione coorte. (Azure coinvolgimento Mobile consente di visualizzare il numero degli utenti è ancora usando l'app, ma solo l'archivio specifici della piattaforma può indicare il numero di utenti dopo la disinstallazione iTunes l'app: ad esempio GooglePlay, Windows Store, ecc.).
- Sessioni: Un utilizzo dell'applicazione da un utente. Sessioni generate da sequenza delle attività eseguite dagli utenti (un'attività è generalmente associata all'utilizzo di una schermata dell'applicazione, ma questo può variare a seconda di come SDK è stata integrata nell'applicazione). Un utente può eseguire solo un'attività alla volta: avviare una sessione non appena l'utente verrà avviata la propria attività prima e si arresta quando avrà terminato il suo ultima attività. Se un utente resta più di alcuni secondi senza eseguire alcuna attività, il suo sequenza delle attività è suddiviso in due sessioni distinte.
- Attività: I nomi di ogni schermata dell'applicazione e il periodo di tempo utente dedicate a ogni schermata. Attività sono un'opzione di analisi personalizzata corrispondenti ai tag "informazioni app" è stata configurata per il proprio app:
- Percorso utente: Mostra come gli utenti sposteranno tra le attività dell'applicazione (schermate). È possibile spostare il dispositivo di scorrimento per regolare il livello dei dettagli. Nodi blu rappresentano le attività dell'applicazione. Le dimensioni sono proporzionali il tempo impiegato per gli utenti al suo interno. Nodi bianchi rappresentano Interrompi e iniziare a sessione. Nodi rossi rappresentano anomalo. I collegamenti rappresentano le transizioni tra le attività dell'applicazione (o tra le attività e anomalo). Fare clic su un nodo o un collegamento per visualizzare una descrizione comando con ulteriori informazioni sui dati: il tempo trascorso in un determinato screen il conteggio delle transizioni e la percentuale di transizioni da attività dell'origine per le attività di destinazione. (Un---60%---> B indica che gli utenti sull'attività A passa all'attività B 60% del tempo.) È possibile riorganizzare il grafico che si desidera rendere più chiara. posizione verrà salvata ogni volta che si apporta una modifica. È possibile visualizzare o nascondere anomalo per schiarire il grafico.
- Eventi: Azioni specifiche da un utente dell'applicazione. Distribuzione degli eventi viene visualizzata come il numero di eventi per ogni utente per la sessione. Un evento rappresenta un'azione immediata, ad esempio, fare clic su un pulsante o la ricezione di una notifica. (Il significato degli eventi dipende come SDK è stata integrata nell'applicazione). Un evento può verificarsi durante una sessione o un processo o può essere rappresentato da singolarmente.
- Processi: Simili agli eventi, ad eccezione di siano incentrate sulla lunghezza dell'azione. Ad esempio processi possono indicare informazioni tecniche sul tempo necessario contenuto carico o una chiamata al servizio web. Anche possibile mostrare il tempo necessario all'utente di compilare un modulo, creare un account o acquisto. Un processo rappresenta la durata di un'attività, ad esempio, la durata di un'attività di download o l'ora in un'intestazione viene visualizzata sullo schermo. (Il significato dei processi dipende come SDK è stata integrata nell'applicazione). Processi sono in genere associati alle attività in background che vengono eseguite all'esterno dell'ambito di una sessione (ad esempio, senza alcuna attività utente).
- Technicals: Le informazioni tecniche su dispositivi degli utenti dell'app che è possibile tenere traccia, ad esempio impostazioni locali, vettore, rete, dispositivo, Firmware e dello schermo di dimensioni dei dispositivi degli utenti e la versione dell'App e la versione SDK utilizzata nell'app.
- Errori: Informazioni tecniche errori all'interno dell'applicazione che non provocano l'applicazione in modo anomalo. Un messaggio di errore rappresenta un problema immediato, ad esempio, un errore di rete o una modifica non valida. (Il significato degli eventi dipende come SDK è stata integrata nell'applicazione). Un errore può verificarsi durante una sessione o un processo o può essere rappresentato da singolarmente.
- Anomalo: Informazioni sugli errori che determina l'arresto anomalo dell'applicazione. Un arresto anomalo è una condizione imprevista nel punto in cui l'applicazione si arresta eseguono le funzioni previste e deve essere interrotto. Un arresto anomalo è in genere a causa di un errore nell'applicazione.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>Accesso la panoramica di conservazione
![Analytics3][12]

Cenni preliminari sulla conservazione viene suddiviso al centro nelle diverse schede, ognuna con la panoramica per un determinato periodo di conservazione. Nell'esempio viene visualizzato il periodo di conservazione di 2 giorni. Altre schede mostrano i periodi di conservazione 4 giorni e 7 giorni.

## <a name="understanding-the-retention-overview-cards"></a>Informazioni sulle schede Panoramica di conservazione
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Ogni scheda è costituita da 3 parti principali:
1. 1: alla coorte e il periodo considerato
2. 2-4: criteri di conservazione per il periodo corrente
3. 5: un grafico Sparkline della cronologia

### <a name="here-is-detailed-information-about-each-element"></a>Ecco informazioni dettagliate su ogni elemento:
1.    Coorte e periodo: questo titolo offre il tipo di coorte. Di seguito "2 giorni" significa che esamineremo il comportamento di utenti più di 2 giorni, gli utenti che si trova in un periodo di 2 giorni e se si connettono in seguenti blocchi di 2 giorni. L'esempio precedente vengono tenuti in considerazione l'attività di utenti tra i 21 e 22 di novembre.
2.    In questo modo il tasso di conservazione sopra i 21 e 22 di novembre per gli utenti che arrivano 19 e 20 novembre. Di seguito è stato 1 l'utente attivo tra i 21 e 22, su 3 che sono state nuovi utenti tra 19 e 20.
3.    Questo indicatore visivo offre le stesse informazioni come illustrato in precedenza rappresentare graficamente. (La terza del cerchio viene generato dal numero 33%). Il colore vengono fornite informazioni aggiuntive: verde indica il numero è aumentando dal calcolo precedente. Giallo significa stabile e rossa indica decrescente.
4.    Indica i valori utilizzati per il calcolo.
5.    Si tratta di un grafico Sparkline della cronologia dei valori di conservazione. Consente di visualizzare i valori in passato avere una visione d'insieme di come derivato.


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
