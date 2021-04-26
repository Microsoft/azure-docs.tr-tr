---
title: Azure dosyaları için sık sorulan sorular (SSS) | Microsoft Docs
description: Azure dosyaları hakkında sık sorulan soruların yanıtlarını alın. Azure dosya paylaşımlarını bulut veya şirket içi Windows, Linux veya macOS dağıtımlarında eşzamanlı olarak bağlayabilirsiniz.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 4d7123aa22d95e3e4c3850be775ddad96f28d280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785316"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Azure Dosyalar hakkında sık sorulan sorular (SSS)
[Azure dosyaları](storage-files-introduction.md) , bulutta endüstri standardı [sunucu ILETI bloğu (SMB) protokolü](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) ve [ağ dosya sistemi (NFS) protokolü](https://en.wikipedia.org/wiki/Network_File_System) (Önizleme) aracılığıyla erişilebilen tam olarak yönetilen dosya paylaşımları sunar. Azure dosya paylaşımlarını bulutta veya Windows, Linux ve macOS 'ın şirket içi dağıtımlarında eşzamanlı olarak bağlayabilirsiniz. Ayrıca, verilerin kullanıldığı yere hızlı erişim için Azure Dosya Eşitleme kullanarak Windows Server makinelerinde Azure dosya paylaşımlarını önbelleğe alabilirsiniz.

Bu makalede, Azure dosyaları ile Azure Dosya Eşitleme kullanımı dahil olmak üzere Azure dosyaları özellikleri ve işlevleri hakkında sık sorulan sorular yanıtlanmaktadır. Sorunuzun yanıtını görmüyorsanız, aşağıdaki kanallarla (yürüyen sırada) bizimle iletişim kurmanız gerekir:

1. Bu makalenin açıklamalar bölümü.
2. [Microsoft Q&Azure depolama Için bir soru sayfası](/answers/topics/azure-file-storage.html).
3. [Azure dosyaları UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft Desteği. Yeni bir destek isteği oluşturmak için, Azure portal **Yardım** sekmesinde **Yardım + Destek** düğmesini seçin ve ardından **Yeni destek isteği**' ni seçin.

## <a name="general"></a>Genel
* <a id="why-files-useful"></a>
  **Azure dosyaları nasıl yararlı olur?**  
   Fiziksel sunucu, cihaz veya gereç yükünü yönetmeden sorumlu olmadan bulutta dosya paylaşımları oluşturmak için Azure dosyalarını kullanabilirsiniz. İşletim sistemi güncelleştirmelerini uygulama ve hatalı disklerin değiştirilmesi dahil olmak üzere, sizin için tek bir iş işi yaptık. Azure dosyalarının konusunda size yardımcı olabilecek senaryolar hakkında daha fazla bilgi edinmek için [Azure dosyalarının neden yararlı olduğuna](storage-files-introduction.md#why-azure-files-is-useful)bakın.

* <a id="file-access-options"></a>
  **Azure dosyalarındaki dosyalara erişmenin farklı yolları nelerdir?**  
    SMB dosya paylaşımları, SMB 3,0 protokolü kullanılarak yerel makinenize bağlanabilir veya dosya paylaşımınızda dosyalara erişmek için [Depolama Gezgini](https://storageexplorer.com/) gibi araçları kullanabilirsiniz. NFS dosya paylaşımları, Azure portal tarafından sunulan betiği kopyalayabilir/yapıştırarak yerel makinenize bağlanabilir. Uygulamanızdan Azure dosya paylaşımında dosyalarınıza erişmek için depolama istemci kitaplıklarını, REST API 'Leri, PowerShell veya Azure CLı kullanabilirsiniz.

* <a id="what-is-afs"></a>
  **Azure Dosya Eşitleme nedir?**  
    Kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirirken, şirket içi bir dosya sunucusunun esnekliğini, performansını ve uyumluluğunu koruyarak Azure Dosya Eşitleme kullanabilirsiniz. Azure Dosya Eşitleme, Windows Server makinelerinizi Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürür. SMB, ağ dosya sistemi (NFS) ve Dosya Aktarım Protokolü Hizmeti (FTPS) dahil olmak üzere verilerinize yerel olarak erişmek için Windows Server 'da bulunan herhangi bir protokolü kullanabilirsiniz. Dünyanın dört bir yanında ihtiyacınız olan sayıda önbellekler olabilir.

* <a id="files-versus-blobs"></a>
  **My Data için Azure Blob Storage ile Azure dosya paylaşımından neden kullanmalıyım?**  
    Azure dosyaları ve Azure Blob depolama, büyük miktarlarda verileri bulutta depolamanın yollarını sunmaktadır, ancak bazıları biraz farklı amaçlar için yararlıdır. 
    
    Azure Blob depolama, yapılandırılmamış verileri depolaması gereken büyük ölçekli, bulutta yerel uygulamalar için yararlıdır. Performansı ve ölçeği en üst düzeye çıkarmak için, Azure Blob depolama, doğru bir dosya sisteminden daha basit bir depolama soyutlamasıdır. Azure Blob depolamaya yalnızca REST tabanlı istemci kitaplıkları aracılığıyla erişebilirsiniz (veya doğrudan REST tabanlı protokolde).

    Azure dosyaları özellikle bir dosya sistemidir. Azure dosyaları, şirket içi işletim sistemleriyle çalışma yıllarından bildiğiniz ve sevdiğiniz tüm dosya soyutlarına sahiptir. Azure Blob depolama gibi Azure dosyaları da bir REST arabirimi ve REST tabanlı istemci kitaplıkları sunar. Azure Blob depolama alanının aksine Azure dosyaları, Azure dosya paylaşımlarına SMB veya NFS erişimi sağlar. Dosya paylaşımları, herhangi bir kod yazmadan veya dosya sistemine özel bir sürücü eklemeden şirket içinde veya bulut VM 'lerinde doğrudan Windows, Linux veya macOS 'a bağlanabilir. Ayrıca, hızlı erişim için Azure Dosya Eşitleme kullanarak, verilerin kullanıldığı yere yakın şekilde Azure SMB dosya paylaşımlarını da önbelleğe alabilirsiniz. 
   
    Azure dosyaları ile Azure Blob depolama arasındaki farklılıklarla ilgili daha ayrıntılı bir açıklama için bkz. [temel Azure depolama hizmetlerine giriş](../common/storage-introduction.md). Azure Blob depolama hakkında daha fazla bilgi için bkz. [BLOB depolamaya giriş](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Azure diskleri yerine neden Azure dosya paylaşımından kullanmalıyım?**  
    Azure disklerindeki bir disk yalnızca bir disktir. Azure disklerinden değer almak için, Azure 'da çalışan bir sanal makineye bir disk bağlamanız gerekir. Azure diskleri, bir şirket içi sunucuda için disk kullanacağınızı her şey için kullanılabilir. Bunu bir işletim sistemi sistem diski olarak, bir işletim sistemi için takas alanı veya bir uygulama için ayrılmış depolama olarak kullanabilirsiniz. Azure diskleri için ilginç bir kullanım, bulutta bir Azure dosya paylaşımının kullanıldığı yerlerde kullanılacak bir dosya sunucusu oluşturmaktır. Azure sanal makinelerinde bir dosya sunucusu dağıtmak, Azure dosyaları tarafından desteklenmeyen dağıtım seçenekleri istediğinizde Azure 'da dosya depolamayı almanın yüksek performanslı bir yoludur. 

    Ancak, arka uç depolama olarak Azure diskleriyle bir dosya sunucusu çalıştırmak, birkaç nedenden dolayı bir Azure dosya paylaşımının kullanılmasıyla çok daha pahalıdır. İlk olarak, disk depolama alanı ödemesinin yanı sıra bir veya daha fazla Azure sanal makinesi çalıştırmanın masrafına de ödeme yapmanız gerekir. İkincisi de dosya sunucusunu çalıştırmak için kullanılan VM 'Leri yönetmeniz gerekir. Örneğin, işletim sistemi yükseltmelerinden siz sorumlusunuz. Son olarak, verilerin şirket içinde önbelleğe alınması gerekiyorsa, bunu yapmak için Dağıtılmış Dosya Sistemi Çoğaltma (DFSR) gibi çoğaltma teknolojilerini ayarlamak ve yönetmek en iyisidir.

    Hem Azure hem de Azure sanal makinelerinde barındırılan bir dosya sunucusunun en iyi şekilde alınması için bir yaklaşım (Azure disklerini arka uç depolama olarak kullanmanın yanı sıra), bulut VM 'de barındırılan bir dosya sunucusuna Azure Dosya Eşitleme yüklemektir. Azure dosya paylaşımının dosya sunucunuz ile aynı bölgedeyse, bulut katmanlamayı etkinleştirebilir ve boş alan yüzdesini maksimum (%99%) olarak ayarlayabilirsiniz. Bu, verilerin çok az çoğaltılmasını sağlar. Ayrıca, NFS protokol desteği gerektiren uygulamalar gibi dosya sunucularınız ile istediğiniz uygulamaları kullanabilirsiniz.

    Azure 'da yüksek performanslı ve yüksek oranda kullanılabilir bir dosya sunucusu ayarlama seçeneği hakkında bilgi için, bkz. [Microsoft Azure IaaS VM Konuk kümelerini dağıtma](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Azure dosyaları ile Azure diskleri arasındaki farklılıkların daha ayrıntılı bir açıklaması için bkz. [temel Azure depolama hizmetlerine giriş](../common/storage-introduction.md). Azure diskleri hakkında daha fazla bilgi edinmek için bkz. [Azure yönetilen disklere genel bakış](../../virtual-machines/managed-disks-overview.md).

* <a id="get-started"></a>
  **Azure dosyalarını kullanmaya başlamak Nasıl yaparım? misiniz?**  
   Azure dosyalarını kullanmaya başlamak kolaydır. İlk olarak, bir [SMB dosya paylaşımından](storage-how-to-create-file-share.md) ya da bir [NFS paylaşımının nasıl](storage-files-how-to-create-nfs-shares.md)oluşturulduğunu oluşturun ve ardından bunu tercih ettiğiniz işletim sistemine bağlayın: 

  * [Windows 'da bir SMB paylaşma bağlama](storage-how-to-use-files-windows.md)
  * [Linux 'ta bir SMB paylaşma bağlama](storage-how-to-use-files-linux.md)
  * [MacOS 'ta bir SMB paylaşma bağlama](storage-how-to-use-files-mac.md)
  * [NFS dosya paylaşımının bağlama](storage-files-how-to-mount-nfs-shares.md)

    Kuruluşunuzda üretim dosya paylaşımlarının yerini alacak bir Azure dosya paylaşımının dağıtılması hakkında daha ayrıntılı bir kılavuz için bkz. [Azure dosyaları dağıtımı Için planlama](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Azure dosyaları tarafından desteklenen depolama yedeklilik seçenekleri nelerdir?**  
    Şu anda Azure dosyaları yerel olarak yedekli depolama (LRS), bölgesel olarak yedekli depolama (ZRS), coğrafi olarak yedekli depolama (GRS) ve coğrafi bölge yedekli depolama (GZRS) destekler. Azure Files Premium katmanı Şu anda yalnızca LRS ve ZRS 'yi destekliyor.

* <a id="tier-options"></a>
  **Azure dosyalarında hangi depolama katmanları desteklenir?**  
    Azure dosyaları iki depolama katmanını destekler: Premium ve standart. Standart dosya paylaşımları genel amaçlı olarak oluşturulur (GPv1 veya GPv2) depolama hesapları ve Premium dosya paylaşımları, FileStorage depolama hesaplarında oluşturulur. [Standart dosya paylaşımları](storage-how-to-create-file-share.md) ve [Premium dosya paylaşımları](./storage-how-to-create-file-share.md)oluşturma hakkında daha fazla bilgi edinin. 
    
    > [!NOTE]
    > BLOB depolama hesaplarından veya *Premium* genel amaçlı (GPv1 veya GPv2) depolama hesaplarından Azure dosya paylaşımları oluşturamazsınız. Standart Azure dosya paylaşımlarının yalnızca *Standart* genel amaçlı hesaplarda oluşturulması gerekir ve Premium Azure dosya paylaşımları yalnızca dosya depolama depolama hesaplarında oluşturulmalıdır. *Premium* genel amaçlı (GPv1 ve GPv2) depolama hesapları yalnızca Premium sayfa Blobları içindir. 

* <a id="file-locking"></a>
  **Azure dosyaları dosya kilitlemeyi destekliyor mu?**  
    Evet, Azure dosyaları SMB/Windows stili dosya kilitlemeyi tam olarak destekler, [bkz. Ayrıntılar](/rest/api/storageservices/managing-file-locks).

* <a id="give-us-feedback"></a>
  **Gerçekten Azure dosyalarına eklenen belirli bir özelliği görmek istiyorum. Eklenebilir mi?**  
    Azure dosyaları ekibi, hizmetimiz ile ilgili tüm geri bildirimleri ve tüm geri bildirimlerinizi duymakla ilgileniyor. Lütfen [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)'daki özellik isteklerini oylayın! Birçok yeni özellik ile size çok fazla göz ımızı umuyoruz.

## <a name="azure-file-sync"></a>Azure Dosya Eşitleme

* <a id="afs-region-availability"></a>
  **Azure Dosya Eşitleme için hangi bölgeler desteklenir?**  
    Kullanılabilir bölgelerin listesi, Azure Dosya Eşitleme planlama kılavuzunun [bölge kullanılabilirliği](../file-sync/file-sync-planning.md#azure-file-sync-region-availability) bölümünde bulunabilir. Genel olmayan bölgeler dahil olmak üzere ek bölgeler için sürekli olarak destek ekleyeceğiz.

* <a id="cross-domain-sync"></a>
  **Aynı eşitleme grubunda etki alanına katılmış ve etki alanına katılmamış sunuculara sahip olabilir miyim?**  
    Evet. Bir eşitleme grubu, etki alanına katılmış olmasalar bile farklı Active Directory üyeliklerine sahip sunucu uç noktalarını içerebilir. Bu yapılandırma Teknik olarak çalışsa da, bir sunucudaki dosyalar ve klasörler için tanımlanan erişim denetim listeleri (ACL 'Ler), eşitleme grubundaki diğer sunucular tarafından zorlanamayacak olabileceğinden, bunu tipik bir yapılandırma olarak önermiyoruz. En iyi sonuçlar için, aynı Active Directory ormanında olan sunucular arasında, farklı Active Directory ormanlarda bulunan ancak güven ilişkileri oluşturan sunucular arasında veya bir etki alanında olmayan sunucular arasında eşitleme yapmanızı öneririz. Bu yapılandırmaların karışımını kullanmaktan kaçınmanızı öneririz.

* <a id="afs-change-detection"></a>
  **SMB veya portalda doğrudan Azure dosya paylaşımınızdaki bir dosya oluşturdum. Dosyanın eşitleme grubundaki sunucularla eşitlenmesi ne kadar sürer?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Azure Dosya Eşitleme 1 TİB veri yüklemek için ne kadar sürer?**
  
    Performans, ortam ayarlarınıza, yapılandırmanıza ve bu bir ilk eşitleme veya devam eden bir eşitleme olup olmadığına göre değişir. Daha fazla bilgi için bkz. [Azure dosya eşitleme performans ölçümleri](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**Aynı dosya yaklaşık olarak aynı anda iki sunucuda değiştirilirse ne olur?**  
    Azure Dosya Eşitleme basit bir çakışma çözümü stratejisi kullanır: aynı anda iki uç noktada değiştirilen dosyalarda her iki değişikliği de tutuyoruz. En son yazılan değişiklik özgün dosya adını tutar. Eski dosya (LastWriteTime tarafından belirlenir), dosya adının sonuna bir uç nokta adı ve çakışma numarası içerir. Sunucu uç noktaları için uç nokta adı sunucunun adıdır. Bulut uç noktaları için uç nokta adı **bulut** olur. Ad bu taksonomiyi izler: 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    Örneğin, CompanyReport.docx ilk çakışması, merkezileştirme sunucusu eski yazmanın gerçekleştiği yerdir CompanyReport-CentralServer.docx hale gelir. İkinci çakışma CompanyReport-CentralServer-1.docx olarak adlandırılır. Azure Dosya Eşitleme dosya başına 100 çakışma dosyasını destekler. Çakışma dosyası sayısı üst sınırına ulaşıldığında, çakışma dosyası sayısı 100 ' den az olana kadar dosya eşitleme başarısız olur.

* <a id="afs-storage-redundancy"></a>
  **Azure Dosya Eşitleme için coğrafi olarak yedekli depolama destekleniyor mu?**  
    Evet, Azure dosyaları yerel olarak yedekli depolama (LRS) ve coğrafi olarak yedekli depolamayı (GRS) destekler. GRS için yapılandırılmış bir hesaptan eşleştirilmiş bölgeler arasında bir depolama hesabı yük devretmesi başlatırsanız, Microsoft yeni bölgeyi yalnızca verilerin bir yedeklemesi olarak değerlendirmenizi önerir. Azure Dosya Eşitleme yeni birincil bölgeyle eşitlemeye otomatik olarak başlamaz. 

* <a id="sizeondisk-versus-size"></a>
  **Azure Dosya Eşitleme, bir dosyanın *disk özelliği üzerindeki boyutu* neden bir dosya için *Boyut* özelliği ile eşleşmiyor?**  
  Bkz. [bulut katmanlaması Azure dosya eşitleme anlama](../file-sync/file-sync-cloud-tiering-overview.md#tiered-vs-locally-cached-file-behavior).

* <a id="is-my-file-tiered"></a>
  **Bir dosyanın katmanlı olup olmadığını nasıl anlayabilirim?**  
  Bkz. [bulut katmanlamayı anlama](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-check-if-your-files-are-being-tiered).

* <a id="afs-recall-file"></a>**Kullanmak istediğim bir dosya katmanlı. Dosyayı yerel olarak kullanmak için diske nasıl geri çekirim?**  
  Bkz. [bulut katmanlamayı anlama](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk).

* <a id="afs-force-tiering"></a>
  **Nasıl yaparım? bir dosyanın veya dizinin katmanlanmasını zorlıyor musunuz?**  
  Bkz. [bulut katmanlamayı anlama](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-force-a-file-or-directory-to-be-tiered).

* <a id="afs-effective-vfs"></a>
  **Bir birimde birden çok sunucu uç noktası olduğunda *birim boş alanı* nasıl yorumlanır?**  
  Bkz. [bulut katmanlamayı anlama](../file-sync/file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume).
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **Bulut katmanlama devre dışı, neden sunucu uç noktası konumunda katmanlı dosyalar var?**  
    Katmanlı dosyaların sunucu uç noktası konumunda bulunabilmesinin iki nedeni vardır:

    - Varolan bir eşitleme grubuna yeni bir sunucu uç noktası eklerken, ilk indirme modu için ad alanını geri çağır seçeneğini veya yalnızca ad alanını hatırla seçeneğini belirlerseniz, dosyalar yerel olarak indirilene kadar katmanlı olarak görünür. Bunu önlemek için, ilk indirme modu için katmanlı dosyaları önleyin seçeneğini belirleyin. Dosyaları el ile geri çekmek için [Invoke-StorageSyncFileRecall](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk) cmdlet 'ini kullanın.

    - Bulut katmanlaması sunucu uç noktasında etkinleştirildiyse ve devre dışı bırakılmışsa, dosyalar erişilene kadar katmanlı olarak kalır.

* <a id="afs-tiered-files-not-showing-thumbnails"></a>
  **Katmanlı dosyalar neden Windows Gezgini 'nde küçük resimleri veya önizlemeleri gösterilmiyor?**  
    Katmanlı dosyalar için, küçük resimler ve önizlemeler sunucu uç noktanıza görünmez. Bu davranış, Windows 'daki küçük resim önbelleği özelliği, çevrimdışı özniteliği olan dosyaların okunmasına göre atlanmasından bu yana beklenmektedir. Bulut katmanlama özelliği etkinken katmanlı dosyalar aracılığıyla okuma, bunların indirilememesine (geri çekilir) neden olur.

    Bu davranış Azure Dosya Eşitleme özgü değildir, Windows Gezgini, çevrimdışı özniteliği ayarlanmış herhangi bir dosya için "gri X" görüntüler. SMB üzerinden dosyalara erişirken X simgesini görürsünüz. Bu davranışın ayrıntılı bir açıklaması için bkz. [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

    Katmanlı dosyaları yönetme hakkında sorularınız için lütfen bkz. [katmanlı dosyaları yönetme](../file-sync/file-sync-how-to-manage-tiered-files.md).

* <a id="afs-files-excluded"></a>
  **Hangi dosya veya klasörler Azure Dosya Eşitleme tarafından otomatik olarak dışlanır?**  
  Bkz. [Atlanan dosyalar](../file-sync/file-sync-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Windows Server 2008 R2, Linux veya ağ bağlantılı depolama (NAS) cihazından Azure Dosya Eşitleme kullanabilir miyim?**  
    Şu anda Azure Dosya Eşitleme yalnızca Windows Server 2019, Windows Server 2016 ve Windows Server 2012 R2 'yi destekler. Şu anda paylaşabiliriz başka bir planımız yok, ancak müşteri talebine göre ek platformları desteklemeye çalışıyoruz. [Azure dosyaları UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 'ta hangi platformları destekleyeceğinizi bize bildirmek istiyorum.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Katmanlı dosyalar neden sunucu uç noktası ad alanı dışında var?**  
    Azure Dosya Eşitleme Agent sürüm 3 ' ten önce, Azure Dosya Eşitleme katmanlı dosyaların sunucu uç noktası dışında, ancak sunucu uç noktasıyla aynı birimde taşınmasını engelledi. Kopyalama işlemleri, katmanlı olmayan dosyaların taşınması ve katmanlı diğer birimlere taşınması etkilenmemiştir. Bu davranışın nedeni, dosya Gezgini 'nin ve diğer Windows API 'Lerinin aynı birim üzerinde taşıma işlemlerinin (neredeyse) anlık yeniden adlandırma işlemlerinde olduğu örtük bir varsayımına sahiptir. Bu, taşınmaların dosya Gezgini veya diğer taşıma yöntemlerinin (komut satırı veya PowerShell gibi), verileri buluttan geri çekAzure Dosya Eşitleme irken yanıt vermemesine neden olacağı anlamına gelir. [Azure dosya eşitleme aracı sürümü 3.0.12.0](../file-sync/file-sync-release-notes.md#supported-versions)ile başlayarak, Azure dosya eşitleme, katmanlı bir dosyayı sunucu uç noktası dışına taşımanızı sağlayacak. Katmanlı dosyanın sunucu uç noktası dışında katmanlı bir dosya olarak var olmasına ve sonra dosyayı arka planda geri çekmesine izin vererek, daha önce bahsedilen negatif etkilerden kaçının. Bu, aynı birimdeki taşımanın anında olduğu ve taşıma tamamlandıktan sonra dosyayı diske geri çekmek için tüm işleri yaptığımız anlamına gelir. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Sunucum üzerinde Azure Dosya Eşitleme bir sorun yaşıyorum (eşitleme, bulut katmanlama vb.). Sunucu uç noktasını kaldırıp yeniden oluşturmanız gerekir mi?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Depolama eşitleme hizmetini ve/veya depolama hesabını farklı bir kaynak grubuna, aboneliğe veya Azure AD kiracısına taşıyabilir miyim?**  
   Evet, depolama eşitleme hizmeti ve/veya depolama hesabı farklı bir kaynak grubuna, aboneliğe veya Azure AD kiracısına taşınabilir. Depolama eşitleme hizmeti veya depolama hesabı taşındıktan sonra, Microsoft. Storagessync uygulamasına depolama hesabına erişim sağlamanız gerekir (bkz. [Azure dosya eşitleme depolama hesabına erişimi olduğundan emin olun](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Bulut uç noktası oluşturulurken, depolama eşitleme hizmeti ve depolama hesabı aynı Azure AD kiracısında olmalıdır. Bulut uç noktası oluşturulduktan sonra, depolama eşitleme hizmeti ve depolama hesabı farklı Azure AD kiracılarına taşınabilir.
    
* <a id="afs-ntfs-acls"></a>
  **Dizin/dosya düzeyinde NTFS ACL 'Lerini Azure dosyalarında depolanan verilerle birlikte Azure Dosya Eşitleme korur.**

    24 Şubat, 2020 itibariyle Azure dosya eşitleme tarafından katmanlı yeni ve mevcut ACL 'Ler NTFS biçiminde kalıcı hale getirilir ve doğrudan Azure dosya paylaşımında yapılan ACL değişiklikleri, eşitleme grubundaki tüm sunucularla eşitlenir. Azure dosyalarına yapılan ACL 'lerdeki tüm değişiklikler, Azure dosya eşitleme aracılığıyla eşitlenir. Verileri Azure dosyalarına kopyalarken, SMB veya REST aracılığıyla öznitelikleri, zaman damgalarını ve ACL 'Leri bir Azure dosya paylaşımında kopyalamak için gerekli "uygunluğu" nı destekleyen bir kopyalama aracı kullandığınızdan emin olun. AzCopy gibi Azure kopyalama araçlarını kullanırken, en son sürümü kullanmak önemlidir. Bir dosyanın tüm önemli meta verilerini kopyalayabilmeniz için Azure kopyalama araçlarına genel bir bakış almak için [dosya kopyalama araçları tablosuna](storage-files-migration-overview.md#file-copy-tools) bakın.

    Dosya eşitleme yönetilen dosya paylaşımlarınız üzerinde Azure Backup etkinleştirdiyseniz, dosya ACL 'Leri yedekleme geri yükleme iş akışının bir parçası olarak geri yüklemeye devam edebilir. Bu, tüm paylaşımın veya tek tek dosyalar/dizinler için geçerlidir.

    Dosya eşitleme tarafından yönetilen dosya paylaşımları için otomatik olarak yönetilen yedekleme çözümünün bir parçası olarak anlık görüntüler kullanıyorsanız, anlık görüntülerin 24 Şubat 2020 ' den önce alınması durumunda, ACL 'niz NTFS ACL 'Lerine düzgün şekilde geri yüklenemez. Bu durumda, Azure desteği 'ne başvurmayı göz önünde bulundurun.

* <a id="afs-lastwritetime"></a>
  **Dizinler için LastWriteTime Azure Dosya Eşitleme eşitler mi?**  
    Hayır, Azure Dosya Eşitleme dizinlerin LastWriteTime değerini eşitlemez. Bu tasarım gereğidir.
    
## <a name="security-authentication-and-access-control"></a>Güvenlik, kimlik doğrulama ve erişim denetimi
* <a id="ad-support"></a>
**Kimlik tabanlı kimlik doğrulaması ve erişim denetimi Azure dosyaları tarafından destekleniyor mu?**  
    
    Evet, Azure dosyaları kimlik tabanlı kimlik doğrulaması ve erişim denetimini destekler. Kimlik tabanlı erişim denetimi kullanmanın iki yolunu seçebilirsiniz: şirket içi Active Directory Domain Services veya Azure Active Directory Domain Services (Azure AD DS). Şirket içi Active Directory Domain Services (AD DS), SMB üzerinden Azure dosya paylaşımlarına erişmek için şirket içinde veya Azure 'da AD DS etki alanına katılmış makineler kullanarak kimlik doğrulamasını destekler. Azure dosyaları için SMB üzerinden Azure AD DS kimlik doğrulaması, Azure AD DS etki alanına katılmış Windows VM 'lerinin Azure AD kimlik bilgilerini kullanarak paylaşımlara, dizinlere ve dosyalara erişmesine olanak sağlar. Daha fazla ayrıntı için bkz. [Azure dosyalarına genel bakış SMB erişimi için kimlik tabanlı kimlik doğrulama desteği](storage-files-active-directory-overview.md). 

    Azure dosyaları, erişim denetimini yönetmek için iki ek yol sunar:

    - Belirli izinlere sahip olan ve belirli bir zaman aralığı için geçerli olan belirteçler oluşturmak için paylaşılan erişim imzaları (SAS) kullanabilirsiniz. Örneğin, 10 dakikalık süre sonu olan belirli bir dosyaya salt okuma erişimi olan bir belirteç oluşturabilirsiniz. Belirteç geçerli olduğunda belirtece sahip olan herkes bu 10 dakika boyunca bu dosyaya salt okuma erişimi sağlar. Paylaşılan erişim imza anahtarları yalnızca REST API veya istemci kitaplıkları aracılığıyla desteklenir. Depolama hesabı anahtarlarını kullanarak Azure dosya paylaşımından SMB üzerinden bağlamanız gerekir.

    - Azure Dosya Eşitleme tüm isteğe bağlı ACL 'Leri veya DACL 'Leri (Active Directory tabanlı veya yerel), eşitlediği tüm sunucu uç noktalarına korur ve çoğaltır. 
    
    Azure Storage hizmetlerinde desteklenen tüm protokollerin kapsamlı bir gösterimi için [Azure depolama 'ya erişimi yetkilendirme](../common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) konusuna başvurabilirsiniz. 
    
* <a id="encryption-at-rest"></a>
**Azure dosya paylaşımımın bekleyen bir şekilde şifrelenmesini nasıl sağlayabilirim?**  

    Evet. Daha fazla bilgi için bkz. [Azure depolama hizmeti şifrelemesi](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Bir Web tarayıcısı kullanarak belirli bir dosyaya nasıl erişim sağlayabilirim?**  

    Belirli izinlere sahip olan ve belirli bir zaman aralığı için geçerli olan belirteçler oluşturmak için paylaşılan erişim imzaları ' nı kullanabilirsiniz. Örneğin, belirli bir dosyaya salt okuma erişimi veren bir belirteç oluşturabilirsiniz. Belirteç geçerli olsa da, URL 'ye sahip olan herkes dosyaya doğrudan herhangi bir Web tarayıcısından erişebilir. Depolama Gezgini gibi bir kullanıcı arabiriminden kolayca paylaşılan erişim imza anahtarı oluşturabilirsiniz.

* <a id="file-level-permissions"></a>
**Paylaşımdaki klasörlerde salt okunurdur veya salt yazılır izinleri belirtmek mümkün mü?**  

    Dosya paylaşımından SMB kullanarak bağlarsanız, izinler üzerinde klasör düzeyinde denetiminiz yoktur. Ancak, REST API veya istemci kitaplıklarını kullanarak bir paylaşılan erişim imzası oluşturursanız, paylaşım içindeki klasörler üzerinde salt okunurdur veya salt yazılır izinler belirleyebilirsiniz.

* <a id="ip-restrictions"></a>
**Bir Azure dosya paylaşımının IP kısıtlamalarını uygulayabilir miyim?**  

    Evet. Azure dosya paylaşımınıza erişim, depolama hesabı düzeyinde kısıtlanabilir. Daha fazla bilgi için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Azure dosyaları hangi veri uyumluluk ilkelerini destekler?**  

   Azure dosyaları, Azure depolama 'daki diğer depolama hizmetlerinde kullanılan depolama mimarisinin üstünde çalışır. Azure dosyaları, diğer Azure Storage hizmetlerinde kullanılan aynı veri uyumluluk ilkelerini uygular. Azure depolama veri uyumluluğu hakkında daha fazla bilgi için [Azure depolama uyumluluk teklifleri](../common/storage-compliance-offerings.md)' ne başvurabilirsiniz ve [Microsoft Güven Merkezi](https://microsoft.com/trustcenter/default.aspx)' ne gidebilirsiniz.

* <a id="file-auditing"></a>
**Azure dosyalarındaki dosya erişimini ve değişiklikleri nasıl denetleyeceğinizi?**

  Azure dosyaları için denetim işlevlerini sağlayan iki seçenek vardır:
  - Kullanıcılar Azure dosya paylaşımıyla doğrudan erişiyorsa, dosya değişikliklerini ve Kullanıcı erişimini izlemek için [Azure depolama günlükleri (Önizleme)](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs) kullanılabilir. Bu Günlükler, sorun giderme amacıyla kullanılabilir ve istekler en iyi çaba temelinde günlüğe kaydedilir.
  - Kullanıcılar, Azure Dosya Eşitleme aracısının yüklü olduğu bir Windows Server aracılığıyla Azure dosya paylaşımıyla erişiyorsa, Windows Server 'da dosya değişikliklerini ve Kullanıcı erişimini izlemek için bir [Denetim ilkesi](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) veya üçüncü taraf ürün kullanın. 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS & Azure AD DS kimlik doğrulaması
* <a id="ad-support-devices"></a>
**Azure dosyaları Azure Active Directory Domain Services (Azure AD DS) kimlik doğrulaması, Azure AD kimlik bilgilerini kullanarak Azure AD kimlik bilgilerini kullanarak SMB erişimini destekler.**

    Hayır, bu senaryo desteklenmiyor.

* <a id="ad-vm-subscription"></a>
**Azure AD kimlik bilgileriyle Azure dosya paylaşımlarına, farklı bir abonelik kapsamındaki bir VM 'den erişebilir miyim?**

    Dosya paylaşımının dağıtıldığı abonelik, sanal makinenin etki alanına katılmış olduğu Azure AD DS dağıtımıyla aynı Azure AD kiracısıyla ilişkiliyse, Azure dosya paylaşımlarına aynı Azure AD kimlik bilgilerini kullanarak erişebilirsiniz. Kısıtlama, abonelikte değil, ilişkili Azure AD kiracısında yer alır.
    
* <a id="ad-support-subscription"></a>
**Azure dosya paylaşımının birincil kiracısından farklı bir Azure AD kiracısı kullanarak Azure AD DS ya da Azure dosya paylaşımları için şirket içi AD DS kimlik doğrulamasını etkinleştirebilir miyim?**

    Hayır, Azure dosyaları yalnızca Azure AD DS veya şirket içi AD DS, dosya paylaşımıyla aynı abonelikte bulunan bir Azure AD kiracısı ile tümleştirmeyi destekler. Bir Azure AD kiracısıyla yalnızca bir abonelik ilişkilendirilebilir. Bu sınırlama hem Azure AD DS hem de şirket içi AD DS kimlik doğrulama yöntemlerine yöneliktir. Kimlik doğrulaması için şirket içi AD DS kullanılırken, [AD DS kimlik bilgisinin depolama hesabının Ilişkilendirildiği Azure AD ile eşitlenmesi gerekir](../../active-directory/hybrid/how-to-connect-install-roadmap.md) .

* <a id="ad-linux-vms"></a>
**Azure AD DS veya şirket içi AD DS Azure dosya paylaşımları için kimlik doğrulaması, Linux VM 'lerini destekliyor mu?**

    Hayır, Linux VM 'lerden kimlik doğrulaması desteklenmez.

* <a id="ad-aad-smb-afs"></a>
**Azure AD DS ya da şirket içi AD DS kimlik doğrulamasını Azure Dosya Eşitleme tarafından yönetilen dosya paylaşımları mi kullanıyorsunuz?**

    Evet, Azure Dosya Eşitleme tarafından yönetilen bir dosya paylaşımında Azure AD DS veya şirket içi AD DS kimlik doğrulamasını etkinleştirebilirsiniz. Yerel dosya sunucularındaki dizin/dosya NTFS ACL 'lerinde yapılan değişiklikler Azure dosyaları ile katmanlanacaktır ve tam tersi de geçerlidir.

* <a id="ad-aad-smb-files"></a>
**Depolama hesabımın AD DS kimlik doğrulamasını seçip etkinleştirdiğimde ve etki alanı bilgilerini nasıl denetlerim?**

    Yönergeler için [buraya](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled)bakın.

* <a id=""></a>
**Azure dosyaları Azure AD kimlik doğrulaması, Linux VM 'lerini destekliyor mu?**

    Hayır, Linux VM 'lerden kimlik doğrulaması desteklenmez.

* <a id="ad-multiple-forest"></a>
**Azure dosya paylaşımları için şirket içi AD DS kimlik doğrulaması, birden çok orman kullanarak bir AD DS ortamıyla tümleştirmeyi destekliyor mu?**    

    Azure dosyaları şirket içi AD DS kimlik doğrulaması yalnızca depolama hesabının kaydedildiği etki alanı hizmetinin ormanı ile tümleşir. Başka bir ormandaki kimlik doğrulamasını desteklemek için ortamınızda bir orman güveni doğru şekilde yapılandırılmış olmalıdır. Azure dosyalarının, kimlik doğrulaması için AD DS bir kimlik (bilgisayar veya hizmet oturum açma hesabı) oluşturduğu düzenli bir dosya sunucusu ile neredeyse AD DS kaydetme şekli. Tek fark, depolama hesabının kayıtlı SPN 'si, etki alanı sonekiyle eşleşmeyen "file.core.windows.net" ile sona erecek. Farklı etki alanı son eki nedeniyle birden çok orman kimlik doğrulamasını etkinleştirmek için sonek yönlendirme ilkenize yönelik herhangi bir güncelleştirmenin gerekli olup olmadığını görmek için etki alanı yöneticinize başvurun. Sonek yönlendirme ilkesini yapılandırmak için aşağıda bir örnek sağlıyoruz.
    
    Örnek: Orman A etki alanındaki kullanıcılar, B ormanında bir etki alanında kayıtlı depolama hesabıyla bir dosya paylaşımıyla erişmek istediğinizde, bu otomatik olarak çalışmayacaktır çünkü depolama hesabının hizmet sorumlusu, A ormanındaki herhangi bir etki alanının sonekiyle eşleşen bir sonekine sahip değildir. "File.core.windows.net" özel son eki için A Ormanı A ormanına bir sonek yönlendirme kuralını el ile yapılandırarak bu sorunu ele alabilirsiniz.
    İlk olarak, B ormanına yeni bir özel sonek eklemeniz gerekir. yapılandırmayı değiştirmek için uygun yönetim izinlerine sahip olduğunuzdan emin olun, ardından aşağıdaki adımları uygulayın:   
    1. B ormanına katılmış bir makine etki alanında oturum açma
    2.  "Active Directory etki alanları ve Güvenleri" konsolunu açın
    3.  "Active Directory etki alanları ve Güvenleri" ne sağ tıklayın
    4.  "Özellikler" e tıklayın
    5.  "Ekle" ye tıklayın
    6.  UPN sonekleri olarak "file.core.windows.net" ekleyin
    7.  Sihirbazı kapatmak için "Uygula" ve ardından "Tamam" seçeneğine tıklayın
    
    Sonra, A ormanına son ek yönlendirme kuralını ekleyerek B ormanına yeniden yönlendirir.
    1.  A ormanına katılmış bir makine etki alanında oturum açma
    2.  "Active Directory etki alanları ve Güvenleri" konsolunu açın
    3.  Dosya paylaşımında erişmek istediğiniz etki alanına sağ tıklayın, ardından "güvenler" sekmesine tıklayın ve giden güvenlerden orman B etki alanı ' nı seçin. İki orman arasında güven yapılandırmadıysanız, önce güveni ayarlamanız gerekir
    4.  "Özellikler..." seçeneğine tıklayın sonra "ad soneki yönlendirmesi"
    5.  "*. File.core.windows.net" sonekinin görünür olup olmadığını denetleyin. Aksi takdirde, ' Yenile ' seçeneğine tıklayın
    6.  "*. File.core.windows.net" öğesini seçin, ardından "etkinleştir" ve "Uygula" seçeneğine tıklayın

* <a id=""></a>
**Azure dosyaları AD DS kimlik doğrulaması için hangi bölgeler kullanılabilir?**

    Ayrıntılar için [AD DS bölgesel kullanılabilirlik](storage-files-identity-auth-active-directory-enable.md#regional-availability) bölümüne bakın.
    
* <a id="ad-aad-smb-afs"></a>
**Azure Dosya Eşitleme tarafından yönetilen dosya paylaşımlarında Azure dosyaları Active Directory (AD) kimlik doğrulamasından faydalanabilir miyim?**

    Evet, Azure dosya eşitleme tarafından yönetilen bir dosya paylaşımında AD kimlik doğrulamasını etkinleştirebilirsiniz. Yerel dosya sunucularındaki dizin/dosya NTFS ACL 'lerinde yapılan değişiklikler Azure dosyaları ile katmanlanacaktır ve tam tersi de geçerlidir.

* <a id="ad-aad-smb-files"></a>
**AD 'de depolama hesabımı temsil etmek üzere bir bilgisayar hesabı veya hizmet oturum açma hesabı oluşturmak için herhangi bir farklılık var mı?**

    [Bilgisayar hesabı](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (varsayılan) veya [hizmet oturum açma hesabının](/windows/win32/ad/about-service-logon-accounts) oluşturulması, kimlik doğrulamasının Azure dosyaları ile nasıl çalıştığı konusunda farklılık içermez. Bir depolama hesabını AD ortamınızda kimlik olarak temsil etmek için kendi seçiminizi yapabilirsiniz. Join-AzStorageAccountForAuth cmdlet 'inde varsayılan DomainAccountType kümesi bilgisayar hesabıdır. Ancak, AD ortamınızda yapılandırılan parola süre sonu yaşı bilgisayar veya hizmet oturum açma hesabı için farklı olabilir ve [ad 'de depolama hesabı Kimliğinizin parolasını güncelleştirme](./storage-files-identity-ad-ds-update-password.md)konusunda dikkate almanız gerekir.
 
* <a id="ad-support-rest-apis"></a>
**Dizin/dosya Windows ACL 'Lerini al/ayarla/Kopyala desteği için REST API 'Ler var mı?**

    Evet, [2019-07-07](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (veya üzeri) REST API kullanılırken dizinler veya dosyalar Için NTFS ACL 'LERINI alan REST API 'leri destekliyoruz. Ayrıca REST tabanlı araçlarda kalıcı Windows ACL 'Leri de destekliyoruz: [AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases).

* <a id="ad-support-rest-apis"></a>
**Azure AD veya AD kimlik bilgileriyle yeni bağlantı başlatmadan önce depolama hesabı anahtarı ile önbelleğe alınmış kimlik bilgilerini kaldırma ve mevcut SMB bağlantılarını silme**

    Depolama hesabı anahtarıyla ilişkili kayıtlı kimlik bilgisini kaldırmak ve SMB bağlantısını kaldırmak için aşağıdaki iki adım işlemi izleyebilirsiniz: 
    1. Kimlik bilgisini kaldırmak için Windows Cmd.exe 'de aşağıdaki cmdlet 'i çalıştırın. Bir tane bulamıyorsanız, kimlik bilgisini kalıcı etmeyeceğiniz ve bu adımı atlayabileceği anlamına gelir.
    
       cmdkey/delete: etki alanı: Target = Storage-Account-name.file.core.windows.net
    
    2. Dosya paylaşımıyla var olan bağlantıyı silin. Bağlama yolunu, bağlı sürücü harfi veya storage-account-name.file.core.windows.net yolu olarak belirtebilirsiniz.
    
       Net Use <sürücü-harfi/paylaşma-yol>/Delete

## <a name="network-file-system"></a>Ağ dosya sistemi

* <a id="when-to-use-nfs"></a>
**Azure dosyaları NFS ne zaman kullanmalıyım?**

    Bkz. [NFS paylaşımları (Önizleme)](storage-files-compare-protocols.md#nfs-shares-preview).

* <a id="backup-nfs-data"></a>
**NFS paylaşımlarında depolanan yedekleme verileri Nasıl yaparım? mı?**

    Verilerinizi NFS paylaşımlarında yedeklemek, rsync veya üçüncü taraf yedekleme iş ortaklarından birindeki ürünler gibi tanıdık araç kullanılarak ayarlanabilir. [Commkasası](https://documentation.commvault.com/commvault/v11/article?p=92634.htm), [Veeaz](https://www.veeam.com/blog/?p=123438)ve [VERITAS](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001) dahil olmak üzere birden çok yedekleme iş ortağı Ilk önizlememiz KAPSAMıNDA ve Azure dosyaları IÇIN hem SMB 3,0 hem de NFS 4,1 ile birlikte çalışmak üzere çözümlerini genişletti.

* <a id="migrate-nfs-data"></a>
**Mevcut verileri bir NFS paylaşımında geçirebilir miyim?**

    Bir bölge içinde, verileri taşımak için SCP, rsync veya SSHFS gibi standart araçları kullanabilirsiniz. Azure dosyaları NFS 'nin eşzamanlı olarak birden çok işlem örneğiyle erişilebilir olduğu için, paralel karşıya yükleme ile kopyalama hızlarını geliştirebilirsiniz. Verileri bir bölgenin dışından getirmek istiyorsanız, şirket içi veri merkezinizden dosya sisteminize bağlamak için bir VPN veya ExpressRoute kullanın.

## <a name="on-premises-access"></a>Şirket içi erişim

* <a id="port-445-blocked"></a>
**ISS sunucum veya Azure dosyaları bağlama başarısız olan bağlantı noktası 445 ' i engelliyor. Ne yapmam gerekir?**

    [Geçici çözüm 445 bağlantı noktası ' i engelleyen çeşitli yollar](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)hakkında bilgi edinebilirsiniz. Azure dosyaları, yalnızca bölge veya veri merkezi dışından SMB 3,0 (şifreleme desteği ile) kullanan bağlantılara izin verir. SMB 3,0 protokolü, internet üzerinden kullanılması çok güvenli olan kanal şifreleme dahil pek çok güvenlik özelliği sunmuştur. Ancak, daha düşük SMB sürümlerinde bulunan güvenlik açıklarının geçmiş nedenlerinden dolayı 445 numaralı bağlantı noktası engellenmiş olabilir. İdeal durumda, bağlantı noktasının yalnızca SMB 1,0 trafiği için engellenmesi ve SMB 1,0 ' nin tüm istemcilerde kapalı olması gerekir.

* <a id="expressroute-not-required"></a>
**Azure dosyaları 'na bağlanmak veya şirket içi Azure Dosya Eşitleme kullanmak için Azure ExpressRoute 'u kullanmak zorunda mıyım?**  

    Hayır. ExpressRoute 'un bir Azure dosya paylaşımının erişimine yönelik olması gerekmez. Doğrudan şirket içi bir Azure dosya paylaşımından bağlantı oluşturuyorsanız, tüm bu gerekli olan bağlantı noktası 445 (TCP Giden) internet erişimi için açık olmalıdır (Bu, SMB 'nin iletişim kurmak için kullandığı bağlantı noktasıdır). Azure Dosya Eşitleme kullanıyorsanız, tüm gerekli olan HTTPS erişimi için bağlantı noktası 443 (TCP Giden) ' dir (SMB gerekmez). Ancak, ExpressRoute 'u bu erişim seçeneklerinden *biriyle kullanabilirsiniz.*

* <a id="mount-locally"></a>
**Yerel makinemdeki bir Azure dosya paylaşımından nasıl bağlayabilirim?**  

    Bağlantı noktası 445 (TCP Giden) açıksa ve istemciniz SMB 3,0 protokolünü destekliyorsa (örneğin, Windows 10 veya Windows Server 2016 kullanıyorsanız), SMB protokolünü kullanarak dosya paylaşımından bağlama yapabilirsiniz. Bağlantı noktası 445, kuruluşunuzun ilkesi veya ISS 'niz tarafından engellenmişse Azure dosya paylaşımınıza erişmek için Azure Dosya Eşitleme kullanabilirsiniz.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Azure dosya paylaşımımı Nasıl yaparım? yedeklerim?**  
    Yanlışlıkla silinmelere karşı koruma için düzenli aralıklarla [paylaşma anlık görüntülerini](storage-snapshots-files.md) kullanabilirsiniz. Ayrıca, bağlı bir dosya paylaşımının yedekleyebilen AzCopy, Robocopy veya bir üçüncü taraf yedekleme aracını da kullanabilirsiniz. Azure Backup Azure dosyalarının yedeklemesini sunmaktadır. [Azure Backup göre Azure dosya paylaşımlarını yedekleme](../../backup/backup-afs.md)hakkında daha fazla bilgi edinin.

## <a name="share-snapshots"></a>Anlık görüntü paylaşma

### <a name="share-snapshots-general"></a>Anlık görüntü paylaşma: genel
* <a id="what-are-snaphots"></a>
**Dosya paylaşma anlık görüntüleri nelerdir?**  
    Azure dosya paylaşımı anlık görüntülerini, dosya paylaşımlarınızın salt okunurdur bir sürümünü oluşturmak için kullanabilirsiniz. Ayrıca, Azure dosyalarını, içeriğinizin önceki bir sürümünü yeniden aynı paylaşıma, Azure 'da alternatif bir konuma veya daha fazla değişiklik için şirket içi olarak kopyalamak için de kullanabilirsiniz. Anlık görüntü paylaşma hakkında daha fazla bilgi edinmek için bkz. [paylaşma anlık görüntüsüne genel bakış](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Paylaşma anlık görüntülerim nerede depolanıyor?**  
    Paylaşılan anlık görüntüler, dosya paylaşımıyla aynı depolama hesabında depolanır.

* <a id="snapshot-consistency"></a>
**Paylaşılan anlık görüntüler uygulamayla tutarlı mi?**  
    Hayır, paylaşılan anlık görüntü, uygulamayla tutarlı değil. Kullanıcının, paylaşma anlık görüntüsünü almadan önce uygulamadan yazma işlemlerini paylaşıma temizlemesi gerekir.

* <a id="snapshot-limits"></a>
**Kullandığım paylaşılan anlık görüntü sayısı için sınırlamalar var mı?**  
    Evet. Azure dosyaları, en fazla 200 paylaşma anlık görüntüsünü koruyabilir. Paylaşma anlık görüntüleri, paylaşma kotasına doğru sayılmaz, bu nedenle tüm paylaşılan anlık görüntüler tarafından kullanılan toplam alanda paylaşma başına bir sınır yoktur. Depolama hesabı sınırları hala geçerlidir. 200 anlık görüntüleri paylaştıktan sonra yeni paylaşılan anlık görüntüler oluşturmak için eski anlık görüntüleri silmeniz gerekir.

* <a id="snapshot-cost"></a>
**Anlık görüntü paylaşma maliyeti ne kadar?**  
    Standart işlem ve standart depolama maliyeti, anlık görüntü için geçerlidir. Anlık görüntüler doğası halinde artımsal. Temel anlık görüntü paylaşımın kendisidir. Sonraki tüm anlık görüntüler artımlı olur ve yalnızca önceki anlık görüntüdeki farkı depolar. Bu, iş yükü karmaşıklığının en az olması halinde faturanızda görülen Delta değişikliklerinin en az olacağı anlamına gelir. Standart Azure dosyaları fiyatlandırma bilgileri için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/storage/files/) . Bugün, paylaşılan anlık görüntü tarafından tüketilen boyutu görüntüleme yolu, faturalanan kapasiteyi kullanılan kapasiteyle karşılaştırmaktır. Raporlamayı geliştirmek için araç üzerinde çalışıyoruz.

* <a id="ntfs-acls-snaphsots"></a>
**Dizinler ve dosyalardaki NTFS ACL 'Leri, paylaşılan anlık görüntülerle kalıcı mi?**  
    Dizinler ve dosyalardaki NTFS ACL 'Leri, paylaşılan anlık görüntülerle kalıcı hale getirilir.

### <a name="create-share-snapshots"></a>Paylaşım anlık görüntüsü oluşturma
* <a id="file-snaphsots"></a>
**Tek tek dosyaların paylaşma anlık görüntüsünü oluşturabilir miyim?**  
    Paylaşma anlık görüntüleri dosya paylaşma düzeyinde oluşturulur. Dosya paylaşımının anlık görüntüsünden tek tek dosyaları geri yükleyebilirsiniz, ancak dosya düzeyi paylaşılan anlık görüntüler oluşturamazsınız. Ancak, paylaşma düzeyi bir paylaşılan anlık görüntü aldıysanız ve belirli bir dosyanın değiştiği paylaşılan anlık görüntüleri listelemek istiyorsanız, bunu Windows bağlı bir paylaşımdaki **önceki sürümler** altında yapabilirsiniz. 
    
    Bir dosya anlık görüntüsü özelliği gerekiyorsa, [Azure dosyaları UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)' da bize bilgi verin.

* <a id="encrypted-snapshots"></a>
**Şifrelenmiş bir dosya paylaşımının paylaşılan anlık görüntülerini oluşturabilir miyim?**  
    Bekleyen bir şifreleme özelliği olan Azure dosya paylaşımlarının paylaşım anlık görüntüsünü alabilirsiniz. Dosyaları bir paylaşma anlık görüntüsünden şifreli bir dosya paylaşımında geri yükleyebilirsiniz. Paylaşımınız şifrelendiyse, paylaşma anlık görüntüsü de şifrelenir.

* <a id="geo-redundant-snaphsots"></a>
**Paylaşma anlık görüntülerimin coğrafi olarak yedekli mi?**  
    Paylaşılan anlık görüntüler, alındığı Azure dosya paylaşımıyla aynı yedekliliğe sahiptir. Hesabınız için coğrafi olarak yedekli depolamayı seçtiyseniz, paylaşma anlık görüntüsü de eşleştirilmiş bölgede Redundantly depolanır.

### <a name="manage-share-snapshots"></a>Paylaşma anlık görüntülerini yönetme
* <a id="browse-snapshots-linux"></a>
**Paylaşılan anlık görüntülerimi Linux 'tan tarayabiliyor miyim?**  
    Linux 'ta paylaşma anlık görüntülerini oluşturmak, listelemek, taramak ve geri yüklemek için Azure CLı kullanabilirsiniz.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Paylaşma anlık görüntülerini farklı bir depolama hesabına kopyalayabilir miyim?**  
    Dosyaları Share Snapshot dosyalarından başka bir konuma kopyalayabilirsiniz, ancak paylaşımın anlık görüntülerini kendi kendilerine kopyalayamazsınız.

### <a name="restore-data-from-share-snapshots"></a>Paylaşma anlık görüntülerinden verileri geri yükleme
* <a id="promote-share-snapshot"></a>
**Bir Share anlık görüntüsünü temel paylaşıma yükseltebilir miyim?**  
    Bir Share anlık görüntüsünden diğer hedeflere veri kopyalayabilirsiniz. Bir Share anlık görüntüsünü temel paylaşıma yükseltemezsiniz.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Paylaşma anlık Görüntümdeki verileri farklı bir depolama hesabına geri yükleyebilir miyim?**  
    Evet. Bir paylaşma anlık görüntüsüne ait dosyalar özgün konuma veya aynı veya farklı bölgelerde aynı depolama hesabını ya da farklı bir depolama hesabını içeren alternatif bir konuma kopyalanabilir. Ayrıca, dosyaları şirket içi bir konuma veya diğer bir buluta kopyalayabilirsiniz.    
  
### <a name="clean-up-share-snapshots"></a>Paylaşma anlık görüntülerini temizle
* <a id="delete-share-keep-snapshots"></a>
**Paylaşımımı silebilir, ancak paylaşma anlık görüntülerimi silmeyebilir miyim?**  
    Paylaşımınızda etkin bir paylaşılan anlık görüntü varsa, paylaşımınızı silemezsiniz. Paylaşma ile birlikte paylaşılan anlık görüntüleri silmek için bir API kullanabilirsiniz. Ayrıca, Azure portal hem paylaşma anlık görüntülerini hem de paylaşmayı silebilirsiniz.

* <a id="delete-share-with-snapshots"></a>
**Depolama hesabımı silersem paylaşma anlık görüntülerime ne olur?**  
    Depolama hesabınızı silerseniz, paylaşma anlık görüntüleri de silinir.

## <a name="billing-and-pricing"></a>Faturalandırma ve fiyatlandırma
* <a id="vm-file-share-network-traffic"></a>
**Azure VM ile bir Azure dosya paylaşımının arasındaki ağ trafiği, aboneliğe ücretlendirilen dış bant genişliği olarak mı sayılır?**  
    Dosya paylaşma ve VM aynı Azure bölgedeyse, dosya paylaşımıyla VM arasındaki trafik için ek ücret alınmaz. Dosya paylaşma ve VM farklı bölgelerde ise, aralarındaki trafik dış bant genişliği olarak ücretlendirilir.

* <a id="share-snapshot-price"></a>
**Anlık görüntü paylaşma maliyeti ne kadar?**  
     Önizleme sırasında, paylaşılan anlık görüntü kapasitesi ücretsizdir. Standart depolama çıkış ve işlem maliyetleri geçerlidir. Genel kullanılabilirlik sonrasında, abonelikler, paylaşılan anlık görüntülerle kapasite ve işlemler için ücretlendirilecektir.
     
     Paylaşılan anlık görüntü, doğası halinde artımsal. Temel paylaşma anlık görüntüsü paylaşımın kendisidir. Sonraki tüm paylaşılan anlık görüntüler artımlı yapılır ve yalnızca önceki paylaşılan anlık görüntüden farkı depolar. Yalnızca değiştirilen içerik için faturalandırılırsınız. 100 GiB veri içeren bir paylaşımınız varsa ancak son paylaşma anlık görüntüsünden bu yana yalnızca 5 GiB değişmişse, paylaşma anlık görüntüsü yalnızca 5 ek GiB kullanır ve 105 GiB için faturalandırılırsınız. İşlem ve standart çıkış ücretleri hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/storage/files/)bakın.

## <a name="scale-and-performance"></a>Ölçek ve performans
* <a id="files-scale-limits"></a>
**Azure dosyalarının ölçek sınırları nelerdir?**  
    Azure dosyaları için ölçeklenebilirlik ve performans hedefleri hakkında daha fazla bilgi için bkz. [Azure dosyaları ölçeklenebilirlik ve performans hedefleri](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Azure dosya paylaşımları için hangi boyutlar kullanılabilir?**  
    Azure dosya paylaşma boyutları (Premium ve standart) 100 TiB 'ye kadar ölçeklenebilir. Standart katmana yönelik daha büyük dosya paylaşımlarına yönelik yönergeler için planlama kılavuzunun [daha büyük dosya paylaşımlarına (Standart katman)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) ekleme bölümüne bakın.

* <a id="lfs-performance-impact"></a>
**Dosya paylaşma kotamı genişleterek iş yüklerimi veya Azure Dosya Eşitleme mi etkiler?**
    
    Hayır. Kotayı genişletmek, iş yüklerinizi veya Azure Dosya Eşitleme etkilemez.

* <a id="open-handles-quota"></a>
**Aynı dosyaya aynı anda kaç istemci erişebilir?**   
    Tek bir dosyada 2.000 açık tanıtıcı kotası vardır. 2.000 açık tutamacı varsa, kotanın ulaşılmadığını belirten bir hata iletisi görüntülenir.

* <a id="zip-slow-performance"></a>
**Azure dosyalarındaki dosyaları unzip diğimde performanmın yavaşlamasına neden olur. Ne yapmam gerekir?**  
    Azure dosyalarına çok sayıda dosya aktarmak için AzCopy (Windows için, Linux ve UNIX için Önizleme sürümünde) veya Azure PowerShell kullanmanızı öneririz. Bu araçlar ağ aktarımı için iyileştirildi.

* <a id="slow-perf-windows-81-2012r2"></a>
**Azure dosya paylaşımımı Windows Server 2012 R2 veya Windows 8.1 'de taktıktan sonra neden performanmın yavaşlamasına neden olur?**  
    Windows Server 2012 R2 ve Windows 8.1 bir Azure dosya paylaşımının takılacağı bilinen bir sorun vardır. Sorun, Windows 8.1 ve Windows Server 2012 R2 için Nisan 2014 toplu güncelleştirmesinde düzeltme eki eklendi. En iyi performans için, tüm Windows Server 2012 R2 ve Windows 8.1 örneklerinin Bu düzeltme ekinin uygulandığından emin olun. (Windows Update aracılığıyla her zaman Windows düzeltme eklerini almalısınız.) Daha fazla bilgi için, [Windows 8.1 veya Server 2012 R2 'Den Azure dosyalarına eriştiğinizde](https://support.microsoft.com/kb/3114025)Ilişkili Microsoft Bilgi Bankası makalesine bakın.

## <a name="features-and-interoperability-with-other-services"></a>Diğer hizmetlerle Özellikler ve birlikte çalışabilirlik
* <a id="cluster-witness"></a>
**Azure dosya paylaşımımı, Windows Server yük devretme Kümemdeki *dosya paylaşma tanığı* olarak kullanabilir miyim?**  
    Şu anda bu yapılandırma bir Azure dosya paylaşımında desteklenmez. Bunu Azure Blob depolama için ayarlama hakkında daha fazla bilgi için bkz. [Yük devretme kümesi Için bulut tanığı dağıtma](/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Azure Container Instance üzerinde bir Azure dosya paylaşımından bağlanabilir miyim?**  
    Evet, Azure dosya paylaşımları, bir kapsayıcı örneğinin kullanım ömrünün ötesinde bilgileri kalıcı hale getirmek istediğinizde iyi bir seçenektir. Daha fazla bilgi için bkz. Azure [Container Instances Ile Azure dosya paylaşma bağlama](../../container-instances/container-instances-volume-azure-files.md).

* <a id="rest-rename"></a>
**REST API bir yeniden adlandırma işlemi var mı?**  
    Şu anda değil.

* <a id="nested-shares"></a>
**İç içe paylaşımları ayarlayabilir miyim? Diğer bir deyişle, bir paylaşıma ait mi?**  
    Hayır. Dosya paylaşımı, takabilmeniz gereken sanal bir sürücü *olduğundan* iç içe paylaşımlar desteklenmez.

* <a id="ibm-mq"></a>
**IBM MQ ile Azure dosyaları Nasıl yaparım? mi kullanıyorsunuz?**  
    IBM, IBM MQ müşterilerinin Azure dosyalarını IBM hizmetiyle yapılandırmalarına yardımcı olan bir belge yayımlamıştır. Daha fazla bilgi için bkz. [Microsoft Azure dosyaları hizmeti Ile IBM MQ çok örnekli kuyruk yöneticisi ayarlama](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Ayrıca bkz.
* [Windows 'da Azure dosyaları sorunlarını giderme](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux 'ta Azure dosyaları sorunlarını giderme](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure Dosya Eşitleme'de sorun giderme](../file-sync/file-sync-troubleshoot.md)
