---
title: Veri akışlarını eşleme
description: Azure Data Factory veri akışlarını eşleştirmeye genel bakış
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/14/2021
ms.openlocfilehash: 826183e09f2aa7f3f22ace8b5ce3e16767d49863
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515670"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory veri akışlarını eşleme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Veri akışlarını eşleme nedir?

Veri akışlarını eşleme, Azure Data Factory ' de görsel olarak tasarlanan veri dönüştürmelerdir. Veri akışları, veri mühendislerinin kod yazmadan veri dönüştürme mantığı geliştirmesini sağlar. Elde edilen veri akışları, ölçeği genişletilmiş Apache Spark kümelerini kullanan Azure Data Factory işlem hatları içinde etkinlik olarak yürütülür. Veri akışı etkinlikleri mevcut Azure Data Factory zamanlama, denetim, akış ve izleme özellikleri kullanılarak gerçekleştirilebilir.

Veri akışlarını eşleme, hiçbir kodlamaya gerek olmadan tamamen görsel bir deneyim sağlar. Veri akışlarınız, ölçeklendirilmemiş veri işleme için ADF tarafından yönetilen yürütme kümelerinde çalışır. Azure Data Factory, tüm kod çevirisi, yol iyileştirmesi ve veri akışı işlerinizin yürütülmesini işler.

## <a name="getting-started"></a>Kullanmaya başlama

Veri akışları, işlem hatları ve veri kümeleri gibi fabrika kaynakları bölmesinden oluşturulur. Bir veri akışı oluşturmak için, **fabrika kaynakları**' nın yanındaki artı işaretini ve ardından **veri akışı**' nı seçin. 

![Yeni veri akışı](media/data-flow/new-data-flow.png)

Bu eylem sizi, dönüşüm mantığınızı oluşturabileceğiniz veri akışı tuvaline götürür. Kaynak dönüştürmeyi yapılandırmaya başlamak için **Kaynak Ekle** ' yi seçin. Daha fazla bilgi için bkz. [kaynak dönüştürme](data-flow-source.md).

## <a name="authoring-data-flows&quot;></a>Veri akışları yazma

Eşleme veri akışı, dönüştürme mantığını kolayca oluşturmak için tasarlanan benzersiz bir yazma tuvaline sahiptir. Veri akışı tuvali üç parçaya ayrılmıştır: üst çubuk, grafik ve yapılandırma paneli. 

![Ekran görüntüsünde, etiketli üst çubuk, grafik ve yapılandırma paneli ile veri akışı tuvali gösterilmektedir.](media/data-flow/canvas-1.png &quot;Tuval")

### <a name="graph"></a>Graf

Grafik, dönüşüm akışını görüntüler. Bir veya daha fazla havuza akan kaynak verilerinin kökenini gösterir. Yeni bir kaynak eklemek için **Kaynak Ekle**' yi seçin. Yeni bir dönüşüm eklemek için, varolan bir dönüşümün sağ alt köşesindeki artı işaretini seçin. [Veri akışı grafiğinin nasıl yönetileceği](concepts-data-flow-manage-graph.md)hakkında daha fazla bilgi edinin.

![Ekran görüntüsü, bir arama metin kutusuyla tuvalin grafik bölümünü gösterir.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Yapılandırma bölmesi

Yapılandırma panelinde seçili olan dönüştürmeye özgü ayarlar gösterilir. Hiçbir dönüşüm seçilmezse, veri akışını gösterir. Genel veri akışı yapılandırmasında **Parametreler** sekmesini kullanarak parametreler ekleyebilirsiniz. Daha fazla bilgi için bkz. [veri akışı parametrelerini eşleme](parameters-data-flow.md).

Her dönüşümde en az dört Yapılandırma sekmesi bulunur.

#### <a name="transformation-settings"></a>Dönüştürme ayarları

Her bir dönüşümün yapılandırma bölmesindeki ilk sekme, bu dönüştürmeye özgü ayarları içerir. Daha fazla bilgi için bkz. bu dönüşümün belge sayfası.

![Kaynak ayarları sekmesi](media/data-flow/source1.png "Kaynak ayarları sekmesi")

#### <a name="optimize"></a>İyileştirme

**Optimizasyon** sekmesi, bölümleme düzenlerini yapılandırma ayarlarını içerir. Veri akışlarınızı iyileştirme hakkında daha fazla bilgi edinmek için bkz. [eşleme veri akışı performans Kılavuzu](concepts-data-flow-performance.md).

![Ekran görüntüsü, Bölüm seçeneği, bölüm türü ve bölüm sayısı dahil en Iyileştirme sekmesini gösterir.](media/data-flow/optimize.png)

#### <a name="inspect&quot;></a>Bilgiyi

**İnceleme** sekmesi, dönüştürmakta olduğunuz veri akışının meta verilerine bir görünüm sağlar. Sütun sayılarını, değişen sütunları, eklenen sütunları, veri türlerini, sütun sırasını ve sütun başvurularını görebilirsiniz. **İnceleme** , meta verilerinizin salt okunurdur görünümüdür. **İnceleme** bölmesinde meta verileri görmek için hata ayıklama modunun etkin olması gerekmez.

![Bilgiyi](media/data-flow/inspect1.png &quot;Bilgiyi")

Dönüşümlerinizi kullanarak verilerinizin şeklini değiştirirken, **İnceleme** bölmesinde meta veri değişiklikleri akışını görürsünüz. Kaynak dönüşümünüze tanımlı bir şema yoksa, veriler **İnceleme** bölmesinde görünmez. Şema DRFT senaryolarında meta verilerin bulunmaması yaygındır.

#### <a name="data-preview"></a>Veri önizlemesi

Hata ayıklama modu açık ise, **veri önizleme** sekmesi her dönüşümde verilerin etkileşimli bir anlık görüntüsünü sunar. Daha fazla bilgi için bkz. [hata ayıklama modunda veri önizlemesi](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Üst çubuk

Üst çubuk, kaydetme ve doğrulama gibi tüm veri akışını etkileyen eylemler içerir. Ayrıca, dönüştürme mantığınızın temel alınan JSON kodunu ve veri akışı komut dosyasını da görüntüleyebilirsiniz. Daha fazla bilgi için [veri akışı betiği](data-flow-script.md)hakkında bilgi edinin.

## <a name="available-transformations"></a>Kullanılabilir dönüşümler

Kullanılabilir dönüşümlerinin bir listesini almak için [eşleme veri akışı dönüşümüne genel bakış ' ı](data-flow-transformation-overview.md) görüntüleyin.

## <a name="data-flow-data-types"></a>Veri akışı veri türleri

dizi ikili Boolean karmaşık ondalık Tarih kayan tamsayı Long Map kısa dize zaman damgası

## <a name="data-flow-activity"></a>Veri akışı etkinliği

Veri [akışı etkinliğini](control-flow-execute-data-flow-activity.md)kullanan ADF işlem hatları içinde veri akışlarını eşleme işlemleri yapılır. Tüm kullanıcılar yapması gereken tümleştirme çalışma zamanının parametre değerlerini ne şekilde kullanacağınızı ve geçisyonunu belirtir. Daha fazla bilgi için [Azure tümleştirme çalışma zamanı](concepts-integration-runtime.md#azure-integration-runtime)hakkında bilgi edinin.

## <a name="debug-mode"></a>Hata ayıklama modu

Hata ayıklama modu, veri akışlarınızı oluştururken ve hata ayıkladığınızda her bir dönüştürme adımının sonuçlarını etkileşimli olarak görmenizi sağlar. Hata ayıklama oturumu, veri akışı mantığınızı oluştururken ve veri akışı etkinlikleriyle işlem hattı hata ayıklama çalıştırmaları çalışırken kullanılabilir. Daha fazla bilgi için bkz. [hata ayıklama modu belgeleri](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flows"></a>Veri akışlarını izleme

Eşleme veri akışı, mevcut Azure Data Factory izleme özellikleri ile tümleşir. Veri akışı izleme çıkışını nasıl anlayacağınızı öğrenmek için bkz. [eşleme veri akışlarını izleme](concepts-data-flow-monitoring.md).

Azure Data Factory ekibi, iş mantığınızı oluşturduktan sonra veri akışlarınızın yürütme süresini iyileştirmenize yardımcı olmak için bir [performans ayarlama Kılavuzu](concepts-data-flow-performance.md) oluşturdu.


## <a name="available-regions"></a>Kullanılabilir bölgeler

Veri akışlarını eşleme, ADF içinde aşağıdaki bölgelerde kullanılabilir:

| Azure bölgesi | ADF 'de veri akışları |
| ------------ | ----------------- |
| Orta Avustralya | |
| Orta Avustralya 2 | |
| Doğu Avustralya | ✓ |
| Güneydoğu Avustralya   | ✓ |
| Güney Brezilya  | ✓ |
| Orta Kanada | ✓ |
| Orta Hindistan | ✓ |
| Central US    | ✓ |
| Doğu Çin |      |
| Çin Doğu 2  |   |
| Çin bölgesel olmayan | |
| Kuzey Çin | ✓ |
| Çin Kuzey 2 | ✓ |
| Doğu Asya | ✓ |
| Doğu ABD   | ✓ |
| Doğu ABD 2 | ✓ |
| Orta Fransa | ✓ |
| Güney Fransa  | |
| Almanya Orta (Sovereign) | |
| Almanya Bölgesel Olmayan (Sovereign) | |
| Almanya Kuzey (genel) | |
| Almanya Kuzeydoğu (Sovereign) | |
| Almanya Orta Batı (genel) |  |
| Doğu Japonya | ✓ |
| Batı Japonya |  |
| Güney Kore - Orta | ✓ |
| Güney Kore - Güney | |
| Orta Kuzey ABD  | ✓ |
| Kuzey Avrupa  | ✓ |
| Norveç Doğu | ✓ |
| Norveç Batı | |
| Güney Afrika - Kuzey    | ✓ |
| Güney Afrika - Batı |  |
| Orta Güney ABD  | |
| Güney Hindistan | |
| Güneydoğu Asya    | ✓ |
| İsviçre Kuzey |   |
| İsviçre Batı | |
| BAE Orta | |
| BAE Kuzey | ✓ |
| Güney Birleşik Krallık  | ✓ |
| Batı Birleşik Krallık |     |
| Orta US DoD | |
| Doğu US DoD | |
| US Gov Arizona | ✓ |
| US Gov Bölgesel Olmayan | |
| US Gov Texas | |
| US Gov Virginia | ✓ |
| Orta Batı ABD |     |
| West Europe   | ✓ |
| Batı Hindistan | |
| Batı ABD   | ✓ |
| Batı ABD 2 | ✓ |

## <a name="next-steps"></a>Sonraki adımlar

* [Kaynak dönüştürme](data-flow-source.md)oluşturmayı öğrenin.
* Veri akışlarınızı [hata ayıklama modunda](concepts-data-flow-debug-mode.md)oluşturmayı öğrenin.
