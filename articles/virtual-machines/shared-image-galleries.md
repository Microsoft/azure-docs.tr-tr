---
title: Paylaşılan galerileriyle VM görüntülerini paylaşma
description: Kuruluşunuz genelinde Linux VM görüntülerini paylaşmak için paylaşılan görüntü galerilerini nasıl kullanacağınızı öğrenin.
author: axayjo
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 10/14/2020
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 32b4cf1555a2d0e074ae1551a5c0085f2758fa2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609150"
---
# <a name="shared-image-galleries-overview"></a>Paylaşılan görüntü galerilerine genel bakış

Paylaşılan Görüntü Galerisi, görüntüleriniz için bir yapı ve düzenleme oluşturmanıza yardımcı olan bir hizmettir. Paylaşılan görüntü galerileri şunları sağlar:

- Görüntülerin genel çoğaltması.
- Daha kolay yönetim için görüntülerin sürümü oluşturma ve gruplama.
- Kullanılabilirlik Alanları destekleyen bölgelerde bölge yedekli depolama (ZRS) hesaplarıyla yüksek düzeyde kullanılabilir görüntüler. ZRS, ZGen hatalarıyla karşı daha iyi esnekliği sunar.
- Premium depolama desteği (Premium_LRS).
- Azure RBAC kullanarak abonelikler arasında ve hatta Active Directory (AD) kiracılar arasında paylaşım yapın.
- Her bölgedeki görüntü çoğaltmalarıyla dağıtımlarınızı ölçeklendirin.

Paylaşılan görüntü galerisini kullanarak, görüntülerinizi kuruluşunuzdaki farklı kullanıcılara, hizmet sorumlularına veya AD gruplarına paylaşabilirsiniz. Paylaşılan görüntüler, dağıtımlarınızın daha hızlı ölçeklendirilmesi için birden çok bölgeye çoğaltılabilir.

Görüntü, nasıl oluşturulduğuna bağlı olarak tam VM 'nin (tüm bağlı veri diskleri dahil) veya yalnızca işletim sistemi diskinin bir kopyasıdır. Görüntüden bir VM oluşturduğunuzda, yeni VM 'nin disklerini oluşturmak için görüntüdeki VHD 'lerin bir kopyası kullanılır. Görüntü depolamada kalır ve yeni VM 'Ler oluşturmak için tekrar tekrar kullanılabilir.

Korumanız gereken çok sayıda görüntünüz varsa ve bunları şirketiniz genelinde kullanılabilir hale getirmek istiyorsanız, depo olarak paylaşılan bir görüntü galerisi kullanabilirsiniz. 

Paylaşılan görüntü Galerisi özelliği birden çok kaynak türüne sahiptir:

| Kaynak | Açıklama|
|----------|------------|
| **Görüntü kaynağı** | Bu, bir görüntü galerisinde **görüntü sürümü** oluşturmak için kullanılabilecek bir kaynaktır. Görüntü kaynağı, başka bir görüntü galerisinde [Genelleştirilmiş veya özelleştirilmiş](#generalized-and-specialized-images), yönetilen bir görüntü, anlık görüntü, VHD veya görüntü sürümü olan mevcut BIR Azure VM olabilir. |
| **Görüntü Galerisi** | Azure Marketi gibi bir **görüntü Galerisi** , görüntüleri yönetmek ve paylaşmak için bir depodur, ancak kimlerin erişimi olduğunu kontrol edersiniz. |
| **Görüntü tanımı** | Görüntü tanımları bir galeri içinde oluşturulur ve görüntü ve bu dosyayı dahili olarak kullanmaya yönelik gereksinimler hakkında bilgi taşır. Bu, görüntünün Windows veya Linux, sürüm notları ve en düşük ve en yüksek bellek gereksinimleri olduğunu içerir. Bu, bir görüntü türünün tanımıdır. |
| **Görüntü sürümü** | Bir **görüntü sürümü** , galerı kullanılırken VM oluşturmak için kullandığınız şeydir. Ortamınız için gerektiğinde bir görüntünün birden fazla sürümüne sahip olabilirsiniz. Yönetilen bir görüntü gibi, bir sanal makine oluşturmak için bir **görüntü sürümü** kullandığınızda, sanal makine için yeni diskler oluşturmak üzere görüntü sürümü kullanılır. Görüntü sürümleri birden çok kez kullanılabilir. |

<br>

![Galerinizdeki bir görüntünün birden fazla sürümünü nasıl kullanabileceğinizi gösteren grafik](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Görüntü tanımları

Görüntü tanımları bir görüntünün sürümleri için bir mantıksal gruplandırmadır. Görüntü tanımı, görüntünün neden oluşturulduğu, ne kadar işletim sistemi olduğu ve görüntünün kullanımıyla ilgili diğer bilgilerin yer aldığı bilgileri barındırır. Bir görüntü tanımı, belirli bir görüntü oluşturma ile ilgili tüm ayrıntılar için bir plan gibidir. Bir görüntü tanımından, ancak tanımdan oluşturulan görüntü sürümlerinden bir VM dağıtmazsınız.

Her görüntü tanımı için, kombinasyon- **Yayımcı**, **teklif** ve **SKU**'da kullanılan üç parametre vardır. Bunlar, belirli bir görüntü tanımını bulmak için kullanılır. Üç değerden birini veya ikisini birden paylaşan görüntü sürümlerine sahip olabilirsiniz.  Örneğin, aşağıda üç görüntü tanımı ve değerleri verilmiştir:

|Görüntü Tanımı|Publisher|Sunduğu|Sku|
|---|---|---|---|
|myImage1|Contoso|Finance|Arka uç|
|myImage2|Contoso|Finance|Ön uç|
|myImage3|Test Etme|Finance|Ön uç|

Bunların üçü de benzersiz değer kümelerine sahiptir. Bu biçim, bir market görüntüsünün en son sürümünü almak için Azure PowerShell ' de [Azure Market görüntüleri](./windows/cli-ps-findimage.md) için yayımcı, TEKLIF ve SKU 'yu nasıl belirteceğinize benzer. Her görüntü tanımının bu değerlerin benzersiz bir kümesine sahip olması gerekir.

Aşağıdaki parametreler hangi görüntü sürümü türlerini içerebilecekleri belirlenir:

- İşletim sistemi durumu-işletim sistemi durumunu [Genelleştirilmiş veya özelleştirilmiş](#generalized-and-specialized-images)olarak ayarlayabilirsiniz. Bu alan gereklidir.
- İşletim sistemi-Windows veya Linux olabilir. Bu alan gereklidir.
-   Hyper-V oluşturma-görüntünün 1. kuşak veya [2. nesil](generation-2.md) Hyper-V VHD 'sinden oluşturulup oluşturulmayacağını belirtin. Varsayılan 1. kuşak.


Aşağıda, kaynaklarınızı daha kolay izleyebilmek için görüntü tanımınızda ayarlanabilme diğer parametreler verilmiştir:

- Açıklama-görüntü tanımının neden mevcut olduğuna ilişkin daha ayrıntılı bilgi için açıklama kullanın. Örneğin, ön uç sunucunuz için uygulamanın önceden yüklenmiş olduğu bir görüntü tanımınız olabilir.
- EULA-görüntü tanımına özgü bir son kullanıcı lisans anlaşmasını işaret etmek için kullanılabilir.
- Gizlilik bildirimi ve sürüm notları-Azure Storage 'da sürüm notlarını ve gizlilik bildirimlerini depolayın ve görüntü tanımının bir parçası olarak bunlara erişmek için bir URI sağlayın.
- Yaşam bitiş tarihi-eski görüntü tanımlarını silmek için Otomasyonu kullanabilmeniz için görüntü tanımınıza son kullanım tarihi ekleyin.
- Etiket-görüntü tanımınızı oluştururken Etiketler ekleyebilirsiniz. Etiketler hakkında daha fazla bilgi için bkz. [kaynakları düzenlemek için etiketleri kullanma](../azure-resource-manager/management/tag-resources.md)
- En düşük ve en yüksek vCPU ve bellek önerileri-görüntünüz vCPU ve bellek önerileri içeriyorsa, bu bilgileri görüntü tanımınıza ekleyebilirsiniz.
- İzin verilmeyen disk türleri-sanal makinenizin depolama gereksinimleriyle ilgili bilgiler sağlayabilirsiniz. Örneğin, görüntü standart HDD disklerine uygun değilse, bunları izin vermeme listesine eklersiniz.
- Market görüntüleri için satın alma planı bilgileri- `-PurchasePlanPublisher` , `-PurchasePlanName` , ve `-PurchasePlanProduct` . Satın alma planı bilgileri hakkında daha fazla bilgi için bkz. [Azure Marketi 'nde görüntüleri bulma](./windows/cli-ps-findimage.md) ve [görüntü oluştururken Azure Marketi satın alma planı bilgilerini sağlama](marketplace-images.md).


## <a name="image-versions"></a>Görüntü sürümleri

Bir **görüntü sürümü** bir sanal makine oluşturmak için kullandığınız şeydir. Ortamınız için gerektiğinde bir görüntünün birden fazla sürümüne sahip olabilirsiniz. Bir sanal makine oluşturmak için **görüntü sürümü** kullandığınızda, sanal makine için yeni diskler oluşturmak üzere görüntü sürümü kullanılır. Görüntü sürümleri birden çok kez kullanılabilir.

Görüntü sürümünün özellikleri şunlardır:

- Sürüm numarası. Bu, görüntü sürümünün adı olarak kullanılır. Her zaman şu biçimdedir: MajorVersion. MinorVersion. Patch. Bir VM oluştururken **en son** kullanımı belirttiğinizde, en yüksek MajorVersion, sonra MinorVersion ve Patch temel alınarak en son görüntü seçilir. 
- Kaynaktaki. Kaynak bir VM, yönetilen disk, anlık görüntü, yönetilen görüntü veya başka bir görüntü sürümü olabilir. 
- En son dışında tut. Sürümün en son görüntü sürümü olarak kullanılmasını sağlayabilirsiniz. 
- Yaşam tarihi sonu. Bu görüntüden VM 'Lerin oluşturuoluşturuoluşturulamadığı tarih.


## <a name="generalized-and-specialized-images"></a>Genelleştirilmiş ve özelleştirilmiş görüntüler

Paylaşılan görüntü Galerisi tarafından desteklenen iki işletim sistemi durumu vardır. Genellikle görüntüler, görüntüyü oluşturmak için kullanılan VM 'nin görüntü alınmadan önce Genelleştirilmiş olmasını gerektirir. Genelleştirme, VM 'den makine ve kullanıcıya özgü bilgileri kaldıran bir işlemdir. Windows için Sysprep aracı kullanılır. Linux için [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` veya `-deprovision+user` Parameters kullanabilirsiniz.

Özel VM 'Ler makineye özgü bilgileri ve hesapları kaldırma işlemi boyunca değil. Ayrıca, özelleştirilmiş görüntülerden oluşturulan VM 'Ler `osProfile` bunlarla ilişkili değildir. Bu, özelleştirilmiş görüntülerin bazı avantajlara ek olarak bazı sınırlamalara sahip olacağı anlamına gelir.

- Özelleştirilmiş görüntülerden oluşturulan VM 'Ler ve ölçek kümeleri daha hızlı çalışıyor olabilir. İlk önyüklemede zaten olan bir kaynaktan oluşturulduğundan, bu görüntülerden oluşturulan VM 'Ler daha hızlı başlatılabilir.
- VM 'de oturum açmak için kullanılabilecek hesaplar, bu VM 'den oluşturulan özelleştirilmiş görüntü kullanılarak oluşturulan herhangi bir VM 'de de kullanılabilir.
- VM 'Ler, yansımanın alındığı VM 'nin **bilgisayar adına** sahip olur. Çakışmaları önlemek için bilgisayar adını değiştirmelisiniz.
- , `osProfile` Kullanılarak, bazı hassas bIlgIlerIn VM 'ye geçirilmesi `secrets` . Bu, Anahtar Kasası, WinRM ve ' de kullanılan diğer işlevleri kullanarak soruna neden olabilir `secrets` `osProfile` . Bazı durumlarda, bu sınırlamalara geçici çözüm bulmak için yönetilen hizmet kimliklerini (MSI) kullanabilirsiniz.

## <a name="regional-support"></a>Bölgesel destek

Tüm ortak bölgeler hedef bölge olabilir, ancak belirli bölgeler, müşterilerin erişim kazanmak için bir istek sürecinden gitmesini gerektirir. Avustralya Orta veya Avustralya Orta 2 gibi bir bölgenin izin verilenler listesine bir abonelik eklenmesini istemek için, [bir erişim isteği](/troubleshoot/azure/general/region-access-request-process) gönder

## <a name="limits"></a>Sınırlar 

Paylaşılan görüntü galerileri kullanarak kaynak dağıtmak için abonelik başına sınırlar vardır:
- 100 paylaşılan görüntü galerileri, her bölge için abonelik başına
- 1.000 görüntü tanımları, her bölge için abonelik başına
- 10.000 görüntü sürümü, her bölge için abonelik başına
- 10 görüntü sürümü çoğaltması, her bölge için abonelik başına
- Görüntüye bağlı herhangi bir disk, boyutu 1 TB 'tan küçük veya ona eşit olmalıdır

Daha fazla bilgi için bkz. geçerli kullanımınızı nasıl denetabileceğine ilişkin örnekler için [sınırlara karşı kaynak kullanımını denetleme](../networking/check-usage-against-limits.md) .
 
## <a name="scaling"></a>Ölçeklendirme
Paylaşılan görüntü Galerisi, Azure 'un görüntüleri tutmasını istediğiniz çoğaltma sayısını belirtmenize olanak tanır. Bu, sanal makine dağıtımları farklı çoğaltmalara yayılabilecek olduğundan, tek bir çoğaltmanın aşırı yüklemesi nedeniyle örnek oluşturma işleminin kısıtlanıyor olma olasılığını azaltmak için çoklu VM dağıtım senaryolarında yardımcı olur.

Paylaşılan görüntü Galerisi ile artık bir sanal makine ölçek kümesindeki bir 1.000 VM örneğine (yönetilen görüntülerle 600 ' den fazla) dağıtabilirsiniz. Görüntü çoğaltmaları daha iyi dağıtım performansı, güvenilirlik ve tutarlılık sağlar.  Bölgenin ölçek ihtiyaçlarına göre her bir hedef bölgede farklı bir çoğaltma sayısı ayarlayabilirsiniz. Her çoğaltma görüntünüzün derin bir kopyası olduğundan, bu, her bir ekstra çoğaltma ile dağıtımlarınızın ölçeğini daha erken ölçeklendirmenize yardımcı olur. İki görüntü veya bölgenin aynı olmadığını anladığımızda, bir bölgede çoğaltmaların nasıl kullanılacağına ilişkin genel kılavuzumuz aşağıda verilmiştir:

- Sanal olmayan makine ölçek kümesi dağıtımları için-eşzamanlı olarak oluşturduğunuz her 20 VM Için bir çoğaltma tutmanız önerilir. Örneğin, bir bölgedeki aynı görüntüyü kullanarak aynı anda 120 VM oluşturuyorsanız, resminizin en az 6 çoğaltmasını tutmanız önerilir. 
- Sanal makine ölçek kümesi dağıtımları için-en çok 600 örneğe sahip her ölçek kümesi dağıtımı Için en az bir çoğaltma tutmanız önerilir. Örneğin, her biri tek bir bölgedeki aynı görüntüyü kullanan 600 VM örneğiyle aynı anda 5 ölçek kümesi oluşturuyorsanız, resminizin en az 5 çoğaltmasını tutmanızı öneririz. 

Görüntü boyutu, içerik ve işletim sistemi türü gibi etkenlere bağlı olarak her zaman çoğaltma sayısını fazla temin etmenizi öneririz.

![Görüntüleri nasıl ölçeklendirekullanabileceğinizi gösteren grafik](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Görüntülerinizi yüksek oranda kullanılabilir yapma

[Azure bölgesel olarak yedekli depolama (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) , bölgedeki bir kullanılabilirlik bölgesi hatasına karşı esnekliği sağlar. Paylaşılan görüntü galerisinin genel kullanıma sunulmasıyla birlikte, görüntülerinizi Kullanılabilirlik Alanları bölgelerde ZRS hesaplarında depolamayı tercih edebilirsiniz. 

Hedef bölgelerin her biri için hesap türünü de seçebilirsiniz. Varsayılan depolama hesabı türü Standard_LRS, ancak Kullanılabilirlik Alanları olan bölgeler için Standard_ZRS seçebilirsiniz. ZRS 'nin bölgesel kullanılabilirliğini [burada](../storage/common/storage-redundancy.md)denetleyin.

![ZRS gösteren grafik](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Çoğaltma
Paylaşılan görüntü Galerisi, görüntülerinizi diğer Azure bölgelerine otomatik olarak çoğaltmanıza da olanak tanır. Her paylaşılan görüntü sürümü, kuruluşunuz için anlamlı hale göre farklı bölgelere çoğaltılabilir. Bir örnek, tüm eski sürümler yalnızca 1 bölgede kullanılabilir durumdayken çok bölgeli en son görüntüyü her zaman çoğaltmalıdır. Bu, paylaşılan görüntü sürümleri için depolama maliyetlerinde tasarruf etmenize yardımcı olabilir. 

Paylaşılan görüntü sürümünün çoğaltılacağı bölgeler, oluşturma zamanından sonra güncelleştirilebilirler. Farklı bölgelere çoğaltılmak için gereken süre, kopyalanan veri miktarına ve sürümün çoğaltılan bölge sayısına bağlıdır. Bu, bazı durumlarda birkaç saat sürebilir. Çoğaltma gerçekleşirken, her bölge için çoğaltmanın durumunu görüntüleyebilirsiniz. Bir bölgede görüntü çoğaltma tamamlandıktan sonra, bölgedeki bu görüntü sürümünü kullanarak bir VM veya ölçek kümesi dağıtabilirsiniz.

![Görüntüleri nasıl çoğaltacağınızı gösteren grafik](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Access

Paylaşılan görüntü Galerisi, görüntü tanımı ve görüntü sürümü tüm kaynaklar olduğundan, yerleşik yerel Azure RBAC denetimleri kullanılarak paylaşılabilir. Azure RBAC kullanarak bu kaynakları diğer kullanıcılar, hizmet sorumluları ve gruplar ile paylaşabilirsiniz. Hatta, içinde oluşturuldukları kiracı dışındaki bireylere erişim de paylaşabilirsiniz. Bir kullanıcının paylaşılan görüntü sürümüne erişimi olduktan sonra, bir VM veya bir sanal makine ölçek kümesi dağıtabilirler.  Kullanıcının ne erişimi olduğunu anlamanıza yardımcı olan paylaşım matrisi aşağıda verilmiştir:

| Kullanıcıyla paylaşıldı     | Paylaşılan Görüntü Galerisi | Görüntü Tanımı | Görüntü sürümü |
|----------------------|----------------------|--------------|----------------------|
| Paylaşılan Görüntü Galerisi | Yes                  | Yes          | Yes                  |
| Görüntü Tanımı     | Hayır                   | Yes          | Yes                  |

En iyi deneyim için Galeri düzeyinde paylaşım yapmanızı öneririz. Ayrı görüntü sürümlerinin paylaşılmasını önermiyoruz. Azure RBAC hakkında daha fazla bilgi için bkz. [Azure rolleri atama](../role-based-access-control/role-assignments-portal.md).

Görüntüler aynı zamanda çok kiracılı bir uygulama kaydı kullanan kiracılar arasında bile ölçeklenebilir şekilde paylaşılabilir. Kiracılar arasında görüntü paylaşma hakkında daha fazla bilgi için bkz. [Azure CLI](./linux/share-images-across-tenants.md) veya [PowerShell](./windows/share-images-across-tenants.md)kullanarak "Azure kiracılar genelinde Galeri VM görüntülerini paylaşma".

## <a name="billing"></a>Faturalandırma
Paylaşılan görüntü Galerisi hizmetinin kullanılması için ek ücret alınmaz. Aşağıdaki kaynaklar için ücretlendirilirsiniz:
-   Her çoğaltmayı depolamanın depolama maliyeti. Depolama maliyeti bir anlık görüntü olarak ücretlendirilir ve görüntü sürümünün çok fazla boyutunu, görüntü sürümünün çoğaltmaların sayısını ve sürümün çoğaltılacağı bölge sayısını temel alır. 
-   Kaynak bölgeden çoğaltılan bölgelere ilk görüntü sürümünün çoğaltılmasıyla ilgili ağ çıkış ücretleri. Sonraki çoğaltmalar bölge içinde işlenir, bu nedenle ek ücret alınmaz. 

Örneğin, yalnızca 10 GB 'lik bir işletim sistemi diski ve bir boş 32 GB veri diski kaplayan bir 127 görüntünüz olduğunu varsayalım. Her görüntünün bulunan boyutu yalnızca 10 GB olur. Görüntü 3 bölgeye çoğaltılır ve her bölgede iki çoğaltma bulunur. Her biri 10 GB kullanan altı toplam anlık görüntü olacaktır. Her anlık görüntü için, 10 GB 'lık boyutu temel alınarak depolama maliyeti ücretlendirilecektir. İlk çoğaltma için, ek iki bölgeye kopyalanacak olan ağ çıkış ücretleri ödersiniz. Her bölgedeki anlık görüntülerin fiyatlandırması hakkında daha fazla bilgi için bkz. [yönetilen diskler fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/). Ağ çıkışı hakkında daha fazla bilgi için bkz. [bant genişliği fiyatlandırması](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="updating-resources"></a>Kaynakları güncelleştirme

Oluşturulduktan sonra, görüntü Galerisi kaynaklarında bazı değişiklikler yapabilirsiniz. Bunlarla sınırlı:
 
Paylaşılan görüntü Galerisi:
- Description

Görüntü tanımı:
- Önerilen vCPU 'Lar
- Önerilen bellek
- Description
- Yaşam tarihi sonu

Görüntü sürümü:
- Bölgesel çoğaltma sayısı
- Hedef bölgeler
- En son dışında tut
- Yaşam tarihi sonu

## <a name="sdk-support"></a>SDK desteği

Aşağıdaki SDK 'lar paylaşılan görüntü galerileri oluşturmayı destekler:

- [.NET](/dotnet/api/overview/azure/virtualmachines/management)
- [Java](/java/azure/)
- [Node.js](/javascript/api/@azure/arm-compute)
- [Python](/python/api/overview/azure/virtualmachines)
- [Git](/azure/go/)

## <a name="templates"></a>Şablonlar

Şablonlar kullanarak paylaşılan görüntü Galerisi kaynağı oluşturabilirsiniz. Çeşitli Azure hızlı başlangıç şablonları mevcuttur: 

- [Paylaşılan Görüntü Galerisi Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Tanımı Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Sürümü Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Görüntü Sürümünden Sanal Makine Oluşturma](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Sık sorulan sorular 

* [Tüm paylaşılan görüntü Galerisi kaynaklarını abonelikler arasında nasıl listeleyebilirim?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Var olan görüntümi paylaşılan görüntü galerisine taşıyabilir miyim?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Özel bir diskten görüntü sürümü oluşturabilir miyim?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Paylaşılan görüntü Galerisi kaynağını oluşturulduktan sonra farklı bir aboneliğe taşıyabilir miyim?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Resim sürümlerimi Azure Çin 21Vianet veya Azure Almanya ya da Azure Kamu bulutu gibi bulutlar arasında çoğaltabilir miyim?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Görüntü sürümlerimi abonelikler arasında çoğaltabilir miyim?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Azure AD kiracılar arasında görüntü sürümlerini paylaşabilir miyim?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Hedef bölgeler arasında görüntü sürümlerini çoğaltmak ne kadar sürer?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Kaynak bölgesi ve hedef bölge arasındaki fark nedir?](#what-is-the-difference-between-source-region-and-target-region)
* [Görüntü sürümünü oluştururken kaynak bölgeyi belirtmek Nasıl yaparım? mı?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Nasıl yaparım? her bölgede oluşturulacak görüntü sürümü çoğaltmalarının sayısını belirtin.](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Paylaşılan görüntü galerisini görüntü tanımı ve görüntü sürümü için olandan farklı bir konumda oluşturabilir miyim?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Paylaşılan görüntü galerisini kullanma ücretleri nelerdir?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Paylaşılan görüntü Galerisi ve görüntü tanımı ve görüntü sürümü oluşturmak için hangi API sürümünü kullanmalıyım?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Görüntü sürümünden paylaşılan VM veya sanal makine ölçek kümesi oluşturmak için hangi API sürümünü kullanmalıyım?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [Paylaşılan görüntü Galerisi görüntülerini kullanmak için yönetilen görüntü kullanılarak oluşturulan sanal makine ölçek kümesini güncelleştirebilir miyim?](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Tüm paylaşılan görüntü Galerisi kaynaklarını abonelikler arasında nasıl listeleyebilirim?

Azure portal erişiminiz olan aboneliklerdeki tüm paylaşılan görüntü Galerisi kaynaklarını listelemek için aşağıdaki adımları izleyin:

1. [Azure portalını](https://portal.azure.com) açın.
1. Sayfayı aşağı kaydırın ve **tüm kaynaklar**' ı seçin.
1. Tüm kaynakları listelemek istediğiniz tüm abonelikleri seçin.
1. **Paylaşılan görüntü Galerisi** türündeki kaynakları arayın.
  
İzinleriniz olan aboneliklerdeki tüm paylaşılan görüntü Galerisi kaynaklarını listelemek için Azure CLı 'de aşağıdaki komutu kullanın:

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

Daha fazla bilgi için bkz. [Azure CLI](update-image-resources-cli.md) veya [PowerShell](update-image-resources-powershell.md)kullanarak **Galeri kaynaklarını yönetme** .

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Var olan görüntümi paylaşılan görüntü galerisine taşıyabilir miyim?
 
Evet. Sahip olduğunuz görüntü türlerine göre 3 senaryo vardır.

 Senaryo 1: yönetilen bir görüntünüz varsa, bundan sonra bir görüntü tanımı ve görüntü sürümü oluşturabilirsiniz. Daha fazla bilgi için bkz. [Azure CLI](image-version-managed-image-cli.md) veya [PowerShell](image-version-managed-image-powershell.md)kullanarak **yönetilen görüntüden görüntü sürümüne geçiş** .

 Senaryo 2: yönetilmeyen bir görüntünüz varsa, bundan yönetilen bir görüntü oluşturabilir ve bundan sonra bir görüntü tanımı ve görüntü sürümü oluşturabilirsiniz. 

 Senaryo 3: yerel dosya sisteminizde bir VHD 'niz varsa, VHD 'yi yönetilen bir görüntüye yüklemeniz gerekir, bundan sonra bir görüntü tanımı ve görüntü sürümü oluşturabilirsiniz.

- VHD bir Windows sanal makinesi ise bkz. [BIR VHD 'Yi karşıya yükleme](./windows/upload-generalized-managed.md).
- VHD bir Linux sanal makinesi için ise bkz. [BIR VHD 'Yi karşıya yükleme](./linux/upload-vhd.md#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Özel bir diskten görüntü sürümü oluşturabilir miyim?

Evet, [CLI](vm-specialized-image-version-cli.md), [PowerShell](vm-specialized-image-version-powershell.md)veya API kullanarak özelleştirilmiş görüntüden bir VM oluşturabilir. 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Paylaşılan görüntü Galerisi kaynağını oluşturulduktan sonra farklı bir aboneliğe taşıyabilir miyim?

Hayır, paylaşılan görüntü Galerisi kaynağını farklı bir aboneliğe taşıyamazsınız. Galerideki görüntü sürümlerini diğer bölgelere çoğaltabilir veya [Azure CLI](image-version-another-gallery-cli.md) veya [PowerShell](image-version-another-gallery-powershell.md)kullanarak başka bir galerinin görüntüsünü kopyalayabilirsiniz.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Resim sürümlerimi Azure Çin 21Vianet veya Azure Almanya ya da Azure Kamu bulutu gibi bulutlar arasında çoğaltabilir miyim?

Hayır, bulut genelinde görüntü sürümlerini çoğaltamaz.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Görüntü sürümlerimi abonelikler arasında çoğaltabilir miyim?

Hayır, bir abonelikteki bölgeler arasında görüntü sürümlerini çoğaltabilir ve RBAC aracılığıyla diğer aboneliklerde kullanabilirsiniz.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Azure AD kiracılar arasında görüntü sürümlerini paylaşabilir miyim? 

Evet, kiracılar genelinde bireyler paylaşmak için RBAC kullanabilirsiniz. Ancak, ölçekte paylaşmak için, [PowerShell](./windows/share-images-across-tenants.md) veya [CLI](./linux/share-images-across-tenants.md)kullanarak "Azure Kiracılarında Galeri görüntülerini paylaşma" konusuna bakın.

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Hedef bölgeler arasında görüntü sürümlerini çoğaltmak ne kadar sürer?

Görüntü sürümü çoğaltma süresi, görüntünün boyutuna ve çoğaltılan bölge sayısına bağlıdır. Bununla birlikte, en iyi uygulama olarak, görüntüyü küçük tutmanız ve kaynak ve hedef bölgelerin en iyi sonuçlar için kapalı olması önerilir. -ReplicationStatus bayrağını kullanarak çoğaltmanın durumunu kontrol edebilirsiniz.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Kaynak bölgesi ve hedef bölge arasındaki fark nedir?

Kaynak bölge, görüntü sürümünüzün oluşturulacağı bölgedir ve hedef bölgeler, görüntü sürümünüzün bir kopyasının depolanacağı bölgelerdir. Her görüntü sürümü için yalnızca bir kaynak bölgeniz olabilir. Ayrıca, bir görüntü sürümü oluştururken kaynak bölge konumunu hedef bölgelerden biri olarak geçirdiğinizden emin olun.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Görüntü sürümünü oluştururken kaynak bölgeyi belirtmek Nasıl yaparım? mı?

Görüntü sürümü oluştururken, kaynak bölgeyi belirtmek için PowerShell 'deki **--Location** etiketini ve **-Location** etiketini kullanabilirsiniz. Görüntü sürümünü oluşturmak için temel görüntü olarak kullandığınız yönetilen görüntünün, görüntü sürümünü oluşturmayı düşündüğünüz konumla aynı konumda olduğundan emin olun. Ayrıca, bir görüntü sürümü oluştururken kaynak bölge konumunu hedef bölgelerden biri olarak geçirdiğinizden emin olun.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Nasıl yaparım? her bölgede oluşturulacak görüntü sürümü çoğaltmalarının sayısını belirtin.

Her bölgede oluşturulacak görüntü sürümü çoğaltmalarının sayısını iki şekilde belirtebilirsiniz:
 
1. Bölge başına oluşturmak istediğiniz çoğaltmaların sayısını belirten bölgesel çoğaltma sayısı. 
2. Bölgesel çoğaltma sayısında bölge sayısı başına varsayılan olan ortak çoğaltma sayısı belirtilmemiş. 

Bölgesel çoğaltma sayısını belirtmek için, konumu bu bölgede oluşturmak istediğiniz çoğaltmaların sayısıyla birlikte geçirin: "Orta Güney ABD = 2". 

Bölgesel çoğaltma sayısı her konum ile belirtilmediyse, varsayılan çoğaltma sayısı belirttiğiniz ortak çoğaltma sayısı olacaktır. 

CLı 'de ortak çoğaltma sayısını belirtmek için, komutta **--Replica-Count** bağımsız değişkenini kullanın `az sig image-version create` .

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Paylaşılan görüntü galerisini görüntü tanımı ve görüntü sürümü için olandan farklı bir konumda oluşturabilir miyim?

Evet, olabilir. Ancak, en iyi uygulama olarak, kaynak grubu, paylaşılan görüntü Galerisi, görüntü tanımı ve görüntü sürümünü aynı konumda tutmanız önerilir.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Paylaşılan görüntü galerisini kullanma ücretleri nelerdir?

Görüntü sürümlerini depolama ücretleri ve kaynak bölgeden hedef bölgelere çoğaltma için ağ çıkış ücretleri dışında, paylaşılan görüntü Galerisi hizmeti kullanımı için herhangi bir ücret alınmaz.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Paylaşılan görüntü Galerisi ve görüntü tanımı ve görüntü sürümü oluşturmak için hangi API sürümünü kullanmalıyım?

Paylaşılan görüntü galerileri, görüntü tanımları ve görüntü sürümleriyle çalışmak için API sürüm 2018-06-01 ' i kullanmanızı öneririz. Bölge yedekli depolama (ZRS) sürüm 2019-03-01 veya üstünü gerektirir.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Görüntü sürümünden paylaşılan VM veya sanal makine ölçek kümesi oluşturmak için hangi API sürümünü kullanmalıyım?

Bir görüntü sürümü kullanan VM ve sanal makine ölçek kümesi dağıtımları için API sürüm 2018-04-01 veya üstünü kullanmanızı öneririz.

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>Paylaşılan görüntü Galerisi görüntülerini kullanmak için yönetilen görüntü kullanılarak oluşturulan sanal makine ölçek kümesini güncelleştirebilir miyim?

Evet, işletim sistemi türü, Hyper-V üretimi ve görüntüler arasında eşleşen veri diski düzeni olduğu sürece, yönetilen bir görüntüden bir paylaşılan görüntü Galerisi görüntüsüne ölçek kümesi görüntü başvurusunu güncelleştirebilirsiniz.

## <a name="troubleshoot-shared-image-gallery-issues"></a>Paylaşılan görüntü Galerisi sorunlarını giderme
Paylaşılan görüntü Galerisi kaynakları üzerinde herhangi bir işlem gerçekleştirmeye yönelik sorunlarla karşılaşırsanız, [sorun giderme kılavuzundaki](troubleshooting-shared-images.md)yaygın hataların listesine bakın.

Buna ek olarak, soru- `azure-virtual-machines-images` [cevap A&](/answers/topics/azure-virtual-machines-images.html), sorunuzu gönderebilirsiniz ve etiketleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure CLI](shared-images-cli.md) veya [PowerShell](shared-images-powershell.md)kullanarak paylaşılan görüntüleri dağıtmayı öğrenin.
