---
title: Microsoft Commercial Market 'te SaaS API v2 v2
description: Yönetim API 'si sürüm 2 ' i kullanarak Microsoft AppSource ve Azure Marketi 'nde SaaS teklifi oluşturmayı ve yönetmeyi öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2acf5178e7d1cfdf907146d733150a48e9696a5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712360"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>Ticari Market 'te SaaS API sürüm 2

Bu makalede, iş ortaklarının, hizmet olarak yazılım (SaaS) tekliflerini Microsoft AppSource ve Azure Marketi 'nde satmasını sağlayan API 'Ler ayrıntılı olarak açıklanır. Iş Ortağı Merkezi 'nde bir transactable SaaS teklifi yayımlamak üzere bu API 'lerle tümleştirme uygulamak için bir yayımcı gereklidir.

## <a name="managing-the-saas-subscription-life-cycle"></a>SaaS Abonelik yaşam döngüsünü yönetme

Ticari Market, SaaS aboneliğinin Son Kullanıcı tarafından satın alındıktan sonra tüm yaşam döngüsünü yönetir. Gerçek SaaS aboneliği etkinleştirme, kullanım, güncelleştirme ve iptali sağlamak için bir mekanizma olarak, giriş sayfasını, API 'Leri, Işlem API 'lerini ve Web kancasını kullanır. Son kullanıcının faturası, Microsoft 'un koruduğu SaaS aboneliğinin durumuna bağlıdır. 

### <a name="states-of-a-saas-subscription"></a>SaaS aboneliğinin durumları

Aşağıdaki diyagramda bir SaaS aboneliğinin durumları ve ilgili eylemler gösterilmektedir.

![Market 'te hizmet aboneliği olarak yazılımın yaşam döngüsünü gösteren diyagram.](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Satın alındı ancak henüz etkinleştirilmedi (*PendingFulfillmentStart*)

Bir son kullanıcı (veya CSP) ticari Market 'te bir SaaS teklifi satın aldıktan sonra, yayımcıya satın alma bildirilmelidir. Yayımcı daha sonra son kullanıcının Yayımcı tarafında yeni bir SaaS hesabı oluşturup yapılandırabilir.

Hesap oluşturmanın gerçekleşmesi için:

1. Müşteri, Microsoft AppSource veya Azure portal başarıyla satın alındıktan sonra SaaS teklifi için kullanılabilen **Yapılandır** düğmesini seçer. Alternatif olarak, müşteri e-postada, satın alma işleminden kısa bir süre sonra alacağı **Yapılandır** düğmesini kullanabilir.
2. Daha sonra Microsoft, giriş sayfası URL 'sini belirteç parametresine (ticari Market 'ten satın alma tanımlama belirteci) ekleyerek satın alma hakkında bilgi verir.

Bu tür çağrıya bir örnek `https://contoso.com/signup?token=<blob>` , ancak Iş Ortağı Merkezi 'nde bu SaaS teklifinin giriş sayfası URL 'si olarak yapılandırılır `https://contoso.com/signup` . Bu belirteç, yayımcıya SaaS satın almayı ve müşteriyi benzersiz bir şekilde tanımlayan bir KIMLIK sağlar.

>[!NOTE]
>Yayımcı, Microsoft tarafından yapılandırma işlemini başlatana kadar SaaS satın alma işleminin bilgilendirilmez.

Giriş sayfası URL 'SI her gün, her gün ve Microsoft 'tan her zaman yeni çağrılar almaya hazırlanmalıdır. Giriş sayfası kullanılamaz hale gelirse, müşteriler SaaS hizmetine kaydolayamaz ve kullanmaya başlayabilir.

Ardından Yayımcı, [SaaS Resolve API](#resolve-a-purchased-subscription)'sini çağırarak ve belirteci üstbilgi parametresinin değeri olarak girerek *belirteci* Microsoft 'a geri iletmelidir `x-ms-marketplace-token header` . Çözümleme API 'SI çağrısının sonucu olarak, belirteç satın alma, satın alınan teklif KIMLIĞI ve satın alınan plan KIMLIĞI gibi SaaS satın alma 'nın ayrıntıları için değiştirilir.

Giriş sayfasında, müşteri yeni veya mevcut SaaS hesabında Azure Active Directory (Azure AD) çoklu oturum açma (SSO) yoluyla oturum açmış olmalıdır.

Yayımcı, bu akış için Microsoft 'un gerektirdiği Kullanıcı deneyimini sağlamak üzere SSO 'yu uygulamalıdır. SSO 'yu yapılandırırken çok kiracılı Azure AD uygulamasını kullandığınızdan ve hem iş hem de okul hesaplarına ya da kişisel Microsoft hesaplarına izin verin. Bu gereksinim, yalnızca Microsoft kimlik bilgileriyle oturum açmış olan SaaS hizmetine yeniden yönlendirilen kullanıcılar için giriş sayfası için geçerlidir. SaaS hizmetinde tüm oturum açma işlemleri için SSO gerekli değildir.

> [!NOTE]
>SSO bir yöneticinin bir uygulamaya izin vermesini gerektiriyorsa, Iş Ortağı Merkezi 'nde teklifin açıklaması, yönetici düzeyinde erişimin gerekli olduğunu açıklamalıdır. Bu açıklama [ticari Market sertifika ilkeleriyle](/legal/marketplace/certification-policies#10003-authentication-options)uyumlu değildir.

Oturum açtıktan sonra, müşteri Yayımcı tarafında SaaS yapılandırmasını tamamlamalıdır. Ardından Yayımcı, SaaS hesabının sağlanması işleminin tamamlandığını Azure Marketi 'ne bir sinyal göndermek için [aboneliği ETKINLEŞTIRME API](#activate-a-subscription) 'sini çağırmalıdır.
Bu eylem müşterinin fatura döngüsünü başlatacak. Abonelik etkinleştirme API 'SI çağrısı başarılı olmazsa, müşteri satın alma için faturalandırılmaz.


![Bir sağlama senaryosu için bir P ı çağrısı gösteren diyagram.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Etkin (*abone olunmuş*)

*Etkin (abone olunmuş)* , sağlanan SaaS aboneliğinin kararlı durumudur. Microsoft tarafı, [aboneliği ETKINLEŞTIR API](#activate-a-subscription) çağrısını işledikten sonra, SaaS aboneliği *abone* olarak işaretlenir. Müşteri artık yayımcının tarafında SaaS hizmetini kullanabilir ve faturalandırılacaktır.

SaaS aboneliği zaten etkin olduğunda, müşteri Azure portal veya Yönetim Merkezi Microsoft 365 **SaaS deneyimini Yönet** ' i seçebilir. Bu eylem, Microsoft 'un **giriş sayfası URL 'Sini** etkinleştirme akışında olduğu gibi *belirteç* parametresiyle çağırmasını de sağlar. Yayımcı, mevcut SaaS hesaplarının yeni satın alma işlemlerini ve yönetimini birbirinden ayırt etmelidir ve bu giriş sayfası URL çağrısını buna göre işler.

#### <a name="being-updated-subscribed"></a>Güncelleştiriliyor (*abone olunmuş*)

Bu eylem, mevcut bir etkin SaaS aboneliğine yapılan bir güncelleştirmenin hem Microsoft hem de yayımcı tarafından işlendiği anlamına gelir. Bu tür bir güncelleştirme, şu şekilde başlatılabilir:

- Ticari Market 'ten müşteri.
- Ticari Market 'ten CSP.
- Yayımcının SaaS sitesindeki müşteri (CSP tarafından oluşturulan satın alma işlemleri için değil).

SaaS aboneliği için iki tür güncelleştirme mevcuttur:

- Müşteri abonelik için başka bir plan seçtiğinde planı güncelleştirin.
- Müşteri, abonelik için satın alınan lisans sayısını değiştirdiğinde miktarı güncelleştirin.

Yalnızca etkin bir abonelik güncelleştirilebilen olabilir. Abonelik güncelleştirilirken, durumu Microsoft tarafında etkin kalır.

##### <a name="update-initiated-from-the-commercial-marketplace"></a>Ticari Market 'ten başlatılan güncelleştirme

Bu akışta müşteri, Azure portal Microsoft 365 veya Yönetim Merkezi 'nden abonelik planı veya bilgisayar lisansı sayısını değiştirir.

1. Güncelleştirme girildikten sonra, Microsoft, Iş Ortağı Merkezi 'nin **bağlantı Web kancası** alanında yapılandırılan, *eylem* için uygun bir değer ve diğer ilgili parametreleri içeren yayımcı Web kancası URL 'sini çağırır. 
1. Yayımcı tarafı, SaaS hizmetinde gerekli değişiklikleri yapması ve [Işlem API 'Sinin güncelleştirme durumunu](#update-the-status-of-an-operation)çağırarak Microsoft 'un tamamlandığında bilgilendirmesini sağlar.
1. Düzeltme Eki *başarısız* durumla gönderilirse, güncelleştirme işlemi Microsoft tarafında tamamlanmaz. SaaS aboneliği, var olan planı ve bilgisayar lisans miktarını tutacaktır.

> [!NOTE]
> Yayımcı, Web kancası bildirimini aldıktan sonra *10 saniyelik bir zaman penceresinde* bir hata/başarı YANıTıYLA [Işlem API 'sinin durumunu güncelleştirmek](#update-the-status-of-an-operation) için düzeltme ekini çağırmalıdır. İşlem durumunun düzeltme eki 10 saniye içinde alınmıyorsa, değişiklik planı *otomatik olarak başarılı olarak düzeltme eki* yapılır. 

Ticari Market 'ten başlatılan bir güncelleştirme senaryosuna yönelik API çağrılarının sırası aşağıdaki diyagramda gösterilmiştir.

![Market tarafından başlatılan güncelleştirme için bir P ı çağrısı gösteren diyagram.](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Yayımcıdan başlatılan güncelleştirme

Bu akışta müşteri, SaaS hizmetinin kendisinden satın alınan abonelik planını veya lisans miktarını değiştirir. 

1. Yayımcı kodu, Yayımcı tarafında istenen değişikliği yapmadan önce [değişiklik planı API](#change-the-plan-on-the-subscription) 'sini ve/veya [Change Quantity API](#change-the-quantity-of-seats-on-the-saas-subscription) 'sini çağırmalıdır. 

1. Microsoft bu değişikliği aboneliğe uygular ve aynı değişikliği uygulamak için yayımcıya **bağlantı Web kancası** üzerinden bildirilir.

1. Yalnızca Yayımcı, SaaS aboneliğine gerekli değişikliği yapması ve [Işlem API 'Sinin güncelleştirme durumunu](#update-the-status-of-an-operation)çağırarak Microsoft 'un değişiklik yapıldığında Microsoft 'a bildirmesi gerekir.

Yayımcı tarafında başlatılan bir güncelleştirme senaryosuna yönelik API çağrılarının sırası aşağıdaki diyagramda gösterilmiştir.

![Yayımcı tarafında başlatılan güncelleştirme için bir P ı çağrısı gösteren diyagram.](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Askıya alındı (*askıya alındı*)

Bu durum müşterinin SaaS hizmeti için ödeme alındığını gösterir. Yayımcı, Microsoft tarafından SaaS Abonelik durumundaki bu değişiklik hakkında bilgilendirilir. Bildirim, *eylem* parametresi *askıya alındı* olarak ayarlanmış bir Web kancası çağrısıyla yapılır.

Yayımcı, Yayımcı tarafında SaaS hizmetinde değişiklik yapabilir veya olmayabilir. Yayımcının bu bilgileri askıya alınmış müşteri tarafından kullanılabilmesini ve müşterinin SaaS hizmetine erişimini kısıtlayıp engellemesini öneririz. Ödemenin hiçbir şekilde alınmayacağı bir olasılık vardır.

Microsoft, aboneliği otomatik olarak iptal etmeden önce müşteriye 30 günlük bir yetkisiz kullanım süresi verir. Abonelik *askıya alındı* durumundaysa:

* İş ortağı veya ISV, SaaS hesabını kurtarılabilir bir durumda tutmanız gerekir, böylece tüm işlevler hiçbir veri veya ayar kaybı olmadan geri yüklenebilir.
* İş ortağı veya ISV, kullanım süresi boyunca ödeme alındığında veya yetkisiz kullanım süresinin sonunda aboneliğin sağlamasını kaldırma isteğinde bulunursa, aboneliğin yeniden devreye alınması için bir istek beklemeniz gerekir. Her iki istek de Web kancası mekanizması aracılığıyla gönderilir.

Yayımcı herhangi bir işlem yapmadan önce, Microsoft tarafında abonelik durumu askıya alındı olarak değiştirilir. Yalnızca etkin abonelikler askıya alınabilir.

#### <a name="reinstated-suspended"></a>Tekrar belirtilen (*askıya alındı*)

Bu eylem, müşterinin ödeme aracınızın tekrar geçerli hale geldiğini, SaaS aboneliği için bir ödeme yapıldığını ve aboneliğin yeniden belirtildiğini gösterir. Bu durumda: 

1. Microsoft, bir *eylem* parametresi olan Web kancasını yeniden *devreye* sokma olarak çağırır.
1. Yayımcı, aboneliğin Yayımcı tarafında tamamen çalıştığından emin olmanızı sağlar.
1. Yayımcı, başarılı durumu olan [yama IŞLEMI API](#update-the-status-of-an-operation) 'sini çağırır.
1. Yeniden devreye sokma işlemi başarılı olur ve müşteri SaaS aboneliği için yeniden faturalandırılır. 

Düzeltme Eki *başarısız* durumuyla gönderilirse,,,, Microsoft tarafında bir i ifade işlemi tamamlanmaz ve abonelik *askıya* alınır.

Yalnızca askıya alınmış bir abonelik tekrar belirtilebilir. Askıya alınmış SaaS aboneliği, yeniden belirtildiği sırada *askıya alınmış* durumda kalır. Bu işlem tamamlandıktan sonra aboneliğin durumu *etkin* olur.

#### <a name="renewed-subscribed"></a>Yenilendi (*abone olunan*)

SaaS aboneliği, bir ay veya yıl boyunca abonelik dönemi sonunda Microsoft tarafından otomatik olarak yenilenir. Otomatik yenileme ayarı için varsayılan değer tüm SaaS abonelikleri için *geçerlidir* . Etkin SaaS abonelikleri, düzenli bir temposunda yenilenmeye devam edecektir. Microsoft, bir abonelik yenilendiğinde yayımcıyı bildirmez. Müşteri, Microsoft 365 Yönetici portalı aracılığıyla bir SaaS aboneliği için otomatik yenilemeyi kapatabilir. Bu durumda, SaaS aboneliği geçerli fatura döneminin sonunda otomatik olarak iptal edilir. Müşteriler, SaaS aboneliğini dilediğiniz zaman da iptal edebilir.

Yalnızca etkin abonelikler otomatik olarak yenilenir. Abonelikler, yenileme işlemi sırasında etkin kalır ve otomatik yenileme başarılı olur. Yenilemeden sonra, abonelik döneminin başlangıç ve bitiş tarihleri yeni dönem tarihlerine güncelleştirilir.

Bir otomatik yenileme, ödemeyle ilgili bir sorun nedeniyle başarısız olursa, abonelik *askıya* alınır ve yayımcı bilgilendirilir.

#### <a name="canceled-unsubscribed"></a>İptal edildi (*aboneliği kaldırma*) 

Abonelikler, yayımcı sitesinden, Azure portal veya Microsoft 365 Yönetim merkezinden bir aboneliğin iptalinden yararlanarak açık bir müşteriye veya CSP eylemine yanıt olarak bu duruma ulaşabilir. Abonelik, 30 günlük *askıya alınma* durumunda olduktan sonra, Dues 'nin faturalandırılmasıyla ilgili olarak, örtük olarak da iptal edilebilir.

Yayımcı bir iptal Web kancası çağrısını aldıktan sonra, istek üzerine en az yedi gün için müşteri verilerini korumaları gerekir. Yalnızca müşteri verileri silinebilir.

SaaS aboneliği, kendi yaşam döngüsünün herhangi bir noktasında iptal edilebilir. Abonelik iptal edildikten sonra yeniden etkinleştirilemez.

## <a name="api-reference"></a>API başvurusu

Bu bölüm, SaaS aboneliğini ve Operations API 'Lerini belgeler.

Satın alma işleminden iptale kadar olan SaaS Abonelik yaşam döngüsünü işlemek için **abonelik API 'leri** kullanılmalıdır.

**Operations API 'leri şu işlemlerde** kullanılmalıdır:

* İşlenmiş Web kancası çağrılarını doğrulayıp kabul edin.
* Yayımcı tarafından onaylanmayı bekleyen uygulamaların bir listesini alın.

> [!NOTE]
> TLS sürüm 1,2 sürümü yakında HTTPS iletişimleri için en düşük sürüm olacak şekilde zorlanacak. Kodunuzda bu TLS sürümünü kullandığınızdan emin olun. 1,0 ve 1,1 TLS sürümleri yakında kullanım dışı bırakılacak.

### <a name="subscription-apis"></a>Abonelik API 'Leri

#### <a name="resolve-a-purchased-subscription"></a>Satın alınan bir aboneliği çözümle

Çözümle bitiş noktası, yayımcının ticari Market 'ten satın alma *kimliği belirtecini (* [satın alınan ancak henüz etkinleştirilmemiş](#purchased-but-not-yet-activated-pendingfulfillmentstart)olarak adlandırılır) kalıcı bir satın alınan SaaS Abonelik kimliğine ve ayrıntılarına değiş tokuş etmesini sağlar.

Bir müşteri ortağın giriş sayfası URL 'sine yeniden yönlendirildiğinde, bu URL çağrısındaki *belirteç* parametresi olarak müşteri tanımlama belirteci geçirilir. Ortağın bu belirteci kullanması ve bunu çözümlemek için bir istek yapması beklenmektedir. API 'YI çözümle yanıtı SaaS Abonelik KIMLIĞINI ve satın alma 'yı benzersiz şekilde tanımlamak için diğer ayrıntıları içerir. Giriş sayfası URL 'SI çağrısıyla birlikte sunulan *belirteç* genellikle 24 saat için geçerlidir. Aldığınız *belirtecin* süresi zaten dolmuşsa, son kullanıcıya aşağıdaki kılavuzu sağlamanızı öneririz:

"Bu satın alma tanımlanamadı. Lütfen bu SaaS aboneliğini Azure portal veya Microsoft 365 Yönetim Merkezi 'nde yeniden açın ve "hesabı Yapılandır" veya "hesabı Yönet" i seçin. "

Çözümleme API 'sini çağırmak, desteklenen tüm durumlarda SaaS aboneliklerinin abonelik ayrıntılarını ve durumunu döndürür.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Yayınla `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|  Parametre         | Değer            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 kullanın.   |

*İstek üst bilgileri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  `x-ms-correlationid` |  İstemcideki işlem için benzersiz bir dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  `authorization`     |  Bu API çağrısını yapan yayımcıyı tanımlayan benzersiz bir erişim belirteci. Biçim, `"Bearer <accessaccess_token>"` [Azure AD uygulamasına dayalı bir belirteç al](./pc-saas-registration.md#get-the-token-with-an-http-post)bölümünde açıklandığı gibi, belirteç değeri yayımcı tarafından alınır. |
|  `x-ms-marketplace-token`  | Çözülecek satın alma tanımlama *belirteci* parametresi.  Müşteri SaaS ortağının Web sitesine yeniden yönlendirildiğinde (örneğin:), belirteç giriş sayfası URL 'SI çağrısına geçirilir `https://contoso.com/signup?token=<token><authorization_token>` . <br> <br>  Kodlanan *belirteç* değerinin, GIRIŞ sayfası URL 'sinin bir parçası olduğunu, bu nedenle bu API çağrısında bir parametre olarak kullanılmadan önce kodu çözülmüş olması gerektiğini unutmayın.  <br> <br> URL 'de, `contoso.com/signup?token=ab%2Bcd%2Fef` *belirtecin* olduğu bir kodlanmış dize örneği `ab%2Bcd%2Fef` aşağıda verilmiştir:  Kodu çözülen aynı belirteç şu şekilde olur: `Ab+cd/ef` |
| | |

*Yanıt kodları:*

Code: 200, belirtilen temelinde benzersiz SaaS Abonelik tanımlayıcıları döndürür `x-ms-marketplace-token` .

Yanıt gövdesi örneği:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Code: 400 hatalı istek. `x-ms-marketplace-token` eksik, hatalı biçimlendirilmiş, geçersiz veya zaman aşımına uğradı.

Kod: 403 Yasak. Yetkilendirme belirteci geçersiz, zaman aşımına uğradı veya sağlanmadı.  İstek, yetkilendirme belirtecini oluşturmak için kullanılan sunucudan farklı bir Azure AD uygulama KIMLIĞIYLE yayınlanan teklifin bir SaaS aboneliğine erişmeye çalışıyor.

Bu hata genellikle [SaaS kaydının](pc-saas-registration.md) doğru bir şekilde gerçekleştirmediğini bir belirtisidir.

Kod: 500 Iç sunucu hatası.  API çağrısını yeniden deneyin.  Hata devam ederse, [Microsoft desteği](https://partner.microsoft.com/support/v2/?stage=1)'ne başvurun.

#### <a name="activate-a-subscription"></a>Aboneliği etkinleştir

SaaS hesabı bir son kullanıcı için yapılandırıldıktan sonra yayımcının, Microsoft tarafında abonelik etkinleştirme API 'sini çağırması gerekir.  Bu API çağrısı başarılı olmazsa müşteri faturalandırılmaz.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Yayınla `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|  Parametre         | Değer             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31 kullanın.   |
| `subscriptionId` | Satın alınan SaaS aboneliğinin benzersiz tanımlayıcısı.  Bu KIMLIK, [Çözümle API 'si](#resolve-a-purchased-subscription)kullanılarak ticari Market yetkilendirme belirteci çözümlendikten sonra elde edilir.
 |

*İstek üst bilgileri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
| `x-ms-correlationid` |  İstemcideki işlem için benzersiz bir dize değeri.  Bu dize, istemci işlemindeki tüm olayları sunucu tarafında bulunan olaylarla ilişkilendirir.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
| `authorization`      |  Bu API çağrısını yapan yayımcıyı tanımlayan benzersiz bir erişim belirteci. Biçim, `"Bearer <access_token>"` [Azure AD uygulamasına dayalı bir belirteç al](./pc-saas-registration.md#get-the-token-with-an-http-post)bölümünde açıklandığı gibi, belirteç değeri yayımcı tarafından alınır. |

*İstek yükü örneği:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Yanıt kodları:*

Kod: 200 abonelik Microsoft tarafında abone olarak işaretlendi.

Bu çağrı için yanıt gövdesi yok.

Kod: 400 hatalı istek: doğrulama başarısız oldu.

* `planId` istek yükünde yok.
* `planId` istek yükünde, satın alınan bir ile eşleşmez.
* `quantity` istek yükünde, satın alınan bir ile eşleşmiyor
* SaaS aboneliği *abone olmuş* veya *askıya alınmış* durumda.

Kod: 403 Yasak. Yetkilendirme belirteci geçersiz, zaman aşımına uğradı veya sağlanmadı. İstek, yetkilendirme belirtecini oluşturmak için kullanılan sunucudan farklı bir Azure AD uygulama KIMLIĞIYLE yayınlanan teklifin bir SaaS aboneliğine erişmeye çalışıyor.

Bu hata genellikle [SaaS kaydının](pc-saas-registration.md) doğru bir şekilde gerçekleştirmediğini bir belirtisidir.

Kod: 404 bulunamadı. SaaS aboneliği *abone olunmayan* bir durumda.

Kod: 500 Iç sunucu hatası.  API çağrısını yeniden deneyin.  Hata devam ederse, [Microsoft desteği](https://partner.microsoft.com/support/v2/?stage=1)'ne başvurun.

#### <a name="get-list-of-all-subscriptions"></a>Tüm Aboneliklerin listesini al

Bu API, yayımcının ticari Market 'te yayımladığı tüm teklifler için satın alınan tüm SaaS aboneliklerinin bir listesini alır.  Tüm olası durumlarda SaaS abonelikleri döndürülür. Bu bilgiler Microsoft tarafında silinmediğinden, abone olunan SaaS abonelikleri de döndürülür.

API, sayfa başına 100 sayfalandırılmış sonuçlar döndürüyor.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Al `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|  Parametre         | Değer             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31 kullanın.  |
| `continuationToken`  | İsteğe bağlı parametre. Sonuçların ilk sayfasını almak için boş bırakın.  `@nextLink`Sonraki sayfayı almak için parametresinde döndürülen değeri kullanın. |

*İstek üst bilgileri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
| `x-ms-correlationid` |  İstemcideki işlem için benzersiz bir dize değeri.  Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
| `authorization`      |  Bu API çağrısını yapan yayımcıyı tanımlayan benzersiz bir erişim belirteci.  Biçim, `"Bearer <access_token>"` [Azure AD uygulamasına dayalı bir belirteç al](./pc-saas-registration.md#get-the-token-with-an-http-post)bölümünde açıklandığı gibi, belirteç değeri yayımcı tarafından alınır. |

*Yanıt kodları:*

Code: 200, yayımcının yetkilendirme belirtecine bağlı olarak, bu yayımcının yaptığı tüm teklifler için mevcut tüm Aboneliklerin listesini döndürür.

*Yanıt gövdesi örneği:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Bu Yayımcı için satın alınan SaaS abonelikleri bulunmazsa boş yanıt gövdesi döndürülür.

Kod: 403 Yasak. Yetkilendirme belirteci kullanılamıyor, geçersiz veya zaman aşımına uğradı.

Bu hata genellikle [SaaS kaydının](pc-saas-registration.md) doğru bir şekilde gerçekleştirmediğini bir belirtisidir. 

Kod: 500 Iç sunucu hatası. API çağrısını yeniden deneyin.  Hata devam ederse, [Microsoft desteği](https://partner.microsoft.com/support/v2/?stage=1)'ne başvurun.

#### <a name="get-subscription"></a>Abonelik al

Bu API, yayımcının ticari Market 'te yayımladığına yönelik bir SaaS teklifi için belirtilen satın alınan SaaS aboneliğini alır. Tüm aboneliklerinin listesini almak için kullanılan API 'YI çağırmak yerine, belirli bir SaaS aboneliğine ait tüm kullanılabilir bilgileri KIMLIĞINE göre almak için bu çağrıyı kullanın.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Al `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   2018-08-31 kullanın. |
| `subscriptionId`     |  Satın alınan SaaS aboneliğinin benzersiz tanımlayıcısı.  Bu KIMLIK, Çözümle API 'SI kullanılarak ticari Market yetkilendirme belirteci çözümlendikten sonra elde edilir. |

*İstek üst bilgileri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  `x-ms-correlationid` |  İstemcideki işlem için benzersiz bir dize değeri.  Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  `authorization`     | Bu API çağrısını yapan yayımcıyı tanımlayan benzersiz bir erişim belirteci. Biçim, `"Bearer <access_token>"` [Azure AD uygulamasına dayalı bir belirteç al](./pc-saas-registration.md#get-the-token-with-an-http-post)bölümünde açıklandığı gibi, belirteç değeri yayımcı tarafından alınır.  |

*Yanıt kodları:*

Code: 200, sunulan bir SaaS aboneliğinin ayrıntılarını döndürür `subscriptionId` .

*Yanıt gövdesi örneği:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Kod: 403 Yasak. Yetkilendirme belirteci geçersiz, zaman aşımına uğradı veya sağlanmadı. İstek, yetkilendirme belirtecini oluşturmak için kullanılan birinden farklı bir Azure AD uygulama KIMLIĞIYLE yayınlanan bir teklif için SaaS aboneliğine erişmeye çalışıyor.

Bu hata genellikle [SaaS kaydının](pc-saas-registration.md) doğru bir şekilde gerçekleştirmediğini bir belirtisidir. 

Kod: 404 bulunamadı.  Belirtilen SaaS aboneliği `subscriptionId` bulunamıyor.

Kod: 500 Iç sunucu hatası.  API çağrısını yeniden deneyin.  Hata devam ederse, [Microsoft desteği](https://partner.microsoft.com/support/v2/?stage=1)'ne başvurun.

#### <a name="list-available-plans"></a>Kullanılabilir planları listeleme

Bu API, `subscriptionId` Bu teklifin belirli bir satın alımı tarafından tanımlanan SaaS teklifinin tüm planlarını alır.  SaaS aboneliğinin lehdar sürümünün abonelik için güncelleştireabileceği tüm özel ve genel planların listesini almak için bu çağrıyı kullanın.  Döndürülen planlar, zaten satın alınan planla aynı coğrafya içinde kullanılabilir olacaktır.

Bu çağrı, zaten satın alınan birinin yanı sıra o müşteri için kullanılabilen planların listesini döndürür.  Liste, yayımcı sitesindeki son kullanıcıya sunulabilir.  Son Kullanıcı, bir abonelik planını döndürülen listede planlardan herhangi birine değiştirebilir.  Planı listede bulunmayan bir ile değiştirmek başarısız olur.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Al `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 kullanın.  |
|  `subscriptionId`    |  Satın alınan SaaS aboneliğinin benzersiz tanımlayıcısı.  Bu KIMLIK, Çözümle API 'SI kullanılarak ticari Market yetkilendirme belirteci çözümlendikten sonra elde edilir. |

*İstek üst bilgileri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  `x-ms-correlationid`  |  İstemcideki işlem için benzersiz bir dize değeri.  Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  `authorization`     |  Bu API çağrısını yapan yayımcıyı tanımlayan benzersiz bir erişim belirteci.  Biçim, `"Bearer <access_token>"` [Azure AD uygulamasına dayalı bir belirteç al](./pc-saas-registration.md#get-the-token-with-an-http-post)bölümünde açıklandığı gibi, belirteç değeri yayımcı tarafından alınır.  |

*Yanıt kodları:*

Code: 200, zaten satın alınmış olan dahil olmak üzere mevcut bir SaaS aboneliği için tüm kullanılabilir planların listesini döndürür.

Yanıt gövdesi örneği:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

`subscriptionId`Bulunmazsa boş yanıt gövdesi döndürülür.

Kod: 403 Yasak. Yetkilendirme belirteci geçersiz, zaman aşımına uğradı veya sağlanmadı.  İstek, yetkilendirme belirtecini oluşturmak için kullanılan birinden farklı bir Azure AD uygulama KIMLIĞIYLE yayınlanan bir teklif için SaaS aboneliğine erişmeye çalışıyor olabilir.

Bu hata genellikle [SaaS kaydının](pc-saas-registration.md) doğru bir şekilde gerçekleştirmediğini bir belirtisidir. 

Kod: 500 Iç sunucu hatası.  API çağrısını yeniden deneyin.  Hata devam ederse, [Microsoft desteği](https://partner.microsoft.com/support/v2/?stage=1)'ne başvurun.

#### <a name="change-the-plan-on-the-subscription"></a>Abonelikte planı değiştirme

SaaS aboneliği için satın alınan mevcut planı yeni bir plana (genel veya özel) güncelleştirmek için bu API 'yi kullanın.  Yayımcı, ticari Market 'te satın alınan SaaS aboneliğinin Yayımcı tarafında bir plan değiştirildiğinde bu API 'YI çağırmalıdır.

Bu API yalnızca *etkin* abonelikler için çağrılabilir.  Herhangi bir plan, var olan başka bir plana (genel veya özel) değiştirilebilir ancak kendisi için geçerli değildir.  Özel planlar için, müşterinin kiracının Iş Ortağı Merkezi 'nde planın hedef kitlesi kapsamında tanımlanması gerekir.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Düzeltmesi `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 kullanın.  |
| `subscriptionId`     | Satın alınan SaaS aboneliğinin benzersiz tanımlayıcısı.  Bu KIMLIK, Çözümle API 'SI kullanılarak ticari Market yetkilendirme belirteci çözümlendikten sonra elde edilir. |

*İstek üst bilgileri:*
 
|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  `x-ms-correlationid`  | İstemcideki işlem için benzersiz bir dize değeri.  Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  `authorization`     |  Bu API çağrısını yapan yayımcıyı tanımlayan benzersiz bir erişim belirteci.  Biçim, `"Bearer <access_token>"` [Azure AD uygulamasına dayalı bir belirteç al](./pc-saas-registration.md#get-the-token-with-an-http-post)bölümünde açıklandığı gibi, belirteç değeri yayımcı tarafından alınır. |

*İstek yükü örneği:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Yanıt kodları:*

Kod: 202 plan değiştirme isteği kabul edildi ve zaman uyumsuz olarak işlendi.  İş ortağının, değişiklik planı isteğinin başarısını veya başarısızlığını tespit etmek için **Işlem konumu URL 'sini** yoklamak beklenir.  İşlem için *başarısız*, *başarılı* veya *çakışmanın* son durumu alınana kadar yoklamanın her biri birkaç saniyede yapılmalıdır.  Son işlem durumu hızlı bir şekilde döndürülmelidir, ancak bazı durumlarda birkaç dakika sürebilir.

Ayrıca, eylem ticari Market tarafında başarıyla tamamlanmaya hazırlandıysa, iş ortağı Web kancası bildirimini de alır.  Yalnızca Yayımcı, Yayımcı tarafında plan değişikliğini yapması gerekir.

*Yanıt üst bilgileri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  İşlemin durumunu almak için URL.  Örneğin, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Kod: 400 hatalı istek: doğrulama başarısızlığı.

* Yeni plan yok veya bu belirli SaaS aboneliği için kullanılabilir değil.
* Yeni plan, geçerli planla aynıdır.
* SaaS Abonelik durumu *abone* değil.
* SaaS aboneliğine yönelik güncelleştirme işlemi içine dahil değildir `allowedCustomerOperations` .

Kod: 403 Yasak. Yetkilendirme belirteci geçersiz, zaman aşımına uğradı veya sağlanmamış.  İstek, yetkilendirme belirtecini oluşturmak için kullanılan birinden farklı bir Azure AD uygulama KIMLIĞIYLE yayınlanan bir teklif için SaaS aboneliğine erişmeye çalışıyor.

Bu hata genellikle [SaaS kaydının](pc-saas-registration.md) doğru bir şekilde gerçekleştirmediğini bir belirtisidir.

Kod: 404 bulunamadı.  İle SaaS aboneliği `subscriptionId` bulunamadı.

Kod: 500 Iç sunucu hatası.  API çağrısını yeniden deneyin.  Hata devam ederse, [Microsoft desteği](https://partner.microsoft.com/support/v2/?stage=1)'ne başvurun.

>[!NOTE]
>Her iki durumda da, bir veya daha fazla lisans için bir kez değişiklik yapılabilir.

>[!Note]
>Bu API, yalnızca son kullanıcıdan değişiklik için açık onay alındıktan sonra çağrılabilir.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>SaaS aboneliğindeki bilgisayar lisansı sayısını değiştirme

SaaS aboneliği için satın alınan bilgisayar sayısını güncelleştirmek (artırmak veya azaltmak) için bu API 'yi kullanın.  Yayımcı, ticari Market 'te oluşturulan bir SaaS aboneliği için Yayımcı tarafında değiştirildiğinde bu API 'yi çağırmalıdır.

Lisans miktarı, geçerli planda izin verilen miktardan daha fazla olamaz.  Bu durumda Yayımcı, bilgisayar bilgisayar sayısını değiştirmeden önce planı değiştirmeli.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Düzeltmesi `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 kullanın.  |
|  `subscriptionId`     | Satın alınan SaaS aboneliğinin benzersiz tanıtıcısı.  Bu KIMLIK, Çözümle API 'SI kullanılarak ticari Market yetkilendirme belirteci çözümlendikten sonra elde edilir.  |

*İstek üst bilgileri:*
 
|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  `x-ms-correlationid`  | İstemcideki işlem için benzersiz bir dize değeri.  Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  `authorization`     | Bu API çağrısını yapan yayımcıyı tanımlayan benzersiz bir erişim belirteci.  Biçim, `"Bearer <access_token>"` [Azure AD uygulamasına dayalı bir belirteç al](./pc-saas-registration.md#get-the-token-with-an-http-post)bölümünde açıklandığı gibi, belirteç değeri yayımcı tarafından alınır.  |

*İstek yükü örneği:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Yanıt kodları:*

Kod: 202 miktarı değiştirme isteği kabul edildi ve zaman uyumsuz olarak işlendi. İş ortağının, değişiklik miktarı isteğinin başarısını veya başarısızlığını tespit etmek için **Işlem konumu URL** 'sini yoklamak beklenir.  İşlem için *başarısız*, *başarılı* veya *çakışmanın* son durumu alınana kadar yoklamanın her biri birkaç saniyede yapılmalıdır.  Son işlem durumu hızlı bir şekilde döndürülmelidir, ancak bazı durumlarda birkaç dakika sürebilir.

Ayrıca, eylem ticari Market tarafında başarıyla tamamlanmaya hazırlandıysa, iş ortağı Web kancası bildirimini de alır.  Yalnızca Yayımcı, Yayımcı tarafında sayı değişikliğini yapması gerekir.

*Yanıt üst bilgileri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  İşlemin durumunu almak için bir kaynağa bağlayın.  Örneğin, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Kod: 400 hatalı istek: doğrulama başarısızlığı.

* Yeni miktar geçerli plan sınırından daha büyük veya daha düşük.
* Yeni miktar eksik.
* Yeni miktar geçerli miktarla aynıdır.
* SaaS Abonelik durumu abone değil.
* SaaS aboneliğine yönelik güncelleştirme işlemi içine dahil değildir `allowedCustomerOperations` .

Kod: 403 Yasak.  Yetkilendirme belirteci geçersiz, zaman aşımına uğradı veya sağlanmadı.  İstek, geçerli yayımcıya ait olmayan bir aboneliğe erişmeye çalışıyor.

Bu hata genellikle [SaaS kaydının](pc-saas-registration.md) doğru bir şekilde gerçekleştirmediğini bir belirtisidir. 

Kod: 404 bulunamadı.  İle SaaS aboneliği `subscriptionId` bulunamadı.

Kod: 500 Iç sunucu hatası.  API çağrısını yeniden deneyin.  Hata devam ederse, [Microsoft desteği](https://partner.microsoft.com/support/v2/?stage=1)'ne başvurun.

>[!Note]
>Tek seferde yalnızca bir plan veya miktar değiştirilebilir.

>[!Note]
>Bu API yalnızca, değişiklik için son kullanıcıdan açık onay alındıktan sonra çağrılabilir.

#### <a name="cancel-a-subscription"></a>Aboneliği iptal et

Belirtilen SaaS aboneliğini kaldırmak için bu API 'yi kullanın.  Yayımcının bu API 'yi kullanması gerekmez ve SaaS aboneliklerini iptal etmek için müşterilerin ticari Market 'e yönlendirilmesini öneririz.

Yayımcı, Yayımcı tarafında ticari Market 'te satın alınan bir SaaS aboneliğinin iptalini uygulamaya karar verirse, bu API 'yi çağırmalıdır.  Bu çağrının tamamlanmasından sonra, aboneliğin durumu Microsoft *tarafında aboneliği kaldırılacak hale gelir* .

Aşağıdaki yetkisiz kullanım dönemlerinde bir abonelik iptal edilirse müşteri faturalandırılmaz:

* Etkinleştirme sonrasında aylık bir abonelik için yirmi dört saat.
* Etkinleştirme sonrasında yıllık bir abonelik için on dört gün.

Önceki yetkisiz kullanım dönemlerinden sonra bir abonelik iptal edilirse müşteri faturalandırılır.  Müşteri, iptalden hemen sonra Microsoft tarafında SaaS aboneliğine erişimi kaybedecektir. 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Silmeli `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 kullanın.  |
|  `subscriptionId`     | Satın alınan SaaS aboneliğinin benzersiz tanımlayıcısı.  Bu KIMLIK, Çözümle API 'SI kullanılarak ticari Market yetkilendirme belirteci çözümlendikten sonra elde edilir.  |

*İstek üst bilgileri:*
 
|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  `x-ms-correlationid`  | İstemcideki işlem için benzersiz bir dize değeri.  Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  `authorization`     |  Bu API çağrısını yapan yayımcıyı tanımlayan benzersiz bir erişim belirteci.  Biçim, `"Bearer <access_token>"` [Azure AD uygulamasına dayalı bir belirteç al](./pc-saas-registration.md#get-the-token-with-an-http-post)bölümünde açıklandığı gibi, belirteç değeri yayımcı tarafından alınır. |

*Yanıt kodları:*

Kod: 202 aboneliği kaldırma isteği kabul edildi ve zaman uyumsuz olarak işlendi.  İş ortağının, bu isteğin başarısını veya başarısızlığını tespit etmek için **Işlem konumu URL 'sini** yoklamak beklenir.  İşlem için *başarısız*, *başarılı* veya *çakışmanın* son durumu alınana kadar yoklamanın her biri birkaç saniyede yapılmalıdır.  Son işlem durumu hızlı bir şekilde döndürülmelidir, ancak bazı durumlarda birkaç dakika sürebilir.

İş ortağı, ticari Market tarafında eylem başarıyla tamamlandığında Web kancası bildirimi de alır.  Yalnızca Yayımcı, Yayımcı tarafında aboneliği iptal etmelidir.

*Yanıt üst bilgileri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  İşlemin durumunu almak için bir kaynağa bağlayın.  Örneğin, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code: 400 hatalı istek.  `allowedCustomerOperations`Bu SaaS aboneliğine yönelik silme listede değil.

Kod: 403 Yasak.  Yetkilendirme belirteci geçersiz, zaman aşımına uğradı veya kullanılamıyor. İstek, yetkilendirme belirtecini oluşturmak için kullanılan birinden farklı bir Azure AD uygulama KIMLIĞIYLE yayınlanan bir teklif için SaaS aboneliğine erişmeye çalışıyor.

Bu hata genellikle [SaaS kaydının](pc-saas-registration.md) doğru bir şekilde gerçekleştirmediğini bir belirtisidir.

Kod: 404 bulunamadı.  İle SaaS aboneliği `subscriptionId` bulunamadı.

Kod: 500 Iç sunucu hatası. API çağrısını yeniden deneyin.  Hata devam ederse, [Microsoft desteği](https://partner.microsoft.com/support/v2/?stage=1)'ne başvurun.

### <a name="operations-apis"></a>İşlem API 'Leri

#### <a name="list-outstanding-operations"></a>Bekleyen işlemleri Listele 

Belirtilen SaaS aboneliği için bekleyen işlemlerin listesini al.  Yayımcı, [Işlem düzeltme EKI API](#update-the-status-of-an-operation)'sini çağırarak döndürülen işlemleri kabul etmelidir.

Şu anda yalnızca geri çağırma **işlemleri** bu API çağrısı için yanıt olarak döndürülür.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Al `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  2018-08-31 kullanın.         |
|    `subscriptionId` | Satın alınan SaaS aboneliğinin benzersiz tanımlayıcısı.  Bu KIMLIK, Çözümle API 'SI kullanılarak ticari Market yetkilendirme belirteci çözümlendikten sonra elde edilir.  |

*İstek üst bilgileri:*
 
|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  `x-ms-correlationid` |  İstemcideki işlem için benzersiz bir dize değeri.  Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  `authorization`     |  Biçim, `"Bearer <access_token>"` [Azure AD uygulamasına dayalı bir belirteç al](./pc-saas-registration.md#get-the-token-with-an-http-post)bölümünde açıklandığı gibi, belirteç değeri yayımcı tarafından alınır.  |

*Yanıt kodları:*

Code: 200, belirtilen SaaS aboneliğinde bekleyen yeniden açma işlemini döndürüyor.

*Yanıt yükü örneği:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Bekleyen bir geri dönüş işlemi yoksa, boş JSON döndürür.

Kod: 400 hatalı istek: doğrulama başarısızlığı.

Kod: 403 Yasak. Yetkilendirme belirteci geçersiz, zaman aşımına uğradı veya sağlanmadı.  İstek, yetkilendirme belirtecini oluşturmak için kullanılan birinden farklı bir Azure AD uygulama KIMLIĞIYLE yayınlanan bir teklif için SaaS aboneliğine erişmeye çalışıyor.

Bu hata genellikle [SaaS kaydının](pc-saas-registration.md) doğru bir şekilde gerçekleştirmediğini bir belirtisidir. 

Kod: 404 bulunamadı.  İle SaaS aboneliği `subscriptionId` bulunamadı.

Kod: 500 Iç sunucu hatası. API çağrısını yeniden deneyin.  Hata devam ederse, [Microsoft desteği](https://partner.microsoft.com/support/v2/?stage=1)'ne başvurun.

#### <a name="get-operation-status"></a>İşlem durumunu al

Bu API, yayımcının belirtilen zaman uyumsuz işlemin durumunu izlemesini sağlar:  **abonelik kaldırma**, **değişiklik planı** veya **değişiklik miktarı**.

`operationId`Bu API çağrısı için, **işlem konumu** tarafından döndürülen değerden, bekleyen işlemleri al API çağrısı veya `<id>` bir Web kancası çağrısında alınan parametre değeri alınabilir.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Al `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 kullanın.  |
|  `subscriptionId`    |  Satın alınan SaaS aboneliğinin benzersiz tanımlayıcısı.  Bu KIMLIK, Çözümle API 'SI kullanılarak ticari Market yetkilendirme belirteci çözümlendikten sonra elde edilir. |
|  `operationId`       |  Alınmakta olan işlemin benzersiz tanımlayıcısı. |

*İstek üst bilgileri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  `x-ms-correlationid` |  İstemcideki işlem için benzersiz bir dize değeri.  Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  `authorization`     |  Bu API çağrısını yapan yayımcıyı tanımlayan benzersiz bir erişim belirteci.  Biçim, `"Bearer <access_token>"` [Azure AD uygulamasına dayalı bir belirteç al](./pc-saas-registration.md#get-the-token-with-an-http-post)bölümünde açıklandığı gibi, belirteç değeri yayımcı tarafından alınır.  |

*Yanıt kodları:*

Code: 200, belirtilen SaaS işleminin ayrıntılarını alır. 

*Yanıt yükü örneği:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Kod: 403 Yasak. Yetkilendirme belirteci geçersiz, zaman aşımına uğradı veya sağlanmadı.  İstek, yetkilendirme belirtecini oluşturmak için kullanılan birinden farklı bir Azure AD uygulama KIMLIĞIYLE yayınlanan bir teklif için SaaS aboneliğine erişmeye çalışıyor.

Bu hata genellikle [SaaS kaydının](pc-saas-registration.md) doğru bir şekilde gerçekleştirmediğini bir belirtisidir. 

Kod: 404 bulunamadı.  

* İle abonelik `subscriptionId` bulunamadı.
* İle işlem `operationId` bulunamadı.

Kod: 500 Iç sunucu hatası.  API çağrısını yeniden deneyin.  Hata devam ederse, [Microsoft desteği](https://partner.microsoft.com/support/v2/?stage=1)'ne başvurun.

#### <a name="update-the-status-of-an-operation"></a>İşlemin durumunu güncelleştirme

İşlemin başarısını veya başarısızlığını Yayımcı tarafında göstermek üzere bekleyen bir işlemin durumunu güncelleştirmek için bu API 'yi kullanın.

`operationId`Bu API çağrısı için, **işlem konumu** tarafından döndürülen değerden, bekleyen işlemleri al API çağrısı veya `<id>` bir Web kancası çağrısında alınan parametre değeri alınabilir.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Düzeltmesi `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  2018-08-31 kullanın.  |
|   `subscriptionId`   |  Satın alınan SaaS aboneliğinin benzersiz tanımlayıcısı.  Bu KIMLIK, Çözümle API 'SI kullanılarak ticari Market yetkilendirme belirteci çözümlendikten sonra elde edilir.  |
|   `operationId`      |  Tamamlanan işlemin benzersiz tanımlayıcısı. |

*İstek üst bilgileri:*

|  Parametre         | Değer             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|   `x-ms-correlationid` |  İstemcideki işlem için benzersiz bir dize değeri.  Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir.  Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  `authorization`     |  Bu API çağrısını yapan yayımcıyı tanımlayan benzersiz bir erişim belirteci.  Biçim, `"Bearer <access_token>"` [Azure AD uygulamasına dayalı bir belirteç al](./pc-saas-registration.md#get-the-token-with-an-http-post)bölümünde açıklandığı gibi, belirteç değeri yayımcı tarafından alınır. |

*İstek yükü örneği:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Yanıt kodları:*

Kod: 200 iş ortağı tarafındaki bir işlemin tamamlandığını bilgilendirmek için bir çağrı.  Örneğin, bu yanıt yayımcı tarafındaki koltuk veya planların değişim değişikliğini işaret edebilen bir uyarı verebilir.

Kod: 403
- Inı.  Yetkilendirme belirteci kullanılamıyor, geçersiz veya zaman aşımına uğradı. İstek, geçerli yayımcıya ait olmayan bir aboneliğe erişmeye çalışıyor olabilir.
- Inı.  Yetkilendirme belirteci geçersiz, zaman aşımına uğradı veya sağlanmadı.  İstek, yetkilendirme belirtecini oluşturmak için kullanılan birinden farklı bir Azure AD uygulama KIMLIĞIYLE yayınlanan bir teklif için SaaS aboneliğine erişmeye çalışıyor.

Bu hata genellikle [SaaS kaydının](pc-saas-registration.md) doğru bir şekilde gerçekleştirmediğini bir belirtisidir.

Kod: 404 bulunamadı.

* İle abonelik `subscriptionId` bulunamadı.
* İle işlem `operationId` bulunamadı.

Kod: 409 çakışması.  Örneğin, daha yeni bir güncelleştirme zaten yerine getirilir.

Kod: 500 Iç sunucu hatası.  API çağrısını yeniden deneyin.  Hata devam ederse, [Microsoft desteği](https://partner.microsoft.com/support/v2/?stage=1)'ne başvurun.

## <a name="implementing-a-webhook-on-the-saas-service"></a>SaaS hizmetinde Web kancası uygulama

Iş Ortağı Merkezi 'nde bir transactable SaaS teklifi oluştururken, iş ortağı bir HTTP uç noktası olarak kullanılacak **bağlantı Web kancası** URL 'sini sağlar.  Bu Web kancasının, Microsoft tarafında gerçekleşen aşağıdaki olayların yayımcısını bildirmek için HTTP POST sonrası çağrısı kullanılarak Microsoft tarafından çağrılır:

* SaaS aboneliği *abone olunma* durumunda olduğunda:
    * Değişiklik planı 
    * Değişiklik miktarı
    * Askıya Alma
    * Abonelikten ayrıl
* SaaS aboneliği *askıya alınma* durumunda olduğunda:
    * Yeniden uygulamaya koy
    * Abonelikten ayrıl

Yayımcının, SaaS aboneliği durumunun Microsoft tarafı ile tutarlı kalmasını sağlamak için SaaS hizmetinde bir Web kancası uygulaması gerekir.  SaaS hizmeti, Web kancası bildirimine göre işlem yapmadan önce Web kancası çağrısını ve yük verilerini doğrulamak ve yetkilendirmek için alma Işlemi API 'sini çağırmak zorundadır.  Yayımcı, Web kancası çağrısı işlendikten hemen sonra HTTP 200 ' i Microsoft 'a döndürmelidir.  Bu değer, Web kancası çağrısının yayımcı tarafından başarıyla alındığını bildirir.

>[!Note]
>Web kancası URL 'SI hizmeti, 7/24 çalışır ve her zaman Microsoft 'tan yeni çağrılar almaya hazırlanmalıdır.  Microsoft, Web kancası çağrısı için bir yeniden deneme ilkesine sahiptir 500 (8 saatten fazla yeniden deneme), ancak yayımcı çağrıyı kabul edip bir yanıt döndüremezse, Web kancasının bildirim aldığı işlem Microsoft tarafında da başarısız olur.

*Web kancası yükü örnekleri:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": " 20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress"
}
```

## <a name="development-and-testing"></a>Geliştirme ve test

Geliştirme sürecini başlatmak için Yayımcı tarafında kukla API yanıtları oluşturmanızı öneririz.  Bu yanıtlar, bu makalede belirtilen örnek yanıtlara bağlı olabilir.

Yayımcı uçtan uca teste hazırsanız:

* Bir SaaS teklifini sınırlı bir önizleme izleyicisine yayımlayın ve önizleme aşamasında saklayın.
* Test sırasında gerçek faturalandırma masrafını önlemek için plan fiyatını 0 olarak ayarlayın.  Başka bir seçenek de sıfır olmayan bir fiyat ayarlamak ve 24 saat içindeki tüm test satın alımlarını iptal etmenizi sağlar.
* Gerçek bir müşteri senaryosunun benzetimini yapmak için tüm akışların uçtan uca çağrıldığından emin olun.
* İş ortağı tam satın alma ve faturalandırma akışını test etmek isterse, $0 üzerinde fiyatlandırıldıkları teklifle bunu yapın.  Satın alma, faturalandırılacak ve bir fatura oluşturulacaktır.

Teklifin nerede yayımlandığına bağlı olarak, Azure portal veya Microsoft AppSource sitelerinden bir satın alma akışı tetiklenebilir.

*Değişiklik planı*, *değişiklik miktarı* ve *abonelik kaldırma* eylemleri Yayımcı tarafında test edilir.  Microsoft tarafında, *abonelik kaldırma* hem Azure Portal hem de yönetim merkezinden (Microsoft AppSource satın alımlarınızın yönetildiği portal) tetiklenebilir.  *Değişiklik miktarı ve plan* yalnızca yönetim merkezinden tetiklenebilir.

## <a name="get-support"></a>Destek alın

Yayımcı desteği seçenekleri için [Iş Ortağı Merkezi ' nde ticari Market programına yönelik desteğe](../support.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Ticari Market 'teki SaaS teklifleri hakkında daha fazla seçenek için bkz. [ticari Market ölçüm hizmeti API 'leri](marketplace-metering-service-apis.md) .

[Farklı programlama dilleri ve örnekleri için istemcileri](https://github.com/microsoft/commercial-marketplace-samples)gözden geçirin ve kullanın.
