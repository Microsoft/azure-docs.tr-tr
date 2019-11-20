---
title: Azure Marketi için Bulut İş Ortağı Portalı sanal makine marketi sekmesi
description: Azure Marketi VM teklifi oluşturma konusunda kullanılan Market sekmesini açıklar.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 9bc3c375c5111c144b6b137ca547875225aba58f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824461"
---
# <a name="virtual-machine-marketplace-tab"></a>Sanal makine marketi sekmesi

**Yeni teklif** sayfasının **Market** sekmesi, olası müşterilerinize pazarlama, satış ve yasal bilgiler ve anlaşmalar sağlamanıza ve Market 'ten oluşturulan müşteri adaylarını yönetmenize olanak sağlar. Bu uzun biçim dört bölüme ayrılmıştır: **genel bakış**, **Pazarlama yapıtları**, **müşteri adayı yönetimi**ve **yasal**.


## <a name="overview-section"></a>Genel bakış bölümü
Bu bölümde, Azure Market teklifiniz hakkında genel bilgileri girersiniz.  Alan adı üzerinde eklenen bir yıldız işareti (*) gerekli olduğunu gösterir.

![Sanal makineler için Market sekmesinin genel bakış bölümü](./media/publishvm_008.png)

Aşağıdaki tabloda bu alanların amacı ve içeriği açıklanmaktadır. Gerekli alanlar bir yıldız işareti (*) ile tanımlanır.

|  **Alan**                |     **Açıklama**                                                          |
|  ---------                |     ---------------                                                          |
| **Başlık\***                 | Teklifin başlığı, genellikle uzun, resmi adı. Bu başlık Market 'te göze çarpacak olarak görüntülenecektir.  En fazla 50 karakter uzunluğunda. |
| **Özet\***               | Çözüm için kısa bir amaç veya işlev.  En fazla 100 karakter uzunluğunda. |
| **Uzun Özet\***          | Çözüm amacı veya işlevi.  En fazla 256 karakter uzunluğunda. |
| **Açıklama\***           | Çözümün açıklaması.  En fazla 3000 karakter uzunluğunda basit HTML biçimlendirmesini destekler. |
| **Microsoft CSP satıcı kanalı\*** | Bulut çözümü sağlayıcıları (CSP) iş ortağı kanalı kabul etme artık kullanılabilir.  Teklifinizi Microsoft CSP iş ortağı kanalları aracılığıyla pazarlama hakkında daha fazla bilgi için lütfen bkz. [bulut çözümü sağlayıcıları](../../cloud-solution-providers.md) . |
| **Pazarlama tanımlayıcısı\***  | Bu teklifle ilişkilendirilecek benzersiz bir URL, genellikle kuruluşunuzun ve çözüm adı, maksimum uzunluk 50 karakter içerir.  Örneğin: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Abonelik kimliklerini önizleme\*** | Ön görüntüleyicilerin 100 abonelik tanımlayıcılarına bir tane ekleyin. Bu beyaz listeye alınan abonelikler, yayımlandıktan sonra, yayımlanmaya başladıktan sonra teklif erişimine sahip olur. |
| **Faydalı bağlantılar**          | Belgeleri, sürüm notlarını, SSS vb. belgelerine URL 'Ler ekleyin. |
| **Önerilen Kategoriler (en fazla 5)\*** | Teklifi olan çok seçimli iş ve teknik Kategoriler, ile en iyi şekilde ilişkilendirilebilir.  İzin verilen en fazla beş.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Pazarlama yapıtları bölümü

Bu ikinci bölüm üç alt bölüme bölünür: **logolar**, **ekran görüntüsü**ve **videolar**. Amblemler yalnızca gerekli olan pazarlama yapıtlarına yöneliktir, ancak en iyi müşteri için tüm müşteriler son derece önerilir. 

![Sanal makineler için yeni teklif formundaki Market sekmesinin pazarlama yapıları bölümü](./media/publishvm_009.png)

Aşağıdaki tabloda bu alanların amacı ve içeriği açıklanmaktadır. Gerekli alanlar bir yıldız işareti (*) ile tanımlanır.

|  **Alan**                |     **Açıklama**                                                          |
|  ---------                |     ---------------                                                          |
| *Ların*  |  |
| **Küçük\***                 | 40x40 pixel. ico bit eşlem                                                      |
| **Orta\***                | 90x90 pixel. ico bit eşlem                                                      |
| **Büyük\***                 | 115x115 pixel. ico bit eşlem                                                   |
| **Geniş\***                  | 255x115 pixel. ico bit eşlem                                                    |
| **Hero**                  | 815x290 bit eşlem.  İsteğe bağlı, ancak karşıya yüklendikten sonra Hero simgesi silinemez. |
| *Görüntülerini*  | İsteğe bağlı, ancak SKU başına en fazla beş ekran görüntüsü. |
| **Ad**                  | Ad veya başlık <!-- TODO - max char length? none specified in UI -->                               |
| **Görüntü**                 | Ekran yakalama görüntüsü, 533x324 piksel                                         |
| *Videolar*  |  |
| **Ad**                  | Ad veya başlık  <!-- TODO - max char length? -->                              |
| **Bağlantı**                  | YouTube veya Vimeo 'da barındırılan video URL 'SI                                        |
| **Resimler**             | 533x324 bit eşlem                                                               |
|   |   |

### <a name="logo-guidelines"></a>Logo yönergeleri

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Bulut İş Ortağı Portalı yüklenen tüm logolar, yönergeleri izlemelidir:

*  Azure tasarımının basit bir renk paleti vardır. Logonuz üzerinde birincil ve ikincil renk sayısını düşük tutun.
*  Azure portal Tema renkleri beyaz ve siyahtır. Bu nedenle, logolarınızın arka plan rengi olarak bu renkleri kullanmaktan kaçının. Logolarınızın Azure portal anlamlı hale getirmek için bir renk kullanın. Basit birincil renkleri öneririz. Saydam arka plan kullanıyorsanız, logoların/metnin beyaz veya siyah veya mavi olmadığından emin olun.
*  Logonuz üzerinde gradyan arka plan kullanmayın.
*  Logo üzerinde metin (şirketinizin veya marka adınız bile) yerleştirmekten kaçının. Logonuzun görünüm "düz" olmalıdır ve gradyanları kullanmaktan kaçınmalıdır.
*  Logoyu uzamayın.

#### <a name="hero-logo"></a>Kahraman logosu

Hero logosu isteğe bağlıdır; Ancak karşıya yüklendikten sonra Hero simgesi silinemez.  Hero logosu simgesi şu yönergeleri izlemelidir:

*  Hero simgeleri için siyah, beyaz ve saydam arka planlara izin verilmez.
*  Hero simgesinin arka planı olarak herhangi bir açık renk kullanmaktan kaçının.  Yayımcının görünen adı, plan başlığı ve teklif uzun Özeti beyaz yazı tipi renginde görüntülenir ve arka planda bir süre önce gelmelidir.
*  Hero logosunu tasarlarken çoğu metni kullanmaktan kaçının.  Yayımcı adı, plan başlığı, teklif uzun Özeti ve bir oluştur düğmesi, teklif listesi sırasında Hero simgesinin içine programlı bir şekilde katıştırılır. 
* Hero simgenin sağ tarafına, 415x100 piksel boyutunuzu ve soldan 370 piksel sapmasını dahil kullanılmamış bir dikdörtgen ekleyin.  

Örnek olarak, aşağıdaki Hero simgesi Azure Container Service içindir.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Azure Container Service için örnek Hero simgesi](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Pazarlama bilgileri örneği 

Aşağıdaki görüntüde, pazarlama bilgilerinin Microsoft Windows Server ana ürün sayfasında nasıl görüntülendiği gösterilmektedir.

![Microsoft Windows Server için örnek ürün sayfası](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Lider yönetimi bölümü

Üçüncü bölüm, Azure Market teklifinizden oluşturulan müşteri adaylarını toplamanıza olanak sağlar. Bu müşteri adayı bilgileri için aşağıdaki depolama seçeneklerini sunar (bir açılan listesinden).

* **Hiçbiri** -varsayılan, müşteri adayı bilgileri toplanmaz.
* Azure tablosu-bir bağlantı dizesi tarafından belirtilen Azure tablosuna yazıldı.
* Dynamics CRM Online-bir URL ve kimlik doğrulama kimlik bilgileriyle belirtilen [Microsoft Dynamics 365 online](https://dynamics.microsoft.com/) örneğine yazılır.
* HTTPS uç noktası-belirtilen HTTPS uç noktasına JSON yükü olarak yazılır.
* Marketo-belirtilen [Marketo](https://www.marketo.com/) örneğine yazılır, sunucu kimliği, bu kimlik ve form kimliği ile belirtilir.
* Salesforce-bir nesne tanımlayıcısıyla belirtilen [Salesforce](https://www.salesforce.com/) veritabanına yazılır.

Teklifinizi başarıyla yayımladıktan sonra, müşteri adayı bağlantısı onaylanır ve yapılandırılmış hedefe otomatik olarak bir test lideri gönderilir. Müşteri Yönetim mimarinizde her değişiklik yapıldığında, müşteri adayı bilgileri sürekli olarak yönetilmelidir ve bu ayarlar hemen güncel olmalıdır.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Yasal Bölüm

Bu son bölüm, her teklif için gereken yasal belgeleri sağlamanıza olanak sağlar.  

|  **Alan**                    |     **Açıklama**                                        |
|  ---------                    |     ---------------                                        |
| **Gizlilik Ilkesi URL 'SI\***      | Postalanan Gizlilik ilkenizin URL 'SI                          |
| **Standart Sözleşme mi kullanıyorsunuz?\***  |   |
| **Kullanım koşulları\***            | düz metin veya basit HTML olarak ilke.                       |
|  |  |


## <a name="next-steps"></a>Sonraki adımlar

Sonraki [destek](./cpp-support-tab.md) sekmesinde teklifiniz için teknik ve Kullanıcı Destek kaynakları sağlarsınız.
