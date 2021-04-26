---
title: Eşleme veri akışı hata ayıklama modu
description: Veri akışları oluştururken etkileşimli bir hata ayıklama oturumu başlatın
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/16/2021
ms.openlocfilehash: 681a3643c04472cc42c1f672f4c9433da30e3955
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565511"
---
# <a name="mapping-data-flow-debug-mode"></a>Eşleme veri akışı hata ayıklama modu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Genel Bakış

Azure Data Factory eşleme veri akışının hata ayıklama modu, veri akışlarınızı oluştururken ve hata ayıkladığınızda veri şekli dönüşümünü etkileşimli bir şekilde izlemenize olanak sağlar. Hata ayıklama oturumu hem veri akışı tasarım oturumlarında hem de veri akışlarının işlem hattı hata ayıklamada yürütülmesi sırasında kullanılabilir. Hata ayıklama modunu açmak için, veri akışı aktivitenleriniz olduğunda veri akışı tuvalinin veya işlem hattı tuvalinin üst çubuğundaki **veri akışı hata ayıklama** düğmesini kullanın.

![Hata ayıklama kaydırıcısının nerede olduğunu gösteren ekran görüntüsü 1](media/data-flow/debug-button.png)

![Hata ayıklama kaydırıcısının nerede olduğunu gösteren ekran görüntüsü 2](media/data-flow/debug-button-4.png)

Kaydırıcıyı açtıktan sonra hangi tümleştirme çalışma zamanı yapılandırmasını kullanmak istediğinizi seçmeniz istenir. Oto Resolveıntegrationruntime seçilirse, varsayılan 60 dakikalık bir zamana kadar genel işlem içeren sekiz çekirdekli bir küme, ön uca olur. Oturumunuz zaman aşımına uğramadan önce daha fazla boşta takımına izin vermek isterseniz, daha yüksek bir TTL ayarı seçebilirsiniz. Veri akışı tümleştirme çalışma zamanları hakkında daha fazla bilgi için bkz. [veri akışı performansı](concepts-data-flow-performance.md#ir).

![IR seçiminde hata ayıkla](media/data-flow/debug-new-1.png "IR seçiminde hata ayıkla")

Hata ayıklama modu açık olduğunda, etkin bir Spark kümesi ile veri akışınızı etkileşimli olarak oluşturacaksınız. Azure Data Factory, hata ayıklamayı kapattıktan sonra oturum kapatılacak. Hata ayıklama oturumunun açık olduğu süre boyunca Azure Databricks tarafından tahakkuk edilen saatlik ücretlerden haberdar olmanız gerekir.

Çoğu durumda, iş mantığınızı doğrulayabilmeniz ve işinizi Azure Data Factory yayımlamadan önce veri dönüştürmelerinizi görüntüleyebilmeniz için veri akışlarınızı hata ayıklama modunda oluşturmak iyi bir uygulamadır. Veri akışınızı bir ardışık düzende test etmek için işlem hattı panelinde "hata ayıkla" düğmesini kullanın.

![Veri akışı hata ayıklama oturumlarını görüntüle](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

> [!NOTE]
> Kullanıcının ADF tarayıcı kullanıcı arabiriminden başlattığı her hata ayıklama oturumu, kendi Spark kümesi ile yeni bir oturumdur. Fabrika başına hata ayıklama oturumlarını görüntülemek ve yönetmek için yukarıdaki hata ayıklama oturumları için izleme görünümünü kullanabilirsiniz. Her bir hata ayıklama oturumunun, TTL saati dahil yürütüldüğü her saat için ücretlendirilirsiniz.

## <a name="cluster-status&quot;></a>Küme durumu

Küme hata ayıklamaya hazırlanışında tasarım yüzeyinin en üstündeki küme durumu göstergesi yeşil olur. Kümeniz zaten ısınma olursa yeşil gösterge neredeyse anında görünür. Hata ayıklama moduna girdiğinizde kümeniz zaten çalışmıyorsa, Spark kümesi soğuk bir önyükleme yapar. Bu gösterge, ortam etkileşimli hata ayıklama için hazırlanana kadar dönmesini sağlar.

Hata ayıklamanız ile işiniz bittiğinde, Spark kümenizin sonlanabilir olması için hata ayıklama anahtarını kapatın ve artık hata ayıklama etkinliği için faturalandırılırsınız.

## <a name=&quot;debug-settings&quot;></a>Hata ayıklama ayarları

Hata ayıklama modunu etkinleştirdikten sonra, veri akışı verilerinin nasıl önizlebildiğini düzenleyebilirsiniz. Hata ayıklama ayarları, veri akışı tuvali araç çubuğunda &quot;hata ayıklama ayarları&quot; seçeneğine tıklanarak düzenlenebilirler. Burada kaynak dönüşümlerinizin her biri için kullanılacak satır sınırını veya dosya kaynağını seçebilirsiniz. Bu ayarda bulunan satır limitleri yalnızca geçerli hata ayıklama oturumu içindir. Azure SYNAPSE Analytics kaynağı için kullanılacak hazırlama bağlantılı hizmetini de seçebilirsiniz. 

![Hata ayıklama ayarları](media/data-flow/debug-settings.png &quot;Hata ayıklama ayarları")

Veri akışınızda veya başvurulan veri kümelerinde parametrelere sahipseniz, **Parametreler** sekmesini seçerek hata ayıklama sırasında kullanılacak değerleri belirtebilirsiniz.

Kaynak veri kümelerinizi değiştirmek zorunda kalmaması için örnek dosyaları veya örnek veri tablolarını işaret etmek üzere buradaki örnekleme ayarlarını kullanın. Burada örnek bir dosya veya tablo kullanarak, veri alt kümesiyle test edilirken veri akışınızda aynı mantık ve özellik ayarlarını koruyabilirsiniz.

![Hata ayıklama ayarları parametreleri](media/data-flow/debug-settings2.png "Hata ayıklama ayarları parametreleri")

ADF veri akışlarında hata ayıklama modu için kullanılan varsayılan IR, 4 çekirdekli tek sürücü düğümüne sahip küçük 4 çekirdekli tek çalışan düğümüdür. Bu, veri akışı mantığınızı sınarken daha küçük veri örnekleriyle sorunsuz bir şekilde çalışabilir. Veri önizleme sırasında hata ayıklama ayarlarınızda satır sınırlarını genişlettikten veya ardışık düzen hata ayıklaması sırasında kaynağınıza daha fazla sayıda örneklenmiş satır ayarlarsanız, yeni bir Azure Integration Runtime daha büyük bir işlem ortamı ayarlamayı düşünebilirsiniz. Daha sonra, hata ayıklama oturumunuzu daha büyük işlem ortamını kullanarak yeniden başlatabilirsiniz.

## <a name="data-preview"></a>Veri önizlemesi

Hata ayıklama tarihinde, veri Önizleme sekmesi alt panelde açılır. Üzerinde hata ayıklama modu olmadan veri akışı, Inceleme sekmesindeki dönüştürmelerinizin her birinin içindeki ve çıkan yalnızca geçerli meta verileri gösterir. Veri önizleme, yalnızca hata ayıklama ayarlarınızda sınırınız olarak ayarlamış olduğunuz satır sayısını sorgular. Veri önizlemeyi getirmek için **Yenile** ' ye tıklayın.

![Veri önizlemesi](media/data-flow/datapreview.png "Veri önizlemesi")

> [!NOTE]
> Dosya kaynakları, okunan satırları değil yalnızca gördüğünüz satırları sınırlar. Çok büyük veri kümelerinde, bu dosyanın küçük bir bölümünü almanız ve test etmeniz için kullanmanız önerilir. Dosya veri kümesi türü olan her kaynak için hata ayıklama ayarlarında geçici bir dosya seçebilirsiniz.

Veri akışında hata ayıklama modunda çalışırken verileriniz havuz dönüşümüne yazılmaz. Bir hata ayıklama oturumu, dönüştürmelerinizi için bir test bandı işlevi sunacak şekilde tasarlanmıştır. Hata ayıklama sırasında havuzlar gerekli değildir ve veri akışınız içinde yok sayılır. Havuzınızdaki verilerin yazılmasını test etmek isterseniz, veri akışını bir Azure Data Factory işlem hattından yürütün ve bir işlem hattından hata ayıklama yürütmesini kullanın.

Veri önizleme,, Spark belleğindeki veri çerçevelerinden satır sınırlarını ve veri örneklemesi kullanarak dönüştürülmüş verilerinizin anlık görüntüsüdür. Bu nedenle, havuz sürücüleri bu senaryoda kullanılmaz veya test edilmez.

### <a name="testing-join-conditions"></a>Test ekleme koşulları

Birim testi katıldığında, varsa veya Arama dönüşümlerine göre, testiniz için küçük bir bilinen veri kümesi kullandığınızdan emin olun. Testinizde kullanılacak geçici bir dosya ayarlamak için yukarıdaki hata ayıklama ayarları seçeneğini kullanabilirsiniz. Bu, büyük bir veri kümesinden satırları sınırlandırırken veya örneklenirken, hangi satırların ve hangi anahtarların test için akışa okunacağını tahmin edemediği için gereklidir. Sonuç belirleyici değildir, yani JOIN koşullarınızın başarısız olabileceği anlamına gelir.

### <a name="quick-actions"></a>Hızlı Eylemler

Veri önizlemesini görtikten sonra, bir sütunda tür atama, kaldırma veya değiştirme yapmak için hızlı bir dönüşüm oluşturabilirsiniz. Sütun başlığına tıklayın ve ardından veri önizleme araç çubuğundan seçeneklerden birini belirleyin.

![Ekran görüntüsü, veri önizleme araç çubuğunu seçenekler ile gösterir: typecast, MODIFY, Statistics ve Remove.](media/data-flow/quick-actions1.png "Hızlı Eylemler")

Bir değişikliği seçtiğinizde, veri önizlemesi hemen yenilenir. Yeni bir dönüşüm oluşturmak için sağ üst köşedeki **Onayla** ' ya tıklayın.

![Ekran görüntüsü Onayla düğmesini gösterir.](media/data-flow/quick-actions2.png "Hızlı Eylemler")

**Tür dönüştürme** ve **değiştirme** türetilmiş bir sütun dönüştürmesi oluşturacak ve **Remove** bir SELECT dönüştürmesi oluşturacak.

![Ekran görüntüsü türetilmiş sütunun ayarlarını gösterir.](media/data-flow/quick-actions3.png "Hızlı Eylemler")

> [!NOTE]
> Veri akışınızı düzenlerseniz hızlı bir dönüştürme eklemeden önce veri önizlemeyi yeniden almanız gerekir.

### <a name="data-profiling"></a>Veri profili oluşturma

Veri önizleme sekmesinizdeki bir sütunu seçmek ve veri önizleme araç çubuğunda **İstatistikler** ' i tıklatmak, her alanla ilgili ayrıntılı istatistiklerle veri kılavuzunuzun en sağında bir grafik açılır. Azure Data Factory görüntülenecek grafik türünün veri örneklemesi temelinde bir belirleme yapılır. Yüksek kardinalite alanları varsayılan olarak NULL/değil NULL grafiklerine göre NULL/olmayan grafik olarak değişir. Ayrıca, dize alanlarının en fazla/uzun uzunluğunu, sayısal alanlardaki minimum/maksimum değerleri, standart dev, yüzdebirlik değeri, sayımlar ve Ortalama değerlerini de görürsünüz.

![Sütun istatistikleri](media/data-flow/stats.png "Sütun istatistikleri")

## <a name="next-steps"></a>Sonraki adımlar

* Veri akışınızı oluşturup hata ayıkladıktan sonra [bir işlem hattınızdan yürütün.](control-flow-execute-data-flow-activity.md)
* İşlem hattınızı bir veri akışı ile sınarken, işlem hattı [hata ayıklama çalıştırma yürütme seçeneğini kullanın.](iterative-development-debugging.md)
