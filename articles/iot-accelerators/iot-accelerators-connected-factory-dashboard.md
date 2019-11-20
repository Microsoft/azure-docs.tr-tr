---
title: Bağlı fabrika panosunu kullanma-Azure | Microsoft Docs
description: Bu makalede, endüstriyel IoT cihazlarınızı izlemek ve yönetmek için bağlı fabrika panosunun özelliklerinin nasıl kullanılacağı açıklanır.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: b53177d578768428665891704269e63bd8edb09e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820169"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Bağlı fabrika çözümü Hızlandırıcı panosundaki özellikleri kullanma

[Sektörel IoT cihazlarımı yönetmek için bulut tabanlı bir çözüm dağıtma](quickstart-connected-factory-deploy.md) hızlı başlangıç, panoda gezinmeniz ve alarmlar için nasıl yanıt verileceğini gösterdi. Bu nasıl yapılır kılavuzunda, endüstriyel IoT cihazlarınızı izlemek ve yönetmek için kullanabileceğiniz bazı ek Pano özellikleri gösterilmektedir.

## <a name="apply-filters"></a>Filtreleri uygulama

Panoda görünen bilgileri **fabrika konumları** panelinde ya da **alarmlar** panelinde filtreleyebilirsiniz:

1. Fabrika konumları veya alarmlar panelinde kullanılabilir filtrelerin listesini görüntülemek için **huni** simgesine tıklayın.

1. Filtreler paneli görüntülenir:

    [Bağlı fabrika çözümü Hızlandırıcı filtrelerini ![](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. İhtiyacınız olan filtreyi seçin ve **Uygula**' ya tıklayın. Ayrıca, filtre alanlarına serbest metin yazmak da mümkündür.

1. Filtre daha sonra uygulanır. Ekstra huni simgesi bir filtrenin uygulandığını gösterir:

    [Bağlı fabrika çözümü Hızlandırıcı filtresi uygulandı ![](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Etkin bir filtre, görünen OEE ve KPI değerlerini etkilemez, yalnızca liste içeriğini filtreler.

1. Bir filtreyi temizlemek için huni öğesine tıklayın ve filtre panelinde **Temizle** ' ye tıklayın.

## <a name="browse-an-opc-ua-server"></a>OPC UA sunucusuna göz atma

Çözüm hızlandırıcıyı dağıtırken, panodan gözatabileceğiniz bir sanal bir OPC UA sunucusu kümesi otomatik olarak temin edersiniz. Sanal sunucular, gerçek sunucuları dağıtmanıza gerek kalmadan çözüm hızlandırıcısında denemeler yapmayı kolaylaştırır.

1. Pano gezinti çubuğundaki **tarayıcı simgesine** tıklayın:

    [![bağlı Factory Çözüm Hızlandırıcısı sunucu tarayıcısı](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Listeden, çözüm hızlandırıcısında sizin için dağıtılan sunucuları gösteren sunuculardan birini seçin:

    [Bağlı fabrika çözümü Hızlandırıcısı sunucu listesini ![](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. **Bağlan**’a tıklayın; güvenlik iletişim kutusu görüntülenir. Benzetim için **devam**' a tıklamanız güvenlidir.

1. Sunucu ağacındaki düğümlerden herhangi birini genişletmek için düğüme tıklayın. Yayımlama telemetrisi olan düğümlerin yanlarında onay işareti vardır:

    [![bağlı Factory Çözüm Hızlandırıcısı sunucu ağacı](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Düğümü okumak, yazmak, yayımlamak veya çağırmak için bir öğeye sağ tıklayın. Kullanabileceğiniz eylemler, izinlerinize ve düğümün özniteliklerine bağlıdır. Okuma seçeneği belirli bir düğümün değerini gösteren bağlam panelini görüntüler. Yazma seçeneği yeni değer girebileceğiniz bir bağlam paneli görüntüler. Çağırma seçeneği çağrı için parametreleri girebileceğiniz bir düğüm görüntüler.

## <a name="publish-a-node"></a>Düğümü yayımlama

*Sanal OPC UA sunucusuna* göz attığınızda, yeni düğümleri yayımlamayı da seçebilirsiniz. Çözümde bu düğümlerden gelen telemetriyi analiz edebilirsiniz. Bu *sanal OPC UA sunucuları* gerçek cihazları dağıtmaya gerek kalmadan çözüm hızlandırıcısının denemesini kolaylaştırır:

1. OPC UA sunucusu tarayıcı ağacında yayımlamak istediğiniz düğüme göz atın.

1. Düğüme sağ tıklayın. **Yayımla**' ya tıklayın:

    [Bağlı fabrika çözümü Hızlandırıcısı yayımlama düğümünü ![](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Yayımlama işleminin başarılı olduğunu bildiren bir bağlam paneli görüntülenir. Düğüm, istasyon düzeyi görünümünde, yanındaki onay işaretiyle görünür:

    [![bağlı fabrika çözümü Hızlandırıcısı yayımlama başarısı](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Komut ve denetim

Bağlı Fabrika, endüstri cihazlarınızı doğrudan buluttan kumanda etmenize ve denetlemenize olanak tanır. Cihaz tarafından oluşturulan alarmları yanıtlamak için bu özelliği kullanabilirsiniz. Örneğin, bir basınç sürümü vanası açmak için cihaza bir komut gönderebilirsiniz. Kullanılabilir komutları, OPC UA sunucuları tarayıcı ağacındaki **StationCommands** düğümünde bulabilirsiniz. Bu senaryoda, Münih’teki üretim hattının montaj istasyonundaki basınç tahliye vanasını açıyorsunuz. Komut ve denetim işlevlerini kullanmak için, çözüm Hızlandırıcı dağıtımı için **yönetici** rolünde olmanız gerekir:

1. Münih, üretim satırı 0, derleme İstasyonu için OPC UA sunucusu tarayıcı ağacındaki **Stationcommands** düğümüne gidin.

1. Kullanmak istediğiniz komutu seçin. **Openpressurereleasevana** düğümüne sağ tıklayın. **Çağır**'a tıklayın:

    [Bağlı fabrika çözümü Hızlandırıcısı çağrı komutu ![](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Hangi yöntemi çağırılacağını ve herhangi bir parametre ayrıntısını bildiren bir bağlam paneli görüntülenir. **Çağır**'a tıklayın:

    [![bağlı fabrika çözümü Hızlandırıcısı çağrı parametreleri](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Bağlam paneli, yöntem çağrısının başarılı olduğunu bildirecek şekilde güncelleştirilir. Çağrı sonucu güncelleştirilen basınç düğümünün değerini okuyarak çağrının başarılı olduğunu doğrulayabilirsiniz.

    [![bağlı fabrika çözümü Hızlandırıcısı çağrısı başarılı](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Arka planda

Bir çözüm hızlandırıcısını dağıttığınızda, dağıtım işlemi seçtiğiniz Azure aboneliğinde birden çok kaynak oluşturur. Bu kaynakları Azure [portalında](https://portal.azure.com)görüntüleyebilirsiniz. Dağıtım işlemi, çözüm hızlandırıcınız için seçtiğiniz adı temel alan bir ada sahip bir **kaynak grubu** oluşturur:

[Bağlı fabrika çözümü Hızlandırıcısı kaynak grubu ![](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Her bir kaynağın ayarlarını, kaynak grubundaki kaynaklar listesinde seçerek görüntüleyebilirsiniz.

[Azure-IoT-bağlı-Factory](https://github.com/Azure/azure-iot-connected-factory) GitHub deposunda çözüm hızlandırıcısının kaynak kodunu da görüntüleyebilirsiniz.

İşiniz bittiğinde, [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) sitesindeki Azure aboneliğinizden çözüm hızlandırıcıyı silebilirsiniz. Bu site, çözüm hızlandırıcısını oluşturduğunuzda sağlanan tüm kaynakları kolayca silmenize imkan tanır.

> [!NOTE]
> Çözüm hızlandırıcısına ilişkin her şeyi sildikten emin olmak için [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) sitesinde silin. Portalda kaynak grubunu silmeyin.

## <a name="next-steps"></a>Sonraki adımlar

Çalışan bir çözüm hızlandırıcısı dağıttığınıza göre aşağıdaki makaleleri okuyarak IoT çözüm hızlandırıcılarıyla çalışmaya başlayabilirsiniz:

* [Bağlı fabrika çözüm Hızlandırıcısını yapılandırma](iot-accelerators-connected-factory-configure.md)
* [Azureiotsolutions.com sitesindeki izinler](iot-accelerators-permissions.md)
