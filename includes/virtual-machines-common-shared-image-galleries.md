---
title: include dosyası
description: include dosyası
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: 4d64d556c96d29556ee36179623ff8cc24532b48
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085283"
---
Paylaşılan görüntü Galerisi, yönetilen görüntülerinizin etrafında yapı ve kuruluş oluşturmanıza yardımcı olan bir hizmettir. Paylaşılan görüntü galerileri şunları sağlar:

- Görüntülerin yönetilen genel çoğaltması.
- Daha kolay yönetim için görüntülerin sürümü oluşturma ve gruplama.
- Kullanılabilirlik Alanları destekleyen bölgelerde bölge yedekli depolama (ZRS) hesaplarıyla yüksek düzeyde kullanılabilir görüntüler. ZRS, bölgesel arızalara karşı daha iyi koruma sağlar.
- RBAC kullanarak abonelikler arasında ve hatta Active Directory (AD) kiracılar arasında paylaşım.
- Her bölgedeki görüntü çoğaltmalarıyla dağıtımlarınızı ölçeklendirin.

Paylaşılan görüntü galerisini kullanarak, görüntülerinizi kuruluşunuzdaki farklı kullanıcılara, hizmet sorumlularına veya AD gruplarına paylaşabilirsiniz. Paylaşılan görüntüler, dağıtımlarınızın daha hızlı ölçeklendirilmesi için birden çok bölgeye çoğaltılabilir.

Yönetilen görüntü, görüntüyü oluşturma yönteminize bağlı olarak tam VM 'nin (bağlı veri diskleri dahil) veya yalnızca işletim sistemi diskinin bir kopyasıdır. Görüntüden bir VM oluşturduğunuzda, yeni VM 'nin disklerini oluşturmak için görüntüdeki VHD 'lerin bir kopyası kullanılır. Yönetilen görüntü depolamada kalır ve yeni VM 'Ler oluşturmak için tekrar tekrar kullanılabilir.

Korumanız gereken çok sayıda yönetilen görüntünüz varsa ve bunları şirketiniz genelinde kullanılabilir hale getirmek istiyorsanız, paylaşılan bir görüntü galerisini bir depo olarak kullanarak görüntülerinizi paylaşmayı kolaylaştırır. 

Paylaşılan görüntü Galerisi özelliği birden çok kaynak türüne sahiptir:

| Kaynak | Açıklama|
|----------|------------|
| **Yönetilen görüntü** | Tek başına kullanılabilen veya bir görüntü galerisinde **görüntü sürümü** oluşturmak için kullanılan temel bir görüntü. Yönetilen görüntüler [Genelleştirilmiş](#generalized-and-specialized-images) VM 'lerden oluşturulur. Yönetilen görüntü, birden çok VM oluşturmak için kullanılabilen ve artık paylaşılan görüntü sürümleri oluşturmak için kullanılabilen özel bir VHD türüdür. |
| **Görüntüye** | Bir VHD 'nin **görüntü sürümü**oluşturmak için kullanılabilecek bir kopyası. Anlık görüntüler [özelleştirilmiş](#generalized-and-specialized-images) bir VM 'den alınabilir (Genelleştirilmiş olmayan bir şekilde) ve özel bir görüntü sürümü oluşturmak için tek başına veya veri disklerinin anlık görüntüleriyle birlikte kullanılır.
| **Görüntü Galerisi** | Azure Marketi gibi bir **görüntü Galerisi** , görüntüleri yönetmek ve paylaşmak için bir depodur, ancak kimlerin erişimi olduğunu kontrol edersiniz. |
| **Görüntü tanımı** | Görüntüler, bir galeri içinde tanımlanır ve bu görüntüyü kuruluşunuzda kullanmaya yönelik gereksinimler hakkında bilgi taşır. Görüntünün Genelleştirilmiş veya özel, işletim sistemi, minimum ve maksimum bellek gereksinimleri ve sürüm notları gibi bilgileri ekleyebilirsiniz. Bu, bir görüntü türünün tanımıdır. |
| **Görüntü sürümü** | Bir **görüntü sürümü** , galerı kullanılırken VM oluşturmak için kullandığınız şeydir. Ortamınız için gerektiğinde bir görüntünün birden fazla sürümüne sahip olabilirsiniz. Yönetilen bir görüntü gibi, bir sanal makine oluşturmak için bir **görüntü sürümü** kullandığınızda, sanal makine için yeni diskler oluşturmak üzere görüntü sürümü kullanılır. Görüntü sürümleri birden çok kez kullanılabilir. |

<br>

![Galerinizdeki bir görüntünün birden fazla sürümünü nasıl kullanabileceğinizi gösteren grafik](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Görüntü tanımları

Görüntü tanımları bir görüntünün sürümleri için bir mantıksal gruplandırmadır. Görüntü tanımı, görüntünün neden oluşturulduğu, ne kadar işletim sistemi olduğu ve görüntüyü kullanma hakkında bilgi içeren bilgileri barındırır. Bir görüntü tanımı, belirli bir görüntü oluşturma ile ilgili tüm ayrıntılar için bir plan gibidir. Bir görüntü tanımından, ancak tanımdan oluşturulan görüntü sürümünden bir VM dağıtmazsınız.

Her görüntü tanımı için, kombinasyon- **Yayımcı**, **teklif** ve **SKU**'da kullanılan üç parametre vardır. Bunlar, belirli bir görüntü tanımını bulmak için kullanılır. Üç değerden birini veya ikisini birden paylaşan görüntü sürümlerine sahip olabilirsiniz.  Örneğin, aşağıda üç görüntü tanımı ve değerleri verilmiştir:

|Görüntü Tanımı|Yayımcı|Sunduğu|Sku|
|---|---|---|---|
|myImage1|Contoso|Finans|Sunucusundan|
|myImage2|Contoso|Finans|Uçta|
|myImage3|Test Etme|Finans|Uçta|

Bunların üçü de benzersiz değer kümelerine sahiptir. Bu biçim, bir market görüntüsünün en son sürümünü almak için Azure PowerShell ' de [Azure Market görüntüleri](../articles/virtual-machines/windows/cli-ps-findimage.md) için yayımcı, TEKLIF ve SKU 'yu nasıl belirteceğinize benzer. Her görüntü tanımının bu değerlerin benzersiz bir kümesine sahip olması gerekir.

Aşağıda, kaynaklarınızı daha kolay izleyebilmek için görüntü tanımınızda ayarlanabilme diğer parametreler verilmiştir:

* İşletim sistemi durumu-işletim sistemi durumunu [Genelleştirilmiş veya özelleştirilmiş](#generalized-and-specialized-images)olarak ayarlayabilirsiniz.
* İşletim sistemi-Windows veya Linux olabilir.
* Açıklama-görüntü tanımının neden mevcut olduğuna ilişkin daha ayrıntılı bilgi için açıklama kullanın. Örneğin, ön uç sunucunuz için uygulamanın önceden yüklenmiş olduğu bir görüntü tanımınız olabilir.
* EULA-görüntü tanımına özgü bir son kullanıcı lisans anlaşmasını işaret etmek için kullanılabilir.
* Gizlilik bildirimi ve sürüm notları-Azure Storage 'da sürüm notlarını ve gizlilik bildirimlerini depolayın ve görüntü tanımının bir parçası olarak bunlara erişmek için bir URI sağlayın.
* Yaşam bitiş tarihi-eski görüntü tanımlarını silmek için Otomasyonu kullanabilmeniz için görüntü tanımınıza son kullanım tarihi ekleyin.
* Etiket-görüntü tanımınızı oluştururken Etiketler ekleyebilirsiniz. Etiketler hakkında daha fazla bilgi için bkz. [kaynakları düzenlemek için etiketleri kullanma](../articles/azure-resource-manager/resource-group-using-tags.md)
* En düşük ve en yüksek vCPU ve bellek önerileri-görüntünüz vCPU ve bellek önerileri içeriyorsa, bu bilgileri görüntü tanımınıza ekleyebilirsiniz.
* İzin verilmeyen disk türleri-sanal makinenizin depolama gereksinimleriyle ilgili bilgiler sağlayabilirsiniz. Örneğin, görüntü standart HDD disklerine uygun değilse, bunları izin vermeme listesine eklersiniz.

## <a name="generalized-and-specialized-images"></a>Genelleştirilmiş ve özelleştirilmiş görüntüler

Paylaşılan görüntü Galerisi tarafından desteklenen iki işletim sistemi durumu vardır. Genellikle görüntüler, görüntüyü oluşturmak için kullanılan VM 'nin görüntü alınmadan önce Genelleştirilmiş olmasını gerektirir. Genelleştirme, VM 'den makine ve kullanıcıya özgü bilgileri kaldıran bir işlemdir. Windows için, Sysprep de kullanılır. Linux için [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` veya `-deprovision+user` parametrelerini kullanabilirsiniz.

Özel VM 'Ler makineye özgü bilgileri ve hesapları kaldırma işlemi boyunca değil. Ayrıca, özelleştirilmiş görüntülerden oluşturulan VM 'lerle ilişkili bir `osProfile` yoktur. Bu, özelleştirilmiş görüntülerin bazı sınırlamalara sahip olacağı anlamına gelir.

- VM 'de oturum açmak için kullanılabilecek hesaplar, bu VM 'den oluşturulan özelleştirilmiş görüntü kullanılarak oluşturulan herhangi bir VM 'de de kullanılabilir.
- VM 'Ler, yansımanın alındığı VM 'nin **bilgisayar adına** sahip olur. Çakışmaları önlemek için bilgisayar adını değiştirmelisiniz.
- `osProfile`, `secrets`kullanılarak sanal makineye bazı hassas bilgilerin iletilme yöntemi olur. Bu, Anahtar Kasası, WinRM ve `osProfile``secrets` kullanan diğer işlevleri kullanarak soruna neden olabilir. Bazı durumlarda, bu sınırlamalara geçici çözüm bulmak için yönetilen hizmet kimliklerini (MSI) kullanabilirsiniz.

> [!IMPORTANT]
> Özel görüntüler şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Bilinen önizleme sınırlamaları** VM 'Ler yalnızca portal veya API kullanılarak özelleştirilmiş görüntülerden oluşturulabilir. , Önizleme için CLı veya PowerShell desteği yoktur.


## <a name="regional-support"></a>Bölgesel destek

Kaynak bölgeler aşağıdaki tabloda listelenmiştir. Tüm ortak bölgeler hedef bölge olabilir, ancak Avustralya Orta ve Avustralya Orta 2 çoğaltmak için aboneliğinizi beyaz listeye almanız gerekir. Beyaz liste istemek için şuraya gidin: https://azure.microsoft.com/global-infrastructure/australia/contact/


| Kaynak bölgeler        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Avustralya Orta     | Çin Doğu        | Güney Hindistan        | Batı Avrupa        |
| Avustralya Orta 2   | Çin Doğu 2      | Güneydoğu Asya     | Birleşik Krallık Güney           |
| Avustralya Doğu        | Çin Kuzey       | Japonya Doğu         | Birleşik Krallık Batı            |
| Avustralya Güneydoğu   | Çin Kuzey 2     | Japonya Batı         | US DoD Orta     |
| Güney Brezilya          | Doğu Asya         | Kore Orta      | US DoD Doğu        |
| Orta Kanada        | Doğu ABD           | Kore Güney        | ABD Devleti Arizona     |
| Doğu Kanada           | Doğu ABD 2         | Orta Kuzey ABD   | ABD Devleti Texas       |
| Orta Hindistan         | EUAP Doğu ABD 2    | Kuzey Avrupa       | ABD Devleti Virginia    |
| Orta ABD            | Fransa Orta    | Orta Güney ABD   | Batı Hindistan         |
| EUAP Orta ABD       | Fransa Güney      | Batı Orta ABD    | Batı ABD            |
|                       |                   |                    | Batı ABD 2          |



## <a name="limits"></a>Sınırlar 

Paylaşılan görüntü galerileri kullanarak kaynak dağıtmak için abonelik başına sınırlar vardır:
- 100 paylaşılan görüntü galerileri, her bölge için abonelik başına
- 1\.000 görüntü tanımları, her bölge için abonelik başına
- 10.000 görüntü sürümü, her bölge için abonelik başına

Daha fazla bilgi için bkz. geçerli kullanımınızı nasıl denetabileceğine ilişkin örnekler için [sınırlara karşı kaynak kullanımını denetleme](https://docs.microsoft.com/azure/networking/check-usage-against-limits) .
 
## <a name="scaling"></a>Ölçeklendirme
Paylaşılan görüntü Galerisi, Azure 'un görüntüleri tutmasını istediğiniz çoğaltma sayısını belirtmenize olanak tanır. Bu, sanal makine dağıtımları farklı çoğaltmalara yayılabilecek olduğundan, tek bir çoğaltmanın aşırı yüklemesi nedeniyle örnek oluşturma işleminin kısıtlanıyor olma olasılığını azaltmak için çoklu VM dağıtım senaryolarında yardımcı olur.

Paylaşılan görüntü Galerisi ile artık bir sanal makine ölçek kümesindeki bir 1.000 VM örneğine (yönetilen görüntülerle 600 ' den fazla) dağıtabilirsiniz. Görüntü çoğaltmaları daha iyi dağıtım performansı, güvenilirlik ve tutarlılık sağlar.  Bölgenin ölçek ihtiyaçlarına göre her bir hedef bölgede farklı bir çoğaltma sayısı ayarlayabilirsiniz. Her çoğaltma görüntünüzün derin bir kopyası olduğundan, bu, her bir ekstra çoğaltma ile dağıtımlarınızın ölçeğini daha erken ölçeklendirmenize yardımcı olur. İki görüntü veya bölgenin aynı olmadığını anladığımızda, bir bölgede çoğaltmaların nasıl kullanılacağına ilişkin genel kılavuzumuz aşağıda verilmiştir:

- Sanal olmayan makine ölçek kümesi (VMSS) dağıtımları için-eşzamanlı olarak oluşturduğunuz her 20 VM Için bir çoğaltma tutmanız önerilir. Örneğin, bir bölgedeki aynı görüntüyü kullanarak aynı anda 120 VM oluşturuyorsanız, resminizin en az 6 çoğaltmasını tutmanız önerilir. 
- Sanal makine ölçek kümesi (VMSS) dağıtımları için-en çok 600 örneğe sahip her ölçek kümesi dağıtımı Için en az bir çoğaltma tutmanız önerilir. Örneğin, her biri tek bir bölgedeki aynı görüntüyü kullanan 600 VM örneğiyle aynı anda 5 ölçek kümesi oluşturuyorsanız, resminizin en az 5 çoğaltmasını tutmanızı öneririz. 

Görüntü boyutu, içerik ve işletim sistemi türü gibi etkenlere bağlı olarak her zaman çoğaltma sayısını fazla temin etmenizi öneririz.

![Görüntüleri nasıl ölçeklendirekullanabileceğinizi gösteren grafik](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Görüntülerinizi yüksek oranda kullanılabilir yapma

[Azure bölgesel olarak yedekli depolama (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) , bölgedeki bir kullanılabilirlik bölgesi hatasına karşı esnekliği sağlar. Paylaşılan görüntü galerisinin genel kullanıma sunulmasıyla birlikte, görüntülerinizi Kullanılabilirlik Alanları bölgelerde ZRS hesaplarında depolamayı tercih edebilirsiniz. 

Hedef bölgelerin her biri için hesap türünü de seçebilirsiniz. Varsayılan depolama hesabı türü Standard_LRS, ancak Kullanılabilirlik Alanları olan bölgeler için Standard_ZRS seçebilirsiniz. ZRS 'nin bölgesel kullanılabilirliğini [burada](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)denetleyin.

![ZRS gösteren grafik](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Çoğaltma
Paylaşılan görüntü Galerisi, görüntülerinizi diğer Azure bölgelerine otomatik olarak çoğaltmanıza da olanak tanır. Her paylaşılan görüntü sürümü, kuruluşunuz için anlamlı hale göre farklı bölgelere çoğaltılabilir. Bir örnek, tüm eski sürümler yalnızca 1 bölgede kullanılabilir durumdayken çok bölgeli en son görüntüyü her zaman çoğaltmalıdır. Bu, paylaşılan görüntü sürümleri için depolama maliyetlerinde tasarruf etmenize yardımcı olabilir. 

Paylaşılan görüntü sürümünün çoğaltılacağı bölgeler, oluşturma zamanından sonra güncelleştirilebilirler. Farklı bölgelere çoğaltılmak için gereken süre, kopyalanan veri miktarına ve sürümün çoğaltılan bölge sayısına bağlıdır. Bu, bazı durumlarda birkaç saat sürebilir. Çoğaltma gerçekleşirken, her bölge için çoğaltmanın durumunu görüntüleyebilirsiniz. Bir bölgede görüntü çoğaltma tamamlandıktan sonra, bölgedeki bu görüntü sürümünü kullanarak bir VM veya ölçek kümesi dağıtabilirsiniz.

![Görüntüleri nasıl çoğaltacağınızı gösteren grafik](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Access

Paylaşılan görüntü Galerisi, görüntü tanımı ve görüntü sürümü tüm kaynaklar olduğundan, yerleşik yerel Azure RBAC denetimleri kullanılarak paylaşılabilir. RBAC kullanarak bu kaynakları diğer kullanıcılar, hizmet sorumluları ve gruplar ile paylaşabilirsiniz. Hatta, içinde oluşturuldukları kiracı dışındaki bireylere erişim de paylaşabilirsiniz. Bir kullanıcının paylaşılan görüntü sürümüne erişimi olduktan sonra, bir VM veya bir sanal makine ölçek kümesi dağıtabilirler.  Kullanıcının ne erişimi olduğunu anlamanıza yardımcı olan paylaşım matrisi aşağıda verilmiştir:

| Kullanıcıyla paylaşıldı     | Paylaşılan Görüntü Galerisi | Görüntü Tanımı | Görüntü sürümü |
|----------------------|----------------------|--------------|----------------------|
| Paylaşılan Görüntü Galerisi | Yes                  | Yes          | Yes                  |
| Görüntü Tanımı     | Hayır                   | Yes          | Yes                  |

En iyi deneyim için Galeri düzeyinde paylaşım yapmanızı öneririz. Ayrı görüntü sürümlerinin paylaşılmasını önermiyoruz. RBAC hakkında daha fazla bilgi için bkz. [RBAC kullanarak Azure kaynaklarına erişimi yönetme](../articles/role-based-access-control/role-assignments-portal.md).

Görüntüler aynı zamanda çok kiracılı bir uygulama kaydı kullanan kiracılar arasında bile ölçeklenebilir şekilde paylaşılabilir. Kiracılar arasında görüntü paylaşma hakkında daha fazla bilgi için bkz. [Azure kiracılar genelinde galerı VM görüntülerini paylaşma](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Faturalandırma
Paylaşılan Görüntü Galerisi hizmetini kullanırken ekstra ücret ödemezsiniz. Aşağıdaki kaynaklar için ücretlendirilirsiniz:
- Paylaşılan görüntü sürümlerini depolamanın depolama maliyeti. Maliyet, görüntü sürümünün yineleme sayısına ve sürümün çoğaltılacağı bölge sayısına bağlıdır. Örneğin, 2 görüntünüz varsa ve her ikisi de 3 bölgeye çoğaltılırsa, boyutları temelinde 6 yönetilen disk için değişiklik yapılır. Daha fazla bilgi için bkz. [yönetilen diskler fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/).
- Kaynak bölgeden çoğaltılan bölgelere ilk görüntü sürümünün çoğaltılmasıyla ilgili ağ çıkış ücretleri. Sonraki çoğaltmalar bölge içinde işlenir, bu nedenle ek ücret alınmaz. 

## <a name="updating-resources"></a>Kaynakları güncelleştirme

Oluşturulduktan sonra, görüntü Galerisi kaynaklarında bazı değişiklikler yapabilirsiniz. Bunlarla sınırlı:
 
Paylaşılan görüntü Galerisi:
- Açıklama

Görüntü tanımı:
- Önerilen vCPU 'Lar
- Önerilen bellek
- Açıklama
- Yaşam tarihi sonu

Görüntü sürümü:
- Bölgesel çoğaltma sayısı
- Hedef bölgeler
- En son dışında tut
- Yaşam tarihi sonu

## <a name="sdk-support"></a>SDK desteği

Aşağıdaki SDK 'lar paylaşılan görüntü galerileri oluşturmayı destekler:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Şablonlar

Şablonlar kullanarak paylaşılan görüntü Galerisi kaynağı oluşturabilirsiniz. Çeşitli Azure hızlı başlangıç şablonları mevcuttur: 

- [Paylaşılan görüntü galerisi oluşturma](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Paylaşılan görüntü galerisinde görüntü tanımı oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Paylaşılan görüntü galerisinde görüntü sürümü oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Görüntü sürümünden VM oluşturma](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

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

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Tüm paylaşılan görüntü Galerisi kaynaklarını abonelikler arasında nasıl listeleyebilirim?

Azure portal erişiminiz olan aboneliklerdeki tüm paylaşılan görüntü Galerisi kaynaklarını listelemek için aşağıdaki adımları izleyin:

1. [Azure portalı](https://portal.azure.com) açın.
1. **Tüm kaynaklara**gidin.
1. Tüm kaynakları listelemek istediğiniz tüm abonelikleri seçin.
1. **Özel Galeri**türündeki kaynakları arayın.
 
   Görüntü tanımlarını ve görüntü sürümlerini görmek için **gizli türleri göster**' i de seçmeniz gerekir.
 
   İzinleriniz olan aboneliklerdeki tüm paylaşılan görüntü Galerisi kaynaklarını listelemek için Azure CLı 'de aşağıdaki komutu kullanın:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Var olan görüntümi paylaşılan görüntü galerisine taşıyabilir miyim?
 
Evet. Sahip olduğunuz görüntü türlerine göre 3 senaryo vardır.

 Senaryo 1: yönetilen bir görüntünüz varsa, bundan sonra bir görüntü tanımı ve görüntü sürümü oluşturabilirsiniz.

 Senaryo 2: yönetilmeyen bir görüntünüz varsa, bundan yönetilen bir görüntü oluşturabilir ve bundan sonra bir görüntü tanımı ve görüntü sürümü oluşturabilirsiniz. 

 Senaryo 3: yerel dosya sisteminizde bir VHD 'niz varsa, VHD 'yi yönetilen bir görüntüye yüklemeniz gerekir, bundan sonra bir görüntü tanımı ve görüntü sürümü oluşturabilirsiniz.

- VHD bir Windows sanal makinesi ise bkz. [BIR VHD 'Yi karşıya yükleme](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- VHD bir Linux sanal makinesi için ise bkz. [BIR VHD 'Yi karşıya yükleme](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Özel bir diskten görüntü sürümü oluşturabilir miyim?

Evet, görüntü olarak özelleştirilmiş diskler için destek önizleme aşamasındadır. Yalnızca Portal ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) veya [LINUX](../articles/virtual-machines/linux/shared-images-portal.md)) ve API 'yi kullanarak özelleştirilmiş görüntüden bir VM oluşturabilirsiniz. Önizleme için PowerShell desteği yok.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Paylaşılan görüntü Galerisi kaynağını oluşturulduktan sonra farklı bir aboneliğe taşıyabilir miyim?

Hayır, paylaşılan görüntü Galerisi kaynağını farklı bir aboneliğe taşıyamazsınız. Bununla birlikte, galerideki görüntü sürümlerini, gereken şekilde diğer bölgelere çoğaltabileceksiniz.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Resim sürümlerimi Azure Çin 21Vianet veya Azure Almanya ya da Azure Kamu bulutu gibi bulutlar arasında çoğaltabilir miyim?

Hayır, bulut genelinde görüntü sürümlerini çoğaltamaz.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Görüntü sürümlerimi abonelikler arasında çoğaltabilir miyim?

Hayır, bir abonelikteki bölgeler arasında görüntü sürümlerini çoğaltabilir ve RBAC aracılığıyla diğer aboneliklerde kullanabilirsiniz.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Azure AD kiracılar arasında görüntü sürümlerini paylaşabilir miyim? 

Evet, kiracılar genelinde bireyler paylaşmak için RBAC kullanabilirsiniz. Ancak, ölçekte paylaşmak için, [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) veya [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md)kullanarak "Azure Kiracılarında Galeri görüntülerini paylaşma" konusuna bakın.

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

CLı 'de ortak çoğaltma sayısını belirtmek için `az sig image-version create` komutunda **--Replica-Count** bağımsız değişkenini kullanın.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Paylaşılan görüntü galerisini görüntü tanımı ve görüntü sürümü için olandan farklı bir konumda oluşturabilir miyim?

Evet, olabilir. Ancak, en iyi uygulama olarak, kaynak grubu, paylaşılan görüntü Galerisi, görüntü tanımı ve görüntü sürümünü aynı konumda tutmanız önerilir.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Paylaşılan görüntü galerisini kullanma ücretleri nelerdir?

Görüntü sürümlerini depolama ücretleri ve kaynak bölgeden hedef bölgelere çoğaltma için ağ çıkış ücretleri dışında, paylaşılan görüntü Galerisi hizmeti kullanımı için herhangi bir ücret alınmaz.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Paylaşılan görüntü Galerisi ve görüntü tanımı ve görüntü sürümü oluşturmak için hangi API sürümünü kullanmalıyım?

Paylaşılan görüntü galerileri, görüntü tanımları ve görüntü sürümleriyle çalışmak için API sürüm 2018-06-01 ' i kullanmanızı öneririz. Bölge yedekli depolama (ZRS) sürüm 2019-03-01 veya üstünü gerektirir.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Görüntü sürümünden paylaşılan VM veya sanal makine ölçek kümesi oluşturmak için hangi API sürümünü kullanmalıyım?

Bir görüntü sürümü kullanan VM ve sanal makine ölçek kümesi dağıtımları için API sürüm 2018-04-01 veya üstünü kullanmanızı öneririz.
