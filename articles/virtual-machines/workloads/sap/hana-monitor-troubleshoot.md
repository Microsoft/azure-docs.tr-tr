---
title: Azure 'da SAP HANA 'da HANA tarafında izleme ve sorun giderme (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA (büyük örnekler) üzerinde HANA tarafında izleme ve sorun giderme.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2c596a876817f0a501025c37e463a7eebb55cf2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099832"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>HANA tarafından izleme ve sorun giderme

Azure 'daki SAP HANA ilgili sorunları (büyük örnekler) etkin bir şekilde çözümlemek için, bir sorunun kök nedenini daraltmak yararlı olur. SAP, size yardımcı olmak için büyük miktarda belge yayımladı.

SAP HANA performansla ilgili geçerli SSS 'ler aşağıdaki SAP notlarında bulunabilir:

- [SAP Note #2222200 – SSS: SAP HANA ağ](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Note #2100040 – SSS: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Note #199997 – SSS: SAP HANA belleği](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Note #200000 – SSS: SAP HANA performans Iyileştirmesi](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Note #199930 – SSS: SAP HANA g/ç Analizi](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Note #2177064 – SSS: SAP HANA hizmeti yeniden başlatma ve kilitlenmeler](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA uyarılar

İlk adım olarak, geçerli SAP HANA uyarı günlüklerini denetleyin. SAP HANA Studio 'da **Yönetim Konsolu ' na gidin: Larınız Göster: tüm uyarılar**. Bu sekmede, en düşük ve en yüksek eşiklerin dışında kalan belirli değerler (boş fiziksel bellek, CPU kullanımı vb.) için tüm SAP HANA uyarıları gösterilir. Varsayılan olarak, denetimler her 15 dakikada bir otomatik olarak yenilenir.

![SAP HANA Studio 'da Yönetim Konsolu ' na gidin: Larınız Göster: tüm uyarılar](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

Hatalı eşik ayarı nedeniyle tetiklenen bir uyarı için, bir çözüm varsayılan değere veya daha makul bir eşik değerine sıfırlanacak.

![Varsayılan değere veya daha makul bir eşik değerine sıfırlayın](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Aşağıdaki uyarılar CPU kaynağı sorunlarını gösteriyor olabilir:

- Ana bilgisayar CPU kullanımı (uyarı 5)
- En son kayıt noktası işlemi (uyarı 28)
- Kayıt noktası süresi (uyarı 54)

SAP HANA veritabanınızda aşağıdakilerden birini izleyerek yüksek CPU tüketimi fark edebilirsiniz:

- Uyarı 5 (konak CPU kullanımı) geçerli veya geçmiş CPU kullanımı için oluşturuldu
- Genel bakış ekranında görüntülenmekte olan CPU kullanımı

![Genel bakış ekranında kullanılan CPU kullanımı](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Yük Graph, geçmişte yüksek CPU tüketimi veya yüksek tüketim gösterebilir:

![Yük Graph, geçmişte yüksek CPU tüketimi veya yüksek tüketim gösterebilir](./media/troubleshooting-monitoring/image4-load-graph.png)

Yüksek CPU kullanımı nedeniyle tetiklenen bir uyarı olabilir, ancak bunlarla sınırlı olmamak üzere bazı işlemler, veri yükleme, yanıt vermeyen işler, uzun süre çalışan SQL deyimleri ve hatalı sorgu performansı gibi çeşitli nedenlerden kaynaklanabilir (örneğin: , HANA küplerinde bant genişliği ile).

Sorun giderme SAP HANA başvurun: [ Ayrıntılı sorun giderme adımları için](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) CPU ile ilgili nedenler ve çözümler sitesi.

## <a name="operating-system"></a>İşletim sistemi

Linux üzerinde SAP HANA en önemli denetimlerinden biri, saydam büyük sayfaların devre dışı bırakıldığından emin olmak için, [SAP HANA sunucularında SAP Note #2131662 – saydam büyük sayfalar (THP)](https://launchpad.support.sap.com/#/notes/2131662)konusuna bakın.

- Saydam büyük sayfaların şu Linux komutu aracılığıyla etkinleştirilip etkinleştirilmediğini kontrol edebilirsiniz: **Cat/sys/kernel/mm/Transparent\_kugepage/Enabled**
- Aşağıdaki gibi _her zaman_ köşeli ayraç içine Alınmısa, saydam büyük sayfaların etkin olduğu anlamına gelir: [Always] her zaman madmenlik; aşağıdaki gibi _hiçbir zaman_ parantez içine alınmadıysa, saydam büyük sayfaların devre dışı bırakıldığı anlamına gelir: her zaman madmenlik [hiçbir zaman]

Aşağıdaki Linux komutu Nothing olarak döndürmelidir: **RPM-qa | grep ulimit.** _Uılimit_ yüklüyse, hemen kaldırın.

## <a name="memory"></a>Bellek

SAP HANA veritabanı tarafından ayrılan bellek miktarının beklenenden yüksek olduğunu gözlemleyebilirsiniz. Aşağıdaki uyarılar, yüksek bellek kullanımıyla ilgili sorunları gösterir:

- Ana bilgisayar fiziksel bellek kullanımı (uyarı 1)
- Ad sunucusunun bellek kullanımı (uyarı 12)
- Sütun deposu tablolarının toplam bellek kullanımı (uyarı 40)
- Hizmetlerin bellek kullanımı (uyarı 43)
- Sütun deposu tablolarının ana depolamanın bellek kullanımı (uyarı 45)
- Çalışma zamanı döküm dosyaları (uyarı 46)

Sorun giderme SAP HANA başvurun: [ Ayrıntılı sorun](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) giderme adımları için bellek sorunları sitesi.

## <a name="network"></a>Ağ

[SAP note #2081065 – SAP HANA ağ sorunlarını giderme](https://launchpad.support.sap.com/#/notes/2081065) ve bu SAP notunda ağ sorun giderme adımlarını gerçekleştirme bölümüne bakın.

1. Sunucu ve istemci arasındaki gidiş dönüş süresini çözümleme.
  A. SQL betiği [_Hana\_ağ\_istemcilerini_](https://launchpad.support.sap.com/#/notes/1969700)çalıştırın _._
  
2. Düğümler arası iletişimi analiz edin.
  A. SQL betiği [_Hana\_ağ\_hizmetlerini_](https://launchpad.support.sap.com/#/notes/1969700)çalıştırın _._

3. Linux komutunu çalıştırın **ıconfig** (çıkış, herhangi bir paket kayıpları oluşup oluşmadığını gösterir).
4. Linux komutunu çalıştırın **tcpdump**.

Ayrıca, gerçek uygulama ağı performansını ölçmek için açık kaynak [Iperf](https://iperf.fr/) aracını (veya benzer) kullanın.

Sorun giderme SAP HANA başvurun: [ Ayrıntılı sorun giderme adımları için](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) ağ performansı ve bağlantı sorunları sitesi.

## <a name="storage"></a>Depolama

Bir son kullanıcı perspektifinden, bir uygulama (veya bir bütün olarak sistem) yavaş çalışır, yanıt vermemeye başlıyor veya g/ç performansı sorunları varsa yanıt vermeyi durdurmuş gibi görünse de durabilir. SAP HANA Studio 'daki **birimler** sekmesinde, eklenen birimleri ve her bir hizmet tarafından hangi birimlerin kullanıldığını görebilirsiniz.

![SAP HANA Studio 'daki birimler sekmesinde, eklenen birimleri ve her bir hizmet tarafından kullanılan birimleri görebilirsiniz](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Ekranın alt kısmındaki eklenen birimler, birimlerin ayrıntılarını (örneğin, dosyalar ve g/ç istatistikleri) görebilirsiniz.

![Ekranın alt kısmındaki eklenen birimler, birimlerin ayrıntılarını (örneğin, dosyalar ve g/ç istatistikleri) görebilirsiniz](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Sorun giderme SAP HANA başvurun: [ G/ç ile ilgili temel nedenler ve](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) çözümler [ve SAP HANA sorun giderme: Ayrıntılı sorun giderme adımları için diskle](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) ilgili kök nedenler ve çözümler sitesi.

## <a name="diagnostic-tools"></a>Tanılama Araçları

Hana\_yapılandırma\_Minichecks aracılığıyla SAP HANA bir sistem durumu denetimi gerçekleştirin. Bu araç, SAP HANA Studio 'da zaten uyarı olarak oluşturulmuş olması gereken önemli olabilecek teknik sorunları döndürür.

[SAP HANA Için SAP Note #1969700 – SQL deyimi koleksiyonuna](https://launchpad.support.sap.com/#/notes/1969700) bakın ve bu nota eklenen SQL deyimlerini. zip dosyasını indirin. Bu. zip dosyasını yerel sabit sürücüde depolayın.

SAP HANA Studio 'da, **sistem bilgileri** sekmesinde **ad** sütununa sağ tıklayın ve **SQL deyimlerini içeri aktar**' ı seçin.

![SAP HANA Studio 'da, sistem bilgileri sekmesinde Ad sütununa sağ tıklayın ve SQL deyimlerini Içeri Aktar ' ı seçin.](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Yerel olarak depolanan SQL deyimlerini. zip dosyasını seçin ve karşılık gelen SQL deyimlerine sahip bir klasör içeri aktarılır. Bu noktada, birçok farklı tanılama denetimi bu SQL deyimleriyle çalıştırılabilir.

Örneğin, SAP HANA sistem çoğaltması bant genişliği gereksinimlerini test etmek için, çoğaltma altındaki **bant genişliği** bildirimine sağ **tıklayın: Bant** genişliği ve SQL konsolunda **Aç** ' ı seçin.

Tüm SQL deyimleri, giriş parametrelerinin (değiştirme bölümü) değiştirilmesine ve sonra yürütülmesine izin vererek açılır.

![Tüm SQL deyimleri, giriş parametrelerinin (değiştirme bölümü) değiştirilmesine ve sonra yürütülmesine izin vererek açılır](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Diğer bir örnek, çoğaltma altındaki deyimlere sağ tıklandır **: Genel**bakış. Bağlam menüsünden **Çalıştır** ' ı seçin:

![Diğer bir örnek, çoğaltma altındaki deyimlere sağ tıklandır: Bakýþ. Bağlam menüsünden Yürüt ' ü seçin](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Bu, sorun gidermenize yardımcı olan bilgiler ile sonuçlanır:

![Bu, sorun gidermenize yardımcı olacak bilgiler ile sonuçlanır](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Hana\_yapılandırma\_Minichecks için aynısını yapın ve _C_ (kritik) sütununda herhangi bir _X_ işaretini kontrol edin.

Örnek çıktılar:

Genel SAP HANA denetimleri için **Hana\_Configuration\_MiniChecks\_Rev 102.01 + 1** .

![Hana\_Configuration\_MiniChecks\_Rev 102.01 + 1 Genel SAP HANA denetimleri için](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**Hana\_hizmetlerine\_genel bakış** SAP HANA Hizmetleri 'nin şu anda çalıştığı konusuna genel bakış.

![Hana\_hizmetlerine\_genel bakış SAP HANA Hizmetleri 'nin şu anda çalıştığı hakkında genel bakış](./media/troubleshooting-monitoring/image12-services-overview.png)

SAP HANA hizmet bilgileri için **\_Hana Hizmetleri\_istatistikleri** (CPU, bellek, vb.).

![SAP HANA\_hizmeti\_bilgileri için Hana Hizmetleri istatistikleri](./media/troubleshooting-monitoring/image13-services-statistics.png)

SAP HANA örneği hakkında genel bilgi için **Hana\_yapılandırmasına\_genel\_bakış Rev110 +** .

![SAP HANA\_örneği\_hakkında\_genel bilgi için Hana yapılandırmasına genel bakış Rev110 +](./media/troubleshooting-monitoring/image14-configuration-overview.png)

SAP HANA parametrelerini denetlemek için **Hana\_yapılandırma\_\_parametreleri Rev70 +** .

![SAP HANA\_parametrelerini\_denetlemek\_için Hana yapılandırma parametreleri Rev70 +](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Sonraki adımlar**

- [STONITH kullanarak SUSE 'de yüksek kullanılabilirlik kümesine](ha-setup-with-stonith.md)bakın.