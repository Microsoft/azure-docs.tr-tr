---
title: Endüstriyel IoT cihazlarımı yönetmek için bir çözüm deneyin-Azure | Microsoft Docs
description: Bu hızlı başlangıçta Bağlı Fabrika Azure IoT çözüm hızlandırıcısını dağıtacak ve çözüm panosunda oturum açıp işlevleri kullanacaksınız.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: dbb49d990581c6b38d0eff32b01dd9b115e6794c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057828"
---
# <a name="quickstart-try-a-cloud-based-solution-to-manage-my-industrial-iot-devices"></a>Hızlı başlangıç: Endüstriyel IoT cihazlarını yönetmek için bulut tabanlı bir çözümü deneme

Bu hızlı başlangıçta endüstriyel IoT cihazlarını izleme ve yönetme amacıyla bulut tabanlı bir uygulama çalıştırmak için Azure IoT Bağlı Fabrika çözüm hızlandırıcısını dağıtmayı öğreneceksiniz. Bağlı Fabrika çözüm hızlandırıcısını dağıttığınızda, genel bir endüstriyel IoT senaryosunu adım adım görmenize olanak sağlayan sanal kaynaklar ile önceden doldurulur. Çözüme bağlanmış olan birçok sanal fabrika, genel donanım verimliliğini (OEE) ve ana performans göstergelerini (KPI) hesaplamak için gereken veri değerlerini raporlar. Bu hızlı başlangıçta çözüm panosunu kullanarak aşağıdaki işlemleri gerçekleştireceksiniz:

* Fabrikayı, üretim hatlarını, istasyon OEE ve KPI değerlerini izleme.
* Bu cihazlardan oluşturulan telemetri verilerini analiz etme.
* Uyarılara yanıt verme.

Bu hızlı başlangıcı tamamlamak etkin bir Azure aboneliğinizin olması gerekir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="deploy-the-solution"></a>Çözümü dağıtma

Çözüm hızlandırıcısını Azure aboneliğinize dağıttığınızda ayarlamanız gereken yapılandırma seçenekleri vardır.

[Microsoft Azure IoT çözüm hızlandırıcıları](https://www.azureiotsolutions.com) sayfasına gidip Azure hesabı kimlik bilgilerinizle oturum açın.

**Bağlı Fabrika** kutucuğuna tıklayın. **Bağlı Fabrika** sayfasında **Hemen deneyin**’e tıklayın:

![Hemen deneyin](./media/quickstart-connected-factory-deploy/connectedfactory.png)

**Bağlı Fabrika çözümü oluştur** sayfasında Bağlı Fabrika çözüm hızlandırıcınız için benzersiz bir **Çözüm adı** girin. Bu ad tüm çözüm hızlandırıcısı kaynaklarını içeren Azure kaynak grubunun adıdır. Bu hızlı başlangıçta **MyDemoConnectedFactory** adı kullanılmıştır.

Çözüm hızlandırıcısını dağıtırken kullanmak istediğiniz **Subscription** (Abonelik) ve **Region** (Bölge) seçimini yapın. Genelde size en yakın bölgeyi seçmeniz gerekir. Bu hızlı başlangıçta **Visual Studio Enterprise** ve **Doğu ABD** kullanıyoruz. Abonelikte [genel yönetici veya kullanıcı](iot-accelerators-permissions.md) olmanız gerekir.

Dağıtımınızı başlatmak için **Oluştur** ' a tıklayın. Bu işlemin çalışması en az beş dakika sürer:

![Bağlı Fabrika çözümünün ayrıntıları](./media/quickstart-connected-factory-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Çözümde oturum açma

Azure aboneliğinize dağıtım tamamlandığında, çözüm dosyasında yeşil bir onay işareti ve **Hazır** yazısı görürsünüz. Ardık Bağlı Fabrika çözüm Hızlandırıcısı panonuzda oturum açabilirsiniz.

**Sağlanan çözümler** sayfasında yeni Bağlı Fabrika çözümü hızlandırıcınıza tıklayın:

![Yeni çözümü seçme](./media/quickstart-connected-factory-deploy/choosenew.png)

Bağlı fabrika çözümü hızlandırıcılarınız hakkındaki bilgileri görüntülenen sayfada görüntüleyebilirsiniz. Bağlı fabrika çözümü hızlandırıcıyı görüntülemek için **çözüm hızlandırıcısına git** ' i seçin:

![Çözüm paneli](./media/quickstart-connected-factory-deploy/solutionpanel.png)

İzin isteğini kabul etmek için **Kabul et** öğesine tıklayın. Bağlı Fabrika çözümü panosu tarayıcınızda görüntülenir. Sanal fabrikalar, üretim hatları ve istasyonları gösterilir.

## <a name="view-the-dashboard"></a>Panoyu görüntüleme

Varsayılan görünüm, *pano* şeklindedir. Portalın diğer alanlarına gitmek için sayfanın sol tarafındaki menüyü kullanın:

[![Çözüm panosu](./media/quickstart-connected-factory-deploy/dashboard-inline.png)](./media/quickstart-connected-factory-deploy/dashboard-expanded.png#lightbox)

Endüstriyel IoT cihazlarınızı yönetmek için panoyu kullanırsınız. Bağlı Fabrika, global fabrika yapılandırmasını göstermek için bir hiyerarşi kullanır. Hiyerarşinin en üst düzeyinde, bir veya daha fazla fabrika içeren kuruluş bulunur. Her fabrika üretim hatlarından ve her üretim hattı da istasyonlardan oluşur. Her düzeyde OEE ve KPI’leri görüntüleyebilir, telemetri için yeni düğümler yayımlayabilir ve alarmlara yanıt verebilirsiniz.

Panoda şunları görebilirsiniz:

## <a name="overall-equipment-efficiency"></a>Genel donanım verimliliği

**Genel Donanım Verimliliği** paneli, kuruluşun tamamı için veya görüntülediğiniz fabrika/üretim hattı/istasyon için OEE değerlerini gösterir. Bu değer, kurumsal düzeyi bulmak için istasyon görünümünden toplanır. OEE şekli ve bu şekli oluşturan öğeler daha fazla analiz edilebilir.

[![Genel ekipman verimliliği](./media/quickstart-connected-factory-deploy/oee-inline.png)](./media/quickstart-connected-factory-deploy/oee-expanded.png#lightbox)

OEE, üretimle ilgili operasyonel parametreleri kullanarak üretim sürecinin verimliliğini derecelendirir. OEE, kullanılabilirlik oranı, performans oranı ve kalite oranının çarpılmasıyla hesaplanan standart bir endüstri ölçümüdür: OEE = kullanılabilirlik x performans x kalite.

Hiyerarşi verilerinin herhangi bir düzeyindeki OEE verilerini daha ayrıntılı bir şekilde analiz edebilirsiniz. OEE, kullanılabilirlik, performans veya kalite yüzdesi halkasına tıklayın. Verilerin farklı zamanlardaki görselleştirmelerinin yer aldığı bir bağlam paneli açılır:

[![Genel ekipman verimlilik ayrıntısı](./media/quickstart-connected-factory-deploy/oeedetail-inline.png)](./media/quickstart-connected-factory-deploy/oeedetail-expanded.png#lightbox)

İstediğiniz grafiğe tıklayarak verileri daha ayrıntılı bir şekilde analiz edebilirsiniz.

### <a name="key-performance-indicators"></a>Temel performans göstergeleri

**Temel performans göstergeleri**, kuruluşun tamamında veya görüntülediğiniz fabrika/üretim hattı/istasyonda saatte üretilen birimlerin ve kullanılan enerjinin (kWh) görüntülendiği paneldir. Bu değerler, kurumsal düzeyi bulmak için istasyon görünümünden toplanır.

[![Ana performans göstergeleri](./media/quickstart-connected-factory-deploy/kpis-inline.png)](./media/quickstart-connected-factory-deploy/kpis-expanded.png#lightbox)

Hiyerarşi verilerinin herhangi bir düzeyindeki KPI verilerini daha ayrıntılı bir şekilde analiz edebilirsiniz. OEE, kullanılabilirlik, performans veya kalite yüzdesi halkasına tıklayın. Verilerin farklı zamanlardaki görselleştirmelerinin yer aldığı bir bağlam paneli açılır:

[![KPI ayrıntısı](./media/quickstart-connected-factory-deploy/kpidetail-inline.png)](./media/quickstart-connected-factory-deploy/kpidetail-expanded.png#lightbox)

İstediğiniz grafiğe tıklayarak verileri daha ayrıntılı bir şekilde analiz edebilirsiniz.

### <a name="factory-locations"></a>Fabrika Konumları

Çözümdeki durumu, konumu ve geçerli üretim yapılandırmasını gösteren bir **fabrika konumları** paneli. Çözüm hızlandırıcısını ilk kez çalıştırdığınızda, panoda sanal fabrikalardan oluşan bir küme görüntülenir. Her üretim hattı benzetimi, benzetimi yapılan görevleri gerçekleştiren ve verileri paylaşan üç gerçek OPC UA sunucusundan oluşur. OPC UA hakkında daha fazla bilgi için bkz. [bağlı fabrika hakkında SSS](iot-accelerators-faq-cf.md):

[![Fabrika konumları](./media/quickstart-connected-factory-deploy/factorylocations-inline.png)](./media/quickstart-connected-factory-deploy/factorylocations-expanded.png#lightbox)

Çözüm hiyerarşisinde gezinebilir ve her düzeydeki OEE ve KPI değerlerini görüntüleyebilirsiniz:

1. **Fabrika Konumları**'nda **Mumbai**'ye tıklayın. Bu konumdaki üretim hatlarını görürsünüz.

1. **Production Line 1**'e tıklayın. Bu üretim hattındaki istasyonları görürsünüz.

1. **Packaging**'e tıklayın. Bu istasyon tarafından yayımlanan OPC UA düğümlerini görürsünüz.

1. **EnergyConsumption**'a tıklayın. Değerin farklı zamanlardaki değişimini gösteren grafikler görürsünüz. İstediğiniz grafiğe tıklayarak verileri daha ayrıntılı bir şekilde analiz edebilirsiniz.

[![Enerji tüketimini görüntüleme](./media/quickstart-connected-factory-deploy/explorelocations-inline.png)](./media/quickstart-connected-factory-deploy/explorelocations-expanded.png#lightbox)

### <a name="map"></a>Harita

Aboneliğinizin [Bing Haritalar API’sine](iot-accelerators-faq-cf.md) erişimi varsa, *Fabrikalar* haritasında size çözümdeki tüm fabrikaların coğrafi konumu ve durumu gösterilir. Konumun detaylarına gitmek için haritada görüntülenen konumlara tıklayın.

[![Harita](./media/quickstart-connected-factory-deploy/map-inline.png)](./media/quickstart-connected-factory-deploy/map-expanded.png#lightbox)

### <a name="alarms"></a>Alarmlar

**Alarmlar** panelinde, raporlanan bir değerin veya hesaplanan OEE/KPI değerinin yapılandırılmış eşiği aşmasından dolayı oluşturulan alarmlar gösterilir. Bu panelde, istasyon düzeyinden kuruluş düzeyine kadar her hiyerarşi düzeyindeki alarmlar görüntülenir. Her alarm açıklama, tarih, saat, konum ve tekrarlanma sayısını içerir:

[![Alarmlar](./media/quickstart-connected-factory-deploy/alarms-inline.png)](./media/quickstart-connected-factory-deploy/alarms-expanded.png#lightbox)

Panodan alarma neden olan verileri analiz edebilirsiniz. Yöneticiyseniz alarmlar üzerinde şu varsayılan eylemleri gerçekleştirebilirsiniz:

* Alarmı kapatma.
* Alarmı kabul etme.

Alarmlardan birine tıklayın, **Eylem seçin** açılan menüsünde **Uyarıyı kabul et**'i seçin ve **Uygula**'ya tıklayın:

[![Uyarıyı Onayla](./media/quickstart-connected-factory-deploy/acknowledge-inline.png)](./media/quickstart-connected-factory-deploy/acknowledge-expanded.png#lightbox)

Alarm verilerini daha fazla çözümlemek için, alarm panelindeki grafiğe tıklayın.

Bu alarmlar, çözüm hızlandırıcısındaki yapılandırma dosyasında belirtilen kurallar tarafından oluşturulur. Bu kurallar, OEE veya KPI rakamları veya OPC UA düğüm değerleri bir eşiğin üzerinde olduğunda alarmlar oluşturabilir. Bu eşik değerini ayarlayabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Daha fazla incelemeyi planlıyorsanız, Bağlı Fabrika çözüm hızlandırıcısını dağıtımda bırakın.

Çözüm hızlandırıcısına ihtiyacınız kalmadıysa [Sağlanan çözümler](https://www.azureiotsolutions.com/Accelerators#dashboard) sayfasından seçtikten sonra **Çözümü sil**'e tıklayarak silebilirsiniz:

![Çözümü sil](media/quickstart-connected-factory-deploy/deletesolution.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Bağlı Fabrika çözümünü dağıttınız ve fabrikalarınız, üretim hatlarınız ve istasyonlarınız arasında gezinmeyi öğrendiniz. Ayrıca tüm hiyerarşi düzeylerindeki OEE ve KPI değerlerini görüntülediniz ve alarmlara yanıt vermeyi öğrendiniz.

Endüstriyel IoT cihazlarınızı yönetmek için panodaki diğer özellikleri nasıl kullanacağınız hakkında bilgi edinmek için aşağıdaki nasıl yapılır kılavuzuna gidin:

> [!div class="nextstepaction"]
> [Bağlı Fabrika panosunu kullanma](iot-accelerators-connected-factory-dashboard.md)