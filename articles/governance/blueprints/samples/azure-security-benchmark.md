---
title: Azure Güvenlik Karşılaştırması şema örneğine genel bakış
description: Azure Güvenlik Karşılaştırması şema örneğine genel bakış. Bu şema örneği, müşterilerin belirli denetimleri değerlendirmesine yardımcı olur.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 58e8c3972b0f05e5b0a3f15da0dfd3eae301ad31
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387001"
---
# <a name="azure-security-benchmark-blueprint-sample"></a>Azure Güvenlik kıyaslaması şeması örneği

Azure Güvenlik kıyaslaması şeması örneği, belirli [Azure Güvenlik kıyaslaması](../../../security/benchmarks/overview.md) denetimlerini değerlendirmenize yardımcı olan [Azure ilkesini](../../policy/overview.md) kullanarak idare koruması-rayları sağlar. Bu şema müşterilerin, Azure Güvenlik Karşılaştırması denetimlerini uygulamayı amaçladığı, Azure’da dağıtılmış herhangi bir mimari için bir dizi ilke dağıtmasına yardımcı olur.

## <a name="control-mapping"></a>Denetim eşlemesi

[Azure ilke denetimi eşleme](../../policy/samples/azure-security-benchmark.md) , bu şema içinde yer alan ilke tanımlarına ilişkin ayrıntıları ve bu Ilke tanımlarının Azure Güvenlik kıyaslamalarındaki **Uyumluluk etki alanları** ve **denetimleriyle** nasıl eşlendiğini açıklar. Bir mimariye atandığında kaynaklar, atanan ilke tanımlarına yönelik uyumsuzluk için Azure Ilkesi tarafından değerlendirilir. Daha fazla bilgi için bkz. [Azure İlkesi](../../policy/overview.md).

## <a name="deploy"></a>Dağıtma

Azure şemaları Azure Güvenlik kıyaslama şeması örneğini dağıtmak için aşağıdaki adımlar gerçekleştirilmelidir:

> [!div class="checklist"]
> - Örnekten yeni bir şema oluşturma
> - Örneğinizin kopyasını **Yayımlandı** olarak işaretleyin
> - Şemanızın kopyasını mevcut bir aboneliğe atayın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

### <a name="create-blueprint-from-sample"></a>Örnekten şema oluştur

İlk olarak, başlangıç noktası olarak örneği kullanarak ortamınızda yeni bir şema oluşturup şema örneğini uygulayın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Soldaki **Başlangıç** sayfasında, _Şema oluştur_ bölümündeki **Oluştur** düğmesini seçin.

1. _Diğer örnekler_ altında **Azure Güvenlik kıyaslaması v1** şeması örneğini bulun ve bu örneği seçmek için adı seçin.

1. Şema örneğinin _Temel bilgiler_ bölümüne girin:

   - **Şema adı**: Azure Güvenlik kıyaslaması şeması örneğinin kopyasına bir ad verin.
   - **Tanım konumu**: üç noktayı kullanın ve örneğin kopyasını kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _Yapıtlar_ sekmesini seçin veya sayfanın en altındaki **Sonraki: Yapıtlar** seçeneğini belirleyin.

1. Şema örneğine dahil edilen yapıtların listesini gözden geçirin. Yapıtların çoğu parametreler içerir; bu parametreleri daha sonra tanımlayacağız. Şema örneğini gözden geçirmeyi tamamladığınızda **Taslağı Kaydet**’i seçin.

### <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımlama

Şimdi şema örneği kopyanız, ortamınızda oluşturulmuştur. Bu, **Taslak** modunda oluşturulur ve atanabilmesi ve dağıtılabilmesi için önce **Yayımlandı** durumunda olmalıdır. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir, ancak bu değişiklik, Azure güvenliği kıyaslama önerileri ile hizalamadan uzaklaştırabilir.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Sayfanın üst kısmında **Şemayı yayımla**’yı seçin. Sağdaki yeni sayfada, şema örneği kopyanız için bir **Sürüm** sağlayın. Bu özellik daha sonra bir değişiklik yaptığınızda faydalı olacaktır. "Azure Güvenlik kıyaslaması şeması örneğinden yayımlanan Ilk sürüm" gibi **değişiklik notlarını** sağlar. Sayfanın alt kısmında **Yayımla** düğmesini seçin.

### <a name="assign-the-sample-copy"></a>Örnek kopyayı atama

Şema örneğinin kopyası başarıyla **yayımlandıktan** sonra, kaydedildiği yönetim grubu içindeki bir aboneliğe atanabilir. Bu adımda, şema örneği kopyasının her bir dağıtımını benzersiz hale getirmek için parametreler sağlanır.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Şema tanımı sayfasının en üstünde **Şema ata**’yı seçin.

1. Şema ataması için parametre değerlerini sağlayın:

   - Temel Bilgiler

     - **Abonelikler**: şema örneğinin kopyasını kaydettiğiniz yönetim grubundaki bir veya daha fazla abonelik seçin. Birden fazla abonelik seçerseniz, girilen parametreler kullanılarak her biri için bir atama oluşturulur.
     - **Atama adı**: ad, BLUEPRINT adına göre önceden doldurulur.
       Gerektiğinde değiştirin veya olduğu gibi bırakın.
     - **Konum**: yönetilen kimliğin oluşturulacağı bölgeyi seçin. Azure Blueprint bu yönetilen kimliği kullanarak tüm yapıtları atanmış şemaya dağıtır. Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikler](../../../active-directory/managed-identities-azure-resources/overview.md).
     - Şema **tanımı sürümü**: şema örneğinin kopyasının **yayınlanmış** bir sürümünü seçin.

   - Kilit ataması

     Ortamınız için şema kilitleme ayarını seçin. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../concepts/resource-locking.md).

   - Yönetilen Kimlik

     Varsayılan sistem tarafından _atanmış_ yönetilen kimlik seçeneğini bırakın.

   - Yapıt parametreleri

     Bu bölümde tanımlanan parametreler, tanımlandığı yapıt için geçerlidir. Bu parametreler, Blueprint atama sırasında tanımlandıklarından [dinamik parametrelerdir](../concepts/parameters.md#dynamic-parameters) . Tam liste veya yapıt parametreleri ve açıklamaları için bkz. [yapıt parametreleri tablosu](#artifact-parameters-table).

1. Tüm parametreler girildikten sonra, sayfanın alt kısmında **Ata** seçeneğini belirleyin. Şema ataması oluşturulur ve yapıt dağıtımı başlar. Dağıtım kabaca bir saat sürer. Dağıtımın durumunu denetlemek için şema atamasını açın.

> [!WARNING]
> Azure Blueprints hizmeti ve yerleşik şema örnekleri **ücretsizdir**. Azure kaynakları, [ürüne göre fiyatlandırılır](https://azure.microsoft.com/pricing/). Bu şema örneği tarafından dağıtılan kaynakları çalıştırma maliyetini tahmin etmek için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) kullanın.

### <a name="artifact-parameters-table"></a>Yapıt parametreleri tablosu

Aşağıdaki tabloda, şema yapıtı parametrelerinin bir listesi verilmiştir:

|Yapıt adı|Yapıt türü|Parametre adı|Açıklama|
|-|-|-|-|
|Azure Güvenlik kıyaslama önerilerini denetleme ve belirli destekleyici VM uzantılarını dağıtma|İlke ataması|Windows VM Yöneticileri grubundan dışlanan kullanıcıların listesi|Yöneticiler yerel grubunda dışlanması gereken üyelerin noktalı virgülle ayrılmış listesi. Ex: yönetici; myUser1; myUser2|
|Azure Güvenlik kıyaslama önerilerini denetleme ve belirli destekleyici VM uzantılarını dağıtma|İlke ataması|Windows VM yöneticileri grubuna dahil olması gereken kullanıcıların listesi|Yöneticiler yerel grubuna dahil edilecek üyelerin noktalı virgülle ayrılmış listesi. Ex: yönetici; myUser1; myUser2|
|Azure Güvenlik kıyaslama önerilerini denetleme ve belirli destekleyici VM uzantılarını dağıtma|İlke ataması|Windows VM yöneticileri grubu 'nun *yalnızca* şunları içermesi gereken kullanıcılar listesi|Administrators yerel grubunun beklenen tüm üyelerinin noktalı virgülle ayrılmış listesi. Ex: yönetici; myUser1; myUser2|
|Azure Güvenlik kıyaslama önerilerini denetleme ve belirli destekleyici VM uzantılarını dağıtma|İlke ataması|Ağ Izleyicisi 'nin etkinleştirilmesi gereken bölgelerin listesi|Bölgelerin tamamı listesini kullanın Get-AzLocation|
|Azure Güvenlik kıyaslama önerilerini denetleme ve belirli destekleyici VM uzantılarını dağıtma|İlke ataması|VM 'Lerin bağlanması gereken sanal ağ|Örnek:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Azure Güvenlik kıyaslama önerilerini denetleme ve belirli destekleyici VM uzantılarını dağıtma|İlke ataması|Sanal ağların kullanması gereken ağ geçidi|Örnek:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Azure Güvenlik kıyaslama önerilerini denetleme ve belirli destekleyici VM uzantılarını dağıtma|İlke ataması|Log Analytics aracılarının bağlanacağı çalışma alanı kimliklerinin listesi|Log Analytics aracısının bağlanması gereken çalışma alanı kimliklerinin noktalı virgülle ayrılmış listesi|
|Azure Güvenlik kıyaslama önerilerini denetleme ve belirli destekleyici VM uzantılarını dağıtma|İlke ataması|Tanılama günlükleri etkinleştirilmiş olması gereken kaynak türlerinin listesi|Seçili kaynak türleri için tanılama ayarını denetle|
|Azure Güvenlik kıyaslama önerilerini denetleme ve belirli destekleyici VM uzantılarını dağıtma|İlke ataması|En son PHP sürümü|Uygulama Hizmetleri için desteklenen en son PHP sürümü|
|Azure Güvenlik kıyaslama önerilerini denetleme ve belirli destekleyici VM uzantılarını dağıtma|İlke ataması|En son Java sürümü|Uygulama Hizmetleri için desteklenen en son Java sürümü|
|Azure Güvenlik kıyaslama önerilerini denetleme ve belirli destekleyici VM uzantılarını dağıtma|İlke ataması|En son Windows Python sürümü|Uygulama Hizmetleri için desteklenen en son Python sürümü|
|Azure Güvenlik kıyaslama önerilerini denetleme ve belirli destekleyici VM uzantılarını dağıtma|İlke ataması|En son Linux Python sürümü|Uygulama Hizmetleri için desteklenen en son Python sürümü|

## <a name="next-steps"></a>Sonraki adımlar

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.