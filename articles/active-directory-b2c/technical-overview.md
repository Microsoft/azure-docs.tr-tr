---
title: Teknik ve özelliklere genel bakış-Azure Active Directory B2C
description: Azure Active Directory B2C sürümündeki özelliklere ve teknolojilere ayrıntılı bir giriş.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4d45e4c79f46061ca177858fd517153fb5f29c41
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123829"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Azure Active Directory B2C teknik ve özelliklere genel bakış

[Azure Active Directory B2C hakkında](active-directory-b2c-overview.md)bir yardımcı olan bu makalede, hizmete yönelik daha ayrıntılı bir giriş sunulmaktadır. Burada, hizmette, üzerinde çalıştığınız birincil kaynaklar, özellikleri ve bunların uygulamalarınızda müşterileriniz için tam özel kimlik deneyimi sağlamanıza nasıl olanak sağladığı açıklanmıştır.

## <a name="azure-ad-b2c-tenant"></a>Azure AD B2C kiracı

Azure Active Directory B2C (Azure AD B2C) ' de *kiracı* , kuruluşunuzu temsil eder ve bir Kullanıcı dizinidir. Her Azure AD B2C kiracısı benzersizdir ve diğer Azure AD B2C kiracılarından ayrıdır. Bir Azure AD B2C kiracısı, zaten sahip olabilecek bir Azure Active Directory kiracısından farklı.

Azure AD B2C kiracısında birlikte çalıştığınız birincil kaynaklar şunlardır:

* **Dizin** - *Dizin* , Azure AD B2C kullanıcılarınızın kimlik bilgilerini ve profil verilerini ve uygulama kayıtlarınızı nerede depoladığını de içerir.
* **Uygulama kayıtları** -kimlik yönetimini etkinleştirmek için Web, mobil ve yerel uygulamalarınızı Azure AD B2C kaydedersiniz. Ayrıca, Azure AD B2C korumak istediğiniz tüm API 'Ler.
* **Kullanıcı akışları** ve **özel ilkeler** -uygulamalarınız için yerleşik (Kullanıcı akışları) ve tamamen özelleştirilebilir (özel ilkeler) kimlik deneyimleri.
  * Kaydolma, oturum açma ve profil düzenlemesi gibi ortak kimlik görevlerinin hızlı yapılandırılması ve etkinleştirilmesi için *Kullanıcı akışlarını* kullanın.
  * Yalnızca ortak kimlik görevleri için değil kullanıcı deneyimlerini etkinleştirmek için *özel ilkeler* kullanın, ancak kuruluşunuz, müşteriler, çalışanlar, iş ortakları ve vatandaşları için benzersiz olan karmaşık kimlik iş akışları için de destek sağlar.
* **Kimlik sağlayıcıları** -Federasyon ayarları:
  * Uygulamalarınızda desteklemek istediğiniz Facebook, LinkedIn veya Twitter gibi *sosyal* kimlik sağlayıcıları.
  * OAuth 2,0, OpenID Connect gibi standart kimlik protokollerini destekleyen *dış* kimlik sağlayıcıları.
  * Kullanıcıların bir Kullanıcı adı (veya e-posta adresi veya diğer KIMLIK) ve parola ile kaydolup oturum açmasını sağlayan *Yerel* hesaplar.
* **Anahtarlar** -belirteçleri imzalamak ve doğrulamak için şifreleme anahtarları ekleyin ve yönetin.

Azure AD B2C kiracı, Azure AD B2C kullanmaya başlamak için oluşturmanız gereken ilk kaynaktır. [Öğreticide nasıl bilgi alabileceğinizi öğrenin: Azure Active Directory B2C kiracı](tutorial-create-tenant.md)oluşturun.

## <a name="accounts-in-azure-ad-b2c"></a>Azure AD B2C hesaplar

Azure AD B2C birkaç kullanıcı hesabı türünü tanımlar. Azure Active Directory, B2B Azure Active Directory ve Azure Active Directory B2C bu hesap türlerini paylaşmalıdır.

* **İş hesabı** -iş hesapları olan kullanıcılar bir Kiracıdaki kaynakları yönetebilir ve bir yönetici rolüyle, kiracılar da yönetebilir. İş hesabı olan kullanıcılar yeni tüketici hesapları oluşturabilir, parolaları sıfırlayabilir, hesapları engelleyebilir/engellemeyi kaldırabilir ve güvenlik grubuna bir hesap atayabilir.
* **Konuk hesap** -dış kullanıcılar kiracınıza Konuk olarak davet edersiniz. Konuk kullanıcıyı Azure AD B2C kiracınıza davet etmek için tipik bir senaryo, yönetim sorumluluklarını paylaşmalıdır.
* **Tüketici hesabı** -tüketici hesapları, kullanıcılar kiracınızda kaydettiğiniz bir uygulamada kaydolma Kullanıcı yolculuğunu tamamlamada Azure AD B2C dizininizde oluşturulan hesaplardır.

![Azure portal Azure AD B2C Kullanıcı Yönetimi sayfası](media/technical-overview/portal-01-users.png)<br/>*Rakam Azure portal içindeki bir Azure AD B2C kiracısındaki Kullanıcı dizini*

### <a name="consumer-accounts"></a>Tüketici hesapları

Bir *Tüketici* hesabıyla, kullanıcılar Azure AD B2C ile güvenli hale getirilmiş uygulamalarda oturum açabilirler. Ancak tüketici hesabı olan kullanıcılar, Azure kaynaklarına (örneğin, Azure portal) erişemez.

Bir tüketici hesabı şu kimlik türleriyle ilişkilendirilebilir:

* **Yerel** kimlik, Kullanıcı adı ve parola Azure AD B2C dizininde yerel olarak depolanır. Genellikle bu kimliklere "yerel hesaplar" olarak başvurduk.
* Kullanıcı kimliğinin Facebook, Microsoft, ADFS veya Salesforce gibi bir federal kimlik sağlayıcısı tarafından yönetildiği **sosyal** veya **kuruluş** kimlikleri.

Tüketici hesabı olan bir Kullanıcı, örneğin Kullanıcı adı, e-posta, çalışan KIMLIĞI, kamu KIMLIĞI ve diğerleri gibi birden çok kimlikle oturum açabilir. Tek bir hesabın hem yerel hem de sosyal birden çok kimliği olabilir.

![Tüketici hesabı kimlikleri](media/technical-overview/identities.png)<br/>*Rakam Azure AD B2C birden çok kimliği olan tek bir tüketici hesabı*

Azure AD B2C, görünen ad, soyadı, verilen ad, şehir ve diğerleri gibi tüketici hesabı profillerinin ortak özniteliklerini yönetmenizi sağlar. Ayrıca, Azure AD şemasını kullanıcılarınız hakkındaki ek bilgileri depolamak için genişletebilirsiniz. Örneğin, ülkelerinin veya bir bültene abone olmak veya Multi-Factor Authentication 'ı etkinleştirmek isteyip istemedikleri gibi, bu kişilerin ülke veya yerleşimi, tercih edilen dili ve tercihleri.

[Azure Active Directory B2C Kullanıcı hesaplarına genel bakış](user-overview.md)Azure AD B2C içindeki kullanıcı hesabı türleri hakkında daha fazla bilgi edinin.

## <a name="external-identity-providers"></a>Harici kimlik sağlayıcıları

Azure AD B2C, kullanıcıların uygulamanızda dış sosyal veya kurumsal kimlik sağlayıcılarından (IDP) kimlik bilgileriyle oturum açmalarına izin verecek şekilde yapılandırabilirsiniz. Azure AD B2C, Facebook, Microsoft hesabı, Google, Twitter ve OAuth 1,0, OAuth 2,0, OpenID Connect, SAML veya WS-Federation protokollerini destekleyen herhangi bir kimlik sağlayıcısı gibi harici kimlik sağlayıcılarını destekler.

![Harici kimlik sağlayıcıları](media/technical-overview/external-idps.png)

Dış kimlik sağlayıcısı Federasyonu sayesinde, tüketicilere yalnızca uygulamanız için yeni bir hesap oluşturmak zorunda kalmadan mevcut sosyal veya kurumsal hesaplarıyla oturum açma olanağı sunabilirsiniz.

Kaydolma veya oturum açma sayfasında, kullanıcının oturum açma için seçecan dış kimlik sağlayıcılarının bir listesini Azure AD B2C gösterir. Dış kimlik sağlayıcılarından birini seçtikten sonra, oturum açma işlemini tamamlaması için seçili sağlayıcının web sitesine alınır (yeniden yönlendirilir). Kullanıcı başarıyla oturum açtıktan sonra, uygulamanızdaki hesabın kimlik doğrulaması için Azure AD B2C geri döndürülürsünüz.

![Sosyal hesap (Facebook) ile mobil oturum açma örneği](media/technical-overview/external-idp.png)

Azure AD B2C kimlik sağlayıcılarının nasıl ekleneceğini öğrenmek için bkz [. Öğretici: Azure Active Directory B2C](tutorial-add-identity-providers.md)' deki uygulamalarınıza kimlik sağlayıcıları ekleyin.

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Kimlik deneyimleri: Kullanıcı akışları veya özel ilkeler

Azure AD B2C Genişletilebilir ilke çerçevesi, çekirdek kuvvetinin. İlkeler, kullanıcılarınızın kaydolma, oturum açma ve profil düzenlemesi gibi kimlik deneyimlerini anlatmaktadır.

Azure AD B2C, bu kimlik deneyimlerini sağlamak için uygulayabileceğiniz iki birincil yol vardır: Kullanıcı akışları ve özel ilkeler.

* **Kullanıcı akışları** , sağladığımız önceden tanımlanmış, yerleşik ve yapılandırılabilir ilkeleridir. bu sayede, dakikalar içinde kaydolma, oturum açma ve ilke düzenlemeyle ilgili deneyimler oluşturabilirsiniz.

* **Özel ilkeler** , karmaşık kimlik deneyimi senaryoları için kendi Kullanıcı yolculukları oluşturmanızı sağlar.

Hem Kullanıcı akışları hem de özel ilkeler, *kimlik deneyimi çerçevesi*, Azure AD B2C's Policy Orchestration Engine tarafından desteklenmektedir.

### <a name="user-flow"></a>Kullanıcı akışı

En yaygın kimlik görevlerini hızlı bir şekilde ayarlamanıza yardımcı olması için Azure portal, *Kullanıcı akışları*adlı birkaç önceden tanımlı ve yapılandırılabilir ilke içerir.

Uygulamalarınızda kimlik deneyimi davranışlarını denetlemek için aşağıdaki gibi Kullanıcı akış ayarlarını yapılandırabilirsiniz:

* Facebook gibi sosyal hesaplar veya oturum açma için bir e-posta adresi ve parola kullanan yerel hesaplar gibi oturum açma için kullanılan hesap türleri
* Tüketiciden toplanacak öznitelikler (örneğin, ilk ad, posta kodu veya yeniden kullanım ülkesi)
* Azure Multi-Factor Authentication (MFA)
* Kullanıcı arabiriminin özelleştirilmesi
* Kullanıcı Kullanıcı akışını tamamladıktan sonra uygulamanızın aldığı bir belirteçte talepler kümesi
* Oturum yönetimi
* ... ve daha fazlası.

Mobil, Web ve tek sayfalı uygulamaların çoğunluğu için en yaygın kimlik senaryoları, Kullanıcı akışlarıyla etkili bir şekilde tanımlanabilir ve uygulanabilir. Özel ilkelerin tam esnekliğini gerektiren karmaşık Kullanıcı yolculuğu senaryolarınız yoksa yerleşik Kullanıcı akışlarını kullanmanızı öneririz.

[Azure Active Directory B2C Kullanıcı akışlardaki](active-directory-b2c-reference-policies.md)Kullanıcı akışları hakkında daha fazla bilgi edinin.

### <a name="custom-policy"></a>Özel ilke

Özel ilkeler, kimlik deneyimi çerçevesi (ıEF) düzenleme altyapısının tam gücüne erişimi kaldırır. Özel ilkeler sayesinde, Imagine 'yi kullanarak neredeyse tüm kimlik doğrulaması, Kullanıcı kaydı ve profil düzenlemesi deneyimi oluşturabilirsiniz.

Kimlik deneyimi çerçevesi, herhangi bir adım birleşimiyle Kullanıcı yolculukları oluşturma olanağı sağlar. Örneğin:

* Diğer kimlik sağlayıcılarıyla federasyona ekleme
* Birinci ve üçüncü taraf Multi-Factor Authentication (MFA) sorunları
* Herhangi bir kullanıcı girişini toplayın
* REST API iletişimini kullanarak dış sistemlerle tümleştirme

Her Kullanıcı yolculuğu bir ilke tarafından tanımlanır ve kuruluşunuz için en iyi kullanıcı deneyimini etkinleştirmek için ihtiyaç duyduğunuz kadar çok sayıda ilke oluşturabilirsiniz.

![IEF tarafından etkinleştirilen karmaşık Kullanıcı yolculuğunun bir örneğini gösteren diyagram](media/technical-overview/custom-policy.png)

Özel bir ilke, hiyerarşik bir zincirde birbirlerine başvuran birkaç XML dosyası tarafından tanımlanır. XML öğeleri, talep şemasını, talep dönüştürmelerini, içerik tanımlarını, talep sağlayıcılarını, teknik profilleri, Kullanıcı yolculuğu düzenleme adımlarını ve kimlik deneyiminin diğer yönlerini tanımlar.

Özel ilkelerin güçlü esnekliği en iyi şekilde karmaşık kimlik senaryoları oluşturmanız gerektiğinde uygundur. Özel ilkeleri yapılandıran geliştiriciler, her bir kimlik sağlayıcısı için gerektiğinde meta veri uç noktalarını, tam talep istekleri Exchange tanımlarını ve parolaları, anahtarları ve sertifikaları yapılandırmak için güvenilir ilişkileri dikkatle tanımlamalıdır.

[Azure Active Directory B2C özel ilkelerindeki](active-directory-b2c-overview-custom.md)özel ilkeler hakkında daha fazla bilgi edinin.

## <a name="protocols-and-tokens"></a>Protokoller ve belirteçler

Azure AD B2C, Kullanıcı yolculukları için [OpenID Connect ve OAuth 2,0 protokollerini](active-directory-b2c-reference-protocols.md) destekler. OpenID Connect'in Azure AD B2C gerçekleştirmesinde uygulamanız kullanıcı yolculuğunu Azure AD B2C'ye kimlik doğrulama istekleri göndererek başlatır.

Azure AD B2C bir isteğin sonucu, [kimlik belirteci veya erişim belirteci](active-directory-b2c-reference-tokens.md)gibi bir güvenlik belirtecidir. Bu güvenlik belirteci, kullanıcının kimliğini tanımlar. Belirteçler, `/token` veya `/authorize` uç nokta gibi Azure AD B2C uç noktalarından alınır. Bu belirteçlerle, bir kimliği doğrulamak ve güvenli kaynaklara erişim sağlamak için kullanılabilecek taleplere erişebilirsiniz.

Dış kimlikler için Azure AD B2C, OAuth 1,0, OAuth 2,0, OpenID Connect, SAML ve WS-beslik kimlik sağlayıcısı ile Federasyonu destekler.

![SAML tabanlı IDP ile federıdc tabanlı istemci uygulaması Federasyonun diyagramı](media/technical-overview/protocols.png)

Yukarıdaki diyagramda Azure AD B2C aynı kimlik doğrulama akışındaki çeşitli protokoller kullanılarak nasıl iletişim kurabileceği gösterilmektedir:

1. Bağlı olan taraf uygulaması, OpenID Connect kullanarak Azure AD B2C bir yetkilendirme isteği başlatır.
1. Uygulamanın bir kullanıcısı SAML protokolünü kullanan bir dış kimlik sağlayıcısı kullanarak oturum açmayı seçtiğinde Azure AD B2C, bu kimlik sağlayıcısıyla iletişim kurmak için SAML protokolünü çağırır.
1. Kullanıcı, dış kimlik sağlayıcısıyla oturum açma işlemini tamamladıktan sonra, Azure AD B2C OpenID Connect kullanarak, bağlı olan taraf uygulamasına belirteci döndürür.

## <a name="application-integration"></a>Uygulama tümleştirme

Bir Kullanıcı uygulamanızda oturum açmak istediğinde bir Web, mobil, masaüstü veya tek sayfalı uygulama (SPA) olduğunda, uygulama bir Kullanıcı akışına veya özel ilkeye göre belirtilen uç noktaya yetkilendirme isteği başlatır. Kullanıcı akışı veya özel ilke, kullanıcının deneyimini tanımlar ve denetler. Bir kullanıcı akışını tamamlarsa (örneğin, *kaydolma veya oturum açma* akışı), Azure AD B2C bir belirteç oluşturur ve kullanıcıyı uygulamanıza geri yönlendirir.

![Azure AD B2C oturum açma sayfası arasında akışı gösteren oklar içeren mobil uygulama](media/technical-overview/app-integration.png)

Birden çok uygulama aynı kullanıcı akışını veya özel ilkeyi kullanabilir. Tek bir uygulama, birden çok Kullanıcı akışı veya özel ilke kullanabilir.

Örneğin, bir uygulamada oturum açmak için uygulama *kaydolma veya oturum açma* Kullanıcı akışını kullanır. Kullanıcı oturum açtıktan sonra *, profil düzenleme kullanıcı akışını* kullanarak bu kez uygulamanın başka bir yetkilendirme isteği başlattığı şekilde profilini düzenlemek isteyebilir.

## <a name="seamless-user-experiences"></a>Sorunsuz kullanıcı deneyimleri

Azure AD B2C, kullanıcılarınızın kimlik deneyimlerini, gösterilen sayfaların markanızdaki görünüm ve hisde sorunsuzca karışmasını sağlayacak şekilde oluşturabilir. Kullanıcılarınızın kimlik yolculukları boyunca ilerlerse kullanıcılarınıza sunulan HTML ve CSS içeriğinin neredeyse tam denetimini alırsınız. Bu esneklikle, uygulamanız ve Azure AD B2C arasında marka ve görsel tutarlılık sağlayabilirsiniz.

![Marka özelleştirilmiş kaydolma oturum açma sayfasının ekran görüntüleri](media/technical-overview/seamless-ux.png)

UI özelleştirmesi hakkında daha fazla bilgi için, [Azure Active Directory B2C içindeki kullanıcı arabirimi özelleştirmesi hakkında](customize-ui-overview.md)bölümüne bakın.

## <a name="localization"></a>Yerelleştirme

Azure AD B2C dil özelleştirmesi, müşteri gereksinimlerinize uyacak farklı dillere sahip etmenize olanak tanır. Microsoft, 36 dil için Çeviriler sağlar, ancak herhangi bir dil için kendi çevirilerinizi de sağlayabilirsiniz. Deneyiminiz yalnızca tek bir dil için sağlanmış olsa bile, sayfalardaki tüm metinleri özelleştirebilirsiniz.

![Farklı dillerde UI metnini gösteren üç kaydolma oturum açma sayfası](media/technical-overview/localization.png)

[Azure Active Directory B2C içinde, yerelleştirme 'Nin dil özelleştirmelerinde](active-directory-b2c-reference-language-customization.md)nasıl çalıştığını görün.

## <a name="add-your-own-business-logic"></a>Kendi iş mantığınızı ekleme

Özel ilkeler kullanmayı tercih ederseniz, kendi iş mantığınızı yolculuğa eklemek için Kullanıcı yolculuğunda bir Resteki API ile tümleştirilebilir. Örneğin, Azure AD B2C, yeniden yapılan bir hizmetle veri alışverişi gerçekleştirebilir:

* Özel Kullanıcı dostu hata iletilerini görüntüleyin.
* Hatalı biçimlendirilmiş verilerin Kullanıcı dizininizde kalıcı olmasını engellemek için Kullanıcı girişini doğrulayın. Örneğin, Kullanıcı tarafından girilen verileri değiştirebilirsiniz (örneğin, tüm küçük harfli bir ad girdiklerinde ilk adını büyük harfe dönüştürme).
* Şirket iş kolu uygulamanızla daha da tümleştirerek Kullanıcı verilerini zenginleştirin.
* Yeniden yapılan çağrıları kullanarak anında iletme bildirimleri gönderebilir, kurumsal veritabanlarını güncelleştirebilir, bir kullanıcı geçiş işlemi çalıştırabilir, izinleri yönetebilir, veritabanlarını denetleyebilir ve daha fazlasını yapabilirsiniz.

Bağlılık programı programları, REST API 'Leri çağırmak için Azure AD B2C's desteği tarafından etkinleştirilen başka bir senaryodur. Örneğin, yeniden deneme hizmetiniz bir kullanıcının e-posta adresini alabilir, müşteri veritabanınızı sorgulayabilir ve sonra Azure AD B2C için kullanıcının bağlılık programı numarasını döndürebilir. Dönüş verileri kullanıcının dizin hesabında Azure AD B2C, ardından ilkedeki sonraki adımlarda daha fazla değerlendirilebilecek veya erişim belirtecine dahil edilebilir.

![Bir mobil uygulamada iş kolu tümleştirmesi](media/technical-overview/lob-integration.png)

Özel bir ilke tarafından tanımlanan Kullanıcı yolculuğu içindeki herhangi bir adımda REST API çağrısı ekleyebilirsiniz. Örneğin, bir REST API çağırabilirsiniz:

* Oturum açma sırasında, Azure AD B2C kimlik bilgilerini doğrulamak için hemen önce
* Oturum açma işleminden hemen sonra
* Azure AD B2C önce dizinde yeni bir hesap oluşturmadan önce
* Azure AD B2C, dizinde yeni bir hesap oluşturduktan sonra
* Azure AD B2C önce bir erişim belirteci vermeden önce

Azure AD B2C ' de yeniden yapılan API tümleştirmesi için özel ilkelerin nasıl kullanılacağını görmek için, bkz. [Azure AD B2C Kullanıcı yolculuğunda REST API talep alışverişlerinde tümleştirme](active-directory-b2c-custom-rest-api-netfw.md).

## <a name="protect-customer-identities"></a>Müşteri kimliklerini koruma

Azure AD B2C, güvenlik, gizlilik ve [Microsoft Azure Güven Merkezi](https://www.microsoft.com/trustcenter/cloudservices/azure)'nde açıklanan diğer taahhütlerle uyumludur.

Oturumlar şifrelenmiş veriler olarak modellenir ve şifre çözme anahtarı yalnızca Azure AD B2C güvenlik belirteci hizmeti tarafından bilinir. Tanımlayıcı şifreleme algoritması, AES-192 kullanılır. Tüm iletişim yolları gizlilik ve bütünlük için TLS ile korunur. Güvenlik belirteci hizmetimiz TLS için Genişletilmiş Doğrulama (EV) sertifikası kullanır. Genel olarak, güvenlik belirteci hizmeti, güvenilmeyen giriş işlenerek siteler arası komut dosyası (XSS) saldırılarını azaltır.

![Yoldaki ve bekleyen güvenli verilerin diyagramı](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Kullanıcı verilerine erişim

Azure AD B2C kiracılar, çalışanlar ve iş ortakları için kullanılan kurumsal Azure Active Directory kiracılar ile birçok özelliği paylaşır. Paylaşılan yönleri, yönetim rollerini görüntüleme, rol atama ve denetim etkinliklerini kapsayan mekanizmalar içerir.

Azure AD B2C, aşağıdakiler de dahil olmak üzere belirli yönetim eylemlerini kimin gerçekleştirebileceği denetlemek için roller atayabilirsiniz:

* Kullanıcı akışlarının tüm yönlerini oluşturun ve yönetin
* Tüm Kullanıcı akışları için kullanılabilen öznitelik şemasını oluşturma ve yönetme
* Kimlik sağlayıcılarını doğrudan federasyonda kullanılmak üzere yapılandırma
* Kimlik deneyimi çerçevesinde (özel ilkeler) güven çerçevesi ilkeleri oluşturma ve yönetme
* Kimlik deneyimi çerçevesinde Federasyon ve şifreleme için gizli dizileri yönetme (özel ilkeler)

Azure AD B2C yönetim rolü desteği de dahil olmak üzere Azure AD rolleri hakkında daha fazla bilgi için, [Azure Active Directory Içindeki yönetici rolü izinleri](../active-directory/users-groups-roles/directory-assign-admin-roles.md)bölümüne bakın.

### <a name="multi-factor-authentication-mfa"></a>Çok faktörlü kimlik doğrulaması (MFA)

Azure AD B2C Multi-Factor Authentication (MFA), kullanıcılarınız için basitlik sağlarken verilere ve uygulamalara erişimi korumaya yardımcı olur. İkinci bir kimlik doğrulama biçimi gerektirerek ek güvenlik sağlar ve bir dizi kullanımı kolay kimlik doğrulama yöntemi sunarak güçlü kimlik doğrulaması sunar. Kullanıcılarınız, yönetici olarak yapabileceğiniz yapılandırma kararları temelinde MFA 'ya yönelik olarak istenebilir veya olmayabilir.

[Azure Active Directory B2C 'de Multi-Factor Authentication 'ı etkinleştirme](active-directory-b2c-reference-mfa.md)içindeki Kullanıcı akışlarında MFA 'yı Etkinleştirme makalesine bakın.

### <a name="smart-account-lockout"></a>Akıllı hesap kilitleme

Deneme yanılma ve parola tahmin girişimlerini engellemek için Azure AD B2C, hesapları isteğin IP 'si, girilen parolalar ve diğer birçok faktöre göre kilitlemek için gelişmiş bir strateji kullanır. Kilitleme süresi, riske ve deneme sayısına göre otomatik olarak artırılır.

![Hesap akıllı kilitleme](media/technical-overview/smart-lockout1.png)

Parola koruma ayarlarını yönetme hakkında daha fazla bilgi için bkz. [Azure Active Directory B2C tehditleri kaynakları ve verileri yönetme](active-directory-b2c-reference-threat-management.md).

### <a name="password-complexity"></a>Parola karmaşıklığı

Kaydolma veya parola sıfırlama sırasında kullanıcılarınızın karmaşıklık kurallarını karşılayan bir parola sağlaması gerekir. Varsayılan olarak, Azure AD B2C güçlü bir parola ilkesi uygular. Azure AD B2C, müşterilerinizin kullanacağı parolaların karmaşıklık gereksinimlerini belirtmek için yapılandırma seçenekleri de sağlar.

Her iki [Kullanıcı akışı](active-directory-b2c-reference-password-complexity.md) ve [özel ilke](active-directory-b2c-reference-password-complexity-custom.md)için parola karmaşıklığı gereksinimlerini yapılandırabilirsiniz.

## <a name="auditing-and-logs"></a>Denetim ve Günlükler

Azure AD B2C, kaynakları, verilen belirteçleri ve yönetici erişimi hakkında etkinlik bilgilerini içeren denetim günlüklerini yayar. Bu denetim günlüklerini, platform etkinliğini anlamak ve sorunları tanılamak için kullanabilirsiniz. Denetim günlüğü girdileri, olayı oluşturan etkinlik gerçekleştiğinde yakında kullanılabilir.

Azure AD B2C kiracınız veya belirli bir kullanıcı için kullanılabilen bir denetim günlüğünde, şunlar dahil olmak üzere bilgiler bulabilirsiniz:

* Bir kullanıcının B2C kaynaklarına erişmesi için yetkilendirmesiyle ilgili etkinlikler (örneğin, B2C ilkelerinin listesine erişen bir yönetici)
* Bir yönetici Azure portal kullanarak oturum açtığında alınan dizin öznitelikleriyle ilgili etkinlikler
* B2C uygulamalarında oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri
* B2C anahtar kapsayıcısında depolanan anahtarlarla CRUD işlemleri
* B2C kaynaklarında CRUD işlemleri (örneğin, ilkeler ve kimlik sağlayıcıları)
* Kullanıcı kimlik bilgilerinin ve belirteç verme 'nin doğrulanması

![Azure portal gösterilen bireysel kullanıcı denetim günlüğü](media/technical-overview/audit-log.png)

Denetim günlükleri hakkında daha fazla bilgi için bkz. [Azure AD B2C denetim günlüklerine erişme](active-directory-b2c-reference-audit-logs.md).

### <a name="usage-insights"></a>Kullanım öngörüleri

Azure AD B2C, kullanıcıların Web uygulamanızda ne zaman oturum açtıklarında veya oturum açtıklarında, kullanıcılarınızın nerede olduğunu ve kullandıkları tarayıcıları ve işletim sistemlerini keşfedebileceğiniz kişileri bulmanızı sağlar. Azure Application Insights özel ilkeler kullanarak Azure AD B2C tümleştirerek, kişilerin nasıl kaydolabileceğinize ilişkin Öngörüler elde edebilir, oturum açabilir, parolasını sıfırlayabilir veya profillerini düzenleyebilirsiniz. Bu tür bilgilerle, yaklaşan geliştirme döngüleriniz için veri odaklı kararlar verebilirsiniz.

Kullanım Analizi hakkında daha fazla bilgi edinmek için [Application Insights kullanarak Azure Active Directory B2C Kullanıcı davranışını izleyin](active-directory-b2c-custom-guide-eventlogger-appins.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure Active Directory B2C Özellikler ve teknik yönlerine daha ayrıntılı bir şekilde karşılaşdığınıza göre, B2C kiracısı oluşturarak hizmeti kullanmaya başlayın:

> [!div class="nextstepaction"]
> [Öğretici: Azure Active Directory B2C kiracı > oluşturma](tutorial-create-tenant.md)