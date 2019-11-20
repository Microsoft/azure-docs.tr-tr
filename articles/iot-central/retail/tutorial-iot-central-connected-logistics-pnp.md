---
title: IoT bağlı lojistik öğreticisi | Microsoft Docs
description: IoT Central için bağlı lojistik uygulama şablonu öğreticisi
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 17d429444b81746a5c5082b8310c5208bc35f3d2
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889037"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Öğretici: bağlı bir lojistik uygulama şablonunu dağıtma ve gözden geçir

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu öğreticide, IoT Central **bağlı bir lojistik** uygulama şablonunu dağıtmaya nasıl başlacağınız gösterilmektedir. Şablonu dağıtmayı, kutudan hangilerinin ekleneceğini ve daha sonra ne yapmak isteyebileceğiniz hakkında bilgi edineceksiniz.

Bu öğreticide, 
* bağlı lojistik uygulaması oluştur 
* uygulamayı gözden geçir 

## <a name="prerequisites"></a>Ön koşullar
* Bu uygulamayı dağıtmak için belirli bir ön koşul gerekmez
* Azure aboneliğinin olması önerilir, ancak bunu yapmadan da deneyebilirsiniz

## <a name="create-connected-logistics-application-template"></a>Bağlı lojistik uygulama şablonu oluştur
Aşağıdaki adımları kullanarak uygulama oluşturabilirsiniz
1. Azure IoT Central uygulama Yöneticisi Web sitesine gidin. Sol taraftaki Gezinti çubuğundan **Oluştur** ' u seçin ve ardından **Perakende** sekmesine tıklayın.

> [!div class="mx-imgBorder"]
> ![bağlı lojistik panosu](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. **Bağlı lojistik uygulaması** altında **uygulama oluştur** ' u seçin

3. **Uygulama oluştur** yeni uygulama formunu açar ve istenen ayrıntıları aşağıda göster olarak doldurur.
   * **Uygulama adı**: varsayılan önerilen adı kullanabilir veya kolay uygulamanızın adını girebilirsiniz.
   * **URL**: önerilen varsayılan URL 'yi kullanabilir veya kolay eşsiz bir URL 'yi girebilirsiniz. Daha önce, zaten bir Azure aboneliğiniz varsa varsayılan ayar önerilir. Aksi takdirde, yedi günlük ücretsiz denemeye başlayabilir ve ücretsiz izleme süre sonundan önce istediğiniz zaman Kullandıkça Öde 'yi dönüştürebilirsiniz.
   * **Fatura bilgileri**: kaynakları sağlamak için dizin, Azure aboneliği ve bölge ayrıntıları gereklidir.
   * **Oluştur** : uygulamanızı dağıtmak için sayfanın alt kısmındaki Oluştur ' u seçin.

> [!div class="mx-imgBorder"]
> ![bağlı lojistik panosu](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

## <a name="walk-through-the-application"></a>uygulamayı gözden geçir 

## <a name="dashboard"></a>Pano

Uygulama şablonunu başarıyla dağıttıktan sonra, varsayılan panonuz bağlı bir lojistik operatör odaklı portaldır. Northwind Trader, okyanus ve arazi içinde Kago 'nun yakıt düzeyini yöneten kurgusal bir lojistik sağlayıcıdır. Bu panoda, ilgili komutlar, işler ve gerçekleştirebileceğiniz eylemlerle birlikte sevkiyatlar hakkında telemetri sağlayan iki farklı ağ geçidi görürsünüz. Bu Pano, kritik lojistik cihaz işlemleri etkinliğini göstermek için önceden yapılandırılmıştır.
Pano, iki farklı ağ geçidi cihaz yönetim işlemi arasında mantıksal olarak bölünür, 
   * Okyanus Sevkiyat ve konum ayrıntıları için lojistik rotası, tüm çok kalıcı ulaşım için önemli bir öğedir
   * İlgili bilgileri & ağ geçidi durumunu görüntüleyin 

> [!div class="mx-imgBorder"]
> ![bağlı lojistik panosu](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * Ağ geçitlerinin, etkin ve bilinmeyen etiketlerin toplam sayısını kolayca izleyebilirsiniz.
   * Üretici yazılımı güncelleştirme, algılayıcıyı devre dışı bırakma, algılayıcı etkinleştirme, algılayıcı eşiğini güncelleştirme, telemetri aralıklarını güncelleştirme, & güncelleştirme cihaz hizmeti sözleşmeleri gibi cihaz yönetim işlemlerini gerçekleştirebilirsiniz.
   * Cihaz pil tüketimini görüntüleme

> [!div class="mx-imgBorder"]
> ![bağlı lojistik panosu](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Cihaz şablonu

Cihaz şablonları sekmesine tıklayın ve ağ geçidi yetenek modelini görürsünüz. Yetenek modeli iki farklı arabirim etrafında yapılandırılır **& özelliği** ve **ağ geçidi komutları**

**Ağ geçidi telemetrisi & özelliği** -bu arabirim, sensör, konum ve cihaz bilgileri ile ilgili tüm Telemetriyi ve algılayıcı eşikleri & güncelleştirme aralıkları gibi cihaz ikizi özellik özelliğini temsil eder.

> [!div class="mx-imgBorder"]
> ![bağlı lojistik panosu](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Ağ geçidi komutları** -bu arabirim tüm ağ geçidi komut yeteneklerini düzenler

> [!div class="mx-imgBorder"]
> ![bağlı lojistik panosu](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Kurallar
Bu uygulama şablonunda bulunan iki farklı kuralı görmek için kurallar sekmesini seçin. Bu kurallar, daha fazla araştırma için işleçlere e-posta bildirimleri olarak yapılandırılmıştır.
 
**Ağ geçidi hırsızlığı uyarısı**: Bu kural, yolculuğun sırasında sensörler tarafından beklenmeyen hafif algılama olduğunda tetiklenir. Olası hırsızlık araştırmak için operatörlerin ASAP olarak bildirilmesi gerekir.
 
**Yanıt vermeyen Ağ Geçidi**: Bu kural, ağ geçidi, uzun süreli bir süre için buluta rapor vermezse tetiklenir. Düşük pil modu, bağlantı kaybı, cihaz sistem durumu nedeniyle ağ geçidi yanıt vermemeye başladı.

> [!div class="mx-imgBorder"]
> ![bağlı lojistik panosu](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>İşler
Bu uygulama şablonunun bir parçası olarak var olan beş farklı işi görmek için işler sekmesini seçin:

> [!div class="mx-imgBorder"]
> ![bağlı lojistik panosu](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

Çözüm genelinde işlemler gerçekleştirmek için işler özelliğinden yararlanabilirsiniz. Burada işler, tüm ağ geçitlerinde belirli algılayıcıları devre dışı bırakma veya sevkiyat moduna ve rotaya göre algılayıcı eşiğini değiştirme gibi görevleri gerçekleştirmek için & ikizi özelliğini kullanarak cihaz komutlarını kullanıyor. 
   * Bu, soğuk zincir ulaşım sırasında pili korumak veya sıcaklık eşiğini azaltmak üzere okyanus sevkiyat sırasında darbe algılayıcılarının devre dışı bırakılması için standart bir işlemdir. 
 
   * İşler, bakım etkinliklerinin güncel kalması için ağ geçitlerinde üretici yazılımı güncelleştirme veya hizmet sözleşmesini güncelleştirme gibi sistem genelinde işlemler gerçekleştirmenize olanak sağlar.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu uygulamayı kullanmaya devam edemeyecekiniz, **yönetim** > **uygulama ayarlarını** ziyaret ederek uygulama şablonunu silin ve **Sil**' e tıklayın.

> [!div class="mx-imgBorder"]
> ![bağlı lojistik panosu](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>Sonraki adımlar
* [Bağlı lojistik kavramı](./architecture-connected-logistics-pnp.md) hakkında daha fazla bilgi edinin
* Diğer [IoT Central perakende şablonları](./overview-iot-central-retail-pnp.md) hakkında daha fazla bilgi edinin
* [IoT Central genel bakış](../preview/overview-iot-central.md) hakkında daha fazla bilgi edinin
