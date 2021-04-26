---
title: Açık kaynaklı araçlarla ağ trafiği düzenlerini görselleştirin
titleSuffix: Azure Network Watcher
description: Bu sayfada, sanal makinelerinizden ve sanal makinelerinizden trafik desenlerini görselleştirmek için Capanalysis ile ağ Izleyicisi paket yakalamanın nasıl kullanılacağı açıklanır.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 0503f6910186b42bf381c662c7942e37c28bfdf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709521"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Açık kaynaklı araçlar kullanarak sanal makinelerinizden gelen ve giden ağ trafiği düzenlerini görselleştirin

Paket yakalamaları, ağ oluşturma ve derin paket incelemesi gerçekleştirmenize olanak tanıyan ağ verileri içerir. Ağınız hakkında Öngörüler kazanmak için paket yakalamalarını çözümlemek üzere kullanabileceğiniz birçok açık kaynak aracı vardır. Bu tür bir araç, açık kaynaklı bir paket yakalama görselleştirme aracı olan CapAnalysis. Paket yakalama verilerinin görselleştirilmesi, ağınız içindeki desenler ve bozukluklar hakkında hızlı bir şekilde Öngörüler elde etmenin değerli bir yoludur. Görselleştirmeler Ayrıca, bu tür öngörüleri kolayca tüketilebilir şekilde paylaşmak için bir yol sağlar.

Azure 'un ağ Izleyicisi, ağınızda paket yakalamaları gerçekleştirmenize izin vererek verileri yakalama olanağı sağlar. Bu makalede, ağ Izleyicisi ile CapAnalysis kullanarak paket yakalamalarından nasıl görselleştirme ve öngörü elde edilecek hakkında bir adım adım sunulmaktadır.

## <a name="scenario"></a>Senaryo

Azure 'daki bir VM üzerinde dağıtılan basit bir Web uygulamanız varsa, akış düzenlerini ve olası anormallikleri hızlıca tanımlamak üzere ağ trafiğini görselleştirmek için açık kaynak araçları kullanmak istiyorsunuz. Ağ izleyicisinden, ağ ortamınızın paket yakalamasını elde edebilir ve depolama hesabınızda doğrudan depolarsınız. CapAnalysis daha sonra paket yakalamayı doğrudan depolama blobundan alabilir ve içeriğini görselleştirin.

![senaryo][1]

## <a name="steps"></a>Adımlar

### <a name="install-capanalysis"></a>CapAnalysis 'i yükler

Bir sanal makineye CapAnalysis yüklemek için buradaki resmi yönergelere bakabilirsiniz https://www.capanalysis.net/ca/how-to-install-capanalysis .
Uzaktan erişim CapAnalysis için, yeni bir gelen güvenlik kuralı ekleyerek sanal makinenizde 9877 numaralı bağlantı noktasını açmanız gerekir. Ağ güvenlik gruplarında kural oluşturma hakkında daha fazla bilgi için, [mevcut BIR NSG 'de kurallar oluşturma](../virtual-network/manage-network-security-group.md#create-a-security-rule)bölümüne bakın. Kural başarıyla eklendikten sonra, CapAnalysis adresinden erişim sağlayabilmelisiniz `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Paket yakalama oturumu başlatmak için Azure ağ Izleyicisi 'ni kullanma

Ağ Izleyicisi, bir sanal makinede trafiği izlemek için paket yakalamanızı sağlar. Paket yakalama oturumu başlatmak için [ağ izleyiciyle paket yakalamalarını yönetme](network-watcher-packet-capture-manage-portal.md) bölümündeki yönergelere başvurabilirsiniz. Paket yakalama, CapAnalysis tarafından erişilecek bir depolama blobuna depolanabilir.

### <a name="upload-a-packet-capture-to-capanalysis"></a>CapAnalysis 'e bir paket yakalama yükleme
"URL 'den Içeri aktar" sekmesini kullanarak Ağ İzleyicisi tarafından alınan bir paket yakalamayı doğrudan karşıya yükleyebilir ve paket yakalamanın depolandığı depolama blobuna bir bağlantı sağlayabilirsiniz.

CapAnalysis için bir bağlantı sağlarken, Depolama Blobu URL 'sine bir SAS belirteci eklemediğinizden emin olun.  Bunu yapmak için depolama hesabından paylaşılan erişim imzası ' na gidin, izin verilen izinleri belirleyin ve bir belirteç oluşturmak için SAS Oluştur düğmesine basın. Daha sonra, SAS belirtecini paket yakalama Depolama Blobu URL 'sine ekleyebilirsiniz.

Elde edilen URL aşağıdaki URL 'ye benzer bir şekilde görünür: `http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere`


### <a name="analyzing-packet-captures"></a>Paket yakalamaları çözümleniyor

CapAnalysis, her biri farklı bir perspektiften analiz sağlayan, paket yakalamanızı görselleştirmek için çeşitli seçenekler sunar. Bu görsel özetler sayesinde, ağ trafiği eğilimlerini anlayabilir ve alışılmadık etkinlikleri hızlı bir şekilde belirleyebilirsiniz. Aşağıdaki listede bu özelliklerden birkaçı gösterilmektedir:

1. Akış tabloları

    Bu tablo, paket verileri, akışlarla ilişkili zaman damgası ve Flow ile ilişkili çeşitli protokoller ve kaynak ve hedef IP ile ilgili akış listesi sağlar.

    ![capanalysis Flow sayfası][5]

1. Protokole Genel Bakış

    Bu bölme, çeşitli protokoller ve coğrafi bölgelerde ağ trafiğinin dağıtımını hızlı bir şekilde görmenizi sağlar.

    ![capanalysis protokolüne genel bakış][6]

1. İstatistikler

    Bu bölme, kaynak ve hedef IP 'lerden gönderilen ve alınan baytlar, kaynak ve hedef IP 'lerin her biri için akışlar, çeşitli akışlar için kullanılan protokol ve akış süresi gibi ağ trafiği istatistiklerini görüntülemenize olanak sağlar.

    ![capanalysis istatistikleri][7]

1. Coğrafi Haritayı görmek

    Bu bölme, her bir ülke/bölgeden gelen trafik hacmine renk ölçeklendirerek ağ trafiğinizin bir harita görünümünü sağlar. Bu ülkenin/bölgedeki IP 'lerden gönderilen ve alınan verilerin oranı gibi ek akış istatistiklerini görüntülemek için vurgulanan ülkeleri/bölgeleri seçebilirsiniz.

    ![coğrafi Haritayı görmek][8]

1. Filtreler

    CapAnalysis, belirli paketlerin hızlı analizine yönelik bir filtre kümesi sağlar. Örneğin, bu trafik alt kümesiyle ilgili Öngörüler elde etmek için verileri protokole göre filtrelemeyi seçebilirsiniz.

    ![filtreler][11]

    [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about)Tüm CapAnalysis ' özellikleri hakkında daha fazla bilgi için ziyaret edin.

## <a name="conclusion"></a>Sonuç

Ağ Izleyicisi 'nin paket yakalama özelliği, ağ ve ağ trafiğinizi daha iyi anlamak için gereken verileri yakalamanızı sağlar. Bu senaryoda, ağ izleyicisinden paket yakalamalarından kolayca açık kaynaklı görselleştirme araçlarıyla nasıl tümleştirilebileceğine gösterildik. Paket yakalamalarını görselleştirmek için CapAnalysis gibi açık kaynaklı araçları kullanarak, derin paket incelemesi gerçekleştirebilir ve ağ trafiğiniz içindeki eğilimleri hızlıca belirleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

NSG akış günlükleri hakkında daha fazla bilgi edinmek için [NSG akış günlüklerini](network-watcher-nsg-flow-logging-overview.md) ziyaret edin

Power BI ile NSG akış günlüklerinizi görselleştirmeyi öğrenin [Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
