---
title: Azure Active Directory B2C nedir?
description: Facebook, Google ve diğer kimlik sağlayıcılarıyla sosyal oturum açma da dahil olmak üzere, uygulamalarınızda dış kimlikleri desteklemek için Azure Active Directory B2C nasıl kullanabileceğinizi öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 007b23f07afec6163c2158feb3f17ba71e44bdb5
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71120587"
---
# <a name="what-is-azure-active-directory-b2c"></a>Azure Active Directory B2C nedir?

Azure Active Directory B2C, hizmet olarak işletmeler arası kimlik sağlar. Müşterileriniz, uygulamalarınıza ve API 'lerinize çoklu oturum açma erişimi sağlamak için tercih edilen sosyal, kurumsal veya yerel hesap kimliklerini kullanır.

![Azure AD B2C kimlik sağlayıcılarının ve aşağı akış uygulamalarının bilgi grafiği](media/active-directory-b2c-overview/azureadb2c-overview.png)

Azure Active Directory B2C (Azure AD B2C), günde milyonlarca kullanıcıyı ve milyarlarca kimlik doğrulamasını destekleyebilen bir müşteri kimlik erişim yönetimi (CıHAR) çözümüdür. Kimlik doğrulama platformunun ölçeklendirilmesi ve güvenliği, hizmet reddi, parola spreyi veya deneme yanılma saldırıları gibi tehditleri otomatik olarak işleme işlemlerini gerçekleştirir.

## <a name="custom-branded-identity-solution"></a>Özel markalı kimlik çözümü

Azure AD B2C, beyaz etiketli bir kimlik doğrulama çözümüdür. Web ve mobil uygulamalarınız ile sorunsuz bir şekilde karışabilmesi için kullanıcı deneyiminin tamamını markanızla özelleştirebilirsiniz.

Kullanıcılarınız kaydolurken Azure AD B2C tarafından görünen her sayfayı özelleştirin, oturum açın ve profil bilgilerini değiştirin. Azure AD B2C deneyiminin uygulamanızın yerel bir parçası olduğundan ve Fede olması için Kullanıcı yolculukları içindeki HTML, CSS ve JavaScript 'i özelleştirin.

![Özelleştirilmiş kaydolma ve oturum açma sayfaları ve arka plan resmi](media/active-directory-b2c-overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Kullanıcı tarafından belirtilen kimlikle çoklu oturum açma erişimi

Azure AD B2C, OpenID Connect, OAuth 2,0 ve SAML dahil standartlara dayalı kimlik doğrulama protokolleri kullanır. En modern uygulamalar ve ticari raf dışı yazılım ile tümleşir.

![Azure AD B2C üçüncü taraf kimliklerinin diyagramı](media/active-directory-b2c-overview/scenario-singlesignon.png)

Web uygulamalarınız, mobil uygulamalarınız ve API 'leriniz için merkezi kimlik doğrulama yetkilisi görevi gören Azure AD B2C, hepsi için çoklu oturum açma (SSO) çözümü oluşturmanızı sağlar. Kullanıcı profili ve tercih bilgileri koleksiyonunu merkezileştirin, oturum açma davranışı ve kaydolma dönüştürmesi hakkında ayrıntılı analizi yakalayın.

## <a name="integrate-with-external-user-stores"></a>Dış Kullanıcı depolarıyla tümleştirme

Azure AD B2C, Kullanıcı başına 100 özel öznitelik tutan bir dizin sağlar. Bununla birlikte, dış sistemlerle de tümleştirilebilir. Örneğin, kimlik doğrulaması için Azure AD B2C kullanın, ancak müşteri verileri için gerçeği kaynağı olarak bir dış müşteri ilişkisi yönetimi (CRM) veya müşteri bağlılık programı veritabanına temsilci seçin.

Başka bir dış kullanıcı deposu senaryosu, uygulamanız için kimlik doğrulamasını Azure AD B2C ve kullanıcı profilini veya kişisel verileri depolayan bir dış sistemle tümleştirin. Örneğin, bölgesel veya şirket içi veri depolama ilkeleri gibi veri fazlalığını karşılamak için.

![Dış Kullanıcı deposuyla iletişim kuran Azure AD B2C mantıksal diyagramı](media/active-directory-b2c-overview/scenario-remoteprofile.png)

Azure AD B2C, kayıt veya profil düzenlemesi sırasında kullanıcıdan bilgileri toplamayı kolaylaştırabilir ve bu verileri dış sisteme kapamaktır. Daha sonra, gelecekteki kimlik doğrulamaları sırasında, Azure AD B2C dış sistemden verileri alabilir ve gerekirse, bunu uygulamanıza gönderdiği kimlik doğrulama belirtecinin bir parçası olarak dahil edebilirsiniz.

## <a name="progressive-profiling"></a>Aşamalı profil oluşturma

Başka bir Kullanıcı yolculuğu seçeneği, aşamalı profil oluşturma işlemini içerir. Aşamalı profil oluşturma, müşterilerinizin en az miktarda bilgi toplayarak ilk işlemini hızla tamamlamalarını sağlar. Daha sonra, gelecekteki oturum açma işlemleri sırasında müşteriden daha fazla profil verisi toplayın.

![Aşamalı profil oluşturma görsel gösterimi](media/active-directory-b2c-overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Üçüncü taraf kimlik doğrulama ve sağlama

Kimlik doğrulama ve sağlama işlemlerini, Kullanıcı verilerini toplayarak ve sonra doğrulama, güven Puanlama ve Kullanıcı hesabı oluşturma onayı gerçekleştirmek için üçüncü taraf bir sisteme geçirerek kimlik doğrulama ve sağlama işlemlerini kolaylaştırmak için Azure AD B2C kullanın.

![Üçüncü taraf kimlik doğrulama için kullanıcı akışını gösteren diyagram](media/active-directory-b2c-overview/scenario-idproofing.png)

Bunlar, işletmeden müşteriye kimlik platformunuz olarak Azure AD B2C yapabileceğiniz işlemlerden bazılarıdır. Bu genel bakışın aşağıdaki bölümleri, Azure AD B2C kullanan bir demo uygulaması boyunca size yol gösterir. Ayrıca, Azure AD B2C daha ayrıntılı bir [teknik genel bakışa](technical-overview.md)doğrudan taşımaya de hoş geldiniz.

## <a name="example-woodgrove-groceries"></a>Örnek: WoodGrove Market

[Woodgrove Market][woodgrove] , birkaç Azure AD B2C özelliği göstermek üzere Microsoft tarafından oluşturulan canlı bir Web uygulamasıdır. Sonraki birkaç bölüm, WoodGrove Web sitesine Azure AD B2C tarafından sunulan bazı kimlik doğrulama seçeneklerini gözden geçirir.

### <a name="business-overview"></a>İşe genel bakış

WoodGrove, bireysel tüketicilere ve iş müşterilerine Market satan bir çevrimiçi Market deposudur. İş müşterileri, şirketlerinin veya yönettikleri işletmelerin adına Market satın alır.

### <a name="sign-in-options"></a>Oturum açma seçenekleri

WoodGrove Market, müşterilerinin mağaza ile sahip olduğu ilişkiye göre çeşitli oturum açma seçenekleri sunar:

* **Bireysel** müşteriler, bir sosyal kimlik sağlayıcısı ya da bir e-posta adresi ve parola gibi bireysel hesaplarla kaydolabilir veya oturum açabilir.
* **İş** müşterileri, kurumsal kimlik bilgileriyle kaydolabilir veya oturum açabilir.
* **Iş ortakları** ve satıcılar, satış için ürünlerle Market 'e sahip Market 'i sağlayan bireylerdir. İş ortağı kimliği [Azure ACTIVE DIRECTORY B2B](../active-directory/b2b/what-is-b2b.md)tarafından sağlanır.

![Bireysel (B2C), iş (B2C) ve iş ortağı (B2B) oturum açma sayfaları](media/active-directory-b2c-overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Bireysel müşterilerin kimliklerini doğrulama

Bir müşteri **Kişisel hesabınızla oturum açma**seçtiğinde, Azure AD B2C tarafından barındırılan özelleştirilmiş bir oturum açma sayfasına yönlendirilir. Aşağıdaki görüntüde, WoodGrove Market Web sitesini görüntülemek için Kullanıcı arabirimini (UI) özelleştirdik. WoodGrove müşterilerinin, kimlik doğrulama deneyiminin Azure AD B2C tarafından barındırılıp güvenliği farkında olmalıdır.

![Azure AD B2C tarafından barındırılan özel WoodGrove oturum açma sayfası](media/active-directory-b2c-overview/sign-in.png)

WoodGrove, müşterilerinin kimlik sağlayıcısı olarak Google, Facebook veya Microsoft hesaplarını kullanarak kaydolmalarına ve oturum açmasına olanak tanır. Ya da, *yerel hesap*olarak adlandırılan öğeleri oluşturmak için e-posta adresini ve parolasını kullanarak kaydolabilir.

Bir müşteri **Kişisel hesabınızla kaydolun** ve **Şimdi kaydolun**, özel bir kaydolma sayfası sunulur.

![Azure AD B2C tarafından barındırılan özel WoodGrove kaydolma sayfası](media/active-directory-b2c-overview/sign-up.png)

Bir e-posta adresi girdikten ve **doğrulama kodu gönder**' i seçtikten sonra, Azure AD B2C kodu gönderir. Kodu girdikten sonra **kodu doğrula**' yı seçin ve ardından formdaki diğer bilgileri girin, ayrıca hizmet koşullarını kabul etmelidir.

**Oluştur** düğmesine tıkladığınızda, kullanıcının WoodGrove Market Web sitesine geri yönlendirmesi Azure AD B2C neden olur. Yeniden yönlendirirse Azure AD B2C, WoodGrove Web uygulamasına bir OpenID Connect kimlik doğrulama belirteci geçirir. Kullanıcı oturum açmıştır ve oturum açmış olduklarını belirtmek için sağ üst köşede görüntülenen görünen adlarını görüntüler.

![Kullanıcının oturum açmış olduğu WoodGrove Market web sitesi üstbilgisi](media/active-directory-b2c-overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>İş müşterilerinin kimliklerini doğrulama

Bir müşteri, **iş müşterileri**altındaki seçeneklerden birini seçtiğinde, WoodGrove Market web sitesi bireysel müşteriler için olduğundan farklı bir Azure AD B2C ilkesi çağırır.

Bu ilke, kullanıcıya kaydolma ve oturum açma için kurumsal kimlik bilgilerini kullanma seçeneği sunar. WoodGrove örneğinde, kullanıcılardan herhangi bir Office 365 veya Azure AD hesabıyla oturum açması istenir. Bu ilke, dünyanın her yerindeki Office 365 müşteriyle Azure AD B2C federasyona `/common` eklemek için [çok kiracılı bir Azure AD uygulaması](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) ve Azure AD uç noktasını kullanır.

### <a name="authenticate-partners"></a>Kimlik doğrulama iş ortakları

**Tedarikçi hesabınız Ile oturum açma** , Azure Active Directory B2B's işbirliği işlevlerini kullanır. Azure AD B2B, iş ortağı kimliklerini yönetmek için Azure Active Directory bir özellik ailesidir. Bu kimlikler, Azure AD B2C korunan uygulamalara erişim için Azure Active Directory federe olabilir.

[Azure ACTIVE DIRECTORY B2B 'de Konuk Kullanıcı erişimi olan](../active-directory/b2b/what-is-b2b.md)Azure AD B2B hakkında daha fazla bilgi edinin.

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure AD B2C ne olduğunu ve bu senaryonun bazı senaryolarına ilişkin bir fikriniz olduğuna göre, özellikleri ve teknik yönlerini biraz daha ayrıntılı olarak inceleyin.

> [!div class="nextstepaction"]
> [Azure AD B2C Teknik Genel Bakış >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo
