
## <a name="about-vhds"></a>VHD'ler hakkında

Azure’da kullanılan VHD’ler, Azure’daki standart veya premium depolama hesabında sayfa blobları olarak depolanır. Sayfa blobları hakkında bilgi için bkz. [Blok bloblarını ve sayfa bloblarını anlama](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Premium depolama hakkında daha fazla ayrıntı için bkz. [Yüksek performanslı premium depolama ve Azure VM'leri](../articles/storage/storage-premium-storage.md).

Azure, sabit bir disk VHD biçimini destekler. Sabit biçim, mantıksal diski dosya içinde doğrusal olarak düzenlediğinden, disk farkı X'in içeriği blob farkı X konumunda depolanır. Blob'un sonundaki küçük bir alt bilgi VHD'nin özelliklerini tanımlar. Çoğunlukla, sabit biçim alanı israf eder, çünkü çoğu diskte kullanılmayan büyük aralıklar vardır. Ancak, Azure .vhd dosyalarını seyrek biçimde depoladığından aynı anda hem sabit hem de dinamik disklerin avantajlarından yararlanırsınız. Daha ayrıntılı bilgi için bkz. [Sanal sabit diskleri kullanmaya başlama](https://technet.microsoft.com/library/dd979539.aspx).

Azure’da disk veya görüntü oluşturmak için kaynak olarak kullanmak istediğiniz tüm .vhd dosyaları salt okunur özelliktedir. Bir disk veya görüntü oluşturduğunuzda, Azure .vhd dosyalarının kopyalarını oluşturur. Bu kopyalar, VHD’yi nasıl kullandığınıza bağlı olarak salt okunur veya okuma-yazma niteliktedir.

Bir görüntüden sanal makine oluşturduğunuzda Azure, sanal makine için kaynak .vhd dosyasının kopyası olan bir disk oluşturur. Yanlışlıkla silmeye karşı korumak üzere Azure, bir görüntü, işletim sistemi diski ya da veri diski oluşturmak için kullanılan her kaynak .vhd dosyasına kira koyar.

Bir kaynak .vhd dosyasını silmeden önce diski veya görüntüyü silerek kirayı kaldırmanız gerekir. Sanal makine tarafından işletim sistemi diski olarak kullanılan bir .vhd dosyasını silmek için, sanal makineyi ve ilişkili tüm diskleri silerek sanal makineyi, işletim sistemi diskini ve kaynak .vhd dosyasını tek seferde silebilirsiniz. Ancak, bir veri diskinin kaynağı olan .vhd dosyasının silinmesi, belirli bir sırada birkaç adımın uygulanmasını gerektirir. İlk olarak, diski sanal makineden ayırın, ardından diski ve sonra .vhd dosyasını silin.

> [!WARNING]
> Kaynak .vhd dosyasını depolama alanından silerseniz veya depolama hesabınızı silerseniz, Microsoft bu verileri kurtaramaz.
> 

## <a name="types-of-disks"></a>Disk türleri 

Disklerinizi oluştururken depolama için seçebileceğiniz iki performans katmanı mevcuttur: Standart Depolama ve Premium Depolama. Ayrıca, yönetilmeyen ve yönetilen olmak üzere iki tür disk mevcuttur ve bunlar herhangi bir performans katmanın bulunabilir.  

### <a name="standard-storage"></a>Standart depolama 

Standart Depolama, HDD’ler ile desteklenir ve yüksek performans sunarken uygun maliyetli depolama sağlar. Standart depolama bir veri merkezine yerel olarak çoğaltılabilir veya birincil ve ikincil veri merkezleri ile coğrafi yedekleri olabilir. Depolama çoğaltma hakkında daha fazla bilgi için bkz. [Azure Depolama çoğaltma](../articles/storage/storage-redundancy.md). 

VM diskleri ile Standart Depolama kullanma hakkında daha fazla bilgi için lütfen bkz. [Standart Depolama ve Diskler](../articles/storage/storage-standard-storage.md).

### <a name="premium-storage"></a>Premium depolama 

Premium Depolama, SSD’ler ile desteklenir ve G/Ç yoğunluklu iş yükleri için yüksek performanslı, düşük gecikme süresine sahip disk desteği sunar. Premium Depolamayı DS, DSv2, GS veya FS serisi Azure VM'ler ile kullanabilirsiniz. Daha fazla bilgi için bkz. [Premium Depolama](../articles/storage/storage-premium-storage.md).

### <a name="unmanaged-disks"></a>Yönetilmeyen diskler

Yönetilmeyen diskler, VM'ler tarafından kullanılan geleneksel türdeki disklerdir. Bunları kullanarak kendi depolama hesabınızı oluşturabilir ve diski oluştururken bu depolama hesabını belirtebilirsiniz. Depolama hesabının [ölçeklendirme hedeflerini](../articles/storage/storage-scalability-targets.md) (örneğin, 20.000 IOPS) aşarak VM’lerin azaltılmasına neden olabileceğinden, aynı depolama hesabına çok fazla disk yerleştirmediğinizden emin olun. Yönetilmeyen disklerde, VM’lerinizden en iyi performansı elde etmek için, bir veya daha fazla depolama hesabının kullanımını nasıl en üst düzeye çıkarabileceğinizi anlamanız gerekir.

### <a name="managed-disks"></a>Yönetilen diskler 

Yönetilen Diskler, depolama hesabı oluşturma/yönetme işlemini arka planda gerçekleştirir ve depolama hesabının ölçeklenebilirlik sınırları hakkında endişe etmeniz gerekmez. Azure’un diski oluşturup yönetebilmesi için disk boyutunu ve performans katmanını (Standart/Premium) belirtmeniz yeterlidir. Disk eklediğinizde veya VM ölçeğini artırıp azalttığınızda bile kullanılan depolama alanı konusunda endişelenmeniz gerekmez. 

Ayrıca, her Azure bölgesinde bir depolama hesabındaki özel görüntülerinizi yönetebilir ve aynı abonelikte yüzlerce VM oluşturmak için kullanabilirsiniz. Yönetilen Diskler hakkında daha fazla bilgi için bkz. [Yönetilen Disklere Genel Bakış](../articles/storage/storage-managed-disks-overview.md).

Yeni VM’ler için Azure Yönetilen Diskleri kullanmanız ve Yönetilen Disklerde sunulan çok sayıda özellikten yararlanmak için önceki yönetilmeyen diskleri yönetilen disklere dönüştürmeniz önerilir.

### <a name="disk-comparison"></a>Disk karşılaştırması

Aşağıdaki tabloda, kullanacağınız seçeneğe karar vermenize yardımcı olmak üzere hem yönetilmeyen hem de yönetilen diskler için Premium ve Standart katmanların karşılaştırması yapılmaktadır.

|    | Azure Premium Disk | Azure Standart Disk |
|--- | ------------------ | ------------------- |
| Disk Türü | Katı Hal Sürücüleri (SSD) | Sabit Disk Sürücüleri (HDD)  |
| Genel Bakış  | G/Ç yoğunluklu iş yükleri çalıştıran veya görev açısından kritik üretim ortamı barındıran VM’ler için SSD tabanlı, yüksek performans ve düşük gecikme süresi sunan disk desteği | Geliştirme/Test VM senaryoları için HDD tabanlı, uygun maliyetli disk desteği |
| Senaryo  | Üretim ve performansa duyarlı iş yükleri | Geliştirme/Test, kritik olmayan, <br>Nadir erişim |
| Disk Boyutu | P10: 128 GB<br>P20: 512 GB<br>P30: 1024 GB | Yönetilmeyen Diskler: 1 GB – 1 TB <br><br>Yönetilen Diskler:<br> S4: 32 GB <br>S6: 64 GB <br>S10: 128 GB <br>S20: 512 GB <br>S30: 1024 GB |
| Disk Başına En Fazla Aktarım Hızı | 200 MB/sn | 60 MB/sn |
| Disk başına en fazla IOPS | 5000 IOPS | 500 IOPS |
