---
title: VM'ler için Azure İzleyici ile uygulama bağımlılıklarını görüntüleme (Önizleme)
description: Map bir VM'ler için Azure İzleyici özelliğidir. Windows ve Linux sistemlerindeki uygulama bileşenlerini otomatik olarak bulur ve hizmetler arasındaki iletişimi eşler. Bu makalede, farklı senaryolarda harita özelliğinin nasıl kullanılacağına ilişkin ayrıntılar sağlanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 7ca79b0df55dbfea6a0e2c016773f9f32bf29667
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199084"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>Uygulama bileşenlerini anlamak için VM'ler için Azure İzleyici (Önizleme) eşleme özelliğini kullanın
VM'ler için Azure İzleyici, Azure 'da veya ortamınızda çalışan Windows ve Linux sanal makinelerinde (VM 'Ler) bulunan uygulama bileşenlerini görüntüleyebilirsiniz. VM 'Leri iki şekilde gözlemleyebilirsiniz. VM grupları arasında bileşenleri görmek için bir eşlemeyi doğrudan bir VM 'den veya Azure Izleyici 'den görüntüleyin. Bu makale, bu iki görüntüleme yöntemini ve Map özelliğinin nasıl kullanılacağını anlamanıza yardımcı olur. 

VM'ler için Azure İzleyici yapılandırma hakkında daha fazla bilgi için bkz. [VM'ler için Azure izleyici etkinleştirme](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Azure'da oturum açın
[Azure Portal](https://portal.azure.com)’ında oturum açın.

## <a name="introduction-to-the-map-experience"></a>Harita deneyimine giriş
Harita deneyimine girmeden önce, bilgileri nasıl sunduklarını ve görselleştirir. Eşleme özelliğini doğrudan bir VM 'den veya Azure Izleyici 'den seçip harita özelliği tutarlı bir deneyim sunar. Tek fark Azure Izleyici 'den, bir haritanın çok katmanlı bir uygulamanın veya kümenin tüm üyelerini gösterdiği.

Map özelliği, şu özelliklere sahip çalışan işlemlerin keşfederek VM bağımlılıklarını görselleştirir: 

- Sunucular arasındaki etkin ağ bağlantıları.
- Gelen ve giden bağlantı gecikmesi.
- Belirli bir zaman aralığı boyunca herhangi bir TCP bağlantılı mimarideki bağlantı noktaları.  
 
İşlem ayrıntılarını ve yalnızca VM ile iletişim kuran işlemleri göstermek için bir VM 'yi genişletin. İstemci grubu, VM 'ye bağlanan ön uç istemcilerinin sayısını gösterir. Sunucu bağlantı noktası grupları, VM 'nin bağlandığı arka uç sunucularının sayısını gösterir. Bu bağlantı noktası üzerinden bağlanan sunucuların ayrıntılı listesini görmek için bir sunucu bağlantı noktası grubunu genişletin.  

VM 'yi seçtiğinizde, sağdaki **Özellikler** bölmesi VM 'nin özelliklerini gösterir. Özellikler, işletim sistemi tarafından bildirilen sistem bilgilerini, Azure VM 'nin özelliklerini ve bulunan bağlantıları özetleyen bir halka grafiği içerir. 

![Özellikler bölmesi](./media/vminsights-maps/properties-pane-01.png)

Bölmesinin sağ tarafında, VM 'nin Azure Izleyici 'ye gönderdiği verilerin bir listesini göstermek için **günlük olayları** ' nı seçin. Bu veriler, sorgulama için kullanılabilir.  Kayıt türü için sonuçları gördüğünüz **Günlükler** sayfasını açmak için herhangi bir kayıt türünü seçin. Ayrıca, VM 'ye karşı filtrelenmiş önceden yapılandırılmış bir sorgu görürsünüz.  

![Günlük olayları bölmesi](./media/vminsights-maps/properties-pane-logs-01.png)

**Günlükler** sayfasını kapatın ve **Özellikler** bölmesine dönün. Burada, VM durum ölçütleri uyarılarını görüntülemek için **Uyarılar** ' ı seçin. Map özelliği, seçili zaman aralığında seçili sunucu için uyarıları göstermek üzere Azure uyarıları ile tümleşir. Sunucu geçerli uyarılar için bir simge görüntüler ve **makine uyarıları** bölmesi uyarıları listeler. 

![Uyarılar bölmesi](./media/vminsights-maps/properties-pane-alerts-01.png)

Harita özelliğinin ilgili uyarıları görüntülemesini sağlamak için, belirli bir bilgisayara uygulanan bir uyarı kuralı oluşturun:

- Uyarıları bilgisayara (örneğin, **bilgisayar aralığı 1 dakikaya göre**) gruplandırmak için bir yan tümce ekleyin.
- Uyarının bir ölçümde temelini oluşturma.

Azure uyarıları ve uyarı kuralları oluşturma hakkında daha fazla bilgi için bkz. [Azure izleyici 'de birleştirilmiş uyarılar](../../azure-monitor/platform/alerts-overview.md).

Sağ üst köşede, **gösterge** seçeneği haritadaki sembolleri ve rolleri tanımlar. Haritalarınızı daha yakından gözden geçirin ve etrafında hareket ettirmek için sağ alt köşedeki yakınlaştırma denetimlerini kullanın. Yakınlaştırma düzeyini ayarlayabilir ve haritanın boyutunu sayfanın boyutuna uydurtırabilirsiniz.  

## <a name="connection-metrics"></a>Bağlantı ölçümleri
**Bağlantılar** BÖLMESI, TCP bağlantı noktası üzerinden VM 'den seçilen bağlantı için standart ölçümleri görüntüler. Ölçümler yanıt süresi, dakika başına istek sayısı, trafik performansı ve bağlantılar içerir.  

![Bağlantılar bölmesindeki ağ bağlantısı grafikleri](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Başarısız bağlantılar
Eşleme, işlem ve bilgisayar için başarısız bağlantıları gösterir. Kesik çizgili kırmızı çizgi, istemci sistemin bir işlem veya bağlantı noktasına ulaşmadığını gösterir. Bağımlılık aracısını kullanan sistemler için aracı başarısız bağlantı girişimlerini raporlar. Harita özelliği, bir bağlantı kurmasının başarısız olduğu TCP yuvalarını gözlemleyerek bir işlemi izler. Bu hata, bir güvenlik duvarının, istemci veya sunucudaki bir yanlış yapılandırmanın ya da kullanılamayan bir uzak hizmetin oluşmasına neden olabilir.

![Haritada başarısız olan bağlantı](./media/vminsights-maps/map-group-failed-connection-01.png)

Başarısız bağlantıların anlaşılması, sorun gidermenize, geçişin doğrulanması, güvenliğin çözümlenmesi ve hizmetin genel mimarisini anlamanıza yardımcı olabilir. Başarısız olan bağlantılar bazen zararsız olur, ancak genellikle bir sorunu işaret ederler. Bağlantılar, örneğin bir yük devretme ortamı aniden ulaşılamaz hale geldiğinde veya iki uygulama katmanı bir bulut geçişten sonra birbirleriyle iletişim kuramıyorsa başarısız olabilir.

### <a name="client-groups"></a>İstemci grupları
Haritada, istemci grupları eşlenen makineye bağlanan istemci makinelerini temsil eder. Tek bir istemci grubu, tek bir işlem veya makinenin istemcilerini temsil eder.

![Haritadaki bir istemci grubu](./media/vminsights-maps/map-group-client-groups-01.png)

İstemci grubundaki sistemlerin izlenen istemcilerini ve IP adreslerini görmek için grubunu seçin. Grubun içerikleri aşağıda gösterilir.  

![İstemci grubunun haritadaki IP adresleri listesi](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Grup izlenen ve izlenmeyen istemcileri içeriyorsa, istemcileri filtrelemek için grubun halka grafiğinin uygun bölümünü seçebilirsiniz.

### <a name="server-port-groups"></a>Sunucu-bağlantı noktası grupları
Sunucu-bağlantı noktası grupları, eşlenen makineden gelen bağlantıları olan sunuculardaki bağlantı noktalarını temsil eder. Grup, sunucu bağlantı noktasını ve bu bağlantı noktası ile bağlantı kurulan sunucu sayısı sayısını içerir. Tek tek sunucuları ve bağlantıları görmek için grubu seçin. 

![Haritadaki sunucu bağlantı noktası grubu](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Grup izlenen ve izlenmeyen sunucuları içeriyorsa, sunucuları filtrelemek için grubun halka grafiğinin uygun bölümünü seçebilirsiniz.

## <a name="view-a-map-from-a-vm"></a>Bir VM 'den harita görüntüleme 

VM'ler için Azure İzleyici doğrudan bir VM 'den erişmek için:

1. Azure portal **sanal makineler**' i seçin. 
2. Listeden bir VM seçin. **İzleme** bölümünde Öngörüler ' i **(Önizleme)** seçin.  
3. **Harita** sekmesini seçin.

Eşleme, belirli bir zaman aralığı boyunca etkin ağ bağlantılarına sahip çalışan işlem gruplarını ve işlemleri bularak VM 'nin bağımlılıklarını görselleştirir.  

Varsayılan olarak, haritada son 30 dakika gösterilir. Bağımlılıkların geçmişte nasıl arandığı hakkında daha fazla bilgi almak istiyorsanız, geçmiş zaman aralıklarını bir saate kadar sorgulayabilirsiniz. Sorguyu çalıştırmak için sol üst köşedeki **Timerange** seçiciyi kullanın. Örneğin, bir olay sırasında veya bir değişiklikten önceki durumu görmek için bir sorgu çalıştırabilirsiniz.  

![Doğrudan VM eşlemesine genel bakış](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesinden Haritayı görüntüle

Bir sanal makine ölçek kümesinden VM'ler için Azure İzleyici doğrudan erişmek için:

1. Azure portal, **Sanal Makine Ölçek Kümeleri**' ni seçin.
2. Listeden bir VM seçin. Sonra **izleme** bölümünde Öngörüler ' i **(Önizleme)** seçin.  
3. **Harita** sekmesini seçin.

Eşleme, ölçek kümesindeki tüm örnekleri grubun bağımlılıklarıyla birlikte Grup düğümü olarak görselleştirir. Genişletilen düğüm, ölçek kümesindeki örnekleri listeler. Tek seferde bu örnek 10 ' da gezinebilirsiniz. 

Belirli bir örnek için bir eşleme yüklemek üzere öncelikle haritada bu örneği seçin. Ardından sağdaki **üç nokta** düğmesini (...) seçin ve **sunucu haritasını yükle**' yi seçin. Görüntülenen haritada, belirli bir zaman aralığı üzerinde etkin ağ bağlantılarına sahip işlem gruplarını ve işlemleri görürsünüz. 

Varsayılan olarak, haritada son 30 dakika gösterilir. Bağımlılıkların geçmişte nasıl arandığı hakkında daha fazla bilgi almak istiyorsanız, geçmiş zaman aralıklarını bir saate kadar sorgulayabilirsiniz. Sorguyu çalıştırmak için **Timerange** seçiciyi kullanın. Örneğin, bir olay sırasında veya bir değişiklikten önceki durumu görmek için bir sorgu çalıştırabilirsiniz.

![Doğrudan VM eşlemesine genel bakış](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Ayrıca, sanal makine ölçek kümesi için **örnekler** görünümünden belirli bir örnek için bir eşlemeye erişebilirsiniz. **Ayarlar** bölümünde, **örnekler**  > **Öngörüler (Önizleme)** bölümüne gidin.

## <a name="view-a-map-from-azure-monitor"></a>Azure Izleyici 'den harita görüntüleme

Azure Izleyici 'de, eşleme özelliği sanal makinelerinizin ve bağımlılıklarınızın genel bir görünümünü sağlar. Azure Izleyici 'deki harita özelliğine erişmek için:

1. Azure portal, **İzle**' yi seçin. 
2. **Öngörüler** bölümünde **sanal makineler (Önizleme)** öğesini seçin.
3. **Harita** sekmesini seçin.

   ![Birden çok VM 'nin Azure Izleyici 'ye Genel Bakış Haritası](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Sayfanın üst kısmındaki **çalışma alanı** seçicisini kullanarak bir çalışma alanı seçin. Birden fazla Log Analytics çalışma alanınız varsa, Çözümle etkinleştirilmiş ve VM 'Leri raporlayan çalışma alanını seçin. 

**Grup** Seçicisi, seçili çalışma alanıyla ilgili olan abonelikleri, kaynak gruplarını, [bilgisayar gruplarını](../../azure-monitor/platform/computer-groups.md)ve sanal makine ölçek kümelerini döndürür. Seçiminiz yalnızca eşleme özelliği için geçerlidir ve performans ya da sistem durumunu etkilemez.

Varsayılan olarak, haritada son 30 dakika gösterilir. Bağımlılıkların geçmişte nasıl arandığı hakkında daha fazla bilgi almak istiyorsanız, geçmiş zaman aralıklarını bir saate kadar sorgulayabilirsiniz. Sorguyu çalıştırmak için **Timerange** seçiciyi kullanın. Örneğin, bir olay sırasında veya bir değişiklikten önceki durumu görmek için bir sorgu çalıştırabilirsiniz.  

## <a name="next-steps"></a>Sonraki adımlar

Performans sorunlarını belirlemek, performansı denetlemek ve sanal makinelerinizin genel kullanımını anlamak için bkz. [VM'ler için Azure izleyici için performans durumunu görüntüleme](vminsights-performance.md). 
