---
title: Çalışma zamanı cihazlar - Azure IOT Edge nasıl yönettiğini öğrenin | Microsoft Docs
description: IoT Edge çalışma zamanının, cihazlarınızda modüller, güvenlik, iletişim ve raporlamayı nasıl yönettiğini öğrenin
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4bdf496995e8b466f1346bfe16365b251c6853c3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076049"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Azure IOT Edge çalışma zamanı ve mimarisini anlama

IoT Edge çalışma zamanı, bir cihazı IoT Edge cihazına veren bir programlar koleksiyonudur. Toplu olarak, IoT Edge çalışma zamanı bileşenleri, IoT Edge cihazların kenarda çalışacak kodu almasını ve sonuçları iletmelerini sağlar. 

IoT Edge çalışma zamanı, IoT Edge cihazlarda aşağıdaki işlevlerden sorumludur:

* Cihaza iş yüklerini yükleyip güncelleştirin.
* Cihazda Azure IoT Edge güvenlik standartlarının bakımını yapın.
* [IoT Edge modüllerinin](iot-edge-modules.md) her zaman çalıştığından emin olun.
* Uzaktan izleme için modül durumunu buluta bildirin.
* Aşağı akış cihazları ve IoT Edge cihazları arasındaki iletişimi yönetin.
* IoT Edge cihazdaki modüller arasındaki iletişimi yönetin.
* IoT Edge cihazı ve bulutu arasındaki iletişimi yönetin.

![Çalışma zamanı öngörüleri ve IOT hub'ına modül durumunu iletişim kurar.](./media/iot-edge-runtime/Pipeline.png)

IOT Edge çalışma zamanı sorumluluklarını iki kategoriye ayrılır: iletişim ve modül yönetimi. Bu iki rol IoT Edge çalışma zamanının parçası olan iki bileşen tarafından gerçekleştirilir. *IoT Edge Aracısı* modülleri dağıttığında ve izlerken *IoT Edge hub* 'ı iletişimden sorumludur. 

IoT Edge hub ve IoT Edge Aracısı, tıpkı bir IoT Edge cihazında çalışan diğer tüm modüller gibi modüllerdir. Bazen *çalışma zamanı modülleri*olarak anırlar. 

## <a name="iot-edge-hub"></a>IOT Edge hub'ı

IoT Edge hub, Azure IoT Edge çalışma zamanını oluşturan iki modülden biridir. IOT hub'ın yerel bir ara sunucu olarak IOT hub'ı aynı protokol uç noktalarını göstererek görür. Bu tutarlılık anlamına istemciler (olmadığını cihazlar veya modülleri) IOT Edge çalışma zamanı, IOT Hub'ına gibi bağlanabilirsiniz. 

>[!NOTE]
> IoT Edge hub, MQTT veya AMQP kullanarak bağlanan istemcileri destekler. HTTP kullanan istemcileri desteklemez. 

IoT Edge hub 'ı yerel olarak çalışan IoT Hub tam bir sürümü değil. IoT Edge hub 'ının IoT Hub için sessizce temsilci olarak temsil eden bazı şeyler vardır. Örneğin, IoT Edge hub, bir cihaz ilk kez bağlanmayı denediğinde kimlik doğrulama isteklerini IoT Hub iletir. İlk bağlantı kurulduktan sonra, güvenlik bilgileri IoT Edge hub 'ı tarafından yerel olarak önbelleğe alınır. Daha sonraki bağlantılar, CİHAZDAN buluta kimlik doğrulaması yapmak zorunda kalmadan izin verilir. 

IoT Edge çözümünüzün kullandığı bant genişliğini azaltmak için IoT Edge hub 'ı buluta kaç tane gerçek bağlantı yapıldığını en iyi duruma getirir. IoT Edge hub, modüller veya aşağı akış cihazları gibi istemcilerden mantıksal bağlantılar alır ve bunları buluta tek bir fiziksel bağlantı için birleştirir. Bu işlemin ayrıntılarını çözümün geri kalanı için saydamdır. İstemciler, bunların tümü aynı bağlantı üzerinden gönderilen olsa da kendi bağlantı buluta sahip oldukları düşünün. 

![IoT Edge hub, fiziksel cihazlar ve IoT Hub arasında bir ağ geçididir](./media/iot-edge-runtime/Gateway.png)

IoT Edge hub, IoT Hub bağlanıp bağlanmadığını belirleyebilir. Bağlantı kaybolursa IoT Edge hub iletileri veya ikizi güncelleştirmelerini yerel olarak kaydeder. Bağlantı yeniden sonra tüm verileri eşitler. Bu geçici önbellek için kullanılan konum, IoT Edge hub 'ının modül ikizi bir özelliği tarafından belirlenir. Önbelleğin boyutunu değil tavan ve cihaz depolama kapasitesine sahip sürece büyüyecektir. Daha fazla bilgi için bkz. [çevrimdışı yetenekler](offline-capabilities.md).

### <a name="module-communication"></a>Modül iletişimi

IoT Edge hub, modülü modül iletişimine kolaylaştırır. İleti Aracısı olarak IoT Edge hub 'ı kullanmak, modülleri birbirinden bağımsız olarak tutar. Modüller yalnızca üzerinde iletileri ve bunlar iletileri yazma çıkışları kabul girişleri belirtmeniz gerekir. Bir çözüm geliştiricisi, bu girdileri ve çıkışları birlikte birleştirerek modüllerin bu çözüme özgü sırada verileri işlemesini sağlar. 

![IoT Edge hub, modüle modül iletişimini kolaylaştırır](./media/iot-edge-runtime/module-endpoints.png)

IoT Edge hub 'ına veri göndermek için, bir modül SendEventAsync yöntemini çağırır. İletiyi göndermek için hangi çıkış ilk bağımsız değişken belirtir. Aşağıdaki sözde kod **output1**üzerine bir ileti gönderir:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync("output1", message); 
   ```

Bir ileti almak için belirli bir girdi gelen iletileri işleyen bir geri çağırma kaydedin. Aşağıdaki sözde kod, **input1**üzerinde alınan tüm iletileri işlemek Için kullanılacak MessageProcessor işlevini kaydeder:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Moduleclient sınıfı ve iletişim yöntemleri hakkında daha fazla bilgi için bkz. tercih ettiğiniz SDK dili için API başvurusu [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet):, [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)veya [Node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Çözüm geliştiricisi, IoT Edge hub 'ının iletileri modüller arasında nasıl geçireceğini belirleyen kuralları belirtmekten sorumludur. Yönlendirme kuralları bulutta tanımlanmıştır ve modülünde IoT Edge hub 'ına dağıtılır ikizi. IOT hub'ı yolları aynı sözdizimi, Azure IOT edge'deki modüller arasında tanımlamak için kullanılır. Daha fazla bilgi için bkz. [IoT Edge modül dağıtmayı ve yollar oluşturmayı öğrenin](module-composition.md).   

![Modüller arasındaki rotalar IoT Edge hub 'ına gider](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IOT Edge Aracısı

IOT Edge, Azure IOT Edge çalışma zamanını oluşturan yapan başka bir modül aracısıdır. Modülleri örnekleme, çalışmaya devam sağlama ve IOT Hub'ına modüllerinin durumunu raporlamaya sorumludur. Bu yapılandırma verileri, ikizi IoT Edge Agent modülünün bir özelliği olarak yazılmıştır. 

[IoT Edge güvenlik arka plan programı](iot-edge-security-manager.md) , cihaz başlangıcında IoT Edge aracısını başlatır. Aracı kendi modül ikizi, IOT Hub'ından alır ve dağıtım bildirimini inceler. Dağıtım bildirimi başlatılması gereken modülleri bildiren bir JSON dosyasıdır. 

Dağıtım bildirimindeki her öğe, bir modülle ilgili belirli bilgileri içerir ve IoT Edge Aracısı tarafından modülün yaşam döngüsünü denetlemek için kullanılır. Bazı daha ilgi çekici özellikleri şunlardır: 

* **Settings. image** : IoT Edge aracısının modülü başlatmak için kullandığı kapsayıcı görüntüsü. Görüntü bir parolayla korunuyorsa, IoT Edge aracısının kapsayıcı kayıt defteri kimlik bilgileriyle yapılandırılması gerekir. Kapsayıcı kayıt defteri için kimlik bilgileri, IoT Edge program klasöründeki `config.yaml` dosyasını güncelleştirerek dağıtım bildirimi veya IoT Edge cihazının kendisi kullanılarak uzaktan yapılandırılabilir.
* **Settings. createOptions** : bir modülün kapsayıcısı başlatılırken doğrudan Moby kapsayıcı Daemon 'a geçirilen bir dize. Bu özelliğe seçenek eklemek, bağlantı noktası iletme veya bir modülün kapsayıcısına birim bağlama gibi gelişmiş yapılandırmalara izin verir.  
* **durum** : IoT Edge aracısının modülü yerleştirdiği durum. Genellikle, bu değer, IoT Edge aracısının cihazdaki tüm modülleri hemen başlatmasını istediği için, *çalışıyor* olarak ayarlanır. Ancak, durdurulacak bir modülün başlangıç durumunu belirtebilir ve daha sonra IoT Edge aracısına bir modül başlatmasını söylemeniz için bekleyin. IoT Edge Aracısı, her modülün durumunu bildirilen özelliklerde buluta geri bildirir. İstenen özellik ve bildirilen özellik arasında bir fark davranan bir cihaz bir göstergesidir. Desteklenen durumlar şunlardır:
   * İndiriliyor
   * Çalışıyor
   * İyi durumda değil
   * Başarısız
   * Durduruldu
* **restartPolicy** : IoT Edge Aracısı bir modülü yeniden başlatır. Olası değerler şunlardır:
   * `never`: IoT Edge Aracısı modülü hiçbir şekilde yeniden başlatmaz.
   * `on-failure`-modül kilitlenirse, IoT Edge Aracısı yeniden başlatır. Modül düzgün şekilde kapatılırsa IoT Edge Aracısı yeniden başlatmaz.
   * `on-unhealthy`-modül kilitlenirse veya sağlıksız kabul edildiğinde, IoT Edge Aracısı yeniden başlatır.
   * `always`-modül kilitlenirse, sağlıksız olarak kabul edilir veya herhangi bir şekilde kapatılırsa, IoT Edge Aracısı tarafından yeniden başlatılır. 
* **ımagepullpolicy** -IoT Edge aracısının bir modül için en son görüntüyü otomatik olarak çekmeye çalışıp çalışmadığını belirtir. Bir değer belirtmezseniz, varsayılan olarak *OnCreate*olur. Olası değerler şunlardır: 
   * `on-create`-bir modül başlatılırken veya yeni bir dağıtım bildirimine göre modül güncelleştirirken, IoT Edge Aracısı modül görüntüsünü kapsayıcı kayıt defterinden çekmeye çalışacaktır.
   * `never`-IoT Edge Aracısı hiçbir koşulda modül görüntüsünü kapsayıcı kayıt defterinden çekmeye çalışmaz. Beklendiğinde modül görüntüsünün cihazda önbelleğe alınması ve herhangi bir modül görüntüsü güncelleştirmelerinin el ile veya üçüncü taraf bir çözüm tarafından yönetilme yapıldığının beklentisi. 

IOT Edge çalışma zamanı yanıtı IOT hub'a gönderir. Olası yanıtların listesi aşağıda verilmiştir:
  * 200 - TAMAM
  * 400 - dağıtım yapılandırması bozuk veya geçersiz.
  * 417-cihazda bir dağıtım yapılandırma kümesi yok.
  * 412 - dağıtım yapılandırma şeması sürümü geçersiz.
  * 406-IoT Edge cihaz çevrimdışı veya durum raporları göndermiyor.
  * 500-IoT Edge çalışma zamanında bir hata oluştu.

Daha fazla bilgi için bkz. [IoT Edge modül dağıtmayı ve yollar oluşturmayı öğrenin](module-composition.md).   

### <a name="security"></a>Güvenlik

IOT Edge Aracısı bir IOT Edge cihazının güvenliği kritik rol oynar. Örneğin, bir modülün görüntüsüne başlatmadan önce doğrulama gibi eylemleri gerçekleştirir. 

Azure IoT Edge güvenlik çerçevesi hakkında daha fazla bilgi için [IoT Edge Güvenlik Yöneticisi](iot-edge-security-manager.md)hakkında makalesini okuyun.

## <a name="next-steps"></a>Sonraki adımlar

[Azure IOT Edge modüllerini anlama](iot-edge-modules.md)
