---
title: Azure IoT Central uygulamanıza görüntü yükleme | Microsoft Docs
description: Bir Oluşturucu olarak, Azure IoT Central uygulamanıza görüntü hazırlama ve yükleme hakkında bilgi edinin.
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d8a863ee971fa154740f3726034fc4604b02fd34
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949930"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Görüntüleri Azure IoT Central uygulamanıza hazırlayın ve karşıya yükleyin

Bu makalede, bir Oluşturucu olarak, özel görüntüleri karşıya yükleyerek Azure IoT Central uygulamanızı nasıl özelleştirebileceğinizi açıklar. Örneğin, cihaz panosunu cihazın bir resmiyle özelleştirebilirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaledeki adımları tamamlayabilmeniz için şunlar gereklidir:

1. Azure IoT Central uygulaması. Daha fazla bilgi için bkz. [Uygulama oluşturma hızlı başlangıcı](quick-deploy-iot-central.md).
1. Görüntü dosyalarını ölçeklendirmeye ve yeniden boyutlandırmayla ilgili bir araç.

## <a name="choose-where-to-use-custom-images"></a>Özel görüntülerin nerede kullanılacağını seçin

Azure IoT Central uygulamasında aşağıdaki konumlara özel görüntüler ekleyebilirsiniz:

* **Uygulamalarım** sayfası

    ![Uygulama Yöneticisi sayfasında görüntü](media/howto-prepare-images/applicationmanager.png)

* Uygulama panosu

    ![Uygulama panosundaki görüntü](media/howto-prepare-images/homepage.png)

* Bir cihaz şablonu

    ![Cihaz şablonundaki resim](media/howto-prepare-images/devicetemplate.png)

* Cihaz panosundaki kutucuk

    ![Cihaz kutucuğunda görüntü](media/howto-prepare-images/devicetile.png)

* Cihaz kümesi panosu üzerindeki kutucuk

    ![Cihaz kümesi kutucuğunda görüntü](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Görüntüleri hazırlama

Dört konumda da PNG, GIF veya JPEG görüntülerini kullanabilirsiniz.

Aşağıdaki tabloda kullanabileceğiniz görüntü boyutları özetlenmektedir:

| Konum | Boyutlar |
| -------- | ------ |
| Uygulama Yöneticisi | 268x160 piksel |
| Cihaz şablonu | 64x64 piksel |
| Pano kutucukları | En küçük boyutsuz kutucuk 200x200 px, daha büyük kutucuklar ise kare veya küçük döşemelerin dikdörtgen katları olabilir. Örneğin, 200x400 px, 400x200 px veya 400x400 px |

Uygulamadaki en iyi ekran için, önceki tabloda gösterilen boyutlarla eşleşen görüntüler oluşturmanız gerekir.

## <a name="upload-the-images"></a>Görüntüleri karşıya yükleme

Aşağıdaki bölümlerde görüntülerin farklı konumlara nasıl yükleneceği açıklanır:

### <a name="application-manager"></a>Uygulama Yöneticisi

**Uygulamalarım** sayfasında kullanmak üzere bir görüntü yüklemek Için, **Yönetim** bölümündeki **uygulama ayarları** sayfasına gidin. Bu görevi gerçekleştirmek için yönetici olmanız gerekir:

![Uygulama görüntüsünü karşıya yükle](media/howto-prepare-images/uploadapplicationmanager.png)

Yerel makinenizden bir görüntü (268x160 piksel) yüklemek için **Uygulama görüntüsü** kutucuğunu seçin.

### <a name="application-dashboard"></a>Uygulama panosu

Uygulama panosuna bir görüntü yüklemek için uygulamanızın **Pano** sayfasına gidin ve **Düzenle**' yi seçin. Bu görevi gerçekleştirmek için bir Oluşturucu olmanız gerekir:

![Pano görüntüsünü karşıya yükle](media/howto-prepare-images/uploadhomepage.png)

**Görüntüyü Yapılandır**altında, yerel makinenizden bir görüntü yüklemek için **görüntü** kutucuğunu seçin. En küçük boyutsuz kutucuk 200x200 px, daha büyük kutucuklar ise kare veya küçük döşemelerin dikdörtgen katları olabilir. Örneğin, 200x400 px, 400x200 px veya 400x400 px.

Karşıya yüklenen görüntüyü **kaydedin** . Düzenleme modundayken yeniden boyutlandırabilirsiniz. Bittiğinde **bitti** ' yi seçin.

### <a name="device-template"></a>Cihaz şablonu

Bir cihaz şablonuna bir görüntü yüklemek için **cihaz şablonları** ' na gidin ve cihaz şablonunu seçin. Bu görevi gerçekleştirmek için bir Oluşturucu olmanız gerekir:

![Cihaz şablonu görüntüsünü karşıya yükle](media/howto-prepare-images/uploaddevicetemplate.png)

Yerel makinenizden bir görüntü (64x64 px) yüklemek için görüntü kutucuğunu seçin.

### <a name="device-dashboard"></a>Cihaz panosu

Bir cihaz panosuna bir görüntü yüklemek için **cihaz şablonları** ' na gidin ve cihaz şablonunu seçin. Ardından **Pano** sekmesini seçin. Bu görevi gerçekleştirmek için bir Oluşturucu olmanız gerekir:

![Cihaz panosu görüntüsünü karşıya yükle](media/howto-prepare-images/uploaddevicedashboard.png)

**Görüntüyü Yapılandır**altında **görüntü** kutucuğunu seçin ve ardından yerel makinenizden karşıya yüklenecek dosyayı seçin. En küçük boyutsuz kutucuk 200x200 px, daha büyük kutucuklar ise kare veya küçük döşemelerin dikdörtgen katları olabilir. Örneğin, 200x400 px, 400x200 px veya 400x400 px.

Karşıya yüklenen görüntüyü **kaydedin** . Düzenleme modundayken yeniden boyutlandırabilir ve yeniden konumlandırabilirsiniz. Bittiğinde **bitti** ' yi seçin.

### <a name="device-set-dashboard"></a>Cihaz kümesi panosu

Bir cihaz kümesi panosuna bir görüntü yüklemek için **cihaz kümeleri** ' ne gidin ve cihaz kümesini ve ardından bir cihazı seçin. Sonra **Pano** sayfasını seçin ve **Düzenle**' yi seçin:

![Cihaz kümesi Pano görüntüsünü karşıya yükle](media/howto-prepare-images/uploaddevicesetdashboard.png)

**Görüntüyü Yapılandır**altında, yerel makinenizden bir görüntü yüklemek için **görüntü** kutucuğunu seçin. En küçük boyutsuz kutucuk 200x200 px, daha büyük kutucuklar ise kare veya küçük döşemelerin dikdörtgen katları olabilir. Örneğin, 200x400 px, 400x200 px veya 400x400 px.

Karşıya yüklenen görüntüyü **kaydedin** . Düzenleme modundayken yeniden boyutlandırabilir ve yeniden konumlandırabilirsiniz. Bittiğinde **bitti** ' yi seçin.

## <a name="next-steps"></a>Sonraki adımlar

Artık görüntüleri Azure IoT Central uygulamanıza hazırlama ve yükleme hakkında daha fazla öğrendiğinize göre, önerilen sonraki adımlar aşağıda verilmiştir:

* [Azure IoT Central kullanıcı arabirimini özelleştirme](./howto-customize-ui.md)
* [Panonuza kutucuk ekleme](./howto-add-tiles-to-your-dashboard.md)
* [Azure IoT Central uygulamanızda cihazları yönetme](howto-manage-devices.md)
