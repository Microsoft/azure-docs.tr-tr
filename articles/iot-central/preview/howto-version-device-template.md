---
title: Azure IoT Central uygulamalarınız için cihaz şablonu sürüm oluşturmayı anlama | Microsoft Docs
description: Yeni sürümler oluşturarak ve canlı bağlı cihazlarınızı etkilemeden cihaz şablonlarınızda yineleme yapın
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 666c3030d91fba3c3bdfe23f808c4e830dff6ec1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895598"
---
# <a name="create-a-new-device-template-version-preview-features"></a>Yeni bir cihaz şablonu sürümü oluşturma (Önizleme özellikleri)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central IoT uygulamalarının hızla geliştirilmesini sağlar. Cihaz özelliklerini, görünümleri ve özelleştirmeleri ekleyerek, düzenleyerek veya silerek cihaz şablonu tasarımlarınız üzerinde hızlıca yineleme yapabilirsiniz. Cihaz şablonunuzu yayımladıktan sonra cihaz yeteneği modeli, modelin yanında kilit simgeleri ile **yayımlandı** olarak gösterilir. Cihaz yetenek modelinde değişiklik yapabilmek için cihaz şablonunun yeni bir sürümünü oluşturmanız gerekir. Bu arada, bulut özellikleri, özelleştirmeler ve görünümler, cihaz şablonunun sürümü gerekmeden herhangi bir zamanda düzenlenebilirler. Bu değişikliklerden birini kaydettikten sonra, işlecin Device Explorer içinde görüntülemesi için en son değişiklikleri kullanılabilir hale getirmek üzere cihaz şablonunu yayımlayabilirsiniz.

> [!NOTE]
> Cihaz şablonu oluşturma hakkında daha fazla bilgi edinmek için bkz. [cihaz şablonu ayarlama ve yönetme](howto-set-up-template.md)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Sürüm oluşturma olmadan cihaz şablonuna özelleştirmeler ekleme

Cihaz olanaklarınızın belirli öğeleri, cihaz şablonu ve arabirimlerinizin sürümü gerekmeden düzenlenebilir. Örneğin, bu alanlardan bazıları görünen adı, anlam türünü, minimum değeri, en büyük değer, ondalık basamak, renk, birim, görüntü birimi, açıklama ve açıklama içerir. Şu özelleştirmelere birini eklemek için:

1. **Cihaz şablonları** sayfasına gidin.
1. Özelleştirmek istediğiniz cihaz şablonunu seçin.
1. **Özelleştir** sekmesini seçin.
1. Cihaz yetenek modelinizde tanımlanan tüm yetenekler burada listelenir. Burada düzenleyebileceğiniz tüm alanlar, cihaz şablonunuzun sürümü gerekmeden uygulamanız genelinde kaydedilebilir ve kullanılabilir. Salt okunurdur düzenlemek istediğiniz alanlar varsa, bunları değiştirmek için cihaz şablonunuzu yüklemeniz gerekir. Düzenlemek istediğiniz bir alanı seçin ve yeni değerleri girin.
1. **Kaydet** düğmesine tıklayın. Bu değerler, başlangıçta cihaz şablonunuzda kaydedilen ve uygulama genelinde kullanılacak olan her şeyi geçersiz kılacak.

## <a name="versioning-a-device-template"></a>Cihaz şablonu sürümü oluşturma

Cihaz şablonunuzun yeni bir sürümünü oluşturmak, cihaz yetenek modelinin düzenlenebildiği şablonun taslak bir sürümünü oluşturur. Yayımlanan tüm arabirimler tek tek sürümleyene kadar yayımlanmaya devam eder. Yayımlanmış bir arabirimi değiştirmek için, önce yeni bir cihaz şablonu sürümü oluşturmanız gerekir.

Cihaz şablonu, cihaz şablonunun özelleştirmeler bölümünde düzenleyebileceğiniz cihaz yetenek modelinin bir parçasını düzenlemeye çalışırken yalnızca sürümlük edilmelidir. 

Bir cihaz şablonunun sürümü için:

1. **Cihaz şablonları** sayfasına gidin.
1. Sürüm oluşturmaya çalıştığınız cihaz şablonunu seçin.
1. Sayfanın üst kısmındaki **Sürüm** düğmesine tıklayın ve şablona yeni bir ad verin. Sizin için düzenlenebilecek yeni bir ad önerdi.
1. **Oluştur**'a tıklayın.
1. Artık cihaz şablonunuz taslak modunda. Arabirimlerinizin hala kilitli olduğunu ve düzenlenebilmesi için ayrı ayrı sürüm oluşturulması gerektiğini görürsünüz. 

### <a name="versioning-an-interface"></a>Arabirim sürümü oluşturma

Bir arabirimin sürümü oluşturma, önceden oluşturduğunuz arabirim içindeki özellikleri eklemenize, güncelleştirmenize ve kaldırmanıza olanak sağlar. 

Bir arabirimin sürümü için:

1. **Cihaz şablonları** sayfasına gidin.
1. Taslak modunda olan cihaz şablonunu seçin.
1. Sürüm ve düzenleme yapmak istediğiniz yayımlanmış modda olan arabirimi seçin.
1. Arabirim sayfasının en üstündeki **Sürüm** düğmesine tıklayın. 
1. **Oluştur**'a tıklayın.
1. Artık arabiriminiz taslak modunda. Mevcut özelleştirmeleri ve görünümleri bozmadan arabiriminize özellikler ekleyebilir veya bu özellikleri düzenleyebilirsiniz. 

> [!NOTE]
> Azure IoT tarafından yayınlanan standart arabirimlerin sürümü oluşturulamıyor veya düzenlenemiyor. Bu standart arabirimler cihaz sertifikası için kullanılır.

> [!NOTE]
> Arabirim yayımlandıktan sonra, bir taslak modunda bile bu özellikleri silemezsiniz. Yetenekler yalnızca taslak modundaki arabirime düzenlenebilir veya eklenebilir.


## <a name="migrate-a-device-across-device-template-versions"></a>Cihazı cihaz şablonu sürümleri arasında geçirme

Cihaz şablonunun birden çok sürümünü oluşturabilirsiniz. Zaman içinde, bu cihaz şablonlarını kullanan birden fazla bağlı cihazınız olacaktır. Cihazları, cihaz şablonunuzun bir sürümünden başka bir sürümüne geçirebilirsiniz. Aşağıdaki adımlarda bir cihazın nasıl geçirileceği açıklanır:

1. **Device Explorer** sayfasına gidin.
1. Başka bir sürüme geçirmeniz gereken cihazı seçin.
1. **Geçir**' i seçin.
1. Cihazı geçirmek istediğiniz sürüm numarasına sahip cihaz şablonunu seçin ve **geçir**' i seçin.

![Cihaz geçirme](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure IoT Central uygulamanızda cihaz şablonu sürümlerini kullanmayı öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Telemetri kuralları oluşturma](tutorial-create-telemetry-rules.md)
