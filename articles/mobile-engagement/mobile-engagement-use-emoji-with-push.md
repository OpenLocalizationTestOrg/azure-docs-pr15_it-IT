<properties 
    pageTitle="Usare le emoticon Emoji all'interno di Azure Mobile coinvolgimento" 
    description="Come usare le emoticon Emoji entro le notifiche push"     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="use-emoji-emoticon-within-push-notifications"></a>Utilizzare emoticon Emoji all'interno delle notifiche Push

È possibile includere Emoji emoticon nei è push notification in pochi semplici passaggi: 

1. Prima di tutto che è necessario trovare la Emoji si desidera inviare il messaggio. Assicurarsi che Emoji si selezionano sarà supportato dal dispositivo di destinazione come dispositivo produttori richiedere alcuni minuti per aggiungere Emojis appena approvati per le piattaforme di dispositivi. 

2. In **Windows** , è possibile passare a questo [collegamento](http://apps.timwhitlock.info/emoji/tables/unicode) e copiare l'icona "Native".

    ![][7] 

3. Nel **Mac** , è possibile trovare che emojis nell'applicazione di dizionario sotto Modifica -> Emoji e simboli.

    ![][6] 

4. A questo punto, passare alla scheda **raggiungere** il portale di Azure Mobile coinvolgimento. Selezionare il tipo della notifica push (annuncio, polling e così via). In questo esempio si sceglie un push annuncio.

5. Specificare i campi diversi della notifica fino a raggiungere il testo del messaggio di notifica. Verrà visualizzata nel punto in cui aggiungere l'Emoji Emoticon. È possibile scegliere di inserire il titolo, il messaggio o entrambe. È necessario trascinare o copiare Emoji che si trova in posizioni sopra. 

    ![][1]

6. Completare gli altri campi per la notifica e salvarlo. 

7. Quando si esegue un test o si attiva l'annuncio verrà visualizzata una notifica con l'emoticon come specificato.   

    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

