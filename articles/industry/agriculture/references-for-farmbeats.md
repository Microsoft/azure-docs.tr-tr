---
title: Farmto 'Lar için başvurular
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: efd294910531509d736dbda274406bd7c801c124
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931211"
---
# <a name="references"></a>Başvurular

Aşağıda, Azure Farmtts API 'Lerinin ana hattını oluşturan notların ve yönergelerin bir koleksiyonu verilmiştir.

## <a name="rest-api"></a>REST API

Azure Farmtts API 'Leri, JSON tabanlı yanıtlara sahip standartlaştırılmış bir uyumlu arabirim sunan agricultürel ve bu özelliği Azure Farmrets özelliklerinden yararlanmanıza yardımcı olur:

- Algılayıcı, kamera, kuruta, hava durumu, uydu ve seçkin zemin verileri almak için API 'leri.
- Ortak veri sağlayıcıları genelinde verilerin normalleştirilmesi/bağlamı.
- Alınan tüm verilerde şema erişimi ve sorgu özellikleri.
- Agronomik özelliklerine göre sorgulanabilen otomatik meta verilerin oluşturulması.  
- Hızlı model oluşturma için otomatik olarak oluşturulan zaman sırası toplamaları.
- Özel veri işleme işlem hatlarını kolayca derlemek için tümleşik Azure Data Factory (ADF) altyapısı.

## <a name="application-development"></a>Uygulama geliştirme

API 'Ler Swagger teknik belgelerini içerir. Tüm API 'Ler ve bunların karşılık gelen istekleri/yanıtları hakkında bilgi için bkz. [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) .

Bu, Farmtts veri hub 'ında tüm nesnelerin/kaynakların bir özetidir:

Çiftlik | Grup, Farmtts sistemi içinde ilgilendiğiniz fiziksel bir konuma karşılık gelir. Her grubun bir grup adı ve benzersiz bir grup KIMLIĞI vardır.
--- | ---|
Cihaz  | Cihaz, grupta bulunan bir fiziksel cihaza karşılık gelir. Her cihazın benzersiz bir cihaz KIMLIĞI vardır. Cihaz genellikle Grup KIMLIĞI olan bir gruba sağlanır.
DeviceModel  | DeviceModel, Cihazın üreticisi, ağ geçidi ya da düğüm gibi verilerin meta verilerine karşılık gelir.
Algılayıcısı  | Algılayıcı, değerleri kaydeden fiziksel bir sensöre karşılık gelir. Bir algılayıcı genellikle cihaz KIMLIĞI olan bir cihaza bağlanır.
SensorModel  | SensorModel, üreticinin üreticisi, bir algılayıcı türü olan analog veya dijital, ortam sıcaklığı, basınç vb. gibi sensörin meta verilerine karşılık gelir.
Telemetri  | Telemetri, belirli bir algılayıcı ve zaman aralığı için telemetri iletilerini okuma yeteneği sağlar.
İş  | İş, istenen çıktıyı almak için Farmtempts sisteminde yürütülen herhangi bir etkinlik iş akışına karşılık gelir. Her iş bir iş KIMLIĞI ve iş türü ile ilişkilendirilir.
JobType  | JobType, sistem tarafından desteklenen farklı iş türlerine karşılık gelir. Bu, sistem tanımlı & Kullanıcı tanımlı iş türlerini içerir.
ExtendedType  | ExtendedType, sistem & sistemdeki kullanıcı tanımlı türlerin listesine karşılık gelir. Bu, Farmcts sisteminde yeni bir algılayıcı veya sahne ya da manzara türü kurulumuna yardımcı olur.
İş Ortağı  | İş ortağı, Farmfor için sensörler/Imagery tümleştirme ortağına karşılık gelir
HC  | Sahne, bir grup bağlamında oluşturulan herhangi bir çıkışa karşılık gelir. Her sahnenin, onunla ilişkili bir sahne KIMLIĞI, sahne kaynağı, sahne türü ve Grup KIMLIĞI vardır. Her sahnenin KIMLIĞI, kendisiyle ilişkili birden fazla sahne dosyasına sahip olabilir.
Manzara dosyası |Manzara, tek bir sahne için oluşturulan tüm dosyalara karşılık gelir. Tek bir sahne kimliği ile ilişkili birden fazla manzara kimliği olabilir.
Kural  |Kural, gruba ilişkin verilerin bir uyarının tetiklenmesi için bir koşula karşılık gelir. Her kural grubun verileri bağlamında olacaktır.
Uyarı  | Uyarı, bir kural koşulu karşılandığında üretilen bir bildirime karşılık gelir. Her uyarı bir kural bağlamında olacaktır.
RoleDefinition  | RoleDefinition bir rol için izin verilen ve izin verilmeyen eylemleri tanımlıyor.
RoleAssignment  |Roleatama, bir rolün Kullanıcı veya hizmet sorumlusu atamasına karşılık gelir.

**Veri biçimi**

JSON (JavaScript Nesne Gösterimi), rastgele veri yapılarının basit bir metin gösterimini sağlayan ortak, dilden bağımsız bir veri biçimidir. Daha fazla bilgi için bkz. json.org.

## <a name="authentication-and-authorization"></a>Kimlik doğrulama ve yetkilendirme

REST API HTTP istekleri Azure Active Directory (Azure AD) ile korunur.
REST API 'Lerine kimliği doğrulanmış bir istek yapmak için, API 'yi çağırabilmeniz için istemci kodu geçerli kimlik bilgileriyle kimlik doğrulaması gerektirir. Kimlik doğrulaması, Azure AD tarafından çeşitli aktörler arasında düzenlenir ve istemcinizi kimlik doğrulaması kanıtı olarak bir erişim belirteci sağlar. Belirteç daha sonra REST API isteklerinin HTTP yetkilendirme üstbilgisinde gönderilir. Azure AD kimlik doğrulaması hakkında daha fazla bilgi için bkz. geliştiriciler için [Azure Active Directory](https://portal.azure.com) .

Erişim belirtecinin şu şekilde üstbilgi bölümündeki sonraki API isteklerinde gönderilmesi gerekir:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**HTTP istek üstbilgileri**

Azure Farmtts veri merkezine bir API çağrısı yaparken belirtilmesi gereken en yaygın istek üstbilgileri aşağıda verilmiştir:


**Üst Bilgi** | **Açıklama ve örnek**
--- | ---
İçerik türü  | Azure Farmtts veri merkezi API 'Leri biçimi Için istek biçimi (Content-Type: Application/<format>) JSON. Content-Type: Application/JSON
Yetkilendirme  | API çağrısı yapmak için gereken erişim belirtecini belirtir. **Yetkilendirme: taşıyıcı < erişim-belirteç >**
ettiğinizde | Yanıt biçimi. Azure Farmfor Data hub API 'Lerinde biçim JSON **kabul etme: Application/JSON**

**API istekleri**

REST API bir istek yapmak için, HTTP (GET, POST, PUT veya DELETE) yöntemini, API hizmetinin URL 'sini, sorgulanacak bir kaynağa URI 'yi, verileri güncelleştirmek, güncelleştirmek veya silmek için bir veya daha fazla HTTP istek üst bilgisi gönderebilirsiniz.

API hizmetinin URL 'SI, veri hub URL 'SI https://\<yourdatahub-Web sitesi-adı >. azurewebsites. net ' in Isteğe bağlı olarak filtrelemeye yönelik çağrıları al, boyut sınırını sınırla ve yanıtlarındaki verileri sıralamak için sorgu parametrelerini ekleyebilirsiniz.

Aşağıdaki örnek istek, cihazların listesini almak için gereklidir:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Çoğu GET, POST ve PUT çağrısı, bir JSON istek gövdesi gerektirir.

Aşağıdaki örnek istek bir cihaz oluşturmaktır (Bu, istek gövdesi ile bir JSON girişi olur).

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**Sorgu parametreleri**

REST **Get** çağrıları için, istek URI 'sinde bir veya daha fazla sorgu parametresi ekleyerek bir API yanıtında verileri filtreleyip sınırlayabilirsiniz ve sıralayabilirsiniz. Sorgu parametreleri için API belgelerine başvurun ve tek tek al aramalarını görüntüleyin.
Örneğin, cihaz listesi sorgulanırken (/Device üzerinde çağrı Al), aşağıdaki sorgu parametreleri belirtilebilir:  

![Proje grubu ları](./media/for-references/query-parameters-device-1.png)

**Hata işleme**

Azure Farmtempts veri merkezi API 'SI, standart HTTP hatalarını döndürür. En yaygın hata kodları şunlardır:

 |Hata kodu             | Açıklama |
 |---                    | --- |
 |200                    | Başarılı |
 |201                    | Oluşturma (post) başarılı |
 |400                    | Hatalı Istek. İstekte bir hata var |
 |401                    | Erişilmesini. API 'nin çağıranı, kaynağa erişme yetkisine sahip değil |
 |404                    | Kaynak bulunamadı |
 |5XX                    | İç sunucu hatası. 5XX ile başlayan hata kodları, sunucuda bir hata olduğu anlamına gelir. Daha fazla bilgi için sunucu günlüklerine ve aşağıdaki bölüme bakın. |


Standart HTTP hatalarına ek olarak, Azure Farmtts veri merkezi API 'Leri de iç hataları aşağıdaki biçimde döndürür:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

Örnek: bir grup oluştururken, giriş yükünde zorunlu bir "Name" alanı belirtilmedi. Ortaya çıkan hata iletisi şöyle olacaktır:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>Azure Active Directory kullanıcılara veya uygulama kayıtlarını ekleme

Azure Farmtts API 'Lerine Azure Active Directory bir kullanıcı veya bir uygulama kaydı tarafından erişilebilir. Azure Active Directory bir uygulama kaydı oluşturmak için aşağıdaki adımları uygulayın:  

1. [Azure portal](https://portal.azure.com) **Azure Active Directory, uygulama kayıtları**> **Yeni kayıt**' a gidin. Alternatif olarak, mevcut bir hesabı kullanabilirsiniz.
2. Yeni bir hesap için aşağıdakileri tamamlamayı doğrulayın:

    - Ad girin
    - **Yalnızca bu kuruluş dizinindeki hesapları Seç (tek kiracılı)**
    - Alanların geri kalanında varsayılan değerler
    - **Kaydol** ' u seçin

3. Yeni/mevcut uygulama kaydı ' ndan **Genel Bakış ' a**, aşağıdakileri doldurun

    - **ISTEMCI kimliğini** ve **Kiracı kimliğini**yakalayın.
    - Yeni bir istemci parolası oluşturmak ve **Istemci gizliliğini**yakalamak için **Sertifikalar ve gizli** diziler bölümüne gidin.
    - Genel Bakış ' a geri dönün ve **yerel dizinde uygulamayı Yönet ' in** yanındaki bağlantıya tıklayın.
    - **Nesne kimliğini** yakalamak için **Özellikler** 'e gidin

4. [Veri hub 'ının Swagger](https://<yourdatahub>.azurewebsites.net/swagger/index.html) öğesine gidin ve aşağıdaki adımları gerçekleştirin:
    - **Roleatama API** 'sine gidin
    - Yeni oluşturduğunuz **nesne kimliği** Için bir roleatama oluşturmak üzere bir **gönderi** yapın. – JSON girişi:

  > [!NOTE]
  > Kullanıcı ve AD kaydı ekleme hakkında daha fazla bilgi için bkz. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) .

Yukarıdaki adımları tamamladıktan sonra, uygulama kaydınız (istemci), taşıyıcı kimlik doğrulaması aracılığıyla bir erişim belirteci kullanarak Azure Farmtts API 'Lerini çağırabilir.  

Üstbilgi bölümündeki sonraki API isteklerinde şu şekilde göndermek için erişim belirtecini kullanın:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
