---
title: Microsoft ticari marketi 'nde SaaS teklifi oluşturma
description: Microsoft AppSource, Azure Marketi 'nde veya Microsoft Iş Ortağı Merkezi 'ndeki ticari Market portalı 'nı kullanarak bulut çözümü sağlayıcısı (CSP) programı aracılığıyla listelemek veya satmaya yönelik yeni bir hizmet olarak yazılım (SaaS) teklifi oluşturmayı öğrenin.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 74d30b7c42002c8f134520e0198774eba1519bcd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553847"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>Ticari Market 'te SaaS teklifi oluşturma

Ticari Market yayımcısı olarak bir hizmet olarak yazılım (SaaS) teklifi oluşturarak, potansiyel müşterilerin SaaS tabanlı teknik çözümünüzü satın almasını sağlayabilirsiniz. Bu makalede, Microsoft ticari Market için bir SaaS teklifi oluşturma işlemi açıklanmaktadır.

## <a name="before-you-begin"></a>Başlamadan önce

Daha önce yapmadıysanız, [ticari Market için bir SaaS teklifi planı](plan-saas-offer.md)makalesini okuyun. SaaS uygulamanız için teknik gereksinimleri ve teklifinizi oluştururken ihtiyaç duyacağınız bilgileri ve varlıkları açıklayacak. Ticari Market 'te basit bir liste (**benimle Iletişime geçin** ) yayımlamayı planlamıyorsanız, SaaS uygulamanızın kimlik doğrulama etrafında teknik gereksinimleri karşılaması gerekir.

> [!IMPORTANT]
> Ayrı bir geliştirme/test (GELIŞTIRME) teklifi ve ayrı bir üretim (ÜRETIM) teklifi oluşturmanızı öneririz. Bu makalede, bir ÜRETIM teklifinin nasıl oluşturulacağı açıklanır. DEV teklifi oluşturma hakkında ayrıntılı bilgi için bkz. [geliştirme ve test teklifi oluşturma](create-saas-dev-test-offer.md).

## <a name="create-a-new-saas-offer"></a>Yeni bir SaaS teklifi oluşturma

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)' nde oturum açın.
1. Sol gezinti menüsünde **ticari Market**  >  **genel bakış**' ı seçin.
1. **Genel bakış** sekmesinde, hizmet olarak **yeni teklif**  >  **yazılımı**' nı seçin.

   :::image type="content" source="media/new-offer-saas.png" alt-text="Sol gezinti menüsünü ve yeni teklif listesini gösterir.":::

1. **Yeni teklif** iletişim kutusunda BIR **teklif kimliği** girin. Bu KIMLIK, varsa ticari Market listeleme ve Azure Resource Manager şablonlarının URL 'sinde görülebilir. Örneğin, bu kutuya **Test-teklif-1** girerseniz, teklif Web adresi olacaktır `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
   + Hesabınızdaki her teklifin benzersiz bir teklif KIMLIĞI olmalıdır.
   + Yalnızca küçük harfleri ve rakamları kullanın. Kısa çizgi ve alt çizgi içerebilir, ancak boşluk içeremez ve 50 karakterle sınırlıdır.
   + Teklif KIMLIĞI, **Oluştur**' u seçtikten sonra değiştirilemez.

1. Bir **teklif diğer adı** girin. Bu, Iş Ortağı Merkezi 'nde teklif için kullanılan addır.

   + Bu ad ticari Market 'te görünür değildir ve müşteriler için gösterilen teklif adından ve diğer değerlerden farklıdır.
   + Teklif diğer adı, **Oluştur**' u seçtikten sonra değiştirilemez.
1. Teklifi oluşturmak ve devam etmek için **Oluştur**' u seçin.

## <a name="configure-your-saas-offer-setup-details"></a>SaaS teklifi kurulum ayrıntılarınızı yapılandırın

**Teklif kurulumu** sekmesinde, **Kurulum Ayrıntıları**' nın altında, teklifinizi Microsoft aracılığıyla satma veya işlemlerinizi bağımsız olarak yönetme seçeneklerinden birini belirlersiniz. Microsoft ile satılan tekliflere _transactable teklifleri_ denir. Bu, Microsoft 'un yayımcı adına bir yazılım lisansı için para değişimini kolaylaştıran bir araçtır. Bu seçenekler hakkında daha fazla bilgi için bkz. [Listeleme seçenekleri](plan-saas-offer.md#listing-options) ve [Yayımlama seçeneğinizi belirleme](determine-your-listing-type.md).

1. Microsoft aracılığıyla satış yapmak ve işlemleri sizin için kolaylaştırmamızı istiyorsanız **Evet**' i seçin. [Bir sınama sürücüsünü etkinleştirmeye](#enable-a-test-drive-optional)devam edin.

1. Teklifinizi ticari Market aracılığıyla listelemek ve işlemleri bağımsız olarak işlemek için **Hayır**' ı seçin ve aşağıdakilerden birini yapın:
   + Teklifiniz için ücretsiz bir abonelik sağlamak üzere **Şimdi al (ücretsiz)** seçeneğini belirleyin. Ardından, görüntülenen **TEKLIF URL** 'si kutusuna, [Azure ACTIVE DIRECTORY (Azure AD) kullanarak tek tıklamayla kimlik doğrulamasından](azure-ad-saas.md)yararlanarak müşterilerin bir deneme yanılabileceği URL 'yi ( *http* veya *https*'den başlayarak) girin. Örneğin, `https://contoso.com/saas-app`.
   + 30 günlük ücretsiz deneme sürümü sağlamak için **ücretsiz deneme**' yı seçin ve AÇıLAN **deneme URL** 'si kutusunda, [Azure Active Directory (Azure AD) kullanarak tek tıklamayla kimlik doğrulama](azure-ad-saas.md)aracılığıyla müşterilerin ücretsiz denemenize erişebileceği URL 'yi ( *http* veya *https*'den başlayarak) girin. Örneğin, `https://contoso.com/trial/saas-app`.
   + Potansiyel müşterilerin teklifinizi satın alarak sizinle iletişim kurabilmesi için **benimle Iletişim kurun**' i seçin.

## <a name="enable-a-test-drive-optional"></a>Sınama sürücüsünü etkinleştir (isteğe bağlı)

Test sürücüsü, bu kullanıcılara, bir sabit sayıda saat boyunca önceden yapılandırılmış bir ortama erişim vererek, teklifinizi olası müşterilere sergilemenin harika bir yoludur. Sınama sürücüsü sunumu, daha fazla dönüştürme hızına neden olur ve yüksek oranda nitelikli müşteri adayları üretir. Sınama sürücüleri hakkında daha fazla bilgi için bkz. [test sürücüsü nedir?](./what-is-test-drive.md).

> [!TIP]
> Bir test sürücüsü, ücretsiz deneme sürümünden farklıdır. Bir test sürücüsü, ücretsiz deneme ya da her ikisini birden sağlayabilirsiniz. Bunlar her ikisi de kendi çözümünüzü sabit bir süre için sağlar. Ancak, bir sınama sürücüsü Ayrıca, ürününüzün temel özelliklerine ve avantajlarına yönelik uygulamalı, kendinden kılavuzlu bir tura sahip olan gerçek hayatta bir uygulama senaryosunda gösterilmekte olan avantajlı bir geliştirme içerir.

**Bir sınama sürücüsünü etkinleştirmek için**
1.  **Test sürücüsü** altında, **sınama sürücüsünü etkinleştir** onay kutusunu seçin.
1.  Görüntülenen listeden test sürücü türünü seçin.

## <a name="configure-lead-management"></a>Müşteri adayı yönetimini yapılandırma

Müşteri ilişkileri yönetimi (CRM) sisteminizi ticari Market teklifinizi kullanarak bağlayın, böylece müşteri, faiz veya ürününüzü dağıtırken müşteri iletişim bilgilerini alabilirsiniz. Bu bağlantıyı, teklifi oluştururken veya sonrasında istediğiniz zaman değiştirebilirsiniz.

> [!NOTE]
> Teklifinizi Microsoft ile satdıysanız veya **benimle iletişim** listeleme seçeneğini belirlediyseniz, müşteri adayı yönetimini yapılandırmanız gerekir. Ayrıntılı kılavuz için bkz. [ticari Market teklifinizin müşteri adayları](partner-center-portal/commercial-marketplace-get-customer-leads.md).

### <a name="configure-the-connection-details-in-partner-center"></a>Iş Ortağı Merkezi 'nde bağlantı ayrıntılarını yapılandırma

1.  **Müşteri liderleri** altında **Bağlan** bağlantısını seçin.
1. **Bağlantı ayrıntıları** iletişim kutusunda listeden bir müşteri adayı hedefi seçin.
1. Görüntülenen alanları doldurun. Ayrıntılı adımlar için aşağıdaki makalelere bakın:

   - [Teklifinizi Azure tablosuna müşteri adayları gönderecek şekilde yapılandırma](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Teklifinizi dynamics 365 müşteri katılımını](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (eski ADıYLA Dynamics CRM Online) göndermek Için uygulamanızı yapılandırın
   - [Teklifinizi, HTTPS uç noktasına müşteri adayları gönderecek şekilde yapılandırma](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Teklifinizi müşteri adaylarını Marketo 'ya gönderecek şekilde yapılandırma](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Teklifinizi Salesforce 'a göndermek için teklifinizi yapılandırma](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Verdiğiniz yapılandırmayı doğrulamak için **Doğrula** bağlantısını seçin.
1. İletişim kutusunu kapatmak için **Tamam**' ı seçin.

## <a name="configure-microsoft-365-app-integration"></a>Microsoft 365 uygulama tümleştirmesini yapılandırma

SaaS teklifinizin ve ilgili Microsoft 365 uygulama tüketiminin [birleştirilmiş bulma ve teslimini](./plan-SaaS-offer.md) , aralarında bağlantı kurarak açabilirsiniz.

### <a name="integrate-with-microsoft-api"></a>Microsoft API ile tümleştirme

1. SaaS teklifiniz Microsoft Graph API ile tümleştirilemez, **Hayır**' ı seçin. Yayımlanan Microsoft 365 uygulama tüketimi istemcilerini bağlamayı sürdürün.  

1. SaaS teklifiniz Microsoft Graph API ile tümleşiyorsa **Evet**' i seçin ve sonra oluşturduğunuz ve Microsoft Graph API 'siyle tümleştirilecek Azure ACTIVE DIRECTORY uygulama kimliği ' ni belirtin. 

### <a name="link-published-microsoft-365-app-consumption-clients"></a>Yayınlanan Microsoft 365 App tüketim istemcilerinin bağlantısını yapın

1. SaaS teklifinizle birlikte çalışarak Office eklentisi, takımlar uygulamanız veya SharePoint Framework çözümlerini yayımlamadıysanız **Hayır**' ı seçin.

1. SaaS teklifinizle birlikte çalışarak Office eklentisi, takımlar uygulaması veya SharePoint Framework çözümlerini yayımladıysanız, **Evet**' i seçin ve ardından yeni bağlantı eklemek için **+ başka bir Appsource bağlantısı ekle** ' yi seçin.  

1. Geçerli bir AppSource bağlantısı sağlayın.

1. Tüm bağlantıları ekleme **+ başka bir appsource bağlantısı ekle** ve geçerli appsource bağlantıları sağla ' yı seçerek devam edin.  

1. Bağlı ürünlerin SaaS teklifinin liste sayfasında gösterildiği sıralama, derece değeri ile belirtilir, bunu seçerek, tutabilir ve = simgesini listede yukarı ve aşağı taşı olarak değiştirebilirsiniz. 

1. Ürün satırında **Sil** ' i seçerek bağlantılı bir ürünü silebilirsiniz.  


> [!IMPORTANT]
> Bağlı bir ürünü durdurduktan sonra, SaaS teklifinde otomatik olarak bağlantısı kesilmez, bağlı ürünler listesinden silmeniz ve SaaS teklifini yeniden göndermeniz gerekir.  

 

## <a name="next-steps"></a>Sonraki adımlar

- [SaaS teklif özelliklerinizi yapılandırma](create-new-saas-offer-properties.md)