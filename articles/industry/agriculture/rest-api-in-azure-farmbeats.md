---
title: Azure Farmtempts API 'Leri
description: JSON tabanlı yanıtlara sahip standartlaştırılmış bir Retıl arabirimi ile agricultürel işletmeler sağlayan Azure Farmtıts API 'Leri hakkında bilgi edinin.
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 292374bcce90da042dc6bd672feaad3750759aa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674512"
---
# <a name="azure-farmbeats-apis"></a>Azure Farmtempts API 'Leri

Bu makalede, Azure Farmtts API 'Leri açıklanmaktadır. Azure Farmstats API 'Leri, Azure Farmtts yeteneklerini kullanmanıza yardımcı olmak üzere JSON tabanlı yanıtlara sahip standart bir Retıl arabirimi sayesinde, örneğin:

- Algılayıcı, kamera, drbir, hava durumu, uydu ve seçkin zemin verileri almak için API 'Ler.
- Ortak veri sağlayıcıları genelinde verilerin normalleştirilmesi ve bağlamı oluşturma.
- Alınan tüm verilerde şema erişimi ve sorgu özellikleri.
- Agronomik özelliklerine göre sorgulanabilen otomatik meta verilerin oluşturulması.
- Hızlı model oluşturma için otomatik olarak oluşturulan zaman sırası toplamaları.
- Özel veri işleme işlem hatlarını kolayca derlemek için tümleşik Azure Data Factory altyapısı.

## <a name="application-development"></a>Uygulama geliştirme

Farmtts API 'Leri Swagger teknik belgelerini içerir. Tüm API 'Ler ve bunların karşılık gelen istekleri veya yanıtları hakkında bilgi için bkz. [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

Aşağıdaki tabloda, Farmrets veri hub 'ında bulunan tüm nesneler ve kaynaklar özetlenmektedir:

| Nesneler ve kaynaklar | Description
--- | ---|
Çiftlik | Grup, Farmtts sistemi içinde ilgilendiğiniz fiziksel bir konuma karşılık gelir. Her grubun bir grup adı ve benzersiz bir grup KIMLIĞI vardır. |
Cihaz  | Cihaz, grupta bulunan bir fiziksel cihaza karşılık gelir. Her cihazın benzersiz bir cihaz KIMLIĞI vardır. Bir cihaz genellikle Grup KIMLIĞI olan bir gruba sağlanır.
DeviceModel  | DeviceModel, cihaz, ağ geçidi veya düğüm olan üretici ve cihaz türü gibi cihazın meta verilerine karşılık gelir.
Algılayıcısı  | Algılayıcı, değerleri kaydeden fiziksel bir sensöre karşılık gelir. Bir algılayıcı genellikle cihaz KIMLIĞI olan bir cihaza bağlanır.
SensorModel  | SensorModel; üretici, analog veya dijital olan algılayıcı türü ve çevresel sıcaklık ve basınç gibi algılayıcı ölçümü gibi algılayıcı meta verilerine karşılık gelir.
Telemetri  | Telemetri, belirli bir algılayıcı ve zaman aralığı için telemetri iletilerini okuma yeteneği sağlar.
İş  | İş, istenen çıktıyı almak için Farmtempts sisteminde yürütülen etkinliklerin herhangi bir iş akışına karşılık gelir. Her iş bir iş KIMLIĞI ve iş türü ile ilişkilendirilir.
JobType  | JobType, sistem tarafından desteklenen farklı iş türlerine karşılık gelir. Sistem tanımlı ve Kullanıcı tanımlı iş türleri dahildir.
ExtendedType  | ExtendedType, sistemdeki sistem ve Kullanıcı tanımlı türlerin listesine karşılık gelir. ExtendedType, Farmtts sisteminde yeni bir algılayıcı, sahne veya sahne dosya türü ayarlamanıza yardımcı olur.
İş Ortağı  | İş ortağı, Farmfor algılayıcısı ve Imagery tümleştirme ortağına karşılık gelir.
HC  | Sahne, bir grup bağlamında oluşturulan herhangi bir çıkışa karşılık gelir. Her sahnenin bir sahne KIMLIĞI, sahne kaynağı, sahne türü ve onunla ilişkili Grup KIMLIĞI vardır. Her sahnenin KIMLIĞI, kendisiyle ilişkili birden fazla sahne dosyasına sahip olabilir.
Manzara dosyası |Manzara, tek bir sahne için oluşturulan tüm dosyalara karşılık gelir. Tek bir sahne kimliği ile ilişkili birden fazla manzara kimliği olabilir.
Kural  |Kural, gruba ilişkin verilerin bir uyarının tetiklenmesi için bir koşula karşılık gelir. Her kural grubun verileri bağlamındadır.
Uyarı  | Uyarı, bir kural koşulu karşılandığında üretilen bir bildirime karşılık gelir. Her uyarı bir kural bağlamındadır.
RoleDefinition  | RoleDefinition bir rol için izin verilen ve izin verilmeyen eylemleri tanımlıyor.
RoleAssignment  |Roleatama, bir rolün Kullanıcı veya hizmet sorumlusu atamasına karşılık gelir.

### <a name="data-format"></a>Veri biçimi

JSON, rastgele veri yapılarının basit bir metin gösterimini sağlayan, dilden bağımsız ortak bir veri biçimidir. Daha fazla bilgi için [JSON Web sitesine](https://www.json.org/)bakın.

## <a name="authentication-and-authorization"></a>Kimlik doğrulaması ve yetkilendirme

REST API HTTP istekleri Azure Active Directory (Azure AD) ile korunur.
REST API 'Lerine kimliği doğrulanmış bir istek yapmak için, API 'yi çağırabilmeniz için istemci kodu geçerli kimlik bilgileriyle kimlik doğrulaması gerektirir. Kimlik doğrulaması, Azure AD tarafından çeşitli aktörler arasında düzenlenir. Kimlik doğrulaması kanıtı olarak istemcinize bir erişim belirteci sağlar. Belirteç daha sonra REST API isteklerinin HTTP yetkilendirme üstbilgisinde gönderilir. Azure AD kimlik doğrulaması hakkında daha fazla bilgi için bkz. geliştiriciler için [Azure Active Directory](https://portal.azure.com) .

Erişim belirtecinin sonraki API isteklerinde gönderilmesi gerekir, başlık bölümünde şöyle olur:

```http
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP istek üstbilgileri

Azure Farmrets veri hub 'ına bir API çağrısı yaptığınızda belirtmeniz gereken en yaygın istek üstbilgileri aşağıda verilmiştir.


**Üst bilgi** | **Açıklama ve örnek**
--- | ---
İçerik Türü  | İstek biçimi (Content-Type: Application/ <format> ). Azure Farmrets veri hub 'ı API 'Leri için, biçim JSON olur. Content-Type: Application/JSON
Yetkilendirme  | API çağrısı yapmak için gereken erişim belirtecini belirtir. Yetkilendirme: taşıyıcı <Access-Token>
Kabul Et | Yanıt biçimi. Azure Farmrets veri hub 'ı API 'Leri için, biçim JSON olur. Kabul et: uygulama/JSON

### <a name="api-requests"></a>API istekleri

REST API bir istek yapmak için HTTP (GET, POST, PUT veya DELETE) yöntemini, API hizmetinin URL 'sini, sorgulanacak bir kaynağa yönelik URI 'yi, veri göndermek, güncelleştirmek veya silmek için bir veya daha fazla HTTP istek üst bilgisi eklemeniz gerekir.

API hizmetinin URL 'SI, veri hub 'ınızın URL 'sidir, örneğin, https:// \<yourdatahub-website-name> . azurewebsites.net.

İsteğe bağlı olarak, filtrelemeye yönelik çağrıları al, boyut sınırını sınırla ve yanıtlarındaki verileri sıralamak için sorgu parametreleri ekleyebilirsiniz.

Aşağıdaki örnek istek, cihaz listesini almak için kullanılır:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Çoğu GET, POST ve PUT çağrısı, bir JSON istek gövdesi gerektirir.

Aşağıdaki örnek istek bir cihaz oluşturur. Bu istek, istek gövdesinde JSON girişi içeriyor.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Sorgu parametreleri

REST GET çağrıları için, istek URI 'sinde bir veya daha fazla sorgu parametresi ekleyerek bir API yanıtında verileri filtreleyip sınırlayabilirsiniz ve sıralayabilirsiniz. Sorgu parametreleri için API belgelerine ve bireysel GET çağrılarına bakın.
Örneğin, cihaz listesini sorgulayıp (/Device üzerinde çağrı Al), aşağıdaki sorgu parametreleri belirlenebilir:

![Cihazların listesi](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Hata işleme

Azure Farmrets veri hub 'ı API 'Leri, standart HTTP hatalarını döndürür. En yaygın hata kodları şunlardır:

 |Hata kodu             | Description |
 |---                    | --- |
 |200                    | Başarılı |
 |201                    | Oluşturma (post) başarılı |
 |400                    | Hatalı Istek. İstekte bir hata var. |
 |401                    | Erişilmesini. API 'nin çağıranın kaynağa erişim yetkisi yok. |
 |404                    | Kaynak bulunamadı |
 |5XX                    | İç sunucu hatası. 5XX ile başlayan hata kodları, sunucuda bir hata olduğu anlamına gelir. Daha fazla bilgi için sunucu günlüklerine ve aşağıdaki bölüme bakın. |


Standart HTTP hatalarına ek olarak, Azure Farmrets veri merkezi API 'Leri de iç hataları aşağıdaki biçimde döndürür:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

Bu örnekte, bir grup oluşturulduğunda, giriş yükünde "ad" zorunlu alanı belirtilmez. Ortaya çıkan hata iletisi şöyle olacaktır:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Azure Active Directory için Kullanıcı veya uygulama kayıtları ekleyin

Azure Farmtts API 'Lerine, Azure Active Directory bir kullanıcı veya uygulama kaydı tarafından erişilebilir. Azure Active Directory bir uygulama kaydı oluşturmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com)gidin ve   >    >  **Yeni kayıt** uygulama kayıtları Azure Active Directory seçin. Alternatif olarak, mevcut bir hesabı kullanabilirsiniz.
2. Yeni bir hesap için aşağıdakileri yapın:

    - Bir ad girin.
    - **Yalnızca bu kuruluş dizinindeki hesapları (tek kiracı)** seçin.
    - Alanların geri kalanında varsayılan değerleri kullanın.
    - **Kaydet**’i seçin.

3. Yeni ve var olan uygulama kaydına **genel bakış** bölmesinde şunları yapın:

    - **ISTEMCI kimliğini** ve **Kiracı kimliğini** yakalayın.
    - Yeni bir istemci parolası oluşturmak ve **Istemci gizliliğini** yakalamak için **Sertifikalar ve gizli** diziler bölümüne gidin.
    - **Genel Bakış ' a** geri dönün ve **yerel dizinde uygulamayı Yönet ' in** yanındaki bağlantıyı seçin.
    - **Nesne kimliğini** yakalamak için **Özellikler** ' e gidin.

4. Veri hub Swagger (https:// <yourdatahub> . azurewebsites.net/swagger/index.html) sayfasına gidin ve şunları yapın:
    - **Roleatama API**'sine gidin.
    - Yeni oluşturduğunuz **nesne kimliği** Için bir **roleatama** NESNESI oluşturmak üzere bir gönderi gerçekleştirin.
 
```json
{
  "roleDefinitionId": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "objectId": "objectId from step 3 above",
  "objectIdType": "ServicePrincipalId",
  "tenantId": "tenant id of your Azure subscription"
}
```

  > [!NOTE]
  > Kullanıcı ekleme ve kayıt Active Directory hakkında daha fazla bilgi için bkz. [Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md).

Önceki adımları tamamladıktan sonra, uygulama kaydınız (istemci), taşıyıcı kimlik doğrulaması aracılığıyla bir erişim belirteci kullanarak Azure Farmtts API 'Lerini çağırabilir.

Üstbilgi bölümündeki sonraki API isteklerinde şu şekilde göndermek için erişim belirtecini kullanın:

```http
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```