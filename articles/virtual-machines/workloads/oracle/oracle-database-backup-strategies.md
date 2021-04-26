---
title: Azure sanal makineler yedekleme stratejilerinde Oracle Database
description: Oracle veritabanlarını bir Azure Linux VM ortamında yedekleme seçenekleri.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 8a1eb1c21663e0294cd384daa0ba644adf78007a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673221"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Azure Linux VM yedekleme stratejilerinde Oracle Database

Veritabanı yedeklemeleri, depolama bileşeni hatası ve veri merkezi başarısızlığı nedeniyle veritabanını veri kaybına karşı korur. Ayrıca, insan hatasından kurtarma ve bir veritabanını geliştirme veya test amacıyla klonlamak için bir yol da olabilirler. 

Azure 'da, tüm depolama alanı yüksek düzeyde yedekli olduğundan ve bir veya daha fazla diskin kaybolması bir veritabanı kesintisine yol açacağından, yedeklemeler en sık insan hatasına karşı korumak için, kopyalama işlemleri veya yasal düzenlemeler için veri koruma için kullanılır. Ayrıca, DataGuard gibi bir olağanüstü durum kurtarma teknolojisinin kullanımda olmadığı bölgesel kesintiye karşı korumaya yönelik bir araçtır. Bu durumda, yedeklemelerin, birincil veritabanı bölgesinin dışında kullanılabilir olması için coğrafi olarak yedekli çoğaltma kullanılarak farklı Azure bölgelerinde depolanması gerekir.

## <a name="azure-storage"></a>Azure Depolama 

[Azure depolama hizmetleri](../../../storage/common/storage-introduction.md) , Microsoft 'un modern veri depolama senaryolarına yönelik bulut depolama çözümüdür. Azure depolama, Azure Linux sanal makinesine dış depolama bağlamak için kullanılabilen ve Oracle veritabanları için yedekleme medyası olarak uygun olan çeşitli hizmetler sunar. Bir yedekleme veya geri yükleme işlemi başlatmak ve yedeklemeyi Azure Storage hizmetine kopyalamak için Oracle RMAN gibi bir yedekleme aracı gereklidir.
 
Azure depolama hizmetleri aşağıdaki avantajları sunar:

-  Dayanıklı ve yüksek oranda kullanılabilir. Yedeklilik, verilerinizin geçici donanım hataları durumunda güvende kalmasını sağlar. Tüm depolama, varsayılan olarak üç yansıtmayla yapılır. Ayrıca, yerel felaketler veya doğal olağanüstü durumdan daha fazla koruma için veri merkezleri veya coğrafi bölgeler arasında veri çoğaltmayı tercih edebilirsiniz. Bu şekilde çoğaltılan veriler beklenmeyen bir kesinti durumunda yüksek oranda kullanılabilir olmaya devam eder.

-  Güvende. Bir Azure depolama hesabına yazılan tüm veriler hizmet tarafından şifrelenir. Azure Depolama, verilerinize erişmesi gereken kişiler üzerinde ayrıntılı denetime sahip olmanızı sağlar.

-  Ölçeklenebilirliği. Azure Depolama, günümüzün uygulamalarına ait veri depolama ve performans gereksinimlerini karşılamak için yüksek düzeyde ölçeklenebilir şekilde tasarlanmıştır.

-  Lebilmesi. Azure, donanım bakımını, güncelleştirmeleri ve kritik sorunları sizin için işler.

-  Erişilebilir. Azure Depolama’daki verilere dünyanın herhangi bir yerinden HTTP ya da HTTPS aracılığıyla erişilebilir. Microsoft, Azure depolama için, .NET, Java, Node.js, Python, PHP, Ruby, Go ve diğerleri gibi çeşitli dillerde istemci kitaplıkları sağlar ve bu da yetişkinlere yönelik REST API. Azure Depolama, Azure PowerShell veya Azure CLI’de betik oluşturmayı destekler. Azure portalı ve Azure Depolama Gezgini ise verilerinizle çalışmaya yönelik kolay görsel çözümler sunar.

Azure depolama platformu, Oracle veritabanı için yedekleme medyası olarak kullanılmak üzere uygun olan aşağıdaki veri hizmetlerini içerir:

-  Azure Blobları: Metin ve ikili veriler için yüksek düzeyde ölçeklenebilir nesne deposu. Ayrıca Data Lake Storage 2. aracılığıyla büyük veri analizi desteğini de içerir.

-  Azure dosyaları: bulut veya şirket içi dağıtımlar için yönetilen dosya paylaşımları.

-  Azure diskleri: Azure VM 'Ler için blok düzeyinde depolama birimleri.

### <a name="cross-regional-storage-mounting"></a>Çapraz bölgesel depolamaya bağlama

Yedekleme depolamasına bölgeler arasında erişme özelliği, BCDR 'nin önemli bir yönüdür ve veritabanlarını yedeklerden farklı coğrafi bölgelere kopyalamak için yararlıdır. Azure bulut depolama, beş [Artıklık](../../../storage/common/storage-redundancy.md) düzeyi sağlar
- Verilerinizin birincil bölgedeki tek bir fiziksel konumda üç kez çoğaltılacağı yerel olarak yedekli depolama [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) .  
- Verilerinizin birincil bölgedeki LRS tarafından korunduğu ve birincil bölgedeki üç kullanılabilirlik bölgesinde (AZ) zaman uyumlu olarak çoğaltılan bölge yedekli depolama [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) . Her biri AZ LRS korumalıdır. 
- Verilerinizin birincil bölgedeki LRS tarafından korunduğu ve zaman uyumsuz olarak LRS tarafından korunduğu bir ikincil bölgeye çoğaltılan Geo-Redundant depolama [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) .
- Coğrafi bölge yedekli depolama [(GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) , ZRS kullanarak birincil bölgedeki üç Azure kullanılabilirlik bölgesi arasında verilerinizi eşzamanlı olarak kopyalar. Ardından verilerinizi zaman uyumsuz şekilde ikincil bölgedeki tek bir fiziksel konuma kopyalar. Tüm konumlarda veriler LRS tarafından da korunmaktadır. 
- Okuma erişimi Geo-Redundant depolama [(RA-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) ve okuma erişimi coğrafi bölge yedekli depolama [(ra-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) , ikincil bölgeye her zaman çoğaltılan verilerin salt okuma erişimine izin verir. 

#### <a name="blob-storage-and-file-storage"></a>BLOB depolama ve dosya depolama

Yedekleme deposu olarak takılacak SMB veya NFS 4,1 (Önizleme) protokolleriyle Azure dosyalarını kullanırken, Azure dosyalarının Okuma Erişimli Coğrafi olarak yedekli depolamayı (RA-GRS) ve Okuma Erişimli Coğrafi bölge-yedekli depolamayı (RA-GZRS) desteklemediğini unutmayın. 

Ayrıca, yedekleme depolama gereksinimi 5 ' ten büyükse Azure dosyalarından, GRS veya GZRS artıklığı desteklemeyen, [büyük dosya paylaşımları özelliği](../../../storage/files/storage-files-planning.md) etkinleştirilmiş olmalıdır-yalnızca LRS desteklenir. 

NFS 3,0 (Önizleme) protokolü kullanılarak takılan Azure Blob Şu anda yalnızca LRS ve ZRS yedekliliği desteklemektedir.  

Herhangi bir artıklık seçeneği ile yapılandırılmış Azure blob, Blobsigortası kullanılarak bağlanabilir.

#### <a name="recovery-services-vault"></a>Kurtarma Hizmetleri kasası 

Kurtarma Hizmeti kasası, zaman içinde oluşturulan kurtarma noktalarını depolayan ve yedekleme ile ilgili işlemleri gerçekleştirmek için bir arabirim sağlayan yönetim varlığıdır. Bu işlemler arasında isteğe bağlı yedekleme, geri yükleme gerçekleştirme ve yedekleme ilkesi oluşturma sayılabilir.
Azure Backup kasa için depolamayı otomatik olarak işler. Bu depolamanın oluşturma sırasında nasıl çoğaltılacağı belirtmeniz gerekir. Öğeler kasada korunduktan sonra değiştirilemez. Bölgesel artıklık için, coğrafi olarak yedekli ayarı seçin.

İkincil bir [Azure eşleştirilmiş bölge](../../../best-practices-availability-paired-regions.md) , [çapraz bölge geri yükleme](../../../backup/backup-create-rs-vault.md) özelliğini etkinleştirin. Çapraz bölge geri yükleme etkinleştirildiğinde, yedekleme depolama alanı GRS 'den Okuma Erişimli Coğrafi olarak yedekli depolamaya (RA-GRS) taşınır. 

### <a name="azure-blob-storage"></a>Azure Blob Depolama

Azure Blob depolama, büyük miktarlarda yapılandırılmamış verileri depolamak için kullanılan, yüksek maliyetli etkili, dayanıklı ve güvenli bulut tabanlı bir depolama hizmetidir ve Oracle Database yedeklemeleri için kullanılacak ideal bir ortamdır. Azure Blob depolama, NFS v 3.0 protokol veya Blobsigortası (Linux SIGORTASı) kullanılarak Azure Linux VM 'ye bağlanabilir.

#### <a name="azure-blob-blobfuse"></a>Azure Blob Blobsigortası

[Blobsigortası](../../../storage/blobs/storage-how-to-mount-container-linux.md) , Azure Blob depolama alanı tarafından desteklenen bir sanal dosya sistemi sağlamak için geliştirilen açık kaynaklı bir projem. Linux SIGORTASı çekirdek modülüyle iletişim kurmak için libsigortası açık kaynaklı kitaplığı kullanır ve Azure Depolama Blobu REST API 'Lerini kullanarak dosya sistemi işlemlerini uygular. Blobsigortası Şu anda Ubuntu ve CentOS/RedHat dağıtımları için kullanılabilir. Ayrıca, [CSI sürücüsü](https://github.com/kubernetes-sigs/blob-csi-driver)kullanılarak Kubernetes için de kullanılabilir. 

Tüm Azure bölgelerinde ubietmekte ve genel amaçlı v1/v2 ve Azure Data Lake Store Gen2 dahil olmak üzere tüm depolama hesabı türleriyle birlikte çalışarak, Blobsigortası tarafından sunulan performans SMB veya NFS gibi alternatif protokollerden daha az olacak şekilde gösterilmiştir. Veritabanı yedekleme medyası olarak uygunluk için, Azure Blob depolamayı bağlamak üzere SMB veya [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) protokollerini kullanmanızı öneririz. 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob NFS v 3.0 (Önizleme)

Ağ dosya sistemi (NFS) v 3.0 protokolü için Azure desteği artık önizlemededir. [NFS](../../../storage/blobs/network-file-system-protocol-support.md) desteği, Windows ve Linux Istemcilerinin BIR Azure VM 'ye bir BLOB depolama kapsayıcısı takmalarını sağlar. 

NFS 3,0 genel önizlemesi, aşağıdaki bölgelerde Standart katman performansına sahip GPV2 depolama hesaplarını destekler: 
- Doğu Avustralya
- Güney Kore - Orta
- Orta Güney ABD. 

Ağ güvenliğini sağlamak için, NFS bağlama için kullanılan depolama hesabının bir sanal ağ içinde yer almalıdır. Azure Active Directory (AD) güvenlik ve erişim denetimi listeleri (ACL 'Ler), NFS 3,0 protokol desteği etkinleştirilmiş hesaplarda henüz desteklenmez.

### <a name="azure-files"></a>Azure Dosyaları

[Azure dosyaları](../../../storage/files/storage-files-introduction.md) , şirket içi veya bulut tabanlı Windows, Linux veya MacOS istemcilerine bağlanabilir bulut tabanlı, tam olarak yönetilen bir dağıtılmış dosya sistemidir.

Azure dosyaları, bulutta, sunucu Ileti bloğu (SMB) protokolü ve ağ dosya sistemi (NFS) Protokolü (Önizleme) aracılığıyla erişilebilen, tam olarak yönetilen platformlar arası dosya paylaşımları sunar. Azure dosyaları şu anda çoklu protokol erişimini desteklemez, bu nedenle bir paylaşımın yalnızca bir NFS paylaşımında veya bir SMB paylaşımında olabilir. Azure dosya paylaşımları oluşturmadan önce gereksinimlerinize en uygun protokolü belirlemeyi öneririz.

Azure dosya paylaşımları, kurtarma hizmetleri Kasası 'na Azure Backup aracılığıyla da korunabilir ve bu da Oracle RMAN yedeklemelerine ek bir koruma katmanı sağlar.

#### <a name="azure-files-nfs-v41-preview"></a>Azure dosyaları NFS v 4.1 (Önizleme)

Azure dosya paylaşımları, ağ dosya sistemi (NFS) v 4.1 protokolü kullanılarak Linux dağıtımlarına bağlanabilir. Önizlemede, desteklenen özelliklere yönelik bir dizi sınırlama vardır. bu özellikler [burada](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)belgelenmiştir. 

Azure dosyaları önizlemede, NFS v 4.1 de aşağıdaki [bölgelerle](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)kısıtlıdır:
- Doğu ABD (LRS ve ZRS)
- Doğu ABD 2
- Batı ABD 2
- West Europe
- Güneydoğu Asya
- Güney Birleşik Krallık
- Doğu Avustralya

#### <a name="azure-files-smb-30"></a>Azure dosyaları SMB 3,0

Azure dosya paylaşımları, SMB çekirdek istemcisi kullanılarak Linux dağıtımları ile bağlanabilir. Linux dağıtımlarında bulunan ortak Internet dosya sistemi (CIFS) protokolü, SMB 'nin bir diyalekti. Linux VM 'lerinde Azure dosyaları SMB 'yi takarken, bu, CIFS türü dosya sistemi olarak bağlanır ve CIFS paketinin yüklenmesi gerekir. 

Azure dosyaları SMB, tüm Azure bölgelerinde genel kullanıma sunulmuştur ve NFS v 3.0 ve v 4.1 protokolleriyle aynı performans özelliklerini gösterir ve bu nedenle, Azure Linux VM 'lerine yedekleme depolama medyası sağlamak için önerilen yöntemdir.  

SMB 'nin kullanılabilir iki sürümü vardır, SMB 2,1 ve SMB 3,0, geçiş sırasında şifrelemeyi desteklediğinden, ikincisi önerilir. Ancak, farklı Linux çekirdekler sürümlerinin SMB 2,1 ve 3,0 için farklı desteği vardır ve uygulamanızın SMB 3,0 'i desteklediğinden emin olmak için tabloyu [buraya](../../../storage/files/storage-how-to-use-files-linux.md) denetlemeniz gerekir. 

Azure dosyaları, çok kullanıcılı bir dosya paylaşma hizmeti olacak şekilde tasarlandığından, yedekleme depolama medyası olarak daha uygun hale getirmek için ayarlamanız gereken bazı özellikler vardır. Önbelleğe alma işleminin kapatılması ve oluşturulan dosyalar için Kullanıcı ve grup kimliklerinin ayarlanması önerilir.

## <a name="azure-netapp-files"></a>Azure NetApp Files

[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) hizmeti, Azure VM 'Lerinde Oracle veritabanları için kapsamlı bir depolama çözümüdür. Kurumsal sınıf, yüksek performanslı, ölçülen dosya depolaması üzerine inşa edilen tüm iş yükü türlerini destekler ve varsayılan olarak yüksek oranda kullanılabilir. Oracle doğrudan NFS (dNFS) sürücüsüyle birlikte Azure NetApp Files, Oracle Database için yüksek düzeyde iyileştirilmiş bir depolama katmanı sağlar.

Azure NetApp Files, yazma (satır) mekanizmasına yeniden yönlendirme kullanan temeldeki depolama sisteminde verimli depolama tabanlı anlık görüntü kopyaları sağlar. Anlık görüntü kopyalarının alma ve geri yükleme işlemleri son derece hızlıdır, ancak genellikle insan hatasından kurtarılarak, belirli bir kuruluşun gerekli geri yükleme işlemlerinin büyük çoğunluğuna yönelik olarak yalnızca bir ilk satır erteleçine sahip olur. Ancak, anlık görüntü kopyaları tamamen bir yedekleme değildir. Tüm yedekleme ve geri yükleme gereksinimlerini kapsayan, bölgesel kesintiden korunmak için dış anlık görüntü çoğaltmaları ve/veya diğer yedek kopyaların uzak bir Coğrafya içinde oluşturulması gerekir. Azure 'da Oracle veritabanları için NetApp dosyalarını kullanma hakkında daha fazla bilgi edinmek için bu [raporu](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf)okuyun.

## <a name="azure-backup-service"></a>Azure Backup hizmeti

[Azure Backup,](../../../backup/backup-overview.md) verilerinizi yedeklemek ve Microsoft Azure buluttan kurtarmak için basit, güvenli ve uygun maliyetli çözümler sağlayan, tam olarak yönetilen bir PaaS 'dir. Azure Backup, şirket içi istemcileri, Azure VM 'leri, Azure dosya paylaşımlarını ve Azure VM 'lerinde SQL Server, Oracle, MySQL, PostreSQL ve SAP HANA veritabanlarını yedekleyebilir ve geri yükleyebilir. 

Azure Backup, özgün verilerin yanlışlıkla yok edilmesiyle karşı koruma sağlamak için bağımsız ve yalıtılmış yedeklemeler sağlar. Yedeklemeler, kurtarma noktalarının yerleşik yönetimi ile bir [Kurtarma Hizmetleri kasasında](../../../backup/backup-azure-recovery-services-vault-overview.md) depolanır. Yapılandırma ve ölçeklenebilirlik basittir, yedeklemeler en iyi duruma getirilir ve gerektiğinde kolayca geri yükleyebilirsiniz. Bakım veya izleme ek yükü olmadan yüksek kullanılabilirlik sağlamak için Azure bulutunun temel alınan gücünü ve sınırsız ölçeğini kullanır. Azure Backup, aktardığınız gelen veya giden verilerin miktarını sınırlamaz veya aktarılan veriler için ücretlendirilmez ve veriler aktarım sırasında ve bekleyen zamanda güvenlidir. 

Dayanıklılık sağlamak Azure Backup, yedekleme verilerinizi yüksek oranda kullanılabilir tutmak için birden çok çoğaltma türü sunar.

- Yerel olarak yedekli depolama [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) , verilerinizi üç kez çoğaltır (verilerinizin üç kopyasını oluşturur) veri merkezindeki bir depolama ölçek biriminde.
- Coğrafi olarak yedekli depolama [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) , varsayılan ve önerilen çoğaltma seçeneğidir. GRS, verilerinizi ikincil bir bölgeye (kaynak verilerin birincil konumundan yüzlerce kilometre uzakta) kopyalar.

GRS yedekliliği ile oluşturulan bir kasada, verileri ikincil, Azure eşlenmiş bir bölgede geri yüklemenize olanak sağlayan [çapraz bölge geri yükleme](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) özelliğini yapılandırma seçeneği bulunur.

Azure Backup hizmeti, uygulamayla tutarlı anlık görüntüler olarak adlandırılan Oracle, MySQL, Mongo DB, SAP HANA ve PostGreSQL gibi çeşitli uygulamalar için Windows ve Linux VM 'Leri yedeklemeleri sırasında uygulama tutarlılığı elde etmek için bir [çerçeve](../../../backup/backup-azure-linux-app-consistent.md) sağlar. Bu, disklerin anlık görüntüsünü almadan önce bir ön komut dosyası (uygulamaları sessiz duruma getirmek için) ve anlık görüntü tamamlandıktan sonra, uygulamaları normal moda döndürmek için betik sonrası (uygulamaları çözmek için komutlar) çağırmasını içerir. Örnek ön betikler ve son betikler, GitHub 'da sağlandığında, Bu betiklerin oluşturulması ve bakımı sizin sorumluluğunuzdadır. Oracle söz konusu olduğunda, veritabanının çevrimiçi yedeklemelere izin vermek için Archivelog modunda olması gerekir ve uygun veritabanı başlangıç ve bitiş yedekleme komutları, oluşturmanız ve korumanız gereken, öncesi ve sonrası betiklerde çalıştırılır. 

Azure Backup, şu anda önizleme aşamasında olan [Gelişmiş bir ön betik ve son betik çerçevesi](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts)sağlıyor. burada Azure Backup hizmeti, seçili uygulamalar için paketlenmiş betikler ve son betikler sağlar. Azure Backup kullanıcıların uygulamayı yalnızca adı olması gerekir ve Azure VM yedeklemesi ilgili ön betikleri ve son betikleri otomatik olarak çağırır. Paketlenmiş betikler ve son betikler Azure Backup takım tarafından korunur, böylece kullanıcılar Bu betiklerin destek, sahiplik ve geçerliliğini garanti edebilir. Şu anda, gelişmiş çerçeve için desteklenen uygulamalar, gelecekte daha fazla uygulama türüyle beklenen Oracle ve MySQL ' dir. Anlık görüntü, depolama alanının tam bir kopyası olur ve yazma anlık görüntüsüne artımlı veya kopya değil, veritabanınızı geri yüklemek için etkili bir ortamdır.

## <a name="next-steps"></a>Sonraki adımlar

- [Oracle Database hızlı başlangıç oluştur](oracle-database-quick-create.md)
- [Oracle Database Azure dosyalarına yedekleme](oracle-database-backup-azure-storage.md)
- [Azure Backup hizmeti kullanarak Oracle Database yedekleme](oracle-database-backup-azure-backup.md)


