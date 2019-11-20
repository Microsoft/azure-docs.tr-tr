---
title: Bulut iş yüklerini korumaya yardımcı olan güvenlik özellikleri
description: Yedeklemeleri daha güvenli hale getirmek için Azure Backup güvenlik özelliklerini kullanmayı öğrenin.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 95eb72fe9d918b527cdceec69a0e90a682d62b07
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172714"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Azure Backup kullanan bulut iş yüklerini korumanıza yardımcı olacak güvenlik özellikleri

Kötü amaçlı yazılım, fidye yazılımı ve yetkisiz erişim gibi güvenlik sorunlarına karşı duyulan endişe gittikçe artıyor. Bu güvenlik sorunları, hem parasal anlamda hem de veriler açısından maliyetli olabilir. Bu tür saldırılara karşı koruma için Azure Backup artık, silme işleminden sonra bile yedekleme verilerini korumaya yardımcı olacak güvenlik özellikleri sağlamaktadır. Bu tür bir özellik, geçici bir DELETE. Geçici bir aktör bir VM 'nin yedeklemesini silse (veya yedekleme verileri yanlışlıkla silinse), yedekleme verileri 14 ek gün boyunca tutulur ve bu yedekleme öğesinin veri kaybı olmadan kurtarılmasını sağlar. "Geçici silme" durumundaki yedekleme verilerinin bu ek 14 gün boyunca saklanması müşteriye hiçbir ücret vermez.

> [!NOTE]
> Geçici silme yalnızca silinen yedekleme verilerini korur. Bir VM, yedekleme olmadan silinirse, geçici silme özelliği verileri korumaz. Tam esnekliği sağlamak için tüm kaynakların Azure Backup korumalı olması gerekir.
>

## <a name="soft-delete"></a>Geçici silme

### <a name="supported-regions"></a>Desteklenen bölgeler

Geçici silme Şu anda Orta Batı ABD, Doğu Asya, Kanada Orta, Kanada Doğu, Fransa Orta, Fransa Güney, Kore Orta, Kore Güney, UK Güney, UK Batı, Avustralya Doğu, Avustralya Güney Doğu, Kuzey Avrupa, Batı ABD, Batı ABD2, Orta ABD, Güney Doğu Asya, Orta Kuzey ABD, Orta Güney ABD, Japonya Doğu, Japonya Batı, Hindistan Güney, Hindistan Orta, Hindistan Batı, Doğu ABD 2, İsviçre Kuzey, İsviçre Batı ve tüm ulusal bölgeler.

### <a name="soft-delete-for-vms"></a>VM 'Ler için geçici silme

1. Bir sanal makinenin yedekleme verilerini silmek için yedeklemenin durdurulması gerekir. Azure portal, kurtarma hizmetleri kasanıza gidin, Yedekleme öğesine sağ tıklayın ve **Yedeklemeyi Durdur**' u seçin.

   ![Azure portal yedekleme öğelerinin ekran görüntüsü](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Aşağıdaki pencerede, yedekleme verilerini silme veya tutma seçeneği sunulur. **Yedekleme verilerini sil** ' i ve sonra **Yedeklemeyi Durdur**' u seçerseniz, VM yedeklemesi kalıcı olarak silinmez. Bunun yerine, yedekleme verileri geçici olarak silinen durumunda 14 gün boyunca tutulacaktır. **Yedekleme verilerini sil** seçilirse, yapılandırılan e-posta kimliğine bir silme e-postası uyarısı gönderilir. Bu, kullanıcıya 14 gün boyunca yedekleme verileri için genişletilmiş saklama süresi kaldığını bildirir. Ayrıca, 12. güne bir e-posta uyarısı gönderilir ve bu, silinen verileri yeniden gösteren iki gün daha olduğunu bildirir. Silme işlemi 15. güne kadar ertelenir ve kalıcı silme gerçekleşir ve verilerin kalıcı olarak silinmesini bildiren son bir e-posta uyarısı gönderilir.

   ![Azure portal ekran görüntüsü, yedekleme ekranını durdur](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Bu 14 gün boyunca, kurtarma hizmetleri kasasında, geçici olarak silinen VM bunun yanında kırmızı bir "geçici silme" simgesiyle görüntülenir.

   ![Azure portal ekran görüntüsü, geçici silme durumunda VM](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Kasada geçici olarak silinen yedekleme öğeleri varsa, kasa bu anda silinemez. Lütfen yedekleme öğeleri kalıcı olarak silindikten sonra kasa silme işlemini deneyin ve kasada geçici olarak silinmiş durumda bir öğe yok.

4. Geçici olarak silinen VM 'yi geri yüklemek için önce silinmesi gerekir. Silmeyi geri almak için, geçici olarak silinen VM 'yi seçin ve sonra **geri al**seçeneğine tıklayın.

   ![Azure portal ekran görüntüsü, geri alma VM](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Silmeyi geri alma seçilirse bir pencere görünür ve geri yükleme işlemi gerçekleştirmek için sanal makinenin tüm geri yükleme noktalarının silinmesi ve kullanılabilir olduğunu gösterir. Yedeklemeler duraklatıldıktan sonra yedekleme verileri sürekli tutulur ve yedekleme ilkesi etkin olmadan yedeklenmez.

   ![Azure portal ekran görüntüsü, silmeyi geri almayı Onayla](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Bu noktada VM 'yi, seçilen geri yükleme noktasından **VM 'Yi geri yükle** ' yi seçerek de geri yükleyebilirsiniz.  

   ![Azure portal, VM 'yi geri yükle seçeneğinin ekran görüntüsü](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Çöp toplayıcı, yalnızca Kullanıcı **yedeklemeyi sürdürür** işlemini gerçekleştirdikten sonra, zaman aşımına uğradı kurtarma noktalarını çalıştırır ve temizler.

5. Silmeyi geri alma işlemi tamamlandıktan sonra, durum "verileri koruyacak şekilde Yedeklemeyi Durdur" ' a döner ve sonra **yedeklemeyi devam et**' i seçebilirsiniz. **Yedeklemeyi yeniden başlatma** işlemi, yedekleme ve bekletme zamanlamalarını tanımlayan Kullanıcı tarafından seçilen bir yedekleme ilkesiyle ilişkili olan yedekleme öğesini etkin duruma getirir.

   ![Azure portal ekran görüntüsü, yedeklemeyi sürdürür seçeneği](./media/backup-azure-security-feature-cloud/resume-backup.png)

Bu akış grafiği, bir yedekleme öğesinin farklı adımlarını ve durumlarını gösterir:

![Geçici olarak silinen yedekleme öğesinin yaşam döngüsü](./media/backup-azure-security-feature-cloud/lifecycle.png)

Daha fazla bilgi için aşağıdaki [sık sorulan sorular](backup-azure-security-feature-cloud.md#frequently-asked-questions) bölümüne bakın.

## <a name="disabling-soft-delete"></a>Geçici silme devre dışı bırakılıyor

Geçici silme, yeni oluşturulan kasaların varsayılan olarak etkindir. Geçici silme güvenliği özelliği devre dışıysa, yedekleme verileri yanlışlıkla veya kötü amaçlı silmelerden korunmaz. Geçici silme özelliği olmadan, korumalı öğelerin tüm silmeleri, geri yükleme özelliği olmadan hemen kaldırılmasına neden olur. "Geçici silme" durumundaki yedekleme verileri müşteri için herhangi bir ücret ödemediğinden bu özelliğin devre dışı bırakılması önerilmez. Geçici silme işleminin devre dışı bırakılmasını göz önünde bulundurmanız gereken tek durumlar, korumalı öğelerinizi yeni bir kasaya taşımayı planlıyorsanız ve silinmeden ve yeniden korumadan önce 14 gün önce bekleyemez (örneğin, bir test ortamında).

### <a name="prerequisites-for-disabling-soft-delete"></a>Geçici silme devre dışı bırakma önkoşulları

- Kasaların (korumalı öğeler olmadan) geçici silme özelliğinin etkinleştirilmesi veya devre dışı bırakılması yalnızca Azure portal yapılabilir. Bu için geçerlidir:
  - Korumalı öğeler içermeyen yeni oluşturulan kasaları
  - Korunan öğeleri silinen ve süresi geçmiş mevcut kasaların (Sabit 14 günlük bekletme döneminin ötesinde)
- Kasa için geçici silme özelliği devre dışıysa, yeniden etkinleştirebilirsiniz, ancak bu seçimi ters çeviremezsiniz ve kasa korumalı öğeler içeriyorsa yeniden devre dışı bırakabilirsiniz.
- Geçici olarak silinen durumunda korunan öğeleri veya öğeleri içeren kasaların geçici silme özelliğini devre dışı bırakabilirsiniz. Bunu yapmanız gerekirse, aşağıdaki adımları izleyin:
  - Tüm korumalı öğeler için silinen verilerin korumasını durdurun.
  - 14 günlük güvenlik saklama işleminin sona ermesini bekleyin.
  - Geçici silme devre dışı bırakın.

Geçici silme devre dışı bırakmak için önkoşulların karşılandığından emin olun ve ardından şu adımları izleyin:

1. Azure portal kasanıza gidin ve **ayarlar** -> **Özellikler**' e gidin.
2. Özellikler bölmesinde, **güvenlik ayarları** -> **Güncelleştir**' i seçin.
3. Güvenlik ayarları bölmesinde, geçici silme altında **devre dışı bırak**' ı seçin.

![Geçici silmeyi devre dışı bırak](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

## <a name="other-security-features"></a>Diğer güvenlik özellikleri

### <a name="storage-side-encryption"></a>Depolama tarafında şifreleme

Azure depolama, verilerinizi buluta kalıcı hale geldiğinde otomatik olarak şifreler. Şifreleme, verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur. Azure depolama 'daki veriler, 256 bit AES şifrelemesi kullanılarak şifrelenmiş ve şifresi çözülür, en güçlü blok şifrelemeleri kullanılabilir ve FIPS 140-2 uyumludur. Azure depolama şifrelemesi, Windows 'da BitLocker şifrelemeye benzer. Azure Backup verileri depolamadan önce otomatik olarak şifreler. Azure Storage, verileri almadan önce şifresini çözer.  

Azure 'da, Azure depolama ile kasa arasındaki yoldaki veriler HTTPS tarafından korunur. Bu veriler, Azure omurga ağında kalır.

Daha fazla bilgi için lütfen [bekleyen veriler Için Azure depolama şifrelemesi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)bölümüne bakın.

### <a name="vm-encryption"></a>VM şifrelemesi

Azure Backup hizmetini kullanarak Windows veya Linux Azure sanal makinelerini (VM) şifrelenmiş disklerle yedekleyebilir ve geri yükleyebilirsiniz. Yönergeler için lütfen [Azure Backup ile şifrelenmiş sanal makineleri yedekleme ve geri yükleme](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)bölümüne bakın.

### <a name="protection-of-azure-backup-recovery-points"></a>Azure Backup kurtarma noktalarının korunması

Kurtarma Hizmetleri kasaları tarafından kullanılan depolama hesapları yalıtılmış ve herhangi bir kötü amaçlı kullanıcı tarafından erişilemez. Erişime yalnızca geri yükleme gibi Azure Backup yönetim işlemleri aracılığıyla izin verilir. Bu yönetim işlemleri rol tabanlı Access Control (RBAC) aracılığıyla denetlenir.

Daha fazla bilgi için lütfen bkz. [Azure Backup kurtarma noktalarını yönetmek Için rol tabanlı Access Control kullanma](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="soft-delete"></a>Geçici silme

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Her kasada geçici silme özelliğini etkinleştirmem gerekir mi?

Hayır, tüm kurtarma hizmetleri kasaları için varsayılan olarak oluşturulur ve etkinleştirilir.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Silme işlemi tamamlandıktan sonra verileri geçici olarak silinen durumda tutulacak gün sayısını yapılandırabilir miyim?

Hayır, silme işleminden sonra 14 gün daha fazla bekletme için düzeltildi.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Bu ek 14 günlük bekletme için maliyeti ödemem gerekiyor mu?

Hayır, bu 14 günlük ek bekletme, geçici silme işlevselliğinin bir parçası olarak ücretsiz maliyetlidir.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Verilerim geçici silme durumunda olduğunda geri yükleme işlemi gerçekleştirebilir miyim?

Hayır, geri yüklemek için geçici olarak silinen kaynağı geri almanız gerekir. Silmeyi geri alma işlemi, kaynağı, zaman içinde herhangi bir noktaya geri yüklediğiniz **verileri koruyun durumuyla yeniden durdur** 'a geri gönderir. Çöp toplayıcı bu durumda duraklatılmaya devam eder.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Anlık görüntülerim kasadaki kurtarma noktalarım ile aynı yaşam döngüsünü izlesin mi?

Evet.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Zamanlanmış yedeklemeleri, geçici olarak silinen bir kaynak için yeniden nasıl tetikleyebilirim?

Geri alma işleminden sonra devam ederseniz kaynak yeniden korunur. Özgeçmişi işlemi, zamanlanan yedeklemeleri seçilen bekletme süresiyle tetiklemek için bir yedekleme ilkesini ilişkilendirir. Ayrıca, çöp toplayıcı, işlem tamamlandıktan hemen sonra çalışır. Bitiş tarihini aşan bir kurtarma noktasından geri yükleme gerçekleştirmek istiyorsanız, bu işlemi, sürdürülmesi işlemini tetiklemeden önce yapmanız önerilir.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Kasada geçici olarak silinen öğeler varsa kasamı silebilir miyim?

Kasadaki geçici olarak silinen durumda yedekleme öğeleri varsa kurtarma hizmetleri Kasası silinemez. Geçici olarak silinen öğeler, silme işleminin 14 gün sonra kalıcı olarak silinir. Kasayı yalnızca tüm geçici silinen öğeler temizlenmeden sonra silebilirsiniz.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Silme işleminden sonra 14 gün geçici silme süresinden önceki verileri silebilir miyim?

Hayır. Geçici olarak silinen öğeleri silmeye zorlenemez, bunlar 14 gün sonra otomatik olarak silinir. Bu güvenlik özelliği, yedeklenen verilerin yanlışlıkla veya kötü amaçlı silmelerden korunmasını sağlamak için etkinleştirilir.  VM üzerinde başka bir işlem gerçekleştirmeden önce 14 gün beklemeniz gerekir.  Geçici olarak silinen öğeler ücretlendirilmeyecektir.  14 gün içinde geçici silme için işaretlenen VM 'Leri yeni bir kasaya yeniden korumaya ihtiyacınız varsa, Microsoft destek 'e başvurun.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>PowerShell veya CLı 'de geçici silme işlemleri yapılabilir mi?

Hayır, PowerShell veya CLı desteği şu anda kullanılamıyor.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Azure VM 'lerinde SQL Server ve Azure VM 'lerinde SAP HANA gibi diğer bulut iş yükleri için de geçici silme destekleniyor mu?

Hayır. Şu anda geçici silme yalnızca Azure sanal makinelerinde desteklenir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Backup Için güvenlik denetimleri](backup-security-controls.md)hakkında bilgi edinin.
