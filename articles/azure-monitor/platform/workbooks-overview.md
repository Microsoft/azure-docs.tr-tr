---
title: Azure Izleyici çalışma kitapları ile etkileşimli raporlar oluşturma | Microsoft docs
description: Önceden oluşturulmuş ve özel parametreli çalışma kitapları ile karmaşık raporlamayı kolaylaştırın
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.service: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2cbc128e59472e36e2b5685efdf4cd02144edf15
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165933"
---
# <a name="azure-monitor-workbooks"></a>Azure Izleyici çalışma kitapları

Çalışma kitapları, veri analizi için esnek bir tuval ve Azure portal içinde zengin görsel raporların oluşturulmasını sağlar. Azure 'da birden çok veri kaynağına dokunmanıza ve bunları Birleşik etkileşimli deneyimler halinde birleştirmeye olanak tanır. 

## <a name="data-sources"></a>Veri kaynakları

Çalışma kitapları, Azure 'daki birden fazla kaynaktaki verileri sorgulayabilir. Çalışma kitaplarının yazarları, temel bileşenlerin kullanılabilirliği, performansı, kullanımı ve genel sistem durumu hakkında Öngörüler sağlamak için bu verileri dönüştürebilir. Örneğin, yüksek CPU veya düşük bellek örneklerini belirlemek ve sonuçları etkileşimli bir raporda kılavuz olarak görüntülemek için sanal makinelerden performans günlüklerini analiz edin.
  
Ancak çalışma kitaplarının gerçek gücü, farklı kaynaklardaki verileri tek bir raporda birleştirebilme özelliğidir. Bu, daha zengin veri ve öngörüleri olanaklı hale getirecek şekilde kaynak genelinde bileşik kaynak görünümleri veya birleştirmeleri oluşturulmasına olanak sağlar.

Çalışma kitapları şu anda aşağıdaki veri kaynaklarıyla uyumludur:

* [Günlükler](workbooks-data-sources.md#logs)
* [Ölçümler](workbooks-data-sources.md#metrics)
* [Azure Kaynak Grafiği](workbooks-data-sources.md#azure-resource-graph)
* [Uyarılar (Önizleme)](workbooks-data-sources.md#alerts-preview)
* [İş yükü sistem durumu (Önizleme)](workbooks-data-sources.md#workload-health-preview)
* [Azure Kaynak Durumu (Önizleme)](workbooks-data-sources.md#azure-resource-health)
* [Azure Veri Gezgini (Önizleme)](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>Görsel öğeler

Çalışma kitapları, verilerinizi görselleştirmeye yönelik zengin bir özellik kümesi sağlar. Her görselleştirme türünün ayrıntılı örnekleri için aşağıdaki örnek bağlantılara başvurabilirsiniz:

* [Metin](workbooks-visualizations.md#text)
* [Grafik](workbooks-visualizations.md#charts)
* [Larına](workbooks-visualizations.md#grids)
* [Parça](workbooks-visualizations.md#tiles)
* [Ağaçlarında](workbooks-visualizations.md#trees)
* [Performansının](workbooks-visualizations.md#graphs)

![Örnek çalışma kitabı görselleştirmeleri](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Başlangıç

Çalışma kitapları deneyimini araştırmak için öncelikle Azure Izleyici hizmetine gidin. Bu, Azure portal arama kutusuna **izleyici** yazılarak yapılabilir.

Sonra **çalışma kitapları (Önizleme)** seçeneğini belirleyin.

![Kırmızı kutuda vurgulanan çalışma kitapları önizleme düğmesinin ekran görüntüsü](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>Galeri

Bu sizi çalışma kitapları galerisine götürür:

![Azure Izleyici çalışma kitapları Galeri görünümünün ekran görüntüsü](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Çalışma kitapları ve çalışma kitabı şablonları

Bir _çalışma kitabını_ yeşil renkte ve çok sayıda _çalışma kitabı şablonunu_ mor olarak görebilirsiniz. Şablonlar, birden çok kullanıcı ve takımlar tarafından esnek yeniden kullanım için tasarlanan, seçkin raporlar olarak görev yapar. Bir şablonu açmak, şablonun içeriğiyle doldurulmuş geçici bir çalışma kitabı oluşturur. 

İş arkadaşları için gelecekteki raporlama deneyimini bozmadan, şablon tabanlı çalışma kitabının parametrelerini ayarlayabilir ve analiz gerçekleştirebilirsiniz. Bir şablon açarsanız, bazı ayarlamalar yapar ve ardından Kaydet simgesini seçerseniz, şablonu orijinal şablondan dokunulmadan önce yeşil olarak gösterilecek bir çalışma kitabı olarak kaydedersiniz. 

Aynı zamanda şablonlar, kaydedilen çalışma kitaplarından de farklılık gösterir. Çalışma kitabının kaydedilmesi ilişkili bir Azure Resource Manager kaynağı oluşturur, ancak yalnızca bir şablonu açarken oluşturulan geçici çalışma kitabının kendisiyle ilişkili benzersiz bir kaynağı olmadığında oluşturulur. Çalışma kitaplarında erişim denetiminin nasıl yönetildiği hakkında daha fazla bilgi edinmek için, [çalışma kitapları erişim denetimi makalesine](workbooks-access-control.md)başvurun.

### <a name="exploring-a-workbook-template"></a>Çalışma kitabı şablonunu keşfetme

Varsayılan uygulama çalışma kitabı şablonlarından birini görmek için **uygulama hata analizi** ' ni seçin.

![Uygulama hatası analiz şablonunun ekran görüntüsü](./media/workbooks-overview/failure-analysis.png)

Daha önce belirtildiği gibi, şablonu açmak, ile etkileşime girebilmeniz için geçici bir çalışma kitabı oluşturur. Çalışma kitabı, varsayılan olarak, yalnızca özgün şablon yazarı tarafından oluşturulan amaçlanan çözümleme deneyiminin bilgilerini görüntüleyen okuma modunda açılır.

Bu çalışma kitabı söz konusu olduğunda, deneyim etkileşimli olur. Bir abonelik, hedeflenen uygulamalar ve göstermek istediğiniz verilerin zaman aralığını ayarlayabilirsiniz. Bu seçimleri yaptıktan sonra, tek bir satırın seçilmesi, raporun alt kısmındaki iki grafikte hangi verilerin işleneceğini değiştirecek bir de etkileşimli olur.

### <a name="editing-mode"></a>Düzen modu

Bu çalışma kitabı şablonunun nasıl bir araya yerleştirileceğini anlamak için, **Düzenle**' ye tıklayarak düzenleme moduna geçiş yapmanız gerekir. 

![Uygulama hatası analiz şablonunun ekran görüntüsü](./media/workbooks-overview/edit.png)

Düzenleme moduna geçtikten sonra, çalışma kitabınızın her bir yönüyle ilgili olarak doğru bir dizi **düzenleme** kutusu göründüğünü fark edersiniz.

![Düzenleme düğmesinin ekran görüntüsü](./media/workbooks-overview/edit-mode.png)

İstek verileri kılavuzunun hemen altındaki Düzenle düğmesini seçmemiz durumunda, çalışma kitabımızın bu bölümünün Application Insights bir kaynaktaki verilere karşı bir kusto sorgusundan oluştuğunu görebiliriz.

![Temel kusto sorgusunun ekran görüntüsü](./media/workbooks-overview/kusto.png)

Sağ taraftaki diğer **düzenleme** düğmelerine tıkladığınızda, markaşağı tabanlı [metin kutuları](workbooks-visualizations.md#text), [parametre seçimi](workbooks-parameters.md) Kullanıcı arabirimi öğeleri ve diğer [grafik/görselleştirme türleri](workbooks-visualizations.md)gibi çalışma kitaplarını oluşturan çekirdek bileşenlerin sayısı görüntülenir. 

Önceden oluşturulmuş şablonları düzenleme modunda inceleyerek, bunları gereksinimlerinize uyacak şekilde değiştirerek ve kendi özel çalışma kitabınızı kaydederek Azure Izleyici çalışma kitaplarında mümkün olan şeyler hakkında bilgi edinmek için harika bir yoldur.

## <a name="pinning-visualizations"></a>Görselleştirmeleri sabitleme

Çalışma kitabındaki metin, sorgu ve ölçüm adımları, çalışma kitabı PIN modundayken veya çalışma kitabı yazarı PIN simgesini görünür hale getirmek üzere bu öğe için ayarları etkinleştirmişse, bu öğelerin sabitleme düğmesi kullanılarak sabitlenebilir. 

PIN moduna erişmek için, düzenleme moduna girmek üzere **Düzenle** ' ye tıklayın ve üstteki çubukta mavi pin simgesini seçin. Her bir pin simgesi, ekranın sağ tarafındaki her ilgili çalışma kitabı bölümünün *düzenleme* kutusunun üstünde görünür.

![PIN deneyimi](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> Çalışma kitabının durumu, PIN sırasında kaydedilir ve temeldeki çalışma kitabı değiştirilirse bir panodaki sabitlenmiş çalışma kitapları güncelleştirmeyecektir. Sabitlenmiş bir çalışma kitabı bölümünü güncelleştirmek için bu bölümü silip yeniden sabitlemek zorunda olursunuz.

## <a name="dashboard-time-ranges"></a>Pano zaman aralıkları

Sabitlenmiş öğe bir *zaman aralığı* parametresi kullanacak şekilde yapılandırıldıysa, sabitlenmiş çalışma kitabı sorgu parçaları panonun zaman aralığına göre değişir. Panonun zaman aralığı değeri, zaman aralığı parametresinin değeri olarak kullanılır ve Pano zaman aralığının herhangi bir değişikliği sabitlenmiş öğenin güncelleştirilmesine neden olur. Sabitlenmiş bir bölüm panonun zaman aralığını kullanıyorsa, zaman aralığı her değiştiğinde panonun zaman aralığını göstermek için sabitlenmiş bölüm güncelleştirmesinin alt başlığını görürsünüz. 

Ayrıca, bir zaman aralığı parametresi kullanan sabitlenmiş çalışma kitabı parçaları, panonun zaman aralığı tarafından belirlenen bir hızda otomatik olarak yenilenir. Sorgunun çalıştırıldığı son zaman sabitlenmiş parçanın alt başlığı içinde görüntülenir.

Sabitlenmiş bir adımda açıkça ayarlanmış bir zaman aralığı varsa (bir zaman aralığı parametresi kullanılmaz), pano ayarlarından bağımsız olarak bu zaman aralığı Pano için her zaman kullanılır. Sabitlenmiş parçanın alt başlığı panonun zaman aralığını göstermez ve sorgu, panoda otomatik olarak yenilemeyecektir. Alt başlık, sorgunun son çalıştırıldığı zamanı gösterecektir.

> [!NOTE]
> *Birleştirme* veri kaynağını kullanan sorgular, panolara sabitlenmesi için şu anda desteklenmiyor.

## <a name="sharing-workbook-templates"></a>Çalışma kitabı şablonlarını paylaşma

Kendi çalışma kitabı şablonlarınızı oluşturmaya başladıktan sonra, bunu daha geniş bir topluluk ile paylaşmak isteyebilirsiniz. Daha fazla bilgi edinmek ve varsayılan Azure Izleyici Galerisi görünümünün parçası olmayan diğer şablonları araştırmak için [GitHub deponuzu](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)ziyaret edin. Mevcut çalışma kitaplarına gitmek için GitHub 'daki [çalışma kitabı kitaplığını](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) ziyaret edin.

## <a name="next-step"></a>Sonraki adım

* Çok sayıda zengin görselleştirmeler seçeneği hakkında daha fazla [bilgi edinmeye başlayın](workbooks-visualizations.md) .
* Çalışma kitabı kaynaklarınıza erişimi [denetleme](workbooks-access-control.md) ve paylaşma.
