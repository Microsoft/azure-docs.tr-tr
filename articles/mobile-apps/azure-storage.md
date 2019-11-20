---
title: Azure depolama ile yüksek düzeyde güvenli, dayanıklı ve ölçeklenebilir uygulamalar oluşturmak için bulut depolama
description: Büyük yapılı ve yapılandırılmamış mobil uygulama verilerini bulutta depolamak için hizmetler hakkında bilgi edinin.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 01407f843ef36095fc87feb3722e85dc477ad8bb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795659"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Azure depolama ile yüksek oranda güvenli, dayanıklı ve ölçeklenebilir uygulamalar için bulut depolama
[Azure depolama](https://azure.microsoft.com/services/storage/) , Microsoft 'un veri nesneleri için büyük ölçüde ölçeklenebilir nesne deposu, bulut için bir dosya sistemi hizmeti, güvenilir mesajlaşma için bir mesajlaşma deposu ve bir NoSQL deposu sunan modern uygulamalara yönelik bulut depolama çözümüdür. Azure Depolama şu özelliklere sahiptir:
- **Dayanıklı ve yüksek oranda kullanılabilir** -artıklığı, geçici donanım arızaları durumunda verilerinizin güvende olmasını sağlar. Ayrıca, yerel felaketler ya da doğal afetlere karşı ek koruma için verileri meri merkezleri ya da coğrafi bölgeler arasında çoğaltmayı seçebilirsiniz. Bu şekilde çoğaltılan veriler beklenmeyen bir kesinti durumunda yüksek oranda kullanılabilir olmaya devam eder.
- **Güvenli** -Azure depolama 'ya yazılan tüm veriler hizmet tarafından şifrelenir. Azure Depolama, verilerinize erişmesi gereken kişiler üzerinde ayrıntılı denetime sahip olmanızı sağlar.
- **Ölçeklenebilir** hizmetler, günümüzün uygulamalarının veri depolama ve performans ihtiyaçlarını karşılamak için büyük ölçüde ölçeklenebilir olacak şekilde tasarlanmıştır.
- **Yönetilen** -Azure, donanım bakımını, güncelleştirmeleri ve kritik sorunları sizin için işler.
- Veriler, dünyanın her yerinden HTTP veya HTTPS üzerinden **erişilebilir** . Microsoft, .NET, Java, Node. js, Python, PHP, Ruby, Go ve diğerleri gibi çeşitli dillerde istemci kitaplıklarını ve çok sayıda REST API sağlar. Komut dosyası Azure PowerShell veya Azure CLı ve Azure portal desteklenir ve Azure Depolama Gezgini verilerle çalışmaya yönelik kolay görsel çözümler sunar.

Mobil uygulamalarınızda bulut depolamayı etkinleştirmek için aşağıdaki hizmetleri kullanın.

## <a name="azure-blob-storage"></a>Azure Blob Depolama
[Azure Blob depolama](https://azure.microsoft.com/services/storage/blobs/) , bulut için nesne depolama çözümü sunar ve metin veya ikili gibi belirli bir veri modeline veya tanımına bağlı olmayan büyük miktarlarda yapılandırılmamış verileri depolamak için iyileştirilmiştir. İstemci kitaplıklarını çeşitli dillerde destekler ve şunları için tasarlanmıştır:
- Görüntülerin veya belgelerin doğrudan bir tarayıcıya sunulması.
- Dağıtılan erişim için dosyaların depolanması.
- Video ve ses akışları.
- Günlük dosyalarına yazma.
- Yedekleme ve geri yükleme, olağanüstü durum kurtarma ve arşivleme için verilerin depolanması.
- Şirket içi veya Azure’da barındırılan bir hizmetle analiz için verilerin depolanması.

**Başvur**
- [Azure portalda](https://portal.azure.com)
- [Belgelerle](/azure/storage/blobs/storage-blobs-introduction)
- [Hızlı başlangıçlar](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Örnekler](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Tablo depolama](https://azure.microsoft.com/services/storage/tables/) , bulutta yapılandırılmış NoSQL verilerini depolayan bir hizmettir ve şema-daha az tasarıma sahip bir anahtar/öznitelik deposu sağlar. Azure Tablo depolama, büyük miktarlarda yapısal veriyi depolar. Hizmet, Azure bulutunun içinden ve dışından kimliği doğrulanmış çağrılar kabul eden bir NoSQL veri deposudur. Azure tabloları, yapılandırılmış ve ilişkisel olmayan verilerin depolanması için idealdir. Tablo depolamanın yaygın kullanımları şunlardır:
- Web ölçeğinde uygulamalara hizmet veren yapılandırılmış verilerin TBs 'i depolanıyor.
- Karmaşık birleştirmeler, yabancı anahtarlar veya saklı yordamlar gerektirmeyen veri kümelerini depolama ve hızlı erişim için Normalleştirilmemiş olabilir.
- Kümelenmiş dizin kullanarak verileri hızlı bir şekilde sorgulama.
- WCF veri hizmeti .NET kitaplıklarıyla OData protokolünü ve LINQ sorgularını kullanarak verilere erişme.

Yapılandırılmış ve ilişkisel olmayan verilerin büyük kümelerini depolamak ve sorgulamak için Tablo depolamayı kullanabilirsiniz; tablolara talep arttıkça da ölçekleneceklerdir.

**Başvur**
- [Azure portalda](https://portal.azure.com)
- [Belgelerle](/azure/storage/tables/table-storage-overview)
- [Örnekler](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Hızlı başlangıçlar](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Dosyaları
[Azure Dosyaları](https://azure.microsoft.com/services/storage/files/), standart Sunucu İleti Bloğu (SMB) protokolü kullanılarak erişilebilen yüksek oranda kullanılabilir ağ dosya paylaşımları oluşturmanıza olanak tanır. Aynı dosyaları hem okuma hem de yazma erişimiyle paylaşabilir. Dosyaları REST arabirimi veya depolama istemci kitaplıkları kullanarak da okuyabilirsiniz. Dosyaya işaret eden ve paylaşılan erişim imzası (SAS) belirteci içeren bir URL kullanarak dünyanın her yerinden dosyalara erişebilirsiniz. SAS belirteçleri oluşturabilirsiniz; bunlar, belirli bir süre için özel bir varlığa belirli bir erişim izni verir.

Azure dosya paylaşımları şunları yapmak için kullanılabilir:
- Şirket içi dosya sunucularını değiştirme veya ekleme: Windows, macOS ve Linux gibi yaygın işletim sistemleri, dünyanın neresinde olursa olsun Azure dosya paylaşımlarını doğrudan bağlayabilirler. Azure dosya paylaşımları ayrıca verilerin kullanıldığı yerde performanslı ve dağıtılmış bir şekilde önbelleğe alınması için Azure Dosya Eşitleme ile şirket içi veya bulut üzerindeki Windows Server’larda çoğaltılabilir.
- Dosya uygulamasının veya Kullanıcı verilerinin depolanması için bir dosya paylaşımının beklediği uygulamaları buluta taşıyın **ve** taşıyın.
- **Bulut geliştirmeyi basitleştirme**: Azure dosyaları, yeni bulut geliştirme projelerini basitleştirecek çeşitli yollarla da kullanılabilir. Örnek:
    - Paylaşılan uygulama ayarları: dağıtılmış uygulamalar için yaygın bir düzende, yapılandırma dosyalarının birçok uygulama örneklerinden erişilebilecekleri merkezi bir konumda bulunması gerekir. Uygulama örnekleri yapılandırmalarını Dosya REST API'si üzerinden yükleyebilir ve kullanıcılar bu verilere SMB paylaşımını yerel ortama bağlayarak erişebilir.
    - Tanılama paylaşma: Azure dosya paylaşma, bulut uygulamalarının günlük, ölçüm ve kilitlenme dökümlerinin yazabilmesi için uygun bir yerdir. Günlükler uygulama örnekleri tarafından Dosya REST API'si aracılığıyla yazılabilir ve geliştiriciler dosya paylaşımını yerel makinelerine bağlayarak bu verilere erişebilir. Bu durum geliştiricilerin bildikleri ve sevdikleri araçları bırakmak zorunda kalmadan bulut geliştirmeye geçmelerini sağladığından önemli bir esneklik sunar.

**Başvur**
- [Azure portalda](https://portal.azure.com)
- [Belgelerle](/azure/storage/files/storage-files-introduction)
- [Hızlı başlangıçlar](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queues"></a>Azure Kuyrukları
[Azure kuyruk depolama](https://azure.microsoft.com/services/storage/queues/) , çok sayıda iletiyi depolamaya yönelik bir hizmettir. HTTP veya HTTPS kullanarak kimliği doğrulanmış çağrılar aracılığıyla dünyanın her yerinden iletilere erişirsiniz. Bir kuyruk iletisi boyutu 64 KB 'ye kadar olabilir ve bir kuyruk, depolama hesabının toplam kapasite sınırına kadar milyonlarca ileti içerebilir. Kuyruklar genellikle zaman uyumsuz olarak işlenecek iş biriktirme listesi oluşturmak için kullanılır.

**Başvur**
- [Azure portalda](https://portal.azure.com)
- [Belgelerle](/azure/storage/queues/)
- [Hızlı başlangıçlar](/azure/storage/queues/storage-quickstart-queues-portal)
- [Örnekler](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
