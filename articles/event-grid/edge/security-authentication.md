---
title: Güvenlik ve kimlik doğrulaması-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge Event Grid güvenlik ve kimlik doğrulaması.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 77b4b265b2e993ccdbc9e07fd2dab5a37ed22a6b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992164"
---
# <a name="security-and-authentication"></a>Güvenlik ve kimlik doğrulaması

Güvenlik ve kimlik doğrulaması gelişmiş bir kavramdır ve öncelikle Event Grid temel bilgileri hakkında daha fazla bilgiler sahibi olmanızı gerektirir. IoT Edge Event Grid yeni başladıysanız [buradan](concepts.md) başlayın. Event Grid modül, IoT Edge üzerindeki mevcut güvenlik altyapısında oluşturulur. Ayrıntılar ve kurulum için [Bu belgeye](../../iot-edge/security.md) bakın.

Aşağıdaki bölümlerde bu ayarların güvenliğinin nasıl sağlandığı ve doğrulandığına ilişkin ayrıntılı bilgiler verilmektedir:-

* TLS yapılandırması
* Gelen istemci kimlik doğrulaması
* Giden sunucu kimlik doğrulaması
* Giden istemci kimlik doğrulaması

>[!IMPORTANT]
>Event Grid modülü güvenliği ve kimlik doğrulaması, IoT Edge mevcut altyapıdan faydalanır. Varsayım, IoT Edge Sub sisteminin güvenli hale getirilmesine yönelik bir seçenektir.

>[!IMPORTANT]
>Event Grid yapılandırması **Varsayılan olarak güvenlidir**. Aşağıdaki alt bölümlerde, kimlik doğrulamanın yönlerini geçersiz kılmak için kullanabileceğiniz tüm seçenekler ve olası değerler açıklanmaktadır. Herhangi bir değişiklik yapmadan önce etkiyi anlayın. Değişikliklerin etkili olması için Event Grid modülünün yeniden dağıtılması gerekir.

## <a name="tls-configuration-aka-server-authentication"></a>TLS yapılandırması (bir. k. sunucu kimlik doğrulaması)

Event Grid modülü hem HTTP hem de HTTPS uç noktalarını barındırır. Her IoT Edge modüle IoT Edge güvenlik arka plan programı tarafından bir sunucu sertifikası atanır. Uç noktanın güvenliğini sağlamak için sunucu sertifikasını kullanıyoruz. Süre dolduğunda, modül IoT Edge güvenlik arka plan programından yeni bir sertifikayla otomatik olarak yenilenir.

Varsayılan olarak, yalnızca HTTPS iletişimine izin verilir. Bu davranışı, **gelen: serverAuth: tlsPolicy** yapılandırması aracılığıyla geçersiz kılabilirsiniz. Aşağıdaki tablo bu özelliğin olası değerlerini yakalar.

| Olası değerler | Açıklama |
| ---------------- | ------------ |
| Katı | Varsayılan. Yalnızca HTTPS 'yi etkinleştirilir
| Etkin | Hem HTTP hem de HTTPS 'yi sunar
| Devre dışı | Yalnızca HTTP 'yi etkinleştirilir

## <a name="inbound-client-authentication"></a>Gelen istemci kimlik doğrulaması

İstemciler, yönetim ve/veya çalışma zamanı işlemleri yapan varlıklardır. İstemciler diğer IoT Edge modüller, IoT olmayan uygulamalar olabilir.

Event Grid modülü iki tür istemci kimlik doğrulamasını destekler:-

* Paylaşılan erişim Imzası (SAS) anahtar tabanlı
* Sertifika tabanlı

Varsayılan olarak, Event Grid modülü yalnızca sertifika tabanlı kimlik doğrulamasını kabul edecek şekilde yapılandırılmıştır. Başlangıçta Event Grid modülü, IoT Edge güvenlik arka plan programından "Trustdemeti" alır ve bunu istemci sertifikasını doğrulamak için kullanır. Bu zincire çözümlenmeyecek istemci sertifikaları, `UnAuthorized`ile reddedilir.

### <a name="certificate-based-client-authentication"></a>Sertifika tabanlı istemci kimlik doğrulaması

Sertifika tabanlı kimlik doğrulaması varsayılan olarak açık olur. Sertifika tabanlı kimlik doğrulamayı, **gelen: clientAuth: clientCert: Enabled**özelliği aracılığıyla devre dışı bırakmayı tercih edebilirsiniz. Aşağıdaki tablo olası değer (ler) i yakalar.

| Olası değerler | Açıklama |
| ----------------  | ------------ |
| doğru | Varsayılan. İstemci sertifikası sunmak için Event Grid modülüne yönelik tüm istekleri gerektirir. Ayrıca, **gelen: clientAuth: clientCert: kaynağı**' nı yapılandırmanız gerekir.
| yanlış | Bir istemciyi sertifikayı sunmaya zormayın.

Aşağıdaki tablo **gelen: clientAuth: clientCert: Source** için olası değerleri yakalar

| Olası değerler | Açıklama |
| ---------------- | ------------ |
| IoT Edge | Varsayılan. Tüm istemci sertifikalarını doğrulamak için IoT Edge Trustpaketini kullanır.

Bir istemci otomatik olarak imzalanan bir sunalıysa, varsayılan olarak Event Grid modülü bu istekleri reddeder. **Gelen: clientAuth: clientCert: allowUnknownCA** özelliği aracılığıyla kendinden imzalı istemci sertifikalarına izin vermeyi seçebilirsiniz. Aşağıdaki tablo olası değer (ler) i yakalar.

| Olası değerler | Açıklama |
| ----------------  | ------------|
| doğru | Varsayılan. Otomatik olarak imzalanan sertifikaların başarıyla sunulmasını sağlar.
| yanlış | Otomatik olarak imzalanan sertifikalar sunulursa istekleri başarısız olur.

>[!IMPORTANT]
>Üretim senaryolarında, **gelen: clientAuth: clientCert: allowUnknownCA** **değerini false**olarak ayarlamak isteyebilirsiniz.

### <a name="sas-key-based-client-authentication"></a>SAS anahtar tabanlı istemci kimlik doğrulaması

Sertifika tabanlı kimlik doğrulamasına ek olarak, Event Grid modülü SAS anahtar tabanlı kimlik doğrulaması da yapabilir. SAS anahtarı, tüm gelen çağrıları doğrulamak için kullanması gereken Event Grid modülünde yapılandırılmış bir gizli dizi gibidir. İstemcilerin ' AEG-SAS-Key ' HTTP üstbilgisinde gizli anahtar belirtmesi gerekir. Eşleşmeyen `UnAuthorized` istek, reddedilir.

SAS anahtar tabanlı kimlik doğrulamasını denetlemeye yönelik yapılandırma **: ınclientauth: sasKeys: Enabled**.

| Olası değerler | Açıklama  |
| ----------------  | ------------ |
| doğru | SAS anahtar tabanlı kimlik doğrulamasına izin verir. **Gelen: clientAuth: sasKeys: KEY1** veya **Inbound: clientauth: saskeys: key2**
| yanlış | Varsayılan. SAS anahtar tabanlı kimlik doğrulaması devre dışı.

 **gelen: clientAuth: sasKeys: KEY1** ve **Inbound: clientauth: saskeys: key2** , gelen isteklere karşı denetlenecek Event Grid modülünü yapılandırdığınız anahtarlardır. Anahtarların en az birinin yapılandırılması gerekir. İstek yapan istemcinin anahtarı, '**AEG-SAS-Key**' istek üstbilgisinin bir parçası olarak sunması gerekir. Her iki anahtar yapılandırılırsa, istemci anahtarlardan birini sunabilir.

> [!NOTE]
>Kimlik doğrulama yöntemlerini her iki şekilde de yapılandırabilirsiniz. Böyle bir durumda SAS anahtarı önce denetlenir ve yalnızca bu başarısız olursa sertifika tabanlı kimlik doğrulaması gerçekleştirilir. Bir isteğin başarılı olması için, kimlik doğrulama yöntemlerinden yalnızca birinin başarılı olması gerekir.

## <a name="outbound-client-authentication"></a>Giden istemci kimlik doğrulaması

Giden bağlamdaki istemci Event Grid modüle başvurur. Gerçekleştirilen işlem abonelere olayları teslim ediyor. Abone modüller sunucu olarak kabul edilir.

Her IoT Edge modüle IoT Edge güvenlik arka plan programı tarafından bir kimlik sertifikası atanır. Giden çağrılar için kimlik sertifikası kullanıyoruz. Süre dolduğunda, modül IoT Edge güvenlik arka plan programından yeni bir sertifikayla otomatik olarak yenilenir.

Giden istemci kimlik doğrulamasını denetleyen yapılandırma **giden: clientAuth: clientCert: Enabled**.

| Olası değerler | Açıklama |
| ----------------  | ------------ |
| doğru | Varsayılan. Event Grid modülünden gelen tüm giden isteklerin bir sertifika sunması gerekir. **Giden: clientAuth: clientCert: Source**yapılandırması gerekir.
| yanlış | Event Grid modülünün sertifikasını sunması gerekmez.

Sertifika kaynağını denetleyen yapılandırma **giden: clientAuth: clientCert: Source**.

| Olası değerler | Açıklama |
| ---------------- | ------------ |
| IoT Edge | Varsayılan. IoT Edge güvenlik Daemon tarafından yapılandırılan modülün kimlik sertifikasını kullanır.

### <a name="outbound-server-authentication"></a>Giden sunucu kimlik doğrulaması

Bir Event Grid abonesi için hedef türlerden biri "Web kancası" dir. Varsayılan olarak, bu tür aboneler için yalnızca HTTPS uç noktaları kabul edilir.

Web kancası hedef ilkesini denetlemek için yapılandırma **: Web kancası: httpsOnly**.

| Olası değerler | Açıklama |
| ----------------  | ------------ |
| doğru | Varsayılan. Yalnızca HTTPS uç noktası olan abonelere izin verir.
| yanlış | HTTP veya HTTPS uç noktası olan abonelere izin verir.

Varsayılan olarak, Event Grid modülü abonenin sunucu sertifikasını doğrular. **Giden: Web kancası: skipServerCertValidation**öğesini geçersiz kılarak doğrulamayı atlayabilirsiniz. Olası değerler şunlardır:-

| Olası değerler | Açıklama |
| ----------------  | ------------ |
| doğru | Abonenin sunucu sertifikasını doğrulama.
| yanlış | Varsayılan. Abonenin sunucu sertifikasını doğrulayın.

Abonenin sertifikası kendinden imzalanmışsa, varsayılan olarak Event Grid modül bu aboneleri reddeder. Kendinden imzalı sertifikaya izin vermek için **giden: Web kancası: allowUnknownCA**' yı geçersiz kılabilirsiniz. Aşağıdaki tablo olası değer (ler) i yakalar.

| Olası değerler | Açıklama |
| ----------------  | ------------ |
| doğru | Varsayılan. Otomatik olarak imzalanan sertifikaların başarıyla sunulmasını sağlar.
| yanlış | Otomatik olarak imzalanan sertifikalar sunulursa istekleri başarısız olur.

>[!IMPORTANT]
>Üretim senaryolarında **giden: Web kancası: allowUnknownCA** ' yı **false**olarak ayarlamak isteyeceksiniz.

> [!NOTE]
>IoT Edge ortam otomatik olarak imzalanan sertifikalar oluşturur. Öneri, üretim iş yükleri için yetkili CA 'Lar tarafından verilen sertifikaları oluşturmak ve hem gelen hem de giden ' de **Allowunknownca** özelliğini **false**olarak ayarlamanıza yöneliktir.

## <a name="summary"></a>Özet

Bir Event Grid modülü **Varsayılan olarak güvenlidir**. Bu varsayılan değerleri üretim dağıtımlarınız için tutmanız önerilir.

Yapılandırma sırasında kullanılacak temel ilkeler şunlardır:

* Modüle yalnızca HTTPS isteklerine izin verin.
* Yalnızca sertifika tabanlı istemci kimlik doğrulamasına izin verin. Yalnızca iyi bilinen CA 'Lar tarafından verilen sertifikalara izin verin. Otomatik olarak imzalanan sertifikalara izin vermeyin.
* SASKey tabanlı istemci kimlik doğrulamasına izin vermeyin.
* Event Grid modülün kimlik sertifikasını her zaman giden çağrılar üzerinde Sun.
* Web kancası hedef türleri için yalnızca HTTPS abonelerine izin verin.
* Web kancası hedef türleri için abonenin sunucu sertifikasını her zaman doğrulayın. Yalnızca iyi bilinen CA 'Lar tarafından verilen sertifikalara izin verin. Otomatik olarak imzalanan sertifikalara izin vermeyin.

Varsayılan olarak, Event Grid modülü aşağıdaki yapılandırmayla dağıtılır:-

 ```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
