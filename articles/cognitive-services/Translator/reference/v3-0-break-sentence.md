---
title: Translator Breakcümle yöntemi
titleSuffix: Azure Cognitive Services
description: Translator Breakcümlesini metodu, tümce sınırlarının bir metin parçasına konumlandırılmasını belirler.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 2da614fe829d0aa82bfa57337baf44491993c68f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895552"
---
# <a name="translator-30-breaksentence"></a>Translator 3,0: Breakcümle

Tümce sınırlarının metin parçasına konumlandırılmasını belirler.

## <a name="request-url"></a>İstek URL’si

Şu kişiye bir `POST` istek gönder:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>İstek parametreleri

Sorgu dizesine geçirilen istek parametreleri şunlardır:

| Sorgu parametresi | Description |
| -------| ----------- |
| api-sürümü <img width=200/>   | **Gerekli sorgu parametresi**.<br/>İstemci tarafından istenen API 'nin sürümü. Değer olmalıdır `3.0` . |
| language | **Isteğe bağlı sorgu parametresi**.<br/>Giriş metninin dilini tanımlayan dil etiketi. Bir kod belirtilmemişse, otomatik dil algılama uygulanır. |
| betik    | **Isteğe bağlı sorgu parametresi**.<br/>Giriş metni tarafından kullanılan betiği tanımlayan betik etiketi. Bir komut dosyası belirtilmemişse, dilin varsayılan betiği kabul edilecektir.  | 

İstek üstbilgileri şunları içerir:

| Üst Bilgiler | Description |
| ------- | ----------- |
| Kimlik doğrulama üst bilgisi <img width=200/>  | **Gerekli istek üst bilgisi**.<br/><a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">Kimlik doğrulaması için kullanılabilen seçeneklere</a>bakın. |
| İçerik Türü | **Gerekli istek üst bilgisi**.<br/>Yükün içerik türünü belirtir. Olası değerler şunlardır: `application/json` . |
| İçerik Uzunluğu    | **Gerekli istek üst bilgisi**.<br/>İstek gövdesinin uzunluğu.  | 
| X-Clienttraceıd   | **Isteğe bağlı**.<br/>İsteği benzersiz şekilde tanımlamak için istemci tarafından oluşturulan bir GUID. Adlı sorgu parametresini kullanarak izleme KIMLIĞINI sorgu dizesine eklerseniz bu üstbilgiyi atlayabilirsiniz `ClientTraceId` .  | 

## <a name="request-body"></a>İstek gövdesi

İsteğin gövdesi bir JSON dizisidir. Her dizi öğesi, adlı bir String özelliği olan bir JSON nesnesidir `Text` . Tümce sınırları, özelliğin değeri için hesaplanır `Text` . Bir metin parçasına sahip örnek bir istek gövdesi şöyle görünür:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Aşağıdaki sınırlamalar geçerlidir:

* Dizi en fazla 100 öğe içerebilir.
* Bir dizi öğesinin metin değeri boşluk dahil 50.000 karakteri aşamaz.
* İstekte bulunan metnin tamamı boşluk dahil 50.000 karakteri aşamaz.
* `language`Sorgu parametresi belirtilmişse, tüm dizi öğeleri aynı dilde olmalıdır. Aksi halde, dil otomatik algılama her bir dizi öğesine bağımsız olarak uygulanır.

## <a name="response-body"></a>Yanıt gövdesi

Başarılı bir yanıt, Giriş dizisindeki her bir dize için bir sonuç içeren bir JSON dizisidir. Bir sonuç nesnesi aşağıdaki özellikleri içerir:

  * `sentLen`: Metin öğesindeki Tümcelerin uzunluklarını temsil eden bir tamsayılar dizisi. Dizinin uzunluğu, Tümcelerin sayısıdır ve değerler her tümcenin uzunluktadır. 

  * `detectedLanguage`: Aşağıdaki özellikler aracılığıyla algılanan dili açıklayan bir nesne:

     * `language`: Algılanan dilin kodu.

     * `score`: Sonucun güvenilirliği belirten bir float değeri. Puan sıfır ile bir ve düşük puan arasında düşük bir güvenilirlik olduğunu gösterir.
     
    `detectedLanguage`Özelliğin yalnızca dil otomatik algılaması istendiğinde sonuç nesnesinde bulunduğunu unutmayın.

Örnek bir JSON yanıtı:

```json
[
  {
    "sentLen": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
]
```

## <a name="response-headers"></a>Yanıt üst bilgileri

<table width="100%">
  <th width="20%">Üst Bilgiler</th>
  <th>Description</th>
  <tr>
    <td>X-RequestId</td>
    <td>İsteği tanımlamak için hizmet tarafından oluşturulan değer. Sorun giderme amacıyla kullanılır.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Yanıt durum kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir. 

<table width="100%">
  <th width="20%">Durum Kodu</th>
  <th>Description</th>
  <tr>
    <td>200</td>
    <td>Başarılı.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Sorgu parametrelerinden biri eksik veya geçersiz. Yeniden denemeden önce istek parametrelerini düzeltin.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>İsteğin kimliği doğrulanamadı. Kimlik bilgilerinin belirtildiğinden ve geçerli olduğundan emin olun.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>İstek yetkili değil. Ayrıntılar hata iletisini denetleyin. Bu genellikle bir deneme aboneliği ile birlikte sunulan tüm ücretsiz çevirilerin kullanıldığını gösterir.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>İstemci istek sınırlarını aştığından, sunucu isteği reddetti.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Beklenmeyen bir hata oluştu. Hata devam ederse, bununla raporla: hatanın tarih ve saati, yanıt başlığından istek tanımlayıcısı `X-RequestId` ve istek üst bilgisinden istemci tanımlayıcısı `X-ClientTraceId` .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Sunucu geçici olarak kullanılamıyor. İsteği yeniden deneyin. Hata devam ederse, bununla raporla: hatanın tarih ve saati, yanıt başlığından istek tanımlayıcısı `X-RequestId` ve istek üst bilgisinden istemci tanımlayıcısı `X-ClientTraceId` .</td>
  </tr>
</table> 

Bir hata oluşursa, istek bir JSON hata yanıtı da döndürür. Hata kodu 3 basamaklı HTTP durum kodunu birleştiren 6 basamaklı bir sayıdır ve ardından hatayı daha fazla kategorilere ayırarak 3 basamaklı bir sayıdır. Ortak hata kodları, [v3 Translator başvurusu sayfasında](./v3-0-reference.md#errors)bulunabilir. 

## <a name="examples"></a>Örnekler

Aşağıdaki örnek, tek bir cümle için tümce sınırlarının nasıl alınacağını gösterir. Tümce dili, hizmet tarafından otomatik olarak algılanır.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```