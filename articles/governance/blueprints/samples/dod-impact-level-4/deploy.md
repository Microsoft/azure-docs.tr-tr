---
title: DOD etkisi düzey 4 şema örneği
description: Blueımpprint yapıt parametresi ayrıntıları dahil olmak üzere DOD etkisi düzeyi 4 şema örneği için adımları dağıtın.
ms.date: 04/13/2021
ms.topic: sample
ms.openlocfilehash: ce0d2d162bf77c147e0e4bc26e68964cd50f4372
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378457"
---
# <a name="deploy-the-dod-impact-level-4-blueprint-sample"></a>DOD etkisi düzeyi 4 şema örneğini dağıtma

Savunma etki düzeyi 4 (DOD IL4) şema örnek Azure şemaları departmanı dağıtmak için aşağıdaki adımlar gerçekleştirilmelidir:

> [!div class="checklist"]
> - Örnekten yeni bir şema oluşturma
> - Örneğinizin kopyasını **Yayımlandı** olarak işaretleyin
> - Şemanızın kopyasını mevcut bir aboneliğe atayın

Azure Kamu aboneliğiniz yoksa başlamadan önce bir [deneme aboneliği](https://azure.microsoft.com/global-infrastructure/government/request/) isteyin.

## <a name="create-blueprint-from-sample"></a>Örnekten şema oluştur

İlk olarak, başlangıç noktası olarak örneği kullanarak ortamınızda yeni bir şema oluşturup şema örneğini uygulayın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Soldaki **Başlangıç** sayfasında, _Şema oluştur_ bölümündeki **Oluştur** düğmesini seçin.

1. _Diğer örnekler_ altında **DOD Impact Level 4** şeması örneğini bulun ve **Bu örneği kullan**' ı seçin.

1. Şema örneğinin _Temel bilgiler_ bölümüne girin:

   - Şema **adı**: DOD etkisi düzeyi 4 şema örneği kopyasına bir ad verin.
   - **Tanım konumu**: üç noktayı kullanın ve örneğin kopyasını kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _Yapıtlar_ sekmesini seçin veya sayfanın en altındaki **Sonraki: Yapıtlar** seçeneğini belirleyin.

1. Şema örneğini oluşturan yapıtların listesini gözden geçirin. Yapıtların çoğu parametreler içerir; bu parametreleri daha sonra tanımlayacağız. Şema örneğini gözden geçirmeyi tamamladığınızda **Taslağı Kaydet**’i seçin.

## <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımlama

Şimdi şema örneği kopyanız, ortamınızda oluşturulmuştur. Bu, **Taslak** modunda oluşturulur ve atanabilmesi ve dağıtılabilmesi için önce **Yayımlandı** durumunda olmalıdır. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir, ancak bu değişiklik, DOD etki düzeyi 4 denetimleriyle hizalamadan uzağa taşınabilir.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Sayfanın üst kısmında **Şemayı yayımla**’yı seçin. Sağdaki yeni sayfada, şema örneği kopyanız için bir **Sürüm** sağlayın. Bu özellik daha sonra bir değişiklik yaptığınızda faydalı olacaktır. "DOD IL4 şema örneğinden yayınlanan ilk sürüm" gibi **değişiklik notları** sağlayın. Sayfanın alt kısmında **Yayımla** düğmesini seçin.

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

|Yapıt adı|Yapıt türü|Parametre adı|Açıklama|
|-|-|-|-|
|İzin verilen konumlar|İlke Ataması|İzin Verilen Konumlar|Bu ilke, kuruluşunuzun kaynakları dağıtırken belirleyebileceği konumları kısıtlamanıza olanak verir. Coğrafi uyumluluk gereksinimlerinizi zorunlu kılmak için kullanabilirsiniz.|
|Kaynak grupları için izin verilen konumlar|İlke Ataması |İzin Verilen Konumlar|Bu ilke, kuruluşunuzun içinde kaynak grupları oluşturabileceğiniz konumları kısıtlamanıza olanak sağlar. Coğrafi uyumluluk gereksinimlerinizi zorunlu kılmak için kullanabilirsiniz.|
|SQL Server 'lar üzerinde denetim dağıtma|İlke ataması|Bekletme döneminin gün cinsinden değer (0 sınırsız saklama anlamına gelir)|Bekletme günleri (belirtilmemişse, 180 gün)|
|SQL Server 'lar üzerinde denetim dağıtma|İlke ataması|SQL Server denetimi için depolama hesabının kaynak grubu adı|Denetim, veritabanı olaylarını Azure Depolama hesabınızdaki bir denetim günlüğüne yazar (bir depolama hesabı, bu bölgedeki tüm sunucular tarafından paylaşılacak bir SQL Server oluşturulduğu her bölgede oluşturulur). Önemli-denetimin düzgün çalışması için kaynak grubunu veya depolama hesaplarını silmeyin veya yeniden adlandırmayın.|
|Ağ güvenlik grupları için tanılama ayarlarını dağıtma|İlke ataması|Ağ güvenlik grubu Tanılama için depolama hesabı öneki|Bu ön ek, oluşturulan depolama hesabı adını biçimlendirmek için ağ güvenlik grubu konumuyla birleştirilir.|
|Ağ güvenlik grupları için tanılama ayarlarını dağıtma|İlke ataması|Ağ güvenlik grubu Tanılama için depolama hesabının kaynak grubu adı (var olmalıdır)|Depolama hesabının oluşturulacağı kaynak grubu. Bu kaynak grubu zaten var olmalıdır.|
|Linux sanal makine ölçek kümeleri için Log Analytics aracısı dağıtma|İlke ataması|Linux sanal makine ölçek kümeleri için Log Analytics çalışma alanı|Bu çalışma alanı atama kapsamının dışındaysa, ilke atamasının asıl KIMLIĞINE ' Log Analytics katkıda bulunan ' izinleri (veya benzeri) el ile vermeniz gerekir.|
|Linux sanal makine ölçek kümeleri için Log Analytics aracısı dağıtma|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Linux işletim sistemini destekleyen VM görüntülerinin listesi|Boş bir dizi, isteğe bağlı parametre olmadığını göstermek için kullanılabilir: \[\]|
|Linux sanal makineleri için Log Analytics aracısı dağıtma|İlke ataması|Linux VM 'Ler için Log Analytics çalışma alanı|Bu çalışma alanı atama kapsamının dışındaysa, ilke atamasının asıl KIMLIĞINE ' Log Analytics katkıda bulunan ' izinleri (veya benzeri) el ile vermeniz gerekir.|
|Linux sanal makineleri için Log Analytics aracısı dağıtma|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Linux işletim sistemini destekleyen VM görüntülerinin listesi|Boş bir dizi, isteğe bağlı parametre olmadığını göstermek için kullanılabilir: \[\]|
|Windows sanal makine ölçek kümeleri için Log Analytics aracısı dağıtma|İlke ataması|Windows sanal makine ölçek kümeleri için Log Analytics çalışma alanı|Bu çalışma alanı atama kapsamının dışındaysa, ilke atamasının asıl KIMLIĞINE ' Log Analytics katkıda bulunan ' izinleri (veya benzeri) el ile vermeniz gerekir.|
|Windows sanal makine ölçek kümeleri için Log Analytics aracısı dağıtma|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Windows işletim sistemini destekleyen VM görüntülerinin listesi|Boş bir dizi, isteğe bağlı parametre olmadığını göstermek için kullanılabilir: \[\]|
|Windows VM 'Leri için Log Analytics aracısı dağıtma|İlke ataması|Windows VM 'Leri için Log Analytics çalışma alanı|Bu çalışma alanı atama kapsamının dışındaysa, ilke atamasının asıl KIMLIĞINE ' Log Analytics katkıda bulunan ' izinleri (veya benzeri) el ile vermeniz gerekir.|
|Windows VM 'Leri için Log Analytics aracısı dağıtma|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Windows işletim sistemini destekleyen VM görüntülerinin listesi|Boş bir dizi, isteğe bağlı parametre olmadığını göstermek için kullanılabilir: \[\]|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Yöneticiler yerel grubuna eklenecek Üyeler|Yöneticiler yerel grubunda dışlanması gereken üyelerin noktalı virgülle ayrılmış listesi. Ex: yönetici; myUser1; myUser2|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Yöneticiler yerel grubunda dışlanması gereken Üyeler|Yöneticiler yerel grubuna dahil edilecek üyelerin noktalı virgülle ayrılmış listesi. Ex: yönetici; myUser1; myUser2|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Tanılama günlükleri etkinleştirilmiş olması gereken kaynak türlerinin listesi|Tanılama günlüğü ayarı etkinleştirilmemişse denetlenecek kaynak türleri listesi. Kabul edilebilir değerler, [Azure izleyici tanılama günlükleri şemalarında](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas)bulunabilir.|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|VM 'Lerin yapılandırılması gereken Log Analytics çalışma alanı KIMLIĞI|Bu, VM 'Lerin için yapılandırılması gereken Log Analytics çalışma alanının KIMLIĞIDIR (GUID).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Azure SQL veritabanları için uzun vadeli coğrafi olarak yedekli yedeklemenin etkinleştirilmesi gerekir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|SQL yönetilen örneklerinizin üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Depolama hesapları için coğrafi olarak yedekli depolama etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|MySQL için Azure veritabanı 'nda coğrafi olarak yedekli yedekleme etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|PostgreSQL için Azure veritabanı için coğrafi olarak yedekli yedekleme etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|CORS, her kaynağın Web uygulamanıza erişmesine izin vermemelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : DOD etki düzeyi 4|İlke ataması|Aboneliğinizdeki yazma izinleri olan hesaplarda MFA etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|

## <a name="next-steps"></a>Sonraki adımlar

DOD etkisi düzeyi 4 şema örneğini dağıtma adımlarını gözden geçirdiğinize göre, şema ve denetim eşlemesi hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [DOD etki düzeyi 4 şema-genel bakış](./index.md) 
>  [DOD etki düzeyi 4 şema-Control eşleme](./control-mapping.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.
