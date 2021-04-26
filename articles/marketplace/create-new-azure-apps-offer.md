---
title: Ticari Market 'te Azure uygulaması teklifi oluşturma
description: Azure Marketi 'nde listeleme veya satma için yeni bir Azure Uygulama teklifi oluşturmayı veya Microsoft Iş Ortağı Merkezi 'nde ticari Market portalını kullanan bulut çözümü sağlayıcısı (CSP) programını kullanmayı öğrenin.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370450"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>Ticari Market 'te Azure uygulaması teklifi oluşturma

Ticari Market yayımcısı olarak, olası müşterilerin çözümünüzü satın alabileceği bir Azure uygulaması teklifi oluşturabilirsiniz. Bu makalede, Microsoft ticari Market için bir Azure uygulaması teklifi oluşturma işlemi açıklanmaktadır.

Daha önce yapmadıysanız, [ticari Market için bir Azure Uygulama teklifi planı](plan-azure-application-offer.md)makalesini okuyun. Bu, kaynakları sağlar ve teklifinizi oluştururken ihtiyaç duyacağınız bilgileri ve varlıkları toplamanıza yardımcı olur.

## <a name="create-a-new-offer"></a>Yeni teklif oluşturma

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)' nde oturum açın.

1. Sol gezinti menüsünde **ticari Market**  >  **genel bakış**' ı seçin.

1. Genel Bakış sayfasında **+ yeni teklif**  >  **Azure uygulaması**' nı seçin.

    ![Sol gezinti menüsünü gösterir.](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. **Yeni teklif** iletişim kutusunda BIR **teklif kimliği** girin. Bu, hesabınızdaki her teklif için benzersiz bir tanımlayıcıdır. Bu KIMLIK, varsa ticari Market listeleme ve Azure Resource Manager şablonlarının URL 'sinde görülebilir. Örneğin, bu kutuya test-teklif-1 girerseniz, teklif Web adresi olacaktır `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

     * Hesabınızdaki her teklifin benzersiz bir teklif KIMLIĞI olmalıdır.
     * Yalnızca küçük harfleri ve rakamları kullanın. Kısa çizgi ve alt çizgi içerebilir, ancak boşluk içeremez ve 50 karakterle sınırlıdır.
     * Teklif KIMLIĞI, **Oluştur**' u seçtikten sonra değiştirilemez.

1. Bir **teklif diğer adı** girin. Bu, Iş Ortağı Merkezi 'nde teklif için kullanılan addır.

     * Bu ad yalnızca Iş Ortağı Merkezi 'nde görünür ve teklif adından ve müşterilere gösterilen diğer değerlerden farklıdır.
     * Teklif diğer adı, **Oluştur**' u seçtikten sonra değiştirilemez.

1. Teklifi oluşturmak ve devam etmek için  **Oluştur**' u seçin.

## <a name="configure-your-azure-application-offer-setup-details"></a>Azure Uygulama teklifi kurulum ayrıntılarını yapılandırma

**Teklif kurulumu** sekmesinde, **Kurulum Ayrıntıları**' nın altında, bir sınama sürücüsü yapılandırıp yapılandırmayamayacağını seçersiniz. Ayrıca, müşteri ilişkileri yönetimi (CRM) sisteminizi ticari Market teklifinizi kullanarak da bağlayacaksınız.

### <a name="enable-a-test-drive-optional"></a>Sınama sürücüsünü etkinleştir (isteğe bağlı)

Test sürücüsü, bu kullanıcılara, bir sabit sayıda saat boyunca önceden yapılandırılmış bir ortama erişim vererek, teklifinizi olası müşterilere sergilemenin harika bir yoludur. Sınama sürücüsü sunumu, daha fazla dönüştürme hızına neden olur ve yüksek oranda nitelikli müşteri adayları üretir. Sınama sürücüleri hakkında daha fazla bilgi için bkz. [test sürücüsü](plan-azure-application-offer.md#test-drive).

#### <a name="to-enable-a-test-drive"></a>Bir sınama sürücüsünü etkinleştirmek için

- **Test sürücüsü** altında, **sınama sürücüsünü etkinleştir** onay kutusunu seçin.

### <a name="customer-lead-management"></a>Müşteri lideri yönetimi

Müşteri ilişkileri yönetimi (CRM) sisteminizi ticari Market teklifinizi kullanarak bağlayın, böylece müşteri, faiz veya ürününüzü dağıtırken müşteri iletişim bilgilerini alabilirsiniz.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>Iş Ortağı Merkezi 'nde bağlantı ayrıntılarını yapılandırmak için

1. **Müşteri liderleri** altında **Bağlan** bağlantısını seçin.
1. **Bağlantı ayrıntıları** iletişim kutusunda listeden bir müşteri adayı hedefi seçin.
1. Görüntülenen alanları doldurun. Ayrıntılı adımlar için aşağıdaki makalelere bakın:

   - [Teklifinizi Azure tablosuna müşteri adayları gönderecek şekilde yapılandırma](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Teklifinizi dynamics 365 müşteri katılımını](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (eski ADıYLA Dynamics CRM Online) göndermek Için uygulamanızı yapılandırın
   - [Teklifinizi, HTTPS uç noktasına müşteri adayları gönderecek şekilde yapılandırma](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Teklifinizi müşteri adaylarını Marketo 'ya gönderecek şekilde yapılandırma](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Teklifinizi Salesforce 'a göndermek için teklifinizi yapılandırma](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Verdiğiniz yapılandırmayı doğrulamak için, varsa **Doğrula** bağlantısını seçin.
1. İletişim kutusunu kapatmak için **Bağlan**' ı seçin.
1. Sonraki sekmeye geçmeden önce **Taslağı kaydet** ' i seçin: Özellikler.

> [!NOTE]
> Müşteri adayı hedefine olan bağlantının güncel kaldığından emin olun, böylelikle müşteri adaylarını kaybetmezsiniz. Her değişiklik yapıldığında bu bağlantıları güncelleştirdiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure uygulaması teklif özelliklerinizi yapılandırma](create-new-azure-apps-offer-properties.md)
