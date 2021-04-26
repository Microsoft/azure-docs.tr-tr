---
title: Kimlik doğrulama-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Threat Modeling Tool kimlik doğrulaması azaltma hakkında bilgi edinin. Bkz. risk azaltma bilgileri ve kod örneklerini görüntüleme.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: ee4dd70faab9ed44b1aa6ca8ca0ec517c7746f66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94832539"
---
# <a name="security-frame-authentication--mitigations"></a>Güvenlik çerçevesi: kimlik doğrulaması | Karşı

| Ürün/hizmet | Makale |
| --------------- | ------- |
| **Web uygulaması**    | <ul><li>[Web uygulamasının kimliğini doğrulamak için standart bir kimlik doğrulama mekanizması kullanmayı düşünün](#standard-authn-web-app)</li><li>[Uygulamalar başarısız kimlik doğrulama senaryolarını güvenli bir şekilde işlemelidir](#handle-failed-authn)</li><li>[Adım artırma veya Uyarlamalı kimlik doğrulamayı etkinleştirme](#step-up-adaptive-authn)</li><li>[Yönetim arabirimlerinin uygun şekilde kilitlendiğinden emin olun](#admin-interface-lockdown)</li><li>[Unutulan parola işlevlerini güvenli bir şekilde uygulayın](#forgot-pword-fxn)</li><li>[Parola ve hesap ilkesinin uygulandığından emin olun](#pword-account-policy)</li><li>[Kullanıcı adı numaralandırmasını engellemek için denetimler uygulama](#controls-username-enum)</li></ul> |
| **Veritabanı** | <ul><li>[Mümkün olduğunda, SQL Server bağlanmak için Windows kimlik doğrulamasını kullanın](#win-authn-sql)</li><li>[Mümkün olduğunda SQL veritabanına bağlanmak için Azure Active Directory kimlik doğrulaması kullanın](#aad-authn-sql)</li><li>[SQL kimlik doğrulama modu kullanıldığında, hesap ve parola ilkesinin SQL Server 'da zorlandığından emin olun](#authn-account-pword)</li><li>[Kapsanan veritabanlarında SQL kimlik doğrulaması kullanma](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[SaS belirteçlerini kullanarak cihaz başına kimlik doğrulama kimlik bilgileri kullanma](#authn-sas-tokens)</li></ul> |
| **Azure Güven sınırı** | <ul><li>[Azure yöneticileri için Azure AD Multi-Factor Authentication etkinleştirme](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric güven sınırı** | <ul><li>[Service Fabric kümesine anonim erişimi kısıtla](#anon-access-cluster)</li><li>[İstemci-düğüm sertifikasının Service Fabric düğümden düğüme sertifikasından farklı olduğundan emin olun](#fabric-cn-nn)</li><li>[Service Fabric kümelerine yönelik istemcilerin kimliğini doğrulamak için AAD kullanma](#aad-client-fabric)</li><li>[Service Fabric sertifikalarının onaylanan bir sertifika yetkilisinden (CA) alındığından emin olun](#fabric-cert-ca)</li></ul> |
| **Kimlik Sunucusu** | <ul><li>[Kimlik sunucusu tarafından desteklenen standart kimlik doğrulama senaryolarını kullanma](#standard-authn-id)</li><li>[Varsayılan kimlik sunucusu belirteç önbelleğini ölçeklenebilir bir alternatifi ile geçersiz kılın](#override-token)</li></ul> |
| **Makine güven sınırı** | <ul><li>[Dağıtılan uygulamanın ikili dosyalarının dijital olarak imzalandığından emin olun](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[WCF 'de MSMQ kuyruklara bağlanırken kimlik doğrulamasını etkinleştirme](#msmq-queues)</li><li>[WCF-Message clientCredentialType 'ı None olarak ayarlamayın](#message-none)</li><li>[WCF-taşıma clientCredentialType 'ı None olarak ayarlamayın](#transport-none)</li></ul> |
| **Web API** | <ul><li>[Web API 'Lerini güvenli hale getirmek için standart kimlik doğrulama tekniklerinin kullanıldığından emin olun](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Azure Active Directory tarafından desteklenen standart kimlik doğrulama senaryolarını kullanın](#authn-aad)</li><li>[Varsayılan ADAL belirteci önbelleğini ölçeklenebilir bir alternatifi ile geçersiz kılın](#adal-scalable)</li><li>[ADAL kimlik doğrulama belirteçlerinin yeniden oynamasını engellemek için TokenReplayCache 'in kullanıldığından emin olun](#tokenreplaycache-adal)</li><li>[OAuth2 istemcilerinden AAD 'ye (veya şirket içi AD) belirteç isteklerini yönetmek için ADAL kitaplıklarını kullanın](#adal-oauth2)</li></ul> |
| **IoT alan ağ geçidi** | <ul><li>[Alan ağ geçidine bağlanan cihazların kimliğini doğrulama](#authn-devices-field)</li></ul> |
| **IoT bulut ağ geçidi** | <ul><li>[Bulut ağ geçidine bağlanan cihazların kimlik doğrulaması yapıldığından emin olun](#authn-devices-cloud)</li><li>[Cihaz başına kimlik doğrulama kimlik bilgilerini kullan](#authn-cred)</li></ul> |
| **Azure Depolama** | <ul><li>[Yalnızca gerekli kapsayıcıların ve Blobların anonim okuma erişimi verildiğinden emin olun](#req-containers-anon)</li><li>[SAS veya SAP kullanarak Azure Storage 'da nesnelere sınırlı erişim verme](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Web uygulamasının kimliğini doğrulamak için standart bir kimlik doğrulama mekanizması kullanmayı düşünün

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| Ayrıntılar | <p>Kimlik doğrulaması, bir varlığın kimliğini, genellikle Kullanıcı adı ve parola gibi kimlik bilgileri aracılığıyla kanıtlayan işlemdir. Göz önünde bulundurmanız gereken birden çok kimlik doğrulama protokolü vardır. Bunlardan bazıları aşağıda listelenmiştir:</p><ul><li>İstemci sertifikaları</li><li>Windows tabanlı</li><li>Forms tabanlı</li><li>Federasyon-ADFS</li><li>Federasyon-Azure AD</li><li>Federasyon kimliği sunucusu</li></ul><p>Kaynak işlemi tanımlamak için standart bir kimlik doğrulama mekanizması kullanmayı düşünün</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>Uygulamalar başarısız kimlik doğrulama senaryolarını güvenli bir şekilde işlemelidir

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| Ayrıntılar | <p>Açıkça kullanıcıların kimliğini doğrulayan uygulamalar, başarısız kimlik doğrulama senaryolarını güvenli bir şekilde işlemelidir. Kimlik doğrulama mekanizması şu şekilde olmalıdır:</p><ul><li>Kimlik doğrulama başarısız olduğunda ayrıcalıklı kaynaklara erişimi reddetme</li><li>Başarısız kimlik doğrulaması ve erişim reddedildi hatası oluştuktan sonra genel bir hata iletisi görüntüle</li></ul><p>Test:</p><ul><li>Başarısız oturum açmalar sonrasında ayrıcalıklı kaynakların korunması</li><li>Başarısız kimlik doğrulama ve erişim reddedildi olayları üzerinde genel bir hata iletisi görüntülenir</li><li>Hesapların sayısı çok fazla başarısız girişim sonrasında devre dışı bırakılır</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Adım artırma veya Uyarlamalı kimlik doğrulamayı etkinleştirme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| Ayrıntılar | <p>Uygulamanın, hassas bilgilere erişim verilmeden önce öncelikli olması için, kullanıcının bir daha fazla yetkilendirme (örneğin, SMS 'de OTP gönderme, e-posta vb.) veya yeniden kimlik doğrulaması istemek gibi ek yetkilere sahip olduğunu doğrulayın. Bu kural, bir hesapta veya eylemde kritik değişiklikler yapmak için de geçerlidir</p><p>Bu Ayrıca, kimlik doğrulamasının uyarlaması, örneğin, örneğin, parametre üzerinde değişiklik yapmasına izin vermek üzere bağlama duyarlı yetkilendirmeyi uygulamanın doğru şekilde zorladığı şekilde uygulanması gerektiği anlamına gelir.</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Yönetim arabirimlerinin uygun şekilde kilitlendiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| Ayrıntılar | İlk çözüm, yalnızca belirli bir kaynak IP aralığından yönetim arabirimine erişim hakkı verilme amaçlıdır. Bu çözüm mümkün değilse, yönetim arabiriminde oturum açmak üzere bir adım adım veya Uyarlamalı kimlik doğrulamasını zorlamak için her zaman önerilir |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Unutulan parola işlevlerini güvenli bir şekilde uygulayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| Ayrıntılar | <p>İlk şey, Unutulan parola ve diğer kurtarma yollarının parola yerine sınırlı bir etkinleştirme belirteci de dahil olmak üzere bir bağlantı gönderdiğini doğrulamasıdır. Geçici belirteçlere (örn. SMS belirteci, yerel mobil uygulamalar vb.) dayalı ek kimlik doğrulaması, bağlantı gönderilmeden önce gerekli olabilir. İkincisi, yeni bir parola alma işlemini devam ederken Kullanıcı hesabını kilitlemeyin.</p><p>Bu, bir saldırgan otomatik bir saldırıya karşı kullanıcıları kasıtlı olarak kilitlemeye karar verdiğinde bir hizmet reddi saldırısına yol açabilir. Üçüncü olarak, yeni parola isteği her ayarlandığında, görüntülenen ileti, Kullanıcı adı numaralandırmasını engellemek için genelleştirilmelidir. Dördüncü, her zaman eski parolaların kullanılmasına izin vermeyin ve güçlü bir parola ilkesi uygulayın.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Parola ve hesap ilkesinin uygulandığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| Ayrıntılar | <p>Kurumsal ilkeyle ve en iyi uygulamalarla uyumlu olarak parola ve hesap ilkesi uygulanmalıdır.</p><p>Deneme yanılma ve sözlük tabanlı tahmin saldırılarına karşı savunmak için: güçlü parola ilkesi, kullanıcıların karmaşık parola oluşturmasını sağlamak için uygulanmalıdır (örneğin, 12 karakter minimum uzunluğu, alfasayısal ve özel karakterler).</p><p>Hesap kilitleme ilkeleri aşağıdaki şekilde uygulanabilir:</p><ul><li>**Yazılımdan kilitleme:** Bu, kullanıcılarınızı deneme yanılma saldırılarına karşı korumak için iyi bir seçenek olabilir. Örneğin, Kullanıcı üç kez yanlış parola girdiğinde, bu, kullanıcının, parolasının devam etmesini zorluyor hale getirmek için bir dakika boyunca parolayı kilitleyebilmesi için bir dakika boyunca bir dakika boyunca bir dakika kadar zaman bir parola giriyordu. Bu örnek için sabit kilit kilitleme önlemler uygularsanız, hesapları kalıcı olarak kilitleyerek bir "DoS" elde edersiniz. Alternatif olarak, uygulama bir OTP (bir kerelik parola) oluşturabilir ve bunu kullanıcıya bant dışı (e-posta, SMS vb.) gönderebilir. Bir eşik sayısı başarısız olan deneme sayısına ulaşıldıktan sonra CAPTCHA uygulaması başka bir yaklaşım olabilir.</li><li>**Sabit kilitleme:** Bu tür bir kilitlemenin, bir kullanıcının uygulamanıza saldırması ve bir yanıt ekibinin kendi kendilerini yapabilmeleri için zaman bilgisayarlarını kalıcı olarak kilitlemesini sağlayarak uygulanması gerekir. Bu işlemden sonra, kullanıcıya kendi hesabını geri yüklemeye veya bunlara yönelik daha fazla yasal eylem vermeye karar verebilirsiniz. Bu tür bir yaklaşım, saldırganın uygulamanızı ve altyapınızı daha fazla oymasına engel olur.</li></ul><p>Varsayılan ve öngörülebilir hesapların saldırılarına karşı savunmak için tüm anahtarların ve parolaların değiştirilebilen ve yükleme zamanından sonra oluşturulduğunu veya değiştirildiğini doğrulayın.</p><p>Uygulamanın parolaları otomatik olarak oluşturması gerekiyorsa, oluşturulan parolaların rastgele olduğundan ve yüksek entropi içerdiğinden emin olun.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Kullanıcı adı numaralandırmasını engellemek için denetimler uygulama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | Kullanıcı adı numaralandırmasını engellemek için tüm hata iletileri genelleştirilmelidir. Ayrıca bazen kayıt sayfası gibi işlevsellikleri sızmaya engel olamaz. Burada, bir saldırgan tarafından otomatik saldırı yapılmasını engellemek için CAPTCHA gibi hız sınırlaması yöntemlerini kullanmanız gerekir. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Mümkün olduğunda, SQL Server bağlanmak için Windows kimlik doğrulamasını kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Veritabanı |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | OnPrem |
| **Öznitelikler**              | SQL sürümü-tümü |
| **Başvurular**              | [SQL Server-bir kimlik doğrulama modu seçin](/sql/relational-databases/security/choose-an-authentication-mode) |
| **Adımlar** | Windows kimlik doğrulaması, Kerberos güvenlik protokolünü kullanır, güçlü parolalara yönelik karmaşıklık doğrulamasına göre parola ilkesi zorlaması sağlar, hesap kilitleme desteği sağlar ve parola kullanım süresini destekler.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>Mümkün olduğunda SQL veritabanına bağlanmak için Azure Active Directory kimlik doğrulaması kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Veritabanı |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | SQL Azure |
| **Öznitelikler**              | SQL sürümü-V12 |
| **Başvurular**              | [Azure Active Directory kimlik doğrulaması kullanarak SQL veritabanı 'na bağlanma](../../azure-sql/database/authentication-aad-overview.md) |
| **Adımlar** | **En düşük sürüm:** Azure SQL veritabanı 'nın, Microsoft dizinine karşı AAD kimlik doğrulamasını kullanmasına izin vermek için V12 gerekir |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>SQL kimlik doğrulama modu kullanıldığında, hesap ve parola ilkesinin SQL Server 'da zorlandığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Veritabanı |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [SQL Server parola ilkesi](/previous-versions/sql/sql-server-2012/ms161959(v=sql.110)) |
| **Adımlar** | SQL Server kimlik doğrulaması kullanılırken, oturum açma işlemleri Windows Kullanıcı hesapları tabanlı olmayan SQL Server oluşturulur. Hem Kullanıcı adı hem de parola, SQL Server kullanılarak oluşturulur ve SQL Server depolanır. SQL Server, Windows parola ilkesi mekanizmalarını kullanabilir. Bu, Windows 'da kullanılan karmaşıklık ve süre sonu ilkelerinin SQL Server içinde kullanılan parolalara uygulanmasını sağlayabilir. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>Kapsanan veritabanlarında SQL kimlik doğrulaması kullanma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Veritabanı |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Onpred, SQL Azure |
| **Öznitelikler**              | SQL sürümü-MSSQL2012, SQL sürümü-V12 |
| **Başvurular**              | [Kapsanan veritabanlarında En Iyi güvenlik uygulamaları](/sql/relational-databases/databases/security-best-practices-with-contained-databases) |
| **Adımlar** | Zorlanan bir parola ilkesinin yokluğu, kapsanan bir veritabanında zayıf bir kimlik bilgilerinin oluşma olasılığını artırabilir. Windows kimlik doğrulamasından yararlanın. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>SaS belirteçlerini kullanarak cihaz başına kimlik doğrulama kimlik bilgileri kullanma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Event Hub |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [Event Hubs kimlik doğrulaması ve güvenlik modeline genel bakış](../../event-hubs/authenticate-shared-access-signature.md) |
| **Adımlar** | <p>Event Hubs güvenlik modeli, paylaşılan erişim Imzası (SAS) belirteçlerinin ve olay yayımcılarının birleşimini temel alır. Yayımcı adı, belirteci alan DeviceID 'yi temsil eder. Bu, ilgili cihazlarla oluşturulan belirteçlerin ilişkilendirilmesine yardımcı olur.</p><p>Tüm iletiler, hizmet tarafında oluşturanın bulunduğu, yük içi kaynak sızdırma girişimlerini algılamaya izin vererek etiketlenir. Cihazların kimliği doğrulanırken, benzersiz bir yayımcının kapsamına alınmış cihaz başına SaS belirtecini oluşturun.</p>|

## <a name="enable-azure-ad-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Azure yöneticileri için Azure AD Multi-Factor Authentication etkinleştirme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Güven sınırı |
| **SDL aşaması**               | Dağıtım |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [Azure AD Multi-Factor Authentication nedir?](../../active-directory/authentication/concept-mfa-howitworks.md) |
| **Adımlar** | <p>Multi-Factor Authentication (MFA), birden fazla doğrulama yöntemi gerektiren bir kimlik doğrulama yöntemidir ve Kullanıcı oturum açma işlemlerine ve işlemlerine kritik ikinci bir güvenlik katmanı ekler. Aşağıdaki doğrulama yöntemlerinin iki veya daha fazlasını gerektirerek işe yarar:</p><ul><li>Bildiğiniz bir şey (genellikle bir parola)</li><li>Sahip olduğunuz bir şey (telefon gibi kolayca çoğaltılmayan güvenilir bir cihaz)</li><li>Bir şeyler (Biyometri)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Service Fabric kümesine anonim erişimi kısıtla

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Service Fabric güven sınırı |
| **SDL aşaması**               | Dağıtım |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Ortam-Azure  |
| **Başvurular**              | [Service Fabric kümesi güvenlik senaryoları](../../service-fabric/service-fabric-cluster-security.md) |
| **Adımlar** | <p>Kullanıcılar, özellikle üzerinde çalışan üretim iş yükleri olduğunda, yetkisiz kullanıcıların kümenize bağlanmasını engellemek için her zaman güvenli hale gelmelidir.</p><p>Bir Service Fabric kümesi oluştururken, güvenlik modunun "güvenli" olarak ayarlandığından emin olun ve gerekli X. 509.952 sunucu sertifikasını yapılandırın. "Güvenli olmayan" bir küme oluşturmak, genel Internet 'e yönetim uç noktaları kullanıma sunarsa anonim kullanıcıların bu sunucuya bağlanmasına izin verir.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>İstemci-düğüm sertifikasının Service Fabric düğümden düğüme sertifikasından farklı olduğundan emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Service Fabric güven sınırı |
| **SDL aşaması**               | Dağıtım |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Ortam-Azure, ortam-tek başına |
| **Başvurular**              | [İstemciden düğüme Sertifika güvenliği Service Fabric](../../service-fabric/service-fabric-cluster-security.md#client-to-node-certificate-security) [istemci sertifikası kullanarak güvenli bir kümeye bağlanma](../../service-fabric/service-fabric-connect-to-secure-cluster.md) |
| **Adımlar** | <p>İstemci-düğüm sertifika güvenliği, bir yönetici istemci sertifikası ve/veya bir kullanıcı istemci sertifikası belirterek Azure portal, Kaynak Yöneticisi şablonları veya tek başına JSON şablonu aracılığıyla küme oluşturulurken yapılandırılır.</p><p>Belirttiğiniz yönetici istemcisi ve Kullanıcı istemci sertifikaları, düğümden düğüme güvenlik için belirttiğiniz birincil ve ikincil sertifikalardan farklı olmalıdır.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>Service Fabric kümelerine yönelik istemcilerin kimliğini doğrulamak için AAD kullanma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Service Fabric güven sınırı |
| **SDL aşaması**               | Dağıtım |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Ortam-Azure |
| **Başvurular**              | [Küme güvenliği senaryoları-güvenlik önerileri](../../service-fabric/service-fabric-cluster-security.md#security-recommendations) |
| **Adımlar** | Azure üzerinde çalışan kümeler, istemci sertifikalarından ayrı olarak Azure Active Directory (AAD) kullanarak yönetim uç noktalarına erişimi de güvenli hale getirebilirsiniz. Azure kümeleri için, düğümden düğüme güvenlik için istemci ve sertifikaların kimliğini doğrulamak üzere AAD Güvenliği kullanmanız önerilir.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Service Fabric sertifikalarının onaylanan bir sertifika yetkilisinden (CA) alındığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Service Fabric güven sınırı |
| **SDL aşaması**               | Dağıtım |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Ortam-Azure |
| **Başvurular**              | [X. 509.440 sertifikaları ve Service Fabric](../../service-fabric/service-fabric-cluster-security.md#x509-certificates-and-service-fabric) |
| **Adımlar** | <p>Service Fabric, düğümlerin ve istemcilerin kimliğini doğrulamak için X. 509.440 sunucu sertifikaları kullanır.</p><p>Hizmet dokularında sertifika kullanırken göz önünde bulundurmanız gereken bazı önemli noktalar:</p><ul><li>Üretim iş yüklerini çalıştıran kümelerde kullanılan sertifikaların, doğru şekilde yapılandırılmış bir Windows Server sertifika hizmeti kullanılarak oluşturulması veya onaylanan bir sertifika yetkilisinden (CA) alınması gerekir. CA, onaylanan bir dış CA veya düzgün yönetilen bir iç ortak anahtar altyapısı (PKI) olabilir</li><li>Üretimde MakeCert.exe gibi araçlarla oluşturulan geçici veya test sertifikalarını hiçbir şekilde kullanmayın</li><li>Otomatik olarak imzalanan bir sertifika kullanabilirsiniz, ancak bunu yalnızca test kümeleri için ve üretimde değil</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Kimlik sunucusu tarafından desteklenen standart kimlik doğrulama senaryolarını kullanma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Kimlik Sunucusu |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [IdentityServer3-büyük resim](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Adımlar** | <p>Kimlik sunucusu tarafından desteklenen tipik etkileşimler aşağıda verilmiştir:</p><ul><li>Tarayıcılar Web uygulamalarıyla iletişim kurar</li><li>Web uygulamaları Web API 'Leriyle iletişim kurar (bazen Kullanıcı adına, bazen kendi başına)</li><li>Tarayıcı tabanlı uygulamalar Web API 'Leriyle iletişim kurar</li><li>Yerel uygulamalar Web API 'Leriyle iletişim kurar</li><li>Sunucu tabanlı uygulamalar Web API 'Leriyle iletişim kurar</li><li>Web API 'leri Web API 'lerle iletişim kurar (bazen Kullanıcı adına, bazen kendi başına)</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>Varsayılan kimlik sunucusu belirteç önbelleğini ölçeklenebilir bir alternatifi ile geçersiz kılın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Kimlik Sunucusu |
| **SDL aşaması**               | Dağıtım |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [Kimlik sunucusu dağıtımı-önbelleğe alma](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Adımlar** | <p>IdentityServer 'da basit bir yerleşik bellek önbelleği vardır. Bu, küçük ölçekli yerel uygulamalar için uygun olsa da, aşağıdaki nedenlerden dolayı orta katman ve arka uç uygulamaları için ölçeklendirmez:</p><ul><li>Bu uygulamalara aynı anda birçok kullanıcı tarafından erişilir. Aynı depodaki tüm erişim belirteçlerini kaydetme, yalıtım sorunları oluşturur ve ölçek üzerinde çalışırken zorluk gösterir: birçok kullanıcı, her biri uygulamanın adına eriştiği kaynaklar olarak çok sayıda belirteç ile, çok büyük sayılar ve çok pahalı arama işlemleri anlamına gelebilir</li><li>Bu uygulamalar genellikle birden çok düğümün aynı önbelleğe erişiminin olması gereken dağıtılmış topolojilerde dağıtılır</li><li>Önbelleğe alınan belirteçler, işlem geri dönüştürme ve kaldırma işlemlerini sürdürmelidir</li><li>Yukarıdaki tüm nedenlerden dolayı, Web uygulamaları uygulanırken, Redsıs için Azure önbelleği gibi ölçeklenebilir bir alternatifi olan varsayılan kimlik sunucusunun belirteç önbelleğini geçersiz kılmanız önerilir</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Dağıtılan uygulamanın ikili dosyalarının dijital olarak imzalandığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Makine güven sınırı |
| **SDL aşaması**               | Dağıtım |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | İkililerin bütünlüğünden emin olmak için dağıtılan uygulamanın ikili dosyalarının dijital olarak imzalandığından emin olun|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>WCF 'de MSMQ kuyruklara bağlanırken kimlik doğrulamasını etkinleştirme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel, NET Framework 3 |
| **Öznitelikler**              | Yok |
| **Başvurular**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)) |
| **Adımlar** | Program MSMQ kuyruğuna bağlanırken kimlik doğrulamasını etkinleştiremezse, bir saldırgan işlenmek üzere kuyruğa anonim olarak ileti gönderebilir. Kimlik doğrulaması, başka bir programa ileti göndermek için kullanılan bir MSMQ kuyruğuna bağlanmak için kullanılmazsa, saldırgan kötü amaçlı olan anonim bir ileti gönderebilir.|

### <a name="example"></a>Örnek
`<netMsmqBinding/>`AŞAĞıDAKI WCF yapılandırma dosyasının öğesi, ileti teslimi için BIR MSMQ kuyruğuna BAĞLANıRKEN WCF 'nin kimlik doğrulamasını devre dışı bırakmasına izin verir.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Tüm gelen veya giden iletiler için, her zaman Windows etki alanı veya sertifika kimlik doğrulaması gerektirecek şekilde MSMQ 'YU yapılandırın.

### <a name="example"></a>Örnek
`<netMsmqBinding/>`AŞAĞıDAKI WCF yapılandırma dosyasının öğesi, BIR MSMQ kuyruğuna bağlanırken sertifika kimlik doğrulamasını etkinleştirmek IÇIN WCF 'ye yönlendirir. İstemcinin kimliği, X. 509.440 sertifikaları kullanılarak doğrulanır. İstemci sertifikası, sunucunun sertifika deposunda bulunmalıdır.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF-Message clientCredentialType 'ı None olarak ayarlamayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | .NET Framework 3 |
| **Öznitelikler**              | İstemci kimlik bilgisi türü-yok |
| **Başvurular**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Adımlar** | Kimlik doğrulamanın yokluğu, herkesin bu hizmete erişebileceği anlamına gelir. İstemcilerinin kimliğini doğrulayamayan bir hizmet, tüm kullanıcılara erişim sağlar. Uygulamayı istemci kimlik bilgileriyle kimlik doğrulaması yapacak şekilde yapılandırın. Bu işlem, Message clientCredentialType for Windows veya Certificate olarak ayarlanarak yapılabilir. |

### <a name="example"></a>Örnek
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF-taşıma clientCredentialType 'ı None olarak ayarlamayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel, .NET Framework 3 |
| **Öznitelikler**              | İstemci kimlik bilgisi türü-yok |
| **Başvurular**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Adımlar** | Kimlik doğrulamanın yokluğu, herkesin bu hizmete erişebileceği anlamına gelir. İstemcilerinin kimliğini doğrulayamayan bir hizmet, tüm kullanıcıların işlevselliğine erişmesine izin verir. Uygulamayı istemci kimlik bilgileriyle kimlik doğrulaması yapacak şekilde yapılandırın. Bu işlem, taşıma clientCredentialType 'ı Windows veya sertifikasına ayarlayarak yapılabilir. |

### <a name="example"></a>Örnek
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Web API 'Lerini güvenli hale getirmek için standart kimlik doğrulama tekniklerinin kullanıldığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [ASP.NET Web API 'si](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [dış kimlik doğrulama hizmetleri, ASP.NET Web API 'Si ile](https://www.asp.net/web-api/overview/security/external-authentication-services) kimlik doğrulaması ve yetkilendirme (C#) |
| **Adımlar** | <p>Kimlik doğrulaması, bir varlığın kimliğini, genellikle Kullanıcı adı ve parola gibi kimlik bilgileri aracılığıyla kanıtlayan işlemdir. Göz önünde bulundurmanız gereken birden çok kimlik doğrulama protokolü vardır. Bunlardan bazıları aşağıda listelenmiştir:</p><ul><li>İstemci sertifikaları</li><li>Windows tabanlı</li><li>Forms tabanlı</li><li>Federasyon-ADFS</li><li>Federasyon-Azure AD</li><li>Federasyon kimliği sunucusu</li></ul><p>Başvurular bölümünde yer alan bağlantılar, bir Web API 'sinin güvenliğini sağlamak için her bir kimlik doğrulama düzeni nasıl uygulanmalarıyla ilgili alt düzey ayrıntılar sağlar.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Azure Active Directory tarafından desteklenen standart kimlik doğrulama senaryolarını kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure AD |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [Azure AD Için kimlik doğrulama senaryoları](../../active-directory/develop/authentication-vs-authorization.md), [Azure Active Directory kod örnekleri](../../active-directory/azuread-dev/sample-v1-code.md) [Azure Active Directory Geliştirici Kılavuzu](../../active-directory/develop/index.yml) |
| **Adımlar** | <p>Azure Active Directory (Azure AD), OAuth 2,0 ve OpenID Connect gibi endüstri standardı protokoller desteğiyle bir hizmet olarak kimlik sağlayarak geliştiriciler için kimlik doğrulamasını basitleştirir. Azure AD tarafından desteklenen beş birincil uygulama senaryosu aşağıda verilmiştir:</p><ul><li>Web uygulamasına Web tarayıcısı: bir kullanıcının Azure AD ile güvenliği sağlanmış bir Web uygulamasında oturum açması gerekir</li><li>Tek sayfalı uygulama (SPA): kullanıcının Azure AD ile güvenliği sağlanan tek sayfalı bir uygulamada oturum açması gerekir</li><li>Yerel uygulama Web API 'sine: bir telefonda, tablette veya BILGISAYAR üzerinde çalışan yerel bir uygulamanın, Azure AD tarafından güvenliği sağlanmış bir Web API 'sinden kaynak alması için bir kullanıcının kimliğini doğrulaması gerekir</li><li>Web uygulaması Web API 'SI: bir Web uygulamasının Azure AD ile güvenliği sağlanmış bir Web API 'sinden kaynak alması gerekir</li><li>Web API 'sine yönelik Daemon veya sunucu uygulaması: Web Kullanıcı arabirimi olmayan bir Daemon uygulamasının veya bir sunucu uygulamasının Azure AD tarafından güvenliği sağlanmış bir Web API 'sinden kaynak alması gerekir</li></ul><p>Düşük düzey uygulama ayrıntıları için lütfen başvurular bölümündeki bağlantılara başvurun</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>Varsayılan ADAL belirteci önbelleğini ölçeklenebilir bir alternatifi ile geçersiz kılın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure AD |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [Web uygulamaları için Azure Active Directory Ile modern kimlik doğrulaması](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications), [REDSıS 'i adal belirteç önbelleği olarak kullanma](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Adımlar** | <p>ADAL (Active Directory Authentication Library) tarafından kullanılan varsayılan önbellek, statik bir mağazaya ve kullanılabilir işlem genelindeki bir bellek içi önbellektir. Bu, yerel uygulamalar için çalışırken, aşağıdaki nedenlerden dolayı orta katman ve arka uç uygulamaları için ölçeklendirmez:</p><ul><li>Bu uygulamalara aynı anda birçok kullanıcı tarafından erişilir. Aynı depodaki tüm erişim belirteçlerini kaydetme, yalıtım sorunları oluşturur ve ölçek üzerinde çalışırken zorluk gösterir: birçok kullanıcı, her biri uygulamanın adına eriştiği kaynaklar olarak çok sayıda belirteç ile, çok büyük sayılar ve çok pahalı arama işlemleri anlamına gelebilir</li><li>Bu uygulamalar genellikle birden çok düğümün aynı önbelleğe erişiminin olması gereken dağıtılmış topolojilerde dağıtılır</li><li>Önbelleğe alınan belirteçler, işlem geri dönüştürme ve kaldırma işlemlerini sürdürmelidir</li></ul><p>Yukarıdaki tüm nedenlerden dolayı, Web uygulamaları uygulanırken, redin için Azure önbelleği gibi ölçeklenebilir bir alternatifi olan varsayılan ADAL belirteci önbelleğinin geçersiz kılınması önerilir.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>ADAL kimlik doğrulama belirteçlerinin yeniden oynamasını engellemek için TokenReplayCache 'in kullanıldığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure AD |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [Web uygulamaları için Azure Active Directory ile modern kimlik doğrulama](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications) |
| **Adımlar** | <p>TokenReplayCache özelliği, geliştiricilerin bir kereden fazla kullanılabileceğini doğrulamak amacıyla belirteçleri kaydetmek için kullanılabilecek bir depo olan belirteç yeniden yürütme önbelleği tanımlamasına olanak tanır.</p><p>Bu, yaygın bir saldırıya karşı, belirteç yeniden yürütme saldırısı olarak adlandırılan bir ölçüdür: oturum açma sırasında gönderilen belirteci kesintiye uğratan bir saldırgan, yeni bir oturum oluşturmak için onu uygulamaya yeniden göndermeye çalışabilir ("yeniden oynat"). Örneğin, OıDC kodu-verme akışında, başarılı Kullanıcı kimlik doğrulamasından sonra, bağlı olan tarafın "/SignIn-OIDC" uç noktasına bir istek "id_token", "Code" ve "State" parametreleriyle yapılır.</p><p>Bağlı olan taraf bu isteği doğrular ve yeni bir oturum oluşturur. Bir saldırgan bu isteği yakalar ve yeniden oynadığında, başarılı bir oturum oluşturabilir ve kullanıcıyı taklit edebilir. OpenID Connect 'te nonce 'in varlığı, saldırının başarılı bir şekilde ele alındığı durumları sınırlayabilir, ancak tamamen ortadan kaldırmaz. Geliştiriciler, uygulamalarını korumak için bir ıtokenreplaycache uygulaması sağlayabilir ve TokenReplayCache 'e bir örnek atayabilir.</p>|

### <a name="example"></a>Örnek
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Örnek
Itokenreplaycache arabiriminin örnek bir uygulaması aşağıda verilmiştir. (Lütfen projenize özgü önbelleğe alma çerçevesini özelleştirin ve uygulayın)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
Şu şekilde, uygulanan önbelleğin "TokenValidationParameters" özelliği aracılığıyla OıDC seçeneklerinde başvurulması gerekebilir.
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Bu yapılandırmanın verimliliğini test etmek için yerel OıDC korumalı uygulamanızda oturum açın ve `"/signin-oidc"` Fiddler 'da uç noktaya isteği yakalayın. Koruma gerçekleşmediği zaman, bu isteği Fiddler 'da yeniden yerleştirmek yeni bir oturum tanımlama bilgisi ayarlar. TokenReplayCache koruması eklendikten sonra istek yeniden yürütüldüğünde, uygulama aşağıdaki gibi bir özel durum oluşturur: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>OAuth2 istemcilerinden AAD 'ye (veya şirket içi AD) belirteç isteklerini yönetmek için ADAL kitaplıklarını kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure AD |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [ADAL](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) |
| **Adımlar** | <p>Azure AD kimlik doğrulama kitaplığı (ADAL), istemci uygulama geliştiricilerinin kullanıcıların bulut veya şirket içi Active Directory (AD) için kolayca kimlik doğrulamasını sağlar ve ardından API çağrılarını güvenli hale getirmek için erişim belirteçleri elde eder.</p><p>ADAL, zaman uyumsuz destek, erişim belirteçleri depolayan ve belirteçleri yenileyen yapılandırılabilir bir belirteç önbelleği, bir erişim belirtecinin süresi dolarsa ve yenileme belirtecinin kullanılabildiği, otomatik belirteç yenilemesi ve daha fazlası gibi geliştiriciler için kimlik doğrulaması kolaylaştıran birçok özelliğe sahiptir.</p><p>ADAL, karmaşıklığın çoğunu işleyerek, bir geliştiricinin uygulamanızdaki iş mantığına odaklanmasını ve güvenlik uzmanı olmadan kaynakları kolayca güvenli hale getirmenize yardımcı olabilir. .NET, JavaScript (istemci ve Node.js), Python, iOS, Android ve Java için ayrı kitaplıklar vardır.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>Alan ağ geçidine bağlanan cihazların kimliğini doğrulama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT alan ağ geçidi |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | Her cihazın, verileri kabul etmeden önce ve bulut ağ geçidiyle yukarı akış iletişimlerini kolaylaştırmadan önce alan ağ geçidi tarafından doğrulandığını doğrulayın. Ayrıca, cihazların ayrı ayrı tanımlanması için cihazların cihaz kimlik bilgileri ile bağlantı aldığından emin olun.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Bulut ağ geçidine bağlanan cihazların kimlik doğrulaması yapıldığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT bulut ağ geçidi |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel, C#, Node.JS,  |
| **Öznitelikler**              | Yok, ağ geçidi seçimi-Azure IoT Hub |
| **Başvurular**              | N/A, [.net Ile Azure IoT Hub](../../iot-hub/quickstart-send-telemetry-dotnet.md), [IoT Hub ve Node JS ile ÇALıŞMAYA](../../iot-hub/quickstart-send-telemetry-node.md)başlama, [SAS ve sertifikalarla IoT 'yi güvenli hale getirme](../../iot-hub/iot-hub-devguide-security.md), [Git deposu](https://github.com/Azure/azure-iot-sdks/) |
| **Adımlar** | <ul><li>**Genel:** Aktarım Katmanı Güvenliği (TLS) veya IPSec kullanarak cihazın kimliğini doğrulayın. Altyapı, tam asimetrik şifrelemeyi işleyemeyen cihazlarda önceden paylaşılan anahtar (PSK) kullanmayı desteklemelidir. Azure AD, OAuth özelliğinden yararlanın.</li><li>**C#:** Bir DeviceClient örneği oluştururken, Create yöntemi, IoT Hub ile iletişim kurmak için AMQP protokolünü kullanan bir DeviceClient örneği oluşturur. HTTPS protokolünü kullanmak için, Create yönteminin protokolü belirtmenize olanak tanıyan geçersiz kılmasını kullanın. HTTPS protokolünü kullanıyorsanız, `Microsoft.AspNet.WebApi.Client` ad alanını dahil etmek için projenize NuGet paketini de eklemeniz gerekir `System.Net.Http.Formatting` .</li></ul>|

### <a name="example"></a>Örnek
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Örnek
**Node.JS: kimlik doğrulaması**
#### <a name="symmetric-key"></a>Simetrik anahtar
* Azure 'da IoT Hub 'ı oluşturma
* Cihaz kimliği kayıt defterinde bir giriş oluşturma
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Sanal cihaz oluşturma
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>SAS belirteci
* Simetrik anahtar kullanılırken dahili olarak üretilir, ancak bunu açıkça oluşturup kullanabilir
* Bir protokol tanımlayın: `var Http = require('azure-iot-device-http').Http;`
* SAS belirteci oluşturma:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* SAS belirteci kullanarak bağlan:
    ```javascript
    Client.fromSharedAccessSignature(sas, Http);
    ```
  #### <a name="certificates"></a>Sertifikalar
* Sertifikayı ve anahtarı depolamak üzere bir. CERT ve. Key dosyası oluşturmak için OpenSSL gibi herhangi bir aracı kullanarak kendinden imzalı bir x509 sertifikası oluşturun
* Sertifikaları kullanarak güvenli bağlantı kabul eden bir cihaz sağlayın.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Sertifika kullanarak bir cihazı bağlama
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    };
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Cihaz başına kimlik doğrulama kimlik bilgilerini kullan

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT bulut ağ geçidi  |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Ağ Geçidi seçimi-Azure IoT Hub |
| **Başvurular**              | [Azure IoT Hub güvenlik belirteçleri](../../iot-hub/iot-hub-devguide-security.md) |
| **Adımlar** | IoT Hub düzeyinde paylaşılan erişim ilkeleri yerine cihaz anahtarı veya Istemci sertifikası temelinde SaS belirteçlerini kullanarak cihaz kimlik doğrulaması kimlik bilgileri başına kullanın. Bu, bir cihazın veya alan ağ geçidinin kimlik doğrulama belirteçlerinin başka bir şekilde kullanılmasını önler |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Yalnızca gerekli kapsayıcıların ve Blobların anonim okuma erişimi verildiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Depolama |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | StorageType-blob |
| **Başvurular**              | [Kapsayıcılar ve Bloblar için anonim okuma erişimini yönetme](../../storage/blobs/anonymous-read-access-configure.md), [paylaşılan erişim imzaları, 1. Bölüm: SAS modelini anlama](../../storage/common/storage-sas-overview.md) |
| **Adımlar** | <p>Varsayılan olarak, bir kapsayıcıya ve içindeki bloblara yalnızca depolama hesabının sahibi tarafından erişilebilir. Anonim kullanıcılara bir kapsayıcıya ve bloblarına okuma izinleri vermek için, bir kapsayıcı izinleri ortak erişime izin verecek şekilde ayarlayabilir. Anonim kullanıcılar, istek için kimlik doğrulaması yapmadan herkese açık bir kapsayıcı içindeki Blobları okuyabilir.</p><p>Kapsayıcılar, kapsayıcı erişimini yönetmek için aşağıdaki seçenekleri sağlar:</p><ul><li>Tam genel okuma erişimi: kapsayıcı ve blob verileri, anonim istek aracılığıyla okunabilir. İstemciler, anonim istek aracılığıyla kapsayıcı içindeki Blobları numaralandırabilir, ancak depolama hesabı içindeki kapsayıcıları numaralandıramaz.</li><li>Yalnızca Bloblar için genel okuma erişimi: Bu kapsayıcı içindeki blob verileri anonim istek aracılığıyla okunabilir, ancak kapsayıcı verileri kullanılamaz. İstemciler, anonim istek aracılığıyla kapsayıcı içindeki Blobları numaralandıramıyor</li><li>Genel okuma erişimi yok: kapsayıcı ve blob verileri yalnızca hesap sahibi tarafından okunabilir</li></ul><p>Anonim erişim, belirli Blobların her zaman anonim okuma erişimi için kullanılabilir olması gereken senaryolar için idealdir. Daha ayrıntılı denetim için, bir paylaşılan erişim imzası oluşturabilir ve bu, farklı izinler ve belirli bir zaman aralığı kullanarak kısıtlı erişimi devredebilir. Büyük olasılıkla gizli veriler içerebilen kapsayıcıların ve Blobların anonim erişime yanlışlıkla verilmemesini sağlayın</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>SAS veya SAP kullanarak Azure Storage 'da nesnelere sınırlı erişim verme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Depolama |
| **SDL aşaması**               | Derleme |
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok |
| **Başvurular**              | [Paylaşılan erişim imzaları, 1. Bölüm: SAS modelini](../../storage/common/storage-sas-overview.md), [paylaşılan erişim imzalarını ve Bölüm 2: BLOB depolama ile SAS oluşturma ve kullanma](../../storage/common/storage-sas-overview.md), [paylaşılan erişim imzalarını ve depolanan erişim ilkelerini kullanarak hesabınızdaki nesnelere erişim yetkisi verme](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Adımlar** | <p>Paylaşılan erişim imzası (SAS) kullanmak, hesap erişim anahtarını açığa çıkarmak zorunda kalmadan, bir depolama hesabındaki nesnelere sınırlı erişim vermek için güçlü bir yoldur. SAS, bir depolama kaynağına kimliği doğrulanmış erişim için gereken tüm bilgileri sorgu parametrelerini kapsayan bir URI 'dir. SAS ile depolama kaynaklarına erişmek için, istemcinin yalnızca SAS 'yi uygun oluşturucuya veya yönteme geçirmesi gerekir.</p><p>Depolama hesabınızdaki kaynaklara erişim sağlamak istediğinizde, hesap anahtarıyla güvenilir olmayan bir istemciye erişim sağlamak istediğinizde SAS kullanabilirsiniz. Depolama hesabı anahtarlarınız hem bir birincil hem de ikincil anahtar içerir ve bu her ikisi de hesabınıza ve içindeki tüm kaynaklara yönetici erişimi verir. Hesap anahtarlarınızın her birini kullandığınızda, hesabınız kötü amaçlı veya ihmal edilebilir kullanım olasılığa karşı açılır. Paylaşılan erişim imzaları, diğer istemcilerin, verdiğiniz izinlere ve hesap anahtarına gerek kalmadan Depolama hesabınızdaki verileri okumasına, yazmasına ve silmesine izin veren güvenli bir alternatif sağlar.</p><p>Her seferinde benzer bir mantıksal parametre kümesine sahipseniz, depolanan erişim Ilkesi (SAP) kullanmak daha iyi bir fikirdir. Depolanan bir erişim Ilkesinden türetilmiş bir SAS kullanılması, bu SAS 'ı hemen iptal etmenizi sağlar. Bu, mümkün olduğunda her zaman depolanan erişim Ilkelerini kullanmak için önerilen en iyi uygulamadır.</p>|