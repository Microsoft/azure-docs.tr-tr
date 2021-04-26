---
ms.openlocfilehash: c99d2489efe7c46b8d50b08861fcbbcd6f8a1966
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97532111"
---
1. Visual Studio Code, **Uzantılar** sekmesini açın (veya CTRL + SHIFT + X tuşlarına basın) ve Azure IoT Hub aratın.
1. Sağ tıklayıp **uzantı ayarları**' nı seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Uzantı ayarları":::
1. "Ayrıntılı Iletiyi göster" i arayın ve etkinleştirin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Ayrıntılı Iletiyi göster":::
1. F5 tuşunu seçerek bir hata ayıklama oturumu başlatın. **TERMINAL** penceresinde bazı iletiler yazdırılır.
1. Koddaki *operations.js* doğrudan yöntemleri `GraphTopologyList` ve ' i çağırır `GraphInstanceList` . Önceki hızlı başlangıçlardan sonra kaynakları temizledikten sonra bu işlem boş listeleri döndürür ve sonra duraklatılır. Enter tuşuna basın.
    
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
  
  * Öğesine yapılan bir çağrı `GraphTopologySet``topologyUrl` 
  * Aşağıdaki gövdesini kullanan öğesine yapılan bir çağrı `GraphInstanceSet` :
  
  ```
  {
    "@apiVersion": "2.0",
    "name": "Sample-Graph",
    "properties": {
      "topologyName": "EVRToFilesOnMotionDetection",
      "description": "Sample graph description",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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
1. **TERMINAL** penceresindeki çıktı, tarihinde duraklatılır `Press Enter to continue` . Henüz ENTER ' ı seçmeyin. Çağrdığınız doğrudan yöntemler için JSON yanıtı yüklerini görmek için yukarı kaydırın.
1. Visual Studio Code **Çıkış** penceresine geçin. IoT Edge modülünün canlı video analizinin IoT Hub 'ına gönderdiği iletileri görürsünüz. Bu hızlı başlangıçta aşağıdaki bölümde bu iletiler ele alınmaktadır.
1. Medya grafiği çalışmaya devam eder ve sonuçları yazdırır. RTSP simülatörü kaynak videoyu döngüye sokmaya devam eder. Medya grafiğini durdurmak için, **TERMINAL** penceresine dönün ve ENTER ' u seçin. 

    Sonraki çağrı dizisi kaynakları temizler:

    * `GraphInstanceDeactivate`Grafik örneğini devre dışı bırakmak için bir çağrı.
    * `GraphInstanceDelete`Örneği silme çağrısı.
    * İçin bir çağrı `GraphTopologyDelete` , topolojiyi siler.
    * İçin son çağrı `GraphTopologyList` , listenin artık boş olduğunu gösterir.
