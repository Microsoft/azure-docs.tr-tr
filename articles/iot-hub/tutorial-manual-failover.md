---
title: Öğretici-Azure IoT Hub 'ın el Ile yük devretmesi | Microsoft Docs
description: Öğretici-IoT Hub 'ınızı farklı bir bölgeye el ile yük devretme işlemini gerçekleştirmeyi ve çalıştığını onaylamaya, sonra da özgün bölgeye geri dönüp yeniden kontrol yapmayı öğrenin.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: 3b6bc972f5c26c78ffff81f5bab8c2812cf2cb11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98622921"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Öğretici: IoT Hub 'ı için el ile yük devretme gerçekleştirme

Elle yük devretme, müşterilerin hub işlemleri için birincil bölgeden coğrafi olarak eşleştirilen ilgili Azure coğrafi eşleme bölgesine [yük devretme](https://en.wikipedia.org/wiki/Failover) gerçekleştirmesini sağlayan bir IoT Hub hizmeti özelliğidir. Elle yük devretme, bölgesel olağanüstü durum veya uzun süreli hizmet kesintisi durumunda gerçekleştirilebilir. Ayrıca sisteminizin olağanüstü durum kurtarma özelliklerini test etmek için de planlı bir yük devretme gerçekleştirebilirsiniz. Bunun için üretim ortamı yerine test amaçlı bir IoT hub kullanmanız önerilir. El ile yük devretme özelliği, müşteriler 18 Mayıs 2017 ' den sonra oluşturulan IoT Hub 'ları için ek bir ücret ödemeden müşterilere sunulur.

Bu öğreticide, aşağıdaki görevleri gerçekleştireceksiniz:

> [!div class="checklist"]
> * Azure portalı kullanarak bir IoT hub'ı oluşturun. 
> * Yük devretme gerçekleştirin. 
> * Hub'ın ikincil konumda çalıştığını görün.
> * IoT hub'ın işlemlerini birincil konuma geri almak için yeniden çalışma gerçekleştirin. 
> * Hub'ın doğru konumda düzgün biçimde çalıştığını onaylayın.

El ile yük devretme ve Microsoft tarafından başlatılan IoT Hub yük devretme hakkında daha fazla bilgi için bkz. [çapraz bölge olağanüstü durum kurtarma](iot-hub-ha-dr.md#cross-region-dr).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

* Güvenlik duvarınızdaki 8883 numaralı bağlantı noktasını açık olduğundan emin olun. Bu öğreticideki cihaz örneği, 8883 numaralı bağlantı noktası üzerinden iletişim kuran MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağ ortamlarında engellenebilir. Bu sorunu geçici olarak çözmek için daha fazla bilgi ve IoT Hub bkz. [bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

1. [Azure Portal](https://portal.azure.com)oturum açın. 

2. **+ Kaynak oluştur**'a tıklayıp **Nesnelerin İnterneti**'ni ve ardından **IoT Hub** öğesini seçin.

   ![IoT hub'ı oluşturmayı gösteren ekran görüntüsü](./media/tutorial-manual-failover/create-hub-01.png)

3. **Temel bilgiler** sekmesini seçin. Aşağıdaki alanları girin.

    **Abonelik**: Kullanmak istediğiniz Azure aboneliğini seçin.

    **Kaynak Grubu**: **Yeni oluştur**'a tıklayın ve kaynak grubu adı olarak **ManlFailRG** girin.

    **Bölge**: size yakın bir bölge seçin. Bu öğreticide `West US 2` kullanılır. Yük devretme yalnızca coğrafi olarak eşleştirilmiş Azure bölgeleri arasında gerçekleştirilebilir. Batı ABD 2 coğrafi olarak eşleştirilmiş bölge WestCentralUS.
    
   **IoT Hub'ı Adı**: IoT hub'ınıza bir ad verin. Hub adının genel olarak benzersiz olması gerekir. 

   ![IoT hub'ı oluşturmak için Temel Bilgiler bölmesini gösteren ekran görüntüsü](./media/tutorial-manual-failover/create-hub-02-basics.png)

   **Gözden geçir ve oluştur**’a tıklayın. (Boyut ve ölçek için varsayılan değerler kullanılır.) 

4. Bilgileri gözden geçirin ve **Oluştur**'a tıklayarak IoT hub'ını oluşturun. 

   ![IoT hub'ı oluşturmak için son adımı gösteren ekran görüntüsü](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Elle yük devretme gerçekleştirme

Bir IoT hub'ı için günlük iki yük devretme ve iki yeniden çalışma sınırı vardır.

1. **Kaynak grupları**'na tıklayın ve **ManlFailRG** adlı kaynak grubunu seçin. Kaynak listesinde hub'ınıza tıklayın. 

1. IoT Hub bölmesinde **Ayarlar** altında **Yük devretme**' ye tıklayın.

   ![IoT Hub'ı özellikler bölmesini gösteren ekran görüntüsü](./media/tutorial-manual-failover/trigger-failover-01.png)

1. El Ile yük devretme bölmesinde **geçerli konumu** ve **Yük devretme konumunu** görürsünüz. Geçerli konum her zaman hub 'ın Şu anda etkin olduğu konumu gösterir. Yük devretme konumu, geçerli konumla eşleştirilmiş standart [Azure coğrafi eşlenmiş bölgesidir](../best-practices-availability-paired-regions.md) . Konum değerlerini değiştiremezsiniz. Bu öğretici için, geçerli konum `West US 2` ve yük devretme konumu olur `West Central US` .

   ![Elle Yük Devretme bölmesini gösteren ekran görüntüsü](./media/tutorial-manual-failover/trigger-failover-02.png)

1. El Ile yük devretme bölmesinin üst kısmında, **Yük devretmeyi Başlat**' a tıklayın. 

1. Onay bölmesinde, IoT Hub 'ınızın adını doldurarak yük devretmek istediğinizi onaylayın. Ardından, yük devretmeyi başlatmak için **Yük devretme**' ye tıklayın.

   Elle yük devretme gerçekleştirmek için gereken süre, hub'ınıza kayıtlı olan cihaz sayısına bağlıdır. Örneğin 100.000 cihazınız varsa 15 dakika, beş milyon cihazınız varsa bir saat veya daha uzun sürebilir.

   ![El Ile yük devretme onay bölmesini gösteren ekran görüntüsü](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   El ile yük devretme işlemi çalışırken, devam etmekte olan bir el ile yük devretme olduğunu söyleyen bir başlık görünür. 

   ![Devam eden Elle Yük Devretme işlemini gösteren ekran görüntüsü](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   IoT Hub bölmesini kapatır ve kaynak grubu bölmesinde tıklayarak yeniden açarsanız, hub 'ın el ile yük devretmenin ortasında olduğunu bildiren bir başlık görürsünüz. 

   ![IoT Hub yük devretme işleminin devam ettiğini gösteren ekran görüntüsü](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Tamamlandıktan sonra, El Ile yük devretme sayfasındaki geçerli ve yük devretme bölgeleri çevrilmiştir ve hub tekrar etkin olur. Bu örnekte, geçerli konum şu anda `WestCentralUS` ve yük devretme konumu artık vardır `West US 2` . 

   ![Yük devretme işleminin tamamlandığını gösteren ekran görüntüsü](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   Genel Bakış sayfasında ayrıca yük devretme işleminin tamamlandığını ve IoT Hub içinde çalıştığını belirten bir başlık gösterilir `West Central US` .

   ![Genel Bakış sayfasında yük devretme işleminin tamamlandığını gösteren ekran görüntüsü](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Yeniden çalışma gerçekleştirme 

Elle yük devretme gerçekleştirdikten sonra hub işlemlerini tekrar birincil bölgeye geçirebilirsiniz. Bu işlem yeniden çalışma olarak adlandırılır. Yük devretme işlemini kısa bir süre önce gerçekleştirdiyseniz yeniden çalışma isteğinde bulunmak için yaklaşık bir saat beklemeniz gerekir. Daha kısa bir süre içinde yeniden çalışma isteğinde bulunursanız bir hata iletisi görüntülenir.

Yeniden çalışma işlemi, elle yük devretme işlemiyle aynı şekilde gerçekleştirilir. Bu adımlar şunlardır: 

1. Yeniden çalışma gerçekleştirmek için IoT hub'ınızın IoT Hub'ı bölmesine dönün.

2. IoT Hub bölmesinde **Ayarlar** altında **Yük devretme**' ye tıklayın. 

3. El Ile yük devretme bölmesinin üst kısmında, **Yük devretmeyi Başlat**' a tıklayın. 

4. Onay bölmesinde, IoT Hub 'ınızın adını doldurarak yeniden çalışmayı istediğinizi onaylayın. Ardından yeniden çalışmayı başlatmak için Tamam'a tıklayın. 

   ![Elle yeniden çalışma isteğinin ekran görüntüsü](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Görüntülenen başlıklar yük devretme gerçekleştirme bölümünde anlatılanlarla aynıdır. Yeniden çalışma tamamlandıktan sonra, `West US 2` ilk olarak geçerli konum ve `West Central US` Yük devretme konumu olarak ayarlanır.

## <a name="clean-up-resources"></a>Kaynakları temizleme 

Bu öğretici için oluşturduğunuz kaynakları kaldırmak için kaynak grubunu silin. Bu eylem grubun içerdiği tüm kaynakları siler. Bu durumda, IoT hub'ı ve kaynak grubu kaldırılır. 

1. **Kaynak Grupları**'na tıklayın. 

2. **ManlFailRG** adlı kaynak grubunu bulun ve seçin. Açmak için tıklayın. 

3. **Kaynak grubunu sil**'e tıklayın. İstendiğinde kaynak grubunun adını girin ve **Sil**'e tıklayarak onaylayın. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki görevleri gerçekleştirerek elle yük devretme yapılandırma ve gerçekleştirmenin yanı sıra yeniden çalışma isteğinde bulunmayı öğrendiniz:

> [!div class="checklist"]
> * Azure portalı kullanarak bir IoT hub'ı oluşturun. 
> * Yük devretme gerçekleştirin. 
> * Hub'ın ikincil konumda çalıştığını görün.
> * IoT hub'ın işlemlerini birincil konuma geri almak için yeniden çalışma gerçekleştirin. 
> * Hub'ın doğru konumda düzgün biçimde çalıştığını onaylayın.

IoT cihazı durumunun nasıl yönetileceğini öğrenmek için sonraki öğreticiye geçin. 

> [!div class="nextstepaction"]
> [IoT cihazı durumunu yönetme](tutorial-device-twins.md)
