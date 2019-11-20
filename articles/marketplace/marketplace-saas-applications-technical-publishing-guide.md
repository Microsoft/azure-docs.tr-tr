---
title: Azure Market SaaS uygulamaları teknik Yayımlama Kılavuzu
description: Azure Market 'Te SaaS uygulamaları yayımlamak için adım adım kılavuz ve yayımlama denetim listeleri
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: keithcharlie
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: kevidal
ms.openlocfilehash: c213bbcb3c7859aa4718916aafab970b3ffd4613
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813035"
---
# <a name="saas-applications-offer-publishing-guide"></a>SaaS uygulamaları için Yayımlama Kılavuzu

SaaS uygulamaları, "benimle Iletişime geçin", "Şimdi deneyin" ve "Şimdi al" eylemine yönelik üç farklı çağrıya sahip Market 'te yayımlanabilir. Bu kılavuzda, her biri için gereksinimler dahil olmak üzere bu üç seçenek açıklanmaktadır. 

## <a name="offer-overview"></a>Teklifin genel bakış  

SaaS uygulamaları, Azure Storets 'de kullanılabilir geçerli seçenekleri açıklar:

| Storefront seçeneği | Döküm | Deneme/Transact |  
| --- | --- | --- |  
| AppSource | Evet (benimle Iletişim kurun) | Evet (PowerBI/Dynamics) |
| Azure Marketi | Hayır | Evet (SaaS uygulamaları) |   

**Liste:**  Listeleme yayımlama seçeneği, bir kişi kullanım teklifi türü içerir ve deneme ya da Işlem düzeyinde katılım uygulanabilir olmadığında kullanılır. Bu yaklaşımın avantajı, işletmeden bir çözüm sunan yayımcıların, işinizi artırma konusunda anlaşabilecek müşteri adaylarını hemen almaya başlamasını sağlar.  
**Deneme/işlem:**  Müşterinin çözümünüz için bir deneme süresi doğrudan satın alma veya isteme seçeneği vardır. Deneme deneyimini sağlamak, müşterilere sunulan katılım düzeyini artırır ve müşterilerin satın almadan önce çözümünüzü araştırmalarını sağlar. Deneme deneyimiyle, daha fazla bilgi sahibi olursunuz ve müşteri görevlendirmelerden daha fazla ve daha zengin müşteri adayları beklemeniz gerekir. Denemeler, deneme süresi boyunca en az ücretsiz destek içermelidir.  

| SaaS uygulamaları teklifi | İş Gereksinimleri | Teknik Gereksinimler |  
| --- | --- | --- |  
| **Bize Ulaşın** | Evet | Hayır |  
| **PowerBI/Dynamics** | Evet | Evet (Azure AD tümleştirmesi) |  
| **SaaS Uygulamaları**| Evet | Evet (Azure AD tümleştirmesi) |     

## <a name="saas-list"></a>SaaS listesi

Deneme olmayan bir SaaS listesi için eyleme yapılan çağrı ve faturalandırma işlevleri "bana başvur" dir. 

SaaS uygulamasını listelemek için Azure Active Directory yapılandırmanız gerekmez. 

|Gereksinimler  |Ayrıntılar  |
|---------|---------|
|Uygulamanız bir SaaS sunumudur  |   Çözümünüz bir SaaS sunumudur ve çok kiracılı bir SaaS ürünü sunuyoruz.      |


## <a name="saas-trial"></a>SaaS deneme sürümü

Ücretsiz, hizmet olarak yazılım (SaaS) tabanlı deneme sürümünü kullanarak bir çözüm veya uygulama sağlarsınız. Ücretsiz deneme teklifleri, sınırlı kullanım veya sınırlı süreli bir deneme hesabı olarak sunulabilir. 


|Gereksinimler  |Ayrıntılar  |
|---------|---------|
|Uygulamanız bir SaaS sunumudur  |   Çözümünüz bir SaaS sunumudur ve çok kiracılı bir SaaS ürünü sunuyoruz.      |
|Uygulamanız AAD etkin     |   Müşteri, etki alanına yeniden yönlendirilir ve müşteriyle doğrudan Transact       |


## <a name="saas-trial-technical-requirements"></a>SaaS deneme teknik gereksinimleri

SaaS uygulamaları için teknik gereksinimler basittir. Yayımcıların yalnızca yayımlanacak Azure Active Directory (Azure AD) ile tümleştirilmesi gerekir. Uygulamalarla Azure AD tümleştirmesi iyi şekilde belgelenmiştir ve Microsoft bu işlemi gerçekleştirmek için birden çok SDK ve kaynak sağlar.  

Başlamak için, Azure Market yayımlaması için adanmış bir aboneliğiniz olması ve işi diğer girişimlerden ayırmanıza olanak öneririz. Bu işlem tamamlandıktan sonra, geliştirme işini başlatmak için SaaS uygulamanızı bu abonelikte dağıtmaya başlayabilirsiniz.  

En iyi Azure Active Directory belge, örnek ve kılavuz aşağıdaki sitelerde bulunur: 

* [Geliştirici Kılavuzu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Azure Active Directory ile tümleştirme](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Uygulamaları Azure Active Directory tümleştirme](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure yol haritası-güvenlik ve kimlik](https://azure.microsoft.com/roadmap/?category=security-identity)

Video öğreticileri için aşağıdakileri gözden geçirin:

* [Vittorio Bertoccı ile Azure Active Directory kimlik doğrulaması](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity teknik Briing-Bölüm 1/2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity teknik Brileme-2. Bölüm](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Microsoft Azure Active Directory ile uygulama oluşturma](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure videoların Active Directory odaklı](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Ücretsiz Azure Active Directory eğitimi şurada bulunabilir:  
* [BT uzmanları için Microsoft Azure Içerik serisi: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Ayrıca, Azure Active Directory hizmet güncelleştirmelerini denetlemek için bir site sağlar   
* [Azure AD hizmet güncelleştirmeleri](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Denemeleri etkinleştirmek için Azure Active Directory kullanma  

Microsoft, tüm Market kullanıcılarının kimliğini Azure AD ile doğrular. bu nedenle, kimliği doğrulanmış bir Kullanıcı Market 'teki deneme listesini tıklatır ve deneme ortamınıza yeniden yönlendiriliyorsa, kullanıcıyı doğrudan bir denemeye ek oturum açma adımı. Kimlik doğrulama sırasında uygulamanızın Azure AD 'den aldığı belirteç, uygulamanızda bir kullanıcı hesabı oluşturmak için kullanabileceğiniz değerli Kullanıcı bilgilerini, sağlama deneyimini otomatikleştirmenizi ve dönüştürme olasılığını artırmanızı sağlar. Belirteç hakkında daha fazla bilgi için bkz. [örnek belirteçleri](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

1 ' i etkinleştirmek için Azure AD kullanma-uygulamanıza kimlik doğrulaması veya deneme sürümü şunları yapar:  
* Müşteri deneyimini Market 'ten deneme sürümüne kadar kolaylaştırın.  
* Kullanıcı Market 'ten etki alanı veya deneme ortamınıza yeniden yönlendirildiğinde bile, bir ' ürün içi deneyim ' hisini korur.  
* Ek bir oturum açma adımı olmadığından yeniden yönlendirmeye bırakma olasılığını düşürür.  
* Azure AD kullanıcılarının büyük popülasyonu için dağıtım sınırlamalarını azaltır.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Market için Azure AD tümleştirmenizi onaylama  

Uygulamanızın tek kiracılı veya çok kiracılı olmasına ve Azure AD Federasyon çoklu oturum açma (SSO) için yeni mi olduğunuza, yoksa zaten desteğe bağlı olarak, Azure AD tümleştirmenizi birkaç farklı yolla onaylayın.  

**Çok kiracılı uygulamalar için:**  

Zaten Azure AD 'yi destekliyorsa şunları yapın:
1.  Uygulamanızı Azure portal kaydetme
2.  ' Tek tıklamayla ' deneme deneyimi almak için Azure AD 'de çok kiracılı destek özelliğini etkinleştirin. Daha ayrıntılı bilgi [burada](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)bulunabilir.  

Azure AD Federasyon SSO 'yu yeni kullanıyorsanız şunları yapın: 
1.  Uygulamanızı Azure portal kaydetme
2.  [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) veya [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)kullanarak Azure AD ile SSO geliştirin.
3.  AAD 'de çok kiracılı destek özelliğini etkinleştirme ' tek tıklamayla ' deneme deneyimini almak için [burada](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)daha fazla bilgi bulabilirsiniz.  

**Tek kiracılı uygulama için aşağıdaki seçeneklerden herhangi birini kullanın:**  
* [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) kullanarak dizininize Konuk Kullanıcı olarak Kullanıcı ekleme
* ' Benimle Iletişim kurun ' kullanarak müşteriler için denemeleri el ile sağlayın
* Müşteri başına ' test sürücüsü ' geliştirin
* SSO ile çok kiracılı örnek Tanıtım uygulaması oluşturma

## <a name="saas-subscriptions"></a>SaaS abonelikleri

Müşterinizin SaaS tabanlı, teknik çözümünüzü bir abonelik olarak satın almasını sağlamak için SaaS uygulaması teklif türünü kullanın. SaaS uygulamanız için aşağıdaki gereksinimlerin karşılanması gerekir:
- Hizmeti bir sabit (aylık veya yıllık) veya Kullanıcı başına ücret ile ücretlendirilir.
- Hizmeti dilediğiniz zaman yükseltmek veya iptal etmek için bir yöntem sağlar.
Microsoft, ticaret hareketini barındırır. Microsoft, müşterinizin adına fatura. Bir SaaS uygulamasını abonelik olarak sunmak için SaaS karşılama API 'Leriyle tümleştirmeniz gerekir.  Hizmetinizin sağlama, yükseltme ve iptal etme desteği gerekir.

| Gereksinim | Ayrıntılar |  
|:--- |:--- |  
|Faturalandırma ve ölçüm | Teklifiniz, yayımlamadan önce seçtiğiniz fiyatlandırma modeline (sabit fiyat veya Kullanıcı başına) göre fiyatlandırılır.  Düz oran modeli kullanıyorsanız, isteğe bağlı olarak, sabit fiyata dahil edilen kullanım için müşterileri ücretlendirmeden kullanılan ek boyutları ekleyebilirsiniz. |  
|İptal | Teklifiniz herhangi bir zamanda müşteri tarafından iptal edilir. |  
|İşlem giriş sayfası | Kullanıcıların SaaS hizmet hesabını oluşturup yönetebilecekleri bir Azure ortak markalı işlem giriş sayfası barındırabilirsiniz. |   
| Abonelik API 'SI | Bir kullanıcı hesabı ve hizmet planı oluşturmak, güncelleştirmek ve silmek için SaaS aboneliğiyle etkileşime girebilen bir hizmeti kullanıma sunacaksınız. Kritik API değişikliklerinin 24 saat içinde desteklenmesi gerekir. Kritik olmayan API değişiklikleri düzenli olarak serbest bırakılır. |  

>[!Note]
>Bulut çözümü sağlayıcıları (CSP) iş ortağı kanalı kabul etme artık kullanılabilir.  Teklifinizi Microsoft CSP iş ortağı kanalları aracılığıyla pazarlama hakkında daha fazla bilgi için lütfen bkz. [bulut çözümü sağlayıcıları](./cloud-solution-providers.md) .

## <a name="next-steps"></a>Sonraki adımlar
Daha önce yapmadıysanız,

- Market 'e [kaydolun](https://azuremarketplace.microsoft.com/sell) .

Kaydolduysanız ve yeni bir teklif oluşturuyor ya da mevcut bir teklif üzerinde çalışıyorsanız,

- Teklifinizin oluşturulması veya tamamlanabilmesi için [bulut iş ortağı portalı oturum açın](https://cloudpartner.azure.com) .
- Daha fazla bilgi için bkz. [Azure SaaS uygulaması teklifi](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer) .
