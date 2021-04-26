---
title: Veri akışları
description: Azure SYNAPSE Analytics 'teki veri akışlarına genel bakış
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 4769cc8abe121625f3bf77785cd681c0f649d166
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567698"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te veri akışları

## <a name="what-are-data-flows"></a>Veri akışları nelerdir?

Veri akışları, Azure SYNAPSE Analytics 'te görsel olarak tasarlanan veri dönüştürmelerdir. Veri akışları, veri mühendislerinin kod yazmadan veri dönüştürme mantığı geliştirmesini sağlar. Elde edilen veri akışları, ölçeklendirilmemiş Apache Spark kümelerini kullanan Azure SYNAPSE Analytics işlem hatları içinde etkinlik olarak yürütülür. Veri akışı etkinlikleri, mevcut Azure SYNAPSE Analytics zamanlama, denetim, akış ve izleme özellikleri kullanılarak gerçekleştirilebilir.

Veri akışları, hiçbir kodlamaya gerek olmadan tamamen görsel bir deneyim sağlar. Veri akışlarınız, ölçeği genişletilmiş veri işleme için SYNAPSE tarafından yönetilen yürütme kümelerinde çalışır. Azure SYNAPSE Analytics, tüm kod çevirisi, yol iyileştirmesi ve veri akışı işlerinizin yürütülmesini işler.

## <a name="getting-started"></a>Kullanmaya başlama

Veri akışları, SYNAPSE Studio 'daki geliştir bölmesinden oluşturulur. Veri akışı oluşturmak için, **Geliştir**' ın yanındaki artı işaretini ve ardından **veri akışı**' nı seçin. 

![Yeni veri akışı](media/data-flow/new-data-flow.png)

Bu eylem sizi, dönüşüm mantığınızı oluşturabileceğiniz veri akışı tuvaline götürür. Kaynak dönüştürmeyi yapılandırmaya başlamak için **Kaynak Ekle** ' yi seçin. Daha fazla bilgi için bkz. [kaynak dönüştürme](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="authoring-data-flows"></a>Veri akışları yazma

Veri akışı, dönüştürme mantığını daha kolay hale getirmek için tasarlanan benzersiz bir yazma tuvaline sahiptir. Veri akışı tuvali üç parçaya ayrılmıştır: üst çubuk, grafik ve yapılandırma paneli. 

![Ekran görüntüsünde, etiketli üst çubuk, grafik ve yapılandırma paneli ile veri akışı tuvali gösterilmektedir.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Graf

Grafik, dönüşüm akışını görüntüler. Bir veya daha fazla havuza akan kaynak verilerinin kökenini gösterir. Yeni bir kaynak eklemek için **Kaynak Ekle**' yi seçin. Yeni bir dönüşüm eklemek için, varolan bir dönüşümün sağ alt köşesindeki artı işaretini seçin. [Veri akışı grafiğinin nasıl yönetileceği](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)hakkında daha fazla bilgi edinin.

![Ekran görüntüsü, bir arama metin kutusuyla tuvalin grafik bölümünü gösterir.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Yapılandırma bölmesi

Yapılandırma panelinde seçili olan dönüştürmeye özgü ayarlar gösterilir. Hiçbir dönüşüm seçilmezse, veri akışını gösterir. Genel veri akışı yapılandırmasında **Parametreler** sekmesini kullanarak parametreler ekleyebilirsiniz. Daha fazla bilgi için bkz. [veri akışı parametreleri](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Her dönüşümde en az dört Yapılandırma sekmesi bulunur.

#### <a name="transformation-settings"></a>Dönüştürme ayarları

Her bir dönüşümün yapılandırma bölmesindeki ilk sekme, bu dönüştürmeye özgü ayarları içerir. Daha fazla bilgi için bkz. bu dönüşümün belge sayfası.

![Kaynak ayarları sekmesi](media/data-flow/source-1.png)

#### <a name="optimize"></a>İyileştirme

**Optimizasyon** sekmesi, bölümleme düzenlerini yapılandırma ayarlarını içerir. Veri akışlarınızı iyileştirme hakkında daha fazla bilgi edinmek için bkz. [eşleme veri akışı performans Kılavuzu](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Ekran görüntüsü en Iyileştirme sekmesini gösterir](media/data-flow/optimize.png)

#### <a name="inspect"></a>Bilgiyi

**İnceleme** sekmesi, dönüştürmakta olduğunuz veri akışının meta verilerine bir görünüm sağlar. Sütun sayılarını, değişen sütunları, eklenen sütunları, veri türlerini, sütun sırasını ve sütun başvurularını görebilirsiniz. **İnceleme** , meta verilerinizin salt okunurdur görünümüdür. **İnceleme** bölmesinde meta verileri görmek için hata ayıklama modunun etkin olması gerekmez.

![İnceleme sekmesi](media/data-flow/inspect.png)

Dönüşümlerinizi kullanarak verilerinizin şeklini değiştirirken, **İnceleme** bölmesinde meta veri değişiklikleri akışını görürsünüz. Kaynak dönüşümünüze tanımlı bir şema yoksa, veriler **İnceleme** bölmesinde görünmez. Şema DRFT senaryolarında meta verilerin bulunmaması yaygındır.

#### <a name="data-preview"></a>Veri önizlemesi

Hata ayıklama modu açık ise, **veri önizleme** sekmesi her dönüşümde verilerin etkileşimli bir anlık görüntüsünü sunar. Daha fazla bilgi için bkz. [hata ayıklama modunda veri önizlemesi](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview).

### <a name="top-bar"></a>Üst çubuk

Üst çubuk, doğrulama ve hata ayıklama ayarları gibi tüm veri akışını etkileyen eylemler içerir. Ayrıca, dönüştürme mantığınızın temel alınan JSON kodunu ve veri akışı komut dosyasını da görüntüleyebilirsiniz.

## <a name="available-transformations"></a>Kullanılabilir dönüşümler

Kullanılabilir dönüşümlerinin bir listesini almak için [eşleme veri akışı dönüşümüne genel bakış ' ı](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) görüntüleyin.

## <a name="data-flow-activity"></a>Veri akışı etkinliği

Veri akışları, [veri akışı etkinliği](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)kullanılarak Azure SYNAPSE Analytics işlem hatları içinde kullanıma alınır. Tüm kullanıcılar yapması gereken tümleştirme çalışma zamanının parametre değerlerini ne şekilde kullanacağınızı ve geçisyonunu belirtir. Daha fazla bilgi için [Azure tümleştirme çalışma zamanı](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime)hakkında bilgi edinin.

## <a name="debug-mode"></a>Hata ayıklama modu

Hata ayıklama modu, veri akışlarınızı oluştururken ve hata ayıkladığınızda her bir dönüştürme adımının sonuçlarını etkileşimli olarak görmenizi sağlar. Hata ayıklama oturumu, veri akışı mantığınızı oluştururken ve veri akışı etkinlikleriyle işlem hattı hata ayıklama çalıştırmaları çalışırken kullanılabilir. Daha fazla bilgi için bkz. [hata ayıklama modu belgeleri](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="monitoring-data-flows"></a>Veri akışlarını izleme

Veri akışı, mevcut Azure SYNAPSE Analytics izleme özellikleri ile tümleşir. Veri akışı izleme çıkışını nasıl anlayacağınızı öğrenmek için bkz. [eşleme veri akışlarını izleme](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Azure SYNAPSE Analytics ekibi, iş mantığınızı oluşturduktan sonra veri akışlarınızın yürütme süresini iyileştirmenize yardımcı olmak için bir [performans ayarlama Kılavuzu](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) oluşturdu.

## <a name="next-steps"></a>Sonraki adımlar

* [Kaynak dönüştürme](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)oluşturmayı öğrenin.
* Veri akışlarınızı [hata ayıklama modunda](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)oluşturmayı öğrenin.
