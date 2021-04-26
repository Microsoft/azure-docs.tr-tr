---
title: 'Öğretici: Azure Active Directory Workday gelen sağlamasını yapılandırma | Microsoft Docs'
description: Workday 'den Azure AD 'ye gelen sağlamayı nasıl yapılandıracağınızı öğrenin
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: ef4381f305292b366348aa3729209dc3f5e8c87b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954098"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Öğretici: Workday 'yi Azure AD Kullanıcı sağlaması için yapılandırma
Bu öğreticinin amacı, Workday 'den çalışan verileri Azure Active Directory ' ye sağlamak için gerçekleştirmeniz gereken adımları gösteriyoruz. 

>[!NOTE]
>Workday 'den temin etmek istediğiniz kullanıcılar, şirket içi AD hesabı gerektirmeyen yalnızca bulutta bulunan kullanıcılar ise bu öğreticiyi kullanın. Kullanıcılar yalnızca şirket içi AD hesabı veya hem AD hem de Azure AD hesabı gerektiriyorsa, lütfen Kullanıcı sağlamasını [Active Directory Için Workday 'yi yapılandırma](workday-inbound-tutorial.md) hakkında öğreticiye bakın. 

## <a name="overview"></a>Genel Bakış

[Azure Active Directory Kullanıcı sağlama hizmeti](../app-provisioning/user-provisioning.md) , Kullanıcı hesaplarını sağlamak Için [Workday insan kaynakları API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 'siyle tümleştirilir. Azure AD Kullanıcı sağlama hizmeti tarafından desteklenen Workday Kullanıcı sağlama iş akışları aşağıdaki insan kaynakları ve kimlik yaşam döngüsü yönetimi senaryolarına yönelik Otomasyonu etkinleştirir:

* **Yeni çalışanların işe** Alım-Workday 'e yeni bir çalışan eklendiğinde, Azure Active Directory ' de otomatik olarak bir kullanıcı hesabı oluşturulur ve Microsoft 365 isteğe bağlı olarak, [Azure AD tarafından desteklenen diğer SaaS uygulamalarına](../app-provisioning/user-provisioning.md)ve e-posta adresinin Workday 'e geri yazılması gerekir.

* **Çalışan özniteliği ve profil güncelleştirmeleri** -bir çalışan kaydı Workday 'de (ad, başlık veya yönetici gibi) güncelleştirildiğinde, kullanıcı hesabı Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamalarını](../app-provisioning/user-provisioning.md)otomatik olarak güncelleştirir.

* **Çalışan sonlandırmaları** -bir çalışan Workday 'de sonlandırıldığında, kullanıcı hesabı Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve [Azure AD tarafından desteklenen diğer SaaS uygulamaları](../app-provisioning/user-provisioning.md)için otomatik olarak devre dışıdır.

* **Çalışan rehires** -bir çalışan Workday 'de yeniden hazırlandığında, eski hesapları otomatik olarak yeniden etkinleştirilebilir veya Azure Active Directory ve isteğe bağlı olarak [Azure AD tarafından desteklenen diğer SaaS uygulamalarına](../app-provisioning/user-provisioning.md)Microsoft 365.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Bu Kullanıcı sağlama çözümü kim için en uygun?

Bu iş günü, Kullanıcı sağlama çözümünün Azure Active Directory için idealdir:

* Workday Kullanıcı sağlaması için önceden oluşturulmuş, bulut tabanlı bir çözüm gerektiren kuruluşlar

* Workday 'den Azure Active Directory 'ye doğrudan Kullanıcı sağlama gerektiren kuruluşlar

* Kullanıcıların Workday 'den elde edilen veriler kullanılarak sağlanması gereken kuruluşlar

* E-posta için Microsoft 365 kullanan kuruluşlar

## <a name="solution-architecture"></a>Çözüm mimarisi

Bu bölümde, yalnızca bulutta bulunan kullanıcılar için uçtan uca Kullanıcı sağlama çözüm mimarisi açıklanmaktadır. İki ilişkili akış vardır:

* **Yetkılı HR veri akışı – Workday 'den Azure Active Directory 'e:** Bu akış çalışan olayları (örneğin, yeni Hires, aktarımlar, sonlandırmalar) ilk kez Workday 'de, sonra da olay verileri Azure Active Directory akışa akar. Olaya bağlı olarak, Azure AD 'de oluşturma/güncelleştirme/etkinleştirme/devre dışı bırakma işlemlerine neden olabilir.
* **Geri yazma akışı – şirket içi Active Directory 'Den Workday 'e:** Active Directory hesap oluşturma işlemi tamamlandıktan sonra, Azure AD Connect aracılığıyla Azure AD ile eşitlenir ve e-posta, Kullanıcı adı ve telefon numarası gibi bilgiler Workday 'e geri yazılabilir.

  ![Genel Bakış](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>Uçtan uca Kullanıcı veri akışı

1. HR ekibi, iş günü çalışan merkezi 'nde çalışan işlemleri (Joiners/Taşımacılar/sızıntı veya yeni Hires/aktarımlar/sonlandırıcılar) gerçekleştirir
2. Azure AD sağlama hizmeti, iş günü EC 'den zamanlanan kimlik eşitlemeleri çalıştırır ve şirket içi Active Directory eşitleme için işlenmesi gereken değişiklikleri tanımlar.
3. Azure AD sağlama hizmeti değişikliği belirler ve Azure AD 'de Kullanıcı için oluşturma/güncelleştirme/etkinleştirme/devre dışı bırakma işlemini çağırır.
4. [Workday geri yazma](workday-writeback-tutorial.md) uygulaması yapılandırılmışsa, Azure AD 'den e-posta, Kullanıcı adı ve telefon numarası gibi öznitelikleri alır. 
5. Azure AD sağlama hizmeti, Workday 'de e-posta, Kullanıcı adı ve telefon numarasını ayarlar.

## <a name="planning-your-deployment"></a>Dağıtımınızı planlama

İş Workday 'den Azure AD 'ye bulut HR tabanlı kullanıcı sağlamayı yapılandırmak, gibi farklı yönleri kapsayan önemli bir planlama gerektirir:

* Eşleşen KIMLIĞI belirleme 
* Öznitelik eşlemesi
* Öznitelik dönüşümü 
* Kapsam belirleme filtreleri

Bu konularda kapsamlı yönergeler için lütfen [bulut HR dağıtım planına](../app-provisioning/plan-cloud-hr-provision.md) bakın. 

## <a name="configure-integration-system-user-in-workday"></a>Workday 'de tümleştirme sistemi kullanıcısını yapılandırma

Çalışan verilerini alma izinlerine sahip bir Workday tümleştirme sistem kullanıcı hesabı oluşturmak için [tümleştirme sistem kullanıcısını yapılandırma](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) bölümüne bakın. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Workday 'den Azure AD 'ye Kullanıcı sağlamayı yapılandırma

Aşağıdaki bölümlerde, yalnızca bulutta bulunan dağıtımlar için Workday 'den Azure AD 'ye Kullanıcı sağlamayı yapılandırmaya yönelik adımlar açıklanır.

* [Azure AD sağlama bağlayıcı uygulamasını ekleme ve Workday bağlantısı oluşturma](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Workday ve Azure AD öznitelik eşlemelerini yapılandırma](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Kullanıcı sağlamayı etkinleştirme ve başlatma](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1. Bölüm: Azure AD sağlama bağlayıcı uygulamasını ekleme ve Workday bağlantısı oluşturma

**Workday 'i yalnızca bulutta bulunan kullanıcıların sağlamasını Azure Active Directory üzere yapılandırmak için:**

1. <https://portal.azure.com> öğesine gidin.

2. Azure portal, araması yapın ve **Azure Active Directory** seçin.

3. **Kuruluş uygulamaları**' nı ve ardından **tüm uygulamalar**' ı seçin.

4. **Uygulama Ekle**' yi seçin ve ardından **Tüm** kategoriyi seçin.

5. **Azure AD Kullanıcı sağlaması Için Workday**'i arayın ve bu uygulamayı Galeriden ekleyin.

6. Uygulama eklendikten ve Uygulama Ayrıntıları Ekranı gösterildikten sonra **sağlama**' yı seçin.

7. **Sağlama** **modunu** **Otomatik** olarak değiştirin.

8. **Yönetici kimlik bilgileri** bölümünü aşağıdaki şekilde doldurun:

   * **Workday Kullanıcı adı** – kiracı etki alanı adının eklendiği Workday tümleştirme sistem hesabının kullanıcı adını girin. Şuna benzemelidir: username@contoso4

   * **İşgünü parolası –** Workday tümleştirme sistem hesabının parolasını girin

   * **Workday Web HIZMETLERI API URL 'si –** Kiracınız için Workday Web Hizmetleri uç noktasının URL 'sini girin. URL, bağlayıcı tarafından kullanılan Workday Web Hizmetleri API 'sinin sürümünü belirler. 
   
     | URL biçimi | WWS API sürümü kullanıldı | XPATH değişiklikleri gerekiyor |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v 21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v 21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #. # | Yes |

      > [!NOTE]
     > URL 'de sürüm bilgisi belirtilmemişse, uygulama Workday Web Hizmetleri (WWS) v 21.1 kullanır ve uygulamayla birlikte gelen varsayılan XPATH API ifadelerinde hiçbir değişiklik yapılması gerekmez. Belirli bir WWS API sürümünü kullanmak için, URL 'de sürüm numarasını belirtin <br>
     > Örnek: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> WWS API v 30.0 + kullanıyorsanız, sağlama işini açmadan önce lütfen **öznitelik eşlemesi-> gelişmiş seçenekler** altında **XPath apı deyimlerini** güncelleştirin > yapılandırma ve [Workday öznitelik başvurusunu](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) [yönetme](workday-inbound-tutorial.md#managing-your-configuration) bölümüne başvuran Workday için öznitelik listesini düzenleyin.  

   * **Bildirim e-postası –** E-posta adresinizi girin ve "hata oluşursa e-posta gönder" onay kutusunu işaretleyin.

   * **Bağlantıyı Sına** düğmesine tıklayın.

   * Bağlantı testi başarılı olursa üstteki **Kaydet** düğmesine tıklayın. Başarısız olursa, Workday URL 'SI ve kimlik bilgilerinin Workday 'de geçerli olduğundan emin olun.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>2. Bölüm: Workday ve Azure AD öznitelik eşlemelerini yapılandırma

Bu bölümde, Kullanıcı verilerinin Workday 'den yalnızca bulutta bulunan kullanıcılar için Azure Active Directory nasıl akacağını yapılandıracaksınız.

1. **Eşlemeler** altındaki sağlama sekmesinde **çalışanları Azure AD ile senkronize** et ' e tıklayın.

2. **Kaynak nesne kapsamı** alanında, bir öznitelik tabanlı filtre kümesi tanımlayarak Workday 'deki hangi Kullanıcı KÜMELERININ Azure AD 'ye sağlanması için kapsamda olması gerektiğini seçebilirsiniz. Varsayılan kapsam "Workday 'deki tüm kullanıcılar" dır. Örnek filtreler:

   * Örnek: 1000000 ve 2000000 arasında çalışan kimlikleri olan kullanıcılara kapsam

      * Öznitelik: Workerıd

      * İşleç: REGEX eşleşmesi

      * Değer: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Örnek: yalnızca çalışanların çalışanları ve normal çalışanları değil

      * Öznitelik: Kıgentıd

      * İşleç: NULL DEĞIL

3. **Hedef nesne eylemleri** alanında, Azure AD 'de gerçekleştirilen eylemlerin genel olarak filtrelemesini sağlayabilirsiniz. **Oluşturma**  ve **güncelleştirme** en yaygın olarak kullanılır.

4. **Öznitelik eşlemeleri** bölümünde, bağımsız Workday özniteliklerinin Active Directory özniteliklerle nasıl eşlendiğini tanımlayabilirsiniz.

5. Güncelleştirmek için varolan bir öznitelik eşlemesine tıklayın veya yeni eşlemeler eklemek için ekranın alt kısmındaki **Yeni eşleme Ekle** ' ye tıklayın. Tek bir öznitelik eşlemesi bu özellikleri destekler:

   * **Eşleme türü**

      * **Doğrudan** – Workday özniteliğinin değerini hiçbir DEĞIŞIKLIK olmadan ad özniteliğine yazar

      * **Sabit** -ad özniteliğine statik, sabit bir dize değeri yaz

      * **İfade** : bir veya daha fazla Workday özniteliğine göre, AD özniteliğine özel bir değer yazmanızı sağlar. [Daha fazla bilgi için ifadelerde bu makaleye bakın](../app-provisioning/functions-for-customizing-application-data.md).

   * **Kaynak özniteliği** -Workday 'den Kullanıcı özniteliği. Aradığınız öznitelik yoksa, bkz. [Workday kullanıcı özniteliklerinin listesini özelleştirme](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes).

   * **Varsayılan değer** : isteğe bağlı. Kaynak özniteliğinde boş bir değer varsa, eşleme bu değeri yazar.
            En yaygın yapılandırma bu boş bırakılmamalıdır.

   * **Target özniteliği** – Azure AD 'deki Kullanıcı özniteliği.

   * **Bu özniteliği kullanarak nesneleri Eşleştir** – bu özniteliğin Workday ve Azure AD arasındaki kullanıcıları benzersiz şekilde tanımlamak için kullanılması gerekip gerekmediğini belirtir. Bu değer genellikle, genellikle çalışan KIMLIĞI özniteliğiyle (yeni) veya Azure AD 'de bir uzantı özniteliğiyle eşlenen Workday için çalışan KIMLIĞI alanında ayarlanır.

   * **Eşleşen öncelik** – birden çok eşleşen öznitelik ayarlanabilir. Birden çok olduğunda, bu alan tarafından tanımlanan sıraya göre değerlendirilir. Bir eşleşme bulunur başlamaz, başka eşleşen öznitelikler değerlendirilir.

   * **Bu eşlemeyi Uygula**

     * **Her zaman** : Bu eşlemeyi hem Kullanıcı oluşturma hem de güncelleştirme eylemlerine Uygula

     * **Yalnızca oluşturma sırasında** -bu eşlemeyi yalnızca Kullanıcı oluşturma eylemlerinde Uygula

6. Eşlemelerinizi kaydetmek için Attribute-Mapping bölümünün en üstündeki **Kaydet** ' e tıklayın.


## <a name="enable-and-launch-user-provisioning"></a>Kullanıcı sağlamayı etkinleştirme ve başlatma

Workday sağlama uygulama yapılandırması tamamlandıktan sonra, Azure portal sağlama hizmeti 'ni açabilirsiniz.

> [!TIP]
> Varsayılan olarak, sağlama hizmetini açtığınızda kapsamdaki tüm kullanıcılar için sağlama işlemleri başlatılır. Eşleme veya Workday veri sorunlarında hatalar varsa, sağlama işi başarısız olabilir ve karantina durumuna geçebilir. Bunu önlemek için, en iyi uygulama olarak, tüm kullanıcılar için tam eşitlemeyi başlatmadan önce, **kaynak nesne kapsamı** filtresini ve öznitelik eşlemelerinizi test eden birkaç test kullanıcıyla test etmenizi öneririz. Eşlemelerin çalıştığını ve size istenen sonuçları vermiş olduktan sonra, filtreyi kaldırabilir ya da daha fazla kullanıcı eklemek için onu kademeli olarak genişletebilirsiniz.

1. **Sağlama** sekmesinde, **sağlama durumunu** **Açık** olarak ayarlayın.

2. **Kaydet**’e tıklayın.

3. Bu işlem, iş günü kiracısında kaç Kullanıcı olduğuna bağlı olarak değişken sayıda saat sürebilen ilk eşitlemeyi başlatacak. İlerleme çubuğunu, eşitleme döngüsünün ilerlemesini izlemek için kontrol edebilirsiniz. 

4. Herhangi bir zamanda, sağlama hizmeti 'nin gerçekleştirdiği işlemleri görmek için Azure portal **Denetim günlükleri** sekmesini kontrol edin. Denetim günlükleri, sağlama hizmeti tarafından gerçekleştirilen tüm bireysel eşitleme olaylarını listeler (örneğin, Workday 'den okuma ve daha sonra Azure Active Directory için eklenen veya güncellenen). 

5. İlk eşitleme tamamlandıktan sonra, aşağıda gösterildiği gibi **sağlama** sekmesinde bir denetim Özeti raporu yazar.

   > [!div class="mx-imgBorder"]
   > ![Sağlama ilerleme çubuğu](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD ve Workday tümleştirme senaryoları ve Web hizmeti çağrıları hakkında daha fazla bilgi edinin](../app-provisioning/workday-integration-reference.md)
* [Gelen sağlama için desteklenen Workday öznitelikleri hakkında daha fazla bilgi edinin](../app-provisioning/workday-attribute-reference.md)
* [Workday geri yazmayı yapılandırma hakkında bilgi edinin](workday-writeback-tutorial.md)
* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
* [Workday ve Azure Active Directory arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin](workday-tutorial.md)
* [Sağlama yapılandırmalarınızı dışarı ve içeri aktarma hakkında bilgi edinin](../app-provisioning/export-import-provisioning-configuration.md)


