---
title: include dosyası
description: include dosyası
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 6b9ec2017ffa5d4e4148b441aa23ed2eca6ee8b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99628952"
---
Azure IoT Edge temel yetilerinden biri, buluttan IoT Edge cihazlarınıza kod dağıtmakta. *IoT Edge modüller* , kapsayıcı olarak uygulanan yürütülebilir paketlerdir. Bu bölümde, [Azure Marketi 'nin IoT Edge modüller bölümünden](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) doğrudan Azure IoT Hub ' den önceden oluşturulmuş bir modül dağıtırsınız.

Bu bölümde dağıttığınız modül bir algılayıcısı taklit eder ve üretilen verileri gönderir. Bu modül, geliştirme ve test amacıyla sanal verileri kullanabilmeniz için IoT Edge kullanmaya başlarken yararlı bir kod parçasıdır. Bu modülün tam olarak ne yaptığını görmek isterseniz, [sanal sıcaklık algılayıcısı kaynak kodunu](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)görüntüleyebilirsiniz.

Azure Marketi 'nden ilk modülünüzü dağıtmak için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub 'ınıza gidin.

1. Soldaki menüden, **otomatik cihaz yönetimi** altında **IoT Edge**' yi seçin.

1. Cihaz listesinden hedef cihazın cihaz KIMLIĞINI seçin.

1. Üstteki çubukta **modülleri ayarla**' yı seçin.

   ![Set modules seçen ekran görüntüsü.](./media/iot-edge-deploy-module/select-set-modules.png)

1. **IoT Edge modüller** altında, **Ekle** açılan menüsünü açın ve ardından **Market modülü**' nü seçin.

   ![Ekle açılan menüsünü gösteren ekran görüntüsü.](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. **IoT Edge Module marketi**' nde, modülü arayın ve seçin `Simulated Temperature Sensor` .

   Modül, istenen **çalışma** durumu Ile IoT Edge modüller bölümüne eklenir.

1. **İleri ' yi seçin:** Sihirbazın bir sonraki adımına devam etmek için yollar.

   ![Modül eklendikten sonra sonraki adıma devam eden gösteren ekran görüntüsü.](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. **Rotalar** sekmesinde, varsayılan yolu, **route**' u kaldırın ve ardından İleri ' yi seçin. sihirbazın sonraki adımına devam etmek Için yeni ' yi **inceleyin ve oluştur** ' a tıklayın.

   >[!Note]
   >Yollar ad ve değer çiftleri kullanılarak oluşturulur. Bu sayfada iki yol görmeniz gerekir. Varsayılan yol, **route**, tüm iletileri IoT Hub gönderir (denir `$upstream` ). Azure Marketi 'nden modülü eklediğinizde, **SimulatedTemperatureSensorToIoTHub** adlı ikinci bir yol otomatik olarak oluşturulmuştur. Bu yol, sanal sıcaklık modülünden tüm iletileri IoT Hub gönderir. Bu durumda gereksiz olduğu için varsayılan yolu silebilirsiniz.

   ![Sonraki adıma geçmek üzere varsayılan yolun kaldırılmasını gösteren ekran görüntüsü.](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. JSON dosyasını gözden geçirin ve ardından **Oluştur**' u seçin. JSON dosyası IoT Edge cihazınıza dağıttığınız tüm modülleri tanımlar. **SimulatedTemperatureSensor** modülünü ve iki çalışma zamanı modülünü, **edgeagent** ve **edgehub**'ı görürsünüz.

   >[!Note]
   >IoT Edge cihazına yeni bir dağıtım gönderdiğinizde cihazınıza hiçbir şey gönderilmez. Bunun yerine cihaz, IoT Hub'ı düzenli olarak sorgulayarak yeni yönergeler olup olmadığını denetler. Güncelleştirilmiş bir dağıtım bildirimi bulması halinde cihaz yeni dağıtımla ilgili bilgileri kullanarak buluttaki modül görüntülerini çeker ve modülleri yerel ortamda çalıştırmaya başlar. Bu işlem birkaç dakika sürebilir.

1. Modül dağıtım ayrıntılarını oluşturduktan sonra, sihirbaz sizi cihaz ayrıntıları sayfasına döndürür. **Modüller** sekmesinde dağıtım durumunu görüntüleyin.

   Üç modül görmeniz gerekir: **$edgeAgent**, **$edgeHub** ve **SimulatedTemperatureSensor**. Bir veya daha fazla modülden **dağıtımda belirtilen** **Evet** varsa ancak **cihaz tarafından raporlanmadığında**, IoT Edge cihazınız hala başlatılıyor demektir. Birkaç dakika bekleyin ve sonra sayfayı yenileyin.

   ![Dağıtılan modüller listesinde sanal sıcaklık algılayıcısı gösteren ekran görüntüsü.](./media/iot-edge-deploy-module/view-deployed-modules.png)
