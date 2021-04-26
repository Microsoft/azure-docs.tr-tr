---
title: Azure AD B2C için en iyi uygulamalar
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C (Azure AD B2C) ile çalışırken göz önünde bulundurmanız gereken öneriler ve en iyi uygulamalar.
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: 2c8a9121d0e36eb51cd02c2c884ddcaa0dd79a79
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226218"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Azure Active Directory B2C için öneriler ve en iyi uygulamalar

Aşağıdaki en iyi yöntemler ve öneriler, Azure Active Directory (Azure AD) B2C 'yi mevcut veya yeni uygulama ortamlarında tümleştirmesinin bazı birincil yönlerini kapsar.

## <a name="fundamentals"></a>Temel Bilgiler

| En iyi yöntem | Description |
|--|--|
| Çoğu senaryo için Kullanıcı akışları seçin | Azure AD B2C kimlik deneyimi çerçevesi, hizmetin temel kuvvetidir. İlkeler kaydolma, oturum açma veya profil düzenlemesi gibi kimlik deneyimlerini tamamen anlatmaktadır. En yaygın kimlik görevlerini ayarlamanıza yardımcı olması için Azure AD B2C portalı, Kullanıcı akışları adlı önceden tanımlanmış ve yapılandırılabilir ilkeler içerir. Kullanıcı akışları ile yalnızca birkaç tıklamayla, dakikalar içinde harika kullanıcı deneyimleri oluşturabilirsiniz. [Kullanıcı akışları ile özel ilkelerin ne zaman kullanılacağını öğrenin](user-flow-overview.md#comparing-user-flows-and-custom-policies).|
| Uygulama kayıtları | Güvenliği sağlanmakta olan her uygulama (Web, yerel) ve API Azure AD B2C kayıtlı olmalıdır. Bir uygulamada iOS ve Android 'in hem Web hem de yerel sürümü varsa, bunları aynı istemci KIMLIĞINE sahip Azure AD B2C bir uygulama olarak kaydedebilirsiniz. [OıDC, SAML, Web ve yerel uygulamaları nasıl kaydedeceğinizi](./tutorial-register-applications.md?tabs=applications)öğrenin. [Azure AD B2C için kullanılabilecek uygulama türleri](./application-types.md)hakkında daha fazla bilgi edinin. |
| Aylık etkin kullanıcıların faturalandırmasını taşıma | Azure AD B2C, aylık etkin kimlik doğrulamalarından aylık etkin kullanıcılar (MAU) faturalandırmaya taşınmıştır. Çoğu müşteri bu modeli uygun maliyetli olarak bulur. [Aylık etkin kullanıcıların faturalandırılması hakkında daha fazla bilgi edinin](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>Planlama ve tasarlama

Uygulamanızı ve hizmet mimarinizi tanımlayın, geçerli sistemleri envanter yapın ve Azure AD B2C için geçişinizi planlayın.

| En iyi yöntem | Description |
|--|--|
| Uçtan uca bir çözümü mimarın | Azure AD B2C tümleştirme planlarken tüm uygulamalarınızın bağımlılıklarını dahil edin. Şu anda ortamınızdaki veya çözüme eklenmesi gerekebilecek tüm hizmet ve ürünleri (örneğin, Azure Işlevleri, müşteri ilişkileri yönetimi (CRM) sistemleri, Azure API Management ağ geçidi ve depolama hizmetleri) göz önünde bulundurun. Tüm hizmetler için güvenliği ve ölçeklenebilirliği hesaba alın. |
| Kullanıcılarınızın deneyimlerini belgeleyin | Müşterilerinizin uygulamanızda yaşayabileceğiniz tüm kullanıcı hakkındaki ayrıntılarını bulabilirsiniz. Uygulamanızın kimliği ve profil yönleri ile etkileşim kurarken karşılaşabileceğiniz tüm ekranı ve dallanma akışlarını dahil edin. Planlamasına kullanılabilirlik, erişilebilirlik ve yerelleştirme dahil edin. |
| Doğru kimlik doğrulama protokolünü seçin |  Farklı uygulama senaryolarının ve önerilen kimlik doğrulama akışlarının bir dökümü için bkz. [senaryolar ve desteklenen kimlik doğrulama akışları](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| Pilot bir kavram kanıtı (POC) uçtan uca Kullanıcı deneyimi | [Microsoft kod örneklerimizle](code-samples.md) ve [topluluk örneklerimizden](https://github.com/azure-ad-b2c/samples)başlayın. |
| Geçiş planı oluşturma |Daha iyi planlama yapmak, geçişi daha sorunsuz hale getirir. [Kullanıcı geçişi](user-migration.md)hakkında daha fazla bilgi edinin.|
| Kullanılabilirlik ve güvenlik karşılaştırması | Çözümünüz, uygulama kullanılabilirliği ve kuruluşunuzun kabul edilebilir risk düzeyi arasındaki doğru dengeyi içermelidir. |
| Şirket içi bağımlılıkları buluta taşıyın | Dayanıklı bir çözümün sağlanmasına yardımcı olmak için mevcut uygulama bağımlılıklarını buluta taşımayı göz önünde bulundurun. |
| Mevcut uygulamaları b2clogin.com 'e geçirme | Login.microsoftonline.com 'in kullanımdan kaldırılması, 04 Aralık 2020 ' de tüm Azure AD B2C kiracılar için geçerli olacaktır. [Daha fazla bilgi edinin](b2clogin.md). |
| Kimlik koruması ve koşullu erişim kullanma | Riskli kimlik doğrulamaları ve erişim ilkeleri üzerinde önemli ölçüde daha fazla denetim için bu özellikleri kullanın. Azure AD B2C Premium P2 gereklidir. [Daha fazla bilgi edinin](conditional-access-identity-protection-overview.md). |

## <a name="implementation"></a>Uygulama

Uygulama aşamasında aşağıdaki önerileri göz önünde bulundurun.

| En iyi yöntem | Description |
|--|--|
| Visual Studio Code için Azure AD B2C uzantısıyla özel ilkeleri Düzenle | Visual Studio Code ve topluluk tarafından oluşturulan bu [uzantıyı Visual Studio Code marketi 'nden](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)indirin. Resmi bir Microsoft ürünü olmasa da, Visual Studio Code Azure AD B2C uzantısı özel ilkelerle çalışmayı kolaylaştırmak için çeşitli özellikler içerir. |
| Sorun gidermeyi öğrenin Azure AD B2C | Geliştirme sırasında [özel ilkelerle ilgili sorunları nasıl giderebileceğinizi](./troubleshoot-custom-policies.md?tabs=applications) öğrenin. Normal bir kimlik doğrulama akışının nasıl göründüğünü öğrenin ve bozukluklar ve hatalar bulmak için araçları kullanın. Örneğin, Kullanıcı günlüğünün çıktı günlüklerini gözden geçirmek için [Application Insights](troubleshoot-with-application-insights.md) kullanın. |
| Kendini kanıtlamış özel ilke desenleri ile yararlanın | Çeşitli gelişmiş Azure AD B2C Müşteri Kimliği ve erişim yönetimi (CıHAR) Kullanıcı ile ilgili [örnekleri](https://github.com/azure-ad-b2c/samples) bulun. |

## <a name="testing"></a>Test Etme

Azure AD B2C uygulamanızı test edin ve otomatikleştirin.

| En iyi yöntem | Description |
|--|--|
| Genel trafik hesabı | Performans ve yerelleştirme gereksinimlerini test etmek için farklı genel adresten gelen trafik kaynaklarını kullanın. Tüm HTMLs, CSS ve bağımlılıkların performans ihtiyaçlarınızı karşıladığından emin olun. |
| İşlevsel ve UI testi | Kullanıcı akışlarını uçtan uca test edin. Selenium, VS Web test vb. kullanarak, birkaç dakikada her bir yapay test ekleyin. |
| Kalem-test | Çözümünüz ile devam etmeden önce tüm bileşenlerin güvenli olduğunu doğrulamak için tüm üçüncü taraf bağımlılıkları dahil olmak üzere, sızma testi alıştırmaları gerçekleştirin. API 'lerinizi erişim belirteçleriyle güvenli hale geçirdiğinizi ve Uygulama senaryonuz için doğru kimlik doğrulama protokolünü kullandığınızı doğrulayın. [Sızma testi](../security/fundamentals/pen-testing.md) hakkında daha fazla bilgi edinin ve [katılım Için Microsoft bulut birleştirilmiş test kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1). |
| A/B testi | Yeni özelliklerinizi, tüm popülasyona göndermeden önce küçük ve rastgele bir Kullanıcı kümesiyle geçirin. Azure AD B2C JavaScript etkinken, Optimizely, netlik ve diğerleri gibi bir/B test araçlarıyla tümleştirilebilir. |
| Yük test etme | Azure AD B2C ölçeklendirebilir, ancak uygulamanız yalnızca tüm bağımlılıkları ölçeklendirebilen ölçeklendirebilir. API 'lerinizi ve CDN 'yi yükleme testi yapın. |
| Azaltma |  Kısa bir süre içinde aynı kaynaktan çok fazla istek gönderiliyorsa, trafiği kısıtlar Azure AD B2C. Yük testi sırasında birkaç trafik kaynağı kullanın ve `AADB2C90229` hata kodunu uygulamalarınızda düzgün şekilde işleyin. |
| Otomasyon | Test ve dağıtımları otomatik hale getirmek için sürekli tümleştirme ve teslim (CI/CD) işlem hatlarını kullanın, örneğin, [Azure DevOps](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Operations

Azure AD B2C ortamınızı yönetin.

| En iyi yöntem | Description |
|--|--|
| Birden çok ortam oluşturma | Daha kolay işlemler ve dağıtım toplaması için geliştirme, test, ön üretim ve üretim için ayrı ortamlar oluşturun. Her biri için Azure AD B2C kiracılar oluşturun. |
| Özel ilkeleriniz için sürüm denetimini kullanma | Azure AD B2C özel ilkeleriniz için GitHub, Azure Repos veya başka bir bulut tabanlı sürüm denetim sistemi kullanmayı düşünün. |
| B2C Kiracılarınızın yönetimini otomatikleştirmek için Microsoft Graph API 'sini kullanın | Microsoft Graph API 'Leri:<br/>[Kimlik deneyimi çerçevesini](/graph/api/resources/trustframeworkpolicy?preserve-view=true&view=graph-rest-beta) yönetme (özel ilkeler)<br/>[Anahtarlar](/graph/api/resources/trustframeworkkeyset?preserve-view=true&view=graph-rest-beta)<br/>[Kullanıcı Akışları](/graph/api/resources/identityuserflow?preserve-view=true&view=graph-rest-beta) |
| Azure DevOps ile tümleştirme | Bir [CI/CD işlem hattı](deploy-custom-policies-devops.md) , kodun farklı ortamlar arasında hareket etmelerini kolaylaştırır ve her zaman üretime hazır hale gelmesini sağlar.   |
| Azure Izleyici ile tümleştirme | [Denetim günlüğü olayları](view-audit-logs.md) yalnızca yedi gün boyunca tutulur. Uzun süreli kullanıma yönelik günlükleri [sürdürmek Için Azure izleyici Ile tümleştirin](azure-monitor.md) veya ortamınızda Öngörüler elde etmek için üçüncü taraf güvenlik bilgileri ve olay yönetimi (SIEM) araçlarıyla tümleştirin. |
| Etkin uyarı ve izleme kurulumu | Application Insights kullanarak Azure AD B2C [Kullanıcı davranışını izleyin](./analytics-with-application-insights.md) . |

## <a name="support-and-status-updates"></a>Destek ve durum güncelleştirmeleri

Hizmetin durumuyla ilgili güncel kalın ve destek seçeneklerini bulun.

| En iyi yöntem | Description |
|--|--|
| [Hizmet güncelleştirmeleri](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Azure AD B2C ürün güncelleştirmeleri ve duyuruları ile güncel kalın. |
| [Microsoft Destek](support-options.md) | Azure AD B2C teknik sorunları için bir destek isteği dosyası. Faturalandırma ve abonelik yönetim desteği ücretsiz olarak sunulmaktadır. |
| [Azure durumu](https://status.azure.com/status) | Tüm Azure hizmetlerinin geçerli sistem durumunu görüntüleyin. |