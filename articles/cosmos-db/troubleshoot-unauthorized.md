---
title: Azure Cosmos DB yetkisiz özel durumların sorunlarını giderme
description: Yetkisiz özel durumları tanılamayı ve gidermeyi öğrenin.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: aa9bf1fd706ccf6064893f1141be5e5b2f185ff3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94411176"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>Yetkisiz özel durumları Azure Cosmos DB tanılama ve sorun giderme
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 401: HTTP isteğinde bulunan MAC imzası, hesaplanan imzayla aynı değil.
401 hata iletisini aldıysanız, "HTTP isteğinde bulunan MAC imzası, hesaplanan imzayla aynı değildir", bu, aşağıdaki senaryolardan kaynaklanıyor olabilir.

Eski SDK 'lar için özel durum, doğru 401 Yetkisiz özel durumu yerine geçersiz bir JSON özel durumu olarak görünebilir. Daha yeni SDK 'lar bu senaryoyu doğru bir şekilde işler ve geçerli bir hata iletisi verir.

## <a name="troubleshooting-steps"></a>Sorun giderme adımları
Aşağıdaki liste, yetkisiz özel durumlar için bilinen nedenleri ve çözümleri içerir.

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>Anahtar doğru şekilde döndürülmedi, en yaygın senaryo
401 MAC imzası, anahtar rotasından kısa bir süre sonra görülür ve sonuç olarak herhangi bir değişiklik yapılmadan duraklar. 

#### <a name="solution"></a>Çözüm:
Anahtar döndürüldü ve [en iyi yöntemleri](secure-access-to-data.md#key-rotation)izmedi. Azure Cosmos DB hesap anahtarı döndürme, Azure Cosmos DB hesap boyutuna bağlı olarak birkaç saniye ile muhtemelen gün arasında bir yere sürebilir.

### <a name="the-key-is-misconfigured"></a>Anahtar yanlış yapılandırılmış 
401 MAC imzası sorunu tutarlı olacak ve bu anahtarı kullanan tüm çağrılar için gerçekleşir.

#### <a name="solution"></a>Çözüm:
Anahtar uygulamada hatalı yapılandırılmış ve hesap için yanlış anahtar kullanıyor ya da tüm anahtar kopyalanmadı.

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>Uygulama, yazma işlemleri için salt okunurdur anahtarlarını kullanıyor
401 MAC imzası sorunu yalnızca create veya Replace gibi yazma işlemlerinde oluşur, ancak okuma istekleri başarılı olur.

#### <a name="solution"></a>Çözüm:
İşlemin başarılı bir şekilde tamamlanmasına izin vermek için uygulamayı okuma/yazma anahtarı kullanacak şekilde değiştirin.

### <a name="race-condition-with-create-container"></a>Kapsayıcı oluştur ile yarış durumu
401 MAC imzası sorunu, bir kapsayıcı oluşturulduktan sonra kısa bir süre sonra görülür. Bu sorun yalnızca kapsayıcı oluşturma tamamlanana kadar oluşur.

#### <a name="solution"></a>Çözüm:
Kapsayıcı oluşturma ile bir yarış durumu vardır. Bir uygulama örneği kapsayıcı oluşturma işlemi tamamlanmadan önce kapsayıcıya erişmeye çalışıyor. Bu yarış durumu için en yaygın senaryo, uygulamanın çalışıyor olması ve kapsayıcının silinip aynı adla yeniden oluşturulması durumunda olur. SDK yeni kapsayıcıyı kullanmaya çalışır, ancak kapsayıcı oluşturma hala devam ediyor, bu yüzden anahtarlara sahip değil.

## <a name="next-steps"></a>Sonraki adımlar
* Azure Cosmos DB .NET SDK kullandığınızda sorunları [tanılayın ve sorun giderin](troubleshoot-dot-net-sdk.md) .
* [.Net v3](performance-tips-dotnet-sdk-v3-sql.md) ve [.net v2](performance-tips.md)için performans yönergeleri hakkında bilgi edinin.
* Java v4 SDK Azure Cosmos DB kullandığınızda sorunları [tanılayın ve sorun giderin](troubleshoot-java-sdk-v4-sql.md) .
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)için performans yönergeleri hakkında bilgi edinin.