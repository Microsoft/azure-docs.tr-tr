---
title: Düzenler-form tanıyıcı
titleSuffix: Azure Cognitive Services
description: Tanıyıcı API kullanımı ve limitleriyle düzen analizine ilişkin kavramları öğrenin.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: cc19d5652f416657cbcd339de61379265587b4e7
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505391"
---
# <a name="form-recognizer-layout-service"></a>Form tanıyıcı düzen hizmeti

Azure form tanıyıcı, düzen hizmetini kullanarak belgelerden metin, tablo, seçim işareti ve yapı bilgilerini ayıklayabilir. Düzen API 'SI, müşterilerin belgeleri çeşitli biçimlerde almasını ve belgelerin yapılandırılmış veri gösterimlerini döndürmesini sağlar. Metin, tablo, seçim işaretleri ve belge yapısını ayıklamak için derin öğrenme modelleriyle güçlü [optik karakter tanıma (OCR)](../computer-vision/overview-ocr.md) olanaklarımızı birleştirir. 

## <a name="what-does-the-layout-service-do"></a>Düzen hizmeti ne yapar?

Düzen API 'SI, önemli doğruluk içeren belgelerden metin, tablo, seçim işareti ve yapı bilgilerini ayıklar ve düzenlenmiş, yapılandırılmış, JSON yanıtını döndürür. Belgeler, telefon ile yakalanan görüntüler, taranmış belgeler ve dijital PDF 'Ler dahil olmak üzere çeşitli biçimlerdeki ve kaliteden oluşabilir. Düzen API 'SI Bu belgelerden yapılandırılan çıktıyı doğru şekilde ayıklar.

![Düzen örneği](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Deneyin

Form tanıyıcı düzen hizmetini denemek için çevrimiçi örnek UI aracına gidin:

> [!div class="nextstepaction"]
> [Form tanıyıcıyı deneyin](https://fott-preview.azurewebsites.net)

Form tanıyıcı düzeni API 'sini denemek için bir Azure aboneliğine ([ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)) ve bir [form tanıyıcı kaynak](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) uç noktası ve anahtarına ihtiyacınız olacaktır. 

![Örnek UI ekran görüntüsü; bir belgenin metni, tabloları ve seçim işaretleri çözümlenmekte](./media/analyze-layout.png)

### <a name="input-requirements"></a>Giriş gereksinimleri 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Düzen çözümleme işlemi

İlk olarak, [Düzen çözümleme](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync) işlemini çağırın. Düzeni çözümle, giriş olarak bir belge (görüntü, TIFF veya PDF dosyası) alır ve metin, tablolar, seçim işaretleri ve belgenin yapısını ayıklar. Çağrı, adlı bir yanıt üst bilgisi alanı döndürüyor `Operation-Location` . `Operation-Location`Değer, bir sonraki adımda kullanılacak sonuç kimliğini içeren BIR URL 'dir.

|Yanıt üst bilgisi| Sonuç URL 'SI |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>Doğal okuma sırası çıkışı (yalnızca Latin)

Sorgu parametresi ile metin çizgilerinin çıkış sırasını belirtebilirsiniz `readingOrder` . `natural`Aşağıdaki örnekte gösterildiği gibi, daha kolay okunabilir bir okuma sırası çıkışı için kullanın. Bu özellik yalnızca Latin dilleri için desteklenir.

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="Düzen okuma düzeni örneği" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Metin ayıklama için sayfa numaralarını veya aralıklarını seçin

Büyük çok sayfalı belgeler için, `pages` metin ayıklama için belirli sayfa numaralarını veya sayfa aralıklarını belirtmek üzere sorgu parametresini kullanın. Aşağıdaki örnek, her iki durumda da tüm sayfalar (1-10) ve seçili 3-6 Sayfalar için metin ayıklanarak 10 sayfalı bir belge gösterir.

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="Seçili sayfaların çıkışını yerleştir çıktısı":::

## <a name="the-get-analyze-layout-result-operation"></a>Düzen çözümleme sonucunu al işlemi

İkinci adım, [Çözümleme düzenleme sonucunu al](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult) işlemini çağıracaktır. Bu işlem, düzen çözümleme işlemi tarafından oluşturulan sonuç KIMLIĞINI giriş olarak alır. Aşağıdaki olası değerlere sahip bir **durum** alanı IÇEREN bir JSON yanıtı döndürür. 

|Alan| Tür | Olası değerler |
|:-----|:----:|:----|
|durum | string | `notStarted`: Analiz işlemi başlatılmamış.<br /><br />`running`: Analiz işlemi devam ediyor.<br /><br />`failed`: Çözümleme işlemi başarısız oldu.<br /><br />`succeeded`: Çözümleme işlemi başarılı oldu.|

Bu işlemi, değeri döndürdüğünden tekrarlayarak çağırın `succeeded` . Saniye başına istek (RPS) oranını aşmamak için 3 ile 5 saniye arasında bir Aralık kullanın.

**Durum** alanı `succeeded` DEĞERI olduğunda, JSON yanıtı ayıklanan düzen, metin, tablolar ve seçim işaretlerini içerecektir. Ayıklanan veriler, ayıklanan metin çizgileri ve kelimeleri, sınırlayıcı kutuları, el yazısı gösterge, tablolar ve seçim işaretleriyle seçili/seçilmemiş olarak gösterilen metin görünümünü içerir. 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Metin satırları için el ile sınıflandırma (yalnızca Latin)

Yanıt, her metin satırının el yazısı stili olup olmadığı sınıflandırıp bir güven puanı ile birlikte yer alır. Bu özellik yalnızca Latin dilleri için desteklenir. Aşağıdaki örnek görüntüdeki metin için el yazısı sınıflandırmasını gösterir.

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="El yazısı sınıflandırması örneği":::

### <a name="sample-json-output"></a>Örnek JSON çıkışı

*Düzen çözümleme sonucunu al* işleminin yanıtı, ayıklanan tüm bilgilerin bulunduğu belgenin yapılandırılmış bir gösterimidir. [Örnek belge dosyası](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) ve yapılandırılmış çıkış [örneği düzen çıktısı](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)için buraya bakın.

JSON çıktısı iki bölümden oluşur:

* `readResults` düğüm, tüm tanınan metin ve seçim işaretlerini içerir. Metin sayfaya, sonra satıra, sonra da tek sözcüklere göre düzenlenir. 
* `pageResults` düğüm, sınırlayıcı kutuları, güvenle ayıklanan tabloları ve hücreleri ve "readResults" içindeki satır ve sözcüklere yönelik bir başvuruyu içerir.

## <a name="example-output"></a>Örnek çıkış

### <a name="text"></a>Metin

Düzen API 'SI, belgelerden (PDF, TIFF) ve görüntülerden (JPG, PNG, BMP) metinleri birden çok metin açısı ve renkleriyle ayıklar. Belge, Faks, yazdırılan ve/veya el yazısı (Yalnızca Ingilizce) metin ve karışık modların fotoğraflarını kabul eder. Metin, çizgiler, kelimeler, sınırlama kutuları, güven puanları ve stil (el ile veya diğer) için belirtilen bilgilerle ayıklanır. Tüm metin bilgileri `readResults` JSON çıktısının bölümüne dahil edilir. 

### <a name="tables"></a>Tables

Düzen API 'SI belgelerden (PDF, TIFF) ve görüntülerden (JPG, PNG, BMP) tabloları ayıklar. Belgeler taranabilir, fotokopi veya dijital olabilir. Tablolar, birleştirilmiş hücreler veya sütunlarla, Kenarlıksız veya Kenarlıksız ve tek açılarla karmaşık olabilir. Ayıklanan tablo bilgileri, sütun ve satır sayısını, satır yayılımını ve sütun yayılımını içerir. Her bir hücre, sınırlayıcı kutusuyla ayıklanır ve bölümünde ayıklanan metne başvurur `readResults` . Tablo bilgileri, `pageResults` JSON çıktısının bölümünde bulunur. 

![Tablolar örneği](./media/tables-example.jpg)

### <a name="selection-marks"></a>Seçim işaretleri

Ayrıca, Düzen API 'SI belgelerden seçim işaretlerini de ayıklar. Ayıklanan seçim işaretleri sınırlayıcı kutusu, güvenirlik ve durumu (seçili/seçilmemiş) içerir. Seçim işareti bilgileri `readResults` JSON çıktısının bölümünde ayıklanır. 

## <a name="next-steps"></a>Sonraki adımlar

* [Form tanıyıcı örneği kullanıcı arabirimi aracını kullanarak kendi düzen ayıklayıcınızı](https://fott-preview.azurewebsites.net/) deneyin
* Seçtiğiniz geliştirme dilinde düzenleri çıkarmaya başlamak için [form tanıyıcı hızlı](quickstarts/client-library.md) başlangıcını doldurun.

## <a name="see-also"></a>Ayrıca bkz.

* [Form Tanıma nedir?](./overview.md)
* [REST API başvuru belgeleri](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
