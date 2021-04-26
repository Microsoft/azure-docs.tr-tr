---
title: Azure Disk Depolama genel bakış
description: VM kullanırken sizin için depolama hesaplarını işleyen Azure yönetilen disklere genel bakış.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: contperf-fy21q1
ms.openlocfilehash: eea5c800d7aa9c8d1e6c0c507136b86ab8bf21f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604041"
---
# <a name="introduction-to-azure-managed-disks"></a>Azure yönetilen disklerine giriş

Azure yönetilen diskler, Azure tarafından yönetilen ve Azure sanal makinelerle kullanılan blok düzeyinde depolama birimleridir. Yönetilen diskler, şirket içi sunucuda bulunan ancak sanallaştırılmış bir fiziksel disk gibidir. Yönetilen diskler ile, tüm yapmanız gerekir disk boyutunu, disk türünü ve diski temin etmek için gereklidir. Diski sağladığınızda Azure, kalanı işler.

Kullanılabilir disk türleri Ultra disklerdir, Premium katı hal sürücüleri (SSD), standart SSD 'Ler ve standart sabit disk sürücüleri (HDD). Her bir disk türü hakkında daha fazla bilgi için bkz. [IaaS VM 'leri için disk türü seçme](disks-types.md).

## <a name="benefits-of-managed-disks"></a>Yönetilen disklerin avantajları

Yönetilen diskleri kullanarak kazanmanızın avantajlarından bazılarını inceleyelim.

### <a name="highly-durable-and-available"></a>Yüksek oranda dayanıklı ve kullanılabilir

Yönetilen diskler% 99,999 kullanılabilirlik için tasarlanmıştır. Yönetilen diskler bu kullanılabilirlik düzeyini mümkün kılmak için verilerinizin üç kopyasını sunar ve yüksek dayanıklılık sağlar. Bir veya iki çoğaltmada sorun olsa dahi diğer çoğaltmalar verilerinizin kalıcı ve hatalara karşı dayanıklı olmasına yardımcı olur. Bu mimari, sektördeki bir hizmet olarak altyapı (IaaS) diskleri için Azure 'un kurumsal düzeyde dayanıklılık kazandığını, sektör lideri %0 yıllık hata oranı ile sürekli olarak sunmamıza yardımcı oldu.

### <a name="simple-and-scalable-vm-deployment"></a>Basit ve ölçeklenebilir VM dağıtımı

Yönetilen diskleri kullanarak, her bölge için bir abonelikte en fazla 50.000 VM **diski** oluşturabilirsiniz, böylece tek bir abonelikte binlerce **VM** oluşturabilirsiniz. Bu özellik, bir market görüntüsü kullanarak bir sanal makine ölçek kümesinde en fazla 1.000 VM oluşturmanıza izin vererek [sanal makine ölçek kümelerinin](../virtual-machine-scale-sets/overview.md) ölçeklenebilirliğini de artırır.

### <a name="integration-with-availability-sets"></a>Kullanılabilirlik kümeleriyle tümleştirme

Yönetilen diskler, [bir kullanılabilirlik kümesindeki VM](./availability-set-overview.md) disklerinin tek bir başarısızlık noktasını önlemek için birbirinden yeterince yalıtılmış olmasını sağlamak üzere kullanılabilirlik kümeleriyle tümleştirilir. Diskler otomatik olarak farklı depolama ölçek birimlerine (damgalar) yerleştirilir. Bir damga, donanım veya yazılım arızası nedeniyle başarısız olursa, yalnızca bu damgalar üzerinde disklere sahip VM örnekleri başarısız olur. Örneğin, beş VM 'de çalışan bir uygulamanız olduğunu ve VM 'Lerin bir kullanılabilirlik kümesinde olduğunu varsayalım. Bu VM 'Lerin disklerin hepsi aynı damgada depolanmayacak, bu nedenle bir damga daha sonra uygulamanın diğer örnekleri çalışmaya devam eder.

### <a name="integration-with-availability-zones"></a>Kullanılabilirlik Alanları ile tümleştirme

Yönetilen diskler [kullanılabilirlik alanları](../availability-zones/az-overview.md)destekler, bu, uygulamalarınızı veri merkezi hatalarından koruyan yüksek kullanılabilirliğe sahip bir tekliftir. Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılığı güvence altına almak için etkinleştirilmiş tüm bölgelerde en az üç ayrı alan vardır. Azure, Kullanılabilirlik Alanları sayesinde sektörün en iyi% 99,99 VM çalışma süresi SLA 'sını sunmaktadır.

### <a name="azure-backup-support"></a>Azure Backup desteği

Bölgesel felate karşı korumak için [Azure Backup](../backup/backup-overview.md) , zaman tabanlı yedeklemeler ve yedekleme bekletme ilkeleriyle bir yedekleme işi oluşturmak için kullanılabilir. Bu, sanal makine veya yönetilen disk geri yüklemeler gerçekleştirmenizi sağlar. Şu anda Azure Backup, 32 tebibyte (TiB) disklere kadar disk boyutlarını destekler. Azure VM yedekleme desteği hakkında [daha fazla bilgi edinin](../backup/backup-support-matrix-iaas.md) .

#### <a name="azure-disk-backup"></a>Azure Disk Yedekleme

Azure Backup, yönetilen disklerde verilerinizi koruyan yerel, bulut tabanlı bir yedekleme çözümü olarak Azure disk yedeklemesi (Önizleme) sunar. Bu, birkaç adımda yönetilen diskler için korumayı yapılandırmanızı sağlayan basit, güvenli ve ekonomik bir çözümdür. Azure disk yedekleme, anlık görüntülerin düzenli olarak oluşturulmasını otomatikleştirerek yönetilen diskler için anlık görüntü yaşam döngüsü yönetimi sağlayan ve yedekleme ilkesini kullanarak yapılandırılan süre için bir anahtar, bir çözüm sunar. Azure disk yedekleme hakkında daha fazla bilgi için bkz. [Azure disk yedekleme 'ye Genel Bakış (önizlemede)](../backup/disk-backup-overview.md).

### <a name="granular-access-control"></a>Ayrıntılı erişim denetimi

Yönetilen bir disk için belirli izinleri bir veya daha fazla kullanıcıya atamak üzere [Azure rol tabanlı erişim denetimi 'ni (Azure RBAC)](../role-based-access-control/overview.md) kullanabilirsiniz. Yönetilen diskler, okuma, yazma (oluşturma/güncelleştirme), silme ve disk için [paylaşılan erişim imzası (SAS) URI 'sini](../storage/common/storage-sas-overview.md) alma gibi çeşitli işlemler sunar. Yalnızca bir kişinin işini gerçekleştirmesi için ihtiyaç duyacağı işlemlere erişim izni verebilirsiniz. Örneğin, bir kişinin yönetilen bir diski bir depolama hesabına kopyalamasını istemiyorsanız, bu yönetilen disk için dışarı aktarma eylemine erişim izni vermemeye izin vermeyi tercih edebilirsiniz. Benzer şekilde, bir kişinin yönetilen bir diski kopyalamak için SAS URI 'sini kullanmasını istemiyorsanız, bu izni yönetilen diske vermemenizi tercih edebilirsiniz.

### <a name="upload-your-vhd"></a>VHD 'nizi karşıya yükleyin

Doğrudan karşıya yükleme, VHD 'nizi Azure yönetilen diskine aktarmayı kolaylaştırır. Daha önce, verilerinizi bir depolama hesabına hazırlama dahil daha fazla ilgili bir işlemi izlemeniz gerekiyordu. Şimdi, daha az adım vardır. Şirket içi VM 'Lere Azure 'a yükleme, büyük yönetilen disklere yükleme, yedekleme ve geri yükleme işlemi basitleştirilmiştir. Ayrıca, yönetilen disklere doğrudan VM 'lere iliştirmeden veri yükleme olanağı vererek maliyeti azaltır. VHD 'leri boyut olarak 32 TiB 'ye kadar yüklemek için doğrudan karşıya yükleme kullanabilirsiniz.

VHD 'nizi Azure 'a aktarmayı öğrenmek için bkz. [CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) veya [PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md) makaleleri.

## <a name="security"></a>Güvenlik

### <a name="private-links"></a>Özel bağlantılar

Yönetilen diskler için özel bağlantı desteği, ağınıza dahili bir yönetilen diski içeri veya dışarı aktarmak için kullanılabilir. Özel bağlantılar, eklenmemiş yönetilen diskler ve bu verileri bölgesel genişletme, olağanüstü durum kurtarma ve Forli analiz için diğer bölgelere dışarı aktarmak için kullanabileceğiniz bir zamana bağlı paylaşılan erişim Imzası (SAS) URI 'SI oluşturmanıza olanak tanır. Ayrıca, bir VHD 'yi Şirket içindeki boş bir diske doğrudan yüklemek için SAS URI 'sini de kullanabilirsiniz. Artık, yönetilen disklerin içeri ve dışarı aktarılmasını kısıtlamak için [özel bağlantılardan](../private-link/private-link-overview.md) yararlanarak yalnızca Azure sanal ağınızda yer alabilir. Özel bağlantılar verilerinizin yalnızca güvenli Microsoft omurga ağı içinde hareket etmenizi sağlamanıza olanak tanır.

Yönetilen bir diski içeri veya dışarı aktarmaya yönelik özel bağlantıları nasıl etkinleştireceğinizi öğrenmek için bkz. [CLI](linux/disks-export-import-private-links-cli.md) veya [Portal](disks-enable-private-links-for-import-export-portal.md) makaleleri.

### <a name="encryption"></a>Şifreleme

Yönetilen diskler iki farklı şifreleme türü sunar. Birincisi, depolama hizmeti tarafından gerçekleştirilen sunucu tarafı şifrelemesi (SSE). İkincisi, sanal makinelerinize yönelik işletim sistemi ve veri diskleri üzerinde etkinleştirebileceğiniz Azure disk şifrelemesi (ADE) ' dir.

#### <a name="server-side-encryption"></a>Sunucu tarafı şifrelemesi

Sunucu tarafı şifreleme, bekleyen şifreleme sağlar ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamak için verilerinizi korur. Yönetilen disklerin kullanılabildiği tüm bölgelerde tüm yönetilen diskler, anlık görüntüler ve görüntüler için sunucu tarafı şifreleme varsayılan olarak etkindir. (Konakta şifrelemeyi etkinleştirmediğiniz takdirde, diğer yandan geçici diskler sunucu tarafı şifreleme tarafından şifrelenmez; bkz. [disk rolleri: geçici diskler](#temporary-disk)).

Azure 'un anahtarlarınızı sizin için yönetmesine izin verebilir, bunlar platform tarafından yönetilen anahtarlardır ya da anahtarları kendiniz yönetebilirsiniz. Bunlar, müşteri tarafından yönetilen anahtarlardır. Ayrıntılar için [Azure disk depolama makalesinin sunucu tarafı şifrelemesini](./disk-encryption.md) ziyaret edin.


#### <a name="azure-disk-encryption"></a>Azure Disk Şifrelemesi

Azure disk şifrelemesi, bir IaaS sanal makinesi tarafından kullanılan işletim sistemini ve veri disklerini şifrelemenizi sağlar. Bu şifreleme yönetilen diskler içerir. Windows için Sürücüler, sektör standardı BitLocker şifreleme teknolojisi kullanılarak şifrelenir. Linux için diskler DM-Crypt teknolojisi kullanılarak şifrelenir. Şifreleme işlemi, disk şifreleme anahtarlarını denetlemenize ve yönetmenize olanak tanımak için Azure Key Vault ile tümleşiktir. Daha fazla bilgi için bkz. [Linux VM 'leri Için Azure disk şifrelemesi](linux/disk-encryption-overview.md) veya [Windows VM 'Leri Için Azure disk şifrelemesi](windows/disk-encryption-overview.md).

## <a name="disk-roles"></a>Disk rolleri

Azure 'da üç ana disk rolü vardır: veri diski, işletim sistemi diski ve geçici disk. Bu roller, sanal makinenize bağlı disklerle eşlenir.

![Eylemde bulunan disk rolleri](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Veri diski

Veri diski, uygulama verilerini depolamak için bir sanal makineye bağlı yönetilen bir disktir veya saklamanız gereken diğer verileri sağlar. Veri diskleri SCSI sürücüler olarak kaydedilir ve seçtiğiniz bir harfle etiketlenir. Her veri diski maksimum 32.767 Gibibyte (gib) kapasiteye sahiptir. Sanal makinenin boyutu, kendisine ekleyebileceğiniz veri disklerinin sayısını ve diskleri barındırmak için kullanabileceğiniz depolama türünü belirler.

### <a name="os-disk"></a>İşletim sistemi diski

Her sanal makinenin bir bağlı işletim sistemi diski vardır. Bu işletim sistemi diskinde, VM oluşturulduğunda seçilen, önceden yüklenmiş bir işletim sistemi vardır. Bu disk, önyükleme birimini içerir.

Bu diskin en fazla 4.095 GiB kapasitesi vardır.

### <a name="temporary-disk"></a>Geçici disk

Çoğu VM, yönetilen bir disk olmayan geçici bir disk içerir. Geçici disk, uygulamalar ve süreçler için kısa vadeli depolama sağlar ve yalnızca sayfa veya takas dosyaları gibi verileri depolamak için tasarlanmıştır. Geçici diskteki veriler, [bakım olayı](./understand-vm-reboots.md) sırasında veya [bir VM 'yi](/troubleshoot/azure/virtual-machines/redeploy-to-new-node-windows?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)yeniden dağıttığınızda kaybolabilir. Sanal makinenin başarılı bir standart yeniden başlatması sırasında, geçici diskteki veriler devam edecektir. Geçici diskler olmadan VM 'Ler hakkında daha fazla bilgi için, bkz. [Yerel geçici disk Içermeyen Azure VM boyutları](azure-vms-no-temp-disk.md).

Azure Linux VM 'lerinde geçici disk genellikle/dev/sdb ve Windows VM 'lerinde geçici disk D: varsayılan olarak olur. Konakta şifrelemeyi etkinleştirmediğiniz takdirde geçici disk sunucu tarafı şifrelemesi tarafından şifrelenmez.

## <a name="managed-disk-snapshots"></a>Yönetilen disk anlık görüntüleri

Yönetilen disk anlık görüntüsü, varsayılan olarak standart yönetilen disk olarak depolanan bir yönetilen diskin salt okunurdur, kilitlenmeyle tutarlı bir tam kopyasıdır. Anlık görüntülerle, yönetilen disklerinizi dilediğiniz zaman bir noktada yedekleyebilirsiniz. Bu anlık görüntüler, kaynak diskten bağımsız olarak bulunur ve yeni yönetilen diskler oluşturmak için kullanılabilir. 

Anlık görüntüler, kullanılan boyuta göre faturalandırılır. Örneğin, sağlanan 64 GiB kapasitesine sahip bir yönetilen diskin anlık görüntüsünü ve 10 GiB 'nin gerçek kullanılan veri boyutunu oluşturursanız, bu anlık görüntü yalnızca 10 GiB 'nin kullanılan veri boyutu için faturalandırılır. [Azure kullanım raporuna](../cost-management-billing/understand/review-individual-bill.md)bakarak anlık görüntülerinizin kullanılan boyutunu görebilirsiniz. Örneğin, bir anlık görüntünün kullanılan veri boyutu 10 GiB ise **günlük** kullanım raporu, tüketilen miktar olarak 10 GiB/(31 gün) = 0,3226 gösterir.

Yönetilen diskler için anlık görüntüler oluşturma hakkında daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

- [Windows 'da yönetilen diskin anlık görüntüsünü oluşturma](windows/snapshot-copy-managed-disk.md)
- [Linux 'ta yönetilen diskin anlık görüntüsünü oluşturma](linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Görüntüler

Yönetilen diskler ayrıca yönetilen bir özel görüntü oluşturmayı destekler. Bir depolama hesabındaki özel VHD 'nizden veya doğrudan Genelleştirilmiş (Sysprep uygulanmış) bir VM 'den görüntü oluşturabilirsiniz. Bu işlem tek bir görüntüyü yakalar. Bu görüntü, hem işletim sistemi hem de veri diskleri dahil olmak üzere bir VM ile ilişkili tüm yönetilen diskleri içerir. Bu yönetilen özel görüntü, herhangi bir depolama hesabını kopyalamaya veya yönetmeye gerek olmadan özel görüntünüzü kullanarak yüzlerce VM oluşturulmasına olanak sağlar.

Görüntü oluşturma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure 'da genelleştirilmiş bir VM 'nin yönetilen görüntüsünü yakalama](windows/capture-image-resource.md)
- [Azure CLI'yi kullanarak Linux sanal makinelerini genelleştirme ve yakalama](linux/capture-image.md)

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

İstediğiniz performansı elde edebilmeniz için VM + disk yapılandırmalarının iyileştirmesini sağlayan en iyi yöntemleri öğrenmek için [yüksek performanslı](premium-storage-performance.md) makalemize bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bir videonun yönetilen diskler üzerinde daha ayrıntılı bir şekilde gitmesini istiyorsanız, bkz. [yönetilen disklerle daha Iyi Azure VM esnekliği](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).

Azure 'un sunduğu ayrı disk türleri hakkında daha fazla bilgi edinin. bu tür, gereksinimlerinize uygun bir uyum ve disk türlerindeki makalemizdeki performans hedefleri hakkında bilgi edinin.

> [!div class="nextstepaction"]
> [IaaS VM’leri için disk türü seçme](disks-types.md)