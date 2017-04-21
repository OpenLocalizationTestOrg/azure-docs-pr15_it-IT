<properties
   pageTitle="Titolo della pagina che viene visualizzato nei risultati di ricerca e scheda del browser"
   description="Descrizione articolo che verrà visualizzati nelle pagine di destinazione e nella maggior parte dei risultati della ricerca"
   services="service-name"
   documentationCenter="dev-center-name"
   authors="GitHub-alias-of-only-one-author"
   manager="manager-alias"
   editor=""/>

<tags
   ms.service="required"
   ms.devlang="may be required"
   ms.topic="article"
   ms.tgt_pltfrm="may be required"
   ms.workload="required"
   ms.date="mm/dd/yyyy"
   ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

# <a name="markdown-template-article-heading-1-or-h1-heading-at-the-top-of-the-article"></a>Modello delle vendite promozionali (articolo titolo 1 o H1): titolo nella parte superiore dell'articolo

Per copiare le vendite promozionali da questo modello, copiare l'articolo il repo locale o fare clic sul pulsante elaborato UI GitHub e copiare le vendite promozionali.

  ![Testo alternativo. non lasciare vuoto. Descrivere l'immagine.][8]

Paragrafo introduttivo: Lorem dolor amet, adipiscing elit. Phasellus interdum nulla risus, lacinia Portal nisl imperdiet file SED. Mauris dolor mauris, tempus sed lacinia nec, felis non euismod. Nunc semper Portal ultrices. Maecenas neque nulla, condimentum vitae ipsum occupare amet, aliquet dignissim nisi.

## <a name="heading-2-h2"></a>Titolo 2 (H2)

Aenean occupare amet leo nec purus placerat fermentum ac gravida odio. Aenean tellus lectus, faucibus in rhoncus in faucibus sed urna.  volutpat mi id purus ultrices iaculis nec non neque. [Testo del collegamento per collegamento esterno azure.microsoft.com](http://weblogs.asp.net/scottgu). Dolor dictum Nullam in aliquam pharetra. Vivamus ac hendrerit mauris [testo del collegamento di esempio per collegamento a un articolo in una cartella di servizio](../articles/expressroute/expressroute-bandwidth-upgrade.md).

Viene visualizzato il traffico più di 10 volte da [Google]  [ gog] rispetto da [Yahoo]  [ yah] o [MSN] [msn].

> [AZURE.NOTE] Testo rientrato nota.  La parola 'nota' verrà aggiunti durante la pubblicazione. Ut dell'Unione europea pretium lacus. Nullam purus est, selezionare il livello est sed iaculis, euismod vehicula odio. Curabitur lacinia, erat tristique iaculis rutrum, erat sem sodales nisi, dell'Unione europea condimentum turpis nisi un purus.

1. Aenean occupare amet leo nec **Purus** placerat fermentum ac gravida odio.

2. Aenean tellus lectus, faucius in **Rhoncus** in faucibus sed urna. Suspendisse volutpat mi id purus ultrices iaculis nec non neque.

    ![Testo alternativo. non lasciare vuoto. Auto Raccoglitore in corsa rosso.][5]

3. Dolor dictum Nullam in aliquam pharetra. Vivamus ac hendrerit mauris. SED dolor dui, condimentum e varius al vehicula in nisl.

    ![Testo alternativo. non lasciare vuoto il campo][6]


Suspendisse volutpat mi id purus ultrices iaculis nec non neque. Dolor dictum Nullam in aliquam pharetra. Vivamus ac hendrerit mauris. Otrus informatus: [1 collegamento a un altro argomento documentazione azure.microsoft.com](virtual-machines-windows-hero-tutorial.md)

## <a name="heading-h2"></a>Titolo (H2)

Ut dell'Unione europea pretium lacus. Nullam purus est, selezionare il livello est sed iaculis, euismod vehicula odio.

1. Curabitur lacinia, erat tristique iaculis rutrum, erat sem sodales nisi, dell'Unione europea condimentum turpis nisi un purus.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2. Vestibulum ante ipsum primis in faucibus orci luctus e ultrices posuere cubilia.

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }


    > [AZURE.NOTE] Duis sed diam non <i>molestie nisl</i> pharetra eget un est. [Collegamento 2 a un altro argomento documentazione azure.microsoft.com](web-sites-custom-domain-name.md)


Quisque commodo eros selezionare il livello lectus euismod auctor eget occupare amet leo. Proin faucibus suscipit tellus dignissim ultrices.

## <a name="heading-2-h2"></a>Titolo 2 (H2)

1. Condimentum sed Maecenas nisi. Potenti Suspendisse.

  + Fusce
  + Malesuada
  + SEM

2. Nullam in massa dell'Unione europea tellus tempus hendrerit.

    ![Testo alternativo. non lasciare vuoto. Esempio di un video 9 di canale.][7]

3. Quisque felis enim, fermentum ut aliquam nec, pellentesque pulvinar magna.




<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

Vestibul ante ipsum primis in faucibus orci luctus e ultrices posuere cubilia Curae; Nullam ultricies, ipsum vitae volutpat hendrerit, eros pretium di purus diam, vitae tincidunt nulla lorem sed turpis: [3 collegamento a un altro argomento documentazione azure.microsoft.com](storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    
