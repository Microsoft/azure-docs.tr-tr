---
title: Azure 'da SAP HANA 'da HANA tarafında izleme ve sorun giderme (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA (büyük örnekler) üzerinde HANA tarafında izleme ve sorun giderme.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83743a6985bef8ce6c03e01ed8d10aa740852106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668813"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>HANA tarafından izleme ve sorun giderme

Azure 'daki SAP HANA ilgili sorunları (büyük örnekler) etkin bir şekilde çözümlemek için, bir sorunun kök nedenini daraltmak yararlı olur. SAP, size yardımcı olmak için büyük miktarda belge yayımladı.

SAP HANA performansla ilgili geçerli SSS 'ler aşağıdaki SAP notlarında bulunabilir:

- [SAP Note #2222200 – SSS: SAP HANA ağ](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Note #2100040 – SSS: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Note #199997 – SSS: SAP HANA bellek](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Note #200000 – SSS: SAP HANA performans Iyileştirmesi](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Not #199930 – SSS: SAP HANA g/ç Analizi](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Note #2177064 – SSS: SAP HANA hizmeti yeniden başlatma ve çökme](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA uyarılar

İlk adım olarak, geçerli SAP HANA uyarı günlüklerini denetleyin. SAP HANA Studio 'da **Yönetim konsoluna gidin: Uyarılar: göster: tüm uyarılar**. Bu sekmede, en düşük ve en yüksek eşiklerin dışında kalan belirli değerler (boş fiziksel bellek, CPU kullanımı vb.) için tüm SAP HANA uyarıları gösterilir. Varsayılan olarak, denetimler her 15 dakikada bir otomatik olarak yenilenir.

![SAP HANA Studio 'da yönetim konsoluna gidin: Uyarılar: göster: tüm uyarılar](./media/troubleshooting-monitoring/image1-show-alerts.png)

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

Yüksek CPU kullanımı nedeniyle tetiklenen bir uyarı olabilir, ancak bunlarla sınırlı olmamak üzere çeşitli nedenlerden kaynaklanabilir: belirli işlemler, veri yükleme, yanıt vermeyen işler, uzun süre çalışan SQL deyimleri ve hatalı sorgu performansı (örneğin, HANA küplerinde bant genişliği ile).

Ayrıntılı sorun giderme adımları için [SAP HANA sorun giderme: CPU Ile Ilgili nedenler ve çözümler](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) sitesine bakın.

## <a name="operating-system"></a>Operating System

Linux üzerinde SAP HANA en önemli denetimlerinden biri, saydam büyük sayfaların devre dışı bırakıldığından emin olmak için, [SAP HANA sunucularında SAP Note #2131662 – saydam büyük sayfalar (THP)](https://launchpad.support.sap.com/#/notes/2131662)konusuna bakın.

- Saydam büyük sayfaların şu Linux komutu aracılığıyla etkinleştirilip etkinleştirilmediğini kontrol edebilirsiniz: **Cat/sys/kernel/mm/Transparent \_ kugepage/Enabled**
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

Ayrıntılı sorun giderme adımları için [SAP HANA sorun giderme: bellek sorunları](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) sitesine bakın.

## <a name="network"></a>Ağ

[SAP note #2081065 – SAP HANA ağ sorunlarını giderme](https://launchpad.support.sap.com/#/notes/2081065) ve bu SAP notunda ağ sorun giderme adımlarını gerçekleştirme bölümüne bakın.

1. Sunucu ve istemci arasındaki gidiş dönüş süresini çözümleme.
  A. SQL betiği [_Hana \_ Ağ \_ istemcilerini_](https://launchpad.support.sap.com/#/notes/1969700)çalıştırın _._
  
2. Düğümler arası iletişimi analiz edin.
  A. SQL betiği [_Hana \_ Ağ \_ hizmetlerini_](https://launchpad.support.sap.com/#/notes/1969700)çalıştırın _._

3. Linux komutunu çalıştırın **ıconfig** (çıkış, herhangi bir paket kayıpları oluşup oluşmadığını gösterir).
4. Linux komutunu çalıştırın **tcpdump**.

Ayrıca, gerçek uygulama ağı performansını ölçmek için açık kaynak [Iperf](https://iperf.fr/) aracını (veya benzer) kullanın.

Ayrıntılı sorun giderme adımları için [SAP HANA sorun giderme: ağ performansı ve bağlantı sorunları](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) sitesine bakın.

## <a name="storage"></a>Depolama

Bir son kullanıcı perspektifinden, bir uygulama (veya bir bütün olarak sistem) yavaş çalışır, yanıt vermemeye başlıyor veya g/ç performansı sorunları varsa yanıt vermeyi durdurmuş gibi görünse de durabilir. SAP HANA Studio 'daki **birimler** sekmesinde, eklenen birimleri ve her bir hizmet tarafından hangi birimlerin kullanıldığını görebilirsiniz.

![SAP HANA Studio 'daki birimler sekmesinde, eklenen birimleri ve her bir hizmet tarafından kullanılan birimleri görebilirsiniz](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Ekranın alt kısmındaki eklenen birimler, birimlerin ayrıntılarını (örneğin, dosyalar ve g/ç istatistikleri) görebilirsiniz.

![Ekranın alt kısmındaki eklenen birimler, birimlerin ayrıntılarını (örneğin, dosyalar ve g/ç istatistikleri) görebilirsiniz](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

[SAP HANA sorun giderme: g/ç Ile Ilgili temel nedenler ve çözümler](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) ve SAP HANA sorun giderme: ayrıntılı sorun giderme adımları Için [diskle ilgili kök nedenler ve çözümler](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) sitesi.

## <a name="diagnostic-tools"></a>Tanılama Araçları

HANA yapılandırma Minichecks aracılığıyla SAP HANA bir sistem durumu denetimi gerçekleştirin \_ \_ . Bu araç, SAP HANA Studio 'da zaten uyarı olarak oluşturulmuş olması gereken önemli olabilecek teknik sorunları döndürür.

[SAP HANA Için SAP Note #1969700 – SQL deyimkoleksiyonu](https://launchpad.support.sap.com/#/notes/1969700) ' na bakın ve söz konusu nota eklenen SQL Statements.zip dosyasını indirin. Bu. zip dosyasını yerel sabit sürücüde depolayın.

SAP HANA Studio 'da, **sistem bilgileri** sekmesinde **ad** sütununa sağ tıklayın ve **SQL deyimlerini içeri aktar**' ı seçin.

![SAP HANA Studio 'da, sistem bilgileri sekmesinde Ad sütununa sağ tıklayın ve SQL deyimlerini Içeri Aktar ' ı seçin.](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Yerel olarak depolanan SQL Statements.zip dosyasını ve karşılık gelen SQL deyimleriyle birlikte içeri aktarılacak bir klasörü seçin. Bu noktada, birçok farklı tanılama denetimi bu SQL deyimleriyle çalıştırılabilir.

Örneğin, SAP HANA sistem çoğaltması bant genişliği gereksinimlerini test etmek için **çoğaltma: bant genişliği** altında **bant genişliği** BILDIRIMINE sağ tıklayın ve SQL konsolunda **Aç** ' ı seçin.

Tüm SQL deyimleri, giriş parametrelerinin (değiştirme bölümü) değiştirilmesine ve sonra yürütülmesine izin vererek açılır.

![Tüm SQL deyimleri, giriş parametrelerinin (değiştirme bölümü) değiştirilmesine ve sonra yürütülmesine izin vererek açılır](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Diğer bir örnek, **çoğaltma: genel bakış** altındaki deyimlere sağ tıklandır. Bağlam menüsünden **Çalıştır** ' ı seçin:

![Diğer bir örnek, çoğaltma: genel bakış altındaki deyimlere sağ tıklandır. Bağlam menüsünden Yürüt ' ü seçin](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Bu, sorun gidermenize yardımcı olan bilgiler ile sonuçlanır:

![Bu, sorun gidermenize yardımcı olacak bilgiler ile sonuçlanır](./media/troubleshooting-monitoring/image10-import-statements-d.png)

HANA yapılandırma Minichecks için aynısını \_ yapın \_ ve _C_ (kritik) sütununda herhangi bir _X_ işaretini kontrol edin.

Örnek çıktılar:

**Hana \_ Genel SAP HANA denetimleri için yapılandırma \_ MiniChecks \_ Rev 102.01 + 1** .

![HANA \_ Configuration \_ MiniChecks \_ Rev 102.01 + 1 Genel SAP HANA denetimleri için](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**Hana \_ Hizmetlere genel \_ bakış** SAP HANA Hizmetleri şu anda çalışıyor.

![HANA \_ hizmetlerine \_ genel bakış SAP HANA Hizmetleri 'nin şu anda çalıştığı hakkında genel bakış](./media/troubleshooting-monitoring/image12-services-overview.png)

**Hana \_ SAP HANA \_** Service ıNFORMATION (CPU, bellek, vb.) için hizmet istatistikleri.

![\_ \_ SAP HANA hizmeti BILGILERI için Hana Hizmetleri istatistikleri](./media/troubleshooting-monitoring/image13-services-statistics.png)

**Hana \_ SAP HANA örneğiyle ilgili genel bilgiler için yapılandırmaya \_ Genel Bakış \_ Rev110 +** .

![\_ \_ \_ SAP HANA örneği hakkında genel bilgi için Hana yapılandırmasına genel bakış Rev110 +](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**Hana \_ SAP HANA parametrelerini denetlemek için \_ \_ Rev70 + yapılandırma parametreleri** .

![\_ \_ \_ SAP HANA PARAMETRELERINI denetlemek için Hana yapılandırma parametreleri Rev70 +](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Sonraki adımlar**

- [STONITH kullanarak SUSE 'de yüksek kullanılabilirlik kümesine](ha-setup-with-stonith.md)bakın.