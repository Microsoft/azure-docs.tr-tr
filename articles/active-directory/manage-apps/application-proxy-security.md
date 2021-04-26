---
title: Azure AD Uygulama Ara Sunucusu için güvenlik konuları | Microsoft Docs
description: Azure AD Uygulama Ara Sunucusu kullanmaya yönelik güvenlik konularını ele alır
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c98bce0be2b456220815a359aae1ee697f3ca2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254974"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu ile uygulamalara uzaktan erişmenin güvenlik konuları

Bu makalede, Azure Active Directory Uygulama Ara Sunucusu kullandığınızda kullanıcılarınızın ve uygulamalarınızın güvende tutulması için çalışan bileşenler açıklanmaktadır.

Aşağıdaki diyagramda Azure AD 'nin şirket içi uygulamalarınıza güvenli uzaktan erişim sağlama şekli gösterilmektedir.

 ![Azure AD Uygulama Ara Sunucusu aracılığıyla güvenli uzaktan erişim diyagramı](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Güvenlik avantajları

Azure AD Uygulama Ara Sunucusu aşağıdaki güvenlik avantajlarını sunmaktadır:

### <a name="authenticated-access"></a>Kimliği doğrulanmış erişim 

Azure Active Directory ön kimlik doğrulaması kullanmayı seçerseniz, ağınıza yalnızca kimliği doğrulanmış bağlantılar erişebilir.

Azure AD Uygulama Ara Sunucusu, tüm kimlik doğrulaması için Azure AD güvenlik belirteci hizmetini (STS) kullanır.  Ön kimlik doğrulaması, çok doğası gereği, yalnızca kimliği doğrulanmış kimlikler arka uç uygulamasına erişebildiğinden, önemli sayıda anonim saldırıları engeller.

Ön kimlik doğrulama yönteminiz olarak PASSTHROUGH ' i seçerseniz, bu avantaja sahip olursunuz. 

### <a name="conditional-access"></a>Koşullu Erişim

Ağınıza yönelik bağlantıların kurulması için daha zengin ilke denetimleri uygulayın.

[Koşullu erişimle](../conditional-access/concept-conditional-access-cloud-apps.md), kullanıcıların uygulamalarınıza nasıl erişmelerine izin verildiğini belirleyen kısıtlamalar tanımlayabilirsiniz. Oturum açma işlemlerini konuma, kimlik doğrulama gücüne ve Kullanıcı risk profiline göre sınırlayan ilkeler oluşturabilirsiniz.

Ayrıca, Kullanıcı kimlik doğrulamalarına başka bir güvenlik katmanı ekleyerek Multi-Factor Authentication ilkelerini yapılandırmak için koşullu erişimi de kullanabilirsiniz. Ayrıca, uygulamalarınız, [erişim](/cloud-app-security/access-policy-aad) ve [oturum](/cloud-app-security/session-policy-aad) ilkeleri aracılığıyla gerçek zamanlı izleme ve DENETIMLER sağlamak Için Azure AD koşullu erişimi aracılığıyla Microsoft Cloud App Security de yönlendirilebilir

### <a name="traffic-termination"></a>Trafik sonlandırma

Tüm trafik bulutta sonlandırılır.

Azure AD Uygulama Ara Sunucusu bir ters proxy olduğundan, arka uç uygulamalarına giden tüm trafik hizmette sonlandırılır. Oturum yalnızca arka uç sunucusuyla yeniden bağlanabilir ve bu, arka uç sunucularınızın doğrudan HTTP trafiğine açık olmayacağı anlamına gelir. Bu yapılandırma, hedeflenen saldırılardan daha iyi korunduğunuz anlamına gelir.

### <a name="all-access-is-outbound"></a>Tüm erişim giden 

Şirket ağına gelen bağlantıları açmanız gerekmez.

Uygulama proxy bağlayıcıları yalnızca Azure AD Uygulama Ara Sunucusu hizmetine giden bağlantıları kullanır, bu da gelen bağlantılara yönelik güvenlik duvarı bağlantı noktalarını açmaya gerek yoktur. Geleneksel proxy 'ler bir çevre ağı ( *DMZ*, *sivil bölge* veya denetimli *alt ağ* olarak da bilinir) ve ağ kenarındaki kimliği doğrulanmamış bağlantılara erişim izni verilmesini gerektirir. Bu senaryo, trafiği analiz etmek ve ortamı korumak için Web uygulaması güvenlik duvarı ürünlerinde yatırımları gerektirir. Uygulama proxy 'Si ile, tüm bağlantılar giden ve güvenli bir kanaldan yer aldığı için bir çevre ağına ihtiyacınız yoktur.

Bağlayıcılar hakkında daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu bağlayıcıları anlama](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Bulut ölçeğinde analiz ve makine öğrenimi 

Son teknoloji güvenlik koruması alın.

Azure Active Directory bir parçası olduğundan, uygulama proxy 'Si, Microsoft Güvenlik Yanıt Merkezi ve dijital Crimes biriminden alınan verilerle [Azure AD kimlik koruması](../identity-protection/overview-identity-protection.md)faydalanabilir. Birlikte güvenliği aşılmış hesapları ve yüksek riskli oturum açma işlemleri için koruma olanağı sunuyoruz. Hangi oturum açma girişimlerinin yüksek riskli olduğunu belirleyen birçok etken hesaba sunuyoruz. Bu faktörler, virüslü cihazların işaretlenmesini, ağların anonimleştirilmesi ve tipik veya olası konumları içerir.

Bu raporların ve olayların birçoğu, güvenlik bilgileri ve olay yönetimi (SıEM) sistemlerinizle tümleştirme için bir API aracılığıyla zaten kullanılabilir.

### <a name="remote-access-as-a-service"></a>Hizmet olarak uzaktan erişim

Şirket içi sunucuları koruma ve düzeltme eki uygulama hakkında endişelenmeniz gerekmez.

Çok sayıda saldırı için düzeltme eki yüklenmemiş yazılım hala hesaplar. Azure AD Uygulama Ara Sunucusu, Microsoft 'un sahip olduğu bir Internet ölçeklendirmiş hizmettir, bu nedenle her zaman en son güvenlik düzeltme eklerini ve yükseltmelerini alırsınız.

Azure AD Uygulama Ara Sunucusu tarafından yayınlanan uygulamaların güvenliğini artırmak için Web Gezgini Robotlarınızın uygulamalarınızı dizinlemesini ve arşivlemesini engelliyoruz. Bir Web Gezgini robotu, yayımlanan bir uygulama için robot ayarlarını almaya çalıştığında, uygulama proxy 'Si içeren bir robots.txt dosyası ile yanıt verir `User-agent: * Disallow: /` .

#### <a name="azure-ddos-protection-service"></a>Azure DDoS koruma hizmeti

Uygulama proxy 'Si aracılığıyla yayımlanan uygulamalar, dağıtılmış hizmet reddi (DDoS) saldırılarına karşı korunur. Bu koruma Microsoft tarafından yönetilir ve tüm veri merkezlerimizde otomatik olarak etkinleştirilir. Azure DDoS Koruması hizmeti, her zaman açık ağ düzeyinde saldırıları ve gerçek zamanlı risk azaltma sağlar. 

## <a name="under-the-hood"></a>Başlık altında

Azure AD Uygulama Ara Sunucusu iki bölümden oluşur:

* Bulut tabanlı hizmet: Bu hizmet Azure 'da çalışır ve dış istemci/kullanıcı bağlantılarının yapıldığı yerdir.
* Şirket [içi bağlayıcı](application-proxy-connectors.md): şirket içi bir bileşen olan bağlayıcı, Azure AD uygulama ara sunucusu hizmetinden gelen istekleri dinler ve iç uygulamalarla bağlantıları işler. 

Bağlayıcı ile uygulama proxy hizmeti arasında bir akış şu durumlarda oluşturulur:

* Bağlayıcı ilk olarak ayarlanır.
* Bağlayıcı, uygulama ara sunucusu hizmetinden yapılandırma bilgilerini çeker.
* Kullanıcı yayımlanmış bir uygulamaya erişiyor.

>[!NOTE]
>Tüm iletişimler TLS üzerinden oluşur ve her zaman bağlayıcıdan uygulama proxy 'Si hizmetine gelir. Hizmet yalnızca giden.

Bağlayıcı, neredeyse tüm çağrılar için uygulama ara sunucusu hizmetinde kimlik doğrulamak üzere bir istemci sertifikası kullanır. Bu işlemin tek istisnası, istemci sertifikasının kurulduğu ilk kurulum adımıdır.

### <a name="installing-the-connector"></a>Bağlayıcıyı yükleme

Bağlayıcı ilk ayarlandığında aşağıdaki akış olayları gerçekleşir:

1. Hizmete Bağlayıcı kaydı, bağlayıcının yüklenmesinin bir parçası olarak gerçekleşir. Kullanıcılardan Azure AD yönetici kimlik bilgilerini girmesi istenir. Bu kimlik doğrulamasından alınan belirteç daha sonra Azure AD Uygulama Ara Sunucusu hizmetine sunulur.
2. Uygulama Proxy Hizmeti belirteci değerlendirir. Kullanıcının kiracıda genel yönetici olup olmadığını denetler. Kullanıcı bir yönetici değilse, işlem sonlandırılır.
3. Bağlayıcı bir istemci sertifikası isteği oluşturur ve belirteç ile birlikte uygulama ara sunucusu hizmetine geçirir. ' Deki hizmet belirteci doğrular ve istemci sertifika isteğini imzalar.
4. Bağlayıcı, uygulama proxy 'Si hizmetiyle gelecekte iletişim kurmak için istemci sertifikasını kullanır.
5. Bağlayıcı, istemci sertifikasını kullanarak hizmetten sistem yapılandırma verilerinin ilk çekmesini gerçekleştirir ve artık istekleri almaya hazırdır.

### <a name="updating-the-configuration-settings"></a>Yapılandırma ayarları güncelleştiriliyor

Uygulama proxy hizmeti yapılandırma ayarlarını güncelleştirdiğinde, aşağıdaki akış olayları gerçekleşir:

1. Bağlayıcı, istemci sertifikasını kullanarak uygulama proxy hizmeti içindeki yapılandırma uç noktasına bağlanır.
2. İstemci sertifikası doğrulandıktan sonra, uygulama proxy hizmeti, bağlayıcı için yapılandırma verileri döndürür (örneğin, bağlayıcının parçası olması gereken bağlayıcı grubu).
3. Geçerli sertifikanın 180 günden daha eski olması durumunda bağlayıcı, istemci sertifikasını her 180 günde bir etkin şekilde güncelleştiren yeni bir sertifika isteği oluşturur.

### <a name="accessing-published-applications"></a>Yayınlanan uygulamalara erişme

Kullanıcılar yayımlanmış bir uygulamaya erişirken, uygulama proxy 'si hizmeti ve uygulama proxy Bağlayıcısı arasında aşağıdaki olaylar gerçekleşir:

1. Hizmet, uygulamanın kullanıcı kimliğini doğrular
2. Hizmet, bağlayıcı kuyruğuna bir istek koyar
3. Bir bağlayıcı, isteği kuyruktan işler
4. Bağlayıcı bir yanıt bekler
5. Hizmet, verileri kullanıcıya akışlar

Bu adımların her birinde nelerin gerçekleştiği hakkında daha fazla bilgi edinmek için okumaya devam edin.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. hizmet, uygulamanın kullanıcı kimliğini doğrular

Uygulamayı, ön kimlik doğrulama yöntemi olarak geçiş kullanacak şekilde yapılandırdıysanız, bu bölümdeki adımlar atlanır.

Uygulamayı Azure AD ile ön kimlik doğrulaması yapacak şekilde yapılandırdıysanız, kullanıcılar kimlik doğrulaması yapmak için Azure AD STS 'ye yönlendirilir ve aşağıdaki adımlar gerçekleştirilir:

1. Uygulama proxy 'Si, belirli bir uygulama için herhangi bir koşullu erişim ilkesi gereksinimini denetler. Bu adım, kullanıcının uygulamaya atanmasını sağlar. İki adımlı doğrulama gerekliyse, kimlik doğrulama sırası kullanıcıdan ikinci bir kimlik doğrulama yöntemi ister.

2. Tüm denetimler başarılı olduktan sonra Azure AD STS, uygulama için imzalı bir belirteç yayınlar ve kullanıcıyı uygulama proxy 'Si hizmetine yeniden yönlendirir.

3. Uygulama proxy 'Si, belirtecin doğru uygulamaya verildiğini doğrular. Belirtecin Azure AD tarafından imzalandığından ve hala geçerli pencerenin içinde olduğundan emin olmak gibi diğer denetimleri de gerçekleştirir.

4. Uygulama proxy 'Si, uygulamaya yönelik kimlik doğrulamanın oluştuğunu göstermek için şifrelenmiş bir kimlik doğrulama tanımlama bilgisi ayarlar. Tanımlama bilgisi, Azure AD ve diğer verilerden (örneğin, kimlik doğrulamanın dayandığı Kullanıcı adı) temel alınarak bir süre sonu zaman damgası içerir. Tanımlama bilgisi, yalnızca uygulama proxy 'Si hizmeti için bilinen özel bir anahtarla şifrelenir.

5. Uygulama proxy 'Si, kullanıcıyı ilk istenen URL 'ye yeniden yönlendirir.

Ön kimlik doğrulama adımlarının herhangi bir bölümü başarısız olursa, kullanıcının isteği reddedilir ve Kullanıcı sorunun kaynağını belirten bir ileti gösterilir.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. hizmet bağlayıcı kuyruğuna bir istek koyar

Bağlayıcılar, giden bir bağlantıyı uygulama proxy 'Si hizmetine açık tutar. Bir istek geldiğinde, hizmet bağlayıcının çekme bağlantısı için açık bağlantılardan birindeki isteği sıraya alır.

İstek, uygulamanın istek başlıkları, şifreli tanımlama bilgisinden alınan veriler, isteği yapan Kullanıcı ve istek KIMLIĞI gibi öğeleri içerir. Şifrelenmiş tanımlama bilgisinden gelen veriler istekle birlikte gönderilse de, kimlik doğrulama tanımlama bilgisinin kendisi değildir.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. bağlayıcı isteği kuyruktan işler. 

İsteğe bağlı olarak, uygulama proxy 'Si aşağıdaki eylemlerden birini gerçekleştirir:

* İstek basit bir işlem ise (örneğin, bir Restdeal isteği olan gövde içinde veri yoksa), bağlayıcı hedef iç kaynağa  bir bağlantı yapar ve ardından bir yanıt bekler.

* İstekte, gövdede kendisiyle ilişkili veriler varsa (örneğin, bir RESTAN *Post* işlemi), bağlayıcı Istemci sertifikasını uygulama proxy 'si örneğine kullanarak giden bir bağlantı yapar. Bu bağlantıyı, verileri istemek ve iç kaynağa bir bağlantı açmak için yapar. Bağlayıcının isteği aldıktan sonra, uygulama proxy hizmeti kullanıcıdan içerik kabul etmeye başlar ve verileri bağlayıcıya iletir. Bağlayıcı, sırasıyla verileri iç kaynağa iletir.

#### <a name="4-the-connector-waits-for-a-response"></a>4. bağlayıcı bir yanıt bekler.

Arka uca tüm içeriğin isteği ve iletimi tamamlandıktan sonra, bağlayıcı bir yanıt bekler.

Bir yanıt aldıktan sonra bağlayıcı, üst bilgi ayrıntılarını döndürmek ve döndürülen verileri akışa başlamak için uygulama ara sunucusu hizmetine giden bir bağlantı oluşturur.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. hizmet, verileri kullanıcıya akıtir. 

Uygulamanın bazı işlemleri burada görülebilir. Uygulama proxy 'Sini uygulamanızdaki üst bilgileri veya URL 'Leri çevirecek şekilde yapılandırdıysanız, bu işlem bu adım sırasında gerektiğinde gerçekleşir.


## <a name="next-steps"></a>Sonraki adımlar

[Azure AD Uygulama Ara Sunucusu kullanırken ağ topolojisi konuları](application-proxy-network-topology.md)

[Azure AD Uygulama Ara Sunucusu bağlayıcılarını anlama](application-proxy-connectors.md)