---
title: Son Kullanıcı kimlik doğrulaması-Azure AD ile Data Lake Storage 1.
description: Azure Active Directory kullanarak Azure Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması elde etme hakkında bilgi edinin
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref
ms.openlocfilehash: a5272f7d580a3f8a68afda9150a1e95c1807eba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103790"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Azure Active Directory kullanarak Azure Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması
> [!div class="op_single_selector"]
> * [Son kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Hizmetten hizmete kimlik doğrulaması](data-lake-store-service-to-service-authenticate-using-active-directory.md)
>
>

Azure Data Lake Storage 1., kimlik doğrulaması için Azure Active Directory kullanır. Data Lake Storage 1. veya Azure Data Lake Analytics ile çalışacak bir uygulama yazmadan önce, Azure Active Directory (Azure AD) ile uygulamanızın kimliğini nasıl doğrulayacağınıza karar vermelisiniz. Kullanılabilen iki ana seçenek şunlardır:

* Son Kullanıcı kimlik doğrulaması (Bu makale)
* Hizmetten hizmete kimlik doğrulaması (yukarıdaki açılan listeden bu seçeneği belirleyin)

Bu seçeneklerin her ikisi de, Data Lake Storage 1. veya Azure Data Lake Analytics yapılan her bir isteğe bağlı olan bir OAuth 2,0 belirteciyle birlikte sağlanmış olması ile sonuçlanır.

Bu makalede, **Son Kullanıcı kimlik doğrulaması için bir Azure AD yerel uygulamasının** nasıl oluşturulacağı ele gösterilmektedir. Hizmetten hizmete kimlik doğrulaması için Azure AD uygulama yapılandırması yönergeleri için [Azure Active Directory kullanarak Data Lake Storage 1. Ile hizmetten hizmete kimlik doğrulaması](./data-lake-store-service-to-service-authenticate-using-active-directory.md)konusuna bakın.

## <a name="prerequisites"></a>Önkoşullar
* Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

* Abonelik KIMLIĞINIZ. Azure portal bunu alabilirsiniz. Örneğin, Data Lake Storage 1. hesabı dikey penceresinde kullanılabilir.

    ![Abonelik KIMLIĞINI al](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Azure AD etki alanı adınız. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. Aşağıdaki ekran görüntüsünde, etki alanı adı **contoso.onmicrosoft.com** ve köşeli AYRAÇLAR içindeki GUID kiracı kimliğidir.

    ![AAD etki alanını al](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Azure kiracı KIMLIĞINIZ. Kiracı KIMLIĞINI alma hakkında yönergeler için bkz. [KIRACı kimliğini alma](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="end-user-authentication"></a>Son kullanıcı kimlik doğrulaması
Bu kimlik doğrulama mekanizması, son kullanıcının Azure AD aracılığıyla uygulamanızda oturum açmasını istiyorsanız önerilen yaklaşımdır. Daha sonra uygulamanız, oturum açan son kullanıcıyla aynı erişim düzeyine sahip Azure kaynaklarına erişebiliyor. Uygulamanızın erişimi sürdürmek için son kullanıcılarınızın kimlik bilgilerini düzenli aralıklarla sağlaması gerekir.

Son Kullanıcı oturum açma işlemi, uygulamanıza erişim belirteci ve yenileme belirteci verilme sonucudur. Erişim belirteci Data Lake Storage 1. veya Data Lake Analytics yapılan her isteğe iliştirilir ve varsayılan olarak bir saat için geçerlidir. Yenileme belirteci yeni bir erişim belirteci almak için kullanılabilir ve varsayılan olarak en fazla iki hafta geçerlidir. Son Kullanıcı oturum açma için iki farklı yaklaşım kullanabilirsiniz.

### <a name="using-the-oauth-20-pop-up"></a>OAuth 2,0 açılır penceresini kullanma
Uygulamanız, son kullanıcının kimlik bilgilerini girebileceği bir OAuth 2,0 yetkilendirme açılır penceresini tetikleyebilirler. Bu açılır pencere, gerekirse Azure AD Iki faktörlü kimlik doğrulaması (2FA) işlemi ile de çalışır.

> [!NOTE]
> Bu yöntem, Python veya Java için Azure AD kimlik doğrulama kitaplığı 'nda (ADAL) henüz desteklenmiyor.
>
>

### <a name="directly-passing-in-user-credentials"></a>Kullanıcı kimlik bilgilerini doğrudan geçirme
Uygulamanız Azure AD 'ye doğrudan Kullanıcı kimlik bilgilerini sağlayabilir. Bu yöntem yalnızca kuruluş KIMLIĞI Kullanıcı hesaplarıyla birlikte kullanılabilir; veya ' de sonlanan hesaplar dahil olmak üzere kişisel/"canlı KIMLIK" Kullanıcı hesaplarıyla uyumlu değildir @outlook.com @live.com . Ayrıca, bu yöntem, Azure AD Iki öğeli kimlik doğrulaması (2FA) gerektiren kullanıcı hesaplarıyla uyumlu değildir.

### <a name="what-do-i-need-for-this-approach"></a>Bu yaklaşım için ne yapmam gerekir?
* Azure AD etki alanı adı. Bu gereksinim, bu makalenin önkoşul bölümünde zaten listelenmiştir.
* Azure AD kiracı KIMLIĞI. Bu gereksinim, bu makalenin önkoşul bölümünde zaten listelenmiştir.
* Azure AD **Yerel uygulaması**
* Azure AD yerel uygulaması için uygulama KIMLIĞI
* Azure AD yerel uygulaması için yeniden yönlendirme URI 'SI
* Yetki verilmiş izinleri ayarlayın


## <a name="step-1-create-an-active-directory-native-application"></a>1. Adım: Active Directory yerel uygulama oluşturma

Azure Active Directory kullanarak Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması için bir Azure AD yerel uygulaması oluşturun ve yapılandırın. Yönergeler için bkz. [Azure AD uygulaması oluşturma](../active-directory/develop/howto-create-service-principal-portal.md).

Bağlantıdaki yönergeleri takip ederken, aşağıdaki ekran görüntüsünde gösterildiği gibi uygulama türü için **Yerel** ' i seçtiğinizden emin olun:

![Web uygulaması oluşturma](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Yerel uygulama oluştur")

## <a name="step-2-get-application-id-and-redirect-uri"></a>2. Adım: uygulama KIMLIĞINI ve yeniden yönlendirme URI 'sini alın

Bkz. uygulama KIMLIĞINI almak için [uygulama kimliğini alma](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) .

Yeniden yönlendirme URI 'sini almak için aşağıdaki adımları uygulayın.

1. Azure portal, **Azure Active Directory**' i seçin, **uygulama kayıtları**' ı tıklatın ve ardından oluşturduğunuz Azure AD yerel uygulamasını bulun ve tıklatın.

2. Uygulamanın **Ayarlar** dikey penceresinde **URI 'leri yeniden yönlendir**' e tıklayın.

    ![Yeniden yönlendirme URI 'SI al](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Görünen değeri kopyalayın.


## <a name="step-3-set-permissions"></a>3. Adım: izinleri ayarlama

1. Azure portal, **Azure Active Directory**' i seçin, **uygulama kayıtları**' ı tıklatın ve ardından oluşturduğunuz Azure AD yerel uygulamasını bulun ve tıklatın.

2. Uygulamanın **Ayarlar** dikey penceresinde **gerekli izinler**' e ve ardından **Ekle**' ye tıklayın.

    ![Yeniden yönlendirme U R I seçeneği verilen ayarlar dikey penceresinin ekran görüntüsü ve bu şekilde adlandırılan gerçek U R ile yeniden yönlendirme U R ı dikey penceresi.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. **API erişimi ekle** dikey PENCERESINDE, **API Seç**' e tıklayın, **Azure Data Lake**' a ve ardından **Seç**' e tıklayın.

    ![API erişimi ekle adlı bir API 'si seçeneği ve Azure Data Lake seçeneği ve Seç seçeneği ile bir API seçin dikey penceresi içeren ekran görüntüsü.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)

4.  **API erişimi ekle** dikey penceresinde **izinleri seç**' e tıklayın, **Data Lake Store tam erişim** vermek Için onay kutusunu işaretleyin ve ardından **Seç**' e tıklayın.

    ![Adlandırılmış izinleri Seç seçeneği ile API erişimi ekle dikey penceresinin ekran görüntüsü ve Azure Data Lake hizmeti için tam erişime sahip olan erişimi etkinleştir dikey penceresi ve seçenek olarak adlandırılan seçim seçeneği.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    **Bitti**’ye tıklayın.

5. **Windows Azure hizmet yönetim API'si** izin vermek için son iki adımı yineleyin.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, bir Azure AD yerel uygulaması oluşturdunuz ve .NET SDK, Java SDK, REST API vb. kullanarak yazdığınız istemci uygulamalarınızda ihtiyacınız olan bilgileri topladı. Artık Data Lake Storage 1. kimlik doğrulaması yapmak için Azure AD Web uygulamasının nasıl kullanılacağına ve ardından mağaza üzerinde başka işlemler gerçekleştirmeye yönelik aşağıdaki makalelere geçebilirsiniz.

* [Java SDK kullanarak Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-java-sdk.md)
* [.NET SDK kullanarak Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-net-sdk.md)
* [Python kullanarak Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-python.md)
* [REST API kullanarak Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-rest-api.md)