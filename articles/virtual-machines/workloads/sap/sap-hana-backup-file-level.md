---
title: Dosya düzeyinde Azure Backup SAP HANA | Microsoft Docs
description: Azure sanal makinelerinde SAP HANA için iki önemli yedekleme olasılıkları vardır. Bu makalede dosya düzeyinde SAP HANA Azure Backup yer almaktadır
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 8860c943dafdb9d166510519d0fb058f523537b3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078910"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Dosya düzeyinde Azure Backup SAP HANA

## <a name="introduction"></a>Giriş

Bu, SAP HANA yedeğine ilişkin üç bölümlü ilgili makalelerin bir parçasıdır. [Azure sanal makineler 'de SAP HANA Için Yedekleme Kılavuzu](./sap-hana-backup-guide.md) , kullanmaya başlama hakkında bir genel bakış ve bilgi sağlar ve [depolama anlık görüntülerine göre SAP HANA yedekleme](./sap-hana-backup-storage-snapshots.md) , depolama anlık görüntü tabanlı yedekleme seçeneğini içerir.

Azure 'daki farklı VM türleri, farklı sayıda VHD 'nin eklenmiş olduğunu sağlar. Ayrıntılar, [Azure 'Da Linux sanal makineleri Için boyutlar](../../linux/sizes.md)bölümünde belgelenmiştir. Bu belgelerde başvurulan testler için 64 bağlı veri diskine izin veren bir GS5 Azure VM kullandık. Daha büyük SAP HANA sistemler için, veri ve günlük dosyaları için çok sayıda disk zaten alınmış olabilir ve muhtemelen en iyi disk GÇ işleme için yazılım şeridi oluşturma ile birlikte. Azure VM 'lerinde SAP HANA dağıtımlar için önerilen disk yapılandırmalarına ilişkin daha fazla bilgi için, [Azure işlemler Kılavuzu 'nda SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)makalesini okuyun. Yapılan öneriler, yerel yedeklemeler için de disk alanı önerilerini de dahil.

Azure Backup hizmeti ile Şu anda kullanılabilir SAP HANA yedekleme tümleştirmesi yok. Yedekleme/geri yükleme 'yi dosya düzeyinde yönetmenin standart yolu, SAP HANA Studio aracılığıyla veya SAP HANA SQL deyimleriyle dosya tabanlı bir yedekleme kullanmaktır. Daha fazla bilgi için bkz. [SAP HANA SQL ve sistem görünümleri başvurusu](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) .

![Bu şekilde SAP HANA Studio 'daki yedekleme menü öğesinin iletişim kutusu gösterilmektedir](media/sap-hana-backup-file-level/image022.png)

Bu şekilde SAP HANA Studio 'daki yedekleme menü öğesinin iletişim kutusu gösterilmektedir. Tür &quot;dosyası seçerken, dosyanın&quot; yedekleme dosyalarını SAP HANA yazdığı dosya sisteminde bir yol belirtmesi gerekir. Geri yükleme aynı şekilde çalışmaktadır.

Bu seçim basit ve düz ileri bir deyişle, bazı önemli noktalar vardır. Daha önce bahsedildiği gibi, bir Azure VM 'nin iliştirilebilecek veri diski sayısı sınırlaması vardır. Veritabanının boyutuna ve birden çok veri diskinde yazılım şeritleme gerektirebilecek disk işleme gereksinimlerine bağlı olarak, SAP HANA yedekleme dosyalarını VM 'nin dosya sistemlerinde depolama kapasitesi olmayabilir. Bu yedekleme dosyalarını taşımaya yönelik çeşitli seçenekler ve terabayt veri işlerken dosya boyutu kısıtlamalarını ve performansı yönetme Bu makalede daha sonra verilmiştir.

Toplam kapasiteye ilişkin daha fazla özgürlük sunan başka bir seçenek de Azure Blob Depolama ' dır. Tek bir blob da 1 TB ile sınırlandırıldığı sürece, tek bir blob kapsayıcısının toplam kapasitesi Şu anda 500 TB 'tır. Buna ek olarak, müşterilere maliyet avantajı olan seyrek erişimli &quot;&quot; BLOB depolama alanı olarak adlandırılan bir seçenek sunar. Bkz [. Azure Blob depolama: Seyrek Erişimli BLOB depolama hakkındaki](../../../storage/blobs/storage-blob-storage-tiers.md) Ayrıntılar için sık ve seyrek erişimli depolama katmanları.

Ek güvenlik için, SAP HANA yedeklemeleri depolamak için coğrafi olarak çoğaltılan bir depolama hesabı kullanın. Depolama hesabı çoğaltma hakkındaki ayrıntılar için bkz. [Azure Storage çoğaltma](../../../storage/common/storage-redundancy.md) .

Bunlardan biri, coğrafi olarak çoğaltılan ayrılmış bir yedekleme depolama hesabında SAP HANA yedeklemeleri için ayrılmış VHD 'ler yerleştirebilir. Ya da bir tane, SAP HANA yedeklemelerini tutan VHD 'leri coğrafi olarak çoğaltılan bir depolama hesabına veya farklı bir bölgedeki depolama hesabına kopyalayabilir.

## <a name="azure-backup-agent"></a>Azure Yedekleme aracısı

Azure Backup, Konuk işletim sistemine yüklenmesi gereken tüm VM 'Lerin yanı sıra yedekleme Aracısı aracılığıyla da dosya ve dizinleri yedekleme seçeneği sunar. Ancak bu aracı yalnızca Windows 'da desteklenir (bkz. [Kaynak Yöneticisi dağıtım modelini kullanarak Windows Server veya Istemcisini Azure 'A yedekleme](../../../backup/backup-configure-vault.md)).

Geçici bir çözüm, SAP HANA yedekleme dosyalarını Azure 'daki bir Windows sanal makinesine (örneğin, SAMBA Share aracılığıyla) kopyalamasıdır ve ardından Azure Backup Aracısı 'nı oradan kullanır. Teknik olarak mümkün olsa da, Linux ve Windows sanal makinesi arasındaki kopya nedeniyle yedekleme veya geri yükleme işleminin bir bit yavaşlamasına neden olur. Bu yaklaşımın izlenmesi önerilmez.

## <a name="azure-blobxfer-utility-details"></a>Azure blobxfer yardımcı programı ayrıntıları

Dizinleri ve dosyaları Azure depolama üzerinde depolamak için, bir tane CLı veya PowerShell kullanabilir ya da [Azure SDK 'lardan](https://azure.microsoft.com/downloads/)birini kullanarak bir araç geliştirebilirsiniz. Ayrıca Azure depolama 'ya veri kopyalamak için kullanıma yönelik kullanıma yönelik bir yardımcı program ve AzCopy de vardır. (bkz. [AzCopy komut satırı yardımcı programıyla veri aktarma](../../../storage/common/storage-use-azcopy.md)).

Bu nedenle, SAP HANA yedekleme dosyalarını kopyalamak için blobxfer kullanıldı. Bu, üretim ortamlarında birçok müşteri tarafından kullanılan ve [GitHub](https://github.com/Azure/blobxfer)'da kullanılabilen açık kaynaktır. Bu araç, bir birinin verileri doğrudan Azure Blob depolama veya Azure dosya paylaşımında kopyalamasına olanak sağlar. Ayrıca, birden çok dosya içeren bir dizin kopyalanırken MD5 karması veya otomatik paralellik gibi yararlı bir özellik yelpazesi sunar.

## <a name="sap-hana-backup-performance"></a>Yedekleme performansını SAP HANA

![Bu ekran görüntüsü, SAP HANA Studio 'daki SAP HANA yedekleme konsoludur](media/sap-hana-backup-file-level/image023.png)

Bu ekran görüntüsü, SAP HANA Studio 'daki SAP HANA yedekleme konsoludur. XFS dosya sistemi kullanılarak HANA VM 'ye bağlı tek bir Azure Standart depolama diskinde 230 GB yedeklemesini yapmak yaklaşık 42 dakika sürer.

![Bu ekran görüntüsü SAP HANA test sanal makinesinde YaST 'dir](media/sap-hana-backup-file-level/image024.png)

Bu ekran görüntüsü, SAP HANA test sanal makinesinde YaST 'dir. Biri, daha önce belirtildiği gibi SAP HANA yedekleme için 1 TB tek diski görebilir. 230 GB 'yi yedeklemek yaklaşık 42 dakika sürer. Ayrıca, 5 200 GB diskler eklenmiş ve yazılım RAID md0, bu beş Azure veri diskinin üzerine şeritle oluşturulmuştur.

![Beş bağlantılı Azure Standart depolama veri diski üzerinde şeritlerle aynı yedeklemeyi yazılım RAID 'de yineleme](media/sap-hana-backup-file-level/image025.png)

Beş bağlantılı Azure Standart depolama veri diski üzerinde şeritle aynı yedeklemeyi yazılım RAID 'de yineleme, yedekleme süresini 42 dakikadan sonra 10 dakikaya getirdi. Diskler VM 'ye önbelleklemeden iliştirilmedi. Bu nedenle, yedekleme zamanı için önemli disk yazma üretiminin ne kadar önemli olduğunu açıktır. Böylece, en iyi performans sürecini hızlandırmak için Azure Premium Depolama 'ya geçiş yapılır. Genel olarak, Azure Premium Depolama, üretim sistemleri için kullanılmalıdır.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>SAP HANA yedekleme dosyalarını Azure Blob depolamaya kopyalama

SAP HANA yedekleme dosyalarını hızlı bir şekilde depolamak için başka bir seçenek Azure Blob Depolama ' dır. Bir tek blob kapsayıcısının 500 TB 'lik bir sınırı vardır. Bu, M32ts, M32ls, M64ls ve GS5 VM Types for Azure kullanarak yeterli sayıda SAP HANA yedeklemesi SAP HANA. &quot;Müşteriler, sık&quot; ve &quot; [soğukBLOBdepolama(bkz.AzureBlobdepolama)arasındaseçimyapmışolabilir.&quot; Sık erişimli ve seyrek erişimli](../../../storage/blobs/storage-blob-storage-tiers.md)depolama katmanları).

Blobxfer aracı ile SAP HANA yedekleme dosyalarını doğrudan Azure Blob depolamaya kopyalamak kolaydır.

![Burada bir tam SAP HANA dosya yedeğinin dosyalarını görebilirsiniz](media/sap-hana-backup-file-level/image026.png)

Burada bir tam SAP HANA dosya yedeğinin dosyalarını görebilirsiniz. Dört dosya vardır ve en büyük bir tane kabaca 230 GB 'dir.

![230 GB 'yi bir Azure Standart depolama hesabı blob kapsayıcısına kopyalamak yaklaşık 3000 saniye sürdü](media/sap-hana-backup-file-level/image027.png)

Başlangıç testinde MD5 karma 'i kullanmayan, 230 GB 'yi bir Azure Standart depolama hesabı blob kapsayıcısına kopyalamak yaklaşık 3000 saniye sürdü.

![Bu ekran görüntüsünde, biri Azure portal nasıl göründüğünü görebilir](media/sap-hana-backup-file-level/image028.png)

Bu ekran görüntüsünde, biri Azure portal nasıl göründüğünü görebilir. SAP-HANA- &quot;yedeklemeleri&quot; adlı bir blob kapsayıcısı oluşturulmuştur ve bu SAP HANA yedekleme dosyalarını temsil eden dört blob 'u içerir. Bunlardan biri kabaca 230 GB boyutunda.

HANA Studio yedekleme konsolu, bir birinin HANA yedekleme dosyalarının en büyük dosya boyutunu kısıtlayasağlar. Örnek ortamda, büyük bir 230 GB dosya yerine birden çok daha küçük yedekleme dosyası olmasını mümkün kılarak performansı artırmış olur.

![HANA tarafında&#39;yedek dosya boyutu sınırının ayarlanması, yedekleme süresini iyileştirir.](media/sap-hana-backup-file-level/image029.png)

Dosyalar bu şekilde gösterildiği gibi sırayla yazıldığı için, HANA tarafında&#39;yedekleme dosyası boyut sınırının ayarlanması, yedekleme süresini geliştirir. Dosya boyutu sınırı 60 GB olarak ayarlanmıştır, bu nedenle yedekleme, 230 GB tek dosya yerine dört büyük veri dosyası oluşturdu. Birden çok yedekleme dosyası kullanmak, M64s, M64ms, M128s ve M128ms gibi daha büyük Azure VM 'lerinden yararlanan HANA veritabanlarını yedeklemeye yönelik bir zorunludur.

![Blobxfer aracının paralelliğini test etmek için, HANA yedeklemeleri için maksimum dosya boyutu 15 GB olarak ayarlanmıştır.](media/sap-hana-backup-file-level/image030.png)

Blobxfer aracının paralelliğini test etmek için, HANA yedeklemeleri için maksimum dosya boyutu 15 GB olarak ayarlanmıştır ve bu, 19 yedekleme dosyası ile sonuçlanır. Bu yapılandırma, blobxfer 'in 230 GB 'ı Azure Blob depolama alanına 3000 saniye ila 875 saniyeye kopyalaması için zaman getirdi.

Bu nedenle, Azure Blob yazma için 60 MB/sn sınırının sınırı vardır. Birden çok blob aracılığıyla paralellik sorunu çözer, ancak bu bir altkenar vardır: Bu HANA yedekleme dosyalarının tümünü Azure Blob Storage 'a kopyalamak için blobxfer aracının performansını artırma, hem HANA VM hem de ağ üzerinde yük koyar. HANA sistemi işlemi etkilenmiş hale gelir.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Yedekleme yazılım RAID 'de adanmış Azure veri disklerinin blob kopyası

El ile VM veri diski yedeklemesinden farklı olarak, bu yaklaşımda, HANA verileri, HANA günlük dosyaları ve yapılandırma dosyaları dahil olmak üzere tüm SAP yüklemesini kaydetmek için bir VM 'deki tüm veri disklerini yedeklemez. Bunun yerine, tam bir SAP HANA dosya yedeklemesi depolamak için birden çok Azure veri VHD 'de şeritle adanmış yazılım RAID 'in olması önerilir. Yalnızca, SAP HANA yedeğine sahip olan bu disklerin bir kopyası. Bunlar, kolayca ayrılmış bir hana yedekleme depolama hesabında tutulabilir veya daha fazla işlenmek üzere adanmış &quot;bir yedekleme Yönetim sanal&quot; makinesine eklenebilir.

![Dahil edilen tüm VHD 'ler * * start-azurestoraygeblobcopy * * PowerShell komutu kullanılarak kopyalandı](media/sap-hana-backup-file-level/image031.png)

Yerel yazılım RAID yedeklemesi tamamlandıktan sonra, dahil edilen tüm VHD 'ler **Start-azurestokgeblobcopy** PowerShell komutu kullanılarak kopyalandı (bkz. [Start-azurestokgeblobcopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Yalnızca yedekleme dosyalarını tutmak için ayrılmış dosya sistemini etkilediği için, diskte SAP HANA veri veya günlük dosyası tutarlılığı hakkında bir sorun yoktur. Bu komutun bir avantajı, sanal makine çevrimiçi kaldığında çalışmasının çalışmadır. Hiçbir işlemin yedek Stripe kümesine yazmadığından emin olmak için, blob kopyalaması öncesinde onu çıkarmanız ve daha sonra yeniden bağlamanız gerekir. Ya da bir tane, dosya sistemini dondurmak &quot;&quot; için uygun bir yol kullanabilir. Örneğin, XFS dosya sistemi\_için XFS donması üzerinden.

![Bu ekran görüntüsünde, Azure portal VHD kapsayıcısında Blobların listesi gösterilmektedir](media/sap-hana-backup-file-level/image032.png)

Bu ekran görüntüsünde, Azure Portal &quot;VHD&quot; kapsayıcısında Blobların listesi gösterilmektedir. Ekran görüntüsünde, SAP HANA yedekleme dosyalarının tutulması için yazılım RAID hizmeti olarak kullanılacak SAP HANA sunucusu VM 'sine eklenmiş beş VHD gösterilir. Ayrıca blob kopyalama komutu aracılığıyla alınan beş kopyayı da gösterir.

![Sınama amacıyla, SAP HANA yedekleme yazılım RAID disklerinin kopyaları App Server VM 'sine iliştirildi](media/sap-hana-backup-file-level/image033.png)

Sınama amacıyla, SAP HANA yedekleme yazılım RAID disklerinin kopyaları App Server VM 'sine iliştirildi.

![App Server VM 'si, disk kopyalarını eklemek için kapatıldı](media/sap-hana-backup-file-level/image034.png)

App Server VM 'si, disk kopyalarını eklemek için kapatıldı. VM 'yi başlattıktan sonra, diskler ve RAID doğru bulundu (UUID ile bağlanmış). Yalnızca bağlama noktası eksik ve bu, YaST partitioner aracılığıyla oluşturulmuş. Daha sonra SAP HANA yedekleme dosya kopyaları işletim sistemi düzeyinde görünür hale geldi.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>SAP HANA yedekleme dosyalarını NFS paylaşımıyla Kopyala

SAP HANA sisteminde performans veya disk alanı açısından olası etkileri azaltmak için, birisi SAP HANA yedekleme dosyalarını bir NFS paylaşımında depolamayı düşünebilirler. Teknik olarak işe yarar, ancak NFS paylaşımının ana bilgisayarı olarak ikinci bir Azure VM kullanılması anlamına gelir. VM ağı bant genişliği nedeniyle küçük bir VM boyutu olmamalıdır. Bu &quot;yedek sanal makineyi&quot; kapatıp yalnızca SAP HANA yedeklemeyi yürütmek için getirir. NFS paylaşımında yazma, ağ üzerinde yük koyar ve SAP HANA sistemi etkiler, ancak yedekleme dosyalarının bundan sonra yalnızca &quot;yedekleme sanal&quot; makinesinde yönetilmesi SAP HANA sistemi etkilemez.

![Başka bir Azure VM 'den bir NFS paylaşımının SAP HANA sunucusu VM 'sine takıldı](media/sap-hana-backup-file-level/image035.png)

NFS kullanım durumunu doğrulamak için, başka bir Azure VM 'den bir NFS paylaşımının SAP HANA sunucusu VM 'sine bağlandığından emin olun. Uygulanan özel NFS ayarlaması yoktu.

![Yedeklemenin doğrudan yapması 1 saat ve 46 dakika sürdü](media/sap-hana-backup-file-level/image036.png)

NFS paylaşma, SAP HANA sunucusundaki gibi bir hızlı Stripe kümesidir. Bununla birlikte, yerel bir Stripe kümesine yazarken 10 dakika yerine yedeklemenin doğrudan NFS paylaşımında yapması 1 saat ve 46 dakika sürdü.

![Alternatif, 1 saat ve 43 dakika içinde çok daha hızlı değildi](media/sap-hana-backup-file-level/image037.png)

Yerel bir Stripe kümesine yedekleme yapmanın ve işletim sistemi düzeyinde (basit bir **CP-AVR** komutu) NFS paylaşımıyla kopyalama işleminin alternatifi çok daha hızlı değildi. 1 saat ve 43 dakika sürdü.

Bu nedenle, ancak 230 GB yedekleme testi için performans iyi değildi. Çok terabayt için bile daha kötü görünebilir.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>SAP HANA yedekleme dosyalarını Azure dosyalarına kopyalama

Bir Azure Linux sanal makinesinde Azure dosya paylaşımının bağlanması mümkündür. [Azure dosya depolaması 'Nı Linux ile kullanma](../../../storage/files/storage-how-to-use-files-linux.md) makalesi, nasıl yapılacağı hakkında ayrıntılı bilgi sağlar. Şu anda bir Azure dosya paylaşımının 5 TB 'lik kota sınırının ve dosya başına 1 TB dosya boyutu sınırının bulunduğunu aklınızda bulundurun. Depolama sınırları hakkında bilgi için bkz. [Azure Storage ölçeklenebilirlik ve performans hedefleri](../../../storage/common/storage-scalability-targets.md) .

Ancak, SAP HANA yedekleme&#39;Şu anda bu tür CIFS bağlamalarla doğrudan çalışmamakta olduğunu göstermiştir. Ayrıca, bu CIFS 'nin önerilmediğini [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529) ' de de belirtilmiştir.

![Bu şekilde SAP HANA Studio 'daki yedekleme iletişim kutusunda bir hata gösterilir](media/sap-hana-backup-file-level/image038.png)

Bu şekilde, CIFS ile bağlı bir Azure dosya paylaşımının yedeklenmesini denerken SAP HANA Studio 'daki yedekleme iletişim kutusunda bir hata gösterilir. Bunun için, önce bir VM dosya sisteminde standart bir SAP HANA yedeklemesi yapması ve ardından yedekleme dosyalarını oradan Azure dosya hizmeti 'ne kopyalamanız gerekir.

![Bu şekilde, 19 SAP HANA yedekleme dosyalarını kopyalamanın yaklaşık 929 saniye sürdüğünü gösterir](media/sap-hana-backup-file-level/image039.png)

Bu şekilde, toplam kabaca 230 GB boyutlu SAP HANA yedekleme dosyalarını Azure dosya paylaşımında kopyalamak yaklaşık 929 saniye sürdüğünü gösterir.

![SAP HANA VM 'deki kaynak dizin yapısı Azure dosya paylaşımında kopyalanmış](media/sap-hana-backup-file-level/image040.png)

Bu ekran görüntüsünde, biri SAP HANA VM 'deki kaynak dizin yapısının Azure dosya paylaşımında (Hana\_yedekleme\_FSL\_15gb) ve 19 bireysel yedekleme dosyasında kopyalandığından emin olabilir.

Azure dosyalarında SAP HANA yedekleme dosyalarını depolamak, SAP HANA dosya yedeklemeleri doğrudan destekledikleri zaman ileride ilginç bir seçenek olabilir. Ya da Azure dosyalarını NFS aracılığıyla bağlamak mümkün olduğunda en yüksek kota sınırı 5 TB 'den büyük ölçüde yüksektir.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure sanal makinelerinde SAP HANA Için Yedekleme Kılavuzu](sap-hana-backup-guide.md) , Başlarken hakkında genel bakış ve bilgiler sağlar.
* [Depolama anlık görüntülerine göre SAP HANA yedekleme](sap-hana-backup-storage-snapshots.md) , depolama anlık görüntü tabanlı yedekleme seçeneğini açıklar.
* Azure 'da SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan (büyük örnekler) oluşturma hakkında bilgi edinmek için bkz. [Azure 'da SAP HANA (büyük örnekler) yüksek kullanılabilirlik ve olağanüstü durum kurtarma](hana-overview-high-availability-disaster-recovery.md).
