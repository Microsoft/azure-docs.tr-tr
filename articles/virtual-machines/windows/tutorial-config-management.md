---
title: Öğretici-Azure 'da Windows sanal makine yapılandırmasını yönetme
description: Bu öğreticide, bir Windows sanal makinesinde değişiklikleri belirlemeyi ve paket güncelleştirmelerini yönetmeyi öğreneceksiniz
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d97323f1916ee46e6b1f8d4ca8723b950baca39c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74064792"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Öğretici: Azure 'da değişiklikleri Izleme ve Windows sanal makinesini güncelleştirme

Azure [değişiklik izleme](../../automation/change-tracking.md) ve [güncelleştirme yönetimi](../../automation/automation-update-management.md)ile Azure 'daki Windows sanal makinelerinizdeki değişiklikleri kolayca tanımlayabilir ve bu VM 'ler için işletim sistemi güncelleştirmelerini yönetebilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Windows güncelleştirmelerini yönetme.
> * Değişiklikleri ve stoku izleyin.

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell’i açma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz, ücretsiz bir etkileşimli kabuktur. Önceden yüklenmiş ve Azure hesabınızla kullanılmak üzere yapılandırılmış ortak Azure araçları vardır.

Cloud Shell herhangi bir kod bloğunu açmak için, bu kod bloğunun sağ üst köşesinden **dene** ' yi seçin.

Ayrıca, [https://shell.azure.com/powershell](https://shell.azure.com/powershell)giderek ayrı bir tarayıcı sekmesinde Cloud Shell açabilirsiniz. Kod bloklarını kopyalamak için **Kopyala** ' yı seçin, Cloud Shell sekmesine yapıştırın ve kodu çalıştırmak için Enter tuşunu seçin.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Bu öğreticide Azure izlemesi ve güncelleştirme yönetimini yapılandırmak için, Azure'da bir Windows VM'sine ihtiyacınız vardır.

İlk olarak, VM için [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) ile bir yönetici kullanıcı adı ve parola ayarlayın:

```azurepowershell-interactive
$cred = Get-Credential
```

Ardından, [New-azvm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)ile VM 'yi oluşturun. Aşağıdaki örnek, `East US` konumunda `myVM` adlı bir VM oluşturur. Henüz yoksa, kaynak grubu `myResourceGroupMonitor` ve destekleyici ağ kaynakları oluşturulur:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Kaynakların ve sanal makinenin oluşturulması birkaç dakika sürer.

## <a name="manage-windows-updates"></a>Windows güncelleştirmelerini yönetme

Güncelleştirme Yönetimi, Azure Windows VM 'leriniz için güncelleştirmeleri ve düzeltme eklerini yönetmenize yardımcı olur. Doğrudan sanal makinenizde, hızlıca şunları yapabilirsiniz:

- Kullanılabilir güncelleştirmelerin durumunu değerlendirme.
- Gerekli güncelleştirmeleri yüklemeyi zamanlama.
- Güncelleştirmelerin VM 'ye başarıyla uygulandığını doğrulamak için dağıtım sonuçlarını gözden geçirin.

Fiyatlandırma bilgileri için bkz. [güncelleştirme yönetimi Için Otomasyon Fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Güncelleştirme Yönetimi’ni etkinleştirme

VM 'niz için Güncelleştirme Yönetimi etkinleştirmek için:

1. Pencerenin en sol tarafında, **sanal makineler**' i seçin.
1. Listeden bir VM seçin.
1. VM penceresinin **işlemler** bölmesinde **güncelleştirme yönetimi**' ni seçin.
1. **Etkinleştir güncelleştirme yönetimi** penceresi açılır.

Bu VM için Güncelleştirme Yönetimi etkinleştirilip etkinleştirilmeyeceğini öğrenmek için doğrulama yapılır. Doğrulama bir Log Analytics çalışma alanı, bağlantılı Otomasyon hesabı için ve çözümün çalışma alanında olup olmadığı için denetimler içerir.

Güncelleştirme Yönetimi gibi özellikler ve hizmetler tarafından oluşturulan verileri toplamak için bir [Log Analytics](../../log-analytics/log-analytics-overview.md) çalışma alanı kullanırsınız. Çalışma alanı, birden fazla kaynaktan alınan verilerin incelenip analiz edilebileceği ortak bir konum sağlar.

Güncelleştirme gerektiren VM 'lerde ek eylemler gerçekleştirmek için Azure Otomasyonu 'nu kullanarak runbook 'ları sanal makinelere karşı çalıştırabilirsiniz. Bu tür eylemler, güncelleştirmeleri indirmeyi veya uygulamayı içerir.

Doğrulama işlemi ayrıca VM 'nin Microsoft Monitoring Agent (MMA) ve otomasyon karma Runbook Worker ile sağlanıp sağlanmadığını kontrol eder. Aracıyı, VM ile iletişim kurmak ve güncelleştirme durumu hakkında bilgi almak için kullanırsınız.

**Etkinleştir güncelleştirme yönetimi** penceresinde, Log Analytics çalışma alanı ve Otomasyon hesabı ' nı seçin ve ardından **Etkinleştir**' i seçin. Çözümün etkinleştirilmesi 15 dakika kadar sürer.

Ekleme sırasında eksik olan aşağıdaki önkoşulların herhangi biri otomatik olarak eklenir:

* [Log Analytics](../../log-analytics/log-analytics-overview.md) çalışma alanı
* [Otomasyon](../../automation/automation-offering-get-started.md)
* VM 'de etkin olan [karma Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)

Çözüm etkinleştirildikten sonra, **güncelleştirme yönetimi** penceresi açılır. Kullanılacak konumu, Log Analytics çalışma alanını ve otomasyon hesabını yapılandırın ve ardından **Etkinleştir**' i seçin. Bu seçenekler soluk görünüyorsa, VM için başka bir otomasyon çözümü etkinleştirilir ve bu çözümün çalışma alanının ve otomasyon hesabının kullanılması gerekir.

![Güncelleştirme Yönetimi Çözümü Etkinleştir](./media/tutorial-monitoring/manageupdates-update-enable.png)

Güncelleştirme Yönetimi çözümünün etkinleştirilmesi 15 dakika kadar sürebilir. Bu süre boyunca tarayıcı penceresini kapatmayın. Çözüm etkinleştirildikten sonra VM 'deki eksik güncelleştirmeler hakkında bilgiler Azure Izleyici günlüklerine akar. Verilerin Analize uygun olması için 30 dakikadan 6 saate kadar zaman alabilir.

### <a name="view-an-update-assessment"></a>Güncelleştirme değerlendirmesini görüntüleme

Güncelleştirme Yönetimi etkinleştirildikten sonra, **güncelleştirme yönetimi** penceresi görüntülenir. Güncelleştirmelerin değerlendirilmesinden sonra **eksik** Güncelleştirmeler sekmesinde eksik güncelleştirmelerin bir listesini görürsünüz.

 ![Güncelleştirme durumunu görüntüleme](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Güncelleştirme dağıtımı zamanlama

Güncelleştirmeleri yüklemek için yayın zamanlamanızı ve hizmet pencerenizi izleyen bir dağıtım zamanlayın. Dağıtıma dahil edilecek güncelleştirme türlerini seçersiniz. Örneğin, kritik güncelleştirmeleri veya güvenlik güncelleştirmelerini dahil edip güncelleştirme paketlerini dışlayabilirsiniz.

VM için yeni bir güncelleştirme dağıtımı zamanlamak üzere güncelleştirme **yönetimi** penceresinin en üstünde **güncelleştirme dağıtımı zamanla** ' yı seçin. **Yeni güncelleştirme dağıtımı** penceresinde, aşağıdaki bilgileri belirtin:

| Seçenek | Açıklama |
| --- | --- |
| **Adı** |Güncelleştirme dağıtımını tanımlamak için benzersiz bir ad girin. |
|**İşletim sistemi**| **Linux** veya **Windows**' u seçin.|
| **Güncelleştirilecek gruplar** |Azure üzerinde barındırılan VM 'Ler için abonelik, kaynak grubu, konum ve etiketlerin birleşimine göre bir sorgu tanımlayın. Bu sorgu, dağıtımınıza dahil etmek için Azure 'da barındırılan sanal makinelerin dinamik bir grubunu oluşturur. </br></br>Azure üzerinde barındırılmayan VM 'Ler için, var olan kayıtlı bir aramayı seçin. Bu aramayla, dağıtıma dahil etmek için bu VM 'lerin bir grubunu seçebilirsiniz. </br></br> Daha fazla bilgi için bkz. [Dinamik Gruplar](../../automation/automation-update-management-groups.md).|
| **Güncelleştirilecek makineler** |**Kaydedilmiş arama**, **içeri aktarılan grup**veya **makineler**seçin.<br/><br/>**Makineler**' i seçerseniz, açılan listeden makineleri tek tek seçebilirsiniz. Her makinenin hazır olması, tablonun **GÜNCELLEŞTIRME ARACıSı hazırlık** sütununda gösterilir.</br></br> Azure Izleyici günlüklerinde bilgisayar grupları oluşturmaya yönelik farklı yöntemler hakkında bilgi edinmek için bkz. [Azure izleyici günlüklerinde bilgisayar grupları](../../azure-monitor/platform/computer-groups.md) |
|**Güncelleştirme sınıflandırmaları**|Tüm gerekli güncelleştirme sınıflandırmalarını seçin.|
|**Güncelleştirmeleri dahil et/Dışla**|**Dahil et/hariç tut** bölmesini açmak için bu seçeneği belirleyin. Dahil edilecek güncelleştirmeler ve dışlanması gerekenler ayrı sekmelerde bulunur. Ekleme hakkında daha fazla bilgi için bkz. [güncelleştirme dağıtımı zamanlama](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment). |
|**Zamanlama ayarları**|Başlatılacak saati seçin ve **bir** veya **tekrardan**birini seçin.|
| **Ön betikler + betikleri sonrası**|Dağıtımdan önce ve sonra çalıştırılacak betikleri seçin.|
| **Bakım penceresi** | Güncelleştirmeler için ayarlanan dakika sayısını girin. Geçerli değerler 30 ila 360 dakika arasındadır. |
| **Yeniden başlatma denetimi**| Yeniden başlatmaları nasıl ele alındığını seçin. Kullanılabilir seçimler şunlardır:<ul><li>**Gerekirse yeniden Başlat**</li><li>**Her zaman yeniden Başlat**</li><li>**Hiçbir şekilde yeniden başlatma**</li><li>**Yalnızca yeniden Başlat**</li></ul>**Gerekirse yeniden Başlat** varsayılan seçimdir. **Yalnızca yeniden başlatma**' yı seçerseniz güncelleştirmeler yüklenmez.|

Zamanlamayı yapılandırmayı tamamladıktan sonra, durum panosuna dönmek için **Oluştur** ' a tıklayın. **Zamanlanan** tablo, oluşturduğunuz dağıtım zamanlamasını gösterir.

Ayrıca, programlama yoluyla güncelleştirme dağıtımları da oluşturabilirsiniz. REST API güncelleştirme dağıtımı oluşturmayı öğrenmek için bkz. [yazılım güncelleştirme yapılandırması-oluştur](/rest/api/automation/softwareupdateconfigurations/create). Ayrıca, haftalık güncelleştirme dağıtımı oluşturmak için kullanabileceğiniz örnek bir runbook da vardır. Bu runbook hakkında daha fazla bilgi edinmek için bkz. [bir kaynak grubundaki bir veya daha fazla VM için haftalık güncelleştirme dağıtımı oluşturma](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="view-results-of-an-update-deployment"></a>Güncelleştirme dağıtımının sonuçlarını görüntüleme

Zamanlanan dağıtım başladıktan sonra, dağıtım durumunu **güncelleştirme yönetimi** penceresinin **güncelleştirme dağıtımları** sekmesinde görebilirsiniz.

Dağıtım Şu anda çalışıyorsa, durumu "sürüyor" olarak gösterilir. Başarılı bir şekilde tamamlandıktan sonra durum "başarılı" olarak değişir. Ancak dağıtımdaki herhangi bir güncelleştirme başarısız olursa, durum "kısmen başarısız" olur.

Bu dağıtımın panosunu görmek için tamamlanan güncelleştirme dağıtımını seçin.

![Belirli bir dağıtım için Güncelleştirme Dağıtımı durum panosu](./media/tutorial-monitoring/manageupdates-view-results.png)

**Güncelleştirme sonuçları** kutucuğu, sanal makine üzerindeki toplam güncelleştirme sayısı ve dağıtım sonuçlarının özetini gösterir. Sağdaki tabloda her güncelleştirmenin ayrıntılı bir dökümü ve yükleme sonuçları gösterilir. Her sonuç aşağıdaki değerlerden birine sahiptir:

* **Denenmedi**: güncelleştirme yüklü değil. Tanımlanan bakım penceresi süresine göre yeterli zaman yok.
* **Başarılı**: Güncelleştirme başarılı oldu.
* **Başarısız**: Güncelleştirme başarısız oldu.

Dağıtımın oluşturduğu tüm günlük girişlerini görmek için **Tüm günlükler**’i seçin.

Hedef sanal makinede güncelleştirme dağıtımını yönetmekten sorumlu runbook 'un iş akışını görmek için **Çıkış** kutucuğunu seçin.

Dağıtım hatalarıyla ilgili ayrıntılı bilgileri görmek için **hatalar** ' ı seçin.

## <a name="monitor-changes-and-inventory"></a>Değişiklikleri ve sayımı izleme

Bilgisayarlarınızda yazılım, dosya, Linux Daemon 'ları, Windows Hizmetleri ve Windows kayıt defteri anahtarlarının envanterini toplayabilir ve görüntüleyebilirsiniz. Makinelerinizin yapılandırmalarının izlenmesi, ortamınızda işletimsel sorunları belirlemenize ve makinelerinizin durumunu daha iyi anlamanıza yardımcı olur.

### <a name="enable-change-and-inventory-management"></a>Değişiklik ve stok yönetimini etkinleştir

VM 'niz için değişiklik ve stok yönetimini etkinleştirmek için:

1. Pencerenin en sol tarafında, **sanal makineler**' i seçin.
1. Listeden bir VM seçin.
1. VM penceresinde **işlemler** ' in altında, **Envanter** veya **değişiklik izleme**' yi seçin.
1. **Değişiklik izleme etkinleştirme ve envanter** bölmesi açılır.

Kullanılacak konumu, Log Analytics çalışma alanını ve otomasyon hesabını yapılandırın ve ardından **Etkinleştir**' i seçin. Seçenekler soluk görünüyorsa, VM için zaten bir otomasyon çözümü etkinleştirilmiştir. Bu durumda, zaten etkinleştirilmiş çalışma alanı ve Otomasyon hesabı kullanılmalıdır.

Çözümler menüde ayrı olarak görünse de, aynı çözüm vardır. Biri etkinleştirildiğinde, sanal makinenizde her ikisi de etkinleştirilir.

![Değişiklik ve Sayım izlemeyi etkinleştirme](./media/tutorial-monitoring/manage-inventory-enable.png)

Çözüm etkinleştirildikten sonra, veriler görüntülenmeden önce VM 'de sayım toplanması biraz zaman alabilir.

### <a name="track-changes"></a>Değişiklikleri izleme

**İşlem**kapsamındaki sanal makinenizde **değişiklik izleme** seçin ve ardından **Ayarları Düzenle**' yi seçin. **Değişiklik izleme** bölmesi açılır. İzlemek istediğiniz ayar türünü seçin ve **+ Ekle**’yi seçerek ayarları yapılandırın.

Windows için kullanılabilir ayarlar seçenekleri şunlardır:

* Windows Kayıt Defteri
* Windows Dosyaları

Değişiklik İzleme hakkında ayrıntılı bilgi için bkz. [VM 'deki değişikliklerle Ilgili sorunları giderme](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Sayımı görüntüleme

Sanal makinenizde **İŞLEMLER** bölümünden **Sayım**’ı seçin. **Yazılım** sekmesinde, bulunan yazılımın gösterildiği bir tablo vardır. Her yazılım kaydı için üst düzey Ayrıntılar tabloda görüntülenir. Bu ayrıntılar yazılım adı, sürüm, yayımcı ve son yenilenme süresini içerir.

![Sayımı görüntüleme](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Etkinlik günlüklerini ve değişiklikleri izleme

VM 'nizin **değişiklik izleme** penceresinde, **Azure etkinlik günlüğü** bölmesini açmak Için **etkinlik günlüğü bağlantısını Yönet** ' i seçin. SANAL makinenizin Azure etkinlik günlüğüne Değişiklik İzleme bağlanmak için **Bağlan** ' ı seçin.

Değişiklik İzleme etkinleştirildikten sonra, sanal makinenizin **genel bakış** bölmesine gıdın ve VM 'nizi durdurmak için **Durdur** ' u seçin. Sorulduğunda **Evet**'i seçerek VM'yi durdurun. VM serbest bırakıldıktan sonra, sanal makineyi yeniden başlatmak için **Başlat** ' ı seçin.

Bir VM 'yi durdurma ve yeniden başlatma, etkinlik günlüğünde bir olay günlüğe kaydeder. **Değişiklik izleme** bölmesine dönün ve bölmenin altındaki **Olaylar** sekmesini seçin. Bir süre sonra, olaylar grafikte ve tabloda görüntülenir. Bu olayla ilgili ayrıntılı bilgileri görüntülemek için her bir olayı seçebilirsiniz.

![Etkinlik günlüğünde değişiklikleri görüntüleme](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Önceki grafik zaman içinde oluşan değişiklikleri gösterir. Bir Azure etkinlik günlüğü bağlantısı ekledikten sonra üstteki çizgi grafiğinde Azure etkinlik günlüğü olayları görüntülenir.

Çubuk grafiklerin her bir satırı, farklı bir rafa yerleştirilebilir değişiklik türünü temsil eder. Bu türler Linux Daemon 'ları, dosyalar, Windows kayıt defteri anahtarları, yazılımlar ve Windows hizmetlerdir. **Değişiklik** sekmesinde değişiklik ayrıntıları gösterilir. Değişiklikler her oluştuğunda, en son değişiklikle önce gösterilen şekilde görünür.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, VM 'niz için Değişiklik İzleme ve Güncelleştirme Yönetimi yapılandırdınız ve gözden geçirdiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Kaynak grubu ve VM oluşturma.
> * Windows güncelleştirmelerini yönetme.
> * Değişiklikleri ve stoku izleyin.

VM 'nizi izleme hakkında bilgi edinmek için sonraki Öğreticiye gidin.

> [!div class="nextstepaction"]
> [Sanal makineleri izleme](tutorial-monitor.md)
