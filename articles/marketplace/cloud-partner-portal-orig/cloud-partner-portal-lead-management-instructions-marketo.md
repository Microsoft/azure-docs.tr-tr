---
title: Marketo 'da lider yönetimini yapılandırma | Azure Marketi
description: Azure Market müşterileri için Marketo için lider yönetimini yapılandırın.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 7949507c8c7ef57cded25cde8579c1945aa93a81
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825209"
---
# <a name="configure-lead-management-in-marketo"></a>Marketo 'da lider yönetimini yapılandırma

Bu makalede, Microsoft satış fırsatlarını işlemek üzere Marketo 'nın nasıl ayarlanacağı açıklanır.

1. Marketo 'da oturum açın.
2. **Design Studio**' yı seçin.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  **Yeni form**' u seçin.
    ![Marketo yeni form](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Yeni formdaki gerekli alanları doldurup **Oluştur**' u seçin.
    ![Marketo yeni form oluştur](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  Alan ayrıntıları sayfasında **son**' u seçin.
    ![Marketo son form](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Onaylayın ve kapatın.

6.  Marketplaceleaddesteklenen sekmesinde **kodu ekle**' yi seçin.
    ![Marketo ekleme kodu seçeneği](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Marketo ekleme kodu, aşağıdaki örneğe benzer bir kod görüntüler.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Ekleme kodunda gösterilen değerleri kopyalayın, bu sayede **sunucu kimliği**, bir **kimlik kimliği**ve **form kimliğini** bulut iş ortağı portalı Marketo alanlarında yapılandırabilirsiniz.

Bir sonraki örneği, Marketo ekleme kodu örneğinde ihtiyacınız olan kimlikleri alma kılavuzu olarak kullanın.

- Sunucu kimliği = **ys12**
- Munchkabağı No = **123-PQR-789**
- Form kimliği = **1179**\
