---
title: 'Öğretici: sorunları bulmak için Batch testi-LUSıS'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, uygulamanızda söylenişi tahmin sorunlarını bulmak ve bunları çözmek için toplu testlerin nasıl kullanılacağı gösterilmektedir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 68a0016e034f4642c4e4ff166a1456f7ecf1ee3c
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904226"
---
# <a name="tutorial-batch-test-data-sets"></a>Öğretici: Batch test veri kümeleri

Bu öğreticide, uygulamanızda söylenişi tahmin sorunlarını bulmak ve bunları çözmek için toplu testlerin nasıl kullanılacağı gösterilmektedir.  

Batch testi, etkin, eğitilen modelin durumunu bilinen bir etiketli utters ve varlık kümesiyle doğrulamanızı sağlar. JSON biçimli toplu iş dosyasında, utlaslar ' ı ekleyin ve ihtiyacınız olan varlık etiketlerini utterance içinde tahmin edin. 

Batch test gereksinimleri:

* Test başına maksimum 1000 utterya. 
* Yinelenen yok. 
* İzin verilen varlık türleri: yalnızca, basit ve kompozit 'ın öğrenildiği varlıkları. Toplu işlem testi yalnızca, öğrenilen amaçlar ve varlıklar için yararlıdır.

Bu öğreticiden başka bir uygulama kullanırken, bir amaca zaten eklenmiş olan örnek utbotları *kullanmayın.* 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Örnek uygulamayı içeri aktar
> * Batch test dosyası oluşturma 
> * Batch testi çalıştırma
> * Test sonuçlarını gözden geçirme
> * Hataları giderme 
> * Toplu işi yeniden test etme

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Örnek uygulamayı içeri aktar

Son öğreticide oluşturulan **HumanResources** adlı uygulamayla devam edin. 

Aşağıdaki adımları kullanın:

1.  [Uygulama JSON dosyasını](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json) indirip kaydedin.

2. JSON'ı yeni bir uygulamaya içeri aktarın.

3. **Yönet** bölümünde **Sürümler** sekmesinde sürümü kopyalayın ve `batchtest` olarak adlandırın. Kopyalama, özgün sürümünüzü etkilemeden farklı LUIS özelliklerini deneyebileceğiniz ideal bir yol sunar. Sürüm adı URL rotasının bir parçası olarak kullanıldığından ad bir URL'de geçerli olmayan hiçbir karakter içeremez. 

4. Uygulamayı eğitin.

## <a name="batch-file"></a>Toplu iş dosyası

1. Bir metin düzenleyicisinde `HumanResources-jobs-batch.json` oluşturun veya [indirin](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json) . 

2. JSON biçimli toplu iş dosyasında, testte tahmin etmek istediğiniz **amacı** ile utterslar ekleyin. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Batch 'i çalıştırma

1. Üst gezinti çubuğunda **Test** ' i seçin. 

2. Sağ taraftaki panelde **Batch test paneli** ' ni seçin. 

    [Batch test paneli vurgulanmış HALSıS uygulamasının ekran görüntüsünü ![](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. **Veri kümesini Içeri aktar**seçeneğini belirleyin.

    [Içeri aktarma veri kümesi vurgulanmış LUO uygulamasının ekran görüntüsünü ![](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. `HumanResources-jobs-batch.json` dosyanın dosya konumunu seçin.

5. Veri kümesini `intents only` adlandırın ve **bitti**' yi seçin.

    ![Dosya Seç](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. **Çalıştır** düğmesini seçin. 

7. **Sonuçları göster**' i seçin.

8. Grafik ve göstergedeki sonuçları gözden geçirin.

    [Batch test sonuçlarıyla HALSıS uygulamasının ekran görüntüsünü ![](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Toplu sonuçları İncele

Batch grafiğinde dört çeyrek daire görüntülenir. Grafiğin sağında bir filtre vardır. Filtre, amaçları ve varlıkları içerir. Grafiğin bir [bölümünü](luis-concept-batch-test.md#batch-test-results) veya grafik içindeki bir noktayı seçtiğinizde, ilişkili söyleyler grafiğin altında görüntülenir. 

Grafiğin üzerine gelindiğinde fare tekerleği grafikteki görüntüyü büyütebilir veya azaltabilir. Bu, grafikte sıkı bir şekilde kümelenmiş çok sayıda noktaya sahip olduğunda faydalıdır. 

Grafik, kırmızı renkte iki bölümden oluşan dört Quadrants içinde yer alır. **Bunlar, odaklanabilecek bölümlerdir**. 

### <a name="getjobinformation-test-results"></a>Getjobınformation test sonuçları

Filtrede görüntülenen **Getjobınformation** test sonuçları, dört tahmine göre 2 ' nin başarılı olduğunu gösterir. Grafiğin altındaki diğer adımları görmek için sol alt çeyrek altında **yanlış negatif** adını seçin. 

Kullanıcı utisin tam metnini görmek için CTRL + E klavye tuşlarını kullanarak etiket görünümüne geçin. 

Söylenişi `Is there a database position open in Los Colinas?`, _getjobınformation_ olarak etiketlidir ancak geçerli model, bir _applyforjob_olarak gösterilen şekilde tahmin edilir. 

**Getjobınformation**'Ten **applyforjob** için pek çok örnek vardır. Bu örnek, **Applyforjob** hedefinin kullanım açısından, yanlış tahmine neden olacak şekilde tartılanmalar. 

Her iki amaç da aynı hata sayısına sahip olduğuna dikkat edin. Tek bir amaç için yanlış tahmin, diğer amacı da etkiler. Her ikisi de hatalar vardır çünkü bu, bir amaç için yanlış tahmin edildiğinden ve aynı zamanda başka bir amaç için tahmin edilmez. 

<a name="fix-the-app"></a>

## <a name="how-to-fix-the-app"></a>Uygulamayı çözme

Bu bölümün amacı, uygulamayı düzelterek **Getjobınformation** için tüm utallerin doğru şekilde tahmin edilebileceği bir uygulamadır. 

Bu toplu iş dosyası araslarını doğru amaca eklemek, görünüşte hızlı bir düzeltme olacaktır. Bu, yapmak istediğiniz şeydir. LU, bu utbotları örnek olarak eklemeden doğru şekilde tahmin etmek istiyorsunuz. 

Ayrıca, utterlik miktarı **Getjobınformation**ile aynı olana kadar **applyforjob** 'tan gelen dıklarından kaldırma işlemini de merak edebilirsiniz. Bu, test sonuçlarını çözebilecek, ancak bu amacı daha doğru bir şekilde tahmin ediyordu. 

Bu çözüm, **Getjobınformation**için daha fazla bildirim eklemektir. İş için uygulanamamakta olan iş bilgilerini bulma amacını hala hedeflerken, söylenişi uzunluğunu, sözcük seçimini ve sözcük düzenlemesini değiştirmeyi unutmayın.

### <a name="add-more-utterances"></a>Daha fazla söylenme ekleyin

1. Üst gezinti panelindeki **Test** düğmesini seçerek Batch test paneli ' ni kapatın. 

2. Amaçlar listesinden **Getjobınformation** öğesini seçin. 

3. `resume`, `c.v.`ve `apply`terimleri dahil ettiğinizden emin olmak için uzunluk, kelime seçimi ve Word düzenlemesi için farklılaştırmaya uygun olan diğer basamaklar ekleyin:

    |**Getjobınformation** hedefi için örnek bildirimler|
    |--|
    |Bir Stoker için ambardaki yeni iş, bir özgeçmişle uygulamam gerekir mi?|
    |Bugün Roofing işleri nerede?|
    |Bir özgeçmişi gerektiren bir sağlık kodlama işi olduğunu duydum.|
    |Okul çocuklarınızın c.v.s. yazmasına yardımcı olmaya yönelik bir iş istiyorum |
    |İşte, bu, topluluk üniversite bilgisayarları kullanarak yeni bir gönderi arıyor.|
    |Alt ve evde hangi pozisyonlar mevcuttur?|
    |Gazete üzerinde bir Intern masası var mı?|
    |My C.v. tedarik, bütçe ve kayıp parayı çözümlemede iyi olduğunu gösterir. Bu tür bir iş için herhangi bir şey var mı?|
    |Dünya işleri şu anda nerede ayrıntıya?|
    |EMS sürücüsü olarak 8 yıl çalıştım. Tüm yeni işler mi?|
    |Yeni yiyecek işleme işleri uygulama gerektiriyor mu?|
    |Kaç tane yeni bahçe işi işi var?|
    |İşgücü ilişkileri ve anlaşmaları için yeni bir ık post var mı?|
    |Kitaplıkta ve arşiv yönetiminde ana yöneticim var. Tüm yeni pozisyonlar?|
    |Şehrde Bugün 13 yıllık Old için bir iş işi var mı?|

    **Işle iş** varlığını etiketlemeyin. Öğreticinin bu bölümü yalnızca amaç tahminini odaklanmıştır.

4. Sağ üst gezinti bölmesinde **eğitme** ' i seçerek uygulamayı eğitme.

## <a name="verify-the-new-model"></a>Yeni modeli doğrula

Toplu iş testinde yer alan çıkışların doğru şekilde tahmin olduğunu doğrulamak için Batch testini yeniden çalıştırın.

1. Üst gezinti çubuğunda **Test** ' i seçin. Toplu iş sonuçları hala açıksa **listeye geri dön**seçeneğini belirleyin.  

1. Toplu işlem adının sağ tarafındaki üç nokta (***...***) düğmesini seçin ve **Çalıştır**' ı seçin. Batch testi tamamlanana kadar bekleyin. **Sonuçları göster** düğmesinin artık yeşil olduğuna dikkat edin. Bu, tüm toplu işin başarıyla çalıştırıldığı anlamına gelir.

1. **Sonuçları göster**' i seçin. Amaçlar, amaç adlarının solunda yeşil simgelere sahip olmalıdır. 

## <a name="create-batch-file-with-entities"></a>Varlıklarla Batch dosyası oluşturma 

Bir toplu iş testinde varlıkları doğrulamak için, varlıkların Batch JSON dosyasında etiketlenmesi gerekir. 

Toplam sözcük ([belirteç](luis-glossary.md#token)) sayısı için varlıkların çeşitlemesi tahmin kalitesini etkileyebilir. Etiketli olarak etiketlenen eğitim verilerinin çok sayıda varlık uzunluğu içerdiğinden emin olun. 

Toplu iş dosyalarını ilk kez yazarken ve test ederken, çalışmayı bildiğiniz birkaç göz önünde ve varlıkla başlamak en iyisidir ve yanlış tahmin edilebilir. Bu, sorunlu alanlara hızlıca odaklanmanıza yardımcı olur. Tahmin edilen birkaç farklı Iş adı kullanarak **Getjobınformation** ve **applyforjob** amaçlarını test ettikten sonra, bu toplu Işlem test dosyası, **iş** varlığı için belirli değerlerle ilgili bir tahmin sorunu olup olmadığını görmek üzere geliştirilmiştir. 

Test utsları 'nda belirtilen bir **iş** varlığının değeri genellikle bir veya iki sözcükten oluşur ve birkaç örnek daha fazla kelime olur. _Kendi_ insan kaynakları uygulamanızda genellikle birçok sözcükten oluşan iş adları varsa, bu uygulamadaki **iş** varlığı ile etiketlenmiş örnekler iyi çalışmaz.

1. [Vscode](https://code.visualstudio.com/) gibi bir metin düzenleyicisinde `HumanResources-entities-batch.json` oluşturun veya [indirin](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json) .

2. JSON biçimli toplu iş dosyasında, test sırasında tahmin etmek istediğiniz **Amaç** ile, her türlü varlıkların konumunu içeren bir nesne dizisi ekleyin. Bir varlık belirteç tabanlı olduğundan, bir karakter üzerinde her bir varlığı başlatıp durdurduğunuzdan emin olun. Bir boşluk üzerinde başlayamaz veya bitmeyin. Bu, toplu işlem dosyası içeri aktarma sırasında hataya neden olur.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Toplu işi varlıklarla çalıştırın

1. Üst gezinti çubuğunda **Test** ' i seçin. 

2. Sağ taraftaki panelde **Batch test paneli** ' ni seçin. 

3. **Veri kümesini Içeri aktar**seçeneğini belirleyin.

4. `HumanResources-entities-batch.json` dosyanın dosya sistemi konumunu seçin.

5. Veri kümesini `entities` adlandırın ve **bitti**' yi seçin.

6. **Çalıştır** düğmesini seçin. Sınama tamamlanana kadar bekleyin.

7. **Sonuçları göster**' i seçin.

## <a name="review-entity-batch-results"></a>Varlık toplu iş sonuçlarını gözden geçirme

Grafik, tüm hedefleri doğru şekilde tahmin edilen şekilde açılır. Hata içeren varlık tahminlerini bulmak için sağ taraftaki filtrenin altına gidin. 

1. Filtredeki **iş** varlığını seçin.

    ![Filtredeki hata varlığı tahminleri](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    Grafik, varlık tahminlerini görüntüleyecek şekilde değişir. 

2. Grafiğin sol alt çeyreğinden **yanlış negatif** ' ı seçin. Ardından, belirteç görünümüne geçmek için klavye birleşimi denetimi + E ' yi kullanın. 

    [varlık tahminlerinin belirteç görünümü ![](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    Grafik altındaki söyleyleri gözden geçirmek, Iş adı `SQL`içerdiğinde tutarlı bir hata gösterir. Örnek uttaslarını ve Iş tümceciği listesini gözden geçirmek, SQL yalnızca bir kez ve yalnızca daha büyük bir iş adının parçası olarak `sql/oracle database administrator`kullanılır.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Varlık toplu iş sonuçlarına göre uygulamayı düzeltir

Uygulamanın düzeltilme işlemi, SQL işlerinin çeşitlerinin doğru şekilde belirlenmesi için lusıs gerektirir. Bu düzeltmeyle ilgili birkaç seçenek vardır. 

* SQL kullanan ve bu kelimeleri bir Iş varlığı olarak etiketleyip daha fazla örnek, açıkça ekleyin. 
* Tümcecik listesine açıkça daha fazla SQL işi ekleyin

Bu görevler sizin için ayrıldınız.

Varlığın doğru tahmin yapılmadan önce bir [model](luis-concept-patterns.md) eklenmesi sorunu çözmeyecek. Bunun nedeni, düzendeki tüm varlıkların algılanana kadar düzenin eşleşmemesi. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Öğretici, geçerli modelle ilgili sorunları bulmak için bir Batch testi kullandı. Değişiklik doğru olduğundan emin olmak için model düzeltildi ve toplu iş dosyasıyla yeniden test edildi.

> [!div class="nextstepaction"]
> [Desenler hakkında bilgi edinin](luis-tutorial-pattern.md)

