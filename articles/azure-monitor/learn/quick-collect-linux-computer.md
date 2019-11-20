---
title: 'Hızlı başlangıç: Azure Izleyici ile karma bir Linux bilgisayardan veri toplama'
description: Bu hızlı başlangıçta, Azure dışında çalışan Linux bilgisayarları için Log Analytics aracısını dağıtmayı ve Azure Izleyici günlükleri ile veri toplamayı etkinleştirmenizi öğreneceksiniz.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: magoedte
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 959f36107ab9f79d4e66cc23b0744f1dbb8b2690
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677958"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Hızlı başlangıç: Azure Izleyici ile Karma ortamda bir Linux bilgisayarından veri toplama

[Azure izleyici](../overview.md) , ayrıntılı analiz ve bağıntı için ortamınızdaki doğrudan fiziksel veya sanal Linux bilgisayarlarınızdaki verileri bir Log Analytics çalışma alanına toplayabilir. [Log Analytics aracısının](../platform/log-analytics-agent.md) yüklenmesi, Azure izleyici 'nin bir veri merkezinde veya diğer bulut ortamından veri toplamasına izin verir. Bu hızlı başlangıçta, birkaç kolay adımla Linux sunucunuzdaki verilerin nasıl yapılandırılacağı ve toplanacağı gösterilmektedir. Azure Linux VM 'Leri hakkında daha fazla bilgi için bkz. [Azure sanal makineler hakkında veri toplama](../../azure-monitor/learn/quick-collect-azurevm.md).  

Desteklenen yapılandırmayı anlamak için bkz. [desteklenen Windows işletim sistemleri](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) ve [ağ güvenlik duvarı yapılandırması](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).
 
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın. 

## <a name="create-a-workspace"></a>Çalışma alanı oluşturma

1. Azure portalda **Tüm hizmetler**’i seçin. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics çalışma alanlarını**seçin.

    ![Azure portal Log Analytics çalışma alanını bulma](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. **Oluştur**' u seçin ve sonra aşağıdaki öğeler için Seçenekler ' i seçin:

   * Yeni **Log Analytics çalışma alanı** için *DefaultLAWorkspace* gibi bir ad sağlayın.  
   * Varsayılan seçili abonelik uygun değilse açılan listeden bağlanacak bir **Abonelik** seçin.
   * **Kaynak Grubu** için, bir veya daha fazla Azure sanal makinesi içeren mevcut bir kaynak grubunu seçin.  
   * VM’lerinizin dağıtıldığı **Konum**’u seçin.  Ek bilgi için bkz. [Log Analytics’in sunulduğu bölgeler](https://azure.microsoft.com/regions/services/).
   * 2 Nisan 2018 tarihinden sonra oluşturulan yeni bir abonelikte çalışma alanı oluşturuyorsanız bu, otomatik olarak *GB başına* fiyatlandırma planını kullanır ve fiyatlandırma katmanı seçme seçeneği kullanılamaz.  2 Nisan’dan önce oluşturulmuş mevcut bir abonelik için veya mevcut bir EA kaydına bağlı aboneliğe yönelik çalışma alanı oluşturuyorsanız, tercih ettiğiniz fiyatlandırma katmanını seçin.  Katmanlar hakkında daha fazla bilgi için bkz. [Log Analytics Fiyatlandırma Ayrıntıları](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Azure portal Log Analytics çalışma alanı oluşturma](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. **Log Analytics çalışma alanı** bölmesinde gerekli bilgileri sağladıktan sonra **Tamam**' ı seçin.  

Bilgilerin doğrulanıp çalışma alanının oluşturulması sırasında işlemin ilerleme durumunu menüdeki **Bildirimler**’in altından izleyebilirsiniz. 

## <a name="obtain-workspace-id-and-key"></a>Çalışma alanı kimliği ve anahtarını alma

Linux için Log Analytics aracısını yüklemeden önce, Log Analytics çalışma alanınızın kimliği ve anahtarına ihtiyacınız olacak.  Aracıyı düzgün bir şekilde yapılandırmak ve Azure Izleyici ile başarılı bir şekilde iletişim kurabildiğinden emin olmak için aracı sarmalayıcı betiği tarafından bu bilgiler gereklidir.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Azure portal sol üst köşesinde **tüm hizmetler**' i seçin. Arama kutusuna **Log Analytics**girin. Siz yazarken, liste, girişinizi temel alarak filtreler. **Log Analytics çalışma alanlarını**seçin.

2. Log Analytics çalışma alanları listenizde, daha önce oluşturduğunuz çalışma alanını seçin. ( **Defaultlaworkspace**adında bir adlandırılmış olabilir.)

3. **Gelişmiş ayarları**seçin:

    ![Azure portal Log Analytics için Gelişmiş ayarlar menüsü](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. **Bağlı Kaynaklar**’ı seçin ve ardından **Linux Sunucuları**’nı seçin.

5. **Çalışma Alanı Kimliği** ve **Birincil Anahtar**’ın sağındaki değer. Her ikisini de kopyalayıp sık kullandığınız bir düzenleyiciye yapıştırın.

## <a name="install-the-agent-for-linux"></a>Linux için aracıyı yükleme

Aşağıdaki adımlar Azure’da ve Azure Kamu bulutunda Log Analytics için aracıyı yapılandırır.  

>[!NOTE]
>Linux için Log Analytics aracısı birden fazla Log Analytics çalışma alanına raporlamak için yapılandırılamaz.  

Linux bilgisayarınızın bir ara sunucu üzerinden Log Analytics’le iletişim kurması gerekiyorsa, ara sunucu yapılandırması komut satırında `-p [protocol://][user:password@]proxyhost[:port]` eklenerek belirtilebilir.  *proxyhost* özelliği, ara sunucunun tam etki adı alanı veya IP adresini kabul eder. 

Örneğin, `https://user01:password@proxy01.contoso.com:30443`

1. Linux bilgisayarı bir Log Analytics çalışma alanına bağlanacak şekilde yapılandırmak için, daha önce kopyalanmış çalışma alanı KIMLIĞI ve birincil anahtar sağlamak üzere aşağıdaki komutu çalıştırın. Bu komut aracıyı indirir, sağlama toplamını doğrular ve aracıyı yükler. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Aşağıdaki komut, `-p` ara sunucu parametresini ve örnek söz dizimini içerir.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Linux bilgisayarı Azure Kamu Bulutu 'nda Log Analytics çalışma alanına bağlanacak şekilde yapılandırmak için, daha önce kopyalanmış çalışma alanı KIMLIĞI ve birincil anahtar sağlamak üzere aşağıdaki komutu çalıştırın. Bu komut aracıyı indirir, sağlama toplamını doğrular ve aracıyı yükler. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Aşağıdaki komut, `-p` ara sunucu parametresini ve örnek söz dizimini içerir.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Aşağıdaki komutu çalıştırarak aracıyı yeniden başlatın: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Olay ve performans verilerini toplama

Azure Izleyici, daha uzun süreli analiz ve raporlama için belirttiğiniz Linux Syslog ve performans sayaçlarından olayları toplayabilir. Ayrıca, belirli bir koşul algıladığında eylem gerçekleştirebilir. Linux Syslog’dan olayları toplamayı yapılandırmak ve birkaç ortak performans sayacı ile başlamak için bu adımları izleyin.  

1. Azure portal sol alt köşesinde **diğer hizmetler**' i seçin. Arama kutusuna **Log Analytics**girin. Siz yazarken, liste, girişinizi temel alarak filtreler. **Log Analytics çalışma alanlarını**seçin.

2. **Veri**' yi seçin ve **Syslog**' ı seçin.  

3. Günlüğün adını yazarak Syslog eklersiniz. **Syslog** girin ve ardından artı işareti **+** seçin.  

4. Tabloda, **Bilgiler**, **Bildirim** ve **Hata Ayıklama** önem derecelerinin işaretini kaldırın. 

5. Yapılandırmayı kaydetmek için sayfanın en üstünde bulunan **Kaydet** ' i seçin.

6. Bir Linux bilgisayarda performans sayaçlarını toplamayı etkinleştirmek için **Linux Performans Verileri**’ni seçin. 

7. Yeni bir Log Analytics çalışma alanı için Linux Performans sayaçlarını ilk kez yapılandırırken, birkaç ortak sayacı hızlı bir şekilde oluşturma seçenekleri sunulur. Her birinin yanında bir onay kutusu görüntülenir.

    ![Azure Izleyici 'de varsayılan Linux performans sayaçları seçildi](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    **Aşağıdaki yapılandırmayı makinelerime Uygula** ' yı seçin ve ardından **Seçili performans sayaçlarını Ekle**' yi seçin. Eklenir ve on saniye koleksiyon örnek aralığı ile ayarlanır.  

8. Yapılandırmayı kaydetmek için sayfanın en üstünde bulunan **Kaydet** ' i seçin.

## <a name="view-data-collected"></a>Toplanan verileri görüntüleyin

Veri toplamayı etkinleştirdiyseniz, şimdi hedef bilgisayardan verileri görmek için basit bir günlük araması örneği çalıştıralım.  

1. Seçilen çalışma alanında, sol bölmedeki **Günlükler**' i seçin.

2. Günlükler sorgusu sayfasında sorgu Düzenleyicisi ' ne `Perf` yazın ve **Çalıştır**' ı seçin.
 
    ![Log Analytics günlük araması](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Örneğin, aşağıdaki görüntüdeki sorgu 10.000 performans kaydı döndürdü. Sonuçlarınız önemli ölçüde daha az olacaktır.

    ![Log Analytics günlük araması sonucu](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında, aracıyı Linux bilgisayardan kaldırıp Log Analytics çalışma alanını silebilirsiniz.  

Aracıyı kaldırmak için Linux bilgisayarında aşağıdaki komutu çalıştırın. *--temizleme* bağımsız değişkeni, aracıyı ve yapılandırmasını tamamen kaldırır.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Çalışma alanını silmek için, daha önce oluşturduğunuz Log Analytics çalışma alanını seçin ve kaynak sayfasında **Sil**' i seçin.

![Log Analytics kaynağını silme](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Sonraki adımlar

Şimdi şirket içi Linux bilgisayarınızdan işletimsel verileri ve performans verilerini topluyorsunuz ve *ücretsiz* olarak topladığınız verileri kolayca keşfetmeye, analiz etmeye ve verilerde işlem gerçekleştirmeye başlayabilirsiniz.  

Verileri görüntüleme ve analiz etmeyi öğrenmek için, öğreticiye devam edin.

> [!div class="nextstepaction"]
> [Log Analytics’te verileri görüntüleme veya analiz etme](../../azure-monitor/learn/tutorial-viewdata.md)
