<properties 
   pageTitle="Interfaccia utente di Azure impegno per dispositivi mobili - portata campagna" 
   description="Laern modalità per creare e gestire le notifiche push campagne marketing tramite coinvolgimento Mobile Azure" 
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


# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Come creare e gestire le campagne di notifica push
È possibile utilizzare la sezione portata dell'interfaccia utente per creare una nuova campagna Push con una formula complessa fornendo tutte le informazioni che necessarie per inviare una notifica push. Le opzioni di una campagna Push variano leggermente a seconda di tipi di quattro della campagna: annunci, sondaggi, inserisce dati e le sezioni (solo per Windows Phone).

### <a name="option-applies-to"></a>Opzione si applica a:
- Lingue: Tutti (annunci, sondaggi, dati inserimenti, riquadri)
- Campagna: Tutti (annunci, sondaggi, dati inserimenti, riquadri)
- Notifica: Annunci, sondaggi
- Content: Univoco per ogni tipo di campagna
- Destinatari: Tutti (annunci, sondaggi, dati inserimenti, riquadri)
- Intervallo di tempo: annunci, sondaggi, riquadri
- Condizione: Tutti (annunci, sondaggi, dati inserimenti, riquadri)
 
![Portata Campaign1][20]

## <a name="languages"></a>Lingue
È possibile utilizzare il menu a discesa lingue per inviare una versione diversa del Push ai dispositivi impostata per l'utilizzo di lingue diverse. Per impostazione predefinita, tutti i dispositivi riceveranno Push stesso indipendentemente dalle quali lingue sono configurate per utilizzare. Gli utenti con il dispositivo impostato su una lingua diversa riceveranno la versione del Push lingua predefinita. Molte delle opzioni della campagna push consentono di specificare il contenuto alternativo per ognuna delle altre lingue selezionate. 
 
![Portata Campaign2][21]

### <a name="language-differences-apply-to"></a>Differenze di linguaggio applicano a:
- Lingue: Univoche lingue possono essere selezionate oltre la lingua predefinita
- Campagna: Uguale per tutte le lingue
- Notifica: Univoco per ogni lingua oltre la lingua predefinita
- Content: Univoco per ogni lingua oltre la lingua predefinita
- Destinatari: Possono essere filtrati da un criterio di lingua distinti
- Intervallo di tempo: uguale per tutte le lingue
- Condizione: Possono essere inviati per ogni lingua alla volta
 
### <a name="supported-languages"></a>Lingue supportate:
- Arabo (ar) 
- Bulgaro (bg) 
- Catalano (ca) 
- Cinese (zh) 
- Croato (h) 
- Czech (cs) 
- Danese (da) 
- Olandese (nl) 
- Inglese (en) 
- Finlandese (fi) 
- Francese (fr) 
- Tedesco (de) 
- Ad esempio greco (el) 
- Ebraico (barra) 
- Hindi (Ciao) 
- Ungherese (hu) 
- Indonesiano (id) 
- Italiano (it) 
- Lingua giapponese 
- Coreano (ko) 
- Lettone (lv) 
- Lituano (lt) 
- Malese (macrolanguage) (ms) 
- Norvegese Bokmål (notebook) 
- Polacco (pl) 
- Portoghese (pt) 
- Romeno (sola lettura) 
- Russo (ru) 
- Serbo (sr) 
- Slovacco (Chiedi) 
- Sloveno (sl) 
- Spagnolo (es) 
- Svedese (sv) 
- Carattere tagalog: (tl) 
- Thai (g) 
- Turco (tr) 
- Ucraino (Regno Unito) 
- Vietnamita (vi) 
 
## <a name="campaign"></a>Campagna
È possibile utilizzare la sezione campagna per impostare il nome e la categoria della campagna anche come se si prevede di ignorare la sezione gruppo di destinatari di una campagna Push e invece di inviare la campagna tramite l'API raggiunga (e alcuni elementi con il livello di difficoltà Push API). Categorie possono essere utilizzate con un modello di notifica personalizzato per le notifiche nell'app di controllo in base alle impostazioni predefinite. È possibile ottenere un elenco le "categorie" esistenti tramite l'API raggiungere.

> Avviso: Se si usa l'opzione "Gruppo di destinatari Ignora push verrà inviato agli utenti tramite l'API" nella sezione "Campagna" di una campagna di raggiungere la campagna non invierà automaticamente, sarà necessario inviare manualmente tramite l'API raggiungere.
 
![Portata Campaign3][22]
 
### <a name="option-applies-to"></a>Opzione si applica a:
- Nome: tutti
- Categoria: Annunci, sondaggi
- Ignora gruppo di destinatari, push verrà inviato agli utenti tramite l'API: tutti
 
## <a name="notification"></a>Notifica
È possibile utilizzare la sezione di notifica per configurare le impostazioni di base per l'inclusione di push: titolo del Push, il messaggio, un'immagine all'interno dell'applicazione, o se è dismissible. Molte impostazioni di notifica sono specifiche per la piattaforma del dispositivo. È possibile scegliere se verrà inviato il comando "in app" o "Indietro app" o entrambi. (Tenere presente che gli utenti possono "acconsentire aggiuntivo" o "al rifiuto esplicito" di "fuori app" inserisce al sistema operativo livello nei propri dispositivi e Azure Mobile coinvolgimento non sarà possibile ignorare questa impostazione. Ricordare che l'API raggiunga gestisce "in app" e "fuori app" inserisce. API Push possono essere utilizzata per gestire "dall'app" push troppo.) Inserisce possono essere personalizzati con immagini o contenuto HTML, con collegamenti completa per il collegamento all'esterno di un'applicazione o in un'altra posizione nell'App (SDK Android 2.1.0 o versioni successive categorie intento obbligatorie). È possibile modificare il badge icona o iOS e inviare il contenuto di testo o web (un visualizzazione popup con collegamento URL contenuto html in un'altra posizione all'interno o all'esterno dell'app). È possibile verificare lo squillo dispositivi Android o vibrazione con il comando. (Ricordare che occorre corretto file per squillare o vibrazione un dispositivo manifesto autorizzazioni SDK in un telefono Android.) Non è attualmente non settore standard di dimensioni Android "ampio", perché dimensioni dello schermo sono diverse su qualsiasi dispositivo, mentre le immagini di 100 x 400 lavorare su quasi tutte le dimensioni massime dello schermo.

### <a name="delivery-types"></a>Tipi di recapito:
-    Disconnettersi da app solo: la notifica verrà recapitata quando l'utente non usare l'applicazione.
- Il timeout della notifica solo app richiede un certificato di Apple o Google (certificato APN o GCM).
- In-app solo: la notifica viene visualizzata solo quando l'applicazione è in esecuzione.
- La notifica utilizza il sistema di recapito Capptain di raggiungere l'utente. È possibile personalizzare completamente il layout o visualizzazione del push.
- In qualsiasi momento: Questa opzione garantisce invia una notifica che sia l'applicazione è in esecuzione o meno.

 
![Portata Campaign4][23]

### <a name="option-applies-to"></a>Opzione si applica a:
- Notifica: Annunci, sondaggi
 
## <a name="content"></a>Contenuto
È possibile utilizzare la sezione contenuta per modificare il contenuto di annunci, sondaggi, inserisce dati e riquadri (solo per Windows Phone). L'impostazione del contenuto delle campagne Push è specifico per il tipo della campagna. 

### <a name="see-also"></a>Vedere anche
- [Documentazione di interfaccia utente - raggiungere - Push contenuto][Link 29]
 
![Portata Campaign5][24]

## <a name="audience"></a>Gruppo di destinatari
È possibile utilizzare la sezione gruppo di destinatari per definire un elenco di voci per limitare la campagna o limiti della campagna in base a criteri personalizzati standard. Il set di opzioni per limitare i destinatari standard consente di push a utenti nuovi o precedenti o solo per gli utenti push nativa. È inoltre possibile impostare quote per limitare il numero di utenti a cui ricevere il push. È possibile modificare manualmente l'espressione per come la campagna viene filtrata in modo da includere uno o più dei criteri per gli utenti di destinazione. È possibile digitare manualmente un'espressione di gruppo di destinatari. Ad esempio un'espressione in modo esplicito definire la relazione tra i criteri. Un criterio è descritta da un identificatore che deve iniziare con una lettera maiuscola e non può contenere spazi. La relazione tra i criteri può essere descritti con 'and', 'o' "not" operatori, nonché '(',')'. Esempio: "Criterion1 o (Criterion1 e non Criterion2)".

> Nota: Con il pubblico incluso nelle campagne, sul lato server l'assegnazione del analisi può essere lento, soprattutto se si tenta di avviare campagne più nello stesso momento.

- Se possibile, avviare solo una campagna alla volta.
- Il massimo, avviare solo quattro campagne alla volta.
- Push solo agli utenti attivi (casella di controllo "esercitare solo gli utenti che possono essere raggiunto utilizzando Push nativo" e "Esercitare solo utenti attivi") in modo che solo gli utenti che ancora con l'app e usarlo saranno necessario eseguire la scansione.
Dopo aver definito i destinatari, è possibile utilizzare il pulsante simulare per sapere quanti utenti riceveranno questo Push. Calcola il numero di utenti noti potenzialmente destinato al gruppo di destinatari (si tratta di una stima in base a un campione di utenti). Tenere presente che fanno parte del gruppo di destinatari anche gli utenti che hanno dopo la disinstallazione dell'applicazione, ma non è raggiungibile.

### <a name="see-also"></a>Vedere anche
- [Interfaccia utente documentazione - portata - nuovo Push criterio][Link 28]

![Portata Campaign6][25]

### <a name="edit-expression"></a>Modifica espressione
![Portata Campaign7][26]
 
### <a name="limit-your-audience-option-applies-to"></a>Limite: che dell' opzione pubblico si applica:
- Partecipazione a solo un sottoinsieme di utenti: tutti (annunci, sondaggi, inserisce dati, le sezioni)
- Partecipazione a solo gli utenti precedenti: tutti (annunci, sondaggi, inserisce dati, le sezioni)
- Partecipare solo i nuovi utenti: tutti (annunci, sondaggi, inserisce dati, le sezioni)
- Partecipazione a solo gli utenti inattivi: annunci, sondaggi, riquadri
- Partecipazione a solo utenti attivi: tutti (annunci, sondaggi, inserisce dati, le sezioni)
- Partecipazione a solo gli utenti che possono essere raggiunto utilizzando Push nativi: annunci e sondaggi
 
## <a name="time-frame"></a>Intervallo di tempo
È possibile utilizzare la sezione intervallo di tempo per impostare quando verrà inviato il push o è possibile omettere l'intervallo di tempo per avviare immediatamente la campagna. Tenere presente che fuso orario degli utenti finali può iniziare la campagna anteriori previsti per gli utenti finali nell'area Asia e inviare piccoli batch di push contemporaneamente fino a quando non corrispondono a tutti i fusi orari nel mondo l'intervallo di tempo impostato per la campagna di un giorno. Fuso orario degli utenti finali può causare ritardi nelle campagne dal momento che è necessario richiedere l'ora dal telefono prima di iniziare il push.

> Nota: Campagne di marketing senza una data di fine possibile memorizzare nella cache inserisce in locale e comunque visualizzarli dopo aver campagne manualmente complete. Per evitare questo comportamento specifici di un'ora di fine per le campagne.

### <a name="see-also"></a>Vedere anche
- [Raggiungere - come visive – pianificazione][Link 3] 
 
![Portata Campaign8][27]

### <a name="settings-apply-to"></a>Impostazioni si applicano a:
- Intervallo di tempo: annunci, sondaggi, riquadri
 
## <a name="test"></a>Test
È possibile utilizzare la sezione Test per l'invio di questo push al dispositivo test prima di salvare la campagna. Se è stato configurato tutte le lingue personalizzate per la campagna, è possibile testare il push in ogni lingua. È ora possibile configurare un dispositivo di test da "Account personale".
> Nota: Nessun lato server dati sono stato effettuato l'accesso quando si utilizza il pulsante "verificare" inserisce, solo i dati vengono registrati per le campagne push reali.

### <a name="see-also"></a>Vedere anche
- [Documentazione di interfaccia utente - Account personale][Link 14]
 
![Portata Campaign9][28]

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
 
