---
title: Azure Machine Learning anomali algılama API 'SI-ekip veri bilimi Işlemi
description: Anomali algılama API 'si, zaman serisi verilerinde zaman içinde tek bir şekilde boşluk olan sayısal değerlere sahip bozuklukları algılayan Microsoft Azure Machine Learning ile oluşturulmuş bir örnektir.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 06/05/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: 25b08bf78de61e556bab790869b45131a01ce6b8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495115"
---
# <a name="machine-learning-anomaly-detection-api"></a>Machine Learning anomali algılama API 'SI

> [!NOTE]
> Bu öğe bakım aşamasındadır. İş, operasyonel ve IoT ölçümlerinden gelen anormallikleri algılamak için Azure bilişsel Hizmetler kapsamındaki Machine Learning algoritmalarının bir galerisiyle desteklenen [anomali ALGıLAYıCı API hizmetini](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) kullanmanızı öneririz.

## <a name="overview"></a>Genel Bakış
[Anomali ALGıLAMA API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) 'si, zaman serisi verilerinde zaman içinde tek bir şekilde boşluk olan sayısal değerlere sahip bozuklukları algılayan Azure Machine Learning ile oluşturulmuş bir örnektir.

Bu API, zaman serisi verilerinde aşağıdaki türlerde anormal desenleri algılayabilir:

* **Pozitif ve olumsuz eğilimler**: Örneğin, bellek kullanımını izlerken, Bellek sızıntısının bir göstergesi olabileceğinden, daha fazla bilgi
* **Dinamik değer aralığındaki değişiklikler**: Örneğin, bir bulut hizmeti tarafından oluşturulan özel durumları izlerken, dinamik değer aralığındaki tüm değişiklikler hizmetin sistem durumunda dengesizmi olduğunu belirtebilir ve
* **Ani artışlar ve DIB 'ler**: Örneğin, bir hizmette oturum açma hatalarının sayısını veya bir e-ticaret sitesinde kullanıma alma sayısını izlerken, ani veya DIB 'ler anormal davranışları gösterebilir.

Bu makine öğrenimi algılayıcıları, zaman içindeki değerlerde bu değişiklikleri izler ve değerlerinde devam eden değişiklikleri bildirir. Bunlar geçici eşik ayarlama gerektirmez ve bunların puanları, yanlış pozitif oranı denetlemek için kullanılabilir. Anomali algılama API 'SI, zaman içinde KPI 'Ları, arama sayısı gibi ölçümler aracılığıyla kullanım izlemeyi, bellek, CPU, dosya okuma vb. sayaçlar aracılığıyla performans izlemeyi, hizmet izleme gibi çeşitli senaryolarda faydalıdır. zaman içinde.

Anomali algılama teklifi, başlamanıza yardımcı olan yararlı araçlarla birlikte sunulur.

* [Web uygulaması](https://anomalydetection-aml.azurewebsites.net/) , verilerinize ait anomali algılama API 'lerinin sonuçlarını değerlendirmenize ve görselleştirmenize yardımcı olur.

> [!NOTE]
> [Bu API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) tarafından desteklenen **BT anomali Öngörüler çözümünü** deneyin
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API dağıtımı
API 'yi kullanabilmeniz için, bunu bir Azure Machine Learning Web hizmeti olarak barındırılacak Azure aboneliğinize dağıtmanız gerekir.  Bunu [Azure yapay zeka Galerisi](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)yapabilirsiniz.  Bu işlem, Azure aboneliğinize iki Azure Machine Learning Studio (klasik) Web hizmeti (ve bunların ilgili kaynaklarını) dağıtır. Bu, mevsimsellik algılama ile anomali algılama ve bir mevsimlik algılama olmadan bir tane.  Dağıtım tamamlandıktan sonra API 'lerinizi [Azure Machine Learning Studio (klasik) Web Hizmetleri](https://services.azureml.net/webservices/) sayfasından yönetebileceksiniz.  Bu sayfadan, uç nokta konumları, API anahtarlarınızın yanı sıra API 'yi çağırmak için örnek kod de bulabilirsiniz.  Daha ayrıntılı yönergeler [burada](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice)bulunabilir.

## <a name="scaling-the-api"></a>API ölçeklendirme
Varsayılan olarak, dağıtımınız 1.000 işlem/ay ve 2 işlem saati/ay içeren ücretsiz bir geliştirme/test faturalandırma planına sahip olacaktır.  Gereksinimlerinize göre başka bir plana yükseltebilirsiniz.  Farklı planların fiyatlandırmasıyla ilgili ayrıntıları [burada](https://azure.microsoft.com/pricing/details/machine-learning/) "ÜRETIM Web API fiyatlandırması" altında bulabilirsiniz.

## <a name="managing-aml-plans"></a>AML planlarını yönetme
Faturalandırma planınızı [buradan](https://services.azureml.net/plans/)yönetebilirsiniz.  Plan adı, API 'yi dağıttığınızda seçtiğiniz kaynak grubu adına ve aboneliğinize özgü bir dizeye göre yapılır.  Planınızın nasıl yükseltileceğiyle ilgili yönergeler [burada](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) "Faturalandırma planlarını yönetme" bölümünde bulunabilir.

## <a name="api-definition"></a>API tanımı
Web hizmeti, bir Web veya mobil uygulama, R, Python, Excel vb. gibi farklı yollarla tüketilen, HTTPS üzerinden REST tabanlı bir API sağlar.  Zaman serisi verilerinizi bu hizmete REST API bir çağrı aracılığıyla gönderirsiniz ve aşağıda açıklanan üç anomali türünün bir birleşimini çalıştırır.

## <a name="calling-the-api"></a>API çağırma
API 'yi çağırmak için uç nokta konumunu ve API anahtarını bilmeniz gerekecektir.  Bunların her ikisi de API 'yi çağırmak için örnek kod ile birlikte [Azure Machine Learning Studio (klasik) Web Hizmetleri](https://services.azureml.net/webservices/) sayfasından kullanılabilir.  İstenen API 'ye gidin ve ardından bulmak için "kullanma" sekmesine tıklayın.  API 'yi bir Swagger API (yani, URL parametresi `format=swagger`) veya Swagger olmayan bir API (örneğin, `format` URL parametresi olmadan) olarak çağırabileceğinizi unutmayın.  Örnek kod Swagger biçimini kullanır.  Swagger olmayan biçimdeki örnek bir istek ve yanıt aşağıda verilmiştir.  Bu örnekler mevsimsellik uç noktasıdır.  Mevsimsiz olma uç noktası benzerdir.

### <a name="sample-request-body"></a>Örnek Istek gövdesi
İstek iki nesne içeriyor: `Inputs` ve `GlobalParameters`.  Aşağıdaki örnek istekte, bazı parametreler açıkça gönderilir, diğerleri değildir (her bitiş noktası için parametrelerin tam listesi için aşağı kaydırın).  İstekte açıkça gönderilmeyen parametreler aşağıda verilen varsayılan değerleri kullanır.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>Örnek Yanıtı
`ColumnNames` alanını görmek için, `details=true` bir URL parametresi olarak eklemeniz gerektiğini unutmayın.  Bu alanların her birinin arkasındaki anlamı görmek için aşağıdaki tablolara bakın.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>Puanlama API 'SI
Puan API 'SI, dönemsel olmayan zaman serisi verilerinde anomali algılamayı çalıştırmak için kullanılır. API, veriler üzerinde bir dizi anomali algılayıcıları çalıştırır ve anomali puanlarını döndürür.
Aşağıdaki şekilde, skor API 'sinin algılayabildiği anormalilerin bir örneği gösterilmektedir. Bu zaman serisinde 2 farklı düzey değişiklik ve 3 ani artış vardır. Kırmızı noktalar, düzey değişikliğin algılandığı süreyi gösterir, ancak siyah noktalar algılanan ani artışları gösterir.
![Puanlama API 'SI][1]

### <a name="detectors"></a>Algılayıcıları
Anomali algılama API 'SI, 3 geniş kategoride algılayıcıları destekler. Her bir algılayıcısının belirli giriş parametreleri ve çıkışları hakkındaki ayrıntılar aşağıdaki tabloda bulunabilir.

| Algılayıcı kategorisi | Algılayıcısı | Açıklama | Giriş Parametreleri | Çıkışlar |
| --- | --- | --- | --- | --- |
| Ani Detektorler |Tani algılayıcı |Değerleri temel alarak ani artışları ve DIB 'leri algılayın ve ilk ve üçüncü kutalileri |*tspikealgılayıcı. duyarlılık:* 1-10 aralığında tamsayı değeri alır, varsayılan: 3; Daha yüksek değerler daha fazla bilgi yakalar ve bu sayede daha az hassas hale gelir |Tani: ikili değerler – ' 1 ' bir ani/DIP algılanırsa, ' 0 ' Aksi takdirde |
| Ani Detektorler | Zani algılayıcı |Veri noktalarının ne kadar ilerlediğiyle ilgili ani artışları ve DIB 'leri Algıla |*zspikealgılayıcı. duyarlılık:* 1-10 aralığında tamsayı değeri Al, varsayılan: 3; Daha yüksek değerler, daha az hassas hale getirmek için daha fazla bilgi yakalar |Zani: ikili değerler – ' 1 ' bir ani/DIP algılanırsa, ' 0 ' Aksi takdirde |
| Yavaş eğilim algılayıcısı |Yavaş eğilim algılayıcısı |Küme duyarlılığı temelinde yavaş pozitif eğilimi Algıla |*trendalgılayıcısı. duyarlılık:* algılayıcı puanı eşiği (varsayılan: 3,25, 3,25 – 5, bunu seçmek için makul bir aralıktır; Daha az hassas) |tscore: eğilim üzerinde anomali puanı temsil eden kayan sayı |
| Düzey değişim detektorları | Çift yönlü düzey değişiklik algılayıcısı |Küme duyarlılığı temelinde hem yukarı ve aşağı düzey değişikliği Algıla |*bileşik velalgılayıcısı. duyarlılık:* algılayıcı puanı eşiği (varsayılan: 3,25, 3,25 – 5, bunu seçmek için makul bir aralıktır; Daha az hassas) |rpscore: daha yukarı ve aşağı düzey değişiklik üzerinde anomali Puanını temsil eden kayan sayı |

### <a name="parameters"></a>Parametreler
Bu giriş parametreleriyle ilgili daha ayrıntılı bilgi aşağıdaki tabloda listelenmiştir:

| Giriş Parametreleri | Açıklama | Varsayılan ayar | Tür | Geçerli Aralık | Önerilen Aralık |
| --- | --- | --- | --- | --- | --- |
| detektorler. geçmişini penceresi |Anomali puan hesaplaması için kullanılan geçmiş (veri noktası sayısı) |500 |integer |10-2000 |Zaman serisine bağımlı |
| algılayıcıları. spıkesdips | Yalnızca ani artışlar, yalnızca DIB 'ler veya her ikisinin de algılanmayacağı |Her ikisi de |numara |Her ikisi, ani artışlar, DIB 'Ler |Her ikisi de |
| bileşik bir algılayıcı. duyarlılık |Çift yönlü düzey değişiklik algılayıcısı için duyarlılık. |3,25 |double |None |3,25-5 (daha az değer daha hassas anlamına gelir) |
| trendalgılayıcısı. duyarlılık |Pozitif eğilim algılayıcısı için duyarlılık. |3,25 |double |None |3,25-5 (daha az değer daha hassas anlamına gelir) |
| tspikealgılayıcısı. duyarlılığı |Tani algılayıcı duyarlılığı |3 |integer |1-10 |3-5 (daha az değer daha hassas anlamına gelir) |
| zspikealgılayıcı. duyarlılık |Zani algılayıcısı için duyarlılık |3 |integer |1-10 |3-5 (daha az değer daha hassas anlamına gelir) |
| postprocess. edilecek satırlar |Çıkış sonuçlarında tutulacak en son veri noktası sayısı |0 |integer |0 (tüm veri noktalarını sakla) veya sonuçlarda tutulacak noktaların sayısını belirtin |Yok |

### <a name="output"></a>Çıktı
API, zaman serisi verilerinizde tüm algılayıcıları çalıştırır ve zaman içindeki her bir nokta için anomali puanlarını ve ikili ani değer göstergelerini döndürür. Aşağıdaki tabloda API 'den gelen çıktılar listelenmiştir.

| Çıkışlar | Açıklama |
| --- | --- |
| Zaman |Toplama (ve/veya) eksik veri imputation uygulanmışsa ham verilerden veya toplanmış (ve/veya) toplanan verilerin zaman damgaları |
| Veriler |Toplama (ve/veya) eksik veri imputation uygulanmışsa ham verilerden veya toplanmış (ve/veya) toplanan verilerin değerleri |
| Tani |Tani algılayıcı tarafından bir ani algılanıp algılanmadığını belirten ikili gösterge |
| Zani |Zani algılayıcı tarafından bir ani algılanıp algılanmadığını belirten ikili gösterge |
| rpscore |Çift yönlü düzey değişiklik üzerinde anomali Puanını temsil eden bir kayan sayı |
| rpalert |1/0 değeri, giriş duyarlılığı temelinde çift yönlü düzey değişiklik anomali olduğunu belirtir |
| tscore |Olumlu eğilime ilişkin anomali Puanını temsil eden bir kayan sayı |
| talert |1/0 değeri, giriş duyarlılığı temelinde olumlu bir eğilim anomali olduğunu belirtir |

## <a name="scorewithseasonality-api"></a>Scorewithmevsimsellik API 'SI
Scorewithmevsimsellik API 'SI, dönemsel desenleri olan zaman serisinde anomali algılamayı çalıştırmak için kullanılır. Bu API, mevsimsel desenlerdeki sapmaları saptamak için yararlıdır.
Aşağıdaki şekilde, bir mevsimlik zaman serisinde algılanan anormalilerin bir örneği gösterilmektedir. Zaman serisinde bir ani (1. siyah nokta), iki DIP (ikinci siyah nokta ve diğeri sonda) ve bir düzey değişikliği (kırmızı nokta) vardır. Hem zaman serisinin ortadaki DIP 'nin hem de düzey değişikliğin yalnızca, serilerde bir mevsimden kaldırıldıktan sonra yeniden yapılandırabileceğini unutmayın.
![mevsimsellik API 'SI][2]

### <a name="detectors"></a>Algılayıcıları
Mevsimsellik uç noktasındaki detekleyler, mevsimsellik olmayan uç noktada, ancak biraz farklı parametre adlarıyla (aşağıda listelenmiştir) benzerdir.

### <a name="parameters"></a>Parametreler

Bu giriş parametreleriyle ilgili daha ayrıntılı bilgi aşağıdaki tabloda listelenmiştir:

| Giriş Parametreleri | Açıklama | Varsayılan ayar | Tür | Geçerli Aralık | Önerilen Aralık |
| --- | --- | --- | --- | --- | --- |
| preprocess. Aggregationınterval |Toplanan giriş zaman serisi için saniye cinsinden toplama aralığı |0 (toplama yapılmaz) |integer |0: toplamayı atla, > 0, tersi |5 dakika ila 1 gün, zaman serisine bağımlı |
| preprocess. aggregationFunc |Belirtilen Aggregationınterval öğesine veri toplamak için kullanılan işlev |Ortası |numara |Ortalama, toplam, uzunluk |Yok |
| preprocess. replaceMissing |Eksik verileri ımpute için kullanılan değerler |LKV (son bilinen değer) |numara |sıfır, LKV, ortalama |Yok |
| detektorler. geçmişini penceresi |Anomali puan hesaplaması için kullanılan geçmiş (veri noktası sayısı) |500 |integer |10-2000 |Zaman serisine bağımlı |
| algılayıcıları. spıkesdips | Yalnızca ani artışlar, yalnızca DIB 'ler veya her ikisinin de algılanmayacağı |Her ikisi de |numara |Her ikisi, ani artışlar, DIB 'Ler |Her ikisi de |
| bileşik bir algılayıcı. duyarlılık |Çift yönlü düzey değişiklik algılayıcısı için duyarlılık. |3,25 |double |None |3,25-5 (daha az değer daha hassas anlamına gelir) |
| postrendalgılayıcı. duyarlılık |Pozitif eğilim algılayıcısı için duyarlılık. |3,25 |double |None |3,25-5 (daha az değer daha hassas anlamına gelir) |
| negtrendalgılayıcısı. duyarlılık |Negatif eğilim algılayıcısı için duyarlılık. |3,25 |double |None |3,25-5 (daha az değer daha hassas anlamına gelir) |
| tspikealgılayıcısı. duyarlılığı |Tani algılayıcı duyarlılığı |3 |integer |1-10 |3-5 (daha az değer daha hassas anlamına gelir) |
| zspikealgılayıcı. duyarlılık |Zani algılayıcısı için duyarlılık |3 |integer |1-10 |3-5 (daha az değer daha hassas anlamına gelir) |
| mevsimsellik. etkinleştir |Mevsimsellik analizinin gerçekleştirilip gerçekleştirilmeyeceğini belirtir |doğru |boole |doğru, yanlış |Zaman serisine bağımlı |
| mevsimsellik. Nummevsimsellik |Algılanabilmesi için en fazla düzenli döngü sayısı |1 |integer |1, 2 |1-2 |
| mevsimsellik. dönüştürme |Anomali algılamayı uygulamadan önce mevsimlere (ve) eğilim bileşenlerinin kaldırılıp kaldırılmayacağı |yılsezon |numara |hiçbiri, demevsim, demevsimeğilimi |Yok |
| postprocess. edilecek satırlar |Çıkış sonuçlarında tutulacak en son veri noktası sayısı |0 |integer |0 (tüm veri noktalarını sakla) veya sonuçlarda tutulacak noktaların sayısını belirtin |Yok |

### <a name="output"></a>Çıktı
API, zaman serisi verilerinizde tüm algılayıcıları çalıştırır ve zaman içindeki her bir nokta için anomali puanlarını ve ikili ani değer göstergelerini döndürür. Aşağıdaki tabloda API 'den gelen çıktılar listelenmiştir.

| Çıkışlar | Açıklama |
| --- | --- |
| Zaman |Toplama (ve/veya) eksik veri imputation uygulanmışsa ham verilerden veya toplanmış (ve/veya) toplanan verilerin zaman damgaları |
| OriginalData |Toplama (ve/veya) eksik veri imputation uygulanmışsa ham verilerden veya toplanmış (ve/veya) toplanan verilerin değerleri |
| ProcessedData |Aşağıdakilerden birini yapın: <ul><li>Önemli mevsimsellik tespit edildi ve zaman dönemi seçeneği işaretliyse, zaman serisini önemli bir şekilde ayarladı;</li><li>önemli mevsimsellik tespit edildi ve demevsimsel eğilim seçeneği işaretliyse, zaman serisini ayarlama ve zaman serisini azaltma</li><li>Aksi takdirde, bu, OriginalData ile aynıdır</li> |
| Tani |Tani algılayıcı tarafından bir ani algılanıp algılanmadığını belirten ikili gösterge |
| Zani |Zani algılayıcı tarafından bir ani algılanıp algılanmadığını belirten ikili gösterge |
| Bileşik Velchangescore |Düzey değişiklik üzerinde anomali Puanını temsil eden bir kayan sayı |
| Bileşik Velchangealert |1/0 değeri, giriş duyarlılığı temelinde bir düzey değişiklik anomali olduğunu belirtir |
| PosTrendScore |Olumlu eğilime ilişkin anomali Puanını temsil eden bir kayan sayı |
| PosTrendAlert |1/0 değeri, giriş duyarlılığı temelinde olumlu bir eğilim anomali olduğunu belirtir |
| Negtrendpuanı |Olumsuz eğilimin anomali Puanını temsil eden bir kayan sayı |
| Negtrenduyarısı |1/0 değeri, giriş duyarlılığı temelinde negatif bir eğilim anomali olduğunu belirtir |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

