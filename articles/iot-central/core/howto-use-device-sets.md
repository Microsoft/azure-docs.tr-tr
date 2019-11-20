---
title: Azure IoT Central uygulamanızda cihaz kümelerini kullanma | Microsoft Docs
description: Bir operatör olarak, Azure IoT Central uygulamanızda cihaz kümelerini kullanma.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 87bbab041405a085d405707ff419fbad55fdcd09
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952764"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Azure IoT Central uygulamanızda cihaz kümelerini kullanma

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu makalede, Azure IoT Central uygulamanızda cihaz kümelerini kullanmak için bir operatör olarak nasıl kullanılacağı açıklanır.

Bir cihaz kümesi, belirtilen bazı ölçütlerle eşleştiğinden, birlikte gruplanmış cihazların bir listesidir. Cihaz kümeleri, cihazları daha küçük ve mantıksal gruplara gruplandırarak, cihazları ölçekli olarak yönetmenize, görselleştirmenize ve çözümlemenize yardımcı olur. Örneğin, bir teknisyenin sorumlu oldukları cihazları bulmasını sağlamak için Seattle 'daki tüm AIR klimaları cihazlarını listelemek üzere bir cihaz kümesi oluşturabilirsiniz. Bu makalede cihaz kümelerinin nasıl oluşturulacağı ve yapılandırılacağı gösterilmektedir.

## <a name="create-a-device-set"></a>Cihaz kümesi oluşturma

Bir cihaz kümesi oluşturmak için:

1. Sol bölmedeki **cihaz kümeleri** ' ni seçin.

1. **+ Yeni**seçeneğini belirleyin.

    ![Yeni cihaz kümesi](media/howto-use-device-sets/image1.png)

1. Cihazınızın tüm uygulama genelinde benzersiz bir ad vermesini sağlayın. Ayrıca, bir açıklama ekleyebilirsiniz. Cihaz kümesi yalnızca tek bir cihaz şablonundan cihaz içerebilir. Bu küme için kullanılacak cihaz şablonunu seçin.

1. Bir özellik, karşılaştırma operatörü ve bir değer seçerek cihaz kümesi için cihazları tanımlamak üzere sorgu oluşturma. Birden çok sorgu ekleyebilirsiniz ve **Tüm** ölçütlere uyan cihazlar cihaz kümesine yerleştirilir. Oluşturduğunuz cihaz kümesine, uygulamaya erişimi olan herkes tarafından erişilebilir. böylece, cihaz kümesini görüntüleyebilir, değiştirebilir veya silebilir.

    ![Cihaz kümesi sorgusu](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Cihaz kümesi dinamik bir sorgudur. Cihazların listesini her görüntülediğinizde, listede farklı cihazlar olabilir. Liste, şu anda sorgu ölçütlerine uyan cihazlara bağlıdır.

1. **Kaydet**'i seçin.

## <a name="configure-the-dashboard-for-your-device-set"></a>Cihaz kümesi için panoyu yapılandırın

Cihaz kümesini oluşturduktan sonra **panosunu**yapılandırabilirsiniz. **Pano** , görüntüleri ve bağlantıları yerleştirdiğiniz giriş sayfasıdır. Cihaz kümesindeki cihazları listelemek için de kılavuzlar ekleyebilirsiniz.

1. Sol bölmedeki **cihaz kümeleri** ' ni seçin.

1. Cihaz kümesini seçin.

1. **Pano** sekmesini seçin.

1. **Düzenle**’yi seçin.

    ![Tasarım modu açık](media/howto-use-device-sets/image3.png)

1. Görüntü ekleme hakkında daha fazla bilgi için bkz. [Azure IoT Central uygulamanıza görüntü hazırlama ve yükleme](howto-prepare-images.md).

1. Bağlantı kutucuğu ekleyin:
    1. Sağ bölmedeki **bağlantı** ' yı seçin.
    1. Bağlantıya bir **başlık**verin.
    1. Bağlantı seçildiğinde açılacak URL 'yi seçin.
    1. Bağlantıya **başlığı**altında gösterilen bir açıklama verin.
    1. **Kaydet**'i seçin.

        ![Bağlantıyı Kaydet](media/howto-use-device-sets/image7.png)

    1. **Panodaki**bağlantı kutucuğunu taşıyabilir ve yeniden boyutlandırabilirsiniz.

1. Kılavuz ekle. Kılavuz, cihaz kümesindeki ve seçtiğiniz sütunlarla bir cihaz tablosudur.
    1. Sağ bölmedeki **kılavuz** ' i seçin.
    1. Kılavuza bir **başlık**verin.
    1. **Ekle/Kaldır**' ı seçerek gösterilecek sütunları seçin. Açılan panelde, görünmesini istediğiniz sütunu seçin ve sağ ok tuşunu seçerek seçin.
    1. **Tamam**’ı seçin.
    1. **Kaydet**'i seçin.

        ![Kılavuzu Kaydet](media/howto-use-device-sets/image9.png)

    1. Kılavuzu **panoya**yerleştirmek için sürükleyin ve bırakın.

        > [!NOTE]
        > Birden çok görüntü, bağlantı ve kılavuz ekleyebilirsiniz.
  
    1. **Done** (Bitti) öğesini seçin.

Azure IoT Central 'de kutucukları kullanma hakkında daha fazla bilgi için bkz. [Pano kutucukları kullanma](howto-use-tiles.md).

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Cihaz kümeleriniz panosunda bir konum haritası yapılandırma

Cihaz kümesi içindeki cihazların konumunu görselleştirmek için bir eşleme ekleyebilirsiniz.

Cihaz kümeleriniz panosuna bir harita eklemek için, cihaz şablonunuzda bir konum ölçümü veya konum özelliği yapılandırmış olmanız gerekir. Daha fazla bilgi için bkz. [konum ölçümü oluşturma](howto-set-up-template.md) veya [bir konum özelliği oluşturma](howto-set-up-template.md).

1. Cihaz kümesi **panosunda**, kitaplıktan **eşle** ' yi seçin.
2. Bir başlık ekleyin ve daha önce yapılandırdığınız konum ölçümünü veya özelliği seçin.
3. **Kaydet** ' i seçtiğinizde harita kutucuğunda cihaz kümesi içindeki cihazların bilinen son konumları görüntülenir.
4. Bir operatör cihaz kümeleri panosunu görüntülediğinde, işleç, konum haritası da dahil olmak üzere yapılandırdığınız tüm kutucukları görür.

Panoda harita kutucuğunu yeniden boyutlandırabilirsiniz. Haritada bir PIN seçilmesi cihaz bilgilerini, adını ve konumunu görüntüler. Cihaz özellik sayfasına gitmek için açılan pencereyi seçin.

## <a name="configure-the-list-for-your-device-set"></a>Cihaz kümesi için listeyi yapılandırın

Cihaz kümesini oluşturduktan sonra **Listeyi**yapılandırabilirsiniz. **Listede** , seçtiğiniz sütunlara sahip bir tabloda ayarlanan cihazdaki tüm cihazlar gösterilmektedir.

1. Sol bölmedeki **cihaz kümeleri** ' ni seçin.

1. **Liste** sekmesini seçin.

1. **Sütun seçenekleri**seçin.

    ![Sütun seçenekleri](media/howto-use-device-sets/image11.png)

1. Göstermek istediğiniz sütunu seçerek ve seçmek için sağ ok tuşunu seçerek gösterilecek sütunları seçin.

    ![Sütun Seç](media/howto-use-device-sets/image12.png)

1. **Tamam**’ı seçin.

## <a name="analytics"></a>Analiz

Cihaz kümelerindeki analiz, sol bölmedeki ana Analiz sekmesi ile aynıdır. Analizler hakkında daha fazla bilgi için bkz. [analiz oluşturma](howto-use-device-sets.md)hakkında daha fazla bilgi edinebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızda cihaz kümelerini nasıl kullanacağınızı öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Telemetri kuralları oluşturma](howto-create-telemetry-rules.md)
