---
title: IoT akıllı envanter yönetimi öğreticisi | Microsoft Docs
description: IoT Central için akıllı envanter yönetimi uygulama şablonu öğreticisi
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 3dbb6ca64451cb60d5a8ec67ecdc528865a4438c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719092"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Öğretici: akıllı envanter yönetimi uygulama şablonunu dağıtma ve yönetme

Bu öğreticide, bir IoT Central **Smart Inventory Management** uygulama şablonu dağıtarak nasıl başlatılacağınız gösterilmektedir. Şablonu dağıtmayı, kutudan hangilerinin ekleneceğini ve daha sonra ne yapmak isteyebileceğiniz hakkında bilgi edineceksiniz.

Bu öğreticide,

> [!div class="checklist"]
> * Akıllı envanter yönetimi uygulaması oluştur 
> * uygulamayı gözden geçir 

## <a name="prerequisites"></a>Önkoşullar

* Bu uygulamayı dağıtmak için belirli bir ön koşul gerekmez
* Azure aboneliğinin olması önerilir, ancak bunu yapmadan da deneyebilirsiniz

## <a name="create-smart-inventory-management-application-template"></a>Akıllı envanter yönetimi uygulama şablonu oluştur

Aşağıdaki adımları kullanarak uygulama oluşturabilirsiniz

1. Azure IoT Central uygulama Yöneticisi Web sitesine gidin. Sol taraftaki Gezinti çubuğundan **Oluştur** ' u seçin ve ardından **Perakende** sekmesine tıklayın.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png" alt-text="Akıllı envanter yönetimi uygulama şablonunun nasıl seçileceğini gösteren ekran görüntüsü":::

2. **Perakende** sekmesini seçin ve **akıllı envanter yönetimi** altında **uygulama oluştur** ' u seçin.

3. **Uygulama oluştur** yeni uygulama formunu açar ve istenen ayrıntıları aşağıda göster olarak doldurur.
    **Uygulama adı**: varsayılan önerilen adı kullanabilir veya kolay uygulamanızın adını girebilirsiniz.
    **URL**: önerilen varsayılan URL 'yi kullanabilir veya kolay eşsiz bir URL 'yi girebilirsiniz. Daha sonra, zaten bir Azure aboneliğiniz varsa varsayılan ayar önerilir. 7 günlük ücretsiz deneme fiyatlandırma planıyla başlayabilir ve ücretsiz izleme süresi dolmadan önce istediğiniz zaman standart bir fiyatlandırma planına dönüştürmeyi tercih edebilirsiniz.
    **Fatura bilgileri**: kaynakları sağlamak için dizin, Azure aboneliği ve bölge ayrıntıları gereklidir.
    **Oluştur**: uygulamanızı dağıtmak için sayfanın alt kısmındaki Oluştur ' u seçin.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png" alt-text="Akıllı envanter yönetimi uygulama şablonundan uygulama oluşturmayı gösteren ekran görüntüsü":::

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png" alt-text="Uygulamayı oluştururken faturalandırma seçeneklerini gösteren ekran görüntüsü":::

## <a name="walk-through-the-application"></a>Uygulamayı gözden geçir 

### <a name="dashboard"></a>Pano 

Uygulama şablonunu başarılı bir şekilde dağıttıktan sonra, varsayılan panonuz bir akıllı envanter yönetimi işletmen odaklı portaldır. Northwind Trader, Bluetooth düşük enerji (BLE) ve radyo frekansı tanımlama (RFıD) ile perakende mağaza ile ambarı yöneten kurgusal bir akıllı envanter sağlayıcısıdır. Bu panoda, envanter hakkında, gerçekleştirebileceğiniz ilgili komutlar, işler ve eylemlerle birlikte telemetri sağlayan iki farklı ağ geçidi görürsünüz. Bu Pano, kritik akıllı envanter yönetimi cihaz işlemleri etkinliğini göstermek üzere önceden yapılandırılmıştır.
Pano, iki farklı ağ geçidi cihaz yönetim işlemi arasında mantıksal olarak bölünür, 
   * Ambar, daha büyük bir tesiste & izleme envanterini izlemek için paletlerdeki sabit bir BLE ağ geçidi & BLE etiketleriyle dağıtılır
   * Perakende mağaza, bir mağaza prizine ait stoğu izlemek ve izlemek için bireysel bir öğe düzeyindeki & RFıD etiketleriyle birlikte bir sabit RFıD ağ geçidi ile uygulanır
   * Ağ Geçidi konumunu, durum & ilgili ayrıntıları görüntüleyin 

> [!div class="mx-imgBorder"]
> ![Smart Inventory managementdashboard 'in üst yarısını gösteren ekran görüntüsü](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Ağ geçitlerinin, etkin ve bilinmeyen etiketlerin toplam sayısını kolayca izleyebilirsiniz.
   * Üretici yazılımı güncelleştirme, algılayıcıyı devre dışı bırakma, algılayıcı etkinleştirme, algılayıcı eşiğini güncelleştirme, telemetri aralıklarını güncelleştirme & cihaz hizmeti sözleşmelerini güncelleştirme gibi cihaz yönetim işlemleri gerçekleştirebilirsiniz
   * Ağ Geçidi cihazları, tam veya artımlı tarama ile isteğe bağlı envanter yönetimi gerçekleştirebilir.

> [!div class="mx-imgBorder"]
> ![Smart Inventory managementdashboard 'in alt yarısını gösteren ekran görüntüsü](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Cihaz şablonu
Cihaz şablonları sekmesine tıklayın ve ağ geçidi yetenek modelini görürsünüz. Yetenek modeli iki farklı arabirim etrafında yapılandırılır **& özelliği** ve **ağ geçidi komutları**

**Ağ geçidi telemetrisi & özelliği** -bu arabirim, algılayıcı, konum, cihaz bilgileri ve ağ geçidi eşikleri ve güncelleştirme aralıkları gibi cihaz ikizi Özellik özellikleriyle ilgili tüm telemetrisi temsil eder.

> [!div class="mx-imgBorder"]
> ![Uygulamadaki Inventory Gateway cihaz şablonunu gösteren ekran görüntüsü](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Ağ geçidi komutları** -bu arabirim tüm ağ geçidi komut yeteneklerini düzenler

> [!div class="mx-imgBorder"]
> ![Inventory Gateway cihaz şablonundaki ağ geçidi komutları arabirimini gösteren ekran görüntüsü](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Kurallar
Bu uygulama şablonunda bulunan iki farklı kuralı görmek için kurallar sekmesini seçin. Bu kurallar, daha fazla araştırma için işleçlere e-posta bildirimleri olarak yapılandırılmıştır.

**Ağ geçidi çevrimdışı**: Bu kural, ağ geçidi, uzun süreli bir süre için buluta rapor vermezse tetiklenir. Düşük pil modu, bağlantı kaybı, cihaz sistem durumu nedeniyle ağ geçidi yanıt vermemeye başladı.

**Bilinmeyen Etiketler**: bir varlıkla ILIŞKILI her RFıD & ble etiketini izlemek önemlidir. Ağ Geçidi çok sayıda bilinmeyen etiketi tespit ediyorsanız, Etiketler kaynak kaynağı oluşturma ile ilgili eşitleme güçlüklerinin bir göstergesi vardır.

> [!div class="mx-imgBorder"]
> ![Akıllı envanter managementapplication 'daki kuralların listesini gösteren ekran görüntüsü](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>İşler
Bu uygulama şablonunun bir parçası olarak var olan beş farklı işi görmek için işler sekmesini seçin: çözüm genelinde işlemler gerçekleştirmek için işler özelliğini kullanabilirsiniz. Burada stok yönetimi işleri, gibi görevleri gerçekleştirmek için cihaz komutlarını ve ikizi özelliğini kullanıyor.
   * Tüm ağ geçidi genelinde okuyucuları devre dışı bırakma
   * arasındaki telemetri eşiğini değiştirme 
   * tüm çözüm genelinde talep üzerine envanter taraması gerçekleştirin.

> [!div class="mx-imgBorder"]
> ![Akıllı envanter managementapplication 'daki işlerin listesini gösteren ekran görüntüsü](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekiniz, **Yönetim** uygulaması ayarlarını ziyaret ederek uygulama şablonunu silin  >   ve **Sil**' e tıklayın.

> [!div class="mx-imgBorder"]
> ![Uygulamayı tamamladığınızda uygulamanın nasıl silineceğini gösteren ekran görüntüsü](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Sonraki adımlar

Akıllı envanter yönetimi hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Akıllı envanter yönetimi kavramı](./architecture-smart-inventory-management.md)
