---
title: Azure veri şifreleme-Rest-Azure güvenliği
description: Bu makalede, Azure veri şifrelemesi için bekleyen, genel yetenekler ve Genel hususlar hakkında genel bir bakış sunulmaktadır.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 1318aaf50d4f05b8a999e5661318dc83997a2f02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96920792"
---
# <a name="azure-data-encryption-at-rest"></a>Bekleyen Azure veri şifrelemesi

Microsoft Azure, şirketinizin güvenlik ve uyumluluk ihtiyaçlarına göre verileri korumaya yönelik araçlar içerir. Bu kağıdın odakları:

- Veriler Microsoft Azure arasında Rest 'ten nasıl korunur
- Veri koruma uygulamasında bir bölümü alan çeşitli bileşenleri açıklar,
- Farklı temel yönetim koruması yaklaşımlarının avantajlarını ve dezavantajlarını inceler.

Bekleyen şifreleme ortak bir güvenlik gereksinimidir. Azure 'da kuruluşlar, bekleyen verileri bir özel anahtar yönetimi çözümünün riski veya maliyeti olmadan şifreleyebilir. Kuruluşların, bekleyen bir şekilde Azure 'un şifrelemeyi tamamen yönetmesine izin verme seçeneği vardır. Ayrıca kuruluşlar, şifrelemeyi veya şifreleme anahtarlarını yakından yönetmek için çeşitli seçeneklere sahiptir.

## <a name="what-is-encryption-at-rest"></a>Bekleyen şifreleme nedir?

Şifreleme, verilerin gizliliğini korumak için kullanılan verilerin güvenli kodlamasıdır. Azure 'daki Rest tasarımlarındaki şifreleme, basit bir kavramsal modele göre büyük miktarlarda verileri şifrelemek ve şifrelerini çözmek için simetrik şifrelemeyi kullanır:

- Verileri depolamaya yazıldığı şekilde şifrelemek için bir simetrik şifreleme anahtarı kullanılır.
- Aynı şifreleme anahtarı, bellekte kullanım için yeniden kullanıma hazır olduğundan bu verilerin şifresini çözmek için kullanılır.
- Veriler bölümlenebilir ve her bölüm için farklı anahtarlar kullanılabilir.
- Anahtarlar kimlik tabanlı erişim denetimi ve denetim ilkeleriyle güvenli bir yerde depolanmalıdır. Veri şifreleme anahtarları genellikle, erişimi daha fazla sınırlandırmak için Azure Key Vault bir anahtar şifreleme anahtarıyla şifrelenir.

Uygulamada, önemli yönetim ve denetim senaryolarında, ölçek ve kullanılabilirlik açısından ek yapılar gerekir. Rest kavramlarını ve bileşenlerini Microsoft Azure şifreleme aşağıda açıklanmıştır.

## <a name="the-purpose-of-encryption-at-rest"></a>Bekleyen şifreleme amacı

Rest 'de şifreleme, depolanan veriler (bekleyen) için veri koruması sağlar. Rest verilerine yönelik saldırılar, verilerin depolandığı donanıma fiziksel erişimi alma girişimlerini ve ardından içerilen verilerin güvenliğini sağlar. Böyle bir saldırıya karşı, bir saldırganın sabit sürücüyü kaldırmasına izin veren bakım sırasında bir sunucunun sabit sürücüsü hatalı işlenmiş olabilir. Daha sonra saldırgan, verilere erişmeyi denemek için sabit sürücüyü kendi denetimindeki bir bilgisayara yerleştirir.

Bekleyen şifreleme, verilerin disk üzerinde şifrelendiğinden emin olmak için, saldırganın şifrelenmemiş verilere erişmesini engellemek üzere tasarlanmıştır. Bir saldırgan şifrelenmiş verileri olan ancak şifreleme anahtarları olmayan bir sabit sürücü alırsa, verileri okumak için saldırganın şifrelemeyi ertelemeniz gerekir. Bu saldırı, sabit bir sürücüdeki şifrelenmemiş verilere erişenden çok daha karmaşık ve kaynak kullanıyor. Bu nedenle, bekleyen şifreleme son derece önerilir ve birçok kuruluş için yüksek öncelikli bir gereksinimdir.

Bekleyen şifreleme, bir kuruluşun veri idare ve uyumluluk çabalarına ihtiyacı olarak da gerekli olabilir. HIPAA, PCI ve Fedratin gibi sektör ve kamu düzenlemeleri, veri koruma ve şifreleme gereksinimleriyle ilgili belirli korumaları düzenleyin. Bekleyen şifreleme, bu düzenlemelerle uyumluluk için gerekli olan zorunlu bir ölçüdür. Microsoft 'un FIPS 140-2 doğrulamasına yaklaşımı hakkında daha fazla bilgi için bkz. [Federal bilgi Işleme standardı (FIPS) yayını 140-2](/microsoft-365/compliance/offering-fips-140-2).

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

Anahtar şifreleme anahtarlarıyla şifrelenen veri şifreleme anahtarları ayrı olarak saklanır ve yalnızca anahtar şifreleme anahtarına erişimi olan bir varlık bu veri şifreleme anahtarlarının şifresini çözebilir. Farklı anahtar depolama modelleri desteklenir. Daha fazla bilgi için bkz. [veri şifreleme modelleri](encryption-models.md) .

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Microsoft bulut hizmetlerinde bekleyen şifreleme

Microsoft Bulut Hizmetleri üç bulut modelinde kullanılır: IaaS, PaaS, SaaS. Aşağıda her bir modele nasıl uydukları hakkında örnek verilmiştir:

- Microsoft 365 gibi bulut tarafından sağlanmış uygulamalar içeren, sunucu veya SaaS olarak yazılım olarak adlandırılan yazılım hizmetleri.
- Bulut, analiz ve hizmet veri yolu işlevselliği gibi şeyler için bulutu kullanarak, müşterilerinin bulutta buluttan faydalanması gereken platform hizmetleri.
- Müşterinin bulutta barındırılan işletim sistemlerini ve uygulamaları dağıttığı ve muhtemelen diğer bulut hizmetlerini kullandığı altyapı hizmetleri veya hizmet olarak altyapı (IaaS).

### <a name="encryption-at-rest-for-saas-customers"></a>SaaS müşterileri için bekleyen şifreleme

Hizmet olarak yazılım (SaaS) müşterileri, genellikle bekleyen veya her hizmette kullanılabilir olan şifrelemeye sahiptir. Microsoft 365, müşterilerin bekleyen şifrelemeyi doğrulaması veya etkinleştirmesi için çeşitli seçeneklere sahiptir. Microsoft 365 hizmetleri hakkında bilgi için bkz. [Microsoft 365 'de şifreleme](/microsoft-365/compliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>PaaS müşterileri için bekleyen şifreleme

Hizmet olarak platform (PaaS) müşterinin verileri genellikle BLOB depolama gibi bir depolama hizmetinde bulunur, ancak aynı zamanda bir sanal makine gibi uygulama yürütme ortamında önbelleğe alınabilir veya depolanabilir. Bekleyen Rest seçeneklerinde şifrelemeyi görmek için, kullandığınız depolama ve uygulama platformları için aşağıdaki tabloyu inceleyin.

### <a name="encryption-at-rest-for-iaas-customers"></a>IaaS müşterileri için bekleyen şifreleme

Hizmet olarak altyapı (IaaS) müşterileri, kullanımda olan çeşitli hizmet ve uygulamalara sahip olabilir. IaaS Hizmetleri, Azure disk şifrelemesi 'ni kullanarak Azure 'da barındırılan sanal makinelerde ve VHD 'lerde bekleyen şifrelemeyi etkinleştirebilir.

#### <a name="encrypted-storage"></a>Şifrelenmiş depolama

PaaS gibi IaaS çözümleri, bekleyen verileri depolayan diğer Azure hizmetlerinden yararlanabilir. Bu durumlarda, kullanılan her Azure hizmeti tarafından sağlandığı gibi, bekleyen destek için şifrelemeyi etkinleştirebilirsiniz. Aşağıdaki tabloda, ana depolama, hizmetler ve uygulama platformları ile bekleyen desteklenen şifreleme modeli numaralandırılır.

#### <a name="encrypted-compute"></a>Şifrelenmiş işlem

Tüm yönetilen diskler, anlık görüntüler ve görüntüler, hizmet tarafından yönetilen anahtar kullanılarak Depolama Hizmeti Şifrelemesi kullanılarak şifrelenir. Rest çözümünde daha kapsamlı bir şifreleme, verilerin şifresiz biçimde kalıcı olmamasını sağlar. Verileri bir sanal makinede işlerken, veriler Windows sayfa dosyası veya Linux takas dosyası, kilitlenme dökümü veya uygulama günlüğü için kalıcı olabilir. Bu verilerin bekleyen bir şekilde şifrelendiğinden emin olmak için IaaS uygulamaları, Azure IaaS sanal makinesi (Windows veya Linux) ve sanal disk üzerinde Azure disk şifrelemesi kullanabilir.

#### <a name="custom-encryption-at-rest"></a>Bekleyen özel şifreleme

Her ne kadar, IaaS uygulamalarının Azure disk şifrelemesi ve şifreleme tarafından kullanılan tüm Azure hizmetleri tarafından sunulan Rest seçeneklerinde kullanılması önerilir. Düzensiz şifreleme gereksinimleri veya Azure tabanlı olmayan depolama gibi bazı durumlarda, bir IaaS uygulamasının geliştiricisi, Rest üzerinde şifreleme uygulaması gerekebilir. IaaS çözümlerinin geliştiricileri, belirli Azure bileşenlerinden yararlanarak Azure yönetimi ve müşteri beklentileri ile daha iyi tümleşebilir. Geliştiriciler özellikle, güvenli anahtar depolaması sağlamak için Azure Key Vault hizmetini ve müşterilerine birçok Azure platform hizmeti ile tutarlı anahtar yönetim seçenekleri sağlamak için kullanmalıdır. Ayrıca, özel çözümlerin, hizmet hesaplarının şifreleme anahtarlarına erişmesini sağlamak için Azure-Managed hizmet kimliklerini kullanması gerekir. Azure Key Vault ve yönetilen hizmet kimlikleri hakkında Geliştirici bilgileri için bkz. ilgili SDK 'lar.

## <a name="azure-resource-providers-encryption-model-support"></a>Azure kaynak sağlayıcıları şifreleme modeli desteği

Microsoft Azure Hizmetleri, bekleyen modellerdeki bir veya daha fazla şifrelemeyi destekler. Ancak bazı hizmetlerde, bir veya daha fazla şifreleme modeli uygulanmayabilir. Müşteri tarafından yönetilen anahtar senaryolarını destekleyen hizmetler için, anahtar şifreleme anahtarları için Azure Key Vault desteklediği anahtar türlerinin yalnızca bir alt kümesini destekleyebilir. Ayrıca, hizmetler bu senaryolar ve anahtar türleri için farklı zamanlamalarda destek bırakabilir. Bu bölümde, her bir ana Azure veri depolama hizmeti için bu yazma sırasında Rest desteğiyle ilgili şifreleme açıklanmaktadır.

### <a name="azure-disk-encryption"></a>Azure disk şifrelemesi

Hizmet olarak altyapı (IaaS) özelliklerinin kullanıldığı tüm müşteriler, Azure disk şifrelemesi aracılığıyla IaaS VM 'Leri ve diskleri için bekleyen şifreleme elde edebilir. Azure disk şifrelemesi hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi belgeleri](./azure-disk-encryption-vms-vmss.md).

#### <a name="azure-storage"></a>Azure depolama

Tüm Azure depolama hizmetleri (BLOB depolama, kuyruk depolama, tablo depolama ve Azure dosyaları), bekleyen sunucu tarafı şifrelemeyi destekler; Ayrıca, bazı hizmetler müşteri tarafından yönetilen anahtarları ve istemci tarafı şifrelemeyi destekler.

- Sunucu tarafı: tüm Azure depolama hizmetleri, varsayılan olarak, uygulama tarafından saydam olan hizmet tarafından yönetilen anahtarları kullanarak sunucu tarafı şifrelemeyi etkinleştirir. Daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama hizmeti şifrelemesi](../../storage/common/storage-service-encryption.md). Azure Blob depolama ve Azure dosyaları, Azure Key Vault 'de RSA 2048 bit müşteri tarafından yönetilen anahtarları da destekler. Daha fazla bilgi için [Azure Key Vault depolama hizmeti şifrelemesi müşteri tarafından yönetilen anahtarları kullanma](../../storage/common/customer-managed-keys-configure-key-vault.md)konusuna bakın.
- İstemci tarafı: Azure Blob 'Ları, tablolar ve kuyruklar, istemci tarafı şifrelemeyi destekler. İstemci tarafı şifreleme kullanılırken, müşteriler verileri şifreler ve verileri şifrelenmiş bir blob olarak yükler. Anahtar Yönetimi Müşteri tarafından yapılır. Daha fazla bilgi için bkz. [Istemci tarafı şifreleme ve Microsoft Azure depolama için Azure Key Vault](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Veritabanı

Azure SQL veritabanı şu anda Microsoft tarafından yönetilen hizmet tarafı ve istemci tarafı şifreleme senaryoları için bekleyen şifrelemeyi desteklemektedir.

Sunucu şifrelemesi desteği şu anda Saydam Veri Şifrelemesi adlı SQL özelliği aracılığıyla sunulmaktadır. Azure SQL veritabanı müşterisi, TDE anahtarı etkinleştirildikten sonra otomatik olarak oluşturulur ve yönetilir. Bekleyen şifreleme, veritabanı ve sunucu düzeylerinde etkinleştirilebilir. Haziran 2017 itibariyle, yeni oluşturulan veritabanlarında [Saydam veri şifrelemesi (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) varsayılan olarak etkindir. Azure SQL veritabanı, Azure Key Vault 'de RSA 2048 bit müşteri tarafından yönetilen anahtarları destekler. Daha fazla bilgi için bkz. [Azure SQL veritabanı ve veri ambarı için kendi anahtarını getir desteğiyle saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql).

Azure SQL veritabanı verilerinin istemci tarafı şifrelemesi [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) özelliği aracılığıyla desteklenir. Always Encrypted, istemci tarafından oluşturulan ve depolanan bir anahtarı kullanır. Müşteriler ana anahtarı bir Windows sertifika deposunda, Azure Key Vault veya yerel bir donanım güvenlik modülünde depolayabilirler. SQL Server Management Studio kullanarak, SQL kullanıcıları hangi sütunu şifrelemek için kullanmak istediğiniz anahtarı seçer.

## <a name="conclusion"></a>Sonuç

Azure hizmetleri 'nde depolanan müşteri verilerinin korunması, Microsoft 'a ilişkin önemli öneme sahiptir. Azure 'da barındırılan tüm hizmetler, Rest seçeneklerinde şifreleme sağlamaya kararlıdır. Azure Hizmetleri, hizmet tarafından yönetilen anahtarları, müşteri tarafından yönetilen anahtarları veya istemci tarafı şifrelemeyi destekler. Azure Hizmetleri, bekleme kullanılabilirliğine göre şifrelemeyi büyük ölçüde geliştirir ve gelecek aylarda önizleme ve genel kullanılabilirlik için yeni seçenekler planlanmaktadır.

## <a name="next-steps"></a>Sonraki adımlar

- Hizmet tarafından yönetilen anahtarlar ve müşteri tarafından yönetilen anahtarlar hakkında daha fazla bilgi edinmek için bkz. [veri şifreleme modelleri](encryption-models.md) .
- Azure 'un, verileri şifreleyen tehditleri azaltmak için [çift şifrelemeyi](double-encryption.md) nasıl kullandığını öğrenin.
- Microsoft 'un, donanım ve üretici yazılımı oluşturma, tümleştirme, işlem başlatma ve onarım işlem hatlarından geçen ana bilgisayarların [Platform bütünlüğünü ve güvenliğini](platform.md) sağlamak için neler yaptığını öğrenin.
