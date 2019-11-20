---
title: Azure Izleyici ile karma Windows bilgisayarından veri toplama
description: Bu hızlı başlangıçta, Azure dışında çalışan Windows bilgisayarları için Log Analytics aracısını dağıtmayı ve Azure Izleyici günlükleri ile veri toplamayı etkinleştirmenizi öğreneceksiniz.
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
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 26919a64313df823dddad5dcfca6de5d08dcd993
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199035"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Azure Izleyici ile Karma ortamda bir Windows bilgisayarından veri toplama

[Azure izleyici](../overview.md) , ortamınızdaki fiziksel veya sanal Windows bilgisayarlarınızdan, ayrıntılı analiz ve bağıntı için bir Log Analytics çalışma alanına doğrudan veri toplayabilir. [Log Analytics aracısının](../platform/log-analytics-agent.md) yüklenmesi, Azure izleyici 'nin bir veri merkezinde veya diğer bulut ortamından veri toplamasına izin verir. Bu hızlı başlangıçta birkaç kolay adımda Windows bilgisayarınızı nasıl yapılandırabileceğiniz ve veri toplayabileceğiniz gösterilmektedir. Azure Windows VM 'Leri hakkında daha fazla bilgi için bkz. [Azure sanal makineler hakkında veri toplama](../../azure-monitor/learn/quick-collect-azurevm.md).  

Desteklenen yapılandırmayı anlamak için bkz. [desteklenen Windows işletim sistemleri](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) ve [ağ güvenlik duvarı yapılandırması](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).
 
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

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


## <a name="get-the-workspace-id-and-key"></a>Çalışma alanı KIMLIĞINI ve anahtarını al

Windows için Log Analytics aracısını (Microsoft Monitoring Agent (MMA) olarak da bilinir) yüklemeden önce, Log Analytics çalışma alanınız için çalışma alanı KIMLIĞI ve anahtarı gereklidir. Kurulum Sihirbazı, aracıyı doğru bir şekilde yapılandırmak ve Azure Izleyici ile iletişim kurabildiğinden emin olmak için bu bilgilere ihtiyaç duyuyor.  

1. Azure portal sol üst köşesinde **tüm hizmetler**' i seçin. Arama kutusuna **Log Analytics**girin. Siz yazarken, liste, girişinizi temel alarak filtreler. **Log Analytics çalışma alanlarını**seçin.

2. Log Analytics çalışma alanları listenizde, daha önce oluşturduğunuz çalışma alanını seçin. ( **Defaultlaworkspace**adında bir adlandırılmış olabilir.)

3. **Gelişmiş ayarları**seçin:

    ![Gelişmiş ayarları Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. **Bağlı Kaynaklar**’ı seçin ve ardından **Windows Sunucuları**’nı seçin.

5. Değerleri **çalışma alanı kimliği** ve **birincil anahtarın**sağına kopyalayın. Bunları en sevdiğiniz düzenleyiciye yapıştırın.

## <a name="install-the-agent-for-windows"></a>Windows için aracıyı yükleme

Aşağıdaki adımlar, Azure ve Azure Kamu 'daki Log Analytics için aracıyı yükler ve yapılandırır. Aracıyı bilgisayarınıza yüklemek için Microsoft Monitoring Agent Kurulum programını kullanacaksınız.

1. Önceki adımlardan devam etmek için, **Windows sunucuları** sayfasında, Indirmek Istediğiniz **Windows aracısını indir** sürümünü seçin. Windows işletim sisteminizin işlemci mimarisi için uygun sürümü seçin.

2. Aracıyı bilgisayarınıza yüklemek için Kurulum'u çalıştırın.

3. **Hoş Geldiniz** sayfasında, **İleri**’yi seçin.

4. **Lisans Koşulları** sayfasında, lisansı okuyun ve sonra **Kabul Ediyorum**’u seçin.

5. **Hedef Klasör** sayfasında, varsayılan yükleme klasörünü değiştirin veya aynı şekilde bırakın ve daha sonra **İleri**’yi seçin.

6. **Aracı kurulum seçenekleri** sayfasında, aracıyı Azure Log Analytics bağlayın ve sonra **İleri**' yi seçin.

7. **Azure Log Analytics** sayfasında, şu adımları uygulayın:

   1. Daha önce kopyaladığınız **çalışma alanı kimliğini** ve **çalışma alanı anahtarını (birincil anahtar)** yapıştırın. Bilgisayarın Azure Kamu 'da bir Log Analytics çalışma alanına rapor etmesi gerekiyorsa Azure **bulut** LISTESINDEN **Azure ABD kamu** ' yı seçin.  
   2. Bilgisayarın, Log Analytics hizmetiyle bir ara sunucu üzerinden iletişim kurması gerekiyorsa **Gelişmiş** seçeneğini belirleyip ara sunucunun URL’sini ve bağlantı noktası numarasını sağlayın. Proxy sunucunuz kimlik doğrulaması gerektiriyorsa, proxy sunucusu ile kimlik doğrulaması için Kullanıcı adını ve parolayı girin ve ardından **İleri**' yi seçin.  

8. Yapılandırma ayarlarını ekledikten sonra **İleri ' yi** seçin:

    ![Microsoft Monitoring Agent kurulum](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. **Yüklemeye Hazır** sayfasında, tercihlerinizi gözden geçirip **Yükle**’yi seçin.

10. **Yapılandırma başarıyla tamamlandı** sayfasında **son**' u seçin.

Yükleme ve Kurulum bittiğinde, Denetim Masası 'nda Microsoft Monitoring Agent görüntülenir. Yapılandırmanızı gözden geçirebilir ve aracının Log Analytics çalışma alanına bağlı olduğunu doğrulayabilirsiniz. Bağlandığınızda, **Azure Log Analytics** sekmesinde aracı şu iletiyi görüntüler: **Microsoft Monitoring Agent Microsoft Log Analytics hizmetine başarıyla bağlandı.**<br><br> ![MMA bağlantı durumu](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Olay ve performans verilerini toplama

Azure Izleyici, daha uzun süreli analiz ve raporlama için Windows olay günlüğü ve performans sayaçlarından belirttiğiniz olayları toplayabilir. Ayrıca, belirli bir koşul algıladığında eylem gerçekleştirebilir. Windows olay günlüğünden olayları toplamayı yapılandırmak ve birkaç ortak performans sayacı ile başlamak için bu adımları izleyin.  

1. Azure portal sol alt köşesinde **diğer hizmetler**' i seçin. Arama kutusuna **Log Analytics**girin. Siz yazarken, liste, girişinizi temel alarak filtreler. **Log Analytics çalışma alanlarını**seçin.

2. **Gelişmiş ayarları**seçin:

    ![Gelişmiş ayarları Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. **Veri**’yi seçin ve ardından **Windows Olay Günlükleri**’ni seçin.  

4. Günlüğün adını girerek bir olay günlüğü eklersiniz. **Sistemi**girin ve ardından artı işaretini ( **+** ) seçin.  

5. Tabloda **hata** ve **Uyarı** önem derecesi ' ni seçin.

6. Sayfanın üst kısmındaki **Kaydet** ' i seçin.

7. Bir Windows bilgisayarda performans sayaçlarını toplamayı etkinleştirmek için **Windows performans sayaçlarını** seçin.

8. Yeni bir Log Analytics çalışma alanı için Windows performans sayaçlarını ilk kez yapılandırdığınızda, birkaç ortak sayacı hızlı bir şekilde oluşturma seçeneği sunulur. Her seçenek, yanındaki onay kutusuyla birlikte listelenir:

    ![Windows performans sayaçları](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    **Seçili performans sayaçlarını Ekle**' yi seçin. Sayaçlar eklenen ve on saniyelik bir koleksiyon örnek aralığıyla önceden ayarlanmış.

9. Sayfanın üst kısmındaki **Kaydet** ' i seçin.

## <a name="view-collected-data"></a>Toplanan verileri görüntüle

Artık veri toplamayı etkinleştirdiğinizden, hedef bilgisayardan bazı verileri görmek için basit bir günlük araması çalıştıralım.  

1. Seçilen çalışma alanında, sol bölmedeki **Günlükler**' i seçin.

2. Günlükler sorgusu sayfasında sorgu Düzenleyicisi ' ne `Perf` yazın ve **Çalıştır**' ı seçin.
 
    ![Log Analytics günlük araması](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Örneğin, bu görüntüdeki sorgu 10.000 performans kaydı döndürdü. Sonuçlarınız önemli ölçüde daha az olacaktır.

    ![Log Analytics günlük araması sonucu](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Aracıyı bilgisayarınızdan kaldırabilir ve artık ihtiyacınız yoksa Log Analytics çalışma alanını silebilirsiniz.  

Aracıyı kaldırmak için şu adımları izleyin:

1. Denetim Masası 'Nı açın.

2. **Programlar ve Özellikler**'i açın.

3. **Programlar ve Özellikler**' de **Microsoft Monitoring Agent** seçin ve ardından **Kaldır**' ı seçin.

Daha önce oluşturduğunuz Log Analytics çalışma alanını silmek için, seçin ve kaynak sayfasında **Sil**' i seçin:

![Log Analytics çalışma alanını sil](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık Windows bilgisayarınızdan işletimsel ve performans verileri topladığınıza göre, topladığınız verileri *ücretsiz*olarak keşfetmeye, analiz etmenize ve üzerinde işlem yapmaya başlayabilirsiniz.  

Verileri görüntüleme ve çözümleme hakkında bilgi edinmek için öğreticiye geçin:

> [!div class="nextstepaction"]
> [Log Analytics’te verileri görüntüleme veya analiz etme](tutorial-viewdata.md)
