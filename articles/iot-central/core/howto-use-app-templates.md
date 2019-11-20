---
title: Azure IoT Central uygulama şablonlarını kullanma | Microsoft Docs
description: Bir operatör olarak, Azure IoT Central uygulamanızda cihaz kümelerini kullanma.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d682c49aa833b9e11dbbddc5e9f6afd52cbb6e84
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952881"
---
# <a name="use-application-templates"></a>Uygulama şablonlarını kullanma

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu makalede, bir çözüm Yöneticisi olarak uygulama şablonlarının nasıl oluşturulacağı ve kullanılacağı açıklanmaktadır.

Bir Azure IoT Central uygulaması oluşturduğunuzda, yerleşik bir örnek şablonlar seçiminiz vardır. Ayrıca, mevcut IoT Central uygulamalarından kendi uygulama şablonlarınızı da oluşturabilirsiniz. Ardından, yeni uygulamalar oluştururken kendi uygulama şablonlarınızı kullanabilirsiniz.

Bir uygulama şablonu oluşturduğunuzda, var olan uygulamanızdan aşağıdaki öğeleri içerir:

- Pano düzeni ve tanımladığınız tüm kutucuklar dahil olmak üzere varsayılan uygulama panosu.
- Ölçümler, ayarlar, özellikler, komutlar ve Pano dahil cihaz şablonları.
- Kuralın. Tüm kural tanımları dahildir. Ancak, e-posta eylemleri hariç eylemler dahil değildir.
- Koşulları ve panoları dahil cihaz kümeleri.

> [!WARNING]
> Bir pano belirli cihazlarla ilgili bilgileri görüntüleyen kutucuklar içeriyorsa, bu kutucuklar **istenen kaynağı** yeni uygulamada bulamadı. Yeni uygulamanızdaki cihazlarla ilgili bilgileri göstermek için bu kutucukları yeniden yapılandırmanız gerekir.

Bir uygulama şablonu oluşturduğunuzda, bu, aşağıdaki öğeleri içermez:

- Cihazlar
- Kullanıcılar
- İş tanımları
- Sürekli veri dışa aktarma tanımları

Bu öğeleri bir uygulama şablonundan oluşturulan tüm uygulamalara el ile ekleyin.

## <a name="create-an-application-template"></a>Uygulama şablonu oluşturma

Mevcut bir IoT Central uygulamasından uygulama şablonu oluşturmak için:

1. Uygulamanızdaki **Yönetim** bölümüne gidin.
1. **Uygulama şablonu dışarı aktar**' ı seçin.
1. **Uygulama şablonu dışarı aktarma** sayfasında, şablonunuz için bir ad ve açıklama girin.
1. Uygulama şablonunu oluşturmak için **dışarı aktar** düğmesini seçin. Artık, bir kişinin şablondan yeni bir uygulama oluşturmasını sağlayan **paylaşılabilir bağlantıyı** kopyalayabilirsiniz:

![Uygulama şablonu oluşturma](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Uygulama şablonu kullanma

Yeni bir IoT Central uygulaması oluşturmak üzere bir uygulama şablonu kullanmak için önceden oluşturulmuş **paylaşılabilir bir bağlantıya**ihtiyacınız vardır. **Paylaşılabilir bağlantıyı** tarayıcınızın adres çubuğuna yapıştırın. **Uygulama oluştur** sayfası, özel uygulama şablonunuz seçili olarak görüntülenir:

![Şablondan uygulama oluşturma](media/howto-use-app-templates/create-app.png)

Ödeme planınızı seçin ve formdaki diğer alanları doldurun. Sonra uygulama şablonundan yeni bir IoT Central uygulaması oluşturmak için **Oluştur** ' u seçin.

## <a name="manage-application-templates"></a>Uygulama şablonlarını yönetme

**Uygulama şablonu dışarı aktarma** sayfasında, uygulama şablonunu silebilir veya güncelleştirebilirsiniz.

Bir uygulama şablonunu silerseniz, yeni uygulamalar oluşturmak için daha önce oluşturulmuş paylaşılabilir bağlantısını artık kullanamazsınız.

Uygulama şablonunuzu güncelleştirmek için, **uygulama şablonu dışarı aktarma** sayfasındaki şablon adını veya açıklamasını değiştirin. Ardından **dışarı aktar** düğmesini tekrar seçin. Bu eylem, yeni bir **paylaşılabilir bağlantı** oluşturur ve önceki **paylaşılabilir bağlantı** URL 'lerini geçersiz kılar.

## <a name="next-steps"></a>Sonraki adımlar

Uygulama şablonlarını nasıl kullanacağınızı öğrendiğinize göre, önerilen sonraki adım [Azure portal IoT Central nasıl yöneteceğinizi öğreneceksiniz.](howto-manage-iot-central-from-portal.md)
