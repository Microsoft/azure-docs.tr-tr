---
title: IoT dijital dağıtım merkezi öğreticisi | Microsoft Docs
description: IoT Central için dijital dağıtım merkezi uygulama şablonu öğreticisi
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 879e1911c715688c2fed4044e23f437b3bbef7b2
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890669"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Öğretici: dijital bir dağıtım merkezi uygulama şablonunu dağıtma ve gözden geçir

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu öğreticide, bir IoT Central **Digital Distribution Center** uygulama şablonu dağıtmaya nasıl başlacağınız gösterilmektedir. Şablonu dağıtmayı, kutudan hangilerinin ekleneceğini ve daha sonra ne yapmak isteyebileceğiniz hakkında bilgi edineceksiniz.

Bu öğreticide, 
* Dijital dağıtım merkezi uygulaması oluştur 
* uygulamayı gözden geçir 

## <a name="prerequisites"></a>Ön koşullar
* Bu uygulamayı dağıtmak için belirli bir ön koşul gerekmez
* Azure aboneliğinin olması önerilir, ancak bunu yapmadan da deneyebilirsiniz

## <a name="create-digital-distribution-center-application-template"></a>Dijital dağıtım merkezi uygulama şablonu oluştur

Aşağıdaki adımları kullanarak uygulama oluşturabilirsiniz

1. Azure IoT Central uygulama Yöneticisi Web sitesine gidin. Sol taraftaki Gezinti çubuğundan **Oluştur** ' u seçin ve ardından **Perakende** sekmesine tıklayın.

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. **Perakende** sekmesini seçin ve * * dijital dağıtım merkezi uygulaması bölümünde **uygulama oluştur** ' u seçin.

3. **Uygulama oluştur** yeni uygulama formunu açar ve istenen ayrıntıları aşağıda göster olarak doldurur.
   **Uygulama adı**: varsayılan önerilen adı kullanabilir veya kolay uygulamanızın adını girebilirsiniz.
   **URL**: önerilen varsayılan URL 'yi kullanabilir veya kolay eşsiz, HATıRLAYABILECEĞINIZ URL 'yi girebilirsiniz. Daha sonra, zaten bir Azure aboneliğiniz varsa varsayılan ayar önerilir. Bundan sonra yedi günlük ücretsiz denemeye başlayabilir ve ücretsiz izleme süre sonundan önce istediğiniz zaman Kullandıkça Öde 'yi dönüştürmeyi seçebilirsiniz.
   **Fatura bilgileri**: kaynakları sağlamak için dizin, Azure aboneliği ve bölge ayrıntıları gereklidir.
   **Oluştur**: uygulamanızı dağıtmak için sayfanın alt kısmındaki Oluştur ' u seçin.

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-create.png)

## <a name="walk-through-the-application-dashboard"></a>Uygulama panosu üzerinden ilerleme 

Uygulama şablonunu başarıyla dağıttıktan sonra, varsayılan panonuz bir dağıtım merkezi işletmen odaklı portaldır. Northwind Trader, taşıyıcı sistemleri yöneten kurgusal bir dağıtım merkezi çözüm sağlayıcısıdır. 

Bu panoda, bir ağ geçidi ve IoT cihazı olarak davranan bir kamera görürsünüz. Ağ Geçidi, ilişkili cihaz ikizi özellikleriyle birlikte geçerli, geçersiz, tanımlanmamış ve boyut gibi paketlere yönelik telemetri sağlıyor. Tüm aşağı akış komutları, kamera gibi IoT cihazlarında yürütülür. Bu Pano, kritik dağıtım merkezi cihaz işlemleri etkinliğini göstermek için önceden yapılandırılmıştır.

Pano, Azure IoT Ağ Geçidi ve IoT cihazının cihaz yönetimi yeteneklerini göstermek üzere mantıksal olarak düzenlenir.  
   * Denetim görevlerini & ağ geçidi komutu yapabilirsiniz
   * Çözümün parçası olan tüm kameraları yönetin. 

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Cihaz şablonu

Cihaz şablonları sekmesine tıklayın ve ağ geçidi yetenek modelini görürsünüz. Yetenek modeli iki farklı arabirim etrafında yapılandırılır **Kamera** ve **dijital dağıtım ağ geçidi**

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Kamera** -bu arabirim, kameraya özgü tüm komut yeteneklerini düzenler 

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Dijital dağıtım ağ geçidi** -bu arabirim, kameradan gelen tüm Telemetriyi, bulut tanımlı cihaz ikizi özellikleri ve ağ geçidi bilgilerini temsil eder.

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Ağ Geçidi komutları
Bu arabirim tüm ağ geçidi komut yeteneklerini düzenler

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Kurallar
Bu uygulama şablonunda bulunan iki farklı kuralı görmek için kurallar sekmesini seçin. Bu kurallar, daha fazla araştırma için işleçlere e-posta bildirimleri olarak yapılandırılmıştır.

 **Çok fazla geçersiz paket uyarısı** -bu kural, kamera taşıyıcı sistem aracılığıyla akış sayısını yüksek sayıda geçersiz paketler algıladığında tetiklenir.
 
**Büyük paket** -bu kural, kameranın kalite için incelenemeyecek çok büyük bir paket algıladığında tetiklenecek. 

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>İşler
Bu uygulama şablonunun bir parçası olarak var olan beş farklı işi görmek için işler sekmesini seçin: çözüm genelinde işlemler gerçekleştirmek için işler özelliğinden yararlanabilirsiniz. Burada dijital dağıtım merkezi işleri, gibi görevleri gerçekleştirmek için ikizi özelliğini & cihaz komutlarını kullanıyor.
   * paket algılamayı başlatmadan önce kamerayı ayarlama 
   * Kamera bellenimini düzenli olarak güncelleştirme
   * veri yüklemeyi yönetmek için telemetri aralığını değiştirme

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu uygulamayı kullanmaya devam edemeyecekiniz, **yönetim** > **uygulama ayarlarını** ziyaret ederek uygulama şablonunu silin ve **Sil**' e tıklayın.

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Sonraki adımlar
* Dijital dağıtım merkezi çözüm mimarisi [dijital dağıtım merkezi kavramı](./architecture-digital-distribution-center-pnp.md) hakkında daha fazla bilgi edinin
* Diğer [IoT Central perakende şablonları](./overview-iot-central-retail-pnp.md) hakkında daha fazla bilgi edinin
* IoT Central hakkında daha fazla bilgi edinin [IoT Central genel bakış](../preview/overview-iot-central.md)
