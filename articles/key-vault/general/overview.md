---
title: Azure Key Vault genel bakış-Azure Key Vault
description: Azure Key Vault, tüm donanım güvenlik modülleri tarafından desteklenen gizli dizileri, anahtarları ve sertifikaları yönetme olanağı sağlayan güvenli bir gizli diziler deposudur.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 3e6ed6bc8ba426a981b7a2390e7d00d9c3b1e785
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643453"
---
# <a name="about-azure-key-vault"></a>Azure Key Vault hakkında

Azure Key Vault aşağıdaki sorunları çözmeye yardımcı olur:

- **Gizli Dizi Yönetimi**: Belirteçleri, parolaları, sertifikaları, API anahtarlarını ve diğer gizli dizileri Güvenle depolamak ve bunlara erişimi sıkı bir şekilde denetlemek için Azure Key Vault kullanılabilir.
- **Anahtar Yönetimi**: Azure Key Vault, Anahtar Yönetimi çözümü olarak da kullanılabilir. Azure Key Vault, verilerinizi şifrelemek için kullanılan şifreleme anahtarlarını oluşturmayı ve denetlemeyi kolaylaştırır. 
- **Sertifika yönetimi** -Azure Key Vault Ayrıca Azure ve iç bağlı kaynaklarınız ile kullanmak üzere genel ve özel aktarım katmanı güvenlik/GÜVENLI yuva KATMANı (TLS/SSL) sertifikalarını kolayca sağlamanıza, yönetmenize ve dağıtmanıza olanak tanıyan bir hizmettir.

Azure Key Vault iki hizmet katmanına sahiptir: bir yazılım anahtarıyla şifreleyen standart ve donanım güvenlik modülü (HSM) ile korunan anahtarlar içeren bir Premium katman. Standart ve Premium Katmanlar arasındaki karşılaştırmayı görmek için [Azure Key Vault fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/key-vault/)bakın.

## <a name="why-use-azure-key-vault"></a>Neden Azure Key Vault kullanmalıyım?

### <a name="centralize-application-secrets"></a>Uygulama gizli dizilerini merkezi hale getirme

Azure Key Vault’ta uygulama gizli dizilerinin depolanmasını merkezi hale getirerek dağılımlarını denetleyebilirsiniz. Key Vault, gizli dizilerin yanlışlıkla sızdırılma olasılığını büyük oranda azaltır. Key Vault kullanırken, uygulama geliştiricilerinin güvenlik bilgilerini uygulamalarında depolaması artık gerekli değildir. Uygulamalarda güvenlik bilgilerini depolamak zorunda kalmamanız, bu bilgileri kodun bir parçası haline getirme gereksinimini ortadan kaldırır. Örneğin, bir uygulamanın bir veritabanına bağlanması gerekebilir. Bağlantı dizesini uygulamanın kodunda depolamak yerine, Key Vault ' de güvenli bir şekilde depolayabilirsiniz.

Uygulamalarınız, URI 'Leri kullanarak ihtiyaç duydukları bilgilere güvenli bir şekilde erişebilir. Bu URI 'Ler, uygulamaların belirli bir gizli sürümü almasına izin verir. Key Vault ' de depolanan gizli bilgileri korumak için özel kod yazmak gerekmez.

### <a name="securely-store-secrets-and-keys"></a>Gizli dizileri ve anahtarları güvenle depolama

Bir anahtar kasasına erişim, bir çağıranın (kullanıcı ya da uygulama) erişim elde edebilmesi için uygun kimlik doğrulaması ve yetkilendirme gerektirir. Kimlik doğrulama işlemi çağıranın kimliğini, yetkilendirme işlemi ise çağıranın yapmaya izinli olduğu işlemleri belirler.

Kimlik doğrulaması Azure Active Directory aracılığıyla yapılır. Yetkilendirme, Azure rol tabanlı erişim denetimi (Azure RBAC) veya Key Vault erişim ilkesi aracılığıyla yapılabilir. Azure RBAC, kasaların yönetimiyle ilgilenirken kullanılır ve bir kasada depolanan verilere erişmeye çalışırken Anahtar Kasası erişim ilkesi kullanılır.

Azure Anahtar kasaları, yazılım korumalı veya Azure Key Vault Premium katmanı ile donanım güvenlik modülleri (HSM 'ler) tarafından korunan donanım korumalı olabilir. Yazılım korumalı anahtarlar, gizlilikler ve sertifikalar, sektör standardı algoritmalar ve anahtar uzunlukları kullanılarak Azure tarafından korunur.  Ek güvence gerektiren durumlar için HSM sınırında hiçbir yerde hiçbir yerde olmayan HSM 'lerde içeri aktarabilir veya anahtarlar oluşturabilirsiniz. Azure Key Vault, Federal bilgi Işleme standartları (FIPS) 140-2 düzey 2 olan nCipher HSMs 'yi kullanır. NCipher araçlarını kullanarak HSM 'nizden bir anahtarı Azure Key Vault taşıyabilirsiniz.

Son olarak Azure Key Vault, Microsoft verilerinizi görmeyecek ve ayıklamayacak şekilde tasarlanmıştır.

### <a name="monitor-access-and-use"></a>Erişimi ve kullanımı izleme

Birkaç Key Vault oluşturduktan sonra anahtarlarınıza ve gizli dizilerinize nasıl ve ne zaman erişildiğini izlemek istersiniz. Kasalarınız için günlük kaydını etkinleştirerek etkinliği izleyebilirsiniz. Azure Key Vault’u aşağıdaki işlemleri yapacak şekilde yapılandırabilirsiniz:

- Bir depolama hesabına arşivleme.
- Bir olay hub'ına akış yapma.
- Günlükleri Azure Izleyici günlüklerine gönderin.

Günlükleriniz üzerinde denetime sahip olursunuz ve erişimi kısıtlayarak günlükleri güvenli hale getirebilir ve artık gerekli olmayan günlükleri de silebilirsiniz.

### <a name="simplified-administration-of-application-secrets"></a>Uygulama gizli dizilerinin basitleştirilmiş yönetimi

Değerli verileri depolarken birkaç adım uygulamanız gerekir. Güvenlik bilgileri güvenli hale getirilmeli, bir yaşam döngüsü izlemelidir ve yüksek oranda kullanılabilir olması gerekir. Azure Key Vault, bu gereksinimleri şu şekilde toplantı sürecini basitleştirir:

- Donanım güvenlik modülleriyle ilgili şirket içi bilgi için gereksinimi kaldırma.
- Kuruluşunuzun kullanım artışlarını karşılamak için kısa bildirimde ölçeği büyütme.
- Bir bölge içindeki Anahtar Kasanızın içeriklerini ikincil bir bölgeye çoğaltma. Veri çoğaltma, yüksek kullanılabilirlik sağlar ve yük devretmeyi tetiklemek için yöneticiden herhangi bir eylemin gereksinimini ortadan alır.
- Portal, Azure CLI ve PowerShell aracılığıyla standart Azure yönetim seçeneklerini sağlama.
- Genel CA’lardan satın aldığınız sertifikalarla ilgili kaydetme ve yenileme gibi belirli görevleri otomatikleştirme.

Ayrıca, Azure Anahtar Kasalarını kullanarak uygulama gizli dizilerini ayırabilirsiniz. Uygulamalar yalnızca erişimine izin verilen kasaya erişebilir ve yalnızca belirli işlemleri gerçekleştirmeye sınırlı olabilir. Uygulama başına bir Azure Key Vault oluşturabilir ve bir Key Vault’ta depolanmış gizli dizileri belirli bir uygulama ve geliştirici takımı ile kısıtlayabilirsiniz.

### <a name="integrate-with-other-azure-services"></a>Diğer Azure hizmetleri ile tümleştirme

Azure 'da güvenli bir mağaza olarak, şunun gibi senaryoları basitleştirmek için Key Vault kullanılmıştır:
-  [Azure Disk Şifrelemesi](../../security/fundamentals/encryption-overview.md)
-  SQL Server ve Azure SQL veritabanı 'nda [her zaman şifrelenmiş](/sql/relational-databases/security/encryption/always-encrypted-database-engine) ve [Saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption) işlevi
- [Azure App Service](/azure/app-service/configure-ssl-certificate).

Key Vault, depolama hesapları, olay hub’ları ve günlük analizi ile tümleştirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Anahtarlar, gizli diziler ve sertifikalar](about-keys-secrets-certificates.md) hakkında daha fazla bilgi edinin
- [Hızlı Başlangıç: CLI kullanarak bir Azure Key Vault oluşturma](../secrets/quick-create-cli.md)
- [Kimlik doğrulama, istekler ve yanıtlar](../general/authentication-requests-and-responses.md)
- [Key Vault Geliştirici Kılavuzu](../general/developers-guide.md)
