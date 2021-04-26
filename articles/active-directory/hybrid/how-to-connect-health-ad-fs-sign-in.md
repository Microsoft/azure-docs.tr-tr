---
title: Connect Health ile Azure AD 'de oturum açma işlemlerini AD FS | Microsoft Docs
description: Bu belgede, AD FS oturum açma işlemlerini Azure AD Connect Health oturum açma raporları ile tümleştirme açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74769feba1d717a2f1a72d311f85bdfbeac7b7db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574801"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>Connect Health ile Azure AD 'de AD FS oturum açma-Önizleme

AD FS oturum açma işlemleri artık, Connect Health kullanılarak Azure Active Directory oturum açma işlemleri raporuyla tümleştirilebilirler. [Azure AD oturum açma Işlemleri raporu](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-all-sign-ins#:~:text=Interactive%20user%20sign-ins%20are%20sign-ins%20where%20a%20user,to%20Azure%20AD%20or%20to%20a%20helper%20app.) raporu, kullanıcıların, uygulamaların ve yönetilen KAYNAKLARıN Azure AD 'de oturum açma ve kaynaklara erişme hakkında bilgiler içerir. 

AD FS aracısının Connect Health, istek başarısız olursa istek ve hata ayrıntıları hakkında bilgi sağlamak için sunucu sürümüne bağlı AD FS birden çok olay kimliğini bir şekilde ilişkilendirir. Bu bilgiler, Azure AD oturum bileşenleri rapor şeması ile bağıntılı ve Azure AD Sign-In rapor UX ' de görüntülenir. Raporun yanı sıra, AD FS verileri ve yeni bir Azure Izleyici çalışma kitabı şablonuyla yeni bir Log Analytics akışı kullanılabilir. Şablon, AD FS hesap kilitleme işlemleri, hatalı parola denemeleri ve beklenmedik oturum açma denemelerinin olumsuz yanı gibi senaryolar için ayrıntılı bir analizler için kullanılabilir ve değiştirilebilir.

## <a name="prerequisites"></a>Önkoşullar
* AD FS yüklenip en son sürüme yükseltilme Azure AD Connect Health.
* Azure AD oturum açma işlemlerini görüntülemek için genel yönetici veya rapor okuyucu rolü

## <a name="what-data-is-displayed-in-the-report"></a>Raporda hangi veriler görüntülenir?
Kullanılabilir veriler, Azure AD oturum açma işlemleri için mevcut verileri yansıtır. Azure AD veya AD FS oturum açma türüne göre bilgilerin bulunduğu beş sekme kullanılabilir. Sunucu sürümüne bağlı olarak AD FS durum bilgisi olan olayları bağlayın ve bunları AD FS şemasıyla eşleştirir. 



#### <a name="user-sign-ins"></a>Kullanıcı oturum açma işlemleri 
Oturum açma dikey penceresindeki her sekme aşağıdaki varsayılan değerleri gösterir:
* Oturum açma tarihi
* Request ID
* Kullanıcı adı veya Kullanıcı KIMLIĞI
* Oturum açma durumu
* Oturum açma için kullanılan cihazın IP adresi
* Sign-In tanımlayıcı

#### <a name="authentication-method-information"></a>Kimlik doğrulama yöntemi bilgileri
Kimlik doğrulama sekmesinde aşağıdaki değerler görüntülenebilir. Kimlik doğrulama yöntemi AD FS denetim günlüklerinden alınır.

|Kimlik Doğrulama Yöntemi|Description|
|-----|-----|
|Formlar|Kullanıcı adı/parola kimlik doğrulaması|
|Windows|Windows-tümleşik kimlik doğrulaması|
|Sertifika|Akıllı kart/VirtualSmart sertifikaları ile kimlik doğrulama|
|WindowsHelloForBusiness|Bu alan, Iş için Windows Hello kimlik doğrulaması içindir. (Microsoft Passport kimlik doğrulaması)|
|Cihaz | İntranet/extranet 'ten "birincil" kimlik doğrulaması ve cihaz kimlik doğrulaması gerçekleştirildikten sonra cihaz kimlik doğrulaması seçildiyse görüntülenir.  Bu senaryoda ayrı kullanıcı kimlik doğrulaması yok.| 
|Federe|AD FS kimlik doğrulaması gerçekleştirmedi ancak üçüncü taraf kimlik sağlayıcısına göndermedi|
|SSO |Çoklu oturum açma belirteci kullanılmışsa, bu alan görüntülenir. SSO bir MFA içeriyorsa, çok faktörlü olarak görünür|
|Çok faktörlü|Çoklu oturum açma belirtecinde MFA varsa ve kimlik doğrulaması için kullanıldıysa, bu alan çok faktörlü olarak görüntülenir.|
|Azure MFA|Azure MFA, AD FS ek kimlik doğrulama sağlayıcısı olarak seçilir ve kimlik doğrulaması için kullanılmıştır|
|ADFSExternalAuthenticationProvider|Bu alan, bir üçüncü taraf kimlik doğrulama sağlayıcısının kaydettirilme ve kimlik doğrulaması için kullanıldığı durumlarda|


#### <a name="ad-fs-additional-details"></a>AD FS ek ayrıntılar
AD FS oturum açma işlemleri için aşağıdaki Ayrıntılar sunulmaktadır:
* Sunucu Adı
* IP Zinciri
* Protokol

### <a name="enabling-log-analytics-and-azure-monitor"></a>Log Analytics ve Azure Izleyicisini etkinleştirme
Log Analytics AD FS oturum açma işlemleri için etkinleştirilebilir ve Sentinel gibi diğer Log Analytics tümleştirilmiş bileşenlerle birlikte kullanılabilir.

> [!NOTE] 
> AD FS oturum açma işlemleri, kuruluşunuzda AD FS oturum açma işlemlerinin miktarına bağlı olarak Log Analytics maliyeti önemli ölçüde artırabilir. Log Analytics etkinleştirmek ve devre dışı bırakmak için akışın onay kutusunu seçin.

Özelliği için Log Analytics etkinleştirmek üzere Log Analytics dikey penceresine gidin ve "ADFSSignIns" akışını seçin. Bu seçim, AD FS oturum açma işlemlerinin Log Analytics akmasını sağlayacak.

Güncelleştirilmiş Azure Izleyici çalışma kitabı şablonuna erişmek için "Azure Izleyici şablonları" na gidin ve "oturum açma" çalışma kitabını seçin.
Çalışma kitapları hakkında daha fazla bilgi için [Azure Izleyici çalışma kitaplarını](https://aka.ms/adfssigninspreview)ziyaret edin.




### <a name="frequently-asked-questions"></a>Sık Sorulan Sorular
***Görmem gereken oturum açma türleri nelerdir?***
Oturum açma raporu, O-auth, WS-beslenir, SAML ve WS-Trust protokolleri aracılığıyla oturum açma işlemlerini destekler. 

***Oturum açma raporunda farklı türlerde oturum açma işlemleri gösteriliyor?***
Kusursuz bir SSO oturumu gerçekleştirildiğinde, tek bir bağıntı KIMLIĞIYLE oturum açma işlemi için bir satır olacaktır.
Tek faktörlü kimlik doğrulaması gerçekleştirilirse, iki satır aynı bağıntı KIMLIĞIYLE, ancak iki farklı kimlik doğrulama yöntemi (ör. form, SSO) ile doldurulur.
Çok faktörlü kimlik doğrulaması durumunda, paylaşılan bağıntı KIMLIĞI ve üç karşılık gelen kimlik doğrulama yöntemi (ör. Forms, AzureMFA, çok faktörlü) ile üç satır olacaktır. Bu örnekte, bu örnekteki çok faktörlü, SSO 'nun MFA 'ya sahip olduğunu gösterir.

***Raporda görebildiğim hatalar nelerdir?***
Sign-In rapor ve açıklamalarında doldurulan AD FS ilişkili hataların tam listesi için [AD FS yardım hata kodu başvurusu](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference) ' nu ziyaret edin

***Oturum açma konusunun "Kullanıcı" bölümünde "00000000-0000-0000-0000-000000000000" görüyorum. Bu ne demek?***
Oturum açma başarısız olursa ve denenen UPN mevcut bir UPN ile eşleşmiyorsa, "Kullanıcı", "Kullanıcı adı" ve "Kullanıcı KIMLIĞI" alanları "00000000-0000-0000-0000-000000000000" olur ve "oturum açma tanımlayıcısı" Kullanıcı girdiği yapılmaya çalışılan değerle doldurulur. Bu gibi durumlarda, oturum açmaya çalışan kullanıcı yok.

***Şirket içi olaylarımı Azure AD oturum açma işlemleri raporuna nasıl ilişkilendirebilirim?***
Sunucu sürümüne bağımlı AD FS olan olay kimlikleri AD FS için Azure AD Connect Health Aracısı. Olaylar AD FS sunucularının güvenlik günlüğünde yer alır. 

***Bazı AD FS oturum açma işlemleri için uygulama KIMLIĞI/adı bölümünde NotSet veya Notapplıcable neden görüyorum?***
AD FS Sign-In raporu, OAuth oturum açma işlemleri için uygulama KIMLIĞI alanında OAuth kimliklerini görüntüler. WS-beslenir WS-Trust oturum açma senaryolarında, uygulama KIMLIĞI NotSet veya Notapplıcable olur ve kaynak kimlikleri ve bağlı olan taraf tanımlayıcıları kaynak KIMLIĞI alanında mevcut olacaktır.

***Önizlemede raporda daha fazla bilinen sorun var mı?***
Raporda, oturum açma yönteminden bağımsız olarak AD FS oturum açma işlemleri için "temel bilgiler" sekmesindeki "kimlik doğrulama gereksinimi" alanının tek faktörlü kimlik doğrulama değeri olarak doldurulduğu bilinen bir sorun vardır. Ayrıca, kimlik doğrulaması Ayrıntıları sekmesi, birincil veya Ikincil kimlik doğrulama türlerini ayırt etmek için devam eden bir düzeltmeyle birlikte gereksinim alanı altında "birincil veya Ikincil" görüntülenir.


## <a name="related-links"></a>İlgili bağlantılar
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Aracı yüklemesini Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Riskli IP raporu](how-to-connect-health-adfs-risky-ip.md)





