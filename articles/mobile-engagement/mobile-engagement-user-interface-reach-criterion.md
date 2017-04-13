<properties 
   pageTitle="Interfaccia utente di Azure impegno per dispositivi mobili - portata criterio" 
   description="Informazioni su come usare i criteri di assegnazione per inviare campagne push a un sottoinsieme seleziona gli utenti con coinvolgimento di Azure Mobile" 
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


# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Come usare i criteri di assegnazione per inviare campagne push a un sottoinsieme seleziona gli utenti

Assegnazione il gruppo di destinatari in base a criteri specifici con il pulsante "Nuovi criteri" è uno dei concetti più potenti di Azure Mobile coinvolgimento che consente di inviati pertinenti notifiche push che i clienti risponderà alle anziché Spamming tutti gli utenti. È possibile limitare il gruppo di destinatari in base a criteri standard e consente di simulare push per determinare il numero di persone riceverà la notifica.

**Vedere anche:**

- [Interfaccia utente documentazione - portata - Push campagne][Link 27]

## <a name="audience-criteria-can-include"></a>Criteri di gruppo di destinatari possono includere:
- **Technicals:** È possibile assegnare in base alle informazioni tecniche stesso è possibile vedere nelle sezioni Analitica e Monitor. **Vedere anche:** [Documentazione di interfaccia utente - Analitica] [ Link 15], [Documentazione dell'interfaccia utente - Monitor][Link 16]
- **Posizione:** Applicazioni che utilizzano "in tempo reale posizione reporting" con geografico recinzione consente ubicazione geografico come criterio di un gruppo di destinatari dalla posizione GPS di destinazione. "Prato Area posizione report" chiamata essere utilizzate anche per assegnare un gruppo di destinatari dalla posizione del cellulare ("in tempo reale posizione reporting" e "Prato Area posizione" deve essere attivata dal SDK). **Vedere anche:** [Documentazione SDK - iOS - integrazione] [ Link 5], [Integrazione - Android - documentazione SDK][Link 5]
- **Raggiungere commenti e suggerimenti:** È possibile assegnare ai partecipanti in base a eventuali suggerimenti relativi da notifiche portata precedenti in base al feedback portata da annunci, sondaggi e inserisce dati. In questo modo è alla destinazione di migliore al pubblico non solo dopo due o tre raggiunga campagne di quanto la prima volta. Può inoltre essere utilizzato per filtrare gli utenti che hanno già ricevuto una notifica con contenuti simili, impostando una campagna non verranno inviati agli utenti che hanno già ricevuto una specifica campagna precedente. È anche possibile escludere gli utenti che sono inclusi una campagna specifica è ancora in corso di ricevere nuovi inserimenti. **Vedere anche:** [Documentazione di interfaccia utente - raggiungere - invio del contenuto][Link 29]
- **Installare verifica:** È possibile tenere traccia delle informazioni in base a in cui gli utenti installato l'App. **Vedere anche:** [Documentazione di interfaccia utente - impostazioni][Link 20]
- **Profilo utente:** È possibile assegnare standard in base alle informazioni utente ed è possibile destinazione in base alle informazioni di app personalizzato creato. Sono inclusi gli utenti che stanno attualmente e gli utenti che hanno risposto a domande specifiche si è scelto di impostare in app anziché semplicemente come hanno risposto a campagne precedente. Tutte le informazioni di App definite per l'app vengono visualizzati in questo elenco.
- Segmenti: È anche possibile destinazione basato sui segmenti che è stato creato in base utente specifico comportamento contenente più criteri. Tutti i segmenti definiti per l'app vengono visualizzate in questo elenco. **Vedere anche:** [Documentazione di interfaccia utente - segmenti][Link 18]
- **Informazioni app:** Tag Info App personalizzati possono essere create da "Impostazioni" per tenere traccia di comportamento degli utenti. **Vedere anche:** [Documentazione di interfaccia utente - impostazioni][Link 20]

## <a name="example"></a>Esempio: 
Se si desidera inviare un annuncio solo ai sottoinsieme degli utenti che hanno eseguito un'azione di acquisto in app.

1. Passare alla pagina di impostazioni dell'applicazione, selezionare menu "Informazioni App" e "Nuove informazioni app"
2. Registrare una nuova informazioni app booleana denominata "inAppPurchase"
3. Rendere l'applicazione di impostare queste informazioni app su "true" quando l'utente esegue correttamente un acquisto in app (tramite sendAppInfo ("inAppPurchase",...) funzione)
4. Se non si desidera eseguire questa operazione dall'applicazione in uso, è possibile farlo dal back-end tramite il dispositivo API)
5. Quindi, è necessario solo creare l'annuncio con un criterio di limitazione al pubblico per gli utenti che dispongono di "inAppPurchase" impostata su "true")
 
> Nota: La destinazione in base a criteri diversi da tag info app richiede Azure Mobile impegno raccogliere informazioni da dispositivi degli utenti prima che venga inviato il push e in modo che possono causare un ritardo. Configurazione di push complesse opzioni (ad esempio aggiornamento badge) possono anche ritardare inserisce. Utilizzando una campagna "una ripresa" dall'API Push è il metodo push più veloce assoluto in Azure Mobile coinvolgimento. Uso di app solo i tag di informazioni come criteri di push per una campagna di mano (operazione dal API raggiunga o l'interfaccia utente) il metodo più veloce successivo dall'app info tag sono memorizzati sul lato server. Usare altri criteri di assegnazione di una campagna push il metodo push più flessibile ma più lento poiché Azure Mobile coinvolgimento previsti per i dispositivi di query per inviare la campagna.
 
![Portata Criterion1][29] 

## <a name="criterion-options-apply-to"></a>Opzioni di criterio applicano a:
- **Technicals**     
- Nome firmware: nome Firmware
- Versione del firmware: versione del Firmware
- Modello di dispositivo: modello di dispositivo
- Produttore del dispositivo: produttore del dispositivo
- Versione dell'applicazione: versione dell'applicazione
- Nome vettore: nome vettore, non definito
- Paese vettore: paese vettore, non definito
- Tipo di rete: tipo di rete
- Impostazioni locali: impostazioni locali
- Dimensioni dello schermo: dimensioni dello schermo
- **Posizione**      
- Ultima nota area: Country, Region, località
- In tempo reale geografico-recinzione: elenco di POIs (nome, azioni), POI circolare (nome, latitudine, longitudine, Radius in metri)
- **Raggiungere commenti e suggerimenti**     
- Commenti e suggerimenti annuncio: annuncio, commenti e suggerimenti
- Sondaggio commenti e suggerimenti: sondaggio, commenti e suggerimenti
- Sondaggio answer feedback: sondaggio commenti e suggerimenti risposta, domanda e la scelta
- Commenti e suggerimenti Push di dati: Push dei dati, commenti e suggerimenti
- **Installare rilevamento modifiche**     
- Archivio: Archivio non definito
- Origine: Origine non definito
- **Profilo utente**     
- Sesso: persone di sesso maschile o femminile non definito
- La data di nascita: operatore, data, non definito
- Consenso esplicito: VERO o FALSO e non definito
- **Informazioni App**      
- Stringa: Stringa non definito
- Data: operatore, data, non definito
- Numero intero: operatore, non definito
- Valore booleano: VERO o FALSO, non definito
- **Segmento**    
- Nome dei segmenti (dall'elenco a discesa), esclusione (destinazione gli utenti che non fanno parte di questo segmento).

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
