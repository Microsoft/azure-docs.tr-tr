---
title: Azure yönetilen HSM 'ye genel bakış-Azure Managed HSM | Microsoft Docs
description: Azure yönetilen HSM, bulut uygulamaları için şifreleme anahtarlarınızı koruyan bir bulut hizmetidir.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 04/01/2021
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: a9df83535bf0ee7a6c484a7682392d2ad0ef59f7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815333"
---
# <a name="what-is-azure-key-vault-managed-hsm"></a>Azure Key Vault Yönetilen HSM nedir?

Azure Key Vault yönetilen HSM, **fıps 140-2 düzey 3** tarafından doğrulanan HSM 'leri kullanarak bulut uygulamalarınızın şifreleme anahtarlarını korumanıza olanak sağlayan, tam olarak yönetilen, yüksek oranda kullanılabilir, tek kiracılı ve standartlara uygun bir bulut hizmetidir. Fiyatlandırma bilgileri için lütfen [Azure Key Vault fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/key-vault/)yönetilen HSM havuzları bölümüne bakın. 

## <a name="why-use-managed-hsm"></a>Neden yönetilen HSM kullanmalıyım?

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>Tam olarak yönetilen, yüksek oranda kullanılabilir, tek kiracılı HSM hizmet olarak

- **Tam olarak yönetilen**: HSM sağlama, yapılandırma, düzeltme eki uygulama ve bakım hizmet tarafından gerçekleştirilir. 
- **Yüksek oranda kullanılabilir ve bölge** dayanıklı (kullanılabilirlik alanları desteklenir): her HSM kümesi, en az iki kullanılabilirlik bölgesi arasında yayılan bırden çok HSM bölümden oluşur. Donanım başarısız olursa, HSM kümenizin üye bölümleri otomatik olarak sağlıklı düğümlere geçirilir.
- **Tek kiracılı**: her BIR yönetilen HSM örneği tek bir müşteriye ayrılmıştır ve bırden çok HSM bölümünün kümesinden oluşur. Her HSM kümesi, her müşterinin HSM kümesini şifreli olarak yalıtan, müşteriye özgü ayrı bir güvenlik etki alanı kullanır.


### <a name="access-control-enhanced-data-protection--compliance"></a>Erişim denetimi, gelişmiş veri koruma & uyumluluğu

- **Merkezi anahtar yönetimi**: kuruluşunuzda kritik ve yüksek değerli anahtarları tek bir yerde yönetin. Anahtar izinleri başına ayrıntılı olarak, ' en az ayrıcalıklı erişim ' prensibi her bir anahtara erişimi denetleyin.
- **Yalıtılmış erişim denetimi**: yönetilen HSM "yerel RBAC" erişim denetimi modeli, belirlenen HSM küme yöneticilerinin, yönetim grubu, abonelik veya kaynak grubu yöneticilerinin geçersiz kılınamamasına karşın, hsms üzerinde tamamen denetime sahip olmasını sağlar.
- **Fıps 140-2 düzey 3 doğrulanan HSM**'ler: FIPS ((Federal Information Protection standart)) 140-2 düzey 3 tarafından doğrulanan HSM 'lerle verilerinizi koruyun ve uyumluluk gereksinimlerini karşılayın. Yönetilen HSM 'ler, Marvell LiquidSecurity HSM bağdaştırıcılarını kullanır.
- **İzleme ve denetim**: Azure izleyici ile tam olarak tümleşik. Tüm etkinliklerin Azure Izleyici aracılığıyla tüm günlüklerini alın. Analiz ve uyarılar için Azure Log Analytics kullanın.
- **Veri** yerleşimi: yönetilen HSM, müşteri VERILERINI müşterinin HSM örneğini dağıttığı bölge dışında depolamaz/işlemez.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Azure ve Microsoft PaaS/SaaS hizmetleriyle tümleşik 

- [Azure depolama](../../storage/common/customer-managed-keys-overview.md), [azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md)ve [Azure Information Protection](/azure/information-protection/byok-price-restrictions)gibi Azure hizmetlerinde bekleyen verileri şifrelemek için ( [bYok](hsm-protected-keys-byok.md)) anahtarlarını oluşturun (veya bu anahtarları kullanarak içeri aktarın).

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Key Vault aynı API ve yönetim arabirimlerini kullanır

- Bir kasa kullanan mevcut uygulamalarınızı (çok kiracılı) yönetilen HSM 'leri kullanacak şekilde kolayca geçirin.
- Kullanılan anahtar yönetim çözümüyle bağımsız olarak tüm uygulamalarınız için aynı uygulama geliştirme ve dağıtım düzenlerini kullanın: çok kiracılı kasa veya tek kiracılı yönetilen HSM 'ler

### <a name="import-keys-from-your-on-premise-hsms"></a>Şirket içi HSM 'lerinizdeki anahtarları içeri aktarın

- Şirket içi HSM 'niz içinde HSM korumalı anahtarlar oluşturun ve bunları yönetilen HSM 'ye güvenli bir şekilde aktarın

## <a name="next-steps"></a>Sonraki adımlar
- Bkz [. hızlı başlangıç: Azure CLI kullanarak](quick-create-cli.md) yönetilen bir HSM oluşturma ve etkinleştirme
- Bkz. [Azure Key Vault Managed HSM kullanarak En Iyi uygulamalar](best-practices.md)
