---
title: IoT akıllı envanter yönetimi öğreticisi | Microsoft Docs
description: IoT Central için akıllı envanter yönetimi uygulama şablonu öğreticisi
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: d72636265ff3ac654faba91d1420b502b35d3192
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889024"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Öğretici: akıllı envanter yönetimi uygulama şablonunu dağıtma ve yönetme

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu öğreticide, bir IoT Central **Smart Inventory Management** uygulama şablonu dağıtarak nasıl başlatılacağınız gösterilmektedir. Şablonu dağıtmayı, kutudan hangilerinin ekleneceğini ve daha sonra ne yapmak isteyebileceğiniz hakkında bilgi edineceksiniz.

Bu öğreticide, 
* Akıllı envanter yönetimi uygulaması oluştur 
* uygulamayı gözden geçir 

## <a name="prerequisites"></a>Ön koşullar
* Bu uygulamayı dağıtmak için belirli bir ön koşul gerekmez
* Azure aboneliğinin olması önerilir, ancak bunu yapmadan da deneyebilirsiniz

## <a name="create-smart-inventory-management-application-template"></a>Akıllı envanter yönetimi uygulama şablonu oluştur

Aşağıdaki adımları kullanarak uygulama oluşturabilirsiniz
1. Azure IoT Central uygulama Yöneticisi Web sitesine gidin. Sol taraftaki Gezinti çubuğundan **Oluştur** ' u seçin ve ardından **Perakende** sekmesine tıklayın.

> [!div class="mx-imgBorder"]
> Akıllı envanter yönetimi panosu ![](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. **Perakende** sekmesini seçin ve * * akıllı envanter yönetimi altında **uygulama oluştur** ' u seçin.

3. **Uygulama oluştur** yeni uygulama formunu açar ve istenen ayrıntıları aşağıda göster olarak doldurur.
   **Uygulama adı**: varsayılan önerilen adı kullanabilir veya kolay uygulamanızın adını girebilirsiniz.
   **URL**: önerilen varsayılan URL 'yi kullanabilir veya kolay eşsiz bir URL 'yi girebilirsiniz. Daha önce, zaten bir Azure aboneliğiniz varsa varsayılan ayar önerilir, aksi takdirde yedi günlük ücretsiz denemeye başlayabilir ve ücretsiz izleme süre sonundan önce istediğiniz zaman Kullandıkça Öde 'yi dönüştürebilirsiniz.
   **Fatura bilgileri**: kaynakları sağlamak için dizin, Azure aboneliği ve bölge ayrıntıları gereklidir.
   **Oluştur**: uygulamanızı dağıtmak için sayfanın alt kısmındaki Oluştur ' u seçin.

> [!div class="mx-imgBorder"]
> Akıllı envanter yönetimi panosu ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

## <a name="walk-through-the-application"></a>uygulamayı gözden geçir 

### <a name="dashboard"></a>Pano 
Uygulama şablonunu başarılı bir şekilde dağıttıktan sonra, varsayılan panonuz bir akıllı envanter yönetimi işletmen odaklı portaldır. Northwind Trader, Bluetooth düşük enerji (BLE) ve radyo frekansı tanımlama (RFıD) ile perakende mağaza ile ambarı yöneten kurgusal bir akıllı envanter sağlayıcısıdır. Bu panoda, envanter hakkında, gerçekleştirebileceğiniz ilgili komutlar, işler ve eylemlerle birlikte telemetri sağlayan iki farklı ağ geçidi görürsünüz. Bu Pano, kritik akıllı envanter yönetimi cihaz işlemleri etkinliğini göstermek üzere önceden yapılandırılmıştır.
Pano, iki farklı ağ geçidi cihaz yönetim işlemi arasında mantıksal olarak bölünür, 
   * Ambar, daha büyük bir tesiste & izleme envanterini izlemek için paletlerdeki sabit bir BLE ağ geçidi & BLE etiketleriyle dağıtılır
   * Perakende mağaza, bir mağaza prizine ait stoğu izlemek ve izlemek için bireysel bir öğe düzeyindeki & RFıD etiketleriyle birlikte bir sabit RFıD ağ geçidi ile uygulanır
   * Ağ Geçidi konumunu, durum & ilgili ayrıntıları görüntüleyin 

> [!div class="mx-imgBorder"]
> Akıllı envanter yönetimi panosu ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Ağ geçitlerinin, etkin ve bilinmeyen etiketlerin toplam sayısını kolayca izleyebilirsiniz.
   * Üretici yazılımı güncelleştirme, algılayıcıyı devre dışı bırakma, algılayıcı etkinleştirme, algılayıcı eşiğini güncelleştirme, telemetri aralıklarını güncelleştirme & cihaz hizmeti sözleşmelerini güncelleştirme gibi cihaz yönetim işlemleri gerçekleştirebilirsiniz
   * Ağ Geçidi cihazları, tam veya artımlı tarama ile isteğe bağlı envanter yönetimi gerçekleştirebilir.

> [!div class="mx-imgBorder"]
> Akıllı envanter yönetimi panosu ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Cihaz şablonu
Cihaz şablonları sekmesine tıklayın ve ağ geçidi yetenek modelini görürsünüz. Yetenek modeli iki farklı arabirim etrafında yapılandırılır **& özelliği** ve **ağ geçidi komutları**

**Ağ geçidi telemetrisi & özelliği** -bu arabirim, sensörler, konum, cihaz bilgileri ve ağ geçidi eşikleri & güncelleştirme aralıkları gibi cihaz ikizi Özellik yeteneği ile ilgili tüm Telemetriyi temsil eder.

> [!div class="mx-imgBorder"]
> Akıllı envanter yönetimi panosu ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Ağ geçidi komutları** -bu arabirim tüm ağ geçidi komut yeteneklerini düzenler

> [!div class="mx-imgBorder"]
> Akıllı envanter yönetimi panosu ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Kurallar
Bu uygulama şablonunda bulunan iki farklı kuralı görmek için kurallar sekmesini seçin. Bu kurallar, daha fazla araştırma için işleçlere e-posta bildirimleri olarak yapılandırılmıştır.

**Ağ geçidi çevrimdışı**: Bu kural, ağ geçidi, uzun süreli bir süre için buluta bildirmezse tetiklenir. Düşük pil modu, bağlantı kaybı, cihaz sistem durumu nedeniyle ağ geçidi yanıt vermemeye başladı.

**Bilinmeyen Etiketler**: bir varlıkla ILIŞKILI her RFıD & ble etiketini izlemek kritik öneme sahiptir. Ağ Geçidi çok sayıda bilinmeyen etiketi tespit ediyorsanız, bu, etiket kaynağı oluşturma uygulamaları ile ilgili eşitleme güçlüklerinin göstergesidir.

> [!div class="mx-imgBorder"]
> Akıllı envanter yönetimi panosu ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>İşler
Bu uygulama şablonunun bir parçası olarak var olan beş farklı işi görmek için işler sekmesini seçin: çözüm genelinde işlemler gerçekleştirmek için işler özelliğinden yararlanabilirsiniz. Burada stok yönetimi işleri, gibi görevleri gerçekleştirmek için ikizi özelliğini & cihaz komutlarını kullanıyor.
   * Tüm ağ geçidi genelinde okuyucuları devre dışı bırakma
   * arasındaki telemetri eşiğini değiştirme 
   * tüm çözüm genelinde talep üzerine envanter taraması gerçekleştirin.

> [!div class="mx-imgBorder"]
> Akıllı envanter yönetimi panosu ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekiniz, **yönetim** > **uygulama ayarlarını** ziyaret ederek uygulama şablonunu silin ve **Sil**' e tıklayın.

> [!div class="mx-imgBorder"]
> Akıllı envanter yönetimi panosu ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Sonraki adımlar
* Akıllı envanter yönetimi [akıllı envanter yönetimi kavramı](./architecture-smart-inventory-management-pnp.md) hakkında daha fazla bilgi edinin
* Diğer [IoT Central perakende şablonları](./overview-iot-central-retail-pnp.md) hakkında daha fazla bilgi edinin
* IoT Central hakkında daha fazla bilgi edinin [IoT Central genel bakış](../preview/overview-iot-central.md)
