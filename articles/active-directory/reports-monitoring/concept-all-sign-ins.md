---
title: Azure Active Directory oturum açma etkinlik raporları-önizleme | Microsoft Docs
description: Azure Active Directory portalındaki oturum açma etkinlik raporlarına giriş
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc9aa77b3fdc3cda94670545f847bb9de31e1160
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718962"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Azure Active Directory oturum açma etkinlik raporları-önizleme

BT Yöneticisi olarak BT ortamınızın nasıl çalıştığını öğrenmek istersiniz. Sisteminizin sistem durumu hakkındaki bilgiler, olası sorunlara yanıt vermek isteyip istemediğinizi değerlendirmenizi sağlar. 

Bu hedefle ilgili sizi desteklemek için Azure Active Directory portalı, üç etkinlik günlüğüne erişmenizi sağlar:

- **Oturum açma** bilgileri – oturum açma işlemleri ve kaynaklarınızın kullanıcılarınız tarafından nasıl kullanıldığı hakkında bilgiler.
- **[Denetim](concept-audit-logs.md)** – kiracınıza uygulanan kullanıcılar ve Grup Yönetimi veya kiracınızın kaynaklarına uygulanan güncelleştirmeler gibi değişiklikler hakkında bilgiler.
- **[Sağlama](concept-provisioning-logs.md)** : ServiceNow 'da bir grup oluşturma veya Workday 'den içeri aktarılan bir kullanıcı gibi sağlama hizmeti tarafından gerçekleştirilen etkinlikler.


Azure Active Directory içindeki klasik oturum açma işlemleri raporu etkileşimli kullanıcı oturum açma bilgileri için bir genel bakış sağlar. Ayrıca, şu anda önizleme aşamasında olan üç ek oturum açma raporlarına erişebilirsiniz:

- Etkileşimli olmayan kullanıcı oturum açma işlemleri

- Hizmet sorumlusu oturum açma işlemleri

- Azure Kaynak oturum açma işlemleri için Yönetilen kimlikler

Bu makalede, Azure kaynakları oturum açma işlemleri için etkileşimli olmayan, uygulama ve yönetilen kimliklerin önizlemesi ile oturum açma etkinlik raporuna genel bakış sunulmaktadır. Önizleme özellikleri olmadan oturum açma raporu hakkında daha fazla bilgi için,  [Azure Active Directory portalındaki oturum açma etkinlik raporları](concept-sign-ins.md)bölümüne bakın.



## <a name="prerequisites"></a>Önkoşullar

Bu özelliği kullanmaya başlayabilmeniz için şu yanıtları bilmeniz gerekir:

- Verilere kimler erişebilir?

- Oturum açma etkinliğine erişebilmek için hangi Azure AD lisansınızın olması gerekir?

### <a name="who-can-access-the-data"></a>Verilere kimler erişebilir?

- Güvenlik Yöneticisi, güvenlik okuyucu ve rapor okuyucu rollerinin kullanıcıları

- Genel Yöneticiler

- Tüm kullanıcılar (yönetici olmayan) kendi oturum açma etkinliklerine erişebilirler 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Oturum açma etkinliğine erişebilmek için hangi Azure AD lisansınızın olması gerekir?

Kiracınız, oturum açma etkinliklerini görmek için kendisiyle ilişkili bir Azure AD Premium lisansına sahip olmalıdır. Azure Active Directory sürümünüzü yükseltmek için bkz. [Azure Active Directory Premium kullanmaya](../fundamentals/active-directory-get-started-premium.md) başlama. Yükseltmeden önce veri etkinliği olmayan bir Premium lisansa yükselttikten sonra, verilerin raporlarda gösterilmesi birkaç gün sürer.



## <a name="sign-ins-report"></a>Oturum açma işlemleri raporu

Oturum açma işlemleri raporu, aşağıdaki soruların yanıtlarını sağlar:

- Kullanıcı, uygulama veya hizmetin oturum açma deseninin anlamı nedir?
- Bir haftada kaç Kullanıcı, uygulama veya hizmet oturum açtı?
- Bu açılan oturumların durumu nedir?


Oturum açma raporu dikey penceresinde şunları yapabilirsiniz:

- **Etkileşimli kullanıcı oturum açma** işlemleri-bir kullanıcının parola, MFA uygulaması aracılığıyla bir yanıt, bir biyometri faktörü veya QR kodu gibi bir kimlik doğrulama faktörü sağladığı oturum açma işlemleri.

- Kullanıcı adına bir istemci tarafından gerçekleştirilen **etkileşimli olmayan kullanıcı oturum** açma işlemleri. Bu oturum açma işlemleri kullanıcıdan herhangi bir etkileşim veya kimlik doğrulama faktörü gerektirmez. Örneğin, bir kullanıcının kimlik bilgilerini girmesini gerektirmeyen yenileme ve erişim belirteçlerini kullanarak kimlik doğrulaması ve yetkilendirme.

- **Hizmet sorumlusu oturum açma** işlemleri-hiçbir Kullanıcı içermeyen uygulamalar ve hizmet sorumluları tarafından oturum açılır. Bu oturum açma işlemleri sırasında, uygulama veya hizmet, kaynakların kimliğini doğrulamak veya kaynaklara erişmek için kendi adına bir kimlik bilgisi sağlar.

- Azure kaynakları **Için Yönetilen kimlikler** Azure tarafından yönetilen gizli dizileri olan Azure kaynaklarında oturum açma işlemleri. Daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../managed-identities-azure-resources/overview.md) 


![Oturum açma işlemleri rapor türleri](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins&quot;></a>Kullanıcı oturum açma işlemleri

Oturum açma dikey penceresindeki her sekme aşağıdaki varsayılan sütunları gösterir. Bazı sekmelerin ek sütunları vardır:

- Oturum açma tarihi

- Request ID

- Kullanıcı adı veya Kullanıcı KIMLIĞI

- Uygulama adı veya uygulama KIMLIĞI

- Oturum açma durumu

- Oturum açma için kullanılan cihazın IP adresi



### <a name=&quot;interactive-user-sign-ins&quot;></a>Etkileşimli kullanıcı oturum açma işlemleri


Etkileşimli kullanıcı oturum açma işlemleri, kullanıcının Azure AD 'ye bir kimlik doğrulama faktörü sağladığı veya Microsoft Authenticator uygulaması gibi bir yardımcı uygulamayla doğrudan etkileşimde bulunduğu oturum açma yöntemlerdir. Kullanıcıların Azure AD 'ye veya bir yardımcı uygulamaya sağladığı bir kullanıcı tarafından sağlanan, MFA zorlukları, biyometrik faktörleri veya QR kodlarına yönelik parola ekleme faktörleri.

> [!NOTE]
> Bu rapor ayrıca Azure AD 'ye federe kimlik sağlayıcılarından gelen Federasyon oturum açma işlemlerini içerir.  



> [!NOTE] 
> Etkileşimli kullanıcı oturum açma işlemleri raporu, Microsoft Exchange istemcilerinden etkileşimli olmayan bazı oturum açma işlemleri içerir. Bu oturum açma işlemleri etkileşimli olmamakla birlikte, ek görünürlük için etkileşimli kullanıcı oturum açma raporuna dahil edilmiştir. Etkileşimli olmayan kullanıcı oturum açma işlemleri raporu Kasım 2020 ' de genel önizlemeye sunulduktan sonra, bu etkileşimli olmayan oturum açma olay günlükleri, daha fazla doğruluk için etkileşimli olmayan kullanıcı oturum açma raporuna taşınmıştır. 


**Rapor boyutu:** küçük <br> 
**Örnekler**

- Kullanıcı, Azure AD oturum açma ekranında Kullanıcı adı ve parola sağlar.

- Bir kullanıcı SMS MFA sınamasını geçirir.

- Bir Kullanıcı Windows 'un Iş için Windows Hello ile kilidini açmak için bir biyometri hareketi sağlar.

- Bir Kullanıcı Azure AD 'ye AD FS SAML onayı ile federe olur.


Varsayılan alanlara ek olarak, etkileşimli oturum açma işlemleri raporu da şunları gösterir: 

- Oturum açma konumu

- Koşullu erişimin uygulanıp uygulanmadığı



Araç çubuğunda **Sütunlar**’a tıklayarak liste görünümünü özelleştirebilirsiniz.

![Etkileşimli kullanıcı oturum açma sütunları](./media/concept-all-sign-ins/columns-interactive.png &quot;Etkileşimli kullanıcı oturum açma sütunları")





Görünümü özelleştirmek, ek alanları görüntülemenize veya zaten görüntülenen alanları kaldırmanıza olanak sağlar.

![Tüm etkileşimli sütunlar](./media/concept-all-sign-ins/all-interactive-columns.png)


İlgili oturum açma hakkında daha ayrıntılı bilgi edinmek için liste görünümünde bir öğe seçin.

![Oturum açma etkinliği](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Etkileşimli kullanıcı oturum açma işlemleri")



### <a name="non-interactive-user-sign-ins"></a>Etkileşimli olmayan kullanıcı oturum açma işlemleri

Etkileşimli olmayan kullanıcı oturum açma işlemleri, bir kullanıcı adına istemci uygulaması veya işletim sistemi bileşenleri tarafından gerçekleştirilen oturum açma bileşenleridir. Etkileşimli kullanıcı oturum açma işlemleri gibi, bu oturum açma işlemleri de bir kullanıcı adına yapılır. Etkileşimli kullanıcı oturum açma işlemlerinin aksine, bu oturum açma işlemleri kullanıcının bir kimlik doğrulama faktörü sağlamasını gerektirmez. Bunun yerine, cihaz veya istemci uygulaması bir kullanıcı adına bir kaynağı doğrulamak veya bir kaynağa erişmek için bir belirteç veya kod kullanır. Genel olarak, Kullanıcı bu oturum açma işlemlerini Kullanıcı etkinliğinin arka planında olduğu şekilde alır.


**Rapor boyutu:** Miktarda <br>
**Örnekler:** 

- İstemci uygulaması, bir erişim belirteci almak için OAuth 2,0 yenileme belirtecini kullanır.

- İstemci bir erişim belirteci almak ve belirteci yenilemek için bir OAuth 2,0 yetkilendirme kodu kullanır.

- Kullanıcı, Azure AD 'ye katılmış bir bılgısayarda bir Web veya Windows uygulamasında çoklu oturum açma (SSO) gerçekleştirir.

- Kullanıcı, bir mobil cihazda FOCı (Istemci kimlikleri ailesi) kullanarak oturum açtıklarında ikinci bir Microsoft Office uygulamasına oturum açar.




Varsayılan alanlara ek olarak, etkileşimli olmayan oturum açma işlemleri raporu da şunları gösterir: 

- Kaynak kimliği

- Gruplanmış oturum açma işlemlerinin sayısı




Bu raporda gösterilen alanları özelleştiremezsiniz.


![Devre dışı sütunlar](./media/concept-all-sign-ins/disabled-columns.png "Devre dışı sütunlar")

Verilerin özetleşebilmesini sağlamak için etkileşimli olmayan oturum açma olayları gruplandırılır. İstemciler genellikle aynı kullanıcı adına, oturum açma işlemi denenme süresi dışında aynı özellikleri paylaşan birçok etkileşimli olmayan oturum açma işlemi oluşturur. Örneğin, bir istemci bir kullanıcı adına saat başına bir erişim belirteci alabilir. Kullanıcı veya istemci durumu değiştirmediğinden, IP adresi, kaynak ve diğer tüm bilgiler her bir erişim belirteci isteği için aynıdır. Azure AD, saat ve Tarih dışında birden çok oturum açma kaydı yaptığında, bu oturum açma işlemleri tek bir satırda toplanır. Birden fazla özdeş oturum açma (Tarih ve saat hariç) içeren bir satır, # Sign-ins sütununda 1 ' den büyük bir değere sahip olur. Tüm farklı oturum açma işlemlerini ve bunların farklı zaman damgalarını görmek için satırı genişletebilirsiniz. Aşağıdaki veriler eşleştiğinde etkileşimli olmayan kullanıcılar için oturum açma işlemleri toplanır:


- Uygulama

- Kullanıcı

- IP Adresi

- Durum

- Kaynak kimliği


Seçenekleriniz şunlardır:

- Bir grubun tek tek öğelerini görmek için bir düğümü genişletin.  

- Tüm ayrıntıları görmek için tek bir öğeye tıklayın 


![Etkileşimli olmayan kullanıcı oturum açma ayrıntıları](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Hizmet sorumlusu oturum açma işlemleri

Etkileşimli ve etkileşimli olmayan kullanıcı oturum açma işlemlerinin aksine, hizmet sorumlusu oturum açma işlemleri bir kullanıcı içermez. Bunun yerine, uygulamalar veya hizmet sorumluları (yalnızca yönetilen kimlik oturum açma raporuna dahil olan yönetilen kimlik oturum açma işlemleri dışında) gibi kullanıcı olmayan herhangi bir hesap tarafından oturum açma işlemleri yapılır. Bu oturum açma işlemleri sırasında, uygulama veya hizmet, kimlik doğrulaması yapmak veya kaynaklara erişmek için sertifika veya uygulama gizli dizisi gibi kendi kimlik bilgilerini sağlar.


**Rapor boyutu:** Miktarda <br>
**Örnekler:**

- Hizmet sorumlusu, kimlik doğrulaması yapmak ve Microsoft Graph erişmek için bir sertifika kullanır. 

- Bir uygulama, OAuth Istemci kimlik bilgileri akışında kimlik doğrulamak için bir istemci gizli anahtarı kullanır. 


Bu rapor, şunu gösteren bir varsayılan liste görünümüne sahiptir:

- Oturum açma tarihi

- Request ID

- Hizmet sorumlusu adı veya KIMLIĞI

- Durum

- IP Adresi

- Kaynak adı

- Kaynak kimliği

- Oturum açma işlemlerinin sayısı

Bu raporda gösterilen alanları özelleştiremezsiniz.

![Devre dışı sütunlar](./media/concept-all-sign-ins/disabled-columns.png "Devre dışı sütunlar")

Hizmet sorumlusu oturum açma günlüklerinde verilerin özetlanmasını kolaylaştırmak için hizmet sorumlusu oturum açma olayları gruplandırılır. Aynı koşullarda aynı varlıktaki oturum açma işlemleri tek bir satırda toplanır. Tüm farklı oturum açma işlemlerini ve bunların farklı zaman damgalarını görmek için satırı genişletebilirsiniz. Aşağıdaki veriler eşleştiğinde, hizmet sorumlusu raporunda oturum açma işlemleri toplanır:

- Hizmet sorumlusu adı veya KIMLIĞI

- Durum

- IP Adresi

- Kaynak adı veya KIMLIĞI

Seçenekleriniz şunlardır:

- Bir grubun tek tek öğelerini görmek için bir düğümü genişletin.  

- Tüm ayrıntıları görmek için tek bir öğeye tıklayın 


![Sütun ayrıntıları](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Sütun ayrıntıları")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Azure kaynakları için yönetilen kimlik oturum açma işlemleri 

Azure kaynakları oturum açma işlemleri için yönetilen kimlik, kimlik bilgileri yönetimini basitleştirmek amacıyla gizli dizileri olan kaynaklar tarafından gerçekleştirilen oturum açma işlemleri.

**Rapor boyutu:** Küçük <br> 
**Örnekler**

Yönetilen kimlik bilgilerine sahip bir VM, bir erişim belirteci almak için Azure AD kullanır.   


Bu rapor, şunu gösteren bir varsayılan liste görünümüne sahiptir:


- Yönetilen kimlik KIMLIĞI

- Yönetilen kimlik adı

- Kaynak

- Kaynak kimliği

- Gruplanmış oturum açma işlemlerinin sayısı

Bu raporda gösterilen alanları özelleştiremezsiniz.

Verilerin özetlanmasını kolaylaştırmak için Azure kaynakları için Yönetilen kimlikler oturum açma günlüklerinde, etkileşimli olmayan oturum açma olayları gruplandırılır. Aynı varlıktaki oturum açma işlemleri tek bir satırda toplanır. Tüm farklı oturum açma işlemlerini ve bunların farklı zaman damgalarını görmek için satırı genişletebilirsiniz. Aşağıdaki tüm veriler eşleştiğinde, Yönetilen kimlikler raporunda oturum açma işlemleri toplanır:

- Yönetilen kimlik adı veya KIMLIĞI

- Durum

- IP Adresi

- Kaynak adı veya KIMLIĞI

Bir düğüm altında gruplandırılan tüm oturum açma işlemlerini görüntülemek için liste görünümünde bir öğe seçin.

Oturum açma ayrıntılarının tüm ayrıntılarını görmek için gruplanmış bir öğe seçin. 


## <a name="sign-in-error-code"></a>Oturum açma hata kodu

Bir oturum açma başarısız olursa, ilgili günlük öğesinin **temel bilgi** bölümünde nedeni hakkında daha fazla bilgi edinebilirsiniz. 

![Ekran görüntüsü ayrıntılı bir bilgi görünümünü gösterir.](./media/concept-all-sign-ins/error-code.png)
 
Günlük öğesi size bir hata nedeni sağladığından, [oturum açma hatası arama aracını](https://login.microsoftonline.com/error)kullanarak daha fazla bilgi alabileceğiniz durumlar vardır. Örneğin, varsa, bu araç size düzeltme adımları sağlar.  

![Hata kodu arama aracı](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>Oturum açma etkinliklerini filtreleme

Bir filtre ayarlayarak, döndürülen oturum açma verilerinin kapsamını daraltabilirsiniz. Azure AD size ayarlayabileceğiniz çok çeşitli ek filtreler sağlar. Filtrenizi ayarlarken, yapılandırılan **Tarih** aralığı Filtrenizle her zaman özel bir dikkat ödemelisiniz. Uygun bir tarih aralığı Filtresi, Azure AD 'nin yalnızca gerçekten önem verdiğiniz verileri döndürdüğünden emin olmanızı sağlar.     

**Tarih** aralığı Filtresi, döndürülen veriler için bir zaman çerçevesi tanımlamanıza olanak sağlar.
Olası değerler şunlardır:

- Bir ay

- Yedi gün

- Yirmi dört saat

- Özel

![Tarih aralığı Filtresi](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Kullanıcı oturum açma işlemlerini filtrele

Etkileşimli ve etkileşimli olmayan oturum açma filtreleri aynı. Bu nedenle, etkileşimli oturum açma işlemleri için yapılandırdığınız filtre etkileşimli olmayan oturum açma işlemleri için kalıcı hale getirilir ve tam tersi de geçerlidir. 






## <a name="access-the-new-sign-in-activity-reports"></a>Yeni oturum açma etkinliği raporlarına erişin 

Azure portal oturum açma Etkinliği raporu, önizleme raporunu değiştirmek ve kapatmak için basit bir yöntem sağlar. Önizleme raporları etkinse, tüm oturum açma etkinliği rapor türlerine erişmenizi sağlayan yeni bir menü alırsınız.     


Etkileşimli olmayan ve uygulama oturum açma bilgileriyle yeni oturum açma raporlarına erişmek için: 

1. [Azure portalda](https://portal.azure.com)**Azure Active Directory**'yi seçin.

    ![Azure AD 'yi seçin](./media/concept-all-sign-ins/azure-services.png)

2. **İzleme** bölümünde **oturum açma** işlemleri ' ne tıklayın.

    ![Oturum açma işlemlerini seçin](./media/concept-all-sign-ins/sign-ins.png)

3. **Önizleme** çubuğuna tıklayın.

    ![Yeni görünümü etkinleştir](./media/concept-all-sign-ins/enable-new-preview.png)

4. Varsayılan görünüme geri dönmek için **Önizleme** çubuğuna tekrar tıklayın. 

    ![Klasik görünümü geri yükle](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Oturum açma etkinlik raporlarını indirin

Bir oturum açma Etkinlik raporunu indirdiğinizde aşağıdakiler doğrudur:

- Oturum açma raporunu CSV veya JSON dosyası olarak indirebilirsiniz.

- En fazla 100-K kayıt yükleyebilirsiniz. Daha fazla veri indirmek istiyorsanız raporlama API 'sini kullanın.

- İndirmeniz, yaptığınız filtre seçimine dayalıdır.

- İndirebileceğiniz kayıt sayısı [Azure Active Directory rapor bekletme ilkeleri](reference-reports-data-retention.md)tarafından sınırlandırılır. 


![Raporları indir](./media/concept-all-sign-ins/download-reports.png "Raporları indir")


Her CSV indirmesi altı farklı dosyadan oluşur:

- Etkileşimli oturum açma işlemleri

- Etkileşimli oturum açma işlemlerinin kimlik doğrulaması ayrıntıları

- Etkileşimli olmayan oturum açma işlemleri

- Etkileşimli olmayan oturum açma işlemlerinin kimlik doğrulaması ayrıntıları

- Hizmet sorumlusu oturum açma işlemleri

- Azure kaynakları için yönetilen kimlik oturum açma işlemleri

Her JSON indirmesi dört farklı dosyadan oluşur:

- Etkileşimli oturum açma işlemleri (auth ayrıntıları içerir)

- Etkileşimli olmayan oturum açma işlemleri (auth ayrıntıları içerir)

- Hizmet sorumlusu oturum açma işlemleri

- Azure kaynakları için yönetilen kimlik oturum açma işlemleri

![Dosyaları indirme](./media/concept-all-sign-ins/download-files.png "Dosyaları indirme")




## <a name="next-steps"></a>Sonraki adımlar

* [Oturum açma Etkinliği raporu hata kodları](reference-sign-ins-error-codes.md)
* [Azure AD veri saklama ilkeleri](reference-reports-data-retention.md)
* [Azure AD rapor gecikmeleri](reference-reports-latencies.md)
