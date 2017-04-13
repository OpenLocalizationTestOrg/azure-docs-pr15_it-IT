<properties
    pageTitle="Rullino verità un'esercitazione ballo"
    description="Procedura per creare il rullo di verità classico gioco ballo ossia prerequisito per tutte le esercitazioni verità coinvolgimento Mobile"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a id="unity-roll-a-ball"></a>Creare le trasmissioni verità un gioco ballo

In questa esercitazione sono illustrati i passaggi principali per un [rullino verità un'esercitazione ballo](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial)leggermente cambiata. Gioco in questo esempio è costituito da un oggetto sferico 'player' controllata dall'utente app e l'obiettivo del gioco è per raccogliere oggetti ritirabili da conflitto l'oggetto lettore con questi oggetti ritirabili. Si presuppone base familiarità con ambiente editor verità. Se si verificano problemi deve fare riferimento all'esercitazione completo. 

### <a name="setting-up-the-game"></a>La configurazione di gioco
La procedura seguente provengono da [esercitazione verità](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Aprire **l'Editor di unità** e fare clic su **Nuovo**. 
    
    ![][51] 
    
2. Specificare un **nome del progetto** & **percorso**, selezionare **3D** e fare clic su **Crea progetto**.
    
    ![][52]

3. Salvare la scena predefinito appena creata come parte del nuovo progetto come con il nome **MiniGame** all'interno di un nuovo ** \_scene** cartella nella cartella **attività** :
    
    ![][53]

4. Creare un **oggetto 3D -> piano** come il campo di gioco e rinominare l'oggetto piano come **terra**

    ![][1]

5. Reimpostare il componente di trasformazione per l'oggetto **terra** in modo che sia in corrispondenza dell'origine. 

    ![][3]

6. Deselezionare **Mostra griglia** dal **menu Gizmos** per l'oggetto **terra** .

    ![][4]

7. Aggiornare il componente di **scala** per l'oggetto **terra** [X = 2, Y = 1, Z = 2]. 

    ![][5]

8. Aggiungere un nuovo **oggetto 3D -> sfera** al progetto e rinominare l'oggetto sfera come **Windows Media Player**. 

    ![][6]

9. Selezionare l'oggetto **lettore** e fare clic su **Reimposta trasformazione** simile all'oggetto piano. 

10. Aggiornamento **trasformazione -> posizione -> coordinata Y** componente per Y lettore come 0,5.  

    ![][7]

11. Creare una nuova cartella denominata **materiali** del progetto in cui verrà creato il materiale per applicare il colore del lettore. 

12. Creare un nuovo **materiale** definita **sfondo** in questa cartella. 

    ![][8]

13. Aggiornare il colore del materiale aggiornando la proprietà **Albedo** di esso. È possibile selezionare i valori RGB di [0,32,64]. 

    ![][9]

14. Trascinare il materiale nella visualizzazione della scena per applicare un colore per l'oggetto **terra** . 

    ![][10]

17. Infine aggiornare il **trasformazione -> rotazione -> Y** a 60 sull'oggetto luce direzionale per motivi di chiarezza. 

    ![][12]

### <a name="moving-the-player"></a>Spostare il lettore multimediale
La procedura seguente provengono da [esercitazione verità](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Aggiungere un componente **RigidBody** all'oggetto **Player** . 

    ![][13]

2. Creare una nuova cartella denominata **script** nel progetto. 

3. Fare clic su **componente -> nuovo Script -> c# Script**. Denominare **PlayerController**e fare clic su **Crea e aggiungere**. Verrà creare e collegare uno script all'oggetto Player.  

    ![][14]

5. Spostare lo script nella cartella **script** nel progetto. 

6. Aprire lo script per la modifica nell'editor script preferito, aggiornare il codice di script con il codice seguente e salvarlo. 

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
    
8. Si noti che lo script precedente utilizza una proprietà di **velocità** . Nell'editor di verità, aggiornare la proprietà di velocità su 10.  

    ![][15]

9. Premere **Riproduci** nell'Editor di unità. A questo punto dovrebbe essere possibile controllare ballo utilizzando la tastiera e deve ruotare e spostarsi all'interno. 

### <a name="moving-the-camera"></a>Spostare la videocamera
La procedura seguente da [esercitazione verità](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) e necessario collegare la **Fotocamera principali** all'oggetto **Player** . 

1. Aggiornare **Transform.Position** per x = 0, Y = 10.5, Z =-10.  
2. Aggiornare **Transform.Rotation** per x = 45, Y = 0, Z = 0.  

    ![][16]

2. Aggiungere un nuovo script chiamato **CameraController** a **MainCamera** e spostarlo nella cartella script. 

    ![][17]

3. Aprire lo script per la modifica e aggiungere il codice seguente:

        using UnityEngine;
        using System.Collections;
        
        public class CameraController : MonoBehaviour {
        
            public GameObject player;
        
            private Vector3 offset;
        
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
            
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
    
5. In ambiente verità - trascinare la variabile di Windows Media Player in alloggiamento Player per l'oggetto fotocamera principale in modo che i due sono associati tra loro. 

    ![][18]

6. Se si premere Riproduci nell'editor di verità e ruotare l'oggetto lettore ballo quindi verrà visualizzata la videocamera seguendo il movimento.  

### <a name="setting-up-the-play-area"></a>Configurare l'area di riproduzione
La procedura indicata di seguito è compresi l' [esercitazione verità](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Verrà creato lati di terra in modo che l'oggetto lettore ballo non viene eliminata all'esterno dell'area di riproduzione movimento del. 

1. Fare clic su **Crea -> Crea vuoto -> oggetto gioco** e denominarla **muri**

    ![][19]

2. In questo oggetto muri - creare un nuovo **oggetto 3D -> cubo** e denominarla "Muro Ovest". 

    ![][20]

3. Aggiornare la **trasformazione -> posizione** e **trasformazione -> scala** per l'oggetto da parete ovest. 

    ![][21]

4. Duplicare muro occidentale per creare una **parete orientali** con la posizione di trasformazione aggiornati e le proporzioni. 

    ![][22]

5. Duplicare muro EST per creare una **parete Nord** con la posizione di trasformazione aggiornati e scala. 

    ![][23]

6. Duplicare il muro Nord e creare una **parete sud** con la posizione di trasformazione aggiornati e scala. 

    ![][24]

### <a name="creating-collectible-objects"></a>La creazione di oggetti ritirabile
La procedura indicata di seguito è compresi l' [esercitazione verità](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Sarà necessario creare alcuni oggetti dall'aspetto accattivante che costituiscono il gruppo di oggetti ritirabili che l'oggetto lettore ballo deve raccogliere da conflitto con loro. 

1. Creare un nuovo **oggetto cubo 3D** e denominarla ritiro. 

2. Regolare la **trasformazione -> rotazione** & **trasformazione -> scala** dell'oggetto prelievo. 

    ![][25]

3. Creare e allegare un **Nuovo c# Script** denominato **rotazione** all'oggetto prelievo. Assicurarsi di inserire lo script nella cartella script. 

    ![][26]

4. Aprire lo script per la modifica e aggiornarlo in modo che i seguenti: 

        using UnityEngine;
        using System.Collections;
        
        public class Rotator : MonoBehaviour {
        
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }

5. A questo punto raggiunto la modalità di riproduzione nell'Editor di unità che mostra l'oggetto prelievo essere rotazione sul suo asse.

6. Creare una nuova cartella denominata **Prefabs** 

    ![][27]

7. Trascinare l'oggetto **ritiro** e inserirlo nella cartella Prefabs.

    ![][28]

8. Creare un nuovo **oggetto gioco vuoto** denominato **Pickups**. Reimpostare la posizione di origine e quindi trascinare l'oggetto prelievo in questo oggetto gioco.  

    ![][29]

9. Duplicare l'oggetto **ritiro** e allontanare sull'oggetto **terra** intorno all'oggetto **Player** aggiornando i valori **X e Z del Transform.Position** in modo appropriato. 

    ![][30]

10. Creare un **nuovo materiale** chiamato **ritiro** e aggiornare in modo da essere rosso a colori aggiornando la **proprietà Albedo** simile a quanto avveniva per l'aggiornamento dell'oggetto terra. 

    ![][31]

11. Applicare il materiale per tutti gli oggetti prelievo 4.

    ![][32]

### <a name="collecting-the-pickup-objects"></a>La raccolta degli oggetti prelievo
La procedura indicata di seguito è compresi l' [esercitazione verità](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Microsoft aggiornerà il lettore in modo che sia possibile raccogliere gli oggetti prelievo dal conflitto con loro. 

1. Apertura dello script **PlayerController** collegato all'oggetto Player per la modifica e aggiornamento per le operazioni seguenti:  

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour {
        
            public float speed;
        
            private Rigidbody rb;
        
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
        
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
        
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
        
                rb.AddForce (movement * speed);
            }
        
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }

2. Creare un nuovo **Tag** chiamato **Selezionare alto** (deve corrispondere novità lo script)  

    ![][33]
    
    ![][34]

3. Applicare questo **Tag** all'oggetto ritiro Prefab. 

    ![][35]

4. Casella di controllo di **IsTrigger** per l'oggetto Prefab attiva.

    ![][36]

5. Aggiungere un corpo rigido all'oggetto ritiro Prefab. Per ottimizzare le prestazioni Microsoft aggiornerà collider statico che è stato usato un collider dinamico. 

    ![][37]
  
6. Infine, verificare la proprietà **IsKinematic** per l'oggetto prefab. 

    ![][38]

7. Passaggi **riprodurre** nell'editor verità e si sarà possibile utilizzare questo gioco **implementazione una sfera** spostando l'oggetto lettore usando i tasti di direzione input. 

### <a name="updating-the-game-for-mobile-play"></a>Aggiornamento del gioco per la riproduzione per dispositivi mobili
Sezioni precedenti concluso l'esercitazione base da verità. A questo punto si modificherà il gioco per rendere più dispositivi mobili descrittivo. Si noti che è stato usato input della tastiera per il gioco finora per la verifica. A questo punto è la modificherà in modo che possiamo controllare il lettore tramite vale a dire il movimento del telefono utilizzando accelerometro come input. 

Aprire lo script **PlayerController** per la modifica e aggiornare il metodo **FixedUpdate** per utilizzare l'animazione dall'accelerometro per spostare l'oggetto di Windows Media Player. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Questa esercitazione si conclude base gioco la creazione di un con verità e distribuire seguente in un dispositivo preferito per eseguire il gioco. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png  
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png  
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png

    
    
    
    
    
    
    
    
    
    
    
    
