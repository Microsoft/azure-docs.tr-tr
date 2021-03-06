---
title: BT Hizmet Yönetimi Bağlayıcısı-Azure Izleyici 'de güvenli dışarı aktarma-BMC ile yapılandırma
description: Bu makalede, Azure Izleyici 'de güvenli dışarı aktarma sırasında ıTSM ürünlerinizi/hizmetlerinizin BMC ile nasıl bağlanacağı gösterilmektedir.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7c50d533f95e1131e26ddd808c77023ae4591a26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041714"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>BMC Helix 'i Azure Izleyici 'ye bağlama

Aşağıdaki bölümlerde, BMC Helix ürününüzü bağlama ve Azure 'da güvenli dışarı aktarma konularında ayrıntılar sağlanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki önkoşulları karşılatığınızdan emin olun:

* Azure AD kaydedilir.
* Desteklenen BMC Helix Multi-Cloud Service Management sürümünüz (sürüm 19,08 veya üzeri) vardır.

## <a name="configure-the-bmc-helix-connection"></a>BMC Helix bağlantısını yapılandırma

1. Güvenli dışarı aktarma için URI 'yi almak üzere BMC Helix ortamında aşağıdaki yordamı kullanın:

   1. Integration Studio 'da oturum açın.
   1. **Azure uyarıları akışından olay oluştur** ' a yönelik arama yapın.
   1. Web kancası URL 'sini kopyalayın.
   
   ![Integration Studio 'daki Web kancası U R L 'nin ekran görüntüsü.](media/itsmc-secure-webhook-connections-bmc/bmc-url.png)
   
2. Sürüme göre yönergeleri izleyin:
   * [Sürüm 20,02 Için Azure izleyici ile önceden oluşturulmuş tümleştirmeyi etkinleştirme](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * [Sürüm 19,11 Için Azure izleyici ile önceden oluşturulmuş tümleştirmeyi etkinleştirme](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

3. BMC Helix 'teki bağlantının yapılandırmasının bir parçası olarak, tümleştirme BMC örneğine gidin ve şu yönergeleri izleyin:

   1. **Katalog**' u seçin.
   2. **Azure uyarıları**' nı seçin.
   3. **Bağlayıcılar**' ı seçin.
   4. **Yapılandırma**' yı seçin.
   5. **Yeni bağlantı yapılandırması Ekle** ' yi seçin.
   6. Yapılandırma bölümüne ilişkin bilgileri girin:
      - **Ad**: kendinizinkini oluşturun.
      - **Yetkilendirme türü**: **yok**
      - **Açıklama**: kendinizinkini oluşturun.
      - **Site**: **bulut**
      - **Örnek sayısı**: **2**, varsayılan değer.
      - **Denetle**: kullanımı etkinleştirmek için varsayılan olarak seçilidir.
      - Azure kiracı KIMLIĞI ve Azure uygulama KIMLIĞI, daha önce tanımladığınız uygulamadan alınır.

![BMC yapılandırmasını gösteren ekran görüntüsü.](media/itsmc-secure-webhook-connections-bmc/bmc-configuration.png)
