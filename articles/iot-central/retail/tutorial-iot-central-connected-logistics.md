---
title: IoT bağlı lojistik öğreticisi | Microsoft Docs
description: IoT Central için bağlı lojistik uygulama şablonu öğreticisi
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 7c6c50b6fd8200726aa11f120d9748cc1b40aa22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831629"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Öğretici: bağlı bir lojistik uygulama şablonunu dağıtma ve gözden geçir

Bu öğreticide, IoT Central *bağlı lojistik* uygulama şablonuyla çalışmaya başlama işlemi gösterilmektedir. Şablonu dağıtmayı ve kullanmayı öğreneceksiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bağlı bir lojistik uygulaması oluşturun.
> * Uygulamadaki önemli özellikleri kullanın.
> * Kritik lojistik cihaz işlemleri etkinliğini göstermek için panoyu kullanın.
> * Cihaz şablonu kullan
> * Kuralları izle
> * Işleri kullanma

## <a name="prerequisites"></a>Önkoşullar

* Bu uygulamayı dağıtmak için belirli bir önkoşul yoktur.
* Ücretsiz fiyatlandırma planını kullanabilir veya bir Azure aboneliği kullanabilirsiniz.

## <a name="create-connected-logistics-application"></a>Bağlı lojistik uygulaması oluştur

Aşağıdaki adımları kullanarak uygulamayı oluşturun:

1. [Azure IoT Central Build](https://aka.ms/iotcentral) sitesine gidin. Sonra bir Microsoft kişisel, iş veya okul hesabıyla oturum açın. Sol taraftaki Gezinti çubuğundan **Oluştur** ' u seçin ve ardından **Perakende** sekmesini seçin:

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png" alt-text="Bağlı lojistik şablonu":::

1. **Bağlı lojistik uygulaması** altında **uygulama oluştur** ' u seçin.

1. **Uygulama oluştur** **Yeni uygulama** formunu açar. Şu ayrıntıları girin:


    * **Uygulama adı**: varsayılan önerilen adı kullanabilir veya kolay uygulamanızın adını girebilirsiniz.
    * **URL**: önerilen varsayılan URL 'yi kullanabilir veya kolay eşsiz bir URL 'yi girebilirsiniz. Daha sonra, zaten bir Azure aboneliğiniz varsa varsayılan ayar önerilir. 7 günlük ücretsiz deneme fiyatlandırma planıyla başlayabilir ve ücretsiz izleme süresi dolmadan önce istediğiniz zaman standart bir fiyatlandırma planına dönüştürmeyi tercih edebilirsiniz.
    * **Fatura bilgileri**: kaynakları sağlamak için dizin, Azure aboneliği ve bölge ayrıntıları gereklidir.
    * **Oluştur**: uygulamanızı dağıtmak için sayfanın alt kısmındaki Oluştur ' u seçin.

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png" alt-text="Bağlı lojistik uygulama şablonu":::

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png" alt-text="Bağlı lojistik faturalandırma bilgileri":::

## <a name="walk-through-the-application"></a>Uygulamayı gözden geçir

Bağlı lojistik uygulama şablonunun nasıl seçileceğini gösteren ekran görüntüsü aşağıda verilmiştir.

> [!div class="mx-imgBorder"]
> ![Bağlı lojistik uygulama şablonunun nasıl seçileceğini gösteren ekran görüntüsü](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

Aşağıdaki bölümler, uygulamanın temel özellikleri boyunca size yol gösterir.

### <a name="dashboard"></a>Pano

Uygulama şablonu dağıttıktan sonra, varsayılan panonuz bağlı bir lojistik operatör odaklı portaldır. Northwind Trader, deniz ve Land 'ta bir kargo filo 'i yöneten kurgusal bir lojistik sağlayıcıdır. Bu panoda, sevkiyatlardan telemetri sağlayan, ilişkili komutlar, işler ve eylemlerle birlikte iki farklı ağ geçidi görürsünüz.

> [!div class="mx-imgBorder"]
> ![Bağlı lojistik uygulama şablonundan uygulama oluşturmayı gösteren ekran görüntüsü](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

> [!div class="mx-imgBorder"]
> ![Uygulamayı oluştururken faturalandırma seçeneklerini gösteren ekran görüntüsü](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

Bu Pano, kritik lojistik cihaz işlemleri etkinliğini gösterecek şekilde önceden yapılandırılmıştır.

Pano iki farklı ağ geçidi cihaz yönetimi işlemini mümkün bir şekilde sunar:

* Kamyonun sevkıyatları için lojistik rotalarını ve okyanus sevkiyatlarının konum ayrıntılarını görüntüleyin.
* Ağ Geçidi durumunu ve diğer ilgili bilgileri görüntüleyin.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png" alt-text="Bağlı lojistik panosu":::

* Toplam ağ geçidi sayısını, etkin ve bilinmeyen etiketleri izleyebilirsiniz.
* Cihaz yönetimi işlemlerini şu şekilde yapabilirsiniz: üretici yazılımını güncelleştirme, algılayıcıları devre dışı bırakma ve etkinleştirme, bir algılayıcı eşiğini güncelleştirme, telemetri aralıklarını güncelleştirme ve cihaz hizmeti sözleşmelerini güncelleştirme.
* Cihaz pili tüketimini görüntüleyin.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png" alt-text="Bağlı lojistik Pano durumu":::

#### <a name="device-template"></a>Cihaz şablonu

Ağ Geçidi yetenek modelini görmek için **cihaz şablonları** ' nı seçin. Yetenek modeli, **ağ geçidi telemetrisi & özelliği** ve **ağ geçidi komutları** arabirimleri etrafında yapılandırılır.

**Ağ geçidi telemetrisi & özelliği** -bu arabirim sensörler, konum ve cihaz bilgileriyle ilgili tüm telemetrileri tanımlar. Arabirim Ayrıca, algılayıcı eşikleri ve güncelleştirme aralıkları gibi cihaz ikizi Özellik özelliklerini de tanımlar.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png" alt-text="Telemetri ve özellik arabirimi":::

**Ağ geçidi komutları** -bu arabirim tüm ağ geçidi komut yeteneklerini düzenler:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png" alt-text="Ağ Geçidi komutları arabirimi":::

### <a name="rules"></a>Kurallar

Bu uygulama şablonundaki kuralların **kurallar** sekmesini seçin. Bu kurallar, daha fazla araştırma için işleçlere e-posta bildirimleri olarak yapılandırılmıştır:

**Ağ geçidi hırsızlığı uyarısı**: Bu kural, yolculuğun sırasında algılayıcılar tarafından beklenmeyen ışık algılaması olduğunda tetiklenir. Olası hırsızlık araştırmak için işleçlere hemen bildirilmesi gerekir.

**Yanıt vermeyen Ağ Geçidi**: Bu kural, ağ geçidi, uzun süreli bir süre için buluta rapor vermezse tetiklenir. Düşük pil, bağlantı kaybı veya cihaz bozulması nedeniyle ağ geçidi yanıt vermemeye başladı.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png" alt-text="Kural tanımları":::

### <a name="jobs"></a>İşler

Bu uygulamadaki işleri görmek için **işler** sekmesini seçin:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png" alt-text="Çalıştırılacak işler":::

İşleri uygulama genelinde işlemler yapmak için kullanabilirsiniz. Bu uygulamadaki işler, tüm ağ geçitlerinde belirli algılayıcıları devre dışı bırakma veya yükleme moduna ve rotaya göre algılayıcı eşiğini değiştirme gibi görevleri yapmak için cihaz komutlarını ve ikizi yeteneklerini kullanır:

* Bu, soğuk zincir ulaşım sırasında pili korumak veya daha düşük sıcaklık eşiğine karşı koruma sağlamak üzere okyanus sevkıyatlarının devre dışı bırakılması standart bir işlemdir.

* İşler, bakım etkinliklerinin güncel kalması için ağ geçitlerinde üretici yazılımı güncelleştirme veya hizmet sözleşmesini güncelleştirme gibi sistem genelinde işlemler yapabilmesini sağlar.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekiniz, **Yönetim**  >  **uygulaması ayarlarını** ziyaret ederek uygulama şablonunu silin ve **Sil**' i seçin.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png" alt-text="Şablon Temizleme":::

## <a name="next-steps"></a>Sonraki adımlar

Hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Bağlı lojistik kavramları](./architecture-connected-logistics.md)
