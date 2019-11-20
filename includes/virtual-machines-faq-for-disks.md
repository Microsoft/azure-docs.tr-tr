---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 92aa28c131d81c41d515762ac2a346048c143790
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008591"
---
Bu makalede, Azure yönetilen diskler ve Azure Premium SSD diskleri hakkında sık sorulan bazı sorular yanıtlanmaktadır.

## <a name="managed-disks"></a>Yönetilen Diskler

**Azure yönetilen diskler nedir?**

Yönetilen diskler, depolama hesabı yönetimini sizin için işleyerek Azure IaaS VM 'Leri için disk yönetimini kolaylaştıran bir özelliktir. Daha fazla bilgi için bkz. [yönetilen disklere genel bakış](../articles/virtual-machines/windows/managed-disks-overview.md).

**Mevcut bir VHD 'den 80 GB olan standart bir yönetilen disk oluşturdum, ne kadar ücret alınacaktır?**

80 GB 'lık bir VHD 'den oluşturulan standart bir yönetilen disk, S10 disk olan bir sonraki kullanılabilir standart disk boyutu olarak değerlendirilir. S10 disk fiyatlandırmasına göre ücretlendirilirsiniz. Daha fazla bilgi edinmek için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/storage).

**Standart yönetilen diskler için herhangi bir işlem maliyeti var mı?**

Evet. Her bir işlem için ücretlendirilirsiniz. Daha fazla bilgi edinmek için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/storage).

**Standart yönetilen bir disk için, diskteki verilerin gerçek boyutu veya diskin sağlanmış kapasitesi için ücretlendirilecektir mi?**

Diskin sağlanan kapasitesine göre ücretlendirilirsiniz. Daha fazla bilgi edinmek için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/storage).

**Premium yönetilen disklerin fiyatlandırması yönetilmeyen disklerden nasıl farklıdır?**

Premium yönetilen disklerin fiyatlandırması, yönetilmeyen Premium disklerle aynıdır.

**Yönetilen disklerimin depolama hesabı türünü (Standart veya Premium) değiştirebilir miyim?**

Evet. Yönetilen disklerinizin depolama hesabı türünü Azure portal, PowerShell veya Azure CLı kullanarak değiştirebilirsiniz.

**Farklı bir aboneliğe sahip yönetilen bir disk oluşturmak için Azure depolama hesabında bir VHD dosyası kullanabilir miyim?**

Evet.

**Farklı bir bölgede yönetilen disk oluşturmak için Azure depolama hesabında bir VHD dosyası kullanabilir miyim?**

Hayır.

**Yönetilen diskler kullanan müşteriler için herhangi bir ölçek sınırlaması var mı?**

Yönetilen diskler, depolama hesaplarıyla ilişkili sınırları ortadan kaldırır. Ancak, en fazla sınır 50.000, bir abonelik için bölge başına yönetilen disk ve disk türü olarak kullanılır.

**Yönetilen bir disk için artımlı bir anlık görüntü alabilir miyim?**

Hayır. Geçerli anlık görüntü özelliği, yönetilen bir diskin tam bir kopyasını oluşturur.

**Kullanılabilirlik kümesindeki VM 'Ler, yönetilen ve yönetilmeyen disklerin birleşiminden oluşur mi?**

Hayır. Bir kullanılabilirlik kümesindeki VM 'Ler, tüm yönetilen diskleri ya da yönetilmeyen diskleri kullanmalıdır. Bir kullanılabilirlik kümesi oluşturduğunuzda, kullanmak istediğiniz disk türünü seçebilirsiniz.

**Yönetilen diskler Azure portal varsayılan seçenektir mi?**

Evet.

**Boş bir yönetilen disk oluşturabilir miyim?**

Evet. Boş bir disk oluşturabilirsiniz. Yönetilen bir disk, bir VM 'den bağımsız olarak oluşturulabilir, örneğin, bir VM 'ye iliştirmeden.

**Yönetilen diskleri kullanan bir kullanılabilirlik kümesi için desteklenen hata etki alanı sayısı nedir?**

Yönetilen diskleri kullanan kullanılabilirlik kümesinin bulunduğu bölgeye bağlı olarak, desteklenen hata etki alanı sayısı 2 veya 3 ' dir.

**Tanılama için standart depolama hesabı nasıl ayarlanır?**

VM Tanılama için bir özel depolama hesabı ayarlarsınız.

**Yönetilen diskler için ne tür bir rol tabanlı Access Control desteği vardır?**

Yönetilen diskler üç temel varsayılan rolü destekler:

* Sahip: erişim dahil her şeyi yönetebilir
* Katkıda bulunan: erişim dışında her şeyi yönetebilir
* Okuyucu: her şeyi görüntüleyebilir, ancak değişiklik yapamaz

**Yönetilen bir diski özel depolama hesabına kopyalayabiliyorum veya dışarı aktardığım bir yol var mı?**

Yönetilen disk için salt okunurdur ve paylaşılan erişim imzası (SAS) URI 'SI oluşturabilir ve içeriği bir özel depolama hesabına veya şirket içi depolamaya kopyalamak için kullanabilirsiniz. SAS URI 'sini Azure portal, Azure PowerShell, Azure CLı veya [AzCopy](../articles/storage/common/storage-use-azcopy.md) kullanarak kullanabilirsiniz

**Yönetilen disklerimin bir kopyasını oluşturabilir miyim?**

Müşteriler yönetilen disklerinin anlık görüntüsünü alabilir ve ardından anlık görüntüyü kullanarak başka bir yönetilen disk oluşturabilir.

**Yönetilmeyen diskler hala destekleniyor mu?**

Evet, hem yönetilmeyen hem de yönetilen diskler destekleniyor. Yeni iş yükleri için yönetilen diskler kullanmanızı ve geçerli iş yüklerinizi yönetilen disklere geçirmeyi öneririz.

**Aynı VM 'de yönetilmeyen ve yönetilen diskleri birlikte bulabilir miyim?**

Hayır.

**128 GB bir disk oluşturup boyutu 130 Gibibyte (gib) olarak artırdığımda, sonraki disk boyutu (256 gib) için ücretlendirilirim mıyım?**

Evet.

**Yerel olarak yedekli depolama, coğrafi olarak yedekli depolama ve bölgesel olarak yedekli depolama ile yönetilen diskler oluşturabilir miyim?**

Azure yönetilen diskler Şu anda yalnızca yerel olarak yedekli depolama ile yönetilen diskleri desteklemektedir.

**Yönetilen disklerimi daraltabilir veya değiştirebilir miyim?**

Hayır. Bu özellik şu anda desteklenmiyor.

**Diskimde bir kirayı bölebilir miyim?**

Hayır. Bu, disk kullanılırken yanlışlıkla silinmeye engel olmak için bir kira olduğu için şu anda desteklenmez.

**Bir VM sağlamak için özelleştirilmiş (Sistem Hazırlama Aracı veya Genelleştirilmiş) işletim sistemi diski kullanıldığında bilgisayar adı özelliğini değiştirebilir miyim?**

Hayır. Bilgisayar adı özelliğini güncelleştiremezsiniz. Yeni VM, işletim sistemi diskini oluşturmak için kullanılan üst VM 'den devralır. 

**Yönetilen disklere sahip VM 'Ler oluşturmak için örnek Azure Resource Manager şablonlarını nerede bulabilirim?**
* [Yönetilen diskleri kullanan şablonların listesi](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Blob 'dan bir disk oluştururken, bu kaynak Blobun sürekli olarak var olan bir ilişki var mı?**

Hayır, yeni disk oluşturulduğunda bu Blobun o anda tam tek başına kopyasıdır ve ikisi arasında bir bağlantı yoktur. İsterseniz, diski oluşturduktan sonra, kaynak blobu yeni oluşturulan diski herhangi bir şekilde etkilemeden silinebilir.

**Yönetilen veya yönetilmeyen bir diski oluşturulduktan sonra yeniden adlandırabilir miyim?**

Yönetilen diskler için bunları yeniden adlandıramazsınız. Ancak, şu anda bir VHD veya VM 'ye bağlı olmadığı sürece, yönetilmeyen bir diski yeniden adlandırabilirsiniz.

**Bir Azure diskinde GPT bölümlendirme kullanabilir miyim?**

GPT bölümlendirme, işletim sistemi disklerinde değil yalnızca veri disklerinde kullanılabilir. İşletim sistemi disklerinin MBR bölümleme stilini kullanması gerekir.

**Hangi disk türleri anlık görüntüleri destekliyor?**

Premium SSD, standart SSD ve standart HDD desteği anlık görüntüleri. Bu üç disk türü için, tüm disk boyutları (boyut olarak 32 TiB 'ye kadar olan diskler dahil) için anlık görüntüler desteklenir. Ultra diskler anlık görüntüleri desteklemez.

### <a name="disk-reservation"></a>Disk ayırma

**Azure disk ayırması nedir?**
Disk ayırma, toplam maliyetinizi azaltmak için önceden bir yıllık disk depolama alanı satın alma seçeneğidir.

**Azure disk rezervasyonu hangi seçenekleri sunar?**
Azure disk ayırma, bir yıllık dönem için P30 (1 TiB) ile P80 (32 TiB) arasında Premium SSD 'Ler satın alma seçeneği sunar. Disk ayırması satın almak için gereken minimum disk miktarı üzerinde hiçbir sınırlama yoktur. Ayrıca, tek, ön ödeme veya aylık ödemeler ile ödeme yapmayı tercih edebilirsiniz. Premium SSD yönetilen disklere uygulanan ek bir işlem maliyeti yoktur.

Ayırmalar, kapasite değil, diskler biçiminde yapılır. Diğer bir deyişle, bir P80 (32 TiB) diski ayırdığınızda tek bir P80 diski alırsınız, daha sonra söz konusu ayırmayı iki küçük P70 (16 TiB) diske Divvy. Kuşkusuz, iki ayrı P70 (16 TiB) disk dahil olmak üzere istediğiniz kadar çok sayıda disk ayırabilirsiniz.

**Azure disk ayırması için nasıl faturalandırılırım?**
- Kurumsal Anlaşma (EA) müşterileri için Azure parasal taahhüt, ilk olarak Azure disk ayırmalarını satın almak için kullanılacaktır. EA müşterilerinin tüm parasal taahhüdünü kullanmış olduğu senaryolarda, disk rezervasyonları yine de satın alınabilir ve bu satın alma işlemleri bir sonraki fazla kullanım faturanızda tek, ön ödeme için faturalandırılır.

- Azure.com aracılığıyla satın alan müşteriler için satın alma sırasında dosyadaki kredi kartı, Azure diskleri rezervasyonuna ait tam ödeme (veya aylık sabit ödemeler) için ücretlendirilir.

**Azure disk ayırması nasıl uygulanır?**
Diskler ayırması, ayrılmış sanal makine (VM) örneklerine benzer bir model izler. Bir sanal makine örneği, bir disk rezervasyonunun farklı SKU 'Lara uygulanamadığından farklılık gösteren fark. VM örnekleri hakkında daha fazla bilgi için bkz. [Azure ayrılmış VM örnekleri ile maliyetleri kaydetme](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) . 

**Birden çok bölgede Azure diskleri ayırması aracılığıyla satın alınan veri depolama alanını kullanabilir miyim?**
Azure diskleri ayırması belirli bir bölge ve SKU (Doğu ABD 2 içinde P30 gibi) için satın alınır ve bu nedenle bu yapılar dışında kullanılamaz. Diğer bölgelerde veya SKU 'Larda disk depolama gereksinimleriniz için her zaman ek bir Azure diskleri ayırması satın alabilirsiniz.

**Azure disklerimin ayırması sona erdiğinde ne olur?**
Süresi dolmadan 30 gün önce ve sona erme tarihinde bir süre sonra bir e-posta bildirimi alacaksınız. Ayırma süresi dolduktan sonra, dağıtılan diskler çalışmaya devam eder ve en son [Kullandıkça Öde tarifesine](https://azure.microsoft.com/pricing/details/managed-disks/)göre faturalandırılır.

## <a name="ultra-disks"></a>Ultra diskler

**Son derece disk verimini ne şekilde ayarlayamalıyım?**
Disk aktarım hızını hangi şekilde ayarlayacağınızdan emin değilseniz, 16 KiB 'nin GÇ boyutunu kabul ederek ve uygulamanızı izlerken performansı buradan ayarlamanız önerilir. Formül: MB/sn cinsinden aktarım hızı = ıOPS * 16/1000.

**Diskmi 40000 ıOPS olarak yapılandırdım, ancak yalnızca 12800 ıOPS görüyorum, diskin performansını neden göremiyorum?**
Disk azaltma 'ya ek olarak, VM düzeyinde uygulanan bir GÇ azaltması vardır. Lütfen kullandığınız VM boyutunun disklerinizde yapılandırılan düzeyleri destekleyebileceğini doğrulayın. VM 'niz tarafından uygulanan GÇ limitleriyle ilgili ayrıntılar için bkz. [Azure 'Da Windows sanal makineleri Için boyutlar](../articles/virtual-machines/windows/sizes.md).

**Bir ultra disk ile önbelleğe alma düzeylerini kullanabilir miyim?**
Hayır, Ultra diskler diğer disk türlerinde desteklenen farklı önbelleğe alma yöntemlerini desteklemez. Disk önbelleğe almayı hiçbiri olarak ayarlayın.

**Var olan sanal makinenize bir ultra disk ekleyebilir miyim?**
Belki de sanal makinenizin Ultra diskleri destekleyen bir bölge ve kullanılabilirlik alanı çifti olması gerekir. Ayrıntılar için bkz. [Ultra disklerle çalışmaya](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md) başlama.

**VM 'im için bir ultra disk işletim sistemi diski olarak kullanabilir miyim?**
Hayır, Ultra diskler yalnızca veri diskleri olarak desteklenir ve yalnızca 4K yerel disk olarak desteklenir.

**Var olan bir diski bir ultra diske dönüştürebilir miyim?**
Hayır, ancak mevcut bir diskteki verileri bir ultra diske geçirebilirsiniz. Var olan bir diski bir ultra diske geçirmek için, her iki diski aynı sanal makineye bağlayın ve disk verilerini bir diskten diğerine kopyalayın ya da veri geçişi için 3. taraf çözümünü kullanın.

**Ultra diskler için anlık görüntüler oluşturabilir miyim?**
Hayır, anlık görüntüler henüz kullanılamıyor.

**Azure Backup Ultra diskler için kullanılabilir mi?**
Hayır, Azure Backup desteği henüz kullanılamıyor.

**Kullanılabilirlik kümesinde çalışan bir VM 'ye bir ultra disk ekleyebilir miyim?**
Hayır, bu henüz desteklenmiyor.

**Ultra diskler kullanan VM 'Ler için Azure Site Recovery etkinleştirebilir miyim?**
Hayır, Azure Site Recovery henüz Ultra diskler için desteklenmiyor.

## <a name="uploading-to-a-managed-disk"></a>Yönetilen bir diske yükleme

**Mevcut bir yönetilen diske veri yükleyebilir miyim?**

Hayır, karşıya yükleme yalnızca **Readytoupload** durumuna sahip yeni bir boş disk oluşturulurken kullanılabilir.

**Nasıl yaparım? yönetilen bir diske mı yüklenecek?**

[Creationdata](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) 'ın [Createoption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) özelliği "karşıya yükle" olarak ayarlanmış bir yönetilen disk oluşturun, ardından verileri karşıya yükleyebilirsiniz.

**Karşıya yükleme durumundayken bir diski bir VM 'ye ekleyebilir miyim?**

Hayır.

**, Bir karşıya yükleme durumundaki bir sürücü anlık görüntüsünü alabilir miyim?**

Hayır.

## <a name="standard-ssd-disks"></a>Standart SSD diskler

**Azure Standart SSD diskleri nelerdir?**
Standart SSD diskler, düşük ıOPS düzeylerinde tutarlı performans gerektiren iş yükleri için uygun maliyetli depolama olarak iyileştirilmiş, katı hal medyası tarafından desteklenen standart disklerdir.

<a id="standard-ssds-azure-regions"></a>**Standart SSD diskler için şu anda desteklenen bölgeler nelerdir?**
Tüm Azure bölgeleri artık Standart SSD diskleri desteklemektedir.

**Standart SSD 'Ler kullanılırken Azure Backup kullanılabilir mi?**
Evet, Azure Backup artık kullanılabilir.

**Nasıl yaparım? Standart SSD diskler mi oluşturulsun?**
Azure Resource Manager şablonları, SDK, PowerShell veya CLı kullanarak Standart SSD diskleri oluşturabilirsiniz. Standart SSD disk oluşturmak için Kaynak Yöneticisi şablonunda gereken parametreler şunlardır:

* Microsoft. COMPUTE için *Apiversion* , `2018-04-01` olarak ayarlanmalıdır (veya üzeri)
* *Manageddisk. storageAccountType* as `StandardSSD_LRS` belirtin

Aşağıdaki örnek, Standart SSD diskleri kullanan bir VM için *Properties. storageProfile. osDisk* bölümünü gösterir:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Şablon ile Standart SSD disk oluşturma hakkında ayrıntılı bir örnek için, bkz. [Standart SSD Veri disklerine sahip bir Windows GÖRÜNTÜSÜNDEN VM oluşturma](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Mevcut disklerimi Standart SSD dönüştürebilir miyim?**
Evet, uygulayabilirsiniz. [Azure yönetilen diskler depolama alanını standartdan Premium 'A dönüştürme ve](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) yönetilen diskleri dönüştürmeye yönelik genel yönergeler için tam tersi. Ve, disk türünü Standart SSD güncelleştirmek için aşağıdaki değeri kullanın.
-AccountType StandardSSD_LRS

**HDD yerine Standart SSD diskleri kullanmanın avantajı nedir?**
Standart SSD diskler, HDD disklerine kıyasla daha iyi gecikme, tutarlılık, kullanılabilirlik ve güvenilirlik sağlar. Uygulama iş yükleri bu nedenle Standart SSD çok daha sorunsuz bir şekilde çalışır. Premium SSD diskler, en fazla GÇ yoğun üretim iş yükleri için önerilen çözümdür.

**Standart SSD 'leri yönetilmeyen diskler olarak kullanabilir miyim?**
Hayır, standart SSD diskler yalnızca yönetilen diskler olarak kullanılabilir.

**Standart SSD diskleri "tek örnekli VM SLA 'Sı" destekliyor mu?**
Hayır, standart SSD 'Lerin tek örnekli VM SLA 'Sı yoktur. Tek örnekli VM SLA 'Sı için Premium SSD diskleri kullanın.

## <a name="migrate-to-managed-disks"></a>Yönetilen Disklere geçme

**Yönetilen diskler performansında geçişin etkileri var mı?**

Geçiş, diskin bir depolama konumundan diğerine taşınmasını içerir. Bu, verilerin arka plan kopyası aracılığıyla düzenlenir, bu işlem birkaç saat sürebilir ve genellikle, disklerdeki veri miktarına bağlı olarak 24 saat daha küçüktür. Bu süre boyunca, bazı okumalar özgün konuma yeniden yönlendiriliyorsa ve tamamlanması daha uzun sürebileceğinden, uygulamanız olağan okuma gecikmesinden daha yüksek bir deneyim yaşayabilir. Bu süre boyunca yazma gecikmesi üzerinde hiçbir etkisi yoktur.  

**Önceden var olan bir Azure Backup hizmeti yapılandırmasında, yönetilen disklere geçişten önce/sonra hangi değişiklikler gerekir?**

Değişiklik gerekmiyor.

**Geçiş çalışmaya devam etmeden önce VM yedeklemelerim Azure Backup hizmet aracılığıyla oluşturulur mi?**

Evet, yedeklemeler sorunsuz şekilde çalışır.

**Önceden var olan Azure diskleri şifreleme yapılandırmasında, yönetilen disklere geçişten önce/sonra hangi değişiklikler gerekir?**

Değişiklik gerekmiyor.

**, Mevcut bir sanal makine ölçek kümesinin yönetilmeyen disklerden yönetilen disklere otomatik olarak geçirilmesi mi?**

Hayır. Yönetilmeyen diskler içeren eski ölçek kümesinden görüntüyü kullanarak, yönetilen disklerle yeni bir ölçek kümesi oluşturabilirsiniz.

**Yönetilen disklere geçmeden önce alınan Sayfa Blobu anlık görüntüsünden yönetilen bir disk oluşturabilir miyim?**

Hayır. Sayfa Blobu anlık görüntüsünü Sayfa Blobu olarak dışa aktarabilir ve ardından dışa aktarılmış sayfa blobundan yönetilen bir disk oluşturabilirsiniz.

**Yönetilen diskler içeren bir VM 'ye Azure Site Recovery tarafından korunan şirket içi makinelerimin yükünü devreder miyim?**

Evet, yönetilen disklerle bir VM 'ye yük devretmeyi seçebilirsiniz.

**Azure 'dan Azure 'a çoğaltma aracılığıyla Azure Site Recovery korunan Azure VM 'lerinde geçişin etkileri var mı?**

Hayır. Yönetilen disklere sahip VM 'Ler için Azure 'dan Azure 'a koruma Azure Site Recovery.

**VM 'Leri, yönetilen disklere daha önce şifrelenmiş olan veya daha önce şifrelenen depolama hesaplarında bulunan yönetilmeyen disklere geçirebilir miyim?**

Yes

## <a name="managed-disks-and-storage-service-encryption"></a>Yönetilen diskler ve Depolama Hizmeti Şifrelemesi

**Azure Depolama Hizmeti Şifrelemesi, yönetilen bir disk oluştururken varsayılan olarak etkindir mi?**

Evet.

**Önyükleme birimi, yönetilen bir diskte varsayılan olarak şifrelendi mı?**

Evet. Varsayılan olarak, işletim sistemi diski dahil olmak üzere tüm yönetilen diskler şifrelenir.

**Şifreleme anahtarlarını kim yönetir?**

Microsoft, şifreleme anahtarlarını yönetir.

**Yönetilen disklerimin Depolama Hizmeti Şifrelemesi devre dışı bırakabilir miyim?**

Hayır.

**Depolama Hizmeti Şifrelemesi yalnızca belirli bölgelerde kullanılabilir mi?**

Hayır. Yönetilen disklerin kullanılabildiği tüm bölgelerde kullanılabilir. Yönetilen diskler tüm genel bölgelerde ve Almanya 'da kullanılabilir. Bununla birlikte, yalnızca Microsoft tarafından yönetilen anahtarlar için, müşteri tarafından yönetilen anahtarlar için değil, Çin 'de de kullanılabilir.

**Yönetilen diskimin şifrelenip şifrelenmediğini nasıl öğrenebilirim?**

Yönetilen bir diskin Azure portal, Azure CLı ve PowerShell 'den oluşturulma zamanını öğrenebilirsiniz. Süre, 9 Haziran 2017 ' den sonra ise, diskiniz şifrelenir.

**10 Haziran 2017 ' den önce oluşturulan mevcut disklerimi nasıl şifreleyebilirim?**

10 Haziran 2017 itibariyle, mevcut yönetilen disklere yazılan yeni veriler otomatik olarak şifrelenir. Ayrıca, var olan verileri şifrelemeyi planlıyoruz ve şifreleme arka planda zaman uyumsuz olarak gerçekleşecektir. Mevcut verileri şimdi şifrelemeniz gerekiyorsa, diskinizin bir kopyasını oluşturun. Yeni diskler şifrelenir.

* [Azure CLı kullanarak yönetilen diskleri kopyalama](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Yönetilen diskleri PowerShell kullanarak kopyalama](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Yönetilen anlık görüntüler ve görüntüler şifrelendi mı?**

Evet. 9 Haziran 2017 ' den sonra oluşturulan tüm yönetilen anlık görüntüler ve görüntüler otomatik olarak şifrelenir. 

**VM 'Leri daha önce yönetilen disklere şifrelenmiş depolama hesaplarında bulunan yönetilmeyen disklere dönüştürebilir miyim?**

Yes

**Yönetilen bir diskten ya da bir anlık görüntüden dışarıya aktarılmış bir VHD mi olacak?**

Hayır. Ancak, şifrelenen bir yönetilen diskten veya anlık görüntüsünden bir VHD 'yi şifrelenmiş bir depolama hesabına dışa aktardığınızda, bu durumda şifrelenir. 

## <a name="premium-disks-managed-and-unmanaged"></a>Premium diskler: yönetilen ve yönetilmeyen

**Bir VM, DSv2 gibi Premium SSD diskleri destekleyen bir boyut serisi kullanıyorsa hem Premium hem de standart veri disklerini ekleyebilir miyim?** 

Evet.

**Hem Premium hem de standart veri disklerini D, Dv2, G veya F serisi gibi Premium SSD diskleri desteklemeyen bir boyut dizisine iliştirebilir miyim?**

Hayır. Yalnızca Premium SSD diskleri destekleyen bir boyut serisi kullanmayan VM 'lere yalnızca standart veri diskleri ekleyebilirsiniz.

**80 GB olan mevcut bir VHD 'den Premium veri diski oluştururum, bu maliyet ne kadar olacaktır?**

Bir 80 GB VHD 'den oluşturulan Premium veri diski, P10 disk olan bir sonraki kullanılabilir Premium disk boyutu olarak değerlendirilir. P10 disk fiyatlandırmasına göre ücretlendirilirsiniz.

**Premium SSD diskleri kullanmak için işlem maliyetleri var mı?**

Her disk boyutu için sabit bir maliyet vardır ve bu, ıOPS ve aktarım hızı için belirli limitlerle sağlanır. Varsa, diğer maliyetler ise giden bant genişliğidir ve anlık görüntü kapasitesidir. Daha fazla bilgi edinmek için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/storage).

**Disk önbelleğinden alabileceği ıOPS ve aktarım hızı sınırları nelerdir?**

Bir DS serisi için önbellek ve yerel SSD için Birleşik Sınırlar, çekirdek başına 4.000 ıOPS ve çekirdek başına 33 MiB. GS serisi çekirdek başına 5.000 ıOPS ve çekirdek başına saniyede 50 MIB sağlar.

**Yerel SSD, yönetilen diskler VM 'si için destekleniyor mu?**

Yerel SSD, yönetilen diskler VM 'ye dahil olan geçici bir depodır. Bu geçici depolama için ek maliyet yoktur. Azure Blob depolamada kalıcı olmadığından, uygulama verilerinizi depolamak için bu yerel SSD 'yi kullanmanızı öneririz.

**Premium disklerde KıRPMA kullanımı için herhangi bir sorun var mı?**

Azure disklerinde, Premium veya Standart diskler üzerinde KıRPMA kullanmanın bir alt tarafı yoktur.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Yeni disk boyutları: yönetilen ve yönetilmeyen

**Hangi bölgeler geçerli Premium SSD disk boyutu için ne kadar fazla özelliği destekler?**

Patlama özelliği şu anda Azure Orta Batı ABD 'da desteklenmektedir.

**Hangi bölgeler 4/8/16 GiB yönetilen disk boyutlarına (P1/P2/P3, E1/E2/E3) destekleniyor?**

Bu yeni disk boyutları şu anda Azure Orta Batı ABD 'da desteklenmektedir.

**, Yönetilmeyen diskler veya sayfa Blobları için P1/P2/P3 disk boyutları desteklenir mi?**

Hayır, yalnızca Premium SSD tarafından yönetilen disklerde desteklenir. 

**E1/E2/E3 disk boyutları, yönetilmeyen diskler veya sayfa Blobları için destekleniyor mu?**

Hayır, her boyuttaki standart SSD yönetilen diskleri, yönetilmeyen disklerle veya sayfa Blobları ile kullanılamaz.

**İşletim sistemi ve veri diskleri için desteklenen en büyük yönetilen disk boyutu nedir?**

Azure 'un bir işletim sistemi diski için desteklediği bölüm türü, ana önyükleme kaydıdır (MBR). MBR biçimi 2 TiB 'ye kadar bir disk boyutunu destekler. Azure 'un bir işletim sistemi diski için desteklediği en büyük boyut 2 TiB 'dir. Azure, Azure sogeign bulutlarında, 4 TiB küresel Azure 'da yönetilen veri diskleri için en fazla 32 TiB destekler.

**İşletim sistemi ve veri diskleri için desteklenen en büyük yönetilmeyen disk boyutu nedir?**

Azure 'un bir işletim sistemi diski için desteklediği bölüm türü, ana önyükleme kaydıdır (MBR). MBR biçimi 2 TiB 'ye kadar bir disk boyutunu destekler. Azure 'un bir işletim sistemi tarafından yönetilmeyen disk için desteklediği en büyük boyut 2 TiB 'dir. Azure, veri yönetilmeyen diskler için en fazla 4 TiB destekler.

**Desteklenen en büyük Sayfa Blobu boyutu nedir?**

Azure 'un desteklediği en büyük Sayfa Blobu boyutu 8 TiB 'dir (8.191 GiB). Bir VM 'ye veri olarak veya işletim sistemi diskleri olarak eklenen en büyük Sayfa Blobu boyutu 4 TiB (4.095 GiB).

**1 TiB 'den büyük disklerin oluşturulması, eklenmesi, yeniden boyutlandırılması ve karşıya yüklenmesi için yeni bir Azure araçları sürümü kullanmam gerekir mi?**

1 TiB 'den büyük disklerin oluşturulması, eklenmesi veya yeniden boyutlandırılması için mevcut Azure araçlarınızı yükseltmeniz gerekmez. VHD dosyanızı Şirket içinden doğrudan bir Sayfa Blobu veya yönetilmeyen disk olarak Azure 'a yüklemek için aşağıda listelenen en son araç kümelerini kullanmanız gerekir. Yalnızca 8 TiB 'ye kadar olan VHD yüklemelerini destekliyoruz.

|Azure Araçları      | Desteklenen sürümler                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Sürüm numarası 4.1.0: Haziran 2017 sürüm veya üzeri|
|Azure CLı v1     | Sürüm numarası 0.10.13: Mayıs 2017 sürüm veya sonrası|
|Azure CLı v2     | Sürüm numarası 2.0.12: Temmuz 2017 sürümü veya üzeri|
|AzCopy           | Sürüm numarası 6.1.0: Haziran 2017 sürüm veya üzeri|

**P4 ve P6 disk boyutları yönetilmeyen diskler veya sayfa Blobları için destekleniyor mu?**

P4 (32 GiB) ve P6 (64 GiB) disk boyutları, yönetilmeyen diskler ve sayfa Blobları için varsayılan disk katmanları olarak desteklenmez. Diskinizin Bu katmanlarla eşleştirilmesini sağlamak için [BLOB katmanını](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) P4 ve P6 olarak ayarlamanız gerekir. Disk boyutu veya disk uzunluğu 32 GiB 'den az olan bir yönetilmeyen disk veya Sayfa Blobu dağıtırsanız ya da blob katmanını ayarlamadan 32 GiB ile 64 GiB arasında devam ederseniz, P10 'de 500 ıOPS ve 100 MIB/s ve eşlenmiş fiyatlandırma katmanı ile çalışmaya devam edersiniz.

**Küçük disk etkinleştirilmeden (15 Haziran 2017 ' de olmak üzere), mevcut Premium yönetilen diskim daha az 64 GiB tarafından oluşturulduysa, nasıl faturalandırılır?**

64 GiB 'den küçük olan mevcut küçük Premium diskler P10 fiyatlandırma katmanına göre faturalandırılmaya devam eder.

**64 GiB 'den küçük Premium disklerin disk katmanını P4 veya P6 'e nasıl değiştirebilirim?**

Küçük disklerinizin anlık görüntüsünü alabilir ve ardından bir disk oluşturarak fiyatlandırma katmanını otomatik olarak P4 veya P6 olarak sağlanan boyuta göre değiştirebilirsiniz.

**Mevcut yönetilen diskleri 4 tebibayttan (TiB) daha az 32 TiB 'ye kadar yeni sunulan disk boyutlarına göre boyutlandırmanıza izin verebilir mi?**

Evet.

**Azure Backup ve Azure Site Recovery hizmeti tarafından desteklenen en büyük disk boyutları nelerdir?**

Azure Backup ve Azure Site Recovery hizmeti tarafından desteklenen en büyük disk boyutu 4 TiB 'dir. 32 TiB 'ye kadar daha büyük diskler için destek henüz kullanılamamaktadır.

**İyileştirilmiş disk ıOPS ve bant genişliğine ulaşmak için Standart SSD ve Standart HDD diskler için daha büyük disk boyutları (> 4 TiB) için önerilen VM boyutları nelerdir?**

Standart SSD disk işleme elde etmek Standart HDD ve büyük disk boyutlarını (> 4 TiB) 500 ıOPS ve 60 MIB/sn 'den daha iyi karşılamak için, performansınızı iyileştirmek üzere aşağıdaki VM boyutlarından birinden yeni bir VM dağıtmanız önerilir: B-serisi, DSv2-serisi, Dsv3-serisi, ESv3-serisi , FS Serisi, Fsv2-serisi, d serisi, GS-Series, NCv2-serisi, NCv3-Series veya LS Serisi VM 'Ler. Mevcut VM 'lere veya yukarıda önerilen boyutları kullanmayan VM 'lere büyük diskler eklemek daha düşük performans yaşayabilir.

**En büyük disk boyutları önizlemesi sırasında dağıtılan disklerimi nasıl yükseltebilirim (> 4 TiB) ve daha yüksek ıOPS, GA & bant genişliğine sahip olabilir mi?**

Diskin bağlı olduğu VM 'yi durdurup başlatabilir veya diskinizin bağlantısını ayırıp yeniden ekleyebilirsiniz. Daha büyük disk boyutlarının performans hedefleri, her iki Premium SSD ve standart SSD 'Ler için de artmıştır.

**Hangi bölgeler, ' de desteklenen 8 TiB, 16 TiB ve 32 TiB tarafından desteklenen disk boyutlarıdır?**

8 TiB, 16 TiB ve 32 TiB disk SKU 'Ları, küresel Azure, Microsoft Azure Kamu ve Azure Çin 21Vianet altındaki tüm bölgelerde desteklenir.

**Tüm disk boyutlarında konak önbelleğini etkinleştirmeyi destekliyoruz.**

Salt okunur ve okuma/yazma işlemleri için 4 TiB 'den az disk boyutlarında konak önbelleğe alma destekliyoruz. 4 TiB 'den fazla disk boyutları için, None dışında önbelleğe alma seçeneğini ayarlamayı desteklemiyoruz. VM 'de önbelleğe alınan verilerle daha iyi performans artışı gözlemleyebileceğiniz daha küçük disk boyutları için önbelleğe almayı öneririz.

## <a name="what-if-my-question-isnt-answered-here"></a>Sorum burada yanıtlanmazsa ne olacak?

Sorunuz burada listelenmiyorsa, bize bilgi verin ve bir yanıt bulmanıza yardımcı olabiliriz. Açıklamalarda Bu makalenin sonuna bir soru gönderebilirsiniz. Azure depolama ekibi ve bu makaleyle ilgili diğer topluluk üyeleriyle birlikte çalışmak için MSDN [Azure depolama Forumu](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)' nu kullanın.

Özellik istemek için isteklerinizi ve fikirlerinizi [Azure Storage geri bildirim forumuna](https://feedback.azure.com/forums/217298-storage)gönderin.
