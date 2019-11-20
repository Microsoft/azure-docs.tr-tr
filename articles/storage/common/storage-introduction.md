---
title: Azure Depolamaya Giriş - Azure’da bulut depolama | Microsoft Docs
description: Azure Depolama, Microsoft’un bulut depolama çözümüdür. Azure Depolama yüksek oranda kullanılabilir, güvenli, sağlam, yüksek düzeyde ölçeklenebilir ve yedekli veri nesneleri için depolama alanı sağlar.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 612da322e0d248f9b0000c8f0006893b87b74e44
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084635"
---
# <a name="introduction-to-azure-storage"></a>Azure Depolama’ya Giriş

Azure Depolama, Microsoft’un modern veri depolama senaryolarına yönelik bulut depolama çözümüdür. Azure Depolama; veri nesneleri için yüksek düzeyde ölçeklenebilir bir nesne deposu, bulut için bir dosya sistemi hizmeti ve güvenilir mesajlaşma için mesajlaşma deposunun yanı sıra bir NoSQL deposu sunar. Azure Depolama şu özelliklere sahiptir:

- **Dayanıklı ve yüksek oranda kullanılabilir.** Yedeklilik, verilerinizin geçici donanım hataları durumunda güvende kalmasını sağlar. Ayrıca, yerel felaketler ya da doğal afetlere karşı ek koruma için verileri meri merkezleri ya da coğrafi bölgeler arasında çoğaltmayı seçebilirsiniz. Bu şekilde çoğaltılan veriler beklenmeyen bir kesinti durumunda yüksek oranda kullanılabilir olmaya devam eder.
- **Güvenlik.** Azure Depolama’ya yazılan tüm veriler hizmet tarafından şifrelenir. Azure Depolama, verilerinize erişmesi gereken kişiler üzerinde ayrıntılı denetime sahip olmanızı sağlar.
- **Ölçeklenebilir.** Azure Depolama, günümüzün uygulamalarına ait veri depolama ve performans gereksinimlerini karşılamak için yüksek düzeyde ölçeklenebilir şekilde tasarlanmıştır. 
- **Yönetilen.** Microsoft Azure, donanım bakımını, güncelleştirmeleri ve kritik sorunları sizin için işler.
- **Erişilebilir.** Azure Depolama’daki verilere dünyanın herhangi bir yerinden HTTP ya da HTTPS aracılığıyla erişilebilir. Microsoft, Azure depolama için, .NET, Java, Node. js, Python, PHP, Ruby, Go ve diğerleri gibi çeşitli dillerde istemci kitaplıkları ve yetişkinlere yönelik REST API sağlar. Azure Depolama, Azure PowerShell veya Azure CLI’de betik oluşturmayı destekler. Azure portalı ve Azure Depolama Gezgini ise verilerinizle çalışmaya yönelik kolay görsel çözümler sunar.  

## <a name="azure-storage-services"></a>Azure Depolama hizmetleri

Azure Depolama şu veri hizmetlerini içerir:

- [Azure Blobları](../blobs/storage-blobs-introduction.md): Metin ve ikili veriler için yüksek düzeyde ölçeklenebilir nesne deposu.
- [Azure Dosyaları](../files/storage-files-introduction.md): Bulut veya şirket içi dağıtımlar için yönetilen dosya paylaşımları.
- [Azure Kuyrukları](../queues/storage-queues-introduction.md): Uygulama bileşenleri arasında güvenilir mesajlaşmaya yönelik bir mesajlaşma deposu. 
- [Azure Tabloları](../tables/table-storage-overview.md): Yapılandırılmış verilerin şemasız depolanmasına yönelik bir NoSQL deposu.

Her hizmete bir depolama hesabı aracılığıyla erişilir. Başlamak için bkz. [Depolama hesabı oluşturma](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Blob depolama

Azure Blob depolama, Microsoft’un buluta yönelik nesne depolama çözümüdür. Blob depolama, metin veya ikili veri gibi çok miktarda yapılandırılmamış veriyi depolamak için iyileştirilmiştir. 

Blob depolama aşağıdaki durumlar için idealdir:

- Görüntülerin veya belgelerin doğrudan bir tarayıcıya sunulması.
- Dağıtılan erişim için dosyaların depolanması.
- Video ve ses akışları.
- Yedekleme ve geri yükleme, olağanüstü durum kurtarma ve arşivleme için verilerin depolanması.
- Şirket içi veya Azure’da barındırılan bir hizmetle analiz için verilerin depolanması.

Blob depolamadaki nesnelere, dünyanın her yerinden HTTP veya HTTPS üzerinden erişilebilir. Kullanıcılar veya istemci uygulamalar, bloblara URL’ler, [Azure Depolama REST API’si](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) veya bir Azure Depolama istemci kitaplığı aracılığıyla erişebilir. Depolama istemci kitaplıkları [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) ve [Ruby](https://azure.github.io/azure-storage-ruby) dahil olmak üzere birden çok dil ile kullanılabilir.

BLOB depolama hakkında daha fazla bilgi için bkz. [BLOB depolamaya giriş](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Azure Dosyaları

[Azure Dosyaları](../files/storage-files-introduction.md), standart Sunucu İleti Bloğu (SMB) protokolü kullanılarak erişilebilen yüksek oranda kullanılabilir ağ dosya paylaşımları oluşturmanıza olanak tanır. Bu durum, birden fazla VM’nin hem okuma hem de yazma erişimi ile aynı dosyaları paylaşabildiği anlamına gelir. Dosyaları REST arabirimi veya depolama istemci kitaplıkları kullanarak da okuyabilirsiniz.

Dosyalara dünyanın herhangi bir yerinden dosyayı işaret eden ve paylaşılan erişim imzası (SAS) belirteci içeren bir URL kullanarak erişebilme olanağı, Azure Dosyaları'nı kurumsal bir dosya paylaşımındaki dosyalardan ayıran özelliklerden biridir. SAS belirteçleri oluşturabilirsiniz; bunlar, belirli bir süre için özel bir varlığa belirli bir erişim izni verir.

Dosya paylaşımları için birçok yaygın senaryoda kullanılabilir:

- Birçok şirket içi uygulama, dosya paylaşımlarını kullanır. Bu özellik, veri paylaşan uygulamaları Azure’a geçirmeyi kolaylaştırır. Dosya paylaşımını şirket içi uygulamanın kullandığı sürücü harfine bağlarsanız, uygulamanızın dosya paylaşımına erişen kısmı, varsa minimum değişikliklerle çalışır.

- Yapılandırma dosyaları bir dosya paylaşımında depolanabilir ve birden çok VM tarafından bu dosyalara erişilebilir. Bir gruptaki birden fazla geliştirici tarafından kullanılan araç ve yardımcı programlar, herkesin bulabilmesi ve aynı sürümü kullandıklarından emin olunması için bir dosya paylaşımında depolanabilir.

- Tanılama günlükleri, ölçümler ve kilitlenme bilgi dökümleri, bir dosya paylaşımına yazılıp daha sonra işlenebilen veya çözümlenebilen verilerin yalnızca üç örneğidir.

Şu anda Active Directory tabanlı kimlik doğrulaması ve erişim denetimi listeleri (ACL) desteklenmemektedir, ancak gelecekte desteklenecektir. Depolama hesabı kimlik bilgileri, dosya paylaşımına erişim için kimlik doğrulaması sağlamak üzere kullanılır. Bu durum, paylaşımın bağlı olduğu herkesin paylaşıma okuma/yazma erişimi elde edeceği anlamına gelir.

Azure Dosyaları hakkında daha fazla bilgi için bkz. [Azure Dosyaları'na Giriş](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Kuyruk depolama

Azure Kuyruk hizmeti, iletileri depolamak ve almak için kullanılır. Kuyruk iletilerinin boyutu 64 KB'ye kadar olabilir ve bir kuyruk, milyonlarca ileti içerebilir. Kuyruklar, genellikle zaman uyumsuz olarak işlenecek ileti listelerini depolamak için kullanılır.

Örneğin, müşterilerinizin resimleri karşıya yükleyebilmesini ve her resmin küçük resimlerini oluşturabilmesini istediğinizi düşünelim. Müşterinizin resimleri karşıya yüklerken küçük resimleri oluşturmanızı beklemesini sağlayabilirsiniz. Alternatif olarak bir kuyruk kullanabilirsiniz. Müşteri karşıya yüklemeyi tamamladığında, kuyruğa bir ileti yazın. Ardından Azure İşlevinin iletiyi kuyruktan alıp ve küçük resimleri oluşturmasını sağlayın. Bu işlemin tüm bölümleri ayrıca ölçeklendirilebileceğinden kullanımınız için ayarlarken daha fazla kontrol sunar.

Azure Kuyrukları hakkında daha fazla bilgi için bkz. [Kuyruklara Giriş](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

Azure Tablo depolama artık Azure Cosmos DB’nin bir parçasıdır. Azure Tablo depolama belgelerini görmek için bkz. [Azure Tablo Depolamaya Genel Bakış](../tables/table-storage-overview.md). Mevcut Azure Tablo depolama hizmetine ek olarak, aktarım hızı için iyileştirilmiş tablolar, genel dağıtım ve otomatik ikincil dizinler sağlayan yeni bir Azure Cosmos DB Tablo API'si teklifi vardır. Daha fazla bilgi edinmek ve yeni premium deneyimini denemek için lütfen [Azure Cosmos DB Tablo API’si](https://aka.ms/premiumtables) konusunu inceleyin.

Tablo depolama hakkında daha fazla bilgi için bkz. [Azure Tablo depolamaya genel bakış](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Disk depolama

Azure yönetilen disk, bir sanal sabit disktir (VHD). Bunu, şirket içi sunucuda bir fiziksel disk gibi düşünebilirsiniz, ancak sanallaştırılabilir. Azure yönetilen diskler, Azure 'daki bir rastgele GÇ depolama nesnesi olan sayfa Blobları olarak depolanır. Sayfa Blobları, blob kapsayıcıları ve Azure depolama hesapları üzerinde bir soyutlama olduğundan, yönetilen bir disk ' Managed ' çağrısı yaptık. Yönetilen disklerle, tüm yapmanız gereken diski temin etmek ve Azure 'un geri kalanını yapması gerekir.

Yönetilen diskler hakkında daha fazla bilgi için bkz. [Azure yönetilen disklere giriş](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Depolama hesabı türleri

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Depolama hesabı türleri hakkında daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](storage-account-overview.md).

## <a name="securing-access-to-storage-accounts"></a>Depolama hesaplarına erişimin güvenliğini sağlama

Azure depolama 'ya yönelik her isteğin yetkilendirilmiş olması gerekir. Azure depolama aşağıdaki yetkilendirme yöntemlerini destekler:

- **Blob ve kuyruk verileri için Azure Active Directory (Azure AD) tümleştirmesi.** Azure depolama, rol tabanlı erişim denetimi (RBAC) aracılığıyla blob ve sıra Hizmetleri için Azure AD ile kimlik doğrulaması ve yetkilendirmeyi destekler. Azure AD ile istekleri yetkilendirmek, üstün güvenlik ve kullanım kolaylığı için önerilir. Daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme](storage-auth-aad.md).
- **Azure dosyaları için SMB üzerinden Azure AD yetkilendirmesi (Önizleme).** Azure dosyaları, Azure Active Directory Domain Services aracılığıyla SMB (sunucu Ileti bloğu) üzerinden kimlik tabanlı yetkilendirmeyi destekler. Etki alanına katılmış Windows sanal makineleriniz (VM), Azure AD kimlik bilgilerini kullanarak Azure dosya paylaşımlarına erişebilir. Daha fazla bilgi için bkz. [Azure dosyaları IÇIN SMB üzerinden Azure Active Directory yetkilendirmeyi genel bakış (Önizleme)](../files/storage-files-active-directory-overview.md).
- **Paylaşılan anahtarla yetkilendirme.** Azure Depolama Blobu, kuyruk ve tablo Hizmetleri ve Azure dosyaları, paylaşılan anahtarla yetkilendirmeyi destekler. paylaşılan anahtar yetkilendirmesi kullanan bir istemci, depolama hesabı erişim anahtarı kullanılarak imzalanan her isteği içeren bir üst bilgi geçirir. Daha fazla bilgi için bkz. [paylaşılan anahtarla yetkilendirme](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Paylaşılan erişim imzalarını (SAS) kullanarak yetkilendirme.** Paylaşılan erişim imzası (SAS), bir depolama kaynağının URI 'sine eklenebileceği bir güvenlik belirteci içeren bir dizedir. Güvenlik belirteci, izinler ve erişim aralığı gibi kısıtlamaları kapsüller. Daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanma](storage-sas-overview.md).
- **Kapsayıcılara ve bloblara anonim erişim.** Bir kapsayıcı ve Blobları herkese açık olabilir. Bir kapsayıcının veya Blobun genel olduğunu belirttiğinizde, herkes onu anonim olarak okuyabilir; kimlik doğrulaması gerekmiyor. Daha fazla bilgi için bkz. [Kapsayıcılar ve bloblar için anonim okuma erişimini yönetme](../blobs/storage-manage-access-to-resources.md).

## <a name="encryption"></a>Şifreleme

Depolama hizmetleri için iki temel şifreleme seçeneği vardır. Güvenlik ve şifreleme hakkında daha fazla bilgi için bkz. [Azure Depolama güvenlik kılavuzu](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Bekleme sırasında şifreleme

Azure depolama şifrelemesi, kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamak için verilerinizi korur ve korur. Azure depolama, depolama hesabına kalıcı yapmadan önce tüm verileri otomatik olarak şifreler ve alma işleminden önce şifresini çözer. Şifreleme, şifre çözme ve anahtar yönetim işlemleri, kullanıcılar için tamamen saydamdır. Müşteriler, Azure Key Vault kullanarak kendi anahtarlarını yönetmeyi de seçebilir. Daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama şifrelemesi](storage-service-encryption.md).

### <a name="client-side-encryption"></a>İstemci Tarafında Şifreleme

Azure depolama istemci kitaplıkları, istemci kitaplığından, verileri kablo üzerinden göndermeden ve yanıtın şifresini çözmeden önce şifrelemek için yöntemler sağlar. İstemci tarafı şifreleme aracılığıyla şifrelenen veriler de Azure Storage tarafından REST 'de şifrelenir. İstemci tarafı şifreleme hakkında daha fazla bilgi için bkz. [Azure depolama için .net Ile istemci tarafı şifreleme](storage-client-side-encryption.md).

## <a name="redundancy"></a>Yedeklilik

Verilerinizin güvende olmasını sağlamak için Azure Depolama, verilerinizin birden çok kopyasını çoğaltır. Depolama hesabınızı ayarlarken bir artıklık seçeneği belirleyin.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Olağanüstü durum kurtarma hakkında daha fazla bilgi için bkz. [Azure Storage 'Da olağanüstü durum kurtarma ve depolama hesabı yük devretmesi (Önizleme)](storage-disaster-recovery-guidance.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Azure Depolama’da veri alışverişi

Verileri Azure depolama alanına veya dışına taşımaya yönelik çeşitli seçenekleriniz vardır. Seçtiğiniz seçenek, veri kümenizin boyutuna ve ağ bant genişliğine bağlıdır. Daha fazla bilgi için bkz. [veri aktarımı için bir Azure çözümü seçme](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Fiyatlandırma

Azure Depolama için fiyatlandırma hakkında ayrıntılı bilgi için bkz. [Fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Depolama API'leri, kitaplıklar ve araçlar

Azure Storage kaynakları HTTP/HTTPS isteği yapabilen her dil ile erişilebilir. Ayrıca, Azure Storage birkaç popüler dilde programlama kitaplıkları sunar. Bu kitaplıklar zaman uyumlu ve zaman uyumsuz çağrılar, işlemleri gruplama, özel durum yönetimi, otomatik yeniden denemeler, işlemsel davranışlar vb. ayrıntıları ele alarak Azure Storage ile çalışmanın çoğu yönünü basitleştirir. Kitaplıklar şu anda aşağıdaki diller için mevcuttur ve yeni kitaplıklar geliştirme aşamasındadır:

### <a name="azure-storage-data-api-and-library-references"></a>Azure Depolama veri API’si ve kitaplık başvuruları

- [Azure depolama REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [.NET için Azure depolama istemci kitaplığı](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Java/Android için Azure depolama istemci kitaplığı](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Node. js için Azure depolama istemci kitaplığı](https://docs.microsoft.com/javascript/api/overview/azure/storage)
- [Python için Azure depolama istemci kitaplığı](https://github.com/Azure/azure-storage-python)
- [PHP için Azure depolama istemci kitaplığı](https://github.com/Azure/azure-storage-php)
- [Ruby için Azure depolama istemci kitaplığı](https://github.com/Azure/azure-storage-ruby)
- [İçin Azure depolama istemci kitaplığıC++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Azure Depolama yönetimi API’si ve kitaplık başvuruları

- [Depolama Kaynak Sağlayıcısı REST API’si](https://docs.microsoft.com/rest/api/storagerp/)
- [.NET için Depolama Kaynak Sağlayıcısı İstemci Kitaplığı](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [Depolama Hizmet Yönetimi REST API'si (Klasik)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Azure Depolama veri taşıma API’si ve kitaplık başvuruları

- [Depolama İçeri/Dışarı Aktarma Hizmeti REST API'si](https://docs.microsoft.com/rest/api/storageimportexport/)
- [.NET için Depolama Veri Taşıma İstemcisi Kitaplığı](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Araçlar ve Yardımcı Programlar

- [Depolama için Azure PowerShell Cmdlet'leri](https://docs.microsoft.com/powershell/module/az.storage)
- [Depolama için Azure CLI Cmdlet'leri](https://docs.microsoft.com/cli/azure/storage)
- [AzCopy Komut Satırı Yardımcı Programı](https://aka.ms/downloadazcopy)
- [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/), Microsoft’un Windows, macOS ve Linux üzerinde Azure Depolama verileriyle görsel olarak çalışmanızı sağlayan ücretsiz ve tek başına uygulamasıdır.
- [Azure Depolama İstemcisi Araçları](../storage-explorers.md)
- [Azure Geliştirici Araçları](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Sonraki adımlar

Çalışır durumda bir Azure Depolama edinmek için bkz. [Depolama hesabı oluşturma](storage-quickstart-create-account.md).
