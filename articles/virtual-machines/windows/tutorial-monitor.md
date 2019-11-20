---
title: Öğretici-Azure 'da Windows sanal makinelerini Izleme
description: Bu öğreticide, Windows sanal makinelerinizde çalışan performansı ve keşfedilen uygulama bileşenlerini izlemeyi öğreneceksiniz.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 9e807927a10399b02c2c89359c2ffffaf87ba57b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112496"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Öğretici: Azure 'da bir Windows sanal makinesini Izleme

Azure izleme, Azure VM 'lerinden önyükleme ve performans verilerini toplamak, bu verileri Azure depolama 'da depolamak ve Portal, Azure PowerShell modülü ve Azure CLı aracılığıyla erişilebilir hale getirmek için aracıları kullanır. Gelişmiş izleme, performans ölçümleri toplayarak, VM 'de yüklü olan uygulama bileşenlerini keşfeterek ve performans grafiklerini ve bağımlılık eşlemesini içerdiğinden VM'ler için Azure İzleyici ile birlikte sunulur.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * VM’de önyükleme tanılamalarını etkinleştirme
> * Önyükleme tanılamasını görüntüleme
> * VM konak ölçümlerini görüntüleme
> * VM'ler için Azure İzleyici etkinleştir
> * VM performans ölçümlerini görüntüleme
> * Uyarı oluşturma

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell'i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. İsterseniz [https://shell.azure.com/powershell](https://shell.azure.com/powershell) adresine giderek Cloud Shell'i ayrı bir tarayıcı sekmesinde de başlatabilirsiniz. **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

## <a name="create-virtual-machine"></a>Sanal makine oluşturma

Bu öğreticide Azure izlemesi ve güncelleştirme yönetimini yapılandırmak için, Azure'da bir Windows VM'sine ihtiyacınız vardır. İlk olarak, VM için [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) ile bir yönetici kullanıcı adı ve parola ayarlayın:

```azurepowershell-interactive
$cred = Get-Credential
```

Şimdi [New-azvm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)ile VM 'yi oluşturun. Aşağıdaki örnekte *EastUS* konumunda *myVM* adlı bir VM oluşturulur. Henüz yoksa, *myResourceGroupMonitorMonitor* kaynak grubu ve destekleyici ağ kaynakları oluşturulur:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Kaynakların ve sanal makinenin oluşturulması birkaç dakika sürer.

## <a name="view-boot-diagnostics"></a>Önyükleme tanılamasını görüntüleme

Windows sanal makinelerinin önyüklemesi yapıldığında, önyükleme tanılama aracısı sorun giderme amacıyla kullanılabilecek bir ekran çıktısı yakalar. Bu özellik varsayılan olarak etkindir. Yakalanan ekran görüntüleri, varsayılan olarak da oluşturulan bir Azure depolama hesabında depolanır.

Önyükleme tanılama verilerini [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata) komutuyla alırsınız. Aşağıdaki örnekte, önyükleme tanılamaları *c:\* sürücüsünün kök dizinine indirilir.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Konak ölçümlerini görüntüleme

Windows VM’sinin, Azure’da etkileşimde bulunduğu ayrılmış bir Konak VM'si vardır. Konağa ait ölçümler otomatik olarak toplanır ve Azure portalında görüntülenebilir.

1. Azure portalında **Kaynak Grupları**’na tıklayın, önce **myResourceGroupMonitor** seçeneğini belirleyin ve ardından kaynak listesinden **myVM**’yi seçin.
2. Konak VM’nin performansını görmek için VM dikey penceresinde **Ölçümler**’e tıklayın ve ardından **Kullanılabilen ölçümler** bölümünden herhangi bir Konak ölçümünü seçin.

    ![Konak ölçümlerini görüntüleme](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Gelişmiş izlemeyi etkinleştir

Azure VM 'nizi VM'ler için Azure İzleyici izlemeyi etkinleştirmek için:

1. Azure portalında **Kaynak Grupları**’na tıklayın, önce **myResourceGroupMonitor** seçeneğini belirleyin ve ardından kaynak listesinden **myVM**’yi seçin.

2. VM sayfasında içinde **izleme** bölümünden **Insights (Önizleme)** .

3. Üzerinde **Insights (Önizleme)** sayfasında **şimdi deneyin**.

    ![Bir VM için sanal makineler için Azure İzleyici etkinleştir](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Üzerinde **Azure İzleyici İçgörüler ekleme** sayfasında mevcut bir Log Analytics varsa, aynı abonelikte çalışma alanı, aşağı açılan listeden seçin.  

    Liste, varsayılan çalışma alanını ve VM 'nin abonelikte dağıtıldığı konumu önceden seçer. 

    >[!NOTE]
    >İzleme verilerini VM 'den depolamak üzere yeni bir Log Analytics çalışma alanı oluşturmak için, bkz. [Log Analytics çalışma alanı oluşturma](../../azure-monitor/learn/quick-create-workspace.md). Log Analytics çalışma alanınız [desteklenen bölgelerden](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics)birine ait olmalıdır.

İzlemeyi etkinleştirdikten sonra, VM 'nin performans ölçümlerini görüntüleyebilmeniz için birkaç dakika beklemeniz gerekebilir.

![Dağıtım işlemi izlemeyi VM'ler için Azure İzleyicisi'ni etkinleştirme](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>VM performans ölçümlerini görüntüleme

VM'ler için Azure İzleyici, bir sanal makinenin ne kadar iyi performans gösterdiğini belirlemenize yardımcı olmak üzere birkaç ana performans göstergelerini (KPI) hedefleyen bir performans grafikleri kümesi içerir. Sanal makinenize erişmek için aşağıdaki adımları uygulayın.

1. Azure portalında **Kaynak Grupları**’na tıklayın, önce **myResourceGroupMonitor** seçeneğini belirleyin ve ardından kaynak listesinden **myVM**’yi seçin.

2. VM sayfasında içinde **izleme** bölümünden **Insights (Önizleme)** .

3. **Performans** sekmesini seçin.

Bu sayfa yalnızca performans kullanım grafiklerini içermez, aynı zamanda bulunan her bir mantıksal disk için bir tablo, kapasitesi, kullanımı ve her bir ölçünün toplam ortalaması dahil değildir.

## <a name="create-alerts"></a>Uyarı oluşturma

Belirli performans ölçümlerine bağlı uyarılar oluşturabilirsiniz. Uyarılar, ortalama CPU kullanımı belirli bir eşiği aştığında veya mevcut boş disk alanı belirli bir miktarın altına düştüğünde bildirim almak için kullanılabilir. Uyarılar Azure portalında görüntülenebilir veya e-posta ile gönderilebilir. Ayrıca oluşturulan uyarılara yanıt olarak Azure Otomasyonu runbook’larını veya Azure Logic Apps’i tetikleyebilirsiniz.

Aşağıdaki örnek, ortalama CPU kullanımı için bir uyarı oluşturur.

1. Azure portalında **Kaynak Grupları**’na tıklayın, önce **myResourceGroupMonitor** seçeneğini belirleyin ve ardından kaynak listesinden **myVM**’yi seçin.

2. Önce VM dikey penceresinde **Uyarı kuralları**’na ve ardından uyarılar dikey penceresinin üstündeki **Ölçüm uyarısı ekle** seçeneğine tıklayın.

3. Uyarınız için **myAlertRule** gibi bir *Ad* girin

4. CPU yüzdesi beş dakika boyunca 1,0’ı aştığında bir uyarı tetiklemek için diğer varsayılan ayarların tümünü seçili bırakın.

5. İsteğe bağlı olarak e-posta bildirimi göndermek için *E-posta sahipleri, katkıda bulunanlar ve okuyucular* kutusunu işaretleyebilirsiniz. Varsayılan eylem olarak portalda bir bildirim sunulur.

6. **Tamam** düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide VM 'nizin performansını yapılandırmış ve görüntülediyseniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Kaynak grubu ve sanal makine oluşturma
> * VM’de önyükleme tanılamalarını etkinleştirme
> * Önyükleme tanılamasını görüntüleme
> * Konak ölçümlerini görüntüleme
> * VM'ler için Azure İzleyici etkinleştir
> * VM ölçümlerini görüntüleme
> * Uyarı oluşturma

Azure Güvenlik Merkezi hakkında daha fazla bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [VM güvenliğini yönetme](../../security/fundamentals/overview.md)
