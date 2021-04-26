---
title: Cmmc düzey 3 şema örneği
description: Cmmc düzey 3 şema örneğine genel bakış. Bu şema örneği, müşterilerin belirli denetimleri değerlendirmesine yardımcı olur.
ms.date: 03/24/2021
ms.topic: sample
ms.openlocfilehash: 950c6064ce8b3d9973ac08e5895a4b6f48e37d6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572661"
---
# <a name="cmmc-level-3-blueprint-sample"></a>Cmmc düzey 3 şema örneği

Cmmc düzey 3 şema örneği, belirli bir [siber güvenlik vade modeli sertifikası (cmmc) çerçeve](https://www.acq.osd.mil/cmmc/index.html) denetimlerini değerlendirmenize yardımcı olan [Azure ilkesini](../../policy/overview.md) kullanarak idare koruması-rayları sağlar. Bu şema, müşterilerin CMMC düzey 3 için denetimler uygulaması gereken herhangi bir Azure dağıtılan mimari için çekirdek bir ilke kümesini dağıtmasının sağlanmasına yardımcı olur.

## <a name="control-mapping"></a>Denetim eşlemesi

[Azure ilke denetimi eşleme](../../policy/samples/cmmc-l3.md) , bu şema içinde yer alan ilke tanımları hakkında ayrıntılar ve bu ilke tanımlarının cmmc çerçevesindeki **denetimlerle** nasıl eşlendiğini açıklar. Bir mimariye atandığında kaynaklar, atanan ilke tanımlarına yönelik uyumsuzluk için Azure Ilkesi tarafından değerlendirilir. Daha fazla bilgi için bkz. [Azure İlkesi](../../policy/overview.md).

## <a name="deploy"></a>Dağıtma

Azure planları cmmc düzey 3 şema örneğini dağıtmak için aşağıdaki adımlar gerçekleştirilmelidir:

> [!div class="checklist"]
> - Örnekten yeni bir şema oluşturma
> - Örneğinizin kopyasını **Yayımlandı** olarak işaretleyin
> - Şemanızın kopyasını mevcut bir aboneliğe atayın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

### <a name="create-blueprint-from-sample"></a>Örnekten şema oluştur

İlk olarak, başlangıç noktası olarak örneği kullanarak ortamınızda yeni bir şema oluşturup şema örneğini uygulayın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Soldaki **Başlangıç** sayfasında, _Şema oluştur_ bölümündeki **Oluştur** düğmesini seçin.

1. _Diğer örnekler_ altında **cmmc düzey 3** şema örneğini bulun ve **Bu örneği kullan**' ı seçin.

1. Şema örneğinin _Temel bilgiler_ bölümüne girin:

   - **Şema adı**: cmmc düzey 3 şema örneğinin kopyasına bir ad verin.
   - **Tanım konumu**: üç noktayı kullanın ve örneğin kopyasını kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _Yapıtlar_ sekmesini seçin veya sayfanın en altındaki **Sonraki: Yapıtlar** seçeneğini belirleyin.

1. Şema örneğine dahil edilen yapıtların listesini gözden geçirin. Yapıtların çoğu parametreler içerir; bu parametreleri daha sonra tanımlayacağız. Şema örneğini gözden geçirmeyi tamamladığınızda **Taslağı Kaydet**’i seçin.

### <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımlama

Şimdi şema örneği kopyanız, ortamınızda oluşturulmuştur. Bu, **Taslak** modunda oluşturulur ve atanabilmesi ve dağıtılabilmesi için önce **Yayımlandı** durumunda olmalıdır. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir, ancak bu değişiklik, cmmc düzey 3 denetimleriyle hizalamadan uzağa taşınabilir.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Sayfanın üst kısmında **Şemayı yayımla**’yı seçin. Sağdaki yeni sayfada, şema örneği kopyanız için bir **Sürüm** sağlayın. Bu özellik daha sonra bir değişiklik yaptığınızda faydalı olacaktır. "Cmmc düzey 3 şema örneğinden yayınlanan ilk sürüm" gibi **değişiklik notları** sağlayın. Sayfanın alt kısmında **Yayımla** düğmesini seçin.

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
|CMMC düzey 3|İlke Ataması|Konuk yapılandırma ilkelerini değerlendirirken yay bağlantılı sunucuları dahil et|' True ' seçeneğini belirleyerek, her yay bağlantılı makine başına aylık olarak ücretlendirildiğiniz kabul etmiş olursunuz; daha fazla bilgi için şu adresi ziyaret edin https://aka.ms/policy-pricing|
|CMMC düzey 3|İlke Ataması|Windows VM Yöneticileri grubundan hariç tutulması gereken kullanıcıların listesi|Yöneticiler yerel grubunda dışlanması gereken kullanıcıların noktalı virgülle ayrılmış listesi; Ex: yönetici; myUser1; myUser2|
|CMMC düzey 3|İlke Ataması|Windows VM yöneticileri grubuna dahil olması gereken kullanıcıların listesi|Administrators yerel grubuna dahil olması gereken kullanıcıların noktalı virgülle ayrılmış listesi; Ex: yönetici; myUser1; myUser2|
|CMMC düzey 3|İlke Ataması|VM Aracısı raporlama için Log Analytics çalışma alanı KIMLIĞI|VM aracılarının rapor etmesi gereken Log Analytics çalışma alanının KIMLIĞI (GUID)|
|CMMC düzey 3|İlke Ataması|İzin verilen eliptik eğri adları|Eliptik Eğri Şifreleme sertifikaları için izin verilen eğri adları listesi.|
|CMMC düzey 3|İlke Ataması|İzin verilen anahtar türleri|İzin verilen anahtar türleri listesi|
|CMMC düzey 3|İlke Ataması|Kubernetes kümesi pod için konak ağı kullanımına izin ver|Pod 'ın konak ağını kullanmasına izin veriliyorsa, aksi takdirde bu değeri true olarak ayarlayın.|
|CMMC düzey 3|İlke Ataması|Kimlik Doğrulama İlkesi Değişikliğini Denetleme|Kimlik doğrulama ilkesinde değişiklik yapıldığında denetim olaylarının oluşturulup oluşturulmayacağını belirtir. Bu ayar, etki alanı düzeyinde ve orman düzeyinde güven ve Kullanıcı hesaplarına veya gruplara verilen ayrıcalıklardaki değişiklikleri izlemek için yararlıdır.|
|CMMC düzey 3|İlke Ataması|Yetkilendirme İlkesi Değişikliğini Denetleme|Kullanıcı hakkının kullanıcı haklarının atanması ve kaldırılması için denetim olaylarının oluşturulup oluşturulmayacağını, güvenlik belirteci nesne iznindeki değişiklikleri, kaynak öznitelikleri değişikliğini ve dosya sistemi nesneleri için merkezi erişim Ilkesi değişikliklerini belirtir.|
|CMMC düzey 3|İlke Ataması|İlke için etki: Azure Backup sanal makineler için etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: bilişsel hizmetler hesapları ağ erişimini kısıtlamalı|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: SQL yönetilen örnekler, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: FHıR için Azure API, bekleyen verileri şifrelemek için müşteri tarafından yönetilen bir anahtar (CMK) kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Web uygulaması güvenlik duvarı (WAF), Azure ön kapı hizmeti için etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: SQL Server için güvenlik açığı değerlendirmesi ayarları, tarama raporlarını alacak bir e-posta adresi içermelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: bilişsel hizmetler hesapları için genel ağ erişimi devre dışı bırakılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: CORS her kaynağın Işlev uygulamalarınıza erişmesine izin vermemelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Uyarlamalı ağ sağlamlaştırma önerileri internet 'e yönelik sanal makinelere uygulanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: aboneliğinize birden fazla sahip atanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: disk şifrelemesi sanal makinelere uygulanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: yüksek önem derecesi uyarıları için abonelik sahibine e-posta bildirimi etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: Anahtar Kasası, temizleme koruması etkin olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: SQL Server 'lar, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Işlev uygulamaları için uzaktan hata ayıklama kapatılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Key Vault için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke etkisi: MariaDB için Azure veritabanı için coğrafi olarak yedekli yedekleme etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etkinlik: CORS her etki alanının FHıR için API 'nize erişmesine izin vermemelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Windows makinelerin ' güvenlik seçenekleri-ağ güvenliği ' gereksinimlerini karşılaması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Uyarlamalı uygulama denetim ilkenizde Allowlist kuralları güncelleştirilmeleri gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Web uygulaması güvenlik duvarı (WAF) Application Gateway için belirtilen modu kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: anahtarların süre sonu tarihleri ayarlanmış olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Azure Izleyici günlük profili, ' Write, ' ' DELETE, ' ve ' Action ' kategorilerinin günlüklerini toplamalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: SQL yönetilen örneği 'nde güvenlik açığı değerlendirmesi etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: API uygulamasının bir parçası olarak kullanılıyorsa ' PHP Version ' öğesinin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: Anahtar Kasası geçici silme etkin olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: yalnızca Redsıs için Azure önbelleğinize güvenli bağlantılar etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke etkisi: PostgreSQL için Azure veritabanı sunucuları için altyapı şifreleme etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Endpoint Protection çözümünün sanal makine ölçek kümelerine yüklenmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: App Service için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Windows makinelerin ' sistem denetim Ilkeleri-Ilke değişikliği ' gereksinimlerini karşılaması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: bilişsel hizmetler hesapları veri şifrelemeyi etkinleştirmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Internet 'ten SSH erişiminin engellenmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: eklenmemiş diskler şifrelenmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: depolama için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: depolama hesapları ağ erişimini kısıtlamalı|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: CORS her kaynağın API uygulamanıza erişmesine izin vermemelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: depolama hesaplarında Gelişmiş tehdit koruması dağıtma|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Otomasyon hesabı değişkenleri şifrelenmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: IoT Hub içindeki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: MySQL için Azure veritabanı sunucuları için altyapı şifreleme etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: belirli güvenlik işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. Security/securitySolutions/Delete)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Windows makinelerin ' güvenlik seçenekleri-ağ erişimi ' gereksinimlerini karşılaması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: depolama hesaplarına Güvenli aktarım etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Azure Izleyici, tüm bölgelerdeki etkinlik günlüklerini toplamalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Web uygulaması güvenlik duvarı (WAF), Azure ön kapı hizmeti için belirtilen modu kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: depolama hesaplarının altyapı şifrelemesi olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: makinelerinizde güvenli uygulamalar tanımlamaya yönelik Uyarlamalı uygulama denetimleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke etkisi: PostgreSQL için Azure veritabanı için coğrafi olarak yedekli yedekleme etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Windows makineleri, ' güvenlik seçenekleri-Kullanıcı hesabı denetimi ' gereksinimlerini karşılamalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: Web uygulamasının bir parçası olarak kullanılıyorsa ' Java sürümü ' nin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: sunucular için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: aboneliğiniz için en fazla 3 sahip belirtilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke etkisi: abonelikler güvenlik sorunları için bir iletişim e-posta adresine sahip olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: depolama hesabı genel erişimine izin verilmemelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: sanal makinelerinizde bir güvenlik açığı değerlendirme çözümünün etkinleştirilmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Kubernetes için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: güvenlik duvarının Key Vault üzerinde etkinleştirilmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Web uygulaması güvenlik duvarı (WAF) Application Gateway için etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: CORS her kaynağın Web uygulamalarınıza erişmesine izin vermemelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: önceki 24 parolanın yeniden kullanılmasına izin veren Windows makinelerini denetleyin|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: kapsayıcı kayıt defterleri, müşteri tarafından yönetilen bir anahtarla şifrelenmelidir (CMK)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: ortak ağ erişimi PostgreSQL esnek sunucuları için devre dışı bırakılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Azure Container Registry görüntülerdeki güvenlik açıkları düzeltilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Service Fabric kümelerinde ClusterProtectionLevel özelliği EncryptAndSign olarak ayarlanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: makinelerdeki SQL Server 'lar için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: bilişsel hizmetler hesapları, müşteri tarafından yönetilen anahtarla veri şifrelemeyi etkinleştirmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: yüksek önem derecesine sahip uyarılar için e-posta bildirimi etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: depolama hesabı, şifreleme için müşteri tarafından yönetilen anahtarı kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Web uygulamasının bir parçası olarak kullanılıyorsa, ' Python sürümü ' öğesinin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: Işlev uygulamasının bir parçası olarak kullanılıyorsa ' Python sürümü ' öğesinin en son olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: WEB uygulamasının bir parçası olarak kullanılıyorsa, ' PHP Version ' öğesinin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: API uygulamasının bir parçası olarak kullanılıyorsa ' Python sürümü ' nin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Anahtarlar belirtilen şifreleme türü RSA veya EC olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Azure aboneliklerinin etkinlik günlüğü için bir günlük profili olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Azure Kubernetes hizmet kümelerindeki işletim sistemleri ve veri diskleri, müşteri tarafından yönetilen anahtarlarla şifrelenmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: Azure SQL veritabanı sunucuları için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: bekleyen Azure Veri Gezgini şifrelemesi, müşteri tarafından yönetilen bir anahtar kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: RSA şifrelemesi kullanan anahtarlar belirtilen minimum anahtar boyutuna sahip olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke etkisi: MySQL için Azure veritabanı 'nda coğrafi olarak yedekli yedekleme etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Kubernetes kümesi Pod yalnızca onaylanan konak ağını ve bağlantı noktası aralığını kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: sistem güncelleştirmelerinin makinelerinizde yüklü olması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Windows makinelerin ' sistem denetim Ilkeleri-ayrıcalık kullanımı ' gereksinimlerini karşılaması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: Azure Stream Analytics işler, verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: API uygulamasının bir parçası olarak kullanılıyorsa ' Java Version ' öğesinin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Web uygulamasını çalıştırmak için kullanılmışsa ' HTTP Version ' nin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: API uygulamanızda en son TLS sürümü kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: MFA 'nın aboneliğinizde yazma izinleri olan hesaplar etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: API uygulamasını çalıştırmak için kullanılmışsa ' HTTP Version ' nin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Microsoft ıaasantimalware uzantısı, Windows Server 'lar üzerinde dağıtılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: Işlev uygulamasının bir parçası olarak kullanılıyorsa ' Java Version ' öğesinin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: tüm ağ bağlantı noktaları, sanal makineniz ile ilişkili ağ güvenlik gruplarında sınırlandırılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Güvenlik Merkezi Standart fiyatlandırma katmanı seçilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: en az parola uzunluğunu 14 karakter olarak kısıtlayan Windows makinelerini denetleyin|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: özel RBAC kurallarının kullanımını denetleme|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: SQL Server 'da denetim etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Log Analytics Aracısı sanal makinelere yüklenmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: MFA 'nın aboneliğinizdeki sahip izinlerine sahip hesaplarda etkinleştirilmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Gelişmiş veri güvenliği SQL yönetilen örneği üzerinde etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: Role-Based Access Control (RBAC), Kubernetes hizmetlerinde kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: sanal makinelerin Konuk yapılandırma uzantısı olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Azure Güvenlik Merkezi 'nde eksik Endpoint Protection Izleyicisi|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: etkinlik günlüğü en az bir yıl bekletilecektir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: sanal makinelerin yönetim bağlantı noktaları, tam zamanında ağ erişim denetimiyle korunmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke etkisi: PostgreSQL sunucuları için genel ağ erişimi devre dışı bırakılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Cosmos DB hesapları için Gelişmiş tehdit koruması dağıtma|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: uygulama hizmetlerindeki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. ClassicNetwork/networkSecurityGroups/Delete)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Delete)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. Network/networkSecurityGroups/Delete)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. Network/networkSecurityGroups/securityRules/Delete)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. SQL/Servers/firewallRules/Delete)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: internet 'e yönelik olmayan sanal makineler ağ güvenlik gruplarıyla korunmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: parola karmaşıklığı ayarı etkin olmayan Windows makinelerini denetleyin|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: kapsayıcı kayıt defterleri için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: Azure Data Box işler cihazdaki bekleyen veriler için çift şifrelemeyi etkinleştirmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Azure için Microsoft kötü amaçlı yazılımdan koruma imzalarını otomatik olarak güncelleştirilecek şekilde yapılandırılması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: belirli Ilke işlemleri için bir etkinlik günlüğü uyarısı olmalıdır (Microsoft. Authorization/Policyaslar/Sil)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: MySQL esnek sunucular için genel ağ erişimi devre dışı bırakılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: depolama hesapları güvenilen Microsoft hizmetlerinden erişime izin verilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke etkisi: Web uygulamaları için uzaktan hata ayıklama kapatılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: RSA şifrelemesi kullanan sertifikalar belirtilen minimum anahtar boyutuna sahip olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: kapsayıcı kayıt defterleri Kısıtlanmamış ağ erişimine izin vermez|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke etkisi: PostgreSQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Konuk yapılandırma uzantısı, sistem tarafından atanmış yönetilen kimlik ile Azure sanal makinelerine dağıtılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: uzun vadeli coğrafi olarak yedekli yedekleme, Azure SQL veritabanları için etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: MySQL sunucuları için genel ağ erişimi devre dışı bırakılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: ters çevrilebilir şifreleme kullanarak parolaları depolamamayan Windows makinelerini denetleme|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Windows makinelerin ' Kullanıcı hakları ataması ' gereksinimlerini karşılaması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: Işlev uygulamasını çalıştırmak için kullanılmışsa ' HTTP Version ' nin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: MFA 'nın aboneliğinizdeki okuma izinlerine sahip hesaplarda etkinleştirilmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Internet 'ten RDP erişiminin engellenmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: passwd dosyası izinleri 0644 olarak ayarlanan Linux makinelerini denetleyin|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: alt ağların bir ağ güvenlik grubuyla ilişkilendirilmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: MySQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: uzaktan hata ayıklama API Apps için kapalı olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: parola olmadan hesaplardan uzak bağlantılara izin veren Linux makinelerini denetleme|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke etkisi: sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Azure Veri Gezgini Çift şifreleme etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Log Analytics Aracısı sanal makine ölçek kümelerine yüklenmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Web uygulamanızda en son TLS sürümü kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Azure Veri Gezgini disk şifrelemesi etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: parola olmayan hesaplara sahip Linux makinelerini denetleyin|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Azure SYNAPSE çalışma alanları, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: İşlev Uygulaması en son TLS sürümü kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: Kubernetes Hizmetleri, güvenlik açığı olmayan bir Kubernetes sürümüne yükseltilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: tüm Internet trafiği, dağıtılan Azure Güvenlik duvarınız aracılığıyla yönlendirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: Linux makinelerin Azure Güvenlik temeli için gereksinimleri karşılaması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke etkisi: MariaDB sunucuları için genel ağ erişimi devre dışı bırakılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için etki: SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke için efekt: eliptik eğri şifrelemesi kullanan anahtarlar belirtilen eğri adlarına sahip olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC düzey 3|İlke Ataması|İlke değerlendirmesinden dışlanan ad alanları: Kubernetes kümesi Pod yalnızca onaylanan konak ağını ve bağlantı noktası aralığını kullanmalıdır|İlke değerlendirmesinden dışlanacak Kubernetes ad alanlarının listesi.|
|CMMC düzey 3|İlke Ataması|Uygulama Hizmetleri için en son Java sürümü|Uygulama Hizmetleri için desteklenen en son Java sürümü|
|CMMC düzey 3|İlke Ataması|App Services için Linux için en son Python sürümü|Uygulama Hizmetleri için desteklenen en son Python sürümü|
|CMMC düzey 3|İlke Ataması|İsteğe bağlı: Log Analytics Aracısı dağıtımını denetlerken kapsama eklemek için desteklenen Linux işletim sistemini destekleyen VM görüntülerinin listesi|Örnek değer: '/Subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.COMPUTE/images/ContosoStdImage '|
|CMMC düzey 3|İlke Ataması|İsteğe bağlı: Log Analytics Agent dağıtımı denetlenirken kapsama eklemek üzere desteklenen Windows işletim sistemi olan VM görüntülerinin listesi|Örnek değer: '/Subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.COMPUTE/images/ContosoStdImage '|
|CMMC düzey 3|İlke Ataması|Ağ Izleyicisi 'nin etkinleştirilmesi gereken bölgelerin listesi|Bölge (ler) için ağ Izleyicisi 'nin etkin olup olmadığını denetleyin.|
|CMMC düzey 3|İlke Ataması|Tanılama günlükleri etkinleştirilmiş olması gereken kaynak türlerinin listesi||
|CMMC düzey 3|İlke Ataması|Konak ağ ad alanında yer alan, izin verilen ana bilgisayar bağlantı noktası aralığındaki en büyük değer|İzin verilen ana bilgisayar bağlantı noktası aralığındaki, ana bilgisayar ağ ad alanında kullanılabilecek maksimum değer.|
|CMMC düzey 3|İlke Ataması|Anahtarlar için minimum RSA anahtar boyutu|RSA anahtarları için en düşük anahtar boyutu.|
|CMMC düzey 3|İlke Ataması|Minimum RSA anahtar boyutu sertifikaları|RSA sertifikaları için en düşük anahtar boyutu.|
|CMMC düzey 3|İlke Ataması|Windows Web sunucuları için en düşük TLS sürümü|TLS sürümleri daha düşük olan Windows Web sunucuları uyumlu değil olarak değerlendirilir|
|CMMC düzey 3|İlke Ataması|Ana bilgisayar ağ ad alanında yer alan, izin verilen ana bilgisayar bağlantı noktası aralığındaki minimum değer|Ana bilgisayar ağ ad alanında kullanılabilen, izin verilen ana bilgisayar bağlantı noktası aralığındaki en küçük değer.|
|CMMC düzey 3|İlke Ataması|Mod gereksinimi|Tüm WAF ilkeleri için gerekli mod|
|CMMC düzey 3|İlke Ataması|Mod gereksinimi|Tüm WAF ilkeleri için gerekli mod|
|CMMC düzey 3|İlke Ataması|Pod hostPath birimlerinin kullanması için izin verilen ana bilgisayar yolları|Pod hostPath birimlerinin kullanmasına izin verilen ana bilgisayar yolları. Tüm konak yollarını engellemek için boş yollar listesi sağlayın.|
|CMMC düzey 3|İlke Ataması|Ağ erişimi: Uzaktan erişilebilir kayıt defteri yolları|Kayıt defteri anahtarının erişim denetim listesinde (ACL) listelenen kullanıcı veya gruplardan bağımsız olarak, ağ üzerinden hangi kayıt defteri yollarına erişilebileceğini belirtir `winreg` .|
|CMMC düzey 3|İlke Ataması|Ağ erişimi: Uzaktan erişilebilir kayıt defteri yolları ve alt yollar|Kayıt defteri anahtarının erişim denetim listesinde (ACL) listelenen kullanıcı veya gruplardan bağımsız olarak, ağ üzerinden hangi kayıt defteri yollarına ve alt yollara erişilebileceğini belirtir `winreg` .|
|CMMC düzey 3|İlke Ataması|Ağ erişimi: Anonim olarak erişilebilecek paylaşımlar|Anonim kullanıcılar tarafından hangi ağ paylaşımlarına erişildiğini belirtir. Bu ilke ayarının varsayılan yapılandırması, tüm kullanıcıların sunucuda paylaşılan kaynaklara erişebilmesi için önce kimlik doğrulaması gerektiğinden çok az etkiye sahiptir.|
|CMMC düzey 3|İlke Ataması|Ağ güvenliği: Kerberos için izin verilen şifreleme türlerini yapılandırma|Kerberos 'un kullanmasına izin verilen şifreleme türlerini belirtir.|
|CMMC düzey 3|İlke Ataması|Ağ güvenliği: LAN Manager kimlik doğrulama düzeyi|Ağ oturum açmaları için hangi sınama yanıt kimlik doğrulama protokolünün kullanıldığını belirtin. Bu seçim, istemciler tarafından kullanılan kimlik doğrulama protokolünün düzeyini, üzerinde anlaşılan oturum güvenliği düzeyini ve sunucular tarafından kabul edilen kimlik doğrulama düzeyini etkiler.|
|CMMC düzey 3|İlke Ataması|Ağ güvenliği: LDAP istemcisi imza gereksinimleri|LDAP bağlama isteklerini veren istemciler adına istenen veri imzalama düzeyini belirtin.|
|CMMC düzey 3|İlke Ataması|Ağ güvenliği: NTLM SSP tabanlı (güvenli RPC dahil) istemciler için en düşük oturum güvenliği|NTLM güvenlik desteği sağlayıcısı (SSP) kullanan uygulamalar için istemciler tarafından hangi davranışlara izin verileceğini belirtir. SSP Arabirimi (SSPI), kimlik doğrulama hizmetleri gerektiren uygulamalar tarafından kullanılır. [https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers)Daha fazla bilgi için bkz..|
|CMMC düzey 3|İlke Ataması|Ağ güvenliği: NTLM SSP tabanlı (güvenli RPC dahil) sunucular için en düşük oturum güvenliği|NTLM güvenlik desteği sağlayıcısı (SSP) kullanan uygulamalar için sunucular tarafından hangi davranışlara izin verileceğini belirtir. SSP Arabirimi (SSPI), kimlik doğrulama hizmetleri gerektiren uygulamalar tarafından kullanılır.|
|CMMC düzey 3|İlke Ataması|Uygulama Hizmetleri için en son PHP sürümü|Uygulama Hizmetleri için desteklenen en son PHP sürümü|
|CMMC düzey 3|İlke Ataması|IoT Hub tanılama günlükleri için gerekli saklama süresi (gün)||
|CMMC düzey 3|İlke Ataması|Ağ Izleyicisi için kaynak grubunun adı|Networkizleyici kaynak grubunun adı (örneğin, NetworkWatcherRG). Bu, ağ Izleyicileri bulunduğu kaynak grubudur.|
|CMMC düzey 3|İlke Ataması|SQL sunucuları için gerekli denetim ayarı||
|CMMC düzey 3|İlke Ataması|Yazılım tabanlı çift şifrelemeyi destekleyen Azure Data Box SKU 'Lar|Yazılım tabanlı çift şifrelemeyi destekleyen Azure Data Box SKU 'Larının listesi|
|CMMC düzey 3|İlke Ataması|UAC: yerleşik yönetici hesabı için yönetici onay modu|Yerleşik yönetici hesabı için yönetici onay modunun davranışını belirtir.|
|CMMC düzey 3|İlke Ataması|UAC: yönetici onay modundaki Yöneticiler için yükseltme isteminin davranışı|Yöneticiler için yükseltme isteminin davranışını belirtir.|
|CMMC düzey 3|İlke Ataması|UAC: uygulama yüklemelerini Algıla ve yükseltme istemi|Bilgisayar için uygulama yükleme algılaması davranışını belirtir.|
|CMMC düzey 3|İlke Ataması|UAC: yönetici onay modunda tüm yöneticileri Çalıştır|Bilgisayar için tüm Kullanıcı hesabı denetimi (UAC) ilkesi ayarlarının davranışını belirtir.|
|CMMC düzey 3|İlke Ataması|Uzak sistemden kapanmaya zorlayan Kullanıcı ve gruplar|Hangi kullanıcıların ve grupların bilgisayarı ağdaki uzak bir konumdan kapatmasına izin verileceğini belirtir.|
|CMMC düzey 3|İlke Ataması|Bu bilgisayara ağ üzerinden erişimi reddedilen kullanıcılar ve gruplar|Hangi kullanıcıların veya grupların ağ üzerinden bilgisayara bağlanmasına izin verilmeyen belirtir.|
|CMMC düzey 3|İlke Ataması|Yerel oturum açma izni verilmeyen kullanıcılar ve gruplar|Hangi kullanıcıların ve grupların bilgisayarda oturum açmasına izin verilmeyeceğini belirtir.|
|CMMC düzey 3|İlke Ataması|Toplu iş olarak oturum açma izni verilmeyen kullanıcılar ve gruplar|Hangi kullanıcıların ve grupların bilgisayarda bir toplu iş olarak oturum açmasına izin verilmeyeceğini belirtir (örneğin, zamanlanmış görev).|
|CMMC düzey 3|İlke Ataması|Hizmet olarak oturum açma izni verilmeyen kullanıcılar ve gruplar|Hangi hizmet hesaplarının bir işlemi hizmet olarak kaydedebileceği açıkça izin verilmeyeceğini belirtir.|
|CMMC düzey 3|İlke Ataması|Uzak Masaüstü Hizmetleri aracılığıyla oturum açma reddedilmiş kullanıcılar ve gruplar|Terminal Hizmetleri/Uzak Masaüstü Istemcisi aracılığıyla bilgisayarda oturum açmasına izin verilmeyen kullanıcıları ve grupları belirtir.|
|CMMC düzey 3|İlke Ataması|Dosya ve dizinleri geri yükleme olabilecek kullanıcılar ve gruplar|Yedeklenen dosya ve dizinleri geri yüklerken hangi kullanıcıların ve grupların dosya, dizin, kayıt defteri ve diğer kalıcı nesne izinlerini atlamasına izin verileceğini belirtir.|
|CMMC düzey 3|İlke Ataması|Sistemi kapatılabilen kullanıcılar ve gruplar|Ortamınızdaki bilgisayarlarda yerel olarak oturum açmış olan kullanıcıların ve grupların, işletim sistemini kapat komutuyla kapatmasına izin verildiğini belirtir.|
|CMMC düzey 3|İlke Ataması|Yerel olarak oturum açabilme Kullanıcı veya gruplar|Ağdaki uzak kullanıcıların bilgisayara bağlanmasına izin verileceğini belirtir. Bu, Uzak Masaüstü Bağlantısı içermez.|
|CMMC düzey 3|İlke Ataması|Dosya ve dizinleri yedekleyebilme kullanıcıları veya grupları|Sistemi yedeklemek için dosya ve dizin izinlerini aşmak için izin verilen kullanıcıları ve grupları belirtir.|
|CMMC düzey 3|İlke Ataması|Sistem saatini değiştiremeyebilirsiniz Kullanıcı veya gruplar|Bilgisayarın dahili saatindeki tarih ve saati değiştirme izni olan kullanıcıları ve grupları belirtir.|
|CMMC düzey 3|İlke Ataması|Saat dilimini değiştiremeyebilirsiniz Kullanıcı veya gruplar|Hangi kullanıcıların ve grupların bilgisayarın saat dilimini değiştirmesine izin verileceğini belirtir.|
|CMMC düzey 3|İlke Ataması|Belirteç nesnesi oluşturmayabilir Kullanıcı veya gruplar|Hangi kullanıcıların ve grupların erişim belirteci oluşturmasına izin verileceğini belirtir, bu durum hassas verilere erişmek için yükseltilmiş haklar sağlayabilir.|
|CMMC düzey 3|İlke Ataması|Yerel olarak oturum açabilme Kullanıcı veya gruplar|Hangi kullanıcıların veya grupların bilgisayarda etkileşimli olarak oturum açabilmesini belirtir. Uzak Masaüstü Bağlantısı veya IIS aracılığıyla oturum açmaya çalışan kullanıcılar da bu kullanıcı hakkını gerektirir.|
|CMMC düzey 3|İlke Ataması|Uzak Masaüstü Kullanıcıları|Uzak Masaüstü Hizmetleri üzerinden oturum açabilme Kullanıcı veya gruplar|
|CMMC düzey 3|İlke Ataması|Denetim ve güvenlik günlüğünü yönetebileceğini Kullanıcı veya gruplar|Dosya ve dizinlerin denetim seçeneklerini değiştirmesine izin verilen kullanıcıları ve grupları belirtir ve güvenlik günlüğünü temizler.|
|CMMC düzey 3|İlke Ataması|Dosyaların veya diğer nesnelerin sahipliğini alabileceği kullanıcılar veya gruplar|Dosyaların, klasörlerin, kayıt defteri anahtarlarının, işlemlerin veya iş parçacıklarının sahipliğini alma izni olan kullanıcıları ve grupları belirtir. Bu Kullanıcı, belirli bir kullanıcıya sahiplik vermek üzere nesneleri korumak için gereken tüm izinleri atlar.|

## <a name="next-steps"></a>Sonraki adımlar

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.