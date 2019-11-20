---
title: 'Öğretici: Amazon Web Services (AWS) ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Amazon Web Services (AWS) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2fea3bca40d8a5783448e68ea89c3b238a0104d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074027"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Öğretici: Amazon Web Services (AWS) ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Amazon Web Services (AWS) Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Amazon Web Services (AWS) Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Amazon Web Services erişimi olan denetim (AWS).
* Kullanıcılarınızın Azure AD hesaplarıyla Amazon Web Services (AWS) için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Azure AD ve AWS ilişkisi diyagramı](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Birden çok örnek için birden çok tanımlayıcı yapılandırabilirsiniz. Örneğin:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Bu değerlerle Azure AD, **#** değerini kaldırır ve doğru değeri SAML belirtecindeki hedef kitle URL 'si olarak `https://signin.aws.amazon.com/saml` gönderir.

Bu yaklaşım aşağıdaki nedenlerle önerilir:

- Her uygulama size benzersiz bir x509 sertifikası sağlar. AWS uygulama örneğinin her bir örneği daha sonra ayrı bir AWS hesabı temelinde yönetilebilen farklı bir sertifika sona erme tarihine sahip olabilir. Bu durumda genel sertifika geçişi daha kolay.

- Azure AD 'de bir AWS uygulamasıyla Kullanıcı sağlamayı etkinleştirebilir ve ardından hizmetimiz bu AWS hesabındaki tüm rolleri getirir. Uygulama üzerinde AWS rollerini el ile eklemeniz veya güncelleştirmeniz gerekmez.

- Uygulama sahibini uygulama için ayrı ayrı atayabilirsiniz. Bu kişi, uygulamayı doğrudan Azure AD 'de yönetebilir.

> [!Note]
> Yalnızca bir galeri uygulaması kullandığınızdan emin olun.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* AWS çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Amazon Web Services (AWS) **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Galeriden Amazon Web Services (AWS) ekleme

Amazon Web Services (AWS) tümleştirmesini Azure AD ile yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Amazon Web Services (AWS) eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Amazon Web Services (AWS)** yazın.
1. Sonuçlar panelinden **Amazon Web Services (AWS)** öğesini seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws"></a>Amazon Web Services (AWS) için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Amazon Web Services (AWS) ile yapılandırın ve test edin. SSO 'nun çalışması için, Amazon Web Services (AWS) ' de bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Amazon Web Services (AWS) ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Amazon Web Services (AWS) SSO 'Yu yapılandırın](#configure-amazon-web-services-aws-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Amazon Web Services (AWS) için B. Simon 'a sahip olmak üzere **[Amazon Web Services (AWS) test kullanıcısı oluşturun](#create-amazon-web-services-aws-test-user)** .
    1. **[Amazon Web Services (AWS) içinde rol sağlamayı yapılandırma](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Amazon Web Services (AWS)** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulmuştur. Kullanıcının **Kaydet**' i seçerek yapılandırmayı kaydetmesi gerekir.

1. Birden fazla örnek yapılandırırken bir tanımlayıcı değeri sağlayın. İkinci örnekten başlayarak, benzersiz bir SPN değeri belirtmek için **#** bir işaret da dahil olmak üzere aşağıdaki biçimi kullanın.

    `https://signin.aws.amazon.com/saml#2`

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Amazon Web Services ayarla (AWS)** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extensiongirin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’ tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, Amazon Web Services (AWS) erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Amazon Web Services (AWS)** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-amazon-web-services-aws-sso"></a>Amazon Web Services (AWS) SSO 'yu yapılandırma

1. Farklı bir tarayıcı penceresinde, AWS şirket sitenizde yönetici olarak oturum açın.

2. **AWS giriş**seçeneğini belirleyin.

    ![AWS şirket sitesinin, AWS giriş simgesi vurgulanmış ekran görüntüsü][11]

3. **Kimlik ve erişim yönetimi '** ni seçin.

    ![AWS Hizmetleri sayfasının ekran görüntüsü, ıAM vurgulanmış][12]

4. **Sağlayıcı oluşturmak** > **kimlik sağlayıcıları** ' nı seçin.

    ![Kimlik sağlayıcıları ve sağlayıcı oluşturma vurgulanmış olarak ıAM sayfasının ekran görüntüsü][13]

5. **Sağlayıcıyı yapılandır** sayfasında, aşağıdaki adımları uygulayın:

    ![Yapılandırma sağlayıcısının ekran görüntüsü][14]

    a. **Sağlayıcı türü**için **SAML**' yi seçin.

    b. **Sağlayıcı adı**için bir sağlayıcı adı yazın (örneğin: *Waad*).

    c. İndirilen **meta veri dosyanızı** Azure Portal yüklemek Için **Dosya Seç**' i seçin.

    d. **Sonraki adımı**seçin.

6. **Sağlayıcı bilgilerini doğrula** sayfasında **Oluştur**' u seçin.

    ![Create vurgulanan ile sağlayıcı bilgilerini doğrulama ekran görüntüsü][15]

7. **Rol oluşturmak** > **Roller** ' i seçin.

    ![Roller sayfasının ekran görüntüsü][16]

8. **Rol oluştur** sayfasında, aşağıdaki adımları uygulayın:  

    ![Rol Oluştur sayfasının ekran görüntüsü][19]

    a. **Güvenilen varlık türünü seçin**altında **SAML 2,0 Federasyonu**' ni seçin.

    b. **SAML 2,0 sağlayıcısı seçin**altında, daha önce oluşturduğunuz **SAML sağlayıcısını** seçin (örneğin: *Waad*).

    c. **Programlı ve AWS Yönetim Konsolu erişimine Izin ver '** i seçin.
  
    d. **İleri ' yi seçin: izinler**.

9. **İzin Ilkeleri Ekle** iletişim kutusunda, uygun ilkeyi kuruluşunuza göre ekleyin. Sonra Ileri ' yi seçin **: İnceleme**.  

    ![İzin ilkesi Ekle iletişim kutusunun ekran görüntüsü][33]

10. **Gözden geçirme** iletişim kutusunda, aşağıdaki adımları uygulayın:

    ![Inceleme iletişim kutusunun ekran görüntüsü][34]

    a. **Rol adı**alanına rol adınızı girin.

    b. **Rol açıklaması**' nda, açıklamayı girin.

    c. **Rol oluştur**' u seçin.

    d. Gereken sayıda rol oluşturun ve bunları kimlik sağlayıcısına eşleyin.

11. Azure AD Kullanıcı sağlaması içindeki AWS hesabından rolleri getirmek için AWS hizmet hesabı kimlik bilgilerini kullanın. Bu için AWS konsolu ana öğesini açın.

12. **Hizmetler**' i seçin. **Güvenlik, kimlik & uyumluluğu**altında, **IAM**' i seçin.

    ![Hizmet ve ıAM ile AWS konsol giriş ekranının ekran görüntüsü](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. IAM bölümünde **ilkeler**' i seçin.

    ![Ilke vurgulanmış şekilde ıAM bölümünün ekran görüntüsü](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Azure AD Kullanıcı sağlaması içindeki AWS hesabından rolleri getirmeye yönelik **Ilke oluştur** ' a tıklayarak yeni bir ilke oluşturun.

    ![Rol oluştur seçeneğinin, ilke oluştur vurgulanarak ekran görüntüsü](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. AWS hesaplarından tüm rolleri getirmek için kendi ilkenizi oluşturun.

    ![JSON vurgulanmış şekilde ilke oluştur sayfasının ekran görüntüsü](./media/amazon-web-service-tutorial/policy1.png)

    a. **Ilke oluştur**bölümünde **JSON** sekmesini seçin.

    b. İlke belgesinde aşağıdaki JSON 'u ekleyin:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. İlkeyi doğrulamak için **Ilkeyi gözden geçir** ' i seçin.

    ![İlke Oluştur sayfasının ekran görüntüsü](./media/amazon-web-service-tutorial/policy5.png)

16. Yeni ilkeyi tanımlayın.

    ![Ad ve açıklama alanları vurgulanmış şekilde ilke oluştur sayfasının ekran görüntüsü](./media/amazon-web-service-tutorial/policy2.png)

    a. **Ad**için **AzureAD_SSOUserRole_Policy**girin.

    b. **Açıklama**için **Bu ilkenin, AWS hesaplarından rolleri getirmeye izin verilecek şekilde**girin.

    c. **Ilke oluştur**' u seçin.

17. AWS ıAM hizmetinde yeni bir kullanıcı hesabı oluşturun.

    a. AWS ıAM konsolunda **Kullanıcılar**' ı seçin.

    ![AWS ıAM konsolunun, kullanıcıların vurgulandığı ekran görüntüsü](./media/amazon-web-service-tutorial/policy3.png)

    b. Yeni bir kullanıcı oluşturmak için **Kullanıcı Ekle**' yi seçin.

    ![Kullanıcı Ekle düğmesinin ekran görüntüsü](./media/amazon-web-service-tutorial/policy4.png)

    c. **Kullanıcı Ekle** bölümünde:

    ![Kullanıcı adı ve erişim türü vurgulanmış şekilde Kullanıcı ekleme sayfasının ekran görüntüsü](./media/amazon-web-service-tutorial/adduser1.png)

    * Kullanıcı adını **Azureadrolemanager**olarak girin.

    * Erişim türü için **programlı erişim**' i seçin. Bu şekilde, Kullanıcı API 'Leri çağırabilir ve AWS hesabından rolleri alabilir.

    * **Sonraki izinleri**seçin.

18. Bu Kullanıcı için yeni bir ilke oluşturun.

    ![Kullanıcı ekleme ekranının ekran görüntüsü](./media/amazon-web-service-tutorial/adduser2.png)

    a. **Mevcut ilkeleri doğrudan Ekle**' yi seçin.

    b. Yeni oluşturulan ilkeyi filtre bölümünde **AzureAD_SSOUserRole_Policy**arama yapın.

    c. İlkeyi seçin ve ardından **İleri: gözden geçir**' i seçin.

19. Ekli Kullanıcı için ilkeyi gözden geçirin.

    ![Kullanıcı Ekle seçeneğinin vurgulandığı Kullanıcı ekleme sayfasının ekran görüntüsü](./media/amazon-web-service-tutorial/adduser3.png)

    a. Kullanıcı adını, erişim türünü ve kullanıcıyla eşlenen ilkeyi gözden geçirin.

    b. **Kullanıcı oluştur**' u seçin.

20. Kullanıcının Kullanıcı kimlik bilgilerini indirin.

    ![Kullanıcı ekleme ekranının ekran görüntüsü](./media/amazon-web-service-tutorial/adduser4.png)

    a. Kullanıcı **erişim anahtarı kimliğini** ve gizli dizi **erişim anahtarını**kopyalayın.

    b. AWS konsolundan rolleri getirmek için bu kimlik bilgilerini Azure AD Kullanıcı sağlama bölümüne girin.

    c. **Kapat**' ı seçin.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Amazon Web Services (AWS) içinde rol sağlamayı yapılandırma

1. Azure AD yönetim portalında AWS uygulamasında **sağlama**' ya gidin.

    ![Sağlama vurgulanmış şekilde AWS uygulamasının ekran görüntüsü](./media/amazon-web-service-tutorial/provisioning.png)

2. Erişim anahtarını ve gizli anahtarı, sırasıyla **ClientSecret** ve **gizli belirteç** alanlarına girin.

    ![Yönetici kimlik bilgileri iletişim kutusunun ekran görüntüsü](./media/amazon-web-service-tutorial/provisioning1.png)

    a. **ClientSecret** alanına AWS Kullanıcı erişimi anahtarını girin.

    b. **Gizli belirteç** alanına AWS Kullanıcı gizli anahtarını girin.

    c. **Bağlantıyı Sına**' yı seçin.

    d. **Kaydet**' i seçerek ayarı kaydedin.

3. **Ayarlar** bölümünde, **sağlama durumu**için **Açık**' ı seçin. Daha sonra **Kaydet**’e tıklayın.

    ![Vurgulanan Ayarlar bölümünün ekran görüntüsü](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Sağlama Hizmeti, rolleri yalnızca AWS 'den Azure AD 'ye aktarır. Bu hizmet, kullanıcıları ve grupları Azure AD 'den AWS 'ye geri sağlamacaktır.

### <a name="create-amazon-web-services-aws-test-user"></a>Amazon Web Services (AWS) test kullanıcısı oluştur

Bu bölümün amacı, Amazon Web Services (AWS) içinde B. Simon adlı bir Kullanıcı oluşturmaktır. Amazon Web Services (AWS), bir kullanıcının SSO için sisteminde oluşturulmasına gerek kalmaz, bu nedenle burada herhangi bir işlem gerçekleştirmeniz gerekmez.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde Amazon Web Services (AWS) kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Amazon Web Services (AWS) otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Bilinen sorunlar

 * **Sağlama** bölümünde, **eşlemeler** alt bölümünde "yükleniyor..." görüntülenir ileti eşleştirmez ve hiçbir şekilde öznitelik eşlemelerini görüntülemez. Bugün desteklenen tek sağlama iş akışı, bir kullanıcı veya grup ataması sırasında AWS 'den Azure AD 'ye roller için içeri aktarmaktır. Bunun için öznitelik eşlemeleri önceden belirlenir ve yapılandırılamaz.

 * **Sağlama** bölümü yalnızca bir AWS kiracısı için tek seferde bir kimlik bilgileri kümesi girmeyi destekler. İçeri aktarılan tüm roller AWS kiracısı için Azure AD [`servicePrincipal` nesnesinin](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) `appRoles` özelliğine yazılır.

   Birden çok AWS kiracının (`servicePrincipals`tarafından temsil edilen), sağlama galerisindeki Azure AD 'ye eklenebilir. Ancak, SSO için kullanılan tek `servicePrincipal` sağlamak üzere kullanılan birden fazla AWS `servicePrincipals` içeri aktarılan rollerin tümünü otomatik olarak yazamayacak bilinen bir sorun vardır.

   Geçici bir çözüm olarak, sağlama yapılandırıldığı her bir AWS `servicePrincipal` içeri aktarılan tüm `appRoles` ayıklamak için [MICROSOFT Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) 'sini kullanabilirsiniz. Daha sonra, bu rol dizelerini SSO 'nun yapılandırıldığı AWS `servicePrincipal` ekleyebilirsiniz.

* Rollerin AWS 'den Azure AD 'ye aktarılmak için aşağıdaki gereksinimleri karşılaması gerekir:

  * Roller tam olarak bir SAML sağlayıcısına sahip olmalıdır ve AWS 'de tanımlı

  * İçeri aktarılmakta olan bir rol için, ARN ve SAML sağlayıcı ARN rolünün Birleşik uzunluğu 119 karakter veya daha az olmalıdır

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Amazon Web Services (AWS) deneyin](https://aad.portal.azure.com/)

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png