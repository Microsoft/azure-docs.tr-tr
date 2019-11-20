---
title: include dosyası
description: include dosyası
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/04/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 12661c77c6a950b482187b09e4465c964e6d6652
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494069"
---
Azure IoT Edge temel yetilerinden biri, buluttan IoT Edge cihazlarınıza kod dağıtabiyor. **IoT Edge modüller** , kapsayıcı olarak uygulanan yürütülebilir paketlerdir. Bu bölümde, [Azure Marketi 'nin IoT Edge modüller bölümünden](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)önceden oluşturulmuş bir modül dağıtırsınız. 

Bu bölümde dağıttığınız modül bir algılayıcısı taklit eder ve üretilen verileri gönderir. Bu modül, geliştirme ve test amacıyla sanal verileri kullanabilmeniz için IoT Edge kullanmaya başlarken yararlı bir kod parçasıdır. Bu modülün tam olarak ne yaptığını görmek isterseniz, [sanal sıcaklık algılayıcısı kaynak kodunu](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)görüntüleyebilirsiniz. 

Azure Marketi 'nden ilk modülünüzü dağıtmak için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com), aramaya **sanal sıcaklık algılayıcısı** ' nı girin ve Market sonucunu açın.

   ![Azure portal aramada sanal sıcaklık algılayıcısı](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Bu modülü alacak bir IoT Edge cihaz seçin. **IoT Edge modülü Için hedef cihazlar** sayfasında, aşağıdaki bilgileri sağlayın:

   1. **Abonelik**: kullanmakta olduğunuz IoT Hub 'ını içeren aboneliği seçin.

   2. **IoT Hub**: kullanmakta olduğunuz IoT Hub 'ının adını seçin.

   3. **IoT Edge cihaz adı**: Bu hızlı başlangıçta önerilen cihaz adını kullandıysanız, **myedgedevice**yazın. Veya IoT Hub 'ınızdaki IoT Edge cihaz listesinden seçilecek **cihaz bul** ' u seçin. 
   
   4. **Oluştur**'u seçin.

3. Artık Azure Marketi 'nden bir IoT Edge modülü seçtiğinizi ve modülü almak üzere bir IoT Edge cihaz seçtiğinizi, modülün nasıl dağıtılacağını tam olarak tanımlamanıza yardımcı olan üç adımlı bir sihirbaza yönlendirilirsiniz. Sihirbazın **Modül Ekle** adımında, **SimulatedTemperatureSensor** modülünün tekrar doldurulduğuna dikkat edin. Öğreticilerde, dağıtımınıza ek modüller eklemek için bu sayfayı kullanın. Bu hızlı başlangıçta yalnızca bu bir modülü dağıtın. Sihirbazın sonraki adımına devam etmek için **İleri ' yi** seçin.

4. Sihirbazın **rotalar belirtin** adımında, iletilerin modüller arasında ve IoT Hub nasıl geçtiğini tanımlarsınız. Hızlı başlangıç için tüm modüllerden gelen tüm iletilerin IoT Hub (`$upstream`) gitmesini istiyorsunuz. Yeniden doldurulmamışsa aşağıdaki kodu ekleyin:

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```
   Sonra **İleri**’yi seçin.

5. Sihirbazın **dağıtımı gözden geçirme** adımında, IoT Edge cihazınıza dağıtılan tüm MODÜLLERI tanımlayan JSON dosyasının önizlemesini yapabilirsiniz. **SimulatedTemperatureSensor** modülünün dahil edildiğini ve **edgeagent** ve **edgehub**adlı iki ek sistem modülü olduğunu unutmayın. Gözden geçirmeyi bitirdiğinizde **Gönder** ' i seçin.

   IoT Edge cihazına yeni bir dağıtım gönderdiğinizde, cihazınıza hiçbir şey itilemez. Bunun yerine, cihaz sorguları tüm yeni yönergeler için düzenli olarak IoT Hub. Cihaz güncelleştirilmiş bir dağıtım bildirimi bulursa, buluttan modül görüntülerini çekmek için yeni dağıtım hakkındaki bilgileri kullanır ve ardından modülleri yerel olarak çalıştırmaya başlar. Bu işlem birkaç dakika sürebilir. 

6. Modül dağıtım ayrıntılarını gönderdikten sonra, sihirbaz sizi IoT Hub 'ınızın **IoT Edge** sayfasına döndürür. Ayrıntılarını görmek için IoT Edge cihaz listesinden cihazınızı seçin. 

7. Cihaz ayrıntıları sayfasında, **modüller** bölümüne gidin. Üç modülün listelenmesi gerekir: $edgeAgent, $edgeHub ve SimulatedTemperatureSensor. Bir veya daha fazla modül dağıtımda belirtilen şekilde listeleniyorsa, ancak cihaz tarafından bildirilmediyse, IoT Edge cihazınız hala başlatılıyor demektir. Birkaç dakika bekleyip sayfanın üst kısmında **Yenile** ' yi seçin. 

   ![Dağıtılan modüller listesinde SimulatedTemperatureSensor görüntüle](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
