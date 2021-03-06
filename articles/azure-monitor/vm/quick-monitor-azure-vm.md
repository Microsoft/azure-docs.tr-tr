---
title: Azure Izleyici ile Azure sanal makinesini izleme
description: Azure Izleyici 'de bir Azure sanal makinesi için verileri nasıl toplayacağınızı ve analiz edeceğinizi öğrenin.
ms.service: azure-monitor
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 7efd8baf54aeacbd2f55640240a15f2517dcd904
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046933"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Hızlı başlangıç: Azure Izleyici ile Azure sanal makinesini Izleme
[Azure izleyici](../overview.md) , Azure sanal makinelerinden oluşturulan verilerin veri toplamaya başlar. Bu hızlı başlangıçta, bir Azure sanal makinesi için otomatik olarak toplanan verileri ve Azure portal nasıl görüntüleyekullanacağınızı gösteren kısa bir adım adım bulacaksınız. VM 'niz için VM [öngörülerini](../vm/vminsights-overview.md) etkinleştirerek VM 'deki aracıları, süreçler ve bunların bağımlılıkları dahil Konuk işletim sistemindeki verileri toplayıp analiz edecek şekilde etkinleştirin.

Bu hızlı başlangıçta mevcut bir Azure sanal makinenizin olduğu varsayılmaktadır. Aksi takdirde, bir [Windows sanal makinesi](../../virtual-machines/windows/quick-create-portal.md) OLUŞTURABILIR veya VM hızlı başlangıçlarımızı takip eden BIR [Linux sanal](../../virtual-machines/linux/quick-create-cli.md) makinesi oluşturabilirsiniz.

Azure kaynaklarından toplanan izleme verilerinin daha ayrıntılı açıklamaları için bkz. Azure [izleyici Ile Azure sanal makinelerini izleme](./monitor-vm-azure.md).


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Azure Kaynak hızlı başlangıç Izleyicisini doldurun.
Genel Bakış sayfasını, etkinlik günlüğünü ve aboneliğinizdeki bir VM 'nin ölçümlerini görüntülemek için Azure [izleyici ile bir Azure kaynağının tamamını izleyin](../essentials/quick-monitor-azure-resource.md) . Azure VM 'Leri, diğer Azure kaynakları ile aynı izleme verilerini toplar, ancak bu yalnızca konak VM için geçerlidir. Bu hızlı başlangıç, Konuk işletim sistemini ve iş yüklerini izlemeye odaklanacaktır.


## <a name="enable-vm-insights"></a>VM öngörülerini etkinleştir
Konak VM 'si için ölçümler ve etkinlik günlükleri toplanırken, Konuk işletim sisteminden ve onun iş yüklerinden izleme verileri toplamak ve analiz etmek için bir aracıya ve bazı yapılandırmaya ihtiyacınız vardır. VM öngörüleri bu aracıları yükleyerek sanal makinelerinizi izlemeye yönelik ek güçlü özellikler sunar.

1. Sanal makinenizin menüsüne gidin.
2. **Genel bakış** sayfasında kutucuktan **Öngörüler 'e git ' e** tıklayın veya **izleme** menüsünden **Öngörüler** ' e tıklayın.

    ![Genel bakış sayfası](media/quick-monitor-azure-vm/overview-insights.png)

3. Sanal makine için VM öngörüleri henüz etkinleştirilmemişse **Etkinleştir**' e tıklayın. 

    ![Öngörüleri etkinleştir](media/quick-monitor-azure-vm/enable-insights.png)

4. Sanal makine zaten bir Log Analytics çalışma alanına bağlı değilse, var olan bir çalışma alanını seçmeniz veya yeni bir tane oluşturmanız istenir. Sanal makineniz ile aynı bölgede benzersiz ada sahip bir çalışma alanı olan varsayılanı seçin.

    ![Çalışma alanını seçme](media/quick-monitor-azure-vm/select-workspace.png)

5. Uzantılar etkinleştirildiğinden ve aracılar sanal makinenizde yüklü olduğundan, ekleme birkaç dakika sürer. Bu tamamlandığında, öngörülerin başarıyla dağıtıldığını belirten bir ileti alırsınız. VM öngörülerini açmak için **Azure İzleyicisi** ' ne tıklayın.

    ![Azure Izleyici 'yi açın](media/quick-monitor-azure-vm/azure-monitor.png)

6. SANAL makinelerinizi aboneliğinizdeki eklendi olan diğer VM 'lerle görürsünüz. Aboneliğinizdeki eklendi olmayan sanal makineleri görüntülemek istiyorsanız **izlenmiyor** sekmesini seçin.

    ![başlarken](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Çalışma alanını yapılandırma
Yeni bir Log Analytics çalışma alanı oluşturduğunuzda, günlükleri toplamak için yapılandırılması gerekir. Bu yapılandırmanın, yapılandırma kendisine bağlanan herhangi bir sanal makineye gönderildikten sonra yalnızca bir kez gerçekleştirilmesi gerekir.

1. **Çalışma alanı yapılandırması** ' nı seçin ve ardından çalışma alanınızı seçin.

2. **Gelişmiş ayarları** seçin

    ![Log Analytics Gelişmiş Ayarlar](../vm/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Windows VM’den veri toplama


2. **Veri**’yi seçin ve ardından **Windows Olay Günlükleri**’ni seçin.

3. Günlüğün adını yazarak bir olay günlüğü ekleyin.  **System** yazın ve ardından artı işaretini seçin **+** .

4. Tabloda, **Hata** ve **Uyarı** önem derecelerini işaretleyin.

5. Yapılandırmayı kaydetmek için sayfanın en üstünde bulunan **Kaydet** ' i seçin.

### <a name="data-collection-from-linux-vm"></a>Linux VM’den veri toplama

1. **Veri**' yi seçin ve **Syslog**' ı seçin.

2. Günlüğün adını yazarak bir olay günlüğü ekleyin.  **Syslog** yazın ve ardından artı işaretini seçin **+** .  

3. Tabloda, önem derecesi **bilgileri**, **bildirim** ve **hata ayıklama** seçimlerini kaldırın. 

4. Yapılandırmayı kaydetmek için sayfanın en üstünde bulunan **Kaydet** ' i seçin.

## <a name="view-data-collected"></a>Toplanan verileri görüntüleyin

7. Sanal makinenize tıklayın ve sonra **izleme** menüsü **öngörüleri** kutucuğunun altındaki **performans** sekmesini seçin. Bu, sanal makinenizin Konuk işletim sisteminden toplanan bir grup performans sayacını gösterir. Daha fazla sayaç görüntülemek için aşağı kaydırın ve ortalama ve yüzdebirlik değeri farklı zamanlarda görüntülemek için fareyi grafiğin üzerine taşıyın.

    ![Ekran görüntüsü performans bölmesini gösterir.](media/quick-monitor-azure-vm/performance.png)

9. Sanal makinede çalışan işlemlerin yanı sıra bağımlılıklarını gösteren haritalar özelliğini açmak için **eşle** ' yi seçin. Henüz açık değilse Özellik bölmesini açmak için **Özellikler** ' i seçin.

    ![Ekran görüntüsü harita bölmesini gösterir.](media/quick-monitor-azure-vm/map.png)

11. Sanal makineniz için süreçler ' ı genişletin. Ayrıntılarını görüntülemek ve bağımlılıklarını vurgulamak için işlemlerden birini seçin.

    ![Ekran görüntüsü, genişletilmiş bir sanal makine için işlemlerle harita bölmesini gösterir.](media/quick-monitor-azure-vm/processes.png)

12. Sanal makinenizi yeniden seçin ve ardından **günlük olayları**' nı seçin. 

    ![Olayları günlüğe kaydet](media/quick-monitor-azure-vm/log-events.png)

13. Sanal makinenin Log Analytics çalışma alanında depolanan tabloların listesini görürsünüz. Bu liste, bir Windows veya Linux sanal makinesi kullanıyor olmanıza bağlı olarak farklı olacaktır. **Olay** tablosuna tıklayın. Bu, Windows olay günlüğü 'ndeki tüm olayları içerir. Log Analytics olay günlüğü girişlerini almak için basit bir sorgu ile açılır.

    ![Log Analytics](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta, sanal makine için VM öngörülerini etkinleştirdi ve Log Analytics çalışma alanını Konuk işletim sisteminin olaylarını toplayacak şekilde yapılandırmış olursunuz. Verileri görüntüleme ve analiz etmeyi öğrenmek için, öğreticiye devam edin.

> [!div class="nextstepaction"]
> [Log Analytics’te verileri görüntüleme veya analiz etme](../logs/log-analytics-tutorial.md)