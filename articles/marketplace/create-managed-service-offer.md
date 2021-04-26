---
title: Microsoft ticari marketi 'nde yönetilen hizmet teklifi oluşturma
description: Microsoft Iş Ortağı Merkezi 'nde ticari Market programını kullanarak Azure Marketi için yeni bir yönetilen hizmet teklifi oluşturmayı öğrenin.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97918415"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>Ticari Market için yönetilen hizmet teklifi oluşturma

Bu makalede, Iş Ortağı Merkezi kullanılarak Microsoft Commercial Market için yönetilen hizmet teklifinin nasıl oluşturulacağı açıklanmaktadır.

Yönetilen bir hizmet teklifini yayımlamak için bulut platformunda bir altın veya gümüş Microsoft uzmanlığına sahip olmanız gerekir. Daha önce yapmadıysanız, [ticari Market Için yönetilen bir hizmet teklifi planı](./plan-managed-service-offer.md)makalesini okuyun. Bu, teklifi Iş Ortağı Merkezi 'nde oluştururken ihtiyacınız olan varlıkları hazırlamanıza yardımcı olur.

## <a name="create-a-new-offer"></a>Yeni teklif oluşturma

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)' nde oturum açın.
2. Sol gezinti menüsünde **ticari Market**  >  **genel bakış**' ı seçin.
3. Genel Bakış sekmesinde **+ yeni teklif**  >  **yönetilen hizmet**' i seçin.

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="Sol gezinti menüsünü gösterir.":::

4. **Yeni teklif** iletişim kutusunda BIR **teklif kimliği** girin. Bu, hesabınızdaki her teklif için benzersiz bir tanımlayıcıdır. Bu KIMLIK, varsa ticari Market listeleme ve Azure Resource Manager şablonlarının URL 'sinde görülebilir. Örneğin, bu kutuya test-teklif-1 girerseniz, teklif Web adresi olacaktır `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Hesabınızdaki her teklifin benzersiz bir teklif KIMLIĞI olmalıdır.
    * Yalnızca küçük harfleri ve rakamları kullanın. Kısa çizgi ve alt çizgi içerebilir, ancak boşluk içeremez ve 50 karakterle sınırlıdır.
    * Teklif KIMLIĞI, **Oluştur**' u seçtikten sonra değiştirilemez.

5. Bir **teklif diğer adı** girin. Bu, Iş Ortağı Merkezi 'nde teklif için kullanılan addır. Çevrimiçi mağazalarda görünmez ve müşteriler tarafından gösterilen teklif adından farklıdır.
6. Teklifi oluşturmak ve devam etmek için **Oluştur**' u seçin.

## <a name="configure-lead-management"></a>Müşteri adayı yönetimini yapılandırma

Müşteri ilişkileri yönetimi (CRM) sisteminizi ticari Market teklifinizi kullanarak bağlayın. böylece müşteri, danışmanlık hizmetinize ilgi ifade edildiğinde müşteri iletişim bilgilerini alabilirsiniz. Bu bağlantıyı, teklifi oluştururken veya sonrasında istediğiniz zaman değiştirebilirsiniz. Ayrıntılı kılavuz için bkz. [ticari Market teklifinizin müşteri adayları](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Iş Ortağı Merkezi 'nde lider yönetimini yapılandırmak için:

1. Iş Ortağı Merkezi 'nde **teklif kurulumu** sekmesine gidin.
2. **Müşteri liderleri** altında **Bağlan** bağlantısını seçin.
3. **Bağlantı ayrıntıları** iletişim kutusunda listeden bir müşteri adayı hedefi seçin.
4. Görüntülenen alanları doldurun. Ayrıntılı adımlar için aşağıdaki makalelere bakın:

    * [Teklifinizi Azure tablosuna müşteri adayları gönderecek şekilde yapılandırma](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Teklifinizi dynamics 365 müşteri katılımını](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (eski ADıYLA Dynamics CRM Online) göndermek Için uygulamanızı yapılandırın
    * [Teklifinizi, HTTPS uç noktasına müşteri adayları gönderecek şekilde yapılandırma](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Teklifinizi müşteri adaylarını Marketo 'ya gönderecek şekilde yapılandırma](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Teklifinizi Salesforce 'a göndermek için teklifinizi yapılandırma](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. Verdiğiniz yapılandırmayı doğrulamak için **doğrula bağlantısını** seçin.
6. Bağlantı ayrıntılarını yapılandırdığınızda, **Bağlan**' ı seçin.
7. **Taslağı kaydet**'i seçin.

Teklifinizi Iş Ortağı Merkezi 'ne gönderdikten sonra, bağlantıyı doğrulayacağız ve size bir test lideri göndereceğiz. Teklifi canlı olmadan önce önizlerken, teklifi önizleme ortamında satın almaya çalışırken lider bağlantınızı test edin.

> [!TIP]
> Müşteri adayı hedefine olan bağlantının güncelleştirildiğinden emin olun, böylece müşteri adaylarını kaybetmezsiniz.

## <a name="configure-offer-properties"></a>Teklif özelliklerini yapılandırma

Iş Ortağı Merkezi ' nde teklifinizin Özellikler sayfasında teklifiniz için geçerli olan kategorileri ve yasal sözleşmeleri tanımlayacaksınız. Bu bilgiler, yönetilen hizmetinizin çevrimiçi mağazada doğru şekilde görüntülenmesini ve doğru müşteri kümesine sunulmasını sağlar.

### <a name="select-a-category"></a>Kategori seçin

**Kategoriler**' in altında, teklifinizi uygun ticari Market arama alanlarıyla gruplandırmak için en az bir ve en fazla beş kategori seçin.

### <a name="provide-terms-and-conditions"></a>Hüküm ve koşullar sağlama

**Yasal**' ın altında, bu teklif için hüküm ve koşullarınızı belirtin. Müşteriler teklifi kullanmadan önce bunları kabul etmeleri gerekecektir. Ayrıca, hüküm ve koşullarınızın bulunabileceği URL 'YI de sağlayabilirsiniz.

Devam etmeden önce **Taslağı kaydet** ' i seçin.

## <a name="next-step"></a>Sonraki adım

* [Yönetilen hizmet teklifi listelemeyi yapılandırma](./create-managed-service-offer-listing.md)