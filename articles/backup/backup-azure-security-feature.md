---
title: Karma yedeklemeleri koruyan güvenlik özellikleri
description: Yedeklemeleri daha güvenli hale getirmek için Azure Backup güvenlik özelliklerini kullanmayı öğrenin
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: c3c62f8ea7813c14fa6e19d825a5253de18f6639
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172690"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Azure Backup kullanan karma yedeklemeleri korumanıza yardımcı olacak güvenlik özellikleri

Kötü amaçlı yazılım, fidye yazılımı ve yetkisiz erişim gibi güvenlik sorunlarına karşı duyulan endişe gittikçe artıyor. Bu güvenlik sorunları, hem parasal anlamda hem de veriler açısından maliyetli olabilir. Bu tür saldırılara karşı koruma için Azure Backup artık karma yedeklemeleri korumaya yardımcı olacak güvenlik özellikleri sağlamaktadır. Bu makalede, Azure kurtarma hizmetleri Aracısı ve Azure Backup Sunucusu kullanarak bu özelliklerin nasıl etkinleştirileceği ve kullanılacağı ele alınmaktadır. Bu özellikler şunlardır:

- **Önleme**. Bir parolayı değiştirme gibi kritik bir işlem gerçekleştirildiğinde ek bir kimlik doğrulama katmanı eklenir. Bu doğrulama, bu tür işlemlerin yalnızca geçerli Azure kimlik bilgilerine sahip kullanıcılar tarafından gerçekleştirilmesini sağlamaktır.
- **Uyarı**verme. Yedekleme verilerini silme gibi kritik bir işlem gerçekleştirildiğinde abonelik yöneticisine bir e-posta bildirimi gönderilir. Bu e-posta, kullanıcının bu eylemler hakkında hızlı bir şekilde bildirilmesini sağlar.
- **Kurtarma**. Silinen yedekleme verileri, silme tarihinden itibaren ek 14 gün boyunca tutulur. Bu, belirli bir süre içindeki verilerin kurtarılabilmesini sağlar, bu nedenle bir saldırı gerçekleşse bile veri kaybı olmaz. Ayrıca, bozuk verilere karşı koruma sağlamak için daha fazla sayıda en düşük kurtarma noktası tutulur.

> [!NOTE]
> Hizmet olarak altyapı (IaaS) VM yedeklemesi kullanıyorsanız güvenlik özellikleri etkinleştirilmemelidir. Bu özellikler IaaS VM yedeklemesi için henüz kullanılamaz, bu yüzden etkinleştirmenin etkinleştirilmesi herhangi bir etkiye sahip olmayacaktır. Güvenlik özellikleri yalnızca kullanıyorsanız etkinleştirilmelidir: <br/>
>  * **Azure Backup Aracısı**. En düşük aracı sürümü 2.0.9052. Bu özellikleri etkinleştirdikten sonra kritik işlemleri gerçekleştirmek için bu aracı sürümüne yükseltmeniz gerekir. <br/>
>  * **Azure Backup sunucusu**. Azure Backup Sunucusu güncelleştirme 1 ile en düşük Azure Backup aracı sürümü 2.0.9052. <br/>
>  * **System Center Data Protection Manager**. Data Protection Manager 2012 R2 UR12 veya Data Protection Manager 2016 UR2 ile en düşük Azure Backup Agent sürümü 2.0.9052. <br/>


> [!NOTE]
> Bu özellikler yalnızca kurtarma hizmetleri Kasası için kullanılabilir. Yeni oluşturulan tüm kurtarma hizmetleri kasaları, varsayılan olarak etkinleştirilen bu özelliklere sahiptir. Mevcut kurtarma hizmetleri kasaları için, kullanıcılar aşağıdaki bölümde bahsedilen adımları kullanarak bu özellikleri etkinleştirir. Özellikler etkinleştirildikten sonra, tüm kurtarma hizmetleri aracı bilgisayarları, Azure Backup Sunucusu örnekleri ve kasaya kayıtlı Data Protection Manager sunucuları için geçerlidir. Bu ayarın etkinleştirilmesi tek seferlik bir işlemdir ve bu özellikleri etkinleştirdikten sonra devre dışı bırakılamaz.
>

## <a name="enable-security-features"></a>Güvenlik özelliklerini etkinleştir

Bir kurtarma hizmetleri Kasası oluşturuyorsanız, tüm güvenlik özelliklerini kullanabilirsiniz. Mevcut bir kasasıyla çalışıyorsanız, aşağıdaki adımları izleyerek güvenlik özelliklerini etkinleştirin:

1. Azure kimlik bilgilerinizi kullanarak Azure portal oturum açın.
2. **Araştır**' ı seçin ve **Kurtarma Hizmetleri**yazın.

    ![Azure portal tarayıcı seçeneğinin ekran görüntüsü](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Kurtarma hizmetleri kasalarının listesi görünür. Bu listeden bir kasa seçin. Seçilen kasa panosu açılır.
3. Kasanın **altında görünen**öğelerin listesinden **Özellikler**' e tıklayın.

    ![Kurtarma Hizmetleri Kasası seçeneklerinin ekran görüntüsü](./media/backup-azure-security-feature/vault-list-properties.png)
4. **Güvenlik ayarları**altında **Güncelleştir**' e tıklayın.

    ![Kurtarma Hizmetleri Kasası özelliklerinin ekran görüntüsü](./media/backup-azure-security-feature/security-settings-update.png)

    Güncelleştirme bağlantısı, özelliklerin bir özetini sunan ve bunları etkinleştirmenizi sağlayan **güvenlik ayarları** dikey penceresini açar.
5. Aşağı açılan listeden **azure Multi-Factor Authentication mı yapılandırdığınıza**göre, [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)etkinleştirilip etkinleştirilmediğini onaylamak için bir değer seçin. Etkinleştirilirse, Azure portal oturum açarken başka bir cihazdan (örneğin, cep telefonu) kimlik doğrulaması yapmanız istenir.

   Yedekleme sırasında kritik işlemler gerçekleştirdiğinizde, Azure portal kullanılabilir bir güvenlik PIN 'i girmeniz gerekir. Azure Multi-Factor Authentication etkinleştirilmesi bir güvenlik katmanı ekler. Yalnızca geçerli Azure kimlik bilgilerine sahip yetkili kullanıcılar ve ikinci bir cihazdan kimlik doğrulaması yapılabilir Azure portal.
6. Güvenlik ayarlarını kaydetmek için **Etkinleştir** ' i seçin ve **Kaydet**' e tıklayın. Yalnızca önceki adımda **Azure Multi-Factor Authentication mı yapılandırdığınız** bir değeri seçtikten sonra **Etkinleştir** ' i seçebilirsiniz.

    ![Güvenlik ayarlarının ekran görüntüsü](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Silinen yedekleme verilerini kurtar

Yedekleme, silinen yedekleme verilerini ek 14 gün boyunca tutar ve yedekleme **verilerini sil ile Yedeklemeyi Durdur** işlemi gerçekleştirildiğinde hemen silinmez. Bu verileri 14 günlük sürede geri yüklemek için, kullanmakta olduğunuz seçeneğe bağlı olarak aşağıdaki adımları uygulayın:

**Azure kurtarma hizmetleri Aracısı** kullanıcıları için:

1. Yedeklemelerin gerçekleştiği bilgisayar hala kullanılabiliyorsa, silinen veri kaynaklarını yeniden koruyun ve tüm eski kurtarma noktalarından kurtarmak için verileri Azure kurtarma hizmetleri 'nde [aynı makineye kurtar](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) ' ı kullanın.
2. Bu bilgisayar kullanılamıyorsa, bu verileri almak için başka bir Azure kurtarma hizmetleri bilgisayarı kullanmak üzere [alternatif bir makineye kurtar](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) ' ı kullanın.

**Azure Backup sunucusu** kullanıcılar için:

1. Yedeklemelerin gerçekleştiği sunucu hala kullanılabiliyorsa, silinen veri kaynaklarını yeniden koruyun ve tüm eski kurtarma noktalarından kurtarmak için **verileri kurtar** özelliğini kullanın.
2. Bu sunucu kullanılamıyorsa, bu verileri almak için başka bir Azure Backup Sunucusu örneğini kullanmak üzere [başka bir Azure Backup sunucusu verileri kurtar](backup-azure-alternate-dpm-server.md) seçeneğini kullanın.

**Data Protection Manager** kullanıcılar için:

1. Yedeklemelerin gerçekleştiği sunucu hala kullanılabiliyorsa, silinen veri kaynaklarını yeniden koruyun ve tüm eski kurtarma noktalarından kurtarmak için **verileri kurtar** özelliğini kullanın.
2. Bu sunucu kullanılamıyorsa, bu verileri almak için başka bir Data Protection Manager sunucusu kullanmak üzere [dış DPM Ekle](backup-azure-alternate-dpm-server.md) seçeneğini kullanın.

## <a name="prevent-attacks"></a>Saldırıları önleme

Yalnızca geçerli kullanıcıların çeşitli işlemler gerçekleştirebildiğine emin olmak için denetimler eklenmiştir. Bunlar, ek bir kimlik doğrulama katmanı eklemeyi ve kurtarma amacıyla en düşük saklama aralığını tutmayı içerir.

### <a name="authentication-to-perform-critical-operations"></a>Kritik işlemleri gerçekleştirmek için kimlik doğrulaması

Kritik işlemler için ek bir kimlik doğrulama katmanı eklemenin bir parçası olarak, verileri silme ve **parola değiştirme** Işlemleri **ile korumayı durdur** işlemini gerçekleştirdiğinizde bir güvenlik PIN kodu girmeniz istenir.

> [!NOTE]
>
> Şu anda, DPM ve MABS için **silme verileriyle** güvenlik PIN 'i durdurma desteklenmez.

Bu PIN 'ı almak için:

1. Azure Portal’da oturum açın.
2. **Kurtarma Hizmetleri kasası** > **ayarları** > **Özellikler**' e gidin.
3. **GÜVENLIK PIN**'ı altında **Oluştur**' a tıklayın. Bu, Azure kurtarma hizmetleri Aracısı kullanıcı arabirimine girilecek PIN 'ı içeren bir dikey pencere açar.
    Bu PIN yalnızca beş dakika için geçerlidir ve bu dönemden sonra otomatik olarak oluşturulur.

### <a name="maintain-a-minimum-retention-range"></a>En düşük saklama aralığını koruyun

Her zaman geçerli sayıda kurtarma noktası olduğundan emin olmak için aşağıdaki denetimler eklenmiştir:

- Günlük bekletme için en az **yedi** günlük bekletme yapılmalıdır.
- Haftalık bekletme için en az **dört** haftalık bekletme yapılmalıdır.
- Aylık bekletme için en az **üç** aylık bekletme yapılmalıdır.
- Yıllık bekletme için en az **bir** yıllık bekletme yapılmalıdır.

## <a name="notifications-for-critical-operations"></a>Kritik işlemler için bildirimler

Genellikle, kritik bir işlem gerçekleştirildiğinde abonelik yöneticisine, işlem hakkındaki ayrıntıları içeren bir e-posta bildirimi gönderilir. Azure portal kullanarak, bu bildirimler için ek e-posta alıcıları yapılandırabilirsiniz.

Bu makalede bahsedilen güvenlik özellikleri, hedeflenen saldırılara karşı savunma mekanizmaları sunmaktadır. Daha da önemlisi, bir saldırı olursa bu özellikler verilerinizi kurtarmanıza olanak sağlar.

## <a name="troubleshooting-errors"></a>Hatalarda sorun giderme

| İşlem | Hata Ayrıntıları | Çözüm |
| --- | --- | --- |
| İlke değişikliği |Yedekleme ilkesi değiştirilemedi. Hata: geçerli işlem, [0x29834] iç hizmet hatası nedeniyle başarısız oldu. Lütfen bir süre sonra işlemi yeniden deneyin. Sorun devam ederse, lütfen Microsoft desteğine başvurun. |**Sağlamak**<br/>Bu hata, güvenlik ayarları etkinleştirildiğinde, yukarıda belirtilen en düşük değerlerin altındaki bekletme aralığını azaltmaya ve desteklenmeyen bir sürümdeyse (Bu makalenin ilk bölümünde desteklenen sürümler belirtilir). <br/>**Önerilen eylem:**<br/> Bu durumda, ilkeyle ilgili güncelleştirmeler ile devam etmek için belirtilen en düşük saklama süresi (günlük için yedi gün, haftalık dört hafta, aylık veya yıllık bir yıl için üç hafta) üzerinde saklama süresi ayarlamanız gerekir. İsteğe bağlı olarak, tercih edilen yaklaşım, tüm güvenlik güncelleştirmelerinden yararlanmak için Azure Backup Sunucusu ve/veya DPM UR yedekleme aracısını güncelleştirmek olacaktır. |
| Parolayı Değiştir |Girilen güvenlik PIN 'ı hatalı. (KIMLIK: 100130) Bu işlemi gerçekleştirmek için doğru güvenlik PIN 'ini girin. |**Sağlamak**<br/> Bu hata, kritik işlem gerçekleştirirken (değiştirme parolası gibi) geçersiz veya süre dolma güvenlik PIN 'ı girdiğinizde gelir. <br/>**Önerilen eylem:**<br/> İşlemi gerçekleştirmek için geçerli bir güvenlik PIN 'ı girmeniz gerekir. PIN 'i almak için Azure portal oturum açın ve kurtarma hizmetleri Kasası > Ayarlar > Özellikler > güvenlik PIN 'ı oluştur ' a gidin. Parolayı değiştirmek için bu PIN 'ı kullanın. |
| Parolayı Değiştir |İşlem başarısız oldu. KIMLIK: 120002 |**Sağlamak**<br/>Bu hata, güvenlik ayarları etkinleştirildiğinde, parolayı değiştirmeye çalışırsınız ve desteklenmeyen bir sürümdür (Bu makalenin ilk notta belirtilen geçerli sürümler).<br/>**Önerilen eylem:**<br/> Parolayı değiştirmek için, önce yedekleme aracısını minimum en düşük sürüm olan 2.0.9052, Azure Backup sunucusu en düşük güncelleştirme 1 ve/veya DPM 2012 R2 UR12 veya DPM 2016 UR2 (aşağıdaki bağlantıları indir) olarak güncelleştirmeniz gerekir, ardından geçerli güvenlik PIN 'ini girin. PIN 'i almak için Azure portal oturum açın ve kurtarma hizmetleri Kasası > Ayarlar > Özellikler > güvenlik PIN 'ı oluştur ' a gidin. Parolayı değiştirmek için bu PIN 'ı kullanın. |

## <a name="next-steps"></a>Sonraki adımlar

- Bu özellikleri etkinleştirmek için [Azure kurtarma hizmetleri kasası ile çalışmaya](backup-azure-vms-first-look-arm.md) başlayın.
- Windows bilgisayarlarını korumaya yardımcı olmak ve yedekleme verilerinizi saldırılara karşı korumak için [en son Azure kurtarma hizmetleri aracısını indirin](https://aka.ms/azurebackup_agent) .
- İş yüklerini korumaya yardımcı olmak ve yedekleme verilerinizi saldırılara karşı korumak için [en son Azure Backup sunucusu indirin](https://aka.ms/latest_azurebackupserver) .
- [System center 2012 R2 Data Protection Manager IÇIN UR12 indirin](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) veya [System Center 2016 için UR2 indirin Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) iş yüklerini korumanıza ve yedekleme verilerinizi saldırılara karşı korumalarına yardımcı olur.
