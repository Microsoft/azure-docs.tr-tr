---
title: Disk şifrelemesi ile Azure HDInsight kümeleri Key Vault erişimi kaybeder
description: Azure HDInsight kümeleriyle etkileşim kurarken Key Vault erişim sorunları için sorun giderme adımları ve olası çözümler.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: ce2929ca84746de1ab8b51882f3004c3699f17ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943109"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Senaryo: disk şifrelemesi ile Azure HDInsight kümeleri Key Vault erişimi kaybeder

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Kaynak Durumu Center (RHC) uyarısı, `The HDInsight cluster is unable to access the key for BYOK encryption at rest` küme düğümlerinin müşterilere kayıp erişimi olan Key Vault (KV) kendi anahtarını getir (BYOK) kümeleri için gösterilir. Benzer uyarılar da Apache ambarı Kullanıcı arabiriminde görülebilir.

## <a name="cause"></a>Nedeni

Uyarı, KV 'nin küme düğümlerinden erişilebilir olmasını sağlar, böylece ağ bağlantısı, KV sistem durumu ve Kullanıcı tarafından atanan yönetilen kimlik için erişim ilkesini sağlar. Bu uyarı yalnızca sonraki düğüm yeniden başlatıldığında aracı kapatmasından önce gelen bir uyarıdır. düğümler yeniden başlatılıncaya kadar küme çalışmaya devam eder.

**Disk şifreleme Key Vault durumu** uyarısı hakkında daha fazla bilgi edinmek Için Apache ambarı Kullanıcı arabirimine gidin. Bu uyarı, doğrulama hatasının nedeni hakkında ayrıntılara sahip olacaktır.

## <a name="resolution"></a>Çözüm

### <a name="kvaad-outage"></a>KV/AAD kesintisi

Daha fazla ayrıntı için [Azure Key Vault kullanılabilirlik ve artıklık](../../key-vault/general/disaster-recovery-guidance.md) ve Azure durum sayfasına bakın https://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV yanlışlıkla silme

* KV içindeki silinen anahtarı otomatik olarak kurtar ' a geri yükleyin. Daha fazla bilgi için bkz. [silinen anahtarı kurtarma](/rest/api/keyvault/recoverdeletedkey).
* Yanlışlıkla silinmelerden kurtulmak için KV ekibine ulaşın.

### <a name="kv-access-policy-changed"></a>KV erişim ilkesi değişti

KV öğesine erişmek için HDI kümesine atanan kullanıcı tarafından atanan yönetilen kimliğin erişim ilkelerini geri yükleyin.

### <a name="key-permitted-operations"></a>Anahtara izin verilen işlemler

KV içindeki her anahtar için izin verilen işlemler kümesini seçebilirsiniz. BYOK anahtarı için sarmalama ve sarmalama işlemlerini etkin duruma aldığınızdan emin olun

### <a name="expired-key"></a>Süre dolma anahtarı

Süre geçtikten sonra anahtar döndürülmemişse, yedekleme HSM 'sinden anahtarı geri yükleyin veya süre sonu tarihini temizlemek için KV ekibine başvurun.

### <a name="kv-firewall-blocking-access"></a>KV güvenlik duvarı engelleme erişimi

KV güvenlik duvarı ayarlarını, BYOK kümesi düğümlerinin KV öğesine erişmesine izin verecek şekilde düzeltir.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>Sanal ağ engelleme erişimi için NSG kuralları

Kümeye bağlı sanal ağla ilişkili NSG kurallarını denetleyin.

## <a name="mitigation-and-prevention-steps"></a>Risk azaltma ve önleme adımları

### <a name="kv-accidental-deletion"></a>KV yanlışlıkla silme

* [Kaynak kilidi kümesiyle](../../azure-resource-manager/management/lock-resources.md)Key Vault yapılandırın.
* Anahtarları donanım güvenlik modülüne yedekleyin.

### <a name="key-deletion"></a>Anahtar silme

Anahtar silinmeden önce küme silinmelidir.

### <a name="kv-access-policy-changed"></a>KV erişim ilkesi değişti

Erişim ilkelerini düzenli olarak denetleme ve test etme.

### <a name="expired-key"></a>Süre dolma anahtarı

* Anahtarları HSM 'nize yedekleyin.
* Süre sonu kümesi olmayan bir anahtar kullanın.
* Süre sonu ayarlanması gerekiyorsa, bitiş tarihinden önce anahtarları döndürün.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.