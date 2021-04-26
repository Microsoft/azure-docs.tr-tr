---
title: Azure NSG akış günlüklerini görselleştirme-Power BI
titleSuffix: Azure Network Watcher
description: Azure ağ Izleyicisi 'nde IP trafiği hakkındaki bilgileri görüntülemenize olanak tanımak üzere ağ güvenlik grubu akış günlüklerini görselleştirmek için Power BI nasıl kullanacağınızı öğrenin.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 50f08ffc304962cc66f6eed07adcb5b7e4aa6692
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011161"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Power BI ile ağ güvenlik grubu akış günlüklerini görselleştirme

Ağ güvenlik grubu akış günlükleri, ağ güvenlik gruplarında giriş ve çıkış IP trafiği hakkındaki bilgileri görüntülemenize olanak sağlar. Bu akış günlükleri her kural temelinde giden ve gelen akışları gösterir, akışın akış (kaynak/hedef IP 'si, kaynak/hedef bağlantı noktası, protokol) hakkında, 5 demet bilgileri için geçerli olduğu ve trafiğe izin verildiyse veya reddedildiyse.

Günlük dosyalarını el ile arayarak akış günlüğü verilerine yönelik Öngörüler elde etmek zor olabilir. Bu makalede, en son akış günlüklerinizi görselleştirmeye yönelik bir çözüm sunuyoruz ve ağınızdaki trafik hakkında bilgi edinebilirsiniz.

> [!Warning]  
> Aşağıdaki adımlar akış günlükleri sürüm 1 ile birlikte çalışır. Ayrıntılar için bkz. [ağ güvenlik grupları için akış günlüğüne giriş](network-watcher-nsg-flow-logging-overview.md). Aşağıdaki yönergeler, değişiklik yapılmadan günlük dosyalarının 2. sürümüyle birlikte çalışmayacaktır.

## <a name="scenario"></a>Senaryo

Aşağıdaki senaryoda, Power BI Desktop 'ı NSG akış günlüğü verilerimizin havuzu olarak yapılandırdığımız depolama hesabına bağlayacağız. Depolama hesabımızla bağlantı kurulduktan sonra, ağ güvenlik grupları tarafından günlüğe kaydedilen trafiğin görsel bir gösterimini sağlamak için günlükleri indirir ve ayrıştırır Power BI.

Şablonda sağlanan görselleri kullanarak şunları inceleyebilirsiniz:

* En üstteki Taliciler
* Yön ve kural kararına göre zaman serisi akış verileri
* Ağ arabirimi MAC adresine göre akışlar
* NSG ve kurala göre akışlar
* Hedef bağlantı noktasına göre akışlar

Belirtilen şablon düzenlenebilir olduğundan, gereksinimlerinize uyacak şekilde yeni veriler, görseller veya sorguları düzenlemek üzere değişiklik yapabilirsiniz.

## <a name="setup"></a>Kurulum

Başlamadan önce, hesabınızdaki bir veya daha fazla ağ güvenlik grubunda ağ güvenlik grubu akış günlüğü etkin olmalıdır. Ağ güvenlik akışı günlüklerinin etkinleştirilmesi hakkındaki yönergeler için aşağıdaki makaleye bakın: [ağ güvenlik grupları için akış günlüğüne giriş](network-watcher-nsg-flow-logging-overview.md).

Ayrıca, makinenizde Power BI Desktop istemcisinin yüklü olması ve depolama hesabınızda bulunan günlük verilerini indirmek ve yüklemek için makinenizde yeterli boş alan olması gerekir.

![Visio diyagramı][1]

### <a name="steps"></a>Adımlar

1. Aşağıdaki Power BI şablonunu indirin ve Power BI Desktop uygulama [ağ Izleyicisi PowerBI akış günlükleri şablonunda](https://aka.ms/networkwatcherpowerbiflowlogstemplate) açın
1. Gerekli sorgu parametrelerini girin
   1. **StorageAccountName** : yüklemek ve görselleştirmek istediğiniz NSG akış günlüklerini içeren depolama hesabının adını belirtir.
   1. **NumberOfLogFiles** : Power BI indirmek ve görselleştirmek istediğiniz günlük dosyası sayısını belirtir. Örneğin, 50 50 belirtilirse en son günlük dosyaları. 2 NSG 'ler etkin ve bu hesaba NSG akış günlükleri gönderecek şekilde yapılandırıldıysa, son 25 saat günlük görüntülenebilir.

      ![Power BI ana][2]

1. Depolama hesabınız için erişim anahtarını girin. Azure portal ' de depolama hesabınıza gidip ayarlar menüsünden **erişim tuşları** ' nı seçerek geçerli erişim anahtarlarını bulabilirsiniz. **Bağlan** ' a tıklayın ve ardından değişiklikleri uygulayın.

    ![erişim tuşları][3]

    ![erişim anahtarı 2][4]

4. Günlükleriniz indirilir ve ayrıştırılır ve artık önceden oluşturulmuş görselleri kullanabilirsiniz.

## <a name="understanding-the-visuals"></a>Görselleri anlama

Şablonda sağlanan bir dizi görseldir ve NSG akış günlüğü verilerini anlamlı hale getirir. Aşağıdaki görüntüler, verilerle doldurulduğu sırada panonun nasıl göründüğüne ilişkin bir örnek gösterir. Aşağıda her görseli daha ayrıntılı olarak inceliyoruz 

![powerbi][5]
 
Üst düzey bir görselde, belirtilen süre boyunca en fazla bağlantıyı başlatan IP 'Leri gösterir. Kutuların boyutu, göreli bağlantı sayısına karşılık gelir. 

![toptaliciler][6]

Aşağıdaki zaman serisi grafiklerde, dönem içindeki akış sayısı gösterilir. Üst grafik akış yönüne göre bölündü ve daha düşük olan karar (izin verme veya reddetme) tarafından bölündü. Bu Görselle trafik eğilimlerini zaman içinde inceleyebilir ve trafik veya trafik segmentlerinin olağandışı artışlarını veya reddetmeyi belirleyebilirsiniz.

![flowsoverperiod][7]

Aşağıdaki grafiklerde, akış yönüne göre üst kesimli ve kararlara göre aşağı kesimli bir ağ arabirimi başına akış gösterilmektedir. Bu bilgilerle, sanal makinelerinizdeki diğer kullanıcılara en iyi şekilde iletildiğini ve belirli bir VM 'ye giden trafiğe izin verilmesi veya reddedilmesi hakkında öngörüler elde edebilirsiniz.

![flowspernıc][8]

Aşağıdaki halka tekerleği grafiği, hedef bağlantı noktasına göre akışların dökümünü gösterir. Bu bilgilerle, belirtilen dönemde kullanılan en yaygın olarak kullanılan hedef bağlantı noktalarını görüntüleyebilirsiniz.

![halka][9]

Aşağıdaki çubuk grafik, NSG ve kurala göre akışı gösterir. Bu bilgilerle, en çok trafikten sorumlu NSG 'leri ve bir NSG 'nin kurala göre dökümünü bulabilirsiniz.

![bargrafik][10]
 
Aşağıdaki bilgilendirici grafiklerde, günlüklerde bulunan NSG 'ler, dönem boyunca yakalanan akış sayısı ve yakalanan en erken günlüğün tarihi hakkındaki bilgileri görüntüler. Bu bilgiler, hangi NSG 'lerin günlüğe kaydedildiği ve akış tarih aralığı hakkında fikir verir.

![infochart1][11]

![infochart2][12]

Bu şablon, yalnızca en ilgilendiğiniz verileri görüntülemenize olanak tanımak için aşağıdaki Dilimleyicileri içerir. Kaynak gruplarınız, NSG 'ler ve kurallarınız üzerinde filtre uygulayabilirsiniz. Ayrıca, 5 demet bilgisini, kararı ve günlüğün yazıldığı saati de filtreleyebilirsiniz.

![Dilimleyicileri][13]

## <a name="conclusion"></a>Sonuç

Ağ Izleyicisi ve Power BI tarafından sunulan ağ güvenlik grubu akış günlüklerini kullanarak bu senaryoda, trafiği görselleştirebildiğimiz ve anlayabiliyoruz. Power BI, belirtilen şablonu kullanarak günlükleri doğrudan depolamadan indirir ve yerel olarak işler. Şablonun yüklenmesi için geçen süre, istenen dosya sayısına ve indirilen dosyaların toplam boyutuna bağlı olarak değişir.

Bu şablonu gereksinimlerinize uygun şekilde özelleştirebilirsiniz. Ağ güvenlik grubu akış günlükleri ile Power BI kullanmanın çok çeşitli yolları vardır. 

## <a name="notes"></a>Notlar

* Günlükler varsayılan olarak saklanır `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Başka bir dizinde diğer veriler varsa, verilerin çekme ve işleme sorgularının değiştirilmesi gerekir.

* Belirtilen şablon, 1 GB 'den fazla günlüğe sahip kullanım için önerilmez.

* Büyük miktarda günlükiz varsa, Data Lake veya SQL Server gibi başka bir veri deposu kullanarak bir çözümü araştırmanızı öneririz.

## <a name="next-steps"></a>Sonraki Adımlar

[Açık kaynak araçları kullanarak Azure ağ IZLEYICISI NSG akış günlüklerini görselleştirme](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) ' i ziyaret ederek NSG akış günlüklerinizi esnek yığın ile görselleştirmeyi öğrenin

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
