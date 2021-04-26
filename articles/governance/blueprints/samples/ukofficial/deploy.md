---
title: UK resmi & UK NHS şema örnekleri dağıt
description: Şema yapıt parametresi ayrıntıları dahil olmak üzere Birleşik Krallık resmi ve UK NHS şeması örnekleri için adımları dağıtın.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: fc41e7d9d3867123286f970d7c54f5e6fff2d276
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578034"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>UK resmi ve UK NHS şema örneklerini dağıtın

UK resmi ve UK NHS şema örneklerini dağıtmak için aşağıdaki adımlar gerçekleştirilmelidir:

> [!div class="checklist"]
> - Örnekten yeni bir şema oluşturma
> - Örneğinizin kopyasını **Yayımlandı** olarak işaretleyin
> - Şemanızın kopyasını mevcut bir aboneliğe atayın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="create-blueprint-from-sample"></a>Örnekten şema oluştur

İlk olarak, başlangıç noktası olarak örneği kullanarak ortamınızda yeni bir şema oluşturup şema örneğini uygulayın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Soldaki **Başlangıç** sayfasında, _Şema oluştur_ bölümündeki **Oluştur** düğmesini seçin.

1. _Diğer örnekler_ altında **UK resmi** veya **UK NHS** şema örneğini bulun ve **Bu örneği kullan**' ı seçin.

1. Şema örneğinin _Temel bilgiler_ bölümüne girin:

   - **Şema adı**: şema örneğinin kopyasına bir ad verin.
   - **Tanım konumu**: üç noktayı kullanın ve örneğin kopyasını kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _Yapıtlar_ sekmesini seçin veya sayfanın en altındaki **Sonraki: Yapıtlar** seçeneğini belirleyin.

1. Şema örneğini oluşturan yapıtların listesini gözden geçirin. Yapıtların çoğu parametreler içerir; bu parametreleri daha sonra tanımlayacağız. Şema örneğini gözden geçirmeyi tamamladığınızda **Taslağı Kaydet**’i seçin.

## <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımlama

Şimdi şema örneği kopyanız, ortamınızda oluşturulmuştur. Bu, **Taslak** modunda oluşturulur ve atanabilmesi ve dağıtılabilmesi için önce **Yayımlandı** durumunda olmalıdır. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir, ancak bu değişiklik bu değişikliği standart dışında taşıyabilir.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Sayfanın üst kısmında **Şemayı yayımla**’yı seçin. Sağdaki yeni sayfada, şema örneği kopyanız için bir **Sürüm** sağlayın. Bu özellik daha sonra bir değişiklik yaptığınızda faydalı olacaktır. "UK resmi veya UK NHS şema örneğinden yayınlanan ilk sürüm" gibi **değişiklik notları** sağlayın. Sayfanın alt kısmında **Yayımla** düğmesini seçin.

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
     - **Konum**: yönetilen kimliğin oluşturulacağı bölgeyi seçin. Azure Blueprint bu yönetilen kimliği kullanarak tüm yapıtları atanmış şemaya dağıtır. Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikler](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - Şema **tanımı sürümü**: şema örneğinin kopyasının **yayınlanmış** bir sürümünü seçin.

   - Kilit ataması

     Ortamınız için şema kilitleme ayarını seçin. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../../concepts/resource-locking.md).

   - Yönetilen Kimlik

     Varsayılan sistem tarafından _atanmış_ yönetilen kimlik seçeneğini bırakın.

   - Yapıt parametreleri

     Bu bölümde tanımlanan parametreler, tanımlandığı yapıt için geçerlidir. Bu parametreler, Blueprint atama sırasında tanımlandıklarından [dinamik parametrelerdir](../../concepts/parameters.md#dynamic-parameters) . Tam liste veya yapıt parametreleri ve açıklamaları için bkz. [yapıt parametreleri tablosu](#artifact-parameters-table).

1. Tüm parametreler girildikten sonra, sayfanın alt kısmında **Ata** seçeneğini belirleyin. Şema ataması oluşturulur ve yapıt dağıtımı başlar. Dağıtım kabaca bir saat sürer. Dağıtımın durumunu denetlemek için şema atamasını açın.

> [!WARNING]
> Azure Blueprints hizmeti ve yerleşik şema örnekleri **ücretsizdir**. Azure kaynakları, [ürüne göre fiyatlandırılır](https://azure.microsoft.com/pricing/). Bu şema örneği tarafından dağıtılan kaynakları çalıştırma maliyetini tahmin etmek için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) kullanın.

## <a name="artifact-parameters-table"></a>Yapıt parametreleri tablosu

Aşağıdaki tabloda, şema yapıtı parametrelerinin bir listesi verilmiştir:

Yapıt adı|Yapıt türü|Parametre adı|Açıklama|
|-|-|-|-|
|UK RESMI veya UK NHS için Blueprint girişimi|İlke ataması |Tanılama günlüklerini denetlemeye yönelik kaynak türleri (Ilke: Birleşik Krallık RESMI veya UK NHS için Blueprint girişimi) |Tanılama günlük ayarı 'nın etkin olup olmadığını denetlemek için kaynak türlerinin listesi.  Kabul edilebilir değerler için bkz. [Azure tanılama günlükleri Için desteklenen hizmetler, şemalar ve Kategoriler](../../../../azure-monitor/essentials/resource-logs-schema.md). |
|\[Önizleme \] : Linux VM 'ler için Log Analytics aracısı dağıtma |İlke ataması |İsteğe bağlı: kapsama eklemek için desteklenen Linux işletim sistemini destekleyen VM görüntülerinin listesi (Ilke: \[ Önizleme \] : Linux sanal makineleri Için Log Analytics aracısı dağıtma) |Seçim Varsayılan değer _none_' dır. Daha fazla bilgi için [Azure portal Log Analytics çalışma alanı oluşturma](../../../../azure-monitor/logs/quick-create-workspace.md)bölümüne bakın. |
|\[Önizleme \] : Windows VM 'leri için Log Analytics aracısı dağıtma |İlke ataması |İsteğe bağlı: kapsama eklemek için desteklenen Windows işletim sistemini destekleyen VM görüntülerinin listesi (Ilke: \[ Önizleme \] : Windows VM 'leri Için Log Analytics aracısı dağıtma) |Seçim Varsayılan değer _none_' dır. Daha fazla bilgi için [Azure portal Log Analytics çalışma alanı oluşturma](../../../../azure-monitor/logs/quick-create-workspace.md)bölümüne bakın. |

## <a name="next-steps"></a>Sonraki adımlar

UK RESMI ve UK NHS şeması örneklerini dağıtmaya yönelik adımları gözden geçirdiğinize göre, genel bakış ve denetim eşlemesi hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [UK resmi ve UK NHS şemaları-genel bakış](./index.md) 
>  [UK resmi ve UK NHS şemaları-denetim eşleme](./control-mapping.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.
