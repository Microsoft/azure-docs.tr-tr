---
title: VM 'Leri Azure Premium depolamaya geçirme | Microsoft Docs
description: Mevcut sanal makinelerinizi Azure Premium depolamaya geçirin. Premium Depolama, Azure sanal makinelerinde çalışan g/ç yoğunluklu iş yükleri için yüksek performanslı ve düşük gecikmeli disk desteği sunar.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 1bf46240303d1f31cd09c1a2723e18d27d3ef789
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124694"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Azure Premium Depolama 'ya geçiş (yönetilmeyen diskler)

> [!NOTE]
> Bu makalede, yönetilmeyen standart diskleri kullanan bir sanal makinenin yönetilmeyen Premium diskler kullanan bir VM 'ye geçirilmesi açıklanmaktadır. Yeni VM 'Ler için Azure yönetilen diskleri kullanmanızı ve önceki yönetilmeyen disklerinizi yönetilen disklere dönüştürmenizi öneririz. Yönetilen diskler, gerek kalmaması için temel depolama hesaplarını işler. Daha fazla bilgi için lütfen [yönetilen disklerimize genel bakış](../../virtual-machines/windows/managed-disks-overview.md)bölümüne bakın.
>

Azure Premium Depolama, g/ç yoğunluklu iş yüklerini çalıştıran sanal makineler için yüksek performanslı ve düşük gecikmeli disk desteği sunar. Uygulamanızın VM disklerini Azure Premium depolamaya geçirerek bu disklerin hızını ve performansından yararlanabilirsiniz.

Bu kılavuzun amacı, Azure Premium Storage 'ın yeni kullanıcılarının geçerli sisteminden Premium depolamaya sorunsuz bir geçiş yapmaya daha iyi hazırlanmasına yardımcı olmak için tasarlanmıştır. Kılavuz, bu işlemdeki anahtar bileşenlerinden üçünü adresler:

* [Premium Depolama 'ya geçiş planlaması](#plan-the-migration-to-premium-storage)
* [Sanal sabit diskleri (VHD 'ler) Premium depolamaya hazırlama ve kopyalama](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Premium Depolama kullanarak Azure sanal makinesi oluşturma](#create-azure-virtual-machine-using-premium-storage)

VM 'Leri diğer platformlardan Azure Premium Depolama 'ya geçirebilir veya mevcut Azure VM 'lerini standart depolamadan Premium depolamaya geçirebilirsiniz. Bu kılavuzda iki senaryonun adımları ele alınmaktadır. Senaryonuza bağlı olarak ilgili bölümde belirtilen adımları izleyin.

> [!NOTE]
> Premium SSD 'lerin bir özelliklerine genel bakış ve fiyatlandırma hakkında bilgi edinebilirsiniz: [IaaS VM 'leri için bir disk türü seçin](../../virtual-machines/windows/disks-types.md#premium-ssd). Uygulamanızın en iyi performansı için yüksek ıOPS gerektiren tüm sanal makine diskleri için Azure Premium Depolama 'ya geçiş yapmanızı öneririz. Diskiniz yüksek ıOPS gerektirmiyorsa, sanal makine disk verilerini SSD 'Ler yerine sabit disk sürücülerinde (HDD) depolayan standart depolamada tutarak maliyetleri sınırlayabilirsiniz.
>

Geçiş işleminin tamamen tamamlanması, bu kılavuzda belirtilen adımlardan önce ve sonra ek eylemler yapılmasını gerektirebilir. Örnek olarak, sanal ağların veya bitiş noktalarının yapılandırılması ya da uygulama içinde, uygulamanızda bazı kapalı kalma süresi gerektirebilecek kod değişiklikleri yapılması sayılabilir. Bu eylemler her bir uygulama için benzersizdir ve bunları, Premium depolamaya mümkün olduğunca sorunsuz şekilde tam geçiş yapmak için bu kılavuzda belirtilen adımlarla birlikte tamamlamalısınız.

## <a name="plan-the-migration-to-premium-storage"></a>Premium Depolama 'ya geçiş planlaması
Bu bölüm, bu makaledeki geçiş adımlarını izlemeye hazırmanızı sağlar ve VM ve disk türlerinde en iyi kararı vermenize yardımcı olur.

### <a name="prerequisites"></a>Önkoşullar
* Bir Azure aboneliğine ihtiyacınız olacaktır. Bir tane yoksa, bir aylık [ücretsiz deneme](https://azure.microsoft.com/pricing/free-trial/) aboneliği oluşturabilir veya daha fazla seçenek Için [Azure fiyatlandırmasını](https://azure.microsoft.com/pricing/) ziyaret edebilirsiniz.
* PowerShell cmdlet 'lerini yürütmek için Microsoft Azure PowerShell modüle ihtiyacınız olacaktır. Yükleme noktası ve yükleme yönergeleri için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/overview).
* Premium depolamada çalışan Azure VM 'lerini kullanmayı planlarken, Premium depolama özellikli VM 'Leri kullanmanız gerekir. Premium depolama özellikli VM 'lerle hem standart hem de Premium Depolama disklerini kullanabilirsiniz. Premium depolama diskleri, daha sonra daha fazla VM türüyle kullanılabilir olacaktır. Tüm kullanılabilir Azure VM disk türleri ve boyutları hakkında daha fazla bilgi için bkz. [sanal makineler Için boyutlar](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ve [Cloud Services için](../../cloud-services/cloud-services-sizes-specs.md)boyutlar.

### <a name="considerations"></a>Dikkat edilmesi gerekenler
Azure VM, uygulamalarınızın VM başına 256 TB 'a kadar depolama alanına sahip olması için çeşitli Premium Depolama disklerinin iliştirmesini destekler. Premium Depolama ile uygulamalarınız VM başına 80.000 ıOPS (saniye başına giriş/çıkış işlemi) ve okuma işlemleri için çok düşük gecikme süreleriyle VM başına 2000 MB ve saniyede MB elde edebilir. Çeşitli VM 'Lerde ve disklerde seçenekleriniz vardır. Bu bölüm, iş yükünüze en uygun bir seçenek bulmanıza yardımcı olur.

#### <a name="vm-sizes"></a>VM boyutları
Azure VM boyutu belirtimleri, [sanal makineler Için boyutlar](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)bölümünde listelenmiştir. Premium depolamayla çalışan sanal makinelerin performans özelliklerini gözden geçirin ve iş yükünüze en uygun VM boyutunu seçin. Disk trafiğini yönlendirmek için sanal makinenizde yeterli kullanılabilir bant genişliği olduğundan emin olun.

#### <a name="disk-sizes"></a>Disk boyutları
VM 'niz ile kullanılabilecek beş tür disk vardır ve her birinin belirli IOPS ve aktarım hızı limitleri vardır. Kapasite, performans, ölçeklenebilirlik ve en yoğun yük bakımından uygulamanızın gereksinimlerine bağlı olarak, sanal makinenizin disk türünü seçerken bu limitleri dikkate alın.

| Premium diskler türü  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Disk boyutu           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| Disk başına IOPS       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Disk başına aktarım hızı | saniyede 100 MB | saniyede 150 MB | saniyede 200 MB | saniyede 250 MB | saniyede 250 MB |

İş yükünüze bağlı olarak, VM 'niz için ek veri disklerinin gerekli olup olmadığını saptayın. Sanal makinenize birkaç kalıcı veri diski ekleyebilirsiniz. Gerekirse, birimin kapasitesini ve performansını artırmak için disklerin genelinde bir dizi oluşturabilirsiniz. (Bkz. disk şeritleme [](../../virtual-machines/windows/premium-storage-performance.md#disk-striping)nedir.) Premium depolama veri disklerini [depolama alanları][4]kullanarak ayırdıysanız, kullanılan her disk için tek bir sütunla yapılandırmalısınız. Aksi halde, disklerin disk genelindeki düzensiz dağıtımı nedeniyle, Şeritli birimin genel performansı beklenenden daha düşük olabilir. Linux VM 'Ler için *mdaddm* yardımcı programını kullanarak aynı elde edebilirsiniz. Ayrıntılar için bkz. [Linux üzerinde yazılım RAID yapılandırma](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) makalesi.

#### <a name="storage-account-scalability-targets"></a>Depolama hesabı ölçeklenebilirlik hedefleri
Premium Depolama hesaplarında, [Azure depolama ölçeklenebilirlik ve performans hedeflerine](storage-scalability-targets.md)ek olarak aşağıdaki ölçeklenebilirlik hedefleri vardır. Uygulama gereksinimleriniz tek bir depolama hesabının ölçeklenebilirlik hedeflerini aşarsa, uygulamanızı birden çok depolama hesabı kullanacak şekilde derleyin ve verilerinizi bu depolama hesaplarında bölümleyin.

| Toplam hesap kapasitesi | Yerel olarak yedekli depolama hesabı için toplam bant genişliği |
|:--- |:--- |
| Disk kapasitesi: 35TB<br />Anlık görüntü kapasitesi: 10 TB |Gelen + giden için saniyede 50 Gigabit |

Premium Depolama belirtimleri hakkında daha fazla bilgi için [Azure depolama ölçeklenebilirlik ve performans hedefleri](storage-scalability-targets.md#premium-performance-storage-account-scale-limits)' ne göz atın.

#### <a name="disk-caching-policy"></a>Disk önbelleğe alma ilkesi
Varsayılan olarak, disk önbelleğe alma ilkesi tüm Premium veri diskleri için *salt okunurdur* ve VM 'ye bağlı olan Premium işletim sistemi diski Için *okuma yazma* işlemi yapılır. Bu yapılandırma ayarı, uygulamanızın IOs için en iyi performansı elde etmek için önerilir. Daha iyi uygulama performansı elde edebilmeniz için, yazma ağır veya salt yazılır veri diskleri (örneğin, SQL Server günlük dosyaları) için disk önbelleğe almayı devre dışı bırakın. Var olan veri disklerinin önbellek ayarları, *set-AzureDataDisk* cmdlet 'inin [Azure Portal](https://portal.azure.com) veya *-hostcaching* parametresi kullanılarak güncelleştirilemeyebilir.

#### <a name="location"></a>Location
Azure Premium depolamanın kullanılabildiği bir konum seçin. Kullanılabilir konumlara ilişkin güncel bilgiler için bkz. [bölgeye göre Azure hizmetleri](https://azure.microsoft.com/regions/#services) . VM için diskleri depolayan depolama hesabıyla aynı bölgede bulunan VM 'Ler, ayrı bölgelerde olduklarından çok daha iyi performans sağlayacaktır.

#### <a name="other-azure-vm-configuration-settings"></a>Diğer Azure VM yapılandırma ayarları
Azure VM oluştururken, belirli VM ayarlarını yapılandırmanız istenir. Daha sonra değiştirebilmeniz veya daha sonra ekleyebilmek için, VM 'nin kullanım ömrü boyunca birkaç ayar düzeltildiğinde unutmayın. Bu Azure VM yapılandırma ayarlarını gözden geçirin ve bunların iş yükü gereksinimlerinize uygun şekilde yapılandırıldığından emin olun.

### <a name="optimization"></a>İyileştirme
[Azure Premium Depolama: Yüksek performans](../../virtual-machines/windows/premium-storage-performance.md) için tasarım, Azure Premium Depolama kullanarak yüksek performanslı uygulamalar oluşturmaya yönelik yönergeler sağlar. Uygulamanız tarafından kullanılan teknolojiler için geçerli olan performans en iyi uygulamaları ile Birleşik yönergeleri izleyebilirsiniz.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Sanal sabit diskleri (VHD 'ler) Premium depolamaya hazırlama ve kopyalama
Aşağıdaki bölümde, VM 'nizden VHD 'leri hazırlama ve VHD 'leri Azure depolama 'ya kopyalama yönergeleri sunulmaktadır.

* [Senaryo 1: "Mevcut Azure VM 'lerini Azure Premium depolamaya geçirdim."](#scenario1)
* [Senaryo 2: "Diğer platformlardaki VM 'Leri Azure Premium Depolama 'ya geçirdim."](#scenario2)

### <a name="prerequisites"></a>Önkoşullar
VHD 'leri geçişe hazırlamak için şunlar gerekir:

* Bir Azure aboneliği, bir depolama hesabı ve bu depolama hesabındaki, VHD 'nizi kopyalayabilmeniz için bir kapsayıcı. Hedef depolama hesabının, gereksiniminize bağlı olarak bir standart veya Premium depolama hesabı olabileceğini unutmayın.
* İçinden birden çok VM örneği oluşturmayı planlıyorsanız VHD 'yi genelleştirmek için bir araç. Örneğin, Windows için Sysprep veya Ubuntu için vırt-Sysprep.
* VHD dosyasını depolama hesabına yüklemek için bir araç. Bkz. [AzCopy komut satırı yardımcı programıyla veri aktarma](storage-use-azcopy.md) veya [Azure Depolama Gezgini](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)kullanma. Bu kılavuzda, AzCopy aracını kullanarak VHD 'nizin kopyalanması açıklanmaktadır.

> [!NOTE]
> AzCopy ile zaman uyumlu kopyalama seçeneğini belirlerseniz, en iyi performans için, hedef depolama hesabıyla aynı bölgede yer alan bir Azure VM 'den bu araçlardan birini çalıştırarak VHD 'nizi kopyalayın. Bir VHD 'yi farklı bir bölgedeki Azure VM 'den kopyalıyorsanız, performanağınız daha yavaş olabilir.
>
> Sınırlı bant genişliğine sahip büyük miktarda veriyi kopyalamak için [Azure içeri/dışarı aktarma hizmetini kullanarak blob depolamaya veri aktarmayı](../storage-import-export-service.md)düşünün; Bu, sabit disk sürücülerine bir Azure veri merkezine aktararak verilerinizi aktarmanızı sağlar. Azure Içeri/dışarı aktarma hizmetini kullanarak verileri yalnızca standart depolama hesabına kopyalayabilirsiniz. Veriler standart depolama hesabınızda olduktan sonra, verileri Premium Depolama hesabınıza aktarmak için [kopyalama blob API 'sini](https://msdn.microsoft.com/library/azure/dd894037.aspx) veya AzCopy öğesini kullanabilirsiniz.
>
> Microsoft Azure yalnızca sabit boyutlu VHD dosyalarını desteklediğini unutmayın. VHDX dosyaları veya dinamik VHD 'ler desteklenmez. Dinamik bir VHD varsa [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) cmdlet 'ini kullanarak onu sabit boyuta dönüştürebilirsiniz.
>
>

### <a name="scenario1"></a>Senaryo 1: "Mevcut Azure VM 'lerini Azure Premium depolamaya geçirdim."
Mevcut Azure VM 'lerini geçiriyorsanız VM 'yi durdurun, VHD türüne göre VHD 'ler hazırlayın ve sonra da AzCopy veya PowerShell ile VHD 'yi kopyalayın.

Temiz bir durum geçirmek için VM 'nin tamamen kapanması gerekir. Geçiş tamamlanana kadar kesinti süresi olacaktır.

#### <a name="step-1-prepare-vhds-for-migration"></a>1\.Adım VHD 'leri geçiş için hazırlama
Mevcut Azure sanal makinelerini Premium depolamaya geçiriyorsanız, VHD 'niz şu şekilde olabilir:

* Genelleştirilmiş bir işletim sistemi görüntüsü
* Benzersiz bir işletim sistemi diski
* Veri diski

Aşağıda, VHD 'nizi hazırlamaya yönelik bu 3 senaryoya kılavuzluk ederiz.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Birden çok VM örneği oluşturmak için genelleştirilmiş bir Işletim sistemi VHD 'SI kullanın
Birden çok genel Azure VM örneği oluşturmak için kullanılacak bir VHD 'yi karşıya yüklüyorsanız, önce bir Sysprep yardımcı programını kullanarak VHD 'yi genelleştirmelisiniz. Bu, şirket içinde veya bulutta bulunan bir VHD için geçerlidir. Sysprep, makineye özgü tüm bilgileri VHD 'den kaldırır.

> [!IMPORTANT]
> Bir anlık görüntü alın veya VM 'nizi genelleştirmeye başlamadan önce yedekleyin. Sysprep çalıştırmak, sanal makine örneğini durdurur ve serbest bırakılır. Aşağıdaki adımları izleyerek Sysprep bir Windows işletim sistemi VHD 'SI. Sysprep komutunu çalıştırmanın sanal makineyi kapatmanızı gerektirdiğini unutmayın. Sysprep hakkında daha fazla bilgi için bkz. [Sysprep genel bakış](https://technet.microsoft.com/library/hh825209.aspx) veya [Sysprep Teknik Başvurusu](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Yönetici olarak bir komut Istemi penceresi açın.
2. Sysprep 'ı açmak için aşağıdaki komutu girin:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Sistem Hazırlama aracında sistem hazır olmayan deneyim (OOBE) seçeneğini belirleyin, generalize onay kutusunu seçin, **Kapat**' ı seçin ve ardından aşağıdaki görüntüde gösterildiği gibi **Tamam**' a tıklayın. Sysprep, işletim sistemini genelleştirirsiniz ve sistemi kapatır.

    ![][1]

Ubuntu sanal makinesi için, vırt-Sysprep ' i kullanarak aynı elde edin. Daha fazla bilgi için bkz. [vırt-Sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) . Ayrıca bkz. diğer Linux işletim sistemleri için açık kaynak [Linux sunucu sağlama yazılımı](https://www.cyberciti.biz/tips/server-provisioning-software.html) .

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Tek bir VM örneği oluşturmak için benzersiz bir Işletim sistemi VHD 'SI kullanın
VM üzerinde çalışan, makineye özgü verileri gerektiren bir uygulamanız varsa, VHD 'yi genelleştirmeyin. Genelleştirilmiş olmayan bir VHD, benzersiz bir Azure VM örneği oluşturmak için kullanılabilir. Örneğin, VHD 'niz üzerinde etki alanı Denetleyicsahipseniz, Sysprep 'in yürütülmesi bir etki alanı denetleyicisi olarak etkisiz hale getirir. VM 'niz üzerinde çalışan uygulamaları ve VHD 'yi genelleştirmeden önce Sysprep çalıştırmanın etkilerini gözden geçirin.

##### <a name="register-data-disk-vhd"></a>Veri diski VHD 'sini kaydetme
Azure 'da geçiş yapmak için veri diskleriniz varsa, bu veri disklerini kullanan VM 'Lerin kapatıldığından emin olmanız gerekir.

VHD 'yi Azure Premium Depolama 'ya kopyalamak ve sağlanmış bir veri diski olarak kaydetmek için aşağıda açıklanan adımları izleyin.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>2\.Adım VHD 'niz için hedef oluşturma
VHD 'nizin sürdürülmesi için bir depolama hesabı oluşturun. VHD 'lerinizin depolanacağı yeri planlarken aşağıdaki noktaları göz önünde bulundurun:

* Hedef Premium depolama hesabı.
* Depolama hesabı konumu, son aşamada oluşturacağınız, Premium depolama özellikli Azure VM 'Lerle aynı olmalıdır. Yeni bir depolama hesabına kopyalayabilir veya gereksinimlerinize göre aynı depolama hesabını kullanmayı planlarsınız.
* Sonraki aşama için hedef depolama hesabının depolama hesabı anahtarını kopyalayın ve kaydedin.

Veri diskleri için, bazı veri disklerini standart bir depolama hesabında tutmayı seçebilirsiniz (örneğin, daha soğuk depolama olan diskler), ancak üretim iş yükü için tüm verileri Premium Depolama kullanmak üzere taşımayı önemle öneririz.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>3. adım. AzCopy veya PowerShell ile VHD 'YI kopyalama
Bu iki seçenekten birini işlemek için kapsayıcı yolunu ve depolama hesabı anahtarınızı bulmanız gerekir. Kapsayıcı yolu ve depolama hesabı anahtarı, **Azure Portal** > **depolamada**bulunabilir. Kapsayıcı URL 'si "https:\//myaccount.blob.Core.Windows.net/myContainer/" şeklinde olacaktır.

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Seçenek 1: AzCopy ile bir VHD 'YI kopyalama (zaman uyumsuz kopya)
AzCopy kullanarak, VHD 'YI Internet üzerinden kolayca yükleyebilirsiniz. VHD 'lerin boyutuna bağlı olarak bu işlem zaman alabilir. Bu seçeneği kullanırken depolama hesabı giriş/çıkış sınırlarını denetlemeyi unutmayın. Ayrıntılar için bkz. [Azure Storage ölçeklenebilirlik ve performans hedefleri](storage-scalability-targets.md) .

1. AzCopy öğesini buradan indirip yükleyin: [AzCopy 'in en son sürümü](https://aka.ms/downloadazcopy)
2. Azure PowerShell açın ve AzCopy 'ın yüklü olduğu klasöre gidin.
3. VHD dosyasını "kaynak" kaynağından "hedefe" kopyalamak için aşağıdaki komutu kullanın.

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Örnek:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```
 
   AzCopy komutunda kullanılan parametrelerin açıklamaları aşağıda verilmiştir:

   * **/Source:** _kaynak:&gt; &lt;_ VHD 'YI içeren klasörün veya depolama kapsayıcısı URL 'sinin konumu.
   * **/SourceKey:** _kaynak-hesap-anahtar&gt;: &lt;_ Kaynak depolama hesabının depolama hesabı anahtarı.
   * **/Dest:** _hedef:&gt; &lt;_ VHD 'yi kopyalamak için depolama kapsayıcısı URL 'SI.
   * **/Destkey:** _hedef-hesap-anahtar&gt;: &lt;_ Hedef depolama hesabının depolama hesabı anahtarı.
   * **/Model:** _dosya adı:&gt; &lt;_ Kopyalanacak VHD 'nin dosya adını belirtin.

AzCopy aracını kullanma hakkında ayrıntılı bilgi için bkz. [AzCopy komut satırı yardımcı programıyla veri aktarma](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Seçenek 2: Bir VHD 'YI PowerShell ile kopyalama (eşitlenmiş kopya)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ayrıca start-AzStorageBlobCopy PowerShell cmdlet 'ini kullanarak VHD dosyasını kopyalayabilirsiniz. VHD 'yi kopyalamak için Azure PowerShell ' de aşağıdaki komutu kullanın. < > Değerlerini, kaynak ve hedef depolama hesabınızdaki karşılık gelen değerlerle değiştirin. Bu komutu kullanmak için hedef depolama hesabınızda VHD adında bir kapsayıcı olması gerekir. Kapsayıcı yoksa, komutunu çalıştırmadan önce bir tane oluşturun.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Örnek:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Senaryo 2: "Diğer platformlardaki VM 'Leri Azure Premium Depolama 'ya geçirdim."
VHD 'yi Azure olmayan bulut depolamadan Azure 'a geçiriyorsanız, önce VHD 'yi yerel bir dizine aktarmanız gerekir. VHD 'nin depolandığı yerel dizinin tüm kaynak yolunu ve ardından Azure depolama 'ya yüklemek için AzCopy komutunu kullanın.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>1\.Adım VHD 'YI yerel bir dizine aktarma
##### <a name="copy-a-vhd-from-aws"></a>AWS 'den bir VHD kopyalama
1. AWS kullanıyorsanız, EC2 örneğini bir Amazon S3 demetini içindeki bir VHD 'ye aktarın. Amazon EC2 komut satırı arabirimi (CLı) aracını yüklemek üzere Amazon EC2 örneklerini dışarı aktarmak için Amazon belgelerinde açıklanan adımları izleyin ve EC2 örneğini bir VHD dosyasına aktarmak için Create-Instance-Export-Task komutunu çalıştırın. **Oluşturma-örnek-dışarı aktarma-görev** komutunu&#95;çalıştırırken DISK&#95;görüntüsü biçim değişkeni için **VHD 'yi** kullandığınızdan emin olun. İçe aktarılmış VHD dosyası, bu işlem sırasında belirlediğiniz Amazon S3 demet içine kaydedilir.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. S3 demetini 'nden VHD dosyasını indirin. VHD dosyasını seçin ve ardından **Eylemler** > ' i**indirin**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Diğer Azure dışı buluttan bir VHD 'yi kopyalama
VHD 'yi Azure olmayan bulut depolamadan Azure 'a geçiriyorsanız, önce VHD 'yi yerel bir dizine aktarmanız gerekir. VHD 'nin depolandığı yerel dizinin tüm kaynak yolunu kopyalayın.

##### <a name="copy-a-vhd-from-on-premises"></a>Şirket içinden bir VHD 'yi kopyalama
VHD 'yi şirket içi bir ortamdan geçiriyorsanız, VHD 'nin depolandığı tam kaynak yoluna ihtiyacınız olacaktır. Kaynak yolu bir sunucu konumu veya dosya paylaşma olabilir.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>2\.Adım VHD 'niz için hedef oluşturma
VHD 'nizin sürdürülmesi için bir depolama hesabı oluşturun. VHD 'lerinizin depolanacağı yeri planlarken aşağıdaki noktaları göz önünde bulundurun:

* Hedef depolama hesabı, uygulamanızın gereksinimlerine bağlı olarak standart veya Premium depolama alanı olabilir.
* Depolama hesabı bölgesi, son aşamada oluşturacağınız, Premium depolama özellikli Azure VM 'Lerle aynı olmalıdır. Yeni bir depolama hesabına kopyalayabilir veya gereksinimlerinize göre aynı depolama hesabını kullanmayı planlarsınız.
* Sonraki aşama için hedef depolama hesabının depolama hesabı anahtarını kopyalayın ve kaydedin.

Üretim iş yükü için tüm verileri Premium Depolama kullanmak üzere taşımayı önemle öneririz.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Adım 3. VHD 'YI Azure depolama 'ya yükleme
Artık yerel dizinde VHD 'niz olduğuna göre,. vhd dosyasını Azure depolama 'ya yüklemek için AzCopy veya AzurePowerShell kullanabilirsiniz. Her iki seçenek de aşağıda verilmiştir:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Seçenek 1: . Vhd dosyasını karşıya yüklemek için Azure PowerShell Add-AzureVhd kullanma

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Örnek \<bir URI > **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_** olabilir. Örnek \<bir FileInfo > **_"c:\yol\to\upload.vhd"_** olabilir.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Seçenek 2: . Vhd dosyasını karşıya yüklemek için AzCopy kullanma
AzCopy kullanarak, VHD 'YI Internet üzerinden kolayca yükleyebilirsiniz. VHD 'lerin boyutuna bağlı olarak bu işlem zaman alabilir. Bu seçeneği kullanırken depolama hesabı giriş/çıkış sınırlarını denetlemeyi unutmayın. Ayrıntılar için bkz. [Azure Storage ölçeklenebilirlik ve performans hedefleri](storage-scalability-targets.md) .

1. AzCopy öğesini buradan indirip yükleyin: [AzCopy 'in en son sürümü](https://aka.ms/downloadazcopy)
2. Azure PowerShell açın ve AzCopy 'ın yüklü olduğu klasöre gidin.
3. VHD dosyasını "kaynak" kaynağından "hedefe" kopyalamak için aşağıdaki komutu kullanın.

   ```azcopy
      AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

   Örnek:

   ```azcopy
      AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
   ```

   AzCopy komutunda kullanılan parametrelerin açıklamaları aşağıda verilmiştir:

   * **/Source:** _kaynak:&gt; &lt;_ VHD 'YI içeren klasörün veya depolama kapsayıcısı URL 'sinin konumu.
   * **/SourceKey:** _kaynak-hesap-anahtar&gt;: &lt;_ Kaynak depolama hesabının depolama hesabı anahtarı.
   * **/Dest:** _hedef:&gt; &lt;_ VHD 'yi kopyalamak için depolama kapsayıcısı URL 'SI.
   * **/Destkey:** _hedef-hesap-anahtar&gt;: &lt;_ Hedef depolama hesabının depolama hesabı anahtarı.
   * **/BlobType: sayfa:** Hedefin bir Sayfa Blobu olduğunu belirtir.
   * **/Model:** _dosya adı:&gt; &lt;_ Kopyalanacak VHD 'nin dosya adını belirtin.

AzCopy aracını kullanma hakkında ayrıntılı bilgi için bkz. [AzCopy komut satırı yardımcı programıyla veri aktarma](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Bir VHD yüklemek için diğer seçenekler
Ayrıca, aşağıdaki yollardan birini kullanarak depolama hesabınıza bir VHD yükleyebilirsiniz:

* [Azure depolama kopya blobu API 'SI](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Blobları karşıya yükleme Azure Depolama Gezgini](https://azurestorageexplorer.codeplex.com/)
* [Depolama Içeri/dışarı aktarma hizmeti REST API başvurusu](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Tahmini karşıya yükleme süresi 7 günden uzunsa Içeri/dışarı aktarma hizmeti kullanmanızı öneririz. Veri boyutu ve aktarım biriminden süreyi tahmin etmek için [Datatransferspeedhesaplayıcı](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) ' yı kullanabilirsiniz.
>
> İçeri/dışarı aktarma, standart bir depolama hesabına kopyalamak için kullanılabilir. AzCopy gibi bir araç kullanarak standart depolamadan Premium depolama hesabına kopyalamanız gerekir.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Premium Depolama kullanarak Azure VM 'Leri oluşturma
VHD 'yi karşıya yükledikten veya istenen depolama hesabına kopyaladıktan sonra, bu bölümdeki yönergeleri izleyerek, senaryonuza bağlı olarak VHD 'yi bir IŞLETIM sistemi görüntüsü veya işletim sistemi diski olarak kaydedin ve bundan sonra bir VM örneği oluşturun. Veri diski VHD 'SI, oluşturulduktan sonra VM 'ye iliştirilebilir.
Bu bölümün sonunda örnek bir geçiş betiği verilmiştir. Bu basit betik tüm senaryolarla eşleşmiyor. Betiği, belirli senaryonuz ile eşleşecek şekilde güncelleştirmeniz gerekebilir. Bu betiğin senaryonuz için geçerli olup olmadığını görmek için [örnek geçiş betiği](#a-sample-migration-script)bölümüne bakın.

### <a name="checklist"></a>Denetim listesi
1. Tüm VHD disklerinin kopyalanması tamamlanana kadar bekleyin.
2. Premium depolamanın geçirdiğiniz bölgede kullanılabilir olduğundan emin olun.
3. Kullanacağınız yeni VM serisini belirleyin. Bu bir Premium depolama alanı olmalıdır ve boyut, bölgedeki kullanılabilirliğine ve gereksinimlerinize bağlı olarak olmalıdır.
4. Kullanacağınız VM boyutunun tam olarak belirleyin. VM boyutunun, sahip olduğunuz veri diski sayısını desteklemeye yetecek kadar büyük olması gerekir. Örneğin 4 veri diskine sahipseniz, VM 'nin 2 veya daha fazla çekirdeği olmalıdır. Ayrıca, güç, bellek ve ağ bant genişliği gereksinimlerini işlemeyi göz önünde bulundurun.
5. Hedef bölgede bir Premium depolama hesabı oluşturun. Bu, yeni VM için kullanacağınız hesaptır.
6. Disk listesi ve karşılık gelen VHD blob 'ları dahil olmak üzere geçerli VM ayrıntılarına sahip olmanız gerekir.

Uygulamanızı kapalı kalma süresi için hazırlayın. Temiz bir geçiş yapmak için, geçerli sistemdeki tüm işlemleri durdurmanız gerekir. Yalnızca yeni platforma geçirebileceğiniz tutarlı bir duruma sahip olabilirsiniz. Kesinti süresi, geçirilecek disklerdeki veri miktarına bağlıdır.

> [!NOTE]
> Özelleştirilmiş bir VHD diskinden bir Azure Resource Manager VM oluşturuyorsanız, lütfen mevcut diski kullanarak Kaynak Yöneticisi VM 'yi dağıtmak için [Bu şablona](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) bakın.
>
>

### <a name="register-your-vhd"></a>VHD 'nizi kaydetme
İşletim sistemi VHD 'sinden bir VM oluşturmak veya yeni bir VM 'ye veri diski eklemek için, önce bunları kaydetmeniz gerekir. VHD 'nin senaryosuna bağlı olarak aşağıdaki adımları izleyin.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Birden çok Azure VM örneği oluşturmak için genelleştirilmiş Işletim sistemi VHD 'SI
Genelleştirilmiş işletim sistemine bir veya daha fazla VM örneği oluşturabilmeniz için, depolama hesabına bir **Azure VM** görüntüsü olarak kaydedin. VHD 'nizi bir Azure VM işletim sistemi görüntüsü olarak kaydetmek için aşağıdaki PowerShell cmdlet 'lerini kullanın. VHD 'nin kopyalandığı tüm kapsayıcı URL 'sini sağlayın.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Bu yeni Azure VM görüntüsünün adını kopyalayın ve kaydedin. Yukarıdaki örnekte, *OSImageName*' dir.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Tek bir Azure VM örneği oluşturmak için benzersiz Işletim sistemi VHD 'SI
Benzersiz işletim sistemi VHD 'SI depolama hesabına yüklendikten sonra, bundan bir VM örneği oluşturabilmeniz için **Azure işletim sistemi diski** olarak kaydedin. VHD 'nizi bir Azure işletim sistemi diski olarak kaydetmek için bu PowerShell cmdlet 'lerini kullanın. VHD 'nin kopyalandığı tüm kapsayıcı URL 'sini sağlayın.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Bu yeni Azure işletim sistemi diskinin adını kopyalayın ve kaydedin. Yukarıdaki örnekte, *OSDisk*'dir.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Yeni Azure VM örneğine eklenecek veri diski VHD 'SI
Veri diski VHD 'SI depolama hesabına yüklendikten sonra, yeni DS serinizle, DSv2 serisi veya GS serisi Azure VM örneğine iliştirilebilecek şekilde Azure veri diski olarak kaydedin.

VHD 'nizi bir Azure veri diski olarak kaydetmek için bu PowerShell cmdlet 'lerini kullanın. VHD 'nin kopyalandığı tüm kapsayıcı URL 'sini sağlayın.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Bu yeni Azure veri diskinin adını kopyalayın ve kaydedin. Yukarıdaki örnekte *veri diski*bulunur.

### <a name="create-a-premium-storage-capable-vm"></a>Premium depolama özellikli VM oluşturma
İşletim sistemi görüntüsü veya işletim sistemi diski kaydedildikten sonra yeni bir DS serisi, DSv2 serisi veya GS serisi bir VM oluşturun. Kaydettiğiniz işletim sistemi görüntüsü veya işletim sistemi disk adını kullanacaksınız. Premium Depolama katmanından VM türünü seçin. Aşağıdaki örnekte, *Standard_DS2* VM boyutunu kullanıyoruz.

> [!NOTE]
> Kapasite ve performans gereksinimlerinizin yanı sıra kullanılabilir Azure disk boyutları ile eşleştiğinden emin olmak için disk boyutunu güncelleştirin.
>
>

Yeni VM 'yi oluşturmak için aşağıdaki adım adım PowerShell cmdlet 'lerini izleyin. İlk olarak, ortak parametreleri ayarlayın:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

İlk olarak, yeni sanal makinelerinizi barındırmakta olduğunuz bir bulut hizmeti oluşturun.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Sonra, senaryonuza bağlı olarak, kaydettiğiniz işletim sistemi görüntüsü veya işletim sistemi diskinden Azure VM örneğini oluşturun.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Birden çok Azure VM örneği oluşturmak için genelleştirilmiş Işletim sistemi VHD 'SI
Kaydolmakta olduğunuz **Azure işletim sistemi görüntüsünü** kullanarak bir veya daha fazla yeni DS SERISI Azure VM örneği oluşturun. Aşağıda gösterildiği gibi yeni VM oluştururken VM yapılandırmasında bu işletim sistemi görüntüsünün adını belirtin.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Tek bir Azure VM örneği oluşturmak için benzersiz Işletim sistemi VHD 'SI
Kaydettiğiniz **Azure işletim sistemi diskini** kullanarak yenı bir DS SERISI Azure VM örneği oluşturun. Yeni VM 'yi oluştururken aşağıda gösterildiği gibi VM yapılandırmasında bu işletim sistemi disk adını belirtin.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Bulut hizmeti, bölge, depolama hesabı, kullanılabilirlik kümesi ve önbelleğe alma ilkesi gibi diğer Azure VM bilgilerini belirtin. Sanal makine örneğinin ilişkili işletim sistemi veya veri disklerinde birlikte bulunması gerektiğini, bu nedenle seçilen bulut hizmeti, bölge ve depolama hesabının tümünün, bu disklerin temel alınan VHD 'Lerle aynı konumda olması gerekir.

### <a name="attach-data-disk"></a>Veri diski ekleme
Son olarak, veri diski VHD 'leri kayıtlıysa, bunları yeni Premium depolama özellikli Azure VM 'sine bağlayın.

Yeni VM 'ye veri diski eklemek ve önbelleğe alma ilkesini belirtmek için aşağıdaki PowerShell cmdlet 'ini kullanın. Aşağıdaki örnekte, önbelleğe alma ilkesi *salt okunur*olarak ayarlanmıştır.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Bu kılavuzun kapsamında olmayan uygulamanızı desteklemek için gereken ek adımlar olabilir.
>
>

### <a name="checking-and-plan-backup"></a>Yedekleme denetleniyor ve planlıyor
Yeni VM çalışır duruma getirildikten sonra, aynı oturum açma kimliğini ve parolayı kullanarak, özgün VM olarak da erişin ve her şeyin beklendiği gibi çalıştığını doğrulayın. Şeritli birimler dahil olmak üzere tüm ayarlar yeni VM 'de mevcut olacaktır.

Son adım, uygulamanın ihtiyaçlarına göre yeni VM için yedekleme ve bakım zamanlamasını planlıyor.

### <a name="a-sample-migration-script"></a>Örnek geçiş betiği
Geçirilecek birden fazla sanal makine varsa, PowerShell betikleri aracılığıyla Otomasyon yararlı olacaktır. Aşağıda, bir VM 'nin geçişini otomatikleştiren örnek bir komut dosyası verilmiştir. Aşağıdaki betiğin yalnızca bir örnek olduğunu ve geçerli VM diskleri hakkında bazı varsayımlar olduğunu unutmayın. Betiği, belirli senaryonuz ile eşleşecek şekilde güncelleştirmeniz gerekebilir.

Varsayımlar şunlardır:

* Klasik Azure VM 'Leri oluşturuyorsunuz.
* Kaynak işletim sistemi diskleriniz ve kaynak veri diskleriniz aynı depolama hesabı ve aynı kapsayıcıda. İşletim sistemi disklerinizin ve veri disklerinizin aynı yerde olmaması durumunda, VHD 'leri depolama hesapları ve kapsayıcılar üzerinden kopyalamak için AzCopy veya Azure PowerShell kullanabilirsiniz. Önceki adıma başvurun: [AzCopy veya PowerShell Ile VHD 'Yi kopyalayın](#copy-vhd-with-azcopy-or-powershell). Senaryolarınızı karşılamak için bu betiğin düzenlenmesinin başka bir seçimdir, ancak daha kolay ve daha hızlı olduğundan AzCopy veya PowerShell kullanmanızı öneririz.

Otomasyon betiği aşağıda verilmiştir. Metni, bilgilerinizi ile değiştirin ve betiği, belirli senaryonuzla eşleşecek şekilde güncelleştirin.

> [!NOTE]
> Mevcut betiği kullanmak, kaynak sanal makinenizin ağ yapılandırmasını korumaz. Geçirilen sanal makinelerinizdeki ağ ayarlarını yeniden yapılandırmaya ihtiyacınız olacak.
>
>

```powershell
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    https://azure.microsoft.com/documentation/articles/powershell-install-configure/
    https://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in seconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module version" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article https://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the source vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting until the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more customization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>İyileştirme
Geçerli VM yapılandırmanız, özellikle standart disklerle iyi çalışmak üzere özelleştirilebilir. Örneğin, dizili bir birimde birçok disk kullanarak performansı artırmak için. Örneğin, Premium depolamada ayrı olarak 4 disk kullanmak yerine, maliyeti tek bir diske sahip olacak şekilde iyileştirebiliyor olabilirsiniz. Bunun gibi iyileştirmeler, büyük/küçük harfe göre işlenmelidir ve geçişten sonra özel adımlara gerek duyar. Ayrıca, bu işlemin, kurulumda tanımlanan disk düzenine bağlı olan veritabanları ve uygulamalar için iyi çalışmadığına de göz önünde bulabilirsiniz.

##### <a name="preparation"></a>Hazırlık
1. Önceki bölümde açıklandığı gibi basit geçişi doldurun. Geçişten sonra yeni VM 'de iyileştirmeler gerçekleştirilecek.
2. İyileştirilmiş yapılandırma için gereken yeni disk boyutlarını tanımlayın.
3. Geçerli disklerin/birimlerin yeni disk belirtimlerine eşlenmesinin belirlenmesi.

##### <a name="execution-steps"></a>Yürütme adımları
1. Premium depolama VM 'sinde doğru boyutlara sahip yeni diskler oluşturun.
2. VM 'de oturum açın ve geçerli birimdeki verileri, bu birimle eşleşen yeni diske kopyalayın. Bunu, yeni bir diskle eşleşmesi gereken tüm geçerli birimler için yapın.
3. Ardından, uygulama ayarlarını yeni disklere geçiş yapmak için değiştirin ve eski birimleri ayırın.

Uygulamanın daha iyi disk performansına yönelik olarak ayarlanması için lütfen [yüksek performanslı](../../virtual-machines/windows/premium-storage-performance.md) makalemize ilişkin uygulama performansını en iyi duruma getirme bölümüne bakın.

### <a name="application-migrations"></a>Uygulama geçişleri
Veritabanları ve diğer karmaşık uygulamalar, geçiş için uygulama sağlayıcısı tarafından tanımlanan özel adımlara gerek duyar. Lütfen ilgili uygulama belgelerine başvurun. Örneğin genellikle veritabanları yedekleme ve geri yükleme aracılığıyla geçirilebilir.

## <a name="next-steps"></a>Sonraki adımlar
Sanal makineleri geçirmeye yönelik belirli senaryolar için aşağıdaki kaynaklara bakın:

* [Azure sanal makinelerini depolama hesapları arasında geçirme](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Bir Windows Server VHD oluşturun ve Azure 'a yükleyin.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bir Linux VHD oluşturma ve Azure 'a yükleme](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Sanal makineleri Amazon AWS 'den Microsoft Azure 'ye geçirme](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Ayrıca, Azure depolama ve Azure sanal makineleri hakkında daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [Azure Depolama](https://azure.microsoft.com/documentation/services/storage/)
* [Azure sanal makineleri](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [IaaS VM 'Leri için bir disk türü seçin](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
