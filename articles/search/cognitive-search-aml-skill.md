---
title: Becerileri 'de özel AML becerisi
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama becerileri 'in yeteneklerini Azure Machine Learning modelleriyle genişletin.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 6cefe543ea8ba992b028448070bf041a77bfec64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630284"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Azure Bilişsel Arama enzenginleştirme ardışık düzeninde AML yeteneği

> [!IMPORTANT] 
> Bu yetenek Şu anda genel önizlemededir. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Şu anda .NET SDK desteği yok.

**AML** BECERISI, AI zenginleştirme 'yi özel bir [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) (AML) modeliyle genişletmenizi sağlar. AML modelinin [eğitilmesi ve dağıtılması](../machine-learning/concept-azure-machine-learning-architecture.md#workspace)bir **AML** becerisi onu AI zenginleştirme ile tümleştirir.

Yerleşik yetenekler gibi, **AML** becerilerinin giriş ve çıkışları vardır. Girişler, bir JSON yükünün başarılı durum koduyla birlikte yanıt olarak çıkışını veren bir JSON nesnesi olarak dağıtılan AML hizmetinize gönderilir. Yanıtın **AML** becerinizde belirtilen çıkışların olması beklenir. Diğer herhangi bir yanıt bir hata olarak değerlendirilir ve hiçbir zenginleştirilmez.

> [!NOTE]
> Dizin Oluşturucu, AML hizmetinden döndürülen belirli standart HTTP durum kodları için iki kez yeniden dener. Bu HTTP durum kodları şunlardır:
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>Önkoşullar

* [AML çalışma alanı](../machine-learning/concept-workspace.md)
* Bu çalışma alanında [dağıtılan bir modelle](../machine-learning/how-to-deploy-azure-kubernetes-service.md) bir [Azure Kubernetes hizmeti AML işlem hedefi](../machine-learning/concept-compute-target.md)
  * [İşlem HEDEFINDE SSL etkin olmalıdır](../machine-learning/how-to-secure-web-service.md#deploy-on-azure-kubernetes-service). Azure Bilişsel Arama yalnızca **https** uç noktalarına erişime izin veriyor
  * Otomatik olarak imzalanan sertifikalar kullanılamayabilir.

## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. Custom. Amlbeceri

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır. Kullanmak istediğiniz parametreler [, AML hizmetinizin gerek duyduğu kimlik doğrulamasına](#WhatSkillParametersToUse) bağlıdır.

| Parametre adı | Açıklama |
|--------------------|-------------|
| `uri` | (Kimlik doğrulaması [yok veya anahtar kimlik doğrulaması](#WhatSkillParametersToUse)için gereklidir) _JSON_ yükünün gönderileceği [AML HIZMETININ Puanlama URI 'si](../machine-learning/how-to-consume-web-service.md) . Yalnızca **https** URI şemasına izin veriliyor. |
| `key` | ( [Anahtar kimlik doğrulaması](#WhatSkillParametersToUse)için gereklidir) [AML hizmeti için anahtar](../machine-learning/how-to-consume-web-service.md#authentication-with-keys). |
| `resourceId` | ( [Belirteç kimlik doğrulaması](#WhatSkillParametersToUse)için gereklidir). AML hizmetinin Azure Resource Manager kaynak KIMLIĞI. Abonelik/{Guid}/resourceGroups/{Resource-Group-name}/Microsoft. MachineLearningServices/Workspaces/{Workspace-Name}/Services/{service_name} biçiminde olmalıdır. |
| `region` | ( [Belirteç kimlik doğrulaması](#WhatSkillParametersToUse)için isteğe bağlı). AML hizmetinin dağıtıldığı [bölge](https://azure.microsoft.com/global-infrastructure/regions/) . |
| `timeout` | Seçim Belirtildiğinde, API çağrısını yapan http istemcisinin zaman aşımını gösterir. XSD "dayTimeDuration" değeri ( [ıso 8601 Duration](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) değerinin kısıtlı bir alt kümesi) olarak biçimlendirilmelidir. Örneğin, `PT60S` 60 saniye için. Ayarlanmamışsa, varsayılan değer olan 30 saniye seçilir. Zaman aşımı en fazla 230 saniyeye ayarlanabilir ve en az 1 saniye olabilir. |
| `degreeOfParallelism` | Seçim Belirtildiğinde, dizin oluşturucunun verdiğiniz bitiş noktasına paralel olarak kullanacağı çağrı sayısını gösterir. Uç noktanız bir istek yükünün çok yüksek altındaysa bu değeri azaltabilir veya uç noktanız daha fazla istek kabul edebilse ve dizin oluşturucunun performansına bir artış istiyorsanız bu değeri azaltabilirsiniz.  Ayarlanmamışsa, varsayılan 5 değeri kullanılır. Degreeofparalellik, en fazla 10 ve en az 1 olarak ayarlanabilir.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>Kullanılacak yetenek parametreleri

Hangi AML yetenek parametrelerinin gerekli olduğu, AML hizmetinizin kullandığı kimlik doğrulamasına bağlıdır. AML hizmetleri üç kimlik doğrulama seçeneği sunar:

* [Anahtar tabanlı kimlik doğrulaması](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication). AML becerilerinden Puanlama isteklerinin kimliğini doğrulamak için bir statik anahtar sağlanır
  * _URI_ ve _anahtar_ parametrelerini kullanma
* [Belirteç tabanlı kimlik doğrulama](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). AML hizmeti, [belirteç tabanlı kimlik doğrulaması kullanılarak dağıtılır](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). Azure Bilişsel Arama hizmetinin [yönetilen kimliğine](../active-directory/managed-identities-azure-resources/overview.md) , AML hizmetinin çalışma alanında [okuyucu rolü](../machine-learning/how-to-assign-roles.md) verilir. AML yeteneği daha sonra, bir statik anahtar gerekmeden AML hizmetinde kimlik doğrulaması yapmak için Azure Bilişsel Arama hizmetinin yönetilen kimliğini kullanır.
  * _RESOURCEID_ parametresini kullanın.
  * Azure Bilişsel Arama hizmeti AML çalışma alanından farklı bir bölgedeyse, AML hizmetinin dağıtıldığı bölgeyi ayarlamak için _Region_ parametresini kullanın
* Kimlik doğrulaması yok. AML hizmetini kullanmak için kimlik doğrulaması gerekmez
  * _URI_ parametresini kullan

## <a name="skill-inputs"></a>Beceri girişleri

Bu beceri için "önceden tanımlanmış" giriş yok. Bu yeteneğin yürütülmesi giriş sırasında zaten kullanılabilir olacak bir veya daha fazla alan seçebilirsiniz ve AML hizmetine gönderilen _JSON_ yükünün farklı alanları olacaktır.

## <a name="skill-outputs"></a>Yetenek çıkışları

Bu beceri için "önceden tanımlanmış" çıkış yok. AML hizmetinizin döndürdüğü yanıta bağlı olarak, _JSON_ yanıtından alınabilmeleri için çıktı alanları ekleyin.

## <a name="sample-definition"></a>Örnek tanım

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>Örnek giriş JSON yapısı

Bu _JSON_ yapısı AML hizmetinize gönderilecek yükü temsil eder. Yapının en üst düzey alanları, yetenek tanımının bölümünde belirtilen "adlara" karşılık gelir `inputs` . Bu alanların değeri `source` Bu alanlardan (belgedeki bir alandan ya da başka bir beceriye ait olabilir) olacaktır

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>Örnek çıkış JSON yapısı

Çıktı, AML hizmetinden döndürülen yanıta karşılık gelir. AML hizmeti yalnızca bir _JSON_ yükü döndürmelidir ( `Content-Type` Yanıt üstbilgisine bakılarak doğrulanır) ve alanları, ' deki `output` ve değeri enzenginleştirme olarak kabul edilen "adlarla" eşleşen bir nesne olmalıdır.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>Satır içi şekillendirme örnek tanımı

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>Satır içi şekillendirme girişi JSON yapısı

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>Satır içi şekillendirme örnek çıkış JSON yapısı

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>Hata durumları
AML 'nizin kullanılamaz duruma veya başarılı olmayan durum kodları göndermeye ek olarak, aşağıdakiler hatalı durumlar olarak kabul edilir:

* AML hizmeti başarı durum kodu döndürürse ancak yanıt olmadığını gösteriyorsa `application/json` , yanıt geçersiz olarak kabul edilir ve hiçbir zenginleştirilmez.
* AML hizmeti geçersiz JSON döndürürse

AML hizmetinin kullanılamadığı veya bir HTTP hatası döndürdüğü durumlarda, Dizin Oluşturucu yürütme geçmişine HTTP hatası ile ilgili tüm ayrıntıları içeren bir kolay hata eklenir.

## <a name="see-also"></a>Ayrıca bkz.

+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [AML hizmeti sorun giderme](../machine-learning/how-to-troubleshoot-deployment.md)