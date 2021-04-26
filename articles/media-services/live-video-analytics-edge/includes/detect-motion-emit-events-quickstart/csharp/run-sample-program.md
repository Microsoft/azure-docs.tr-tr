---
ms.openlocfilehash: 766dd13f58268c044435a22fb30c1de816d4d151
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97531961"
---
Örnek kodu çalıştırmak için aşağıdaki adımları izleyin:

1. Visual Studio Code, **Uzantılar** sekmesini açın (veya CTRL + SHIFT + X tuşlarına basın) ve Azure IoT Hub aratın.
1. Sağ tıklayıp **uzantı ayarları**' nı seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Uzantı ayarları":::
1. "Ayrıntılı Iletiyi göster" i arayın ve etkinleştirin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Ayrıntılı Iletiyi göster":::
1. Visual Studio Code ' de, *src/buluttan cihaza-Console-App/operations.json*' a gidin.
1. **Graphtopologyset** düğümünde aşağıdaki değeri görtığınızdan emin olun:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/2.0/topology.json"`
1. **Graphınstanceset** ve **Graphtopologydelete** düğümlerinde, değerinin `topologyName` `name` grafik topolojisinde özelliğin değeriyle eşleştiğinden emin olun:

    `"topologyName" : "MotionDetection"`
    
1. F5 tuşunu seçerek bir hata ayıklama oturumu başlatın. **TERMINAL** penceresinde bazı iletiler görüntülenir.
1. Dosyasındaki *operations.js* , ve çağrıları ile başlatılır `GraphTopologyList` `GraphInstanceList` . Önceki hızlı başlangıçlarını bitirdikten sonra kaynakları temizledikten sonra bu işlem boş listeleri döndürür ve ardından duraklatılır. Devam etmek için Enter tuşunu seçin.

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
        "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
        "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    **TERMINAL** penceresinde, bir sonraki doğrudan yöntem çağrısı kümesi gösterilir:
     * Daha önce kullanılan bir çağrısı `GraphTopologySet``topologyUrl`
     * Aşağıdaki gövdesini kullanan öğesine yapılan bir çağrı `GraphInstanceSet` :
         
    ```
    {
      "@apiVersion": "2.0",
      "name": "Sample-Graph",
      "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample graph description",
        "parameters": [
          {
            "name": "rtspUrl",
            "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
          },
          {
            "name": "rtspUserName",
            "value": "testuser"
          },
          {
            "name": "rtspPassword",
            "value": "testpassword"
          }
        ]
      }
    }
    ```
     
    * `GraphInstanceActivate`Grafik örneğini ve videonun akışını başlatan öğesine yönelik bir çağrı.
    * `GraphInstanceList`Grafik örneğinin çalışır durumda olduğunu gösteren ikinci bir çağrı.
1. **TERMINAL** penceresindeki çıktı, tarihinde duraklatılır `Press Enter to continue` . Henüz ENTER ' ı seçmeyin. Doğrudan çağrdığınız yöntemler için JSON yanıtı yüklerini görmek üzere yukarı kaydırın.
1. Visual Studio Code **Çıkış** penceresine geçin. IoT Edge modülündeki canlı video analizinin IoT Hub 'ına gönderdiğini iletiler görürsünüz. Bu hızlı başlangıçta aşağıdaki bölümde bu iletiler ele alınmaktadır.
1. Medya grafiği çalışmaya devam eder ve sonuçları yazdırır. RTSP simülatörü kaynak videoyu döngüye sokmaya devam eder. Medya grafiğini durdurmak için, **TERMINAL** penceresine dönün ve ENTER ' u seçin. 

    Sonraki çağrı dizisi kaynakları temizler:

    * `GraphInstanceDeactivate`Grafik örneğini devre dışı bırakmak için bir çağrı.
    * `GraphInstanceDelete`Örneği silme çağrısı.
    * İçin bir çağrı `GraphTopologyDelete` , topolojiyi siler.
    * İçin nihai bir çağrı `GraphTopologyList` , listenin boş olduğunu gösterir.
