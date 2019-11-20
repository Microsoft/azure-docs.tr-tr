---
title: Azure Log Analytics Ağ Performansı İzleyicisi çözümdeki performans Izleyicisi özelliği | Microsoft Docs
description: Ağ Performansı İzleyicisi performans Izleyicisi özelliği, ağınızdaki çeşitli noktalarda ağ bağlantısını izlemenize yardımcı olur. Bulut dağıtımlarını ve şirket içi konumları, birden çok veri merkezini ve şubelerin yanı sıra görev açısından kritik çok katmanlı uygulamaları veya mikro hizmetleri izleyebilirsiniz.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 745ec6ee8e69ad911e42b6360b3408d79d660718
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898844"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Ağ Performansı İzleyicisi çözümü: performans izleme

[Ağ performansı İzleyicisi](network-performance-monitor.md) performans izleyicisi özelliği, ağınızdaki çeşitli noktalarda ağ bağlantısını izlemenize yardımcı olur. Bulut dağıtımlarını ve şirket içi konumları, birden çok veri merkezini ve şubelerin yanı sıra görev açısından kritik çok katmanlı uygulamaları veya mikro hizmetleri izleyebilirsiniz. Performans Izleyicisinde, kullanıcılarınız şikayet etmeden önce ağ sorunlarını tespit edebilirsiniz. Önemli avantajlar şunları yapabilirsiniz: 

- Çeşitli alt ağlarda kaybı ve gecikmeyi izleyin ve Uyarılar ayarlayın.
- Ağdaki tüm yolları (gereksiz yollar dahil) izleyin.
- Geçici ve zaman içinde ağ sorunlarını giderin ve bu da çoğaltma zor olur.
- Ağ üzerinde, performansın düşmesine neden olan belirli bir segmenti belirleme.
- SNMP gereksinimi olmadan ağın sistem durumunu izleyin.


![Ağ Performansı İzleyicisi](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Yapılandırma
Ağ Performansı İzleyicisi yapılandırmasını açmak için [ağ performansı İzleyicisi çözümünü](network-performance-monitor.md)açın ve **Yapılandır**' ı seçin.

![Ağ Performansı İzleyicisi’ni Yapılandırma](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Yeni ağlar oluştur

Ağ Performansı İzleyicisi bir ağ, alt ağlar için bir mantıksal kapsayıcıdır. Ağ altyapınızın izlenmesini gereksinimlerinize göre düzenlemenize yardımcı olur. Kolay bir ada sahip bir ağ oluşturabilir ve iş mantığınıza göre bu ağa alt ağlar ekleyebilirsiniz. Örneğin, Londra adlı bir ağ oluşturabilir ve Londra veri merkezinizdeki tüm alt ağları ekleyebilirsiniz. Ya da *ContosoFrontEnd* adlı bir ağ oluşturabilir ve uygulamanızın ön ucuna sunan contoso adlı tüm alt ağları bu ağa ekleyebilirsiniz. Çözüm, ortamınızda bulunan tüm alt ağları içeren varsayılan bir ağı otomatik olarak oluşturur. 

Her ağ oluşturduğunuzda, bu ağa bir alt ağ ekleyin. Daha sonra bu alt ağ varsayılan ağdan kaldırılır. Bir ağı silerseniz, tüm alt ağları otomatik olarak varsayılan ağa döndürülür. Varsayılan ağ, Kullanıcı tanımlı herhangi bir ağda bulunmayan tüm alt ağlar için bir kapsayıcı görevi görür. Varsayılan ağı düzenleyemez veya silemezsiniz. Her zaman sistemde kalır. İhtiyaç duyduğunuz kadar çok özel ağ oluşturabilirsiniz. Çoğu durumda, kuruluşunuzdaki alt ağlar birden fazla ağda düzenlenir. Alt ağlarınızı iş mantığınızla gruplandırmak için bir veya daha fazla ağ oluşturun.

Yeni bir ağ oluşturmak için:


1. **Ağlar** sekmesini seçin.
1.  **Ağ ekle**' yi seçin ve ağ adını ve açıklamasını girin. 
2. Bir veya daha fazla alt ağ seçin ve ardından **Ekle**' yi seçin. 
3. Yapılandırmayı kaydetmek için **Kaydet** ' i seçin. 


### <a name="create-monitoring-rules"></a>İzleme kuralları oluşturma 

Performans Izleyicisi iki alt ağ arasında veya iki ağ arasında ağ bağlantısı performansının ihlal edildiğinde sistem durumu olayları oluşturur. Sistem bu eşikleri otomatik olarak öğrenebilirler. Özel eşikler de sağlayabilirsiniz. Sistem, herhangi bir ağ veya alt ağ bağlantısı çifti ile sistem tarafından öğrenilen eşiğe ulaştığında bir sistem durumu olayı oluşturan varsayılan kuralı otomatik olarak oluşturur. Bu işlem, izleme kurallarını açıkça oluşturmadığınız sürece çözümün ağ altyapınızı izlemesine yardımcı olur. Varsayılan kural etkinleştirilirse, tüm düğümler, izleme için etkinleştirdiğiniz tüm diğer düğümlere yapay işlemler gönderir. Varsayılan kural küçük ağlarla yararlı olur. Bir mikro hizmet çalıştıran az sayıda sunucunuz olduğu ve tüm sunucuların birbirlerine bağlantısı olduğundan emin olmak istediğiniz bir senaryoya örnek bir örnektir.

>[!NOTE]
> Özellikle de izleme için çok sayıda düğüm kullandığınız büyük ağlarda, varsayılan kuralı devre dışı bırakmanız ve özel izleme kuralları oluşturmanız önerilir. Özel izleme kuralları, çözüm tarafından oluşturulan trafiği azaltabilir ve ağınızın izlenmesini düzenlemenize yardımcı olabilir.

İş mantığınıza göre izleme kuralları oluşturun. İki Office sitesinin yönetim merkezine ağ bağlantısının performansını izlemek istiyorsanız örnek bir örnektir. Ağ O1 'de Office site1 'deki tüm alt ağları gruplayın. Sonra tüm alt ağları ağ O2 'de Office site2 'de gruplayın. Son olarak, ağ H 'deki Yönetim merkezinde bulunan tüm alt ağları gruplayın. iki izleme kuralı oluşturun--biri O1 ile H arasında, diğeri O2 ile H arasında. 

Özel izleme kuralları oluşturmak için:

1. **İzleyici** sekmesinde **Kural Ekle** ' yi seçin ve kural adı ve açıklamasını girin.
2. Listelerden izlenecek ağ veya alt ağ bağlantılarının çiftini seçin. 
3. Ağ açılır listesinden istediğiniz alt ağları içeren ağı seçin. Ardından ilgili alt ağ açılır listesinden alt ağları seçin. Bir ağ bağlantısındaki tüm alt ağları izlemek istiyorsanız **tüm alt ağlar**' ı seçin. Benzer şekilde, istediğiniz diğer alt ağları da seçin. Yaptığınız seçimlerin belirli alt ağ bağlantıları için izlemeyi dışlamak üzere **özel durum Ekle**' yi seçin. 
4. Yapay işlemleri yürütmek için ıCMP ve TCP protokolleri arasında seçim yapın. 
5. Seçtiğiniz öğeler için sistem durumu olayları oluşturmak istemiyorsanız, **Bu kuralın kapsadığı bağlantılarda sistem durumu Izlemeyi etkinleştir**' i temizleyin. 
6. İzleme koşullarını seçin. Sistem durumu olayı oluşturma için özel eşikler ayarlamak üzere eşik değerlerini girin. Koşulun değeri seçili ağ veya alt ağ çiftinin seçili eşiğini aştığında, bir sistem durumu olayı oluşturulur. 
7. Yapılandırmayı kaydetmek için **Kaydet** ' i seçin. 

Bir izleme kuralını kaydettikten sonra, **uyarı oluştur**' u seçerek bu kuralı uyarı yönetimi tümleştirebilirsiniz. Arama sorgusuyla otomatik olarak bir uyarı kuralı oluşturulur. Diğer gerekli parametreler otomatik olarak doldurulur. Bir uyarı kuralı kullanarak, Ağ Performansı İzleyicisi içindeki mevcut uyarılara ek olarak e-posta tabanlı uyarılar alabilirsiniz. Uyarılar Ayrıca runbook 'larla düzeltici eylemleri tetikleyip, Web kancaları kullanarak mevcut hizmet yönetimi çözümleriyle tümleştirilebilir. Uyarı ayarlarını düzenlemek için **uyarıyı Yönet** ' i seçin. 

Artık, özelliği kullanmak için daha fazla performans Izleyicisi kuralı oluşturabilir veya çözüm panosuna geçebilirsiniz.

### <a name="choose-the-protocol"></a>Protokolü seçin

Ağ Performansı İzleyicisi, paket kaybı ve bağlantı gecikme süresi gibi ağ performans ölçümlerini hesaplamak için yapay işlemler kullanır. Bu kavramı daha iyi anlamak için, bir ağ bağlantısının bir ucuna bağlı Ağ Performansı İzleyicisi aracısını düşünün. Bu Ağ Performansı İzleyicisi Aracısı, daha sonra ağın başka bir ucuna bağlı ikinci bir Ağ Performansı İzleyicisi aracısına araştırma paketleri gönderir. İkinci aracı yanıt paketleriyle yanıt verir. Bu işlem birkaç kez yinelenir. Yanıt sayısını ve her yanıtın alınması için geçen süreyi ölçerek, ilk Ağ Performansı İzleyicisi Aracısı değerlendirir bağlantı gecikmesi ve paket bırakır. 

Bu paketlerin biçimi, boyutu ve sırası, izleme kuralları oluştururken seçtiğiniz protokol tarafından belirlenir. Paketlerin protokolüne bağlı olarak, yönlendiriciler ve anahtarlar gibi ara ağ cihazları bu paketleri farklı şekilde işleyebilir. Sonuç olarak, protokol seçiminiz sonuçların doğruluğunu etkiler. Protokol seçiminiz, Ağ Performansı İzleyicisi çözümünü dağıttıktan sonra el ile herhangi bir adım uygulamanız gerekip gerekmediğini de belirler. 

Ağ Performansı İzleyicisi yapay işlemleri yürütmek için ıCMP ve TCP protokolleri arasında seçim sağlar. Yapay bir işlem kuralı oluştururken ıCMP ' yi seçerseniz, Ağ Performansı İzleyicisi aracıları ağ gecikmesini ve paket kaybını hesaplamak için ıCMP YANKı iletilerini kullanır. ICMP ECHO, geleneksel ping yardımcı programı tarafından gönderilen aynı iletiyi kullanır. Protokol olarak TCP kullandığınızda Ağ Performansı İzleyicisi aracıları ağ üzerinden TCP SYN paketleri gönderir. Bu adımın ardından bir TCP el sıkışma tamamlama işlemi gelir ve bağlantı RST paketleri kullanılarak kaldırılır. 

Bir protokol seçmeden önce aşağıdaki bilgileri göz önünde bulundurun: 

* **Birden çok ağ yolu bulma.** TCP birden çok yol keşfederken daha doğru ve her alt ağda daha az aracı olmalıdır. Örneğin, TCP kullanan bir veya iki aracı, alt ağlar arasındaki tüm gereksiz yolları bulabilir. Benzer sonuçlara ulaşmak için ıCMP kullanan birkaç aracıya ihtiyacınız vardır. ICMP kullanarak iki alt ağ arasında bir dizi yolunuz varsa, kaynak veya hedef alt ağda 5 ' ten fazla aracı gerekir.

* **Sonuçların doğruluğu.** Yönlendiriciler ve anahtarlar, TCP paketlerine kıyasla ıCMP YANKı paketlerine daha düşük öncelik atama eğilimindedir. Bazı durumlarda, ağ cihazlarının yoğun bir şekilde yüklenmesi durumunda TCP tarafından elde edilen veriler, uygulamalar tarafından karşılaşılan kaybı ve gecikme süresini daha yakından yansıtır. Bu durum, uygulama trafiğinin çoğunun TCP üzerinden akacağından oluşur. Bu gibi durumlarda, ıCMP, TCP ile karşılaştırıldığında daha az doğru sonuçlar sağlar. 

* **Güvenlik duvarı yapılandırması.** TCP protokolü, TCP paketlerinin bir hedef bağlantı noktasına gönderilmesini gerektirir. Ağ Performansı İzleyicisi aracıları tarafından kullanılan varsayılan bağlantı noktası 8084 ' dir. Aracıları yapılandırırken bağlantı noktasını değiştirebilirsiniz. Ağ güvenlik duvarları veya ağ güvenlik grubu (NSG) kurallarınızın bağlantı noktasında trafiğe izin verdiğinizden emin olun. Ayrıca aracıların yüklendiği bilgisayarlardaki yerel güvenlik duvarının Bu bağlantı noktasında trafiğe izin verecek şekilde yapılandırıldığından emin olmanız gerekir. Windows çalıştıran bilgisayarlarınızda güvenlik duvarı kurallarını yapılandırmak için PowerShell betiklerini kullanabilirsiniz, ancak ağ güvenlik duvarınızı el ile yapılandırmanız gerekir. Buna karşılık, ıCMP bir bağlantı noktası kullanarak çalışmaz. Çoğu kurumsal senaryoda, ping yardımcı programı gibi ağ tanılama araçları 'nı kullanmanıza izin veren güvenlik duvarları aracılığıyla ıCMP trafiğine izin verilir. Bir makineye diğerinden ping işlemi yapabiliyorsanız, güvenlik duvarlarını el ile yapılandırmak zorunda kalmadan ıCMP protokolünü kullanabilirsiniz.

>[!NOTE] 
> Bazı güvenlik duvarları ıCMP 'yi engelleyebilir, bu da güvenlik bilgileriniz ve olay yönetimi sisteminizde çok sayıda olay oluşmasına neden olan yeniden aktarım anlamına gelebilir. Seçtiğiniz protokolün bir ağ güvenlik duvarı veya NSG tarafından engellenmediğinden emin olun. Aksi takdirde, Ağ Performansı İzleyicisi ağ kesimini izleyemiyorum. İzleme için TCP kullanmanızı öneririz. TCP 'yi kullanamıyoruz, örneğin: 
>
> - Windows istemcilerinde TCP RAW Sockets kullanılmasına izin verilmediğinden Windows istemci tabanlı düğümleri kullanırsınız.
> - Ağ güvenlik duvarınız veya NSG, TCP 'yi engelliyor.
> - Protokolü nasıl geçeceğimizi bilemezsiniz.

Dağıtım sırasında ıCMP kullanmayı seçerseniz, varsayılan izleme kuralını düzenleyerek istediğiniz zaman TCP 'ye geçiş yapabilirsiniz.

1. > **izleyici** > **ağ performansı** ' na gidin > **izleyicisini**  **yapılandırın** . Ardından **varsayılan kural**' ı seçin. 
2. **Protokol** bölümüne ilerleyin ve kullanmak istediğiniz protokolü seçin. 
3. Ayarı uygulamak için **Kaydet** ' i seçin. 

Varsayılan kural belirli bir protokolü kullanıyor olsa da, farklı bir protokolle yeni kurallar oluşturabilirsiniz. Hatta, bazı kuralların ıCMP ve diğer kullanıcıların TCP kullanan kurallar karışımı oluşturabilirsiniz. 

## <a name="walkthrough"></a>Kılavuz 

Şimdi bir sistem durumu olayının kök nedenine basit bir araştırmaya göz atın.

Çözüm panosunda bir sistem durumu olayı, bir ağ bağlantısının sağlıksız olduğunu gösterir. Sorunu araştırmak için **Izlenen ağ bağlantıları** kutucuğunu seçin.

Ayrıntıya gitme sayfası, **DMZ2-DMZ1** ağ bağlantısının sağlıksız olduğunu gösterir. Bu ağ bağlantısı için **alt ağ bağlantılarını görüntüle** ' yi seçin. 


Ayrıntıya gitme sayfasında, **DMZ2-DMZ1** ağ bağlantısındaki tüm alt ağ bağlantıları gösterilmektedir. Her iki alt ağ bağlantısı için, gecikme eşiği geçti, bu da ağ bağlantısını sağlıksız hale getirir. Her iki alt ağ bağlantısının gecikme eğilimlerini de görebilirsiniz. Grafikteki zaman seçim denetimini, gerekli zaman aralığına odaklanmak için kullanın. Gecikme süresinin en yüksek değerine ulaştığı günün saatini görebilirsiniz. Sorunu araştırmak için günlüklere bu sürenin daha sonra arama yapın. Daha fazla ayrıntıya geçmek için **düğüm bağlantılarını görüntüle** ' yi seçin. 
 
 ![Alt ağ bağlantıları sayfası](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Önceki sayfaya benzer şekilde, belirli bir alt ağ bağlantısı için ayrıntıya gitme sayfası, bileşen düğüm bağlantılarını listeler. Önceki adımda yaptığınız gibi, burada benzer eylemleri gerçekleştirebilirsiniz. İki düğüm arasındaki topolojiyi görüntülemek için **topolojiyi görüntüle** ' yi seçin. 
 
 ![Düğüm bağlantıları sayfası](media/network-performance-monitor-performance-monitor/node-links.png) 

Seçilen iki düğüm arasındaki tüm yollar topoloji eşlemesinde çizilir. Topoloji eşlemesindeki iki düğüm arasındaki yolların atlama topolojisini görselleştirebilirsiniz. Bu, iki düğüm arasında kaç yolun var olduğunu ve veri paketlerinin hangi yollardan alınacağını açık bir şekilde bir resim sunar. Ağ performansı performansı sorunları kırmızı renkle gösterilir. Hatalı bir ağ bağlantısını veya hatalı bir ağ cihazını bulmak için topoloji eşlemesindeki kırmızı öğelere bakın. 

 ![Topoloji haritası olan topoloji panosu](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

**Eylem** bölmesindeki her bir yoldaki kaybı, gecikme süresini ve atlama sayısını inceleyebilirsiniz. Uygun olmayan yolların ayrıntılarını görüntülemek için kaydırma çubuğunu kullanın. Yalnızca seçili yolların topolojisi çizilmek üzere uygun olmayan atlamaya sahip yolları seçmek için filtreleri kullanın. Topoloji eşlemesini yakınlaştırmak veya uzaklaştırmak için fare tekerleğini kullanın. 

Aşağıdaki görüntüde, ağın belirli bir bölümünde sorunlu alanların asıl nedeni kırmızı yollarda ve atlarda görünür. FQDN ve IP adresini içeren düğümün özelliklerini açığa çıkarmak için topoloji eşlemesinde bir düğüm seçin. Bir atlama seçildiğinde, atlamanın IP adresi gösterilir. 
 
![Düğüm özellikleri seçili olan topoloji eşlemesi](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Sonraki adımlar
Ayrıntılı ağ performansı veri kayıtlarını görüntülemek için [günlüklerde arama](../../azure-monitor/log-query/log-query-overview.md) yapın.
