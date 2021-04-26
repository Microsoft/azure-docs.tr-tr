---
title: Azure DDoS koruma standardı için DDoS koruması telemetrisini görüntüleyin ve yapılandırın
description: Azure DDoS koruma standardı için DDoS koruması telemetrisini görüntülemeyi ve yapılandırmayı öğrenin.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 0a04c6c58f8bfa5370a6529b81a5a85090413a2a
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107542"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>DDoS konuma telemetrisini görüntüleme ve yapılandırma

Azure DDoS koruması standardı, DDoS saldırı analiziyle ayrıntılı saldırı öngörüleri ve görselleştirmeleri sağlar. Sanal ağlarını DDoS saldırılarına karşı koruyan müşteriler, saldırı saldırılarına karşı saldırı ve risk azaltma & raporları aracılığıyla saldırının etkilerini azaltmak için gerçekleştirilen saldırı ve eylemler hakkında ayrıntılı görünürlük sağlar. Zengin telemetri, DDoS saldırısının süresi boyunca ayrıntılı ölçümler dahil olmak üzere Azure Izleyici aracılığıyla sunulur. DDos Koruması tarafından sunulan tüm Azure İzleyici ölçümleri için uyarı yapılandırılabilir. Günlüğe kaydetme, Azure Izleme tanılama arabirimi aracılığıyla [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), splunk (Azure Event Hubs), OMS Log Analytics ve gelişmiş analiz Için Azure depolama ile daha da tümleştirilebilir.

Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * DDoS koruması telemetrisini görüntüle
> * DDoS risk azaltma ilkelerini görüntüleme
> * DDoS koruması telemetrisini doğrulama ve test etme

### <a name="metrics"></a>Ölçümler

> [!NOTE]
> Azure portal **toplama** için birden çok seçenek görüntülenirken, her ölçüm için yalnızca aşağıdaki tabloda listelenen toplama türleri desteklenir. Bu karışıklıktan dolayı özür dileriz. Bu sorunu çözmek için çalışıyoruz.

Azure DDoS koruma standardı için aşağıdaki [ölçümler](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) kullanılabilir. Bu ölçümler Ayrıca Tanılama ayarları aracılığıyla dışarı aktarılabilir (bkz. [DDoS tanılama günlüğünü görüntüleme ve yapılandırma](diagnostic-logging.md)).


| Metric | Ölçüm görünen adı | Birim | Toplama Türü | Description |
| --- | --- | --- | --- | --- |
| BytesDroppedDDoS | Gelen bayt bırakıldı DDoS | BytesPerSecond | Maksimum | Gelen bayt bırakıldı DDoS| 
| BytesForwardedDDoS | İletilen gelen bayt sayısı | BytesPerSecond | Maksimum | İletilen gelen bayt sayısı |
| BytesInDDoS | Gelen bayt sayısı DDoS | BytesPerSecond | Maksimum | Gelen bayt sayısı DDoS |
| Ddobir Ggersynpackets | DDoS azaltmayı tetiklemek için gelen SYN paketleri | Sayaçpersaniye | Maksimum | DDoS azaltmayı tetiklemek için gelen SYN paketleri |
| DDO, Ggertcppackets | DDoS azaltmayı tetiklemek için gelen TCP paketleri | Sayaçpersaniye | Maksimum | DDoS azaltmayı tetiklemek için gelen TCP paketleri |
| DDO, Ggerudppaketleri | DDoS azaltmayı tetiklemek için gelen UDP paketleri | Sayaçpersaniye | Maksimum | DDoS azaltmayı tetiklemek için gelen UDP paketleri |
| Iunderddosattack | DDoS saldırısı altında | Count | Maksimum | DDoS saldırısı altında |
| PacketsDroppedDDoS | DDoS bırakılan gelen paketler | Sayaçpersaniye | Maksimum | DDoS bırakılan gelen paketler |
| PacketsForwardedDDoS | DDoS iletilen gelen paketler | Sayaçpersaniye | Maksimum | DDoS iletilen gelen paketler |
| PacketsInDDoS | Gelen paketler DDoS | Sayaçpersaniye | Maksimum | Gelen paketler DDoS |
| TCPBytesDroppedDDoS | Gelen TCP bayt bırakılan DDoS | BytesPerSecond | Maksimum | Gelen TCP bayt bırakılan DDoS |
| TCPBytesForwardedDDoS | İletilen gelen TCP baytları | BytesPerSecond | Maksimum | İletilen gelen TCP baytları |
| TCPBytesInDDoS | Gelen TCP bayt DDoS | BytesPerSecond | Maksimum | Gelen TCP bayt DDoS |
| TCPPacketsDroppedDDoS | Gelen TCP paketleri bırakıldı DDoS | Sayaçpersaniye | Maksimum | Gelen TCP paketleri bırakıldı DDoS |
| TCPPacketsForwardedDDoS | DDoS iletilen gelen TCP paketleri | Sayaçpersaniye | Maksimum | DDoS iletilen gelen TCP paketleri |
| TCPPacketsInDDoS | Gelen TCP paketleri DDoS | Sayaçpersaniye | Maksimum | Gelen TCP paketleri DDoS |
| UDPBytesDroppedDDoS | Gelen UDP bayt bırakılan DDoS | BytesPerSecond | Maksimum | Gelen UDP bayt bırakılan DDoS |
| UDPBytesForwardedDDoS | İletilen gelen UDP baytları | BytesPerSecond | Maksimum | İletilen gelen UDP baytları |
| UDPBytesInDDoS | Gelen UDP bayt sayısı DDoS | BytesPerSecond | Maksimum | Gelen UDP bayt sayısı DDoS |
| UDPPacketsDroppedDDoS | Gelen UDP paketleri bırakıldı | Sayaçpersaniye | Maksimum | Gelen UDP paketleri bırakıldı |
| UDPPacketsForwardedDDoS | DDoS iletilen gelen UDP paketleri | Sayaçpersaniye | Maksimum | DDoS iletilen gelen UDP paketleri |
| UDPPacketsInDDoS | Gelen UDP paketleri DDoS | Sayaçpersaniye | Maksimum | Gelen UDP paketleri DDoS |

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- Bu öğreticideki adımları tamamlayabilmeniz için önce bir [Azure DDoS standart koruma planı](manage-ddos-protection.md) oluşturmanız ve DDoS koruma standardının bir sanal ağ üzerinde etkinleştirilmesi gerekir.
- DDoS, bir sanal ağ içindeki kaynaklara atanan genel IP adreslerini izler. Sanal ağda genel IP adresi olan hiç kaynağınız yoksa önce genel IP adresiyle bir kaynak oluşturmanız gerekir. Azure App Service ortamları dışında, [Azure hizmetleri Için sanal ağda](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (arka uç sanal makinelerin sanal ağda bulunduğu Azure yük dengeleyiciler dahil) (klasik değil), Kaynak Yöneticisi aracılığıyla dağıtılan tüm KAYNAKLARıN genel IP adresini izleyebilirsiniz. Bu öğreticiye devam etmek için hızlı bir şekilde [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinesi oluşturabilirsiniz.  

## <a name="view-ddos-protection-telemetry"></a>DDoS koruması telemetrisini görüntüle

Bir saldırının telemetrisi Azure İzleyici aracılığıyla gerçek zamanlı olarak sağlanır. Telemetri yalnızca, bir genel IP adresi hafifletme kapsamında olduğunda kullanılabilir. 

1. [Azure Portal](https://portal.azure.com/) oturum açın ve DDoS koruma planınıza gidin.
2. **İzleme** seçeneğinin altından **Ölçümler**’i seçin.
3. **Kapsam**'ı seçin. Günlüğe kaydetmek istediğiniz genel IP adresini içeren **aboneliği** seçin, **kaynak türü** için **genel IP adresi** ' ni seçin, sonra ÖLÇÜMLERINI günlüğe kaydetmek istediğiniz belirli genel IP adresini seçin ve ardından **Uygula**' yı seçin.
4. **Toplama** türünü **en fazla** olarak seçin.

Ölçüm adları farklı paket türleri ve bayt ve paketlerin yanı sıra, her ölçüm üzerinde aşağıdaki gibi temel bir etiket adı vardır:

- **Bırakılan etiket adı** (örneğin, **gönderilen gelen paketler**): DDoS koruma sistemi tarafından bırakılan/itilen paketlerin sayısı.
- **İletilen etiket adı** (örneğin, **iletilen gelen paketler**): DDoS sistemi tarafından, filtrelenmemiş hedef VIP 'ye iletilen paketlerin sayısı.
- **Etiket adı** (örneğin, **gelen paketler DDoS**): temizleme sistemine gelen toplam paket sayısı – bırakılan ve iletilen paketlerin toplamını temsil eder.

## <a name="view-ddos-mitigation-policies"></a>DDoS risk azaltma ilkelerini görüntüleme

DDoS koruması standardı, DDoS 'ın etkinleştirildiği sanal ağdaki korumalı kaynağın her genel IP adresi için otomatik olarak ayarlanmış üç hafifletme ilkesi (TCP SYN, TCP & UDP) uygular. Aşağıdaki resimde gösterildiği gibi, **toplama** türü olan DDoS azaltma ölçümlerini ' Max ' olarak tetiklemek Için, DDoS azaltma ve **gelen UDP paketlerini** **tetiklemek üzere gelen TCP paketlerini** seçerek ilke eşiklerini görüntüleyebilirsiniz:

![Risk azaltma ilkelerini görüntüleme](./media/manage-ddos-protection/view-mitigation-policies.png)

İlke eşikleri, Azure Machine Learning tabanlı ağ trafiği profili oluşturma yöntemiyle otomatik olarak yapılandırılır. Yalnızca ilke eşiğine ulaşıldığında, saldırı altındaki IP adresi için DDoS risk azaltma meydana gelir.

## <a name="validate-and-test"></a>Doğrula ve test et

DDoS koruması telemetrisini doğrulamak için DDoS saldırılarının benzetimini yapmak için bkz. [DDoS algılamayı doğrulama](test-through-simulations.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

- DDoS koruma ölçümleri için uyarıları yapılandırma
- DDoS koruması telemetrisini görüntüle
- DDoS risk azaltma ilkelerini görüntüleme
- DDoS koruması telemetrisini doğrulama ve test etme

Saldırı risk azaltma raporlarının ve akış günlüklerinin nasıl yapılandırılacağı hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [DDoS tanılama günlüğünü görüntüleme ve yapılandırma](diagnostic-logging.md)