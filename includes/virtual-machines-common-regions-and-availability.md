# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Azure'da sanal makineler için kullanılabilirlik ve bölgeler
Sanal makinelerinizin (VM’ler) Azure’da nasıl ve hangi konumda çalıştığının yanı sıra performans, kullanılabilirlik ve yedekliliği artırmak için kullanabileceğiniz seçeneklerin de anlaşılması önemlidir. Azure, dünyanın dört bir yanındaki birden fazla veri merkezinde çalışmaktadır. Bu veri merkezleri, coğrafi bölgeler halinde gruplandırılarak uygulamalarınızı oluşturacağınız yeri seçme esnekliği tanır. Bu makalede, Azure’un kullanılabilirlik ve yedeklilik özelliklerine genel bakış sunulmaktadır.

## <a name="what-are-azure-regions"></a>Azure bölgeleri nelerdir?
Azure 'Batı ABD', 'Kuzey Avrupa' veya 'Güneydoğu Asya' gibi tanımlı coğrafi bölgelerde VM gibi kaynaklar oluşturmanıza olanak tanır. Şu anda dünya çapında 30 Azure bölgesi mevcuttur. [Bölgeler ve konumlarının listesini](https://azure.microsoft.com/regions/) gözden geçirebilirsiniz. Her bölge içinde, yedeklilik ve kullanılabilirlik sağlayan birden fazla veri merkezi mevcuttur. Bu yaklaşım, uygulamalarınızı oluştururken kullanıcılarınıza en yakın VM'leri oluşturmak ve her türlü hukuk, uyumluluk veya vergi gereksinimlerini karşılamak için size esneklik sağlar.

## <a name="special-azure-regions"></a>Özel Azure bölgeleri
Uygulamalarınızı oluştururken kullanmak isteyebileceğiniz, uyumluluk veya hukuk gereksinimlerine yönelik bazı özel Azure bölgeleri mevcuttur. Bu özel bölgeleri şunlardır:

* **ABD Virginia** ve **ABD Iowa**
  * ABD kamu kuruluşları ve iş ortaklarına yönelik olarak ABD’de bulunan ve denetlenen kişilerce çalıştırılan fiziksel ve mantıksal ağdan yalıtılmış Azure örneği. [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) ve [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA) gibi ek uyumluluk sertifikaları içerir. [Azure Kamu](https://azure.microsoft.com/features/gov/) hakkında daha fazla bilgi alın.
* **Orta Hindistan**, **Güney Hindistan** ve **Batı Hindistan**
  * Bu bölgeler, şu anda Hindistan’da yerel kaydı bulunan toplu lisanslama müşterileri ve iş ortakları tarafından kullanılabilir. 2016 yılında, doğrudan çevrimiçi abonelik satın almış kullanıcılar bölgelere erişebilir.
* **Çin Doğu** ve **Çin Kuzey**
  * Bu bölgeler, Microsoft ile 21Vianet arasında, Microsoft’un veri merkezlerini doğrudan yönetmediği benzersiz ortaklık ile kullanıma sunulmaktadır. [Çin’de Microsoft Azure](http://www.windowsazure.cn/) hakkında daha fazla bilgi alın.
* **Almanya Orta** ve **Almanya Kuzeydoğu**
  * Bu bölgeler şu anda, müşteri verilerinin Almanya’da barındırıldığı bir veri emanetçisi modeli aracılığıyla sunulmaktadır. Bu modelde veriler, Deutsche Telekom’a ait bir şirket olan ve Alman veri emanetçisi olarak görev yapan T-Systems’ın denetimindedir.

## <a name="region-pairs"></a>Bölge çiftleri
Her Azure bölgesi aynı coğrafyadaki (ABD, Avrupa veya Asya) başka bir bölgeyle eşleştirilir. Bu yaklaşım her iki bölgeyi de aynı anda etkileyen bir doğal felaket, toplumsal karmaşa, güç kesintisi veya fiziksel ağ kesintisi olasılığını azaltması gereken bir coğrafyada VM depolama gibi kaynak çoğaltma işlemlerine olanak tanır. Bölge çiftlerinin diğer avantajları şunlardır:

* Daha geniş bir Azure kesintisi durumunda, uygulamalar için geri yükleme süresini azaltmak üzere her çift içinden bir bölgeye öncelik verilir. 
* Kapalı kalma süresini ve uygulama kesintisi riskini azaltmak amacıyla, planlı Azure güncelleştirmeleri, bölge çiftlerine tek tek uygulanır.
* Veriler, vergi ve yasa uygulama yetkisi bakımından çiftiyle aynı coğrafyada (Brezilya Güney hariç) bulunmaya devam eder.

Bölge çiftlerinin örnekleri şunlardır:

| Birincil | İkincil |
|:--- |:--- |
| Batı ABD |Doğu ABD |
| Kuzey Avrupa |Batı Avrupa |
| Güneydoğu Asya |Doğu Asya |

[Bölgesel çiftlerin tam listesini burada](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions) görebilirsiniz.

## <a name="feature-availability"></a>Özellik kullanılabilirliği
Belirli VM boyutları ya da depolama türleri gibi bazı hizmetler veya VM özellikleri yalnızca belirli bölgelerde kullanılabilir. Ayrıca, belirli bir bölge seçmenizi gerektirmeyen [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) veya [Azure DNS](../articles/dns/dns-overview.md) gibi bazı genel Azure hizmetleri de vardır. Uygulama ortamınızı tasarlamanıza yardımcı olmak üzere [her bölgedeki Azure hizmetleri kullanılabilirliğini](https://azure.microsoft.com/regions/#services) denetleyebilirsiniz. 

## <a name="storage-availability"></a>Depolama kullanılabilirliği
Kullanılabilir çoğaltma seçenekleri düşünüldüğünde Azure bölge ve coğrafyalarının anlaşılması önemlidir. Depolama türüne bağlı olarak farklı çoğaltma seçenekleriniz vardır.

**Azure Yönetilen Diskler**
* Yerel olarak yedekli depolama (LRS)
  * Depolama hesabınızı oluşturduğunuz bölge içinde verilerinizi üç kez çoğaltır.

**Depolama hesabı temelli diskler**
* Yerel olarak yedekli depolama (LRS)
  * Depolama hesabınızı oluşturduğunuz bölge içinde verilerinizi üç kez çoğaltır.
* Bölgesel olarak yedekli depolama (ZRS)
  * Tek bir bölge ya da iki bölgedeki iki veya üç tesiste verilerinizi üç kez çoğaltır.
* Coğrafi olarak yedekli depolama (GRS)
  * Verilerinizi birincil bölgeden yüzlerce kilometre uzaktaki bir ikincil bölgeye çoğaltır.
* Okuma erişimli coğrafi olarak yedekli depolama (RA-GRS)
  * GRS ile olduğu gibi verilerinizi ikincil bölgeye çoğaltır, ancak daha sonra ikincil konumdaki verilere salt okunur erişim sağlar.

Aşağıdaki tabloda, depolama çoğaltma türleri arasındaki farkları hızlı bir genel bakış sunulmaktadır:

| Çoğaltma stratejisi | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Veriler birden çok tesis arasında çoğaltılır. |Hayır |Evet |Evet |Evet |
| Veriler ikincil konumdan ve birincil konumdan okunabilir. |Hayır |Hayır |Hayır |Evet |
| Ayrı düğümlerde tutulan veri kopyası sayısı. |3 |3 |6 |6 |

[Azure Depolama çoğaltma seçenekleri hakkında buradan](../articles/storage/storage-redundancy.md) daha fazla bilgi alabilirsiniz. Yönetilen diskler hakkında daha fazla bilgi için bkz. [Azure Yönetilen Disklere genel bakış](../articles/storage/storage-managed-disks-overview.md).

### <a name="storage-costs"></a>Depolama maliyetleri
Fiyatlar seçtiğiniz depolama türüne ve kullanılabilirliğe bağlı olarak değişir.

**Azure Yönetilen Diskler**
* Premium Yönetilen Diskler, Katı Hal Sürücüleri (SSD) ile desteklenirken, Standart Yönetilen Diskler, normal dönen disklerle desteklenir. Hem Premium hem de Standart Yönetilen Diskler, diskin sağlanan kapasitesine göre ücretlendirilir.

**Yönetilmeyen diskler**
* Premium depolama, Katı Hal Sürücüleri (SSD) ile desteklenirken, diskin kapasitesine göre ücretlendirilir.
* Standart depolama, normal dönen disklerle desteklenir ve kullanımdaki kapasiteye ve istenen depolama kullanılabilirliğine göre ücretlendirilir.
  * RA-GRS için, verileri başka bir Azure bölgesine çoğaltmak için gereken bant genişliğine yönelik ek bir Coğrafi Çoğaltma Veri Aktarımı ücreti vardır.

Farklı depolama türleri ve kullanılabilirlik seçenekleri hakkında fiyatlandırma bilgileri için bkz. [Azure Depolama Fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

## <a name="azure-images"></a>Azure görüntüleri
Azure'da VM’ler bir görüntüden oluşturulur. Genellikle, görüntüler iş ortaklarının önceden yapılandırılmış tam işletim sistemi veya uygulama görüntüleri sağlayabildiği [Azure Market](https://azure.microsoft.com/marketplace/)’ten alınır.

Azure Market’te bir görüntüden VM oluşturduğunuzda, aslında şablonlarla çalışırsınız. Azure Resource Manager şablonları; VM, depolama, sanal ağ vb. öğelerden oluşan karmaşık uygulama ortamları oluşturmak için kullanılabilen, bildirim temelli JavaScript Nesne Gösterimi (JSON) dosyalarıdır. [Şablonlarınızı oluşturma](../articles/resource-group-authoring-templates.md) işlemi dahil [Azure Resource Manager şablonlarını](../articles/azure-resource-manager/resource-group-overview.md) kullanma hakkında daha fazla bilgi alabilirsiniz.

Ayrıca, kendi özel görüntülerinizi oluşturabilir ve derleme gereksinimlerinize göre özel VM’leri hızlıca oluşturmak üzere [Azure CLI](../articles/virtual-machines/linux/upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ya da [Azure PowerShell](../articles/virtual-machines/windows/upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kullanarak karşıya yükleyebilirsiniz.

## <a name="availability-sets"></a>Kullanılabilirlik kümeleri
Kullanılabilirlik kümesi, Azure’un, uygulamanızın yedeklilik ve kullanılabilirlik sağlamak üzere nasıl oluşturulduğunu anlamasına olanak tanıyan bir mantıksal VM grubudur. Yüksek oranda kullanılabilir bir uygulama sağlamak ve [%99,95 Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) hedefini karşılamak üzere bir kullanılabilirlik kümesinde iki ya da daha fazla BM oluşturulması önerilir. Tek bir VM, [Azure Premium Depolama](../articles/storage/storage-premium-storage.md) kullanıyorsa, Azure SLA planlanmamış bakım olayları için geçerli olur. Kullanılabilirlik kümesi, donanım hatalarına karşı koruyan ve güncelleştirmelerin güvenli bir şekilde uygulanmasını sağlayan iki ek gruptan oluşur: hata etki alanları (FD) ve güncelleme etki alanları (UD).

![Güncelleme etki alanı ve hata etki alanı yapılandırmasının kavramsal çizimi](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

[Linux VM](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) veya [Windows VM](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kullanılabilirliğini yönetme hakkında daha fazla bilgi alabilirsiniz.

### <a name="fault-domains"></a>Hata etki alanları
Hata etki alanı, ortak bir güç kaynağı ve ağ anahtarını paylaşan, şirket içi veri merkezindeki rafa benzer bir temel alınan donanım mantık grubudur. Bir kullanılabilirlik kümesinde VM’ler oluşturduğunuzda, Azure platformu VM’lerinizi bu hata etki alanlarına otomatik olarak dağıtır. Bu yaklaşım, olası fiziksel donanım hatalarının, ağ kesintilerinin veya güç kesintilerinin etkisini sınırlar.

#### <a name="managed-disk-fault-domains-and-availability-sets"></a>Yönetilen Disk hata etki alanları ve kullanılabilirlik kümeleri
[Azure Yönetilen Diskler](../articles/storage/storage-faq-for-disks.md)’i kullanan sanal makineler, yönetilen kullanılabilirlik kümesi kullanılırken yönetilen disk hata etki alanları ile hizalanır. Bu hizalama, bir VM'ye bağlı tüm yönetilen disklerin, aynı yönetilen disk hata etki alanı içinde olmasını sağlar. Yönetilen bir kullanılabilirlik kümesinde yalnızca, yönetilen disklere sahip VM’ler oluşturulabilir. Yönetilen disk hata etki alanlarının sayısı bölgeye göre farklılık gösterir (bölge başına iki ya da üç yönetilen disk hata etki alanı).

### <a name="update-domains"></a>Güncelleme etki alanları
Güncelleme etki alanı, bakımdan geçirilebilen ya da aynı anda yeniden başlatılabilen bir temel alınan donanım mantıksal grubudur. Bir kullanılabilirlik kümesinde VM’ler oluşturduğunuzda, Azure platformu VM’lerinizi bu güncelleme etki alanlarına otomatik olarak dağıtır. Bu yaklaşım, Azure platformu periyodik bakımdan geçirilirken uygulamanızın en az bir örneğinin her zaman çalışır durumda kalmasını sağlar. Yeniden başlatılmakta olan güncelleme etki alanlarının sırası, planlanan bakım sırasında sıralı olarak uygulanmayabilir, ancak aynı anda yalnızca bir güncelleme etki alanı yeniden başlatılır.

## <a name="next-steps"></a>Sonraki adımlar
Azure ortamınızı oluşturmak için bu kullanılabilirlik ve yedeklilik özelliklerini kullanmaya başlayabilirsiniz. En iyi uygulama bilgileri için bkz. [Azure kullanılabilirlik en iyi uygulamaları](../articles/best-practices-availability-checklist.md).

