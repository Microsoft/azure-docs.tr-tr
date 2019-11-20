---
title: Azure Izleyici ile Azure sanal makinesinden veri toplama | Microsoft Docs
description: Log Analytics Aracısı VM Uzantısını etkinleştirmeyi ve Log Analytics ile Azure VM’lerinizden veri toplamayı etkinleştirmeyi öğrenin.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: quickstart
author: mgoedtel
ms.author: magoedte
ms.date: 08/19/2019
ms.custom: mvc, sec-javascript-october2019
ms.openlocfilehash: 84b076cf92c57f3cd912a6c4b401a5ef81f8557b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900548"
---
# <a name="collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>Azure Izleyici ile Azure sanal makinesinden veri toplama

[Azure izleyici](../overview.md) , ayrıntılı analiz ve bağıntı için Azure sanal makinelerinizden doğrudan bir Log Analytics çalışma alanına veri toplayabilir. [Windows](../../virtual-machines/extensions/oms-windows.md) ve [LINUX](../../virtual-machines/extensions/oms-linux.md) için Log Analytics VM uzantısının yüklenmesi, Azure izleyici 'nin Azure sanal makinelerinizden veri toplamasına izin verir. Bu hızlı başlangıçta, birkaç kolay adımla VM uzantısını kullanarak Azure Linux veya Windows sanal makinelerinizdeki verileri nasıl yapılandıracağınız ve toplayacağınız gösterilmektedir.  
 
Bu hızlı başlangıçta mevcut bir Azure sanal makinenizin olduğu varsayılmaktadır. Yoksa VM hızlı başlangıçlarımızı izleyerek bir [Windows VM](../../virtual-machines/windows/quick-create-portal.md) veya bir [Linux VM](../../virtual-machines/linux/quick-create-cli.md) oluşturabilirsiniz.

## <a name="sign-in-to-azure-portal"></a>Azure portalında oturum açın

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın. 

## <a name="create-a-workspace"></a>Çalışma alanı oluşturma

1. Azure portalda **Tüm hizmetler**’i seçin. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics çalışma alanlarını**seçin.

    ![Azure portalı](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. **Oluştur**' u seçin ve sonra aşağıdaki öğeler için Seçenekler ' i seçin:

   * Yeni **Log Analytics çalışma alanı** için *DefaultLAWorkspace* gibi bir ad sağlayın.  
   * Varsayılan seçili abonelik uygun değilse açılan listeden bağlanacak bir **Abonelik** seçin.
   * **Kaynak Grubu** için, bir veya daha fazla Azure sanal makinesi içeren mevcut bir kaynak grubunu seçin.  
   * VM’lerinizin dağıtıldığı **Konum**’u seçin.  Ek bilgi için bkz. [Log Analytics’in sunulduğu bölgeler](https://azure.microsoft.com/regions/services/).
   * 2 Nisan 2018 tarihinden sonra oluşturulan yeni bir abonelikte çalışma alanı oluşturuyorsanız bu, otomatik olarak *GB başına* fiyatlandırma planını kullanır ve fiyatlandırma katmanı seçme seçeneği kullanılamaz.  2 Nisan’dan önce oluşturulmuş mevcut bir abonelik için veya mevcut bir EA kaydına bağlı aboneliğe yönelik çalışma alanı oluşturuyorsanız, tercih ettiğiniz fiyatlandırma katmanını seçin.  Katmanlar hakkında daha fazla bilgi için bkz. [Log Analytics Fiyatlandırma Ayrıntıları](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Log Analytics kaynak dikey penceresi oluşturma](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. **Log Analytics çalışma alanı** bölmesinde gerekli bilgileri sağladıktan sonra **Tamam**' ı seçin.  

Bilgilerin doğrulanıp çalışma alanının oluşturulması sırasında işlemin ilerleme durumunu menüdeki **Bildirimler**’in altından izleyebilirsiniz. 

## <a name="enable-the-log-analytics-vm-extension"></a>Log Analytics VM Uzantısını etkinleştirme

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Zaten Azure’da dağıtılan Windows ve Linux sanal makineler için, Log Analytics aracısını Log Analytics VM Uzantısı ile yüklersiniz. Uzantıyı kullanmak yükleme işlemini kolaylaştırır ve aracıyı belirttiğiniz Log Analytics çalışma alanına veri göndermek üzere otomatik olarak yapılandırır. Aracı Ayrıca yeni bir sürüm yayınlandığında otomatik olarak yükseltilir ve en son özelliklere ve düzeltmelere sahip olmanızı sağlar. Devam etmeden önce, VM 'nin çalıştığını doğrulayın, aksi takdirde işlem başarıyla tamamlanacaktır.  

>[!NOTE]
>Linux için Log Analytics aracısı birden fazla Log Analytics çalışma alanına raporlamak için yapılandırılamaz. 

1. Azure portal sol üst köşede bulunan **tüm hizmetler** ' i seçin. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics çalışma alanlarını**seçin.

2. Log Analytics çalışma alanlarınızın listesinde, daha önceden oluşturduğunuz *DefaultLAWorkspace* çalışma alanını seçin.

3. Sol taraftaki menüde, çalışma alanı veri kaynakları altında, **sanal makineler**' i seçin.  

4. **Sanal makine** listesinden aracıyı yüklemek istediğiniz bir sanal makine seçin. VM için **Log Analytics bağlantı durumunun** **Bağlı değil** olduğuna dikkat edin.

5. Sanal makinenizin ayrıntılarında, **Bağlan**’ı seçin. Aracı otomatik olarak yüklenir ve Log Analytics çalışma alanınız için yapılandırılır. Bu işlem birkaç dakika sürer. bu süre, **durumun** **bağlantı**gösterdiği zamanı gösterir.

6. Aracıyı yükleyip bağlandıktan sonra, **Log Analytics bağlantı durumu** **Bu çalışma alanı** olarak güncelleştirilir.

## <a name="collect-event-and-performance-data"></a>Olay ve performans verilerini toplama

Azure Izleyici, daha uzun süreli analiz ve raporlama için belirttiğiniz Windows olay günlüklerinden veya Linux Syslog ve performans sayaçlarından olayları toplayabilir ve belirli bir koşul algılandığında işlem yapabilir. Windows sistem günlüğü ve Linux Syslog’dan olayları toplamayı yapılandırmak ve birkaç ortak performans sayacı ile başlamak için bu adımları izleyin.  

### <a name="data-collection-from-windows-vm"></a>Windows VM’den veri toplama

1. **Gelişmiş ayarlar**’ı seçin.

    ![Log Analytics Gelişmiş Ayarlar](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

2. **Veri**’yi seçin ve ardından **Windows Olay Günlükleri**’ni seçin.

3. Bir olay günlüğü eklemek için günlüğün adını yazın.  **System** yazın ve ardından artı işareti **+** seçin.

4. Tabloda, **Hata** ve **Uyarı** önem derecelerini işaretleyin.

5. Yapılandırmayı kaydetmek için sayfanın en üstünde bulunan **Kaydet** ' i seçin.

6. Bir Windows bilgisayarda performans sayaçlarını toplamayı etkinleştirmek için **Windows Performans Verileri**’ni seçin.

7. Yeni bir Log Analytics çalışma alanı için Windows Performans sayaçlarını ilk kez yapılandırırken, birkaç ortak sayacı hızlı bir şekilde oluşturma seçenekleri sunulur. Her birinin yanında bir onay kutusu görüntülenir.

    ![Varsayılan Windows performans sayaçları seçildi](media/quick-collect-azurevm/windows-perfcounters-default.png)

    **Seçili performans sayaçlarını Ekle**' yi seçin.  Eklenir ve on saniye koleksiyon örnek aralığı ile ayarlanır.
  
8. Yapılandırmayı kaydetmek için sayfanın en üstünde bulunan **Kaydet** ' i seçin.

### <a name="data-collection-from-linux-vm"></a>Linux VM’den veri toplama

1. **Syslog**’u seçin.  

2. Bir olay günlüğü eklemek için günlüğün adını yazın.  **Syslog** yazın ve ardından artı işareti **+** seçin.  

3. Tabloda, önem derecesi **bilgileri**, **bildirim** ve **hata ayıklama**seçimlerini kaldırın. 

4. Yapılandırmayı kaydetmek için sayfanın en üstünde bulunan **Kaydet** ' i seçin.

5. Bir Linux bilgisayarda performans sayaçlarını toplamayı etkinleştirmek için **Linux Performans Verileri**’ni seçin. 

6. Yeni bir Log Analytics çalışma alanı için Linux Performans sayaçlarını ilk kez yapılandırırken, birkaç ortak sayacı hızlı bir şekilde oluşturma seçenekleri sunulur. Her birinin yanında bir onay kutusu görüntülenir.

    ![Varsayılan Windows performans sayaçları seçildi](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    **Aşağıdaki yapılandırmayı makinelerime Uygula** ' yı seçin ve ardından **Seçili performans sayaçlarını Ekle**' yi seçin.  Eklenir ve on saniye koleksiyon örnek aralığı ile ayarlanır.  

7. Yapılandırmayı kaydetmek için sayfanın en üstünde bulunan **Kaydet** ' i seçin.

## <a name="view-data-collected"></a>Toplanan verileri görüntüleyin

Veri toplamayı etkinleştirdiyseniz, şimdi hedef VM’lerden verileri görmek için basit bir günlük araması örneği çalıştıralım.  

1. Seçilen çalışma alanında, sol bölmedeki **Günlükler**' i seçin.

2. Günlükler sorgusu sayfasında sorgu Düzenleyicisi ' ne `Perf` yazın ve **Çalıştır**' ı seçin.

    ![Log Analytics günlük araması sorgu örneği](./media/quick-collect-windows-computer/log-analytics-portal-queryexample.png) 

    Örneğin, aşağıdaki görüntüdeki sorgu 10.000 performans kaydı döndürdü. Sonuçlarınız önemli ölçüde daha az olacaktır.

    ![Log Analytics günlük araması sonucu](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında, Log Analytics çalışma alanını silin. Bunu yapmak için, daha önce oluşturduğunuz Log Analytics çalışma alanını seçin ve kaynak sayfasında **Sil**' i seçin.


![Log Analytics kaynağını silme](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Sonraki adımlar

Şimdi Windows veya Linux sanal makinelerinizden işletimsel verileri ve performans verilerini topluyorsunuz ve *ücretsiz* olarak topladığınız verileri kolayca keşfetmeye, analiz etmeye ve verilerde işlem gerçekleştirmeye başlayabilirsiniz.  

Verileri görüntüleme ve analiz etmeyi öğrenmek için, öğreticiye devam edin.

> [!div class="nextstepaction"]
> [Log Analytics’te verileri görüntüleme veya analiz etme](../../azure-monitor/learn/tutorial-viewdata.md)
