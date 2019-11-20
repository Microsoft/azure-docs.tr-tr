---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f30518c3bfc9876cbddaf8295ff9e8b667a70200
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014542"
---
## <a name="overview"></a>Genel Bakış
Azure depolama, Blobların anlık görüntülerini alma özelliğini sağlar. Anlık görüntüler blob durumunu zaman içinde yakalar. Bu makalede, anlık görüntüleri kullanarak sanal makine disklerinin yedeklerini koruyabilmeniz için bir senaryo açıklanır. Azure Backup ve kurtarma hizmeti 'ni kullanmayı ve sanal makine diskleriniz için özel bir yedekleme stratejisi oluşturmayı tercih ettiğinizde bu yöntemi kullanabilirsiniz.

Azure sanal makine diskleri, Azure depolama 'da sayfa Blobları olarak depolanır. Bu makaledeki sanal makine diskleri için bir yedekleme stratejisi açıkladık, sayfa Blobları bağlamında anlık görüntülere başvurduk. Anlık görüntüler hakkında daha fazla bilgi edinmek için [bir Blobun anlık görüntüsünü oluşturma](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)konusuna bakın.

## <a name="what-is-a-snapshot"></a>Anlık görüntü nedir?
Blob anlık görüntüsü, bir Blobun zaman içinde yakalanan salt okunurdur. Anlık görüntü oluşturulduktan sonra okunabilir, kopyalanabilir veya silinebilir, ancak değiştirilmez. Anlık görüntüler, bir blobu zaman içinde göründüğü şekilde yedeklemenin bir yolunu sağlar. REST sürüm 2015-04-05 ' e kadar tam anlık görüntüleri kopyalama imkanına sahip olursunuz. REST sürüm 2015-07-08 ve üzeri ile Artımlı anlık görüntüleri de kopyalayabilirsiniz.

## <a name="full-snapshot-copy"></a>Tam anlık görüntü kopyası
Anlık görüntüler, temel Blobun yedeklerini tutmak için bir blob olarak başka bir depolama hesabına kopyalanabilir. Ayrıca, bir anlık görüntüyü, blob 'u önceki bir sürüme geri yüklemek gibi temel blobunun üzerine kopyalayabilirsiniz. Bir anlık görüntü, bir depolama hesabından diğerine kopyalandığında, taban Sayfa Blobu ile aynı alanı kaplar. Bu nedenle, tüm anlık görüntülerin bir depolama hesabından diğerine kopyalanması yavaştır ve hedef depolama hesabında çok fazla alan tüketir.

> [!NOTE]
> Temel blobu başka bir hedefe kopyalarsanız, Blobun anlık görüntüleri onunla birlikte kopyalanmaz. Benzer şekilde, bir temel Blobun kopyası ile üzerine yazmış olursunuz, temel blob ile ilişkili anlık görüntüler etkilenmez ve temel blob adı altında bozulmaz.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Anlık görüntüleri kullanarak diskleri yedekleme
Sanal makine diskleriniz için bir yedekleme stratejisi olarak, disk veya sayfa blobunun düzenli anlık görüntülerini alabilir ve [BLOB Işlemini Kopyala](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) veya [AzCopy](../articles/storage/common/storage-use-azcopy.md)gibi araçları kullanarak bunları başka bir depolama hesabına kopyalayabilirsiniz. Bir anlık görüntüyü, bir hedef sayfa blobuna farklı bir adla kopyalayabilirsiniz. Elde edilen hedef Sayfa Blobu anlık görüntü değil yazılabilir bir sayfa blobuna sahiptir. Bu makalenin ilerleyen kısımlarında, anlık görüntüleri kullanarak sanal makine disklerinin yedeklerini alma adımlarını açıklıyoruz.

### <a name="restore-disks-using-snapshots"></a>Anlık görüntüleri kullanarak diskleri geri yükleme
Diskinizin daha önce yedekleme anlık görüntülerinden birinde yakalanan kararlı bir sürüme geri yüklenmesi zaman aldığında, temel sayfa blobunun üzerine bir anlık görüntü kopyalayabilirsiniz. Anlık görüntü temel sayfa blobuna yükseltildikten sonra, anlık görüntü kalır, ancak kaynağı hem okunabilir hem de yazılabilir bir kopya ile üzerine yazılır. Bu makalenin ilerleyen kısımlarında, diskinizin önceki bir sürümünü anlık görüntüsünden geri yükleme adımları anlatılmaktadır.

### <a name="implementing-full-snapshot-copy"></a>Tam anlık görüntü kopyası uygulama
Aşağıdakileri yaparak tam bir anlık görüntü kopyası uygulayabilirsiniz.

* İlk olarak, [anlık görüntü blobu](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) işlemini kullanarak temel Blobun anlık görüntüsünü alın.
* Ardından, [kopya blobu](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob)kullanarak anlık görüntüyü bir hedef depolama hesabına kopyalayın.
* Temel Blobun yedek kopyalarını sürdürmek için bu işlemi tekrarlayın.

## <a name="incremental-snapshot-copy"></a>Artımlı anlık görüntü kopyası
[Getpageranges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) API 'sindeki yeni özellik, sayfa bloblarınızın veya disklerinizin anlık görüntülerini yedeklemek için çok daha iyi bir yol sağlar. API, temel blob ve anlık görüntüler arasındaki değişikliklerin listesini, yedekleme hesabında kullanılan depolama alanı miktarını azaltır. API, Premium depolamada bulunan sayfa bloblarını ve standart depolamayı destekler. Bu API 'yi kullanarak Azure VM 'Ler için daha hızlı ve daha verimli yedekleme çözümleri oluşturabilirsiniz. Bu API, REST sürüm 2015-07-08 ve üzeri ile kullanılabilir olacaktır.

Artımlı anlık görüntü kopyası, bir depolama hesabından diğerine, arasındaki farka göre kopyalama yapmanıza olanak sağlar

* Temel blob ve anlık görüntüsü veya
* Temel Blobun iki anlık görüntüsü

Aşağıdaki koşullar sağlandığında,

* Blob, Oca-1-2016 veya üzeri sürümlerde oluşturulmuştur.
* Blob, [Putpage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) veya iki anlık görüntü arasındaki [kopya blobu](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) ile üzerine yazılmadı.

**Note**: Bu özellik Premium ve standart Azure sayfa Blobları için kullanılabilir.

Anlık görüntüler kullanarak özel bir Yedekleme stratejiniz varsa, anlık görüntüleri bir depolama hesabından diğerine kopyalamak yavaş olabilir ve çok fazla depolama alanı tüketebilir. Anlık görüntünün tamamını bir yedekleme depolama hesabına kopyalamak yerine, bir yedekleme sayfası blobuna ardışık anlık görüntüler arasındaki farkı yazabilirsiniz. Bu şekilde, kopyalanacak zaman ve yedeklemelerin depolandığı alan önemli ölçüde azaltılır.

### <a name="implementing-incremental-snapshot-copy"></a>Artımlı anlık görüntü kopyası uygulama
Aşağıdakileri yaparak Artımlı anlık görüntü kopyası uygulayabilirsiniz.

* [Anlık görüntü blobu](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob)kullanarak temel Blobun anlık görüntüsünü alın.
* Anlık görüntüyü, [kopya blobu](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob)kullanarak aynı veya başka bir Azure bölgesindeki hedef yedekleme depolama hesabına kopyalayın. Bu, yedekleme sayfası blobudur. Yedekleme sayfası blobunun anlık görüntüsünü alın ve yedekleme hesabında saklayın.
* Anlık görüntü blobu kullanarak temel Blobun başka bir anlık görüntüsünü alın.
* [Getpageranges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges)kullanarak temel Blobun birinci ve ikinci anlık görüntüleri arasındaki farkı alın. Farkı almak istediğiniz anlık görüntünün tarih saat değerini belirtmek için **prevsnapshot**New parametresini kullanın. Bu parametre mevcut olduğunda REST yanıtı yalnızca hedef anlık görüntü ve Temizleme sayfaları dahil olmak üzere önceki anlık görüntü arasında değiştirilen sayfaları içerir.
* Bu değişiklikleri yedekleme sayfası blobuna uygulamak için [putpage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) kullanın.
* Son olarak, yedekleme sayfası blobunun anlık görüntüsünü alın ve yedekleme depolama hesabında saklayın.

Sonraki bölümde, artımlı anlık görüntü kopyası kullanarak disklerin yedeklerini nasıl koruyabilmeniz gerektiğini daha ayrıntılı bir şekilde anlatmaktadır

## <a name="scenario"></a>Senaryo
Bu bölümde, anlık görüntüler kullanılarak sanal makine diskleri için özel bir yedekleme stratejisi içeren bir senaryo açıklanır.

Premium Storage P30 disk ekli olan DS serisi bir Azure VM 'yi düşünün. *Mypremıumdisk* adlı P30 diski, *mypremıumaccount*adlı bir Premium Depolama hesabında depolanır. *Mypremıumdisk*yedeklemesini depolamak için *mybackupstdaccount* adlı standart bir depolama hesabı kullanılır. Her 12 saatte bir *mypremıumdisk* anlık görüntüsünü tutmak istiyoruz.

Depolama hesabı oluşturma hakkında bilgi edinmek için bkz. [depolama hesabı oluşturma](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

Azure VM 'lerini yedekleme hakkında bilgi edinmek için bkz. [Azure VM yedeklemelerini planlayın](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Artımlı anlık görüntü kullanarak bir diskin yedeklerini sürdürme adımları
Aşağıdaki adımlarda *mypremıumdisk* anlık görüntülerinin nasıl alınacağını ve *mybackupstdaccount*içinde yedeklemeleri nasıl korutabileceğiniz açıklanır. Yedekleme, *mybackupstdpageblob*adlı standart bir sayfa blobuna sahiptir. Yedekleme sayfası blobu her zaman *mypremıumdisk*'in son anlık görüntüsüyle aynı durumu yansıtır.

1. *Mypremiumdisk_ss1*adlı *mypremıumdisk* anlık görüntüsünü alarak Premium Depolama diskiniz için yedekleme sayfası blobu oluşturun.
2. Bu anlık görüntüyü *mybackupstdpageblob*adlı bir Sayfa Blobu olarak mybackupstdaccount öğesine kopyalayın.
3. [Anlık görüntü blobu](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) kullanarak *mybackupstdpageblob_ss1*adlı *mybackupstdpageblob* 'un anlık görüntüsünü alın ve *mybackupstdaccount*içinde saklayın.
4. Yedekleme penceresi sırasında *mypremıumdisk*' in başka bir anlık görüntüsünü oluşturun, *mypremiumdisk_ss2*söyleyin ve *mypremıumaccount*içinde saklayın.
5. *Mypremiumdisk_ss2* ve *mypremiumdisk_ss1*iki **anlık görüntü arasında** artımlı değişiklikler alın, bu, *mypremiumdisk_ss2* üzerinde [getpageranges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) kullanarak *mypremiumdisk_ss1*zaman damgasına ayarlanır. Bu artımlı değişiklikleri *mybackupstdaccount*içindeki *mybackupstdpageblob* yedekleme sayfasına yazın. Artımlı değişiklikler içinde silinen aralıklar varsa, bunların yedekleme sayfası blobundan temizlenmesi gerekir. Yedekleme sayfası blobuna artımlı değişiklikler yazmak için [putpage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) kullanın.
6. *Mybackupstdpageblob_ss2*adlı yedekleme sayfası blob *'backupstdpageblob*' un bir anlık görüntüsünü alın. Önceki anlık görüntü *mypremiumdisk_ss1* Premium Depolama hesabından silin.
7. Her yedekleme penceresinde 4-6 arasındaki adımları yineleyin. Bu şekilde, *mypremıumdisk* yedeklemelerini standart bir depolama hesabında koruyabilirsiniz.

![Artımlı anlık görüntü kullanarak diski yedekleme](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Bir diski anlık görüntülerden geri yükleme adımları
Aşağıdaki adımlarda, *mybackupstdaccount*yedekleme depolama hesabından Premium disk, *mypremıumdisk* 'in daha önceki bir anlık görüntüye nasıl geri yükleneceği anlatılmaktadır.

1. Premium diski geri yüklemek istediğiniz zaman noktasını belirler. Bunun, *mybackupstdaccount*yedekleme depolama hesabında depolanan anlık görüntü *mybackupstdpageblob_ss2*olduğunu varsayalım.
2. Mybackupstdaccount içinde, anlık görüntü *mybackupstdpageblob_ss2* yeni yedekleme temel sayfası blobu *mybackupstdpageblobrestore*olarak yükseltin.
3. *Mybackupstdpageblobrestored_ss1*adlı geri yüklenen bu yedekleme sayfası blobunun anlık görüntüsünü alın.
4. Yeni Premium disk *mypremıumdiskgeri yüklendiği*için *mybackupstdaccount* öğesinden *mypremıumaccount* 'a geri yüklenen *mybackupstdpageblobrestore adlı* geri yüklenen sayfa blobunu kopyalayın.
5. Daha sonra artımlı yedeklemeler yapmak için *mypremiumdiskrestored_ss1* adlı *mypremıumdiskgeri yüklenmiş*bir anlık görüntü alın.
6. DS serisi VM 'yi geri yüklenen disk *mypremıumdiskgeri yüklendi* diskine getirin ve eski *MYPREMıUMDISK* 'yi VM 'den ayırın.
7. Yedekleme sayfası blobu olarak *geri yüklenen mybackupstdpageblobı* kullanılarak geri yüklenen disk *mypremıumdiskrestore*Için önceki bölümde açıklanan Yedekleme sürecini başlatın.

![Diski anlık görüntülerden geri yükle](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Sonraki Adımlar
Bir Blobun anlık görüntülerini oluşturma ve VM yedekleme altyapınızı planlama hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kullanın.

* [Blob 'un anlık görüntüsünü oluşturma](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [VM yedekleme altyapınızı planlayın](../articles/backup/backup-azure-vms-introduction.md)

