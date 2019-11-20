---
title: Azure Active Directory portalında günlükleri sağlama (Önizleme) | Microsoft Docs
description: Azure Active Directory portalında etkinlik raporlarını sağlama konusuna giriş
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
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6e0c697f9ab9796feade9b4d5c2a64794f3980b
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612790"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Azure Active Directory portalında raporları sağlama (Önizleme)

Azure Active Directory (Azure AD) içindeki raporlama mimarisi aşağıdaki bileşenlerden oluşur:

- **Etkinlik** 
    - **Oturum açma** bilgileri – yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinlikleri hakkında bilgiler.
    - **Denetim günlükleri - denetim** [günlükleri](concept-audit-logs.md) , kullanıcılar ve Grup Yönetimi, yönetilen uygulamalar ve Dizin etkinlikleri hakkında sistem etkinliği bilgilerini sağlar.
    - **Günlükleri sağlama** -Azure AD sağlama hizmeti tarafından sağlanan Kullanıcı, Grup ve roller hakkında sistem etkinliği sağlar. 

- **Güvenlik** 
    - **Riskli oturum** açma işlemleri- [riskli oturum](concept-risky-sign-ins.md) açma, bir kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilmiş olabilecek oturum açma girişimine yönelik bir göstergedir.
    - **Risk için Işaretlenen kullanıcılar** - [riskli bir Kullanıcı](concept-user-at-risk.md) , tehlikeye girmiş olabilecek bir kullanıcı hesabı göstergesidir.

Bu konu, sağlama raporuna genel bir bakış sağlar.

## <a name="prerequisites"></a>Ön koşullar

### <a name="who-can-access-the-data"></a>Verilere kimler erişebilir?
* Güvenlik Yöneticisi, güvenlik okuyucu, rapor okuyucu, uygulama Yöneticisi ve bulut uygulaması yönetici rolleri kullanıcıları
* Genel Yöneticiler


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Sağlama etkinliklerine erişmek için hangi Azure AD lisansınızın olması gerekir?

Tüm sağlama etkinliği raporunu görmek için kiracınızın kendisiyle ilişkili bir Azure AD Premium lisansı olması gerekir. Azure Active Directory sürümünüzü yükseltmek için bkz. [Azure Active Directory Premium kullanmaya](../fundamentals/active-directory-get-started-premium.md) başlama. 

## <a name="provisioning-logs"></a>Sağlama günlükleri

Sağlama günlükleri aşağıdaki soruların yanıtlarını sağlar:

* ServiceNow 'da hangi gruplar başarıyla oluşturuldu?
* Roller Amazon Web Services içeri aktarılsın mı?
* DropBox 'ta başarısız olan kullanıcılar nelerdir?

Sağlama günlüklerine, [Azure portal](https://portal.azure.com) **Azure Active Directory** dikey penceresinin **izleme** bölümünde **sağlama günlükleri** ' ni seçerek erişebilirsiniz. Bazı sağlama kayıtlarının portalda gösterilmesi iki saate kadar sürebilir.

![Sağlama günlükleri](./media/concept-provisioning-logs/access-provisioning-logs.png "Sağlama günlükleri")


Bir sağlama günlüğü, şunu gösteren bir varsayılan liste görünümüne sahiptir:

- Kimlik
- Eylem
- Kaynak sistem
- Hedef sistem
- Durum
- Tarih


![Varsayılan sütunlar](./media/concept-provisioning-logs/default-columns.png "Varsayılan sütunlar")

Araç çubuğunda **Sütunlar**’a tıklayarak liste görünümünü özelleştirebilirsiniz.

![Sütun seçici](./media/concept-provisioning-logs/column-chooser.png "Sütun seçici")

Bu sayede ek alanları görüntüleyebilir ya da zaten görüntülenen alanları kaldırabilirsiniz.

![Kullanılabilir sütunlar](./media/concept-provisioning-logs/available-columns.png "Kullanılabilir sütunlar")

Daha ayrıntılı bilgi edinmek için liste görünümünde bir öğe seçin.

![Ayrıntılı bilgiler](./media/concept-provisioning-logs/steps.png "Filtre")


## <a name="filter-provisioning-activities"></a>Sağlama etkinliklerini filtrele

Bildirilen verileri sizin için uygun bir düzeye daraltmak için, sağlama verilerini aşağıdaki varsayılan alanları kullanarak filtreleyebilirsiniz. Filtrelerdeki değerlerin kiracınıza göre dinamik olarak doldurulduğunu unutmayın. Örneğin, kiracınızda herhangi bir oluşturma olayınız yoksa, Create için bir filtre seçeneği olmayacaktır.

- Kimlik
- Eylem
- Kaynak sistem
- Hedef sistem
- Durum
- Tarih


![Filtreyle](./media/concept-provisioning-logs/filter.png "Filtre")

**Kimlik** filtresi, ilgilendiğiniz adı veya kimliği belirtmenize olanak sağlar. Bu kimlik bir Kullanıcı, Grup, rol ya da başka bir nesne olabilir. Nesnenin adına veya KIMLIğINE göre arama yapabilirsiniz. KIMLIK senaryoya göre farklılık gösterir. Örneğin, Azure AD 'den SalesForce 'a bir nesne sağlarken, kaynak KIMLIĞI Azure AD 'deki kullanıcının nesne KIMLIĞIDIR, ancak targetID, Salesforce 'daki kullanıcının KIMLIĞIDIR. Workday 'den Active Directory 'e sağlanırken kaynak KIMLIĞI iş günü çalışan çalışan KIMLIĞIDIR. Kullanıcı adının Kimlik sütununda her zaman mevcut olamayacağını unutmayın. Her zaman bir KIMLIK olur. 

**Kaynak sistem** filtresi, kimliğin nereden sağlandığını belirtmenize olanak sağlar. Örneğin, Azure AD 'den ServiceNow 'a bir nesne sağlanırken kaynak sistem Azure AD ' dir. 

**Hedef sistem** filtresi, kimliğin sağlandığı yeri belirtmenize olanak sağlar. Örneğin, Azure AD 'den ServiceNow 'a bir nesne sağlanırken, hedef sistem ServiceNow olur. 

**Durum** filtresi şunları seçmenizi sağlar:

- Tümü
- Başarılı
- Hata
- Atlanmış

**Eylem** filtresi aşağıdakileri filtrelemenizi sağlar:

- Oluşturma 
- Güncelleştirme
- Sil
- Devre Dışı Bırak
- Diğer

**Tarih** filtresi, döndürülen veriler için bir zaman çerçevesi tanımlamanıza olanak sağlar.  
Olası değerler şunlardır:

- 1 ay
- 7 gün
- 30 gün
- 24 saat
- Özel zaman aralığı

Özel bir zaman çerçevesini seçtiğinizde, bir başlangıç tarihi ve bitiş tarihi yapılandırabilirsiniz.


Varsayılan alanlara ek olarak, seçili olduğunda Filtrenizle aşağıdaki alanları da dahil edebilirsiniz:

- **Iş kimliği** -benzersiz BIR iş kimliği, sağlamayı etkinleştirdiğiniz her uygulamayla ilişkilendirilir.   

- **Cycle ID** -sağlama döngüsünü benzersiz şekilde tanımlar. Bu KIMLIĞI, bu olayın gerçekleştiği döngüyü aramak için desteklemek üzere paylaşabilirsiniz.

- **Kimliği Değiştir** -sağlama olayı için benzersiz tanımlayıcı. Bu KIMLIĞI, sağlama olayını aramak için desteklemek üzere paylaşabilirsiniz.   



  

## <a name="provisioning-details"></a>Sağlama ayrıntıları 

Sağlama listesi görünümünde bir öğe seçtiğinizde, bu öğe hakkında daha fazla ayrıntı alırsınız.
Ayrıntılar aşağıdaki kategorilere göre gruplandırılır:

- Adımlar

- Sorun giderme ve öneriler

- Değiştirilen özellikler

- Özet


![Filtreyle](./media/concept-provisioning-logs/provisioning-tabs.png "Sıralanan")



### <a name="steps"></a>Adımlar

**Adımlar** sekmesi, bir nesne sağlamak için kullanılan adımları özetler. Bir nesnenin sağlanması dört adımdan oluşabilir: 

- Nesne içeri aktar
- Nesnenin kapsam içinde olup olmadığını belirleme
- Kaynak ve hedef arasında nesne Eşleştir
- Nesne sağla (işlem yapın, bu bir oluşturma, güncelleştirme, silme veya devre dışı bırakma olabilir)



![Filtreyle](./media/concept-provisioning-logs/steps.png "Filtre")


### <a name="troubleshoot-and-recommendations"></a>Sorun giderme ve öneriler


**Sorun giderme ve öneriler** sekmesi, hata kodunu ve nedenini sağlar. Hata bilgileri yalnızca bir hata durumunda kullanılabilir. 


### <a name="modified-properties"></a>Değiştirilen özellikler

**Değiştirilen özellikler** eski değeri ve yeni değeri gösterir. Eski değer olmadığı durumlarda eski değer sütunu boştur. 


### <a name="summary"></a>Özet

**Özet** sekmesi, kaynak ve hedef sistemdeki nesne için ne olduğunu ve tanımlayıcılara genel bakış sağlar. 

## <a name="what-you-should-know"></a>Bilmeniz gerekenler

- Azure portal, bir Premium sürümünüz varsa ve ücretsiz sürümünüz varsa 7 gün sonra, bildirilen sağlama verilerini 30 gün boyunca depolar.

- Değişiklik KIMLIĞI özniteliğini benzersiz tanımlayıcı olarak kullanabilirsiniz. Bu örneğin, ürün desteğiyle etkileşim kurarken faydalı olur.

- Şu anda sağlama verilerini indirme seçeneği yoktur.

- Şu anda Log Analytics desteği yoktur.

- Sağlama günlüklerine bir uygulama bağlamından eriştiğinizde, olayları otomatik olarak belirli bir uygulamayla denetim günlüklerinin yaptığı şekilde filtreetmez.

## <a name="error-codes"></a>Hata Kodları

Sağlama günlüklerinde bulabileceğiniz hataların nasıl çözümleneceğini daha iyi anlamak için aşağıdaki tabloyu kullanın. Eksik olan herhangi bir hata kodu için, bu sayfanın en altındaki bağlantıyı kullanarak geri bildirim sağlayın. 

|Hata kodu|Açıklama|
|---|---|
|Çakışma, EntryConflict|Çakışan Kullanıcı hesabının eşleştirilmek ve üzerinde alınması gerekiyorsa, Azure AD 'de veya uygulamada çakışan öznitelik değerlerini düzeltin ya da eşleşen öznitelik yapılandırmanızı gözden geçirin. Eşleşen öznitelikleri yapılandırma hakkında daha fazla bilgi için aşağıdaki [belgeleri](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) gözden geçirin.|
|TooManyRequests|Hedef uygulama, aşırı yüklenmiş ve çok fazla istek aldığından kullanıcıyı güncelleştirme girişimini reddetti. Yapılacak bir şey yok. Bu deneme otomatik olarak kullanımdan kaldırılacak. Microsoft bu sorunla aynı zamanda bilgilendirildi.|
|Dahili sunucu hatası |Hedef uygulama beklenmeyen bir hata döndürdü. Hedef uygulamayla ilgili olarak çalışmasını engelleyen bir hizmet sorunu olabilir. Bu deneme, 40 dakika içinde otomatik olarak kullanımdan kaldırılacaktır.|
|InsufficientRights, MethodNotAllowed, NotAllowed, yetkisiz| Azure AD, hedef uygulamayla kimlik doğrulaması yapabiliyor, ancak güncelleştirmeyi gerçekleştirme yetkisi yoktu. Lütfen hedef uygulamanın sunduğu yönergeleri ve ilgili uygulama [öğreticisini](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)gözden geçirin.|
|UnprocessableEntity|Hedef uygulama beklenmeyen bir yanıt döndürdü. Hedef uygulamanın yapılandırması doğru olmayabilir veya hedef uygulamayla bunun çalışmasını engelleyen bir hizmet sorunu olabilir.|
|WebExceptionProtocolError |Hedef uygulamaya bağlanılırken bir HTTP protokol hatası oluştu. Yapılacak bir şey yok. Bu deneme, 40 dakika içinde otomatik olarak kullanımdan kaldırılacaktır.|
|Invalidanchor|Sağlama hizmeti tarafından daha önce oluşturulmuş veya eşleştirilmiş bir Kullanıcı artık yok. Kullanıcının mevcut olduğundan emin olmak için denetleyin. Tüm kullanıcıların yeniden eşleşmesini zorlamak için, [işi yeniden başlatmak](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)üzere MS Graph API kullanın. Yeniden başlatmanın sağlanması, tamamlanması zaman alabilir ve bir başlangıç döngüsünü tetikleyeceğini unutmayın. Ayrıca, sağlama hizmetinin çalışması için kullandığı önbelleği de siler, yani Kiracıdaki tüm kullanıcılar ve gruplar yeniden değerlendirilmek ve belirli sağlama olayları bırakılamaz.|
|NotImplemented | Hedef uygulama beklenmeyen bir yanıt döndürdü. Uygulamanın yapılandırması doğru olmayabilir veya hedef uygulamayla bunun çalışmasını engelleyen bir hizmet sorunu olabilir. Lütfen hedef uygulamanın sunduğu yönergeleri ve ilgili uygulama [öğreticisini](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)gözden geçirin. |
|MandatoryFieldsMissing, MissingValues |Gerekli değerler eksik olduğundan kullanıcı oluşturulamadı. Kaynak kayıttaki eksik öznitelik değerlerini düzeltin veya gerekli alanların atlanmadığından emin olmak için eşleşen öznitelik yapılandırmanızı gözden geçirin. Eşleşen öznitelikleri yapılandırma hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) .|
|SchemaAttributeNotFound |Hedef uygulamada mevcut olmayan bir öznitelik belirtildiğinden, işlem gerçekleştirilemedi. Öznitelik özelleştirme hakkındaki [belgelere](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) bakın ve yapılandırmanızın doğru olduğundan emin olun.|
|InternalError |Azure AD sağlama hizmeti içinde bir iç hizmet hatası oluştu. Yapılacak bir şey yok. Bu deneme 40 dakika içinde otomatik olarak yeniden denenecek.|
|Invaliddomain |Geçersiz bir etki alanı adı içeren bir öznitelik değeri nedeniyle işlem gerçekleştirilemedi. Kullanıcının etki alanı adını güncelleştirin veya hedef uygulamadaki izin verilen listeye ekleyin. |
|aş |Hedef uygulamanın yanıt vermesi çok uzun sürdüğü için işlem tamamlanamadı. Yapılacak bir şey yok. Bu deneme 40 dakika içinde otomatik olarak yeniden denenecek.|
|Licenselimitexceıbaşında|Bu Kullanıcı için kullanılabilir lisans olmadığından, Kullanıcı hedef uygulamada oluşturulamadı. Hedef uygulama için ek lisanslar temin edebilir ya da doğru özniteliklerin doğru özniteliklerle atandığından emin olmak için Kullanıcı atamalarınızı ve öznitelik eşleme yapılandırmanızı gözden geçirin.|
|DuplicateTargetEntries  |Hedef uygulamada yapılandırılmış eşleşen özniteliklerle birden fazla kullanıcı bulunduğundan işlem tamamlanamadı. Hedef uygulamadan yinelenen kullanıcıyı kaldırın ya da öznitelik eşlemelerinizi [burada](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)açıklandığı gibi yeniden yapılandırın.|
|DuplicateSourceEntries | Yapılandırılmış eşleşen özniteliklerle birden fazla kullanıcı bulunduğundan işlem tamamlanamadı. Yinelenen kullanıcıyı kaldırın ya da öznitelik eşlemelerinizi [burada](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)açıklandığı gibi yeniden yapılandırın.|

## <a name="next-steps"></a>Sonraki adımlar

* [Kullanıcı hazırlama durumunu denetleme](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Azure AD Galeri uygulamasına kullanıcı sağlamayı yapılandırma sorunu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


