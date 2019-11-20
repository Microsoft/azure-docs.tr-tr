---
title: Korunan Azure Backup iş yüklerini izleme
description: Bu makalede, Azure portal kullanarak Azure Backup iş yükleri için izleme ve bildirim özellikleri hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: ea5102a95a9bef17f25219e00dec4654bf7f06d6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172873"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure Backup iş yüklerini izleme

Azure Backup, yedekleme gereksinimine ve altyapı topolojisine (Şirket içi vs Azure) göre birden çok yedekleme çözümü sağlar. Herhangi bir yedekleme kullanıcısı veya Yöneticisi, tüm çözümlerde neler olduğunu ve önemli senaryolarda bildirilmesi bekleni görmelidir. Bu makalede, Azure Backup hizmeti tarafından sunulan izleme ve bildirim özellikleri ayrıntılı olarak açıklanır.

## <a name="backup-jobs-in-recovery-services-vault"></a>Kurtarma Hizmetleri kasasındaki yedekleme Işleri

Azure Backup, Azure Backup tarafından korunan iş yükleri için yerleşik izleme ve uyarı özellikleri sağlar. Kurtarma Hizmetleri Kasası ayarlarında, **izleme** bölümü yerleşik işler ve uyarılar sağlar.

![RS Kasası yerleşik izleme](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

İşler, yedekleme, yedekleme, geri yükleme, yedeklemeyi silme gibi işlemler yapıldığında oluşturulur.

Aşağıdaki Azure Backup çözümlerinden gerçekleştirilen işler aşağıda gösterilmiştir:

- Azure VM yedeklemesi
- Azure dosya yedeklemesi
- SQL gibi Azure iş yükü yedeklemesi
- Azure Backup aracısı (MAB)

System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) işleri gösterilmez.

> [!NOTE]
> Azure VM 'Leri içindeki SQL yedeklemeleri gibi Azure iş yükleri çok fazla sayıda yedekleme işine sahiptir. Örneğin, günlük yedeklemeleri her 15 dakikada bir çalıştırılabilir. Bu nedenle, bu tür VERITABANı iş yükleri için yalnızca Kullanıcı tarafından tetiklenen işlemler görüntülenir. Zamanlanan yedekleme işlemleri görüntülenmiyor.

## <a name="backup-alerts-in-recovery-services-vault"></a>Kurtarma Hizmetleri kasasındaki yedekleme uyarıları

Uyarılar, kullanıcıların ilgili eylemi yapabilmesi için bilgilendirildikleri senaryolara yöneliktir. **Yedekleme uyarıları** bölümü Azure Backup hizmeti tarafından oluşturulan uyarıları gösterir. Bu uyarılar hizmet tarafından tanımlanır ve Kullanıcı özel uyarı oluşturamaz.

### <a name="alert-scenarios"></a>Uyarı senaryoları

Aşağıdaki senaryolar, hizmet tarafından alertable senaryoları olarak tanımlanmıştır.

- Yedekleme/Geri Yükleme hataları
- Azure Backup Aracısı (MAB) için yedekleme uyarılarla başarılı oldu
- Verileri sakla/silme ile korumayı durdur

### <a name="exceptions-when-an-alert-is-not-raised"></a>Uyarı oluşmayan özel durumlar

Bir hata üzerinde bir uyarı çıkarılmayan bazı özel durumlar vardır; bunlar:

- Kullanıcı çalışan işi açıkça iptal etti
- Devam eden başka bir yedekleme işi olduğu için iş başarısız oldu (bir önceki işin tamamlanmasını beklemek zorunda olduğumuz bu yana burada hiçbir şey yapması gerekmez)
- Yedeklenen Azure VM artık mevcut olmadığından VM yedekleme işi başarısız oluyor

Yukarıdaki özel durumlar, bu işlemlerin sonucunun (öncelikli olarak kullanıcının tetiklendiği) Portal/PS/CLı istemcilerinde hemen gösterdiği olduğunu anlamak için tasarlanmıştır. Bu nedenle, Kullanıcı hemen haberdar edilir ve bir bildirime gerek kalmaz.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Aşağıdaki Azure Backup çözümlerinin uyarıları burada gösterilmektedir

- Azure VM yedeklemeleri
- Azure Dosya yedeklemeleri
- Azure iş yükü yedeklemeleri, örneğin SQL
- Azure Backup aracısı (MAB)

> [!NOTE]
> System Center Data Protection Manager (SC-DPM) ve Microsoft Azure Backup sunucusu (MABS) uyarıları burada gösterilmez.

### <a name="alert-types"></a>Uyarı türleri

Uyarı önem derecesine bağlı olarak, uyarılar üç tür halinde tanımlanabilir:

- **Kritik**: prensibi, herhangi bir yedekleme veya Kurtarma hatası (zamanlanmış veya Kullanıcı tarafından tetiklenen), bir uyarının oluşturulmasına ve kritik bir uyarı olarak ve ayrıca yedekleme silme gibi bozucu işlemlere neden olarak gösterilmelidir.
- **Uyarı**: yedekleme işlemi başarılı olur, ancak birkaç uyarı varsa uyarı uyarıları olarak listelenir.
- **Bilgilendirici**: bugün itibariyle, Azure Backup hizmeti tarafından bir bilgilendirme uyarısı oluşturulmaz.

## <a name="notification-for-backup-alerts"></a>Yedekleme uyarıları için bildirim

> [!NOTE]
> Bildirim yapılandırması yalnızca Azure Portal üzerinden yapılabilir. PS/CLı/REST API/Azure Resource Manager şablon desteği desteklenmez.

Bir uyarı ortaya çıktığında kullanıcılara bildirilir. Azure Backup, e-posta ile yerleşik bir bildirim mekanizması sağlar. Tek tek e-posta adreslerini veya bir uyarı oluşturulduğunda bildirilecek dağıtım listelerini belirtebilir. Ayrıca, her bir uyarı için bildirim almayı veya saatlik bir özette grupları seçip bildirim almayı seçebilirsiniz.

![RS Kasası yerleşik e-posta bildirimi](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

> [!NOTE]
> SQL yedeklemeleriyle ilgili uyarılar birleştirilir ve e-posta yalnızca ilk oluşum için gönderilir. Ancak, uyarı Kullanıcı tarafından devre dışı bırakılırsa, bir sonraki oluşum başka bir e-posta tetikleyecektir.

Bildirim yapılandırıldığında, bir hoş geldiniz veya tanıtım e-postası alırsınız. Bu, bir uyarı ortaya çıktığında Azure Backup bu adreslere e-posta gönderebileceğinizi onaylar.<br>

Sıklık bir saatlik Özet olarak ayarlandıysa ve bir süre içinde bir uyarı harekete geçirilir ve çözümlenirse, yaklaşan saatlik Özet 'in bir parçası olmayacaktır.

> [!NOTE]
>
> - **Verileri silme ile korumayı durdur** gibi bir bozucu işlem gerçekleştirilirse, kurtarma hizmeti Kasası için bildirimler yapılandırılmadığı halde bir uyarı oluşturulur ve abonelik sahiplerine, yöneticilerine ve ortak yöneticilerine bir e-posta gönderilir.
> - Başarılı işlerin bildirimini yapılandırmak için [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)kullanın.

## <a name="inactivating-alerts"></a>Uyarıları etkinleştirme

Etkin bir uyarıyı devre dışı bırakmak/çözümlemek için, devre dışı bırakmak istediğiniz uyarıya karşılık gelen liste öğesine tıklayabilirsiniz. Bu, en üstteki ' devre dışı ' düğmesi ile uyarı hakkında ayrıntılı bilgi görüntüleyen bir ekran açar. Bu düğmeye tıkladığınızda, uyarının durumu ' etkin değil ' olarak değişir. Ayrıca, bu uyarıya karşılık gelen liste öğesine sağ tıklayıp ' devre dışı bırak ' seçeneğini belirleyerek bir uyarıyı devre dışı bırakabilirsiniz.

![RS Kasası uyarısı intivasyonu](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Sonraki adımlar

[Azure Izleyici 'yi kullanarak Azure Backup iş yüklerini izleme](backup-azure-monitoring-use-azuremonitor.md)
