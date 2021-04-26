---
title: Salesforce 'ta lider Yönetimi-Microsoft ticari Market
description: Microsoft AppSource ve Azure Marketi için müşteri adaylarını yapılandırmak üzere Salesforce kullanmayı öğrenin
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 73caf848ab5c6f8e973469066ce4612a075a52f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94489327"
---
# <a name="configure-lead-management-for-salesforce"></a>Salesforce için lider yönetimini yapılandırma

Bu makalede, Salesforce sisteminizin Microsoft AppSource ve Azure Market 'teki tekliflerinizi satış fırsatlarını işleyecek şekilde nasıl ayarlanacağı açıklanır.

> [!NOTE]
> Azure Marketi, **ülke** alanı için değerlerin bir listesi gibi önceden doldurulmuş listeleri desteklemez. Devam etmeden önce ayarlanmış hiçbir liste olmadığından emin olun. Alternatif olarak, bir [https uç noktasını](./commercial-marketplace-lead-management-instructions-https.md) veya bir [Azure tablosunu](./commercial-marketplace-lead-management-instructions-azure-table.md) , müşteri adaylarını alacak şekilde yapılandırabilirsiniz.

## <a name="set-up-your-salesforce-system"></a>Salesforce sisteminizi ayarlama

1. Salesforce 'ta oturum açın.
1. **Web 'Den sağlama** ayarlarına gidin. 
    
    Salesforce aydınlatma deneyimini kullanıyorsanız
    1. Salesforce giriş sayfasında **Kurulum** ' u seçin.

       ![Salesforce kurulumu](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. **Kurulum** sayfasında **platform araçları**  >  **özellik ayarları**  >  **Pazarlama**  >  **Web-müşteri adayı**' na gidin.

        ![Salesforce Web 'den sağlama](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Salesforce Classic deneyimini kullanıyorsanız:

    1. Salesforce giriş sayfasında **Kurulum** ' u seçin.

       ![Salesforce klasik kurulum](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. **Kurulum** sayfasında, **derleme**  >  **Özelleştir**  >  **müşteri adaylarını**  >  **Web 'den müşteri adayı**' nı seçin.

        ![Salesforce klasik Web 'den sağlama](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   Kalan adımlar, her iki Salesforce deneyimi için de aynıdır.

1. Web 'den **müşteri adayı kurulumu** sayfasında, **Web 'Den müşteri adayı oluştur form** düğmesini seçin.
1. **Web 'Den müşteri adayı kurulumunda**, Web 'Den yapılacak **Müşteri formu oluştur**' u seçin.

    ![Salesforce Web 'den müşteri adayına kurulum](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. **Web 'Den müşteri adayı oluşturma formunda**, ayarın temizlenmiş olduğundan emin olun `Include reCAPTCHA in HTML` ve **Oluştur**' u seçin.

    ![Salesforce bir Web 'den müşteri adayı form bölmesi oluşturma](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Size bazı HTML metinleri sunulacaktır. "OID" metnini arayın ve **"OID" DEĞERINI** HTML metinden (yalnızca tırnak işaretleri arasındaki metin) kopyalayın ve kaydedin. Bu değeri, yayımlama portalındaki **kuruluş tanımlayıcı** alanına yapıştırabilirsiniz.

    ![Salesforce HTML "OID" değerini gösteren bir Web-müşteri adayı formu oluştur](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. **Tamamlandı**' ı seçin.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Teklifinizi Salesforce 'a göndermek için teklifinizi yapılandırma

Yayımlama portalında teklifiniz için lider yönetimi bilgilerini yapılandırmaya hazırsanız, bu adımları izleyin.

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)' nde oturum açın.

1. Teklifinizi seçin ve **teklif kurulumu** sekmesine gidin.

1. **Müşteri müşteri adayları** bölümünde **Bağlan**' ı seçin.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="Müşteri liderleri":::

1. **Bağlantı ayrıntıları** açılır penceresinde, **müşteri adayı hedefi** için **Salesforce** ' ı seçin ve `oid` değeri **kuruluş tanımlayıcı** alanına oluşturduğunuz Web 'den müşteri adayı formundan yapıştırın.

    ![Bağlantı ayrıntıları açılır penceresi kişi e-posta kutusunu doğrula](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. **İletişim e-postası** altında, şirketinizdeki kişilerin yeni bir müşteri adayı alındığında e-posta bildirimleri alması gereken e-posta adreslerini girin. Birden çok e-postayı noktalı virgülle ayırarak sağlayabilirsiniz.

1. **Tamam**’ı seçin.

Bir müşteri adayı hedefine başarıyla bağlandığınızdan emin olmak için **Doğrula**' yı seçin. Başarılı olursa, müşteri adayı hedefinde bir test liderine sahip olacaksınız.

>[!NOTE]
>Teklif için müşteri adaylarını almadan önce teklifin geri kalanını yapılandırmayı ve bunu yayımlamanız gerekir.
