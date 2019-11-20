---
title: Azure IoT Central uygulamasını dışarı aktarma | Microsoft Docs
description: Bir çözüm Yöneticisi olarak, bir uygulama şablonunu yeniden kullanabilmek için dışarı aktarmak istiyorum.
author: dominicbetts
ms.author: dobett
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 81e24712de186e2d5ec03e33ecabe485cc0d9f31
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894766"
---
# <a name="export-your-application-preview-features"></a>Uygulamanızı dışa aktarma (Önizleme özellikleri)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu makalede, bir IoT Central uygulamasının yeniden kullanabilmek için bir çözüm Yöneticisi olarak nasıl dışarı aktarılacağı açıklanmaktadır.

İki seçeneğiniz vardır:

- Yalnızca uygulamanızın yinelenen bir kopyasını oluşturmanız gerekiyorsa uygulamanızın bir kopyasını oluşturabilirsiniz.
- Birden çok kopya oluşturmayı planlıyorsanız uygulamanızdan bir uygulama şablonu oluşturabilirsiniz.

## <a name="copy-your-application"></a>Uygulamanızı kopyalayın

Herhangi bir uygulamanın bir kopyasını, eksi herhangi bir cihaz örneğini, cihaz veri geçmişini ve Kullanıcı verilerini oluşturabilirsiniz. Kopya, ücretlendirilebilecek Kullandıkça Öde uygulamasıdır. Bir uygulamayı kopyalayarak deneme uygulaması oluşturamazsınız.

**Kopyala**' yı seçin. İletişim kutusunda yeni Kullandıkça Öde uygulamasının ayrıntılarını girin. Sonra devam etmek istediğinizi onaylamak için **Kopyala** ' yı seçin. Formdaki alanlar hakkında daha fazla bilgi edinmek için bkz. [uygulama oluşturma](quick-deploy-iot-central.md) hızlı başlangıcı.

![Uygulama ayarları sayfası](media/howto-use-app-templates/appcopy2.png)

Uygulama kopyalama işlemi başarılı olduktan sonra, bağlantısını kullanarak yeni uygulamaya gidebilirsiniz.

![Uygulama ayarları sayfası](media/howto-use-app-templates/appcopy3a.png)

Bir uygulamayı kopyalamak kuralların ve e-posta eyleminin tanımını da kopyalar. Flow ve Logic Apps gibi bazı eylemler kural KIMLIĞI aracılığıyla belirli kurallara bağlıdır. Bir kural farklı bir uygulamaya kopyalandığında kendi kural KIMLIĞINI alır. Bu durumda, kullanıcıların yeni bir eylem oluşturması ve yeni kuralı onunla ilişkilendirilmesi gerekir. Genel olarak, yeni uygulamada güncel olduklarından emin olmak için kuralları ve eylemleri denetlemeniz iyi bir fikirdir.

> [!WARNING]
> Bir pano belirli cihazlarla ilgili bilgileri görüntüleyen kutucuklar içeriyorsa, bu kutucuklar **istenen kaynağı** yeni uygulamada bulamadı. Yeni uygulamanızdaki cihazlarla ilgili bilgileri göstermek için bu kutucukları yeniden yapılandırmanız gerekir.

## <a name="create-an-application-template"></a>Uygulama şablonu oluşturma

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

Mevcut bir IoT Central uygulamasından uygulama şablonu oluşturmak için:

1. Uygulamanızdaki **Yönetim** bölümüne gidin.
1. **Uygulama şablonu dışarı aktar**' ı seçin.
1. **Uygulama şablonu dışarı aktarma** sayfasında, şablonunuz için bir ad ve açıklama girin.
1. Uygulama şablonunu oluşturmak için **dışarı aktar** düğmesini seçin. Artık, bir kişinin şablondan yeni bir uygulama oluşturmasını sağlayan **paylaşılabilir bağlantıyı** kopyalayabilirsiniz:

![Uygulama şablonu oluşturma](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Uygulama şablonu kullanma

Yeni bir IoT Central uygulaması oluşturmak üzere bir uygulama şablonu kullanmak için önceden oluşturulmuş **paylaşılabilir bir bağlantıya**ihtiyacınız vardır. **Paylaşılabilir bağlantıyı** tarayıcınızın adres çubuğuna yapıştırın. **Uygulama oluştur** sayfası, özel uygulama şablonunuz seçili olarak görüntülenir:

![Şablondan uygulama oluşturma](media/howto-use-app-templates/create-app.png)

Ödeme planınızı seçin ve formdaki diğer alanları doldurun. Sonra uygulama şablonundan yeni bir IoT Central uygulaması oluşturmak için **Oluştur** ' u seçin.

### <a name="manage-application-templates"></a>Uygulama şablonlarını yönetme

**Uygulama şablonu dışarı aktarma** sayfasında, uygulama şablonunu silebilir veya güncelleştirebilirsiniz.

Bir uygulama şablonunu silerseniz, yeni uygulamalar oluşturmak için daha önce oluşturulmuş paylaşılabilir bağlantısını artık kullanamazsınız.

Uygulama şablonunuzu güncelleştirmek için, **uygulama şablonu dışarı aktarma** sayfasındaki şablon adını veya açıklamasını değiştirin. Ardından **dışarı aktar** düğmesini tekrar seçin. Bu eylem, yeni bir **paylaşılabilir bağlantı** oluşturur ve önceki **paylaşılabilir bağlantı** URL 'lerini geçersiz kılar.

## <a name="next-steps"></a>Sonraki adımlar

Uygulama şablonlarını nasıl kullanacağınızı öğrendiğinize göre, önerilen sonraki adım [Azure portal IoT Central nasıl yöneteceğinizi öğreneceksiniz.](../core/howto-manage-iot-central-from-portal.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
