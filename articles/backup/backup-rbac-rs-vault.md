---
title: Azure rol tabanlı erişim denetimi ile yedeklemeleri yönetme
description: Kurtarma Hizmetleri kasasındaki yedekleme yönetimi işlemlerine erişimi yönetmek için Azure rol tabanlı erişim denetimi kullanın.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 0b321a5f33bd75ce8615d6d2a90442a83d9fff67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102613451"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Azure Backup kurtarma noktalarını yönetmek için Azure rol tabanlı erişim denetimi kullanma

Azure rol tabanlı erişim denetimi (Azure RBAC), Azure için ayrıntılı erişim yönetimine izin vermez. Azure RBAC kullanarak ekibiniz içinde görevleri ayırabilir, bu işlere gerek duyan kişilere sadece erişim miktarını verebilirsiniz.

> [!IMPORTANT]
> Azure Backup tarafından sunulan roller Azure portal veya REST API ya da kurtarma hizmetleri Kasası PowerShell veya CLı cmdlet 'leri aracılığıyla gerçekleştirilebilecek eylemlerle sınırlıdır. Azure Backup Aracısı istemci kullanıcı arabirimi veya System Center Data Protection Manager Kullanıcı arabirimi veya Azure Backup Sunucusu Kullanıcı arabiriminde gerçekleştirilen eylemler, bu rollerin denetimi dışındadır.

Azure Backup, yedekleme yönetimi işlemlerini denetlemek için üç yerleşik rol sağlar. [Azure yerleşik rolleri](../role-based-access-control/built-in-roles.md) hakkında daha fazla bilgi edinin

* [Yedek katılımcısı](../role-based-access-control/built-in-roles.md#backup-contributor) -bu rolün, kurtarma hizmetleri kasasını silme ve başkalarına erişim verme dışında yedekleme oluşturma ve yönetme izinleri vardır. Bu rolü, her yedekleme yönetimi işlemini yapan yedekleme yönetiminin Yöneticisi olarak düşünün.
* [Yedekleme işletmeni](../role-based-access-control/built-in-roles.md#backup-operator) -bu rolün, yedeklemenin kaldırılması ve yedekleme ilkelerinin yönetilmesi dışında, katkıda bulunan her şeye yönelik izinleri vardır. Bu rol katkıda bulunan, verileri silme veya şirket içi kaynakların kaydını kaldırma gibi bozucu işlemler gerçekleştiremedikçe, katkıda buluna eşdeğerdir.
* [Yedekleme okuyucusu](../role-based-access-control/built-in-roles.md#backup-reader) -bu rolün tüm yedekleme yönetimi işlemlerini görüntüleme izinleri vardır. Bu rolü bir izleme kişisi olarak düşünün.

Daha da fazla denetim için kendi rollerinizi tanımlamak istiyorsanız bkz. Azure RBAC 'de [özel roller oluşturma](../role-based-access-control/custom-roles.md) .

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Yedekleme yerleşik rollerini yedekleme yönetim eylemlerine eşleme

### <a name="minimum-role-requirements-for-azure-vm-backup"></a>Azure VM yedeklemesi için en düşük rol gereksinimleri

Aşağıdaki tabloda, bu işlemi gerçekleştirmek için gereken yedekleme yönetimi eylemleri ve ilgili minimum Azure rolü yer alır.

| Yönetim Işlemi | Gerekli en düşük Azure rolü | Kapsam gerekli | Yapıyı |
| --- | --- | --- | --- |
| Kurtarma Hizmetleri kasası oluşturma | Yedek Katılımcısı | Kasayı içeren kaynak grubu |   |
| Azure VM 'lerinin yedeklenmesini etkinleştirme | Yedekleme Operatörü | Kasayı içeren kaynak grubu |   |
| | Sanal Makine Katılımcısı | VM kaynağı |  Alternatif olarak, yerleşik rol yerine, şu izinlere sahip özel bir rol düşünebilirsiniz: Microsoft. COMPUTE/virtualMachines/Write |
| VM 'nin isteğe bağlı yedeklemesi | Yedekleme Operatörü | Kurtarma Hizmetleri kasası |   |
| Sanal makineyi geri yükleme | Yedekleme Operatörü | Kurtarma Hizmetleri kasası |   |
| | Katılımcı | VM 'nin dağıtılacağı kaynak grubu |   Alternatif olarak, yerleşik rol yerine, aşağıdaki izinlere sahip olan özel bir rol düşünebilirsiniz: Microsoft. resources/abonelikler/resourceGroups/Write Microsoft. DomainRegistration/Domains/Write, Microsoft. Domain/virtualMachines/Write Microsoft. Network/virtualNetworks/Read/ACTION |
| | Sanal Makine Katılımcısı | Yedeklenen kaynak VM |   Alternatif olarak, yerleşik rol yerine, şu izinlere sahip özel bir rol düşünebilirsiniz: Microsoft. COMPUTE/virtualMachines/Write |
| Yönetilmeyen diskleri geri yükleme VM yedeklemesi | Yedekleme Operatörü | Kurtarma Hizmetleri kasası |
| | Sanal Makine Katılımcısı | Yedeklenen kaynak VM | Alternatif olarak, yerleşik rol yerine, şu izinlere sahip özel bir rol düşünebilirsiniz: Microsoft. COMPUTE/virtualMachines/Write |
| | Depolama Hesabı Katılımcısı | Disklerin geri yükleneceği depolama hesabı kaynağı |   Alternatif olarak, yerleşik rol yerine, şu izinlere sahip özel bir rol düşünebilirsiniz: Microsoft. Storage/storageAccounts/Write |
| Yönetilen diskleri VM yedeklemesinden geri yükleme | Yedekleme Operatörü | Kurtarma Hizmetleri kasası |
| | Sanal Makine Katılımcısı | Yedeklenen kaynak VM |    Alternatif olarak, yerleşik rol yerine, şu izinlere sahip özel bir rol düşünebilirsiniz: Microsoft. COMPUTE/virtualMachines/Write |
| | Depolama Hesabı Katılımcısı | Geri yükleme 'nin bir parçası olarak seçilen geçici depolama hesabı, verileri yönetilen disklere dönüştürmeden önce kasadan bekletme |   Alternatif olarak, yerleşik rol yerine, şu izinlere sahip özel bir rol düşünebilirsiniz: Microsoft. Storage/storageAccounts/Write |
| | Katılımcı | Yönetilen disklerin geri yükleneceği kaynak grubu | Alternatif olarak, yerleşik rol yerine, şu izinlere sahip özel bir rol düşünebilirsiniz: Microsoft. resources/abonelikler/resourceGroups/Write|
| Tek tek dosyaları VM yedeklemesinden geri yükleme | Yedekleme Operatörü | Kurtarma Hizmetleri kasası |
| | Sanal Makine Katılımcısı | Yedeklenen kaynak VM | Alternatif olarak, yerleşik rol yerine, şu izinlere sahip özel bir rol düşünebilirsiniz: Microsoft. COMPUTE/virtualMachines/Write |
| Çapraz bölge geri yükleme | Yedekleme Operatörü | Kurtarma Hizmetleri kasasının aboneliği | Bu, yukarıda bahsedilen geri yükleme izinlerinin yanı sıra. Özellikle CRR için yerleşik rol yerine, şu izinlere sahip özel bir rol düşünebilirsiniz: "Microsoft. RecoveryServices/Locations/backupAadProperties/Read" "Microsoft. RecoveryServices/Locations/backupCrrJobs/ACTION" "Microsoft. RecoveryServices/Locations/backupCrrJob/ACTION" "Microsoft. RecoveryServices/Locations/backupCrossRegionRestore/Action" "Microsoft. RecoveryServices/Locations/Backupcrroperationsonuçları/Read" "Microsoft. RecoveryServices/Locations/backupCrrOperationsStatus/Read" |
| Azure VM yedeklemesi için yedekleme ilkesi oluşturma | Yedek Katılımcısı | Kurtarma Hizmetleri kasası |
| Azure VM yedeklemesi 'nin yedekleme ilkesini değiştirme | Yedek Katılımcısı | Kurtarma Hizmetleri kasası |
| Azure VM yedeklemesi 'nin yedekleme ilkesini silme | Yedek Katılımcısı | Kurtarma Hizmetleri kasası |
| VM yedeklemede Yedeklemeyi Durdur (verileri sakla veya verileri Sil) | Yedek Katılımcısı | Kurtarma Hizmetleri kasası |
| Şirket içi Windows Server/Client/SCDPM veya Azure Backup Sunucusu Kaydet | Yedekleme Operatörü | Kurtarma Hizmetleri kasası |
| Kayıtlı şirket içi Windows Server/Client/SCDPM veya Azure Backup Sunucusu Sil | Yedek Katılımcısı | Kurtarma Hizmetleri kasası |

> [!IMPORTANT]
> VM 'yi bir VM kaynak kapsamında belirtir ve VM ayarlarının bir parçası olarak **yedekleme** ' yi SEÇERSENIZ, VM zaten yedeklense de **yedeklemeyi etkinleştir** ekranını açar. Bunun nedeni, yedekleme durumunu doğrulama çağrısının yalnızca abonelik düzeyinde çalışmadır. Bunu önlemek için kasaya gidin ve VM 'nin yedekleme öğesi görünümünü açın ya da bir abonelik düzeyinde VM katılımcısı rolünü belirtin.

### <a name="minimum-role-requirements-for-azure-workload-backups-sql-and-hana-db-backups"></a>Azure iş yükü yedeklemeleri için en düşük rol gereksinimleri (SQL ve HANA DB yedeklemeleri)

Aşağıdaki tabloda, bu işlemi gerçekleştirmek için gereken yedekleme yönetimi eylemleri ve ilgili minimum Azure rolü yer alır.

| Yönetim Işlemi | Gerekli en düşük Azure rolü | Kapsam gerekli | Yapıyı |
| --- | --- | --- | --- |
| Kurtarma Hizmetleri kasası oluşturma | Yedek Katılımcısı | Kasayı içeren kaynak grubu |   |
| SQL ve/veya HANA veritabanlarının yedeklenmesini etkinleştir | Yedekleme Operatörü | Kasayı içeren kaynak grubu |   |
| | Sanal Makine Katılımcısı | DB 'nin yüklendiği VM kaynağı |  Alternatif olarak, yerleşik rol yerine, şu izinlere sahip özel bir rol düşünebilirsiniz: Microsoft. COMPUTE/virtualMachines/Write |
| DB 'nin isteğe bağlı yedeklemesi | Yedekleme Operatörü | Kurtarma Hizmetleri kasası |   |
| Veritabanını geri yükle veya dosya olarak geri yükle | Yedekleme Operatörü | Kurtarma Hizmetleri kasası |   |
| | Sanal Makine Katılımcısı | Yedeklenen kaynak VM |   Alternatif olarak, yerleşik rol yerine, şu izinlere sahip özel bir rol düşünebilirsiniz: Microsoft. COMPUTE/virtualMachines/Write |
| | Sanal Makine Katılımcısı | DB 'nin geri yükleneceği veya dosyaların oluşturulduğu hedef VM |   Alternatif olarak, yerleşik rol yerine, şu izinlere sahip özel bir rol düşünebilirsiniz: Microsoft. COMPUTE/virtualMachines/Write |
| Azure VM yedeklemesi için yedekleme ilkesi oluşturma | Yedek Katılımcısı | Kurtarma Hizmetleri kasası |
| Azure VM yedeklemesi 'nin yedekleme ilkesini değiştirme | Yedek Katılımcısı | Kurtarma Hizmetleri kasası |
| Azure VM yedeklemesi 'nin yedekleme ilkesini silme | Yedek Katılımcısı | Kurtarma Hizmetleri kasası |
| VM yedeklemede Yedeklemeyi Durdur (verileri sakla veya verileri Sil) | Yedek Katılımcısı | Kurtarma Hizmetleri kasası |

### <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Azure dosya paylaşımının yedeklenmesi için en düşük rol gereksinimleri

Aşağıdaki tabloda, yedekleme yönetim eylemleri ve Azure dosya paylaşma işlemini gerçekleştirmek için gereken ilgili rol yer alır.

| Yönetim Işlemi | Rol gerekli | Kaynaklar |
| --- | --- | --- |
| Azure dosya paylaşımlarının yedeklenmesini etkinleştir | Yedek Katılımcısı |Kurtarma Hizmetleri kasası |
| |Depolama Hesabı | Katkıda bulunan depolama hesabı kaynağı |
| VM 'nin isteğe bağlı yedeklemesi | Yedekleme Operatörü | Kurtarma Hizmetleri kasası |
| Dosya payını geri yükle | Yedekleme Operatörü | Kurtarma Hizmetleri kasası |
| | Depolama Hesabı Katılımcısı | Geri yükleme kaynağı ve hedef dosya paylaşımlarının mevcut olduğu depolama hesabı kaynakları |
| Tek tek dosyaları geri yükle | Yedekleme Operatörü | Kurtarma Hizmetleri kasası |
| |Depolama Hesabı Katılımcısı|Geri yükleme kaynağı ve hedef dosya paylaşımlarının mevcut olduğu depolama hesabı kaynakları |
| Korumayı durdurma |Yedek Katılımcısı | Kurtarma Hizmetleri kasası |
| Depolama hesabının kasadan kaydını sil |Yedek Katılımcısı | Kurtarma Hizmetleri kasası |
| |Depolama Hesabı Katılımcısı | Depolama hesabı kaynağı|

## <a name="next-steps"></a>Sonraki adımlar

* [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): Azure Portal Azure RBAC ile çalışmaya başlayın.
* İle erişimin nasıl yönetileceğini öğrenin:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Azure rol tabanlı erişim denetimi sorunlarını giderme](../role-based-access-control/troubleshooting.md): yaygın sorunları gidermeye yönelik öneriler alın.
