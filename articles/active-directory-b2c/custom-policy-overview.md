---
title: Özel ilkeye genel bakış Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C özel ilkelerle ve kimlik deneyimi çerçevesiyle ilgili bir konu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2a8aabac6960909f2a3d90fcee01cebb0ad7a832
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256948"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Azure AD B2C özel ilkeye genel bakış

Özel ilkeler, Azure Active Directory B2C (Azure AD B2C) kiracınızın davranışlarını tanımlayan yapılandırma dosyalarıdır. [Kullanıcı akışları](user-flow-overview.md) , en yaygın kimlik görevlerinin Azure AD B2C portalında önceden tanımlanmış olsa da, özel ilkeler birçok farklı görevi tamamlayabilmesi için bir kimlik geliştiricisi tarafından tamamen düzenlenebilir.

Özel bir ilke tamamen yapılandırılabilir ve ilke odaklı olur. Özel bir ilke, standart protokollerde varlıklar arasındaki güveni düzenler. Örneğin, OpenID Connect, OAuth, SAML ve standart olmayan birkaç farklı şekilde, örneğin REST API tabanlı sistemden sisteme talep alışverişi. Framework, Kullanıcı dostu, beyaz etiketli deneyimler oluşturur.

Özel bir ilke, bir veya daha fazla XML biçimli dosya olarak temsil edilir ve hiyerarşik bir zincirde birbirini ifade eder. XML öğeleri, derleme bloklarını, kullanıcıyla etkileşimi ve diğer tarafları ve iş mantığını tanımlar. 

## <a name="custom-policy-starter-pack"></a>Özel ilke başlangıç paketi

Azure AD B2C özel ilke [Starter Pack](tutorial-create-user-flows.md?pivots=b2c-custom-policy#get-the-starter-pack) , hızlı bir şekilde çalışmaya başlamanıza olanak sağlamak için önceden oluşturulmuş birkaç ilkeyle birlikte gelir. Bu başlangıç paketlerinin her biri, açıklanan senaryolara ulaşmak için gereken en az teknik profil ve Kullanıcı bağlantısı sayısını içerir:

- **LocalAccounts** -yalnızca yerel hesapların kullanılmasına izin verir.
- **Socialaccounts** -yalnızca sosyal (veya federal) hesapların kullanılmasını mümkün.
- **SocialAndLocalAccounts** -hem yerel hem de sosyal hesapların kullanımını mümkün. Örneklerimizin çoğu bu ilkeye başvurur.
- **SocialAndLocalAccountsWithMFA** -sosyal, yerel ve Multi-Factor Authentication seçeneklerini sunar.

## <a name="understanding-the-basics"></a>Temel bilgileri anlama 

### <a name="claims"></a>Talepler

Bir talep, Azure AD B2C ilkesi yürütmesi sırasında verilerin geçici olarak depolanmasını sağlar. Kullanıcı hakkındaki bilgileri, örneğin ilk adı, soyadı veya Kullanıcı veya diğer sistemlerden elde edilen başka herhangi bir talebi (talep alışverişleri) depolayabilirler. [Talep şeması](claimsschema.md) , taleplerinizi bildirdiğiniz yerdir. 

İlke çalıştığında, Azure AD B2C iç ve dış taraflara talepler gönderip alır ve ardından bu taleplerin bir alt kümesini, belirtecin bir parçası olarak bağlı olan taraf uygulamanıza gönderir. Talepler şu yollarla kullanılır: 

- Bir talep, dizin kullanıcı nesnesine göre kaydedilir, okur veya güncelleştirilir.
- Bir dış kimlik sağlayıcısından talep alındı.
- Talepler özel bir REST API hizmeti kullanılarak gönderilir veya alınır.
- Veriler, oturum açma veya profil düzenleme akışları sırasında kullanıcıdan talep olarak toplanır.

### <a name="manipulating-your-claims"></a>Taleplerinizi düzenleme

[Talep dönüştürmeleri](claimstransformations.md) , belirli bir talebi başka bir talebe dönüştürmek, bir talebi değerlendirmek veya bir talep değeri ayarlamak için kullanılabilen önceden tanımlanmış işlevlerdir. Örneğin, bir dize koleksiyonuna bir öğe ekleme, bir dizenin durumunu değiştirme veya tarih ve saat talebi değerlendirme. Bir talep dönüştürmesi bir Transform yöntemini belirtir. 

### <a name="customize-and-localize-your-ui"></a>Kullanıcı arabiriminizi özelleştirin ve yerelleştirin

Kullanıcılarınızın web tarayıcısında bir sayfa sunarak bilgi toplamak istediğinizde, [kendi kendine onaylanan teknik profili](self-asserted-technical-profile.md)kullanın. [Talepler eklemek ve Kullanıcı girişini özelleştirmek](./configure-user-input.md)için kendi kendini onaylanan teknik profilinizi düzenleyebilirsiniz.

Kendi kendini onaylanan teknik profiliniz için [Kullanıcı arabirimini özelleştirmek](customize-ui-with-html.md) üzere, özelleştirilmiş HTML içeriğiyle [İçerik tanımı](contentdefinitions.md) öğesinde bir URL belirtirsiniz. Kendi kendine onaylanan teknik profilde, bu içerik tanımı KIMLIĞI ' ni işaret edersiniz.

Dile özgü dizeleri özelleştirmek için [Yerelleştirme](localization.md) öğesini kullanın. Bir içerik tanımı, yüklenecek yerelleştirilmiş kaynakların bir listesini belirten bir [Yerelleştirme](localization.md) başvurusu içerebilir. Azure AD B2C, Kullanıcı arabirimi öğelerini URL 'nizden yüklenen HTML içeriğiyle birleştirir ve ardından sayfayı kullanıcıya görüntüler. 

## <a name="relying-party-policy-overview"></a>Bağlı olan taraf ilkesine genel bakış

SAML protokolünde bir hizmet sağlayıcı olarak bilinen bir bağlı olan taraf uygulaması, belirli bir Kullanıcı yolculuğu yürütmek için [bağlı olan taraf ilkesini](relyingparty.md) çağırır. Bağlı olan taraf ilkesi, yürütülecek Kullanıcı yolculuğunu ve belirtecin içerdiği taleplerin listesini belirtir. 

![İlke yürütme akışını gösteren diyagram](./media/custom-policy-overview/custom-policy-execution.png)

Aynı ilkeyi kullanan tüm bağlı olan taraf uygulamaları aynı belirteç taleplerini alır ve Kullanıcı aynı kullanıcı yolculuğuna gider.

### <a name="user-journeys"></a>Kullanıcı yolculukları

[Kullanıcı yolculukları](userjourneys.md) , uygulamanıza erişmek için kullanıcının takip edecesiyle birlikte iş mantığını tanımlamanızı sağlar. Kullanıcı, uygulamanıza sunulacak talepleri almak için Kullanıcı yolculuğu üzerinden alınır. Kullanıcı yolculuğu bir [düzenleme adımı](userjourneys.md#orchestrationsteps)dizisinden oluşturulmuştur. Bir Kullanıcı belirteç almak için son adıma ulaşmalıdır. 

Aşağıdaki yönergelerde, [sosyal ve yerel hesap Başlatıcı paketi](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) ilkesine düzenleme adımlarını nasıl ekleyebileceğiniz açıklanır. Eklenen REST API çağrının bir örneği aşağıda verilmiştir.

![özelleştirilmiş Kullanıcı yolculuğu](media/custom-policy-overview/user-journey-flow.png)


### <a name="orchestration-steps"></a>Düzenleme adımları

Düzenleme adımı amaçlanan amacını veya işlevselliğini uygulayan bir yönteme başvurur. Bu yönteme [Teknik bir profil](technicalprofiles.md)denir. Kullanıcı yolculuğunda iş mantığını daha iyi temsil etmek için dallandırma gerektiğinde, düzenleme adımı [alt yolculuğa](subjourneys.md)başvurur. Bir alt yolculuğun kendi düzenleme adımları kümesi vardır.

Bir Kullanıcı bir belirteci almak için Kullanıcı yolculuğunda son düzenleme adımına ulaşmalıdır. Ancak kullanıcıların tüm düzenleme adımlarını dolaşmaları gerekebilir. Düzenleme adımları düzenleme adımında tanımlanan [önkoşullara](userjourneys.md#preconditions) göre koşullu olarak çalıştırılabilir. 

Bir düzenleme adımı tamamlandıktan sonra, Azure AD B2C çıktıların talep **çantasında** depolar. Talep çantasındaki talepler, Kullanıcı yolculuğunda daha fazla düzenleme adımı tarafından kullanılabilir.

Aşağıdaki diyagramda Kullanıcı yolculuğun düzenleme adımlarının talep çantasına nasıl erişebileceği gösterilmektedir.

![Azure AD B2C Kullanıcı yolculuğu](media/custom-policy-overview/user-journey-diagram.png)

### <a name="technical-profile"></a>Teknik profil

Teknik bir profil, farklı türlerde taraflar ile iletişim kurmak için bir arabirim sağlar. Kullanıcı yolculuğu, iş mantığınızı tanımlamak için düzenleme adımları aracılığıyla teknik profilleri çağırmayı birleştirir.

Tüm teknik profil türleri aynı kavramı paylaşır. Giriş talepleri gönderir, talep dönüşümünü çalıştırır ve yapılandırılmış tarafla iletişim kurun. İşlem tamamlandıktan sonra teknik profil, istek paketine giden çıkış taleplerini döndürür. Daha fazla bilgi için bkz. [Teknik profillere genel bakış](technicalprofiles.md).

### <a name="validation-technical-profile"></a>Doğrulama teknik profili

Bir Kullanıcı Kullanıcı arabirimiyle etkileşime geçtiğinde, toplanan verileri doğrulamak isteyebilirsiniz. Kullanıcıyla etkileşime geçmek için, [kendiliğinden onaylanan bir teknik profilin](self-asserted-technical-profile.md) kullanılması gerekir.

Kullanıcı girişini doğrulamak için, kendi kendine onaylanan teknik profilden bir [doğrulama teknik profili](validation-technical-profile.md) çağırılır. Doğrulama teknik profili, etkileşimli olmayan teknik bir profili çağırmak için bir yöntemdir. Bu durumda, teknik profil çıkış taleplerini veya bir hata iletisi döndürebilir. Hata iletisi kullanıcıya ekranda işlenir ve kullanıcının yeniden denenmesine izin verir.

Aşağıdaki diyagramda, Azure AD B2C kullanıcının kimlik bilgilerini doğrulamak için bir doğrulama teknik profili nasıl kullandığı gösterilmektedir.

![Doğrulama teknik profili diyagramı](media/custom-policy-overview/validation-technical-profile.png)

## <a name="inheritance-model"></a>Devralma modeli

Her başlatıcı paketi aşağıdaki dosyaları içerir:

- Tanımlarının çoğunu içeren bir **temel** dosya. İlkeleriniz için sorun giderme ve uzun süreli bakımda yardımcı olması için bu dosyada yaptığınız değişikliklerin sayısını en aza indirmenize çalışın.
- Kiracınız için benzersiz yapılandırma değişikliklerini tutan bir **uzantı** dosyası. Bu ilke dosyası temel dosyadan türetilir. Yeni işlevsellik eklemek veya var olan işlevleri geçersiz kılmak için bu dosyayı kullanın. Örneğin, bu dosyayı yeni kimlik sağlayıcılarıyla federasyona eklemek için kullanın.
- Web, mobil veya masaüstü uygulamalarınız gibi bağlı olan taraf uygulaması tarafından doğrudan çağrılan tek bir görev odaklı dosya olan bir **bağlı olan taraf (RP)** dosyası. Kaydolma, oturum açma, parola sıfırlama veya profil düzenleme gibi her benzersiz görev, kendi bağlı olan taraf ilkesi dosyasını gerektirir. Bu ilke dosyası, uzantılar dosyasından türetilir.

Devralma modeli aşağıdaki gibidir:

- Herhangi bir düzeydeki alt ilke üst ilkeden devralınabilir ve yeni öğeler ekleyerek genişletebilirler.
- Daha karmaşık senaryolar için daha fazla devralma düzeyi (toplamda en fazla 10) ekleyebilirsiniz.
- Daha fazla bağlı olan taraf ilkesi ekleyebilirsiniz. Örneğin, Hesabımı Sil, telefon numarası, SAML bağlı olan taraf ilkesini ve daha fazlasını değiştirin.

Aşağıdaki diyagramda, ilke dosyaları ve bağlı olan taraf uygulamaları arasındaki ilişki gösterilmektedir.

![Güven çerçevesi ilke devralma modelini gösteren diyagram](media/custom-policy-overview/policies.png)


## <a name="guidance-and-best-practices"></a>Rehberlik ve en iyi deneyimler

### <a name="best-practices"></a>En iyi uygulamalar

Azure AD B2C özel bir ilke içinde, kendi iş mantığınızı tümleştirmenize gerek duyduğunuz kullanıcı deneyimlerini oluşturabilir ve hizmetin işlevselliğini genişletebilirsiniz. Kullanmaya başlamak için en iyi uygulama ve önerilerden oluşan bir kümesidir.

- **Uzantı ilkesi** veya **bağlı olan taraf ilkesi** içinde mantığınızı oluşturun. Aynı KIMLIĞE başvurarak temel ilkeyi geçersiz kılacak yeni öğeler ekleyebilirsiniz. Bu, Microsoft 'un yeni başlangıç paketleri yayımlarsa temel ilkeyi daha sonra yükseltmeyi kolaylaştırırken projenizi ölçeklendirmenize olanak tanır.
- **Temel ilke** içinde herhangi bir değişiklik yapmaktan kaçınıyoruz. Gerektiğinde, değişikliklerin yapıldığı yere yorum yapın.
- Teknik profil meta verileri gibi bir öğeyi geçersiz kıldığınızda, tüm teknik profilin temel ilkeden kopyalanmasını önleyin. Bunun yerine, öğesinin yalnızca gerekli kısmını kopyalayın. Değişikliğin nasıl yapılacağını gösteren bir örnek için bkz. [e-posta doğrulamayı devre dışı bırakma](./disable-email-verification.md) .
- Temel işlevlerin paylaşıldığı teknik profillerin çoğaltılmasını azaltmak için [Teknik profille içerme](technicalprofiles.md#include-technical-profile)özelliğini kullanın.
- Oturum açma sırasında Azure AD dizinine yazmaktan kaçının, bu da azaltma sorunlarına yol açabilir.
- İlkenize REST API 'Ler gibi dış bağımlılıklar varsa, bunların yüksek oranda kullanılabilir olduklarından emin olur.
- Daha iyi bir kullanıcı deneyimi için, özel HTML şablonlarınızın [çevrimiçi içerik teslimi](../cdn/index.yml)kullanılarak küresel bir şekilde dağıtıldığından emin olun. Azure Content Delivery Network (CDN), yükleme sürelerini azaltmanıza, bant genişliğinden tasarruf etmenize ve yanıt hızını iyileştirmenize olanak tanır.
- Kullanıcı yolculuğunda bir değişiklik yapmak istiyorsanız, tüm Kullanıcı yolculuğunu temel ilkeden uzantı ilkesine kopyalayın. Kopyaladığınız Kullanıcı yolculuğuna benzersiz bir Kullanıcı yolculuğu KIMLIĞI sağlayın. Ardından [bağlı olan taraf ilkesinde](relyingparty.md), [Varsayılan Kullanıcı](relyingparty.md#defaultuserjourney) yolculuğu öğesini yeni Kullanıcı yolculuğuna işaret etmek üzere değiştirin.

## <a name="troubleshooting"></a>Sorun giderme

Azure AD B2C ilkeleriyle geliştirme yaparken, Kullanıcı yolculuğunu yürütürken hatalarla veya özel durumlarla karşılaşabilirsiniz. Application Insights kullanılarak araştırılır.

- [Özel durumları tanılamak](troubleshoot-with-application-insights.md)için Application Insights Azure AD B2C ile tümleştirin.
- [Visual Studio Code için Azure AD B2C uzantısı](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) , ilke adı ve saatine göre günlüklere erişmenize ve bunları [görselleştirmenize](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md) yardımcı olabilir.
- Özel ilkeleri ayarlamadaki en yaygın hata hatalı biçimli XML 'dir. XML dosyanızı karşıya yüklemeden önce hataları belirlemek için [XML şema doğrulamasını](troubleshoot-custom-policies.md) kullanın.

## <a name="continuous-integration"></a>Sürekli tümleştirme

Azure Pipelines ' de ayarladığınız bir sürekli tümleştirme ve teslim (CI/CD) işlem hattı kullanarak, yazılım teslimine ve kod denetim otomasyonuna [Azure AD B2C özel ilkelerinizi dahil](deploy-custom-policies-devops.md) edebilirsiniz. Geliştirme, test ve üretim gibi farklı Azure AD B2C ortamlara dağıtırken, el ile işlemleri kaldırmanızı ve Azure Pipelines kullanarak otomatikleştirilmiş test gerçekleştirmenizi öneririz.

## <a name="prepare-your-environment"></a>Ortamınızı hazırlama

Azure AD B2C özel ilke ile çalışmaya başlayın:

1. [Azure AD B2C kiracısı oluşturma](tutorial-create-tenant.md)
1. Azure portal kullanarak [bir Web uygulaması kaydedin](tutorial-register-applications.md) , böylece ilkenizi test edebilirsiniz.
1. Gerekli [ilke anahtarlarını](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-signing-and-encryption-keys) ekleyin ve [kimlik deneyimi çerçevesi uygulamalarını kaydedin](tutorial-create-user-flows.md?pivots=b2c-custom-policy#register-identity-experience-framework-applications).
1. [Azure AD B2C İlkesi başlangıç paketini alın](tutorial-create-user-flows.md?pivots=b2c-custom-policy#get-the-starter-pack) ve kiracınıza yükleyin. 
1. Başlangıç paketini karşıya yükledikten sonra [kaydolma veya oturum açma ilkenizi test](tutorial-create-user-flows.md?pivots=b2c-custom-policy#test-the-custom-policy)edin.
1. [Visual Studio Code](https://code.visualstudio.com/) indirip yüklemenizi öneririz (vs Code). Visual Studio Code, masaüstünüzde çalışan ve Windows, macOS ve Linux 'ta kullanılabilen hafif ancak güçlü bir kaynak kod düzenleyicidir. VS Code, [vs Code için Azure AD B2C uzantısını](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) yükleyerek Azure AD B2C özel ilke XML dosyalarınızda hızlıca gezinebilirsiniz ve düzenleyebilirsiniz
 
## <a name="next-steps"></a>Sonraki adımlar

Azure AD B2C ilkenizi ayarlayıp test ettikten sonra ilkenizi özelleştirmeye başlayabilirsiniz. Aşağıdakilerin nasıl yapılacağını öğrenmek için aşağıdaki makalelere bakın:

- Özel ilkeler kullanarak [talepler ekleyin ve Kullanıcı girişini özelleştirin](./configure-user-input.md) . Bir talep tanımlama ve Kullanıcı arabirimine bir talep ekleme hakkında bilgi edinmek için bazı başlangıç paketi teknik profillerini özelleştirin.
- Özel bir ilke kullanarak uygulamanızın [Kullanıcı arabirimini özelleştirin](customize-ui-with-html.md) . Kendi HTML içeriğinizi oluşturmayı ve içerik tanımını özelleştirmeyi öğrenin.
- Özel bir ilke kullanarak uygulamanızın [Kullanıcı arabirimini yerelleştirin](./language-customization.md) . Desteklenen dillerin listesini ayarlamayı ve yerelleştirilmiş kaynaklar öğesini ekleyerek dile özgü Etiketler sağlamayı öğrenin.
- İlke geliştirme ve test etme işlemleri sırasında [e-posta doğrulamasını devre dışı](./disable-email-verification.md)bırakabilirsiniz. Teknik profil meta verilerinin üzerine yazmayı öğrenin.
- Özel ilkeler kullanarak [bir Google hesabı ile oturum açma ayarlayın](./identity-provider-google.md) . OAuth2 Technical profile ile yeni bir talep sağlayıcı oluşturmayı öğrenin. Ardından Kullanıcı yolculuğu 'nı Google oturum açma seçeneğini içerecek şekilde özelleştirin.
- Özel ilkeleriniz ile ilgili sorunları tanılamak için, [Application Insights Azure Active Directory B2C günlüklerini toplayabilirsiniz](troubleshoot-with-application-insights.md). Yeni teknik profillerin nasıl ekleneceğini ve bağlı olan taraf ilkenizi nasıl yapılandıracağınızı öğrenin.