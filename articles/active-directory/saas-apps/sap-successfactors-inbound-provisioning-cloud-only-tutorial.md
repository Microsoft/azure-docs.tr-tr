---
title: 'Öğretici: Azure Active Directory | için başarılı etmenleri yapılandırma Microsoft Docs'
description: Başarılı faktörlerden Azure AD 'ye gelen sağlamayı nasıl yapılandıracağınızı öğrenin
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: a39f7701d0244eff7f914908a6708dfc14873b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954865"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning"></a>Öğretici: Azure AD Kullanıcı sağlaması için SAP başarılı faktörleri yapılandırma
Bu öğreticinin amacı, başarılı bir şekilde, başarılı bir şekilde e-posta adresi geri yazma işlemi sayesinde, başarılı bir şekilde çalışan verileri Azure Active Directory, çalışan verileri sağlamak için gerçekleştirmeniz gereken adımları gösterir. 

>[!NOTE]
>Bu öğreticiyi, başarılı bir şekilde sağlamak istediğiniz kullanıcılar şirket içi AD hesabına gerek gerektirmeyen yalnızca bulutta yer alıyorsa bu öğreticiyi kullanın. Kullanıcılar yalnızca şirket içi AD hesabı veya hem AD hem de Azure AD hesabı gerektiriyorsa, lütfen Kullanıcı sağlamasını [Active Directory IÇIN SAP başarılı faktörleri yapılandırma](sap-successfactors-inbound-provisioning-tutorial.md#overview) hakkında öğreticiye bakın. 

## <a name="overview"></a>Genel Bakış

[Azure Active Directory Kullanıcı sağlama hizmeti](../app-provisioning/user-provisioning.md) , kullanıcıların kimlik yaşam döngüsünü yönetmek Için [çalışan merkezi](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) ile tümleşir. 

Azure AD Kullanıcı sağlama hizmeti tarafından desteklenen Kullanıcı sağlama iş akışlarının başarılı olması, aşağıdaki insan kaynakları ve kimlik yaşam döngüsü yönetimi senaryolarına yönelik Otomasyonu etkinleştirir:

* **Yeni çalışanları Işe alma** -başarılı bir şekilde yeni bir çalışan eklendiğinde, Azure Active Directory ' de otomatik olarak bir kullanıcı hesabı oluşturulur ve isteğe Microsoft 365 bağlı olarak, [Azure AD tarafından desteklenen diğer SaaS uygulamaları](../app-provisioning/user-provisioning.md)ve e-posta adresi başarılı etkenlere geri yazılır.

* **Çalışan özniteliği ve profil güncelleştirmeleri** -bir çalışan kaydı başarılı bir şekilde güncelleştirildiğinde (ad, başlık veya yönetici gibi), kullanıcı hesapları Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamalarını](../app-provisioning/user-provisioning.md)otomatik olarak güncelleştirir.

* **Çalışan sonlandırmaları** -bir çalışan başarılı bir şekilde sonlandırılırsa, kullanıcı hesapları Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamaları](../app-provisioning/user-provisioning.md)için otomatik olarak devre dışıdır.

* **Çalışan rehires** -bir çalışan başarılı bir şekilde yeniden çalıştırıldığında, eski hesapları Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamaları](../app-provisioning/user-provisioning.md)için otomatik olarak yeniden etkinleştirilebilir veya yeniden sağlanabilir.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Bu Kullanıcı sağlama çözümü kim için en uygun?

Azure Active Directory Kullanıcı sağlama çözümünün bu başarılı olması için idealdir:

* Başarılı bir şekilde Kullanıcı sağlama için önceden oluşturulmuş ve bulut tabanlı bir çözüm gerektiren kuruluşlar

* Başarılı faktörlere Azure Active Directory için doğrudan Kullanıcı sağlama gerektiren kuruluşlar

* Kullanıcıların, çalışan merkezi 'nden alınan veriler kullanılarak sağlanması gereken kuruluşlar [(EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* E-posta için Microsoft 365 kullanan kuruluşlar

## <a name="solution-architecture"></a>Çözüm Mimarisi

Bu bölümde, yalnızca bulutta bulunan kullanıcılar için uçtan uca Kullanıcı sağlama çözüm mimarisi açıklanmaktadır. İki ilişkili akış vardır:

* **Yetkılı HR veri akışı – Azure Active Directory:** Bu akış çalışan olayları (örneğin, yeni Hires, aktarımlar, sonlandırmalar), ilk olarak bulut başarılı bir şekilde gelir ve ardından olay verileri Azure Active Directory içine akar. Olaya bağlı olarak, Azure AD 'de oluşturma/güncelleştirme/etkinleştirme/devre dışı bırakma işlemlerine neden olabilir.
* **E-posta geri yazma akışı – şirket içi Active Directory 'Den başarılı etkenlere:** Azure Active Directory hesap oluşturma işlemi tamamlandıktan sonra, Azure AD 'de oluşturulan e-posta özniteliği değeri veya UPN, başarılı etkenlere geri yazılabilir.

  ![Genel Bakış](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Uçtan uca Kullanıcı veri akışı

1. HR ekibi, başarılı bir şekilde çalışan merkezi 'nde çalışan işlemleri (Joiners/Taşımacılar/satın ıcılar veya yeni Hires/aktarımlar/sonlandırmalar) gerçekleştirir
2. Azure AD sağlama hizmeti, başarılı olan kimlik ve şirket içi Active Directory eşitleme için işlenmesi gereken değişiklikleri tanımlar.
3. Azure AD sağlama hizmeti değişikliği belirler ve Azure AD 'de Kullanıcı için oluşturma/güncelleştirme/etkinleştirme/devre dışı bırakma işlemini çağırır.
4. Başarılı bir şekilde [geri yazma uygulaması](sap-successfactors-writeback-tutorial.md) yapılandırıldıysa, kullanıcının e-posta adresı Azure AD 'den alınır. 
5. Azure AD sağlama hizmeti, kullanılan eşleşen özniteliğe göre başarılı bir şekilde e-posta özniteliği yazar.

## <a name="planning-your-deployment"></a>Dağıtımınızı planlama

Bulut HR tabanlı Kullanıcı sağlamasının Azure AD 'ye başarılı bir şekilde yapılandırılması, şu gibi farklı yönleri kapsayan önemli bir planlama gerektirir:

* Eşleşen KIMLIĞI belirleme 
* Öznitelik eşlemesi
* Öznitelik dönüşümü 
* Kapsam belirleme filtreleri

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

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Kullanıcı hazırlama işlemini başarılı etmenlerden Azure AD 'ye yapılandırma

Bu bölümde, Azure AD 'ye başarılı bir şekilde Kullanıcı hesabı sağlama adımları sağlanmaktadır.

* [Sağlama bağlayıcı uygulamasını ekleyin ve başarılı etkenlere bağlantıyı yapılandırın](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Öznitelik eşlemelerini yapılandırma](#part-2-configure-attribute-mappings)
* [Kullanıcı sağlamayı etkinleştirme ve başlatma](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>1. kısım: sağlama bağlayıcı uygulamasını ekleyin ve başarılı etkenlere bağlantıyı yapılandırın

**Başarılı faktörleri Azure AD sağlamaya yönelik olarak yapılandırmak için:**

1. Şuraya gidin: <https://portal.azure.com>

2. Sol gezinti çubuğunda **Azure Active Directory** ' yi seçin.

3. **Kuruluş uygulamaları**' nı ve ardından **tüm uygulamalar**' ı seçin.

4. **Uygulama Ekle**' yi seçin ve **Tüm** kategorisini seçin.

5. **Kullanıcı sağlamayı Azure Active Directory Için başarılı faktörleri** arayın ve bu uygulamayı Galeriden ekleyin.

6. Uygulama eklendikten ve Uygulama Ayrıntıları Ekranı gösterildikten sonra **sağlama** ' yı seçin.

7. **Sağlama** **modunu** **Otomatik** olarak değiştirme

8. **Yönetici kimlik bilgileri** bölümünü aşağıdaki şekilde doldurun:

   * **Yönetici Kullanıcı adı** – şirket kimliği eklenerek, başarılı olan API Kullanıcı hesabının kullanıcı adını girin. Şu biçimdedir: **UserName \@ CompanyID**

   * **Yönetici parolası –** Başarılı etken API 'SI Kullanıcı hesabının parolasını girin. 

   * **Kiracı URL 'si –** Başarılı olan OData API hizmetleri uç noktasının adını girin. Yalnızca http veya https olmayan sunucunun ana bilgisayar adını girin. Bu değer şöyle görünmelidir: **api-Server-Name.SuccessFactors.com**.

   * **Bildirim e-postası –** E-posta adresinizi girin ve "hata oluşursa e-posta gönder" onay kutusunu işaretleyin.
    > [!NOTE]
    > Azure AD sağlama hizmeti, sağlama işi [karantina](../app-provisioning/application-provisioning-quarantine-status.md) durumuna geçtiğinde e-posta bildirimi gönderir.

   * **Bağlantıyı Sına** düğmesine tıklayın. Bağlantı testi başarılı olursa üstteki **Kaydet** düğmesine tıklayın. Başarısız olursa, başarılı olan kimlik bilgilerinin ve URL 'nin geçerli olduğunu iki kez kontrol edin.
    >[!div class="mx-imgBorder"]
    >![Azure portalda](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Kimlik bilgileri başarıyla kaydedildikten sonra, **eşlemeler** bölümünde varsayılan eşleme, kullanıcıları Azure Active Directory başarılı bir **şekilde eşitler**

### <a name="part-2-configure-attribute-mappings"></a>2. Bölüm: öznitelik eşlemelerini yapılandırma

Bu bölümde, Kullanıcı verilerinin başarıyla Active Directory olarak nasıl akacağını yapılandıracaksınız.

1. **Eşlemeler** altındaki sağlama sekmesinde, **Azure Active Directory Için başarılı etmenleri eşitler**' a tıklayın.

1. **Kaynak nesne kapsamı** alanında, bir öznitelik tabanlı filtre kümesi tanımlayarak, başarılı bir şekilde hangi Kullanıcı KÜMELERININ Azure AD 'ye sağlanması için kapsamda olması gerektiğini seçebilirsiniz. Varsayılan kapsam, "tüm kullanıcılar başarılı faktörlerdeki" dir. Örnek filtreler:

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
   > Sağlama altyapısının varsayılan davranışı, kapsam dışına çıkan kullanıcıları devre dışı bırakmak/silmektir. Bu işlem, Azure AD tümleştirmesinde başarılı bir şekilde istenmeyebilir. Bu varsayılan davranışı geçersiz kılmak için, [kapsam dışı olan kullanıcı hesaplarını silmeyi atlama](../app-provisioning/skip-out-of-scope-deletions.md) makalesine başvurun.
  
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

Öznitelik eşleme yapılandırmanız tamamlandıktan sonra, artık [Kullanıcı sağlama hizmetini etkinleştirebilir ve başlatabilirsiniz](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Kullanıcı sağlamayı etkinleştirme ve başlatma

Uygulama yapılandırmalarının sağlanması başarılı bir şekilde tamamlandıktan sonra, Azure portal sağlama hizmeti 'ni açabilirsiniz.

> [!TIP]
> Varsayılan olarak, sağlama hizmetini açtığınızda kapsamdaki tüm kullanıcılar için sağlama işlemleri başlatılır. Eşleme veya Workday veri sorunlarında hatalar varsa, sağlama işi başarısız olabilir ve karantina durumuna geçebilir. Bunu önlemek için, en iyi uygulama olarak, tüm kullanıcılar için tam eşitlemeyi başlatmadan önce, **kaynak nesne kapsamı** filtresini ve öznitelik eşlemelerinizi test eden birkaç test kullanıcıyla test etmenizi öneririz. Eşlemelerin çalıştığını ve size istenen sonuçları vermiş olduktan sonra, filtreyi kaldırabilir ya da daha fazla kullanıcı eklemek için onu kademeli olarak genişletebilirsiniz.

1. **Sağlama** sekmesinde, **sağlama durumunu** **Açık** olarak ayarlayın.

2. **Kaydet**’e tıklayın.

3. Bu işlem ilk eşitlemeyi başlatacak ve bu da, başarılı bir sayıda kullanıcının başarılı olan kiracı kiracısında olmasına bağlı olarak birkaç saat sürebilir. İlerleme çubuğunu, eşitleme döngüsünün ilerlemesini izlemek için kontrol edebilirsiniz. 

4. Herhangi bir zamanda, sağlama hizmeti 'nin gerçekleştirdiği işlemleri görmek için Azure portal **Denetim günlükleri** sekmesini kontrol edin. Denetim günlükleri, sağlama hizmeti tarafından gerçekleştirilen tüm bireysel eşitleme olaylarını listeler (örneğin, Workday 'den okuma ve daha sonra Active Directory için eklenen veya güncellenen). 

5. İlk eşitleme tamamlandıktan sonra, aşağıda gösterildiği gibi **sağlama** sekmesinde bir denetim Özeti raporu yazar.

   > [!div class="mx-imgBorder"]
   > ![Sağlama ilerleme çubuğu](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Gelen sağlama için desteklenen başarılı etmenler öznitelikleri hakkında daha fazla bilgi edinin](../app-provisioning/sap-successfactors-attribute-reference.md)
* [E-posta geri yazmanın başarılı etkenlere nasıl yapılandırılacağını öğrenin](sap-successfactors-writeback-tutorial.md)
* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
* [Başarılı ve Azure Active Directory arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin](successfactors-tutorial.md)
* [Diğer SaaS uygulamalarını Azure Active Directory ile tümleştirmeyi öğrenin](tutorial-list.md)
* [Sağlama yapılandırmalarınızı dışarı ve içeri aktarma hakkında bilgi edinin](../app-provisioning/export-import-provisioning-configuration.md)