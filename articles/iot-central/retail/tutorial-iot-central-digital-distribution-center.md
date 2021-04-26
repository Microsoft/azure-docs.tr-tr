---
title: IoT dijital dağıtım merkezi öğreticisi | Microsoft Docs
description: IoT Central için dijital dağıtım merkezi uygulama şablonu öğreticisi
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 40b0d7eeb77f165c50c5470e1b5869e240d36534
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702700"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Öğretici: dijital bir dağıtım merkezi uygulama şablonunu dağıtma ve gözden geçir

Bu öğreticide, bir IoT Central **Digital Distribution Center** uygulama şablonu dağıtmaya nasıl başlacağınız gösterilmektedir. Şablonu dağıtmayı, kutudan hangilerinin ekleneceğini ve daha sonra ne yapmak isteyebileceğiniz hakkında bilgi edineceksiniz.

Bu öğreticide, 

> [!div class="checklist"]
> * Dijital dağıtım merkezi uygulaması oluştur 
> * Uygulamayı gözden geçir 

## <a name="prerequisites"></a>Önkoşullar
* Bu uygulamayı dağıtmak için belirli bir ön koşul gerekmez
* Azure aboneliğinin olması önerilir, ancak bunu yapmadan da deneyebilirsiniz

## <a name="create-digital-distribution-center-application-template"></a>Dijital dağıtım merkezi uygulama şablonu oluştur

Aşağıdaki adımları kullanarak uygulama oluşturabilirsiniz

1. Azure IoT Central uygulama Yöneticisi Web sitesine gidin. Sol taraftaki Gezinti çubuğundan **Oluştur** ' u seçin ve ardından **Perakende** sekmesine tıklayın.

    :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-homepage.png" alt-text="Dijital dağıtım merkezi uygulama şablonu":::
1. **Perakende** sekmesini seçin ve **dijital dağıtım merkezi uygulaması** altında **uygulama oluştur** ' u seçin.

1. **Uygulama oluştur** yeni uygulama formunu açar ve istenen ayrıntıları aşağıda göster olarak doldurur.
   **Uygulama adı**: varsayılan önerilen adı kullanabilir veya kolay uygulamanızın adını girebilirsiniz.
   **URL**: önerilen varsayılan URL 'yi kullanabilir veya kolay eşsiz bir URL 'yi girebilirsiniz. Daha sonra, zaten bir Azure aboneliğiniz varsa varsayılan ayar önerilir. 7 günlük ücretsiz deneme fiyatlandırma planıyla başlayabilir ve ücretsiz izleme süresi dolmadan önce istediğiniz zaman standart bir fiyatlandırma planına dönüştürmeyi tercih edebilirsiniz.
   **Fatura bilgileri**: kaynakları sağlamak için dizin, Azure aboneliği ve bölge ayrıntıları gereklidir.
   **Oluştur**: uygulamanızı dağıtmak için sayfanın alt kısmındaki Oluştur ' u seçin.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create.png" alt-text="Dijital dağıtım merkezi uygulama şablonundan uygulama oluşturmayı gösteren ekran görüntüsü":::

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create-billinginfo.png" alt-text="Uygulamayı oluştururken faturalandırma seçeneklerini gösteren ekran görüntüsü":::

## <a name="walk-through-the-application-dashboard"></a>Uygulama panosu üzerinden ilerleme 

Uygulama şablonunu başarıyla dağıttıktan sonra, varsayılan panonuz bir dağıtım merkezi işletmen odaklı portaldır. Northwind Trader, taşıyıcı sistemleri yöneten kurgusal bir dağıtım merkezi çözüm sağlayıcısıdır. 

Bu panoda, bir ağ geçidi ve IoT cihazı olarak davranan bir kamera görürsünüz. Ağ Geçidi, ilişkili cihaz ikizi özellikleriyle birlikte geçerli, geçersiz, tanımlanmamış ve boyut gibi paketlere yönelik telemetri sağlıyor. Tüm aşağı akış komutları, kamera gibi IoT cihazlarında yürütülür. Bu Pano, kritik dağıtım merkezi cihaz işlemleri etkinliğini göstermek için önceden yapılandırılmıştır.

Pano, Azure IoT Ağ Geçidi ve IoT cihazının cihaz yönetimi yeteneklerini göstermek üzere mantıksal olarak düzenlenir.  
   * Denetim görevlerini & ağ geçidi komutu yapabilirsiniz
   * Çözümün parçası olan tüm kameraları yönetin. 

> [!div class="mx-imgBorder"]
> ![Dijital dağıtım merkezi panosunu gösteren ekran görüntüsü](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Cihaz şablonu

Cihaz şablonları sekmesine tıklayın ve ağ geçidi yetenek modelini görürsünüz. Yetenek modeli iki farklı arabirim etrafında yapılandırılır **Kamera** ve **dijital dağıtım ağ geçidi**

> [!div class="mx-imgBorder"]
> ![Uygulamadaki dijital dağıtım ağ geçidi cihaz şablonunu gösteren ekran görüntüsü](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Kamera** -bu arabirim, kameraya özgü tüm komut yeteneklerini düzenler 

> [!div class="mx-imgBorder"]
> ![Dijital dağıtım ağ geçidi cihaz şablonunda kamera arabirimini gösteren ekran görüntüsü](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Dijital dağıtım ağ geçidi** -bu arabirim, kameradan gelen tüm Telemetriyi, bulut tanımlı cihaz ikizi özellikleri ve ağ geçidi bilgilerini temsil eder.

> [!div class="mx-imgBorder"]
> ![Dijital dağıtım ağ geçidi cihaz şablonundaki dijital dağıtım ağ geçidi arabirimini gösteren ekran görüntüsü](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Ağ Geçidi komutları
Bu arabirim tüm ağ geçidi komut yeteneklerini düzenler

> [!div class="mx-imgBorder"]
> ![Dijital dağıtım ağ geçidi cihaz şablonundaki ağ geçidi komutları arabirimini gösteren ekran görüntüsü](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Kurallar
Bu uygulama şablonunda bulunan iki farklı kuralı görmek için kurallar sekmesini seçin. Bu kurallar, daha fazla araştırma için işleçlere e-posta bildirimleri olarak yapılandırılmıştır.

 **Çok fazla geçersiz paket uyarısı** -bu kural, kamera taşıyıcı sistem aracılığıyla akış sayısını yüksek sayıda geçersiz paketler algıladığında tetiklenir.
 
**Büyük paket** -bu kural, kameranın kalite için incelenemeyecek çok büyük bir paket algıladığında tetiklenecek. 

> [!div class="mx-imgBorder"]
> ![Dijital dağıtım merkezi uygulamasındaki kuralların listesini gösteren ekran görüntüsü](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>İşler
Bu uygulama şablonunun bir parçası olarak var olan beş farklı işi görmek için işler sekmesini seçin: çözüm genelinde işlemler gerçekleştirmek için işler özelliğinden yararlanabilirsiniz. Burada dijital dağıtım merkezi işleri, gibi görevleri gerçekleştirmek için ikizi özelliğini & cihaz komutlarını kullanıyor.
   * paket algılamayı başlatmadan önce kamerayı ayarlama 
   * Kamera bellenimini düzenli olarak güncelleştirme
   * veri yüklemeyi yönetmek için telemetri aralığını değiştirme

> [!div class="mx-imgBorder"]
> ![Dijital dağıtım merkezi uygulamasındaki işlerin listesini gösteren ekran görüntüsü](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu uygulamayı kullanmaya devam edemeyecekiniz, **Yönetim** uygulaması ayarlarını ziyaret ederek uygulama şablonunu silin  >   ve **Sil**' e tıklayın.

> [!div class="mx-imgBorder"]
> ![Uygulamayı tamamladığınızda uygulamanın nasıl silineceğini gösteren ekran görüntüsü](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Sonraki adımlar

Dijital dağıtım merkezi çözüm mimarisi hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Dijital dağıtım merkezi kavramı](./architecture-digital-distribution-center.md)
