---
title: Azure AD 'de kurumsal uygulamalar için rol talebi yapılandırma
titleSuffix: Microsoft identity platform
description: Azure Active Directory 'de kurumsal uygulamalar için SAML belirtecinde verilen rol talebini yapılandırmayı öğrenin
services: active-directory
documentationcenter: ''
author: jeevansd
manager: CelesteDG
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c671626a431a47e5100cf42ca0c9e29ab580ab3a
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803502"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Nasıl yapılır: kurumsal uygulamalar için SAML belirtecinde verilen rol talebini yapılandırma

Azure Active Directory (Azure AD) kullanarak, bir uygulamayı yetkilendirdikten sonra aldığınız yanıt belirtecindeki rol talebi için talep türünü özelleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Dizin kurulumuna sahip bir Azure AD aboneliği.
- Çoklu oturum açma (SSO) etkin olan bir abonelik. SSO 'yu uygulamanızla yapılandırmanız gerekir.

## <a name="when-to-use-this-feature"></a>Bu özelliğin ne zaman kullanılacağı

Uygulamanız bir SAML yanıtında özel rollerin geçirilmesini bekliyorsa, bu özelliği kullanmanız gerekir. Azure AD 'den uygulamanıza geri iletilmesi için gereken sayıda rol oluşturabilirsiniz.

## <a name="create-roles-for-an-application"></a>Bir uygulama için roller oluşturma

1. [Azure Portal](https://portal.azure.com)sol bölmedeki **Azure Active Directory** simgesini seçin.

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

    a. Kiracınız için genel yönetici veya coadmin (kimlik bilgilerini kullanarak Graph Explorer sitesinde oturum açın.

    b. Rolleri oluşturmak için yeterli izinlere sahip olmanız gerekir. İzinleri almak için **izinleri değiştir** ' i seçin.

      !["İzinleri değiştir" düğmesi](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Listeden aşağıdaki izinleri seçin (henüz yoksa) ve **Izinleri Değiştir**' i seçin.

      ![İzinler listesi ve "Izinleri Değiştir" düğmesi](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > Dizin okuma ve yazma için genel yönetici izinlerine ihtiyaç duyduğumuz için, bulut uygulaması Yöneticisi ve uygulama Yöneticisi rolü bu senaryoda çalışmayacaktır.

    d. Onayı kabul edin. Sistemde yeniden oturum açtınız.

    e. Sürümü **Beta**olarak değiştirin ve aşağıdaki sorguyu kullanarak kiracınızdan hizmet sorumluları listesini getirin:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Birden çok dizin kullanıyorsanız şu modele uyun: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Hizmet sorumlularını getirmeye yönelik sorgu ile grafik Gezgini iletişim kutusu](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Müşterilerin hizmette bazı kesintileri görebilmesi için API 'Leri yükseltme sürecimiz zaten var.

    f. Getirilen hizmet sorumluları listesinden, değiştirmeniz gereken bir tane alın. Uygulamayı listelenen tüm hizmet sorumlularından aramak için CTRL + F de kullanabilirsiniz. **Özellikler** sayfasından KOPYALADıĞıNıZ nesne kimliğini arayın ve hizmet sorumlusuna ulaşmak için aşağıdaki sorguyu kullanın:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Değiştirmeniz gereken hizmet sorumlusunu alma sorgusu](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Hizmet sorumlusu nesnesinden **Approles** özelliğini ayıklayın.

      ![AppRoles özelliğinin ayrıntıları](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Özel uygulamayı (Azure Marketi uygulaması değil) kullanıyorsanız, iki varsayılan rol görürsünüz: User ve msiam_access. Market uygulaması için msiam_access tek varsayılan roldür. Varsayılan rollerde herhangi bir değişiklik yapmanız gerekmez.

    h. Uygulamanız için yeni roller oluşturun.

      Aşağıdaki JSON **Approles** nesnesine bir örnektir. Uygulamanız için istediğiniz rolleri eklemek için benzer bir nesne oluşturun.

      ```
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

      > [!Note]
      > Düzeltme Eki işlemi için msiam_access sonra yalnızca yeni roller ekleyebilirsiniz. Ayrıca, kuruluşunuzun ihtiyacı olarak çok sayıda rol ekleyebilirsiniz. Azure AD, bu rollerin değerini SAML yanıtında talep değeri olarak gönderir. Yeni rollerin KIMLIĞI için GUID değerlerini oluşturmak üzere, [bunun](https://www.guidgenerator.com/) gibi Web araçlarını kullanın

    i. Graph Explorer 'a geri dönün ve yöntemi **Al** 'dan **Patch**öğesine değiştirin. Önceki örnekte gösterildiği gibi **Approles** özelliğini güncelleştirerek istenen rollere sahip olmak için hizmet sorumlusu nesnesine yama yapın. Düzeltme Eki işlemini yürütmek için **Sorguyu Çalıştır** ' ı seçin. Başarı iletisi rolün oluşturulmasını onaylar.

      ![Başarı iletisiyle düzeltme eki işlemi](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Hizmet sorumlusu daha fazla rolle düzeltme eki uygulandıktan sonra, kullanıcıları ilgili rollere atayabilirsiniz. Portala gidip uygulamaya göz atarak kullanıcıları atayabilirsiniz. **Kullanıcılar ve gruplar** sekmesini seçin. Bu sekmede, uygulamaya zaten atanmış olan tüm kullanıcılar ve gruplar listelenir. Yeni rollere yeni kullanıcılar ekleyebilirsiniz. Ayrıca, mevcut bir kullanıcıyı seçebilir ve rolü değiştirmek için **Düzenle** ' yi seçebilirsiniz.

    !["Kullanıcılar ve gruplar" sekmesi](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Rolü herhangi bir kullanıcıya atamak için yeni rolü seçin ve sayfanın altındaki **ata** düğmesini seçin.

    !["Atamayı Düzenle" bölmesi ve "rol Seç" bölmesi](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Yeni rolleri görmek için Azure portal oturumunuzu yenilemeniz gerekir.

8. Rol talebinin özelleştirilmiş bir eşlemesini tanımlamak için **öznitelikler** tablosunu güncelleştirin.

9. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde AŞAĞıDAKI tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:

    | Öznitelik adı | Öznitelik değeri |
    | -------------- | ----------------|
    | Rol adı  | Kullanıcı. atandroles |

    >[!NOTE]
    >Rol talep değeri null ise, Azure AD bu değeri belirtece göndermeyecektir ve bu varsayılan olarak tasarım başına olur.

    a. **Kullanıcı öznitelikleri & talepler** iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

      !["Öznitelik Ekle" düğmesi](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. **Kullanıcı taleplerini Yönet** iletişim kutusunda, **yeni talep Ekle**' ye tıklayarak SAML belirteci özniteliğini ekleyin.

      !["Öznitelik Ekle" düğmesi](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      !["Öznitelik Ekle" bölmesi](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. **Ad** kutusuna, gereken şekilde öznitelik adını yazın. Bu örnek, talep adı olarak **rol adını** kullanır.

    d. **Ad alanı** kutusunu boş bırakın.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. **Kaydet**’i seçin.

10. Uygulamanızı bir kimlik sağlayıcısı tarafından başlatılan çoklu bir oturum açma ile test etmek için, [erişim panelinde](https://myapps.microsoft.com) oturum açın ve uygulama kutucuğunu seçin. SAML belirtecinde, verdiğiniz talep adına sahip kullanıcı için atanan tüm rolleri görmeniz gerekir.

## <a name="update-an-existing-role"></a>Mevcut bir rolü güncelleştir

Mevcut bir rolü güncelleştirmek için aşağıdaki adımları gerçekleştirin:

1. [Microsoft Graph Gezginini](https://developer.microsoft.com/graph/graph-explorer)açın.

2. Kiracınız için genel yönetici veya coadmin (kimlik bilgilerini kullanarak Graph Explorer sitesinde oturum açın.

3. Sürümü **Beta**olarak değiştirin ve aşağıdaki sorguyu kullanarak kiracınızdan hizmet sorumluları listesini getirin:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Birden çok dizin kullanıyorsanız şu modele uyun: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Hizmet sorumlularını getirmeye yönelik sorgu ile grafik Gezgini iletişim kutusu](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Getirilen hizmet sorumluları listesinden, değiştirmeniz gereken bir tane alın. Uygulamayı listelenen tüm hizmet sorumlularından aramak için CTRL + F de kullanabilirsiniz. **Özellikler** sayfasından KOPYALADıĞıNıZ nesne kimliğini arayın ve hizmet sorumlusuna ulaşmak için aşağıdaki sorguyu kullanın:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Değiştirmeniz gereken hizmet sorumlusunu alma sorgusu](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Hizmet sorumlusu nesnesinden **Approles** özelliğini ayıklayın.

    ![AppRoles özelliğinin ayrıntıları](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Mevcut rolü güncelleştirmek için aşağıdaki adımları kullanın.

    !["Description" ve "DisplayName" vurgulanmış "PATCH" için istek gövdesi](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Yöntemi **Al** 'dan **Patch**öğesine değiştirin.

    b. Mevcut rolleri kopyalayın ve **Istek gövdesi**altına yapıştırın.

    c. Rol açıklaması, rol değeri veya rol görünen adını gerektiği şekilde güncelleştirerek bir rolün değerini güncelleştirin.

    d. Gerekli tüm rolleri güncelleştirdikten sonra **Sorguyu Çalıştır**' ı seçin.

## <a name="delete-an-existing-role"></a>Mevcut bir rolü Sil

Mevcut bir rolü silmek için aşağıdaki adımları gerçekleştirin:

1. [Microsoft Graph Gezginini](https://developer.microsoft.com/graph/graph-explorer) başka bir pencerede açın.

2. Kiracınız için genel yönetici veya coadmin (kimlik bilgilerini kullanarak Graph Explorer sitesinde oturum açın.

3. Sürümü **Beta**olarak değiştirin ve aşağıdaki sorguyu kullanarak kiracınızdan hizmet sorumluları listesini getirin:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Birden çok dizin kullanıyorsanız şu modele uyun: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Hizmet sorumluları listesini getirme sorgusuyla grafik Gezgini iletişim kutusu](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Getirilen hizmet sorumluları listesinden, değiştirmeniz gereken bir tane alın. Uygulamayı listelenen tüm hizmet sorumlularından aramak için CTRL + F de kullanabilirsiniz. **Özellikler** sayfasından KOPYALADıĞıNıZ nesne kimliğini arayın ve hizmet sorumlusuna ulaşmak için aşağıdaki sorguyu kullanın:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Değiştirmeniz gereken hizmet sorumlusunu alma sorgusu](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Hizmet sorumlusu nesnesinden **Approles** özelliğini ayıklayın.

    ![Hizmet sorumlusu nesnesinden appRoles özelliğinin ayrıntıları](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Varolan rolü silmek için aşağıdaki adımları kullanın.

    ![IsEnabled, false olarak ayarlanan "PATCH" için istek gövdesi](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Yöntemi **Al** 'dan **Patch**öğesine değiştirin.

    b. Uygulamadan mevcut rolleri kopyalayın ve **Istek gövdesi**altına yapıştırın.

    c. Silmek istediğiniz rol için **IsEnabled** değerini **false** olarak ayarlayın.

    d. **Sorguyu Çalıştır**' ı seçin.

    > [!NOTE]
    > Msiam_access rolüne sahip olduğunuzdan ve KIMLIğIN oluşturulan rolle aynı olduğundan emin olun.

7. Rol devre dışı bırakıldıktan sonra, bu rol bloğunu **Approles** bölümünden silin. Yöntemi **Düzeltme Eki**olarak tutun ve **Sorguyu Çalıştır**' ı seçin.

8. Sorguyu çalıştırdıktan sonra, rol silinir.

    > [!NOTE]
    > Rolün kaldırılabilmesi için önce devre dışı bırakılması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Ek adımlar için bkz. [uygulama belgeleri](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
