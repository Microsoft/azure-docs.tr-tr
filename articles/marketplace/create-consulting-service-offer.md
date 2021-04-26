---
title: Microsoft ticari marketi 'nde danışmanlık hizmeti teklifi oluşturma
description: Microsoft Iş Ortağı Merkezi 'nde ticari Market programını kullanarak Microsoft AppSource veya Azure Marketi için yeni bir danışmanlık hizmeti teklifi oluşturmayı öğrenin.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754572"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>Ticari Market 'te danışmanlık hizmeti teklifi oluşturma

Bu makalede, Iş Ortağı Merkezi kullanılarak Microsoft ticari Market için danışmanlık hizmeti teklifinin nasıl oluşturulacağı açıklanmaktadır. 

## <a name="before-you-begin"></a>Başlamadan önce

Bir danışmanlık hizmeti teklifi yayımlamak için, alanındaki uzmanlığı göstermek üzere belirli uygunluk gereksinimlerini karşılamanız gerekir. Daha önce yapmadıysanız, [ticari Market için bir danışmanlık hizmeti teklifi planı](./plan-consulting-service-offer.md)makalesini okuyun. Bir danışmanlık hizmeti için önkoşulları ve teklifi Iş Ortağı Merkezi ile oluşturduğunuzda ihtiyacınız olan varlıkları açıklar.

## <a name="create-a-new-consulting-service-offer"></a>Yeni bir danışmanlık hizmeti teklifi oluşturun

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)' nde oturum açın.
2.  Sol gezinti menüsünde **ticari Market**  >  **genel bakış**' ı seçin.
3.  Genel Bakış sekmesinde **+ yeni teklif**  >  **danışmanlık hizmeti**' ni seçin.

    ![Sol gezinti menüsünü gösterir.](./media/new-offer-consulting-service.png)

4. **Yeni teklif** iletişim kutusunda BIR **teklif kimliği** girin. Bu KIMLIK, ticari Market listesinin URL 'sinde görülebilir. Örneğin, bu kutuya test-teklif-1 girerseniz, teklif Web adresi olacaktır `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Hesabınızdaki her teklifin benzersiz bir teklif KIMLIĞI olmalıdır.
    * Yalnızca küçük harfleri ve rakamları kullanın. Teklif KIMLIĞI, kısa çizgi ve alt çizgi içerebilir, ancak boşluk içeremez ve 50 karakterle sınırlıdır.
    * Teklif KIMLIĞI, **Oluştur**' u seçtikten sonra değiştirilemez.

5. Bir **teklif diğer adı** girin. Bu, Iş Ortağı Merkezi 'nde teklif için kullanılan addır. Çevrimiçi mağazalarda görünmez ve müşteriler tarafından gösterilen teklif adından farklıdır.
6. Teklifi oluşturmak ve devam etmek için **Oluştur**' u seçin.

## <a name="configure-lead-management"></a>Müşteri adayı yönetimini yapılandırma

Müşteri ilişkileri yönetimi (CRM) sisteminizi ticari Market teklifinizi kullanarak bağlayın. böylece müşteri, danışmanlık hizmetinize ilgi ifade edildiğinde müşteri iletişim bilgilerini alabilirsiniz. Bu bağlantıyı, teklifi oluştururken veya sonrasında istediğiniz zaman değiştirebilirsiniz. Ayrıntılı kılavuz için bkz. [ticari Market teklifinizin müşteri adayları](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Iş Ortağı Merkezi 'nde lider yönetimini yapılandırmak için:

1.  Iş Ortağı Merkezi 'nde **teklif kurulumu** sekmesine gidin.
2.  **Müşteri liderleri** altında **Bağlan** bağlantısını seçin.
3.  **Bağlantı ayrıntıları** iletişim kutusunda listeden bir müşteri adayı hedefi seçin.
4.  Görüntülenen alanları doldurun. Ayrıntılı adımlar için aşağıdaki makalelere bakın:

    * [Teklifinizi Azure tablosuna müşteri adayları gönderecek şekilde yapılandırma](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Teklifinizi dynamics 365 müşteri katılımını](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (eski ADıYLA Dynamics CRM Online) göndermek Için uygulamanızı yapılandırın
    * [Teklifinizi, HTTPS uç noktasına müşteri adayları gönderecek şekilde yapılandırma](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Teklifinizi müşteri adaylarını Marketo 'ya gönderecek şekilde yapılandırma](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Teklifinizi Salesforce 'a göndermek için teklifinizi yapılandırma](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  Verdiğiniz yapılandırmayı doğrulamak için **doğrula bağlantısını** seçin.
6.  Bağlantı ayrıntılarını yapılandırdığınızda, **Bağlan**' ı seçin.
7.  **Taslağı kaydet**'i seçin.

Teklifinizi Iş Ortağı Merkezi 'ne gönderdikten sonra, bağlantıyı doğrulayacağız ve size bir test lideri göndereceğiz. Teklifi canlı olmadan önce önizlerken, teklifi önizleme ortamında satın almaya çalışırken lider bağlantınızı test edin.

> [!TIP]
> Müşteri adayı hedefine olan bağlantının güncelleştirildiğinden emin olun, böylece müşteri adaylarını kaybetmezsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Danışmanlık hizmeti teklif özelliklerinizi yapılandırma](./create-consulting-service-offer-properties.md)