---
title: Bağlantı Izleyicisi oluşturma-Azure portal
titleSuffix: Azure Network Watcher
description: Bu makalede, Azure portal kullanarak bağlantı Izleyicisinde bir izleyicinin nasıl oluşturulacağı açıklanır.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: 9942b608601b495dd041a8b59aaecb3f5dd81fa7
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505238"
---
# <a name="create-a-monitor-in-connection-monitor-by-using-the-azure-portal"></a>Azure portal kullanarak bağlantı Izleyicisinde izleyici oluşturma

> [!IMPORTANT]
> 1 Temmuz 2021 ' den itibaren, mevcut bir çalışma alanına yeni testler ekleyemez veya Ağ Performansı İzleyicisi yeni bir çalışma alanı etkinleştiremeyeceksiniz. Ayrıca, bağlantı Izleyicisinde (klasik) yeni bağlantı izleyicileri ekleyemeyeceksiniz. 1 Temmuz 2021 ' den önce oluşturulan testleri ve bağlantı izleyicilerini kullanmaya devam edebilirsiniz. Geçerli iş yüklerinizde hizmet kesintisini en aza indirmek için, [testlerinizi ağ performansı İzleyicisi ](migrate-to-connection-monitor-from-network-performance-monitor.md) veya  [bağlantı izleyicisinden (klasik)](migrate-to-connection-monitor-from-connection-monitor-classic.md) Azure ağ Izleyicisi 'ndeki yeni bağlantı Izleyicisinden, 29 Şubat 2024 tarihinden önce geçirin.

Kaynaklarınız arasındaki iletişimi izlemek için bağlantı Izleyicisini nasıl kullanacağınızı öğrenin. Bu makalede, Azure portal kullanarak nasıl bir izleyici oluşturacağınız açıklanır. Bağlantı Izleyicisi karma ve Azure bulut dağıtımlarını destekler.


## <a name="before-you-begin"></a>Başlamadan önce 

Bağlantı Izleyicisi 'ni kullanarak oluşturduğunuz bağlantı izleyicilerinde, hem şirket içi makineleri hem de Azure VM 'lerini kaynak olarak ekleyebilirsiniz. Bu bağlantı izleyicileri, uç noktalara bağlantıyı da izleyebilir. Uç noktalar Azure 'da veya başka bir URL veya IP üzerinde olabilir.

İşte başlamanıza yönelik bazı tanımlar:

* **Bağlantı İzleyicisi kaynağı**. Bölgeye özgü bir Azure kaynağı. Aşağıdaki varlıkların hepsi bir bağlantı İzleyicisi kaynağının özellikleridir.
* **Uç nokta**. Bağlantı denetimlerine katılan kaynak veya hedef. Uç nokta örnekleri şunlardır:
    * Azure VM 'Leri.
    * Azure sanal ağları.
    * Azure alt ağları.
    * Şirket içi aracılar.
    * Şirket içi alt ağlar.
    * Birden çok alt ağ içeren şirket içi özel ağlar.
    * URL 'Ler ve IP 'ler.
* **Test yapılandırması**. Bir test için protokole özgü bir yapılandırma. Seçtiğiniz protokole bağlı olarak, bağlantı noktası, eşikler, sınama sıklığı ve diğer parametreleri tanımlayabilirsiniz.
* **Test grubu**. Kaynak uç noktaları, hedef uç noktaları ve test yapılandırmalarının bulunduğu grup. Bir bağlantı İzleyicisi birden fazla test grubu içerebilir.
* **Test**. Kaynak uç noktası, hedef uç noktası ve test yapılandırmasının birleşimi. Test, izleme verilerinin kullanılabildiği en ayrıntılı düzeydir. İzleme verileri, başarısız olan denetim yüzdesini ve gidiş dönüş süresini (RTT) içerir.

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="Bir bağlantı izleyicisini gösteren ve test grupları ile testler arasındaki ilişkiyi tanımlayan diyagram.":::


## <a name="create-a-connection-monitor"></a>Bağlantı izleyicisi oluşturma

Azure portal kullanarak bağlantı Izleyicisinde bir izleyici oluşturmak için:

1. Azure portal giriş sayfasında **Ağ İzleyicisi**' ne gidin.
1. Sol bölmedeki **izleme** bölümünde **Bağlantı İzleyicisi**' ni seçin.

   Bağlantı Izleyicisinde oluşturulan tüm bağlantı izleyicilerini görürsünüz. Klasik bağlantı Izleyicisinde oluşturulan bağlantı izleyicilerini görmek için **Bağlantı İzleyicisi** sekmesine gidin.

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Bağlantı Izleyicisinde oluşturulan bağlantı izleyicilerini gösteren ekran görüntüsü.":::
   
    
1. **Bağlantı İzleyicisi** panosunda, sol üst köşedeki **Oluştur**' u seçin.

   

1. **Temel bilgiler** sekmesinde, bağlantı monitörünüz için bilgi girin: 
   * **Bağlantı Izleyicisi adı**: bağlantı monitörünüz için bir ad girin. Azure kaynakları için standart adlandırma kurallarını kullanın.
   * **Abonelik**: bağlantı monitörünüz için bir abonelik seçin.
   * **Bölge**: bağlantı monitörünüz için bir bölge seçin. Yalnızca bu bölgede oluşturulan kaynak VM 'Leri seçebilirsiniz.
   * **Çalışma alanı yapılandırması**: özel bir çalışma alanı veya varsayılan çalışma alanı seçin. Çalışma alanınız izleme verilerinizi barındırır.
       * Varsayılan çalışma alanını kullanmak için onay kutusunu işaretleyin. 
       * Özel bir çalışma alanı seçmek için onay kutusunu temizleyin. Ardından özel çalışma alanınızın aboneliğini ve bölgesini seçin. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="Bağlantı Izleyicisinde temel bilgiler sekmesini gösteren ekran görüntüsü.":::
   
1. Sekmenin alt kısmındaki **İleri: test grupları**' nı seçin.

1. Test gruplarınızı kaynak, hedef ve test yapılandırması ekleyin. Test gruplarınızı ayarlama hakkında bilgi edinmek için bkz. [bağlantı Izleyicisinde test grupları oluşturma](#create-test-groups-in-a-connection-monitor). 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="Bağlantı Izleyicisinde test grupları sekmesini gösteren ekran görüntüsü.":::

1. Sekmenin alt kısmındaki Ileri ' yi seçin **: uyarı oluştur**. Uyarı oluşturma hakkında bilgi edinmek için bkz. [bağlantı Izleyicisinde uyarı oluşturma](#create-alerts-in-connection-monitor).

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="Uyarı Oluştur sekmesini gösteren ekran görüntüsü.":::

1. Sekmenin alt kısmındaki Ileri ' yi seçin ve **ardından Oluştur**' a tıklayın.

1. **Gözden geçir + oluştur** sekmesinde, bağlantı izleyicisini oluşturmadan önce temel bilgileri ve test gruplarını gözden geçirin. Bağlantı izleyicisini düzenlemeniz gerekiyorsa, ilgili sekmelere geri giderek bunu yapabilirsiniz. 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="Bağlantı Izleyicisinde Inceleme ve oluşturma sekmesini gösteren ekran görüntüsü.":::
   > [!NOTE] 
   > **Gözden geçir + oluştur** sekmesi, Bağlantı İzleyicisi aşamasında aylık maliyeti gösterir. Şu anda **geçerli maliyet/ay** sütununda hiçbir ücret gösterililmez. Bağlantı Izleyicisi genel kullanıma sunulduğunda, bu sütunda aylık bir ücret gösterilir. 
   > 
   > Bağlantı Izleyicisi aşamasında bile, Log Analytics alma ücretleri uygulanır.

1. Bağlantı izleyicisini oluşturmaya hazırsanız, **gözden geçir + oluştur** sekmesinin altındaki **Oluştur**' u seçin.

Bağlantı Izleyicisi, arka planda Bağlantı İzleyicisi kaynağını oluşturur.

## <a name="create-test-groups-in-a-connection-monitor"></a>Bağlantı izleyicisinde test grupları oluşturma

Bir bağlantı izleyicisinde bulunan her bir test grubu, ağ parametrelerini test eden kaynakları ve hedefleri içerir. Başarısız olan denetim yüzdesi ve test yapılandırmalarının RTT üzerinden test edilmiştir.

Azure portal, bir bağlantı izleyicisinde bir test grubu oluşturmak için aşağıdaki alanlar için değerler belirtirsiniz:

* **Test grubunu devre dışı bırak**: Bu onay kutusunu, test grubunun belirttiği tüm kaynaklar ve hedefler için izlemeyi devre dışı bırakmak üzere seçebilirsiniz. Bu seçim varsayılan olarak temizlenir.
* **Ad**: test grubunuzu adlandırın.
* **Kaynaklar**: aracılar bunlara yüklenmişse Azure sanal makinelerini ve şirket içi makineleri kaynak olarak belirtebilirsiniz. Kaynağınız için bir aracı yükleme hakkında bilgi edinmek için bkz. [izleme aracılarını yükleme](./connection-monitor-overview.md#install-monitoring-agents).
   * Azure aracılarını seçmek için **Azure uç noktaları** sekmesini seçin. Burada yalnızca bağlantı izleyicisini oluştururken belirttiğiniz bölgeye bağlı olan VM 'Leri görürsünüz. Varsayılan olarak VM 'Ler, ait oldukları abonelikte gruplandırılır. Bu gruplar daraltılmıştır. 
   
       **Abonelik** düzeyinden hiyerarşideki diğer düzeylere ayrıntıya gidebilirsiniz:

      **Abonelik**  >  **Kaynak grubu**  >  **VNET**  >  **Alt ağ**  >  **Aracılarla VM 'ler**

      Ayrıca, ağacı başka bir düzeyden başlatmak için grubu seçiciye **göre** de değiştirebilirsiniz. Örneğin, sanal ağa göre gruplandırdıysanız,   >  aracılarla sanal ağ **alt ağı**  >  **VM**'lerinde aracıların bulunduğu VM 'leri görürsünüz.

       VNET, alt ağ veya tek VM seçtiğinizde, karşılık gelen kaynak KIMLIĞI uç nokta olarak ayarlanır. Varsayılan olarak, Azure ağ Izleyicisi uzantısının bulunduğu seçili VNET veya alt ağdaki tüm sanal makineler izlemeye katılır. Kapsamı azaltmak için belirli alt ağları veya aracıları seçin ya da kapsam özelliğinin değerini değiştirin. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="Bağlantı Izleyicisinde kaynakları ekle bölmesini ve Azure uç noktaları sekmesini gösteren ekran görüntüsü.":::

   * Şirket içi aracıları seçmek için, **Azure dışı uç noktaları** sekmesini seçin. Varsayılan olarak, aracılar bölgelere göre çalışma alanları halinde gruplandırılır. Tüm bu çalışma alanlarının yapılandırılmış Ağ Performansı İzleyicisi vardır. 
   
       Çalışma alanınıza Ağ Performansı İzleyicisi eklemeniz gerekiyorsa [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/solarwinds.solarwinds-orion-network-performance-monitor?tab=Overview)'nden alın. Ağ Performansı İzleyicisi ekleme hakkında daha fazla bilgi için bkz. [Azure izleyici 'de çözümleri izleme](../azure-monitor/insights/solutions.md). Şirket içi makineler için aracıların nasıl yapılandırılacağı hakkında bilgi için bkz. Şirket [içi makineler Için aracılar](connection-monitor-overview.md#agents-for-on-premises-machines).
   
       **Bağlantı Izleyicisi oluştur** altında, **temel bilgiler** sekmesinde varsayılan bölge seçilidir. Bölgeyi değiştirirseniz, yeni bölgedeki çalışma alanlarından aracılar seçebilirsiniz. Bir veya daha fazla aracıyı veya alt ağı seçebilirsiniz. **Alt ağ** görünümünde, izleme Için belirli IP 'leri seçebilirsiniz. Birden çok alt ağ eklerseniz, **OnPremises_Network_1** adlı özel bir şirket içi ağ oluşturulur. **Gruba** göre grupla ' yı Group By seçicisine göre de değiştirebilirsiniz.

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="Bağlantı Izleyicisinde kaynakları ekle bölmesini ve Azure dışı uç noktaları sekmesini gösteren ekran görüntüsü.":::

   * Son kullanılan uç noktaları seçmek için **en son uç nokta** sekmesini kullanabilirsiniz 
   
   * Kaynakları ayarlamayı bitirdiğinizde sekmenin en altında **bitti** ' yi seçin. **Test grubu oluştur** görünümündeki uç noktayı seçerek uç nokta adı gibi temel özellikleri de düzenleyebilirsiniz. 

* **Hedefler**: BIR Azure VM 'ye, bir şirket içi makineye veya herhangi bir uç noktaya (genel IP, URL veya FQDN) yönelik bağlantıyı hedef olarak belirterek izleyebilirsiniz. Tek bir test grubunda Azure VM 'leri, şirket içi makineler, Office 365 URL 'Leri, Dynamics 365 URL 'Leri ve özel uç noktaları ekleyebilirsiniz.

    * Azure VM 'lerini hedef olarak seçmek için **Azure uç noktaları** sekmesini seçin. Varsayılan olarak, Azure VM 'Leri **temel bilgiler** sekmesinde **Bağlantı İzleyicisi oluştur** altında seçtiğiniz bölgede bulunan bir abonelik hiyerarşisinde gruplandırılır. Bölgeyi değiştirebilir ve yeni bölgeden Azure VM 'Leri seçebilirsiniz. Ardından, kaynak Azure uç noktalarını ayarladığınızda yaptığınız gibi, **abonelik** düzeyinden hiyerarşideki diğer düzeylere gidebilirsiniz.

      Kaynak Azure uç noktalarını ayarladığınızda, sanal ağlar, alt ağlar veya tek VM 'Ler arasından seçim yapabilirsiniz. VNET, alt ağ veya tek VM seçtiğinizde, karşılık gelen kaynak KIMLIĞI uç nokta olarak ayarlanır. Varsayılan olarak, ağ Izleyicisi uzantısının bulunduğu seçili VNET veya alt ağdaki tüm sanal makineler izlemeye katılır. Kapsamı azaltmak için belirli alt ağları veya aracıları seçin ya da kapsam özelliğinin değerini değiştirin. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text=" Hedefleri Ekle bölmesini ve Azure uç noktaları sekmesini gösteren ekran görüntüsü<. >":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text=" Abonelik düzeyindeki hedefleri Ekle bölmesini gösteren ekran görüntüsü<. >":::
       
    
    * Azure olmayan aracıları hedef olarak seçmek için **Azure dışı uç noktaları** sekmesini seçin. Varsayılan olarak, aracılar bölgelere göre çalışma alanları halinde gruplandırılır. Tüm bu çalışma alanlarının yapılandırılmış Ağ Performansı İzleyicisi var. 
    
      Çalışma alanınıza Ağ Performansı İzleyicisi eklemeniz gerekiyorsa Azure Marketi 'nden alın. Ağ Performansı İzleyicisi ekleme hakkında daha fazla bilgi için bkz. [Azure izleyici 'de çözümleri izleme](../azure-monitor/insights/solutions.md). Şirket içi makineler için aracıların nasıl yapılandırılacağı hakkında bilgi için bkz. Şirket [içi makineler Için aracılar](connection-monitor-overview.md#agents-for-on-premises-machines).

       **Bağlantı Izleyicisi oluştur** altında, **temel bilgiler**   sekmesinde varsayılan bölge seçilidir. Bölgeyi değiştirirseniz, yeni bölgedeki çalışma alanlarından aracılar seçebilirsiniz. Bir veya daha fazla aracıyı veya alt ağı seçebilirsiniz. **Alt ağ** görünümünde, izleme Için belirli IP 'leri seçebilirsiniz. Birden çok alt ağ eklerseniz, **OnPremises_Network_1** adlı özel bir şirket içi ağ oluşturulur.  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="Hedefleri Ekle bölmesini ve Azure dışı uç noktaları sekmesini gösteren ekran görüntüsü.":::
    
    * Genel uç noktaları hedef olarak seçmek için **dış adresler** sekmesini seçin. Uç noktalar listesi, Office 365 test URL 'Leri ve Dynamics 365 test URL 'Lerini ada göre gruplanmış olarak içerir. Aynı bağlantı izleyicisinde diğer test gruplarında oluşturulan uç noktaları da seçebilirsiniz. 
    
        Uç nokta eklemek için sağ üst köşedeki **uç nokta Ekle**' yi seçin. Ardından bir uç nokta adı ve URL, IP veya FQDN belirtin.

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="Bağlantı Izleyicisinde genel uç noktaların hedef olarak nereye ekleneceğini gösteren ekran görüntüsü.":::

    * Son kullanılan uç noktaları seçmek için **son bitiş noktası**   sekmesine gidin.
    * Hedefleri seçme işini bitirdiğinizde **bitti**' yi seçin. **Test grubu oluştur** görünümündeki uç noktayı seçerek uç nokta adı gibi temel özellikleri de düzenleyebilirsiniz. 

* **Test yapılandırması**: bir test grubuna bir veya daha fazla test yapılandırması ekleyebilirsiniz. **Yeni yapılandırma** sekmesini kullanarak yeni bir test yapılandırması oluşturun. Ya da aynı bağlantı Izleyicisinde bulunan başka bir test grubundan **var olanı seç** sekmesinden bir test yapılandırması ekleyin.

    * **Test yapılandırması adı**: test yapılandırmasını adlandırın.
    * **Protokol**: **TCP**, **ICMP** veya **http**' yi seçin. HTTP 'yi HTTPS olarak değiştirmek için protokol olarak **http** ' yi seçin ve bağlantı noktası olarak **443** ' yı seçin.
        * **TCP test yapılandırması oluştur**: Bu onay kutusu yalnızca **protokol** listesinde **http** ' yi seçtiğinizde görünür. Yapılandırmanızda başka bir yerde belirttiğiniz kaynakları ve hedefleri kullanan başka bir test yapılandırması oluşturmak için bu onay kutusunu işaretleyin. Yeni test yapılandırması **\<name of test configuration> _networkTestConfig** olarak adlandırılır.
        * İzleme işlemi **devre dışı bırak**: Bu onay kutusu, protokol TCP veya ICMP olduğunda geçerlidir. Kaynakları, topolojiyi ve Atlama RTT 'yi bulmaktan durdurmak için bu kutuyu seçin.
    * **Hedef bağlantı noktası**: tercih ettiğiniz bir hedef bağlantı noktası sağlayabilirsiniz.
        * **Bağlantı noktasında dinle**: Bu onay kutusu, protokol TCP olduğunda geçerlidir. Zaten açık değilse, seçilen TCP bağlantı noktasını açmak için bu onay kutusunu işaretleyin. 
    * **Sınama sıklığı**: Bu listede, kaynakların, belirttiğiniz protokol ve bağlantı noktasındaki hedeflere ne sıklıkta ping uygulayabileceğini belirtin. 30 saniye, 1 dakika, 5 dakika, 15 dakika veya 30 dakika seçebilirsiniz. 30 saniye ile 30 dakika arasında başka bir sıklık girmek için **özel** ' i seçin. Kaynaklar, seçtiğiniz değere göre hedeflere bağlantıyı test edecektir. Örneğin, 30 saniye seçerseniz, kaynaklar her 30 saniyelik dönemde en az bir kez hedefe bağlantıyı kontrol eder.
    * **Başarı eşiği**: aşağıdaki ağ parametrelerinde eşikler ayarlayabilirsiniz:
       * **Denetim başarısız**: kaynaklar, belirttiğiniz kriterleri kullanarak hedeflere bağlantı denetlediğinde başarısız olan denetimlerin yüzdesini ayarlayın. TCP veya ıCMP protokolü için, başarısız denetimlerin yüzdesi paket kaybı yüzdesine eşit olabilir. HTTP protokolü için, bu değer yanıt olmadan alınan HTTP isteklerinin yüzdesini temsil eder.
       * Gidiş **dönüş süresi**: saniye cinsinden, test yapılandırması üzerinden hedefe bağlanmak için kaynakların ne kadar süreceğine ilişkin RTT 'yi ayarlayın.
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="Bağlantı Izleyicisinde bir test yapılandırmasının ayarlandığı yeri gösteren ekran görüntüsü.":::
       
## <a name="create-alerts-in-connection-monitor"></a>Bağlantı Izleyicisinde uyarı oluşturma

Test yapılandırmalarında ayarlanan eşiklere göre başarısız olan testler üzerinde uyarılar ayarlayabilirsiniz.

Azure portal, bir bağlantı izleyicisine yönelik uyarılar oluşturmak için bu alanlar için değerler belirtirsiniz: 

- **Uyarı oluştur**: Azure izleyici 'de bir ölçüm uyarısı oluşturmak için bu onay kutusunu seçebilirsiniz. Bu onay kutusunu seçtiğinizde diğer alanlar düzenlenmek üzere etkinleştirilir. Uyarı için ek ücretler, uyarı [fiyatlandırmasına](https://azure.microsoft.com/pricing/details/monitor/)göre geçerli olacaktır. 

- **Kapsam**  >  **Kaynak**  >  **Hiyerarşi**: Bu değerler, **temel bilgiler** sekmesinde belirtilen değerlere göre otomatik olarak doldurulur.

- **Koşul adı**: Bu uyarı, `Test Result(preview)` ölçümde oluşturulur. Bağlantı İzleyicisi testinin sonucu başarısız olan bir sonuç olduğunda, uyarı kuralı harekete geçmeyecektir. 

- **Eylem grubu adı**: e-postanızı doğrudan girebilir veya eylem grupları aracılığıyla uyarı oluşturabilirsiniz. E-postanızı doğrudan girerseniz, **NPM e-posta ActionGroup** adlı bir eylem grubu oluşturulur. E-posta KIMLIĞI, bu eylem grubuna eklenir. Eylem gruplarını kullanmayı seçerseniz, önceden oluşturulmuş bir eylem grubu seçmeniz gerekir. Bir eylem grubu oluşturmayı öğrenmek için [Azure Portal eylem grupları oluşturma](../azure-monitor/alerts/action-groups.md)bölümüne bakın. Uyarı oluşturulduktan sonra [uyarılarınızı yönetebilirsiniz](../azure-monitor/alerts/alerts-metric.md#view-and-manage-with-azure-portal). 

- **Uyarı kuralı adı**: bağlantı izleyicisinin adı.

- **Oluşturma sonrasında kuralı etkinleştir**: uyarı kuralını koşula göre etkinleştirmek için bu onay kutusunu işaretleyin. Kuralı etkinleştirmeden oluşturmak istiyorsanız bu onay kutusunu devre dışı bırakın. 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="Bağlantı Izleyicisinde uyarı Oluştur sekmesini gösteren ekran görüntüsü.":::

## <a name="scale-limits"></a>Ölçek sınırları

Bağlantı izleyicilerinde bu ölçek sınırları vardır:

* Her bölge için abonelik başına en fazla bağlantı İzleyicisi: 100
* Bağlantı İzleyicisi başına en fazla test grubu: 20
* Bağlantı İzleyicisi başına en fazla kaynak ve hedef sayısı: 100
* Bağlantı İzleyicisi başına en fazla test yapılandırması: Azure portal ile 2

## <a name="next-steps"></a>Sonraki adımlar

* [İzleme verilerini çözümlemeyi ve uyarıları ayarlamayı](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)öğrenin.
* [Ağınızdaki sorunları tanılamayı](./connection-monitor-overview.md#diagnose-issues-in-your-network)öğrenin.
