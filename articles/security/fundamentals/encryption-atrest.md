---
title: Microsoft Azure veri şifrelemesi-Rest | Microsoft Docs
description: Bu makalede, bekleyen veri şifreleme, genel yetenekler ve genel konular hakkında Microsoft Azure genel bir bakış sunulmaktadır.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2019
ms.author: barclayn
ms.openlocfilehash: fc3c7ea2f0060fc6ab20fa821c9e45d6e5173d4f
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795792"
---
# <a name="azure-data-encryption-at-rest"></a>Azure veri şifreleme-Rest

Microsoft Azure, şirketinizin güvenlik ve uyumluluk ihtiyaçlarına göre verileri korumaya yönelik araçlar içerir. Bu kağıdın odakları:

- Veriler Microsoft Azure arasında Rest 'ten nasıl korunur
- Veri koruma uygulamasında bir bölümü alan çeşitli bileşenleri açıklar,
- Farklı temel yönetim koruması yaklaşımlarının avantajlarını ve dezavantajlarını inceler.

Bekleyen şifreleme ortak bir güvenlik gereksinimidir. Azure 'da kuruluşlar, bekleyen verileri bir özel anahtar yönetimi çözümünün riski veya maliyeti olmadan şifreleyebilir. Kuruluşların, bekleyen bir şekilde Azure 'un şifrelemeyi tamamen yönetmesine izin verme seçeneği vardır. Ayrıca kuruluşlar, şifrelemeyi veya şifreleme anahtarlarını yakından yönetmek için çeşitli seçeneklere sahiptir.

## <a name="what-is-encryption-at-rest"></a>Bekleyen şifreleme nedir?

Bekleyen şifreleme, kalıcı olduğunda verilerin kodlanması (şifreleme). Azure 'daki Rest tasarımlarındaki şifreleme, basit bir kavramsal modele göre büyük miktarlarda verileri şifrelemek ve şifrelerini çözmek için simetrik şifrelemeyi kullanır:

- Verileri depolamaya yazıldığı şekilde şifrelemek için bir simetrik şifreleme anahtarı kullanılır.
- Aynı şifreleme anahtarı, bellekte kullanım için yeniden kullanıma hazır olduğundan bu verilerin şifresini çözmek için kullanılır.
- Veriler bölümlenebilir ve her bölüm için farklı anahtarlar kullanılabilir.
- Anahtarlar kimlik tabanlı erişim denetimi ve denetim ilkeleriyle güvenli bir yerde depolanmalıdır. Veri şifreleme anahtarları genellikle, erişimi daha fazla sınırlandırmak için Azure Key Vault bir anahtar şifreleme anahtarıyla şifrelenir.

Uygulamada, önemli yönetim ve denetim senaryolarında, ölçek ve kullanılabilirlik açısından ek yapılar gerekir. Rest kavramlarını ve bileşenlerini Microsoft Azure şifreleme aşağıda açıklanmıştır.

## <a name="the-purpose-of-encryption-at-rest"></a>Bekleyen şifreleme amacı

Rest 'de şifreleme, depolanan veriler (bekleyen) için veri koruması sağlar. Rest verilerine yönelik saldırılar, verilerin depolandığı donanıma fiziksel erişimi alma girişimlerini ve ardından içerilen verilerin güvenliğini sağlar. Böyle bir saldırıya karşı, bir saldırganın sabit sürücüyü kaldırmasına izin veren bakım sırasında bir sunucunun sabit sürücüsü hatalı işlenmiş olabilir. Daha sonra saldırgan, verilere erişmeyi denemek için sabit sürücüyü kendi denetimindeki bir bilgisayara yerleştirir.

Bekleyen şifreleme, verilerin disk üzerinde şifrelendiğinden emin olmak için, saldırganın şifrelenmemiş verilere erişmesini engellemek üzere tasarlanmıştır. Bir saldırgan şifrelenmiş verileri olan ancak şifreleme anahtarları olmayan bir sabit sürücü alırsa, verileri okumak için saldırganın şifrelemeyi ertelemeniz gerekir. Bu saldırı, sabit bir sürücüdeki şifrelenmemiş verilere erişenden çok daha karmaşık ve kaynak kullanıyor. Bu nedenle, bekleyen şifreleme son derece önerilir ve birçok kuruluş için yüksek öncelikli bir gereksinimdir.

Bekleyen şifreleme, bir kuruluşun veri idare ve uyumluluk çabalarına ihtiyacı olarak da gerekli olabilir. HIPAA, PCI ve Fedratin gibi sektör ve kamu düzenlemeleri, veri koruma ve şifreleme gereksinimleriyle ilgili belirli korumaları düzenleyin. Bekleyen şifreleme, bu düzenlemelerle uyumluluk için gerekli olan zorunlu bir ölçüdür.

Uyumluluk ve mevzuat gereksinimlerinin karşılamalarının yanı sıra, bekleyen şifreleme da derinlemesine savunma koruması sağlar. Microsoft Azure, hizmetler, uygulamalar ve veriler için uyumlu bir platform sağlar. Ayrıca kapsamlı tesis ve fiziksel güvenlik, veri erişim denetimi ve denetim sağlar. Ancak, diğer güvenlik ölçülerinden biri başarısız olduğunda ve bekleyen şifreleme bu tür bir güvenlik ölçüsü sağladığından, ek "çakışan" güvenlik önlemleri sağlanması önemlidir.

Microsoft, bulut hizmetlerinde Rest seçeneklerinde şifrelemeye ve müşterilerin şifreleme anahtarları ve anahtar kullanımı günlüklerinin denetimini yapmasına kararlıdır. Ayrıca, Microsoft, bekleyen tüm müşteri verilerini varsayılan olarak şifrelemek için de çalışmaktadır.

## <a name="azure-encryption-at-rest-components"></a>Rest bileşenlerinde Azure şifrelemesi

Daha önce açıklandığı gibi, şifreleme hedefi, diskte kalıcı olan verilerin gizli bir şifreleme anahtarıyla şifrelenmesini sağlamaktır. Bu hedefin güvenli anahtar oluşturma, depolama, erişim denetimi ve şifreleme anahtarlarının yönetimine ulaşmak için sağlanmalıdır. Ayrıntılar farklılık gösterebilir, ancak Rest uygulamalarındaki Azure hizmetleri şifrelemesi, aşağıdaki diyagramda gösterilen koşullarda açıklanabilir.

![Bileşenler](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

Şifreleme anahtarlarının depolama konumu ve bu anahtarlara erişim denetimi, bekleyen modeldeki bir şifrelemeye göre yapılır. Anahtarların yüksek oranda güvenli olması gerekir, ancak belirtilen kullanıcılar tarafından yönetilebilir ve belirli hizmetler için kullanılabilir. Azure hizmetleri için Azure Key Vault önerilen anahtar depolama çözümüdür ve hizmetler arasında ortak bir yönetim deneyimi sağlar. Anahtarlar, anahtar kasalarında depolanır ve yönetilir ve bir anahtar kasasına erişim, kullanıcılara veya hizmetlere verilebilir. Azure Key Vault, müşteri tarafından yönetilen şifreleme anahtarı senaryolarında kullanılmak üzere anahtar oluşturulmasını veya müşteri anahtarlarının içeri aktarılacağını destekler.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Key Vault ' de depolanan anahtarları kullanarak, bekleyen şifreleme ve şifre çözme sırasında şifrelemeyi yönetmek ya da bunlara erişmek için, Azure Active Directory hesaplara verilebilir.

### <a name="key-hierarchy"></a>Anahtar hiyerarşisi

Rest uygulamasında bir şifrelemede birden fazla şifreleme anahtarı kullanılıyor. Bir şifreleme anahtarının Azure Key Vault, anahtarların güvenli anahtar erişimini ve merkezi yönetimini sağlar. Ancak, şifreleme anahtarlarına hizmet yerel erişimi, her veri işlemi için Key Vault etkileşimde bulunarak, daha güçlü şifreleme ve daha iyi performans sağlayan toplu şifreleme ve şifre çözme işlemleri için daha verimlidir. Tek bir şifreleme anahtarının kullanımını kısıtlamak, anahtarın tehlikeye girdiği riski azaltır ve bir anahtarın değiştirilebilmesi için yeniden şifrelemenin maliyeti azalır. Rest modellerdeki Azure şifrelemeleri, tüm bu ihtiyaçları karşılamak için aşağıdaki anahtar türlerinden oluşan bir anahtar hiyerarşisi kullanır:

- **Veri şifreleme anahtarı (dek)** – bir bölümü veya veri bloğunu şifrelemek için kullanılan BIR simetrik AES256 anahtarı.  Tek bir kaynakta birçok bölüm ve birçok veri şifreleme anahtarı olabilir. Farklı bir anahtarla her bir veri bloğunu şifrelemek, şifre çözümleme saldırılarını daha zor hale getirir. Belirli bir bloğu şifreleyen ve şifresini çözen kaynak sağlayıcı veya uygulama örneği, DEKs 'e erişim için gereklidir. Bir DEK yeni bir anahtarla değiştirildiğinde, yalnızca ilişkili bloğundaki veriler yeni anahtarla yeniden şifrelenmelidir.
- **Anahtar şifreleme anahtarı (kek)** : veri şifreleme anahtarlarını şifrelemek için kullanılan bir şifreleme anahtarı. Key Vault hiçbir şekilde ayrılmayacak anahtar şifreleme anahtarının kullanımı, veri şifreleme anahtarlarının kendilerine şifreli ve denetimli olmasını sağlar. KEK 'e erişimi olan varlık, DEK gerektiren varlıktan farklı olabilir. Bir varlık her bir DEK ' ın erişimini belirli bir bölüme sınırlamak için DEK aracı erişimi sağlayabilir. KEK 'in şifresini çözmek için gerekli olduğundan, KEK, KEK silinerek etkin bir şekilde silinebilen tek bir noktasıdır.

Anahtar şifreleme anahtarlarıyla şifrelenen veri şifreleme anahtarları ayrı olarak saklanır ve yalnızca anahtar şifreleme anahtarına erişimi olan bir varlık bu veri şifreleme anahtarlarının şifresini çözebilir. Farklı anahtar depolama modelleri desteklenir. Sonraki bölümde daha sonra her bir modeli daha ayrıntılı tartışacağız.

## <a name="data-encryption-models"></a>Veri şifreleme modelleri

Çeşitli şifreleme modellerinin ve bunların uzmanlarının ve dezavantajlarını anlamak, Azure 'daki çeşitli kaynak sağlayıcılarının bekleyen şifreleme uygulamasını anlamak için gereklidir. Bu tanımlar, ortak dil ve Taksonomi sağlamak için Azure 'daki tüm kaynak sağlayıcıları genelinde paylaşılır.

Sunucu tarafı şifreleme için üç senaryo vardır:

- Hizmet tarafından yönetilen anahtarları kullanarak sunucu tarafı şifreleme
  - Azure kaynak sağlayıcıları şifreleme ve şifre çözme işlemlerini gerçekleştirir
  - Microsoft, anahtarları yönetir
  - Tam bulut işlevselliği

- Azure Key Vault 'de müşteri tarafından yönetilen anahtarları kullanarak sunucu tarafı şifreleme
  - Azure kaynak sağlayıcıları şifreleme ve şifre çözme işlemlerini gerçekleştirir
  - Müşteri denetimleri Azure Key Vault aracılığıyla anahtarları
  - Tam bulut işlevselliği

- Müşteri tarafından denetlenen donanımda müşteri tarafından yönetilen anahtarları kullanarak sunucu tarafı şifreleme
  - Azure kaynak sağlayıcıları şifreleme ve şifre çözme işlemlerini gerçekleştirir
  - Müşteri tarafından denetlenen donanımda müşteriler denetim anahtarları
  - Tam bulut işlevselliği

İstemci tarafı şifreleme için aşağıdakileri göz önünde bulundurun:

- Azure hizmetleri şifresi çözülen verileri göremez
- Müşteriler, anahtarları şirket içinde (veya diğer güvenli depolarda) yönetebilir ve depolar. Anahtarlar Azure hizmetleri için kullanılamaz
- Azaltılmış bulut işlevselliği

Azure 'daki desteklenen şifreleme modelleri, daha önce bahsedilen "Istemci şifrelemesi" ve "sunucu tarafı şifreleme" olarak iki ana gruba ayrılır. Kullanılan Rest modelindeki şifrelemeden bağımsız olarak Azure Hizmetleri, TLS veya HTTPS gibi güvenli bir taşımanın kullanımını her zaman önerir. Bu nedenle, Aktarımdaki şifrelemeye, Aktarım Protokolü tarafından değinilmesi gerekir ve bekleyen modeldeki şifrelemeyi belirlemede büyük bir faktör olmamalıdır.

### <a name="client-encryption-model"></a>İstemci şifreleme modeli

İstemci şifreleme modeli, kaynak sağlayıcısı veya hizmet tarafından Azure veya çağıran uygulama dışında gerçekleştirilen şifrelemeyi ifade eder. Şifreleme, Azure 'daki hizmet uygulaması veya müşteri veri merkezinde çalışan bir uygulama tarafından gerçekleştirilebilir. Her iki durumda da, bu şifreleme modeli kullanılırken Azure Kaynak sağlayıcısı, verilerin şifresini herhangi bir şekilde çözebilme veya şifreleme anahtarlarına erişim olmadan şifrelenmiş bir veri blobu alır. Bu modelde, anahtar yönetimi çağıran hizmet/uygulama tarafından yapılır ve Azure hizmetine opaktır.

![İstemci](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Sunucu tarafı şifreleme modeli

Sunucu tarafı şifreleme modelleri, Azure hizmeti tarafından gerçekleştirilen şifrelemeye başvurur. Bu modelde, kaynak sağlayıcısı şifreleme ve şifre çözme işlemlerini gerçekleştirir. Örneğin, Azure Storage verileri düz metin işlemlerinde alabilir ve şifreleme ve şifre çözme işlemlerini dahili olarak gerçekleştirir. Kaynak sağlayıcı, belirtilen yapılandırmaya bağlı olarak Microsoft tarafından veya müşteri tarafından yönetilen şifreleme anahtarlarını kullanabilir.

![Sunucu](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Sunucu tarafı şifreleme anahtarı yönetim modelleri

Rest modellerindeki sunucu tarafı şifrelemenin her biri, anahtar yönetiminin farklı özelliklerini gösterir. Bu, şifreleme anahtarlarının nerede ve nasıl oluşturulduğunu, ayrıca erişim modellerini ve anahtar döndürme yordamlarını da içerir.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Hizmet tarafından yönetilen anahtarları kullanarak sunucu tarafı şifreleme

Birçok müşteri için, en önemli gereksinim, verilerin bekleyen her durumda şifrelendiğinden emin olunması sağlamaktır. Hizmet tarafından yönetilen anahtarları kullanan sunucu tarafı şifreleme, müşterilerin belirli bir kaynağı (depolama hesabı, SQL DB vb.) şifreleme için işaretlemesini ve anahtar verme, döndürme ve yedekleme gibi tüm anahtar yönetim yönlerini Microsoft 'a bırakarak bu modeli sağlar . Rest 'de şifrelemeyi destekleyen çoğu Azure hizmeti, şifreleme anahtarlarının yönetimini Azure 'a boşaltma için genellikle bu modeli destekler. Azure Kaynak sağlayıcısı anahtarları oluşturur, güvenli depolamaya koyar ve gerektiğinde bunları alır. Bu, hizmetin anahtarlara tam erişimi olduğu ve hizmetin kimlik bilgisi yaşam döngüsü yönetimi üzerinde tam denetime sahip olduğu anlamına gelir.

![lebilmesi](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

Hizmet tarafından yönetilen anahtarlar kullanılarak yapılan sunucu tarafı şifreleme, bu nedenle, müşteri için düşük ek yük ile bekleyen şifreleme gereksinimini hızla ortadan kaldırır. Bir müşteri kullanılabilir olduğunda, genellikle hedef abonelik ve kaynak sağlayıcısı için Azure portal açar ve bunu gösteren bir kutuyu kontrol eder ve verilerin şifrelenmesini ister. Bazı kaynak yöneticileri, hizmet tarafından yönetilen anahtarlarla sunucu tarafında şifreleme varsayılan olarak açık olur.

Microsoft tarafından yönetilen anahtarlarla sunucu tarafı şifreleme, hizmetin anahtarları depolamak ve yönetmek için tam erişimi olduğunu göstermez. Bazı müşteriler daha fazla güvenlik elde ettikleri için anahtarları yönetmek isteyebilir, bu modelin değerlendirmesi sırasında özel bir anahtar depolama çözümüyle ilişkili maliyet ve risk göz önünde bulundurulmalıdır. Çoğu durumda, bir kuruluş, şirket içi bir çözümün kaynak kısıtlamalarının veya risklerin, Rest anahtarlarındaki şifrelemenin bulut yönetimi riskinden daha büyük olabileceğini tespit edebilir.  Ancak, bu model, şifreleme anahtarlarının oluşturulmasını veya yaşam döngüsünü denetlemek için gerekli olan veya farklı personele hizmetin (yani, ayrılmış) bir hizmetin şifreleme anahtarlarını yönetmesine izin veren kuruluşlar için yeterli olmayabilir. hizmet için genel yönetim modelinden önemli yönetim.

##### <a name="key-access"></a>Anahtar erişimi

Hizmet tarafından yönetilen anahtarlarla sunucu tarafı şifreleme kullanıldığında, anahtar oluşturma, depolama ve hizmet erişiminin hepsi hizmet tarafından yönetilir. Genellikle, temel Azure kaynak sağlayıcıları veri şifreleme anahtarlarını verilere yakın bir depoda depolar ve anahtar şifreleme anahtarları güvenli bir dahili depoda depolanırken hızlı bir şekilde kullanılabilir ve erişilebilir olur.

**Üstünlü**

- Basit kurulum
- Microsoft, anahtar döndürme, yedekleme ve artıklığı yönetir
- Müşterinin, uygulamayla ilişkili maliyeti veya özel bir anahtar yönetim şeması riski yoktur.

**Olumsuz**

- Şifreleme anahtarları üzerinde müşteri denetimi yok (anahtar belirtimi, yaşam döngüsü, iptal, vb.)
- Hizmet için genel yönetim modelinden anahtar yönetimini ayırma yeteneği yoktur

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault 'de müşteri tarafından yönetilen anahtarları kullanarak sunucu tarafı şifreleme

Gereksinimin, bekleyen verileri şifrelemek ve şifreleme anahtarlarını denetlemek için gereken senaryolarda, müşteriler Key Vault içindeki müşteri tarafından yönetilen anahtarları kullanarak sunucu tarafı şifrelemeyi kullanabilir. Bazı hizmetler Azure Key Vault yalnızca kök anahtar şifreleme anahtarını saklayabilir ve şifreli veri şifreleme anahtarını verilere daha yakın bir iç konuma saklayabilir. Bu senaryoda müşteriler, kendi anahtarlarını Key Vault (BYOK – Kendi Anahtarını Getir) öğesine getirebilir ya da yenilerini oluşturabilir ve bunları istediğiniz kaynakları şifrelemek için kullanabilir. Kaynak sağlayıcısı şifreleme ve şifre çözme işlemlerini gerçekleştirdiğinde, yapılandırılan anahtar şifreleme anahtarını tüm şifreleme işlemleri için kök anahtar olarak kullanır.

Anahtar şifreleme anahtarlarının kaybolması, veri kaybı anlamına gelir. Bu nedenle anahtarlar silinmemelidir. Anahtarlar her oluşturulduğunda veya döndürüldüğünde yedeklenmelidir. Anahtar şifreleme anahtarlarını depolayan kasalardaki [geçici silme](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) etkinleştirilmelidir. Anahtar silmek yerine, etkin ayarını false olarak ayarlayın veya bitiş tarihini ayarlayın.

##### <a name="key-access"></a>Anahtar erişimi

Azure Key Vault içindeki müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifreleme modeli, gerektiğinde şifrelemek ve şifresini çözmek için anahtarlara erişen hizmeti içerir. Rest anahtarlarındaki şifrelemeye, bir erişim denetim ilkesi aracılığıyla bir hizmet tarafından erişilebilir hale getirilir. Bu ilke, anahtarı almak için hizmet kimliği erişimine izin verir. İlişkili bir abonelik adına çalışan bir Azure hizmeti, Bu abonelikteki bir kimlikle yapılandırılabilir. Hizmet Azure Active Directory kimlik doğrulaması gerçekleştirebilir ve bu hizmet, abonelik adına işlem gören bir kimlik doğrulama belirteci alabilir. Bu belirteç daha sonra, erişimi verilen bir anahtarı elde etmek için Key Vault sunulabilir.

Şifreleme anahtarlarını kullanan işlemler için, bir hizmet kimliğine şu işlemlerden herhangi birine erişim verilebilir: şifre çözme, şifreleme, unwrapKey, wrapKey, doğrulama, imzalama, alma, listeleme, güncelleştirme, oluşturma, içeri aktarma, silme, yedekleme ve geri yükleme.

Bekleyen verileri şifrelemek veya şifrelerini çözmek için bir anahtar almak üzere Kaynak Yöneticisi hizmet örneğinin çalışacağı hizmet kimliğinin UnwrapKey olması gerekir (şifre çözme anahtarını almak için) ve WrapKey (yeni anahtar oluştururken anahtar kasasına bir anahtar eklemek için).

>[!NOTE]
>Key Vault yetkilendirmesi hakkında daha fazla ayrıntı için, [Azure Key Vault belgelerindeki](../../key-vault/key-vault-secure-your-key-vault.md)anahtar kasasının güvenliğini sağlama sayfasına bakın.

**Üstünlü**

- Kullanılan anahtarlar üzerinde tam denetim – şifreleme anahtarları, müşterinin denetiminde müşterinin Key Vault yönetilir.
- Birden çok hizmeti tek bir ana öğe ile şifreleme yeteneği
- Hizmet için genel yönetim modelinden anahtar yönetimini ayırt edebilir
- , Bölgeler arasında hizmet ve anahtar konumunu tanımlayabilir

**Olumsuz**

- Müşteri, anahtar erişim yönetimi için tam sorumluluğa sahiptir
- Müşteri, anahtar yaşam döngüsü yönetimi için tam sorumluluğa sahiptir
- Ek kurulum & yapılandırma ek yükü

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Müşteri tarafından denetlenen donanımda müşteri tarafından yönetilen anahtarları kullanarak sunucu tarafı şifreleme

Bazı Azure Hizmetleri, ana bilgisayarı kendi anahtarınızı (HYOK) anahtar yönetim modelinize etkinleştirir. Bu yönetim modu, bekleyen verileri şifrelemek ve Microsoft 'un denetimi dışında özel bir depodaki anahtarları yönetmek için gereken senaryolarda faydalıdır. Bu modelde, hizmetin anahtarı bir dış siteden alması gerekir. Performans ve kullanılabilirlik garantisi etkilendi ve yapılandırma daha karmaşıktır. Ayrıca, şifreleme ve şifre çözme işlemleri sırasında hizmetin DEK erişimi olduğundan, bu modelin genel güvenlik garantisi, anahtarların Azure Key Vault ' de müşteri tarafından yönetilme biçimine benzer.  Sonuç olarak, belirli anahtar yönetim gereksinimlerine sahip olmadıkları sürece bu model çoğu kuruluşa uygun değildir. Bu sınırlamalar nedeniyle, çoğu Azure hizmeti, müşteri denetimli donanımda sunucu tarafından yönetilen anahtarlar kullanılarak sunucu tarafı şifrelemeyi desteklemez.

##### <a name="key-access"></a>Anahtar erişimi

Müşteri denetimli donanımda hizmet tarafından yönetilen anahtarlar kullanılarak sunucu tarafı şifreleme kullanıldığında, anahtarlar müşteri tarafından yapılandırılan bir sistemde tutulur. Bu modeli destekleyen Azure Hizmetleri, müşterinin sağladığı anahtar deposuyla güvenli bir bağlantı kurmak için bir yol sağlar.

**Üstünlü**

- Kullanılan kök anahtar üzerinde tam denetim – şifreleme anahtarları müşterinin sunduğu bir mağaza tarafından yönetilir
- Birden çok hizmeti tek bir ana öğe ile şifreleme yeteneği
- Hizmet için genel yönetim modelinden anahtar yönetimini ayırt edebilir
- , Bölgeler arasında hizmet ve anahtar konumunu tanımlayabilir

**Olumsuz**

- Anahtar depolama, güvenlik, performans ve kullanılabilirlik için tam sorumluluk
- Anahtar erişim yönetimi için tam sorumluluk
- Anahtar yaşam döngüsü yönetimi için tam sorumluluk
- Önemli kurulum, yapılandırma ve devam eden bakım maliyetleri
- Müşteri veri merkezi ve Azure veri merkezleri arasında ağ kullanılabilirliğine yönelik daha fazla bağımlılık.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Microsoft bulut hizmetlerinde bekleyen şifreleme

Microsoft Bulut Hizmetleri üç bulut modelinde kullanılır: IaaS, PaaS, SaaS. Aşağıda her bir modele nasıl uydukları hakkında örnek verilmiştir:

- Yazılım Hizmetleri, bulut tarafından sunulan ve Office 365 gibi uygulamalar tarafından sağlanmış bir sunucu veya SaaS olarak adlandırılır.
- Bulut, analiz ve hizmet veri yolu işlevselliği gibi şeyler için bulutu kullanarak, müşterilerinin bulutta buluttan faydalanması gereken platform hizmetleri.
- Müşterinin bulutta barındırılan işletim sistemlerini ve uygulamaları dağıttığı ve muhtemelen diğer bulut hizmetlerini kullandığı altyapı hizmetleri veya hizmet olarak altyapı (IaaS).

### <a name="encryption-at-rest-for-saas-customers"></a>SaaS müşterileri için bekleyen şifreleme

Hizmet olarak yazılım (SaaS) müşterileri, genellikle bekleyen veya her hizmette kullanılabilir olan şifrelemeye sahiptir. Office 365, müşterilerin bekleyen şifrelemeyi doğrulaması veya etkinleştirmesi için çeşitli seçeneklere sahiptir. Office 365 hizmetleri hakkında bilgi için bkz. [office 365 ' de şifreleme](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>PaaS müşterileri için bekleyen şifreleme

Hizmet olarak platform (PaaS) müşterinin verileri genellikle BLOB depolama gibi bir depolama hizmetinde bulunur, ancak aynı zamanda bir sanal makine gibi uygulama yürütme ortamında önbelleğe alınabilir veya depolanabilir. Bekleyen Rest seçeneklerinde şifrelemeyi görmek için, kullandığınız depolama ve uygulama platformları için aşağıdaki tabloyu inceleyin.

### <a name="encryption-at-rest-for-iaas-customers"></a>IaaS müşterileri için bekleyen şifreleme

Hizmet olarak altyapı (IaaS) müşterileri, kullanımda olan çeşitli hizmet ve uygulamalara sahip olabilir. IaaS Hizmetleri, Azure disk şifrelemesi 'ni kullanarak Azure 'da barındırılan sanal makinelerde ve VHD 'lerde bekleyen şifrelemeyi etkinleştirebilir.

#### <a name="encrypted-storage"></a>Şifrelenmiş depolama

PaaS gibi IaaS çözümleri, bekleyen verileri depolayan diğer Azure hizmetlerinden yararlanabilir. Bu durumlarda, kullanılan her Azure hizmeti tarafından sağlandığı gibi, bekleyen destek için şifrelemeyi etkinleştirebilirsiniz. Aşağıdaki tabloda, ana depolama, hizmetler ve uygulama platformları ile bekleyen desteklenen şifreleme modeli numaralandırılır. 

#### <a name="encrypted-compute"></a>Şifrelenmiş işlem

Tüm yönetilen diskler, anlık görüntüler ve görüntüler, hizmet tarafından yönetilen anahtar kullanılarak Depolama Hizmeti Şifrelemesi kullanılarak şifrelenir. Rest çözümünde daha kapsamlı bir şifreleme, verilerin şifresiz biçimde kalıcı olmamasını sağlar. Verileri bir sanal makinede işlerken, veriler Windows sayfa dosyası veya Linux takas dosyası, kilitlenme dökümü veya uygulama günlüğü için kalıcı olabilir. Bu verilerin bekleyen bir şekilde şifrelendiğinden emin olmak için IaaS uygulamaları, Azure IaaS sanal makinesi (Windows veya Linux) ve sanal disk üzerinde Azure disk şifrelemesi kullanabilir.

#### <a name="custom-encryption-at-rest"></a>Bekleyen özel şifreleme

Her ne kadar, IaaS uygulamalarının Azure disk şifrelemesi ve şifreleme tarafından kullanılan tüm Azure hizmetleri tarafından sunulan Rest seçeneklerinde kullanılması önerilir. Düzensiz şifreleme gereksinimleri veya Azure tabanlı olmayan depolama gibi bazı durumlarda, bir IaaS uygulamasının geliştiricisi, Rest üzerinde şifreleme uygulaması gerekebilir. IaaS çözümlerinin geliştiricileri, belirli Azure bileşenlerinden yararlanarak Azure yönetimi ve müşteri beklentileri ile daha iyi tümleşebilir. Geliştiriciler özellikle, güvenli anahtar depolaması sağlamak için Azure Key Vault hizmetini ve müşterilerine birçok Azure platform hizmeti ile tutarlı anahtar yönetim seçenekleri sağlamak için kullanmalıdır. Ayrıca, özel çözümlerin, hizmet hesaplarının şifreleme anahtarlarına erişmesini sağlamak için Azure tarafından yönetilen hizmet kimliklerini kullanması gerekir. Azure Key Vault ve yönetilen hizmet kimlikleri hakkında Geliştirici bilgileri için bkz. ilgili SDK 'lar.

## <a name="azure-resource-providers-encryption-model-support"></a>Azure kaynak sağlayıcıları şifreleme modeli desteği

Microsoft Azure Hizmetleri, bekleyen modellerdeki bir veya daha fazla şifrelemeyi destekler. Ancak bazı hizmetlerde, bir veya daha fazla şifreleme modeli uygulanmayabilir. Müşteri tarafından yönetilen anahtar senaryolarını destekleyen hizmetler için, anahtar şifreleme anahtarları için Azure Key Vault desteklediği anahtar türlerinin yalnızca bir alt kümesini destekleyebilir. Ayrıca, hizmetler bu senaryolar ve anahtar türleri için farklı zamanlamalarda destek bırakabilir. Bu bölümde, her bir ana Azure veri depolama hizmeti için bu yazma sırasında Rest desteğiyle ilgili şifreleme açıklanmaktadır.

### <a name="azure-disk-encryption"></a>Azure disk şifrelemesi

Hizmet olarak altyapı (IaaS) özelliklerinin kullanıldığı tüm müşteriler, Azure disk şifrelemesi aracılığıyla IaaS VM 'Leri ve diskleri için bekleyen şifreleme elde edebilir. Azure disk şifrelemesi hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi belgeleri](../azure-security-disk-encryption-overview.md).

#### <a name="azure-storage"></a>Azure depolama alanı

Tüm Azure depolama hizmetleri (BLOB depolama, kuyruk depolama, tablo depolama ve Azure dosyaları), bekleyen sunucu tarafı şifrelemeyi destekler; Ayrıca, bazı hizmetler müşteri tarafından yönetilen anahtarları ve istemci tarafı şifrelemeyi destekler. 

- Sunucu tarafı: tüm Azure depolama hizmetleri, varsayılan olarak, uygulama tarafından saydam olan hizmet tarafından yönetilen anahtarları kullanarak sunucu tarafı şifrelemeyi etkinleştirir. Daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama hizmeti şifrelemesi](../../storage/common/storage-service-encryption.md). Azure Blob depolama ve Azure dosyaları, Azure Key Vault 'de RSA 2048 bit müşteri tarafından yönetilen anahtarları da destekler. Daha fazla bilgi için [Azure Key Vault depolama hizmeti şifrelemesi müşteri tarafından yönetilen anahtarları kullanma](../../storage/common/storage-encryption-keys-portal.md)konusuna bakın.
- İstemci tarafı: Azure Blob 'Ları, tablolar ve kuyruklar, istemci tarafı şifrelemeyi destekler. İstemci tarafı şifreleme kullanılırken, müşteriler verileri şifreler ve verileri şifrelenmiş bir blob olarak yükler. Anahtar Yönetimi Müşteri tarafından yapılır. Daha fazla bilgi için bkz. [Istemci tarafı şifreleme ve Microsoft Azure depolama için Azure Key Vault](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Veritabanı

Azure SQL veritabanı şu anda Microsoft tarafından yönetilen hizmet tarafı ve istemci tarafı şifreleme senaryoları için bekleyen şifrelemeyi desteklemektedir.

Sunucu şifrelemesi desteği şu anda Saydam Veri Şifrelemesi adlı SQL özelliği aracılığıyla sunulmaktadır. Azure SQL veritabanı müşterisi, TDE anahtarı etkinleştirildikten sonra otomatik olarak oluşturulur ve yönetilir. Bekleyen şifreleme, veritabanı ve sunucu düzeylerinde etkinleştirilebilir. Haziran 2017 itibariyle, yeni oluşturulan veritabanlarında [Saydam veri şifrelemesi (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) varsayılan olarak etkindir. Azure SQL veritabanı, Azure Key Vault 'de RSA 2048 bit müşteri tarafından yönetilen anahtarları destekler. Daha fazla bilgi için bkz. [Azure SQL veritabanı ve veri ambarı için kendi anahtarını getir desteğiyle saydam veri şifrelemesi](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Azure SQL veritabanı verilerinin istemci tarafı şifrelemesi [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) özelliği aracılığıyla desteklenir. Always Encrypted, istemci tarafından oluşturulan ve depolanan bir anahtarı kullanır. Müşteriler ana anahtarı bir Windows sertifika deposunda, Azure Key Vault veya yerel bir donanım güvenlik modülünde depolayabilirler. SQL Server Management Studio kullanarak, SQL kullanıcıları hangi sütunu şifrelemek için kullanmak istediğiniz anahtarı seçer.

#### <a name="encryption-model-and-key-management-table"></a>Şifreleme modeli ve anahtar yönetimi tablosu

|                                  |                    | **Şifreleme modeli ve anahtar yönetimi** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Hizmet tarafından yönetilen anahtar kullanılarak sunucu tarafı**     | **Müşteri tarafından yönetilen anahtarı kullanarak sunucu tarafı**             | **İstemci-yönetilen anahtarı kullanarak istemci tarafı**      |
| **AI ve Machine Learning**      |                    |                    |                    |
| Azure Bilişsel Arama                     | Evet                | Önizleme            | -                  |
| Azure Machine Learning   | Evet                | -                  | -                  |
| Azure Machine Learning Studio    | Evet                | Önizleme, RSA 2048 bit | -               |
| Power BI                         | Evet                | Önizleme, RSA 2048 bit | -                  |
| **Analizler**                    |                    |                    |                    |
| Azure Stream Analytics           | Evet                | -                  | -                  |
| Event Hubs                       | Evet                | Önizleme, tüm RSA uzunlukları. | -                  |
| Azure Analysis Services          | Evet                | -                  | -                  |
| Azure Veri Kataloğu               | Evet                | -                  | -                  |
| Azure HDInsight üzerinde Apache Kafka  | Evet                | Tüm RSA uzunlukları.   | -                  |
| Azure Veri Gezgini              | Evet                | -                  | -                  |
| Azure Data Factory               | Evet                | Evet                | -                  |
| Azure Data Lake Store            | Evet                | Evet, RSA 2048 bit  | -                  |
| **Kapsayıcılar**                   |                    |                    |                    |
| Azure Kubernetes Service         | Evet                | -                  | -                  |
| Container Kayıt Defteri               | Evet                | -                  | -                  |
| **İşlem**                      |                    |                    |                    |
| Virtual Machines                 | Evet                | Evet, RSA 2048 bit  | -                  |
| Sanal makine ölçek kümesi        | Evet                | Evet, RSA 2048 bit  | -                  |
| SAP HANA                         | Evet                | Evet, RSA 2048 bit  | -                  |
| **Veritabanları**                    |                    |                    |                    |
| Sanal Makinelerde SQL Server   | Evet                | Evet, RSA 2048 bit  | Evet                |
| Azure SQL Veritabanı               | Evet                | Evet, RSA 2048 bit  | Evet                |
| MariaDB için Azure SQL veritabanı   | Evet                | -                  | -                  |
| MySQL için Azure SQL veritabanı     | Evet                | -                  | -                  |
| PostgreSQL için Azure SQL veritabanı | Evet                | -                  | -                  |
| Azure SQL Veri Ambarı         | Evet                | Evet, RSA 2048 bit  | Evet                |
| SQL Server Stretch Database      | Evet                | Evet, RSA 2048 bit  | Evet                |
| Tablo Depolama                    | Evet                | -                  | Evet                |
| Azure Cosmos DB                  | Evet                | -                  | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps                     | Evet                | -                  | Evet                |
| Azure Repos                      | Evet                | -                  | Evet                |
| **Kimlik**                     |                    |                    |                    |
| Azure Active Directory           | Evet                | -                  | -                  |
| Azure Active Directory Domain Services | Evet          | Evet, RSA 2048 bit  | -                  |
| **Tümleştirme**                  |                    |                    |                    |
| Service Bus                      | Evet                | -                  | Evet                |
| Event Grid                       | Evet                | -                  | -                  |
| API Management                   | Evet                | -                  | -                  |
| **IoT Hizmetleri**                 |                    |                    |                    |
| IoT Hub’ı                          | Evet                | -                  | Evet                |
| **Yönetim ve Idare**    |                    |                    |                    |
| Azure Site Recovery              | Evet                | Evet, RSA 2048 bit  | Evet                |
| **Medyasını**                        |                    |                    |                    |
| Media Services                   | Evet                | -                  | Evet                |
| **Depolama**                      |                    |                    |                    |
| Blob Depolama                     | Evet                | Evet, RSA 2048 bit  | Evet                |
| Disk Depolama                     | Evet                | -                  | -                  |
| Yönetilen Disk Depolama             | Evet                | -                  | -                  |
| Dosya Depolama                     | Evet                | Evet, RSA 2048 bit  | -                  |
| Kuyruk Depolama                    | Evet                | -                  | Evet                |
| Avere vFXT                       | Evet                | -                  | -                  |
| Azure NetApp Files               | Evet                | -                  | -                  |
| Arşiv Depolama                  | Evet                | Evet, RSA 2048 bit  | -                  |
| StorSimple                       | Evet                | Evet, RSA 2048 bit  | Evet                |
| Azure Backup                     | Evet                | -                  | Evet                |
| Data Box                         | Evet                | -                  | Evet                |

## <a name="conclusion"></a>Sonuç

Azure hizmetleri 'nde depolanan müşteri verilerinin korunması, Microsoft 'a ilişkin önemli öneme sahiptir. Azure 'da barındırılan tüm hizmetler, Rest seçeneklerinde şifreleme sağlamaya kararlıdır. Azure depolama, Azure SQL veritabanı ve anahtar analizi ve zekası hizmetleri gibi temel hizmetler, Rest seçeneklerinde zaten şifreleme sağlıyor. Bu hizmetlerden bazıları müşteri denetimli anahtarları ve istemci tarafı şifrelemeyi ve hizmet tarafından yönetilen anahtarları ve şifrelemeyi destekler. Microsoft Azure hizmetler, bekleme kullanılabilirliğine göre şifrelemeyi büyük ölçüde geliştirir ve gelecek aylarda önizleme ve genel kullanılabilirlik için yeni seçenekler planlanmaktadır.
