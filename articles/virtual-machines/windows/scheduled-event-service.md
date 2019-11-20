---
title: Azure 'da Windows VM 'leriniz için zamanlanmış olayları izleme
description: Zamanlanan olaylar için Azure sanal makinelerinizi izlemeyi öğrenin.
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073306"
---
# <a name="monitoring-scheduled-events"></a>İzleme Zamanlanan Olaylar

Güncelleştirmeler, hizmetlerin güvenli ve güncel kalmasını sağlamak için her gün farklı Azure bölümlerine uygulanır. Planlı güncelleştirmelere ek olarak planlanmamış olaylar da oluşabilir. Örneğin, herhangi bir donanım performansında azalma veya hata algılanırsa Azure hizmetlerinin planlanmamış bakım gerçekleştirmesi gerekebilir. Dinamik geçiş, güncelleştirmeleri koruyan ve genel olarak güncelleştirmelerin etkisinden katı bir çubuk tutarak, bu olaylar çoğu zaman müşterilere saydamdır ve bir etkisi yoktur ve sanal makine donmasına neden olur. Ancak bazı uygulamalarda, sanal makine donmasına karşın birkaç saniye bir etkiye neden olabilir. Yaklaşan Azure bakımı hakkında daha fazla bilgi için bu uygulamalar için en iyi deneyimi elde etmek önemlidir. [Zamanlanan olaylar hizmeti](scheduled-events.md) , yaklaşan bakım hakkında bildirim almak için size bir programlama arabirimi sağlar ve bakımı sorunsuz bir şekilde işlemenizi sağlar. 

Bu makalede, sanal makinelerinizi etkileyen bakım olayları hakkında bildirim almak ve izleme ve Analize yardımcı olabilecek bir temel Otomasyon oluşturmak için zamanlanmış olayları nasıl kullanabileceğinizi göstereceğiz.


## <a name="routing-scheduled-events-to-log-analytics"></a>Zamanlanan olayları Log Analytics 'a yönlendirme

Zamanlanan Olaylar, [azure Instance Metadata Service](instance-metadata-service.md)her Azure sanal makinesinde kullanılabilen bir parçası olarak kullanılabilir. Müşteriler, zamanlanmış bakım bildirimleri bulmak ve durumu kaydetme ve sanal makineyi döndürme dışında yapma gibi azaltmalar gerçekleştirmek için sanal makinelerinin uç noktasını sorgulamak üzere otomasyon yazabilir. Azure bakım olaylarının denetim günlüğüne sahip olmak için Zamanlanan Olaylar kaydetmek üzere otomasyon oluşturmanız önerilir. 

Bu makalede, Log Analytics için bakım Zamanlanan Olaylar yakalama konusunda size kılavuzluk edeceğiz. Daha sonra, takımınızda e-posta gönderme ve sanal makinelerinizi etkileyen tüm olayların geçmiş bir görünümünü alma gibi bazı temel bildirim eylemlerini tetikleyeceğiz. Olay toplama ve otomasyon için [Log Analytics](/azure/azure-monitor/learn/quick-create-workspace)kullanacağız, ancak bu günlükleri toplamak ve Otomasyonu tetiklemek için herhangi bir izleme çözümünü kullanabilirsiniz.

![Olay yaşam döngüsünü gösteren diyagram](./media/notifications/events.png)

## <a name="prerequisites"></a>Önkoşullar

Bu örnekte, bir [kullanılabilirlik kümesinde bir Windows sanal makinesi](tutorial-availability-sets.md)oluşturmanız gerekecektir. Zamanlanan Olaylar, kullanılabilirlik kümesi, bulut hizmeti, sanal makine ölçek kümesi veya tek başına VM 'lerdeki sanal makinelerin hiçbirini etkileyebilecek değişiklikler hakkında bildirimler sağlar. Kullanılabilirlik kümesindeki diğer tüm VM 'lerin olaylarını almak için, toplayıcı olarak görev yapacak VM 'lerden birinde zamanlanan olayları yoklayan bir [hizmet](https://github.com/microsoft/AzureScheduledEventsService) çalıştıracağız.    

Öğreticinin sonunda Grup kaynak grubunu silmeyin.

Ayrıca, kullanılabilirlik kümesindeki VM 'lerden bilgi toplamak için kullanacağınız [bir Log Analytics çalışma alanı oluşturmanız](/azure/azure-monitor/learn/quick-create-workspace) gerekecektir.

## <a name="set-up-the-environment"></a>Ortamı ayarlama

Artık bir kullanılabilirlik kümesinde 2 başlangıç VM 'si olmalıdır. Artık aynı Kullanılabilirlik kümesinde myCollectorVM adlı bir 3. sanal makine oluşturuyoruz. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

[GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip)'dan projenin yükleme. zip dosyasını indirin.

**Mycollectorvm** ' ye bağlanın ve. zip dosyasını sanal makineye kopyalayın ve tüm dosyaları ayıklayın. VM 'niz üzerinde bir PowerShell istemi açın. İsteminizi `SchService.ps1`içeren klasöre taşıyın, örneğin: `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`ve hizmeti ayarlayın.

```powershell
.\SchService.ps1 -Setup
```

Hizmeti başlatın.

```powershell
.\SchService.ps1 -Start
```

Hizmet artık zamanlanan tüm olaylar için her 10 saniyede bir yoklama başlatacak ve bakımı hızlandırmak için olayları onaylacaktır.  Dondurma, yeniden başlatma, yeniden dağıtma ve preempt, zamanlama olayları tarafından yakalanan olaylardır.   Olayı onaylamadan önce bazı azaltmaları tetiklemek için betiği genişletebileceğinizi unutmayın.

Hizmet durumunu doğrulayın ve çalıştığından emin olun.

```powershell
.\SchService.ps1 -status  
```

Bu, `Running`döndürmelidir.

Hizmet artık zamanlanan tüm olaylar için her 10 saniyede bir yoklama başlatacak ve bakımı hızlandırmak için olayları onaylacaktır.  Dondurma, yeniden başlatma, yeniden dağıtma ve preempt, zamanlama olayları tarafından yakalanan olaylardır. Olayı onaylamadan önce bazı azaltmaları tetiklemek için betiği genişletebilirsiniz.

Yukarıdaki olaylardan herhangi biri, zamanlama olay hizmeti tarafından yakalandıktan sonra, uygulama olay günlüğü olay durumu, olay türü, kaynaklar (sanal makine adları) ve NotBefore (minimum uyarı süresi) oturum açar. KIMLIĞI 1234 olan olayları uygulama olay günlüğünde bulabilirsiniz.

Hizmet kurulduktan ve başlatıldıktan sonra, Windows uygulama günlüklerinde olayları günlüğe kaydeder.   Bu işe yarar sağlamak için kullanılabilirlik kümesindeki sanal makinelerden birini yeniden başlatın ve Windows günlükleri > Olay Görüntüleyicisi 'nde günlüğe kaydedilmiş bir olay görmeniz gerekir. Bu uygulama günlüğü, VM 'nin yeniden başlatıldığını gösterir. 

![Olay Görüntüleyicisi 'nin ekran görüntüsü.](./media/notifications/event-viewer.png)

Olaylar zamanlama olay hizmeti tarafından yakalandıktan sonra, olay durumu, olay türü, kaynaklar (VM adı) ve NotBefore (en düşük bildirim süresi) ile uygulama bile oturum açar. KIMLIĞI 1234 olan olayları uygulama olay günlüğünde bulabilirsiniz.

> [!NOTE] 
> Bu örnekte, sanal makineler bir kullanılabilirlik kümesinde yer alan ve zamanlanan olayları dinlemek ve Log Analytics 'e yönlendirmek için toplayıcı olarak tek bir sanal makine belirlememizi etkinleştirmiş. Tek başına sanal makineleriniz varsa, hizmeti her sanal makinede çalıştırabilir ve ardından bunları Log Analytics çalışma alanınıza ayrı ayrı bağlayabilirsiniz.
>
> Ayarladığımızda Windows 'u seçtik, ancak Linux 'ta benzer bir çözüm tasarlayabilirsiniz.

Herhangi bir noktada, `–stop` ve `–remove`anahtarlarını kullanarak zamanlanmış olay hizmetini durdurabilir/kaldırabilirsiniz.

## <a name="connect-to-the-workspace"></a>Çalışma alanına bağlan


Şimdi bir Log Analytics çalışma alanını toplayıcı VM 'sine bağlamak istiyoruz. Log Analytics çalışma alanı bir depo işlevi görür ve olay günlüğü toplamayı, toplayıcı VM 'den uygulama günlüklerini yakalamak için yapılandıracağız. 

 Zamanlanan Olaylar, hizmetimizdeki uygulama günlüğü olarak kaydedilecek olay günlüğüne yönlendirmek için sanal makinenizi Log Analytics çalışma alanınıza bağlamanız gerekir.  
 
1. Oluşturduğunuz çalışma alanının sayfasını açın.
1. **Veri kaynağına bağlan** altında **Azure sanal makineleri (VM 'ler)** seçeneğini belirleyin.

    ![Bir VM 'ye veri kaynağı olarak bağlanma](./media/notifications/connect-to-data-source.png)

1. **Mycollectorvm**öğesini arayın ve seçin. 
1. **Mycollectorvm**için yeni sayfada **Bağlan**' ı seçin.

Bu işlem, sanal makinenize [Microsoft Monitoring Agent](/azure/virtual-machines/extensions/oms-windows) 'ı yükler. SANAL makinenizin çalışma alanına bağlanması ve uzantıyı yüklemesi birkaç dakika sürer. 

## <a name="configure-the-workspace"></a>Çalışma alanını yapılandırma

1. Çalışma alanınızın sayfasını açın ve **Gelişmiş ayarlar**' ı seçin.
1. Sol menüden **verileri** seçin ve ardından **Windows olay günlükleri**' ni seçin.
1. **Aşağıdaki olay günlüklerinden topla**' da, *uygulama* yazmaya başlayın ve ardından listeden **uygulama** ' yı seçin.

    ![Gelişmiş ayarları seçin](./media/notifications/advanced.png)

1. **Hata**, **Uyarı**ve **bilgi** seçili bırakın ve sonra ayarları kaydetmek için **Kaydet** ' i seçin.


> [!NOTE]
> Bir gecikme olur ve günlüğün kullanılabilir olması 10 dakika kadar sürebilir. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Azure Izleyici ile bir uyarı kuralı oluşturma 


Olaylar Log Analytics gönderildikten sonra, zamanlama olaylarını aramak için aşağıdaki [sorguyu](/azure/azure-monitor/log-query/get-started-portal) çalıştırabilirsiniz.

1. Sayfanın üst kısmında, **Günlükler** ' i seçin ve aşağıdaki metin kutusuna yapıştırın:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. **Kaydet**' i seçin ve ardından ad Için *Logquery* yazın, **sorgu** türü olarak bırakın, **Kategori**olarak *vmlogs* yazın ve ardından **Kaydet**' i seçin. 

    ![Sorguyu Kaydet](./media/notifications/save-query.png)

1. **Yeni uyarı kuralı**’nı seçin. 
1. **Kural oluştur** sayfasında, **kaynak**olarak `collectorworkspace` bırakın.
1. **Koşul**' ın altında, *müşteri günlük araması <login undefined>her seferinde* girişi seçin. **Sinyal mantığını Yapılandır** sayfası açılır.
1. **Eşik değeri**altında *0* girin ve **bitti**' yi seçin.
1. **Eylemler**altında **eylem grubu oluştur**' u seçin. **Eylem grubu Ekle** sayfası açılır.
1. **Eylem grubu adı**' nda, *Myactiongroup*yazın.
1. **Kısa ad**alanına **Myactiongroup**yazın.
1. **Kaynak grubu**' nda **myResourceGroupAvailability**' yi seçin.
1. Eylemler ' in altında, **eylem adı** tür **e-** postası ' na ve ardından **e-posta/SMS/Push/ses** **E-posta/SMS/Push/ses** sayfası açılır.
1. **E-posta**seçeneğini belirleyin, e-posta adresinizi yazın ve **Tamam**' ı seçin.
1. **Eylem grubu Ekle** sayfasında **Tamam**' ı seçin. 
1. **Kural oluştur** sayfasında, **Uyarı ayrıntıları**' nın altında, **Uyarı kuralı adı**Için *Myalert* yazın ve **Açıklama**için *e-posta uyarısı kuralı* yazın.
1. İşiniz bittiğinde **Uyarı kuralı oluştur**' u seçin.
1. Kullanılabilirlik kümesindeki VM 'lerden birini yeniden başlatın. Birkaç dakika içinde, uyarının tetiklendiği bir e-posta almalısınız.

Uyarı kurallarınızı yönetmek için kaynak grubuna gidin, sol menüden **Uyarılar** ' ı seçin ve ardından sayfanın en üstündeki **Uyarı kurallarını yönet** ' i seçin.

     
## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. GitHub 'da [Zamanlanmış olaylar hizmeti](https://github.com/microsoft/AzureScheduledEventsService) sayfası.
