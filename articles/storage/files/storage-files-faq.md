---
title: Azure dosyaları için sık sorulan sorular (SSS) | Microsoft Docs
description: Azure dosyaları hakkında sık sorulan soruların yanıtlarını bulun.
author: roygara
ms.service: storage
ms.date: 07/30/2019
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 6526f27177b5fb8640deb5302d8cb3aa4acf1a97
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824270"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Azure dosyaları hakkında sık sorulan sorular (SSS)
[Azure dosyaları](storage-files-introduction.md) , bulutta endüstri standardı [sunucu ILETI bloğu (SMB) protokolü](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)aracılığıyla erişilebilen tam olarak yönetilen dosya paylaşımları sunar. Azure dosya paylaşımlarını bulutta veya Windows, Linux ve macOS 'ın şirket içi dağıtımlarında eşzamanlı olarak bağlayabilirsiniz. Ayrıca, verilerin kullanıldığı yere hızlı erişim için Azure Dosya Eşitleme kullanarak Windows Server makinelerinde Azure dosya paylaşımlarını önbelleğe alabilirsiniz.

Bu makalede, Azure dosyaları ile Azure Dosya Eşitleme kullanımı dahil olmak üzere Azure dosyaları özellikleri ve işlevleri hakkında sık sorulan sorular yanıtlanmaktadır. Sorunuzun yanıtını görmüyorsanız, aşağıdaki kanallarla (yürüyen sırada) bizimle iletişim kurmanız gerekir:

1. Bu makalenin açıklamalar bölümü.
2. [Azure depolama Forumu](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure dosyaları UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft Desteği. Yeni bir destek isteği oluşturmak için, Azure portal **Yardım** sekmesinde **Yardım + Destek** düğmesini seçin ve ardından **Yeni destek isteği**' ni seçin.

## <a name="general"></a>Genel
* <a id="why-files-useful"></a>**Azure dosyaları nasıl yararlı 
  ?**  
   Fiziksel sunucu, cihaz veya gereç yükünü yönetmeden sorumlu olmadan bulutta dosya paylaşımları oluşturmak için Azure dosyalarını kullanabilirsiniz. İşletim sistemi güncelleştirmelerini uygulama ve hatalı disklerin değiştirilmesi dahil olmak üzere, sizin için tek bir iş işi yaptık. Azure dosyalarının konusunda size yardımcı olabilecek senaryolar hakkında daha fazla bilgi edinmek için [Azure dosyalarının neden yararlı olduğuna](storage-files-introduction.md#why-azure-files-is-useful)bakın.

* <a id="file-access-options"></a>
  **Azure dosyalarındaki dosyalara erişmenin farklı yolları nelerdir?**  
    SMB 3,0 protokolünü kullanarak dosya paylaşımından yerel makinenize bağlanabilir veya dosya paylaşımınızda dosyalara erişmek için [Depolama Gezgini](https://storageexplorer.com/) gibi araçları kullanabilirsiniz. Uygulamanızdan Azure dosya paylaşımında dosyalarınıza erişmek için depolama istemci kitaplıklarını, REST API 'Leri, PowerShell veya Azure CLı kullanabilirsiniz.

* <a id="what-is-afs"></a>**Azure dosya eşitleme ne 
  ?**  
    Kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirirken, şirket içi bir dosya sunucusunun esnekliğini, performansını ve uyumluluğunu koruyarak Azure Dosya Eşitleme kullanabilirsiniz. Azure Dosya Eşitleme, Windows Server makinelerinizi Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürür. SMB, ağ dosya sistemi (NFS) ve Dosya Aktarım Protokolü Hizmeti (FTPS) dahil olmak üzere verilerinize yerel olarak erişmek için Windows Server 'da bulunan herhangi bir protokolü kullanabilirsiniz. Dünyanın dört bir yanında ihtiyacınız olan sayıda önbellekler olabilir.

* <a id="files-versus-blobs"></a>
  **Azure dosya paylaşımından Azure Blob depolama alanı Için neden kullanmalıyım?**  
    Azure dosyaları ve Azure Blob depolama, büyük miktarlarda verileri bulutta depolamanın yollarını sunmaktadır, ancak bazıları biraz farklı amaçlar için yararlıdır. 
    
    Azure Blob depolama, yapılandırılmamış verileri depolaması gereken büyük ölçekli, bulutta yerel uygulamalar için yararlıdır. Performansı ve ölçeği en üst düzeye çıkarmak için, Azure Blob depolama, doğru bir dosya sisteminden daha basit bir depolama soyutlamasıdır. Azure Blob depolamaya yalnızca REST tabanlı istemci kitaplıkları aracılığıyla erişebilirsiniz (veya doğrudan REST tabanlı protokolde).

    Azure dosyaları özellikle bir dosya sistemidir. Azure dosyaları, şirket içi işletim sistemleriyle çalışma yıllarından bildiğiniz ve sevdiğiniz tüm dosya soyutlarına sahiptir. Azure Blob depolama gibi Azure dosyaları da bir REST arabirimi ve REST tabanlı istemci kitaplıkları sunar. Azure Blob depolama alanının aksine Azure dosyaları, Azure dosya paylaşımlarına SMB erişimi sağlar. SMB kullanarak, herhangi bir kod yazmadan veya dosya sistemine özel bir sürücü eklemeden veya şirket içinde ya da bulut VM 'lerinde Azure dosya paylaşımından doğrudan Windows, Linux veya macOS üzerinde bağlantı takabilirsiniz. Ayrıca, hızlı erişim için Azure Dosya Eşitleme kullanarak Azure dosya paylaşımlarını önbelleğe alabilir ve verilerin kullanıldığı yere yakın bir şekilde erişebilirsiniz. 
   
    Azure dosyaları ile Azure Blob depolama arasındaki farklılıklarla ilgili daha ayrıntılı bir açıklama için bkz. [Azure Blob depolama, Azure dosyaları veya Azure diskleri ne zaman kullanılacağına karar verme](../common/storage-decide-blobs-files-disks.md). Azure Blob depolama hakkında daha fazla bilgi için bkz. [BLOB depolamaya giriş](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Azure diskleri yerine neden Azure dosya paylaşımından kullanmalıyım?**  
    Azure disklerindeki bir disk yalnızca bir disktir. Azure disklerinden değer almak için, Azure 'da çalışan bir sanal makineye bir disk bağlamanız gerekir. Azure diskleri, bir şirket içi sunucuda için disk kullanacağınızı her şey için kullanılabilir. Bunu bir işletim sistemi sistem diski olarak, bir işletim sistemi için takas alanı veya bir uygulama için ayrılmış depolama olarak kullanabilirsiniz. Azure diskleri için ilginç bir kullanım, bulutta bir Azure dosya paylaşımının kullanıldığı yerlerde kullanılacak bir dosya sunucusu oluşturmaktır. Azure sanal makinelerinde bir dosya sunucusu dağıtmak, şu anda Azure dosyaları tarafından desteklenmeyen (NFS protokol desteği veya Premium depolama gibi) dağıtım seçenekleri gerektirdiğinde Azure 'da dosya depolamayı almanın yüksek performanslı bir yoludur. 

    Ancak, arka uç depolama olarak Azure diskleriyle bir dosya sunucusu çalıştırmak, birkaç nedenden dolayı bir Azure dosya paylaşımının kullanılmasıyla çok daha pahalıdır. İlk olarak, disk depolama alanı ödemesinin yanı sıra bir veya daha fazla Azure sanal makinesi çalıştırmanın masrafına de ödeme yapmanız gerekir. İkincisi de dosya sunucusunu çalıştırmak için kullanılan VM 'Leri yönetmeniz gerekir. Örneğin, işletim sistemi yükseltmelerinden siz sorumlusunuz. Son olarak, verilerin şirket içinde önbelleğe alınması gerekiyorsa, bunu yapmak için Dağıtılmış Dosya Sistemi Çoğaltma (DFSR) gibi çoğaltma teknolojilerini ayarlamak ve yönetmek en iyisidir.

    Hem Azure hem de Azure sanal makinelerinde barındırılan bir dosya sunucusunun en iyi şekilde alınması için bir yaklaşım (Azure disklerini arka uç depolama olarak kullanmanın yanı sıra), bulut VM 'de barındırılan bir dosya sunucusuna Azure Dosya Eşitleme yüklemektir. Azure dosya paylaşımının dosya sunucunuz ile aynı bölgedeyse, bulut katmanlamayı etkinleştirebilir ve boş alan yüzdesini maksimum (%99%) olarak ayarlayabilirsiniz. Bu, verilerin çok az çoğaltılmasını sağlar. Ayrıca, NFS protokol desteği gerektiren uygulamalar gibi dosya sunucularınız ile istediğiniz uygulamaları kullanabilirsiniz.

    Azure 'da yüksek performanslı ve yüksek oranda kullanılabilir bir dosya sunucusu ayarlama seçeneği hakkında bilgi için, bkz. [Microsoft Azure IaaS VM Konuk kümelerini dağıtma](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Azure dosyaları ile Azure diskleri arasındaki farklılıklarla ilgili daha ayrıntılı bir açıklama için bkz. [Azure Blob depolama, Azure dosyaları veya Azure diskleri ne zaman kullanılacağına karar verme](../common/storage-decide-blobs-files-disks.md). Azure diskleri hakkında daha fazla bilgi edinmek için bkz. [Azure yönetilen disklere genel bakış](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>**Azure dosyaları kullanmaya başlamak Nasıl yaparım? 
  mı?**  
   Azure dosyalarını kullanmaya başlamak kolaydır. İlk olarak, [bir dosya paylaşma oluşturun](storage-how-to-create-file-share.md)ve bunu tercih ettiğiniz işletim sistemine bağlayın: 

  * [Windows 'da bağla](storage-how-to-use-files-windows.md)
  * [Linux 'ta bağlama](storage-how-to-use-files-linux.md)
  * [MacOS 'ta bağlama](storage-how-to-use-files-mac.md)

    Kuruluşunuzda üretim dosya paylaşımlarının yerini alacak bir Azure dosya paylaşımının dağıtılması hakkında daha ayrıntılı bir kılavuz için bkz. [Azure dosyaları dağıtımı Için planlama](storage-files-planning.md).

* <a id="redundancy-options"></a>**Azure dosyaları tarafından hangi depolama yedekliliği seçeneklerinin desteklendiğini 
  ?**  
    Şu anda Azure dosyaları yerel olarak yedekli depolama (LRS), bölgesel olarak yedekli depolama (ZRS), coğrafi olarak yedekli depolama (GRS) ve coğrafi bölge-yedekli depolama (GZRS) (Önizleme) destekler. Gelecekte Okuma Erişimli Coğrafi olarak yedekli (RA-GRS) depolamayı desteklemeyi planlıyoruz, ancak şu anda paylaşılacak zaman çizelgelerinizi yok.

* <a id="tier-options"></a>**Azure dosyalarında hangi depolama katmanlarının desteklendiğini 
  ?**  
    Azure dosyaları iki depolama katmanını destekler: Premium ve standart. Standart dosya paylaşımları genel amaçlı olarak oluşturulur (GPv1 veya GPv2) depolama hesapları ve Premium dosya paylaşımları, FileStorage depolama hesaplarında oluşturulur. [Standart dosya paylaşımları](storage-how-to-create-file-share.md) ve [Premium dosya paylaşımları](storage-how-to-create-premium-fileshare.md)oluşturma hakkında daha fazla bilgi edinin. 
    
    > [!NOTE]
    > BLOB depolama hesaplarından veya *Premium* genel amaçlı (GPv1 veya GPv2) depolama hesaplarından Azure dosya paylaşımları oluşturamazsınız. Standart Azure dosya paylaşımlarının yalnızca *Standart* genel amaçlı hesaplarda oluşturulması gerekir ve Premium Azure dosya paylaşımları yalnızca dosya depolama depolama hesaplarında oluşturulmalıdır. *Premium* genel amaçlı (GPv1 ve GPv2) depolama hesapları yalnızca Premium sayfa Blobları içindir. 

* <a id="give-us-feedback"></a>**Azure dosyaları 'na eklenen belirli bir özelliği gerçekten görmek istiyorum 
  . Eklenebilir mi?**  
    Azure dosyaları ekibi, hizmetimiz ile ilgili tüm geri bildirimleri ve tüm geri bildirimlerinizi duymakla ilgileniyor. Lütfen [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)'daki özellik isteklerini oylayın! Birçok yeni özellik ile size çok fazla göz ımızı umuyoruz.

## <a name="azure-file-sync"></a>Azure Dosya Eşitleme

* <a id="afs-region-availability"></a>**Azure dosya eşitleme Için hangi bölgelerin desteklendiği 
  ?**  
    Kullanılabilir bölgelerin listesi, Azure Dosya Eşitleme planlama kılavuzunun [bölge kullanılabilirliği](storage-sync-files-planning.md#region-availability) bölümünde bulunabilir. Genel olmayan bölgeler dahil olmak üzere ek bölgeler için sürekli olarak destek ekleyeceğiz.

* <a id="cross-domain-sync"></a>
  **aynı eşitleme grubunda etki alanına katılmış ve etki alanına katılmamış sunuculara sahip olabilir miyim?**  
    Evet. Bir eşitleme grubu, etki alanına katılmış olmasalar bile farklı Active Directory üyeliklerine sahip sunucu uç noktalarını içerebilir. Bu yapılandırma Teknik olarak çalışsa da, bir sunucudaki dosyalar ve klasörler için tanımlanan erişim denetim listeleri (ACL 'Ler), eşitleme grubundaki diğer sunucular tarafından zorlanamayacak olabileceğinden, bunu tipik bir yapılandırma olarak önermiyoruz. En iyi sonuçlar için, aynı Active Directory ormanında olan sunucular arasında, farklı Active Directory ormanlarda bulunan ancak güven ilişkileri oluşturan sunucular arasında veya bir etki alanında olmayan sunucular arasında eşitleme yapmanızı öneririz. Bu yapılandırmaların karışımını kullanmaktan kaçınmanızı öneririz.

* <a id="afs-change-detection"></a>
  **bir dosyayı doğrudan Azure dosya paylaşımım içinde, SMB veya portalda kullanarak oluşturdum. Dosyanın eşitleme grubundaki sunucularla eşitlenmesi ne kadar sürer?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Aynı dosya yaklaşık olarak aynı anda iki sunucuda değiştirilirse ne olur?**  
    Azure Dosya Eşitleme basit bir çakışma çözümü stratejisi kullanır: iki sunucu üzerinde aynı anda değiştirilen dosyalarda her iki değişikliği de tutuyoruz. En son yazılan değişiklik özgün dosya adını tutar. Eski dosyanın "kaynak" makinesi ve bu ada eklenen çakışma numarası vardır. Bu taksonomiyi izler: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\<ext\>  

    Örneğin, CompanyReport-CentralServer. docx ' in ilk çakışması, merkezileştirme sunucusu daha eski yazmanın gerçekleştiği yerdir. İkinci çakışma CompanyReport-CentralServer-1. docx olarak adlandırılır. Azure Dosya Eşitleme dosya başına 100 çakışma dosyasını destekler. Çakışma dosyası sayısı üst sınırına ulaşıldığında, çakışma dosyası sayısı 100 ' den az olana kadar dosya eşitleme başarısız olur.

* <a id="afs-storage-redundancy"></a>
  **Azure dosya eşitleme için coğrafi olarak yedekli depolama alanı destekleniyor mu?**  
    Evet, Azure dosyaları yerel olarak yedekli depolama (LRS) ve coğrafi olarak yedekli depolamayı (GRS) destekler. GRS için yapılandırılmış bir hesaptan eşleştirilmiş bölgeler arasında bir depolama hesabı yük devretmesi başlatırsanız, Microsoft yeni bölgeyi yalnızca verilerin bir yedeklemesi olarak değerlendirmenizi önerir. Azure Dosya Eşitleme yeni birincil bölgeyle eşitlemeye otomatik olarak başlamaz. 

* <a id="sizeondisk-versus-size"></a>
  **bir dosyanın *disk özelliğindeki boyut* , Azure dosya eşitleme kullanıldıktan sonra *Boyut* özelliği ile eşleşmiyor mu?**  
  Bkz. [bulut katmanlamayı anlama](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>**bir dosyanın katmanlı olup olmadığını nasıl anlayabilirim 
  ?**  
  Bkz. [bulut katmanlamayı anlama](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Kullanmak istediğim bir dosya katmanlı. Dosyayı yerel olarak kullanmak için diske nasıl geri çekirim?**  
  Bkz. [bulut katmanlamayı anlama](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>**bir dosya veya dizinin katmanlanmasını zor
  nasıl yaparım? mi?**  
  Bkz. [bulut katmanlamayı anlama](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>**bir birimde birden çok sunucu uç noktası olduğunda *birim boş alanı* nasıl yorumlanır 
  ?**  
  Bkz. [bulut katmanlamayı anlama](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>**hangi dosya ve klasörlerin Azure dosya eşitleme tarafından otomatik olarak dışlandığından 
  ?**  
    Varsayılan olarak, Azure Dosya Eşitleme aşağıdaki dosyaları dışlar:
  * Desktop. ini
  * thumbs. db
  * ehThumbs. db
  * ~$\*.\*
  * \*. laccdb
  * \*. tmp
  * 635D02A9D91C401B97884B82B3BCDADEA.\*

    Aşağıdaki klasörler varsayılan olarak da dışarıda bırakılır:

  * \System Volume bilgileri
  * GERI dönüşüm \$. BÖLME
  * \Syncsharemlak

* <a id="afs-os-support"></a>
   **, Windows Server 2008 R2, Linux veya ağa bağlı depolama (NAS) cihazından Azure dosya eşitleme kullanabilir miyim?**  
    Şu anda Azure Dosya Eşitleme yalnızca Windows Server 2019, Windows Server 2016 ve Windows Server 2012 R2 'yi destekler. Şu anda paylaşabiliriz başka bir planımız yok, ancak müşteri talebine göre ek platformları desteklemeye çalışıyoruz. [Azure dosyaları UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 'ta hangi platformları destekleyeceğinizi bize bildirmek istiyorum.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **katmanlı dosyaların sunucu uç noktası ad alanı dışında neden var?**  
    Azure Dosya Eşitleme Agent sürüm 3 ' ten önce, Azure Dosya Eşitleme katmanlı dosyaların sunucu uç noktası dışında, ancak sunucu uç noktasıyla aynı birimde taşınmasını engelledi. Kopyalama işlemleri, katmanlı olmayan dosyaların taşınması ve katmanlı diğer birimlere taşınması etkilenmemiştir. Bu davranışın nedeni, dosya Gezgini 'nin ve diğer Windows API 'Lerinin aynı birim üzerinde taşıma işlemlerinin (neredeyse) anlık yeniden adlandırma işlemlerinde olduğu örtük bir varsayımına sahiptir. Bu, taşınmaların dosya Gezgini veya diğer taşıma yöntemlerinin (komut satırı veya PowerShell gibi), verileri buluttan geri çekAzure Dosya Eşitleme irken yanıt vermemesine neden olacağı anlamına gelir. [Azure dosya eşitleme aracı sürümü 3.0.12.0](storage-files-release-notes.md#supported-versions)ile başlayarak, Azure dosya eşitleme, katmanlı bir dosyayı sunucu uç noktası dışına taşımanızı sağlayacak. Katmanlı dosyanın sunucu uç noktası dışında katmanlı bir dosya olarak var olmasına ve sonra dosyayı arka planda geri çekmesine izin vererek, daha önce bahsedilen negatif etkilerden kaçının. Bu, aynı birimdeki taşımanın anında olduğu ve taşıma tamamlandıktan sonra dosyayı diske geri çekmek için tüm işleri yaptığımız anlamına gelir. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Sunucum üzerinde Azure dosya eşitleme bir sorun yaşıyorum (eşitleme, bulut katmanlama vb.). Sunucu uç noktasını kaldırıp yeniden oluşturmanız gerekir mi?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **depolama eşitleme hizmetini ve/veya depolama hesabını farklı bir kaynak grubuna veya aboneliğe taşıyabilir miyim?**  
   Evet, depolama eşitleme hizmeti ve/veya depolama hesabı, mevcut Azure AD kiracısı içinde farklı bir kaynak grubuna veya aboneliğe taşınabilir. Depolama hesabı taşınmışsa, karma Dosya Eşitleme hizmetine depolama hesabına erişim sağlamanız gerekir (bkz. [Azure dosya eşitleme depolama hesabına erişimi olduğundan emin olun](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure Dosya Eşitleme, aboneliğin farklı bir Azure AD kiracısına taşınmasını desteklemez.
    
* <a id="afs-ntfs-acls"></a>
   **, dizin/dosya DÜZEYINDE NTFS ACL 'Lerini Azure dosyalarında depolanan verilerle birlikte korumak Azure dosya eşitleme ister misiniz?**

    Şirket içi dosya sunucularından taşınan NTFS ACL 'Leri, meta veri olarak Azure Dosya Eşitleme tarafından kalıcı hale getirilir. Azure dosyaları, Azure Dosya Eşitleme hizmeti tarafından yönetilen dosya paylaşımlarına erişim için Azure AD kimlik bilgileriyle kimlik doğrulamasını desteklemez.
    
## <a name="security-authentication-and-access-control"></a>Güvenlik, kimlik doğrulama ve erişim denetimi
* <a id="ad-support"></a>
**Azure dosyaları tarafından desteklenen kimlik tabanlı kimlik doğrulaması ve erişim denetimi mi?**  
    
    Evet, Azure dosyaları Azure AD etki alanı hizmeti (Azure AD DS) ile kimlik tabanlı kimlik doğrulaması ve erişim denetimini destekler. Azure dosyaları için SMB üzerinden Azure AD DS kimlik doğrulaması, Azure AD DS etki alanına katılmış Windows VM 'lerinin Azure AD kimlik bilgilerini kullanarak paylaşımlara, dizinlere ve dosyalara erişmesine olanak sağlar. Daha fazla ayrıntı için bkz. [SMB erişimi Için Azure dosyalarına Azure Active Directory etki alanı hizmeti (azure AD DS) kimlik doğrulama desteği 'Ne genel bakış](storage-files-active-directory-overview.md). 

    Azure dosyaları, erişim denetimini yönetmek için iki ek yol sunar:

    - Belirli izinlere sahip olan ve belirli bir zaman aralığı için geçerli olan belirteçler oluşturmak için paylaşılan erişim imzaları (SAS) kullanabilirsiniz. Örneğin, 10 dakikalık süre sonu olan belirli bir dosyaya salt okuma erişimi olan bir belirteç oluşturabilirsiniz. Belirteç geçerli olduğunda belirtece sahip olan herkes bu 10 dakika boyunca bu dosyaya salt okuma erişimi sağlar. Paylaşılan erişim imza anahtarları yalnızca REST API veya istemci kitaplıkları aracılığıyla desteklenir. Depolama hesabı anahtarlarını kullanarak Azure dosya paylaşımından SMB üzerinden bağlamanız gerekir.

    - Azure Dosya Eşitleme tüm isteğe bağlı ACL 'Leri veya DACL 'Leri (Active Directory tabanlı veya yerel), eşitlediği tüm sunucu uç noktalarına korur ve çoğaltır. Windows Server Active Directory önceden kimlik doğrulaması yapabildiğinden, Active Directory tabanlı kimlik doğrulaması ve ACL desteği için tam destek alınana kadar Azure Dosya Eşitleme etkin bir durma-boşluk seçeneğidir.
    
    Azure Storage hizmetlerinde desteklenen tüm protokollerin kapsamlı bir gösterimi için [Azure depolama 'ya erişimi yetkilendirme](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) konusuna başvurabilirsiniz. 

* <a id="ad-support-devices"></a>Azure **dosyaları 
azure AD DS kimlik doğrulaması, Azure AD kimlik bilgilerini kullanarak Azure AD kimlik bilgilerini kullanarak SMB erişimini destekliyor mu?**

    Hayır, bu senaryo desteklenmiyor.

* <a id="ad-support-rest-apis"></a>
 **, dizin/dosya NTFS ACL 'Lerini almayı/ayarlamayı/kopyalamayı desteklemeye YÖNELIK REST API 'ler var mı?**

    Şimdilik, dizinler veya dosyalar için NTFS ACL 'Lerini almak, ayarlamak veya kopyalamak üzere REST API 'Lerini desteklemiyoruz.

* <a id="ad-vm-subscription"></a>
**farklı bir abonelik kapsamındaki BIR VM 'den Azure AD kimlik bilgileriyle Azure dosyalarına erişebilir miyim?**

    Dosya paylaşımının dağıtıldığı abonelik, VM 'nin etki alanına katılmış olduğu Azure AD Domain Services dağıtımıyla aynı Azure AD kiracısıyla ilişkili ise, Azure dosyalarına aynı Azure AD kimlik bilgilerini kullanarak erişebilirsiniz. Kısıtlama, abonelikte değil, ilişkili Azure AD kiracısında yer alır.    
    
* <a id="ad-support-subscription"></a>
 **, Azure dosyaları azure AD DS kimlik doğrulamasını, dosya paylaşımının ilişkilendirildiği birincil kiracıdan farklı bir Azure AD kiracısı ile etkinleştirebilir miyim?**

    Hayır, Azure dosyaları yalnızca dosya paylaşımıyla aynı abonelikte bulunan bir Azure AD kiracısı ile Azure AD DS tümleştirmesini destekler. Bir Azure AD kiracısıyla yalnızca bir abonelik ilişkilendirilebilir.

* <a id="ad-linux-vms"></a>**Azure dosyaları 
azure AD DS kimlik doğrulaması, Linux VM 'lerini destekliyor mu?**

    Hayır, Linux VM 'lerden kimlik doğrulaması desteklenmez.

* <a id="ad-aad-smb-afs"></a>
**Azure dosya eşitleme tarafından yönetilen dosya paylaşımlarında Azure dosyaları azure AD DS kimlik doğrulamasından yararlanabilir miyim?**

    Hayır, Azure dosyaları Azure Dosya Eşitleme tarafından yönetilen dosya paylaşımlarında NTFS ACL 'Lerinin korumayı desteklemez. Şirket içi dosya sunucularından taşınan dosya ACL 'Leri Azure Dosya Eşitleme tarafından kalıcı hale getirilir. Azure dosyalarına yerel olarak yapılandırılmış tüm NTFS ACL 'Leri Azure Dosya Eşitleme hizmeti tarafından üzerine yazılır. Ayrıca, Azure dosyaları Azure Dosya Eşitleme hizmeti tarafından yönetilen dosya paylaşımlarına erişim için Azure AD kimlik bilgileriyle kimlik doğrulamasını desteklemez.

* <a id="encryption-at-rest"></a>**Azure dosya paylaşımımın bekleyen bir şekilde şifrelendiğinden nasıl emin olabilirim 
?**  

    Evet. Daha fazla bilgi için bkz. [Azure depolama hizmeti şifrelemesi](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>**bir Web tarayıcısı kullanarak belirli bir dosyaya nasıl erişim sağlayabilirim 
?**  

    Belirli izinlere sahip olan ve belirli bir zaman aralığı için geçerli olan belirteçler oluşturmak için paylaşılan erişim imzaları ' nı kullanabilirsiniz. Örneğin, belirli bir dosyaya salt okuma erişimi veren bir belirteç oluşturabilirsiniz. Belirteç geçerli olsa da, URL 'ye sahip olan herkes dosyaya doğrudan herhangi bir Web tarayıcısından erişebilir. Depolama Gezgini gibi bir kullanıcı arabiriminden kolayca paylaşılan erişim imza anahtarı oluşturabilirsiniz.

* <a id="file-level-permissions"></a>
**Paylaşımdaki klasörlerde salt okunurdur veya salt yazılır izinleri belirtmek mümkün mü?**  

    Dosya paylaşımından SMB kullanarak bağlarsanız, izinler üzerinde klasör düzeyinde denetiminiz yoktur. Ancak, REST API veya istemci kitaplıklarını kullanarak bir paylaşılan erişim imzası oluşturursanız, paylaşım içindeki klasörler üzerinde salt okunurdur veya salt yazılır izinler belirleyebilirsiniz.

* <a id="ip-restrictions"></a>
 **, bir Azure dosya PAYLAŞıMıNıN IP kısıtlamalarını uygulayabilir miyim?**  

    Evet. Azure dosya paylaşımınıza erişim, depolama hesabı düzeyinde kısıtlanabilir. Daha fazla bilgi için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>**Azure dosyaları hangi veri uyumluluk ilkelerini destekler 
?**  

   Azure dosyaları, Azure depolama 'daki diğer depolama hizmetlerinde kullanılan depolama mimarisinin üstünde çalışır. Azure dosyaları, diğer Azure Storage hizmetlerinde kullanılan aynı veri uyumluluk ilkelerini uygular. Azure depolama veri uyumluluğu hakkında daha fazla bilgi için [Azure depolama uyumluluk teklifleri](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)' ne başvurabilirsiniz ve [Microsoft Güven Merkezi](https://microsoft.com/trustcenter/default.aspx)' ne gidebilirsiniz.

## <a name="on-premises-access"></a>Şirket içi erişim

* <a id="port-445-blocked"></a>ISS mi 
 **, Azure dosyaları bağlama başarısız olan bağlantı noktası 445 ' i engelliyor. Ne yapmam gerekir?**

    [Geçici çözüm 445 bağlantı noktası ' i engelleyen çeşitli yollar](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked)hakkında bilgi edinebilirsiniz. Azure dosyaları, yalnızca bölge veya veri merkezi dışından SMB 3,0 (şifreleme desteği ile) kullanan bağlantılara izin verir. SMB 3,0 protokolü, internet üzerinden kullanılması çok güvenli olan kanal şifreleme dahil pek çok güvenlik özelliği sunmuştur. Ancak, daha düşük SMB sürümlerinde bulunan güvenlik açıklarının geçmiş nedenlerinden dolayı 445 numaralı bağlantı noktası engellenmiş olabilir. İdeal durumda, bağlantı noktasının yalnızca SMB 1,0 trafiği için engellenmesi ve SMB 1,0 ' nin tüm istemcilerde kapalı olması gerekir.

* <a id="expressroute-not-required"></a>
Azure **dosyaları 'na bağlanmak veya şirket içi Azure dosya eşitleme kullanmak Için Azure ExpressRoute 'u kullanmak zorunda mıyım?**  

    Hayır. ExpressRoute 'un bir Azure dosya paylaşımının erişimine yönelik olması gerekmez. Doğrudan şirket içi bir Azure dosya paylaşımından bağlantı oluşturuyorsanız, tüm bu gerekli olan bağlantı noktası 445 (TCP Giden) internet erişimi için açık olmalıdır (Bu, SMB 'nin iletişim kurmak için kullandığı bağlantı noktasıdır). Azure Dosya Eşitleme kullanıyorsanız, tüm gerekli olan HTTPS erişimi için bağlantı noktası 443 (TCP Giden) ' dir (SMB gerekmez). Ancak, ExpressRoute 'u bu erişim seçeneklerinden *biriyle kullanabilirsiniz.*

* <a id="mount-locally"></a>**Azure dosya paylaşımından yerel makineme nasıl bağlayabilirim 
?**  

    Bağlantı noktası 445 (TCP Giden) açıksa ve istemciniz SMB 3,0 protokolünü destekliyorsa (örneğin, Windows 10 veya Windows Server 2016 kullanıyorsanız), SMB protokolünü kullanarak dosya paylaşımından bağlama yapabilirsiniz. Bağlantı noktası 445, kuruluşunuzun ilkesi veya ISS 'niz tarafından engellenmişse Azure dosya paylaşımınıza erişmek için Azure Dosya Eşitleme kullanabilirsiniz.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**nasıl yaparım? Azure dosya paylaşımımı yedekle?**  
    Yanlışlıkla silinmelere karşı koruma için düzenli aralıklarla [paylaşma anlık görüntülerini](storage-snapshots-files.md) kullanabilirsiniz. Ayrıca, bağlı bir dosya paylaşımının yedekleyebilen AzCopy, Robocopy veya bir üçüncü taraf yedekleme aracını da kullanabilirsiniz. Azure Backup Azure dosyalarının yedeklemesini sunmaktadır. [Azure Backup göre Azure dosya paylaşımlarını yedekleme](https://docs.microsoft.com/azure/backup/backup-azure-files)hakkında daha fazla bilgi edinin.

## <a name="share-snapshots"></a>Anlık görüntü paylaşma

### <a name="share-snapshots-general"></a>Anlık görüntü paylaşma: genel
* <a id="what-are-snaphots"></a>**dosya paylaşma anlık görüntüleri ne 
?**  
    Azure dosya paylaşımı anlık görüntülerini, dosya paylaşımlarınızın salt okunurdur bir sürümünü oluşturmak için kullanabilirsiniz. Ayrıca, Azure dosyalarını, içeriğinizin önceki bir sürümünü yeniden aynı paylaşıma, Azure 'da alternatif bir konuma veya daha fazla değişiklik için şirket içi olarak kopyalamak için de kullanabilirsiniz. Anlık görüntü paylaşma hakkında daha fazla bilgi edinmek için bkz. [paylaşma anlık görüntüsüne genel bakış](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>**paylaşma anlık görüntülerimin nerede depolandığı 
?**  
    Paylaşılan anlık görüntüler, dosya paylaşımıyla aynı depolama hesabında depolanır.

* <a id="snapshot-consistency"></a>
**anlık görüntü paylaşma uygulaması tutarlı mi?**  
    Hayır, paylaşılan anlık görüntü, uygulamayla tutarlı değil. Kullanıcının, paylaşma anlık görüntüsünü almadan önce uygulamadan yazma işlemlerini paylaşıma temizlemesi gerekir.

* <a id="snapshot-limits"></a>
, **kullanabilirim paylaşılan anlık görüntü sayısı için sınırlamalar var mı?**  
    Evet. Azure dosyaları, en fazla 200 paylaşma anlık görüntüsünü koruyabilir. Paylaşma anlık görüntüleri, paylaşma kotasına doğru sayılmaz, bu nedenle tüm paylaşılan anlık görüntüler tarafından kullanılan toplam alanda paylaşma başına bir sınır yoktur. Depolama hesabı sınırları hala geçerlidir. 200 anlık görüntüleri paylaştıktan sonra yeni paylaşılan anlık görüntüler oluşturmak için eski anlık görüntüleri silmeniz gerekir.

* <a id="snapshot-cost"></a>**anlık görüntü paylaşma maliyeti ne kadar 
?**  
    Standart işlem ve standart depolama maliyeti, anlık görüntü için geçerlidir. Anlık görüntüler doğası halinde artımsal. Temel anlık görüntü paylaşımın kendisidir. Sonraki tüm anlık görüntüler artımlı olur ve yalnızca önceki anlık görüntüdeki farkı depolar. Bu, iş yükü karmaşıklığının en az olması halinde faturanızda görülen Delta değişikliklerinin en az olacağı anlamına gelir. Standart Azure dosyaları fiyatlandırma bilgileri için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/storage/files/) . Bugün, paylaşılan anlık görüntü tarafından tüketilen boyutu görüntüleme yolu, faturalanan kapasiteyi kullanılan kapasiteyle karşılaştırmaktır. Raporlamayı geliştirmek için araç üzerinde çalışıyoruz.

* <a id="ntfs-acls-snaphsots"></a>
 **, dizin ve DOSYALARDAKI NTFS ACL 'lerinde paylaşılan anlık görüntülerle kalıcı mi?**  
    Dizinler ve dosyalardaki NTFS ACL 'Leri, paylaşılan anlık görüntülerle kalıcı hale getirilir.

### <a name="create-share-snapshots"></a>Paylaşma anlık görüntüleri oluşturma
* <a id="file-snaphsots"></a>
, **tek tek dosyaların paylaşma anlık görüntüsünü oluşturabilir miyim?**  
    Paylaşma anlık görüntüleri dosya paylaşma düzeyinde oluşturulur. Dosya paylaşımının anlık görüntüsünden tek tek dosyaları geri yükleyebilirsiniz, ancak dosya düzeyi paylaşılan anlık görüntüler oluşturamazsınız. Ancak, paylaşma düzeyi bir paylaşılan anlık görüntü aldıysanız ve belirli bir dosyanın değiştiği paylaşılan anlık görüntüleri listelemek istiyorsanız, bunu Windows bağlı bir paylaşımdaki **önceki sürümler** altında yapabilirsiniz. 
    
    Bir dosya anlık görüntüsü özelliği gerekiyorsa, [Azure dosyaları UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)' da bize bilgi verin.

* <a id="encrypted-snapshots"></a>
**şifreli bir dosya paylaşımının paylaşılan anlık görüntülerini oluşturabilir miyim?**  
    Bekleyen bir şifreleme özelliği olan Azure dosya paylaşımlarının paylaşım anlık görüntüsünü alabilirsiniz. Dosyaları bir paylaşma anlık görüntüsünden şifreli bir dosya paylaşımında geri yükleyebilirsiniz. Paylaşımınız şifrelendiyse, paylaşma anlık görüntüsü de şifrelenir.

* <a id="geo-redundant-snaphsots"></a>**paylaşma anlık görüntülerimin coğrafi olarak yedekli mi 
?**  
    Paylaşılan anlık görüntüler, alındığı Azure dosya paylaşımıyla aynı yedekliliğe sahiptir. Hesabınız için coğrafi olarak yedekli depolamayı seçtiyseniz, paylaşma anlık görüntüsü de eşleştirilmiş bölgede Redundantly depolanır.

### <a name="manage-share-snapshots"></a>Paylaşma anlık görüntülerini yönetme
* <a id="browse-snapshots-linux"></a>
 **, Linux 'tan paylaşma anlık görüntülerimi tarayabilirim miyim?**  
    Linux 'ta paylaşma anlık görüntülerini oluşturmak, listelemek, taramak ve geri yüklemek için Azure CLı kullanabilirsiniz.

* <a id="copy-snapshots-to-other-storage-account"></a>
**paylaşma anlık görüntülerini farklı bir depolama hesabına kopyalayabilir miyim?**  
    Dosyaları Share Snapshot dosyalarından başka bir konuma kopyalayabilirsiniz, ancak paylaşımın anlık görüntülerini kendi kendilerine kopyalayamazsınız.

### <a name="restore-data-from-share-snapshots"></a>Paylaşma anlık görüntülerinden verileri geri yükleme
* <a id="promote-share-snapshot"></a>
 **, bir Share anlık görüntüsünü temel paylaşıma yükseltebilir miyim?**  
    Bir Share anlık görüntüsünden diğer hedeflere veri kopyalayabilirsiniz. Bir Share anlık görüntüsünü temel paylaşıma yükseltemezsiniz.

* <a id="restore-snapshotted-file-to-other-share"></a>
 **, paylaşma anlık görüntüsünden farklı bir depolama hesabına veri geri yükleyebilir miyim?**  
    Evet. Bir paylaşma anlık görüntüsüne ait dosyalar özgün konuma veya aynı veya farklı bölgelerde aynı depolama hesabını ya da farklı bir depolama hesabını içeren alternatif bir konuma kopyalanabilir. Ayrıca, dosyaları şirket içi bir konuma veya diğer bir buluta kopyalayabilirsiniz.    
  
### <a name="clean-up-share-snapshots"></a>Paylaşma anlık görüntülerini temizle
* <a id="delete-share-keep-snapshots"></a>
**paylaşımımı silebilir, ancak paylaşma anlık görüntülerimi silmeyebilir miyim?**  
    Paylaşımınızda etkin bir paylaşılan anlık görüntü varsa, paylaşımınızı silemezsiniz. Paylaşma ile birlikte paylaşılan anlık görüntüleri silmek için bir API kullanabilirsiniz. Ayrıca, Azure portal hem paylaşma anlık görüntülerini hem de paylaşmayı silebilirsiniz.

* <a id="delete-share-with-snapshots"></a>**depolama hesabımı silersem paylaşma anlık görüntülerime ne olacağını 
?**  
    Depolama hesabınızı silerseniz, paylaşma anlık görüntüleri de silinir.

## <a name="billing-and-pricing"></a>Faturalandırma ve fiyatlandırma
* <a id="vm-file-share-network-traffic"></a>
**bir Azure VM Ile Azure dosya paylaşma arasındaki ağ trafiği, aboneliğe ücretlendirilen harici bir bant genişliği olarak mı sayılır?**  
    Dosya paylaşma ve VM aynı Azure bölgedeyse, dosya paylaşımıyla VM arasındaki trafik için ek ücret alınmaz. Dosya paylaşma ve VM farklı bölgelerde ise, aralarındaki trafik dış bant genişliği olarak ücretlendirilir.

* <a id="share-snapshot-price"></a>**anlık görüntü paylaşma maliyeti ne kadar 
?**  
     Önizleme sırasında, paylaşılan anlık görüntü kapasitesi ücretsizdir. Standart depolama çıkış ve işlem maliyetleri geçerlidir. Genel kullanılabilirlik sonrasında, abonelikler, paylaşılan anlık görüntülerle kapasite ve işlemler için ücretlendirilecektir.
     
     Paylaşılan anlık görüntü, doğası halinde artımsal. Temel paylaşma anlık görüntüsü paylaşımın kendisidir. Sonraki tüm paylaşılan anlık görüntüler artımlı yapılır ve yalnızca önceki paylaşılan anlık görüntüden farkı depolar. Yalnızca değiştirilen içerik için faturalandırılırsınız. 100 GiB veri içeren bir paylaşımınız varsa ancak son paylaşma anlık görüntüsünden bu yana yalnızca 5 GiB değişmişse, paylaşma anlık görüntüsü yalnızca 5 ek GiB kullanır ve 105 GiB için faturalandırılırsınız. İşlem ve standart çıkış ücretleri hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/storage/files/)bakın.

## <a name="scale-and-performance"></a>Ölçek ve performans
* <a id="files-scale-limits"></a>**Azure dosyalarının ölçek sınırları nelerdir 
?**  
    Azure dosyaları için ölçeklenebilirlik ve performans hedefleri hakkında daha fazla bilgi için bkz. [Azure dosyaları ölçeklenebilirlik ve performans hedefleri](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>**Azure dosya paylaşımları Için hangi boyutların kullanılabildiği 
?**  
    Azure dosya paylaşma boyutları (Premium ve standart) 100 TiB 'ye kadar ölçeklenebilir. Standart katmana yönelik daha büyük dosya paylaşımlarına yönelik yönergeler için planlama kılavuzunun [daha büyük dosya paylaşımlarına (Standart katman)](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) ekleme bölümüne bakın.

* <a id="lfs-performance-impact"></a>
**dosya paylaşma kotamı genişleterek iş yüklerimi veya Azure dosya eşitleme mi etkiler?**
    
    Hayır. Kotayı genişletmek, iş yüklerinizi veya Azure Dosya Eşitleme etkilemez.

* <a id="open-handles-quota"></a>**aynı dosyaya aynı anda kaç istemcinin erişebileceğini** 
   
    Tek bir dosyada 2.000 açık tanıtıcı kotası vardır. 2\.000 açık tutamacı varsa, kotanın ulaşılmadığını belirten bir hata iletisi görüntülenir.

* <a id="zip-slow-performance"></a>**Azure dosyalarındaki dosyaları unzip diğimde Performanmın yavaş 
. Ne yapmam gerekir?**  
    Azure dosyalarına çok sayıda dosya aktarmak için AzCopy (Windows için, Linux ve UNIX için Önizleme sürümünde) veya Azure PowerShell kullanmanızı öneririz. Bu araçlar ağ aktarımı için iyileştirildi.

* <a id="slow-perf-windows-81-2012r2"></a>**Azure dosya paylaşımımı Windows Server 2012 R2 veya Windows 8.1 üzerinde taktıktan sonra neden performanmın yavaşladığını 
?**  
    Windows Server 2012 R2 ve Windows 8.1 bir Azure dosya paylaşımının takılacağı bilinen bir sorun vardır. Sorun, Windows 8.1 ve Windows Server 2012 R2 için Nisan 2014 toplu güncelleştirmesinde düzeltme eki eklendi. En iyi performans için, tüm Windows Server 2012 R2 ve Windows 8.1 örneklerinin Bu düzeltme ekinin uygulandığından emin olun. (Windows Update aracılığıyla her zaman Windows düzeltme eklerini almalısınız.) Daha fazla bilgi için, [Windows 8.1 veya Server 2012 R2 'Den Azure dosyalarına eriştiğinizde](https://support.microsoft.com/kb/3114025)Ilişkili Microsoft Bilgi Bankası makalesine bakın.

## <a name="features-and-interoperability-with-other-services"></a>Diğer hizmetlerle Özellikler ve birlikte çalışabilirlik
* <a id="cluster-witness"></a>
 **, Azure dosya paylaşımımı Windows Server yük devretme Kümemin *dosya paylaşma tanığı* olarak kullanabilir miyim?**  
    Şu anda bu yapılandırma bir Azure dosya paylaşımında desteklenmez. Bunu Azure Blob depolama için ayarlama hakkında daha fazla bilgi için bkz. [Yük devretme kümesi Için bulut tanığı dağıtma](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
Azure **Container Instance üzerinde bir Azure dosya paylaşımından bağlanabilir miyim?**  
    Evet, Azure dosya paylaşımları, bir kapsayıcı örneğinin kullanım ömrünün ötesinde bilgileri kalıcı hale getirmek istediğinizde iyi bir seçenektir. Daha fazla bilgi için bkz. Azure [Container Instances Ile Azure dosya paylaşma bağlama](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**REST API yeniden adlandırma işlemi var mı?**  
    Şu anda değil.

* <a id="nested-shares"></a>
**iç içe paylaşımlar ayarlayabilir miyim? Diğer bir deyişle, bir paylaşıma ait mi?**  
    Hayır. Dosya paylaşımı, takabilmeniz gereken sanal bir sürücü *olduğundan* iç içe paylaşımlar desteklenmez.

* <a id="ibm-mq"></a>
**nasıl yaparım? Azure dosyaları IBM MQ ile kullanılsın mı?**  
    IBM, IBM MQ müşterilerinin Azure dosyalarını IBM hizmetiyle yapılandırmalarına yardımcı olan bir belge yayımlamıştır. Daha fazla bilgi için bkz. [Microsoft Azure dosyaları hizmeti Ile IBM MQ çok örnekli kuyruk yöneticisi ayarlama](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Ayrıca bkz.
* [Windows 'da Azure dosyaları sorunlarını giderme](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux 'ta Azure dosyaları sorunlarını giderme](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure Dosya Eşitleme ile ilgili sorunları giderme](storage-sync-files-troubleshoot.md)
