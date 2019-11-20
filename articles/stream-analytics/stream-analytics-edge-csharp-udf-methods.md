---
title: Azure Stream Analytics işleri için .NET Standard işlevleri geliştirme (Önizleme)
description: Stream Analytics işleri için c# Kullanıcı tanımlı işlevleri yazmayı öğrenin.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: 0bc59ac3e55466f8ac06a3a8fa9cf08fecbb5ce3
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024959"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Azure Stream Analytics işleri için .NET Standard Kullanıcı tanımlı işlevler geliştirme (Önizleme)

Azure Stream Analytics, olay verilerinin akışları üzerinde dönüşümler ve hesaplamalar gerçekleştirmek için SQL benzeri bir sorgu dili sunar. Birçok yerleşik işlev vardır ancak bazı karmaşık senaryolar ek esneklik gerektirir. Kullanıcı tanımlı işlevler (UDF) .NET Standard ile, Stream Analytics sorgu dilini genişletmek için herhangi bir .net standart dilinde (C#, F#, vb.) yazılmış kendi işlevlerinizi çağırabilirsiniz. UDF 'ler, karmaşık matematik hesaplamaları gerçekleştirmenizi, ML.NET kullanarak özel ML modellerini içeri aktarmanızı ve eksik veriler için özel imputation mantığı kullanmanıza imkan tanır. Stream Analytics işleri için UDF özelliği şu anda önizlemededir ve üretim iş yükleri içinde kullanılmamalıdır.

Bulut işlerinin .NET Kullanıcı tanımlı işlevi, ' de kullanılabilir:
* Orta Batı ABD (kullanılabilir)
* Kuzey Avrupa (kullanılabilir)
* Doğu ABD (kullanılabilir)
* Batı ABD (yakında kullanıma alınıyor)
* Doğu ABD 2 (yakında kullanıma alınıyor)
* Batı Avrupa (yakında kullanıma alınıyor)

Bu özelliği başka bir bölgede kullanmaya ilgileniyorsanız, [erişim isteyebilirsiniz](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Genel Bakış
Azure Stream Analytics için Visual Studio Araçları, UDF 'Leri yazmanızı, işlerinizi yerel olarak test yapmayı ve Stream Analytics işinizi Azure 'da yayımlamayı kolaylaştırır. Azure 'da yayımlandıktan sonra, IoT Hub kullanarak işinizi IoT cihazlarına dağıtabilirsiniz.

UDF 'Leri uygulamak için üç yol vardır:

* Bir ASA projesindeki CodeBehind dosyaları
* Yerel projeden UDF
* Azure Storage hesabından mevcut bir paket

## <a name="package-path"></a>Paket yolu

Herhangi bir UDF paketinin biçimi `/UserCustomCode/CLR/*`yoludur. Dinamik bağlantı kitaplıkları (dll 'Ler) ve kaynaklar `/UserCustomCode/CLR/*` klasörü altına kopyalanır ve bu, Kullanıcı dll 'Lerinin sistem ve Azure Stream Analytics dll 'lerden yalıtılmasına yardımcı olur. Bu paket yolu, bunları kullanmak için kullanılan yöntemden bağımsız olarak tüm işlevler için kullanılır.

## <a name="supported-types-and-mapping"></a>Desteklenen türler ve eşleme

|**UDF türü (C#)**  |**Azure Stream Analytics türü**  |
|---------|---------|
|Kalacağını  |  bigint   |
|double  |  double   |
|string  |  nvarchar (max)   |
|Hem  |  Hem   |
|sýný  |  IRecord   |
|object  |  IRecord   |
|Dizi\<nesnesi >  |  IArray   |
|sözlük < dize, nesne >  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
Kullanıcı tanımlı işlevleri **Script. ASQL** codebehind içinde yazabilirsiniz. Visual Studio Araçları otomatik olarak CodeBehind dosyasını derleme dosyasına derler. Derlemeler bir zip dosyası olarak paketlenir ve işinizi Azure 'a gönderdiğinizde depolama hesabınıza yüklenir. [ C# Stream Analytics Edge işleri](stream-analytics-edge-csharp-udf.md) öğreticisini izleyerek codebehind C# kullanarak bir UDF yazma hakkında bilgi edinebilirsiniz. 

## <a name="local-project"></a>Yerel proje
Kullanıcı tanımlı işlevler, daha sonra bir Azure Stream Analytics sorgusunda başvurulan bir derlemede yazılabilir. Bu, .NET Standard dilinin, yordamsal mantık veya özyineleme gibi ifade dilinin ötesinde tam gücünü gerektiren karmaşık işlevler için önerilen seçenektir. Yerel bir projeden gelen UDF 'ler, birkaç Azure Stream Analytics sorgu arasında işlev mantığını paylaşmanız gerektiğinde de kullanılabilir. Yerel projenize UDF 'ler eklemek, işlevlerinizi Visual Studio 'dan yerel olarak hata ayıklama ve test etme olanağı sunar.

Yerel bir projeye başvurmak için:

1. Çözümünüzde yeni bir sınıf kitaplığı oluşturun.
2. Kodunuzu sınıfınıza yazın. Sınıfların *ortak* olarak tanımlanması gerektiğini ve nesnelerin *statik ortak*olarak tanımlanması gerektiğini unutmayın. 
3. Projenizi derleyin. Araçlar bin klasöründeki tüm yapıtları bir ZIP dosyasına paketler ve ZIP dosyasını depolama hesabına yükler. Dış başvurular için NuGet paketi yerine derleme başvurusunu kullanın.
4. Azure Stream Analytics projenizdeki yeni sınıfa başvurun.
5. Azure Stream Analytics projenize yeni bir işlev ekleyin.
6. Derleme yolunu iş yapılandırma dosyasında yapılandırın `JobConfig.json`. Derleme yolunu **Yerel proje başvurusu veya codebehind**olarak ayarlayın.
7. Hem işlev projesini hem de Azure Stream Analytics projeyi yeniden derleyin.  

### <a name="example"></a>Örnek

Bu örnekte **udftest** bir C# sınıf kitaplığı projem ve **asaudfdemo** , **udftest**'e başvuracaktır Azure Stream Analytics projem.

![Visual Studio 'da IoT Edge Azure Stream Analytics projesi](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. C# Projenizi oluşturun, bu, Azure Stream Analytics sorgusundan UDF 'nize C# bir başvuru eklemenize olanak tanır.
    
   ![Visual Studio 'da Azure Stream Analytics IoT Edge projesi oluşturma](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Başvuruyu, ASA projesindeki C# projeye ekleyin. Başvurular düğümüne sağ tıklayın ve başvuru Ekle ' yi seçin.

   ![Visual Studio 'da bir C# projeye başvuru ekleme](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Listeden C# proje adını seçin. 
    
   ![Başvuru listesinden C# projenizin adını seçin](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. **Çözüm Gezgini** **Başvurular** altında listelenen **udftest** ' i görmeniz gerekir.

   ![Çözüm Gezgini 'nde Kullanıcı tanımlı işlev başvurusunu görüntüleme](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. **İşlevler** klasörüne sağ tıklayın ve **Yeni öğe**' yi seçin.

   ![Azure Stream Analytics Edge çözümünde IŞLEVLERE yeni öğe ekleme](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Azure Stream Analytics projenize C# bir **squarefunction. JSON** işlevi ekleyin.

   ![Visual Studio 'da Stream Analytics Edge öğelerinden CSharp işlevi seçme](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Yapılandırma iletişim kutusunu açmak için **Çözüm Gezgini** işlevine çift tıklayın.

   ![Visual Studio 'da C Sharp işlev yapılandırması](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. C# İşlev YAPıLANDıRMASıNDA, **asa proje başvurusundan yükle** ' yi ve açılan listeden ilgili derleme, sınıf ve yöntem adlarını seçin. Stream Analytics sorgusundaki yöntemlere, türlere ve işlevlere başvurmak için sınıfların *ortak* olarak tanımlanması ve nesnelerin *statik ortak*olarak tanımlanması gerekir.

   ![Stream Analytics C Sharp işlev yapılandırması](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Mevcut paketler

.NET Standard UDF 'Leri dilediğiniz IDE 'de yazabilir ve Azure Stream Analytics sorgınızdan çağırabilirsiniz. İlk olarak kodunuzu derleyin ve tüm dll 'Leri paketleyin. Paketin biçimi `/UserCustomCode/CLR/*`yoludur. Sonra, Azure Depolama hesabınızdaki kapsayıcının köküne `UserCustomCode.zip` yükleyin.

Azure depolama hesabınıza derleme ZIP paketleri yüklendikten sonra, Azure Stream Analytics sorgulardaki işlevleri kullanabilirsiniz. Tüm yapmanız gereken Stream Analytics iş yapılandırmasındaki depolama bilgilerini içerir. Visual Studio Araçları paketinizi indirmediği için işlevi bu seçenekle yerel olarak test edebilirsiniz. Paket yolu doğrudan hizmete ayrıştırılır. 

İş yapılandırma dosyasında derleme yolunu yapılandırmak için `JobConfig.json`:

**Kullanıcı Tanımlı Kod Yapılandırması** bölümünü genişletin ve yapılandırmaya aşağıdaki önerilen değerleri ekleyin:

   |**Ayar**|**Önerilen Değer**|
   |-------|---------------|
   |Genel depolama ayarları kaynağı|Geçerli hesaptaki veri kaynağını seçin|
   |Genel depolama ayarları aboneliği| aboneliğinizi < >|
   |Genel depolama ayarları depolama hesabı| depolama hesabınızı < >|
   |Özel kod depolama ayarları kaynağı|Geçerli hesaptaki veri kaynağını seçin|
   |Özel kod depolama ayarları depolama hesabı|depolama hesabınızı < >|
   |Özel kod depolama ayarları kapsayıcısı|depolama kapsayıcınızı < >|
   |Özel kod derleme kaynağı|Buluttan mevcut derleme paketleri|
   |Özel kod derleme kaynağı|UserCustomCode. zip|

## <a name="limitations"></a>Sınırlamalar
UDF önizlemesi Şu anda aşağıdaki sınırlamalara sahiptir:

* .NET Standard UDF 'ler yalnızca Visual Studio 'da yazılabilir ve Azure 'da yayımlanabilir. .NET Standard UDF 'Leri salt okuma sürümleri Azure portal **işlevler** altında görüntülenebilir. .NET Standard işlevlerinin yazılması Azure portal desteklenmez.

* Azure portal sorgu Düzenleyicisi portalda .NET Standard UDF kullanırken bir hata gösterir. 

* Özel kod Azure Stream Analytics altyapısı ile bağlam paylaştığından, özel kod Azure Stream Analytics kodla çakışan bir ad alanı/DLL_name olan herhangi bir şeye başvuramaz. Örneğin, *Newtonsoft JSON*öğesine başvurulamıyor.

## <a name="next-steps"></a>Sonraki adımlar

* [Öğretici: Azure Stream Analytics işi C# için Kullanıcı tanımlı bir işlev yazma (Önizleme)](stream-analytics-edge-csharp-udf.md)
* [Öğretici: JavaScript Kullanıcı tanımlı işlevleri Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics işleri görüntülemek için Visual Studio 'Yu kullanma](stream-analytics-vs-tools.md)
