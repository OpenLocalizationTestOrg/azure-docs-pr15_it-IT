A causa di sviluppo, la versione di Android SDK installata in Studio Android potrebbe non corrisponde alla versione nel codice. SDK Android a cui fa riferimento in questa esercitazione è versione 23, le ultime in fase di scrittura. Come vengono visualizzate nuove versioni di SDK, è consigliabile usare l'ultima versione disponibile, potrebbe aumentare il numero di versione.

Due sintomi di mancata corrispondenza di versione sono:

1. Durante la compilazione o ricompila il progetto, potrebbero essere visualizzati messaggi di errore Gradle simili a "**non è riuscito a trovare la destinazione Google Inc.:Google APIs:n**".

2. In base a oggetti di Android standard nel codice deve risolvere `import` istruzioni possono generare messaggi di errore.

Se viene visualizzato uno di questi elementi, la versione di Android SDK installato in Studio Android potrebbe non corrispondere destinazione SDK del progetto scaricato.  Per verificare la versione, apportare le modifiche seguenti:


1. Android Studio, fare clic su **Strumenti** => **Android** => **SDK Manager**. Se non è installato la versione più recente della piattaforma SDK, quindi fare clic su per installarlo. Prendere nota del numero di versione.

2. Nella scheda progetti, in **Script Gradle**, aprire il file **build.gradle (modeule: app)**. Assicurarsi che la **compileSdkVersion** e **buildToolsVersion** siano impostati per la versione più recente di SDK installata. I contrassegni che potrebbero simile al seguente:
 
            compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
    
3. In Gestione di progetti di Studio Android rapida nodo del progetto, scegliere **proprietà**e nella colonna sinistra scegliere **Android**. Assicurarsi che la **Destinazione Build progetto** è impostata per la stessa versione SDK **targetSdkVersion**.

4. Studio Android, il file manifesto non è più utilizzato per specificare la versione minima di SDK, a differenza di maiuscole/minuscole con Eclipse SDK di destinazione.
