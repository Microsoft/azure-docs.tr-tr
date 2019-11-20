---
title: Azure AD uygulama ara sunucusu ve Qlik Sense | Microsoft Docs
description: Azure portalında uygulama ara sunucusunu etkinleştirmek ve için ters proxy bağlayıcıları yükleyin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f54e08e6c3b7b673541f124a90f32dbc860fa44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65859531"
---
# <a name="application-proxy-and-qlik-sense"></a>Uygulama Ara sunucusu ve Qlik Sense 
Azure Active Directory Uygulama Ara sunucusu ve Qlik Sense kolayca Qlik Sense dağıtımınız için uzaktan erişim sağlamak için uygulama ara sunucusu kullanmanız mümkün olduğundan emin olmak için birlikte kurdu.  

## <a name="prerequisites"></a>Önkoşullar 
Bu senaryonun kalanı aşağıdaki işlemleri olduğunu varsayar:
 
- Yapılandırılmış [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Yüklü uygulama Proxy Bağlayıcısı](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Uygulamalarınızı Azure'da yayımlayın 
QlikSense yayımlamak için Azure'da iki uygulama yayımlamak gerekir.  

### <a name="application-1"></a>Uygulama #1: 
Uygulamanızı yayımlamak için aşağıdaki adımları izleyin. 1-8, bkz: adımlarının daha ayrıntılı için [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](application-proxy-add-on-premises-application.md). 


1. Azure portalına genel yönetici olarak oturum açın. 
2. Seçin **Azure Active Directory** > **kurumsal uygulamalar**. 
3. Seçin **Ekle** dikey penceresinin üstünde. 
4. Seçin **şirket içi uygulama**. 
5. Yeni uygulamanız hakkındaki bilgilerle gerekli alanları doldurun. Ayarları için aşağıdaki yönergeleri kullanın: 
   - **İç URL**: Bu uygulama QlikSense URL bir iç URL olması gerekir. Örneğin, **https&#58;//demo.qlikemm.com:4244** 
   - **Ön kimlik doğrulama yöntemi**: Azure Active Directory (ancak bu zorunlu önerilir) 
1. Seçin **Ekle** dikey pencerenin alt kısmındaki. Uygulamanızı eklenir ve Hızlı Başlangıç menüsü açılır. 
2. Hızlı Başlangıç menüde **test etmek için kullanıcı atama**, ve en az bir kullanıcı uygulamaya ekleyin. Bu test hesabı şirket içi uygulamaya erişimi olduğundan emin olun. 
3. Seçin **atama** test kullanıcı atama kaydetmek için. 
4. (İsteğe bağlı) Uygulama Yönetimi dikey penceresinde, çoklu oturum açma seçin. Seçin **Kerberos kısıtlanmış temsil** açılan menüsünden ve Qlik yapılandırmanıza göre gerekli alanları doldurun. **Kaydet**’i seçin. 

### <a name="application-2"></a>Uygulama #2: 
Aşağıdaki istisnalarla birlikte uygulama #1 olduğu gibi aynı adımları izleyin: 

**#5. adım**: İç URL, artık uygulama tarafından kullanılan kimlik doğrulaması bağlantı noktası ile QlikSense URL olmalıdır. Varsayılan değer **4244** HTTPS ve HTTP için 4248. Örn: **https&#58;//demo.qlik.com:4244**</br></br> 
 **#10 adım:** Yoksa SSO'yu ayarlama ve bırakın **çoklu oturum açma devre dışı**
 
 
## <a name="testing"></a>Test Etme 
Uygulamanızı şimdi test etmek hazırdır. Her iki uygulama için atanan bir kullanıcı olarak uygulama #1 ve oturum açma QlikSense yayımlamak için kullanılan dış URL erişin.  

## <a name="additional-references"></a>Ek başvurular
Uygulama Ara sunucusu ile uygulama yayımlama Qlik Sense hakkında daha fazla bilgi için aşağıdaki Qlik topluluk makaleler için bakın: 
- [Azure AD ile tümleşik Windows Qlik Sense ile Kerberos kısıtlanmış temsil kullanarak kimlik doğrulaması](https://community.qlik.com/docs/DOC-20183)
- [Azure AD uygulama proxy'si ile Qlik Sense tümleştirme](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama Ara sunucusu ile uygulama yayımlama](application-proxy-add-on-premises-application.md)
- [Uygulama Proxy bağlayıcıları ile çalışma](application-proxy-connector-groups.md)

