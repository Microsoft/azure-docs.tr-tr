---
title: Batch testi gerçekleştirme-LUSıS
titleSuffix: Azure Cognitive Services
description: Yanlış amaçlar ve varlıklara sahip olan uttersları bulmak için Language Understanding (LUSıS) toplu işlem testi kümelerini kullanın.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: bfef7eae7158a05b09a3534e8fb44335333d8cf1
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904348"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Bir dizi örnek ile toplu test

 Toplu işlem testi, geçerli eğitilen modelinize ait, LUSıS 'deki performansını ölçecek kapsamlı bir sınamadır. Toplu iş testi için kullanılan veri kümeleri, tahmin çalışma zamanı uç noktasından alınan amaçlar veya deterlerden örnek olarak yer almamalıdır. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Batch testi için bir veri kümesi dosyasını içeri aktarma

1. Üstteki çubukta **Test** ' i seçin ve ardından **Batch test paneli**' ni seçin.

    ![Batch test bağlantısı](./media/luis-how-to-batch-test/batch-testing-link.png)

2. **Veri kümesini Içeri aktar**seçeneğini belirleyin. **Yeni veri kümesini Içeri aktar** iletişim kutusu görünür. **Dosya Seç** ' i seçin ve test etmek için *1.000 ' den fazla* yer içermeyen doğru [JSON biçimine](luis-concept-batch-test.md#batch-file-format) sahip bir JSON dosyası bulun.

    İçeri aktarma hataları tarayıcının üst kısmındaki kırmızı bir bildirim çubuğunda raporlanır. İçeri aktarma hataları olduğunda, hiçbir veri kümesi oluşturulmaz. Daha fazla bilgi için bkz. [genel hatalar](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. **Veri kümesi adı** alanına veri kümesi dosyanız için bir ad girin. Veri kümesi dosyası *etiketli amaç* ve *varlıklar*da dahil olmak üzere bir **dizi** yer içerir. Sözdizimi için [örnek toplu iş dosyasını](luis-concept-batch-test.md#batch-file-format) gözden geçirin. 

4. **Done** (Bitti) öğesini seçin. Veri kümesi dosyası eklenir.

## <a name="run-rename-export-or-delete-dataset"></a>Veri kümesini çalıştırma, yeniden adlandırma, dışarı aktarma veya silme

Veri kümesini çalıştırmak, yeniden adlandırmak, dışarı aktarmak veya silmek için veri kümesi satırının sonundaki üç nokta (***...***) düğmesini kullanın.

![Veri kümesi eylemleri](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Eğitilen uygulamanızda Batch testi çalıştırma

Testi çalıştırmak için veri kümesi adını seçin. Test tamamlandığında, bu satır veri kümesinin test sonucunu görüntüler.

![Batch test sonucu](./media/luis-how-to-batch-test/run-test.png)

İndirilebilir veri kümesi, toplu test için karşıya yüklenen dosyadır.

|Durum|Anlamı|
|--|--|
|![Başarılı test yeşil daire simgesi](./media/luis-how-to-batch-test/batch-test-result-green.png)|Tüm söyleymalar başarılı.|
|![Kırmızı x simgesi başarısız test](./media/luis-how-to-batch-test/batch-test-result-red.png)|En az bir söylenişi hedefi tahmine uymuyor.|
|![Teste hazırlanma simgesi](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Test çalıştırılmaya hazırlanıyor.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Batch test sonuçlarını görüntüle 

Batch test sonuçlarını gözden geçirmek için **sonuçları göster**' i seçin.

![Batch test sonuçları](./media/luis-how-to-batch-test/run-test-results.png)

<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>Grafik sonuçlarını filtrele

Grafiği belirli bir amaç veya varlığa göre filtrelemek için, sağ taraftaki filtreleme panelinde amacı veya varlığı seçin. Veri noktaları ve kendi dağıtım güncelleştirmeleri seçiminize göre grafiğe göre yapılır. 
 
![Görselleştirilen Batch test sonucu](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Tek noktalı söylenişi verilerini görüntüle

Grafikte, tahminiyle ilgili belirsizlik Puanını görmek için bir veri noktasının üzerine gelin. Sayfanın alt kısmındaki utterlik listesine karşılık gelen utterlik listesini almak için bir veri noktası seçin. 

![Seçili söylenişi](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Bölüm verilerini görüntüle

Dört bölümden oluşan grafikte, grafiğin sağ üst köşesinde **yanlış pozitif** gibi bölüm adını seçin. Grafiğin altında, bu bölümdeki tüm söyleymalar bir listede grafiğin altında görüntülenir. 

![Bölüme göre seçilen söyleyleri](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Bu önceki görüntüde, söylenişi `switch on`, birlikte çalışan amaç ile etiketlidir, ancak None hedefinin tahminini almış olur. Bu, beklenen tahmini yapmak için, en çok daha fazla örnek elde edilmesi gerektiğini belirten bir göstergesidir. 

Grafiğin kırmızı renkte iki bölümü, beklenen tahminiyle eşleşmeyen utterlere işaret ediyor. Bu, LUIN daha fazla eğitim ihtiyacı olan SÖYLERE işaret ediyor. 

Yeşil renkte grafiğin iki bölümü beklenen tahminiyle eşleşiyor.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Sonraki adımlar

Test, LUP uygulamanızın doğru amaçları ve varlıkları algıladığını gösteriyorsa, daha fazla yer etiketleyerek veya özellik ekleyerek LUSıS uygulamanızın performansını iyileştirebilmek için çalışabilirsiniz. 

* [LUSıS ile önerilen bir şekilde etiketleme](luis-how-to-review-endpoint-utterances.md) 
* [LUSıS uygulamanızın performansını artırmak için özellikleri kullanın](luis-how-to-add-features.md) 
* [Toplu iş testini bu öğreticiyle anlayın](luis-tutorial-batch-testing.md)
* [Batch test kavramlarını öğrenin](luis-concept-batch-test.md).
