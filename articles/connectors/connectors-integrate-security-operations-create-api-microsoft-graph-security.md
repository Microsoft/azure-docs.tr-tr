---
title: Microsoft Graph güvenliği & güvenlik işlemlerini tümleştirin ve yönetin
description: Microsoft Graph güvenlik & ile uygulamanızın tehdit korumasını, algılamayı ve yanıtını geliştirebilirsiniz Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ecfan
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: a83cd68df2f1d722517d6239bf6959075860d0b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94888547"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Güvenlik işlemlerini Microsoft Graph güvenlik & tümleştirerek tehdit korumasını geliştirebilirsiniz Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [Microsoft Graph güvenlik](/graph/security-concept-overview) Bağlayıcısı sayesinde, Microsoft güvenlik ürünleri, hizmetleri ve iş ortaklarının tümleştirilmesine yönelik otomatik iş akışları oluşturarak uygulamanızın tehditleri nasıl algıladığı, koruduğunu ve yanıtladığını artırabilirsiniz. Örneğin, uyarılar gibi Microsoft Graph güvenlik varlıklarını izleyen ve yöneten [Azure Güvenlik Merkezi oyun kitapları](../security-center/workflow-automation.md) oluşturabilirsiniz. Microsoft Graph güvenlik Bağlayıcısı tarafından desteklenen bazı senaryolar aşağıda verilmiştir:

* Sorguları veya uyarı KIMLIĞINE göre uyarıları alın. Örneğin, yüksek önem derecesi uyarıları içeren bir liste alabilirsiniz.

* Uyarıları güncelleştirin. Örneğin, uyarı atamalarını güncelleştirebilir, uyarılara yorum ekleyebilir veya uyarıları etiketleyebilir.

* [Uyarı abonelikleri (Web kancaları)](/graph/api/resources/webhooks)oluşturarak uyarıların ne zaman oluşturulduğunu veya değiştirildiğini izler.

* Uyarı aboneliklerinizi yönetin. Örneğin, etkin abonelikler alabilir, bir aboneliğin süre sonu süresini genişletebilir veya abonelikleri silebilirsiniz.

Mantıksal uygulamanızın iş akışı, Microsoft Graph güvenlik bağlayıcısından yanıt alan eylemleri kullanabilir ve bu çıktıyı iş akışınızda diğer eylemler için kullanılabilir hale getirir. Ayrıca, iş akışınızda başka eylemlere de sahip olabilirsiniz Microsoft Graph güvenlik Bağlayıcısı eylemleriyle çıktıyı kullanın. Örneğin, Microsoft Graph güvenlik Bağlayıcısı aracılığıyla yüksek önem derecesine sahip uyarılar alırsanız, bu uyarıları Outlook bağlayıcısını kullanarak bir e-posta iletisiyle gönderebilirsiniz. 

Microsoft Graph güvenliği hakkında daha fazla bilgi edinmek için [Microsoft Graph güvenlik API 'sine genel bakış](/graph/security-concept-overview)bölümüne bakın. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps? ne olduğunu](../logic-apps/logic-apps-overview.md)gözden geçirin. Power otomatikleştir veya PowerApps arıyorsanız, bkz. [Power otomatikleştir nedir? ne](https://flow.microsoft.com/) de [Power Apps nedir?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* Microsoft Graph Güvenlik bağlayıcısını kullanmak için Azure Active Directory (AD) kiracısının yöneticisinin *açıkça verilmiş* onayı olması gerekir. Bu, [Microsoft Graph Güvenlik Kimlik Doğrulaması gereksinimlerinin](/graph/security-authorization) bir parçasıdır. Bu onay, Microsoft Graph güvenlik bağlayıcısının uygulama KIMLIĞI ve adının yanı sıra [Azure Portal](https://portal.azure.com)de bulabileceğinizi gerektirir:

  | Özellik | Değer |
  |----------|-------|
  | **Uygulama adı** | `MicrosoftGraphSecurityConnector` |
  | **Uygulama Kimliği** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Bağlayıcıya izin vermek için, Azure AD kiracı yöneticiniz aşağıdaki adımları izleyebilir:

  * [Azure AD uygulamalarına kiracı yöneticisi onayı verme](../active-directory/develop/v2-permissions-and-consent.md).

  * Mantıksal uygulamanız ilk kez çalıştırıldığında [uygulama onayı deneyimi](../active-directory/develop/application-consent-experience.md) aracılığıyla uygulamanız Azure AD kiracınızın yöneticisinden onay isteyebilir.
   
* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Uyarılar gibi Microsoft Graph güvenlik varlıklarınıza erişmek istediğiniz mantıksal uygulama. Microsoft Graph güvenlik tetikleyicisi kullanmak için boş bir mantıksal uygulama gerekir. Microsoft Graph bir güvenlik eylemi kullanmak için, senaryonuza uygun tetikleyiciyle başlayan bir mantıksal uygulama gerekir.

## <a name="connect-to-microsoft-graph-security"></a>Microsoft Graph güvenliğine bağlanma 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com/)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Boş Logic Apps için, Microsoft Graph bir güvenlik eylemi eklemeden önce tetikleyiciyi ve istediğiniz diğer eylemleri ekleyin.

   -veya-

   Mevcut Logic Apps için, Microsoft Graph güvenlik eylemi eklemek istediğiniz son adım altında **yeni adım**' ı seçin.

   -veya-

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini (+) seçin ve **Eylem Ekle**' yi seçin.

1. Arama kutusuna filtreniz olarak "Microsoft Graph Security" yazın. Eylemler listesinden istediğiniz eylemi seçin.

1. Microsoft Graph güvenlik kimlik bilgilerinizle oturum açın.

1. Seçtiğiniz eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="add-triggers"></a>Tetikleyici ekleme

Azure Logic Apps, her mantıksal uygulama, belirli bir olay gerçekleştiğinde veya belirli bir koşul karşılandığında tetiklenen bir [tetikleyiciyle](../logic-apps/logic-apps-overview.md#logic-app-concepts)başlamalıdır. Tetikleyici her tetiklendiğinde Logic Apps altyapısı bir mantıksal uygulama örneği oluşturur ve uygulamanızın iş akışını çalıştırmaya başlar.

> [!NOTE] 
> Tetikleyici tetiklendiğinde, tetikleyici tüm yeni uyarıları işler. Hiçbir uyarı alınmıyorsa, tetikleyici çalıştırması atlanır. Sonraki tetikleyici yoklama, tetikleyicisinin özelliklerinde belirttiğiniz yinelenme aralığına göre yapılır.

Bu örnek, uygulamanıza yeni uyarılar gönderildiğinde bir mantıksal uygulama iş akışını nasıl başlatacağınızı gösterir.

1.  Azure portal veya Visual Studio 'da mantıksal uygulama Tasarımcısı ' nı açan boş bir mantıksal uygulama oluşturun. Bu örnek Azure portal kullanır.

1.  Tasarımcıda arama kutusuna filtreniz olarak "Microsoft Graph Security" yazın. Tetikleyiciler listesinde, bu tetikleyiciyi seçin: **tüm yeni uyarılarda**

1.  Tetikleyicide, izlemek istediğiniz uyarılar hakkında bilgi sağlayın. Daha fazla özellik için **yeni parametre Ekle** listesini açın ve bu özelliği tetikleyiciye eklemek için bir parametre seçin.

   | Özellik | Özellik (JSON) | Gerekli | Tür | Description |
   |----------|-----------------|----------|------|-------------|
   | **Aralık** | `interval` | Yes | Tamsayı | İş akışının sıklık temelinde ne sıklıkta çalışacağını açıklayan pozitif bir tamsayı. En düşük ve en büyük aralıklar aşağıda verilmiştir: <p><p>-Ay: 1-16 ay <br>Gün: 1-500 gün <br>-Saat: 1-12000 saat <br>-Dakika: 1-72000 dakika <br>-İkinci: 1-9999999 saniye <p>Örneğin, Aralık 6 ve Sıklık "month" ise, yinelenme 6 aydır. |
   | **Sıklık** | `frequency` | Evet | Dize | Yinelenme için zaman birimi: **saniye**, **dakika**, **saat**, **gün**, **hafta** veya **ay** |
   | **Saat dilimi** | `timeZone` | Hayır | Dize | Yalnızca bir başlangıç saati belirttiğinizde geçerlidir çünkü bu tetikleyici [UTC sapmasını](https://en.wikipedia.org/wiki/UTC_offset)kabul etmez. Uygulamak istediğiniz saat dilimini seçin. |
   | **Başlangıç zamanı** | `startTime` | Hayır | Dize | Bu biçimde bir başlangıç tarihi ve saati belirtin: <p><p>YYYY-MM-DDThh: mm: ss saat dilimi seçerseniz <p>-veya- <p>YYYY-MM-DDThh: mm: ssZ saat dilimi seçme <p>Örneğin, 18 Eylül 2017 ile 2:00 PM arasında bir süre istiyorsanız "2017-09-18T14:00:00" belirtin ve Pasifik standart saati gibi bir saat dilimi seçin. Ya da saat dilimi olmadan "2017-09-18T14:00:00Z" belirtin. <p>**Note:** Bu başlangıç saati, gelecekte en fazla 49 yıla sahiptir ve UTC [8601 tarih saat belirtimini](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) UTC [Tarih saat biçiminde](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)izlemelidir, ancak [UTC bir fark](https://en.wikipedia.org/wiki/UTC_offset)olmadan gelmelidir. Bir saat dilimi seçmezseniz, sonunda boşluk olmadan "Z" harfini eklemeniz gerekir. Bu "Z", eşdeğer [nadeniz saati](https://en.wikipedia.org/wiki/Nautical_time)anlamına gelir. <p>Basit zamanlamalar için başlangıç zamanı ilk oluşumdır, ancak karmaşık zamanlamalar için tetikleyici başlangıç zamanından daha önce harekete geçmez. [*Başlangıç tarihini ve saatini kullanmanın yolları nelerdir?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

1.  Şimdi, tetikleyici sonuçlarıyla gerçekleştirmek istediğiniz görevler için mantıksal uygulamanıza bir veya daha fazla eylem eklemeye devam edin.

## <a name="add-actions"></a>Eylem ekleme

Microsoft Graph güvenlik Bağlayıcısı ile kullanılabilen çeşitli eylemleri kullanma hakkında daha ayrıntılı bilgiler aşağıda verilmiştir.

### <a name="manage-alerts"></a>Uyarıları yönetme

En son sonuçları filtrelemek, sıralamak veya almak için *yalnızca* [Microsoft Graph tarafından desteklenen OData sorgu parametrelerini](/graph/query-parameters)sağlayın.  Tüm temel URL 'YI veya http eylemini (örneğin, ya da `https://graph.microsoft.com/v1.0/security/alerts` `GET` veya işlem) belirtmeyin `PATCH` . Yüksek önem derecesine sahip bir liste istediğinizde, **uyarıları al** eyleminin parametrelerini gösteren belirli bir örnek aşağıda verilmiştir:

`Filter alerts value as Severity eq 'high'`

Bu bağlayıcı ile kullanabileceğiniz sorgular hakkında daha fazla bilgi için [Microsoft Graph güvenlik uyarıları başvuru belgelerine](/graph/api/alert-list)bakın. Bu bağlayıcıyla ilgili gelişmiş deneyimler oluşturmak için bağlayıcının desteklediği [şema özellikleri uyarıları](/graph/api/resources/alert) hakkında daha fazla bilgi edinin.

| Eylem | Açıklama |
|--------|-------------|
| **Uyarıları al** | Bir veya daha fazla [Uyarı özelliklerine](/graph/api/resources/alert)göre filtrelenmiş uyarıları alın, örneğin, `Provider eq 'Azure Security Center' or 'Palo Alto Networks'` . | 
| **KIMLIĞE göre uyarı al** | Uyarı KIMLIğINE bağlı olarak belirli bir uyarı alın. | 
| **Uyarıyı Güncelleştir** | Uyarı KIMLIĞINE göre belirli bir uyarıyı güncelleştirin. İsteğinize gerekli ve düzenlenebilir özellikleri geçirdiğinizden emin olmak için, [Uyarılar için düzenlenebilir özelliklere](/graph/api/alert-update)bakın. Örneğin, araştırabilmeleri için bir güvenlik analiste uyarı atamak üzere uyarının **atanmış** özelliğini güncelleştirebilirsiniz. |
|||

### <a name="manage-alert-subscriptions"></a>Uyarı aboneliklerini yönetme

Microsoft Graph [*abonelikleri*](/graph/api/resources/subscription)veya [*Web kancalarını*](/graph/api/resources/webhooks)destekler. Abonelikleri almak, güncelleştirmek veya silmek için [Microsoft Graph tarafından desteklenen OData sorgu parametrelerini](/graph/query-parameters) Microsoft Graph varlık yapısına sağlayın ve `security/alerts` sonra OData sorgusunun ardından ekleyin. Temel URL 'yi (örneğin,) *eklemeyin* `https://graph.microsoft.com/v1.0` . Bunun yerine, şu örnekteki biçimi kullanın:

`security/alerts?$filter=status eq 'NewAlert'`

| Eylem | Açıklama |
|--------|-------------|
| **Abonelik oluşturma** | Herhangi bir değişiklik hakkında size bildirimde bulunan [bir abonelik oluşturun](/graph/api/subscription-post-subscriptions) . İstediğiniz belirli uyarı türleri için bu aboneliği filtreleyebilirsiniz. Örneğin, size yüksek önem derecesi uyarıları bildiren bir abonelik oluşturabilirsiniz. |
| **Etkin abonelikleri al** | Süre [dolmamış abonelikler alın](/graph/api/subscription-list). | 
| **Aboneliği Güncelleştir** | Abonelik KIMLIĞINI sağlayarak [bir aboneliği güncelleştirin](/graph/api/subscription-update) . Örneğin, aboneliğinizi genişletmek için aboneliğin `expirationDateTime` özelliğini güncelleştirebilirsiniz. | 
| **Aboneliği silme** | Abonelik KIMLIĞINI sağlayarak [bir aboneliği silin](/graph/api/subscription-delete) . | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Tehdit zekası göstergelerini yönetme

En son sonuçları filtrelemek, sıralamak veya almak için *yalnızca* [Microsoft Graph tarafından desteklenen OData sorgu parametrelerini](/graph/query-parameters)sağlayın.  Tüm temel URL 'YI veya http eylemini (örneğin, ya da `https://graph.microsoft.com/beta/security/tiIndicators` `GET` veya işlem) belirtmeyin `PATCH` . Tehdit türüne sahip bir listeyi istediğiniz zaman **Al** eyleminin parametrelerini gösteren belirli bir örnek aşağıda verilmiştir `DDoS` :

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Bu bağlayıcı ile kullanabileceğiniz sorgular hakkında daha fazla bilgi için, [Microsoft Graph güvenlik tehdit bilgileri gösterge başvurusu belgelerindeki "Isteğe bağlı sorgu parametreleri"](/graph/api/tiindicators-list)başlığına bakın. Bu bağlayıcıyla ilgili gelişmiş deneyimler oluşturmak için bağlayıcının desteklediği [şema özellikleri tehdit bilgileri göstergesi](/graph/api/resources/tiindicator) hakkında daha fazla bilgi edinin.

| Eylem | Açıklama |
|--------|-------------|
| **Tehdit zekası göstergelerini al** | Örneğin, bir veya daha fazla [Tiındicator özelliği](/graph/api/resources/tiindicator)temelinde filtrelenmiş tigöstergelerini al `threatType eq 'MaliciousUrl' or 'DDoS'` |
| **KIMLIĞE göre tehdit zekası göstergesi al** | Tiındicator KIMLIĞI temelinde belirli bir Tiındicator alın. | 
| **Tehdit zekası göstergesi oluşturma** | Tiındicators koleksiyonuna göndererek yeni bir Tiındicator oluşturun. İsteğinize gerekli özellikleri geçirdiğinizden emin olmak için, [Tiındicator oluşturmak için gereken özelliklere](/graph/api/tiindicators-post)bakın. |
| **Birden çok tehdit zekasının göstergelerini gönder** | Bir Tiındicators koleksiyonu naklederek birden çok yeni Tiındicators oluşturun. İsteğinize gerekli özellikleri geçirdiğinizden emin olmak için, [birden çok Tiındicators göndermek için gereken özelliklere](/graph/api/tiindicator-submittiindicators)bakın. |
| **Tehdit zekası göstergesini Güncelleştir** | Tiındicator KIMLIĞI temelinde belirli bir Tiındicator 'ı güncelleştirin. İsteğinize gerekli ve düzenlenebilir özellikleri geçirdiğinizden emin olmak için, [Tiındicator için düzenlenebilir özelliklere](/graph/api/tiindicator-update)bakın. Örneğin, göstergenin targetProduct güvenlik aracından eşleşmesi durumunda uygulanacak eylemi güncelleştirmek için, Tiındicator 'ın **Action** özelliğini güncelleştirebilirsiniz. |
| **Birden çok tehdit zekası göstergelerini güncelleştirme** | Birden çok Tiındicators 'ı güncelleştirin. İsteğinize gerekli özellikleri geçirdiğinizden emin olmak için, [birden çok Tiındicators 'ı güncelleştirmek için gereken özelliklere](/graph/api/tiindicator-updatetiindicators)bakın. |
| **Tehdit zekası göstergesini KIMLIĞE göre Sil** | Tiındicator KIMLIĞI temelinde belirli bir Tiındicator öğesini silin. |
| **Kimliklere göre birden çok tehdit zekası göstergelerini silme** | Birden çok Tiındicators kimliklerini kimliklerine göre silin. İsteğinize gerekli özellikleri geçirdiğinizden emin olmak için, [birden çok Tiındicators 'ı kimliklere göre silmek için gerekli özelliklere](/graph/api/tiindicator-deletetiindicators)bakın. |
| **Çoklu tehdit zekası göstergelerini dış kimliklere göre Sil** | Dış kimliklere göre birden çok Tiındicators silin. İsteğinize gerekli özellikleri geçirdiğinizden emin olmak için, [dış kimliklere göre birden çok Tiındicators silmek için gereken özelliklere](/graph/api/tiindicator-deletetiindicatorsbyexternalid)bakın. |
|||

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/microsoftgraphsecurity/)gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
