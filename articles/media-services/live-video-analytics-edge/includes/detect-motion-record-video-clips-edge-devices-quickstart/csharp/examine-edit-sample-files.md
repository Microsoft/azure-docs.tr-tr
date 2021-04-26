---
ms.openlocfilehash: 426c735dfd0d015cdc1a734edde9d336fb88cfbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97486763"
---
Bu hızlı başlangıç önkoşulları kapsamında, örnek kodu bir klasöre indirdiniz. Örnek kodu incelemek ve düzenlemek için bu adımları izleyin.

1. Visual Studio Code, *src/Edge* bölümüne gidin. *. Env* dosyanızı ve birkaç dağıtım şablonu dosyasını görürsünüz.

    Dağıtım şablonu, sınır cihazının bazı özellikler için kullanıldığı dağıtım bildirimini ifade eder. *. Env* dosyası bu değişkenlerin değerlerini içerir.
1. *Src/buluttan cihaza-Console-App* klasörüne gidin. Burada dosya ve diğer birkaç dosya *appsettings.js* görürsünüz:
    * ***C2D-Console-App. csproj*** -Visual Studio Code için proje dosyası.
    * ***operations.json*** -programın çalıştırmasını istediğiniz işlemler listesi.
    * ***Program. cs*** -örnek program kodu. Bu kod:

        * Uygulama ayarlarını yükler.
        * IoT Edge modülündeki canlı video analizinin sunduğu doğrudan yöntemleri çağırır. [Doğrudan yöntemlerini](../../../direct-methods.md)çağırarak canlı video akışlarını çözümlemek için modülünü kullanabilirsiniz. 
        * Pencereyi, **TERMINAL** penceresinde programın çıktısını Incelemenize ve **Çıkış** penceresinde modül tarafından oluşturulan olayları incelemenize olanak sağlamak için duraklar.
        * Kaynakları temizlemek için doğrudan yöntemleri çağırır.

1. Dosyadaki *operations.js* düzenleyin:
    * Grafik topolojisine olan bağlantıyı değiştirin:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json"`
    * Altında `GraphInstanceSet` , grafik topolojisinin adını önceki bağlantıdaki değerle eşleşecek şekilde düzenleyin:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Video dosyasını işaret etmek için RTSP URL 'sini düzenleyin:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * Altında `GraphTopologyDelete` , adı düzenleyin:

        `"name": "EVRToFilesOnMotionDetection"`
