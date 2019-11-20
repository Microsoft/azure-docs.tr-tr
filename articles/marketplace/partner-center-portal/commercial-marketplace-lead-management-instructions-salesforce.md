---
title: Salesforce için lider yönetimini yapılandırma | Azure Marketi
description: Azure Market müşterileri için Salesforce 'ta lider yönetimini yapılandırın.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: bdd3bb50fc69083c73eb01a84bf0fb0db82a8a65
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812145"
---
# <a name="configure-lead-management-for-salesforce"></a>Salesforce için lider yönetimini yapılandırma

Bu makalede, Salesforce teklifinizin satış fırsatlarını işlemek üzere Salesforce sisteminizin nasıl ayarlanacağı açıklanır.

## <a name="set-up-your-salesforce-system"></a>Salesforce sisteminizi ayarlama

1. Salesforce 'ta oturum açın.
2. Salesforce aydınlatma deneyimini kullanıyorsanız.
    1. Salesforce ana sayfasından **Kurulum** ' u seçin.
    ![Salesforce kurulum](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Kurulum sayfasında, **platform araçları-> özellik ayarları ' na (> pazarlama-> Web 'Den müşteri adayına**kadar) kadar gezinin.
    Salesforce Web-müşteri adayı](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png) ![

3. Salesforce Classic deneyimini kullanıyorsanız:
    1. Salesforce ana sayfasından **Kurulum** ' u seçin.
    ![Salesforce klasik kurulum](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Kurulum sayfasında, **oluşturma-> özelleştirme-> müşteri adayları-> Web 'Den müşteri adayına**kadar sol gezinti aracılığıyla gezinin.
    ![Salesforce klasik Web 'den sağlama](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Kullandığınız Salesforce deneyimi ne olursa olsun, yönergelerin geri kalanı aynıdır.

4. Web 'den **müşteri adayı Kurulumu sayfasında**, **Web 'Den müşteri adayı oluştur form** düğmesini seçin.
5. **Web 'Den müşteri adayı kurulumunda**, Web 'Den yapılacak **Müşteri formu oluştur**' u seçin.
    ![Salesforce-Web 'den müşteri adayına kurulum](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. Web 'den **müşteri adayı oluştur formunda**`the Include reCAPTCHA in HTML` ayarının işaretlenmemiş olduğundan emin olun ve **Oluştur**' u seçin. 
    Salesforce ![-bir Web 'den sağlama formu oluşturun](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Size bazı HTML metinleri sunulacaktır. "OID" metnini arayın ve **OID DEĞERINI** HTML metinden (yalnızca tırnak işaretleri arasındaki metin) kopyalayın ve kaydedin. Bu değeri, yayımlama portalındaki **kuruluş tanımlayıcı** alanına yapıştırabilirsiniz.
    Salesforce ![-bir Web 'den sağlama formu oluşturun](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Seçili **tamamlandı**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Teklifinizi Salesforce 'a göndermek için teklifinizi yapılandırma

Yayımlama portalında teklifiniz için müşteri adayı yönetim bilgilerini yapılandırmaya hazırsanız, aşağıdaki adımları izleyin:

1. Teklifiniz için **teklif kurulumu** sayfasına gidin.
1. Müşteri adayı Yönetimi bölümünde **Bağlan** ' ı seçin.
    ![sağlama yönetimi-bağlantı](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Bağlantı ayrıntıları açılır penceresinde, **müşteri adayı hedefi** için **Salesforce** ' ı seçin ve daha önceki adımları **kuruluş tanımlayıcı** alanına uygulayarak oluşturduğunuz Web 'den müşteri adayı formundan `oid` yapıştırın.

1. **Kaydet**’i seçin. 

    >[!Note]
    >Teklif için müşteri adaylarını almadan önce teklifin geri kalanını yapılandırmayı ve bunu yayımlamanız gerekir.

    ![Bağlantı ayrıntıları-bir müşteri adayı hedefi seçin](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Bağlantı ayrıntıları-bir müşteri adayı hedefi seçin](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)