---
title: Uygulama ayarları-LUSıS
titleSuffix: Azure Cognitive Services
description: Azure bilişsel hizmetler dil dilleri uygulamaları için uygulama ayarları, uygulama ve portalda depolanır.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: bae4f09b539e26ca8c0d4ce97999776dc0911601
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961784"
---
# <a name="application-settings"></a>Uygulama ayarları

Bu uygulama ayarları, [dışarıya](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) kaydedilen uygulamada DEPOLANıR ve REST API 'leri ile [güncelleştirilir](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) . Uygulama sürümü ayarlarınızı değiştirmek, uygulama eğitim durumunuzu eğitilen olarak sıfırlar.

|Ayar|Varsayılan değer|Notlar|
|--|--|--|
|Normalizenoktalama|True|Noktalama işaretlerini kaldırır.|
|NormalizeDiacritics|True|Aksanları kaldırır.|

## <a name="diacritics-normalization"></a>Aksanların normalleştirilmesi 

`settings` parametresindeki lusıs JSON uygulama dosyanıza Aksanlar için söylenişi normalleştirmesini açın.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Aşağıdaki söyleyenlerdeki aksanların normalleştirmeyi nasıl etkilediği gösterilmektedir:

|Aksanların yanlış olarak ayarlandığı|Aksanların değeri true olarak ayarlandı|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Aksanlar için dil desteği

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Portekizce (Brezilya) `pt-br` vurguları

|Aksanların değeri false olarak ayarlandı|Aksanların değeri true olarak ayarlandı|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`| 
|||

#### <a name="dutch-nl-nl-diacritics"></a>Felemenkçe `nl-nl` aksanlar

|Aksanların değeri false olarak ayarlandı|Aksanların değeri true olarak ayarlandı|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`| 
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Fransızca `fr-` aksanlar

Bu hem Fransızca hem de Kanada alt kültürlerini içerir.

|Aksanların değeri false olarak ayarlandı|Aksanların değeri true olarak ayarlandı|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`| 
|`ê`|`e`| 
|`î`|`i`| 
|`ô`|`o`| 
|`û`|`u`| 
|`ç`|`c`| 
|`ë`|`e`| 
|`ï`|`i`| 
|`ü`|`u`| 
|`ÿ`|`y`| 

#### <a name="german-de-de-diacritics"></a>Almanya `de-de` aksanlar

|Aksanların değeri false olarak ayarlandı|Aksanların değeri true olarak ayarlandı|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>İtalyanca `it-it` aksanlar

|Aksanların değeri false olarak ayarlandı|Aksanların değeri true olarak ayarlandı|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`| 
|`í`|`i`| 
|`î`|`i`| 
|`ò`|`o`| 
|`ó`|`o`| 
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>İspanyolca `es-` aksanlar

Buna hem İspanyolca hem de Kanada Meksika dahildir.

|Aksanların değeri false olarak ayarlandı|Aksanların değeri true olarak ayarlandı|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Noktalama normalleştirmesi

Noktalama işaretleri için `settings` parametresindeki LUSıS JSON uygulama dosyanıza noktalama işareti normalleştirmesini açın.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

Aşağıdaki noktalamalar, noktalama işaretlerinin nasıl etkilediğini göstermektedir:

|Noktalama işareti yanlış olarak ayarlandı|Noktalama işareti doğru olarak ayarlandı|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Noktalama kaldırıldı

Aşağıdaki noktalama işaretleri, `NormalizePunctuation` true olarak ayarlandığında kaldırılır.

|Noktalama işaretleri|
|--|
|`-`| 
|`.`| 
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|
