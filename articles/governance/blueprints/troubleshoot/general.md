---
title: Sık karşılaşılan hataları giderme
description: İlke ihlalleri ve şema parametre işlevleri gibi şemaları oluşturma, atama ve kaldırma ile ilgili sorunların nasıl giderileceği hakkında bilgi edinin.
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 65cf8ef9a5dcba0165aad8522f91ff1eb2c963a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98918853"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Azure şemaları 'nı kullanarak hatalarda sorun giderme

Şemaları oluştururken, atarken veya kaldırırken hatalarla karşılaşabilirsiniz. Bu makalede, oluşabilecek çeşitli hatalar ve bunların nasıl çözümleneceği açıklanmaktadır.

## <a name="finding-error-details"></a>Hata ayrıntılarını bulma

Birçok hata, bir kapsamda şema atamanın sonucu olacaktır. Bir atama başarısız olduğunda, şema başarısız dağıtım hakkında ayrıntılar sağlar. Bu bilgiler sorunu çözülecektir ve sonraki dağıtım başarılı olur.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki sayfadan **atanan** şemalar ' ı seçin ve başarısız atamayı bulmak için şema atamalarını filtrelemek için arama kutusunu kullanın. Ayrıca, tüm başarısız atamaları birlikte gruplanmış olarak görmek için Atamalar tablosunu **sağlama durumu** sütununa göre sıralayabilirsiniz.

1. _Başarısız_ durumu olan şema ' i seçin veya sağ tıklayıp **atama ayrıntılarını görüntüle**' yi seçin.

1. Atamanın başarısız olduğu kırmızı bir başlık, BLUEPRINT atama sayfasının en üstünde yer alan bir uyarı. Daha fazla ayrıntı edinmek için başlık üzerinde herhangi bir yeri seçin.

Hatanın bir bütün olarak değil bir yapıtın neden olması yaygındır. Bir yapıt Key Vault oluşturursa ve Azure Ilkesi Key Vault oluşturmayı engelliyorsa, tüm atama başarısız olur.

## <a name="general-errors"></a>Genel hatalar

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Senaryo: Ilke Ihlali

#### <a name="issue"></a>Sorun

İlke ihlali nedeniyle şablon dağıtımı başarısız oldu.

#### <a name="cause"></a>Nedeni

Bir ilke, birkaç nedenden dolayı dağıtımla çakışabilir:

- Oluşturulan kaynak ilkeyle kısıtlıdır (genellikle SKU veya konum kısıtlamaları)
- Dağıtım, ilke tarafından yapılandırılan alanları ayarlıyor (etiketlerle ortak)

#### <a name="resolution"></a>Çözüm

Şema, hata ayrıntılarında ilkelerle çakışmayacak şekilde değiştirin. Bu değişiklik mümkün değilse, ilke atamasının kapsamının, şema ile çakışmadan, artık ilkeyle çakışmamasını sağlamak için alternatif bir seçenek bulunur.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Senaryo: Blueprint parametresi bir işlevdir

#### <a name="issue"></a>Sorun

İşlevler olan Blueprint parametreleri yapıtlara geçirilmeden önce işlenir.

#### <a name="cause"></a>Nedeni

Bir yapıtı gibi bir işlevi kullanan bir şema parametresini geçirmek, `[resourceGroup().tags.myTag]` dinamik işlev yerine yapıtın üzerinde ayarlanan işlevin işlenmiş sonucuna neden olur.

#### <a name="resolution"></a>Çözüm

Bir işlevi bir parametre olarak kullanarak geçirmek için, tüm dizeyi, `[` şema parametresi gibi görünen şekilde kaçış `[[resourceGroup().tags.myTag]` . Kaçış karakteri, şema, şema işlerken değeri bir dize olarak işlemeye neden olur. Daha sonra, planlar hizmeti, işlevi yapıtlara, beklendiği gibi dinamik olmasını sağlayacak şekilde koyar. Daha fazla bilgi için bkz. [Azure Resource Manager şablonlarındaki sözdizimi ve ifadeler](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="delete-errors"></a>Hataları Sil

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Senaryo: atama silme zaman aşımı

#### <a name="issue"></a>Sorun

Şema atamasının silinmesi tamamlanmaz.

#### <a name="cause"></a>Nedeni

Şema ataması silindiğinde, terminal dışı bir durumda kalabilir. Bu durum, şema atama tarafından oluşturulan kaynaklar hala silinmeyi bekliyor veya Azure şemaları 'na bir durum kodu döndürmediğinde oluşur.

#### <a name="resolution"></a>Çözüm

Terminal olmayan bir durumdaki şema atamaları, _altı saatlik_ bir zaman aşımından sonra **başarısız** olarak otomatik olarak işaretlenir. Zaman aşımı şeması şema atamasının durumunu ayarladığında, silme yeniden denenebilir.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

- Azure [forumları](https://azure.microsoft.com/support/forums/)aracılığıyla Azure uzmanlarından yanıtlar alın.
- [@AzureSupport](https://twitter.com/azuresupport)Azure Community 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı ile bağlanın: yanıtlar, destek ve uzmanlar.
- Daha fazla yardıma ihtiyacınız varsa, bir Azure destek olayı dosyası gönderebilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.