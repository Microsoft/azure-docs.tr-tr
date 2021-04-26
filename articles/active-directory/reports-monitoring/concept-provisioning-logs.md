---
title: Azure portal (Önizleme) içindeki sağlama günlüklerine genel bakış | Microsoft Docs
description: Azure Active Directory Azure portal aracılığıyla günlük raporlarının sağlanmasına yönelik bir giriş alın.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 468e885bab6aab4becb5aaaec7b4d52ce5ef5e07
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535986"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Azure portal sağlama günlüklerine genel bakış (Önizleme)

Azure Active Directory (Azure AD) içindeki raporlama mimarisi aşağıdaki bileşenlerden oluşur:

- Etkinlik: 
    - **Oturum açma** işlemleri: yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinlikleri hakkında bilgiler.
    - [Denetim günlükleri](concept-audit-logs.md): Kullanıcı ve Grup Yönetimi, yönetilen uygulamalar ve Dizin etkinlikleri hakkında sistem etkinliği bilgileri.
    - **Sağlama günlükleri**: Azure AD sağlama hizmeti tarafından sağlanan kullanıcılar, gruplar ve roller hakkında sistem etkinliği. 

- Güven 
    - **Riskli oturum açma** işlemleri: [riskli oturum](../identity-protection/overview-identity-protection.md) açma, bir kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilmiş olabilecek oturum açma girişimine yönelik bir göstergedir.
    - **Risk için Işaretlenen kullanıcılar**: [riskli bir Kullanıcı](../identity-protection/overview-identity-protection.md) , tehlikeye girmiş olabilecek bir kullanıcı hesabı göstergesidir.

Bu konu, sağlama günlüklerine genel bir bakış sağlar. Günlükler, şu gibi soruların yanıtlarını sağlar: 

* ServiceNow 'da hangi gruplar başarıyla oluşturuldu?
* Hangi kullanıcılar Adobe 'dan başarıyla kaldırıldı?
* Workday 'den hangi kullanıcıların Active Directory başarıyla oluşturulduğu? 

## <a name="prerequisites"></a>Önkoşullar

Bu kullanıcılar sağlama günlüklerindeki verilere erişebilir:

* Uygulama sahipleri (kendi uygulamalarına ait Günlükler)
* Güvenlik Yöneticisi, güvenlik okuyucu, rapor okuyucu, güvenlik operatörü, uygulama Yöneticisi ve bulut uygulaması yönetici rolleri kullanıcıları
* [Provisioninglogs iznine](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions) sahip özel bir roldeki kullanıcılar
* Genel yöneticiler


Sağlama etkinliği raporunu görüntülemeniz için kiracınızın ilişkili bir Azure AD Premium lisansı olması gerekir. Azure AD sürümünüzü yükseltmek için bkz. [Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md)kullanmaya başlama. 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Sağlama günlükleriyle etkileşim kurma yolları 
Müşteriler, sağlama günlükleriyle dört şekilde etkileşim kurabilir:

- Sonraki bölümde açıklandığı gibi Azure portal günlüklere erişim.
- Sağlama günlüklerinin [Azure izleyici](../app-provisioning/application-provisioning-log-analytics.md)'ye akışı. Bu yöntem, genişletilmiş veri saklama ve özel panolar, uyarılar ve sorgular oluşturmak için izin verir.
- Sağlama günlükleri için [MICROSOFT Graph API](/graph/api/resources/provisioningobjectsummary) sorgulanıyor.
- Sağlama günlükleri bir CSV veya JSON dosyası olarak indiriliyor.

## <a name="access-the-logs-from-the-azure-portal"></a>Azure portal günlüklere erişin
Sağlama günlüklerine, [Azure portal](https://portal.azure.com) **Azure Active Directory** bölmesinin **izleme** bölümünde **sağlama günlükleri** ' ni seçerek erişebilirsiniz. Bazı sağlama kayıtlarının portalda görünmesi iki saate kadar sürebilir.

![Sağlama günlüklerine erişim seçimlerini gösteren ekran görüntüsü.](./media/concept-provisioning-logs/access-provisioning-logs.png "Sağlama günlükleri")


Bir sağlama günlüğü, şunu gösteren bir varsayılan liste görünümüne sahiptir:

- Kimlik
- Eylem
- Kaynak sistem
- Hedef sistem
- Durum
- Tarih


![Bir sağlama günlüğündeki varsayılan sütunları gösteren ekran görüntüsü.](./media/concept-provisioning-logs/default-columns.png "Varsayılan sütunlar")

Araç çubuğundaki **sütunları** seçerek liste görünümünü özelleştirebilirsiniz.

![Sütunları özelleştirmek için düğmeyi gösteren ekran görüntüsü.](./media/concept-provisioning-logs/column-chooser.png "Sütun seçici")

Bu alan, ek alanları görüntülemenizi veya zaten görüntülenen alanları kaldırmanızı sağlar.

![Bazıları seçili olan kullanılabilir sütunları gösteren ekran görüntüsü.](./media/concept-provisioning-logs/available-columns.png "Kullanılabilir sütunlar")

Daha ayrıntılı bilgi edinmek için liste görünümünde bir öğe seçin.

![Ayrıntılı bilgileri gösteren ekran görüntüsü.](./media/concept-provisioning-logs/steps.png "Filtre")


## <a name="filter-provisioning-activities"></a>Sağlama etkinliklerini filtrele

Sağlama verilerinizi filtreleyebilirsiniz. Bazı filtre değerleri, kiracınıza göre dinamik olarak doldurulur. Örneğin, kiracınızda hiçbir "oluşturma" olayı yoksa bir filtre **Oluştur** seçeneği olmayacaktır.

Varsayılan görünümde, aşağıdaki filtreleri seçebilirsiniz:

- **Kimlik**
- **Date**
- **Durum**
- **Eylem**


![Filtre değerlerini gösteren ekran görüntüsü.](./media/concept-provisioning-logs/default-filter.png "Filtre")

**Kimlik** filtresi, ilgilendiğiniz adı veya kimliği belirtmenize olanak sağlar. Bu kimlik bir Kullanıcı, Grup, rol ya da başka bir nesne olabilir. 

Nesnenin adına veya KIMLIğINE göre arama yapabilirsiniz. KIMLIK senaryoya göre farklılık gösterir. Örneğin, Azure AD 'den Salesforce 'a bir nesne sağlıyorsanız kaynak KIMLIĞI, kullanıcının Azure AD 'deki nesne KIMLIĞIDIR. Hedef KIMLIĞI, Salesforce 'daki kullanıcının KIMLIĞIDIR. Workday 'den Active Directory 'e kaynak KIMLIĞI, iş günü çalışan çalışan KIMLIĞI olur. 

> [!NOTE]
> Kullanıcının adı, **kimlik** sütununda her zaman bulunmayabilir. Her zaman bir KIMLIK olur. 


**Tarih** filtresi, döndürülen veriler için bir zaman çerçevesi tanımlamanıza olanak sağlar. Olası değerler şunlardır:

- 1 ay
- 7 gün
- 30 gün
- 24 saat
- Özel zaman aralığı

Özel bir zaman çerçevesini seçtiğinizde, bir başlangıç tarihi ve bitiş tarihi yapılandırabilirsiniz.

**Durum** filtresi şunları seçmenizi sağlar:

- **Tümü**
- **Başarılı**
- **Hata**
- **Atlandı**

**Eylem** filtresi, bu eylemleri filtrelemenizi sağlar:

- **Oluştur** 
- **Güncelleştirme**
- **Silme**
- **Devre Dışı Bırak**
- **Diğer**

Varsayılan görünümün filtrelerine ek olarak, aşağıdaki filtreleri ayarlayabilirsiniz.

![Filtre olarak ekleyebileceğiniz alanları gösteren ekran görüntüsü.](./media/concept-provisioning-logs/add-filter.png "Bir alan seçin")

- **Iş kimliği**: benzersiz BIR iş kimliği, sağlamayı etkinleştirdiğiniz her uygulamayla ilişkilendirilir.   

- **GEÇIŞ kimliği**: geçiş kimliği, sağlama döngüsünü benzersiz şekilde tanımlar. Bu olayın gerçekleştiği döngüyü aramak için bu KIMLIĞI ürün desteğiyle paylaşabilirsiniz.

- **DEĞIŞIKLIK kimliği**: değişiklik kimliği, sağlama olayının benzersiz tanımlayıcısıdır. Bu KIMLIĞI, sağlama olayını aramak için ürün desteğiyle paylaşabilirsiniz.   

- **Kaynak sistem**: kimliğin nereden sağlandığını belirtebilirsiniz. Örneğin, Azure AD 'den ServiceNow 'a bir nesne sağlıyorsanız, kaynak sistem Azure AD olur. 

- **Hedef sistem**: kimliğin nerede sağlandığını belirtebilirsiniz. Örneğin, Azure AD 'den ServiceNow 'a bir nesne sağlıyorsanız, hedef sistem ServiceNow olur. 

- **Uygulama**: yalnızca belirli bir dizeyi içeren bir görünen ada sahip uygulama kayıtlarını gösterebilirsiniz.

## <a name="provisioning-details"></a>Sağlama ayrıntıları 

Sağlama listesi görünümünde bir öğe seçtiğinizde, bu öğe hakkında daha fazla ayrıntı alırsınız. Ayrıntılar aşağıdaki sekmelerde gruplandırılmıştır.

![Sağlama ayrıntılarını içeren dört sekme gösteren ekran görüntüsü.](./media/concept-provisioning-logs/provisioning-tabs.png "Sekmeler")

- **Adımlar**: bir nesne sağlamak için kullanılan adımları özetler. Bir nesnenin sağlanması dört adımdan oluşabilir:
  
  1. Nesneyi içeri aktarın.
  1. Nesnenin kapsam içinde olup olmadığını belirleme.
  1. Kaynak ve hedef arasında nesneyi eşleştirin.
  1. Nesneyi sağlayın (oluşturma, güncelleştirme, silme veya devre dışı bırakma).

  ![Ekran görüntüsü, adımlar sekmesindeki sağlama adımlarını gösterir.](./media/concept-provisioning-logs/steps.png "Filtre")

- **& önerileri sorunlarını giderme**: hata kodunu ve nedenini sağlar. Hata bilgileri yalnızca bir hata oluşursa kullanılabilir.

- **Değiştirilen özellikler**: eski değeri ve yeni değeri gösterir. Eski değer yoksa, bu sütun boştur.

- **Özet**: kaynak ve hedef sistemlerdeki nesne için ne olduğuna ve tanımlayıcılara bir genel bakış sağlar.

## <a name="download-logs-as-csv-or-json"></a>Günlükleri CSV veya JSON olarak indir

Daha sonra kullanmak üzere Azure portal günlüklere giderek ve **İndir**' i seçerek sağlama günlüklerini indirebilirsiniz. Dosya, seçtiğiniz filtre ölçütlerine göre filtrelenecektir. İndirmenin boyutunu ve süresini azaltmak için filtreleri mümkün olduğunca özel yapın. 

CSV indirmesi üç dosya içerir:

* **Provisioninglogs**: sağlama adımları ve değiştirilen özellikler dışında tüm günlükleri indirir.
* **ProvisioningLogs_ProvisioningSteps**: sağlama adımlarını ve değişiklik kimliğini içerir. Olayı diğer iki dosya ile birleştirmek için değişiklik KIMLIĞINI kullanabilirsiniz.
* **ProvisioningLogs_ModifiedProperties**: değiştirilen öznitelikleri ve değişiklik kimliğini içerir. Olayı diğer iki dosya ile birleştirmek için değişiklik KIMLIĞINI kullanabilirsiniz.

#### <a name="open-the-json-file"></a>JSON dosyasını açın
JSON dosyasını açmak için [Microsoft Visual Studio kod](https://aka.ms/vscode)gibi bir metin düzenleyicisi kullanın. Visual Studio Code, söz dizimi vurgulaması sağlayarak dosyayı daha kolay okunabilir hale getirir. Ayrıca, tarayıcı kullanarak [Microsoft Edge](https://aka.ms/msedge)gibi düzenlenemeyen BIR biçimde json dosyasını da açabilirsiniz. 

#### <a name="prettify-the-json-file"></a>JSON dosyasını önceden yapılandırma
Yükleme boyutunu azaltmak için JSON dosyası küçültülmüş biçimde indirilir. Bu biçim, yükü daha zor hale getirir. Dosyayı önceden tanımlamak için iki seçeneğe göz atın:

- [JSON biçimlendirmek için Visual Studio Code](https://code.visualstudio.com/docs/languages/json#_formatting)kullanın.

- JSON 'yi biçimlendirmek için PowerShell 'i kullanın. Bu betik, JSON 'ı sekmeler ve boşluklar içeren bir biçimde çıktısını alır: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>JSON dosyasını Ayrıştır

PowerShell kullanarak JSON dosyasıyla çalışmak için bazı örnek komutlar aşağıda verilmiştir. Rahat olduğunuz herhangi bir programlama dilini kullanabilirsiniz.  

İlk olarak, şu komutu çalıştırarak [json dosyasını okuyun](/powershell/module/microsoft.powershell.utility/convertfrom-json) :

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

Şimdi, senaryonuza göre verileri ayrıştırabilirsiniz. Aşağıda birkaç örnek verilmiştir: 

- JSON dosyasındaki tüm iş kimliklerini çıkar:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Eylemin "Oluştur" olduğu olaylar için tüm değişiklik kimliklerini çıkar:

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Bilmeniz gerekenler

Raporları sağlamaya yönelik bazı ipuçları ve konular aşağıda verilmiştir:

- Azure portal, bir Premium sürümünüz varsa ve ücretsiz sürümünüz varsa 7 gün sonra, bildirilen sağlama verilerini 30 gün boyunca depolar. Sağlama günlüklerini 30 günden daha fazla bekletme için [Log Analytics](../app-provisioning/application-provisioning-log-analytics.md) yayımlayabilirsiniz. 

- Değişiklik KIMLIĞI özniteliğini benzersiz tanımlayıcı olarak kullanabilirsiniz. Bu, örneğin ürün desteğiyle etkileşim kurarken yararlı olur.

- Kapsamda olmayan kullanıcılar için Atlanan olayları görebilirsiniz. Özellikle eşitleme kapsamı tüm kullanıcılar ve gruplar olarak ayarlandığında bu beklenmektedir. Hizmet, Kiracıdaki tüm nesneleri, kapsam dışı olsalar bile değerlendirir. 

- Sağlama günlükleri Şu anda kamu bulutu 'nda kullanılamıyor. Sağlama günlüklerine erişemiyorsanız, geçici bir geçici çözüm olarak denetim günlüklerini kullanın. 

- Sağlama günlükleri rol içeri aktarmaları göstermez (AWS, Salesforce ve Zendesk için geçerlidir). Rol içeri aktarmaları için günlükleri denetim günlüklerinde bulabilirsiniz. 

## <a name="error-codes"></a>Hata kodları

Sağlama günlüklerinde bulduğunuz hataların nasıl çözümleneceğini daha iyi anlamak için aşağıdaki tabloyu kullanın. Eksik olan herhangi bir hata kodu için, bu sayfanın en altındaki bağlantıyı kullanarak geri bildirim sağlayın. 

|Hata kodu|Açıklama|
|---|---|
|Çakışma, EntryConflict|Çakışan öznitelik değerlerini Azure AD 'de veya uygulamada düzeltin. Ya da çakışan Kullanıcı hesabının eşleştirilmek ve üzerinde alınması gerekiyorsa, eşleşen öznitelik yapılandırmanızı gözden geçirin. Eşleşen öznitelikleri yapılandırma hakkında daha fazla bilgi için [belgeleri](../app-provisioning/customize-application-attributes.md) gözden geçirin.|
|TooManyRequests|Hedef uygulama, aşırı yüklenmiş ve çok fazla istek aldığından kullanıcıyı güncelleştirme girişimini reddetti. Yapılacak bir şey yok. Bu deneme otomatik olarak kullanımdan kaldırılacak. Microsoft bu sorunla aynı zamanda bilgilendirildi.|
|InternalServerError |Hedef uygulama beklenmeyen bir hata döndürdü. Hedef uygulamayla bir hizmet sorunu, bunun çalışmasını engelleyebilir. Bu deneme, 40 dakika içinde otomatik olarak kullanımdan kaldırılacaktır.|
|InsufficientRights, MethodNotAllowed, NotAllowed, yetkisiz| Azure AD, hedef uygulamayla doğrulandı, ancak güncelleştirmeyi gerçekleştirme yetkisi yoktu. Hedef uygulamanın sunduğu tüm yönergeleri, ilgili uygulama [öğreticisiyle](../saas-apps/tutorial-list.md)birlikte gözden geçirin.|
|UnprocessableEntity|Hedef uygulama beklenmeyen bir yanıt döndürdü. Hedef uygulamanın yapılandırması doğru olmayabilir veya hedef uygulamayla bir hizmet sorunu bunun çalışmasını engelleyebilir.|
|WebExceptionProtocolError |Hedef uygulamaya bağlanılırken bir HTTP protokol hatası oluştu. Yapılacak bir şey yok. Bu deneme, 40 dakika içinde otomatik olarak kullanımdan kaldırılacaktır.|
|Invalidanchor|Sağlama hizmeti tarafından daha önce oluşturulmuş veya eşleştirilmiş bir Kullanıcı artık yok. Kullanıcının var olduğundan emin olun. Tüm kullanıcılarla yeni bir eşleştirmeye zorlamak için Microsoft Graph API 'sini kullanarak [işi yeniden başlatın](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). <br><br>Sağlama işleminin yeniden başlatılması, tamamlanması zaman alabilir ve bir başlangıç döngüsünü tetikler. Sağlama yeniden başlatıldığında sağlama hizmeti 'nin çalışmak için kullandığı önbellek de silinir. Diğer bir deyişle, Kiracıdaki tüm kullanıcılar ve grupların yeniden değerlendirilmesi ve belirli sağlama olaylarının bırakılması gerekir.|
|NotImplemented | Hedef uygulama beklenmeyen bir yanıt döndürdü. Uygulamanın yapılandırması doğru olmayabilir veya hedef uygulamayla bir hizmet sorunu bunun çalışmasını engelleyebilir. Hedef uygulamanın sunduğu tüm yönergeleri, ilgili uygulama [öğreticisiyle](../saas-apps/tutorial-list.md)birlikte gözden geçirin. |
|MandatoryFieldsMissing, MissingValues |Gerekli değerler eksik olduğundan kullanıcı oluşturulamadı. Kaynak kayıttaki eksik öznitelik değerlerini düzeltin veya gerekli alanların atlanmadığından emin olmak için eşleşen öznitelik yapılandırmanızı gözden geçirin. Eşleşen öznitelikleri yapılandırma hakkında [daha fazla bilgi edinin](../app-provisioning/customize-application-attributes.md) .|
|SchemaAttributeNotFound |Hedef uygulamada mevcut olmayan bir öznitelik belirtildiğinden işlem gerçekleştirilemedi. Öznitelik özelleştirme hakkındaki [belgelere](../app-provisioning/customize-application-attributes.md) bakın ve yapılandırmanızın doğru olduğundan emin olun.|
|InternalError |Azure AD sağlama hizmeti içinde bir iç hizmet hatası oluştu. Yapılacak bir şey yok. Bu deneme 40 dakika içinde otomatik olarak yeniden denenecek.|
|Invaliddomain |Öznitelik değeri geçersiz bir etki alanı adı içerdiğinden işlem gerçekleştirilemedi. Kullanıcının etki alanı adını güncelleştirin veya hedef uygulamadaki izin verilen listeye ekleyin. |
|Zaman aşımı |Hedef uygulamanın yanıt vermesi çok uzun sürdüğü için işlem tamamlanamadı. Yapılacak bir şey yok. Bu deneme 40 dakika içinde otomatik olarak yeniden denenecek.|
|Licenselimitexceıbaşında|Bu Kullanıcı için kullanılabilir lisans olmadığından, Kullanıcı hedef uygulamada oluşturulamadı. Hedef uygulama için daha fazla lisans temin edin. Ya da doğru özniteliklerle doğru kullanıcıların atandığından emin olmak için Kullanıcı atamalarınızı ve öznitelik eşleme yapılandırmanızı gözden geçirin.|
|DuplicateTargetEntries  |Hedef uygulamada yapılandırılmış eşleşen özniteliklerle birden fazla kullanıcı bulunduğundan işlem tamamlanamadı. Hedef uygulamadan yinelenen kullanıcıyı kaldırın veya [öznitelik eşlemelerinizi yeniden yapılandırın](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Yapılandırılmış eşleşen özniteliklerle birden fazla kullanıcı bulunduğundan işlem tamamlanamadı. Yinelenen kullanıcıyı kaldırın veya [öznitelik eşlemelerinizi yeniden yapılandırın](../app-provisioning/customize-application-attributes.md).|
|Importatlandı | Her kullanıcı değerlendirildiğinde, sistem, kullanıcıyı kaynak sistemden içeri aktarmaya çalışır. Bu hata genellikle içeri aktarılmakta olan kullanıcının öznitelik eşlemelerinizde tanımlanan eşleşen özelliği eksik olduğu durumlarda oluşur. Eşleşen öznitelik için Kullanıcı nesnesinde bir değer olmadan, sistem kapsamları, eşleştirmeyi veya dışarı aktarma değişikliklerini değerlendiremez. Bu hatanın varlığı kullanıcının kapsamda olduğunu belirtmediğinden, henüz Kullanıcı için kapsamları değerlendirmemiş olmanız gerektiğini unutmayın.|
|Entrysynchronizationatlandı | Sağlama Hizmeti, kaynak sistemi başarıyla sorguladı ve kullanıcıyı tanımladı. Kullanıcı üzerinde başka bir eylem yapılmadı ve bunlar atlandı. Kullanıcı kapsam dışında bırakılmış olabilir veya daha fazla değişiklik yapmadan Kullanıcı hedef sistemde zaten var olabilir.|
|Systemforcrossdomainıdentitymanagementmultipleentriesınresponse| Bir kullanıcıyı veya grubu alma isteği, yanıtta birden çok kullanıcı veya grup aldı. Sistem yanıtta yalnızca bir kullanıcı veya grup almayı bekliyor. [Örneğin](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group), bir grubu almak ve üyeleri hariç tutmak için bir filtre sağlamak üzere bir get isteği yaptıysanız ve etki alanları arası kimlik yönetimi (SCIM) uç noktası, üyeleri geri döndürürse, bu hatayı alırsınız.|

## <a name="next-steps"></a>Sonraki adımlar

* [Kullanıcı hazırlama durumunu denetleme](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Azure AD Galeri uygulamasına kullanıcı sağlamayı yapılandırma sorunu](../app-provisioning/application-provisioning-config-problem.md)
* [Sağlama günlükleri için Graph API](/graph/api/resources/provisioningobjectsummary)