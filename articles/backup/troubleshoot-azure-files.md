---
title: Azure Dosya Paylaşımlarını Yedekleme sorunlarını giderme
description: Bu makalede, Azure dosya paylaşımlarınızın korunması sırasında oluşan sorunlarla ilgili sorun giderme bilgileri verilmektedir.
ms.date: 08/20/2019
ms.topic: conceptual
ms.openlocfilehash: 62a4f83c93230c150a7c406d0614dbee3d125e9c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171759"
---
# <a name="troubleshoot-problems-backing-up-azure-file-shares"></a>Azure Dosya Paylaşımlarını yedekleme sorunlarını giderme

Aşağıdaki tablolarda listelenen bilgilerle Azure Dosya Paylaşımları yedeklemesi kullanılırken karşılaşılan sorunları ve hataları giderebilirsiniz.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Önizleme sırasında Azure dosya paylaşımı yedeklemesine yönelik sınırlamalar

Azure Dosya paylaşımları için yedekleme, Önizleme aşamasındadır. Hem genel amaçlı v1 hem de genel amaçlı v2 depolama hesaplarında Azure dosya paylaşımları desteklenir. Aşağıdaki yedekleme senaryoları, Azure dosya paylaşımları için desteklenmemektedir:

- Azure Backup kullanarak Azure dosyalarını korumak için kullanılabilir CLı yoktur.
- Günlük zamanlanan maksimum yedekleme sayısı birdir.
- Günlük zamanlanan maksimum istek üzerine yedekleme sayısı dörttür.
- Kurtarma Hizmetleri kasanızdaki yedeklemelerin yanlışlıkla silinmesini önlemek için depolama hesabındaki [kaynak kilitlerini](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) kullanın.
- Azure Backup tarafından oluşturulan anlık görüntülerin silmeyin. Anlık görüntülerin silinmesi, kurtarma noktalarının kaybolması ve/veya geri yükleme işlemlerinin başarısız olmasıyla sonuçlanabilir
- Azure Backup tarafından korunan dosya paylaşımlarını silmeyin. Geçerli çözüm, dosya paylaşımının silindiği ve bu nedenle tüm geri yükleme noktalarını kaybetmesine Azure Backup tarafından alınan tüm anlık görüntüleri siler

[Bölge yedekli depolama](../storage/common/storage-redundancy-zrs.md) (ZRS) çoğaltması Ile depolama hesaplarında Azure dosya paylaşımları için yedekleme şu anda yalnızca Orta ABD (cu düzeyinde kapsanır), Doğu ABD (EUS), Doğu ABD 2 (EUS2), Kuzey Avrupa (ne), Güneydoğu Asya (SEA), Batı Avrupa (we) ve Batı ABD 2 (WUS2) ' de kullanılabilir.

## <a name="configuring-backup"></a>Yedeklemeyi yapılandırma

Aşağıdaki tablo, yedeklemenin yapılandırılmasına yöneliktir:

| Hata iletileri | Geçici çözüm veya çözümleme ipuçları |
| ------------------ | ----------------------------- |
| Azure dosya paylaşımına yönelik yedeklemeyi yapılandırmak için Depolama Hesabımı bulamıyorum | <ul><li>Bulma işlemi tamamlanana kadar bekleyin. <li>Depolama hesabından herhangi bir Dosya paylaşımının zaten başka bir Kurtarma Hizmetleri kasası ile korunup korunmadığını denetleyin. **Not**: Bir Depolama Hesabındaki tüm dosya paylaşımları tek bir Kurtarma Hizmetleri kasası kapsamında korunabilir. <li>Desteklenmeyen herhangi bir Depolama Hesabında Dosya paylaşımının mevcut olmadığından emin olun.<li> Depolama hesabında **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver** onay kutusunun işaretli olduğundan emin olun. [Daha fazla bilgi edinin.](../storage/common/storage-network-security.md)|
| Portal durumlarında hata oluştu. Depolama hesaplarını bulma işlemi başarısız oldu. | Aboneliğiniz iş ortağı hesabıysa (CSP etkin) hatayı yoksayın. Aboneliğinizde CSP etkin değilse ve depolama hesaplarınız bulunamıyorsa desteğe başvurun.|
| Seçilen Depolama Hesabı doğrulaması veya kaydı başarısız oldu.| İşlemi yeniden deneyin, sorun devam ederse desteğe başvurun.|
| Seçilen Depolama Hesabında Dosya paylaşımları listelenemedi veya bulunamadı. | <ul><li> Kaynak Grubunda Depolama Hesabının mevcut olduğundan (ve silinmediğinden veya kasadaki son doğrulamadan/kayıttan sonra taşınmadığından) emin olun.<li>Korumak istediğiniz Dosya paylaşımının silinmemiş olduğundan emin olun. <li>Depolama Hesabının, Dosya paylaşımı yedeklemesi için desteklenen bir depolama hesabı olduğundan emin olun.<li>Dosya paylaşımının, aynı Kurtarma Hizmetleri kasasında zaten korumalı olup olmadığını denetleyin.|
| Yedekleme Dosyası paylaşım yapılandırması (veya koruma ilkesi yapılandırması) başarısız oluyor. | <ul><li>Sorunun devam edip etmediğini görmek için işlemi yeniden deneyin. <li> Korumak istediğiniz Dosya paylaşımının silinmemiş olduğundan emin olun. <li> Aynı anda birden çok Dosya paylaşımını korumaya çalışıyorsanız ve dosya paylaşımlarının bir kısmı başarısız oluyorsa başarısız Dosya paylaşımları için yedeklemeyi yapılandırmayı tekrar deneyin. |
| Bir Dosya paylaşımının koruması kaldırıldıktan sonra Kurtarma Hizmetleri kasası silinemiyor. | Azure portalında, Kasa > **Yedekleme Altyapısı** > **Depolama hesapları**’nı açın ve Kurtarma Hizmetleri kasasından depolama hesabını kaldırmak için **Kaydı Kaldır**’a tıklayın.|

## <a name="error-messages-for-backup-or-restore-job-failures"></a>Yedekleme veya geri yükleme işi hataları için hata iletileri

| Hata iletileri | Geçici çözüm veya çözümleme ipuçları |
| -------------- | ----------------------------- |
| Dosya paylaşımı bulunamadığından işlem başarısız oldu. | Korumak istediğiniz Dosya paylaşımının silinmemiş olduğundan emin olun.|
| Depolama hesabı bulunamadı veya desteklenmiyor. | <ul><li>Depolama hesabının Kaynak Grubunda mevcut olduğundan ve silinmediğinden veya son doğrulamadan sonra Kaynak Grubundan kaldırılmadığından emin olun. <li> Depolama hesabının, Dosya paylaşımı yedeklemesi için desteklenen bir Depolama hesabı olduğundan emin olun.|
| Bu dosya paylaşımı için anlık görüntü sayısı üst sınırına ulaştınız, eski görüntülerin süresi dolduktan sonra yenilerini almanız mümkün olacaktır. | <ul><li> Bu hata, bir Dosya için birden fazla isteğe bağlı yedekleme oluşturduğunuzda oluşabilir. <li> Azure Backup tarafından alınanlar da dahil olmak üzere, Dosya paylaşımı başına 200 anlık görüntü sınırı söz konusudur. Zamanlanmış olan daha eski yedeklemeler (veya anlık görüntüler) otomatik olarak temizlenir. Üst sınıra ulaşılırsa isteğe bağlı yedeklemelerin (veya anlık görüntülerin) silinmesi gerekir.<li> Azure Dosyaları portalından isteğe bağlı yedeklemeleri (Azure dosya paylaşımı anlık görüntülerini) silin. **Not**: Azure Backup tarafından oluşturulan anlık görüntüleri silerseniz kurtarma noktalarını kaybedersiniz. |
| Depolama hizmeti azaltması nedeniyle Dosya paylaşımını yedekleme veya geri yükleme başarısız oldu. Bunun sebebi, depolama hizmetinin, söz konusu depolama hesabına yönelik diğer istekleri işlemekle meşgul olmasından kaynaklanıyor olabilir.| Bir süre sonra işlemi yeniden deneyin. |
| Hedef Dosya Paylaşımı Bulunamadı hatası ile geri yükleme başarısız oldu. | <ul><li>Seçilen Depolama Hesabının mevcut olduğundan ve Hedef Dosya paylaşımının silinmediğinden emin olun. <li> Depolama Hesabının, Dosya paylaşımı yedeklemesi için desteklenen bir depolama hesabı olduğundan emin olun. |
| Depolama hesabının Kilitli durumda olması nedeniyle Yedekleme veya Geri Yükleme işleri başarısız oldu. | Depolama Hesabı üzerindeki kilidi kaldırın veya okuma kilidi yerine silme kilidini kullanarak işlemi yeniden deneyin. |
| Başarısız dosyaların sayısı, eşik değerinden daha fazla olduğu için kurtarma başarısız oldu. | <ul><li> Kurtarma hatası nedenleri bir dosyada listelenmektedir. (Yol, İş ayrıntılarında belirtilir.) Lütfen hataları giderin ve yalnızca başarısız dosyalar için geri yükleme işlemini yeniden deneyin. <li> Dosya geri yükleme hatalarının sık karşılaşılan nedenleri şunlardır: <br/> - Başarısız dosyaların o sırada kullanımda olması, <br/> - Üst dizinde başarısız dosyayla aynı ada sahip bir dizinin mevcut olması. |
| Hiçbir dosya kurtarılamadığı için kurtarma başarısız oldu. | <ul><li> Kurtarma hatası nedenleri bir dosyada listelenmektedir. (Yol, İş ayrıntılarında belirtilir.) Hataları giderin ve yalnızca başarısız dosyalar için geri yükleme işlemlerini yeniden deneyin. <li> Dosya geri yükleme hatasının sık karşılaşılan nedenleri şunlardır: <br/> - Başarısız dosyaların o sırada kullanımda olması. <br/> - Üst dizinde başarısız dosyayla aynı ada sahip bir dizinin mevcut olması. |
| Kaynaktaki dosyalardan biri mevcut olmadığından geri yüklemenin başarısız olmaktadır. | <ul><li> Seçilen öğeler kurtarma noktası verilerinde mevcut değildir. Dosyaları kurtarmak için doğru dosya listesini sağlayın. <li> Kurtarma noktasına karşılık gelen dosya paylaşımı anlık görüntüsü el ile silindi. Farklı bir kurtarma noktası seçin ve geri yükleme işlemini yeniden deneyin. |
| Aynı hedefe yönelik olarak devam eden bir Kurtarma işi mevcuttur. | <ul><li>Dosya paylaşımı yedeklemesi, aynı hedef Dosya paylaşımına yönelik paralel kurtarmayı desteklememektedir. <li>Mevcut kurtarma işleminin tamamlanmasını bekleyip tekrar deneyin. Kurtarma Hizmetleri kasasında bir kurtarma işi bulamazsanız aynı abonelikte yer alan diğer Kurtarma Hizmetleri kasalarını denetleyin. |
| Hedef dosya paylaşımı dolu olduğundan geri yükleme işlemi başarısız olmuştur. | Hedef dosya paylaşımı için boyut kotasını geri yükleme verilerine uyacak şekilde artırın ve işlemi yeniden deneyin. |
| Hedef dosya paylaşımı ile ilişkili Dosya Eşitleme Hizmeti kaynaklarında geri yükleme öncesi işlemler gerçekleştirilirken bir hata oluştuğundan geri yükleme işlemi başarısız oldu. | Lütfen daha sonra yeniden deneyin, sorun devam ederse lütfen Microsoft desteğine başvurun. |
| Bir veya daha fazla dosyayı kurtarma başarısız oldu. Daha fazla bilgi için, yukarıda verilen yoldaki başarısız dosya listesini kontrol edin. | <ul> <li> Kurtarma hatasının nedenleri dosyada listelenmektedir (yol, İş ayrıntılarında belirtilir), nedenleri giderin ve yalnızca başarısız dosyalar için geri yükleme işlemini yeniden deneyin. <li> Dosya geri yükleme hatalarının sık karşılaşılan nedenleri şunlardır: <br/> - Başarısız dosyaların o sırada kullanımda olması. <br/> - Üst dizinde başarısız dosyalarla aynı ada sahip bir dizinin mevcut olması. |

## <a name="modify-policy"></a>İlkeyi Değiştir

| Hata iletileri | Geçici çözüm veya çözümleme ipuçları |
| ------------------ | ----------------------------- |
| Bu öğe için başka bir yapılandırma koruması işlemi devam ediyor. | Lütfen önceki değiştirme ilkesi işleminin bitmesini bekleyin ve bir süre sonra yeniden deneyin.|
| Seçilen öğede başka bir işlem devam ediyor. | Lütfen devam eden diğer işlemin tamamlanmasını bekleyin ve bir süre sonra yeniden deneyin |

## <a name="next-steps"></a>Sonraki adımlar

Azure dosya paylaşımlarını yedekleme hakkında daha fazla bilgi için bkz.:

- [Azure dosya paylaşımlarını yedekleme](backup-azure-files.md)
- [Azure dosya paylaşımlarını yedekleme ile ilgili SSS](backup-azure-files-faq.md)
