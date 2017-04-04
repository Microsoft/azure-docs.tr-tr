## <a name="what-is-blob-storage"></a>Blob Storage nedir?
Azure Blob Storage; HTTP veya HTTPS aracılığıyla dünyanın her yerinde erişilebilen metin veya ikili veriler gibi büyük miktarda yapılandırılmamış nesne verilerinin depolanması için bir hizmettir. Verileri genel olarak herkese açık kullanıma sunmak veya uygulama verilerini özel olarak depolamak için Blob Storage’ı kullanabilirsiniz.

Blob Storage’ın yaygın kullanımları şunlardır:

* Görüntülerin veya belgelerin doğrudan bir tarayıcıya sunulması
* Dağıtılan erişim için dosyaların depolanması
* Video ve ses akışları
* Yedekleme ve geri yükleme, olağanüstü durum kurtarma ve arşivleme için verilerin depolanması
* Şirket içi veya Azure barındırılan hizmetle analiz için verilerin depolanması

## <a name="blob-service-concepts"></a>Blob hizmeti kavramları
Blob hizmetinde şu bileşenler bulunur:

![Blob mimarisi](./media/storage-blob-concepts-include/blob1.png)

* **Depolama Hesabı:** Tüm Azure Storage erişimi bir depolama hesabıyla yapılır. Bu depolama hesabı **Genel amaçlı depolama hesabı** veya nesnelerin/blobların depolanması için özelleştirilen **Blob Storage hesabı** olabilir. Daha fazla bilgi için bkz. [Azure depolama hesapları hakkında](../articles/storage/storage-create-storage-account.md).
* **Kapsayıcı:** Kapsayıcı, bir dizi blobun gruplandırılmasını sağlar. Tüm bloblar bir kapsayıcıda olmalıdır. Bir hesapta sınırsız sayıda kapsayıcı olabilir. Kapsayıcıda sınırsız sayıda blob depolanabilir. Kapsayıcı adındaki harflerin küçük harf olması gerektiğini unutmayın.
* **Blob:** Herhangi bir türde ve boyutta bir dosya. Azure Storage üç tür blob sunar: blok blobları, sayfa blobları ve ekleme blobları.
  
    *Blok blobları*, belgeler ve medya dosyaları gibi metin veya ikili dosyaların depolanması için idealdir. *Ekleme blobları* blok bloblarına benzer; bloklardan oluşturulmuş olsalar da ekleme işlemleri için iyileştirilmişlerdir; bu nedenle, günlük kaydı senaryoları için kullanışlıdırlar. Tek bir blok blobu, her birinin büyüklüğü 100 MB’a kadar olabilen 50.000 blok içerebilir; toplam boyut 4,75 TB'tan biraz fazladır (100 MB X 50.000). Tek bir ekleme blobu, her birinin büyüklüğü 4 MB’a kadar olabilen 50.000 blok içerebilir; toplam boyut 195 GB'tan biraz fazladır (4 MB X 50.000).
  
    *Sayfa blobları* boyut olarak 1 TB'ye kadar olabilir; sık gerçekleştirilen okuma/yazma işlemleri için daha verimlidir. Azure Virtual Machines sayfa bloblarını işletim sistemi ve veri diskleri olarak kullanır.
  
    Kapsayıcıları ve blobları adlandırma hakkında ayrıntılı bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](/rest/api/storageservices/fileservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

