---
title: Azure Active Directory Raporlama API 'SI önkoşulları | Microsoft Docs
description: Azure AD Raporlama API 'sine erişim önkoşulları hakkında bilgi edinin
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00c519ef06637c5193b347f0bbc906c6232a7ca8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532533"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Azure Active Directory Raporlama API 'sine erişim önkoşulları

[Azure Active Directory (Azure AD) raporlama API'leri](./concept-reporting-api.md), bir dizi REST tabanlı API aracılığıyla verilere programlı erişim sağlar. Bu API 'Leri, programlama dilleri ve araçlarından çağırabilirsiniz.

Raporlama API 'SI, Web API 'Lerine erişim yetkisi vermek için [OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) kullanır.

Raporlama API 'sine erişiminizi hazırlamak için şunları yapmanız gerekir:

1. [Rolleri atama](#assign-roles)
2. [Lisans gereksinimleri](#license-requirements)
3. [Uygulamayı kaydetme](#register-an-application)
4. [İzinleri verme](#grant-permissions)
5. [Yapılandırma ayarlarını topla](#gather-configuration-settings)

## <a name="assign-roles"></a>Rolleri atama

API aracılığıyla raporlama verilerine erişim sağlamak için, aşağıdaki rollerden birine atanmış olması gerekir:

- Güvenlik okuyucusu

- Güvenlik Yöneticisi

- Genel Yönetici

## <a name="license-requirements"></a>Lisans gereksinimleri

Bir kiracının oturum açma raporlarına erişebilmek için bir Azure AD kiracısının ilişkili Azure AD Premium lisansı olması gerekir. Azure AD Premium P1 (veya üzeri), herhangi bir Azure AD kiracısına yönelik oturum açma raporlarına erişmek için gereklidir. Alternatif olarak, Dizin türü Azure AD B2C ise, oturum açma raporlarına ek lisans gereksinimi olmadan API aracılığıyla erişilebilir. 


## <a name="register-an-application"></a>Bir uygulamayı kaydetme

Raporlama API 'sine bir betik kullanarak erişiyorsanız bile kayıt gereklidir. Kayıt, yetkilendirme çağrıları için gerekli olan bir **uygulama kimliği** sağlar ve kodunuzun belirteçleri almasını sağlar.

Dizininizi Azure AD Raporlama API 'sine erişecek şekilde yapılandırmak için, Azure AD kiracınızda **genel yönetici** dizini rolünün da üyesi olan bir Azure yönetici hesabıyla [Azure Portal](https://portal.azure.com) oturum açmalısınız.

> [!IMPORTANT]
> Yönetici ayrıcalıklarına sahip kimlik bilgileri altında çalışan uygulamalar çok güçlü olabilir, bu nedenle lütfen uygulamanın KIMLIĞI ve gizli kimlik bilgilerini güvenli bir konumda tutmayı unutmayın.
> 

**Bir Azure AD uygulamasını kaydetmek için:**

1. [Azure Portal](https://portal.azure.com)sol gezinti bölmesinden **Azure Active Directory** ' i seçin.
   
    ![Ekran görüntüsü Azure portal menüsünden Seçili Azure Active Directory gösterir.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **Azure Active Directory** sayfasında **uygulama kayıtları**' i seçin.

    ![Ekran görüntüsü Yönet menüsünden Seçili Uygulama kayıtları gösterir.](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. **Uygulama kayıtları** sayfasından **Yeni kayıt**' yi seçin.

    ![Ekran görüntüsünde yeni kayıt seçili görüntülenir.](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. **Uygulama kaydetme** sayfası:

    ![Ekran görüntüsünde, bu adımdaki değerleri girebileceğiniz bir uygulama kaydetme sayfası gösterilir.](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. **Ad** metin kutusuna yazın `Reporting API application` .

    b. **Desteklenen hesap türü** için **yalnızca bu kuruluştaki hesaplar**' ı seçin.

    c. **Yeniden yönlendirme URL 'si** ' nde **Web** metin kutusu seçin yazın `https://localhost` .

    d. **Kaydet**’i seçin. 


## <a name="grant-permissions"></a>İzinleri verme 

Erişmek istediğiniz API 'ye bağlı olarak, uygulamanıza aşağıdaki izinleri vermeniz gerekir:  

| API | İzin |
| --- | --- |
| Windows Azure Active Directory | Dizin verilerini oku |
| Microsoft Graph | Tüm denetim günlüğü verilerini okuyun |

![Ekran görüntüsü, bir P I izinleri bölmesinde izin Ekle ' yi seçebileceğiniz yeri gösterir.](./media/howto-configure-prerequisites-for-reporting-api/36.png)

Aşağıdaki bölümde her iki API için de adımlar listelenmektedir. API 'lerden birine erişmek istemiyorsanız ilgili adımları atlayabilirsiniz.

**API 'Leri kullanmak için uygulamanıza izin vermek için:**


1. **API izinleri** ' ni seçin ve **izin ekleyin**. 

    ![Ekran görüntüsü, izin Ekle seçeneğini seçebileceğiniz bir P I Izinleri sayfasını gösterir.](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. **API Izinleri iste sayfasında**, **Eski API** **Azure Active Directory grafiğini** destekler ' i bulun. 

    ![Ekran görüntüsü, Azure Active Directory grafik seçebileceğiniz bir P I izinleri ıste sayfasını gösterir.](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. **Gerekli izinler** sayfasında **Uygulama izinleri**' ni, **Dizin** onay kutusu dizini ' ni genişletin **. ReadAll**' u seçin.  **Izin Ekle**' yi seçin.

    ![Ekran görüntüsü, uygulama izinleri seçebileceğiniz bir P I izinleri ıste sayfasını gösterir.](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. **Raporlama API 'Si uygulaması-API izinleri** sayfasında, yönetici izni **ver**' i seçin. 

    ![Ekran görüntüsünde, yönetici izni ver ' i seçebileceğiniz bir P I uygulaması olan p I izinleri bildiren bir ekran görüntülenir.](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Note: **Microsoft Graph** , API kaydı sırasında varsayılan olarak eklenir.

    ![Ekran görüntüsü, izin Ekle seçeneğini seçebileceğiniz bir P I izinleri sayfasını gösterir.](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Yapılandırma ayarlarını topla 

Bu bölümde, dizininizden aşağıdaki ayarların nasıl alınacağı gösterilmektedir:

- Etki alanı adı
- İstemci Kimliği
- Gizli anahtar

Raporlama API 'sine yapılan çağrıları yapılandırırken bu değerlere ihtiyacınız vardır. 

### <a name="get-your-domain-name"></a>Etki alanı adınızı alın

**Etki alanı adınızı almak için:**

1. [Azure Portal](https://portal.azure.com), sol gezinti bölmesinde **Azure Active Directory**' i seçin.
   
    ![Ekran görüntüsü Azure portal menüsünden Seçili Azure Active Directory gösterir.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **Azure Active Directory** sayfasında, **özel etki alanı adları**' nı seçin.

    ![Ekran görüntüsünde Azure Active Directory seçilen özel etki alanı adları gösterilmektedir.](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Etki alanı adınızı etki alanları listesinden kopyalayın.


### <a name="get-your-applications-client-id"></a>Uygulamanızın istemci KIMLIĞINI alın

**Uygulamanızın istemci KIMLIĞINI almak için:**

1. [Azure Portal](https://portal.azure.com), sol gezinti bölmesinde **Azure Active Directory**' e tıklayın.
   
    ![Ekran görüntüsü Azure portal menüsünden Seçili Azure Active Directory gösterir.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Uygulama **kayıtları** sayfasından uygulamanızı seçin.

3. Uygulama sayfasından **uygulama kimliği** ' ne gidin ve **kopyalamak Için tıklayın ' ı** seçin.

    ![Ekran görüntüsü, b uygulamasını kopyalayabileceğiniz bir P I uygulaması raporlama sayfasını gösterir.](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Uygulamanızın istemci gizliliğini alın
 Denetim günlüklerine erişmeye veya API kullanarak oturum açmaya çalışırken hatalardan kaçının.

**Uygulamanızın istemci gizli anahtarını almak için:**

1. [Azure Portal](https://portal.azure.com), sol gezinti bölmesinde **Azure Active Directory**' e tıklayın.
   
    ![Ekran görüntüsü Azure portal menüsünden Seçili Azure Active Directory gösterir.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Uygulama **kayıtları** sayfasından uygulamanızı seçin.

3.  **API uygulaması** sayfasında **Sertifikalar ve gizlilikler** ' ı seçin, **Istemci gizli** dizileri bölümünde **+ yeni istemci parolası**' na tıklayın. 

    ![Ekran görüntüsü, bir istemci gizli anahtarı ekleyebileceğiniz sertifikalar & gizli dizi sayfasını gösterir.](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. **İstemci parolası Ekle** sayfasında şunları ekleyin:

    a. **Açıklama** metin kutusuna yazın `Reporting API` .

    b. **Süresi sona erdiğinde** **2 yıl içinde** öğesini seçin.

    c. **Kaydet**’e tıklayın.

    d. Anahtar değerini kopyalayın.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Raporlama API 'sindeki hataların sorunlarını giderme

Bu bölümde, Microsoft Graph API 'sini ve bunların çözümlenme adımlarını kullanarak etkinlik raporlarına erişirken kullanabileceğiniz ortak hata iletileri listelenmektedir.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Hata: Kullanıcı rolleri Microsoft Graph alınamadı

 Graph Explorer 'ı kullanarak oturum açmaya çalışırken bir hata oluşmasını önlemek için, Graph Explorer Kullanıcı arabirimindeki oturum açma düğmelerini kullanarak hesabınızda oturum açın. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Hata: Microsoft Graph Premium lisans denetimi yapılamadı 

Graph Explorer kullanarak oturum açma erişimiyle çalışmaya çalışırken bu hata iletisiyle karşılaşırsanız, sol gezinti bölmesinde hesabınızın altındaki **Izinleri Değiştir** ' i seçin ve **Görevler. ReadWrite** ve **Dizin. Read. All**' ı seçin. 

![İzin Kullanıcı arabirimini değiştirme](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Hata: kiracı B2C değil veya kiracı Premium lisansa sahip değil

Oturum açma raporlarına erişmek için Azure Active Directory Premium 1 (P1) lisansı gerekir. Oturum açma işlemleri sırasında bu hata iletisini görürseniz, kiracınızın bir Azure AD P1 lisansıyla lisanslanmasını sağlayın.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Hata: izin verilen roller Kullanıcı içermiyor. 

 Denetim günlüklerine erişmeye veya API kullanarak oturum açmaya çalışırken hatalardan kaçının. Hesabınızın Azure Active Directory kiracınızdaki **güvenlik okuyucusu** veya **rapor okuyucu** rolünün bir parçası olduğundan emin olun.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hata: uygulamada AAD ' dizin verilerini oku ' izni eksik 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Hata: uygulamada Microsoft Graph API ' tüm denetim günlüğü verilerini oku ' izni eksik

Uygulamanızın doğru izin kümesiyle çalıştığından emin olmak için [Azure Active Directory Raporlama API 'sine erişmek Için önkoşulların](howto-configure-prerequisites-for-reporting-api.md) içindeki adımları izleyin. 

## <a name="next-steps"></a>Sonraki adımlar

* [Sertifikalarla Azure Active Directory raporlama API’sini kullanarak veri alma](tutorial-access-api-with-certificates.md)
* [API başvurusunu denetle](/graph/api/resources/directoryaudit) 
* [Oturum açma Etkinliği raporu API başvurusu](/graph/api/resources/signin)