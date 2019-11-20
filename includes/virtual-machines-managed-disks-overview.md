---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7ceff623c6559ef5e929d6d5bff9e07cca9039d2
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796273"
---
## <a name="benefits-of-managed-disks"></a>Yönetilen disklerin avantajları

Yönetilen diskleri kullanarak kazanmanızın avantajlarından bazılarını inceleyelim.

### <a name="highly-durable-and-available"></a>Yüksek oranda dayanıklı ve kullanılabilir

Yönetilen diskler% 99,999 kullanılabilirlik için tasarlanmıştır. Yönetilen diskler, verilerinizin üç kopyasını sunarak, yüksek dayanıklılık elde etmenizi sağlar. Çoğaltmaların birinde hatta ikisinde sorunlarla karşılaşılırsa, kalan çoğaltmalar verilerinizin kalıcı olmasını ve başarısızlıklara karşı yüksek tolerans gösterilmesini sağlar. Bu mimari, sektördeki bir hizmet olarak altyapı (IaaS) diskleri için Azure 'un kurumsal düzeyde dayanıklılık kazandığını, sektör lideri %0 yıllık hata oranı ile sürekli olarak sunmamıza yardımcı oldu.

### <a name="simple-and-scalable-vm-deployment"></a>Basit ve ölçeklenebilir VM dağıtımı

Yönetilen diskleri kullanarak, her bölge için bir abonelikte en fazla 50.000 VM **diski** oluşturabilirsiniz, böylece tek bir abonelikte binlerce **VM** oluşturabilirsiniz. Bu özellik, bir market görüntüsü kullanarak bir sanal makine ölçek kümesinde en fazla 1.000 VM oluşturmanıza izin vererek [sanal makine ölçek kümelerinin](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) ölçeklenebilirliğini de artırır.

### <a name="integration-with-availability-sets"></a>Kullanılabilirlik kümeleriyle tümleştirme

Yönetilen diskler, [bir kullanılabilirlik kümesindeki VM](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) disklerinin tek bir başarısızlık noktasını önlemek için birbirinden yeterince yalıtılmış olmasını sağlamak üzere kullanılabilirlik kümeleriyle tümleştirilir. Diskler otomatik olarak farklı depolama ölçek birimlerine (damgalar) yerleştirilir. Bir damga, donanım veya yazılım arızası nedeniyle başarısız olursa, yalnızca bu damgalar üzerinde disklere sahip VM örnekleri başarısız olur. Örneğin, beş VM 'de çalışan bir uygulamanız olduğunu ve VM 'Lerin bir kullanılabilirlik kümesinde olduğunu varsayalım. Bu VM 'Lerin disklerin hepsi aynı damgada depolanmayacak, bu nedenle bir damga daha sonra uygulamanın diğer örnekleri çalışmaya devam eder.

### <a name="integration-with-availability-zones"></a>Kullanılabilirlik Alanları ile tümleştirme

Yönetilen diskler [kullanılabilirlik alanları](../articles/availability-zones/az-overview.md)destekler, bu, uygulamalarınızı veri merkezi hatalarından koruyan yüksek kullanılabilirliğe sahip bir tekliftir. Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılık sağlamak için, tüm etkin bölgelerde en az üç ayrı bölge vardır. Azure, Kullanılabilirlik Alanları sayesinde sektörün en iyi% 99,99 VM çalışma süresi SLA 'sını sunmaktadır.

### <a name="azure-backup-support"></a>Azure Backup desteği

Bölgesel felate karşı korumak için [Azure Backup](../articles/backup/backup-overview.md) , zaman tabanlı yedeklemeler ve yedekleme bekletme ilkeleriyle bir yedekleme işi oluşturmak için kullanılabilir. Bu, ' de kolay VM geri 'leri gerçekleştirmenize olanak tanır. Şu anda Azure Backup en fazla dört tebibyte (TiB) disk boyutunu destekler.  Azure Backup yönetilen disklerin yedeklenmesini ve geri yüklenmesini destekler. Azure VM yedekleme desteği hakkında [daha fazla bilgi edinin](../articles/backup/backup-support-matrix-iaas.md) .

### <a name="granular-access-control"></a>Ayrıntılı erişim denetimi

[Azure rol tabanlı erişim denetimi 'ni (RBAC)](../articles/role-based-access-control/overview.md) , yönetilen bir disk için bir veya daha fazla kullanıcıya belirli izinler atamak üzere kullanabilirsiniz. Yönetilen diskler, okuma, yazma (oluşturma/güncelleştirme), silme ve disk için [paylaşılan erişim imzası (SAS) URI 'sini](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) alma gibi çeşitli işlemler sunar. Yalnızca bir kişinin işini gerçekleştirmesi için ihtiyaç duyacağı işlemlere erişim izni verebilirsiniz. Örneğin, bir kişinin yönetilen bir diski bir depolama hesabına kopyalamasını istemiyorsanız, bu yönetilen disk için dışarı aktarma eylemine erişim izni vermemeye izin vermeyi tercih edebilirsiniz. Benzer şekilde, bir kişinin yönetilen bir diski kopyalamak için SAS URI 'sini kullanmasını istemiyorsanız, bu izni yönetilen diske vermemenizi tercih edebilirsiniz.

### <a name="upload-your-vhd"></a>VHD 'nizi karşıya yükleyin

 Doğrudan karşıya yükleme, VHD 'nizi Azure yönetilen diskine aktarmayı kolaylaştırır. Daha önce, verilerinizi bir depolama hesabına hazırlama dahil daha fazla ilgili bir işlemi izlemeniz gerekiyordu. Şimdi, daha az adım vardır. Şirket içi VM 'Lere Azure 'a yükleme, büyük yönetilen disklere yükleme, yedekleme ve geri yükleme işlemi basitleştirilmiştir. Ayrıca, yönetilen disklere doğrudan VM 'lere iliştirmeden veri yükleme olanağı vererek maliyeti azaltır. VHD 'leri boyut olarak 32 TiB 'ye kadar yüklemek için doğrudan karşıya yükleme kullanabilirsiniz.

 VHD 'nizi Azure 'a aktarmayı öğrenmek için bkz. [CLI](../articles/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli.md) veya [PowerShell](../articles/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md) makaleleri.

## <a name="encryption"></a>Şifreleme

Yönetilen diskler iki farklı şifreleme türü sunar. Birincisi, depolama hizmeti tarafından gerçekleştirilen sunucu tarafı şifrelemesi (SSE). İkincisi, sanal makinelerinize yönelik işletim sistemi ve veri diskleri üzerinde etkinleştirebileceğiniz Azure disk şifrelemesi (ADE) ' dir.

### <a name="server-side-encryption"></a>Sunucu tarafı şifrelemesi

[Azure sunucu tarafı şifrelemesi](../articles/virtual-machines/windows/disk-encryption.md) , bekleyen şifreleme sağlar ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamak için verilerinizi korur. Yönetilen disklerin kullanılabildiği tüm bölgelerde tüm yönetilen diskler, anlık görüntüler ve görüntüler için sunucu tarafı şifreleme varsayılan olarak etkindir. Azure 'un anahtarlarınızı sizin için yönetmesine izin verebilir, bunlar platform tarafından yönetilen anahtarlardır ya da anahtarları kendiniz yönetebilirsiniz. Bunlar, müşteri tarafından yönetilen anahtarlardır (Önizleme). Daha fazla ayrıntı için [yönetilen DISKLER SSS sayfasını](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) ziyaret edin.

### <a name="azure-disk-encryption"></a>Azure Disk Şifrelemesi

Azure disk şifrelemesi, bir IaaS sanal makinesi tarafından kullanılan işletim sistemini ve veri disklerini şifrelemenizi sağlar. Bu şifreleme yönetilen diskler içerir. Windows için Sürücüler, sektör standardı BitLocker şifreleme teknolojisi kullanılarak şifrelenir. Linux için diskler DM-Crypt teknolojisi kullanılarak şifrelenir. Şifreleme işlemi, disk şifreleme anahtarlarını denetlemenize ve yönetmenize olanak tanımak için Azure Key Vault ile tümleşiktir. Daha fazla bilgi için bkz. [IaaS VM 'leri Için Azure disk şifrelemesi](../articles/security/azure-security-disk-encryption-overview.md).

## <a name="disk-roles"></a>Disk rolleri

Azure 'da üç ana disk rolü vardır: veri diski, işletim sistemi diski ve geçici disk. Bu roller, sanal makinenize bağlı disklerle eşlenir.

![Eylemde bulunan disk rolleri](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Veri diski

Veri diski, uygulama verilerini depolamak için bir sanal makineye bağlı yönetilen bir disktir veya saklamanız gereken diğer verileri sağlar. Veri diskleri SCSI sürücüler olarak kaydedilir ve seçtiğiniz bir harfle etiketlenir. Her veri diski maksimum 32.767 Gibibyte (gib) kapasiteye sahiptir. Sanal makinenin boyutu, kendisine ekleyebileceğiniz veri disklerinin sayısını ve diskleri barındırmak için kullanabileceğiniz depolama türünü belirler.

### <a name="os-disk"></a>İşletim sistemi diski

Her sanal makinenin bir bağlı işletim sistemi diski vardır. Bu işletim sistemi diskinde, VM oluşturulduğunda seçilen, önceden yüklenmiş bir işletim sistemi vardır. Bu disk, önyükleme birimini içerir.

Bu diskin en fazla 2.048 GiB kapasitesi vardır.

### <a name="temporary-disk"></a>Geçici disk

Her VM, yönetilen bir disk olmayan geçici bir disk içerir. Geçici disk, uygulamalar ve süreçler için kısa vadeli depolama sağlar ve yalnızca sayfa veya takas dosyaları gibi verileri depolamak için tasarlanmıştır. Geçici diskteki veriler, [bakım olayı](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) olayı sırasında veya [bir VM 'yi](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)yeniden dağıttığınızda kaybolabilir. Azure Linux VM 'lerinde geçici disk varsayılan olarak/dev/sdb ve Windows VM 'lerinde geçici disk D: varsayılan olarak olur. Sanal makinenin başarılı bir standart yeniden başlatması sırasında, geçici diskteki veriler devam edecektir.

## <a name="managed-disk-snapshots"></a>Yönetilen disk anlık görüntüleri

Yönetilen disk anlık görüntüsü, varsayılan olarak standart yönetilen disk olarak depolanan bir yönetilen diskin salt okunurdur, kilitlenmeyle tutarlı bir tam kopyasıdır. Anlık görüntülerle, yönetilen disklerinizi dilediğiniz zaman bir noktada yedekleyebilirsiniz. Bu anlık görüntüler, kaynak diskten bağımsız olarak bulunur ve yeni yönetilen diskler oluşturmak için kullanılabilir. 

Anlık görüntüler, kullanılan boyuta göre faturalandırılır. Örneğin, sağlanan 64 GiB kapasitesine sahip bir yönetilen diskin anlık görüntüsünü ve 10 GiB 'nin gerçek kullanılan veri boyutunu oluşturursanız, bu anlık görüntü yalnızca 10 GiB 'nin kullanılan veri boyutu için faturalandırılır. [Azure kullanım raporuna](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)bakarak anlık görüntülerinizin kullanılan boyutunu görebilirsiniz. Örneğin, bir anlık görüntünün kullanılan veri boyutu 10 GiB ise **günlük** kullanım raporu, tüketilen miktar olarak 10 GiB/(31 gün) = 0,3226 gösterir.

Yönetilen diskler için anlık görüntüler oluşturma hakkında daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [Windows 'da yönetilen diskin anlık görüntüsünü oluşturma](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Linux 'ta yönetilen diskin anlık görüntüsünü oluşturma](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Görüntüler

Yönetilen diskler ayrıca yönetilen bir özel görüntü oluşturmayı destekler. Bir depolama hesabındaki özel VHD 'nizden veya doğrudan Genelleştirilmiş (Sysprep uygulanmış) bir VM 'den görüntü oluşturabilirsiniz. Bu işlem tek bir görüntüyü yakalar. Bu görüntü, hem işletim sistemi hem de veri diskleri dahil olmak üzere bir VM ile ilişkili tüm yönetilen diskleri içerir. Bu yönetilen özel görüntü, herhangi bir depolama hesabını kopyalamaya veya yönetmeye gerek olmadan özel görüntünüzü kullanarak yüzlerce VM oluşturulmasına olanak sağlar.

Görüntü oluşturma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure 'da genelleştirilmiş bir VM 'nin yönetilen görüntüsünü yakalama](../articles/virtual-machines/windows/capture-image-resource.md)
* [Azure CLI'yi kullanarak Linux sanal makinelerini genelleştirme ve yakalama](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Anlık görüntülere karşı görüntüler

Görüntüler ve anlık görüntüler arasındaki farkı anlamak önemlidir. Yönetilen disklerle, serbest bırakılmış olan genelleştirilmiş bir VM 'nin görüntüsünü alabilirsiniz. Bu görüntü, VM 'ye bağlı tüm diskleri içerir. Bu görüntüyü kullanarak bir VM oluşturabilirsiniz ve tüm diskleri içerir.

Anlık görüntü, anlık görüntünün alındığı zaman noktasındaki bir diskin kopyasıdır. Yalnızca bir disk için geçerlidir. Bir disk (işletim sistemi diski) olan bir VM varsa, anlık görüntüyü veya görüntüsünü alabilir ve anlık görüntüden ya da görüntüden bir VM oluşturabilirsiniz.

Bir anlık görüntü, içerdiği bir disk dışında herhangi bir diskin farkında değildir. Bu, dizme gibi birden çok diskin koordinasyonunu gerektiren senaryolarda kullanılmasını soruna neden olur. Anlık görüntülerin birbirleriyle koordine kurabilmesi gerekir ve bu, şu anda desteklenmemektedir.

## <a name="disk-allocation-and-performance"></a>Disk ayırma ve performans

Aşağıdaki diyagramda, üç düzeyli bir sağlama sistemi kullanılarak, diskler için gerçek zamanlı bant genişliği ve ıOPS gösterilmektedir:

![Bant genişliği ve ıOPS ayırmayı gösteren üç düzey sağlama sistemi](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

İlk düzey sağlama, disk başına ıOPS ve bant genişliği atamasını belirler.  İkinci düzeyde, işlem sunucusu ana bilgisayarı SSD sağlama işlemini uygular ve yalnızca, önbelleğe alma (ReadWrite ve salt okunur) diskler ve yerel ve geçici disklere dahil olmak üzere yalnızca sunucunun SSD 'de depolanan verilere uygulanır. Son olarak, VM ağı sağlama işlemi, işlem konağının Azure depolama 'nın arka ucuna gönderdiği herhangi bir g/ç için üçüncü düzeyde gerçekleşir. Bu şemayla, VM 'nin performansı, sanal makinenin yerel SSD 'yi nasıl kullandığı, eklenen disklerin sayısı ve bağlı olduğu disklerin performans ve önbelleğe alma türü gibi çeşitli faktörlere bağlıdır.

Bu sınırlamalara örnek olarak, bir Standard_DS1v1 VM 'nin, SSD ve ağ düzeylerindeki limitlerin dışında, önbelleğe alınıp alınmadığı P30 bir diskin 5.000 ıOPS 'sini elde etmesinin engellenmiş olması önlenir:

![Standard_DS1v1 örnek ayırma](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Azure, ağ trafiğinin diğer düşük önceliğine göre önceliği alan, disk trafiği için öncelikli ağ kanalını kullanır. Bu, disklerin ağ çekişmelerinde beklenen performansını korumalarına yardımcı olur. Benzer şekilde, Azure depolama, kaynak çekişmelerini ve diğer sorunları otomatik yük dengeleme ile arka planda işler. Azure depolama, bir disk oluştururken gerekli kaynakları ayırır ve trafik düzeyini işlemek için kaynakların proaktif ve reaktif dengelemesini uygular. Bu, disklerin beklenen ıOPS ve üretilen iş hedeflerine dayanmasını sağlar. Gerektiğinde performans ve kurulum uyarılarını izlemek için VM düzeyi ve disk düzeyi ölçümlerini kullanabilirsiniz.

İstediğiniz performansı elde edebilmeniz için VM + disk yapılandırmalarının iyileştirmesini sağlayan en iyi yöntemleri öğrenmek için [yüksek performanslı](../articles/virtual-machines/windows/premium-storage-performance.md) makalemize bakın.

## <a name="next-steps"></a>Sonraki adımlar

Azure 'un sunduğu ayrı disk türleri hakkında daha fazla bilgi edinin. bu tür, gereksinimlerinize uygun bir uyum ve disk türlerindeki makalemizdeki performans hedefleri hakkında bilgi edinin.
