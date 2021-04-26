---
title: Bağlı fabrika çözümünü özelleştirme-Azure | Microsoft Docs
description: Bağlı fabrika çözümü hızlandırıcısının davranışının nasıl özelleştirileceğine ilişkin bir açıklama.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: a092859517ee6ac41e62e55fe95f72b8bfdf87d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646253"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Bağlı fabrika çözümünün OPC UA sunucularınızdan verileri nasıl görüntülediğini özelleştirin

> [!IMPORTANT]
> Bu makaleyi güncelleştirdiğimiz sürece, en güncel içerik için bkz. [Azure endüstriyel IoT](https://azure.github.io/Industrial-IoT/) .

Bağlı fabrika çözümü, çözüme bağlı olan OPC UA sunucularından verileri toplar ve görüntüler. Çözümünüzdeki OPC UA sunucularına gözatabilir ve bu sunuculara komut gönderebilirsiniz. OPC UA hakkında daha fazla bilgi için bkz. [Bağlı Fabrika SSS](iot-accelerators-faq-cf.md).

Çözümdeki toplanan verilerin örnekleri, panoda, fabrika, hat ve istasyon düzeylerinde görüntüleyebileceğiniz genel ekipman verimliliği (OEE) ve ana performans göstergelerini (KPI 'Lar) içerir. Aşağıdaki ekran görüntüsünde, **Münih** Factory 'de **üretim satırı 1**' deki **derleme** İstasyonu için OEE ve KPI değerleri gösterilmektedir:

![Çözümdeki OEE ve KPI değerleri örneği][img-oee-kpi]

Çözüm, *istasyon* olarak adlandırılan OPC UA sunucularındaki belirli veri öğelerinden ayrıntılı bilgileri görüntülemenize olanak sağlar. Aşağıdaki ekran görüntüsünde, belirli bir istasyondan üretilmiş öğe sayısının çizimleri gösterilmektedir:

![Üretilmiş öğe sayısı çizimleri][img-manufactured-items]

Grafiklerinden birine tıklarsanız, Time Series Insights (TSI) kullanarak verileri daha fazla inceleyebilirsiniz:

![Time Series Insights kullanarak verileri araştırma][img-tsi]

Bu makalede şunları açıklanmaktadır:

- Veriler, çözümdeki çeşitli görünümlerde kullanılabilir hale getirilir.
- Çözümün verileri görüntüleme şeklini nasıl özelleştirebileceğinizi öğrenin.

## <a name="data-sources"></a>Veri kaynakları

Bağlı fabrika çözümü, çözüme bağlı olan OPC UA sunucularından verileri görüntüler. Varsayılan yükleme, fabrika simülasyonu çalıştıran birkaç OPC UA sunucusu içerir. Çözümünüze [bir ağ geçidi üzerinden bağlanan] [lnk-Connect-CF] olan kendi OPC UA sunucularınızı ekleyebilirsiniz.

Bağlı bir OPC UA sunucusunun, panoda çözümünüze gönderemediği veri öğelerine gözatabiliriz:

1. **BIR OPC UA sunucusu seçin** görünümüne gitmek için **tarayıcı** ' yı seçin:

    ![OPC UA sunucu görünümü seçin sayfasına gidin][img-select-server]

1. Bir sunucu seçin ve **Bağlan**' a tıklayın. Güvenlik Uyarısı göründüğünde **devam** ' a tıklayın.

    > [!NOTE]
    > Bu uyarı her bir sunucu için yalnızca bir kez görünür ve çözüm panosu ile sunucu arasında bir güven ilişkisi kurar.

1. Artık sunucunun çözüme gönderemediği veri öğelerine gözatabiliriz. Çözüme gönderilen öğeler bir onay işaretine sahiptir:

    ![Yayımlanan öğeler][img-published]

1. Çözümde *yöneticiyseniz* , bağlı fabrika çözümünde kullanılabilir hale getirmek için bir veri öğesi yayımlamayı seçebilirsiniz. Yönetici olarak, veri öğelerinin değerini ve OPC UA sunucusundaki çağrı yöntemlerini de değiştirebilirsiniz.

## <a name="map-the-data"></a>Verileri eşleme

Bağlı fabrika çözümü, yayımlanan veri öğelerini OPC UA sunucusundan çözümdeki çeşitli görünümlere eşleştirir ve toplar. Bağlı fabrika çözümü, çözümü sağladığınızda Azure hesabınıza dağıtılır. Visual Studio bağlı fabrika çözümünde bir JSON dosyası bu eşleme bilgilerini depolar. Bu JSON yapılandırma dosyasını bağlı fabrika Visual Studio çözümünde görüntüleyebilir ve değiştirebilirsiniz. Bir değişiklik yaptıktan sonra çözümü yeniden dağıtabilirsiniz.

Yapılandırma dosyasını şu şekilde kullanabilirsiniz:

- Mevcut sanal fabrikaları, üretim satırlarını ve istasyonları düzenleyin.
- Çözüme bağlandığınız gerçek OPC UA sunucularından verileri eşleyin.

Belirli gereksinimlerinizi karşılayacak verileri eşleme ve toplama hakkında daha fazla bilgi için bkz. [bağlı fabrika çözümü hızlandırıcıyı yapılandırma ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Değişiklikleri dağıtma

Dosyadaki **ContosoTopologyDescription.js** değişiklik yapmayı bitirdiğinizde, bağlı fabrika çözümünü Azure hesabınıza yeniden dağıtmanız gerekir.

**Azure IoT ile bağlantılı fabrika** deposu, çözümü yeniden derlemek ve dağıtmak için kullanabileceğiniz bir **build.ps1** PowerShell betiği içerir.

## <a name="next-steps"></a>Sonraki Adımlar

Aşağıdaki makaleleri okuyarak bağlı Factory Çözüm Hızlandırıcısı hakkında daha fazla bilgi edinin:

* [Azureiotsolutions.com sitesindeki izinler][lnk-permissions]
* [Bağlı Fabrika hakkında SSS](iot-accelerators-faq-cf.md)
* [SSS][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md