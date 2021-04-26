---
title: Özel bir komut uygulaması çalıştırırken hata ayıklama hataları
titleSuffix: Azure Cognitive Services
description: Bu makalede, özel komutlar uygulamasındaki çalışma zamanı hatalarının nasıl ayıklanalınacağını öğreneceksiniz.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: b41bcda7381e5811ef1e1f91c102d8c85f50b3b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103196923"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>Özel bir komut uygulaması çalıştırırken hata ayıklama hataları

Bu makalede, özel komutlar uygulaması çalıştırılırken hata ayıkladığınızda hata ayıklama nasıl yapılacağı açıklanır. 

## <a name="connection-failed"></a>Bağlantı başarısız oldu

[İstemci uygulamasından (konuşma SDK 'sı ile)](./how-to-custom-commands-setup-speech-sdk.md) veya [Windows Voice Yardımcısı istemcisiyle](./how-to-custom-commands-developer-flow-test.md)özel komut uygulaması çalıştırırsanız, bağlantı hatalarıyla aşağıda listelenen şekilde karşılaşabilirsiniz:

| Hata kodu | Ayrıntılar |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: WebSocket yükseltmesi bir kimlik doğrulama hatasıyla başarısız oldu |
| [1002](#error-1002) | Sunucu, ' 101 ' durum kodu beklenirken ' 404 ' durum kodunu döndürdü. |

### <a name="error-401"></a>Hata 401
- İstemci uygulamasında belirtilen bölge, özel komut uygulamasının bölgesiyle eşleşmiyor

- Konuşma kaynak anahtarı geçersiz
    
    Konuşma kaynak anahtarınızın doğru olduğundan emin olun.

### <a name="error-1002"></a>Hata 1002 
- Özel komut uygulamanız yayımlanmadı
    
    Uygulamanızı portalda yayımlayın.

- Özel komut ApplicationId 'niz geçerli değil

    Özel komut uygulama KIMLIĞINIZIN doğru olduğundan emin olun.
 konuşma kaynağınızın dışında özel komut uygulaması

    Özel komut uygulamasının konuşma kaynağınızın altında oluşturulduğundan emin olun.

Bağlantı sorunlarını giderme hakkında daha fazla bilgi için, referans [Windows Voice Yardımcısı Istemci sorunlarını giderme](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting)


## <a name="dialog-is-canceled"></a>İletişim kutusu iptal edildi

Özel komutlar uygulamanızı çalıştırırken, bazı hatalar meydana geldiğinde iletişim kutusu iptal edilir.

- Uygulamayı portalda test ediyorsanız, iptal açıklaması doğrudan görüntülenir ve bir hata earcon oynar. 

- Uygulamayı [Windows Voice Assistant istemcisiyle](./how-to-custom-commands-developer-flow-test.md)çalıştırıyorsanız bir hata earcon oynar. **Etkinlik günlükleri** altında **olay: cancelleddialog** ' a ulaşabilirsiniz.

- İstemci uygulaması örnek [istemci uygulamamızı (konuşma SDK 'sı ile)](./how-to-custom-commands-setup-speech-sdk.md)takip ediyorsanız, bir hata earcon oynatabilir. **Durum** altında **olay: cancelleddialog** ' a ulaşabilirsiniz.

- Kendi istemci uygulamanızı oluşturuyorsanız, istenen günlüklerinizi her zaman, CancelledDialog olaylarını işleyecek şekilde tasarlayabilirsiniz.

CancelledDialog olayı, aşağıda listelendiği gibi iptal kodu ve açıklamalardan oluşur:

| İptal kodu | İptal açıklaması |
| ------- | --------------- | ----------- |
| [Maxturnthresholdulaşıldı](#no-progress-was-made-after-the-max-number-of-turns-allowed) | İzin verilen en fazla sayıdaki dönüşden sonra ilerleme yapılmadı |
| [Recognizerquotageçildi](#recognizer-usage-quota-exceeded) | Tanıyıcı kullanım kotası aşıldı |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | Tanıyıcıya bağlantı başarısız oldu |
| [Recognizeryetkilendirilmemiş](#this-application-cannot-be-accessed-with-the-current-subscription) | Bu uygulamaya geçerli abonelikle erişilemiyor |
| [Recognizerınputexceededallodilimlerin uzunluğu](#input-exceeds-the-maximum-supported-length) | Giriş, tanıyıcı için desteklenen uzunluk üst sınırını aşıyor |
| [RecognizerNotFound](#recognizer-not-found) | Tanıyıcı bulunamadı |
| [Recognizerınvalidquery](#invalid-query-for-the-recognizer) | Tanıyıcı için geçersiz sorgu |
| [Recognizerhatası](#recognizer-return-an-error) | Tanıyıcı bir hata döndürüyor |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>İzin verilen en fazla sayıdaki dönüşden sonra ilerleme yapılmadı
Gerekli bir yuva, belirli sayıda dönüşden sonra başarıyla güncellenmediğinde iletişim kutusu iptal edilir. Derleme en fazla sayı 3 ' dir.

### <a name="recognizer-usage-quota-exceeded"></a>Tanıyıcı kullanım kotası aşıldı
Language Understanding (LUA), kaynak kullanımı için sınırlara sahiptir. Genellikle "tanıyıcı kullanım kotası aşıldı hatası" nedeniyle şunlar olabilir: 
- LUSıS yazma sınırı aşıyor

    Özel komutlar uygulamanıza bir tahmin kaynağı ekleyin: 
    1. **Ayarlar**, lusıs kaynağı 'na gidin
    1. **Tahmin** kaynağından bir tahmin kaynağı seçin veya **Yeni kaynak oluştur** ' a tıklayın. 

- LUSıS tahmin kaynağınız sınırı aşıyor

    F0 tahmini kaynağınız varsa, 10 bin/ay, 5 sorgu/saniye sınırına sahip olur.

LUSıS kaynak limitleri hakkında daha fazla bilgi için, [Language Understanding kaynak kullanımı ve limiti](../luis/luis-limits.md#resource-usage-and-limits) ' ne bakın

### <a name="connection-to-the-recognizer-failed"></a>Tanıyıcıya bağlantı başarısız oldu
Genellikle Language Understanding (LUSıS) tanıyıcı için geçici bağlantı hatası anlamına gelir. Yeniden deneyin ve sorun çözümlenmelidir.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>Bu uygulamaya geçerli abonelikle erişilemiyor
Aboneliğinizin LUSıS uygulamasına erişim yetkisi yok. 

### <a name="input-exceeds-the-maximum-supported-length"></a>Giriş, desteklenen uzunluk üst sınırını aşıyor
Giriş, 500 karakteri aştı. Giriş için en fazla 500 karakterden yalnızca izin veririz.

### <a name="invalid-query-for-the-recognizer"></a>Tanıyıcı için geçersiz sorgu
Giriş, 500 karakteri aştı. Giriş için en fazla 500 karakterden yalnızca izin veririz.

### <a name="recognizer-return-an-error"></a>Tanıyıcı bir hata döndürdü
LUSıS tanıyıcı, girişinizi tanımaya çalışırken bir hata döndürdü.

### <a name="recognizer-not-found"></a>Tanıyıcı bulunamadı
Özel komutlar iletişim kutusu modelinizde belirtilen tanıyıcı türü bulunamıyor. Şu anda yalnızca [Language Understanding (lusıs) tanıyıcıyı](https://www.luis.ai/)destekliyoruz.

## <a name="other-common-errors"></a>Diğer yaygın hatalar
### <a name="unexpected-response"></a>Beklenmeyen yanıt
Beklenmeyen yanıtlar birden çok şeyin oluşmasına neden olabilir. Başlamak için birkaç denetim:
- Örnek cümlelerde Evet/amaç yok

    Şimdilik onaylama özelliği ile kullanılması dışında Evet/Hayır amacını desteklemiyoruz. Örnek tümcelerde tanımlı tüm Evet/Hayır amaçları saptanmaz.

- Komutlar arasında benzer amaçlar ve örnek cümleler

    İki komut benzer hedefleri ve örnek cümleleri paylaşıyorsa, LUSıS tanıma doğruluğu etkilenebilir. Komut işlevlerini ve örnek cümleleri mümkün olduğunca farklı hale getirmek için deneyebilirsiniz.

    Tanıma doğruluğunu iyileştirmeye yönelik en iyi yöntem için, [lusıs en iyi uygulaması](../luis/luis-concept-best-practices.md)' na bakın.

- İletişim kutusu iptal edildi
    
    Yukarıdaki bölümde iptal etme nedenlerini denetleyin.

### <a name="error-while-rendering-the-template"></a>Şablon işlenirken hata oluştu
Konuşma yanıtında tanımsız bir parametre kullanılır. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>Nesne başvurusu bir nesnenin örneğine ayarlanmadı
**Etkinliği Istemciye gönder** EYLEMINDE tanımlanan JSON yükünde boş bir parametreye sahipsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [GitHub 'da örneklere bakın](https://aka.ms/speech/cc-samples)
