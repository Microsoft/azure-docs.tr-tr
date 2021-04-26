---
title: Azure disk yedekleme 'de yedekleme hatalarıyla ilgili sorunları giderme
description: Azure disk yedekleme 'de yedekleme hatalarıyla ilgili sorunları nasıl giderebileceğinizi öğrenin
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: f6e9bcbbd618cf820ce91f8a3092d5be4aa045cb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105107253"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup"></a>Azure disk yedekleme 'de yedekleme hatalarıyla ilgili sorunları giderme

Bu makalede, Azure disk ile ilgili yedekleme ve geri yükleme sorunları hakkında sorun giderme bilgileri sağlanmaktadır. [Azure disk yedekleme](disk-backup-overview.md) bölgesi kullanılabilirliği, desteklenen senaryolar ve sınırlamalar hakkında daha fazla bilgi için bkz. [destek matrisi](disk-backup-support-matrix.md).

## <a name="common-issues-faced-with-azure-disk-backup"></a>Azure disk yedekleme ile karşılaştığı yaygın sorunlar

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>Hata kodu: Usererroranlık değeri Trgsubscriptionuyuşmazlığı

Hata Iletisi: Snapshot veri deposu için geçersiz abonelik seçildi

Önerilen eylem: diskler ve disk anlık görüntüleri aynı abonelikte depolanır. Abonelik içinde disk anlık görüntülerini depolamak için herhangi bir kaynak grubunu seçebilirsiniz. Kaynak diskle aynı aboneliği seçin. Daha fazla bilgi için bkz. [destek matrisi](disk-backup-support-matrix.md).

### <a name="error-code-usererrorsnapshotrgnotfound"></a>Hata kodu: Usererroranlık değeri Trgnotfound

Hata Iletisi: anlık görüntü veri deposu kaynak grubu mevcut olmadığından işlem gerçekleştirilemedi.

Önerilen eylem: kaynak grubunu oluşturun ve gerekli izinleri buna sağlayın. Daha fazla bilgi için bkz. [yedeklemeyi yapılandırma](backup-managed-disks.md#configure-backup).

### <a name="error-code-usererrormanageddisknotfound"></a>Hata kodu: Usererrormanageddisde Tfound

Hata Iletisi: yönetilen disk artık mevcut olmadığından işlem gerçekleştirilemedi.

Önerilen eylem: kaynak disk silinmiş veya farklı bir konuma taşınabileceği için yedeklemeler başarısız olmaya devam edecektir. Yanlışlıkla silinirse diski geri yüklemek için mevcut geri yükleme noktasını kullanın. Disk farklı bir konuma taşınırsa, disk için yedeklemeyi yapılandırın.

### <a name="error-code-usererrornotenoughpermissionondisk"></a>Hata kodu: UserErrorNotEnoughPermissionOnDisk

Hata Iletisi: Azure Backup hizmeti bu işlemi yapmak için diskte ek izinler gerektirir.

Önerilen eylem: yedekleme kasasının yönetilen kimliğine diskte uygun izinleri verin. Yedekleme Kasası yönetilen kimliği ve nasıl sağlayacaklarınız için hangi izinlerin gerekli olduğunu anlamak için [belgelere](backup-managed-disks.md) bakın.

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>Hata kodu: Usererrornotenoughpermissiononanlık ileti Trg

Hata Iletisi: Azure Backup hizmeti bu işlemi yapmak için Snapshot Data Store kaynak grubunda ek izinler gerektirir.

Önerilen eylem: yedekleme kasasının yönetilen kimliğine anlık görüntü veri deposu kaynak grubunda uygun izinleri verin. Anlık görüntü veri deposu kaynak grubu, disk anlık görüntülerinin depolandığı yerdir. Kaynak grubu olduğunu anlamak için, Yedekleme Kasası tarafından yönetilen kimlik için hangi izinlerin gerekli olduğunu ve nasıl sağlayacağınızı anlamak için [belgelere](backup-managed-disks.md) bakın.

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>Hata kodu: Usererrordiskbackupdiskorunmsipermissionsnotsun

Hata Iletisi: geçersiz disk veya Azure Backup hizmeti bu işlemi yapmak için diskte ek izinler gerektiriyor

Önerilen eylem: kaynak disk silinmiş veya farklı bir konuma taşınabileceği için yedeklemeler başarısız olmaya devam edecektir. Yanlışlıkla silinirse diski geri yüklemek için mevcut geri yükleme noktasını kullanın. Disk farklı bir konuma taşınırsa, disk için yedeklemeyi yapılandırın. Disk silinmemişse veya taşınmışsa, yedekleme kasasının yönetilen kimliğine diskte uygun izinleri verin. Yedekleme kasasının yönetilen kimliği için hangi izinlerin gerekli olduğunu ve nasıl sağlayacağınızı anlamak için [belgelere](backup-managed-disks.md) bakın.

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>Hata kodu: Usererrordiskbackupanlık görüntüyle Trgormsipermissionsnotsun

Hata Iletisi: anlık görüntü veri deposu kaynak grubu artık mevcut olmadığından işlem gerçekleştirilemedi. Ya da Azure Backup hizmeti bu işlemi yapmak için Snapshot Data Store kaynak grubunda ek izinler gerektirir.

Önerilen eylem: bir kaynak grubu oluşturun ve yedek kasasının yönetilen kimliğine anlık görüntü veri deposu kaynak grubunda uygun izinleri verin. Anlık görüntü veri deposu kaynak grubu, disk anlık görüntülerinin depolandığı yerdir. Kaynak grubunun ne olduğunu, yedekleme kasasının yönetilen kimliği için hangi izinlerin gerekli olduğunu ve nasıl sağlayacağınızı öğrenmek için [belgelere](backup-managed-disks.md) bakın.

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>Hata kodu: UserErrorDiskBackupAuthorizationFailed

Hata Iletisi: Yedekleme Kasası yönetilen kimliğinde bu işlemi yapmak için gereken izinler eksik.

Önerilen eylem: yedekleme kasasının yönetilen kimliğine, yedeklenecek diskte ve anlık görüntülerin depolandığı anlık görüntü veri deposu kaynak grubunda uygun izinleri verin. Yedekleme kasasının yönetilen kimliği için hangi izinlerin gerekli olduğunu ve nasıl sağlayacağınızı anlamak için [belgelere](backup-managed-disks.md) bakın.

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>Hata kodu: Usererroranlık değeri Trgormsipermissionsnotsun

Hata Iletisi: anlık görüntü veri deposu kaynak grubu artık mevcut olmadığından işlem gerçekleştirilemedi. Ya da, bu işlemi yapmak için Azure Backup hizmeti Snapshot Data Store kaynak grubunda ek izinler gerektirir.

Önerilen eylem: kaynak grubunu oluşturun ve yedekleme kasasının yönetilen kimliğine, anlık görüntü veri deposu kaynak grubunda uygun izinleri verin. Anlık görüntü veri deposu kaynak grubu, anlık görüntülerin depolandığı yerdir. Kaynak grubunun ne olduğunu, yedekleme kasasının yönetilen kimliği için hangi izinlerin gerekli olduğunu ve nasıl sağlayacağınızı öğrenmek için [belgelere](backup-managed-disks.md) bakın.

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>Hata kodu: Usererroroperationalstoreparametersnotsaðlanan

Hata Iletisi: anlık görüntü veri deposu kaynak grubu parametresi sağlanmadığından işlem gerçekleştirilemedi

Önerilen eylem: Snapshot Data Store kaynak grubu parametresini sağlayın. Anlık görüntü veri deposu kaynak grubu, disk anlık görüntülerinin depolandığı yerdir. Daha fazla bilgi için [belgelerine](backup-managed-disks.md)bakın.

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>Hata kodu: UserErrorInvalidOperationalStoreResourceGroup

Hata Iletisi: belirtilen anlık görüntü veri deposu kaynak grubu geçersiz

Önerilen eylem: Snapshot veri deposu için geçerli bir kaynak grubu sağlayın. Anlık görüntü veri deposu kaynak grubu, disk anlık görüntülerinin depolandığı yerdir. Daha fazla bilgi için [belgelerine](backup-managed-disks.md)bakın.

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>Hata kodu: UserErrorDiskBackupDiskTypeNotSupported

Hata Iletisi: desteklenmeyen disk türü

Önerilen eylem: desteklenmeyen senaryolar ve sınırlamalar üzerinde [destek matrisine](disk-backup-support-matrix.md) bakın.

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>Hata kodu: UserErrorSameNameDiskAlreadyExists

Hata Iletisi: seçili hedef kaynak grubunda aynı ada sahip bir disk zaten varolduğundan geri yüklenemedi

Önerilen eylem: geri yükleme için farklı bir disk adı belirtin. Daha fazla bilgi için bkz. [Azure yönetilen disklerini geri yükleme](restore-managed-disks.md).

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>Hata kodu: UserErrorRestoreTargetRGNotFound

Hata Iletisi: hedef kaynak grubu mevcut olmadığından Işlem başarısız oldu.

Önerilen eylem: geri yüklemek için geçerli bir kaynak grubu sağlayın. Daha fazla bilgi için bkz. [Azure yönetilen disklerini geri yükleme](restore-managed-disks.md).

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>Hata kodu: UserErrorNotEnoughPermissionOnTargetRG

Hata Iletisi: Azure Backup hizmeti bu işlemi yapmak için hedef kaynak grubunda ek izinler gerektirir.

Önerilen eylem: yedekleme kasasının yönetilen kimliğine, hedef kaynak grubunda uygun izinleri verin. Hedef kaynak grubu, diskin geri yükleneceği seçili konumdur. Yedekleme kasasının yönetilen kimliği için hangi izinlerin gerekli olduğunu ve nasıl sağlayacağınızı anlamak için [geri yükleme belgelerine](restore-managed-disks.md) bakın.

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>Hata kodu: Usererrorsubscriptiondiskquotalımıtulaşıldı

Hata Iletisi: abonelikte disk kotası üst sınırına ulaşıldığından Işlem başarısız oldu.

Önerilen eylem: daha fazla bilgi için [Azure aboneliğine ve hizmet sınırlarına ve kota belgelerine](../azure-resource-manager/management/azure-subscription-service-limits.md) bakın veya Microsoft desteği başvurun.

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>Hata kodu: UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

Hata Iletisi: hedef kaynak grubu mevcut olmadığından Işlem başarısız oldu. Veya Azure Backup hizmeti bu işlemi yapmak için hedef kaynak grubunda ek izinler gerektirir.

Önerilen eylem: geri yüklenecek geçerli bir kaynak grubu sağlayın ve yedekleme kasasının yönetilen kimliğine hedef kaynak grubunda uygun izinleri verin. Hedef kaynak grubu, diskin geri yükleneceği seçili konumdur. Yedekleme kasasının yönetilen kimliği için hangi izinlerin gerekli olduğunu ve nasıl sağlayacağınızı anlamak için [geri yükleme belgelerine](restore-managed-disks.md) bakın.

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>Hata kodu: UserErrorDESKeyVaultKeyDisabled

Hata Iletisi: disk şifreleme kümesi için kullanılan anahtar kasası anahtarı etkin durumda değil.

Önerilen eylem: disk şifreleme kümesi için kullanılan Anahtar Kasası anahtarını etkinleştirin. [Destek matrisindeki](disk-backup-support-matrix.md)sınırlamalara bakın.

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>Hata kodu: UserErrorMSIPermissionsNotPresentOnDES

Hata Iletisi: Azure Backup hizmeti, diskle kullanılan disk şifrelemesi kümesine erişmek için izne ihtiyaç duyuyor.

Önerilen eylem: yedekleme kasasının yönetilen kimliğine disk şifreleme kümesine (DES) okuyucu erişimi sağlayın.

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>Hata kodu: UserErrorDESKeyVaultKeyNotAvailable

Hata Iletisi: disk şifreleme kümesi için kullanılan anahtar kasası anahtarı kullanılamıyor.

Önerilen eylem: disk şifreleme kümesi için kullanılan Anahtar Kasası anahtarının devre dışı olmadığından ve silinmediğinden emin olun.

### <a name="error-code-usererrordisksnapshotnotfound"></a>Hata kodu: UserErrorDiskSnapshotNotFound

Hata Iletisi: Bu geri yükleme noktası için disk anlık görüntüsü silindi.

Önerilen eylem: anlık görüntüler, aboneliğinizdeki anlık görüntü veri deposu kaynak grubunda depolanır. Seçilen geri yükleme noktasıyla ilgili anlık görüntünün silinmiş veya bu kaynak grubundan taşınmış olması mümkündür. Geri yüklemek için başka bir kurtarma noktası kullanmayı deneyin. Ayrıca, [geri yükleme belgelerinde](restore-managed-disks.md)belirtilen anlık görüntü kaynak grubunu seçmek için önerilen yönergeleri izleyin.

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>Hata kodu: Usererrorsnapshotmetadadtanotfound

Hata Iletisi: Bu geri yükleme noktası için disk anlık görüntüsü meta verileri silindi

Önerilen eylem: geri yüklemek için başka bir kurtarma noktası kullanmayı düşünün. Daha fazla bilgi için [geri yükleme belgelerine](restore-managed-disks.md)bakın.

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>Hata kodu: BackupAgentPluginHostValidateProtectionError

Hata Iletisi: yedekleme kasasının yapılandırma korumasını denediğiniz bölgede disk yedeklemesi henüz kullanılamıyor.

Önerilen eylem: Yedekleme Kasası desteklenen bir bölgede olmalıdır. Bölge kullanılabilirliği için [destek matrisine](disk-backup-support-matrix.md)bakın.

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>Hata kodu: Usererrordppdatasourcealreadyhasbackupınstance

Hata Iletisi: yedeklemeyi yapılandırmaya çalıştığınız disk zaten korunuyor. Disk, bir yedekleme kasasındaki bir yedekleme örneğiyle zaten ilişkilendirilmiş.

Önerilen eylem: Bu disk, bir yedekleme kasasındaki bir yedekleme örneğiyle zaten ilişkilendirilmiş. Bu diski yeniden korumak istiyorsanız, yedekleme örneğini Şu anda korunduğu yedekleme kasasından silin ve diğer kasadaki diski yeniden koruyun.

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>Hata kodu: UserErrorDppDatasourceAlreadyProtected

Hata Iletisi: yedeklemeyi yapılandırmaya çalıştığınız disk zaten korunuyor. Disk, bir yedekleme kasasındaki bir yedekleme örneğiyle zaten ilişkilendirilmiş.

Önerilen eylem: Bu disk, bir yedekleme kasasındaki bir yedekleme örneğiyle zaten ilişkilendirilmiş. Bu diski yeniden korumak istiyorsanız, yedekleme örneğini Şu anda korunduğu yedekleme kasasından silin ve diğer kasadaki diski yeniden koruyun.

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>Hata kodu: Usererrormaxconcurrentoperationlimitulaşıldı

Hata Iletisi: izin verilen en fazla eşzamanlı yedeklemeye ulaşıldığından işlem başlatılamıyor.

Önerilen eylem: önceki çalışan Yedekleme tamamlanana kadar bekleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Disk Yedekleme destek matrisi](disk-backup-support-matrix.md)