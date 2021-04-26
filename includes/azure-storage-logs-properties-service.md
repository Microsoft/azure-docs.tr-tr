---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 9b18687c0a6f3e48d94431e88be8ae8137c9dcdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011149"
---
| Özellik | Açıklama |
|:--- |:---|
|**Adı** | Depolama hesabının adı. Örneğin: `mystorageaccount`.  |
|**requestUrl** | İstenen URL. |
|**userAgentHeader** | **Kullanıcı Aracısı üst bilgi** değeri, tırnak içinde. Örneğin: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**Referyeniden üstbilgi** | **Başvuran** üst bilgi değeri. Örneğin: `http://contoso.com/about.html`.|
|**Clientrequestıd 'ye sahip** | İsteğin **x-MS-Client-Request-id** üstbilgi değeri. Örneğin: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**özelliği** | Döndürülen nesnenin, tırnak içinde ETag tanımlayıcısı. Örneğin: `0x8D101F7E4B662C4`.  |
|**Sunucu Redms** | İstenen işlemi gerçekleştirmek için milisaniye olarak ifade edilen toplam süre. Bu değer ağ gecikmesini (gelen isteği okumak ve yanıtı istek sahibine göndermek için geçen süre) içermez. Örneğin: `22`. |
|**Türü** | Bu istekle ilişkili hizmet. Örneğin: `blob` ,, `table` `files` veya `queue` . |
|**operationCount** | İstekte bulunan günlüğe kaydedilen her işlemin sayısı. Bu sayı bir diziniyle başlar `0` . Bazı istekler birden fazla işlem gerektirir. Çoğu istek yalnızca bir işlem gerçekleştirir. Örneğin: `1`. |
|**requestHeaderSize** | İstek üstbilgisinin boyutu bayt cinsinden ifade edilir. Örneğin: `578`. <br>Bir istek başarısız olursa, bu değer boş olabilir. |
|**requestBodySize** | Depolama hizmeti tarafından okunan, bayt olarak ifade edilen istek paketlerinin boyutu. <br> Örneğin: `0`. <br>Bir istek başarısız olursa, bu değer boş olabilir.  |
|**responseHeaderSize** | Yanıt üstbilgisinin boyutu bayt cinsinden ifade edilir. Örneğin: `216`. <br>Bir istek başarısız olursa, bu değer boş olabilir.  |
|**responseBodySize** | Depolama hizmeti tarafından yazılan Yanıt paketlerinin bayt cinsinden boyutu. Bir istek başarısız olursa, bu değer boş olabilir. Örneğin: `216`.  |
|**requestMd5** | İstekteki **Content-MD5** üst bilgisi veya **x-MS-Content-MD5** üst bilgisi değeri. Bu alanda belirtilen MD5 karma değeri istekteki içeriği temsil eder. Örneğin: `788815fd0198be0d275ad329cafd1830`. <br>Bu alan boş olabilir.  |
|**serverMd5** | Depolama hizmeti tarafından hesaplanan MD5 karmasının değeri. Örneğin: `3228b3cf1069a5489b298446321f8521`. <br>Bu alan boş olabilir.  |
|**Zamanı** | Döndürülen nesnenin son değiştirilme zamanı (LMT).  Örneğin: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Birden çok nesne döndüresağlayan işlemler için bu alan boştur. |
|**conditionsUsed** | Bir koşulu temsil eden, anahtar-değer çiftleri için noktalı virgülle ayrılmış bir liste. Koşullar aşağıdakilerden herhangi biri olabilir: <li> If-Modified-Since <li> If-değiştirilmemiş-bu yana <li> If-Match <li> If-None-Match  <br> Örneğin: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | Depolama hizmetine gönderilen isteğin Içerik uzunluğu üstbilgisinin değeri. İstek başarılı olduysa, bu değer requestBodySize değerine eşittir. Bir istek başarısız olursa, bu değer requestBodySize eşit olmayabilir veya boş olabilir. |
|**tlsVersion** | İstek bağlantısında kullanılan TLS sürümü. Örneğin: `TLS 1.2`. |
|**Smbtreeconnectıd** | Sunucu Ileti bloğu ( **SMB) ağaç** bağlantı zamanında oluşturulmuştur. Örnek: `0x3` |
|**smbPersistentHandleID** | Ağ yeniden bağlandığında SMB2 oluşturma isteğinden kalıcı tanıtıcı KIMLIĞI.  [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 olarak SMB2_FILEID olarak başvurulur **. Kalıcı**. Örnek: `0x6003f` |
|**Smbvolatilehandleıd** | Ağ yeniden bağlandığında geri dönüştürülecek bir SMB2 CREATE isteğinden geçici tanıtıcı KIMLIĞI.  [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 olarak SMB2_FILEID olarak başvurulur **. Geçici**. Örnek: `0xFFFFFFFF00000065` |
|**Smbmessageıd** | Bağlantı göreli **MessageID**. Örnek: `0x3b165` |
|**Smbalacaklı Tstüketilen** | 64K birimi olarak istek tarafından tüketilen giriş veya çıkış. Örnek: `0x3` |
|**smbCommandDetail** | Genel istek türü yerine bu özel istek hakkında daha fazla bilgi. Örnek: `0x2000 bytes at offset 0xf2000` |
|**Smbfileıd** | Dosya veya dizinle ilişkili **FileId** .  Kabaca bir NTFS FileId 'ye benzer. Örnek: `0x9223442405598953` |
|**Smbsessionıd** | Oturum Kurulum zamanında SMB2 **SessionID** oluşturuldu. Örnek: `0x8530280128000049` |
|**Smbcommandana uint32** | **SMB2_HEADER. Command** içindeki değer. Şu anda bu sayı 0 ile 18 arasında bir sayıdır. Örnek: `0x6` |
|**smbCommandMinor** | Doğru yerde, **Smbcommandana**'nin alt sınıfı. Örnek: `DirectoryCloseAndDelete` |