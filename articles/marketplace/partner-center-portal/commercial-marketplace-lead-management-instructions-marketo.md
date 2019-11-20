---
title: Marketo 'da lider yönetimini yapılandırma | Azure Marketi
description: Azure Market müşterileri için Marketo için lider yönetimini yapılandırın.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 8d13e8c3aeabf6d3fdea80ffddbae47b80adc139
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812130"
---
# <a name="configure-lead-management-in-marketo"></a>Marketo 'da lider yönetimini yapılandırma

Bu makalede, satış fırsatlarını Market teklifinizden işlemek üzere Marketo CRM sisteminizin nasıl ayarlanacağı açıklanır.

## <a name="set-up-your-marketo-crm-system"></a>Marketo CRM sisteminizi ayarlama

1. Marketo 'da oturum açın.
2. **Design Studio**' yı seçin.
    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  **Yeni form**' u seçin.
    ![Marketo yeni form](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Yeni formdaki gerekli alanları doldurup **Oluştur**' u seçin.
    ![Marketo yeni form oluştur](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  Alan ayrıntıları sayfasında **son**' u seçin.
    ![Marketo son form](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Onaylayın ve kapatın.

7. *Marketplaceleaddesteklenen* sekmesinde **kodu ekle**' yi seçin. 

    ![Kod ekleme](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo ekleme kodu, aşağıdaki örneğe benzer bir kod görüntüler.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Ekleme kodu formunda gösterilen aşağıdaki alanların değerlerini kopyalayın. Bu değerleri, bir sonraki adımda müşteri adaylarını almak üzere teklifinizi yapılandırmak için kullanacaksınız. Bir sonraki örneği, Marketo ekleme kodu örneğinde ihtiyacınız olan kimlikleri alma kılavuzu olarak kullanın.

    - Sunucu KIMLIĞI = **ys12**
    - Munchkabağı No = **123-PQR-789**
    - Form KIMLIĞI = **1179**

    **Bu değerleri anlamak için başka bir yol**

    - Sunucu KIMLIĞI, Marketo örneğinizin URL 'sinde bulunur (örneğin, "`serverID.marketo.com`").
    - "Munchbal hesap KIMLIĞI" alanında yönetici > bir menü veya Marketo REST API ana bilgisayar alt etki alanının ilk kısmından, aboneliğinizin yeniden oluşturma KIMLIĞINI alın: `https://{Munchkin ID}.mktorest.com`.
    - Form KIMLIĞI, Market 'ten gelen müşteri adaylarını yönlendirmek için adım 7 ' de oluşturduğunuz ekleme kodu formunun KIMLIĞIDIR.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Teklifinizi müşteri adaylarını Marketo 'ya gönderecek şekilde yapılandırma

Yayımlama portalında teklifiniz için müşteri adayı yönetim bilgilerini yapılandırmaya hazırsanız, aşağıdaki adımları izleyin: 

1. Teklifiniz için **teklif kurulumu** sayfasına gidin.
1. Müşteri adayı Yönetimi bölümünde **Bağlan** ' ı seçin. 

    ![Lider yönetimi-Bağlan](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Bağlantı ayrıntıları açılır penceresinde, müşteri adayı hedefi için **Marketo** ' yı seçin.

    ![Bir müşteri adayı hedefi seçin](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. **Sunucu kimliği**, **Hesap KIMLIĞI**ve **form kimliği**sağlayın.

    >[!Note]
    >Teklif için müşteri adaylarını almadan önce teklifin geri kalanını yapılandırmayı ve bunu yayımlamanız gerekir. 

