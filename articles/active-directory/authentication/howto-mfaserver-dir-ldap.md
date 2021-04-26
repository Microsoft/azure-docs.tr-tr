---
title: LDAP kimlik doğrulaması ve Azure MFA sunucusu-Azure Active Directory
description: LDAP kimlik doğrulaması ve Azure Multi-Factor Authentication Sunucusu dağıtılıyor.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8961cccbd57294e477f0d33202fe91cd292b7814
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742230"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP kimlik doğrulaması ve Azure Multi-Factor Authentication Sunucusu

Varsayılan olarak, Azure Multi-Factor Authentication Sunucusu kullanıcıları Active Directory'den aktarmak ya da eşitlemek üzere yapılandırılmıştır. Ancak, ADAM dizini veya belirli Active Directory etki alanı denetleyicisi gibi farklı LDAP dizinlerini bağlamak için yapılandırılabilir. LDAP aracılığıyla bir dizine bağlanıldığında Azure Multi-Factor Authentication Sunucusu, kimlik doğrulamaları gerçekleştirmek için LDAP proxy işlevi görebilir. Ayrıca, IIS Kimlik Doğrulaması ile ön kimlik doğrulaması için ya da Azure MFA kullanıcı portalında birincil kimlik doğrulaması için LDAP bağlamanın RADIUS hedefi olarak kullanılmasına izin verir.

Azure Multi-Factor Authentication 'yi LDAP proxy 'si olarak kullanmak için, LDAP istemcisi (örneğin, VPN gereci, uygulaması) ve LDAP Dizin sunucusu arasında Azure Multi-Factor Authentication Sunucusu ekleyin. Azure Multi-Factor Authentication doğrulama sunucusunun istemci sunucular ve LDAP dizini ile iletişim kurmak için yapılandırılmış olması gerekir. Bu yapılandırmada, Azure Multi-Factor Authentication Sunucusu istemci sunucularından uygulamalarından gelen LDAP isteklerini kabul eder ve bunları birincil kimlik bilgilerini doğrulamak amacıyla hedef LDAP dizini sunucusuna iletir. LDAP dizini birincil kimlik bilgilerini doğrularsa, Azure Multi-Factor Authentication ikinci bir kimlik doğrulaması gerçekleştirir ve LDAP istemcisine geri yanıt gönderir. Kimlik doğrulama sürecinin tamamının başarılı olması için hem LDAP sunucusu kimlik doğrulamasının hem de ikinci adım kimlik doğrulama işleminin başarılı olması gerekir.

> [!IMPORTANT]
> 1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için MFA sunucusu sağlamamaktadır. Oturum açma olayları sırasında çok faktörlü kimlik doğrulaması (MFA) gerektirmek isteyen yeni müşteriler, bulut tabanlı Azure AD Multi-Factor Authentication kullanmalıdır.
>
> Bulut tabanlı MFA 'yı kullanmaya başlamak için bkz. [öğretici: Azure AD Multi-Factor Authentication Ile güvenli Kullanıcı oturum açma olayları](tutorial-enable-azure-mfa.md).
>
> MFA sunucusunu 1 Temmuz 2019 tarihinden önce etkinleştiren mevcut müşteriler, en son sürümü, gelecekteki güncelleştirmeleri indirebilir ve her zamanki gibi etkinleştirme kimlik bilgilerini oluşturabilir.

## <a name="configure-ldap-authentication"></a>LDAP kimlik doğrulamasını yapılandırma

LDAP kimlik doğrulamasını yapılandırmak için, bir Windows sunucusuna Azure Multi-Factor Authentication Sunucusu yükleyin. Aşağıdaki yordamı kullanın:

### <a name="add-an-ldap-client"></a>LDAP istemcisi ekleme

1. Azure Multi-Factor Authentication Sunucusu, sol taraftaki menüden LDAP kimlik doğrulaması simgesini seçin.
2. **LDAP Kimlik Doğrulamasını etkinleştir** onay kutusunu işaretleyin.

   ![MFA sunucusunda LDAP kimlik doğrulaması](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Azure Multi-Factor Authentication LDAP Hizmeti, LDAP isteklerini dinlemek için standart olmayan bağlantı noktalarına bağlanması gerekiyorsa, Istemciler sekmesinde TCP bağlantı noktası ve SSL (TLS) bağlantı noktasını değiştirin.
4. İstemciden Azure Multi-Factor Authentication Sunucusu LDAPS kullanmayı planlıyorsanız, MFA sunucusuyla aynı sunucuda bir TLS/SSL sertifikası yüklü olmalıdır. SSL (TLS) sertifikası kutusunun yanındaki Git ' **e tıklayın ve** güvenli bağlantı için kullanılacak bir sertifika seçin.
5. **Ekle**'ye tıklayın.
6. LDAP Istemcisi Ekle iletişim kutusuna, sunucu için kimlik doğrulaması yapan gereç, sunucu veya uygulamanın IP adresini ve bir uygulama adını girin (isteğe bağlı). Uygulama adı Azure Multi-Factor Authentication raporlarında görünür ve SMS veya Mobil Uygulama kimlik doğrulama iletilerinde görüntülenebilir.
7. Tüm kullanıcılar Sunucu’ya aktarılmışsa ya da aktarılacaksa ve iki aşamalı doğrulamaya tabi olacaksa **Azure Multi-Factor Authentication kullanıcılarının eşleşmesini gerektir** kutusunu işaretleyin. Önemli sayıda kullanıcı henüz sunucuya aktarılmamışsa ve/veya iki aşamalı doğrulamadan muaf tutulmamışsa, kutuyu işaretlenmemiş olarak bırakın. Bu özellik hakkında ek bilgi için bkz. MFA sunucusu yardım dosyası.

Bu adımları tekrarlayarak diğer LDAP istemcilerini ekleyin.

### <a name="configure-the-ldap-directory-connection"></a>LDAP dizini bağlantısını yapılandırma

Azure Multi-Factor Authentication LDAP kimlik doğrulamaları almak üzere yapılandırıldığında, bu kimlik doğrulamalarını LDAP dizinine sunmalıdır. Bu nedenle, LDAP hedefi olarak kullanmak üzere, Hedef sekmesi yalnızca tek bir, gri seçenek görüntüler.

> [!NOTE]
> Dizin tümleştirmesinin Active Directory Domain Services dışındaki dizinlerde çalışması garanti edilmez.

1. LDAP dizini bağlantısını yapılandırmak için **Dizin Tümleştirme** simgesine tıklayın.
2. Ayarlar sekmesinde **Özel LDAP yapılandırması kullan** radyo düğmesini seçin.
3. Düzenle 'yi seçin **...**
4. LDAP Yapılandırmasını Düzenle iletişim kutusunda, LDAP dizinine bağlanmak için gerekli bilgilerle alanları doldurun. Bu alanların açıklamaları Azure Multi-Factor Authentication Sunucusu yardım dosyasında da bulunmaktadır.

    ![Dizin tümleştirme LDAP yapılandırması](./media/howto-mfaserver-dir-ldap/ldap.png)

5. **Test** düğmesine tıklayarak LDAP bağlantısını test edin.
6. LDAP bağlantı testi başarılı olduysa **Tamam** düğmesine tıklayın.
7. **Filtreler** sekmesine tıklayın. Sunucu, Active Directory kapsayıcıları, güvenlik gruplarını ve kullanıcıları yüklemek üzere önceden yapılandırılmıştır. Farklı bir LDAP dizinine bağlanıyorsanız, büyük olasılıkla görüntülenen filtreleri düzenlemeniz gerekir. Filtreler hakkında daha fazla bilgi için **Yardım** bağlantısına tıklayın.
8. **Öznitelikler** sekmesine tıklayın. Sunucu, Active Directory öznitelikleri eşlenecek şekilde önceden yapılandırılmıştır.
9. Farklı bir LDAP dizinine bağlıyorsanız veya önceden yapılandırılmış öznitelik eşlemelerini değiştirmek istiyorsanız için **Düzenle…** düğmesine tıklayın.
10. Öznitelikleri Düzenle iletişim kutusunda, dizininizin LDAP öznitelik eşlemelerini değiştirin. Öznitelik adlarını yazabilir veya **…** simgesine tıklayarak seçebilirsiniz. düğmesine tıklanarak seçilebilir. Öznitelikler hakkında daha fazla bilgi için **Yardım** bağlantısına tıklayın.
11. **Tamam** düğmesine tıklayın.
12. **Şirket Ayarları** simgesine tıklayın ve **Kullanıcı Adı Çözümleme** sekmesini seçin.
13. Active Directory’ye etki alanına katılmış bir sunucudan bağlanıyorsanız, **Kullanıcı adlarını eşlemek için Windows güvenlik tanımlayıcılarını (SID’ler) kullan** radyo düğmesini seçili bırakın. Diğer durumlarda **Kullanıcı adlarını eşlemek için LDAP benzersiz tanımlayıcı özniteliğini kullan** radyo düğmesini seçin.

**Kullanıcı adlarını eşlemek için LDAP benzersiz tanımlayıcı özniteliğini kullan** radyo düğmesi seçili olduğunda Azure Multi-Factor Authentication Sunucusu her kullanıcı adını LDAP dizinindeki benzersiz bir tanımlayıcıya çözümlemeyi dener. Dizin tümleştirme-> öznitelikleri sekmesinde tanımlanan Kullanıcı adı özniteliklerinde bir LDAP araması gerçekleştirilir. Bir kullanıcının kimliği doğrulandığında, Kullanıcı adı LDAP dizinindeki benzersiz tanımlayıcıya çözümlenir. Benzersiz tanımlayıcı, kullanıcıyı Azure Multi-Factor Authentication veri dosyasında eşleştirmek için kullanılır. Bu, büyük/küçük harfe duyarsız karşılaştırmalar ve uzun ve kısa Kullanıcı adı biçimlerine olanak sağlar.

Bu adımları tamamladıktan sonra, MFA sunucusu yapılandırılan istemcilerden gelen LDAP erişimi istekleri için yapılandırılan bağlantı noktalarını dinler ve LDAP dizinine kimlik doğrulaması için bir proxy görevi görür.

## <a name="configure-ldap-client"></a>LDAP istemcisini yapılandırma

LDAP istemcisini yapılandırmak için yönergeleri kullanın:

* LDAP aracılığıyla gerecinizi, sunucunuzu ya da uygulamanızı LDAP dizinindeymiş gibi Azure Multi-Factor Authentication Sunucusu için kimliğini doğrulamak üzere yapılandırın. Azure Multi-Factor Authentication Sunucusu’na ait olacak sunucu adı ya da IP adresi için olanlar dışında, LDAP dizininize normalde doğrudan bağlandığınızla aynı ayarları kullanın.
* LDAP zaman aşımını 30-60 saniye olarak yapılandırın, böylece kullanıcının kimlik bilgilerini LDAP diziniyle doğrulama, ikinci adım doğrulamayı gerçekleştirme, yanıtlarını alma ve LDAP erişim isteğine yanıt verme zamanı vardır.
* LDAPS kullanılıyorsa, LDAP sorgularını yapan gereç veya sunucu, Azure Multi-Factor Authentication Sunucusu yüklü olan TLS/SSL sertifikasına güvenmelidir.
