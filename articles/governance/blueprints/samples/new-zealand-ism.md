---
title: Yeni Zelanda ISM kısıtlı şema örneği
description: Yeni Zelanda ISM kısıtlı şema örneğine genel bakış. Bu şema örneği, müşterilerin belirli denetimleri değerlendirmesine yardımcı olur.
ms.date: 03/22/2021
ms.topic: sample
ms.openlocfilehash: a52470ea45e6358007dc49122599e87091fbf8f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803939"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>Yeni Zelanda ISM kısıtlı şema örneği

Yeni Zelanda ISM kısıtlı şema örneği, belirli [Yeni Zelanda bilgi güvenliği el ile](https://www.nzism.gcsb.govt.nz/) denetimlerini değerlendirmenize yardımcı olan [Azure ilkesini](../../policy/overview.md) kullanarak idare koruyucu-rayları sağlar. Bu şema, müşterilerin Yeni Zelanda ıSM için denetim uygulaması gereken herhangi bir Azure dağıtılan mimari için bir çekirdek ilke kümesi dağıtmasının sağlanmasına yardımcı olur.

## <a name="control-mapping"></a>Denetim eşlemesi

[Azure ilke denetimi eşleme](../../policy/samples/new-zealand-ism.md) , bu şema içinde yer alan ilke tanımları hakkında ayrıntılar ve bu Ilke tanımlarının Yeni Zelanda bilgi güvenliği el ile olan **denetimlerle** nasıl eşlendiğini açıklar. Bir mimariye atandığında kaynaklar, atanan ilke tanımlarına yönelik uyumsuzluk için Azure Ilkesi tarafından değerlendirilir. Daha fazla bilgi için bkz. [Azure İlkesi](../../policy/overview.md).

## <a name="deploy"></a>Dağıtma

Azure şemaları Yeni Zelanda ISM kısıtlı şema örneğine dağıtmak için aşağıdaki adımların alınması gerekir:

> [!div class="checklist"]
> - Örnekten yeni bir şema oluşturma
> - Örneğinizin kopyasını **Yayımlandı** olarak işaretleyin
> - Şemanızın kopyasını mevcut bir aboneliğe atayın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

### <a name="create-blueprint-from-sample"></a>Örnekten şema oluştur

İlk olarak, başlangıç noktası olarak örneği kullanarak ortamınızda yeni bir şema oluşturup şema örneğini uygulayın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Soldaki **Başlangıç** sayfasında, _Şema oluştur_ bölümündeki **Oluştur** düğmesini seçin.

1. _Diğer örnekler_ altında **Yeni Zelanda ISM kısıtlı** şema örneğini bulun ve **Bu örneği kullan**' ı seçin.

1. Şema örneğinin _Temel bilgiler_ bölümüne girin:

   - **Şema adı**: Yeni Zelanda ISM kısıtlı şema örneğinin kopyasına bir ad verin.
   - **Tanım konumu**: üç noktayı kullanın ve örneğin kopyasını kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _Yapıtlar_ sekmesini seçin veya sayfanın en altındaki **Sonraki: Yapıtlar** seçeneğini belirleyin.

1. Şema örneğine dahil edilen yapıtların listesini gözden geçirin. Yapıtların çoğu parametreler içerir; bu parametreleri daha sonra tanımlayacağız. Şema örneğini gözden geçirmeyi tamamladığınızda **Taslağı Kaydet**’i seçin.

### <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımlama

Şimdi şema örneği kopyanız, ortamınızda oluşturulmuştur. Bu, **Taslak** modunda oluşturulur ve atanabilmesi ve dağıtılabilmesi için önce **Yayımlandı** durumunda olmalıdır. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir, ancak bu değişiklik, Yeni Zelanda ISM kısıtlanmış denetimleriyle hizalamadan uzağa taşınabilir.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Sayfanın üst kısmında **Şemayı yayımla**’yı seçin. Sağdaki yeni sayfada, şema örneği kopyanız için bir **Sürüm** sağlayın. Bu özellik daha sonra bir değişiklik yaptığınızda faydalı olacaktır. "Yeni Zelanda ISM kısıtlamalı şema örneğinden yayımlanan ilk sürüm" gibi **değişiklik notları** sağlayın. Sayfanın alt kısmında **Yayımla** düğmesini seçin.

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
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Windows VM yöneticileri grubuna dahil olması gereken kullanıcıların listesi|Administrators yerel grubuna dahil olması gereken kullanıcıların noktalı virgülle ayrılmış listesi; Ex: yönetici; myUser1; myUser2|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Windows VM Yöneticileri grubundan hariç tutulması gereken kullanıcıların listesi|Yöneticiler yerel grubunda dışlanması gereken kullanıcıların noktalı virgülle ayrılmış listesi; Ex: yönetici; myUser1; myUser2|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Windows VM yöneticileri grubu 'nun yalnızca şunları içermesi gereken kullanıcılar listesi|Administrators yerel grubunun beklenen tüm üyelerinin noktalı virgülle ayrılmış listesi; Ex: yönetici; myUser1; myUser2|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|VM Aracısı raporlama için Log Analytics çalışma alanı KIMLIĞI|VM aracılarının rapor etmesi gereken Log Analytics çalışma alanının KIMLIĞI (GUID)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Web uygulaması güvenlik duvarı (WAF), Azure ön kapı hizmeti için etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: SQL Server için güvenlik açığı değerlendirmesi ayarları, tarama raporlarını alacak bir e-posta adresi içermelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Uyarlamalı ağ sağlamlaştırma önerileri internet 'e yönelik sanal makinelere uygulanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için efekt: aboneliğinize birden fazla sahip atanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: disk şifrelemesi sanal makinelere uygulanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Işlev uygulamaları için uzaktan hata ayıklama kapatılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Web uygulaması güvenlik duvarı (WAF) Application Gateway için belirtilen modu kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Application Gateway için WAF modu gereksinimi|Önleme veya algılama modunun Application Gateway hizmetinde etkinleştirilmesi gerekir|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: SQL yönetilen örneği 'nde güvenlik açığı değerlendirmesi etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İsteğe bağlı: ilke için galerisindeki görüntülere ek eklemek üzere desteklenen Windows işletim sistemi görüntülerinin listesi: dağıt-Windows sanal makinelerinde etkinleştirilecek bağımlılık aracısını yapılandırma|Konuk yapılandırması hakkında daha fazla bilgi için şu adresi ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: yalnızca Redsıs için Azure önbelleğinize güvenli bağlantılar etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Endpoint Protection çözümünün sanal makine ölçek kümelerine yüklenmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke değerlendirilirken yay bağlantılı sunucuları dahil et: Yöneticiler grubunda belirtilen üyelerin hiçbirini denetleme Windows makinelerini denetleyin|' True ' seçeneğini belirleyerek, her yay bağlantılı makine başına aylık olarak ücretlendirildiğiniz kabul etmiş olursunuz|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İsteğe bağlı: ilke galerisindeki görüntülere ek eklemek için Windows işletim sistemi 'nin desteklediği özel VM görüntülerinin listesi: [Önizleme]: Log Analytics Aracısı listelenen sanal makine görüntüleri için etkinleştirilmelidir|Konuk yapılandırması hakkında daha fazla bilgi için şu adresi ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İsteğe bağlı: ilke galerisindeki görüntülere ek eklemek için desteklenen Linux işletim sistemini destekleyen özel VM görüntülerinin listesi: [Önizleme]: Log Analytics Aracısı listelenen sanal makine görüntüleri için etkinleştirilmelidir|Konuk yapılandırması hakkında daha fazla bilgi için şu adresi ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: depolama hesapları ağ erişimini kısıtlamalı|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İsteğe bağlı: ilke için galerisindeki görüntülere ek eklemek üzere desteklenen Windows işletim sistemi görüntülerinin listesi: dağıt-Windows sanal makine ölçek kümelerinde etkinleştirilecek bağımlılık aracısını yapılandırma|Konuk yapılandırması hakkında daha fazla bilgi için şu adresi ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke değerlendirilirken yay bağlantılı sunucuları dahil et: Yöneticiler grubunda ek hesaplara sahip Windows makinelerini denetleme|' True ' seçeneğini belirleyerek, her yay bağlantılı makine başına aylık olarak ücretlendirildiğiniz kabul etmiş olursunuz|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: depolama hesaplarına Güvenli aktarım etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Web uygulaması güvenlik duvarı (WAF), Azure ön kapı hizmeti için belirtilen modu kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Azure ön kapı hizmeti için WAF modu gereksinimi|Azure ön kapı hizmeti 'nde önleme veya algılama modunun etkinleştirilmesi gerekir|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: makinelerinizde güvenli uygulamalar tanımlamaya yönelik Uyarlamalı uygulama denetimleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için efekt: aboneliğiniz için en fazla 3 sahip belirtilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: [Önizleme]: depolama hesabı genel erişimine izin verilmemelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: sanal makinelerinizde bir güvenlik açığı değerlendirme çözümünün etkinleştirilmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Web uygulaması güvenlik duvarı (WAF) Application Gateway için etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: CORS her kaynağın Web uygulamalarınıza erişmesine izin vermemelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke değerlendirilirken yaya bağlı sunucuları dahil et: güvenli iletişim protokolleri kullanmayan Windows Web sunucularını denetleme|' True ' seçeneğini belirleyerek, her yay bağlantılı makine başına aylık olarak ücretlendirildiğiniz kabul etmiş olursunuz|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Windows Web sunucuları için en düşük TLS sürümü|TLS sürümleri daha düşük olan Windows Web sunucuları uyumlu değil olarak değerlendirilir|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İsteğe bağlı: ilke galerisindeki görüntülere ek eklemek için desteklenen Linux işletim sistemini destekleyen özel VM görüntülerinin listesi: Log Analytics Aracısı, listelenen sanal makine görüntüleri için sanal makine ölçek kümelerinde etkinleştirilmelidir|Konuk yapılandırması hakkında daha fazla bilgi için şu adresi ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İsteğe bağlı: ilke galerisindeki görüntülere ek eklemek için desteklenen Windows işletim sistemini destekleyen özel VM görüntülerinin listesi: Log Analytics Aracısı, listelenen sanal makine görüntüleri için sanal makine ölçek kümelerinde etkinleştirilmelidir|Konuk yapılandırması hakkında daha fazla bilgi için şu adresi ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için efekt: yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke değerlendirilirken yaya bağlı sunucuları dahil et: Yöneticiler grubunda belirtilen üyelere sahip olan Windows makinelerini denetle|' True ' seçeneğini belirleyerek, her yay bağlantılı makine başına aylık olarak ücretlendirildiğiniz kabul etmiş olursunuz|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Azure aboneliklerinin etkinlik günlüğü için bir günlük profili olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Tanılama günlükleri etkinleştirilmiş olması gereken kaynak türlerinin listesi||
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: sistem güncelleştirmelerinin makinelerinizde yüklü olması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: API uygulamanızda en son TLS sürümü kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için efekt: MFA 'nın aboneliğinizde yazma izinleri olan hesaplar etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Microsoft ıaasantimalware uzantısı, Windows Server 'lar üzerinde dağıtılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için efekt: Azure DDoS koruma standardı etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için efekt: MFA 'nın aboneliğinizdeki sahip izinlerine sahip hesaplarda etkinleştirilmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Gelişmiş veri güvenliği SQL yönetilen örneği üzerinde etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Azure Güvenlik Merkezi 'nde eksik Endpoint Protection Izleyicisi|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: etkinlik günlüğü en az bir yıl bekletilecektir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: sanal makinelerin yönetim bağlantı noktaları, tam zamanında ağ erişim denetimiyle korunmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için efekt: Service Fabric kümeler yalnızca istemci kimlik doğrulaması için Azure Active Directory kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Windows Defender Exploit Guard 'ın etkinleştirilmediği Windows makinelerini denetleyin|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke değerlendirilirken yaya bağlı sunucuları dahil et: Windows Defender Exploit Guard 'ın etkinleştirilmediği Windows makinelerini denetleyin|' True ' seçeneğini belirleyerek, her yay bağlantılı makine başına aylık olarak ücretlendirildiğiniz kabul etmiş olursunuz|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Windows Defender Exploit Guard 'ın kullanılamadığı Windows makinelerini raporlamak için uyumluluk durumu|Windows Defender Exploit Guard, Windows 10/Windows Server güncelleştirme 1709 ile başlayarak kullanılabilir. Bu değerin ' uyumlu değil ' olarak ayarlanması, Windows Defender Exploit Guard 'ın (Windows Server 2012 R2 gibi) uyumlu olmayan eski sürümlerine sahip makineleri gösterir. Bu değerin ' uyumlu ' olarak ayarlanması, bu makinelerin uyumlu olduğunu gösterir.|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke etkisi: Web uygulamaları için uzaktan hata ayıklama kapatılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için efekt: MFA 'nın aboneliğinizdeki okuma izinlerine sahip hesaplarda etkinleştirilmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için efekt: uzaktan hata ayıklama API Apps için kapalı olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: parola olmadan hesaplardan uzak bağlantılara izin veren Linux makinelerini denetleme|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke değerlendirilirken yaya bağlı sunucuları dahil et: parola olmadan hesaplardan uzak bağlantılara izin veren Linux makinelerini denetleme|' True ' seçeneğini belirleyerek, her yay bağlantılı makine başına aylık olarak ücretlendirildiğiniz kabul etmiş olursunuz|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke etkisi: sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Web uygulamanızda en son TLS sürümü kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Windows makinelerin ' güvenlik ayarları-hesap Ilkeleri ' gereksinimlerini karşılaması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Windows VM yerel hesapları için parola geçmişini zorla|Parolanın tekrarlanmadan önce bir kullanıcı hesabı için kaç kez yeni parola oluşturulması gerektiği ile parola yeniden kullanımı sınırlarını belirtir|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlkeyi değerlendirirken yaya bağlı sunucuları dahil et: Windows makineleri ' güvenlik ayarları-hesap Ilkeleri ' gereksinimlerini karşılamalıdır|' True ' seçeneğini belirleyerek, her yay bağlantılı makine başına aylık olarak ücretlendirildiğiniz kabul etmiş olursunuz|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Windows VM yerel hesapları için en fazla parola yaşı|Kullanıcı hesabı parolasının değiştirilebilmesi için geçmesi gereken en fazla gün sayısını belirtir; değerin biçimi virgülle ayrılmış iki tamsayılardır ve kapsamlı bir Aralık belirlenir|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Windows VM yerel hesapları için en az parola yaşı|Kullanıcı hesabı parolasının değiştirilebilmesi için geçmesi gereken en az gün sayısını belirtir|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Windows VM yerel hesapları için en az parola uzunluğu|Bir kullanıcı hesabı parolasının içerebileceği en az karakter sayısını belirtir|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|Parolanın Windows VM yerel hesapları için karmaşıklık gereksinimlerini karşılaması gerekir|Bir kullanıcı hesabı parolasının karmaşık olması gerekip gerekmediğini belirtir; gerekirse, karmaşık bir parola kullanıcının hesap adının veya tam adının bir kısmını içermemelidir; en az 6 karakter uzunluğunda olmalıdır; büyük harf, küçük harf, sayı ve alfabetik olmayan karakterlerin bir karışımını içerir|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: parola olmayan hesaplara sahip Linux makinelerini denetleyin|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke değerlendirilirken yaya bağlı sunucuları dahil et: parola olmayan hesaplara sahip Linux makinelerini denetleyin|' True ' seçeneğini belirleyerek, her yay bağlantılı makine başına aylık olarak ücretlendirildiğiniz kabul etmiş olursunuz|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: İşlev Uygulaması en son TLS sürümü kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: [Önizleme]: tüm Internet trafiği, dağıtılan Azure Güvenlik duvarınız aracılığıyla yönlendirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Yeni Zelanda ıSM kısıtlanmış|İlke Ataması|İlke için etki: SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|

## <a name="next-steps"></a>Sonraki adımlar

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.