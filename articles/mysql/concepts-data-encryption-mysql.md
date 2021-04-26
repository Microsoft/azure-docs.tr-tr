---
title: Müşteri tarafından yönetilen anahtar ile veri şifreleme-MySQL için Azure veritabanı
description: Müşteri tarafından yönetilen bir anahtarla MySQL için Azure veritabanı veri şifrelemesi, bekleyen veri koruması için Kendi Anahtarını Getir (BYOK) sağlar. Kuruluşlar bu sayede anahtarların ve verilerin yönetiminde görev ayrımı yapabilir.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: d8e40cf9dac496266f67ad94e1e65db01e42f9d2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816845"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Müşteri tarafından yönetilen bir anahtarla MySQL için Azure veritabanı veri şifrelemesi

MySQL için Azure Veritabanı'nda verilerin müşteri tarafından yönetilen anahtarlarla şifrelenmesini sağlayarak bekleyen veriler için kendi anahtarını getir (KAG) yaklaşımından faydalanabilirsiniz. Kuruluşlar bu sayede anahtarların ve verilerin yönetiminde görev ayrımı yapabilir. Müşteri tarafından yönetilen şifreleme senaryosunda anahtarın yaşam döngüsü, anahtar kullanım izinleri ve anahtar işlemlerinin denetimi konusunda sorumlu ve tam denetim sahibi olursunuz.

MySQL için Azure veritabanı 'nda müşteri tarafından yönetilen anahtarlarla veri şifreleme, sunucu düzeyinde ayarlanır. Belirli bir sunucu için, anahtar şifreleme anahtarı (KEK) olarak adlandırılan ve hizmet tarafından kullanılan veri şifreleme anahtarını (DEK) şifrelemek için kullanılan müşteri tarafından yönetilen bir anahtar kullanılır. KEK, müşterinin sahip olduğu ve müşteri tarafından yönetilen [Azure Key Vault](../key-vault/general/security-features.md) örneğinde depolanan bir asimetrik anahtardır. Anahtar şifreleme anahtarı (KEK) ve veri şifreleme anahtarı (DEK), bu makalenin ilerleyen kısımlarında daha ayrıntılı olarak açıklanmıştır.

Key Vault, bulut tabanlı, dış anahtar yönetim sistemidir. Bu yüksek oranda kullanılabilir ve isteğe bağlı olarak FIPS 140-2 düzey 2 tarafından doğrulanan donanım güvenlik modülleri (HSM 'ler) tarafından desteklenen RSA şifreleme anahtarlarına yönelik ölçeklenebilir, güvenli depolama alanı sağlar. Depolanan bir anahtara doğrudan erişime izin vermez, ancak yetkili varlıklara şifreleme ve şifre çözme hizmetleri sağlar. Key Vault anahtarı oluşturabilir, içeri aktarabilir veya [Şirket içi BIR HSM cihazından aktarılmasını](../key-vault/keys/hsm-protected-keys.md)sağlayabilir.

> [!NOTE]
> Bu özellik, MySQL için Azure veritabanı 'nın "Genel Amaçlı" ve "bellek için Iyileştirilmiş" fiyatlandırma katmanlarını desteklediği tüm Azure bölgelerinde kullanılabilir. Diğer sınırlamalar için [sınırlandırma](concepts-data-encryption-mysql.md#limitations) bölümüne bakın.

## <a name="benefits"></a>Avantajlar

MySQL için Azure veritabanı 'nda müşteri tarafından yönetilen anahtarlarla veri şifreleme aşağıdaki avantajları sağlar:

* Veri erişimi, anahtarı kaldırabilme ve veritabanını erişilemez hale getirme özelliği tarafından tam olarak denetlenir 
* Anahtar yaşam döngüsü üzerinde, kurumsal ilkelerle hizalamak için anahtar dönüşü dahil tam denetim
* Azure Key Vault içinde anahtarların merkezi yönetimi ve organizasyonu
* Güvenlik ofisler ve DBA ve sistem yöneticileri arasında görev ayrımı uygulama yeteneği


## <a name="terminology-and-description"></a>Terminoloji ve açıklama

**Veri şifreleme anahtarı (dek)**: bir bölümü veya veri bloğunu şifrelemek için kullanılan BIR simetrik AES256 anahtarı. Farklı bir anahtarla her bir veri bloğunu şifrelemek, şifre çözümleme saldırılarını daha zor hale getirir. Belirli bir bloğu şifreleyen ve şifresini çözen kaynak sağlayıcı veya uygulama örneği, DEKs 'e erişim için gereklidir. Bir DEK 'ı yeni bir anahtarla değiştirdiğinizde, yalnızca ilişkili bloğundaki verilerin yeni anahtarla yeniden şifrelenmesi gerekir.

**Anahtar şifreleme anahtarı (kek)**: Deks 'leri şifrelemek için kullanılan bir şifreleme anahtarı. Key Vault hiçbir şekilde ayrılmayacak olan bir KEK, bunların şifreli ve denetimli olmasını sağlar. KEK 'e erişimi olan varlık, DEK gerektiren varlıktan farklı olabilir. KEK 'in şifresini çözmek için gerekli olduğundan, KEK, KEK silinerek etkin bir şekilde silinebilen tek bir noktasıdır.

KEKs ile şifrelenen DEKs 'ler ayrı olarak depolanır. Yalnızca KEK erişimi olan bir varlık bu DEKs 'in şifresini çözebilir. Daha fazla bilgi için bkz. [rest 'de şifrelemede güvenlik](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Müşteri tarafından yönetilen anahtar çalışma ile veri şifreleme

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysqloverview.png" alt-text="Kendi Anahtarını Getir bir genel bakış gösteren diyagram":::

Bir MySQL sunucusunun, DEK ' de şifreleme için Key Vault depolanan müşteri tarafından yönetilen anahtarları kullanması için, bir Key Vault Yöneticisi sunucuya aşağıdaki erişim haklarını verir:

* **Get**: anahtar kasasındaki anahtarın ortak bölümünü ve özelliklerini almak için.
* **wrapKey**: dek ' i şifreleyebilmek için. Şifrelenmiş DEK, MySQL için Azure veritabanı 'nda depolanır.
* **unwrapKey**: dek ' nin şifresini çözebilmek için. MySQL için Azure veritabanı 'nın, verileri şifrelemek/şifrelerini çözmek için şifresi çözülmüş DEK gerekir

Anahtar Kasası Yöneticisi ayrıca [Key Vault denetim olaylarının günlüğe kaydedilmesini etkinleştirerek](../azure-monitor/insights/key-vault-insights-overview.md)daha sonra denetlenebilir.

Sunucu, anahtar kasasında depolanan müşteri tarafından yönetilen anahtarı kullanacak şekilde yapılandırıldığında, sunucu DEK şifrelemeleri anahtar kasasına gönderir. Key Vault, Kullanıcı veritabanında depolanan şifreli DEK döndürür. Benzer şekilde, gerektiğinde sunucu, şifre çözme için korunan DEK anahtar kasasına gönderilir. Denetçiler, günlüğe kaydetme etkinse Key Vault denetim olay günlüklerini gözden geçirmek için Azure Izleyici 'yi kullanabilir.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>MySQL için Azure veritabanı için veri şifrelemeyi yapılandırma gereksinimleri

Key Vault yapılandırmaya yönelik gereksinimler aşağıda verilmiştir:

* MySQL için Azure veritabanı Key Vault ve aynı Azure Active Directory (Azure AD) kiracısına ait olmalıdır. Çapraz kiracı Key Vault ve sunucu etkileşimleri desteklenmez. Key Vault kaynağı taşıma daha sonra veri şifrelemeyi yeniden yapılandırmanız gerekir.
* Yanlışlıkla bir anahtar (veya Key Vault) silme işlemi gerçekleşdiğinde veri kaybını korumak için, saklama süresi **90 gün** olarak ayarlanan anahtar kasasındaki [geçici silme](../key-vault/general/soft-delete-overview.md) özelliğini etkinleştirin. Saklama süresi açıkça <= 90 gün olarak ayarlanmadığı takdirde, geçici olarak silinen kaynaklar varsayılan olarak 90 gün boyunca tutulur. Kurtarma ve Temizleme eylemlerinin Key Vault erişim ilkesiyle ilişkili kendi izinleri vardır. Geçici silme özelliği varsayılan olarak kapalıdır, ancak PowerShell veya Azure CLı aracılığıyla etkinleştirebilir (Azure portal aracılığıyla etkinleştirebileceğinizi unutmayın).
* Anahtar kasasında saklama süresi **90 gün** olarak ayarlanan [korumayı temizle](../key-vault/general/soft-delete-overview.md#purge-protection) özelliğini etkinleştirin. Temizleme koruması, yalnızca geçici silme etkin olduğunda etkinleştirilebilir. Azure CLı veya PowerShell aracılığıyla etkinleştirilebilir. Temizleme koruması açık olduğunda, saklama süresi geçene kadar bir kasa veya silinen durumdaki bir nesne temizlenemiyor. Geçici olarak silinen kasalar ve nesneler kurtarılabilir ve bu da bekletme ilkesinin izlenmeyeceğinden emin olur. 
* MySQL için Azure veritabanı 'na, benzersiz yönetilen kimliğini kullanarak Get, wrapKey ve unwrapKey izinleriyle anahtar kasasına erişim izni verin. Azure portal, MySQL üzerinde veri şifrelemesi etkinleştirildiğinde benzersiz ' hizmet ' kimliği otomatik olarak oluşturulur. Azure portal kullanırken, ayrıntılı, adım adım yönergeler için bkz. [MySQL için veri şifrelemeyi yapılandırma](howto-data-encryption-portal.md) .

Aşağıda, müşteri tarafından yönetilen anahtarı yapılandırmaya yönelik gereksinimler verilmiştir:

* DEK şifrelemek için kullanılan müşteri tarafından yönetilen anahtar yalnızca asimetrik, RSA 2048 olabilir.
* Anahtar etkinleştirme tarihi (ayarlandıysa), geçmişteki bir tarih ve saat olmalıdır. Sona erme tarihi ayarlanmadı.
* Anahtar *etkin* durumda olmalıdır.
* Anahtarın, bekletme süresi **90 gün** olarak ayarlanan [geçici silme](../key-vault/general/soft-delete-overview.md) işlemi yapılmalıdır. Bu, gerekli anahtar özniteliği recoveryLevel ' a örtük olarak ayarlanır: "kurtarılabilir". Bekletme < 90 gün, recoveryLevel: "CustomizedRecoverable" olarak ayarlanırsa, saklama süresi ayarı **90 gün** olarak ayarlanmalıdır.
* Anahtarın [Temizleme koruması etkin](../key-vault/general/soft-delete-overview.md#purge-protection)olmalıdır.
* Anahtar kasasında [mevcut bir anahtarı içeri](/rest/api/keyvault/ImportKey/ImportKey) aktarıyorsanız, bunu desteklenen dosya biçimlerinde ( `.pfx` , `.byok` ,) sağladığınızdan emin olun `.backup` .

## <a name="recommendations"></a>Öneriler

Müşteri tarafından yönetilen bir anahtar kullanarak veri şifrelemeyi kullanırken, Key Vault yapılandırmaya yönelik öneriler aşağıda verilmiştir:

* Bu kritik kaynağı kimlerin silebilen ve yanlışlıkla veya yetkisiz silme işlemini engelleyebilecek kişileri denetlemek için Key Vault bir kaynak kilidi ayarlayın.
* Tüm şifreleme anahtarlarında denetim ve raporlamayı etkinleştirin. Key Vault, diğer güvenlik bilgilerine ve olay yönetim araçlarına eklemek kolay olan Günlükler sağlar. Azure Izleyici Log Analytics, zaten tümleştirilmiş bir hizmetin bir örneğidir.
* MySQL için Key Vault ve Azure veritabanı 'nın aynı bölgede bulunduğundan emin olun ve bu DEK sarmalama için daha hızlı bir erişim sağlayın ve sarmalama işlemlerini geri sarın.
* Azure Keykasasını yalnızca **Özel uç nokta ve seçili ağlarda** kilitleyin ve yalnızca *Güvenilen Microsoft* hizmetlerinin kaynakları güvenli hale getirmeye izin verin.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="Güvenilen-hizmet-AKV":::

Müşteri tarafından yönetilen anahtarı yapılandırmaya yönelik öneriler aşağıda verilmiştir:

* Müşterinin yönettiği anahtarın bir kopyasını güvenli bir yerde tutun veya Emanet hizmetine sağlayın.

* Key Vault anahtarı oluşturursa, anahtarı ilk kez kullanmadan önce anahtar yedeklemesi oluşturun. Key Vault için yalnızca yedeklemeyi geri yükleyebilirsiniz. Backup komutu hakkında daha fazla bilgi için bkz. [Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Erişilemeyen müşteri tarafından yönetilen anahtar koşulu

Key Vault, müşteri tarafından yönetilen bir anahtarla veri şifrelemeyi yapılandırdığınızda, sunucunun çevrimiçi kalması için bu anahtara sürekli erişim gerekir. Sunucu, Key Vault müşterinin yönettiği anahtara erişimi kaybederse, sunucu 10 dakikalık bir süre içinde tüm bağlantıları reddetmeye başlar. Sunucu karşılık gelen bir hata iletisi yayınlar ve sunucu durumunu *erişilemez* olarak değiştirir. Sunucunun bu duruma ulaşmasının bazı nedenleri şunlardır:

* Veri şifrelemesi etkin olan MySQL için Azure veritabanınız için zaman Içinde geri yükleme sunucusunda bir nokta oluşturuyorsanız, yeni oluşturulan sunucu *erişilemez* durumda olur. [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) veya [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)aracılığıyla bu hatayı çözebilirsiniz.
* Veri şifrelemesi etkin olan MySQL için Azure veritabanı için bir okuma çoğaltması oluşturuyorsanız, çoğaltma sunucusu *erişilemez* durumda olur. [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) veya [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)aracılığıyla bu hatayı çözebilirsiniz.
* Keykasasını silerseniz, MySQL için Azure veritabanı anahtara erişemez ve *erişilemez* duruma geçer. [Key Vault](../key-vault/general/key-vault-recovery.md) kurtarın ve sunucuyu *kullanılabilir* hale getirmek için veri şifrelemeyi yeniden doğrulayın.
* Anahtar kasasından anahtarı sileriz, MySQL için Azure veritabanı anahtara erişemez ve *erişilemez* duruma geçer. [Anahtarı](../key-vault/general/key-vault-recovery.md) kurtarın ve sunucuyu *kullanılabilir* hale getirmek için veri şifrelemeyi yeniden doğrulayın.
* Azure Keykasasında depolanan anahtarın süresi dolarsa, anahtar geçersiz hale gelir ve MySQL için Azure veritabanı *erişilemez* duruma geçer. [CLI](/cli/azure/keyvault/key#az_keyvault_key_set-attributes) kullanarak anahtar sona erme tarihini genişletin ve ardından sunucuyu *kullanılabilir* hale getirmek için veri şifrelemesini yeniden doğrulayın.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Key Vault 'den yanlışlıkla anahtar erişimi iptali

Key Vault için yeterli erişim haklarına sahip birinin, yanlışlıkla anahtara sunucu erişimini devre dışı bırakmasından kaynaklanabilir:

* Anahtar kasasının Get, wrapKey ve unwrapKey izinleri sunucudan iptal ediliyor.
* Anahtar siliniyor.
* Anahtar Kasası siliniyor.
* Anahtar kasasının güvenlik duvarı kurallarını değiştirme.
* Azure AD 'de sunucunun yönetilen kimliğini silme.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Key Vault 'de müşteri tarafından yönetilen anahtarı izleyin

Veritabanı durumunu izlemek ve saydam veri şifreleme koruyucusu erişiminin kaybedilmesi için uyarı etkinleştirmek üzere aşağıdaki Azure özelliklerini yapılandırın:

* [Azure Kaynak durumu](../service-health/resource-health-overview.md): müşteri anahtarına erişimi kesilen erişilemeyen bir veritabanı, veritabanına ilk bağlantı reddedildikten sonra "erişilemez" olarak gösterilir.
* [Etkinlik günlüğü](../service-health/alerts-activity-log-service-notifications-portal.md): müşteri tarafından yönetilen Key Vault müşteri anahtarına erişim başarısız olursa, girdiler etkinlik günlüğüne eklenir. Bu olaylar için uyarı oluşturursanız, erişimi mümkün olan en kısa sürede yeniden yükleyebilirsiniz.

* [Eylem grupları](../azure-monitor/alerts/action-groups.md): tercihlerinize göre size bildirim ve uyarı göndermek için bu grupları tanımlayın.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Key Vault bir müşterinin yönetilen anahtarıyla geri yükleme ve çoğaltma

MySQL için Azure veritabanı, Key Vault ' de depolanan bir müşterinin yönetilen anahtarıyla şifrelendikten sonra, sunucunun yeni oluşturulan kopyası da şifrelenir. Bu yeni kopyayı yerel veya coğrafi geri yükleme işlemiyle ya da okuma çoğaltmaları aracılığıyla yapabilirsiniz. Ancak, kopya yeni bir müşterinin şifreleme için yönetilen anahtarını yansıtacak şekilde değiştirilebilir. Müşteri tarafından yönetilen anahtar değiştirildiğinde, sunucunun eski yedeklemeleri en son anahtarı kullanmaya başlar.

Geri yükleme veya okuma çoğaltması oluşturma sırasında müşteri tarafından yönetilen veri şifrelemesini ayarlarken oluşan sorunlardan kaçınmak için, bu adımları kaynak ve geri yüklenmiş/çoğaltma sunucularında izlemeniz önemlidir:

* MySQL için kaynak Azure veritabanından geri yükleme veya okuma çoğaltması oluşturma işlemini başlatın.
* Yeni oluşturulan sunucuyu (geri yüklenmiş/çoğaltma) erişilemez durumda tutun, çünkü benzersiz kimliği henüz Key Vault izinler olarak verilmemiştir.
* Geri yüklenen/çoğaltma sunucusunda, yeni oluşturulan sunucuya Key Vault ' de depolanan anahtara sarmalama ve sarmalama izinleri verilmesini sağlamak için veri şifreleme ayarlarındaki müşteri tarafından yönetilen anahtarı yeniden doğrulayın.

## <a name="limitations"></a>Sınırlamalar

MySQL için Azure veritabanı 'nda, müşteriler tarafından yönetilen anahtar (CMK) kullanılarak bekleyen verilerin şifrelenmesi için bazı sınırlamalar vardır-

* Bu işlevselliğe yönelik destek, **genel amaçlı** ve bellek için **iyileştirilmiş** fiyatlandırma katmanlarında sınırlandırılmıştır.
* Bu özellik yalnızca 16 TB'a kadar depolamayı destekleyen bölgelerde ve sunucularda desteklenir. 16 TB 'a kadar depolamayı destekleyen Azure bölgelerinin listesi için [buradaki](concepts-pricing-tiers.md#storage) belgelerde bulunan depolama bölümüne bakın

    > [!NOTE]
    > - Yukarıda listelenen bölgelerde oluşturulan tüm yeni MySQL sunucuları, müşteri yöneticisi anahtarlarıyla şifreleme desteği **mevcuttur**. Geri yüklenen zaman noktası (INR) sunucusu veya okuma çoğaltması, teorik olarak ' New ' olarak nitelendirilecektir.
    > - Sağlanan sunucunuzun 16 TB 'a kadar destekleyip desteklemediğini doğrulamak için, portalda fiyatlandırma katmanı dikey penceresine gidebilir ve sağlanan sunucunuzun desteklediği en fazla depolama boyutunu görebilirsiniz. Kaydırıcıyı 4TB 'a kadar taşıyabiliyorsanız, sunucunuz müşterinin yönettiği anahtarlarla şifrelemeyi desteklemiyor olabilir. Ancak, veriler hizmet tarafından yönetilen anahtarlar kullanılarak her zaman şifrelenir. Sorularınız varsa lütfen öğesine ulaşın AskAzureDBforMySQL@service.microsoft.com .

* Şifreleme yalnızca RSA 2048 şifreleme anahtarıyla desteklenir.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Portal](howto-data-encryption-portal.md) ve [Azure CLI](howto-data-encryption-cli.md)kullanarak MySQL için Azure veritabanınız için müşteri tarafından yönetilen bir anahtarla veri şifrelemeyi ayarlamayı öğrenin.
