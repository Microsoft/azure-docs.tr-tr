---
title: 'Öğretici: birden çok hesaba bağlanmak için Amazon Web Services tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure AD ve Amazon Web Services (eski öğretici) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 0ddcb239ba106bcdc2f0a29d68eb395876fadc06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384121"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>Öğretici: Amazon Web Services ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) Amazon Web Services (AWS) (eski öğretici) ile tümleştirmeyi öğreneceksiniz.

Bu tümleştirme aşağıdaki avantajları sağlar:

- AWS 'ye erişimi olan Azure AD 'de denetim yapabilirsiniz.
- Kullanıcılarınızın Azure AD hesaplarıyla çoklu oturum açma (SSO) kullanarak AWS 'de otomatik olarak oturum açmasını sağlayabilirsiniz.
- Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz Azure portal.

![AWS ile Azure AD tümleştirmesi diyagramı.](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Bir AWS uygulamasını tüm AWS hesaplarınıza *bağlanmanızı öneririz* . Bunun yerine, AWS hesabınızın birden çok örneğini Azure AD 'de AWS uygulamalarının birden çok örneğine yapılandırmak için [AWS Ile Azure AD SSO tümleştirmesi](./amazon-web-service-tutorial.md) kullanmanızı öneririz. 

Aşağıdaki nedenlerden dolayı bir AWS uygulamasının tüm AWS hesaplarınıza *bağlanmasını öneririz* :

* Bu yaklaşım yalnızca az sayıda AWS hesabı ve rolüne sahipseniz kullanın, çünkü bu model AWS hesabı sayısı ve içerdikleri roller artdıkça ölçeklenebilir değildir. Yaklaşım, Azure AD Kullanıcı sağlama ile AWS rolü-içeri aktarma işlevini kullanmaz, bu nedenle rolleri el ile eklemeniz, güncelleştirmeniz veya silmeniz gerekir. 

* Tüm rollerin uygulamaya yama yapmak için Microsoft Graph Explorer yaklaşımını kullanmanız gerekir. Bildirim dosyası yaklaşımını kullanmanızı önermiyoruz.

* Müşteriler tek bir AWS uygulaması için 1.200 eklendikten sonra, uygulama rollerinin bir bütün olarak bir işlem, boyutla ilgili hataları atma işlemini başlatır. Uygulama nesnesi için sabit boyut sınırı vardır.

* Hesapların herhangi birine eklendikçe rolleri el ile güncelleştirmeniz gerekir. Bu, bir *ekleme* yaklaşımına değil, bir *değiştirme* yaklaşımıdır. Ayrıca, hesap numaralarınız büyüyordur bu, hesaplar ve rollerle bir *n* &times; *n* ilişki haline gelir.

* Tüm AWS hesapları aynı Federasyon meta veri XML dosyasını kullanır. Sertifika geçişi sırasında, tüm AWS hesaplarında aynı anda sertifikayı güncelleştirme büyük bir alıştırma olabilir.

## <a name="prerequisites"></a>Önkoşullar

AWS ile Azure AD tümleştirmesini yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD aboneliğiniz yoksa bir [aylık deneme sürümü](https://azure.microsoft.com/pricing/free-trial/)alabilirsiniz.
* AWS SSO özellikli bir abonelik.

> [!NOTE]
> Gerekli olmadığı takdirde, bu öğreticideki adımları üretim ortamında test etmenizi önermiyoruz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

AWS, SP tarafından başlatılan ve ıDP tarafından başlatılan SSO 'yu destekler.

## <a name="add-aws-from-the-gallery"></a>Galeriden AWS ekleme

AWS 'nin tümleştirmesini Azure AD ile yapılandırmak için galerisinden AWS 'yi, yönetilen hizmet olarak yazılım (SaaS) uygulamaları listenize eklersiniz.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak Azure portal oturum açın.
1. Sol bölmede, çalışmak istediğiniz Azure AD hizmeti ' ni seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Amazon Web Services** yazın.
1. Sonuçlar listesinde **Amazon Web Services**' yi seçin ve ardından uygulamayı ekleyin. Birkaç saniye içinde, uygulama kiracınıza eklenir.

1. **Özellikler** bölmesine gidin ve ardından **nesne kimliği** kutusunda görüntülenen değeri kopyalayın.

    ![Özellikler bölmesindeki nesne KIMLIĞI kutusunun ekran görüntüsü.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma ve test etme

Bu bölümde, "Britta Simon" adlı bir test kullanıcısına göre AWS ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.

Çoklu oturum açma 'nın çalışması için Azure AD 'nin AWS 'deki karşıdaki kullanıcının Azure AD kullanıcısına ne olduğunu bilmeleri gerekir. Diğer bir deyişle, Azure AD kullanıcısı ve AWS 'deki aynı kullanıcı arasındaki bir bağlantı ilişkisinin oluşturulması gerekir.

AWS 'de, bağlantı ilişkisini kurmak için AWS **Kullanıcı** adının değeri olarak Azure AD 'de **Kullanıcı adı** değerini atayın.

AWS ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdakileri yapın:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
1. Uygulama tarafında SSO ayarlarını yapılandırmak için **[AWS SSO 'Yu yapılandırın](#configure-aws-sso)** .
1. Yapılandırmanın çalıştığını doğrulamak için **[test SSO 'su](#test-sso)** .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Bu bölümde, Azure portal Azure AD SSO 'yu etkinleştirin ve aşağıdaki işlemleri gerçekleştirerek AWS uygulamanızda SSO 'yu yapılandırın:

1. Azure portal, **Amazon Web Services (AWS)** uygulama tümleştirme sayfasının sol bölmesinde, **Çoklu oturum açma**' yı seçin.

    !["Çoklu oturum açma" komutunun ekran görüntüsü.](common/select-sso.png)

1. Çoklu oturum **açma yöntemi seçin** bölmesinde, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    !["Çoklu oturum açma yöntemi seçin" bölmesinin ekran görüntüsü.](common/select-saml-option.png)

1. **SAML Ile tek Sign-On ayarla** bölmesinde **Düzenle** düğmesini (kurşun kalem simgesi) seçin.

    !["SAML ile tekli Sign-On ayarlama" bölmesindeki Düzenle düğmesinin ekran görüntüsü.](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölmesi açılır. Uygulama Azure ile önceden tümleştirildiği için bu bölümü atlayın. **Kaydet**’i seçin.

   AWS uygulaması, belirli bir biçimde SAML onayları bekliyor. Bu özniteliklerin değerlerini, **uygulama tümleştirme** sayfasındaki **Kullanıcı öznitelikleri & talepler** bölümünde yönetebilirsiniz. 
   
1. **SAML Ile tek Sign-On ayarlama** sayfasında **Düzenle** düğmesini seçin.

    !["Kullanıcı öznitelikleri" bölmesindeki Düzenle düğmesinin ekran görüntüsü.](common/edit-attribute.png)

1. **Kullanıcı öznitelikleri** bölmesinin **Kullanıcı talepleri** bölümünde, AŞAĞıDAKI tablodaki değerleri kullanarak SAML belirteci özniteliğini yapılandırın:

    | Name  | Kaynak özniteliği  | Ad Alanı |
    | --------------- | --------------- | --------------- |
    | Roleoturumadı | User. UserPrincipalName | `https://aws.amazon.com/SAML/Attributes` |
    | Rol | Kullanıcı. atandroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | "900 saniye (15 dakika) ila 43200 saniye (12 saat) arasında bir değer girin" |  `https://aws.amazon.com/SAML/Attributes` |
  
   a. **Yeni talep Ekle** ' yi seçin ve ardından **Kullanıcı taleplerini Yönet** bölmesinde şunları yapın:

   !["Kullanıcı talepleri" bölmesinde "yeni talep Ekle" ve "Kaydet" düğmelerinin ekran görüntüsü.](common/new-save-attribute.png)

   !["Kullanıcı taleplerini yönetme" bölmesinin ekran görüntüsü.](common/new-attribute-details.png)

   b. **Ad** kutusuna öznitelik adını girin.  

   c. **Ad alanı** kutusuna ad alanı değerini girin.

   d. **Kaynak** için **öznitelik**' i seçin.

   e. **Kaynak özniteliği** açılan listesinde, özniteliğini seçin.

   f. **Tamam**' ı ve ardından **Kaydet**' i seçin.

   >[!NOTE]
   >Azure AD 'deki roller hakkında daha fazla bilgi için bkz. [uygulamanıza uygulama rolleri ekleme ve bunları belirtece alma](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, Federasyon meta veri xml dosyasını indirmek için **İndir** ' i seçin ve ardından bilgisayarınıza kaydedin.

   !["SAML meta verileri XML" indirme bağlantısının "SAML Imzalama sertifikası" bölmesinde ekran görüntüsü.](common/metadataxml.png)

### <a name="configure-aws-sso"></a>AWS SSO 'yu yapılandırma

1. Yeni bir tarayıcı penceresinde, AWS şirket sitenizde yönetici olarak oturum açın.

1. **AWS giriş** simgesini seçin.

   !["AWS giriş" simgesinin ekran görüntüsü.][11]

1. **AWS Hizmetleri** bölmesinde, **güvenlik, kimlik & uyumluluğu** altında, **IAM (kimlik & erişim yönetimi)** öğesini seçin.

    !["AWS Hizmetleri" bölmesindeki "kimlik ve erişim yönetimi" bağlantısının ekran görüntüsü.][12]

1. Sol bölmede, **kimlik sağlayıcıları**' nı seçin ve ardından **sağlayıcı oluştur**' u seçin.

    !["Sağlayıcı oluşturma" düğmesinin ekran görüntüsü.][13]

1. **Sağlayıcıyı yapılandır** bölmesinde şunları yapın:

    !["Sağlayıcıyı yapılandırma" bölmesinin ekran görüntüsü.][14]

    a. **Sağlayıcı türü** aşağı açılan listesinde **SAML**' yi seçin.

    b. **Sağlayıcı adı** kutusuna bir sağlayıcı adı (örneğin,) girin. *Waad*).

    c. **Meta veri belgesi** kutusunun yanındaki **Dosya Seç** ' i seçerek INDIRILEN Federasyon meta veri XML dosyanızı Azure Portal yükleyin.

    d. **Sonraki adımı** seçin.

1. **Sağlayıcı bilgilerini doğrula** bölmesinde **Oluştur**' u seçin.

    !["Sağlayıcı bilgilerini doğrulama" bölmesinin ekran görüntüsü.][15]

1. Sol bölmede **Roller**' i seçin ve ardından **rol oluştur**' u seçin.

    ![Roller bölmesinde "rol oluştur" düğmesinin ekran görüntüsü.][16]

    > [!NOTE]
    > İçeri aktarılmakta olan bir rolün rol Amazon kaynak adı (ARN) ve SAML sağlayıcısı ARN 'in Birleşik uzunluğu 240 veya daha az karakter olmalıdır.

1. **Rol oluştur** sayfasında, aşağıdakileri yapın:  

    !["Rol oluşturma" sayfasında "SAML 2,0 Federasyonu" güvenilen varlık düğmesinin ekran görüntüsü.][19]

    a. **Güvenilen varlık türünü seçin** altında **SAML 2,0 Federasyonu**' ni seçin.

    b. **SAML 2,0 sağlayıcısı seçin** altında, daha önce oluşturduğunuz SAML sağlayıcısını seçin (örneğin, *Waad*)

    c. **Programlı ve AWS Yönetim Konsolu erişimine Izin ver '** i seçin.

    d. Şunu seçin: **İleri: İzinler**.

1. Arama kutusuna **yönetici erişimi** girin, **Yönetimtoraccess** onay kutusunu seçin ve ardından **İleri: Etiketler**' i seçin.

    ![Yönetimtoraccess ilkesi seçili olan "Ilke adı" listesinin ekran görüntüsü.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. **Etiket ekle (isteğe bağlı)** bölmesinde şunları yapın:

    !["Etiket ekle (isteğe bağlı)" bölmesinin ekran görüntüsü.](./media/aws-multi-accounts-tutorial/config2.png)

    a. **Anahtar** kutusuna anahtar adını girin (örneğin, *azureadtest*).

    b. **Değer (isteğe bağlı)** kutusunda, anahtar değerini şu biçimde girin: `<accountname-aws-admin>` . Hesap adı tüm küçük harflerden oluşmalıdır.

    c. Şunu seçin: **İleri: Gözden Geçir**.

1. **Gözden geçirme** bölmesinde şunları yapın:

    !["Rol adı" ve "rol açıklaması" kutuları vurgulanmış şekilde Inceleme bölmesinin ekran görüntüsü.][34]

    a. **Rol adı** kutusunda, değeri aşağıdaki biçimde girin: `<accountname-aws-admin>` .

    b. **Rol açıklaması** kutusuna rol adı için kullandığınız değeri girin.

    c. **Rol oluştur**' u seçin.

    d. İhtiyaç duyduğunuz sayıda rol oluşturun ve bunları kimlik sağlayıcısına eşleyin.

    > [!NOTE]
    > Benzer şekilde, *AccountName-finans-admin*, *AccountName-Read-Only-User*, *AccountName-DevOps-User* veya *AccountName-TPM-User* gibi başka roller de oluşturabilirsiniz; her biri buna bağlı farklı bir ilke ile yapılır. Bu rol ilkelerini, her bir AWS hesabının gereksinimlerine göre daha sonra değiştirebilirsiniz. AWS hesaplarında her bir rol için aynı ilkeleri tutmanız iyi bir fikirdir.

1. Aşağıdaki ekran görüntüsünde gösterildiği gibi, AWS hesabının hesap KIMLIĞINI Amazon elastik Işlem bulutu (Amazon EC2) Özellikler bölmesinden veya ıAM panosundan aklınızda olduğunuzdan emin olun:

    ![Hesap KIMLIĞININ "kimlik ve erişim yönetimi" bölmesinde nerede görüntülendiğini gösteren ekran görüntüsü.](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Azure portal oturum açın ve ardından **gruplar**' a gidin.

1. Daha önce oluşturduğunuz ıAM rolleriyle aynı ada sahip yeni gruplar oluşturun ve ardından bu yeni grupların her birinin **nesne kimliği** kutusunda bulunan değeri unutmayın.

    ![Yeni bir grup için hesap ayrıntılarının ekran görüntüsü.](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Geçerli AWS hesabının oturumunu kapatın ve ardından Azure AD ile SSO 'yu yapılandırmak istediğiniz başka bir hesapta oturum açın.

1. Hesaplarda tüm roller oluşturulduktan sonra, bu hesaplar için **Roller** listesinde gösterilir.

    ![Rol listesinin her rolün adını, açıklamasını ve güvenilir varlıklarını gösteren ekran görüntüsü.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

Daha sonra tüm hesaplar genelinde tüm roller için tüm rol ve güvenilen varlıkları yakalamanız gerekir. Bunları Azure AD uygulamasıyla el ile eşlemeniz gerekir. Bunun için:

1. Rol ARN ve güvenilen varlık değerlerini kopyalamak için her bir rolü seçin. Azure AD 'de oluşturacağınız tüm roller için bunlara ihtiyacınız olacaktır.

    ![Rol ARNs ve güvenilen varlıklar için Özet bölmesinin ekran görüntüsü.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Önceki adımı tüm hesaplardaki tüm roller için tekrarlayın ve sonra bunları aşağıdaki biçimde bir metin dosyasında depolayın: `<Role ARN>,<Trusted entities>` .

1. [Microsoft Graph Gezginini](https://developer.microsoft.com/graph/graph-explorer)açın ve ardından aşağıdakileri yapın:

    a. Kiracınız için genel yönetici veya ortak yönetici kimlik bilgileriyle Microsoft Graph gezgin sitesinde oturum açın.

    b. Rolleri oluşturmak için yeterli izinlere sahip olmanız gerekir. **İzinleri değiştir**' i seçin.

      ![Microsoft Graph gezgin kimlik doğrulama bölmesinde "izinleri değiştir" bağlantısının ekran görüntüsü.](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. İzinler listesinde, aşağıdaki ekran görüntüsünde gösterilen izinlere sahip değilseniz, her birini seçin ve ardından **Izinleri Değiştir**' i seçin. 

      ![Uygun izinlere sahip Microsoft Graph gezgin izinleri listesinin ekran görüntüsü.](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Graph Explorer 'da yeniden oturum açın ve site kullanım koşullarını kabul edin. 

    e. Bölmenin en üstünde, yöntem için **Al** ' ı seçin, sürüm için **Beta** ' yı seçin ve ardından sorgu kutusuna aşağıdakilerden birini girin: 
    
    * Kiracınızdaki tüm hizmet sorumlularını getirmek için kullanın `https://graph.microsoft.com/beta/servicePrincipals` . 
    * Birden çok dizin kullanıyorsanız, `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` birincil etki alanınızı içeren öğesini kullanın.

    ![Microsoft Graph gezgin sorgusunun "Istek gövdesi" bölmesinin ekran görüntüsü.](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Hizmet sorumluları listesinden, değiştirmeniz gereken bir tane alın. 
    
    Ayrıca, CTRL + F ' i seçerek uygulamanın listelenen tüm hizmet sorumlularını için arama yapabilirsiniz. Belirli bir hizmet sorumlusunu almak için, burada gösterildiği gibi daha önce Azure AD Özellikler bölmesinden kopyaladığınız hizmet sorumlusu nesne KIMLIĞINI sorguya dahil edin:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

      ![Nesne KIMLIĞINI içeren bir hizmet sorumlusu sorgusunu gösteren ekran görüntüsü.](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    örneğin: Hizmet sorumlusu nesnesinden appRoles özelliğini ayıklayın.

    ![Hizmet sorumlusu nesnesinden appRoles özelliğinin ayıklanması için kodun ekran görüntüsü.](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Artık uygulamanız için yeni roller oluşturmanız gerekir. 

    i. Aşağıdaki JSON kodu appRoles nesnesine bir örnektir. Uygulamanız için istediğiniz rolleri eklemek için benzer bir nesne oluşturun.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Yalnızca düzeltme eki işlemi için *msiam_access* eklendikten sonra yeni roller ekleyebilirsiniz. Kuruluşunuzun ihtiyaçlarına bağlı olarak istediğiniz sayıda rol de ekleyebilirsiniz. Azure AD, bu rollerin *DEĞERINI* SAML yanıtında talep değeri olarak gönderir.

    j. Microsoft Graph Gezgini ' nde yöntemi **Al** ' dan **Düzeltme Eki**' nden değiştirin. Hizmet sorumlusu nesnesini, önceki örnekte gösterildiği gibi appRoles özelliğini güncelleştirerek istediğiniz rollerle birlikte yayükleyebilirsiniz. Düzeltme Eki işlemini yürütmek için **Sorguyu Çalıştır** ' ı seçin. Başarı iletisi AWS uygulamanız için rol oluşturulmasını onaylar.

      ![Microsoft Graph gezgin bölmesinin, yöntemi düzeltme eki olarak değiştirilmiş ekran görüntüsü.](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Hizmet sorumlusu daha fazla rolle düzeltme eki uygulandıktan sonra, kullanıcıları ve grupları ilgili rollerine atayabilirsiniz. Bu Azure portal, AWS uygulamasına gidip üstteki **Kullanıcılar ve gruplar** sekmesini seçerek yapabilirsiniz.

1. Gruptaki belirli bir rolü atayabilmeniz için her bir AWS rolü için yeni bir grup oluşturmanızı öneririz. Bire bir eşleme, bir grubun bir role atandığı anlamına gelir. Daha sonra, o gruba ait olan üyeleri ekleyebilirsiniz.

1. Grupları oluşturduktan sonra grubu seçin ve uygulamaya atayın.

    !["Kullanıcılar ve gruplar" bölmesinin ekran görüntüsü.](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Grupları atadığınızda iç içe gruplar desteklenmez.

1. Rolü gruba atamak için, rolü seçin ve ardından **ata**' yı seçin.

    !["Atama Ekle" bölmesinin ekran görüntüsü.](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Rolleri atadıktan sonra, Azure portal oturumunuzu yenileyerek bunları görüntüleyebilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı Microsoft uygulamalarım ' ı kullanarak test edersiniz.

Uygulamalarım içindeki **AWS** kutucuğunu seçtiğinizde, AWS uygulama sayfası rolü seçme seçeneği ile açılır.

![SSO 'yu test etmek için AWS sayfasının ekran görüntüsü.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Ayrıca, talep olarak geçirilen rolleri görmek için SAML yanıtını doğrulayabilirsiniz.

![SAML yanıtının ekran görüntüsü.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarım portalından oturum açma ve uygulamaları başlatma](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

AWS 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin konumunu gerçek zamanlı olarak koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. Daha fazla bilgi için bkz. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
