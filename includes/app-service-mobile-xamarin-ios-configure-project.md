####<a name="configuring-the-ios-project-in-xamarin-studio"></a>Configurazione del progetto iOS in Xamarin Studio

1. In Xamarin.Studio, aprire **Info.plist**e aggiornare l' **Identificatore di aggregazione** con l'ID di gruppo creato in precedenza con il nuovo ID di App.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)

2. Scorrere fino a **Modalità di sfondo** e selezionare la casella **Abilita modalità di sfondo** e la casella di **notifiche Remote** . 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. Fare doppio clic sul progetto nel Pannello di soluzioni per aprire **Opzioni di Project**.

4.  Scegliere **iOS Bundle accesso** in **Crea**e selezionare il **profilo di Provisioning** appena configurato per il progetto e i corrispondenti **identità** . 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    In questo modo che verrà utilizzato il nuovo profilo per la firma del codice. Per il dispositivo Xamarin ufficiale documentazione di provisioning, vedere [Provisioning dispositivo Xamarin].

####<a name="configuring-the-ios-project-in-visual-studio"></a>Configurare il progetto di iOS in Visual Studio

1. In Visual Studio, fare clic sul progetto e quindi fare clic su **proprietà**.

2. Nelle pagine delle proprietà fare clic sulla scheda **iOS applicazione** e aggiornare l' **identificatore** con l'ID creata in precedenza.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

3. Nella scheda **iOS Bundle firma** , selezionare il **profilo di Provisioning** appena configurato per il progetto e i corrispondenti **identità** . 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    In questo modo che verrà utilizzato il nuovo profilo per la firma del codice. Per il dispositivo Xamarin ufficiale documentazione di provisioning, vedere [Xamarin Provisioning di dispositivo].

4. Fare doppio clic su Info.plist per aprirla e quindi attivare **RemoteNotifications** in modalità di sfondo. 



[Dispositivo Xamarin il Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/