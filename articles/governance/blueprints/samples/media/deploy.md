---
title: Media şema örneğini dağıtma
description: Şema yapıt parametresi ayrıntıları dahil olmak üzere Media şema örneği için adımları dağıtın.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 760f75711f66e2b178ef12a9a4e24ff2e536179c
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386440"
---
# <a name="deploy-the-media-blueprint-sample"></a>Medya şeması örneğini dağıtma

Media şema örneğini dağıtmak için aşağıdaki adımlar gerçekleştirilmelidir:

> [!div class="checklist"]
> - Örnekten yeni bir şema oluşturma
> - Örneğinizin kopyasını **Yayımlandı** olarak işaretleyin
> - Şemanızın kopyasını mevcut bir aboneliğe atayın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="create-blueprint-from-sample"></a>Örnekten şema oluştur

İlk olarak, başlangıç noktası olarak örneği kullanarak ortamınızda yeni bir şema oluşturup şema örneğini uygulayın.

1. **Tüm hizmetler** ' i seçin ve sol bölmedeki **ilke** ara ' yı seçin. **İlke** sayfasında, **planlar**' ı seçin.

1. Soldaki **Başlangıç** sayfasında, _Şema oluştur_ bölümündeki **Oluştur** düğmesini seçin.

1. _Diğer örnekler_ altında **Media** şema örneğini bulun ve **Bu örneği kullan**' ı seçin.

1. Şema örneğinin _Temel bilgiler_ bölümüne girin:

   - **Şema adı**: şema örneğinin kopyasına bir ad verin.
   - **Tanım konumu**: üç noktayı kullanın ve örneğin kopyasını kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _Yapıtlar_ sekmesini seçin veya sayfanın en altındaki **Sonraki: Yapıtlar** seçeneğini belirleyin.

1. Şema örneğini oluşturan yapıtların listesini gözden geçirin. Yapıtların çoğu parametreler içerir; bu parametreleri daha sonra tanımlayacağız. Şema örneğini gözden geçirmeyi tamamladığınızda **Taslağı Kaydet**’i seçin.

## <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımlama

Şimdi şema örneği kopyanız, ortamınızda oluşturulmuştur. Bu, **Taslak** modunda oluşturulur ve atanabilmesi ve dağıtılabilmesi için önce **Yayımlandı** durumunda olmalıdır. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir, ancak bu değişiklik bu değişikliği standart dışında taşıyabilir.

1. **Tüm hizmetler** ' i seçin ve sol bölmedeki **ilke** ara ' yı seçin. **İlke** sayfasında, **planlar**' ı seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Sayfanın üst kısmında **Şemayı yayımla**’yı seçin. Sağdaki yeni sayfada, şema örneği kopyanız için bir **Sürüm** sağlayın. Bu özellik daha sonra bir değişiklik yaptığınızda faydalı olacaktır. "Media şema örneğinden yayımlanan ilk sürüm" gibi **değişiklik notları** sağlayın. Sayfanın alt kısmında **Yayımla** düğmesini seçin.

## <a name="assign-the-sample-copy"></a>Örnek kopyayı atama

Şema örneğinin kopyası başarıyla **yayımlandıktan** sonra, kaydedildiği yönetim grubu içindeki bir aboneliğe atanabilir. Bu adımda, şema örneği kopyasının her bir dağıtımını benzersiz hale getirmek için parametreler sağlanır.

1. **Tüm hizmetler** ' i seçin ve sol bölmedeki **ilke** ara ' yı seçin. **İlke** sayfasında, **planlar**' ı seçin.

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
|\[Önizleme \] : Linux VM 'ler için Log Analytics aracısı dağıtma |İlke ataması |Linux VM 'Ler için Log Analytics çalışma alanı |Daha fazla bilgi için [Azure portal Log Analytics çalışma alanı oluşturma](../../../../azure-monitor/logs/quick-create-workspace.md)bölümüne bakın. |
|\[Önizleme \] : Linux VM 'ler için Log Analytics aracısı dağıtma |İlke ataması |İsteğe bağlı: kapsama eklemek için desteklenen Linux işletim sistemini destekleyen VM görüntülerinin listesi |Boş bir dizi, isteğe bağlı parametre olmadığını göstermek için kullanılabilir: `[]` |
|\[Önizleme \] : Windows VM 'leri için Log Analytics aracısı dağıtma |İlke ataması |İsteğe bağlı: kapsama eklemek için desteklenen Windows işletim sistemini destekleyen VM görüntülerinin listesi |Boş bir dizi, isteğe bağlı parametre olmadığını göstermek için kullanılabilir: `[]` |
|\[Önizleme \] : Windows VM 'leri için Log Analytics aracısı dağıtma |İlke ataması |Windows VM 'Leri için Log Analytics çalışma alanı |Daha fazla bilgi için [Azure portal Log Analytics çalışma alanı oluşturma](../../../../azure-monitor/logs/quick-create-workspace.md)bölümüne bakın. |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için medya denetimlerini denetleme ve belırlı VM uzantılarını dağıtma |İlke ataması |VM 'Lerin yapılandırılması gereken Log Analytics çalışma alanı KIMLIĞI |Bu, VM 'Lerin için yapılandırılması gereken Log Analytics çalışma alanının KIMLIĞIDIR (GUID). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için medya denetimlerini denetleme ve belırlı VM uzantılarını dağıtma |İlke ataması |Tanılama günlükleri etkinleştirilmiş olması gereken kaynak türlerinin listesi |Tanılama günlüğü ayarı etkinleştirilmemişse denetlenecek kaynak türleri listesi. Kabul edilebilir değerler, [Azure izleyici tanılama günlükleri şemalarında](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas)bulunabilir. |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için medya denetimlerini denetleme ve belırlı VM uzantılarını dağıtma |İlke ataması |Yöneticiler grubu |Grubu. Örnek: `Administrator; myUser1; myUser2` |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için medya denetimlerini denetleme ve belırlı VM uzantılarını dağıtma |İlke ataması |Windows VM yöneticileri grubuna dahil edilecek kullanıcıların listesi |Yöneticiler yerel grubuna dahil edilecek üyelerin noktalı virgülle ayrılmış listesi. Örnek: `Administrator; myUser1; myUser2` |
|Depolama hesaplarında Gelişmiş tehdit koruması dağıtma |İlke ataması |Etki |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|SQL Server 'lar üzerinde denetim dağıtma |İlke ataması |Bekletme döneminin gün cinsinden değer (0 sınırsız saklama anlamına gelir) |Bekletme günleri (belirtilmemişse, _180_ gün) |
|SQL Server 'lar üzerinde denetim dağıtma |İlke ataması |SQL Server denetimi için depolama hesabının kaynak grubu adı |Denetim, veritabanı olaylarını Azure Depolama hesabınızdaki bir denetim günlüğüne yazar (bir depolama hesabı, bu bölgedeki tüm sunucular tarafından paylaşılan bir SQL Server oluşturulduğu her bölgede oluşturulur). Önemli-denetimin düzgün çalışması için kaynak grubunu veya depolama hesaplarını silmeyin veya yeniden adlandırmayın. |
|Ağ güvenlik grupları için tanılama ayarlarını dağıtma |İlke ataması |Ağ güvenlik grubu Tanılama için depolama hesabı öneki |Bu ön ek, oluşturulan depolama hesabı adını biçimlendirmek için ağ güvenlik grubu konumuyla birleştirilir. |
|Ağ güvenlik grupları için tanılama ayarlarını dağıtma |İlke ataması |Ağ güvenlik grubu Tanılama için depolama hesabının kaynak grubu adı (var olmalıdır) |Depolama hesabının oluşturulduğu kaynak grubu. Bu kaynak grubu zaten var olmalıdır. |

## <a name="next-steps"></a>Sonraki adımlar

Medya örneğini dağıtma adımlarını gözden geçirdiğinize göre, genel bakış ve denetim eşlemesi hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [Medya şemaları-genel bakış](./index.md) 
>  [Medya şemaları-denetim eşleme](./control-mapping.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.
