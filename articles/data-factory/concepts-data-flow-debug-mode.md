---
title: Azure Data Factory eşleme veri akışı hata ayıklama modu
description: Veri akışları oluştururken etkileşimli bir hata ayıklama oturumu başlatın
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: c65ed14abb86b3b434e46fbe857487b06e217aad
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387365"
---
# <a name="mapping-data-flow-debug-mode"></a>Eşleme veri akışı hata ayıklama modu



## <a name="overview"></a>Genel Bakış

Azure Data Factory eşleme veri akışının hata ayıklama modu, veri akışlarınızı oluştururken ve hata ayıkladığınızda veri şekli dönüşümünü etkileşimli bir şekilde izlemenize olanak sağlar. Hata ayıklama oturumu hem veri akışı tasarım oturumlarında hem de veri akışlarının işlem hattı hata ayıklamada yürütülmesi sırasında kullanılabilir. Hata ayıklama modunu açmak için tasarım yüzeyinin en üstündeki "veri akışı hata ayıklaması" düğmesini kullanın.

![Hata ayıklama kaydırıcısı](media/data-flow/debugbutton.png "Hata ayıklama kaydırıcısı")

Kaydırıcıyı açtıktan sonra hangi tümleştirme çalışma zamanı yapılandırmasını kullanmak istediğinizi seçmeniz istenir. Oto Resolveıntegrationruntime seçilirse, 60 dakikalık bir yaşam süresine sahip sekiz çekirdekli genel işlem olan bir küme de Sun olur. Veri akışı tümleştirme çalışma zamanları hakkında daha fazla bilgi için bkz. [veri akışı performansı](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime).

![IR seçiminde hata ayıkla](media/data-flow/debugbutton2.png "IR seçiminde hata ayıkla")

Hata ayıklama modu açık olduğunda, etkin bir Spark kümesi ile veri akışınızı etkileşimli olarak oluşturacaksınız. Azure Data Factory, hata ayıklamayı kapattıktan sonra oturum kapatılacak. Hata ayıklama oturumunun açık olduğu süre boyunca Azure Databricks tarafından tahakkuk edilen saatlik ücretlerden haberdar olmanız gerekir.

Çoğu durumda, iş mantığınızı doğrulayabilmeniz ve işinizi Azure Data Factory yayımlamadan önce veri dönüştürmelerinizi görüntüleyebilmeniz için veri akışlarınızı hata ayıklama modunda oluşturmak iyi bir uygulamadır. Veri akışınızı bir ardışık düzende test etmek için işlem hattı panelinde "hata ayıkla" düğmesini kullanın.

## <a name="cluster-status"></a>Küme durumu

Küme hata ayıklamaya hazırlanışında tasarım yüzeyinin en üstündeki küme durumu göstergesi yeşil olur. Kümeniz zaten ısınma olursa yeşil gösterge neredeyse anında görünür. Hata ayıklama moduna girdiğinizde kümeniz zaten çalışmıyorsa, kümenin dönmesi için 5-7 dakika beklemeniz gerekir. Gösterge, başlamaya kadar dönmesini sağlayacak.

Hata ayıklamanız ile işiniz bittiğinde, Azure Databricks kümenizin sonlanabilir olması için hata ayıklama anahtarını kapatın ve artık hata ayıklama etkinliği için faturalandırılırsınız.

## <a name="debug-settings"></a>Hata ayıklama ayarları

Hata ayıklama ayarları, veri akışı tuvali araç çubuğunda "hata ayıklama ayarları" seçeneğine tıklanarak düzenlenebilirler. Burada kaynak dönüşümlerinizin her biri için kullanılacak satır sınırını veya dosya kaynağını seçebilirsiniz. Bu ayarda bulunan satır limitleri yalnızca geçerli hata ayıklama oturumu içindir. Ayrıca, bir SQL DW kaynağı için kullanılacak hazırlama bağlantılı hizmetini de seçebilirsiniz. 

![Hata ayıklama ayarları](media/data-flow/debug-settings.png "Hata ayıklama ayarları")

Veri akışınızda veya başvurulan veri kümelerinde parametrelere sahipseniz, **Parametreler** sekmesini seçerek hata ayıklama sırasında kullanılacak değerleri belirtebilirsiniz.

![Hata ayıklama ayarları parametreleri](media/data-flow/debug-settings2.png "Hata ayıklama ayarları parametreleri")

## <a name="data-preview"></a>Veri önizleme

Hata ayıklama tarihinde, veri Önizleme sekmesi alt panelde açılır. Üzerinde hata ayıklama modu olmadan veri akışı, Inceleme sekmesindeki dönüştürmelerinizin her birinin içindeki ve çıkan yalnızca geçerli meta verileri gösterir. Veri önizleme, yalnızca hata ayıklama ayarlarınızda sınırınız olarak ayarlamış olduğunuz satır sayısını sorgular. Veri önizlemeyi getirmek için **Yenile** ' ye tıklayın.

![Veri önizleme](media/data-flow/datapreview.png "Veri önizleme")

> [!NOTE]
> Dosya kaynakları, okunan satırları değil yalnızca gördüğünüz satırları sınırlar. Çok büyük veri kümelerinde, bu dosyanın küçük bir bölümünü almanız ve test etmeniz için kullanmanız önerilir. Dosya veri kümesi türü olan her kaynak için hata ayıklama ayarlarında geçici bir dosya seçebilirsiniz.

Veri akışında hata ayıklama modunda çalışırken verileriniz havuz dönüşümüne yazılmaz. Bir hata ayıklama oturumu, dönüştürmelerinizi için bir test bandı işlevi sunacak şekilde tasarlanmıştır. Hata ayıklama sırasında havuzlar gerekli değildir ve veri akışınız içinde yok sayılır. Havuzınızdaki verilerin yazılmasını test etmek isterseniz, veri akışını bir Azure Data Factory işlem hattından yürütün ve bir işlem hattından hata ayıklama yürütmesini kullanın.

### <a name="testing-join-conditions"></a>Test ekleme koşulları

Birim testi katıldığında, varsa veya Arama dönüşümlerine göre, testiniz için küçük bir bilinen veri kümesi kullandığınızdan emin olun. Testinizde kullanılacak geçici bir dosya ayarlamak için yukarıdaki hata ayıklama ayarları seçeneğini kullanabilirsiniz. Bu, büyük bir veri kümesinden satırları sınırlandırırken veya örneklenirken, hangi satırların ve hangi anahtarların test için akışa okunacağını tahmin edemediği için gereklidir. Sonuç belirleyici değildir, yani JOIN koşullarınızın başarısız olabileceği anlamına gelir.

### <a name="quick-actions"></a>Hızlı Eylemler

Veri önizlemesini görtikten sonra, bir sütunda tür atama, kaldırma veya değiştirme yapmak için hızlı bir dönüşüm oluşturabilirsiniz. Sütun başlığına tıklayın ve ardından veri önizleme araç çubuğundan seçeneklerden birini belirleyin.

![Hızlı Eylemler](media/data-flow/quick-actions1.png "Hızlı Eylemler")

Bir değişikliği seçtiğinizde, veri önizlemesi hemen yenilenir. Yeni bir dönüşüm oluşturmak için sağ üst köşedeki **Onayla** ' ya tıklayın.

![Hızlı Eylemler](media/data-flow/quick-actions2.png "Hızlı Eylemler")

**Tür dönüştürme** ve **değiştirme** türetilmiş bir sütun dönüştürmesi oluşturacak ve **Remove** bir SELECT dönüştürmesi oluşturacak.

![Hızlı Eylemler](media/data-flow/quick-actions3.png "Hızlı Eylemler")

> [!NOTE]
> Veri akışınızı düzenlerseniz hızlı bir dönüştürme eklemeden önce veri önizlemeyi yeniden almanız gerekir.

### <a name="data-profiling"></a>Veri profili oluşturma

Veri önizleme sekmesinizdeki bir sütunu seçmek ve veri önizleme araç çubuğunda **İstatistikler** ' i tıklatmak, her alanla ilgili ayrıntılı istatistiklerle veri kılavuzunuzun en sağında bir grafik açılır. Azure Data Factory görüntülenecek grafik türünün veri örneklemesi temelinde bir belirleme yapılır. Yüksek kardinalite alanları varsayılan olarak NULL/değil NULL grafiklerine göre NULL/olmayan grafik olarak değişir. Ayrıca, dize alanlarının en fazla/uzun uzunluğunu, sayısal alanlardaki minimum/maksimum değerleri, standart dev, yüzdebirlik değeri, sayımlar ve Ortalama değerlerini de görürsünüz.

![Sütun istatistikleri](media/data-flow/stats.png "Sütun istatistikleri")

## <a name="next-steps"></a>Sonraki adımlar

* Veri akışınızı oluşturup hata ayıkladıktan sonra [bir işlem hattınızdan yürütün.](control-flow-execute-data-flow-activity.md)
* İşlem hattınızı bir veri akışı ile sınarken, işlem hattı [hata ayıklama çalıştırma yürütme seçeneğini kullanın.](iterative-development-debugging.md)
