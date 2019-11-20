---
title: Azure Active Directory B2C özel öznitelikleri tanımlayın | Microsoft Docs
description: Müşterileriniz hakkında bilgi toplamak için Azure Active Directory B2C ' de uygulamanız için özel öznitelikler tanımlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e6b0eac0b8cf7f61d76f90a4f769ba11abab6999
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065670"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel öznitelikleri tanımlama

 Müşterilere yönelik her uygulamanın, toplanması gereken bilgiler için benzersiz gereksinimleri vardır. Azure Active Directory B2C (Azure AD B2C) kiracınız, belirtilen ad, soyadı, şehir ve posta kodu gibi özniteliklerde depolanan yerleşik bir bilgi kümesiyle gelir. Azure AD B2C, her müşteri hesabında depolanan özniteliklerin kümesini genişletebilirsiniz.

 [Azure Portal](https://portal.azure.com/) özel öznitelikler oluşturabilir ve bunları kaydolma Kullanıcı akışlarınızda, kaydolma veya oturum açma Kullanıcı akışlarınızda veya profil düzenlemesi Kullanıcı akışlarınızda kullanabilirsiniz. Ayrıca, [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)kullanarak bu öznitelikleri okuyabilir ve yazabilirsiniz. Azure AD B2C özel öznitelikler [Azure AD Graph API Dizin şeması uzantılarını](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions)kullanır.

> [!NOTE]
> Azure AD B2C kiracının sorgulanmasında daha yeni [MICROSOFT Graph API](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) desteği hala geliştirme aşamasındadır.
>

## <a name="create-a-custom-attribute"></a>Özel öznitelik oluştur

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olmak için Azure portalın sağ üst köşesinde bu dizine geçin. Abonelik bilgilerinizi ve ardından **Dizin Değiştir**’i seçin.

    ![Azure AD B2C kiracınıza geçiş yapma](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Kiracınızı içeren dizini seçin.

    ![Dizin ve abonelik filtresi 'nde B2C kiracısı vurgulandı](./media/active-directory-b2c-reference-custom-attr/select-directory.PNG)

3. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
4. **Kullanıcı öznitelikleri**' ni seçin ve ardından **Ekle**' yi seçin.
5. Özel öznitelik için bir **ad** sağlayın (örneğin, "shoesize")
6. Bir **veri türü**seçin. Yalnızca **String**, **Boolean**ve **Int** kullanılabilir.
7. İsteğe bağlı olarak, bilgilendirici amaçlar için bir **Açıklama** girin.
8. **Oluştur**'a tıklayın.

Özel öznitelik artık **Kullanıcı öznitelikleri** listesinde ve Kullanıcı akışlarınızda kullanılmak üzere kullanılabilir. Özel bir öznitelik, **Kullanıcı öznitelikleri**listesine eklediğinizde değil, yalnızca herhangi bir Kullanıcı akışında kullanıldığı zaman oluşturulur.


## <a name="use-a-custom-attribute-in-your-user-flow"></a>Kullanıcı akışındaki özel bir öznitelik kullanma

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
2. İlkeyi (örneğin, "B2C_1_SignupSignin") açmak için seçin.
4. **Kullanıcı öznitelikleri** ' ni seçin ve ardından özel özniteliği seçin (örneğin, "shoesize"). **Kaydet**’e tıklayın.
5. **Uygulama talepleri** ' ı seçin ve ardından özel özniteliği seçin.
6. **Kaydet**’e tıklayın.

Yeni oluşturulan özel özniteliği kullanan bir Kullanıcı akışı kullanarak yeni bir kullanıcı oluşturduktan sonra, nesne [Azure AD Graph Explorer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)'da sorgulanabilir. Alternatif olarak, müşteri deneyimini doğrulamak için Kullanıcı akışındaki [**Kullanıcı akışını Çalıştır**](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) özelliğini de kullanabilirsiniz. Şimdi kayıt yolculuğu sırasında toplanan öznitelikler listesinde **Showesize** ' i görmeniz ve uygulamanıza geri gönderilen belirteçte onu görmeniz gerekir.

