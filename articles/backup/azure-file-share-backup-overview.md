---
title: Azure dosya paylaşma yedeklemesi hakkında
description: Azure dosya paylaşımlarını kurtarma hizmetleri kasasında nasıl yedekleyeceğinizi öğrenin
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c4f9dd816ace2c9aec8f48207fbce88acf34e24a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516824"
---
# <a name="about-azure-file-share-backup"></a>Azure dosya paylaşma yedeklemesi hakkında

Azure dosya paylaşma yedeklemesi, buluttaki verilerinizi koruyan ve şirket içi yedekleme çözümlerinde yer alan ek bakım üst kafalarını ortadan kaldıran yerel, bulut tabanlı bir yedekleme çözümüdür. Azure Backup hizmeti Azure Dosya Eşitleme ile sorunsuz bir şekilde tümleşir ve yedeklemelerinizin yanı sıra dosya paylaşımlarınızın verilerini merkezileştirmenizi sağlar. Bu basit, güvenilir ve güvenli çözüm, çeşitli basit adımlarda kurumsal dosya paylaşımlarınız için koruma yapılandırmanıza olanak sağlar. bu sayede, verilerinizi herhangi bir olağanüstü durum senaryosunda kurtarmanıza yönelik bir güvence vardır.

## <a name="key-benefits-of-azure-file-share-backup"></a>Azure dosya paylaşımının yedeğinin önemli avantajları

* **Sıfır altyapı**: dosya paylaşımlarınız için korumayı yapılandırmak için dağıtım gerekmez.
* **Özelleştirilmiş bekletme**: gereksinimlerinize göre günlük/haftalık/aylık bekletme ile yedeklemeler yapılandırabilirsiniz.
* **Yerleşik yönetim özellikleri**: yedeklemeleri zamanlayabilir ve veri ayıklama ek yükü olmadan istenen saklama süresini belirtebilirsiniz.
* **Anında geri yükleme**: Azure dosya paylaşma yedeklemesi dosya paylaşımının anlık görüntülerini kullanır, bu nedenle yalnızca anında geri yüklemek istediğiniz dosyaları seçebilirsiniz.
* **Uyarı ve raporlama**: yedekleme ve geri yükleme hatalarıyla ilgili uyarıları yapılandırabilir ve dosya paylaşımlarınız genelinde yedeklemeler hakkında öngörü almak için Azure Backup tarafından sunulan raporlama çözümünü kullanabilirsiniz.
* **Dosya paylaşımlarının yanlışlıkla silinmesine karşı koruma**: Azure Backup, depolama hesabı düzeyinde [geçici silme özelliğini](../storage/files/storage-files-prevent-file-share-deletion.md) 14 günlük bir bekletme dönemi ile sunar. Kötü amaçlı aktör dosya paylaşımından silse bile, dosya paylaşımının içerik ve kurtarma noktaları (anlık görüntüler) yapılandırılabilir bir bekletme süresi boyunca tutulur ve böylece kaynak içeriğin ve anlık görüntülerin başarılı ve tamamen kurtarılmasına izin verilmez.

## <a name="architecture"></a>Mimari

![Azure dosya paylaşma yedekleme mimarisi](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Yedekleme işlemi nasıl işler?

1. Azure dosya paylaşımları için yedeklemeyi yapılandırmanın ilk adımı bir kurtarma hizmetleri Kasası oluşturuyor. Kasa, farklı iş yükleri genelinde yapılandırılmış yedeklemelerin birleştirilmiş bir görünümünü sunar.

2. Bir kasa oluşturduktan sonra Azure Backup hizmeti, kasaya kayıtlı olabilecek depolama hesaplarını bulur. Korumak istediğiniz dosya paylaşımlarını barındıran depolama hesabını seçebilirsiniz.

3. Depolama hesabını seçtikten sonra Azure Backup hizmeti, depolama hesabında mevcut olan dosya paylaşımları kümesini listeler ve bunların adlarını yönetim katmanı kataloğunda depolar.

4. Daha sonra, yedekleme ilkesini (zamanlama ve bekletme) gereksinimlerinize göre yapılandırıp yedeklenecek dosya paylaşımlarını seçersiniz. Azure Backup hizmeti zamanlanmış yedeklemeler yapmak için denetim düzlemine zamanlamaları kaydeder.

5. Azure Backup Zamanlayıcı, belirtilen ilkeye göre yedeklemeleri zamanlanan saatte tetikler. Bu işin bir parçası olarak dosya paylaşımının anlık görüntüsü dosya paylaşma API 'SI kullanılarak oluşturulur. Meta veri deposunda yalnızca anlık görüntü URL 'SI depolanır.

    >[!NOTE]
    >Yedekleme hizmeti depolama hesabınızın parçası olan anlık görüntüleri oluşturup yönettiğinden ve yedeklemeler kasaya aktarılmadığından, dosya paylaşımının verileri yedekleme hizmetine aktarılmaz.

6. Azure dosya paylaşma içeriğini (tek tek dosyalar veya tam paylaşma) kaynak dosya paylaşımında bulunan anlık görüntülerden geri yükleyebilirsiniz. İşlem tetiklendikten sonra, anlık görüntü URL 'SI meta veri deposundan alınır ve veriler listelenir ve kaynak anlık görüntüsünden seçtiğiniz hedef dosya paylaşımında aktarılır.

7. Azure Dosya Eşitleme kullanıyorsanız, yedekleme hizmeti Azure Dosya Eşitleme hizmetine geri yüklenen dosyaların yollarını gösterir ve ardından bu dosyalarda bir arka plan değişikliği algılama işlemini tetikler. Değiştirilen tüm dosyalar sunucu uç noktasına eşitlenir. Bu işlem, Azure dosya paylaşımının özgün geri yükleme ile paralel olarak gerçekleşir.

8. Yedekleme ve geri yükleme işi izleme verileri Azure Backup Izleme hizmetine gönderilir. Bu, dosya paylaşımlarınız için bulut yedeklemelerini tek bir panoda izlemenize olanak sağlar. Ayrıca, yedekleme durumu etkileniyorsa uyarıları veya e-posta bildirimlerini de yapılandırabilirsiniz. E-postalar Azure e-posta hizmeti aracılığıyla gönderilir.

## <a name="backup-costs"></a>Yedekleme maliyetleri

Azure dosya paylaşma yedekleme çözümüyle ilişkili iki maliyet vardır:

1. **Anlık görüntü depolama maliyeti**: anlık görüntüler için tahakkuk eden depolama ücretleri, [burada](https://azure.microsoft.com/pricing/details/storage/files/) bahsedilen fiyatlandırma ayrıntılarına göre Azure dosyaları kullanımıyla birlikte faturalandırılır

2. **Korumalı örnek ücreti**: 1 Eylül 2020 ' den itibaren müşteriler [burada](https://azure.microsoft.com/pricing/details/backup/)bahsedilen fiyatlandırma ayrıntılarına göre korunan bir örnek ücreti ödeyecek. Korumalı örnek ücreti, bir depolama hesabındaki korunan dosya paylaşımlarının toplam boyutuna bağlıdır.

Azure dosya paylaşımlarını yedeklemeye yönelik ayrıntılı tahminler almak için ayrıntılı [Azure Backup fiyatlandırma tahminleyicisi](https://aka.ms/AzureBackupCostEstimates)'ni indirebilirsiniz.  

## <a name="next-steps"></a>Sonraki adımlar

* [Azure dosya paylaşımlarını nasıl yedekleyeceğinizi](backup-afs.md) öğrenin
* [Azure dosyalarını yedekleme hakkında soruların](backup-azure-files-faq.yml) yanıtlarını bulun
