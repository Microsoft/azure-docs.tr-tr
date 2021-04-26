---
title: Azure disk yedekleme 'ye Genel Bakış
description: Azure disk yedekleme çözümü hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 04/09/2021
ms.openlocfilehash: 42f37c1f500be719e0bd79bad41226ab3ab2d911
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285148"
---
# <a name="overview-of-azure-disk-backup"></a>Azure disk yedekleme 'ye Genel Bakış

Azure disk yedekleme, yönetilen disklerde verilerinizi koruyan yerel, bulut tabanlı bir yedekleme çözümüdür. Bu, birkaç adımda yönetilen diskler için korumayı yapılandırmanızı sağlayan basit, güvenli ve ekonomik bir çözümdür. Verilerinizi bir olağanüstü durum senaryosunda kurtarmanıza de sağlar.

Azure disk yedekleme, anlık görüntülerin düzenli olarak oluşturulmasını otomatikleştirerek yönetilen diskler için anlık görüntü yaşam döngüsü yönetimi sağlayan ve yedekleme ilkesini kullanarak yapılandırılan süre için bir anahtar, bir çözüm sunar. Disk anlık görüntülerini sıfır altyapı maliyetiyle ve özel betik oluşturmaya veya herhangi bir yönetim yüküne gerek kalmadan yönetebilirsiniz. Bu, günde birden çok yedekleme desteği olan [Artımlı anlık görüntüleri](../virtual-machines/disks-incremental-snapshots.md) kullanarak yönetilen bir diskin zaman içinde yedeğini alan çökme ile tutarlı bir yedekleme çözümüdür. Ayrıca, aynı zamanda aracılı bir çözümdür ve üretim uygulaması performansını etkilemez. Hem işletim sistemi hem de veri disklerinin (paylaşılan diskler dahil), çalışmakta olan bir Azure sanal makinesine bağlı olup olmadığına bakılmaksızın yedekleme ve geri yükleme desteği sağlar.

Veri diskleri dahil olmak üzere sanal makinenin uygulamayla tutarlı yedeklemesini veya bir sanal makinenin tamamını yedekten geri yükleme, bir dosya veya klasörü geri yükleme veya ikincil bir bölgeye geri yükleme seçeneği için [Azure VM yedekleme](backup-azure-vms-introduction.md) çözümünü kullanın. Azure Backup, [Azure VM yedekleme](./backup-azure-vms-introduction.md) çözümlerine ek olarak, disk yedekleme 'yi kullanarak yönetilen disklerin yedeklenmesi için yan yana destek sunar. Bu, sanal makinelerin bir gününde bir uygulamayla tutarlı yedeklemeler ve ayrıca işletim sistemi disklerinin veya kilitlenme tutarlılığı olan belirli bir veri diskinin daha sık yedeklemesi gerektiğinde ve üretim uygulaması performansını etkilemezseniz yararlıdır.

Azure disk yedekleme, kuruluşların her ölçekte yedeklemeleri yönetmek, izlemek, çalıştırmak ve analiz etmek için Azure 'da **tek bir birleştirilmiş yönetim deneyimi** sunan yedekleme merkezi ile tümleşiktir.

## <a name="key-benefits-of-disk-backup"></a>Disk yedeklemesinin önemli avantajları

Azure disk yedekleme, [Artımlı anlık görüntü](../virtual-machines/disks-incremental-snapshots.md) kullanan aracısız ve kilitlenmeyle tutarlı bir çözümdür ve aşağıdaki avantajları sunar:

- Sanal makineyi kesintiye uğramadan daha sık ve hızlı yedeklemeler.
- , Üretim uygulamasının performansını etkilemez.
- Özel betikler çalıştırmak veya aracıları yüklemek gerektirmediğinden dolayı güvenlik konusu yoktur.
- Tüm sanal makineleri yedeklemeye kıyasla belirli diskleri yedeklemeye yönelik ekonomik bir çözüm.

Azure disk yedekleme çözümü aşağıdaki senaryolarda kullanışlıdır:

- Uygulama quiescent olmadan gün başına sık sık yedeklemeler olması gerekir.
- Bir küme senaryosunda çalışan uygulamalar: hem Windows Server yük devretme kümesi hem de Linux kümeleri paylaşılan disklere yazıyor.
- Uygulama üzerinde güvenlik veya performans sorunları nedeniyle aracısız yedekleme için özel gereksinim.
- İş kolu uygulamaları Birim Gölge Kopyası Hizmeti (VSS) desteklemediğinden, sanal makinenin uygulamayla tutarlı yedeklemesi uygulanabilir değildir.

Şu durumlarda Azure disk yedeklemesi 'ni göz önünde bulundurun:

- Görev açısından kritik bir uygulama, kurtarma noktası hedefini karşılamak için günde birden çok yedekleme talep eden, ancak üretim ortamını veya uygulama performansını etkilemeden bir Azure sanal makinesinde çalışmaktadır.
- Kuruluşunuz veya sektör Yönetmeliği, güvenlik sorunları nedeniyle aracıları yüklemeyi kısıtlıyor.
- Uygulamayla tutarlı yedekleme sağlamak için özel ön ve son betik yürütme ve Linux sanal makinelerinde dondurma ve çözme işlemini çağırma, üretim iş yükü kullanılabilirliği üzerinde vadesi dolan yükü ortadan kaldırır.
- Azure Kubernetes Service (AKS kümesi) üzerinde çalışan Kapsayıcılı uygulamalar, kalıcı depolama olarak yönetilen diskleri kullanıyor. Günümüzde, yönetilen diski, yönetilmesi zor olan otomasyon betikleri aracılığıyla yedeklemeniz gerekir.
- Yönetilen bir disk, dosya paylaşma olarak kullanılan veya veritabanı yedekleme dosyalarını içeren önemli iş verilerini barındırır ve Azure VM yedeklemesine yatırım yaparak yedekleme maliyetini iyileştirmek istiyorsunuz.
- Web sunucusu, eyalet-daha az makineler barındıran veya uygulama yapılandırma ayarları ile hazırlama ortamı olarak hizmet veren çok sayıda Linux ve Windows tek disk sanal makineniz (yani yalnızca bir işletim sistemi diski olan bir sanal makine veya bağlı veri diski) vardır ve işletim sistemi diskini korumak için uygun maliyetli bir yedekleme çözümüne ihtiyacınız vardır. Örneğin, sanal makineyi yükseltmeden veya düzeltme eki uygulamadan önce hızlı bir isteğe bağlı yedeklemeyi tetiklemeniz gerekir.
- Bir sanal makine, Azure VM yedekleme çözümü tarafından desteklenmeyen bir işletim sistemi yapılandırması çalıştırıyor (örneğin, Windows 2008 32-bit sunucu).

## <a name="how-the-backup-and-restore-process-works"></a>Yedekleme ve geri yükleme işleminin nasıl çalıştığı

- Azure yönetilen diskler için yedeklemeyi yapılandırmanın ilk adımı bir [Yedekleme Kasası](backup-vault-overview.md)oluşturuyor. Kasa, farklı iş yükleri genelinde yapılandırılmış yedeklemelerin birleştirilmiş bir görünümünü sunar.

- Ardından yedekleme sıklığını ve saklama süresini yapılandırmanıza olanak tanıyan bir yedekleme ilkesi oluşturun.

- Yedeklemeyi yapılandırmak için yedekleme kasasına gidin, bir yedekleme ilkesi atayın, yedeklenmesi gereken yönetilen diski seçin ve anlık görüntülerin depolanacağı ve yönetildiği bir kaynak grubu sağlayın. Azure Backup, yedekleme sıklığına göre diskin artımlı bir anlık görüntüsünü oluşturan zamanlanmış yedekleme işlerini otomatik olarak tetikler. Daha eski anlık görüntüler, yedekleme ilkesi tarafından belirtilen saklama süresine göre silinir.

- Azure Backup yönetilen diskin [Artımlı anlık görüntülerini](../virtual-machines/disks-incremental-snapshots.md#restrictions) kullanır. Artımlı anlık görüntüler, son anlık görüntüden itibaren diskte delta değişiklikleri için faturalandırılan yönetilen disklerin uygun maliyetli, zaman içinde bir yedeklemesinden oluşur. Bunlar her zaman, ana disklerin depolama türü ne olursa olsun standart HDD depolamada en düşük maliyetli depolama alanı üzerinde depolanır. Diskin ilk anlık görüntüsü, diskin kullanılan boyutunu kaplayacaktır ve son anlık görüntüden bu yana arka arkaya Artımlı anlık görüntüler depolama Delta değişikliklerini diskte saklar.

- Yönetilen bir diskin yedeklemesini yapılandırdıktan sonra, yedekleme kasasında bir yedekleme örneği oluşturulur. Yedekleme örneğini kullanarak, yedekleme işlemlerinin sistem durumunu bulabilir, isteğe bağlı yedeklemeleri tetikleyebilir ve geri yükleme işlemleri yapabilirsiniz. Ayrıca, tek bir cam görünümü bölmesi sağlayan yedekleme merkezi 'ni kullanarak birden çok kasada ve yedekleme örneklerinde yedeklemelerin sistem durumunu görüntüleyebilirsiniz.

- Geri yüklemek için, diski geri yüklemek istediğiniz kurtarma noktasını seçmeniz yeterlidir. Geri yüklenen diskin anlık görüntüden oluşturulacağı kaynak grubunu belirtin. Azure Backup, anlık görüntüler aboneliğinizde yerel olarak depolandığından bir anında geri yükleme deneyimi sağlar.

- Yedekleme Kasası, diğer Azure kaynaklarına erişmek için yönetilen kimlik kullanır. Yönetilen bir diskin yedeklemesini yapılandırmak ve geçmiş yedeklemeden geri yüklemek için, yedekleme kasasının yönetilen kimliği, kaynak diskte bir izin kümesi, anlık görüntülerin oluşturulduğu ve yönetildiği anlık görüntü kaynak grubu ve yedeklemeyi geri yüklemek istediğiniz hedef kaynak grubu gerektirir. Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak yönetilen kimliğe izin verebilirsiniz. Yönetilen kimlik, yalnızca Azure kaynaklarıyla kullanılabilecek özel bir türün hizmet sorumlusundan oluşur. [Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md)hakkında daha fazla bilgi edinin.

- Şu anda Azure disk yedekleme, yönetilen disklerin işletimsel yedeklemesini destekler ve yedeklemeleri Yedekleme Kasası depolamasına kopyalamaz. Desteklenen ve desteklenmeyen senaryoların ve bölge kullanılabilirliğinin ayrıntılı bir listesi için [destek matrisine](disk-backup-support-matrix.md) bakın.

## <a name="pricing"></a>Fiyatlandırma

Azure Backup, Azure disklerini korumak için bir anlık görüntü yaşam döngüsü yönetimi çözümü sunar. Azure Backup tarafından oluşturulan disk anlık görüntüleri, Azure aboneliğinizdeki kaynak grubunda depolanır ve **anlık görüntü depolama** ücretleri uygulanır. Anlık görüntü fiyatlandırması hakkında daha fazla bilgi için [yönetilen disk fiyatlandırmasını](https://azure.microsoft.com/pricing/details/managed-disks/) ziyaret edebilirsiniz.<br></br>Anlık görüntüler yedekleme kasasına kopyalanmadığından, Azure Backup **korunan bir örnek** ücreti ödemez ve **yedekleme depolama** maliyeti uygulanmaz. Ayrıca, artımlı anlık görüntüler, Delta değişikliklerini son anlık görüntü olarak kaplar ve ana yönetilen disklerin depolama türünden bağımsız olarak her zaman standart depolamada depolanır ve standart depolama fiyatlandırmasına göre ücretlendirilir. Bu, Azure disk yedeklemesini uygun maliyetli bir çözüm yapar.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Disk Yedekleme destek matrisi](disk-backup-support-matrix.md)
