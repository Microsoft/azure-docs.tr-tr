---
title: 'Öğretici: Azure Active Directory iş günü geri yazmayı yapılandırma | Microsoft Docs'
description: Azure AD 'den Workday 'ye öznitelik geri yazma 'yı nasıl yapılandıracağınızı öğrenin
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 3a623a487dd31caf8c85b18771d90e3a6306df68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954013"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Öğretici: Azure AD 'den Workday 'ye öznitelik geri yazmayı yapılandırma
Bu öğreticinin amacı, Azure AD 'deki öznitelikleri Workday 'e geri yazma için gerçekleştirmeniz gereken adımları gösteriyoruz. Workday geri yazma sağlama uygulaması, aşağıdaki Workday özniteliklerine değer atanmasını destekler:
* İş e-postası 
* Workday Kullanıcı adı
* İş kolu telefon numarası (ülke kodu, alan kodu, numara ve uzantı dahil)
* İş kolu telefon numarası birincil bayrağı
* İş cep telefonu numarası (ülke kodu, alan kodu, sayı dahil)
* İş mobil birincil bayrağı

## <a name="overview"></a>Genel Bakış

[Şirket ıçı ad](workday-inbound-tutorial.md) sağlama uygulaması veya [Workday 'den Azure AD](workday-inbound-cloud-only-tutorial.md) sağlama uygulaması için Workday 'yi kullanarak gelen sağlama tümleştirmesini ayarladıktan sonra, Workday geri yazma uygulamasını iş e-postası ve telefon numarası gibi iletişim bilgilerini Workday 'e yazacak şekilde yapılandırabilirsiniz. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Bu Kullanıcı sağlama çözümü kim için en uygun?

Bu Workday için geri yazma Kullanıcı sağlama çözümü idealdir:

* BT tarafından yönetilen yetkili özniteliklerini (e-posta adresi, Kullanıcı adı ve telefon numarası gibi) yeniden Workday 'e geri yazması gereken Microsoft 365 kullanan kuruluşlar

## <a name="configure-integration-system-user-in-workday"></a>Workday 'de tümleştirme sistemi kullanıcısını yapılandırma

Çalışan verilerini alma izinlerine sahip bir Workday tümleştirme sistem kullanıcı hesabı oluşturmak için [tümleştirme sistem kullanıcısını yapılandırma](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) bölümüne bakın. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Azure AD öznitelik geri yazma özelliğini Workday 'e yapılandırma

Kullanıcı e-posta adreslerinin ve Kullanıcı adının Azure Active Directory ' den Workday 'e geri yazmayı yapılandırmak için bu yönergeleri izleyin.

* [Geri yazma Bağlayıcısı uygulamasını ekleme ve Workday bağlantısı oluşturma](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Geri yazma öznitelik eşlemelerini yapılandırma](#part-2-configure-writeback-attribute-mappings)
* [Kullanıcı sağlamayı etkinleştirme ve başlatma](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>1. kısım: geri yazma Bağlayıcısı uygulamasını ekleme ve Workday bağlantısı oluşturma

**Workday geri yazma bağlayıcısını yapılandırmak için:**

1. <https://portal.azure.com> öğesine gidin.

2. Azure portal, araması yapın ve **Azure Active Directory** seçin.

3. **Kuruluş uygulamaları**' nı ve ardından **tüm uygulamalar**' ı seçin.

4. **Uygulama Ekle**' yi seçin, ardından **Tüm** kategorisini seçin.

5. **Workday geri yazma** araması yapın ve bu uygulamayı Galeriden ekleyin.

6. Uygulama eklendikten ve Uygulama Ayrıntıları Ekranı gösterildikten sonra **sağlama**' yı seçin.

7. **Sağlama** **modunu** **Otomatik** olarak değiştirin.

8. **Yönetici kimlik bilgileri** bölümünü aşağıdaki şekilde doldurun:

   * **Yönetici Kullanıcı adı** – kiracı etki alanı adının eklendiği Workday tümleştirme sistem hesabının kullanıcı adını girin. Şöyle görünmelidir: *Kullanıcı adı \@ contoso4*

   * **Yönetici parolası –** Workday tümleştirme sistem hesabının parolasını girin

   * **Kiracı URL 'si –** Kiracınız için Workday Web Hizmetleri uç noktasının URL 'sini girin. Bu değer şöyle görünmelidir: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources` , burada *contoso4* doğru kiracı adınızla değiştirilmiştir ve *WD3-Impl* doğru ortam dizesiyle (gerekliyse) değiştirilmiştir.

   * **Bildirim e-postası –** E-posta adresinizi girin ve "hata oluşursa e-posta gönder" onay kutusunu işaretleyin.

   * **Bağlantıyı Sına** düğmesine tıklayın. Bağlantı testi başarılı olursa üstteki **Kaydet** düğmesine tıklayın. Başarısız olursa, Workday URL 'SI ve kimlik bilgilerinin Workday 'de geçerli olduğundan emin olun.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Bölüm 2: geri yazma öznitelik eşlemelerini yapılandırma

Bu bölümde, geri yazma özniteliklerinin Azure AD 'den Workday 'e nasıl akabildiğini yapılandıracaksınız. 

1. **Eşlemeler** altındaki sağlama sekmesinde, eşleme adına tıklayın.

2. **Kaynak nesne kapsamı** alanında, Azure Active Directory ' deki Kullanıcı kümelerinin geri yazmanın parçası olması gerekir. Varsayılan kapsam, "Azure AD 'deki tüm kullanıcılar" dır.

3. **Öznitelik eşlemeleri** bölümünde, iş günü çalışan kimliği veya çalışan kimliğinin depolandığı Azure Active Directory özniteliğini belirtmek IÇIN eşleşen kimliği güncelleştirin. Popüler bir eşleştirme yöntemi, Workday çalışan KIMLIĞI 'ni veya çalışan KIMLIĞINI Azure AD 'de extensionAttribute1-15 ile eşitlemeniz ve ardından Azure AD 'de bu özniteliği kullanarak Workday 'de kullanıcıları eşleştirmek için kullanılır.

4. Genellikle Azure AD *userPrincipalName* özniteliğini Workday *KullanıcıKimliği* ÖZNITELIĞINE eşleyin ve Azure AD *posta* özniteliğini Workday *emadresi* özniteliğiyle eşleyin. 

     >[!div class="mx-imgBorder"]
     >![Azure portalda](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Telefon numarası öznitelik değerlerini Azure AD 'den Workday 'e eşlemek için aşağıda paylaşılan kılavuzu kullanın. 

     | Workday Phone özniteliği | Beklenen değer | Eşleme Kılavuzu |
     |-------------------------|----------------|------------------|
     | Workphonelandlineisprımary | doğru/yanlış | Çıktısı "true" veya "false" dize değeri olan sabit veya ifade eşlemesi. |
     | Workphonelandlinecountrykod adı | [Üç harfli ISO 3166-1 ülke kodu](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Çıktısı üç harfli ülke kodu olan sabit veya ifade eşlemesi. |
     | WorkphoneLandlineCountryCodeNumber | [Uluslararası ülke çağırma kodu](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Çıktısı geçerli bir ülke kodu (+ işareti olmadan) olan sabit veya ifade eşlemesi. |
     | WorkphoneLandlineNumber | Alan kodunu içeren tam telefon numarası | *TelephoneNumber* özniteliğiyle eşleyin. Boşluk, köşeli ayraçlar ve ülke kodunu kaldırmak için Regex kullanın. Aşağıdaki örneğe bakın. |
     | WorkphoneLandlineExtension | Uzantı numarası | *TelephoneNumber* uzantı içeriyorsa, değeri ayıklamak için Regex kullanın. |
     | Workphonemobileisprımary | doğru/yanlış | Sabit eşleme veya çıktısı "true" veya "false" dize değeri olan ifade eşlemesi |
     | Workphonemobilecountrykod adı | [Üç harfli ISO 3166-1 ülke kodu](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Çıktısı üç harfli ülke kodu olan sabit veya ifade eşlemesi. |
     | WorkphoneMobileCountryCodeNumber | [Uluslararası ülke çağırma kodu](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Çıktısı geçerli bir ülke kodu (+ işareti olmadan) olan sabit veya ifade eşlemesi. |
     | WorkphoneMobileNumber | Alan kodunu içeren tam telefon numarası | *Mobil* özniteliğe eşleyin. Boşluk, köşeli ayraçlar ve ülke kodunu kaldırmak için Regex kullanın. Aşağıdaki örneğe bakın. |

     > [!NOTE]
     > Azure AD, Change_Work_Contact Workday Web hizmetini çağırırken şu sabit değerleri gönderir: <br>
     > * **Communication_Usage_Type_ID** , "Work" sabit dizesine ayarlanır <br>
     > * **Phone_Device_Type_ID** , cep telefonu numaraları için "mobil" ve "sabit" sabit dizesine ayarlanır. <br>
     > 
     > Workday kiracınız farklı Type_IDs kullanıyorsa geri yazma hatalarıyla karşılaşırsınız. Bu tür hataların oluşmasını engellemek için, Workday **başvuru kimliklerini koru** görevini kullanabilir ve TYPE_IDS Azure AD tarafından kullanılan değerlerle eşleşecek şekilde güncelleştirebilirsiniz. <br>
     >  

     **Başvuru Regex ifadeleri-örnek 1**

     Azure AD 'deki telefon numarası self servis parola sıfırlama (SSPR) için gereken biçim kullanılarak ayarlandıysa, aşağıdaki normal ifadeyi kullanın. <br>
     Örnek: telefon numarası değer + 1 1112223333-> ise, Regex ifadesi 1112223333 çıktısını alacak

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Başvuru Regex ifadeleri-örnek 2**

     Azure AD 'de telefon numarası (XXX) XXX-XXXX biçimi kullanılarak ayarlandıysa aşağıdaki normal ifadeyi kullanın. <br>
     Örnek: telefon numarası değeri (111) 222-3333->, Regex ifadesi 1112223333 çıktısını alacak

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Eşlemelerinizi kaydetmek için Attribute-Mapping bölümünün en üstündeki **Kaydet** ' e tıklayın.

## <a name="enable-and-launch-user-provisioning"></a>Kullanıcı sağlamayı etkinleştirme ve başlatma

Workday sağlama uygulama yapılandırması tamamlandıktan sonra, Azure portal sağlama hizmeti 'ni açabilirsiniz.

> [!TIP]
> Varsayılan olarak, sağlama hizmetini açtığınızda kapsamdaki tüm kullanıcılar için sağlama işlemleri başlatılır. Eşleme veya Workday veri sorunlarında hatalar varsa, sağlama işi başarısız olabilir ve karantina durumuna geçebilir. Bunu önlemek için, en iyi uygulama olarak, tüm kullanıcılar için tam eşitlemeyi başlatmadan önce, **kaynak nesne kapsamı** filtresini ve öznitelik eşlemelerinizi test eden birkaç test kullanıcıyla test etmenizi öneririz. Eşlemelerin çalıştığını ve size istenen sonuçları vermiş olduktan sonra, filtreyi kaldırabilir ya da daha fazla kullanıcı eklemek için onu kademeli olarak genişletebilirsiniz.

1. **Sağlama** sekmesinde, **sağlama durumunu** **Açık** olarak ayarlayın.

1. **Kapsam** açılan menüsünde **tüm kullanıcıları ve grupları Eşitle**' yi seçin. Bu seçenekle, geri yazma uygulaması tüm kullanıcıların eşlenen özniteliklerini Azure AD 'den Workday 'e yazacak ve **eşleme**  ->  **kaynak nesne kapsamı** altında tanımlanan kapsam kurallarına tabidir. 

   > [!div class="mx-imgBorder"]
   > ![Geri yazma kapsamını Seç](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > Workday geri yazma sağlama uygulaması **yalnızca atanan kullanıcı ve grupları Eşitle** seçeneğini desteklemez.
 

2. **Kaydet**’e tıklayın.

3. Bu işlem, kaynak dizinde kaç Kullanıcı olduğuna bağlı olarak değişken sayıda saat sürebilen ilk eşitlemeyi başlatacak. İlerleme çubuğunu, eşitleme döngüsünün ilerlemesini izlemek için kontrol edebilirsiniz. 

4. Herhangi bir zamanda, sağlama hizmetinin gerçekleştirdiği işlemleri görmek için Azure portal **sağlama günlükleri** sekmesini kontrol edin. Denetim günlükleri, kaynak kaynağından içeri aktarılan ve hedef uygulamaya aktarılan kullanıcılar gibi sağlama hizmeti tarafından gerçekleştirilen tüm bireysel eşitleme olaylarını listeler.  

5. İlk eşitleme tamamlandıktan sonra, aşağıda gösterildiği gibi **sağlama** sekmesine bir özet raporu yazar.

     > [!div class="mx-imgBorder"]
     > ![Sağlama ilerleme çubuğu](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="known-issues-and-limitations"></a>Bilinen sorunlar ve sınırlamalar

* Geri yazma uygulaması, **Communication_Usage_Type_ID** ve **Phone_Device_Type_ID** parametreleri için önceden tanımlanmış bir değer kullanır. Workday kiracınız Bu öznitelikler için farklı bir değer kullanıyorsa geri yazma işlemi başarısız olur. Önerilen bir geçici çözüm, Workday 'deki Type_IDs güncelleştirbir çözümdür. 
* Geri yazma uygulaması, ikincil telefon numaralarını güncelleştirecek şekilde yapılandırıldığında, Workday 'deki mevcut ikincil telefon numarasının yerini almaz. Çalışan kaydına bir tane daha ikincil telefon numarası ekler. Bu davranışa geçici bir çözüm yoktur. 


## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD ve Workday tümleştirme senaryoları ve Web hizmeti çağrıları hakkında daha fazla bilgi edinin](../app-provisioning/workday-integration-reference.md)
* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
* [Workday ve Azure Active Directory arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin](workday-tutorial.md)
* [Diğer SaaS uygulamalarını Azure Active Directory ile tümleştirmeyi öğrenin](tutorial-list.md)
* [Sağlama yapılandırmalarınızı dışarı ve içeri aktarma hakkında bilgi edinin](../app-provisioning/export-import-provisioning-configuration.md)

