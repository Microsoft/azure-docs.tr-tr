---
title: Konuşmacı Tanıma API’si nedir?
titleSuffix: Azure Cognitive Services
description: Bilişsel hizmetler 'de konuşmacı tanıma API 'SI ile konuşmacı doğrulama ve konuşmacı tanımlama.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464907"
---
# <a name="speaker-recognition-api---preview"></a>Konuşmacı Tanıma API 'SI-Önizleme

Konuşmacı Tanıma API 'Leri, konuşmacı doğrulama ve konuşmacı tanıma için gelişmiş AI algoritmaları sağlayan bulut tabanlı API 'lerdir. Konuşmacı Tanıma iki kategoriye ayrılmıştır: konuşmacı doğrulama ve konuşmacı tanımlama.

## <a name="speaker-verification"></a>Konuşmacı Doğrulama

Seste, bir biresiyle ilişkilendirilebilen benzersiz özellikler vardır.  Uygulamalar, çağrı merkezleri ve Web Hizmetleri gibi senaryolarda, doğrulama için ek bir faktör olarak sesli kullanım sağlayabilir.

Konuşmacı Doğrulama API 'Leri, kullanıcıların hem ses hem de konuşma parolalarını kullanmasını doğrulamaya yardımcı olmak üzere akıllı bir araç olarak görev yapar.

### <a name="enrollment"></a>Kayıt

Konuşmacı doğrulama kaydı metne bağımlıdır, bu da hoparlörlerde hem kayıt hem de doğrulama aşamaları sırasında kullanılacak belirli bir parolayı seçmesi gerektiği anlamına gelir.

Konuşmacı kayıt aşamasında, konuşmacı sesi belirli bir tümceciği belirten şekilde kaydedilir. Seçili tümcecik tanındığında, sesli Özellikler benzersiz bir ses imzası oluşturmak için ayıklanır. Bu konuşmacı kaydı verileri birlikte konuşmacı doğrulamak için kullanılır. Konuşmacı kayıt verileri, güvenli bir sistemde depolanır. Müşteri ne kadar süreyle tutulacağını denetler. Müşteriler, API çağrıları aracılığıyla bireysel Konuşmacılar için kayıt verilerini oluşturabilir, güncelleştirebilir ve kaldırabilir.  Abonelik silindiğinde, abonelikle ilişkili tüm konuşmacı kaydı verileri de silinir.

Müşteriler, konuşmacı doğrulaması için kullanıcılardan uygun izinleri almış olduklarından emin olmalıdır.

### <a name="verification"></a>Doğrulama

Doğrulama aşamasında, müşteri, doğrulanacak kişi ile ilişkili KIMLIKLE konuşmacı doğrulama API 'sini çağırmalıdır.  Hizmet, ses özelliklerini ve parolayı giriş konuşma kaydından ayıklar. Daha sonra, bu özellikler, müşterinin herhangi bir eşleşmeyi doğrulamak ve doğrulamak üzere aradığı konuşmacı için konuşmacı kayıt verilerinin karşılık gelen öğelerine karşı karşılaştırır.  Yanıt, farklı güven düzeylerine sahip "kabul et" veya "Reddet" döndürür.  Müşteri daha sonra bu kişinin kayıtlı konuşmacı olup olmadığına karar vermenize yardımcı olması için sonuçların nasıl kullanılacağını belirler.

Eşik güvenirlik düzeyi, kullanılan senaryoya ve diğer doğrulama faktörlerine bağlı olarak ayarlanmalıdır. Güven düzeyiyle denemeler yapmanızı ve her bir uygulama için uygun ayarı göz önünde bulundurmalarını öneririz. API 'Ler, sesin canlı bir kişiden mi yoksa bir imitation ya da kayıtlı konuşmacı kaydından mi olduğunu belirlemektir.

Hizmet, doğrulama aşamasında hizmete gönderilen konuşma kaydını veya ayıklanan ses özelliklerini korumaz.

Konuşmacı doğrulama hakkında daha fazla ayrıntı için lütfen [Konuşmacı - Doğrulama](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652) API’sine bakın.

## <a name="speaker-identification"></a>Konuşmacı Belirleme

Uygulamalar, bir kayıtlı konuşmacı grubu verildiğinde "kimin konuşduğunu" belirlemek için ses kullanabilir. Konuşmacı kimliği API 'Leri, verimlilik, kişiselleştirme ve çağrı merkezi dökümü gibi senaryolarda kullanılabilir.

### <a name="enrollment"></a>Kayıt

Konuşmacı belirleme için kayıt metinden bağımsızdır; başka bir deyişle, konuşmacının ses kaydında söyleyecekleri üzerinde bir kısıtlama yoktur. Parola gerekli değildir.

Kayıt aşamasında hoparlörün sesi kaydedilir ve benzersiz bir ses imzası oluşturmak için ses özellikleri ayıklanır. Konuşma sesi ve ayıklanan Özellikler güvenli bir sistemde depolanır. Müşteri ne kadar süreyle tutulacağını denetler. Müşteriler, API çağrıları aracılığıyla bireysel Konuşmacılar için bu konuşmacı kayıt verilerini oluşturabilir, güncelleştirebilir ve kaldırabilir. Abonelik silindiğinde, abonelikle ilişkili tüm konuşmacı kaydı verileri de silinir.

Müşteriler, konuşmacı tanımlaması için kullanıcılardan uygun izinleri almış olduklarından emin olmalıdır.

### <a name="identification"></a>İsi

Tanıtım aşamasında, konuşmacı tanımlama hizmeti giriş konuşma kaydından ses özelliklerini ayıklar. Ardından, özellikleri belirtilen konuşmacı listesinin kayıt verileriyle karşılaştırır. Kayıtlı konuşmacı ile eşleşme bulunduğunda, yanıt, bir güven düzeyi olan konuşmacı KIMLIĞINI döndürür.  Aksi takdirde, hiçbir konuşmacı kayıtlı konuşmacı ile eşleşmezse yanıt "Reddet" i döndürür.

Eşik güven düzeyi, senaryoya bağlı olarak ayarlanmalıdır. Güven düzeyiyle denemeler yapmanızı ve her bir uygulama için uygun ayarı göz önünde bulundurmalarını öneririz. API 'Ler, sesin canlı bir kişiden mi yoksa bir imitation ya da kayıtlı konuşmacı kaydından mi olduğunu belirlemektir.

Hizmet, kimlik aşaması için hizmete gönderilen konuşma kaydını veya ayıklanan ses özelliklerini korumaz.

Konuşmacı tanımlama hakkında daha fazla ayrıntı için lütfen API [Konuşmacı kimliği](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e)' ne bakın.
