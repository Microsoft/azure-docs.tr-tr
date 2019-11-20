---
title: Azure Marketi için Bulut İş Ortağı Portalı sanal makine SKU 'Ları sekmesi
description: Azure Marketi 'nde bir sanal makine teklifi oluşturmak için kullanılan SKU 'Ların sekmesini açıklar.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 101f928e296d0b48b4e077676393772829aa76ab
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808978"
---
# <a name="virtual-machine-skus-tab"></a>Sanal makine SKU 'Ları sekmesi

**Yeni teklif** sayfasının **SKU 'ları** sekmesi bir veya daha fazla SKU oluşturmanıza ve bunları yeni teklifinizle ilişkilendirmenize olanak sağlar.  Farklı SKU 'Lar bir çözümü Özellik kümelerine, VM görüntü türlerine, aktarım hızına veya ölçeklenebilirlik, faturalandırma modellerine veya başka bir özelliğe göre ayırt edebilir.


## <a name="create-a-sku"></a>SKU oluşturma

Başlangıçta, yeni bir teklifin ilişkili SKU 'Ları yoktur, bu nedenle **yenı SKU**' ya tıklayarak bir tane oluşturacaksınız.

![Sanal makineler için yeni teklif sekmesindeki yeni SKU düğmesi](./media/publishvm_005.png)

<br/>

**Yenı SKU** iletişim kutusu görüntülenir.  Yeni SKU için tanımlayıcıyı girip **Tamam**' a tıklayın. (Tanımlayıcı adlandırma kuralları için aşağıya bakın.)  **SKU 'lar** sekmesinde artık düzenlenecek alanlar görüntülenir.    Alan adı üzerinde eklenen bir yıldız işareti (*) gerekli olduğunu gösterir.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Sanal makineler için yeni teklif formundaki SKU sekmesi](./media/publishvm_006.png)

Aşağıdaki tabloda bu alanların amacı, içeriği ve biçimlendirmesi açıklanmaktadır.  Gerekli alanlar bir yıldız işareti (*) ile tanımlanır.

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Alan**       |     **Açıklama**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU ayarları*   |    |
| **SKU KIMLIĞI\***       | Bu SKU için tanımlayıcı.  Bu ad, küçük harfli alfasayısal karakterler veya tireler (-) içeren en fazla 50 karakter içerir, ancak kısa çizgi ile bitemez.  Teklif yayımlandıktan sonra değiştirilemez.  |
|  *SKU ayrıntıları*   |  |
| **Başlık\***        | Market 'te görüntülenmek üzere teklif için kolay ad. En fazla 50 karakter uzunluğunda. |
| **Özet\***      | Market 'te görüntüleme teklifinin kısa açıklaması. En fazla 100 karakter uzunluğunda. |
| **Açıklama\***  | Teklifin daha ayrıntılı bir açıklamasını sağlayan açıklama metni.  <!-- TD: max len/guidance? 3k characters -->  |
| **Bu SKU 'YU Gizle\*** | SKU 'nun Market 'te müşterilere görünür olup olmayacağını gösterir.  Yalnızca çözüm şablonları aracılığıyla kullanılabilir olmasını istiyorsanız SKU 'YU gizlemek isteyebilirsiniz.  Bu, ilk test veya geçici ya da mevsimsel teklifler için de yararlı olabilir. |
| **Bulut kullanılabilirliği\*** | SKU 'nun hangi bulutta kullanılabilir olacağını belirler.  Varsayılan değer Azure 'un genel sürümüdür.  Microsoft Azure Kamu, ABD Federal, eyalet, yerel veya kaş kamu kurumları ve bunların sertifikalı iş ortakları için denetimli erişime sahip bir Devlet kurumunun bulutuna sahiptir.  Kamu Bulutu hakkında daha fazla bilgi için bkz. [Azure Kamu 'Ya hoş geldiniz](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Bu özel bir SKU mı?\*** | SKU 'nun özel mi yoksa genel mi olduğunu gösterir. Varsayılan **değer (genel) değildir.**  Daha fazla bilgi için bkz. [ortak ve özel SKU 'lar](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Ülke/bölge kullanılabilirliği\*** | SKU 'nuzun satın alma için hangi ülkelerin veya dünya bölgelerinin kullanılabilir olacağını belirler. En az bir bölge/ülke seçin. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Fiyatlandırma*   |  |
| **Lisans modeli\***| Kullanılacak standart faturalandırma modeli.  **Kullanım tabanlı aylık FATURALANDıRıLAN SKU**' yı seçerseniz, çekirdek başına fiyatlandırma ayrıntılarını ve ücretsiz deneme süresi sunmak isteyip istemediğinizi belirtmenizi sağlayan bir Accordion bölümü açılır.  Bu bölüm ayrıca, bu fiyatlandırma zamanlamasını Excel 'e aktarıp içeri aktarmanızı sağlar. Daha fazla bilgi için bkz. [Azure Marketi 'Ndeki faturalandırma seçenekleri](../../billing-options-azure-marketplace.md). | 
|  *VM görüntüleri*   |  |
| **İşletim sistemi ailesi\*** | Çözüm VM 'sinin Windows veya Linux tabanlı olup olmadığını gösterir. |
| **Işletim sistemi türünü seçin** | Belirtilen işletim sisteminin belirli bir satıcısı veya sürümü. |
| **OS kolay adı\*** | Müşterilere görüntülenecek işletim sistemi adı.  |
| **Önerilen VM boyutları\*** | Standartlaştırılmış bir listeden en fazla altı adet önerilen VM boyutu seçimini sağlar.  Bu liste Azure portal ve Microsoft marketlerine iletilir.  Bu listedeki geçerli olan ilk sanal makine boyutu (söz konusu müşteri aboneliği, bölge, bölge vb.), bu potansiyel müşteri için varsayılan olarak ayarlanır.  Kullanıcı bu boyutu çözüm görüntüsüyle uyumlu olacak şekilde değiştirebilir. | 
| **Açık bağlantı noktaları**| Açık bağlantı noktaları ve SKU için destekedilecek protokol.  Bu yapılandırmaların, çözüm VM ağı için yapılandırdığınız sanal ağla eşleşmesi gerekir. Bu ayarlar VM dağıtımı sırasında devreye girer. Ancak, bir SKU yayımladıktan sonra bağlantı noktası ayarları değiştirilebilir. Daha fazla bilgi için bkz. [Azure Portal ile bağlantı noktalarını bir sanal makineye açma](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Aşağıdaki varsayılan ağ eşlemeleri tüm VM 'lere eklenir. &emsp; Windows: 3389-> 3389 TCP, 5986-> 5986 TCP; Linux &emsp;: 22-> 22, TCP (SSH). |
| **Disk sürümü**  | Disk sürüm numarası ve disk URL 'SI tarafından belirtilen ilişkili çözüm sanal makinesi. Disk sürümü [anlam sürümü](https://semver.org/) biçiminde olmalıdır: `<major>.<minor>.<patch>`.  URL, işletim sistemi VHD 'si için oluşturulan paylaşılan erişim imzası URI 'sidir.  SKU başına en fazla sekiz disk sürümü ekleyebilirsiniz, ancak Azure Marketi 'nde yalnızca bir SKU için en yüksek disk sürüm numarası görünür. Diğer sürümler yalnızca API 'Ler aracılığıyla görünür olur.  <!--TD: Add more specific link to API --> <br/> **Yeni Data Disk** Accordion bölümü, sanal makinenize en fazla 15 veri diski eklemenize olanak sağlar.  Belirli bir VM sürümü ve ilişkili veri disklerine sahip bir SKU yayımladığınızda, bu yapılandırma değiştirilemez.  SKU 'ya ek VM sürümleri eklenirse, aynı zamanda aynı sayıda veri diski de desteklemesi gerekir. <br/> Azure tabanlı VM görüntünüzü oluşturmadıysanız, daha sonra bu alanı Güncelleştir ekleyebilirsiniz.  İlişkili VM kaynağı oluşturma hakkında daha fazla bilgi için bkz. [VM teknik varlıkları oluşturma](./cpp-create-technical-assets.md)bölümü.  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> İlerlemenizi kaydetmek için **Kaydet** ' e tıklayın. Bir sonraki sekmede, teklifinizin [Test sürücüsünü](./cpp-test-drive-tab.md)destekleyip desteklemediğini belirtirsiniz.


## <a name="additional-pricing-considerations"></a>Ek fiyatlandırma konuları

Yukarıda açıklanan fiyatlandırma modeli temel bir açıklamadır.  Değişiklikler yaşıyor ve yerel ya da bölgesel vergi yönetmelikleri ve Microsoft fiyatlandırma ilkeleri bundan etkilenebilir. 

### <a name="new-core-sizes-added-on-722019"></a>7/2/2019 üzerine yeni temel boyutlar eklendi

VM yayımcılarının, yeni Azure sanal makine boyutlarına (çekirdek sayısına göre) yeni fiyatlara ek olarak, 2 Temmuz 2019 tarihinde bilgilendirildi.  Yeni fiyatlar, 10, 44, 48, 60, 120, 208 ve 416 temel boyutlarına yöneliktir.  Mevcut VM için, bu çekirdek boyutları için yeni fiyatlar, geçerli fiyatlara göre otomatik olarak hesaplanır.  Yayımcılar, ek fiyatları gözden geçirmek ve istediğiniz değişiklikleri yapmak için 1 Ağustos 2019 ' e kadar.  Bu tarihten sonra, yayımcı tarafından zaten yeniden yayımlanmamışsa, bu yeni çekirdek boyutları için otomatik olarak hesaplanan fiyatlar geçerli olur.


### <a name="simplified-currency-pricing"></a>Basitleştirilmiş para birimi fiyatlandırması

1 2018 Eylül 'den itibaren, portala **Basitleştirilmiş para birimi fiyatlandırması** adlı yeni bir bölüm eklenecektir. Microsoft, müşterilerinizden dünya genelindeki daha öngörülebilir fiyatlandırma ve koleksiyonlar sağlayarak Azure Market işletmesini kolaylaştırıyor. Bu hızlandırma, müşterilerinize faturadığımız para birimi sayısını azaltmayı içerir.  Daha fazla bilgi için bkz. [Azure Marketi 'nde mevcut VM teklifini güncelleştirme](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Vergiler ve fiyatlar hakkında ek bilgiler

* Microsoft bazı ülkeleri/bölgeleri *vergi havalesi yapılmış ülkeler*olarak sınıflandırır.  Bu ülkelerde/bölgelerde Microsoft, müşterilerden gelen vergileri toplar ve kamu vergilerini ödeyen (remits).  Diğer ülkelerde/bölgelerde iş ortakları genellikle müşterilerinden vergiler toplamaktan ve kamu vergilerini kamu ödemesinden sorumludur. İkinci ülkelerde/bölgelerde satışı yapmayı seçerseniz, yerel vergileri hesaplama ve ödeme yeteneğinin olması gerekir.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Bir teklif canlı olduktan sonra fiyatlar değiştirilemez. Ancak, hala desteklenen bölgeler ekleyebilir veya kaldırabilirsiniz. 
* Microsoft, müşteri standart Azure VM kullanım ücretlerini planlı SKU ücretlerine ek olarak ücretlendirir.
* Fiyatlar, fiyat ayarlama sırasında kullanılabilir para birimi fiyatları üzerinden yerel para birimi cinsinden tüm bölgeler için ayarlanır.  <!-- TD: Meaning? - Offer created, published, other? -->
* Her bölgenin fiyatını ayrı ayrı ayarlamak için lütfen fiyatlandırma elektronik tablosunu dışa aktarın, özel fiyatlandırma uygulayın ve içeri aktarın. 


## <a name="next-steps"></a>Sonraki adımlar

İsteğe bağlı olarak, bu özelliği destekliyorsanız [test sürücüsü](./cpp-test-drive-tab.md) bilgilerini belirtirsiniz; Aksi takdirde, teklifiniz için [Market](./cpp-marketplace-tab.md) verileri sağlarsınız.
