---
title: Azure Backup’taki yenilikler
description: Azure Backup yeni özellikler hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 68e0e5cc0876840c30ab9e428a2b96bd7d667756
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516340"
---
# <a name="whats-new-in-azure-backup"></a>Azure Backup’taki yenilikler

Azure Backup, Azure 'daki verilerinizin korunmasını geliştiren yeni özellikleri sürekli olarak geliştirir ve serbest bırakır. Bu yeni özellikler, veri korumanızı yeni iş yükü türlerine genişletir, güvenliği artırır ve yedekleme verilerinizin kullanılabilirliğini geliştirir. Ayrıca yeni yönetim, izleme ve otomasyon özellikleri de ekler.

Yeni yayınlar hakkında daha fazla bilgi edinmek için bu sayfayı kitap halinde işaretleyerek veya [güncelleştirmelere abone](https://azure.microsoft.com/updates/?query=backup)olabilirsiniz.

## <a name="updates-summary"></a>Güncelleştirme Özeti

- Mart 2021
  - [Azure disk yedekleme artık genel kullanıma sunuldu](#azure-disk-backup-is-now-generally-available)
  - [Yedekleme merkezi genel kullanıma sunuldu](#backup-center-is-now-generally-available)
  - [Azure Backup için Arşiv katmanı desteği (önizlemede)](#archive-tier-support-for-azure-backup-in-preview)
- Şubat 2021
  - [Azure Blobları için yedekleme (önizlemede)](#backup-for-azure-blobs-in-preview)
- Ocak 2021
  - [Azure disk yedekleme (önizlemede)](#azure-disk-backup-in-preview)
  - [Müşteri tarafından yönetilen anahtarları kullanarak bekleyen şifreleme (genel kullanılabilirlik)](#encryption-at-rest-using-customer-managed-keys)
- Kasım 2020
  - [Azure dosya paylaşma (AFS) yedeklemesi için Azure Resource Manager şablonu](#azure-resource-manager-template-for-afs-backup)
  - [Azure VM 'lerinde SAP HANA veritabanları için artımlı yedeklemeler (önizlemede)](#incremental-backups-for-sap-hana-databases-in-preview)
- Eylül 2020
  - [Yedekleme merkezi (önizlemede)](#backup-center-in-preview)
  - [PostgreSQL için Azure veritabanı 'nı yedekleme (önizlemede)](#backup-azure-database-for-postgresql-in-preview)
  - [Seçmeli disk yedekleme ve geri yükleme](#selective-disk-backup-and-restore)
  - [Azure VM 'lerinde SQL Server ve SAP HANA veritabanları için çapraz bölge geri yükleme (önizlemede)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [En çok 32 diske sahip VM 'lerin yedeklenmesi için destek (genel kullanılabilirlik)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Azure VM 'lerinde SQL için Basitleştirilmiş yedekleme yapılandırma deneyimi](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [RHEL Azure sanal makinelerinde yedekleme SAP HANA (önizlemede)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [Yedekleme verileri için bölge yedekli depolama (ZRS) (önizlemede)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Azure VM 'lerinde SQL Server ve SAP HANA iş yükleri için geçici silme](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-is-now-generally-available"></a>Azure disk yedekleme artık genel kullanıma sunuldu

Azure Backup düzenli anlık görüntü oluşturmayı otomatikleştirerek ve yedekleme ilkesini kullanarak bunları yapılandırılmış süreler boyunca tutarak Azure yönetilen disklere anlık görüntü yaşam döngüsü yönetimi sunar.

Daha fazla bilgi için bkz. [Azure disk yedekleme 'ye genel bakış](disk-backup-overview.md).

## <a name="backup-center-is-now-generally-available"></a>Yedekleme merkezi genel kullanıma sunuldu

Yedekleme merkezi, tek bir merkezi konsoldan yedekleme yönetimini keşfetmenize, yönetmenize, izlemenize, çalıştırmanıza ve iyileştirmenize olanak tanıyarak veri koruma yönetimini ölçeklenebilir şekilde basitleştirir.

Daha fazla bilgi için bkz. [yedekleme merkezine genel bakış](backup-center-overview.md).

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Azure Backup için Arşiv katmanı desteği (önizlemede)

Azure Backup artık Azure sanal makineleri ve Azure sanal makinelerinde SQL Server için Arşiv katmanının kullanılabilirliği ile uzun süreli bekletme yedeklerinin maliyetini azaltmanıza olanak tanır.

Daha fazla bilgi için bkz. [Arşiv katmanı desteği (Önizleme)](archive-tier-support.md).

## <a name="backup-for-azure-blobs-in-preview"></a>Azure Blobları için yedekleme (önizlemede)

Blob 'lar için işlemsel yedekleme, blok bloblarınızı bozulmalar, blob silme işlemleri ve yanlışlıkla depolama hesabı silme gibi çeşitli veri kaybı senaryolarından korumanıza imkan tanıyan yönetilen, yerel bir veri koruma çözümüdür. Veriler yerel olarak kaynak depolama hesabının içinde depolanır ve gerektiğinde seçilen bir zaman noktasına kurtarılabilir. Bu nedenle, bloblarınızı korumak için basit, güvenli ve uygun maliyetli bir yol sağlar.

Blob 'Lar için işletimsel yedekleme, yedekleme merkezi ile tümleşir, diğer yedekleme yönetim özellikleri arasında, yedeklemeleri ölçekteki yönetmenize, izlemenize, çalıştırmanıza ve çözümlemenize yardımcı olabilecek tek bir cam bölmesi sağlamak için yedekleme merkezi ile tümleştirilir.

Daha fazla bilgi için bkz. [Azure Blobları için işlemsel yedeklemeye genel bakış (önizlemede)](blob-backup-overview.md).

## <a name="azure-disk-backup-in-preview"></a>Azure disk yedekleme (önizlemede)

Azure disk yedekleme, düzenli anlık görüntü oluşturmayı otomatikleştirerek ve yedekleme ilkesini kullanarak yapılandırılmış bir süre boyunca tutarak [Azure yönetilen diskler](../virtual-machines/managed-disks-overview.md) için anlık görüntü yaşam döngüsü yönetimi sağlayan bir anahtar oluşturma çözümü sunmaktadır. Disk anlık görüntülerini sıfır altyapı maliyetiyle ve özel betik oluşturmaya veya herhangi bir yönetim yüküne gerek kalmadan yönetebilirsiniz. Bu, günde birden çok yedekleme desteği olan [Artımlı anlık görüntüleri](../virtual-machines/disks-incremental-snapshots.md) kullanarak yönetilen bir diskin zaman içinde yedeğini alan çökme ile tutarlı bir yedekleme çözümüdür. Ayrıca, aynı zamanda aracılı bir çözümdür ve üretim uygulaması performansını etkilemez. Hem işletim sistemi hem de veri disklerinin (paylaşılan diskler dahil), çalışmakta olan bir Azure sanal makinesine bağlı olup olmadığına bakılmaksızın yedekleme ve geri yükleme desteği sağlar.

Daha fazla bilgi için bkz. [Azure disk yedekleme (önizlemede)](disk-backup-overview.md).

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları kullanarak bekleyen şifreleme

Müşteri tarafından yönetilen anahtarlar kullanılarak bekleyen şifreleme desteği genel kullanıma sunuldu. Bu sayede, Azure Anahtar Kasası 'nda depolanan anahtarları kullanarak kurtarma hizmetleri kasalarınızda yedekleme verilerini şifreleyebilirsiniz. Kurtarma Hizmetleri kasasındaki yedeklemeleri şifrelemek için kullanılan şifreleme anahtarı, kaynağı şifrelemek için kullanılan olanlardan farklı olabilir. Veriler, Key Vault depolanan anahtarlarınız kullanılarak korunan bir AES 256 tabanlı veri şifreleme anahtarı (DEK) kullanılarak korunur. Platform tarafından yönetilen anahtarlar kullanılarak şifrelemeye kıyasla (varsayılan olarak kullanılabilir), bu anahtarlar üzerinde daha fazla denetim sağlar ve uyumluluk gereksinimlerinizi daha iyi karşılayabilmeniz için size yardımcı olabilir.

Daha fazla bilgi için bkz. [müşteri tarafından yönetilen anahtarlar kullanılarak yedekleme verilerinin şifrelenmesi](encryption-at-rest-with-cmk.md).

## <a name="azure-resource-manager-template-for-afs-backup"></a>AFS yedeklemesi için Azure Resource Manager şablonu

Azure Backup artık Azure Resource Manager (ARM) şablonu kullanarak mevcut Azure dosya paylaşımları için yedeklemeyi yapılandırmayı destekliyor. Şablon, kurtarma hizmetleri kasası ve yedekleme ilkesi için uygun ayrıntıları belirterek mevcut bir Azure dosya paylaşımının korumasını yapılandırır. Bu, isteğe bağlı olarak yeni bir kurtarma hizmetleri kasası ve yedekleme ilkesi oluşturur ve dosya paylaşımının bulunduğu depolama hesabını kurtarma hizmetleri kasasına kaydeder.

Daha fazla bilgi için bkz. [Azure Backup için Azure Resource Manager şablonları](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>SAP HANA veritabanları için artımlı yedeklemeler (önizlemede)

Azure Backup artık Azure VM 'lerinde barındırılan SAP HANA veritabanları için artımlı yedeklemeleri desteklemektedir. Bu, SAP HANA verilerinizin daha hızlı ve daha verimli yedeklemeler sağlar.

Daha fazla bilgi için bkz. [yedekleme ilkesi oluşturma sırasında kullanılabilen çeşitli seçenekler](/sap-hana-faq-backup-azure-vm.yml#policy) ve [SAP HANA veritabanları için yedekleme ilkesi oluşturma](tutorial-backup-sap-hana-db.md#creating-a-backup-policy).

## <a name="backup-center-in-preview"></a>Yedekleme merkezi (önizlemede)

Azure Backup, bir merkezi konsolundan tüm yedeklemelerinizi yönetmek için yeni bir yerel yönetim özelliği etkinleştirdi. Yedekleme merkezi, Azure 'un yerel yönetim deneyimleri ile tutarlı bir şekilde ölçeklenebilir bir şekilde veri korumayı izleme, çalıştırma, yönetme ve iyileştirmenize olanak tanır.

Yedekleme merkezi ile abonelikler, konumlar, kaynak grupları, kasaların ve hatta Azure açık Thouse kullanan kiracılar arasında stoğunuzun toplu bir görünümünü alırsınız. Yedekleme merkezi aynı zamanda yedekleme, geri yükleme, ilkelerin oluşturulması veya kasaların tek bir yerden yapılandırılması gibi, yedeklemeyle ilgili etkinliklerinizi tetikleyebileceğiniz bir işlem merkezidir. Ayrıca, Azure Ilkesiyle sorunsuz tümleştirme sayesinde ortamınızı yönetebilir ve bir yedekleme perspektifinden uyumluluğu izleyebilirsiniz. Azure Backup 'e özgü, yerleşik Azure Ilkeleri de yedeklemeleri aynı ölçekte yapılandırmanıza olanak tanır.

Daha fazla bilgi için bkz. [yedekleme merkezine genel bakış](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql-in-preview"></a>PostgreSQL için Azure veritabanı 'nı yedekleme (önizlemede)

Azure Backup ve Azure veritabanı Hizmetleri, Azure PostgreSQL için kurumsal sınıf bir yedekleme çözümü oluşturmak üzere bir araya geliyor (şimdi önizlemede). Artık, yedeklemelerin 10 yıla kadar bekletilmesini sağlayan müşteri denetimli bir yedekleme ilkesiyle veri koruma ve uyumluluk ihtiyaçlarınızı karşılayabilirsiniz. Bu şekilde, yedekleme ve geri yükleme işlemlerini ayrı veritabanı düzeyinde yönetmeye yönelik ayrıntılı denetime sahip olursunuz. Benzer şekilde, bir PostgreSQL sürümüne veya blob depolama alanına kolayca geri yükleyebilirsiniz.

Daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı yedeklemesi](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Seçmeli disk yedekleme ve geri yükleme

Azure Backup, sanal makine yedekleme çözümünü kullanarak bir VM 'deki tüm disklerin (işletim sistemi ve veri) yedeklenmesini destekler. Artık, seçmeli diskler Yedekleme ve geri yükleme işlevselliğini kullanarak bir VM 'deki veri disklerinin bir alt kümesini yedekleyebilirsiniz. Bu, yedekleme ve geri yükleme gereksinimleriniz için verimli ve ekonomik bir çözüm sunar. Her kurtarma noktası yalnızca yedekleme işlemine dahil edilen diskleri içerir.

Daha fazla bilgi için bkz. [Azure sanal makineleri Için seçmeli disk yedekleme ve geri yükleme](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>SQL Server ve SAP HANA için çapraz bölge geri yüklemesi (önizlemede)

Bölgeler arası geri yükleme 'nin kullanıma sunulmasıyla birlikte, ortamınızda bir birincil bölgedeki gerçek kalma süresi sorunlarını azaltmak için, artık ikincil bir bölgedeki geri yüklemeleri başlatabilirsiniz. Bu, ikincil bölgenin tamamen müşterinin denetimi geri yüklemelerini sağlar. Azure Backup, bu geri yüklemeler için ikincil bölgeye çoğaltılan yedeklenen verileri kullanır.

Artık, Azure sanal makineler için çapraz bölge geri yükleme desteğinin yanı sıra, özelliği de Azure sanal makinelerinde SQL ve SAP HANA veritabanlarını geri yüklemek için genişletilmiştir.

Daha fazla bilgi için bkz. [SQL veritabanları Için çapraz bölge geri yükleme](restore-sql-database-azure-vm.md#cross-region-restore) ve [SAP HANA veritabanları Için çapraz bölge geri yükleme](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>En fazla 32 diski olan VM 'lerin yedeklenmesi için destek

Bu aşamada Azure Backup VM başına 16 yönetilen disk destekliyordu. Azure Backup, VM başına 32 ile yönetilen diskin yedeklenmesini desteklemektedir.

Daha fazla bilgi için [VM depolama desteği matrisine](backup-support-matrix-iaas.md#vm-storage-support)bakın.

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Azure VM 'lerinde SQL için daha basit yedekleme yapılandırması

Azure VM 'lerinde SQL Server yedeklemeleri yapılandırmak, Azure portal VM bölmesi ile tümleştirilmiş satır içi yedekleme yapılandırması sayesinde artık daha da kolaydır. Yalnızca birkaç adımda, mevcut tüm veritabanlarının yanı sıra gelecekte eklenen tüm veritabanlarını korumak için SQL Server yedeklemesini etkinleştirebilirsiniz.

Daha fazla bilgi için bkz. [VM bölmesinden bir SQL Server yedekleme](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>RHEL Azure sanal makinelerinde yedekleme SAP HANA (önizlemede)

Azure Backup, Azure için yerel yedekleme çözümüdür ve SAP tarafından geri sertifikalı olarak sertifikalıdır. Azure Backup, SAP HANA çalıştıran en yaygın Linux işletim sistemlerinden biri olan Red Hat Enterprise Linux (RHEL) için destek eklemiştir.

Daha fazla bilgi için [SAP HANA veritabanı yedekleme senaryosu destek matrisine](sap-hana-backup-support-matrix.md#scenario-support)bakın.

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>Yedekleme verileri için bölge yedekli depolama (ZRS) (önizlemede)

Azure depolama, değişen artıklık seçenekleriyle yüksek performans, yüksek kullanılabilirlik ve yüksek veri dayanıklılığı için harika bir denge sağlar. Azure Backup, yedeklemelerinizi yerel olarak yedekli depolama (LRS) ve coğrafi olarak yedekli depolama (GRS) olarak depolamak için bu avantajları, yedekleme verilerini de genişletmenizi sağlar. Artık, bölgesel olarak yedekli depolama (ZRS) desteğiyle ilgili ek dayanıklılık seçenekleri mevcuttur.

Daha fazla bilgi için bkz. [Kurtarma Hizmetleri Kasası için depolama yedekliliği ayarlama](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>SQL Server ve SAP HANA iş yükleri için geçici silme

Kötü amaçlı yazılım, fidye ve yetkisiz erişim gibi güvenlik sorunları hakkında sorunlar artıyor. Bu güvenlik sorunları hem para hem de veri bakımından maliyetli olabilir. Bu tür saldırılara karşı koruma sağlamak için Azure Backup, silme işleminden sonra bile yedekleme verilerini korumaya yardımcı olmak için güvenlik özellikleri sağlar.

Bu tür bir özellik, geçici bir DELETE. Kötü niyetli bir aktör bir yedeği silse (veya yedekleme verileri yanlışlıkla silinse), yedekleme verileri 14 ek gün boyunca tutulur ve bu yedekleme öğesinin veri kaybı olmadan kurtarılmasını sağlar. "Geçici silme" durumundaki yedekleme verileri için 14 günlük ek bekletme, size herhangi bir maliyet vermez.

Artık, Azure VM 'Leri için geçici silme desteğinin yanı sıra Azure VM 'lerinde SQL Server ve SAP HANA iş yükleri da geçici silme ile korunur.

Daha fazla bilgi için bkz. [Azure VM 'de SQL Server Için geçici silme ve Azure VM iş yükleri SAP HANA](soft-delete-sql-saphana-in-azure-vm.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Backup kılavuz ve en iyi uygulamalar](guidance-best-practices.md)