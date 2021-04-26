---
title: Mikro-Karşılama Merkezi uygulama şablonu öğreticisi | Microsoft Docs
description: Azure IoT Central için mikro Karşılama Merkezi uygulama şablonuyla ilgili bir öğretici
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 01/09/2020
ms.openlocfilehash: ab4d609b1638b47ad73b6f1143adee2870577305
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390479"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Öğretici: mikro Karşılama Merkezi uygulama şablonunu dağıtma ve gözden geçir

Bu öğreticide, bir perakende çözümü oluşturmak için Azure IoT Central mikro Karşılama Merkezi uygulama şablonunu kullanırsınız.

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
> * Uygulama şablonunu dağıtma
> * Uygulama şablonunu kullanma

## <a name="prerequisites"></a>Önkoşullar
Bu öğretici serisini tamamlayabilmeniz için bir Azure aboneliğine sahip olmanız gerekir. İsteğe bağlı olarak 7 günlük ücretsiz deneme kullanabilirsiniz. Azure aboneliğiniz yoksa, [Azure kaydolma sayfasında](https://aka.ms/createazuresubscription)bir tane oluşturabilirsiniz.

## <a name="create-an-application"></a>Uygulama oluşturma 
Bu bölümde, bir şablondan yeni bir Azure IoT Central uygulaması oluşturacaksınız. Bu uygulamayı, tüm bir çözüm oluşturmak için öğretici serisi boyunca kullanacaksınız.

Yeni bir Azure IoT Central uygulaması oluşturmak için:

1. [Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) Web sitesine gidin.
1. Azure aboneliğiniz varsa, ona erişmek için kullandığınız kimlik bilgileriyle oturum açın. Aksi takdirde, Microsoft hesabı kullanarak oturum açın:

   ![Microsoft hesabı oturum açma iletişim kutusunun ekran görüntüsü](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Yeni bir Azure IoT Central uygulaması oluşturmaya başlamak için **Yeni Uygulama**'yı seçin.

1. **Perakende**' ı seçin.  Perakende sayfasında çeşitli perakende uygulama şablonları görüntülenir.

Önizleme özelliklerini kullanan yeni bir mikro Karşılama Merkezi uygulaması oluşturmak için:  
1. **Mikro Karşılama Merkezi** uygulama şablonunu seçin. Bu şablon, öğreticide kullanılan tüm cihazların cihaz şablonlarını içerir. Şablon Ayrıca, karşılama merkezinizdeki koşulların yanı sıra robot taşıyıcılar için koşullar için de bir operatör panosu sağlar. 

    ![Azure IoT Central IoT uygulama sayfanızı derleme ekran görüntüsü](./media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png)
    
1. İsteğe bağlı olarak, kolay bir **uygulama adı** seçin. Uygulama şablonu kurgusal şirket Northwind Traders ' i temel alır. 

    >[!NOTE]
    >Kolay bir uygulama adı kullanırsanız, uygulama URL 'SI için yine de benzersiz bir değer kullanmanız gerekir.

1. Azure aboneliğiniz varsa dizininizi, Azure aboneliğinizi ve bölgenizi girin. Aboneliğiniz yoksa, 7 günlük ücretsiz denemeyi etkinleştirebilir ve gerekli iletişim bilgilerini tamamlayabilirsiniz.  

    Dizinler ve abonelikler hakkında daha fazla bilgi için bkz. [uygulama oluşturma](../core/quick-deploy-iot-central.md) hızlı başlangıcı.

1. **Oluştur**’u seçin.

    ![Azure IoT Central yeni uygulama sayfasının ekran görüntüsü](./media/tutorial-micro-fulfillment-center-app/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Uygulamayı gözden geçir 

Uygulama şablonunu başarıyla dağıttıktan sonra, **Northwind Traders Micro-Karşılama Merkezi panosunu** görürsünüz. Northwind Traders, bu Azure IoT Central uygulamasında yönetilen bir mikro karşılama merkezinin bulunduğu kurgusal bir satıcıdır. Bu operatör panosunda, bu şablondaki cihazlarla ilgili bilgileri ve telemetrisi, gerçekleştirebileceğiniz bir dizi komut, iş ve eylemi görürsünüz. Pano, mantıksal olarak iki bölüme ayrılır. Sol tarafta, karşılama yapısı içinde ortam koşullarını izleyebilir ve sağ tarafta, bir robot taşıyıcının durumunu tesis içinde izleyebilirsiniz.  

Panodan şunları yapabilirsiniz:
   * Çekme sayısı, işlenen siparişlerin sayısı ve yapı sistemi durumu gibi özellikler gibi cihaz telemetrisine bakın.  
   * Karşılama yapısında robot taşıyıcılar 'nın kat planını ve konumunu görüntüleyin.
   * Denetim sistemini sıfırlama, taşıyıcının bellenimini güncelleştirme ve ağı yeniden yapılandırma gibi tetikleyici komutları.

     ![Northwind Traders Micro-Karşılama Merkezi panosunun en üstteki yarısını ekran görüntüsü.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard1.png)
   * Bir işlecin, yerine getirme Merkezi içindeki koşulları izlemek için kullanabileceği bir pano örneği görürsünüz. 
   * Karşılama Merkezi içindeki ağ geçidi cihazında çalışan yüklerin sistem durumunu izleyin.    

     ![Northwind Traders Micro-Karşılama Merkezi panosunun Botton yarısının ekran görüntüsü.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard2.png)

## <a name="device-template"></a>Cihaz şablonu
Cihaz şablonları sekmesini seçerseniz, şablonun parçası olan iki farklı cihaz türü olduğunu görürsünüz: 
   * **Robot taşıyıcı**: Bu cihaz şablonu, karşılama yapısında dağıtılan ve uygun depolama ve alma işlemlerini gerçekleştiren, çalışan bir robot taşıyıcının tanımını temsil eder. Şablonu seçerseniz, robot 'un sıcaklık ve eksen konumu gibi cihaz verilerini gönderdiğini ve robot taşıyıcı durumu gibi özellikler olduğunu görürsünüz. 
   * **Yapı koşulu izleme**: Bu cihaz şablonu, ortam koşulunun yanı sıra, yerine getirme merkezinizi desteklemek için çeşitli Edge iş yüklerini barındıran ağ geçidi cihazını da izlemenize olanak tanıyan bir cihaz koleksiyonunu temsil eder. Cihaz sıcaklık, çekme sayısı ve sipariş sayısı gibi telemetri verileri gönderir. Ayrıca, ortamınızda çalışan işlem iş yüklerinin durumu ve durumu hakkında bilgi gönderir. 

     ![Mikro-Karşılama Merkezi cihaz şablonları](./media/tutorial-micro-fulfillment-center-app/device-templates.png)

Cihaz grupları sekmesini seçerseniz, bu cihaz şablonlarının kendileri için otomatik olarak cihaz grupları oluşturduğunu de görürsünüz.

## <a name="rules"></a>Kurallar
**Kurallar** sekmesinde, robot taşıyıcının sıcaklık koşullarını izlemek için uygulama şablonunda bulunan örnek bir kural görürsünüz. Bu kuralı, tesisteki belirli bir robot fazla Isıtma olursa ve bakım için çevrimdışına alınması gerekiyorsa işleci uyarmak için kullanabilirsiniz. 

Örnek kuralı, iş işlevleriniz için daha uygun olan kuralları tanımlamak için ilham olarak kullanın.

![Kurallar sekmesinin ekran görüntüsü](./media/tutorial-micro-fulfillment-center-app/rules.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz uygulama şablonunu silin. **Yönetim**  >  **uygulaması ayarları**' na gidin ve **Sil**' i seçin.

![Mikro-Karşılama Merkezi uygulama ayarları sayfasının ekran görüntüsü](./media/tutorial-micro-fulfillment-center-app/delete.png)

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdakiler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Mikro-Karşılama Merkezi Çözüm mimarisi](./architecture-micro-fulfillment-center.md)
