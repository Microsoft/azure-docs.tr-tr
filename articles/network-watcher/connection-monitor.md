---
title: Öğretici-Azure portal kullanarak ağ iletişimini Izleme
description: Bu öğreticide, Azure ağ Izleyicisi 'nin bağlantı izleyici özelliği ile iki sanal makine arasındaki ağ iletişimini izlemeyi öğrenin.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 4f9f3e2af9abc054d3b1d5ce2bd1f1b27f7bf958
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066050"
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>Öğretici: Azure portalını kullanarak iki sanal makine arasındaki ağ iletişimini izleme

> [!NOTE]
> Bu öğretici bağlantı Izleyicisini (klasik) kapsar. Gelişmiş bağlantı izleme deneyimi için yeni ve geliştirilmiş [bağlantı izleyicisini](connection-monitor-overview.md) deneyin

> [!IMPORTANT]
> 1 Temmuz 2021 tarihinden itibaren bağlantı Izleyicisine (klasik) yeni bağlantı izleyicileri ekleyemeyeceksiniz, ancak 1 2021 Temmuz 'dan önce oluşturulan mevcut bağlantı izleyicilerini kullanmaya devam edebilirsiniz. Geçerli iş yüklerinizde hizmet kesintisini en aza indirmek için, [bağlantı izleyicisinden (klasik)](migrate-to-connection-monitor-from-connection-monitor-classic.md) Azure Ağ İzleyicisi 'Nde 29 Şubat 2024 ' den önceki yeni bağlantı izleyicisine geçiş yapın.

Bir sanal makine (VM) ve başka bir sanal makine gibi bir uç nokta arasındaki iletişimin başarılı olması, kuruluşunuz için kritik olabilir. Bazen iletişimi kesebilecek yapılandırma değişiklikleri olur. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * İki sanal makine oluşturma
> * Ağ İzleyicisi’nin bağlantı izleme özelliği ile sanal makineler arasındaki iletişimi izleme
> * Bağlantı İzleyicisi ölçümlerine göre uyarı oluşturma
> * İki sanal makine arasındaki bir iletişim sorununu tanılama ve nasıl çözümleyebileceğinizi öğrenme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.



## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-vms"></a>VM oluşturma

İki sanal makine oluşturma.

### <a name="create-the-first-vm"></a>Birinci sanal makineyi oluşturma

1. Azure portalının sol üst köşesinde bulunan **+ Kaynak oluştur** seçeneğini belirleyin.
2. **İşlem** seçeneğini belirleyin ve bir işletim sistemi seçin. Bu öğreticide **Windows Server 2016 Datacenter** kullanılmaktadır.
3. Aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Tamam**’ı seçin:

    |Ayar|Değer|
    |---|---|
    |Ad|myVm1|
    |Kullanıcı adı| Seçtiğiniz bir kullanıcı adını girin.|
    |Parola| Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.|
    |Abonelik| Aboneliğinizi seçin.|
    |Kaynak grubu| **Yeni oluştur**’u seçin ve **myResourceGroup** değerini girin.|
    |Konum| **Doğu ABD** seçin|

4. Sanal makine için bir boyut seçin ve **Seç** seçeneğini belirleyin.
5. **Ayarlar** bölümünde **Uzantılar**’ı seçin. Aşağıdaki resimde gösterildiği gibi **Uzantı ekle**’yi ve **Windows için Ağ İzleyicisi Aracısı**’nı seçin:

    ![Ağ İzleyicisi aracısı uzantısı](./media/connection-monitor/nw-agent-extension.png)

6. **Windows için Ağ İzleyicisi Aracısı** bölümünde **Oluştur**’u seçin, **Uzantıyı yükle** bölümünde **Tamam**’ı seçin ve sonra **Uzantılar** bölümünde **Tamam**’ı seçin.
7. Diğer **Ayarlar** için varsayılan değerleri kabul edin ve **Tamam**’a tıklayın.
8. **Özet**’in **Oluştur** bölümünde **Oluştur**’u seçerek sanal makine dağıtımını başlatın.

### <a name="create-the-second-vm"></a>İkinci sanal makineyi oluşturma

[İlk sanal makineyi oluşturma](#create-the-first-vm) bölümündeki adımları, aşağıdaki değişikliklerle tekrar tamamlayın:

|Adım|Ayar|Değer|
|---|---|---|
| 1 | **Ubuntu Server** sürümü seçin |                                                                         |
| 3 | Name                                  | myVm2                                                                   |
| 3 | Kimlik doğrulaması türü                   | SSH genel anahtarınızı yapıştırın veya **Parola**’yı seçin bir parola girin. |
| 3 | Kaynak grubu                        | **Mevcut olanı kullan**’ı seçin ve **myResourceGroup** seçeneğini belirleyin.                 |
| 6 | Uzantıları                            | **Linux için ağ Izleyicisi Aracısı**                                             |

Sanal makinenin dağıtılması birkaç dakika sürer. Kalan adımlara devam etmeden önce sanal makinenin dağıtımı tamamlamasını bekleyin.

## <a name="create-a-connection-monitor"></a>Bağlantı izleyicisi oluşturma

*myVm1*’den *myVm2*’ye 22 numaralı TCP bağlantı noktası üzerinden iletişimi izlemek için bir bağlantı izleyicisi oluşturun.

1. Portalın sol tarafından **Tüm hizmetler**’i seçin.
2. **Filtre** kutusuna *ağ izleyicisi* yazmaya başlayın. **Ağ İzleyicisi**, arama sonuçlarında görüntülendiğinde seçin.
3. **İZLEME** bölümünde **Bağlantı izleyicisi**’ni seçin.
4. **+ Ekle**'yi seçin.
5. İzlemek istediğiniz bağlantı için bilgileri girin veya seçin ve sonra **Ekle**’yi seçin. Aşağıdaki resimde gösterilen örnekte izlenen bağlantı, 22 numaralı bağlantı noktası üzerinden *myVm1* sanal makinesinden *myVm2* sanal makinesine doğrudur:

    | Ayar                  | Değer               |
    | ---------                | ---------           |
    | Ad                     | myVm1-myVm2(22)     |
    | Kaynak                   |                     |
    | Sanal makine          | myVm1               |
    | Hedef              |                     |
    | Sanal makine seçme |                     |
    | Sanal makine          | myVm2               |
    | Bağlantı noktası                     | 22                  |

    ![Bağlantı İzleyicisi ekleme](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>Bağlantı izleyicisini görüntüleme

1. Bağlantı izlemeyi görüntülemek için [Bağlantı izleyicisi oluşturma](#create-a-connection-monitor) bölümündeki 1.-3. adımları tamamlayın. Aşağıdaki resimde gösterildiği gibi mevcut bağlantı izleyicilerinin bir listesini görürsünüz:

    ![Bağlantı izleyicileri](./media/connection-monitor/connection-monitors.png)

2. Aşağıdaki resimde gösterildiği gibi izleyiciye ilişkin ayrıntıları görmek için, önceki resimde gösterildiği gibi **myVm1-myVm2(22)** adlı izleyiciyi seçin:

    ![İzleyici ayrıntıları](./media/connection-monitor/vm-monitor.png)

    Aşağıdaki bilgileri not edin:

    | Öğe                     | Değer                      | Ayrıntılar                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Durum                   | Erişilebilir                  | Uç noktanın erişilebilir olup olmadığını bilmenizi sağlar.|
    | AVG. ROUND-TRIP          | Bağlantıyı kurmak için milisaniye cinsinden gidiş dönüş süresini bilmenizi sağlar. Bağlantı izleyicisi 60 saniyede bir bağlantıyı araştırır; böylece zaman içindeki gecikmeyi izleyebilirsiniz.                                         |
    | Atlamalar                     | Bağlantı izleyicisi, iki uç nokta arasındaki atlamaları bilmenizi sağlar. Bu örnekte bağlantı, aynı sanal ağ üzerindeki iki sanal makine arasındadır, bu nedenle 10.0.0.5 IP adresine yalnızca bir atlama vardır. Örneğin, mevcut bir sistem veya özel rota, VPN ağ geçidi ya da ağ sanal gereci aracılığıyla sanal makineler arasında trafiği yönlendiriyorsa, ek atlamalar listelenir.                                                                                                                         |
    | DURUM                   | Her uç nokta için yeşil onay işareti, her uç noktanın iyi durumda olduğunu bilmenizi sağlar.    ||

## <a name="generate-alerts"></a>Uyarı oluşturma

Uyarılar, Azure İzleyici'deki uyarı kuralları tarafından oluşturulur ve kaydedilmiş sorguları veya özel günlük aramalarını düzenli aralıklarla otomatik olarak çalıştırabilir. Oluşturulan uyarı, başka birini bilgilendirmek veya başka bir işlem başlatmak gibi bir veya daha fazla eylemi otomatik olarak çalıştırabilir. Uyarı kuralı ayarlarken kullanabileceğiniz ölçümlerin listesi hedeflediğiniz kaynağa göre belirlenir.

1. Azure portalda **İzleyici** hizmetini ve ardından **Uyarılar** > **Yeni uyarı kuralı**'nı seçin.
2. **Hedef seçin**'e tıklayıp hedeflemek istediğiniz kaynakları seçin. **Abonelik** öğesini seçin ve **Kaynak türü**'nü kullanmak istediğiniz Bağlantı İzleyicisine göre ayarlayın.

    ![hedef seçili uyarı ekranı](./media/connection-monitor/set-alert-rule.png)
1. Hedeflemek üzere bir kaynak seçtikten sonra **Ölçüt ekle**'yi seçin. Ağ İzleyicisi, [uyarı oluşturabileceğiniz ölçümlere sahiptir](../azure-monitor/alerts/alerts-metric-near-real-time.md#metrics-and-dimensions-supported). **Kullanılabilir sinyaller** bölümünü ProbesFailedPercent ve AverageRoundtripMs ölçümleri olarak ayarlayın:

    ![sinyaller seçili uyarı sayfası](./media/connection-monitor/set-alert-signals.png)
1. Uyarı kuralı adı, açıklaması ve önem derecesi gibi uyarı ayrıntılarını girin. Uyarı yanıtını otomatikleştirmek ve özelleştirmek için uyarıya bir eylem grubu da ekleyebilirsiniz.

## <a name="view-a-problem"></a>Sorunu görüntüleme

Varsayılan olarak Azure, aynı sanal ağ üzerindeki sanal makineler arasında tüm bağlantı noktaları üzerinden iletişime izin verir. Zamanla siz veya kuruluşunuzdaki birisi, Azure’ın varsayılan kurallarını geçersiz kılarak yanlışlıkla bir iletişim hatasına neden olabilir. Aşağıdaki adımları tamamlayarak bir iletişim sorunu oluşturun ve sonra bağlantı izleyicisini yeniden görüntüleyin:

1. Portalın üst kısmındaki arama kutusuna *myResourceGroup* değerini girin. **myResourceGroup** kaynak grubu, arama sonuçlarında görüntülendiğinde bu kaynak grubunu seçin.
2. **myVm2-nsg** ağ güvenlik grubunu seçin.
3. Aşağıdaki resimde gösterildiği gibi **elen güvenlik kuralları**’nı ve sonra **Ekle**’yi seçin:

    ![Gelen güvenlik kuralları](./media/connection-monitor/inbound-security-rules.png)

4. Bir sanal ağdaki tüm sanal makineler arasında iletişime izin veren varsayılan kural, **AllowVnetInBound** adlı kuraldır. 22 numaralı bağlantı numarası üzerinden gelen iletişimi reddeden **AllowVnetInBound** kuralından daha yüksek öncelikli (daha küçük sayılı) bir kural oluşturun. Aşağıdaki bilgileri girin veya seçin, kalan varsayılan değerleri kabul edin ve sonra **Ekle**’yi seçin:

    | Ayar                 | Değer          |
    | ---                     | ---            |
    | Hedef bağlantı noktası aralıkları | 22             |
    | Eylem                  | Reddet           |
    | Öncelik                | 100            |
    | Ad                    | DenySshInbound |

5. Bağlantı izleyicisi 60’ar saniyelik aralıklarla araştırma yaptığından birkaç dakika bekleyin ve sonra portalın sol tarafında **Ağ İzleyicisi**’ni, ardından **Bağlantı izleyicisi**’ni ve sonra tekrar **myVm1-myVm2(22)** izleyicisini seçin. Aşağıdaki resimde gösterildiği gibi sonuçlar şimdi farklıdır:

    ![İzleyici ayrıntıları hatası](./media/connection-monitor/vm-monitor-fault.png)

    **myvm2529** ağ arabirimi için durum sütununda kırmızı bir ünlem simgesi olduğunu görebilirsiniz.

6. Durumun neden değiştiğini öğrenmek için önceki resimde 10.0.0.5 seçeneğini belirleyin. Bağlantı izleyicisi size iletişim hatasının nedenini bildirir: *Trafik aşağıdaki ağ güvenlik grubu kuralı nedeniyle engellendi: UserRule_DenySshInbound*.

    Birisinin 4. adımda oluşturduğunuz güvenlik kuralını uyguladığını bilmiyorsanız, bağlantı izleyicisinden bu kuralın iletişim sorununa yol açtığını öğrenirsiniz. Daha sonra sanal makineler arasındaki iletişimi geri yüklemek için kuralı değiştirebilir, geçersiz kılabilir veya kaldırabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu ve içerdiği tüm kaynakları silin:

1. Portalın üst kısmındaki **Ara** kutusuna *myResourceGroup* değerini girin. Arama sonuçlarında **myResourceGroup** seçeneğini gördüğünüzde bunu seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **KAYNAK GRUBU ADINI YAZIN:** için *myResourceGroup* girin ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, iki sanal makine arasındaki bir bağlantının nasıl izleneceğini öğrendiniz. Bir ağ güvenlik grubu kuralının, bir sanal makineyle iletişimi engellediğini öğrendiniz. Bağlantı izleyicisinin döndürebileceği farklı yanıtların tümü hakkında bilgi edinmek için [yanıt türleri](network-watcher-connectivity-overview.md#response) bölümüne bakın. Bir sanal makine, tam etki alanı adı, tekdüzen kaynak tanımlayıcısı veya IP adresi arasındaki bağlantıyı da izleyebilirsiniz.

Belirli bir noktada, bir sanal ağ üzerindeki kaynakların, Azure sanal ağ geçidi tarafından bağlanan diğer ağlardaki kaynaklarla iletişim kuramadığını fark edebilirsiniz. Sanal ağ geçidi ile bir sorunu tanılama hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Ağlar arasındaki iletişim sorunlarını tanılama](diagnose-communication-problem-between-networks.md)
