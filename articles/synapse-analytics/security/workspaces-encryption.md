---
title: Azure SYNAPSE Analytics şifrelemesi
description: Azure SYNAPSE Analytics 'te şifrelemeyi açıklayan bir makale
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 71249534c6a088088213659b5a45e042229721c7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813191"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics çalışma alanları için şifreleme

Bu makalede şunları anlayacaksınız:
* SYNAPSE Analytics çalışma alanlarında bekleyen verilerin şifrelenmesi.
* Müşteri tarafından yönetilen anahtarla şifrelemeyi etkinleştirmek için SYNAPSE çalışma alanlarının yapılandırması.
* Çalışma alanlarındaki verileri şifrelemek için kullanılan anahtarları yönetme.

## <a name="encryption-of-data-at-rest"></a>Bekleyen verilerin şifrelenmesi

Bekleyen bir şifreleme çözümü, verilerin şifreli olmayan biçimde hiçbir zaman kalıcı olmamasını sağlar. Bekleyen verilerin çift şifrelemesi, tek bir katmanın güvencesine karşı korumak için iki, ayrı şifreleme katmanları ile tehditleri azaltır. Azure SYNAPSE Analytics, çalışma alanınızdaki veriler için müşterinin yönettiği bir anahtarla ikinci bir şifreleme katmanı sunar. Bu anahtar Azure Key Vault, anahtar yönetiminin ve döndürmenin sahipliğini almanıza olanak tanıyan [](../../key-vault/general/overview.md)korunmuş bir şekilde korunur.

Azure hizmetleri için ilk şifreleme katmanı, platform tarafından yönetilen anahtarlarla etkinleştirilir. Varsayılan olarak, Azure diskleri ve Azure depolama hesaplarındaki veriler Rest 'de otomatik olarak şifrelenir. [Azure şifrelemesi 'Ne genel bakış](../../security/fundamentals/encryption-overview.md)'da Microsoft Azure ' de şifrelemenin nasıl kullanıldığı hakkında daha fazla bilgi edinin.

## <a name="azure-synapse-encryption"></a>Azure SYNAPSE şifrelemesi

Bu bölüm, SYNAPSE çalışma alanlarında müşterinin yönettiği anahtar şifrelemenin nasıl etkinleştirildiğini ve zorlandığını daha iyi anlamanıza yardımcı olur. Bu şifreleme, Azure Key Vault oluşturulan mevcut anahtarları veya yeni anahtarları kullanır. Bir çalışma alanındaki tüm verileri şifrelemek için tek bir anahtar kullanılır. SYNAPSE çalışma alanları, 2048 ve 3072 bayt boyutlu anahtarlarla RSA anahtarlarını destekler.

> [!NOTE]
> SYNAPSE çalışma alanları, şifreleme için Elliptic-Curve şifreleme (ECC) anahtarları kullanımını desteklemez.

Aşağıdaki SYNAPSE bileşenlerindeki veriler, çalışma alanı düzeyinde yapılandırılmış müşteri tarafından yönetilen anahtarla şifrelenir:
* SQL havuzları
 * Adanmış SQL havuzları
 * Sunucusuz SQL havuzları
* Apache Spark havuzları
* Tümleştirme çalışma zamanları, işlem hatları, veri kümeleri Azure Data Factory.

## <a name="workspace-encryption-configuration"></a>Çalışma alanı şifreleme yapılandırması

Çalışma alanları, müşteri tarafından yönetilen bir anahtarla çalışma alanı oluşturma sırasında çift şifrelemeyi etkinleştirecek şekilde yapılandırılabilir. Yeni çalışma alanınızı oluştururken "güvenlik" sekmesinde "müşteri tarafından yönetilen anahtar kullanarak çift şifrelemeyi etkinleştir" seçeneğini seçin. Anahtar tanımlayıcı URI 'SI girmeyi veya çalışma alanıyla **aynı bölgedeki** anahtar kasaların listesinden seçim yapabilirsiniz. Key Vault, **Temizleme koruması 'nın etkinleştirilmesini** gerektirir.

> [!IMPORTANT]
> Çift şifrelemenin yapılandırma ayarı, çalışma alanı oluşturulduktan sonra değiştirilemez.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Bu diyagramda, müşteri tarafından yönetilen bir anahtarla çift şifrelemeye yönelik bir çalışma alanı etkinleştirmek için seçilmesi gereken seçeneği gösterilmektedir.":::

### <a name="key-access-and-workspace-activation"></a>Anahtar erişimi ve çalışma alanı etkinleştirme

Müşteri tarafından yönetilen anahtarlarla Azure SYNAPSE şifreleme modeli, gerektiğinde şifreleme ve şifre çözme için Azure Key Vault anahtarlara erişen çalışma alanını içerir. Anahtarlar, erişim ilkesi aracılığıyla veya [RBAC erişimi Azure Key Vault](../../key-vault/general/rbac-guide.md)çalışma alanı tarafından erişilebilir hale getirilir. Azure Key Vault erişim ilkesi aracılığıyla izin verirken, ilke oluşturma sırasında ["yalnızca uygulama"](../../key-vault/general/security-features.md#key-vault-authentication-options) seçeneğini belirleyin (çalışma alanının yönetilen kimliğini seçin ve yetkili bir uygulama olarak eklemeyin).

 Çalışma alanının, çalışma alanının etkinleştirilmeden önce anahtar kasasında ihtiyaç duyması gereken izinler verilmelidir. Çalışma alanı etkinleştirmesine yönelik bu aşamalı yaklaşım, çalışma alanındaki verilerin müşteri tarafından yönetilen anahtarla şifrelenmesini sağlar. Şifrelemenin adanmış SQL havuzları için etkinleştirilip etkinleştirilebileceğine veya devre dışı bırakılabileceğini unutmayın. her havuz varsayılan olarak şifreleme için etkinleştirilmemiştir.

#### <a name="permissions"></a>İzinler

Bekleyen verileri şifrelemek veya şifrelerini çözmek için, çalışma alanı yönetilen kimliği aşağıdaki izinlere sahip olmalıdır:
* WrapKey (yeni anahtar oluştururken Key Vault bir anahtar eklemek için).
* UnwrapKey (şifre çözme anahtarını almak için).
* Al (bir anahtarın ortak bölümünü okumak için)

#### <a name="workspace-activation"></a>Çalışma alanı etkinleştirme

Çalışma alanınız (Çift şifreleme etkinken) oluşturulduktan sonra, etkinleştirme başarılı olana kadar "bekleyen" bir durumda kalır. Tüm işlevleri tamamen kullanabilmeniz için çalışma alanının etkinleştirilmesi gerekir. Örneğin, etkinleştirme başarılı olduktan sonra yalnızca yeni bir adanmış SQL havuzu oluşturabilirsiniz. Çalışma alanı yönetilen kimlik erişimini anahtar kasasına verin ve çalışma alanındaki etkinleştirme bağlantısına tıklayın Azure portal başlık. Etkinleştirme başarıyla tamamlandıktan sonra, çalışma alanınız, içindeki tüm verilerin müşteri tarafından yönetilen anahtarınızla korunduğu güvenle birlikte kullanıma hazır hale gelir. Daha önce belirtildiği gibi, etkinleştirme işleminin başarılı olabilmesi için anahtar kasasının Temizleme koruması etkinleştirilmiş olması gerekir.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Bu diyagramda, çalışma alanı için etkinleştirme bağlantısı olan başlık görüntülenir.":::


### <a name="manage-the-workspace-customer-managed-key"></a>Çalışma alanı müşteri tarafından yönetilen anahtarı yönetme 

Azure portal **şifreleme** sayfasından verileri şifrelemek için kullanılan müşteri tarafından yönetilen anahtarı değiştirebilirsiniz. Burada, bir anahtar tanımlayıcı kullanarak yeni bir anahtar seçebilir veya çalışma alanıyla aynı bölgede erişiminiz olan anahtar kasalarından seçim yapabilirsiniz. Daha önce kullanılan farklı bir anahtar kasasında bir anahtar seçerseniz, yeni anahtar kasasında çalışma alanı yönetilen kimliği "Al", "Wrap" ve "sarmalama" izinlerini verin. Çalışma alanı, yeni anahtar kasasına erişimini doğrular ve çalışma alanındaki tüm veriler yeni anahtarla yeniden şifrelenir.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Bu diyagramda Azure portal çalışma alanı şifreleme bölümü gösterilmektedir.":::

>[!IMPORTANT]
>Bir çalışma alanının şifreleme anahtarını değiştirirken, anahtarı, çalışma alanında yenisiyle yeni bir anahtarla değiştirene kadar saklayın. Bu, yeni anahtarla yeniden şifrelenmeden önce eski anahtarla verilerin şifresinin çözülmesini sağlar.

Otomatik, dönemsel anahtar döndürme veya anahtarlar üzerindeki eylemlerin Azure Anahtar Kasası ilkeleri, yeni anahtar sürümlerinin oluşturulmasına neden olabilir. Çalışma alanındaki tüm verileri etkin anahtarın en son sürümüyle yeniden şifrelemeyi tercih edebilirsiniz. Yeniden şifrelemek için Azure portal anahtarı geçici bir anahtarla değiştirin ve ardından şifreleme için kullanmak istediğiniz anahtara geri dönün. Örnek olarak, Active Key KEY1 'ın en son sürümünü kullanarak veri şifrelemeyi güncelleştirmek için, çalışma alanı müşteri tarafından yönetilen anahtarı geçici anahtar, key2 olarak değiştirin. Key2 ile şifrelemeyi bekleyin. Daha sonra çalışma alanı müşteri tarafından yönetilen anahtarı KEY1 'e geri geçirin. çalışma alanındaki veriler en son KEY1 sürümü ile yeniden şifrelenir.

> [!NOTE]
> Azure SYNAPSE Analytics, yeni anahtar sürümleri oluşturulduğunda verileri otomatik olarak yeniden şifrelemek için işlev ekleme konusunda etkin bir şekilde çalışır. Bu işlevsellik kullanılabilir olana kadar, çalışma alanınızda tutarlılığı sağlamak için yukarıda açıklanan işlemi kullanarak verilerin yeniden şifrelenmesini zorlayın.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>Hizmet tarafından yönetilen anahtarlarla SQL Saydam Veri Şifrelemesi

SQL Saydam Veri Şifrelemesi (TDE) *, çalışma alanlarındaki* adanmış SQL havuzları için çift şifreleme etkinleştirilmemiş olarak kullanılabilir. Bu çalışma alanında, adanmış SQL havuzlarındaki veriler için çift şifrelemeyi sağlamak üzere hizmet tarafından yönetilen bir anahtar kullanılır. Hizmet tarafından yönetilen anahtarla TDE bağımsız adanmış SQL havuzları için etkinleştirilebilir veya devre dışı bırakılabilir.

## <a name="next-steps"></a>Sonraki adımlar

[SYNAPSE çalışma alanları için şifreleme koruması uygulamak üzere yerleşik Azure Ilkeleri kullanma](../policy-reference.md)

