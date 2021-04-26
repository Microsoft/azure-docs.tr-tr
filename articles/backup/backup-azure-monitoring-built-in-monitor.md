---
title: Korunan Azure Backup iş yüklerini izleme
description: Bu makalede, Azure portal kullanarak Azure Backup iş yükleri için izleme ve bildirim özellikleri hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 83ed5af00bb61d7a8929e710b52e60c33c0f479b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559222"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure Backup iş yüklerini izleme

Azure Backup, yedekleme gereksinimine ve altyapı topolojisine (Şirket içi vs Azure) göre birden çok yedekleme çözümü sağlar. Herhangi bir yedekleme kullanıcısı veya Yöneticisi, tüm çözümlerde neler olduğunu ve önemli senaryolarda bildirilmesi beklendiğini görmelidir. Bu makalede, Azure Backup hizmeti tarafından sunulan izleme ve bildirim özellikleri ayrıntılı olarak açıklanır.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Kurtarma Hizmetleri kasasındaki yedekleme öğeleri

Tüm yedekleme öğelerinizi bir kurtarma hizmetleri Kasası aracılığıyla izleyebilirsiniz. Kasadaki **yedekleme öğeleri** bölümüne gidildiğinde, kasala ilişkili her iş yükü türünün yedekleme öğelerinin sayısını sağlayan bir görünüm açılır. Herhangi bir satıra tıkladığınızda, belirtilen iş yükü türünün tüm yedekleme öğelerini, her bir öğe için son yedekleme durumu, kullanılabilir en son geri yükleme noktası ve benzeri bilgilerle birlikte listeleme ayrıntılı bir görünüm açılır.

![RS Kasası yedekleme öğeleri](media/backup-azure-monitoring-laworkspace/backup-items-view.png)

> [!NOTE]
> DPM kullanılarak Azure 'a yedeklenen öğeler için, listede DPM sunucusu kullanılarak korunan tüm veri kaynakları (hem disk hem de çevrimiçi) gösterilir. Yedekleme verileri tutulan veri kaynağı için koruma durdurulmuşsa, veri kaynağı portalda yine de listelenir. Kurtarma noktalarının diskte, çevrimiçi veya her ikisinde de mevcut olup olmadığını görmek için veri kaynağının ayrıntılarına gidebilirsiniz. Ayrıca, çevrimiçi korumanın durdurulduğu, ancak veriler korunduğu veri kaynakları, veriler tamamen silinene kadar, çevrimiçi kurtarma noktaları için faturalandırmaya devam eder.
>
> Yedekleme öğelerinin kurtarma hizmetleri Kasası portalında görünür olması için DPM sürümünün DPM 1807 (5.1.378.0) veya DPM 2019 (sürüm 10.19.58.0 veya üzeri) olması gerekir.

## <a name="backup-jobs-in-recovery-services-vault"></a>Kurtarma Hizmetleri kasasındaki yedekleme Işleri

Azure Backup, Azure Backup tarafından korunan iş yükleri için yerleşik izleme ve uyarı özellikleri sağlar. Kurtarma Hizmetleri Kasası ayarlarında, **izleme** bölümü yerleşik işler ve uyarılar sağlar.

![RS Kasası yerleşik izleme](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

İşler, yedekleme yapılandırması, yedekleme, geri yükleme, yedeği silme gibi işlemler gerçekleştirildiğinde oluşturulur.

Aşağıdaki Azure Backup çözümlerinden gerçekleştirilen işler aşağıda gösterilmiştir:

- Azure VM yedeklemesi
- Azure dosya yedeklemesi
- SQL ve SAP HANA gibi Azure iş yükü yedekleme
- Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı

System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) işleri gösterilmez.

> [!NOTE]
> Azure VM 'Leri içindeki SQL ve SAP HANA yedeklemeleri gibi Azure iş yükleri çok fazla sayıda yedekleme işine sahiptir. Örneğin, günlük yedeklemeleri her 15 dakikada bir çalıştırılabilir. Bu nedenle, bu tür VERITABANı iş yükleri için yalnızca Kullanıcı tarafından tetiklenen işlemler görüntülenir. Zamanlanan yedekleme işlemleri görüntülenmiyor.

## <a name="backup-alerts-in-recovery-services-vault"></a>Kurtarma Hizmetleri kasasındaki yedekleme uyarıları

> [!NOTE]
> Uyarıları kasaların tamamında görüntüleme işlemi şu anda yedekleme merkezi 'nde desteklenmiyor. Bu kasadaki uyarıları görüntülemek için tek bir kasaya gitmeniz gerekir.

Uyarılar, kullanıcıların ilgili eylemi yapabilmesi için bilgilendirildikleri senaryolara yöneliktir. **Yedekleme uyarıları** bölümü Azure Backup hizmeti tarafından oluşturulan uyarıları gösterir. Bu uyarılar hizmet tarafından tanımlanır ve Kullanıcı özel uyarı oluşturamaz.

### <a name="alert-scenarios"></a>Uyarı senaryoları

Aşağıdaki senaryolar, hizmet tarafından alertable senaryoları olarak tanımlanmıştır.

- Yedekleme/Geri Yükleme hataları
- Yedekleme, Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı için uyarılarla başarılı oldu
- Verileri sakla/silme ile korumayı durdur

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Aşağıdaki Azure Backup çözümlerinin uyarıları burada gösterilmektedir

- Azure VM yedeklemeleri
- Azure Dosya yedeklemeleri
- SQL, SAP HANA gibi Azure iş yükü yedeklemeleri
- Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı

> [!NOTE]
> System Center Data Protection Manager (SC-DPM) ve Microsoft Azure Backup sunucusu (MABS) uyarıları burada gösterilmez.

### <a name="consolidated-alerts"></a>Birleştirilmiş uyarılar

SQL ve SAP HANA gibi Azure iş yükü yedekleme çözümleri için, günlük yedeklemeleri çok sık oluşturulabilir (ilkeye göre 15 dakikada bir). Bu nedenle, günlük yedekleme hatalarının de çok sık olması olasıdır (15 dakikada bir). Bu senaryoda, her hata oluşumu için bir uyarı oluşturulduğunda Son Kullanıcı ortaya çıkar. Bu nedenle, ilk oluşum için bir uyarı gönderilir ve sonraki başarısızlıklar aynı ana neden nedeniyle varsa, daha fazla uyarı oluşturulmaz. İlk uyarı hata sayısıyla güncelleştirilir. Ancak, uyarı Kullanıcı tarafından devre dışı bırakılırsa, sonraki oluşum başka bir uyarı tetikler ve bu, bu oluşum için ilk uyarı olarak kabul edilir. Azure Backup SQL ve SAP HANA yedeklemeleri için uyarı birleştirme işlemini gerçekleştirir.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Uyarı oluşmayan özel durumlar

Bir hata üzerinde bir uyarı ortaya çıkarılmazsa birkaç özel durum vardır. Bunlar:

- Kullanıcı çalışan işi açıkça iptal etti
- Devam eden başka bir yedekleme işi olduğu için iş başarısız oldu (bir önceki işin tamamlanmasını beklemek zorunda olduğumuz bu yana burada hiçbir şey yapması gerekmez)
- Yedeklenen Azure VM artık mevcut olmadığından VM yedekleme işi başarısız oluyor
- [Birleştirilmiş uyarılar](#consolidated-alerts)

Yukarıdaki özel durumlar, bu işlemlerin sonucunun (öncelikli olarak kullanıcının tetiklendiği) Portal/PS/CLı istemcilerinde hemen gösterdiği olduğunu anlamak için tasarlanmıştır. Böylece Kullanıcı anında haberdar olur ve bir bildirime gerek kalmaz.

### <a name="alert-types"></a>Uyarı türleri

Uyarı önem derecesine bağlı olarak, uyarılar üç tür halinde tanımlanabilir:

- **Kritik**: prensibi, herhangi bir yedekleme veya Kurtarma hatası (zamanlanmış veya Kullanıcı tarafından tetiklenen), bir uyarının oluşturulmasına ve kritik bir uyarı olarak ve ayrıca yedekleme silme gibi bozucu işlemlere neden olarak gösterilmelidir.
- **Uyarı**: yedekleme işlemi başarılı olur, ancak birkaç uyarı varsa uyarı uyarıları olarak listelenir. Uyarı uyarıları Şu anda yalnızca Azure Backup aracı yedeklemeleri için kullanılabilir.
- **Bilgilendirici**: şu anda Azure Backup hizmeti tarafından bir bilgilendirme uyarısı üretilmez.

## <a name="notification-for-backup-alerts"></a>Yedekleme uyarıları için bildirim

> [!NOTE]
> Bildirimin yapılandırması yalnızca Azure portal aracılığıyla yapılabilir. PS/CLı/REST API/Azure Resource Manager şablonu desteği desteklenmez.

Bir uyarı ortaya çıktığında kullanıcılara bildirilir. Azure Backup, e-posta ile yerleşik bir bildirim mekanizması sağlar. Tek tek e-posta adreslerini veya bir uyarı oluşturulduğunda bildirilecek dağıtım listelerini belirtebilir. Ayrıca, her bir uyarı için bildirim almayı veya saatlik bir özette grupları seçip bildirim almayı seçebilirsiniz.

![RS Kasası yerleşik e-posta bildirimi](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Bildirim yapılandırıldığında, bir hoş geldiniz veya tanıtım e-postası alırsınız. Bu, bir uyarı ortaya çıktığında Azure Backup bu adreslere e-posta gönderebileceğinizi onaylar.<br>

Sıklık bir saatlik Özet olarak ayarlandıysa ve bir süre içinde bir uyarı harekete geçirilir ve çözümlenirse, yaklaşan saatlik Özet 'in bir parçası olmayacaktır.

> [!NOTE]
>
> - **Verileri silme ile korumayı durdur** gibi bir bozucu işlem gerçekleştirilirse, kurtarma hizmetleri Kasası için bildirimler yapılandırılmadığı halde bir uyarı oluşturulur ve abonelik sahiplerine, yöneticilerine ve ortak yöneticilere e-posta gönderilir.
> - Başarılı işlerin bildirimini yapılandırmak için [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)kullanın.

## <a name="inactivating-alerts"></a>Uyarıları etkinleştirme

Etkin bir uyarıyı devre dışı bırakmak/çözümlemek için, devre dışı bırakmak istediğiniz uyarıya karşılık gelen liste öğesini seçebilirsiniz. Bu, en üstteki **devre dışı bırak** düğmesi ile uyarı hakkında ayrıntılı bilgi görüntüleyen bir ekran açar. Bu düğme seçildiğinde, uyarının durumu **etkin değil** olarak değiştirilir. Ayrıca, bu uyarıya karşılık gelen liste öğesine sağ tıklayıp **devre dışı bırak**' ı seçerek bir uyarıyı devre dışı bırakabilirsiniz.

![RS Kasası uyarısı intivasyonu](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Azure Backup için Azure Izleyici uyarıları (Önizleme)

Azure Backup Ayrıca, Azure Izleyici aracılığıyla, kullanıcıların yedekleme dahil farklı Azure hizmetlerinde uyarı yönetimi için tutarlı bir deneyim sahibi olmasını sağlamak üzere uyarılar sağlar. Azure Izleyici uyarıları sayesinde, uyarıları e-posta, ıTSM, Web kancası, mantıksal uygulama gibi Azure Backup tarafından desteklenen herhangi bir bildirim kanalına yönlendirebilirsiniz.

Şu anda bu özellik PostgreSQL için Azure veritabanları sunucu, Azure Blobları ve Azure yönetilen diskler için kullanılabilir. Uyarılar aşağıdaki senaryolar için oluşturulur ve bir yedekleme kasasına gidip **Uyarılar** menü öğesine tıklanarak erişilebilir:

- Yedekleme verilerini sil
- Yedekleme hatası (yedekleme hatası için uyarı almak için, **EnableAzureBackupJobFailureAlertsToAzureMonitor** adlı afec bayrağını önizleme portalı aracılığıyla kaydetmeniz gerekir)
- Geri yükleme hatası (geri yükleme hatası için uyarı almak için, **EnableAzureBackupJobFailureAlertsToAzureMonitor** adlı afec bayrağını önizleme portalı aracılığıyla kaydetmeniz gerekir)

Azure Izleyici uyarıları hakkında daha fazla bilgi için bkz. [Azure 'da uyarılara genel bakış](../azure-monitor/alerts/alerts-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

[Azure Izleyici 'yi kullanarak Azure Backup iş yüklerini izleme](backup-azure-monitoring-use-azuremonitor.md)