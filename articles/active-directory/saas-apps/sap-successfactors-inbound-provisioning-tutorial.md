---
title: "Öğretici: AD ve Azure AD 'de başarılı olan özel faktörleri yapılandırma | Microsoft Docs"
description: Başarılı faktörlerden gelen sağlamayı yapılandırmayı öğrenin
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: 7b59e0ae2fbb73f341d5254fd2804d50ad141a19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98953810"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning"></a>Öğretici: Kullanıcı sağlamasını Active Directory için SAP başarılı faktörleri yapılandırma 
Bu öğreticinin amacı, kullanıcıları başarıyla Active Directory (AD) ve Azure AD 'ye, isteğe bağlı e-posta adresi olarak başarılı bir şekilde geri yazma işlemi ile Azure AD 'ye sağlamak için gerçekleştirmeniz gereken adımları gösteriyoruz. 

>[!NOTE]
>Başarılı bir şekilde sağlamak istediğiniz kullanıcıların şirket içi bir AD hesabına ve isteğe bağlı olarak bir Azure AD hesabına ihtiyacı varsa bu öğreticiyi kullanın. Kullanıcıların yalnızca Azure AD hesabına (yalnızca bulutta kullanıcılar) ihtiyacı varsa, [Azure AD Kullanıcı sağlaması IÇIN SAP başarılı faktörleri yapılandırma](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) hakkında öğreticiye bakın. 


## <a name="overview"></a>Genel Bakış

[Azure Active Directory Kullanıcı sağlama hizmeti](../app-provisioning/user-provisioning.md) , kullanıcıların kimlik yaşam döngüsünü yönetmek Için [çalışan merkezi](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) ile tümleşir. 

Azure AD Kullanıcı sağlama hizmeti tarafından desteklenen Kullanıcı sağlama iş akışlarının başarılı olması, aşağıdaki insan kaynakları ve kimlik yaşam döngüsü yönetimi senaryolarına yönelik Otomasyonu etkinleştirir:

* **Yeni çalışanların işe** Alım-başarılı etkenlere yeni bir çalışan eklendiğinde, bir kullanıcı hesabı Active Directory, Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamalarında](../app-provisioning/user-provisioning.md)otomatik olarak oluşturulur ve e-posta adresi başarılı etkenlere geri yazılır.

* **Çalışan özniteliği ve profil güncelleştirmeleri** -bir çalışan kaydı başarılı bir şekilde güncelleştirildiğinde (ad, başlık veya yönetici gibi), kullanıcı hesapları Active Directory, Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamalarında](../app-provisioning/user-provisioning.md)otomatik olarak güncelleştirilir.

* **Çalışan sonlandırmaları** -bir çalışan başarılı bir şekilde sonlandırılırsa, kullanıcı hesapları Active Directory, Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamalarında](../app-provisioning/user-provisioning.md)otomatik olarak devre dışı bırakılır.

* **Çalışan rehires** -bir çalışan başarılı bir şekilde yeniden çalıştırıldığında, eski hesapları Active Directory, Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamaları](../app-provisioning/user-provisioning.md)için otomatik olarak yeniden etkinleştirilebilir veya yeniden sağlanabilir.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Bu Kullanıcı sağlama çözümü kim için en uygun?

Active Directory Kullanıcı sağlama çözümünün bu başarılı olması için idealdir:

* Başarılı bir şekilde Kullanıcı sağlama için önceden oluşturulmuş ve bulut tabanlı bir çözüm gerektiren kuruluşlar

* Başarılı faktörlere Active Directory için doğrudan Kullanıcı sağlama gerektiren kuruluşlar

* Kullanıcıların, çalışan merkezi 'nden alınan veriler kullanılarak sağlanması gereken kuruluşlar [(EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Kullanıcıları katılım, taşıma ve bir veya daha fazla Active Directory ormanlara, etki alanına ve yalnızca başarılı bir şekilde [çalışan merkezi](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 'nde algılanan değişiklik bilgilerini temel alan kuruluş

* E-posta için Microsoft 365 kullanan kuruluşlar

## <a name="solution-architecture"></a>Çözüm Mimarisi

Bu bölümde, yaygın karma ortamlar için uçtan uca Kullanıcı sağlama çözüm mimarisi açıklanmaktadır. İki ilişkili akış vardır:

* **Yetkılı HR veri akışı – başarılı bir şekilde, şirket içi Active Directory:** Bu akış çalışan olayları (örneğin, yeni alar, aktarımlar, sonlandırmalar), ilk olarak bulut başarılı bir şekilde çalışan Merkezi ' nde ve olay verileri Azure AD ve sağlama Aracısı aracılığıyla şirket içi Active Directory akar. Olaya bağlı olarak, AD 'de oluşturma/güncelleştirme/etkinleştirme/devre dışı bırakma işlemlerine yol açabilir.
* **E-posta geri yazma akışı – şirket içi Active Directory 'Den başarılı etkenlere:** Active Directory hesap oluşturma işlemi tamamlandıktan sonra, Azure AD Connect Sync ile Azure AD ile eşitlenir ve e-posta özniteliği başarılı etkenlere geri yazılabilir.

  ![Genel Bakış](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Uçtan uca Kullanıcı veri akışı

1. HR ekibi, başarılı bir şekilde çalışan merkezi 'nde çalışan işlemleri (Joiners/Taşımacılar/satın ıcılar veya yeni Hires/aktarımlar/sonlandırmalar) gerçekleştirir
2. Azure AD sağlama hizmeti, başarılı olan kimlik ve şirket içi Active Directory eşitleme için işlenmesi gereken değişiklikleri tanımlar.
3. Azure AD sağlama hizmeti, AD hesabı oluşturma/güncelleştirme/etkinleştirme/devre dışı bırakma işlemlerini içeren bir istek yüküne sahip şirket içi Azure AD Connect sağlama aracısını çağırır.
4. Azure AD Connect sağlama Aracısı, AD hesabı verileri eklemek/güncelleştirmek için bir hizmet hesabı kullanır.
5. Azure AD Connect eşitleme altyapısı, AD 'de güncelleştirmeleri çekmek için Delta eşitlemesi çalıştırır.
6. Active Directory güncelleştirmeleri Azure Active Directory ile eşitlenir.
7. Başarılı bir şekilde [geri yazma uygulaması](sap-successfactors-writeback-tutorial.md) yapılandırılmışsa, kullanılan eşleşen özniteliğe göre e-posta özniteliği başarıyla başarılı etkenlere yazar.

## <a name="planning-your-deployment"></a>Dağıtımınızı planlama

Bulut HR tabanlı Kullanıcı sağlamasının başarılı bir şekilde AD 'ye yapılandırılması, şu gibi farklı yönleri kapsayan önemli bir planlama gerektirir:
* Azure AD Connect sağlama aracısının kurulumu 
* Dağıtım için AD Kullanıcı sağlama uygulamalarına yönelik başarılı etmen sayısı
* Eşleşen KIMLIK, öznitelik eşleme, dönüşüm ve kapsam filtreleri

Bu konularda kapsamlı yönergeler için lütfen [bulut HR dağıtım planına](../app-provisioning/plan-cloud-hr-provision.md) bakın. Desteklenen varlıklar, ayrıntıları işleme ve farklı ık senaryolar için tümleştirmenin nasıl özelleştirileceği hakkında bilgi edinmek için lütfen [SAP başarılı faktörleri tümleştirme başvurusuna](../app-provisioning/sap-successfactors-integration-reference.md) bakın. 

## <a name="configuring-successfactors-for-the-integration"></a>Tümleştirme için başarılı faktörleri yapılandırma

Tüm başarılı bir şekilde sağlama bağlayıcılarının ortak bir gereksinimi, başarılı bir şekilde başarılı olan OData API 'Lerini çağırmak için doğru izinlere sahip bir başarılı etken hesabının kimlik bilgilerini gerektirleridir. Bu bölümde, hizmet hesabını başarılı bir şekilde oluşturma ve uygun izinleri verme adımları açıklanmaktadır. 

* [API kullanıcı hesabını başarılı etkenlere göre oluştur/tanımla](#createidentify-api-user-account-in-successfactors)
* [API izinleri rolü oluşturma](#create-an-api-permissions-role)
* [API kullanıcısı için bir Izin grubu oluşturma](#create-a-permission-group-for-the-api-user)
* [İzin grubuna izin rolü verme](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API kullanıcı hesabını başarılı etkenlere göre oluştur/tanımla
Başarılı bir şekilde OData API 'Leri çağırmak için kullanılacak olan başarılı bir kullanıcı hesabı oluşturmak veya tanımlamak için, başarılı bir yönetim ekibiniz veya uygulama iş ortağınızla birlikte çalışın. Azure AD 'de sağlama uygulamaları yapılandırılırken bu hesabın Kullanıcı adı ve parola kimlik bilgileri gerekecektir. 

### <a name="create-an-api-permissions-role"></a>API izinleri rolü oluşturma

1. Yönetim merkezine erişimi olan bir kullanıcı hesabıyla SAP 'de başarılı bir şekilde oturum açın.
1. *Izin Rollerini Yönet*' i arayın ve ardından arama sonuçlarından **izin Rollerini Yönet** ' i seçin.
  ![Izin rollerini yönetme](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
1. Izin rolü listesinden **Yeni oluştur**' a tıklayın.
    > [!div class="mx-imgBorder"]
    > ![Yeni Izin rolü oluştur](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
1. Yeni izin rolü için bir **rol adı** ve **Açıklama** ekleyin. Ad ve açıklama rolün API kullanım izinleri olduğunu göstermelidir.
    > [!div class="mx-imgBorder"]
    > ![İzin rolü ayrıntısı](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
1. Izin ayarları altında **izin...** öğesine tıklayın, ardından izin listesini aşağı kaydırın ve **tümleştirme araçlarını Yönet**' e tıklayın. **Yöneticinin, temel kimlik doğrulaması aracılığıyla OData API 'Sine erişmesine Izin ver** kutusunu işaretleyin.
    > [!div class="mx-imgBorder"]
    > ![Tümleştirme araçlarını Yönet](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
1. Aynı kutuda aşağı kaydırın ve **çalışan yönetim API 'si**' ni seçin. ODATA API kullanarak okumak ve ODATA API kullanarak düzenlemek için aşağıda gösterildiği gibi izinler ekleyin. Başarılı bir şekilde geri yazma senaryosunda aynı hesabı kullanmayı planlıyorsanız Düzenle seçeneğini belirleyin. 
    > [!div class="mx-imgBorder"]
    > ![Okuma yazma izinleri](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Aynı izinler kutusunda **Kullanıcı izinleri-> çalışan verileri** ' ne gidin ve hizmet hesabının başarılı Özellikler kiracısından okuyagörüntüleyebileceği öznitelikleri gözden geçirin. Örneğin, başarılı faktörlerden *Kullanıcı adı* özniteliğini almak için, bu öznitelik Için "Görünüm" izninin verildiğinden emin olun. Benzer şekilde, görüntüleme izni için her bir özniteliği gözden geçirin. 

    > [!div class="mx-imgBorder"]
    > ![Çalışan verileri izinleri](./media/sap-successfactors-inbound-provisioning/review-employee-data-permissions.png)
   

    >[!NOTE]
    >Bu sağlama uygulaması tarafından alınan özniteliklerin tüm listesi için lütfen [başarılı oldu öznitelik başvurusuna](../app-provisioning/sap-successfactors-attribute-reference.md) başvurun

1. **Bitti**' ye tıklayın. **Değişiklikleri Kaydet**’e tıklayın.

### <a name="create-a-permission-group-for-the-api-user"></a>API kullanıcısı için bir Izin grubu oluşturma

1. Başarılı etmenleri yönetici merkezinde, *Izin gruplarını yönet*' i arayın ve ardından arama sonuçlarından **izin gruplarını yönet** ' i seçin.
    > [!div class="mx-imgBorder"]
    > ![İzin gruplarını yönet](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
1. Izin gruplarını yönet penceresinde **Yeni oluştur**' a tıklayın.
    > [!div class="mx-imgBorder"]
    > ![Yeni Grup Ekle](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
1. Yeni grup için bir grup adı ekleyin. Grup adı, grubun API kullanıcıları için olduğunu göstermelidir.
    > [!div class="mx-imgBorder"]
    > ![İzin grubu adı](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
1. Gruba üye ekleyin. Örneğin, kişiler havuzu açılan menüsünden **Kullanıcı adı** ' nı seçip tümleştirme IÇIN kullanılacak API hesabının kullanıcı adını girebilirsiniz. 
    > [!div class="mx-imgBorder"]
    > ![Grup üyeleri ekleme](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
1. Izin grubunu oluşturmayı tamamladıktan sonra **bitti** ' ye tıklayın.

### <a name="grant-permission-role-to-the-permission-group"></a>İzin grubuna izin rolü verme

1. Başarılı bir şekilde Yönetim Merkezi 'nde, *Izin Rollerini Yönet*' i arayın ve ardından arama sonuçlarından **izin Rollerini Yönet** ' i seçin.
1. **Izin rolü listesinden**, API kullanım izinleri için oluşturduğunuz rolü seçin.
1. **Bu rolü Izin ver altında... öğesine** tıklayın **.**
1. Açılan menüden **Izin grubu...** ' yi seçin ve ardından **Seç...** öğesine tıklayarak yukarıda oluşturulan grubu aramak ve seçmek için gruplar penceresini açın. 
    > [!div class="mx-imgBorder"]
    > ![İzin grubu Ekle](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
1. İzin grubuna verme Izni rolünü gözden geçirin. 
    > [!div class="mx-imgBorder"]
    > ![İzin rolü ve Grup Ayrıntısı](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
1. **Değişiklikleri Kaydet**’e tıklayın.

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Başarılı faktörlerden Active Directory için Kullanıcı sağlamayı yapılandırma

Bu bölüm, tümleştirmenizi kapsamındaki her bir Active Directory etki alanına başarılı bir şekilde Kullanıcı hesabı sağlama adımları sağlar.

* [Sağlama bağlayıcı uygulamasını ekleyin ve sağlama aracısını indirin](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Şirket içi sağlama aracılarını yükleyip yapılandırın](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Başarılı faktörlere ve Active Directory bağlantıyı yapılandırın](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Öznitelik eşlemelerini yapılandırma](#part-4-configure-attribute-mappings)
* [Kullanıcı sağlamayı etkinleştirme ve başlatma](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>1. kısım: sağlama bağlayıcı uygulamasını ekleme ve sağlama aracısını indirme

**Sağlamayı Active Directory için başarılı faktörleri yapılandırmak için:**

1. Şuraya gidin: <https://portal.azure.com>

2. Sol gezinti çubuğunda **Azure Active Directory** ' yi seçin.

3. **Kuruluş uygulamaları**' nı ve ardından **tüm uygulamalar**' ı seçin.

4. **Uygulama Ekle**' yi seçin ve **Tüm** kategorisini seçin.

5. **Kullanıcı sağlamayı Active Directory Için başarılı faktörleri** arayın ve bu uygulamayı Galeriden ekleyin.

6. Uygulama eklendikten ve Uygulama Ayrıntıları Ekranı gösterildikten sonra **sağlama** ' yı seçin.

7. **Sağlama** **modunu** **Otomatik** olarak değiştirme

8. Sağlama aracısını indirmek için görüntülenecek bilgi başlığına tıklayın. 
   >[!div class="mx-imgBorder"]
   >![Aracıyı indir](./media/workday-inbound-tutorial/pa-download-agent.png "Aracı ekranını indir")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>2. Bölüm: şirket içi sağlama aracılarını yükleyip yapılandırın

Şirket içi Active Directory sağlamak için, sağlama aracısının, istenen Active Directory etki alanına ağ erişimi olan, etki alanına katılmış bir sunucuya yüklenmesi gerekir.

İndirilen Aracı yükleyicisini sunucu konağına aktarın ve aracı yapılandırmasını gerçekleştirmek için [aracıyı yükleme bölümünde](../cloud-sync/how-to-install.md) listelenen adımları izleyin.

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>3. kısım: sağlama uygulamasında, başarılı etkenlere ve Active Directory bağlantı yapılandırın
Bu adımda, başarılı faktörlerle bağlantı kurmaya ve Azure portal Active Directory. 

1. Azure portal, [1. bölümde](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) oluşturulan kullanıcı sağlama uygulamasına Active Directory Için başarılı faktörlere geri dön

1. **Yönetici kimlik bilgileri** bölümünü aşağıdaki şekilde doldurun:

   * **Yönetici Kullanıcı adı** – şirket kimliği eklenerek, başarılı olan API Kullanıcı hesabının kullanıcı adını girin. Şu biçimdedir: **UserName \@ CompanyID**

   * **Yönetici parolası –** Başarılı etken API 'SI Kullanıcı hesabının parolasını girin. 

   * **Kiracı URL 'si –** Başarılı olan OData API hizmetleri uç noktasının adını girin. Yalnızca http veya https olmayan sunucunun ana bilgisayar adını girin. Bu değer şöyle görünmelidir: **<api-Server-name>. SuccessFactors.com**.

   * **Active Directory orman-** Active Directory etki alanının "adı", aracıyla birlikte kaydedilir. Sağlama için hedef etki alanını seçmek üzere açılan menüyü kullanın. Bu değer genellikle şöyle bir dizedir: *contoso.com*

   * **Active Directory kapsayıcı-** Aracının varsayılan olarak Kullanıcı hesapları oluşturması gereken kapsayıcı DN 'sini girin.
        Örnek: *OU = kullanıcılar, DC = contoso, DC = com*
        > [!NOTE]
        > Bu ayar yalnızca *parentDistinguishedName* özniteliği öznitelik eşlemelerinde yapılandırılmamışsa, Kullanıcı hesabı oluşturmaları için oynatılır. Bu ayar Kullanıcı arama veya güncelleştirme işlemleri için kullanılmaz. Tüm etki alanı alt ağacının, arama işleminin kapsamında olması.

   * **Bildirim e-postası –** E-posta adresinizi girin ve "hata oluşursa e-posta gönder" onay kutusunu işaretleyin.
     > [!NOTE]
     > Azure AD sağlama hizmeti, sağlama işi [karantina](../app-provisioning/application-provisioning-quarantine-status.md) durumuna geçtiğinde e-posta bildirimi gönderir.

   * **Bağlantıyı Sına** düğmesine tıklayın. Bağlantı testi başarılı olursa üstteki **Kaydet** düğmesine tıklayın. Başarısız olursa, aracı kurulumunda yapılandırılmış başarılı kimlik bilgilerinin ve AD kimlik bilgilerinin geçerli olup olmadığını iki kez denetleyin.
     >[!div class="mx-imgBorder"]
     >![Azure portalda](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Kimlik bilgileri başarıyla kaydedildikten sonra, **eşlemeler** bölümünde varsayılan eşleme, **kullanıcıları şirket içinde olan başarılı bir şekilde eşitler Active Directory** görüntülenir.

### <a name="part-4-configure-attribute-mappings"></a>Bölüm 4: öznitelik eşlemelerini yapılandırma

Bu bölümde, Kullanıcı verilerinin başarıyla Active Directory olarak nasıl akacağını yapılandıracaksınız.

1. **Eşlemeler** altındaki sağlama sekmesinde, **Active Directory Şirket Içi Kullanıcı Için başarılı olan kullanıcıları eşitler ' e** tıklayın.

1. **Kaynak nesne kapsamı** alanında, öznitelik tabanlı filtrelerin bir kümesini tanımlayarak, başarılı bir şekilde hangi Kullanıcı kümelerinin ad sağlamak için kapsamda olması gerektiğini seçebilirsiniz. Varsayılan kapsam, "tüm kullanıcılar başarılı faktörlerdeki" dir. Örnek filtreler:

   * Örnek: 1000000 ve 2000000 arasında personIdExternal kullanıcılara kapsam (2000000 hariç)

      * Öznitelik: Personıdexternal

      * İşleç: REGEX eşleşmesi

      * Değer: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Örnek: yalnızca çalışanlar ve çalışanların çalışanları yoktur

      * Öznitelik: EmployeeID

      * İşleç: NULL DEĞIL

   > [!TIP]
   > Sağlama uygulamasını ilk kez yapılandırırken, istenen sonucu elde ettiğinizden emin olmak için öznitelik eşlemelerinizi ve ifadelerini test etmeniz ve doğrulamanız gerekir. Microsoft, başarılı bir test kullanıcısı ile eşlemelerinizi test etmek için **kaynak nesne kapsamı** kapsamındaki kapsam filtrelerinin kullanılmasını önerir. Eşlemelerin çalıştığını doğruladıktan sonra filtreyi kaldırabilir ya da daha fazla kullanıcı eklemek için onu kademeli olarak genişletebilirsiniz.

   > [!CAUTION] 
   > Sağlama altyapısının varsayılan davranışı, kapsam dışına çıkan kullanıcıları devre dışı bırakmak/silmektir. Bu, başarılı bir şekilde AD tümleştirmede istenmeyebilir. Bu varsayılan davranışı geçersiz kılmak için, [kapsam dışı olan kullanıcı hesaplarını silmeyi atlama](../app-provisioning/skip-out-of-scope-deletions.md) makalesine başvurun.
  
1. **Hedef nesne eylemleri** alanında Active Directory üzerinde gerçekleştirilen eylemleri genel olarak filtreleyebilirsiniz. **Oluşturma** ve **güncelleştirme** en yaygın olarak kullanılır.

1. **Öznitelik eşlemeleri** bölümünde, tek tek faktörlerin özniteliklerinin Active Directory özniteliklerle nasıl eşlendiğini tanımlayabilirsiniz.

     >[!NOTE]
     >Uygulama tarafından desteklenen başarılı bir öznitelik özniteliği listesi için lütfen başarılı bir şekilde ifade edin [öznitelik başvurusu](../app-provisioning/sap-successfactors-attribute-reference.md)

1. Güncelleştirmek için varolan bir öznitelik eşlemesine tıklayın veya yeni eşlemeler eklemek için ekranın alt kısmındaki **Yeni eşleme Ekle** ' ye tıklayın. Tek bir öznitelik eşlemesi bu özellikleri destekler:

      * **Eşleme türü**

         * **Direct** – başarılı bir şekilde, bir değişiklik olmadan,

         * **Sabit** -ad özniteliğine statik, sabit bir dize değeri yaz

         * **İfade** – bir veya daha fazla başarılı bir ÖZNITELIĞE göre ad özniteliğine özel bir değer yazmanızı sağlar. [Daha fazla bilgi için ifadelerde bu makaleye bakın](../app-provisioning/functions-for-customizing-application-data.md).

      * **Kaynak özniteliği** -başarılı faktörlerdeki Kullanıcı özniteliği

      * **Varsayılan değer** : isteğe bağlı. Kaynak özniteliğinde boş bir değer varsa, eşleme bu değeri yazar.
            En yaygın yapılandırma bu boş bırakılmamalıdır.

      * **Target özniteliği** – Active Directory içindeki kullanıcı özniteliği.

      * **Bu özniteliği kullanarak nesneleri Eşleştir** : Bu eşlemenin, kullanıcıları başarılı bir şekilde ve Active Directory arasında benzersiz şekilde tanımlamak için kullanılıp kullanılmayacağını belirtir. Bu değer genellikle, genellikle Active Directory içindeki çalışan KIMLIĞI özniteliklerinden biriyle eşlenen başarılı faktörlerin çalışan KIMLIĞI alanında ayarlanır.

      * **Eşleşen öncelik** – birden çok eşleşen öznitelik ayarlanabilir. Birden çok olduğunda, bu alan tarafından tanımlanan sıraya göre değerlendirilir. Bir eşleşme bulunur başlamaz, başka eşleşen öznitelikler değerlendirilir.

      * **Bu eşlemeyi Uygula**

         * **Her zaman** : Bu eşlemeyi hem Kullanıcı oluşturma hem de güncelleştirme eylemlerine Uygula

         * **Yalnızca oluşturma sırasında** -bu eşlemeyi yalnızca Kullanıcı oluşturma eylemlerinde Uygula

1. Eşlemelerinizi kaydetmek için Attribute-Mapping bölümünün en üstündeki **Kaydet** ' e tıklayın.

Öznitelik eşleme yapılandırmanız tamamlandıktan sonra, [isteğe bağlı sağlamayı](../app-provisioning/provision-on-demand.md) kullanarak tek bir kullanıcı için sağlamayı test edebilir ve ardından [Kullanıcı sağlama hizmetini etkinleştirip başlatabilirsiniz](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Kullanıcı sağlamayı etkinleştirme ve başlatma

Uygulama yapılandırmalarının sağlanması başarılı bir şekilde tamamlandıktan sonra ve [isteğe bağlı sağlama](../app-provisioning/provision-on-demand.md)ile tek bir kullanıcı için sağlamayı doğruladıktan sonra, Azure Portal sağlama hizmeti 'ni açabilirsiniz.

> [!TIP]
> Varsayılan olarak, sağlama hizmetini açtığınızda kapsamdaki tüm kullanıcılar için sağlama işlemleri başlatılır. Eşlemede hata varsa veya veri sorunlarını başarıyla içeriyorsa, sağlama işi başarısız olabilir ve karantina durumuna geçebilir. Bunu önlemek için, en iyi uygulama olarak, tüm kullanıcılar için tam eşitlemeyi başlatmadan önce, **kaynak nesne kapsamı** filtresini ve öznitelik eşlemelerinizi, [isteğe bağlı sağlama](../app-provisioning/provision-on-demand.md) kullanarak birkaç test kullanıcısı ile test etmenizi öneririz. Eşlemelerin çalıştığını ve size istenen sonuçları vermiş olduktan sonra, filtreyi kaldırabilir ya da daha fazla kullanıcı eklemek için onu kademeli olarak genişletebilirsiniz.

1. **Sağlama** dikey penceresine gidin ve **sağlamayı Başlat**' a tıklayın.

1. Bu işlem ilk eşitlemeyi başlatacak ve bu da, başarılı bir sayıda kullanıcının başarılı olan kiracı kiracısında olmasına bağlı olarak birkaç saat sürebilir. İlerleme çubuğunu, eşitleme döngüsünün ilerlemesini izlemek için kontrol edebilirsiniz. 

1. Herhangi bir zamanda, sağlama hizmeti 'nin gerçekleştirdiği işlemleri görmek için Azure portal **Denetim günlükleri** sekmesini kontrol edin. Denetim günlükleri, sağlama hizmeti tarafından gerçekleştirilen, hangi kullanıcıların başarılı bir şekilde okunmakta olduğu ve daha sonra Active Directory olarak eklendiği veya güncelleştirildiği gibi tüm bireysel eşitleme olaylarını listeler. 

1. İlk eşitleme tamamlandıktan sonra, aşağıda gösterildiği gibi **sağlama** sekmesinde bir denetim Özeti raporu yazar.

   > [!div class="mx-imgBorder"]
   > ![Sağlama ilerleme çubuğu](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Gelen sağlama için desteklenen başarılı etmenler öznitelikleri hakkında daha fazla bilgi edinin](../app-provisioning/sap-successfactors-attribute-reference.md)
* [E-posta geri yazmanın başarılı etkenlere nasıl yapılandırılacağını öğrenin](sap-successfactors-writeback-tutorial.md)
* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
* [Başarılı ve Azure Active Directory arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin](successfactors-tutorial.md)
* [Diğer SaaS uygulamalarını Azure Active Directory ile tümleştirmeyi öğrenin](tutorial-list.md)
* [Sağlama yapılandırmalarınızı dışarı ve içeri aktarma hakkında bilgi edinin](../app-provisioning/export-import-provisioning-configuration.md)