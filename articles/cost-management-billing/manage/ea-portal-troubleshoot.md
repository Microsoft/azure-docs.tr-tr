---
title: Azure EA portalı erişim sorunlarını giderme
description: Bu makalede, Azure EA portalında Azure Kurumsal Anlaşma (EA) ile ilişkili olarak oluşabilen bazı sorunlar açıklanır.
author: bandersmsft
ms.author: banders
ms.date: 03/26/2021
ms.topic: troubleshooting
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 852fb85607318772870a8b6826c934997b84ff6d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726475"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Azure EA portalı erişim sorunlarını giderme

Bu makalede Azure Kurumsal Anlaşma (EA) ile ilişkili olarak oluşabilen yaygın sorunlar açıklanır. Azure EA portalı, kurumsal anlaşma kullanıcıları ve maliyetlerini yönetmek için kullanılır. Azure EA portalı erişimini yapılandırırken veya güncelleştirirken bu sorunlarla karşılaşabilirsiniz.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Kayda yönetici ekleme sorunları

Kurumsal kayıtlar için farklı türde kimlik doğrulama düzeyleri vardır. Kimlik doğrulaması düzeyleri yanlış uygulandığında Azure EA portalında oturum açmaya çalıştığınızda sorunlarla karşılaşabilirsiniz.

Farklı kimlik doğrulaması düzeylerine sahip kullanıcılara erişim izni vermek için Azure EA portalını kullanırsınız. Kuruluş yöneticisi, kuruluşlarının güvenlik gereksinimlerini karşılamak için kimlik doğrulaması düzeyini güncelleştirebilir.

### <a name="authentication-level-types"></a>Kimlik doğrulaması düzeyi türleri

- Yalnızca Microsoft Hesabı - Microsoft hesapları aracılığıyla kullanıcıları kullanmak, oluşturmak ve yönetmek isteyen kuruluşlara yöneliktir.
- İş veya Okul Hesabı - Buluta Federasyon ile Active Directory ayarlamış olan ve tüm hesapları tek bir kiracıda bulunan kuruluşlara yöneliktir.
- Kiracılar Arasında İş veya Okul Hesabı - Buluta Federasyon ile Active Directory ayarlamış ve birden çok kiracıda hesapları bulunacak olan kuruluşlara yöneliktir.
- Karma Hesap - Microsoft Hesabı ve/veya İş ya da Okul Hesabıyla kullanıcı eklemenize olanak tanır.

Kayda eklenen ilk iş veya okul hesabı, _varsayılan_ etki alanını belirler. Başka bir kiracının iş veya okul hesabını eklemek için kaydın altındaki kimlik doğrulaması düzeyini kiracılar arası kimlik doğrulaması olarak değiştirmeniz gerekir.

Kimlik Doğrulaması Düzeyini güncelleştirmek için:

1. Azure EA portalında Kuruluş Yöneticisi olarak oturum açın.
2. Sol gezinti panelinde **Yönet**’e tıklayın.
3. **Kayıt** sekmesine tıklayın.
4. **Kayıt Ayrıntıları**’ndan **Kimlik Doğrulaması Düzeyi**’ni seçin.
5. Kalem simgesine tıklayın.
6. **Kaydet**’e tıklayın.

![Kimlik doğrulaması düzeylerini gösteren örnek ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Microsoft hesaplarının [https://signup.live.com](https://signup.live.com/) adresinde oluşturulmuş bir ilişkili kimliği olması gerekir.

İş veya okul hesapları, federasyonla Active Directory ayarlamış olan ve tüm hesapları tek bir kiracıda bulunan kuruluşlar tarafından kullanılabilir. Şirketin iç Active Directory hizmeti federasyon hizmetiyse kullanıcılar iş veya okul federasyon kullanıcısı kimlik doğrulamasıyla eklenebilir.

Kuruluşunuz Active Directory federasyonu kullanmıyorsa iş veya okul e-posta adresinizi kullanamazsınız. Bunun yerine yeni bir e-posta adresi kaydedin veya oluşturun ve bunu Microsoft hesabı olarak kaydedin.

## <a name="unable-to-access-the-azure-ea-portal"></a>Azure EA portalına erişilemiyor

Azure EA portalında oturum açmaya çalışırken hata iletisi alıyorsanız aşağıdaki sorun giderme adımlarını izleyin:

- Doğru Azure EA portalı URL’sini kullandığınızdan emin olun (https://ea.azure.com ).
- Azure EA portalına erişiminizin iş veya okul hesabı olarak mı yoksa Microsoft hesabı olarak mı eklendiğini belirleyin.
  - İş hesabınızı kullanıyorsanız, iş e-postanızı ve parolanızı girin. İş parolanız kuruluşunuz tarafından sağlanır. Parolayla ilgili sorun yaşıyorsanız sıfırlamak için BT bölümünüze danışabilirsiniz.
  - Microsoft hesabı kullanıyorsanız, Microsoft hesabı e-posta adresinizi ve parolanızı girin. Microsoft hesabı parolanızı unuttuysanız [https://account.live.com/password/reset](https://account.live.com/password/reset) adresinde parolayı sıfırlayabilirsiniz.
- Önceki veya mevcut oturumlardan kalan tanımlama bilgilerinin ya da önbelleğe alınmış bilgilerin saklanmaması için gizli veya özel tarayıcı oturumu kullanarak oturum açın. Tarayıcınızın önbelleğini temizleyin ve https://ea.azure.com adresini açmak için gizli veya özel bir pencere kullanın.
- Microsoft hesabını kullanırken _Geçersiz Kullanıcı_ hatası alıyorsanız, bunun nedeni birden çok Microsoft hesabınız olması olabilir. Oturum açmaya çalıştığınız e-posta adresi birincil e-posta adresi olmayabilir.
Öte yandan _Geçersiz Kullanıcı_ hatası almanızın nedeni, kullanıcı kayda eklenirken yanlış hesap türünün kullanılmış olması da olabilir. Örneğin, Microsoft hesabı yerine iş veya okul hesabı kullanılmıştır. Bu örnekte başka bir EA yöneticisi doğru hesabı ekler veya [desteğe](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c) başvurmanız gerekir.
  - Birincil diğer adı denetlemeniz gerekiyorsa [https://account.live.com](https://account.live.com) adresine gidin. Ardından **Bilgileriniz**'e ve **Microsoft'ta oturum açma şeklinizi yönetin**'e tıklayın. Alternatif bir e-posta adresini doğrulamak ve hassas bilgilere erişim kodunu almak için yönergeleri izleyin. Güvenlik kodunu girin. İki öğeli kimlik doğrulaması ayarlamak istemiyorsanız **Daha sonra ayarla**’yı seçin.
  - Hesap diğer adlarınızı görüntüleyebileceğiniz **Microsoft'ta oturum açma şeklinizi yönetin** sayfasını görürsünüz. Azure EA portalında oturum açmak için kullandığınız diğer adın birincil diğer ad olduğundan emin olun. Değilse, bunu birincil diğer adınız olarak ayarlayabilirsiniz. Öte yandan bunun yerine Azure EA portalı için olan birincil adı da kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Azure EA portalı yöneticilerinin yaygın yönetim görevleri hakkında bilgi edinmek için [Azure EA portalı yönetimi](ea-portal-administration.md) makalesini okumaları gerekir.
- Azure EA etkinleştirmesine yönelik yaygın sorunlar hakkında sorular ve yanıtlar için [maliyet yönetimi + FATURALANDıRMA SSS](../cost-management-billing-faq.yml) makalesini okuyun.
