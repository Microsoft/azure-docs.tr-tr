---
title: Öğretici - etkin sanal ağları tümleştirme ve olay hub'ları güvenlik duvarlarında | Microsoft Docs
description: Bu öğreticide, Event Hubs güvenli erişim sağlamak amacıyla sanal ağları ve güvenlik duvarları ile tümleştirmeyi öğrenin.
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 11/28/2018
ms.topic: tutorial
ms.service: event-hubs
ms.custom: mvc
ms.openlocfilehash: 0f7c7e348c154aab1deb10273346a5395599b745
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605853"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Öğretici: Sanal ağ tümleştirme ve güvenlik duvarları, Event Hubs ad alanınızdaki etkinleştir

[Sanal ağ (VNet) hizmet uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md) sanal ağ özel adres alanınızı ve Azure Hizmetleri, sanal ağınızın kimliğini doğrudan bağlantı genişletir. Uç noktalar kritik Azure hizmeti kaynaklarınızı sanal ağlarınızla sınırlayarak güvenliğini sağlamanıza imkan verir. Sanal ağınızdan Azure hizmetine giden trafik her zaman Microsoft Azure omurga ağında kalır.

Güvenlik duvarları, belirli IP adresleri (veya IP adresi aralıkları), Event Hubs ad alanına erişimi sınırlamak izin ver

Bu öğreticide, sanal ağ hizmet uç noktaları tümleştirin ve Portalı'nı kullanarak var olan Azure Event Hubs ad alanınız ile güvenlik duvarları (IP Filtreleme) ayarlamak gösterilir.

Bu öğreticide, şunların nasıl yapılır:
> [!div class="checklist"]
> * Sanal ağ hizmet uç noktaları ile Event Hubs ad alanınız tümleştirmeyi öğreneceksiniz.
> * Güvenlik Duvarı (IP Filtreleme) ile Event Hubs ad alanınız ayarlama.

>[!WARNING]
> Sanal ağlar tümleştirme uygulama diğer Azure Hizmetleri, Event Hubs ile etkileşim engelleyebilirsiniz.
>
> Sanal ağların etkin olduğunda, birinci taraf entegrasyonlara desteklenmez.
> Sanal ağlar ile çalışmayan Azure senaryoları-
> * Azure tanılama ve günlüğe kaydetme
> * Azure Stream Analytics
> * Event Grid tümleştirmesi
> * Web Apps ve İşlevler bir sanal ağda olması gerekir.
> * IOT hub'ı yönlendirir
> * IOT Device Explorer


> [!IMPORTANT]
> Sanal ağlar desteklenir **standart** ve **adanmış** Event Hubs'ın katmanları. Temel katmanda desteklenmiyor.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz hesap] [] oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Mevcut bir Event Hubs ad alanı bir nedenle yararlanılacaktır bir Event Hubs ad alanı kullanılabilir olduğundan emin olun. Lütfen yoksa başvurursanız [Bu öğretici](./event-hubs-create.md)

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

İlk olarak, Git [Azure portalında][Azure portal] kullanarak Azure aboneliğinizde oturum açın.

## <a name="select-event-hubs-namespace"></a>Event Hubs ad alanını seçin

Bu öğreticinin amacı doğrultusunda bir Event Hubs ad alanı oluşturan ve olarak gider.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Güvenlik duvarları ve sanal ağlar deneyimine gitmek

Gezinti Menüsü çekme Portal'daki sol bölmede kullanın **'Güvenlik duvarları ve sanal ağlar'** seçeneği.

  ![Menüsüne gidin](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  Bu sayfa, ziyaret ettiğiniz ilk kez **tüm ağlar** radyo düğmesini seçili olmalıdır. Bu, Event Hubs ad alanı, tüm gelen bağlantıları izin verdiğini gösterir.

## <a name="add-virtual-network-service-endpoint"></a>Sanal ağ hizmet uç noktası ekleme

Erişimi sınırlamak için bu Event Hubs ad alanı için sanal ağ hizmet uç noktası tümleştirmek gerekir.

1. Tıklayın **seçili ağlar** menü seçenekleri ile sayfanın geri kalanını etkinleştirmek için sayfanın üst kısmındaki radyo düğmesi.
  ![Seçili ağlar](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Sayfa sanal ağ alt kısmında seçeneğini ***+ var olan sanal ağı Ekle***. Bu, önceden oluşturulan bir sanal ağ seçmek için izin bölmesinde slayt.
  ![Var olan sanal ağı Ekle](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. Listeden sanal ağı seçin ve alt ağ seçin.
   ![alt ağ seçin](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. Sanal ağ listeye eklemeden önce hizmet uç noktasını gerekir. Hizmet uç noktası etkin değilse, portal etkinleştirmek isteyip istemediğinizi sorar.
  ![alt ağ seçin ve uç noktayı etkinleştirme](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > Hizmet uç noktasını bulamıyorsanız, ARM şablonunu kullanarak eksik sanal ağ hizmet uç noktası göz ardı edebilirsiniz. Bu işlev, portalda kullanılamıyor.

5. Seçilen alt ağdaki hizmet uç noktası etkinleştirildikten sonra izin verilen sanal ağlar listesine eklemek için devam edebilirsiniz.
  ![uç noktası etkinleştirildikten sonra alt ağ ekleme](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. İsabet devam **Kaydet** hizmette sanal ağ yapılandırmasını kaydetmek için üst Şeritteki düğme. Lütfen onay portal bildirimlerinde gösterilecek birkaç dakika bekleyin.

## <a name="add-firewall-for-specified-ip"></a>Belirtilen IP Güvenlik Duvarı ekleme

Güvenlik duvarı kurallarını kullanarak biz sınırlı IP adresleri ya da belirli bir IP adresi Event Hubs ad alanı için erişimi sınırlayabilirsiniz.

1. Tıklayın **seçili ağlar** menü seçenekleri ile sayfanın geri kalanını etkinleştirmek için sayfanın üst kısmındaki radyo düğmesi.
  ![Seçili ağlar](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. İçinde **Güvenlik Duvarı** bölümündeki ***adres aralığı*** kılavuz, bir veya birçok belirli IP adresi veya IP adresleri aralığı ekleyebilirsiniz.
  ![IP adreslerini ekleyin](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. Birden çok IP adresi (veya IP adresi aralıkları) ekledikten sonra isabet **Kaydet** üst Şeritteki yapılandırma sunucusu tarafında kaydedildiğinden emin olun. Lütfen onay portal bildirimlerinde gösterilecek birkaç dakika bekleyin.
  ![IP adreslerini ekleyin ve Kaydet'e tıklayın](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>Geçerli IP adresiniz için güvenlik duvarı kuralları ekleme

1. Ayrıca geçerli IP adresinizi hızlı bir şekilde kontrol ederek ekleyebilirsiniz ***istemci IP adresiniz (Bilgisayarınızı geçerli IP adresi) ekleme*** onay kutusunu hemen üzerinde ***adres aralığı*** kılavuz.
  ![geçerli IP adresi ekleme](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. Geçerli IP adresiniz için güvenlik duvarı kuralları ekledikten sonra isabet **Kaydet** üst Şeritteki yapılandırma sunucusu tarafında kaydedildiğinden emin olun. Lütfen onay portal bildirimlerinde gösterilecek birkaç dakika bekleyin.
  ![geçerli IP adresini ekleyin ve Kaydet'e tıklayın](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Sonuç

Bu öğreticide, var olan bir Event Hubs ad alanı ile sanal ağ uç noktaları ve güvenlik duvarı kuralları tümleşiktir. Size nasıl modellerin için:
> [!div class="checklist"]
> * Sanal ağ hizmet uç noktaları ile Event Hubs ad alanınız tümleştirmeyi öğreneceksiniz.
> * Güvenlik Duvarı (IP Filtreleme) ile Event Hubs ad alanınız ayarlama.


[Azure portal]: https://portal.azure.com/
