---
title: Azure NetApp Files için maliyet modeli | Microsoft Docs
description: ', Hizmetten giderleri yönetmek için Azure NetApp Files maliyet modelini açıklar.'
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995110"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp Files için maliyet modeli 

Azure NetApp Files maliyet modelini anlamak, hizmetten harcamalarınızı yönetmenize yardımcı olur.

## <a name="calculation-of-capacity-consumption"></a>Kapasite tüketiminin hesaplanması

Azure NetApp Files, sağlanan depolama kapasitesine göre faturalandırılır.  Sağlanan kapasite, kapasite havuzları oluşturularak ayrılır.  Kapasite havuzları, saatlik artışlarla $/provisioned-gib/aya göre faturalandırılır. Tek bir kapasite havuzu için en küçük boyut 4 TiB 'dir ve kapasite havuzları daha sonra 1-TiB artışlarla genişletilebilir. Birimler kapasite havuzları içinde oluşturulur.  Her birime havuzlar tarafından sağlanan kapasiteden bir kota atanır. En az 100 GiB 'ye kadar birimlere atanabilecek kota, en fazla 100 TiB olabilir.  

Etkin bir birimde, kotaya karşı kapasite tüketimi mantıksal (etkin) kapasiteye göre belirlenir.

Bir birimin gerçek kapasite tüketimi, depolama kotasını aşarsa, birim büyümeye devam edebilir. Gerçek birim boyutu sistem sınırından (100 TiB) daha az olduğu sürece yazma işlemleri yine de izin verilecek.  

Kapasite havuzundaki toplam kullanılan kapasite, sağlanan tutara göre, havuzdaki tüm birimlerin atanan kotasının veya gerçek tüketiminin toplamıdır: 

   ![Kullanılan toplam kapasite hesaplaması](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Aşağıdaki diyagramda bu kavramlar gösterilmektedir.  
* 4 TiB sağlanan kapasiteye sahip bir kapasite havuzumuz var.  Havuz üç birim içerir.  
    * 1\. birime 2 TiB kotası atanır ve 800 GiB tüketimine sahiptir.  
    * 2\. birime, 1 TiB kotası atanır ve 100 GiB tüketimine sahiptir.  
    * Birim 3 ' ün bir 500 GiB kotası atanır ancak 800 GiB tüketimine sahiptir (fazla kullanım).  
* Kapasite havuzu 4 TiB kapasite (sağlanan miktar) için ölçümdedir.  
    3,8 TiB kapasitesi (1 ve 2. birimler 'den 2 GB ve 1 TiB kotayı ve birim 3 ' ü için 800 GiB) tüketilebilir. Ve 200 kapasite kalmadı.

   ![Üç hacime sahip kapasite havuzu](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Kapasite tüketimine fazla kullanım  

Bir havuzun toplam kullanılan kapasitesi sağlanan kapasiteyi aşarsa, veri yazmaları gene de izin verilir.  Yetkisiz kullanım süresi (bir saat) geçtikten sonra, havuzun kullanılan kapasitesi sağlanan kapasiteyi aşarsa, sağlanan kapasite toplam kullanılan kapasiteden daha fazla olana kadar havuz boyutu otomatik olarak 1 TiB artışlarına yükseltilir.  Örneğin, yukarıdaki çizimde, hacim 3 büyümeye devam ediyorsa ve gerçek tüketim 1,2 TiB 'ye ulaşırsa, yetkisiz kullanım süresinden sonra havuz otomatik olarak 5 TiB olarak yeniden boyutlandırılır.  Sonuç olarak, sağlanan havuz kapasitesi (5 TiB) kullanılan kapasiteyi (4,2 TiB) aşmaktadır.  

## <a name="manual-changes-of-the-pool-size"></a>Havuz boyutunun el ile değişiklikleri  

Havuz boyutunu el ile artırabilir veya azaltabilirsiniz. Ancak, aşağıdaki kısıtlamalar geçerlidir:
* Hizmet en düşük ve en fazla sınırları  
    [Kaynak limitleri](azure-netapp-files-resource-limits.md)hakkındaki makaleye bakın.
* İlk 4-TiB en düşük satın alma işleminden sonra 1-TiB artışı
* Bir saatlik minimum faturalama artışı
* Sağlanan havuz boyutu, havuzdaki toplam kullanılan kapasiteye kıyasla daha düşük bir boyuta düşmeyebilir.

## <a name="behavior-of-maximum-size-pool-overage"></a>Maksimum boyut havuzu fazla kullanım davranışı   

Oluşturabileceğiniz veya yeniden boyutlandırabileceğiniz bir kapasite havuzunun en büyük boyutu 500 TiB olur.  Bir kapasite havuzundaki kullanılan toplam kapasite 500 TiB 'yi aşarsa aşağıdaki durumlar oluşur:
* Veri yazma işlemleri yine de izin verilecek (birim, 100 TiB sistem sınırının altındaysa).
* Bir saatlik yetkisiz kullanım süresinden sonra, havuz sağlanan kapasite toplam kullanılan kapasiteyi aşana kadar havuz, 1-TiB artışlarla otomatik olarak yeniden boyutlandırılır.
* Birim kotası atamak için 500 TiB 'yi aşan ek sağlanmış ve faturalandırılan havuz kapasitesi kullanılamaz. Ayrıca, performans QoS sınırlarını genişletmek için kullanılamaz.  
    Performans sınırları ve QoS boyutlandırma hakkında [hizmet düzeylerine](azure-netapp-files-service-levels.md) bakın.

Aşağıdaki diyagramda şu kavramlar gösterilmektedir:
* Premium Depolama katmanına sahip bir kapasite havuzumuz ve 500-TiB kapasitesi vardır. Havuz dokuz birim içerir.
    * 1 ile 8 arasında birimlere her biri 60 bir kota atanır.  Kullanılan toplam kapasite 480 TiB 'dir.  
        Her birimde 3,75 GiB/sn (60 TiB * 64 MIB/s) için bir QoS sınırı vardır.  
    * 9\. birime 20 TiB kotası atanır.  
        9\. birimde, 1,25 GiB/sn (20 TiB * 64 MIB/s) için bir QoS sınırı vardır.
* Birim 9, bir fazla kullanım senaryosudur. Bu, gerçek tüketim olan 25 GB 'a sahiptir.  
    * Bir saatlik yetkisiz kullanım süresinden sonra, kapasite havuzu 505 TiB olarak yeniden boyutlandırılır.  
        Diğer bir deyişle, 1 ile 8 arasında birimler için kullanılan toplam kapasite = 8 * 60-TiB kotası ve birim 9 için 25 TiB gerçek tüketim.
    * Faturalanan kapasite 505 TiB 'dir.
    * Birime atanan toplam kota 500 TiB 'yi aşamadığından, birim 9 için birim kotası arttırılamaz.
    * Havuzun toplam QoS hala 500 TiB 'yi temel alan için ek QoS aktarım hızı atanmayabilir.

   ![Dokuz birim içeren kapasite havuzu](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Anlık görüntülerin kapasite tüketimi 

Azure NetApp Files içindeki anlık görüntülerin kapasite tüketimi, üst birimin kotasına göre ücretlendirilir.  Sonuç olarak, birimin ait olduğu kapasite havuzuyla aynı faturalandırma oranını paylaşır.  Ancak, etkin birimin aksine, anlık görüntü tüketimi tüketilen artımlı kapasiteye göre ölçülür.  Azure NetApp Files anlık görüntüleri doğası gereği fark edilir. Verilerin değişim hızına bağlı olarak, anlık görüntüler genellikle etkin birimin mantıksal kapasitesinden çok daha az kapasite tüketir. Örneğin, yalnızca 10 GiB fark verisi içeren bir 500-GiB biriminin anlık görüntüsüdür olduğunu varsayalım. Bu anlık görüntü için birim kotasına göre ücretlendirilen kapasite 500 GiB değil 10 GiB olacaktır. 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure NetApp Files fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files için hizmet düzeyleri](azure-netapp-files-service-levels.md)
* [Azure NetApp Files için kaynak sınırları](azure-netapp-files-resource-limits.md)
