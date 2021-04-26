---
title: Hizmet yapılandırması-Soru-Cevap Oluşturma
description: Kaynakların nasıl ve nerede yapılandırılacağını anlayın.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: da46084c8c2616284c31ef155927e8dbcbd19e0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102209368"
---
# <a name="service-configuration"></a>Hizmet yapılandırması

Soru-Cevap Oluşturma her sürümü farklı bir Azure kaynakları kümesi (Hizmetler) kullanır. Bu makalede, bu hizmetler için desteklenen özelleştirmeler açıklanmaktadır. 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

Soru-Cevap Oluşturma, [Generateanswer API](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer)tarafından kullanılan sorgu çalışma zamanını sağlamak için App Service kullanır.

Bu ayarlar, App Service için Azure portal kullanılabilir. **Ayarlar ' ı** ve ardından **yapılandırma**' yı seçerek ayarlar kullanılabilir.

Uygulama Ayarları listesinden ayrı bir ayar ayarlayabilir veya **Gelişmiş Düzenle**' yi seçerek çeşitli ayarları değiştirebilirsiniz.

|Kaynak|Ayar|
|--|--|
|AzureSearchAdminKey|Bilişsel Arama-QnA çifti depolama ve Ranker #1 için kullanılır|
|AzureSearchName|Bilişsel Arama-QnA çifti depolama ve Ranker #1 için kullanılır|
|DefaultAnswer|Eşleşme bulunamadığında yanıt metni|
|Userappınsi, Sappıd|Sohbet günlüğü ve telemetrisi|
|Userappınsightskey|Sohbet günlüğü ve telemetrisi|
|Userappınsightsname|Sohbet günlüğü ve telemetrisi|
|QNAMAKER_EXTENSION_VERSION|Her zaman _en son_ olarak ayarlayın. Bu ayar App Service QnAMaker site uzantısını başlatacak.|

Değişiklikleri yapmayı tamamladıktan sonra, Azure portal **genel bakış** sayfasından hizmeti **yeniden başlatmanız** gerekir.

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

App Service özelleştirmeler, Soru-Cevap Oluşturma yönetilen (Önizleme) için uygulanmaz.

---

## <a name="qna-maker-service"></a>Soru-Cevap Oluşturma Hizmeti

Soru-Cevap Oluşturma Hizmeti, aşağıdaki kullanıcıların tek bir Soru-Cevap Oluşturma hizmetinde ve tüm bilgi bankalarında işbirliği yapması için yapılandırma sağlar.

Hizmetinizdeki [ortak çalışanları nasıl ekleyeceğinizi](./reference-role-based-access-control.md) öğrenin.

## <a name="change-azure-cognitive-search"></a>Azure Bilişsel Arama 'yi değiştirme

Soru-Cevap Oluşturma hizmetinize bağlı [bilişsel arama hizmetini nasıl değiştireceğinizi](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) öğrenin.

## <a name="change-default-answer"></a>Varsayılan yanıtı değiştirme

[Varsayılan yanıtlarınızın metnini değiştirmeyi](How-To/change-default-answer.md)öğrenin. 

## <a name="telemetry"></a>Telemetri

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

Application Insights, Soru-Cevap Oluşturma GA ile Telemetriyi izlemek için kullanılır. Soru-Cevap Oluşturma özgü yapılandırma ayarları yoktur.

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

[Soru-cevap oluşturma yönetilen (Önizleme) hizmetinize telemetri eklemeyi](How-To/get-analytics-knowledge-base.md)öğrenin. 

---

## <a name="app-service-plan"></a>App Service Planı

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA (kararlı sürüm)](#tab/v1)

App Service planı Soru-Cevap Oluşturma özgü yapılandırma ayarlarına sahip değil.

# <a name="qnamaker-managed-preview-release"></a>[QnAMaker yönetilen (Önizleme sürümü)](#tab/v2)

App Service planı, Soru-Cevap Oluşturma Managed (Önizleme) ile kullanılmaz.

---

## <a name="next-steps"></a>Sonraki adımlar

Bilgi Bankası 'nda içeri aktarmak istediğiniz belge ve URL [biçimleri](reference-document-format-guidelines.md) hakkında daha fazla bilgi edinin.
