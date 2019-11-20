---
title: Azure Data Factory veri akışlarını eşleme
description: Azure Data Factory veri akışlarını eşleştirmeye genel bakış
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: ed2502ffebbacf5e66e3e4738e2e88ce7fb8a562
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681554"
---
# <a name="what-are-mapping-data-flows"></a>Veri akışlarını eşleme nedir?

Veri akışlarını eşleme, Azure Data Factory ' de görsel olarak tasarlanan veri dönüştürmelerdir. Veri akışları, veri mühendislerinin kod yazmadan grafik veri dönüştürme mantığı geliştirmesini sağlar. Elde edilen veri akışları, ölçeklendirilen Spark kümelerini kullanan Azure Data Factory işlem hatları içinde etkinlik olarak yürütülür. Veri akışı etkinlikleri mevcut Data Factory zamanlama, denetim, akış ve izleme özellikleri aracılığıyla çalıştırılabilir.

Veri akışlarını eşleme, hiçbir kodlamaya gerek olmadan tam bir görsel deneyim sağlar. Veri akışlarınız, ölçeği genişletilmiş veri işleme için kendi yürütme kümenizde çalışır. Azure Data Factory, tüm kod çevirisi, yol iyileştirmesi ve veri akışı işlerinizin yürütülmesini işler.

## <a name="getting-started"></a>Başlarken

Bir veri akışı oluşturmak için, **fabrika kaynakları**altındaki artı işaretini ve ardından **veri akışı**' nı seçin. 

![Yeni veri akışı](media/data-flow/newdataflow2.png "Yeni veri akışı")

Bu sizi, dönüşüm mantığınızı oluşturabileceğiniz veri akışı tuvaline götürür. Kaynak dönüştürmeyi yapılandırmaya başlamak için **Kaynak Ekle** ' yi seçin. Daha fazla bilgi için bkz. [kaynak dönüştürme](data-flow-source.md).

## <a name="data-flow-canvas"></a>Veri akışı tuvali

Veri akışı tuvali üç parçaya ayrılmıştır: üst çubuk, grafik ve yapılandırma paneli. 

![Tuvalinin](media/data-flow/canvas1.png "Tuvalinin")

### <a name="graph"></a>Graph

Grafik, dönüşüm akışını görüntüler. Bir veya daha fazla havuza akan kaynak verilerinin kökenini gösterir. Yeni bir kaynak eklemek için **Kaynak Ekle**' yi seçin. Yeni bir dönüşüm eklemek için, varolan bir dönüşümün sağ alt köşesindeki artı işaretini seçin.

![Tuvalinin](media/data-flow/canvas2.png "Tuvalinin")

### <a name="azure-integration-runtime-data-flow-properties"></a>Azure tümleştirme çalışma zamanı veri akışı özellikleri

![Hata ayıklama düğmesi](media/data-flow/debugbutton.png "Hata ayıklama düğmesi")

ADF 'de veri akışları ile çalışmaya başladığınızda, tarayıcı kullanıcı arabiriminin en üstündeki veri akışları için "hata ayıklama" anahtarını açmak isteyeceksiniz. Bu, etkileşimli hata ayıklama, veri önizlemeleri ve işlem hattı hata ayıklama yürütmeleri için kullanılacak bir Azure Databricks kümesi kullanacaktır. Kullanılan kümenin boyutunu özel bir [Azure Integration Runtime](concepts-integration-runtime.md)seçerek ayarlayabilirsiniz. Hata ayıklama oturumu, son veri önizlemeniz veya son hata ayıklama işlem hattı yürütmesinden sonra 60 dakika boyunca etkin kalır.

Veri akışı etkinlikleriyle işlem hatlarınızı çalıştırdığınızda ADF, "Çalıştır" özelliğindeki [etkinlikle](control-flow-execute-data-flow-activity.md) ilişkili Azure Integration Runtime kullanır.

Varsayılan Azure Integration Runtime, verileri önizlemenize ve hata ayıklama işlem hatlarını en az maliyetle hızlı bir şekilde yürütebilmenizi sağlayan küçük 4 çekirdekli tek çalışan düğümü kümesidir. Büyük veri kümelerine yönelik işlemler gerçekleştiriyorsanız daha büyük bir Azure IR yapılandırma ayarlayın.

Azure IR veri akışı özelliklerinde bir TTL ayarlayarak, ADF 'yi bir küme kaynakları havuzunu (VM 'Ler) tutmaya söyleyebilirsiniz. Bu, sonraki etkinliklerde daha hızlı iş yürütmeye neden olur.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Azure tümleştirme çalışma zamanı ve veri akışı stratejileri

##### <a name="execute-data-flows-in-parallel"></a>Veri akışlarını paralel olarak yürütme

Bir işlem hattındaki veri akışlarını paralel olarak çalıştırırsanız, ADF her bir etkinliğe bağlı Azure Integration Runtime ayarlara bağlı olarak her etkinlik yürütmesi için ayrı Azure Databricks kümelerini alır. ADF işlem hatlarında paralel yürütmeler tasarlamak için, veri akışı etkinliklerinizi Kullanıcı arabiriminde öncelik kısıtlamaları olmadan ekleyin.

Bu üç seçenekten Bu seçenek büyük olasılıkla en kısa sürede yürütülecektir. Ancak, her paralel veri akışı ayrı kümeler üzerinde aynı anda yürütülür, bu nedenle olayların sıralaması belirleyici değildir.

##### <a name="overload-single-data-flow"></a>Tek veri akışını aşırı yükleme

Tüm mantığınızı tek bir veri akışı içinde yerleştirirseniz, ADF 'nin hepsi tek bir Spark küme örneğindeki aynı iş yürütme bağlamında yürütülür.

İş kurallarınız ve iş mantığınızın bir arada olması nedeniyle bu seçeneğin izlenmesi ve sorun gidermesi daha zor olabilir. Bu seçenek ayrıca, daha fazla yeniden kullanılabilirlik sağlamaz.

##### <a name="execute-data-flows-serially"></a>Veri akışlarını hizmet halinde yürütme

Veri akışı etkinliklerinizi işlem hattında seri olarak çalıştırırsanız ve Azure IR yapılandırmasında bir TTL belirlediyseniz, ADF daha hızlı yürütme süreleri elde eden işlem kaynaklarını (VM 'Ler) yeniden kullanacaktır. Her yürütme için yeni bir Spark bağlamı almaya devam edersiniz.

Bu üç seçenekten, bu durum büyük olasılıkla uçtan uca yürütmek için en uzun zaman alır. Ancak, her bir veri akışı adımında mantıksal işlemlerin temiz bir şekilde ayrılmasını sağlar.

### <a name="configuration-panel"></a>Yapılandırma bölmesi

Yapılandırma panelinde seçili olan dönüştürmeye özgü ayarlar gösterilir. Hiçbir dönüşüm seçilmezse, veri akışını gösterir. Genel veri akışı yapılandırmasında, **genel** sekmesinin altındaki adı ve açıklamayı düzenleyebilir veya **Parametreler** sekmesi aracılığıyla parametreler ekleyebilirsiniz. Daha fazla bilgi için bkz. [veri akışı parametrelerini eşleme](parameters-data-flow.md).

Her dönüşümde en az dört Yapılandırma sekmesi vardır.

#### <a name="transformation-settings"></a>Dönüştürme ayarları

Her bir dönüşümün yapılandırma bölmesindeki ilk sekme, bu dönüştürmeye özgü ayarları içerir. Daha fazla bilgi için bkz. bu dönüşümün belge sayfası.

![Kaynak ayarları sekmesi](media/data-flow/source1.png "Kaynak ayarları sekmesi")

#### <a name="optimize"></a>İyileştirme

**Optimizasyon** sekmesi, bölümleme düzenlerini yapılandırma ayarlarını içerir.

![İyileştir](media/data-flow/optimize1.png "İyileştirme")

Varsayılan ayar **geçerli bölümleme kullanır**, bu, Azure Data Factory Spark üzerinde çalışan veri akışları için yerel bölümlendirme şemasını kullanmasını söyler. Çoğu senaryoda, bu ayar önerilir.

Bölümlemeyi ayarlamak isteyebileceğiniz örnekler vardır. Örneğin, dönüştürmelerinizi Gölü tek bir dosyaya çıkarmak istiyorsanız, bir havuz dönüşümünde **tek bölüm** ' ü seçin.

Bölümleme düzenlerini denetlemek isteyebileceğiniz başka bir durum da performansı iyileştiriliyor. Bölümlemenin ayarlanması, verilerin işlem düğümleri genelinde dağıtılması üzerinde denetim sağlar ve genel veri akışı performansından hem olumlu hem de olumsuz etkileri olabilir. Daha fazla bilgi için bkz. [veri akışı performans Kılavuzu](concepts-data-flow-performance.md).

Herhangi bir dönüşümdeki Bölümlendirmeyi değiştirmek için, **optimizasyon** sekmesini seçin ve **bölümlendirme** radyo düğmesini seçin. Daha sonra bölümlemeye yönelik bir dizi seçenek sunulur. Bölümlemenin en iyi yöntemi, veri birimleriniz, aday anahtarlarınız, null değerleri ve kardinalite temelinde farklılık gösterir. 

En iyi uygulama, varsayılan bölümlendirme ile başlamalı ve farklı bölümlendirme seçeneklerini denemelerdir. İşlem hattı hata ayıklama çalıştırmalarını kullanarak test edebilir ve izleme görünümündeki her bir dönüşüm gruplandırmasında yürütme süresini ve bölüm kullanımını görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [veri akışlarını izleme](concepts-data-flow-monitoring.md).

Aşağıdaki bölümlendirme seçenekleri kullanılabilir.

##### <a name="round-robin"></a>Hepsini bir kez deneme 

Hepsini bir kez deneme, verileri bölümler arasında eşit olarak dağıtan basit bir bölümdür. Katı ve akıllı bölümlendirme stratejisi uygulamak için iyi bir ana aday olmadığında hepsini bir kez deneme kullanın. Fiziksel bölüm sayısını ayarlayabilirsiniz.

##### <a name="hash"></a>Karma

Azure Data Factory, benzer değerlere sahip satırlar aynı bölüme düşecek şekilde Tekdüzen bölümler oluşturmak için bir sütun karması oluşturacak. Karma seçeneğini kullandığınızda olası bölüm eğsınaması için test edin. Fiziksel bölüm sayısını ayarlayabilirsiniz.

##### <a name="dynamic-range"></a>Dinamik Aralık

Dinamik Aralık, sağladığınız sütunlara veya ifadelere göre Spark dinamik aralıklarını kullanacaktır. Fiziksel bölüm sayısını ayarlayabilirsiniz. 

##### <a name="fixed-range"></a>Sabit Aralık

Bölümlenmiş veri sütunlarınızın içindeki değerler için sabit bir Aralık sağlayan bir ifade oluşturun. Bölüm eğriliğini önlemek için, bu seçeneği kullanmadan önce verilerinizin iyi şekilde anlaşılmasını sağlayabilirsiniz. İfade için girdiğiniz değerler, bölüm işlevinin bir parçası olarak kullanılır. Fiziksel bölüm sayısını ayarlayabilirsiniz.

##### <a name="key"></a>Anahtar

Verilerinizin önem düzeyini iyi anlamak istiyorsanız anahtar bölümleme iyi bir strateji olabilir. Anahtar bölümleme, sütuninizdeki her benzersiz değer için bölümler oluşturacaktır. Bu sayı, verilerdeki benzersiz değerleri temel alacak olduğundan bölüm sayısını ayarlayamazsınız.

#### <a name="inspect"></a>Bilgiyi

**İnceleme** sekmesi, dönüştürmakta olduğunuz veri akışının meta verilerine bir görünüm sağlar. Sütun sayılarını, sütun değiştirildiğini, eklenen sütunları, veri türlerini, sütun sıralamasını ve sütun başvurularını görebilirsiniz. **İnceleme** , meta verilerinizin salt okunurdur görünümüdür. **İnceleme** bölmesinde meta verileri görmek için hata ayıklama modunun etkin olması gerekmez.

![Bilgiyi](media/data-flow/inspect1.png "Bilgiyi")

Dönüşümlerinizi kullanarak verilerinizin şeklini değiştirirken, **İnceleme** bölmesinde meta veri değişiklikleri akışını görürsünüz. Kaynak dönüşümünüze tanımlı bir şema yoksa, veriler **İnceleme** bölmesinde görünmez. Şema DRFT senaryolarında meta verilerin bulunmaması yaygındır.

#### <a name="data-preview"></a>Veri önizleme

Hata ayıklama modu açık ise, **veri önizleme** sekmesi her dönüşümde verilerin etkileşimli bir anlık görüntüsünü sunar. Daha fazla bilgi için bkz. [hata ayıklama modunda veri önizlemesi](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Üst çubuk

Üst çubuk, kaydetme ve doğrulama gibi tüm veri akışını etkileyen eylemler içerir. Grafiği **göster** ve **grafiği gizle** düğmelerini kullanarak grafik ve yapılandırma modları arasında da geçiş yapabilirsiniz.

![Grafiği gizle](media/data-flow/hideg.png "Grafiği gizle")

Grafınızı gizlerseniz, **önceki** ve **sonraki** düğmelerini kullanarak dönüştürme düğümleriniz arasında daha da gezinebilirsiniz.

![Önceki ve sonraki düğmeler](media/data-flow/showhide.png "önceki ve sonraki düğmeler")

## <a name="next-steps"></a>Sonraki adımlar

* [Kaynak dönüştürme](data-flow-source.md)oluşturmayı öğrenin.
* Veri akışlarınızı [hata ayıklama modunda](concepts-data-flow-debug-mode.md)oluşturmayı öğrenin.
