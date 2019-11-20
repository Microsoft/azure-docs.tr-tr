---
title: Azure portal IoT Central Yönet | Microsoft Docs
description: Bu makalede, Azure portal IoT Central uygulamalarınızın nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 59fc01920a94e8ab3e7037e4bb226ea18a4314bf
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73806502"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure portal IoT Central yönetme

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) web sitesinde IoT Central uygulamaları oluşturup yönetmek yerine uygulamalarınızı yönetmek için [Azure Portal](https://portal.azure.com) kullanabilirsiniz.

## <a name="create-iot-central-applications"></a>IoT Central uygulamaları oluşturma

Bir uygulama oluşturmak için [Azure Portal](https://ms.portal.azure.com) gidin ve soldaki ana bölmede **kaynak oluştur** ' u seçin.

![Yönetim Portalı: gezinti menüsü](media/howto-manage-iot-central-from-portal/image0.png)

Arama çubuğuna **IoT Central**yazın.

![Yönetim Portalı: arama](media/howto-manage-iot-central-from-portal/image0a1.png)

Arama sonuçlarındaki **uygulama satırı IoT Central** öğesini seçin.

![Yönetim Portalı: arama sonuçları](media/howto-manage-iot-central-from-portal/image0b1.png)

Şimdi **Oluştur**' u seçin.

![Yönetim Portalı: IoT Central kaynağı](media/howto-manage-iot-central-from-portal/image0c1.png)

Formdaki tüm alanları doldurur. Bu form, [Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) Web sitesinde uygulama oluşturmak için doldurduğunuz forma benzer. Daha fazla bilgi için [IoT Central uygulaması oluşturma](quick-deploy-iot-central.md) hızlı başlangıcı bölümüne bakın.

**Konum** , uygulamanızı oluşturmak istediğiniz fiziksel konum veya [Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/) . Genellikle en iyi performansı elde etmek için cihazlarınıza fiziksel olarak en yakın konumu seçmeniz gerekir. Azure IoT Central 'ın [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) sayfasında kullanılabildiği bölgeleri görebilirsiniz. Bir konum seçtikten sonra, uygulamanızı daha sonra farklı bir konuma taşıyamazsınız.

> [!NOTE]
> **Önizleme uygulaması** şablonu şu anda yalnızca **Kuzey Avrupa** ve **Orta ABD** bölgelerinde kullanılabilir.

![Yönetim Portalı: IoT Central kaynak oluşturma](media/howto-manage-iot-central-from-portal/image1a.png)  

Tüm alanları doldurduktan sonra **Oluştur**' u seçin.

## <a name="manage-existing-iot-central-applications"></a>Mevcut IoT Central uygulamalarını yönetme

Zaten bir Azure IoT Central uygulamanız varsa, bunu silebilir veya Azure portal farklı bir aboneliğe veya kaynak grubuna taşıyabilirsiniz.

> [!NOTE]
> Azure portal deneme uygulamalarını, aboneliğinizle ilişkili olmadıklarından göremezsiniz.

Başlamak için sol taraftaki ana bölmedeki **tüm kaynaklar** ' ı seçin. Kaynak listenizde bulmak için uygulamanızın adını yazmak üzere arama kutusunu kullanın. Ardından, yönetmek istediğiniz IoT Central uygulamayı seçin.

![Yönetim Portalı: kaynak yönetimi](media/howto-manage-iot-central-from-portal/image2a.png)

Uygulamaya gitmek için IoT Central uygulama URL 'sini seçin.

![Yönetim Portalı: kaynak yönetimi](media/howto-manage-iot-central-from-portal/image3.png)

Uygulamayı farklı bir kaynak grubuna taşımak için kaynak grubunun yanındaki **Değiştir** ' i seçin. **Kaynakları taşı** sayfasında, bu uygulamayı geçirmek istediğiniz kaynak grubunu seçin.

![Yönetim Portalı: kaynak yönetimi](media/howto-manage-iot-central-from-portal/image4a.png)

Uygulamayı farklı bir aboneliğe taşımak için aboneliğin yanındaki **değişiklik** bağlantısını seçin. Görüntülenen iletişim kutusunda bu uygulamayı geçirmek istediğiniz aboneliği seçin.

![Yönetim Portalı: kaynak yönetimi](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamalarını Azure portal nasıl yönetebileceğinizi öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Uygulamanızı yönetme](howto-administer.md)