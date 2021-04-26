---
title: Görüntü İşleme API'si v 3.0 okuma sürümüne yükseltin
titleSuffix: Azure Cognitive Services
description: V 2.0/v 2.1 'deki Görüntü İşleme v 3.0 okuma API 'sine nasıl yükselteceğinizi öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 7a05b04872b4f957e879d93972edc45e2932d059
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364099"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>V2. x okuma sürümünden v3. x ' e yükseltme

Bu kılavuzda, var olan kapsayıcınızı veya Cloud API kodunuzu Read v2. x adresinden v3. x ' e nasıl yükselteceğiniz gösterilmektedir.

## <a name="determine-your-api-path"></a>API yolunuzu belirleme
Geçiş yaptığınız 3. x sürümünü temel alan API yolundaki **Sürüm dizesini** anlamak için aşağıdaki tabloyu kullanın.

|Ürün türü| Sürüm | 3. x API yolundaki sürüm dizesi |
|:-----|:----|:----|
|Hizmet | 3,0 veya 3,1 ' i okuyun | **v 3.0** veya **v 3.1** sırasıyla |
|Hizmet | 3,2 önizlemesini okuyun | **v 3.2-Önizleme. 1** |
|Kapsayıcı | 3,0 önizlemesini okuyun veya 3,1 Önizleme makalesini okuyun | **v 3.0** veya **v 3.1-Önizleme. 2** sırasıyla |


Ardından aşağıdaki bölümleri kullanarak işlemlerinizi daraltın ve API yolunuzda **Sürüm dizesini** tablodaki değerle değiştirin. Örneğin, **okuma v 3.2 Önizleme** bulutu ve kapsayıcı sürümleri IÇIN, API yolunu **https://{Endpoint}/Vision/v 3.2-Preview. 1/okuma/çözümleme [? Language]** olarak güncelleştirin.

## <a name="servicecontainer"></a>Hizmet/kapsayıcı

### `Batch Read File`

|2. x 'i oku |3. x oku  |
|----------|-----------|
|https://{Endpoint}/vizyon/**v 2.0/okuma/çekirdek/asyncBatchAnalyze**     |https://{Endpoint}/Vision/<**sürüm dizesi**>/Read/Analyze [? Language]|
    
Yeni bir isteğe bağlı _dil_ parametresi var. Belgenizin dilini bilmiyorsanız veya çok dilli olabilir, dahil değildir. 

### `Get Read Results`

|2. x 'i oku |3. x oku  |
|----------|-----------|
|https://{Endpoint}/vizyon/**v 2.0/okuma/işlemler**/{operationId}     |https://{Endpoint}/vizyon/<**sürüm dizesi**>/Read/analiz Zeresults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result` durum bayrağı

Çağrısı `Get Read Operation Result` başarılı olduğunda, JSON gövdesinde bir durum dizesi alanı döndürür.
 
|2. x 'i oku |3. x oku  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>API yanıtı (JSON) 

JSON üzerinde aşağıdaki değişiklikleri aklınızda yapın:
* V2. x içinde `Get Read Operation Result` durum olduğunda OCR tanıma JSON döndürür `Succeeded"` . V 3.0 'da Bu alan `succeeded` .
* Sayfa dizisinin kökünü almak için, JSON hiyerarşisini ' den ' e değiştirin `recognitionResults` `analyzeResult` / `readResults` . Sayfa başına çizgi ve sözcükler JSON hiyerarşisi değişmeden kalır, bu nedenle kod değişikliği gerekli değildir.
* Sayfa açısı `clockwiseOrientation` olarak yeniden adlandırıldı `angle` ve Aralık 0-360 derece-180 180 arasında olacak şekilde değiştirilmiştir. Kodunuza bağlı olarak, çoğu matematik işlevi her iki aralığı de işleyemediğinden değişiklik yapmanız gerekebilir.

V 3.0 API 'SI, isteğe bağlı olarak kullanabileceğiniz aşağıdaki geliştirmeleri de sunar:
* `createdDateTime` ve `lastUpdatedDateTime` , işleme süresini izleyebilmek için eklenir. Diğer ayrıntılar için belgelere bakın. 
* `version` sonuçları oluşturmak için kullanılan API 'nin sürümünü söyler
* Sözcük başına `confidence` eklendi. Bu değer, 0,95 değeri, tanımanın doğru olması için %95 olasılığı olacağı için ayarlanır. Güvenirlik puanı, insan gözden geçirmesi için hangi metnin gönderileceğini seçmek üzere kullanılabilir. 
    
    
2. X içinde, çıkış biçimi aşağıdaki gibidir: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
V 3.0 'da, ayarlandı:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>Yalnızca hizmet

### `Recognize Text`
`Recognize Text`*tüm görüntü işleme API'si sürümlerinde kullanımdan* kaldırılmakta olan bir *Önizleme* işlemidir. `Recognize Text` `Read` ' Den (v 3.0) veya `Batch Read File` (v 2.0, v 2.1) sürümüne geçiş yapmanız gerekir. v 3.0 `Read` , metin tanıma ve ek özellikler için daha yeni, daha iyi modeller içerir, bu nedenle önerilir. Sürümünden sürümüne yükseltmek `Recognize Text` için `Read` :

|Metin Tanıma 2. x |3. x oku  |
|----------|-----------|
|https://{Endpoint}/vizyon/**v 2.0/recognizeText [? modu]**|https://{Endpoint}/Vision/<**sürüm dizesi**>/Read/Analyze [? Language]|
    
_Mode_ parametresi ' de desteklenmez `Read` . Hem el ile hem de yazılı metin otomatik olarak desteklenecektir.
    
V 3.0 'da yeni bir isteğe bağlı _dil_ parametresi mevcuttur. Belgenizin dilini bilmiyorsanız veya çok dilli olabilir, dahil değildir. 

### `Get Recognize Text Operation Result`

|Metin Tanıma 2. x |3. x oku  |
|----------|-----------|
|https://{Endpoint}/Vision/**v 2.0/textOperations/**{operationId}|https://{Endpoint}/vizyon/<**sürüm dizesi**>/Read/analiz Zeresults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result` durum bayrakları
Çağrısı `Get Recognize Text Operation Result` başarılı olduğunda, JSON gövdesinde bir durum dizesi alanı döndürür. 
 
|Metin Tanıma 2. x |3. x oku  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>API yanıtı (JSON)

JSON üzerinde aşağıdaki değişiklikleri aklınızda yapın:    
* V2. x içinde `Get Read Operation Result` durum olduğunda OCR tanıma JSON döndürür `Succeeded` . V3. x içinde bu alan `succeeded` .
* Sayfa dizisinin kökünü almak için, JSON hiyerarşisini ' den ' e değiştirin `recognitionResult` `analyzeResult` / `readResults` . Sayfa başına çizgi ve sözcükler JSON hiyerarşisi değişmeden kalır, bu nedenle kod değişikliği gerekli değildir.

V 3.0 API 'SI, isteğe bağlı olarak kullanabileceğiniz aşağıdaki geliştirmeleri de sağlar. Daha fazla ayrıntı için API başvurusuna bakın:
* `createdDateTime` ve `lastUpdatedDateTime` , işleme süresini izleyebilmek için eklenir. Diğer ayrıntılar için belgelere bakın. 
* `version` sonuçları oluşturmak için kullanılan API 'nin sürümünü söyler
* Sözcük başına `confidence` eklendi. Bu değer, 0,95 değeri, tanımanın doğru olması için %95 olasılığı olacağı için ayarlanır. Güvenirlik puanı, insan gözden geçirmesi için hangi metnin gönderileceğini seçmek üzere kullanılabilir. 
* `angle` metnin, saat yönünde (-180, 180]) ölçülen genel yönü.
* `width` ve `"height"` size belgenizin boyutlarını verir ve `"unit"` belge türüne bağlı olarak bu boyutların birimini (piksel veya inç) sağlar.
* `page` çok sayfalı belgeler destekleniyor
* `language` belgenin giriş dili (isteğe bağlı _dil_ parametresinden).


2. X içinde, çıkış biçimi aşağıdaki gibidir: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
V3. x içinde ayarlandı:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>Yalnızca kapsayıcı

### `Synchronous Read`

|2,0 okuyun |3. x oku  |
|----------|-----------|
|https://{Endpoint}/vizyon/**v 2.0/okuma/çekirdek/analiz**     |https://{Endpoint}/Vision/<**sürüm dizesi**>/Read/syncAnalyze [? Language]|
