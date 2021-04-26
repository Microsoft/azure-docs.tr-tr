---
title: Müşteri adayı yönetimi soruları ve sorun giderme-Microsoft Iş Ortağı Merkezi
description: Iş Ortağı Merkezi 'nde ticari Market müşteri adaylarını yapılandırırken sık karşılaşılan hatalar ve sorular hakkında bilgi edinin
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 681498073a5f111e3babd50443623589ffeec02f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330929"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>Müşteri adayı yapılandırması için sık sorulan sorular ve sorun giderme

Bu makalede, ticari Market tekliflerinizin müşteri adayı yönetimiyle ilgili bazı yaygın sorular yanıtlanmaktadır. Ayrıca, müşteri adaylarını Iş Ortağı Merkezi 'nde müşteri ilişkileri yönetimi (CRM) sistemine yapılandırırken karşılaşabileceğiniz hatalara de yöneliktir.

## <a name="common-questions-about-lead-management"></a>Müşteri adayı yönetimi hakkında sık sorulan sorular

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Lider hedefini ayarlama konusunda nereden yardım alabilirim?

CRM sisteminizin ticari Market tekliflerinizi nasıl bağlayabileceği hakkında genel bilgi için bkz. [ticari Market teklifinizden müşteri adayları](partner-center-portal/commercial-marketplace-get-customer-leads.md) . Bir hata varsa, aşağıdaki sorun giderme kılavuzunu gözden geçirin. Daha fazla destek için, [Iş Ortağı Merkezi Yardım ve destek](https://aka.ms/marketplacepublishersupport)aracılığıyla bir destek bileti gönderebilirsiniz. Teklif **oluşturma**  >    >  **müşteri adayı yönetimi yapılandırma** türünden teklif oluşturmayı seçin.

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Ticari Market 'te teklif yayımlamak için bir müşteri adayı hedefi yapılandırmak istiyorum mu?

Yanıt, yayımlamakta olduğunuz teklifin türüne bağlıdır. Hizmet olarak yazılım (SaaS) ve Dynamics 365 müşteri katılımı kullanımı, tüm Dynamics 365 finans ve Işlemler tekliflerini, tüm Dynamics 365 Iş Merkezi tekliflerini ve tüm danışmanlık hizmeti tekliflerini listelemek için **benimle Iletişim kurun** . Sonuç olarak, bir müşteri adayı hedefine bağlantı gerektirir. Teklif türü listede yoksa, bir müşteri adayı hedefine bağlantı gerekli değildir. İş fırsatlarını kaçırmamak için bir lider hedefi yapılandırmanızı öneririz.

#### <a name="how-can-i-find-the-test-lead"></a>Test lideri nasıl bulabilirim?

`"MSFT_TEST"`Müşteri adayı Hedefinizdeki arama yapın. Microsoft tarafından örnek bir test lideri aşağıda verilmiştir. Test lideri biçiminin, müşteri adayı hedefine göre değiştiğini unutmayın.

```
{
    "userDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Canlı teklifim var, ancak neden hiçbir müşteri adaylarını görmüyorum?

Müşteri adayı hedefine olan bağlantınızın geçerli olduğundan emin olun. Iş Ortağı Merkezi 'nde teklifiniz üzerinde **Yayımla** ' yı seçtikten sonra size bir test lideri göndereceğiz. Test lideri görürseniz, bağlantı geçerli olur. Ayrıca, önizleme adımı sırasında teklif önizlemeyi almaya çalışırken, müşteri adayı bağlantınızı test edebilirsiniz. Ticari Market 'teki listede **Şimdi al**, **benimle Iletişim kurun** veya **ücretsiz deneme sürümü** ' nü seçin.

Ayrıca, doğru verileri baktığınızdan emin olun. Müşteri adayı hedefine gönderdiğimiz müşteri adayı verilerinin bir açıklaması için bkz. [müşteri adayı verilerini anlama](partner-center-portal/commercial-marketplace-get-customer-leads.md) .

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Azure Blob depolamayı lider hedefim olarak yapılandırdım, ancak neden müşteri adayını görmüyorum?

Azure Blob depolama, artık bir lider hedefi olarak desteklenmediğinden teklifiniz tarafından oluşturulan müşteri adaylarını kaçırıyoruz. Diğer [lider hedefi seçeneklerinden](partner-center-portal/commercial-marketplace-get-customer-leads.md)herhangi birine geçin. 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Ticari Market 'ten bir e-posta aldım, ancak neden CRM 'de müşteri adayını bulamıyorum?

Son kullanıcının e-posta etki alanı. edu 'dan olabilir. Gizlilik nedenleriyle,. edu etki alanından kişisel bilgileri geçirmeiyoruz. [Iş Ortağı Merkezi Yardım ve destek](https://aka.ms/marketplacepublishersupport)aracılığıyla bir destek bileti gönderebilirsiniz.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Müşteri adayı hedefi olarak bir Azure tablosu yapılandırdım. Müşteri adaylarını nasıl görüntüleyebilirim?

Azure tablosunda depolanan müşteri adayı verilerine Azure portal erişebilirsiniz. Ayrıca, Azure depolama hesabınızın tablo verilerini görüntülemek için [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) ücretsiz olarak indirip yükleyebilirsiniz.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Müşteri adayı hedefi olarak bir Azure tablosu yapılandırdım. Her yeni ticari market lideri gönderildiğinde bildirim alabilir miyim?

Evet. Azure tablosuna bir müşteri adayı eklendiğinde bir e-posta gönderen Microsoft Flow ayarlamak için [Azure tablosu kullanarak müşteri adayı yönetimini yapılandırma](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) bölümündeki yönergeleri izleyin.

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Salesforce 'um hedefi olarak Salesforce yapılandırdım, ancak neden müşteri adaylarını bulamıyorum?

Web 'den müşteri adayı formunun seçim listesine göre zorunlu bir alan olup olmadığını denetleyin. Varsa, alanı zorunlu olmayan bir metin alanına geçirin.

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Lider hedefimde bir sorun oluştu ve bazı müşteri adaylarını kaçırdım. E-postada bana gönderilebilirim?

Kişisel bilgi ilkeleri nedeniyle, güvenli olmayan e-posta ile müşteri adayı bilgilerini paylaşamıyoruz.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Müşteri adayı hedefi olarak bir Azure tablosu yapılandırdım. Bana ne kadara mal olacak?

Müşteri adayı oluşturma verileri düşüktür. Neredeyse tüm yayımcılar için 1 GB 'tan az. Maliyet, alınan müşteri adaylarının sayısına bağlıdır. Örneğin, bir ayda 1.000 müşteri adayı alındıysa, maliyet 50 ilaaydır. Depolama fiyatlandırması hakkında daha fazla bilgi için bkz. [Azure depolama genel bakış fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

Sorunuz yanıtlanmazsa, [Iş Ortağı Merkezi Yardım ve destek](https://aka.ms/marketplacepublishersupport)aracılığıyla Microsoft desteği başvurun. Teklif **oluşturma**  >    >  **müşteri adayı yönetimi yapılandırma** türünden teklif oluşturmayı seçin.

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Yeni müşteri adayları alındığında e-posta bildirimleri alıyorum. Başka birini bu e-postaları alacak şekilde nasıl yapılandırabilirim?

İş Ortağı Merkezi ' nde teklifinizi erişin ve **teklif kurulumu** sayfasına gidin > **müşteri adayı yönetimi**'ni  >  **düzenleyin**. **İletişim e-postası** alanındaki e-posta adreslerini güncelleştirin.

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> Müşteri adayı yapılandırma hatalarını giderme

**Müşteri adayı Dynamics CRM 'ye kaydedilemedi. Dynamics CRM hesap ayarlarını kontrol edin. LastCRMError: Dynamics CRM 'de oturum açılamıyor, LastCRMException:** 

> Microsoft 365 kimlik doğrulaması seçildiyse, Kullanıcı hesabının ve parolanın geçerli olup olmadığını denetleyin. Azure Active Directory seçilmişse, kiracı KIMLIĞI, uygulama KIMLIĞI ve uygulama gizli anahtarı 'nın Azure Active Directory üzerinde ayarlanmış olan ile eşleşip eşleşmediğini denetleyin. [Buradaki](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)yönergeleri izleyin. Hesap Kullanıcı adı/parola geçerliyse, lütfen Dynamics 365 ' e erişime sahip olduğundan ve atanmış bir lisansa sahip olduğundan emin olun (Office kullanıcısı kullanılıyorsa Azure Active Directory veya güvenlik ayarları kullanıyorsanız 11-15 adımları). 

**Müşteri adayı Dynamics CRM 'ye kaydedilemedi. Kullanıcı, lider varlığındaki leadsourcecode özniteliği için oluşturma izinlerine sahip değil** 

> Uygulama/kullanıcının Microsoft Market lider yazıcı için güvenlik rolleri eksik. Azure Active Directory kullanıyorsanız 11-15 adımlarını veya [burada](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)bir Office kullanıcısı kullanıyorsanız güvenlik ayarlarını izleyin.

**AAD kullanılarak Dynamics CRM 'ye müşteri adayı kaydedilemedi. Özel durum:: kiracı bulunamadı. Bu örnek, kiracı için etkin abonelikler yoksa gerçekleşebilir.**  

> Lider Yönetimi bölümünde belirtilen dizin kimliği geçerli bir dizin değil. Lütfen adım 2 ' deki yönergelere (Azure Active Directory [altında) göre](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)dizin kimliğini alın.

**Müşteri adayı Dynamics CRM 'ye kaydedilemedi. LastCRMError: SecLib:: RetrievePrivilegeForUser başarısız oldu-kullanıcıya hiçbir rol atanmadı.**  

> Çözüm: Microsoft Market lider yazıcısına güvenlik rolü atayın. Güvenlik ayarları altında [buradaki](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) yönergeleri izleyin.

**AAD kullanılarak Dynamics CRM 'ye müşteri adayı kaydedilemedi. Özel durum:: tanımlayıcılı uygulama dizinde bulunamadı** 

> Lider Yönetimi bölümünde belirtilen uygulama kimliği geçerli bir dizin değil. Lütfen 8. adımdaki yönergelere göre dizin kimliğini alın (Azure Active Directory altında, [buradan](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**AAD kullanılarak Dynamics CRM 'ye müşteri adayı kaydedilemedi. Özel durum:: Istenen kiracı tanımlayıcısı geçerli değil ve geçerli dış etki alanı biçimi değil** 

> Lider Yönetimi bölümünde belirtilen dizin kimliği geçerli bir dizin değil. Lütfen adım 2 ' deki yönergelere (Azure Active Directory altında, [buradan](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)) göre dizin kimliğini alın. 

**AAD kullanılarak Dynamics CRM 'ye müşteri adayı kaydedilemedi. Özel durum:: kimlik bilgileri doğrulanırken hata.: geçersiz istemci parolası belirtildi.** 

> Çözüm: Azure portal oturum açın, uygulama anahtarının Iş Ortağı Merkezi 'nde olan ile eşleşip eşleşmediğini denetleyin. Lütfen 10. adımdaki yönergeye (Azure Active Directory), [buradan](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)) göre parola oluşturun. 

**Müşteri adayı Dynamics CRM 'ye kaydedilemedi. LastCRMError: 00:02:00 sonrasında bir yanıt beklenirken istek kanalı zaman aşımına uğradı. Istek çağrısına geçirilen zaman aşımı değerini artırın veya bağlamada SendTimeout değerini artırın. Bu işlem için ayrılan süre daha uzun bir zaman aşımı değerinin bir bölümü olabilir.**  

> Çözüm: Iş Ortağı Merkezi 'nde oturum açın, kurulum teklif >> müşteri liderleri >> URL 'SI, geçerli bir dinamik CRM örneği olup olmadığını kontrol edin.

