---
title: Azure Key Vault genel bakış-Azure Key Vault | Microsoft Docs
description: Azure Key Vault, güvenli bir gizli dizi deposu olarak çalışan bir bulut hizmetidir.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 6b59995dc09c0ecf1e5700d8337bd30074083f76
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73483909"
---
# <a name="what-is-azure-key-vault"></a>Azure Anahtar Kasası nedir?

Azure Key Vault aşağıdaki sorunları çözmeye yardımcı olur:

- **Gizli Dizi Yönetimi**: Belirteçleri, parolaları, sertifikaları, API anahtarlarını ve diğer gizli dizileri Güvenle depolamak ve bunlara erişimi sıkı bir şekilde denetlemek için Azure Key Vault kullanılabilir.
- **Anahtar Yönetimi**: Azure Key Vault, Anahtar Yönetimi çözümü olarak da kullanılabilir. Azure Key Vault, verilerinizi şifrelemek için kullanılan şifreleme anahtarlarını oluşturmayı ve denetlemeyi kolaylaştırır. 
- **Sertifika Yönetimi**: Azure Key Vault aynı zamanda, Azure ve bağlı iç kaynaklarınızla kullanım için genel ve özel Güvenli Yuva Katmanı/Aktarım Katmanı Güvenliği (SSL/TLS) sertifikalarını kolayca hazırlamanıza, yönetmenize ve dağıtmanıza olanak sağlayan bir hizmettir. 
- **Gizli dizileri Donanım Güvenlik Modülleri desteğiyle saklayın**: Gizli diziler ve anahtarlar yazılım tarafından korunabilir veya FIPS 140-2 Düzey 2, HSM'leri doğrular

## <a name="why-use-azure-key-vault"></a>Neden Azure Key Vault kullanmalıyım?

### <a name="centralize-application-secrets"></a>Uygulama gizli dizilerini merkezi hale getirme

Azure Key Vault’ta uygulama gizli dizilerinin depolanmasını merkezi hale getirerek dağılımlarını denetleyebilirsiniz. Key Vault, gizli dizilerin yanlışlıkla sızdırılma olasılığını büyük oranda azaltır. Key Vault kullanırken, uygulama geliştiricilerinin güvenlik bilgilerini uygulamalarında depolaması artık gerekli değildir. Uygulamalarda güvenlik bilgilerini depolamak zorunda kalmamanız, bu bilgileri kodun bir parçası haline getirme gereksinimini ortadan kaldırır. Örneğin, bir uygulamanın bir veritabanına bağlanması gerekebilir. Bağlantı dizesini uygulamanın kodunda depolamak yerine, Key Vault ' de güvenli bir şekilde depolayabilirsiniz.

Uygulamalarınız, URI 'Leri kullanarak ihtiyaç duydukları bilgilere güvenli bir şekilde erişebilir. Bu URI 'Ler, uygulamaların belirli bir gizli sürümü almasına izin verir. Key Vault ' de depolanan gizli bilgileri korumak için özel kod yazmak gerekmez.

### <a name="securely-store-secrets-and-keys"></a>Gizli dizileri ve anahtarları güvenle depolama

Gizli diziler ve anahtarlar, endüstri standardında algoritmalar, anahtar uzunlukları ve donanım güvenliği modülleri (HSM'ler) kullanılarak Azure tarafından korunur. Kullanılan HSM’ler Federal Bilgi İşleme Standartları (FIPS) 140-2 Düzey 2 ile doğrulanmıştır.

Bir anahtar kasasına erişim, bir çağıranın (kullanıcı ya da uygulama) erişim elde edebilmesi için uygun kimlik doğrulaması ve yetkilendirme gerektirir. Kimlik doğrulama işlemi çağıranın kimliğini, yetkilendirme işlemi ise çağıranın yapmaya izinli olduğu işlemleri belirler.

Kimlik doğrulaması Azure Active Directory aracılığıyla yapılır. Yetkilendirme, rol tabanlı erişim denetimi (RBAC) veya Key Vault erişim ilkesi aracılığıyla yapılabilir. Kasaların yönetimi gerçekleştirilirken RBAC, bir kasada depolanmış verilere erişmeye çalışırken ise anahtar kasası erişim ilkesi kullanılır.

Azure Anahtar Kasaları yazılım veya donanım HSM korumalı olabilir. Ek güvence gereken durumlar için HSM sınırını asla terk etmeyen donanım güvenlik modüllerinde (HSM'ler) anahtarları içeri aktarabilir veya oluşturabilirsiniz. Microsoft, nCipher donanım güvenlik modüllerini kullanır. NCipher araçlarını kullanarak HSM 'nizden bir anahtarı Azure Key Vault taşıyabilirsiniz.

Son olarak Azure Key Vault, Microsoft verilerinizi görmeyecek ve ayıklamayacak şekilde tasarlanmıştır.

### <a name="monitor-access-and-use"></a>Erişimi ve kullanımı izleme

Birkaç Key Vault oluşturduktan sonra anahtarlarınıza ve gizli dizilerinize nasıl ve ne zaman erişildiğini izlemek istersiniz. Kasalarınız için günlük kaydını etkinleştirerek etkinliği izleyebilirsiniz. Azure Key Vault’u aşağıdaki işlemleri yapacak şekilde yapılandırabilirsiniz:

- Bir depolama hesabına arşivleme.
- Bir olay hub'ına akış yapma.
- Günlükleri Azure Izleyici günlüklerine gönderin.

Günlükleriniz üzerinde denetime sahip olursunuz ve erişimi kısıtlayarak günlükleri güvenli hale getirebilir ve artık gerekli olmayan günlükleri de silebilirsiniz.

### <a name="simplified-administration-of-application-secrets"></a>Uygulama gizli dizilerinin basitleştirilmiş yönetimi

Değerli verileri depolarken birkaç adım uygulamanız gerekir. Güvenlik bilgileri güvenli hale getirilmeli, bir yaşam döngüsünü izlemelidir, yüksek oranda kullanılabilir olması gerekir. Azure Key Vault, bu gereksinimleri şu şekilde toplantı sürecini basitleştirir:

- Şirket içi Donanım Güvenlik Modüllerine yönelik gereksinimi ortadan kaldırma
- Kuruluşunuzun ani kullanım artışlarını karşılamak için kısa süre içinde ölçek artırma.
- Bir bölge içindeki Anahtar Kasanızın içeriklerini ikincil bir bölgeye çoğaltma. Veri çoğaltma, yüksek kullanılabilirlik sağlar ve yük devretmeyi tetiklemek için yöneticiden herhangi bir eylemin gereksinimini ortadan alır.
- Portal, Azure CLI ve PowerShell aracılığıyla standart Azure yönetim seçeneklerini sağlama.
- Genel CA’lardan satın aldığınız sertifikalarla ilgili kaydetme ve yenileme gibi belirli görevleri otomatikleştirme.

Ayrıca, Azure Anahtar Kasalarını kullanarak uygulama gizli dizilerini ayırabilirsiniz. Uygulamalar yalnızca erişimine izin verilen kasaya erişebilir ve yalnızca belirli işlemleri gerçekleştirmeye sınırlı olabilir. Uygulama başına bir Azure Key Vault oluşturabilir ve bir Key Vault’ta depolanmış gizli dizileri belirli bir uygulama ve geliştirici takımı ile kısıtlayabilirsiniz.

### <a name="integrate-with-other-azure-services"></a>Diğer Azure hizmetleri ile tümleştirme

Azure 'da güvenli bir mağaza olarak, şunun gibi senaryoları basitleştirmek için Key Vault kullanılmıştır:
-  [Azure disk şifrelemesi](../security/fundamentals/encryption-overview.md)
-  SQL Server ve Azure SQL veritabanı 'ndaki [her zaman şifrelenmiş]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) işlevler
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate). 

Key Vault, depolama hesapları, olay hub’ları ve günlük analizi ile tümleştirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı Başlangıç: CLI kullanarak bir Azure Key Vault oluşturma](quick-create-cli.md)
- [Anahtar Kasasından gizli dizi okumak için bir Azure web uygulaması yapılandırma](tutorial-web-application-keyvault.md)
