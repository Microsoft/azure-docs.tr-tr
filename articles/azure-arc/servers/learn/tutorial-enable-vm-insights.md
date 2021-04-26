---
title: Öğretici-Azure Izleyici VM öngörüleri ile karma makine Izleme
description: Azure Izleyici 'de karma makineden verileri nasıl toplayacağınızı ve analiz edeceğinizi öğrenin.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: f59ad448440110e2c5e6dd1fa1b2858d9cf42e91
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834273"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-vm-insights"></a>Öğretici: VM öngörüleri ile karma makineyi Izleme

[Azure izleyici](../../../azure-monitor/overview.md) , ayrıntılı analiz ve bağıntı için doğrudan karma makinelerinizden bir Log Analytics çalışma alanına veri toplayabilir. Genellikle bu durum, yapılandırma yönetimi standartlarınızı takip eden bir betik, el ile veya otomatik yöntemi kullanarak makineye [Log Analytics aracısını](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent) yüklemeyi kuyruğa alır. Yay özellikli sunucular yakın zamanda Windows ve Linux için Log Analytics ve bağımlılık Aracısı [VM uzantılarını](../manage-vm-extensions.md) yüklemeye yönelik destek sunarak, [VM öngörülerini](../../../azure-monitor/vm/vminsights-overview.md) Azure dışı sanal makinelerinizden veri toplamak üzere etkinleştirir.

Bu öğreticide, basit bir adım kümesi sonrasında VM öngörülerini etkinleştirerek Linux veya Windows makinelerinizdeki verileri nasıl yapılandıracağınızı ve toplayacağınız ve daha kısa bir süre sürer.  

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

* VM Uzantısı işlevselliği yalnızca [Desteklenen bölgeler](../overview.md#supported-regions)listesinde kullanılabilir.

* Seçtiğiniz sunucuların işletim sisteminin VM öngörüleri tarafından desteklendiğinden emin olmak için bkz. [desteklenen işletim sistemleri](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) .

* [Log Analytics aracısına genel bakış](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements)bölümünde sunulan Log Analytics aracısına yönelik güvenlik duvarı gereksinimlerini gözden geçirin. VM öngörüleri eşleme bağımlılık Aracısı herhangi bir veri iletmez ve güvenlik duvarları veya bağlantı noktalarında değişiklik gerektirmez.

## <a name="sign-in-to-azure-portal"></a>Azure portalda oturum açın

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="enable-vm-insights"></a>VM öngörülerini etkinleştir

1. **Tüm hizmetler**' e tıklayarak ve ardından **makineler-Azure yay**' i arayıp seçerek Azure yay hizmetini Azure Portal başlatın.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Tüm hizmetlerde yay etkin sunucular için arama yapın" border="false":::

1. **Makineler-Azure yay** sayfasında [hızlı başlangıç](quick-enable-hybrid-vm.md) makalesinde oluşturduğunuz bağlı makineyi seçin.

1. **İzleme** bölümünün altındaki sol bölmeden **Öngörüler** ' i seçin ve ardından öğesini **etkinleştirin**.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Sol taraftaki menüden Öngörüler seçeneğini belirleyin" border="false":::

1. Azure Izleyici **öngörüleri ekleme** sayfasında, bir çalışma alanı oluşturmanız istenir. Bu öğreticide, zaten varsa, var olan bir Log Analytics çalışma alanı seçmenizi önermiyoruz. Kayıtlı bağlı makineniz ile aynı bölgede benzersiz ada sahip bir çalışma alanı olan varsayılanı seçin. Bu çalışma alanı oluşturulur ve sizin için yapılandırılır.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="VM Insights sayfasını etkinleştir" border="false":::

1. Yapılandırma gerçekleştirilirken durum iletileri alırsınız. Bu işlem, uzantıların bağlı makinenize yüklendiği birkaç dakika sürer.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="VM Insights ilerleme durumu iletisini etkinleştir" border="false":::

    Bu tamamlandığında, makinenin başarıyla eklendi olduğunu ve öngörüleri başarıyla dağıtıldığını belirten bir ileti alırsınız.

## <a name="view-data-collected"></a>Toplanan verileri görüntüleyin

Dağıtım ve yapılandırma tamamlandıktan sonra **Öngörüler**' i seçin ve ardından **performans** sekmesini seçin. Performans sekmesinde, makinenizin Konuk işletim sisteminden toplanan bir grup performans sayacını gösterir. Daha fazla sayaç görüntülemek için aşağı kaydırın ve Log Analytics VM uzantısının makinede yüklendiği zamandan itibaren başlayan ortalama ve yüzdebirlik değeri görüntülemek için fareyi grafiğin üzerine taşıyın.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="Seçilen makine için VM öngörüleri performans grafikleri" border="false":::

Makine üzerinde çalışan işlemlerin ve bunların bağımlılıklarındaki özellikleri gösteren haritalar özelliğini açmak için **eşle** ' yi seçin. Henüz açık değilse Özellik bölmesini açmak için **Özellikler** ' i seçin.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="Seçili makine için VM öngörüleri Haritası" border="false":::

Makinenize yönelik süreçler ' ı genişletin. Ayrıntılarını görüntülemek ve bağımlılıklarını vurgulamak için işlemlerden birini seçin.

Makinenizi yeniden seçin ve ardından **günlük olayları**' nı seçin. Makinenin Log Analytics çalışma alanında depolanan tabloların listesini görürsünüz. Bu liste, bir Windows veya Linux makinesi kullanıyor olmanıza bağlı olarak farklı olacaktır. **Olay** tablosunu seçin. **Olay** tablosu, Windows olay günlüğü 'ndeki tüm olayları içerir. Log Analytics, toplanan olay günlüğü girişlerini almak için basit bir sorgu ile açılır.

## <a name="next-steps"></a>Sonraki adımlar

Azure Izleyici hakkında daha fazla bilgi edinmek için aşağıdaki makaleye bakın:

> [!div class="nextstepaction"]
> [Azure İzleyici'ye genel bakış](../../../azure-monitor/overview.md)