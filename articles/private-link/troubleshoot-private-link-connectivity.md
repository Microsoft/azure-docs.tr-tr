---
title: Azure Özel Bağlantı hizmeti bağlantı sorunlarını giderme
description: Özel bağlantı bağlantısını tanılamaya yönelik adım adım yönergeler
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 45a7a146dd929408b50a0045fe2598726ee05505
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544318"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Azure Özel Bağlantı hizmeti bağlantı sorunlarını giderme

Bu makalede, Azure özel bağlantı kuruluma yönelik bağlantıyı doğrulamak ve tanılamak için adım adım yönergeler sağlanmaktadır.

Azure özel bağlantısı ile Azure depolama, Azure Cosmos DB ve Azure SQL veritabanı gibi Azure platform hizmeti (PaaS) hizmetlerine ve Azure 'da barındırılan müşteri veya iş ortağı hizmetlerine sanal ağınızdaki özel bir uç nokta üzerinden erişebilirsiniz. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağı üzerinden geçer ve bu da genel İnternet 'ten etkilenme olasılığını ortadan kaldırır. Ayrıca, kendi özel bağlantı hizmetinizi sanal ağınızda oluşturup müşterilerinize özel olarak iletebilirsiniz.

Özel bağlantı erişimi için Azure Load Balancer standart katmanının arkasında çalışan hizmetinize izin verebilirsiniz. Hizmetinizin tüketicileri, sanal ağı içinde özel bir uç nokta oluşturabilir ve bu hizmete özel olarak erişmek için bu hizmetle eşlenir.

Özel bağlantıyla kullanılabilen bağlantı senaryoları şunlardır:

- Aynı bölgedeki sanal ağ
- Region, eşlenen sanal ağlar
- Genel eşlenmiş sanal ağlar
- VPN veya Azure ExpressRoute devreleri üzerinden şirket içi müşteri

## <a name="deployment-troubleshooting"></a>Dağıtım sorunlarını giderme

Özel bağlantı hizmetiniz için tercih ettiğiniz alt ağdan kaynak IP adresini seçmemekte olduğunuz, sorun giderme durumları için [özel bağlantı hizmetinde ağ Ilkelerini devre dışı bırakma](./disable-private-link-service-network-policy.md) hakkındaki bilgileri gözden geçirin.

Kaynak IP adresini seçtiğiniz alt ağ için **Privatelinkservicenetworkpolicies** ayarının devre dışı bırakıldığından emin olun.

## <a name="diagnose-connectivity-problems"></a>Bağlantı sorunlarını Tanıla

Özel bağlantı kurulumlarınızla bağlantı sorunlarıyla karşılaşırsanız, tüm olağan yapılandırmaların beklendiğinden emin olmak için bu adımları gözden geçirin.

1. Kaynağa göz atarak özel bağlantı yapılandırmasını gözden geçirin.

    a. **Özel bağlantı merkezine** gidin.

      ![Özel bağlantı merkezi](./media/private-link-tsg/private-link-center.png)

    b. Sol bölmede **özel bağlantı Hizmetleri**' ni seçin.

      ![Özel bağlantı Hizmetleri](./media/private-link-tsg/private-link-service.png)

    c. Tanımak istediğiniz özel bağlantı hizmetini filtreleyin ve seçin.

    d. Özel uç nokta bağlantılarını gözden geçirin.
     - Bağlantı Aradığınız özel uç noktanın **onaylanmış** bir bağlantı durumuyla listelendiğinden emin olun.
     - Durum **Beklemede** ise, seçin ve onaylayın.

       ![Özel uç nokta bağlantıları](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Adı seçerek bağlanmakta olduğunuz özel uç noktaya gidin. Bağlantı durumunun **Onaylandı** olarak göründüğünden emin olun.

       ![Özel uç nokta bağlantısına genel bakış](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Her iki taraf onaylandıktan sonra bağlantıyı yeniden deneyin.

    e. **Özellikler** sekmesindeki **genel bakış** sekmesinde ve **kaynak kimliğinde** **diğer adı** inceleyin.
     - **Diğer ad** ve **kaynak kimliği** bilgilerinin, bu hizmete özel bir uç nokta oluşturmak Için kullanmakta olduğunuz **diğer ad** ve **kaynak kimliğiyle** eşleştiğinden emin olun.

       ![Diğer ad bilgilerini doğrulama](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Kaynak KIMLIĞI bilgilerini doğrulama](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. **Genel bakış** sekmesinde **görünürlük** bilgilerini gözden geçirin.
     - Aboneliğinizin **görünürlük** kapsamında olduğundan emin olun.

       ![Görünürlük bilgilerini doğrulama](./media/private-link-tsg/pls-overview-pane-visibility.png)

    örneğin: **Genel bakış** sekmesinde **yük dengeleyici** bilgilerini gözden geçirin.
     - Yük dengeleyici bağlantısını seçerek yük dengeleyiciye gidebilirsiniz.

       ![Yük dengeleyici bilgilerini doğrulama](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Yük dengeleyici ayarlarının beklentileriniz uyarınca yapılandırıldığından emin olun.
       - **Ön uç IP yapılandırmasını** gözden geçirin.
       - **Arka uç havuzlarını** inceleyin.
       - **Yük Dengeleme kurallarını** gözden geçirin.

       ![Yük dengeleyici özelliklerini doğrulama](./media/private-link-tsg/pls-ilb-properties.png)

     - Yük dengeleyicinin önceki ayarlara göre çalıştığından emin olun.
       - Yük dengeleyici arka uç havuzunun kullanılabildiği alt ağ dışında herhangi bir alt ağda bir VM seçin.
       - Yük dengeleyici ön ucuna önceki sanal makineden erişmeyi deneyin.
       - Bağlantı, Yük Dengeleme kurallarına göre onu arka uç havuzuna yapıyorsa, yük dengeleyici çalışır.
       - Ayrıca, yük dengeleyici aracılığıyla verilerin akan olup olmadığını görmek için Azure Izleyici aracılığıyla yük dengeleyici ölçümünü gözden geçirebilirsiniz.

1. Verilerin akan olup olmadığını görmek için [Azure izleyici](../azure-monitor/overview.md) 'yi kullanın.

    a. Özel bağlantı hizmeti kaynağında **ölçümler**' i seçin.
     - **Bayt cinsinden** veya **giden** baytları seçin.
     - Özel bağlantı hizmetine bağlanmaya çalıştığınızda verilerin akışa alındığını görün. Yaklaşık 10 dakikalık bir gecikme süresi bekler.

       ![Özel bağlantı hizmeti ölçümlerini doğrulama](./media/private-link-tsg/pls-metrics.png)

1. Sorununuz hala çözülmedi ve bir bağlantı sorunu hala varsa [Azure destek](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ekibine başvurun.

## <a name="next-steps"></a>Sonraki adımlar

 * [Özel bağlantı hizmeti (CLı) oluşturma](./create-private-link-service-cli.md)
 * [Azure özel uç nokta sorun giderme kılavuzu](troubleshoot-private-endpoint-connectivity.md)