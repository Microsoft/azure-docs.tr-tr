---
title: Azure dosya paylaşma yedeklemesi sorunlarını giderme
description: Bu makalede, Azure dosya paylaşımlarınızın korunması sırasında oluşan sorunlarla ilgili sorun giderme bilgileri verilmektedir.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4c934d2295fa702425e8df0a03636b9f9208cfa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515082"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Azure dosya paylaşımlarını yedeklerken sorunları giderme

Bu makalede, Azure Backup hizmetini kullanarak yedeklemeyi yapılandırırken veya Azure dosya paylaşımlarını geri yüklerken, içinde aldığınız herhangi bir sorunu gidermek için sorun giderme bilgileri sağlanmaktadır.

## <a name="common-configuration-issues"></a>Yaygın yapılandırma sorunları

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Azure dosya paylaşımının yedeklemesini yapılandırmak için depolama Hesabım bulunamadı

- Bulma işlemi tamamlanana kadar bekleyin.
- Depolama hesabı altındaki herhangi bir dosya paylaşımının zaten başka bir kurtarma hizmetleri kasasıyla korunuyor olup olmadığını denetleyin.

  >[!NOTE]
  >Bir depolama hesabındaki tüm dosya paylaşımları yalnızca bir kurtarma hizmetleri Kasası altında korunabilir. Depolama hesabınızın kaydedildiği kurtarma hizmetleri kasasını bulmak için [bu betiği](scripts/backup-powershell-script-find-recovery-services-vault.md) kullanabilirsiniz.

- Dosya paylaşımının desteklenmeyen depolama hesaplarında bulunmadığından emin olun. Desteklenen depolama hesaplarını bulmak için [Azure dosya paylaşma yedeklemesi Için destek matrisine](azure-file-share-support-matrix.md) başvurabilirsiniz.
- Depolama hesabı adı ve kaynak grubu adının Birleşik uzunluğunun, yeni depolama hesapları ve klasik depolama hesaplarında 77 karakter olması durumunda 84 karakteri aşmadığından emin olun.
- Güvenilen Microsoft hizmetlerinin depolama hesabına erişmesine izin verme seçeneğinin etkin olduğundan emin olmak için depolama hesabının güvenlik duvarı ayarlarını kontrol edin.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Portal durumlarında hata oluştu, depolama hesaplarını bulma işlemi başarısız oldu

Bir iş ortağı aboneliğiniz varsa (CSP etkin), hatayı yoksayın. Aboneliğiniz CSP etkinleştirilmemişse ve depolama hesaplarınız bulunamadıysa, desteğe başvurun.

### <a name="selected-storage-account-validation-or-registration-failed"></a>Seçilen depolama hesabı doğrulaması veya kaydı başarısız oldu

Kaydı yeniden deneyin. Sorun devam ederse desteğe başvurun.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Seçili depolama hesabında dosya paylaşımları listelenemedi veya bulunamadı

- Depolama hesabının kaynak grubunda mevcut olduğundan ve silinmemiş ya da kasadaki son doğrulamadan veya kayıttan sonra taşınmadığından emin olun.
- Korumak istediğiniz dosya paylaşımının silinmediğinden emin olun.
- Depolama hesabının dosya paylaşma yedeklemesi için desteklenen bir depolama hesabı olduğundan emin olun. Desteklenen depolama hesaplarını bulmak için [Azure dosya paylaşma yedeklemesi Için destek matrisine](azure-file-share-support-matrix.md) başvurabilirsiniz.
- Dosya paylaşımının aynı kurtarma hizmetleri kasasında zaten korumalı olup olmadığını denetleyin.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>Yedekleme dosya paylaşma yapılandırması (veya koruma ilkesi yapılandırması) başarısız oluyor

- Sorunun devam edip etmediğini görmek için yapılandırmayı yeniden deneyin.
- Korumak istediğiniz dosya paylaşımının silinmediğinden emin olun.
- Aynı anda birden çok dosya paylaşımını korumaya çalışıyorsanız ve dosya paylaşımlarının bazıları başarısız olursa, başarısız dosya paylaşımları için yedeklemeyi yapılandırmayı yeniden deneyin.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Dosya paylaşımının korumasını kaldırdıktan sonra kurtarma hizmetleri Kasası silinemiyor

Azure Portal, **kasa**  >  **Yedekleme altyapısı**  >  **depolama hesaplarınızı** açın. Kurtarma Hizmetleri kasasından depolama hesaplarını kaldırmak için **kaydı** Kaldır ' ı seçin.

>[!NOTE]
>Kurtarma Hizmetleri Kasası, yalnızca kasaya kayıtlı tüm depolama hesaplarının kaydı kaldırıldıktan sonra silinebilir.

## <a name="common-backup-or-restore-errors"></a>Ortak yedekleme veya geri yükleme hataları

>[!NOTE]
>Yedekleme veya geri yükleme işlemleri gerçekleştirmek için yeterli izinlere sahip olduğunuzdan emin olmak için [Bu belgeye](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) başvurun.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>Dosya paylaşma bulunamadığı için FileShareNotFound-Işlem başarısız oldu

Hata kodu: FileShareNotFound

Hata Iletisi: dosya paylaşma bulunamadığı için Işlem başarısız oldu

Korumaya çalıştığınız dosya paylaşımının silinmemiş olduğundan emin olun.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable-depolama hesabı bulunamadı veya desteklenmiyor

Hata kodu: UserErrorFileShareEndpointUnreachable

Hata Iletisi: depolama hesabı bulunamadı veya desteklenmiyor

- Depolama hesabının kaynak grubunda mevcut olduğundan ve son doğrulamadan sonra silinmemiş veya kaynak grubundan kaldırılmadığından emin olun.

- Depolama hesabının dosya paylaşma yedeklemesi için desteklenen bir depolama hesabı olduğundan emin olun.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>Afsmaxsnapshotulaşıldı-bu dosya paylaşımının en fazla anlık görüntü sınırına ulaştınız; eskilerinin süreleri dolduğunda daha fazla bilgi alabilirsiniz

Hata kodu: Afsmaxsnapshotulaşıldı

Hata Iletisi: Bu dosya paylaşımının en fazla anlık görüntü sınırına ulaştınız; daha eski bir süre sona erdiğinde daha fazla işlem yapabilirsiniz.

- Bu hata, bir dosya paylaşımında birden çok isteğe bağlı yedekleme oluşturduğunuzda ortaya çıkabilir.
- Azure Backup tarafından alınanları içeren dosya paylaşımında 200 anlık görüntü sınırı vardır. Zamanlanmış olan daha eski yedeklemeler (veya anlık görüntüler) otomatik olarak temizlenir. Üst sınıra ulaşılırsa isteğe bağlı yedeklemelerin (veya anlık görüntülerin) silinmesi gerekir.

Azure Dosyaları portalından isteğe bağlı yedeklemeleri (Azure dosya paylaşımı anlık görüntülerini) silin.

>[!NOTE]
> Azure Backup tarafından oluşturulan anlık görüntüleri silerseniz kurtarma noktalarını kaybedersiniz.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound-belirtilen depolama hesabı artık olmadığı için Işlem başarısız oldu

Hata kodu: UserErrorStorageAccountNotFound

Hata Iletisi: belirtilen depolama hesabı artık olmadığı için Işlem başarısız oldu.

Depolama hesabının hala mevcut olduğundan ve silinmediğinden emin olun.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound-belirtilen depolama hesabı ayrıntıları yanlış

Hata kodu: UserErrorDTSStorageAccountNotFound

Hata Iletisi: belirtilen depolama hesabı ayrıntıları hatalı.

Depolama hesabının hala mevcut olduğundan ve silinmediğinden emin olun.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound-kaynak grubu yok

Hata kodu: UserErrorResourceGroupNotFound

Hata Iletisi: kaynak grubu yok

Var olan bir kaynak grubunu seçin veya yeni bir kaynak grubu oluşturun.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest-bu dosya paylaşımında bir yedekleme işi zaten devam ediyor

Hata kodu: ParallelSnapshotRequest

Hata Iletisi: Bu dosya paylaşımında bir yedekleme işi zaten devam ediyor.

- Dosya paylaşma yedeklemesi, aynı dosya paylaşımında paralel anlık görüntü isteklerini desteklemez.

- Mevcut yedekleme işinin bitmesini bekleyin ve sonra yeniden deneyin. Kurtarma Hizmetleri kasasında bir yedekleme işi bulamıyorsanız, aynı abonelikte diğer kurtarma hizmetleri kasalarını kontrol edin.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>Filesharebackupfailedwithazurerprequestazaltma/FileshareRestoreFailedWithAzureRpRequestThrottling-dosya paylaşım yedeklemesi veya geri yükleme depolama hizmeti azaltma nedeniyle başarısız oldu. Bunun nedeni, depolama hizmetinin verilen depolama hesabı için diğer istekleri işlemekle meşgul olması olabilir

Hata kodu: Filesharebackupfailedwithazurerprequestazaltma/FileshareRestoreFailedWithAzureRpRequestThrottling

Hata Iletisi: depolama hizmeti azaltma nedeniyle dosya paylaşma yedeklemesi veya geri yüklemesi başarısız oldu. Bunun sebebi, depolama hizmetinin, söz konusu depolama hesabına yönelik diğer istekleri işlemekle meşgul olmasından kaynaklanıyor olabilir.

Yedekleme/geri yükleme işlemini daha sonra deneyin.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound-hedef dosya paylaşma bulunamadı

Hata kodu: TargetFileShareNotFound

Hata Iletisi: hedef dosya paylaşma bulunamadı.

- Seçili depolama hesabının var olduğundan ve hedef dosya paylaşımının silinmediğinden emin olun.

- Depolama hesabının dosya paylaşma yedeklemesi için desteklenen bir depolama hesabı olduğundan emin olun.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>Usererrorstorageaccountııkilitlenen-yedekleme veya geri yükleme işleri depolama hesabının kilitli durumda olması nedeniyle başarısız oldu

Hata kodu: Usererrorstorageaccountıkilitleme

Hata Iletisi: depolama hesabının kilitli durumda olmasından dolayı yedekleme veya geri yükleme işleri başarısız oldu.

Depolama hesabındaki kilidi kaldırın veya **okuma kilidi** yerine **silme kilidi** kullanın ve yedekleme veya geri yükleme işlemini yeniden deneyin.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>Hatalı dosya sayısı eşikten daha fazla olduğu için DataTransferServiceCoFLimitReached-Recovery başarısız oldu

Hata kodu: DataTransferServiceCoFLimitReached

Hata Iletisi: başarısız dosya sayısı eşikten fazla olduğundan kurtarma başarısız oldu.

- Kurtarma hatası nedenleri bir dosyada (iş ayrıntılarında belirtilen yol) listelenir. Hatalara yönelik sorunları giderin ve geri yükleme işlemini yalnızca başarısız dosyalar için yeniden deneyin.

- Dosya geri yükleme hatalarının sık karşılaşılan nedenleri:

  - başarısız olan dosyalar şu anda kullanımda
  - üst dizinde başarısız olan dosyayla aynı ada sahip bir dizin var.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover, hiçbir dosya kurtarılamadığından kurtarma başarısız oldu

Hata kodu: DataTransferServiceAllFilesFailedToRecover

Hata Iletisi: hiçbir dosya kurtarılamadığından kurtarma başarısız oldu.

- Kurtarma hatası nedenleri bir dosyada (iş ayrıntılarında belirtilen yol) listelenir. Hataları giderin ve yalnızca başarısız dosyalar için geri yükleme işlemlerini yeniden deneyin.

- Dosya geri yükleme hatalarının sık karşılaşılan nedenleri:

  - başarısız olan dosyalar şu anda kullanımda
  - üst dizinde başarısız olan dosyayla aynı ada sahip bir dizin var.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>Usererrordtssourceurınotvalid-kaynaktaki dosyalardan biri mevcut olmadığından geri yükleme başarısız oluyor

Hata kodu: Datatransferservicesourceurınotvalid

Hata Iletisi: kaynaktaki dosyalardan biri mevcut olmadığından geri yükleme başarısız olur.

- Seçilen öğeler kurtarma noktası verilerinde mevcut değildir. Dosyaları kurtarmak için doğru dosya listesini sağlayın.
- Kurtarma noktasına karşılık gelen dosya paylaşımı anlık görüntüsü el ile silindi. Farklı bir kurtarma noktası seçin ve geri yükleme işlemini yeniden deneyin.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>Usererrordtsdestkilitlendi-aynı hedefe yönelik bir kurtarma işi var

Hata kodu: Usererrordtsdestkilitlendi

Hata Iletisi: bir kurtarma işi aynı hedefe yönelik bir işlemdir.

- Dosya paylaşma yedeklemesi, aynı hedef dosya paylaşımında paralel kurtarmayı desteklemez.

- Mevcut kurtarma işleminin tamamlanmasını bekleyip tekrar deneyin. Kurtarma Hizmetleri kasasında bir kurtarma işi bulamıyorsanız, aynı abonelikte diğer kurtarma hizmetleri kasalarını kontrol edin.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>Hedef dosya paylaşım dolu olduğundan UserErrorTargetFileShareFull-restore işlemi başarısız oldu

Hata kodu: UserErrorTargetFileShareFull

Hata Iletisi: hedef dosya paylaşma dolu olduğu için geri yükleme işlemi başarısız oldu.

Hedef dosya paylaşımının boyut kotasını geri yükleme verilerine uyacak şekilde artırın ve geri yükleme işlemini yeniden deneyin.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>Usererrortargetfilesharequotanotyetecek-Target dosya paylaşımının geri yükleme için yeterli depolama boyutu kotası yok

Hata kodu: Usererrortargetfilesharequotanotyeterince

Hata Iletisi: hedef dosya paylaşımının geri yükleme için yeterli depolama boyutu kotası yok

Hedef dosya paylaşımının boyut kotasını geri yükleme verilerine uyacak şekilde artırın ve işlemi yeniden deneyin

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>Dosya Eşitleme PreRestoreFailed-geri yükleme işlemi, hedef dosya paylaşımıyla ilişkili Dosya Eşitleme hizmeti kaynakları üzerinde önceden geri yükleme işlemleri gerçekleştirilirken bir hata oluştuğundan başarısız oldu

Hata kodu: Dosya Eşitleme PreRestoreFailed

Hata Iletisi: hedef dosya paylaşımıyla ilişkili Dosya Eşitleme hizmet kaynaklarında ön geri yükleme işlemleri gerçekleştirilirken bir hata oluştuğundan geri yükleme işlemi başarısız oldu.

Verileri daha sonra geri yüklemeyi deneyin. Sorun devam ederse, Microsoft desteğine başvurun.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress-Azure Dosya Eşitleme hizmet değişikliği algılaması hedef dosya paylaşımında devam ediyor. Değişiklik algılama, hedef dosya paylaşımında önceki bir geri yükleme tarafından tetiklendi

Hata kodu: AzureFileSyncChangeDetectionInProgress

Hata Iletisi: hedef dosya paylaşımında Azure Dosya Eşitleme hizmet değişikliği algılaması devam ediyor. Değişiklik algılama, hedef dosya paylaşımında önceki bir geri yükleme tarafından tetiklendi.

Farklı bir hedef dosya paylaşma kullanın. Alternatif olarak, geri yüklemeyi yeniden denemeden önce hedef dosya paylaşımında Azure Dosya Eşitleme hizmet değişikliği algılamanın tamamlanmasını bekleyebilirsiniz.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>Usererrorafsrecoverysomefilesnotgeri yüklendi-bir veya daha fazla dosya başarıyla kurtarılamadı. Daha fazla bilgi için yukarıda verilen yoldaki başarısız dosya listesini denetleyin

Hata kodu: Usererrorafsrecoverysomefilesnotgeri yüklendi

Hata Iletisi: bir veya daha fazla dosya başarıyla kurtarılamadı. Daha fazla bilgi için, yukarıda verilen yoldaki başarısız dosya listesini kontrol edin.

- Kurtarma hatası nedenleri dosyada (Iş ayrıntılarında belirtilen yol) listelenmiştir. Nedenleri çözün ve yalnızca başarısız dosyalar için geri yükleme işlemini yeniden deneyin.
- Dosya geri yükleme hatalarının sık karşılaşılan nedenleri:

  - başarısız olan dosyalar şu anda kullanımda
  - üst dizinde başarısız olan dosyayla aynı ada sahip bir dizin var.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound-kurtarma noktasına karşılık gelen Azure dosya paylaşımının anlık görüntüsü bulunamıyor

Hata kodu: UserErrorAFSSourceSnapshotNotFound

Hata Iletisi: kurtarma noktasına karşılık gelen Azure dosya paylaşımının anlık görüntüsü bulunamıyor

- Kurtarma için kullanmaya çalıştığınız kurtarma noktasına karşılık gelen dosya paylaşımının anlık görüntüsünün hala mevcut olduğundan emin olun.

  >[!NOTE]
  >Azure Backup tarafından oluşturulan bir dosya paylaşımının anlık görüntüsünü silerseniz, ilgili kurtarma noktaları kullanılamaz hale gelir. Güvence altına almak için anlık görüntüleri silmediğimiz için önerilir.

- Verilerinizi kurtarmak için başka bir geri yükleme noktası seçmeyi deneyin.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget-aynı hedef dosya paylaşımında başka bir geri yükleme işi devam ediyor

Hata kodu: UserErrorAnotherRestoreInProgressOnSameTarget

Hata Iletisi: başka bir geri yükleme işi aynı hedef dosya paylaşımında devam ediyor

Farklı bir hedef dosya paylaşma kullanın. Alternatif olarak işleminizi iptal edebilir veya diğer geri yükleme işleminin tamamlanmasını bekleyebilirsiniz.

## <a name="common-modify-policy-errors"></a>Ortak ilke hatalarını değiştirme

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation-bu öğe için başka bir yapılandırma koruma işlemi devam ediyor

Hata kodu: BMSUserErrorConflictingProtectionOperation

Hata Iletisi: Bu öğe için başka bir yapılandırma koruma işlemi devam ediyor.

Önceki değiştirme ilkesi işleminin bitmesini bekleyin ve daha sonra yeniden deneyin.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>Bmsusererrorobjectkilitlendi-seçili öğede başka bir işlem devam ediyor

Hata kodu: Bmsusererrorobjectkilitlendi

Hata Iletisi: seçili öğede başka bir işlem devam ediyor.

Devam eden diğer işlemin tamamlanmasını bekleyin ve daha sonra yeniden deneyin.

## <a name="common-soft-delete-related-errors"></a>Yaygın geçici silme ile Ilgili hatalar

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState-bu nokta ile ilişkili anlık görüntü, geçici olarak silinmiş durumdaki bir dosya paylaşımında yer aldığı için bu geri yükleme noktası kullanılamıyor

Hata kodu: UserErrorRestoreAFSInSoftDeleteState

Hata Iletisi: Bu nokta ile ilişkili anlık görüntü, geçici olarak silinmiş durumdaki bir dosya paylaşımında olduğundan, bu geri yükleme noktası kullanılamıyor.

Dosya paylaşımının geçici olarak silinmiş durumunda olduğu durumlarda geri yükleme işlemini gerçekleştiremezsiniz. Dosya paylaşımından dosyalar portalından silmeyi veya [silmeyi](scripts/backup-powershell-script-undelete-file-share.md) geri yüklemeyi deneyin.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState-geri yükleme noktası anlık görüntülerini içeren ilişkili dosya paylaşımında kalıcı olarak silindiğinden listelenen geri yükleme noktaları kullanılamıyor

Hata kodu: UserErrorRestoreAFSInDeleteState

Hata Iletisi: geri yükleme noktası anlık görüntülerini içeren ilişkili dosya paylaşımında kalıcı olarak silindiğinden listelenen geri yükleme noktaları kullanılamıyor.

Yedeklenen dosya paylaşımının silinip silinmediğini denetleyin. Geçici olarak silinmiş durumdaysa, geçici silme bekletme döneminin daha fazla olup olmadığını ve geri kurtarılamamış olduğunu kontrol edin. Bu durumda, tüm anlık görüntülerinizi kalıcı olarak kaybedersiniz ve verileri kurtaramayacaksınız.

>[!NOTE]
> Yedeklenen dosya paylaşımının silinmesini veya geçici olarak silinmiş durumda olup olmadığını, tüm geri yükleme noktalarınızın kaybolmasını önlemek için geçici silme bekletme süresi bitmeden önce silmeyi geri almayı öneririz.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState-Azure dosya paylaşma geçici olarak silinmiş durumda olduğundan yedekleme başarısız oldu

Hata kodu: UserErrorBackupAFSInSoftDeleteState

Hata Iletisi: Azure dosya paylaşma geçici olarak silinmiş durumda olduğundan yedekleme başarısız oldu

Yedekleme işlemine devam etmek ve verilerin kalıcı olarak silinmesini engellemek için **dosyalar portalından** dosya paylaşımının veya [silmeyi geri alma betiği](scripts/backup-powershell-script-undelete-file-share.md) kullanarak silmeyi geri alın.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState-ilişkili Azure dosya paylaşımının kalıcı olarak silindiği için yedekleme başarısız oldu

Hata kodu: UserErrorBackupAFSInDeleteState

Hata Iletisi: ilişkili Azure dosya paylaşımının kalıcı olarak silindiği için yedekleme başarısız oldu

Yedeklenen dosya paylaşımının kalıcı olarak silinip silinmediğini denetleyin. Yanıt Evet ise, yinelenen yedekleme hatalarından kaçınmak için dosya paylaşımının yedeklemesini durdurun. Korumayı durdurmayı öğrenmek için bkz. [Azure dosya paylaşımının korumasını durdurma](./manage-afs-backup.md#stop-protection-on-a-file-share)

## <a name="next-steps"></a>Sonraki adımlar

Azure dosya paylaşımlarını yedekleme hakkında daha fazla bilgi için bkz.:

- [Azure dosya paylaşımlarını yedekleme](backup-afs.md)
- [Azure dosya paylaşımlarını yedekleme ile ilgili SSS](backup-azure-files-faq.yml)
