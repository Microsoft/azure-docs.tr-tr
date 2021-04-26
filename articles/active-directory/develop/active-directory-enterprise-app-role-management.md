---
title: Kurumsal Azure AD uygulamaları için rol talebi yapılandırma | Mavisi
titleSuffix: Microsoft identity platform
description: Azure Active Directory 'de kurumsal uygulamalar için SAML belirtecinde verilen rol talebini yapılandırmayı öğrenin
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: 3c1b4b3d57bdce4a66adb35717f46d8989ee2453
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648824"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Nasıl yapılır: kurumsal uygulamalar için SAML belirtecinde verilen rol talebini yapılandırma

Azure Active Directory (Azure AD) kullanarak, bir uygulamayı yetkilendirdikten sonra aldığınız yanıt belirtecindeki rol talebi için talep türünü özelleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Dizin kurulumuna sahip bir Azure AD aboneliği.
- Çoklu oturum açma (SSO) etkin olan bir abonelik. SSO 'yu uygulamanızla yapılandırmanız gerekir.

> [!NOTE]
> Bu makalede, Azure AD 'de API 'Leri kullanarak hizmet sorumlusu üzerinde uygulama rollerinin nasıl oluşturulacağı/silineceği ve silineceği açıklanmaktadır. Uygulama rolleri için yeni kullanıcı arabirimini kullanmak istiyorsanız [lütfen ayrıntılara bakın](./howto-add-app-roles-in-azure-ad-apps.md).

## <a name="when-to-use-this-feature"></a>Bu özelliğin ne zaman kullanılacağı

Uygulamanız Azure AD tarafından döndürülen SAML yanıtında özel roller bekliyorsa bu özelliği kullanın. İhtiyaç duyduğunuz kadar çok sayıda rol oluşturabilirsiniz.

## <a name="create-roles-for-an-application"></a>Bir uygulama için roller oluşturma

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>sol bölmedeki **Azure Active Directory** simgesini seçin.

    ![Azure Active Directory simgesi][1]

2. **Kurumsal uygulamalar**' ı seçin. Ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar bölmesi][2]

3. Yeni bir uygulama eklemek için iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesini seçin.

    !["Yeni uygulama" düğmesi][3]

4. Arama kutusuna uygulamanızın adını yazın ve ardından sonuç panelinden uygulamanızı seçin. Uygulamayı eklemek için **Ekle** düğmesini seçin.

    ![Sonuçlar listesindeki uygulama](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Uygulama eklendikten sonra **Özellikler** sayfasına gidin ve nesne kimliğini kopyalayın.

    ![Özellikler sayfası](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. [Microsoft Graph Gezginini](https://developer.microsoft.com/graph/graph-explorer) başka bir pencerede açın ve aşağıdaki adımları uygulayın:

    1. Kiracınız için genel yönetici veya coadmin (kimlik bilgilerini kullanarak Graph Explorer sitesinde oturum açın.

    1. Rolleri oluşturmak için yeterli izinlere sahip olmanız gerekir. İzinleri almak için **izinleri değiştir** ' i seçin.

        !["İzinleri değiştir" düğmesi](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

        > [!NOTE]
        > Dizin okuma ve yazma için genel yönetici izinlerine ihtiyaç duyduğumuz için, bulut uygulaması Yöneticisi ve uygulama Yöneticisi rolü bu senaryoda çalışmayacaktır.

    1. Listeden aşağıdaki izinleri seçin (henüz yoksa) ve **Izinleri Değiştir**' i seçin.

        ![İzinler listesi ve "Izinleri Değiştir" düğmesi](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    1. Onayı kabul edin. Sistemde yeniden oturum açtınız.

    1. Sürümü **Beta** olarak değiştirin ve aşağıdaki sorguyu kullanarak kiracınızdan hizmet sorumluları listesini getirin:

        `https://graph.microsoft.com/beta/servicePrincipals`

        Birden çok dizin kullanıyorsanız şu modele uyun: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

        ![Hizmet sorumlularını getirmeye yönelik sorgu ile grafik Gezgini iletişim kutusu](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

    1. Getirilen hizmet sorumluları listesinden, değiştirmeniz gereken bir tane alın. Uygulamayı listelenen tüm hizmet sorumlularından aramak için CTRL + F de kullanabilirsiniz. **Özellikler** sayfasından KOPYALADıĞıNıZ nesne kimliğini arayın ve hizmet sorumlusuna ulaşmak için aşağıdaki sorguyu kullanın:

        `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

        ![Değiştirmeniz gereken hizmet sorumlusunu alma sorgusu](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    1. Hizmet sorumlusu nesnesinden **Approles** özelliğini ayıklayın.

        ![AppRoles özelliğinin ayrıntıları](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

        Özel uygulamayı (Azure Marketi uygulaması değil) kullanıyorsanız, iki varsayılan rol görürsünüz: Kullanıcı ve msiam_access. Market uygulaması için tek varsayılan rol msiam_access. Varsayılan rollerde herhangi bir değişiklik yapmanız gerekmez.

    1. Uygulamanız için yeni roller oluşturun.

        Aşağıdaki JSON **Approles** nesnesine bir örnektir. Uygulamanız için istediğiniz rolleri eklemek için benzer bir nesne oluşturun.

        ```json
        {
          "appRoles": [
            {
               "allowedMemberTypes": [
                  "User"
                ],
                "description": "msiam_access",
                "displayName": "msiam_access",
                "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
                "isEnabled": true,
                "origin": "Application",
                "value": null
            },
            {
                "allowedMemberTypes": [
                    "User"
                ],
                "description": "Administrators Only",
                "displayName": "Admin",
                "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
                "isEnabled": true,
                "origin": "ServicePrincipal",
                "value": "Administrator"
            }
         ]
        }
        ```

        Düzeltme Eki işlemi için msiam_access sonrasında yalnızca yeni roller ekleyebilirsiniz. Ayrıca, kuruluşunuzun ihtiyacı olarak çok sayıda rol ekleyebilirsiniz. Azure AD, bu rollerin değerini SAML yanıtında talep değeri olarak gönderir. Yeni rollerin KIMLIĞI için GUID değerlerini oluşturmak üzere, [bunun](https://www.guidgenerator.com/) gibi Web araçlarını kullanın

    1. Graph Explorer 'a geri dönün ve yöntemi **Al** 'dan **Patch** öğesine değiştirin. Önceki örnekte gösterildiği gibi **Approles** özelliğini güncelleştirerek istenen rollere sahip olmak için hizmet sorumlusu nesnesine yama yapın. Düzeltme Eki işlemini yürütmek için **Sorguyu Çalıştır** ' ı seçin. Başarı iletisi rolün oluşturulmasını onaylar.

        ![Başarı iletisiyle düzeltme eki işlemi](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

1. Hizmet sorumlusu daha fazla rolle düzeltme eki uygulandıktan sonra, kullanıcıları ilgili rollere atayabilirsiniz. Portala gidip uygulamaya göz atarak kullanıcıları atayabilirsiniz. **Kullanıcılar ve gruplar** sekmesini seçin. Bu sekmede, uygulamaya zaten atanmış olan tüm kullanıcılar ve gruplar listelenir. Yeni rollere yeni kullanıcılar ekleyebilirsiniz. Ayrıca, mevcut bir kullanıcıyı seçebilir ve rolü değiştirmek için **Düzenle** ' yi seçebilirsiniz.

    !["Kullanıcılar ve gruplar" sekmesi](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Rolü herhangi bir kullanıcıya atamak için yeni rolü seçin ve sayfanın altındaki **ata** düğmesini seçin.

    !["Atamayı Düzenle" bölmesi ve "rol Seç" bölmesi](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    
    Yeni rolleri görmek için Azure portal oturumunuzu yenileyin.

1. Rol talebinin özelleştirilmiş bir eşlemesini tanımlamak için **öznitelikler** tablosunu güncelleştirin.

1. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde AŞAĞıDAKI tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:

    | Öznitelik adı | Öznitelik değeri |
    | -------------- | ----------------|
    | Rol adı  | Kullanıcı. atandroles |

    Rol talep değeri null ise, Azure AD bu değeri belirtece göndermeyecektir ve bu varsayılan olarak tasarım başına olur.

    1. **Kullanıcı öznitelikleri & talepler** iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

        ![Kullanıcı öznitelikleri & talepler iletişim kutusunu açmak için kullanılan düzenleme simgesini vurgulayan ekran görüntüsü.](./media/active-directory-enterprise-app-role-management/editattribute.png)

    1. **Kullanıcı taleplerini Yönet** iletişim kutusunda, **yeni talep Ekle**' ye tıklayarak SAML belirteci özniteliğini ekleyin.

        !["Öznitelik Ekle" düğmesi](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

        !["Öznitelik Ekle" bölmesi](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    1. **Ad** kutusuna, gereken şekilde öznitelik adını yazın. Bu örnek, talep adı olarak **rol adını** kullanır.

    1. **Ad alanı** kutusunu boş bırakın.

    1. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    1. **Kaydet**’i seçin.

10. Uygulamanızı bir kimlik sağlayıcısı tarafından başlatılan çoklu bir oturum açma ile test etmek için, [erişim panelinde](https://myapps.microsoft.com) oturum açın ve uygulama kutucuğunu seçin. SAML belirtecinde, verdiğiniz talep adına sahip kullanıcı için atanan tüm rolleri görmeniz gerekir.

## <a name="update-an-existing-role"></a>Mevcut bir rolü güncelleştir

Mevcut bir rolü güncelleştirmek için aşağıdaki adımları gerçekleştirin:

1. [Microsoft Graph Gezginini](https://developer.microsoft.com/graph/graph-explorer)açın.

1. Kiracınız için genel yönetici veya coadmin (kimlik bilgilerini kullanarak Graph Explorer sitesinde oturum açın.

1. Sürümü **Beta** olarak değiştirin ve aşağıdaki sorguyu kullanarak kiracınızdan hizmet sorumluları listesini getirin:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Birden çok dizin kullanıyorsanız şu modele uyun: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Hizmet sorumlularını getirmeye yönelik sorgu ile grafik Gezgini iletişim kutusu](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. Getirilen hizmet sorumluları listesinden, değiştirmeniz gereken bir tane alın. Uygulamayı listelenen tüm hizmet sorumlularından aramak için CTRL + F de kullanabilirsiniz. **Özellikler** sayfasından KOPYALADıĞıNıZ nesne kimliğini arayın ve hizmet sorumlusuna ulaşmak için aşağıdaki sorguyu kullanın:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Değiştirmeniz gereken hizmet sorumlusunu alma sorgusu](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. Hizmet sorumlusu nesnesinden **Approles** özelliğini ayıklayın.

    ![AppRoles özelliğinin ayrıntıları](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

1. Mevcut rolü güncelleştirmek için aşağıdaki adımları kullanın.

    !["Description" ve "DisplayName" vurgulanmış "PATCH" için istek gövdesi](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    1. Yöntemi **Al** 'dan **Patch** öğesine değiştirin.

    1. Mevcut rolleri kopyalayın ve **Istek gövdesi** altına yapıştırın.

    1. Rol açıklaması, rol değeri veya rol görünen adını gerektiği şekilde güncelleştirerek bir rolün değerini güncelleştirin.

    1. Gerekli tüm rolleri güncelleştirdikten sonra **Sorguyu Çalıştır**' ı seçin.

## <a name="delete-an-existing-role"></a>Mevcut bir rolü Sil

Mevcut bir rolü silmek için aşağıdaki adımları gerçekleştirin:

1. [Microsoft Graph Gezginini](https://developer.microsoft.com/graph/graph-explorer) başka bir pencerede açın.

1. Kiracınız için genel yönetici veya coadmin (kimlik bilgilerini kullanarak Graph Explorer sitesinde oturum açın.

1. Sürümü **Beta** olarak değiştirin ve aşağıdaki sorguyu kullanarak kiracınızdan hizmet sorumluları listesini getirin:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Birden çok dizin kullanıyorsanız şu modele uyun: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Hizmet sorumluları listesini getirme sorgusuyla grafik Gezgini iletişim kutusu](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. Getirilen hizmet sorumluları listesinden, değiştirmeniz gereken bir tane alın. Uygulamayı listelenen tüm hizmet sorumlularından aramak için CTRL + F de kullanabilirsiniz. **Özellikler** sayfasından KOPYALADıĞıNıZ nesne kimliğini arayın ve hizmet sorumlusuna ulaşmak için aşağıdaki sorguyu kullanın:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Değiştirmeniz gereken hizmet sorumlusunu alma sorgusu](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. Hizmet sorumlusu nesnesinden **Approles** özelliğini ayıklayın.

    ![Hizmet sorumlusu nesnesinden appRoles özelliğinin ayrıntıları](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

1. Varolan rolü silmek için aşağıdaki adımları kullanın.

    ![IsEnabled, false olarak ayarlanan "PATCH" için istek gövdesi](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    1. Yöntemi **Al** 'dan **Patch** öğesine değiştirin.

    1. Uygulamadan mevcut rolleri kopyalayın ve **Istek gövdesi** altına yapıştırın.

    1. Silmek istediğiniz rol için **IsEnabled** değerini **false** olarak ayarlayın.

    1. **Sorgu Çalıştır**'ı seçin.

    Msiam_access rolüne sahip olduğunuzdan ve KIMLIğIN oluşturulan rolle aynı olduğundan emin olun.

1. Rol devre dışı bırakıldıktan sonra, bu rol bloğunu **Approles** bölümünden silin. Yöntemi **Düzeltme Eki** olarak tutun ve **Sorguyu Çalıştır**' ı seçin.

1. Sorguyu çalıştırdıktan sonra, rol silinir.

    Rolün kaldırılabilmesi için önce devre dışı bırakılması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Ek adımlar için bkz. [uygulama belgeleri](../saas-apps/tutorial-list.md).

<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png