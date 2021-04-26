---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için gezik yapılandırma | Microsoft Docs'
description: Azure AD 'den gezik 'a Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3e40f87d-8624-4b14-b098-80ff916103c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2021
ms.author: Zhchia
ms.openlocfilehash: 19436b7faef081757e4500c76e7537ee78081bfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950459"
---
# <a name="tutorial-configure-travelperk-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için gezik yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem seyahat perk hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmetini kullanarak kullanıcıları ve grupları otomatik olarak [gezme](https://www.travelperk.com/) için sağlar ve hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Desteklenen özellikler

> [!div class="checklist"]
>
> - Seyahat halinde Kullanıcı oluşturma
> - Artık erişim gerektirmeyen gezik 'daki kullanıcıları kaldırın
> - Kullanıcı özniteliklerinin Azure AD ile seyahat perk arasında eşitlenmiş olmasını sağlama
> - Seyahat perk ['Da çoklu oturum açma](./travelperk-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

- [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md).
- Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici).
- Etkin bir [seyahat perk](https://app.travelperk.com/signup) yönetici hesabı.
- Premium/Pro [planı](https://www.travelperk.com/pricing/).


## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama

1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile yolculuk perk arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini belirleme.

## <a name="step-2-configure-travelperk-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için gezik 'yi yapılandırma

1. [Seyahat perk](https://app.travelperk.com/company/integrations/scim) uygulamasında Yönetici hesabınızla oturum açın.

2. **Şirket ayarları**  >  **tümleştirmelerine** gidin  >  **SCIM**

3. **SCıM API 'Sini etkinleştir** ' e tıklayın

   ![Etkinleştir](./media/travelperk-provisioning-tutorial/configuration.png)

4. Ayrıca, onayları SCıM aracılığıyla da etkinleştirebilirsiniz. Onaylar, belirtilen onaylayanlar tarafından ilk olarak dönüşlerin onaylanmasını sağlayarak ek idare ayarlamanıza yardımcı olur. [Burada](https://support.travelperk.com/hc/en-us/articles/360044168971-How-do-approval-processes-work-)hakkında daha fazla bilgi edinebilirsiniz.

5. Her bir kişiye ait yöneticinin, gidiş onayından sorumlu kullanıcı tarafından otomatik olarak mı olmasını istediğinizi belirtebilirsiniz. Bu nedenle, ilgili otomatik onay işlemine bir onaylayan atanır. Seyahat perk, Azure 'un **yönetici** değerini kullanıcının istediği onaylayana eşleyebilir. Kullanıcının, sağlanan Kullanıcı onaylayanı olmadan önce platformda mevcut olması gerekir.
Onaylayanlar, gezik üzerinde düzgün şekilde yapılandırılmamışsa oluşturulmaz.

6. Otomatik onay işlemi oluşturma, tümleştirme sayfasından SCıM etkinleştirildikten sonra **SCIM ayarlarında** kullanılabilir. Açmak için, **bir kimlik sağlayıcısı aracılığıyla** seçim yapın ve **otomatik onay işlemi oluşturmayı etkinleştir** için geçiş anahtarını değiştirin.

7. Gerekli onay süreci yapılandırıldıktan sonra **Değişiklikleri Kaydet** ' e tıklayın.

   ![Otomatikleştirme](./media/travelperk-provisioning-tutorial/approval.png)

## <a name="step-3-add-travelperk-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD Uygulama Galerisi 'nden seyahat perk ekleme

Azure AD Uygulama Galerisi 'nden gezik 'yi, gezik 'ye sağlamayı yönetmeye başlamak için ekleyin. Daha önce, SSO için Seyahatperk 'yı ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz.

- Gezik 'e Kullanıcı atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz.

- Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz.

## <a name="step-5-configure-automatic-user-provisioning-to-travelperk"></a>5. Adım. Gezik için otomatik Kullanıcı sağlamayı yapılandırma

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak TestApp içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan bir adım adım yol gösterir.

### <a name="to-configure-automatic-user-provisioning-for-travelperk-in-azure-ad"></a>Azure AD 'de gezik için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

   ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **gezik**' ı seçin.

   ![Uygulamalar listesindeki seyahat perk bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

   ![Hazırlama sekmesi](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

   ![Sekme otomatik sağlama](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde **Yetkilendir**' e tıklayın. **Seyahatperk**'Nin oturum açma sayfasına yönlendirilirsiniz. **Kullanıcı adınızı** ve **parolanızı** girin ve **oturum aç** düğmesine tıklayın. Yetkilendirme sayfasında, **uygulamayı Yetkilendir** ' e tıklayın. Azure AD 'nin Seyahatperk 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, SecureLogin hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

   ![Yönetici kimlik bilgileri](./media/travelperk-provisioning-tutorial/authorize.png)

   ![Hoş Geldiniz](./media/travelperk-provisioning-tutorial/login.png)

   ![Access](./media/travelperk-provisioning-tutorial/authorization.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

   ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları gezik olarak eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'Den Gezperk 'ye eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Gezperk içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, Seyahatperk API 'sinin bu özniteliğe göre kullanıcıların filtrelenmesini desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   | Öznitelik                                                                         | Tür      | Filtreleme Için destekleniyor |
   | --------------------------------------------------------------------------------- | --------- | ----------------------- |
   | userName                                                                          | Dize    | &check;                 |
   | externalId                                                                        | Dize    |
   | active                                                                            | Boole   |
   | name.honorificPrefix                                                              | Dize    |
   | name.familyName                                                                   | Dize    |
   | name.givenName                                                                    | Dize    |
   | Name. middleName                                                                   | Dize    |
   | preferredLanguage                                                                 | Dize    |
   | locale                                                                            | Dize    |
   | phoneNumbers[type eq "work"].value                                                | Dize    |
   | externalId                                                                        | Dize    |
   | başlık                                                                             | Dize    |
   | urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: costCenter             | Dize    |
   | urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Manager                | Başvuru |
   | urn: IETF: params: Scim: schemas: Extension: gezperk: 2.0: Kullanıcı: cinsiyet                 | Dize    |
   | urn: IETF: params: Scim: schemas: Extension: gezperk: 2.0: User: Dateofdoğum            | Dize    |
   | urn: IETF: params: Scim: schemas: Extension: gezperk: 2.0: Kullanıcı: ınvoiceprofiles        | Dizi     |
   | urn:IETF:params:SCIM:schemas:exten:seyahatperk: 2.0:Kullanıcı:acil Gencycontact. Name  | Dize    |
   | urn: IETF: params: Scim: schemas: Extension: gezperk: 2.0: Kullanıcı: acil durumda. telefon | Dize    |
   | urn: IETF: params: Scim: schemas: Extension: gezperk: 2.0: Kullanıcı: Geziilkesi           | Dize    |

10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. Gezik için Azure AD sağlama hizmeti 'ni etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek gezik için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer.

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme

Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.

## <a name="additional-resources"></a>Ek kaynaklar

- [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)