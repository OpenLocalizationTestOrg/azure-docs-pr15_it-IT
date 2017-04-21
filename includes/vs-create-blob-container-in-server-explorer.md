È possibile creare lo spazio di archiviazione Azure code utilizzando Visual Studio **Esplora Server**.

![Server Explorer BLOB][Image1]

1. Nel menu **Visualizza** scegliere **Esplora Server**.
2. In Esplora Server espandere il nodo **Azure** per l'abbonamento, espandere il nodo **dello spazio di archiviazione** e per l'account di archiviazione specificato nel servizio di archiviazione di Azure connessi.
3. Selezionare il nodo **code** e scegliere **Crea coda** dal menu di scelta rapida.
4. Immettere un nome per il contenitore e scegliere **OK**.   

Per impostazione predefinita, il nuovo contenitore è privato e specificare il tasto di scelta di spazio di archiviazione per scaricare BLOB da questo contenitore. Se si desidera rendere pubblico i file nel contenitore, selezionare il contenitore in **Esplora** risorse e premere `F4` per visualizzare la finestra **proprietà** . Impostare l' **accesso in lettura pubblico** su **Blob**. Tutti gli utenti su Internet può vedere BLOB in un contenitore di pubblico, ma è possibile modificare o eliminare solo se si dispone della chiave di accesso appropriato.


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png