---
title: Avustralya kamu ile korunan şema örneğini dağıtma
description: Şema yapıt parametresi ayrıntıları dahil olmak üzere Avustralya kamu ile korunan şema örneği için adımları dağıtın.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: c97c8b180b621bf1e583482d67fb203460a145cb
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386439"
---
# <a name="deploy-the-australian-government-ism-protected-blueprint-sample"></a>Avustralya kamu ile korunan şema örneğini dağıtma

Azure şemaları ile korunan şema örneğini dağıtmak için aşağıdaki adımlar gerçekleştirilmelidir:

> [!div class="checklist"]
> - Örnekten yeni bir şema oluşturma
> - Örneğinizin kopyasını **Yayımlandı** olarak işaretleyin
> - Şemanızın kopyasını mevcut bir aboneliğe atayın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="create-blueprint-from-sample"></a>Örnekten şema oluştur

İlk olarak, başlangıç noktası olarak örneği kullanarak ortamınızda yeni bir şema oluşturup şema örneğini uygulayın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Soldaki **Başlangıç** sayfasında, _Şema oluştur_ bölümündeki **Oluştur** düğmesini seçin.

1. _Diğer örnekler_ altında **ronunalý** şema örneğini bulun ve **Bu örneği kullan**' ı seçin.

1. Şema örneğinin _Temel bilgiler_ bölümüne girin:

   - **Şema adı**: ronizlem Protected şeması örneğinin kopyasına bir ad verin.
   - **Tanım konumu**: üç noktayı kullanın ve örneğin kopyasını kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _Yapıtlar_ sekmesini seçin veya sayfanın en altındaki **Sonraki: Yapıtlar** seçeneğini belirleyin.

1. Şema örneğini oluşturan yapıtların listesini gözden geçirin. Yapıtların çoğu parametreler içerir; bu parametreleri daha sonra tanımlayacağız. Şema örneğini gözden geçirmeyi tamamladığınızda **Taslağı Kaydet**’i seçin.

## <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımlama

Şimdi şema örneği kopyanız, ortamınızda oluşturulmuştur. Bu, **Taslak** modunda oluşturulur ve atanabilmesi ve dağıtılabilmesi için önce **Yayımlandı** durumunda olmalıdır. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir, ancak bu değişiklik, söz konusu değişikliği, ISM korumalı denetimlerle hizalamadan uzağa taşıyabilir.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Sayfanın üst kısmında **Şemayı yayımla**’yı seçin. Sağdaki yeni sayfada, şema örneği kopyanız için bir **Sürüm** sağlayın. Bu özellik daha sonra bir değişiklik yaptığınızda faydalı olacaktır. "ISM korumalı şema örneğinden yayımlanan ilk sürüm" gibi **değişiklik notları** sağlayın. Sayfanın alt kısmında **Yayımla** düğmesini seçin.

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
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|VM 'Lerin yapılandırılması gereken Log Analytics çalışma alanı KIMLIĞI|Bu, VM 'Lerin için yapılandırılması gereken Log Analytics çalışma alanının KIMLIĞIDIR (GUID).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Tanılama günlükleri etkinleştirilmiş olması gereken kaynak türlerinin listesi|Tanılama günlüğü ayarı etkinleştirilmemişse denetlenecek kaynak türleri listesi. Kabul edilebilir değerler, [Azure izleyici kaynak günlükleri kategorilerinde](../../../../azure-monitor/essentials/resource-logs-categories.md#supported-log-categories-per-resource-type)bulunabilir.|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Windows VM Yöneticileri grubundan hariç tutulması gereken kullanıcıların listesi|Yöneticiler yerel grubunda dışlanması gereken üyelerin noktalı virgülle ayrılmış listesi. Ex: yönetici; myUser1; myUser2|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Windows VM yöneticileri grubuna dahil edilecek kullanıcıların listesi|Yöneticiler yerel grubuna dahil edilecek üyelerin noktalı virgülle ayrılmış listesi. Ex: yönetici; myUser1; myUser2|
|\[Önizleme \] : LINUX VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)|İlke ataması|Linux VM Ölçek Kümeleri için Log Analytics çalışma alanı (VMSS)|Bu çalışma alanı atama kapsamının dışındaysa, ilke atamasının asıl KIMLIĞINE ' Log Analytics katkıda bulunan ' izinleri (veya benzeri) el ile vermeniz gerekir.|
|\[Önizleme \] : LINUX VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Linux işletim sistemini destekleyen VM görüntülerinin listesi|Boş bir dizi, isteğe bağlı parametre olmadığını göstermek için kullanılabilir: \[\]|
|\[Önizleme \] : Linux VM 'ler için Log Analytics aracısı dağıtma|İlke ataması|Linux VM 'Ler için Log Analytics çalışma alanı|Bu çalışma alanı atama kapsamının dışındaysa, ilke atamasının asıl KIMLIĞINE ' Log Analytics katkıda bulunan ' izinleri (veya benzeri) el ile vermeniz gerekir.|
|\[Önizleme \] : Linux VM 'ler için Log Analytics aracısı dağıtma|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Linux işletim sistemini destekleyen VM görüntülerinin listesi|Boş bir dizi, isteğe bağlı parametre olmadığını göstermek için kullanılabilir: \[\]|
|\[Önizleme \] : WINDOWS VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)|İlke ataması|Windows VM Ölçek Kümeleri için Log Analytics çalışma alanı (VMSS)|Bu çalışma alanı atama kapsamının dışındaysa, ilke atamasının asıl KIMLIĞINE ' Log Analytics katkıda bulunan ' izinleri (veya benzeri) el ile vermeniz gerekir.|
|\[Önizleme \] : WINDOWS VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Windows işletim sistemini destekleyen VM görüntülerinin listesi|Boş bir dizi, isteğe bağlı parametre olmadığını göstermek için kullanılabilir: \[\]|
|\[Önizleme \] : Windows VM 'leri için Log Analytics aracısı dağıtma|İlke ataması|Windows VM 'Leri için Log Analytics çalışma alanı|Bu çalışma alanı atama kapsamının dışındaysa, ilke atamasının asıl KIMLIĞINE ' Log Analytics katkıda bulunan ' izinleri (veya benzeri) el ile vermeniz gerekir.|
|\[Önizleme \] : Windows VM 'leri için Log Analytics aracısı dağıtma|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Windows işletim sistemini destekleyen VM görüntülerinin listesi|Boş bir dizi, isteğe bağlı parametre olmadığını göstermek için kullanılabilir: \[\]|
|Depolama hesaplarında Gelişmiş tehdit koruması dağıtma|İlke ataması|Etki|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|SQL Server 'lar üzerinde denetim dağıtma|İlke ataması|Bekletme döneminin gün cinsinden değer (0 sınırsız saklama anlamına gelir)|Bekletme günleri (belirtilmemişse, 180 gün)|
|SQL Server 'lar üzerinde denetim dağıtma|İlke ataması|SQL Server denetimi için depolama hesabının kaynak grubu adı|Denetim, veritabanı olaylarını Azure Depolama hesabınızdaki bir denetim günlüğüne yazar (bir depolama hesabı, bu bölgedeki tüm sunucular tarafından paylaşılacak bir SQL Server oluşturulduğu her bölgede oluşturulur). Önemli-denetimin düzgün çalışması için kaynak grubunu veya depolama hesaplarını silmeyin veya yeniden adlandırmayın.|
|Ağ güvenlik grupları için tanılama ayarlarını dağıtma|İlke ataması|Ağ güvenlik grubu Tanılama için depolama hesabı öneki|Bu ön ek, oluşturulan depolama hesabı adını biçimlendirmek için ağ güvenlik grubu konumuyla birleştirilir.|
|Ağ güvenlik grupları için tanılama ayarlarını dağıtma|İlke ataması|Ağ güvenlik grubu Tanılama için depolama hesabının kaynak grubu adı (var olmalıdır)|Depolama hesabının oluşturulacağı kaynak grubu. Bu kaynak grubu zaten var olmalıdır.|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Kaynaklar ve kaynak grupları için izin verilen konumlar|Kuruluşunuzun, kaynakları dağıtmada belirtebileceğiniz Azure konumları listesi. Bu verilen değer, ilke girişimi içindeki ' Izin verilen konumlar ' ilkesi tarafından da kullanılır.|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|SQL yönetilen örneklerinizin üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Güvenlik açığı değerlendirmesi sanal makinelerde etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Depolama hesapları için coğrafi olarak yedekli depolama etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|MariaDB için Azure veritabanı 'nda coğrafi olarak yedekli yedekleme etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|MySQL için Azure veritabanı 'nda coğrafi olarak yedekli yedekleme etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|PostgreSQL için Azure veritabanı için coğrafi olarak yedekli yedekleme etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|İnternet 'e yönelik sanal makineler için ağ güvenlik grubu kuralları sağlamlaştırılmış olmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|CORS, her kaynağın Web uygulamanıza erişmesine izin vermemelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Aboneliğinizdeki yazma izinleri olan hesaplarda MFA etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Azure SQL veritabanları için uzun vadeli coğrafi olarak yedekli yedeklemenin etkinleştirilmesi gerekir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Windows VM Yöneticileri grubundan dışlanan kullanıcıların listesi|Yöneticiler yerel grubunda dışlanması gereken üyelerin noktalı virgülle ayrılmış listesi. Ex: yönetici; myUser1; myUser2|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|VM 'Lerin yapılandırılması gereken Log Analytics çalışma alanı kimliği|Bu, VM 'Lerin için yapılandırılması gereken Log Analytics çalışma alanının KIMLIĞIDIR (GUID).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|SQL Server için güvenlik açığı değerlendirmesi ayarları, tarama raporlarını alacak bir e-posta adresi içermelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Uyarlamalı ağ sağlamlaştırma önerileri internet 'e yönelik sanal makinelere uygulanmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Aboneliğinize birden fazla sahip atanmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Disk şifrelemesi sanal makinelere uygulanmalıdır| İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|İşlev Uygulaması için uzaktan hata ayıklama kapatılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir| İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|SQL yönetilen örneklerinizin üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Yalnızca Redis Cache güvenli bağlantılar etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Uç nokta koruma çözümü, sanal makine ölçek kümelerine yüklenmelidir |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Windows işletim sistemini destekleyen VM görüntülerinin listesi|İsteğe bağlı parametre olmadığını göstermek için boş bir dizi kullanılabilir: []
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Linux işletim sistemini destekleyen VM görüntülerinin listesi |İsteğe bağlı parametre olmadığını göstermek için boş bir dizi kullanılabilir: []|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Depolama hesaplarına Kısıtlanmamış ağ erişimini denetleme|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Depolama hesaplarına güvenli aktarım etkinleştirilmelidir  |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Uyarlamalı uygulama denetimleri sanal makinelerde etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Aboneliğiniz için en fazla 3 sahip belirtilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Önizle Güvenlik açığı değerlendirmesi sanal makinelerde etkinleştirilmelidir  |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|CORS, her kaynağın Web uygulamanıza erişmesine izin vermemelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir |  İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|İşlev Uygulaması yalnızca HTTPS v2 üzerinden erişilebilir olmalıdır  |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir  |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Azure aboneliklerinin etkinlik günlüğü için bir günlük profili olmalıdır  |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Tanılama günlükleri etkinleştirilmiş olması gereken kaynak türlerinin listesi|Tanılama günlüğü ayarı etkinleştirilmemişse denetlenecek kaynak türleri listesi. Kabul edilebilir değerler, [Azure izleyici kaynak günlükleri kategorilerinde](../../../../azure-monitor/essentials/resource-logs-categories.md#supported-log-categories-per-resource-type)bulunabilir.|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Makinelerinize sistem güncelleştirmeleri yüklenmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|App Service için en son TLS sürümü kullanılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Microsoft ıaasantimalware uzantısı Windows Server 'lar üzerinde dağıtılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Web uygulamasına yalnızca HTTPS v2 üzerinden erişilebilir olması gerekir  |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|DDoS koruma standardı etkinleştirilmelidir  |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Gelişmiş veri güvenliği, SQL yönetilen örneklerinizin üzerinde etkinleştirilmelidir |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Azure Güvenlik Merkezi 'nde eksik Endpoint Protection 'ı izleme  |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Tam zamanında ağ erişim denetimi, sanal makinelere uygulanmalıdır |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Service Fabric kümeler yalnızca istemci kimlik doğrulaması için Azure Active Directory kullanmalıdır |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|App Service yalnızca HTTPS v2 üzerinden erişilebilir olmalıdır |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir  |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Web uygulaması için uzaktan hata ayıklama kapatılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir  |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md).|
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir  |İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Parola geçmişini zorla  |  Parolanın tekrarlanmadan önce bir kullanıcı hesabı için kaç kez yeni parola oluşturulması gerektiği ile parola yeniden kullanımı sınırlarını belirtir. |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Maksimum parola yaşı  |  Kullanıcı hesabı parolasının değiştirilebilmesi için geçmesi gereken en fazla gün sayısını belirtir. Değerin biçimi virgülle ayrılmış iki tamsayılardır ve kapsamlı bir Aralık belirlenir. |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|En az parola yaşı |   Kullanıcı hesabı parolasının değiştirilebilmesi için geçmesi gereken en az gün sayısını belirtir. |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Minimum parola uzunluğu  |   Bir kullanıcı hesabı parolasının içerebileceği en az karakter sayısını belirtir. |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Parolanın karmaşıklık gereksinimlerini karşılaması gerekir|Bir kullanıcı hesabı parolasının karmaşık olması gerekip gerekmediğini belirtir. Gerekirse, karmaşık bir parola kullanıcının hesap adının veya tam adının bir kısmını içermemelidir; en az 6 karakter uzunluğunda olmalıdır; büyük harf, küçük harf, sayı ve alfabetik olmayan karakterlerin bir karışımını içerir. |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|App Service için uzaktan hata ayıklama kapatılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Web uygulamanızda en son TLS sürümü kullanılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|İşlev Uygulaması en son TLS sürümü kullanılmalıdır|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |
|\[Önizleme \] : denetim gereksinimlerini desteklemek Için Avustralya kamu Ile korunan denetimleri denetleme ve belırlı VM uzantılarını dağıtma|İlke ataması|SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir|İlke etkileri hakkında daha fazla bilgi için bkz. [Azure Ilke efektlerini anlama](../../../policy/concepts/effects.md). |

## <a name="next-steps"></a>Sonraki adımlar

Avustralya kamu ile korunan şema örneğini dağıtma adımlarını gözden geçirdiğinize göre, şema ve denetim eşlemesi hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [ISM korumalı şema-genel bakış](./index.md) 
>  [ISM korumalı şema-Control eşleme](./control-mapping.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.