---
title: HIPAA HiTRUST 9,2 şema örneğine genel bakış
description: HIPAA HiTRUST 9,2 şema örneğine genel bakış. Bu şema örneği, müşterilerin belirli HIPAA HiTRUST 9,2 denetimlerini değerlendirmesine yardımcı olur.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 168946319c11f31ee41594d82d9ff186dea232cd
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386593"
---
# <a name="hipaa-hitrust-92-blueprint-sample"></a>HIPAA HiTRUST 9,2 şema örneği

HIPAA HiTRUST 9,2 şema örneği, belirli HIPAA HiTRUST 9,2 denetimlerini değerlendirmenize yardımcı olan [Azure ilkesini](../../policy/overview.md) kullanarak idare koruması-rayları sağlar. Bu şema, müşterilerin HIPAA zrust 9,2 denetimleri uygulaması gereken herhangi bir Azure dağıtılan mimari için çekirdek bir ilke kümesini dağıtmasının sağlanmasına yardımcı olur.

## <a name="control-mapping"></a>Denetim eşlemesi

[Azure ilke denetimi eşleme](../../policy/samples/hipaa-hitrust-9-2.md) , bu şema içinde yer alan ilke tanımlarına ilişkin ayrıntıları ve bu Ilke tanımlarının hıpaa, 9,2 HIPAA 'da **Uyumluluk etki alanları** ve **denetimleriyle** nasıl eşlendiğini sağlar. Bir mimariye atandığında kaynaklar, atanan ilke tanımlarına yönelik uyumsuzluk için Azure Ilkesi tarafından değerlendirilir. Daha fazla bilgi için bkz. [Azure İlkesi](../../policy/overview.md).

## <a name="deploy"></a>Dağıtma

Azure planları HIPAA HiTRUST 9,2 şema örneğini dağıtmak için aşağıdaki adımlar gerçekleştirilmelidir:

> [!div class="checklist"]
> - Örnekten yeni bir şema oluşturma
> - Örneğinizin kopyasını **Yayımlandı** olarak işaretleyin
> - Şemanızın kopyasını mevcut bir aboneliğe atayın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

### <a name="create-blueprint-from-sample"></a>Örnekten şema oluştur

İlk olarak, başlangıç noktası olarak örneği kullanarak ortamınızda yeni bir şema oluşturup şema örneğini uygulayın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Soldaki **Başlangıç** sayfasında, _Şema oluştur_ bölümündeki **Oluştur** düğmesini seçin.

1. _Diğer örnekler_ altında **HIPAA HiTRUST** şema örneğini bulun ve **Bu örneği kullan**' ı seçin.

1. Şema örneğinin _Temel bilgiler_ bölümüne girin:

   - **Şema adı**: HIPAA zrust 9,2 şema Sample kopyasına bir ad verin.
   - **Tanım konumu**: üç noktayı kullanın ve örneğin kopyasını kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _Yapıtlar_ sekmesini seçin veya sayfanın en altındaki **Sonraki: Yapıtlar** seçeneğini belirleyin.

1. Şema örneğini oluşturan yapıtların listesini gözden geçirin. Yapıtların çoğu parametreler içerir; bu parametreleri daha sonra tanımlayacağız. Şema örneğini gözden geçirmeyi tamamladığınızda **Taslağı Kaydet**’i seçin.

### <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımlama

Şimdi şema örneği kopyanız, ortamınızda oluşturulmuştur. Bu, **Taslak** modunda oluşturulur ve atanabilmesi ve dağıtılabilmesi için önce **Yayımlandı** durumunda olmalıdır. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir, ancak bu değişiklik, HIPAA 'nın HIPAA 'dan uzaklaşmaya 9,2 denetimleriyle hizalı olarak taşınmasını sağlayabilir.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Sayfanın üst kısmında **Şemayı yayımla**’yı seçin. Sağdaki yeni sayfada, şema örneği kopyanız için bir **Sürüm** sağlayın. Bu özellik daha sonra bir değişiklik yaptığınızda faydalı olacaktır. "HIPAA HiTRUST 9,2 şema örneğinden yayınlanan ilk sürüm" gibi **değişiklik notları** sağlayın. Sayfanın alt kısmında **Yayımla** düğmesini seçin.

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

|Yapıt adı |Parametre adı |Açıklama |
|---|---|---|
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Internet 'e yönelik uç nokta ile erişim kısıtlı olmalıdır |Aşırı izin veren gelen NSG kuralları izlemeyi etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Hesaplar: Yönetici hesabı durumu |Yerel Konuk hesabının devre dışı bırakılıp bırakılmadığını belirtir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Uyarlamalı uygulama denetimleri sanal makinelerde etkinleştirilmelidir |Azure Güvenlik Merkezi 'nde uygulama beyaz listesinin izlenmesini etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Internet veya Windows etki alanı için eş zamanlı bağlantılara izin ver |Bilgisayarların hem etki alanı tabanlı ağa hem de etki alanı tabanlı olmayan bir ağa aynı anda bağlanmasını önlemeyi belirtir. 0 değeri, eşzamanlı bağlantılara izin verir ve 1 değeri bunları engeller. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |API uygulaması yalnızca HTTPS v2 üzerinden erişilebilir olmalıdır |API App v2 'de HTTPS kullanımını izlemeyi etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Uygulama adları (joker karakterleri destekler) |Yüklenmesi gereken uygulamaların adlarının noktalı virgülle ayrılmış listesi. örn. ' Microsoft SQL Server 2014 (64 bit); Microsoft Visual Studio Code ' veya ' Microsoft SQL Server 2014 * ' (' Microsoft SQL Server 2014 ' ile başlayan herhangi bir uygulamayı eşleştirmek için) |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |İşlem Sonlandırmayı Denetleme |Bir işlemden çıkıldıktan sonra denetim olaylarının oluşturulup oluşturulmayacağını belirtir. Kritik işlemlerin sonlandırılmasını izlemek için önerilir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Depolama hesaplarına Kısıtlanmamış ağ erişimini denetleme |Depolama hesabına ağ erişiminin izlenmesini etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Denetim: Güvenlik denetimleri günlüğe alınamıyorsa sistemi hemen kapat |Güvenlik olaylarının günlüğü tutulamazsa sistemin kapatılıp kapatılmayacak olup olmadığını denetler. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Sertifika parmak izleri |Güvenilen kök sertifika deposu (CERT: \ localmachıneroot) altında bulunması gereken, noktalı virgülle ayrılmış bir sertifika parmak izi listesi. ör. THUMBPRINT1; THUMBPRINT2 THUMBPRINT3 |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Batch hesaplarındaki tanılama günlükleri etkinleştirilmelidir |Batch hesaplarında tanılama günlüklerinin izlenmesini etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Olay Hub 'ındaki tanılama günlükleri etkinleştirilmelidir |Olay Hub 'ı hesaplarında tanılama günlüklerinin izlenmesini etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Arama hizmetlerindeki tanılama günlükleri etkinleştirilmelidir |Azure Search hizmetindeki tanılama günlüklerinin izlenmesini etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Sanal makine ölçek kümelerindeki tanılama günlükleri etkinleştirilmelidir |Service Fabric tanılama günlüklerinin izlenmesini etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Disk şifrelemesi sanal makinelere uygulanmalıdır |VM disk şifrelemesi için izlemeyi etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Güvenli olmayan konuk oturum açmaları etkinleştir |SMB istemcisinin bir SMB sunucusuna güvenli olmayan konuk oturumlarına izin verip vermeyeceğini belirtir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Sanal makinelere anlık ağ erişim denetimi uygulanmalıdır |Ağın anında erişiminin izlenmesini etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Yönetim bağlantı noktaları sanal makinelerinizde kapatılmalıdır |Sanal makinelerde açık yönetim bağlantı noktalarının izlenmesini etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir |Abonelikte yazma izinleri olan hesaplar için MFA izlemesini etkinleştirin veya devre dışı bırakın |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir |Abonelikte sahip izinleri olan hesaplar için MFA izlemesini etkinleştirin veya devre dışı bırakın |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Ağ erişimi: Uzaktan erişilebilir kayıt defteri yolları |Kayıt defteri anahtarının erişim denetim listesinde (ACL) listelenen kullanıcı veya gruplardan bağımsız olarak, ağ üzerinden hangi kayıt defteri yollarına erişilebileceğini belirtir `winreg` . |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Ağ erişimi: Uzaktan erişilebilir kayıt defteri yolları ve alt yollar |Kayıt defteri anahtarının erişim denetim listesinde (ACL) listelenen kullanıcı veya gruplardan bağımsız olarak, ağ üzerinden hangi kayıt defteri yollarına ve alt yollara erişilebileceğini belirtir `winreg` . |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Ağ erişimi: Anonim olarak erişilebilecek paylaşımlar |Anonim kullanıcılar tarafından hangi ağ paylaşımlarına erişildiğini belirtir. Bu ilke ayarının varsayılan yapılandırması, tüm kullanıcıların sunucuda paylaşılan kaynaklara erişebilmesi için önce kimlik doğrulaması gerektiğinden çok az etkiye sahiptir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Kurtarma Konsolu: disket kopyalamaya ve tüm sürücülere ve klasörlere erişime Izin ver |Kurtarma Konsolu ortam değişkenlerinin ayarlanmasına izin veren Kurtarma Konsolu SET komutunun kullanılabilir yapılıp yapılmayacağını belirtir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |API uygulaması için uzaktan hata ayıklama kapatılmalıdır |API uygulaması için uzaktan hata ayıklamayı izlemeyi etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Web uygulaması için uzaktan hata ayıklama kapatılmalıdır |Web uygulaması için uzaktan hata ayıklamayı izlemeyi etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Batch hesaplarındaki Günlükler için gereken bekletme (gün cinsinden) |Gerekli tanılama günlükleri bekletme süresi (gün) |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Azure Search hizmetindeki günlüklerin gerekli saklama (gün cinsinden) |Gerekli tanılama günlükleri bekletme süresi (gün) |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Olay Hub 'ı hesaplarında gereken bekletme (gün cinsinden) |Gerekli tanılama günlükleri bekletme süresi (gün) |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Ağ güvenlik grupları için tanılama ayarlarını dağıtmak üzere depolama hesabının kaynak grubu adı (var olmalıdır) |Depolama hesabının oluşturulacağı kaynak grubu. Bu kaynak grubu zaten var olmalıdır. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Kubernetes hizmetlerinde Role-Based Access Control (RBAC) kullanılmalıdır |RBAC etkin olmadan Kubernetes hizmetlerinin izlenmesini etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |SQL yönetilen örnek TDE koruyucusu kendi anahtarınızla şifrelenmelidir |Saydam Veri Şifrelemesi (TDE), kendi anahtar desteklerinizi kullanarak izlemeyi etkinleştirin veya devre dışı bırakın. Kendi anahtar desteğiniz ile TDE, TDE koruyucu üzerinde daha fazla saydamlık ve denetim, HSM destekli bir dış hizmetle artırılmış güvenlik ve görev ayrımı ile ilgili yükseltme sağlar. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |SQL Server TDE koruyucusu kendi anahtarınızla şifrelenmelidir |Saydam Veri Şifrelemesi (TDE), kendi anahtar desteklerinizi kullanarak izlemeyi etkinleştirin veya devre dışı bırakın. Kendi anahtar desteğiniz ile TDE, TDE koruyucu üzerinde daha fazla saydamlık ve denetim, HSM destekli bir dış hizmetle artırılmış güvenlik ve görev ayrımı ile ilgili yükseltme sağlar. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Ağ güvenlik grupları için tanılama ayarlarını dağıtmak üzere bölgesel depolama hesabı için depolama hesabı ön eki |Bu ön ek, oluşturulan depolama hesabı adını biçimlendirmek için ağ güvenlik grubu konumuyla birleştirilir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir |Sanal makine ölçek kümelerini etkinleştirme veya devre dışı bırakma sistem güncelleştirmeleri raporlaması |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir |Sanal makine ölçek kümelerini etkinleştirme veya devre dışı bırakma sistem güncelleştirmeleri raporlaması |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Çok noktaya yayın ad çözümlemesini kapat |Tek bir alt ağ üzerindeki yerel alt ağ bağlantısı üzerinden çok noktaya yayın kullanarak ileten bir ikincil ad çözümleme protokolü olan LLMNR 'ın etkinleştirilip etkinleştirilmeyeceğini belirtir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Sanal makinelerin yeni Azure Resource Manager kaynaklara geçirilmesi gerekir |Klasik işlem VM 'lerinin izlenmesini etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir |Sanal makine ölçek kümelerini etkinleştirme veya devre dışı bırakma işletim sistemi güvenlik açıkları izleme |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir |Bir güvenlik açığı değerlendirme çözümü tarafından sanal makine güvenlik açıklarını algılamayı etkinleştirme veya devre dışı bırakma |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |SQL yönetilen örneklerinizin üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir |Yinelenen güvenlik açığı değerlendirmesi taramaları etkin olmayan SQL yönetilen örneklerini denetleyin. Güvenlik açığı değerlendirmesi, olası veritabanı güvenlik açıklarını düzeltmenizi, izleyebilir ve yardımcı olabilir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (etki alanı): yerel güvenlik duvarı kuralları uygulama |Yerel yöneticilerin, etki alanı profili için grup ilkesi tarafından yapılandırılan güvenlik duvarı kurallarıyla birlikte uygulanan yerel güvenlik duvarı kuralları oluşturmalarına izin verilip verilmeyeceğini belirtir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (etki alanı): giden bağlantılar için davranış |Giden bir güvenlik duvarı kuralıyla eşleşmeyen etki alanı profili için giden bağlantıların davranışını belirtir. Varsayılan 0 değeri bağlantılara izin verilecek anlamına gelir ve 1 değeri bağlantı engellenecek anlamına gelir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (etki alanı): giden bağlantılar için davranış |Giden bir güvenlik duvarı kuralıyla eşleşmeyen etki alanı profili için giden bağlantıların davranışını belirtir. Varsayılan 0 değeri bağlantılara izin verilecek anlamına gelir ve 1 değeri bağlantı engellenecek anlamına gelir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (etki alanı): bildirimleri görüntüle |Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı 'Nın, bir programın, etki alanı profili için gelen bağlantıları alması engellendiğinde kullanıcıya bildirimleri görüntüleyip görüntülemediğini belirtir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (etki alanı): profil ayarlarını kullan |Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı 'Nın ağ trafiğini filtrelemek için etki alanı profili ayarlarını kullanıp kullanmadığını belirtir. Kapalı seçeneğini belirlerseniz, Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı bu profil için güvenlik duvarı kurallarından veya bağlantı güvenliği kurallarından hiçbirini kullanmaz. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (özel): yerel bağlantı güvenlik kurallarını Uygula |Yerel yöneticilerin, özel profil için grup ilkesi tarafından yapılandırılan bağlantı güvenliği kurallarıyla birlikte uygulanan bağlantı güvenliği kuralları oluşturmalarına izin verilip verilmeyeceğini belirtir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (özel): yerel güvenlik duvarı kuralları uygulama |Yerel yöneticilerin, özel profil için grup ilkesi tarafından yapılandırılan güvenlik duvarı kurallarıyla birlikte uygulanan yerel güvenlik duvarı kuralları oluşturmalarına izin verilip verilmeyeceğini belirtir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (özel): giden bağlantılar için davranış |Giden bir güvenlik duvarı kuralıyla eşleşmeyen özel profil için giden bağlantıların davranışını belirtir. Varsayılan 0 değeri bağlantılara izin verilecek anlamına gelir ve 1 değeri bağlantı engellenecek anlamına gelir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (özel): bildirimleri görüntüle |Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı 'Nın, özel profil için bir programın gelen bağlantıları alması engellendiğinde kullanıcıya bildirimleri görüntüleyip görüntülemediğini belirtir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (özel): profil ayarlarını kullanın |Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı 'Nın ağ trafiğini filtrelemek için özel profilin ayarlarını kullanıp kullanmadığını belirtir. Kapalı seçeneğini belirlerseniz, Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı bu profil için güvenlik duvarı kurallarından veya bağlantı güvenliği kurallarından hiçbirini kullanmaz. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (genel): yerel bağlantı güvenlik kurallarını Uygula |Yerel yöneticilerin, genel profil için grup ilkesi tarafından yapılandırılan bağlantı güvenliği kurallarıyla birlikte uygulanan bağlantı güvenliği kuralları oluşturmalarına izin verilip verilmeyeceğini belirtir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (genel): yerel güvenlik duvarı kuralları uygulama |Yerel yöneticilerin, genel profil için grup ilkesi tarafından yapılandırılan güvenlik duvarı kurallarıyla birlikte uygulanan yerel güvenlik duvarı kuralları oluşturmalarına izin verilip verilmeyeceğini belirtir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (genel): giden bağlantılar için davranış |Giden bir güvenlik duvarı kuralıyla eşleşmeyen ortak profil için giden bağlantıların davranışını belirtir. Varsayılan 0 değeri bağlantılara izin verilecek anlamına gelir ve 1 değeri bağlantı engellenecek anlamına gelir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (genel): bildirimleri görüntüle |Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı 'nın, genel profil için bir programın gelen bağlantıları alması engellendiğinde kullanıcıya bildirimleri görüntüleyip görüntülemediğini belirtir. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı (genel): profil ayarlarını kullanın |Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı 'Nın ağ trafiğini filtrelemek için ortak profilin ayarlarını kullanıp kullanmadığını belirtir. Kapalı seçeneğini belirlerseniz, Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı bu profil için güvenlik duvarı kurallarından veya bağlantı güvenliği kurallarından hiçbirini kullanmaz. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı: Domain: tek noktaya yayın yanıtına Izin ver |Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı 'Nın, yerel bilgisayarın giden çok noktaya yayın veya yayın iletilerine tek noktaya yayın yanıtları almasına izin verip vermeyeceğini belirtir; Etki alanı profili için. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı: özel: tek noktaya yayın yanıtına Izin ver |Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı 'Nın, yerel bilgisayarın giden çok noktaya yayın veya yayın iletilerine tek noktaya yayın yanıtları almasına izin verip vermeyeceğini belirtir; Özel profil için. |
|Denetim gereksinimlerini desteklemek için, HITRUST/HIPAA denetimlerini denetleme ve belirli VM uzantılarını dağıtma |Windows Güvenlik Duvarı: ortak: tek noktaya yayın yanıtına Izin ver |Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı 'Nın, yerel bilgisayarın giden çok noktaya yayın veya yayın iletilerine tek noktaya yayın yanıtları almasına izin verip vermeyeceğini belirtir; Genel profil için. |

## <a name="next-steps"></a>Sonraki adımlar

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.
