---
title: CAF geçiş giriş bölgesi şeması dağıtma örnek
description: CAF geçiş giriş bölgesi şeması, şema yapıt parametresi ayrıntıları dahil olmak üzere adımları dağıtın.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: d5aed1d492fea102598a22921d04ed8681c727cb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470124"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-migrate-landing-zone-blueprint-sample"></a>Azure geçişi giriş bölgesi şeması örneği için Microsoft Bulut benimseme çerçevesini dağıtma

Azure planları CAF geçiş giriş bölgesi şema örneğini dağıtmak için aşağıdaki adımlar gerçekleştirilmelidir:

> [!div class="checklist1"]
> - [CAF Foundation](../caf-foundation/index.md) şema örneğini dağıtmak için önerilir

> [!div class="checklist2"]
> - Örnekten yeni bir şema oluşturma
> - Örneğinizin kopyasını **Yayımlandı** olarak işaretleyin
> - Şemanızın kopyasını mevcut bir aboneliğe atayın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="create-blueprint-from-sample"></a>Örnekten şema oluştur

İlk olarak, başlangıç noktası olarak örneği kullanarak ortamınızda yeni bir şema oluşturup şema örneğini uygulayın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Soldaki **Başlangıç** sayfasında, _Şema oluştur_ bölümündeki **Oluştur** düğmesini seçin.

1. _Diğer Örnekler_ bölümünden **CAF Geçişi giriş bölgesi** şema örneğini bulun ve **Bu örneği kullanın** seçeneğini belirleyin.

1. Şema örneğinin _Temel bilgiler_ bölümüne girin:
   - **Blueprint adı** CAF geçiş giriş bölgesi şeması örneğinin kopyasına bir ad verin.
   - **Tanım konumu** Üç noktayı kullanın ve örnek kopyanızı ' e kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _Yapıtlar_ sekmesini seçin veya sayfanın en altındaki **Sonraki: Yapıtlar** seçeneğini belirleyin.

1. Şema örneğini oluşturan yapıtların listesini gözden geçirin. Yapıtların çoğu parametreler içerir; bu parametreleri daha sonra tanımlayacağız. Şema örneğini gözden geçirmeyi tamamladığınızda **Taslağı Kaydet**’i seçin.

## <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımlama

Şimdi şema örneği kopyanız, ortamınızda oluşturulmuştur. Bu, **Taslak** modunda oluşturulur ve atanabilmesi ve dağıtılabilmesi için önce **Yayımlandı** durumunda olmalıdır. Şema örneğinin kopyası, ortamınıza ve ihtiyaçlarınıza göre özelleştirilebilir, ancak bu değişiklik sonucunda kopya, CAF geçişi giriş bölgesi kılavuzundan dışarı taşınabilir.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Sayfanın üst kısmında **Şemayı yayımla**’yı seçin. Sağdaki yeni sayfada, şema örneği kopyanız için bir **Sürüm** sağlayın. Bu özellik daha sonra bir değişiklik yaptığınızda faydalı olacaktır. "CAF geçiş giriş bölgesinden yayınlanan ilk sürüm, şema örneğinden" gibi **değişiklik notları** sağlayın. Sayfanın alt kısmında **Yayımla** düğmesini seçin.

## <a name="assign-the-sample-copy"></a>Örnek kopyayı atama

Şema örneğinin kopyası başarıyla **yayımlandıktan** sonra, kaydedildiği yönetim grubu içindeki bir aboneliğe atanabilir. Bu adımda, şema örneği kopyasının her bir dağıtımını benzersiz hale getirmek için parametreler sağlanır.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Şema tanımı sayfasının en üstünde **Şema ata**’yı seçin.

1. Şema ataması için parametre değerlerini sağlayın:

   - Temel Bilgiler
     - **Abonelikler**: şema örneğinin kopyasını kaydettiğiniz yönetim grubundaki bir veya daha fazla abonelik seçin. Birden fazla abonelik seçerseniz, girilen parametreler kullanılarak her biri için bir atama oluşturulur.
     - **Atama adı**: ad, BLUEPRINT adına göre önceden doldurulur.
       Gerektiğinde değiştirin veya olduğu gibi bırakın.
     - **Konum**: yönetilen kimliğin oluşturulacağı bölgeyi seçin.
     - Azure Blueprint bu yönetilen kimliği kullanarak tüm yapıtları atanmış şemaya dağıtır.
       Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikler](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - Şema **tanımı sürümü**: şema örneğinin kopyasının **yayınlanmış** bir sürümünü seçin.
    
   - Kilit ataması

     Ortamınız için şema kilitleme ayarını seçin. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../../concepts/resource-locking.md).

   - Yönetilen Kimlik

     Varsayılan _sistem tarafından atanan_ yönetilen kimlik seçeneğini veya _kullanıcı tarafından atanan_ kimlik seçeneğini belirleyin.

   - Şema parametreleri

     Bu bölümde tanımlanan parametreler, tutarlılık sağlamak için şemadaki yapıtların birçoğu tarafından kullanılır.

       - **Kuruluş**: contoso veya fabrikam gibi kuruluşunuzun adını girin, benzersiz olmalıdır.
       - **Azureregion**: dağıtım Için bir Azure bölgesi seçin.
       
   - Yapıt parametreleri

     Bu bölümde tanımlanan parametreler, tanımlandığı yapıt için geçerlidir. Bu parametreler, Blueprint atama sırasında tanımlandıklarından [dinamik parametrelerdir](../../concepts/parameters.md#dynamic-parameters) . Tam liste veya yapıt parametreleri ve açıklamaları için bkz. [yapıt parametreleri tablosu](#artifact-parameters-table).

1. Tüm parametreler girildikten sonra, sayfanın alt kısmında **Ata** seçeneğini belirleyin. Şema ataması oluşturulur ve yapıt dağıtımı başlar. Dağıtım yaklaşık beş dakika sürer. Dağıtımın durumunu denetlemek için şema atamasını açın.

> [!WARNING]
> Azure Blueprints hizmeti ve yerleşik şema örnekleri **ücretsizdir**. Azure kaynakları, [ürüne göre fiyatlandırılır](https://azure.microsoft.com/pricing/). Bu şema örneği tarafından dağıtılan kaynakları çalıştırma maliyetini tahmin etmek için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) kullanın.

## <a name="artifact-parameters-table"></a>Yapıt parametreleri tablosu

Aşağıdaki tabloda, şema yapıtı parametrelerinin bir listesi verilmiştir:

|Yapıt adı|Yapıt türü|Parametre adı|Açıklama|
|-|-|-|-|
|VNET giriş bölgesini dağıt|Resource Manager şablonu|IPAddress_Space|**Kilitli** - İlk iki sekizlik tabanı sağlayın; örnek, 10.0|
|Key Vault’u dağıt|Resource Manager şablonu|KV-AccessPolicy|Key Vault içinde izinler vermek için **kilitli** grup veya Kullanıcı nesne kimliği|
|Log Analytics’i dağıt|Resource Manager şablonu|LogAnalytics_DataRetention|**Kilitli** -Log Analytics verilerin tutulacağı gün sayısı|
|Log Analytics’i dağıt|Resource Manager şablonu|LogAnalytics_Location|**Kilitli** - Çalışma alanı oluşturulurken kullanılan bölge|
|Azure Geçişi’ni dağıt|Resource Manager şablonu|Azure_Migrate_Location|**Kilitli** -Azure geçişi 'ni dağıtmak için bölgeyi seçin|

## <a name="next-steps"></a>Sonraki adımlar

CAF geçiş giriş bölgesi şeması örneğini dağıtma adımlarını gözden geçirdiğinize göre, mimari hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [CAF geçiş giriş bölgesi şeması-genel bakış](./index.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.
