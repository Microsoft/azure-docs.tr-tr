---
title: StorSimple Snapshot Manager Kullanıcı arabirimi | Microsoft Docs
description: StorSimple Snapshot Manager Kullanıcı arabirimini açıklar ve yedekleme işlerini ve yedekleme kataloğunu yönetmek için nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 35fa0fc134045585eebd21324352f9b4ff272e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954248"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Yedekleme işlerini ve yedekleme kataloğunu yönetmek için StorSimple Snapshot Manager Kullanıcı arabirimini kullanma

## <a name="overview"></a>Genel Bakış
StorSimple Snapshot Manager, yedeklemeleri alıp yönetmek için kullanabileceğiniz, sezgisel bir kullanıcı arabirimine sahiptir. Bu öğretici, Kullanıcı arabirimine bir giriş sağlar ve sonra bileşenlerin her birinin nasıl kullanılacağını açıklar. StorSimple Snapshot Manager ayrıntılı bir açıklaması için bkz. [storsimple Snapshot Manager nedir?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Konsol açıklaması
Kullanıcı arabirimini görüntülemek için, masaüstünüzdeki StorSimple Snapshot Manager simgesine tıklayın. Konsol penceresi, aşağıdaki çizimde gösterildiği gibi görünür.

![StorSimple Snapshot Manager bölmeleri](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Konsol penceresinin beş ana öğesi vardır. Her öğenin tamamen açıklaması için uygun bağlantıya tıklayın.

* [Menü çubuğu](#menu-bar) 
* [Araç çubuğu](#tool-bar) 
* [Kapsam bölmesi](#scope-pane) 
* [Sonuçlar bölmesi](#results-pane) 
* [Eylemler bölmesi](#actions-pane) 

Ayrıca, StorSimple Snapshot Manager [klavye gezintisini ve çok sayıda kısayolu](#keyboard-navigation-and-shortcuts)destekler.

### <a name="console-accessibility"></a>Konsol erişilebilirliği
StorSimple Snapshot Manager Kullanıcı arabirimi, Windows işletim sisteminin yanı sıra Microsoft Yönetim Konsolu (MMC) tarafından sunulan erişilebilirlik özelliklerini ve bazı StorSimple Snapshot Manager özgü klavye kısayollarını destekler. 

* Windows erişilebilirlik özelliklerinin açıklaması için [Windows Için klavye kısayolları](https://support.microsoft.com/kb/126449)' na gidin. 
* MMC erişilebilirlik özelliklerinin açıklaması için bkz. [MMC Için erişilebilirlik 3,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc766075(v=ws.11))
* StorSimple Snapshot Manager erişilebilirlik özelliklerinin bir açıklaması için [klavye gezintisi ve kısayollar](#keyboard-navigation-and-shortcuts)' a gidin.

## <a name="menu-bar"></a>Menü çubuğu
Konsol penceresinin en üstündeki menü çubuğu [Dosya](#file-menu), [eylem](#action-menu), [Görünüm](#view-menu), [Sık Kullanılanlar](#favorites-menu), [pencere](#window-menu)ve [Yardım](#help-menu) menülerini içerir.

Menüdeki kullanılabilir komutların listesini görmek için menü çubuğundaki herhangi bir öğeye tıklayın. Aşağıdaki örnek, menü çubuğunda seçilen **Görünüm** menüsünü gösterir.

![Görünüm menüsü seçili](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Dosya menüsü
**Dosya** menüsünde standart Microsoft Yönetim Konsolu (MMC) komutları bulunur.

#### <a name="menu-access"></a>Menü erişimi
**Dosya** menüsünü görüntülemek için, menü çubuğunda **Dosya** ' ya tıklayın. Aşağıdaki menü görünür.

![StorSimple Snapshot Manager dosya menüsü](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Menü açıklaması
Aşağıdaki tabloda **Dosya** menüsünde görünen öğeler açıklanmaktadır.

| Menü öğesi | Description |
|:--- |:--- |
| Yeni |StorSimple Snapshot Manager temel alan yeni bir konsol oluşturmak için **Yeni** ' ye tıklayın. |
| Aç |Mevcut bir konsolu açmak için **Aç** ' a tıklayın. |
| Kaydet |Geçerli konsolu kaydetmek için **Kaydet** ' e tıklayın. |
| Farklı kaydet |Geçerli konsolun yeni, yeniden adlandırılmış bir örneğini oluşturmak için **farklı kaydet** ' e tıklayın. Bir görünümü özelleştirmek ve daha sonra almak üzere kaydetmek için **farklı kaydet** seçeneğini kullanın. Örneğin, belirli sunuculara işaret eden StorSimple Snapshot Manager ek bileşenleri oluşturabilirsiniz. |
| Ek bileşen Ekle/Kaldır |Ek bileşen eklemek veya kaldırmak ve **kapsam** bölmesinde düğümleri düzenlemek Için **ek bileşen Ekle/Kaldır** ' a tıklayın. Daha fazla bilgi için, [MMC 3,0 ' de ek bileşenler ve uzantılar ekleme, kaldırma ve düzenleme](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc722035(v=ws.11))bölümüne gidin. |
| Seçenekler |Konsol simgesini değiştirmek, Kullanıcı erişimi modlarını ve izinlerini belirtmek veya kullanılabilir disk alanını artırmak için konsol dosyalarını silmek için **Seçenekler** ' e tıklayın. |
| Dosya yollarının listesi |Son açtığınız bir dosyayı yeniden açmak için numaralandırılmış listede bir yola tıklayın. |
| Çıkış |**Dosya** menüsünü kapatmak için **Çıkış** ' a tıklayın. |

### <a name="action-menu"></a>Eylem menüsü
Kullanılabilir eylemlerden seçim yapmak için **eylem** menüsünü kullanın. Sizin için kullanılabilir öğeler, **kapsam** bölmesi veya **sonuçlar** bölmesinde yaptığınız seçime bağlıdır.

#### <a name="menu-access"></a>Menü erişimi
**Eylem** menüsünü görüntülemek için aşağıdakilerden birini yapın:

* **Kapsam** bölmesi veya **sonuçlar** bölmesinde bir öğeye sağ tıklayın.
* **Kapsam** bölmesi veya **sonuçlar** bölmesinde bir öğe seçin ve ardından menü çubuğunda **eylem** ' i tıklatın. 

Örneğin, **kapsam** bölmesinde en üstteki düğümü seçer ve ardından menü çubuğunda sağ tıklayıp ya da **eylem** ' e tıkladığınızda, aşağıdaki menü görünür.

![StorSimple Snapshot Manager Eylem menüsü](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

**Eylemler** bölmesi (konsolunun sağında) **eylem** menüsüyle aynı eylem listesini içerir. Ayrıca, **Eylemler** bölmesi, **sonuçlar** bölmesinin özel bir görünümünü oluşturmanızı sağlayan **Görünüm** menüsü seçeneklerini içerir.

![Görünüm menüsü açık eylemler bölmesi](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Menü açıklaması
Aşağıdaki tablo, StorSimple Snapshot Manager eylemlerinin alfabetik bir listesini içerir. 

* **Eylem** sütunu, düğümler ve sonuçlar üzerinde gerçekleştirebileceğiniz eylemleri listeler. 
* **Gezinti** sütunu, eylemi seçebilmeniz Için uygun **eylem** menüsünün nasıl görüntüleneceğini açıklar. Bazı eylemler birden çok **eylem** menülerinde görünür. Bu eylemler için madde işaretli listeden bir **Gezinti** seçeneği belirleyin. 
* **Açıklama** sütunu, **eylem** menüsünde veya Eylemler bölmesinde her bir eylemin nasıl kullanılacağını açıklar ve ne yaptığını açıklar.

> [!NOTE]
> **Eylemler** bölmesi ve **eylem** menüleri, **Görünüm**, **buradan yeni pencere**, **yenileme**, **dışarı aktarma listesi** ve **Yardım** gibi ek seçenekler içerir. Bu seçenekler, MMC 'nin bir parçası olarak kullanılabilir ve StorSimple Snapshot Manager özgü değildir. Tablo, bu seçeneklerin açıklamalarını içerir.
> 
> 

| Eylem | Gezinti | Description |
|:--- |:--- |:--- |
| Kimlik doğrulaması |**Cihazlar** düğümüne tıklayın ve **sonuçlar** bölmesinde bir cihaza sağ tıklayın. |Cihaz için yapılandırdığınız parolayı girmek için **kimlik doğrulaması** ' na tıklayın. |
| Kopyalama |**Yedekleme kataloğu**' nu genişletin, **bulut anlık görüntüleri**' ni genişletin, bulunan bir yedeğe tıklayın ve ardından **sonuçlar** bölmesinde bir birim seçin. |Bir bulut anlık görüntüsünün kopyasını oluşturmak ve bunu belirleyeceğiniz bir konumda depolamak için **Kopyala** ' ya tıklayın. |
| Cihaz yapılandırma |**Cihazlar** düğümüne sağ tıklayın. |Tek bir cihazı veya birden çok cihazı Windows ana bilgisayarına bağlanacak şekilde yapılandırmak için **cihaz yapılandırma** ' ya tıklayın. |
| Yedekleme Ilkesi oluştur |Aşağıdakilerden birini yapın:<ul><li>**Yedekleme ilkeleri**' ne sağ tıklayın.</li><li>**Birim grupları**' na tıklayın veya genişletin ve ardından bir birim grubuna sağ tıklayın.</li><li>**Yedekleme kataloğu**' na tıklayın veya genişletin ve ardından bir birim grubuna sağ tıklayın.</li></ul> |Bir birim grubu için zamanlanmış bir yedekleme yapılandırmak üzere **yedekleme Ilkesi oluştur** ' a tıklayın. |
| Birim grubu oluştur |Aşağıdakilerden birini yapın:<ul><li>**Birimler** düğümüne tıklayın ve ardından **sonuçlar** bölmesinde bir birime sağ tıklayın.</li><li>**Birim grupları** düğümüne sağ tıklayın.</li></ul> |Birim grubuna birimler atamak için **birim grubu oluştur** ' a tıklayın. |
| Sil |Bir düğüme veya sonuca tıklayın (Bu öğe birçok **eylem** menülerinde ve  **Eylemler** bölmelerinde görüntülenir.) |Seçtiğiniz düğümü veya sonucu silmek için **Sil** ' e tıklayın. Onay iletişim kutusu göründüğünde, silme işlemini onaylayın veya iptal edin. |
| Ayrıntılar |**Cihazlar** düğümüne tıklayın ve ardından **sonuçlar** bölmesinde bir cihaza sağ tıklayın. |Bir cihazın yapılandırma ayrıntılarını görmek için **Ayrıntılar** ' a tıklayın. |
| Düzenle |**Yedekleme ilkeleri**' ne tıklayın ve ardından **sonuçlar** bölmesinde bir ilkeye sağ tıklayın. |Birim grubu için Yedekleme zamanlamasını değiştirmek üzere **Düzenle** ' ye tıklayın. |
| Listeyi dışarı aktar |Herhangi bir düğüme veya sonuca tıklayın (Bu öğe tüm **eylem** menülerinde ve **Eylemler** bölmelerinde görüntülenir.) |Listeyi bir virgülle ayrılmış değer (CSV) dosyasına kaydetmek için **Listeyi dışarı aktar** ' a tıklayın. Daha sonra bu dosyayı analiz için bir elektronik tablo uygulamasına aktarabilirsiniz. |
| Help |Herhangi bir düğüme veya sonuca tıklayın. (Bu öğe tüm **eylem** menüleri ve **Eylemler** bölmeleri üzerinde görünür.) |Çevrimiçi yardım 'ı ayrı bir tarayıcı penceresinde açmak için **Yardım 'a** tıklayın. |
| Buradan Yeni Pencere |Herhangi bir düğüme veya sonuca tıklayın (Bu öğe tüm **eylem** menülerinde ve **Eylemler** bölmelerinde görüntülenir.) |Yeni bir StorSimple Snapshot Manager penceresi açmak için **buradan yeni pencere** ' ye tıklayın. |
| Yenile |Herhangi bir düğüme veya sonuca tıklayın (Bu öğe tüm **eylem** menülerinde ve **Eylemler** bölmelerinde görüntülenir.) |Şu anda görüntülenen StorSimple Snapshot Manager penceresini güncelleştirmek için **Yenile** ' ye tıklayın. |
| Cihazı Yenile |**Cihazlar** düğümüne tıklayın ve **sonuçlar** bölmesinde bir cihaza sağ tıklayın. |Belirli bir bağlı cihazı StorSimple Snapshot Manager ile eşleştirmek için **cihazı Yenile** ' ye tıklayın. |
| Cihazları Yenile |**Cihazlar** düğümüne sağ tıklayın. |Bağlı cihazlar listenizi StorSimple Snapshot Manager ile eşleştirmek için **cihazları Yenile** ' ye tıklayın. |
| Birimleri yeniden Tara |**Birimler** düğümüne sağ tıklayın. |**Sonuçlar** bölmesinde görüntülenen birimlerin listesini güncelleştirmek için **birimleri yeniden Tara** ' ya tıklayın. |
| Geri Yükleme |**Yedekleme kataloğu**' nu genişletin, bir birim grubunu genişletin, **Yerel anlık görüntüler** veya **bulut anlık görüntüleri**' ni genişletin ve ardından bir yedeklemeye sağ tıklayın. |Geçerli birim grubu verilerini seçilen Yedeklemedeki verilerle değiştirmek için **geri yükle** ' ye tıklayın. |
| Yedek al |Aşağıdakilerden birini yapın:<ul><li>**Birim grupları**' nı genişletin ve ardından bir birim grubuna sağ tıklayın.</li><li>**Yedekleme kataloğu**' nu genişletin ve ardından bir birim grubuna sağ tıklayın.</li></ul> |Yedekleme işini hemen başlatmak için **yedeklemeyi al** ' ı tıklatın. |
| Içeri aktarmalar görüntüsünü değiştirme |**Kapsam** bölmesinde üst düğüme sağ tıklayın (örneklerdeki **StorSimple Snapshot Manager** düğümü). |StorSimple Device Manager hizmet panosundan içeri aktarılan birim gruplarını ve ilişkili yedekleri göstermek veya gizlemek için **Içeri aktarmalar görüntüsünü değiştirme** ' ye tıklayın. |

### <a name="view-menu"></a>Görünüm menüsü
**Sonuçlar** bölmesi içeriğinin özel bir görünümünü oluşturmak için **Görünüm** menüsünü kullanın. **Görünüm** menüsünde **sütun Ekle/Kaldır** ve **Özelleştirme** seçenekleri bulunur.

#### <a name="menu-access"></a>Menü erişimi
Menü çubuğundaki veya **Eylemler** bölmesindeki **Görünüm** menüsüne erişebilirsiniz.

![StorSimple Snapshot Manager Görünüm menüsü](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Menü açıklaması
Aşağıdaki tabloda, **Görünüm** menüsünde görüntülenen öğeler açıklanmaktadır.

| Menü öğesi | Description |
|:--- |:--- |
| Sütun Ekle/Kaldır |**Sonuçlar** bölmesinde sütun eklemek veya kaldırmak Için **sütun Ekle/Kaldır** ' a tıklayın. |
| Özelleştirme |StorSimple Snapshot Manager konsol penceresinde öğeleri göstermek veya gizlemek için **Özelleştir** ' e tıklayın. |

### <a name="favorites-menu"></a>Sık Kullanılanlar menüsü
Sık kullandığınız sayfa görünümlerini ve görevleri eklemek, kaldırmak ve düzenlemek için **Sık Kullanılanlar** menüsünü kullanın. 

#### <a name="menu-access"></a>Menü erişimi
Menü çubuğundaki **Sık Kullanılanlar** menüsüne erişebilirsiniz.

![StorSimple Snapshot Manager sık kullanılanlar menüsü](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Menü açıklaması
Aşağıdaki tabloda, **Sık Kullanılanlar** menüsünde görüntülenen öğeler açıklanmaktadır.

| Menü öğesi | Description |
|:--- |:--- |
| Sık kullanılanlara ekleme |Geçerli görünümü sık kullanılanlar listenize eklemek için **Sık Kullanılanlara Ekle** ' ye tıklayın. |
| Sık Kullanılanları Düzenle |Sık Kullanılanlar klasörünüzün içeriğini düzenlemek için **Sık Kullanılanları Düzenle** ' ye tıklayın. |

### <a name="window-menu"></a>Pencere menüsü
StorSimple Snapshot Manager konsol pencerelerini eklemek ve yeniden düzenlemek için **pencere** menüsünü kullanın.

#### <a name="menu-access"></a>Menü erişimi
Menü çubuğundaki **pencere** menüsüne erişebilirsiniz.

![StorSimple Snapshot Manager pencere menüsü](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Menünün altındaki numaralandırılmış liste, şu anda açık olan pencereleri gösterir. Pencereyi ön plana getirmek için listedeki herhangi bir pencereye tıklayın. 

#### <a name="menu-description"></a>Menü açıklaması
Aşağıdaki tabloda pencere menüsünde görüntülenen öğeler açıklanmaktadır.

| Menü öğesi | Description |
|:--- |:--- |
| Yeni pencere |Yeni **pencere** ' ye tıklayarak yeni bir konsol penceresi açın (varolan pencereye ek olarak). |
| Seçilemez |Açık konsol pencerelerini geçişli bir stilde göstermek için **basamakla** ' ya tıklayın. |
| Yatay Döşe |Açık konsol pencerelerini bir kutucuk (veya kılavuz) biçiminde göstermek için **Yatay Döşe** ' ye tıklayın. |
| Simgeleri Düzenle |Masaüstünüzde birden fazla konsol penceresi açıksa ve bu sürümü dağıtırsanız, ekranın alt kısmındaki yatay bir satırda düzenlemek için **simgeleri düzenleyin ve ardından simgeleri Düzenle** ' ye tıklayın. |

### <a name="help-menu"></a>Yardım menüsü
StorSimple Snapshot Manager ve MMC için kullanılabilir çevrimiçi yardımı görüntülemek üzere **Yardım** menüsünü kullanın. Ayrıca, sisteminizde yüklü olan MMC ve StorSimple Snapshot Manager yazılım sürümleri hakkındaki bilgileri de görüntüleyebilirsiniz. 

Menü çubuğundaki **Yardım** menüsüne erişebilirsiniz. Ayrıca, **işlemler** bölmesinden storsimple Snapshot Manager Yardım konularına da erişebilirsiniz.

![StorSimple Snapshot Manager Yardım menüsü](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Menü açıklaması
Aşağıdaki tabloda Yardım menüsünde görüntülenen öğeler açıklanmaktadır.

| Menü öğesi | Description |
|:--- |:--- |
| StorSimple Snapshot Manager Yardım |StorSimple **Snapshot Manager** Yardım ' a tıklayarak storsimple Snapshot Manager yardımını ayrı bir pencerede açın. |
| Yardım konuları |MMC çevrimiçi yardımını ayrı bir pencerede açmak için **Yardım konuları** ' na tıklayın. |
| TechCenter Web sitesi |Microsoft TechNet Tech Center giriş sayfasını ayrı bir pencerede açmak için **TechCenter Web sitesi** ' ne tıklayın. |
| Microsoft Yönetim Konsolu hakkında |Hangi Microsoft Yönetim Konsolu sürümünün sisteminizde yüklü olduğunu görmek için **Microsoft Yönetim Konsolu hakkında** ' ya tıklayın. |
| StorSimple Snapshot Manager hakkında |Ek bileşenin hangi sürümünün sisteminizde yüklü olduğunu görmek için **StorSimple Snapshot Manager hakkında** ' ya tıklayın. |

## <a name="tool-bar"></a>Araç çubuğu
Menü çubuğunun altında bulunan araç çubuğu, gezinti ve görev simgelerini içerir. Her simge, belirli bir görevin kısayoludur.

### <a name="icon-descriptions"></a>Simge açıklamaları
Aşağıdaki tabloda araç çubuğunda görüntülenen simgeler açıklanmaktadır. 

| Simge | Description |
|:--- |:--- |
| ![Sol ok](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Önceki sayfaya dönmek için sol ok simgesine tıklayın. |
| ![Sağ ok](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Sonraki sayfaya gitmek için sağ oka tıklayın (ok gri ise, eylem kullanılamaz). |
| ![Yukarı simgesi](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Konsol ağacında ( **kapsam** bölmesi) bir düzey yukarı gitmek için yukarı simgesine tıklayın. |
| ![Konsol ağacını göster/gizle](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |**Kapsam** bölmesini göstermek veya gizlemek için konsol ağacını göster/gizle simgesine tıklayın. |
| ![Listeyi dışarı aktar](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Bir listeyi belirttiğiniz CSV dosyasına aktarmak için listeyi dışarı aktar simgesine tıklayın. |
| ![Yardım simgesi](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Çevrimiçi bir MMC yardım konusunu açmak için yardım simgesine tıklayın. |
| ![Eylemler bölmesini göster/gizle](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |**Eylemler** bölmesini göstermek veya gizlemek için **eylemleri** göster/gizle bölmesi simgesine tıklayın. |

## <a name="scope-pane"></a>Kapsam bölmesi
**Kapsam** bölmesi, storsimple Snapshot Manager Kullanıcı arabirimindeki en sol bölmesidir. Konsol (veya düğüm) ağacını içerir ve StorSimple Snapshot Manager birincil gezinti mekanizmasıdır. 

### <a name="scope-pane-structure"></a>Kapsam bölmesi yapısı
**Kapsam** bölmesi bir ağaç yapısında düzenlenmiş bir dizi tıklatılabilir nesne (düğümler) içerir. 

![Kapsam bölmesi](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Bir düğümü genişletmek veya daraltmak için, düğüm adının yanındaki ok simgesine tıklayın.
* Bir düğümün durumunu veya içeriğini görüntülemek için düğüm adına tıklayın. Bilgiler **sonuçlar** bölmesinde görünür. 

**Kapsam** bölmesi aşağıdaki düğümleri içerir: 

* [Cihazlar düğümü](#devices-node) 
* [Birimler düğümü](#volumes-node) 
* [Birim grupları düğümü](#volume-groups-node) 
* [Yedekleme Ilkeleri düğümü](#backup-policies-node) 
* [Yedekleme kataloğu düğümü](#backup-catalog-node) 
* [İşler düğümü](#jobs-node) 

### <a name="scope-pane-tasks"></a>Kapsam bölmesi görevleri
Belirli bir düğümdeki bir eylemi tamamlamaya yönelik **kapsam** bölmesini kullanabilirsiniz. Bir görevi seçmek için aşağıdakilerden birini yapın:

* Düğüme sağ tıklayın ve açılan menüden görevi seçin.
* Düğümüne tıklayın ve ardından menü çubuğunda **eylem** ' e tıklayın. Görüntülenen menüden görevi seçin.
* Düğüme tıklayın ve sonra **Eylemler** bölmesinde eylemi seçin.

Bir düğüm seçip bir görev listesini görmek için bu yöntemlerden birini kullandığınızda, yalnızca o düğümde gerçekleştirilebilecek eylemler gösterilir.

### <a name="devices-node"></a>Cihazlar düğümü
**Cihazlar** düğümü, storsimple Snapshot Manager bağlı StorSimple cihazlarını ve StorSimple Sanal cihazlarını temsil eder. Bir cihazı bağlamak ve yapılandırmak için bu düğümü seçin, ilişkili birimlerini, birim gruplarını ve mevcut yedek kopyaları içeri aktarın. Birden çok cihaz tek bir konağa bağlanabilir.

* Düğümü genişletmek için **cihazlar**' ın yanındaki ok simgesine tıklayın.
* Kullanılabilir eylemlerin bir menüsünü görmek için **cihazlar** düğümüne sağ tıklayın veya genişletilmiş görünümde görüntülenen düğümlerden herhangi birine sağ tıklayın.
* Yapılandırılmış cihazların listesini görmek için **kapsam** bölmesinde **cihazlar** ' a tıklayın. Cihazların listesi, her cihazla ilgili bilgilerle birlikte **sonuçlar** bölmesinde görünür.

### <a name="volumes-node"></a>Birimler düğümü
**Birimler** düğümü, iSCSI ve bir cihaz aracılığıyla bulunmuş olanlar da dahil olmak üzere, ana bilgisayar tarafından bağlanan birimlere karşılık gelen sürücüleri temsil eder. Kullanılabilir birimlerin listesini görüntülemek ve birim gruplarına ayrı birimler atamak için bu düğümü kullanın.

* Düğümü genişletmek için **birimler**' in yanındaki ok simgesine tıklayın.
* Kullanılabilir eylemlerin bir menüsünü görmek için, **birimler** düğümüne sağ tıklayın veya genişletilmiş görünümde görüntülenen düğümlerden herhangi birine sağ tıklayın.
* Birimlerin listesini görmek için **kapsam** bölmesinde **birimler** ' e tıklayın. Birimlerin listesi, her birimle ilgili bilgilerle birlikte **sonuçlar** bölmesinde görünür.

### <a name="volume-groups-node"></a>Birim grupları düğümü
Birim grupları da tutarlılık grupları olarak bilinir. Her birim grubu, yedekleme işlemleri sırasında uygulama tutarlılığının sağlanmasına yardımcı olan uygulamayla ilgili birimlerin bir havuzudur. Bu grupları yapılandırmak ve etkileşimli yedeklemeler almak ya da yedekleme zamanlamaları oluşturmak için **Birim grupları** düğümünü kullanın. 

* Düğümü genişletmek için, **Birim grupları**' nın yanındaki ok simgesine tıklayın.
* Kullanılabilir eylemlerin bir menüsünü görmek için, **Birim grupları** düğümüne sağ tıklayın veya genişletilmiş görünümde görüntülenen düğümlerden herhangi birine sağ tıklayın.
* Birim gruplarının bir listesini görmek için **kapsam** bölmesinde **Birim grupları** ' na tıklayın. Birim gruplarının listesi, her birim grubuyla ilgili bilgilerle birlikte, **sonuçlar** bölmesinde görünür.

### <a name="backup-policies-node"></a>Yedekleme Ilkeleri düğümü
Yedekleme ilkeleri, yerel ve bulut anlık görüntüleri için iş zamanlamalardır. Yedeklemenin ne sıklıkta oluşturulduğunu ve yedeklemenin ne kadar süreyle tutulacağını belirtmek için **yedekleme ilkeleri** düğümünü kullanın. 

* Düğümü genişletmek için **yedekleme ilkeleri**' nin yanındaki ok simgesine tıklayın.
* Kullanılabilir eylemlerin bir menüsünü görmek için **yedekleme ilkeleri** düğümüne sağ tıklayın veya genişletilmiş görünümde görüntülenen düğümlerden herhangi birine sağ tıklayın.
* Yedekleme ilkelerinin bir listesini görmek için **kapsam** bölmesinde **yedekleme ilkeleri** ' ne tıklayın. Yedekleme ilkelerinin listesi, her ilkeyle ilgili bilgilerle birlikte **sonuçlar** bölmesinde görünür.

> [!NOTE]
> En fazla 64 yedeklemeyi tutabilirsiniz.


### <a name="backup-catalog-node"></a>Yedekleme kataloğu düğümü
**Yedekleme kataloğu** düğümü, Azure StorSimple birimlerinin yerinde ve yerinde yedeklerinin listesini içerir. Bu düğüm birim grubuna göre düzenlenir ve her birim grubu kapsayıcısı yerel anlık görüntüler ( **Yerel anlık görüntü** düğümü) ve bulut anlık görüntüleri ( **bulut anlık görüntüleri** düğümü) için ayrı yapılar içerir. Genişletildiğinde, her birim grubu kapsayıcısı, etkileşimli olarak veya yapılandırılmış bir ilke tarafından gerçekleştirilen tüm başarılı yedeklemeleri listeler.

* Düğümü genişletmek için **Yedekleme kataloğu**' nun yanındaki ok simgesine tıklayın.
* Kullanılabilir eylemlerin bir menüsünü görmek için, **Yedekleme kataloğu** düğümüne sağ tıklayın veya genişletilmiş görünümde görüntülenen düğümlerden herhangi birine sağ tıklayın.
* Yedekleme anlık görüntülerinin listesini görmek için **kapsam** bölmesinde **Yedekleme kataloğu** ' na tıklayın. Anlık görüntülerin listesi, her anlık görüntü hakkındaki bilgilerle birlikte **sonuçlar** bölmesinde görünür.

### <a name="local-snapshots-node"></a>Yerel anlık görüntüler düğümü
**Yerel anlık görüntüler** düğümü, belirli bir birim grubu için yerel anlık görüntüleri listeler. Düğüm, **kapsam** bölmesindeki **Yedekleme kataloğu** düğümü altında bulunur. Yerel anlık görüntüler, Azure StorSimple cihazında depolanan birim verilerinin zaman içindeki kopyalardır. Genellikle, bu yedekleme türü hızlı bir şekilde oluşturulup geri yüklenebilir. Yerel bir yedek kopya gibi yerel bir anlık görüntü kullanabilirsiniz.

* Düğümü genişletmek için **Yerel anlık görüntüler**' in yanındaki ok simgesine tıklayın.
* Kullanılabilir eylemlerin bir menüsünü görmek için **Yerel anlık görüntüler** düğümüne sağ tıklayın veya genişletilmiş görünümde görüntülenen düğümlerden herhangi birine sağ tıklayın.
* Yerel anlık görüntülerin listesini görmek için **kapsam** bölmesinde **Yerel anlık görüntüler** ' e tıklayın. Anlık görüntülerin listesi, her anlık görüntü hakkındaki bilgilerle birlikte **sonuçlar** bölmesinde görünür.

### <a name="cloud-snapshots-node"></a>Bulut anlık görüntüleri düğümü
**Bulut anlık görüntüleri** düğümü, belirli bir birim grubu için bulut anlık görüntülerini listeler. Düğüm, **kapsam** bölmesindeki **Yedekleme kataloğu** düğümü altında bulunur. Bulut anlık görüntüleri, bulutta depolanan birim verilerinin zaman içindeki bir kopyalardır. Bulut anlık görüntüsü, farklı, site dışı bir depolama sisteminde çoğaltılan bir anlık görüntüye eşdeğerdir. Bulut anlık görüntüleri özellikle olağanüstü durum kurtarma senaryolarında yararlıdır.

* Düğümü genişletmek için **bulut anlık görüntüleri**' nin yanındaki ok simgesine tıklayın.
* Kullanılabilir eylemlerin bir menüsünü görmek için, **bulut anlık görüntüleri** düğümüne sağ tıklayın veya genişletilmiş görünümde görüntülenen düğümlerden herhangi birine sağ tıklayın.
* Bulut anlık görüntülerinin listesini görmek için **kapsam** bölmesinde **bulut anlık görüntüleri** ' ne tıklayın. Anlık görüntülerin listesi, her anlık görüntü hakkındaki bilgilerle birlikte **sonuçlar** bölmesinde görünür.

### <a name="jobs-node"></a>İşler düğümü
**İşler** düğümü, zamanlanmış, çalışan ve son tamamlanan yedekleme işleri hakkında bilgiler içerir. 

* Düğümü genişletmek için **işler**' in yanındaki ok simgesine tıklayın.
* Kullanılabilir eylemlerin bir menüsünü görmek için **işler** düğümüne sağ tıklayın veya genişletilmiş görünümde görüntülenen düğümlerden herhangi birine sağ tıklayın.
* Zamanlanan işlerin bir listesini görmek için **işler** düğümünü genişletin ve ardından **Zamanlanmış**' e tıklayın. Daha önce yapılandırılan işlerin listesi ve her işle ilgili bilgiler **sonuçlar** bölmesinde görünür. 
* Son tamamlanan işlerin listesini görmek için **işler** düğümünü genişletin ve ardından **son 24 saat**' e tıklayın. Son 24 saat içinde tamamlanan işlerin bir listesi, **sonuçlar** bölmesinde görüntülenir. **Sonuçlar** bölmesi tamamlanan her işle ilgili bilgileri de içerir.
* Şu anda çalışmakta olan işlerin listesini görmek için **işler** düğümünü genişletin ve ardından **çalışıyor**' a tıklayın. Şu anda çalışmakta olan işlerin listesi ve her işle ilgili bilgiler **sonuçlar** bölmesinde görünür.

## <a name="results-pane"></a>Sonuçlar bölmesi
**Sonuçlar** bölmesi, storsimple Snapshot Manager Kullanıcı arabirimindeki orta bölmesidir. **Kapsam** bölmesinde seçtiğiniz düğüm için listeleri ve ayrıntılı durum bilgilerini içerir.

### <a name="example"></a>Örnek
Aşağıdaki örneği görmek için **kapsam** bölmesinde **Birim grupları** düğümüne tıklayın. **Sonuçlar** bölmesi, her grup hakkındaki ayrıntıların bulunduğu birim gruplarının bir listesini görüntüler.

![Sonuçlar bölmesi](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

**Sonuçlar** bölmesinde gösterilen ayrıntıları yapılandırabilirsiniz: **kapsam** bölmesinde bir düğüme sağ tıklayın, **Görünüm**' e ve ardından **sütun Ekle/Kaldır**' a tıklayın.

## <a name="actions-pane"></a>Eylemler bölmesi
**Eylemler** bölmesi, storsimple Snapshot Manager Kullanıcı arabirimindeki sağ bölmesidir. **Kapsam** bölmesi veya **sonuçlar** bölmesinde seçtiğiniz düğüm, görünüm veya veriler üzerinde gerçekleştirebileceğiniz bir işlem menüsü içerir. **Eylemler** bölmesi, **kapsam** bölmesi ve **sonuçlar** bölmesinde öğeler için kullanılabilir olan **eylem** menüleri ile aynı komutları içerir. Her eylemin açıklaması için **eylem** menüsü bölümündeki tabloya bakın.

### <a name="examples"></a>Örnekler
Aşağıdaki örneği görmek için, **kapsam** bölmesinde **işler** düğümünü genişletin ve **zamanlanan**' ye tıklayın. **Eylemler** bölmesi, **zamanlanan** düğüm için kullanılabilir eylemleri görüntüler.

![Eylemler bölmesi zamanlanmış işler örneği](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Daha fazla seçenek görmek için, **kapsam** bölmesinde, **işler** düğümünü genişletin, **Zamanlanmış**' e tıklayın ve ardından **sonuçlar** bölmesinde zamanlanmış bir işe tıklayın. **Eylemler** bölmesi, aşağıdaki örnekte gösterildiği gibi zamanlanan iş için kullanılabilir eylemleri görüntüler.

![Eylemler bölmesi iş eylemleri örneği](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Klavye gezintisi ve kısayolları
StorSimple Snapshot Manager, Windows işletim sisteminin ve Microsoft Yönetim Konsolu 'nun (MMC) erişilebilirlik özelliklerini sunar. Ayrıca, aşağıdaki bölümlerde açıklandığı gibi, StorSimple Snapshot Manager özgü bazı klavye gezinti özelliklerini ve kısayollarını da içerir.

* [Klavye gezinti tuşları](#keyboard-navigation-keys) 
* [Menü çubuğu kısayol tuşları](#menu-bar-shortcut-keys) 
* [Kapsam bölmesi kısayol tuşları](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Klavye gezinti tuşları
Aşağıdaki tabloda, StorSimple Snapshot Manager Kullanıcı arabiriminde gezinmek için kullanabileceğiniz anahtarlar açıklanmaktadır. 

| Gezinti tuşu | Eylem |
|:--- |:--- |
| Aşağı ok tuşu |Bir menüdeki veya bölmedeki bir sonraki öğeye dikey olarak gitmek için aşağı ok tuşunu kullanın. |
| Enter |Bir eylemi tamamlayıp sonraki adıma geçmek için ENTER tuşuna basın. Örneğin, **İleri**' yi, **Tamam**' ı veya **Oluştur**' u ve ardından bir sihirbazda bir sonraki adıma gitmek için ENTER tuşuna basın. |
| Esc |Menüyü kapatmak veya bir sayfayı iptal etmek ve kapatmak için ESC tuşuna basın. |
| F1 |Şu anda etkin olan pencereyle ilgili yardım konusunu görüntülemek için F1 tuşuna basın. |
| F5 |Bir düğümü yenilemek için F5 tuşuna basın. |
| F6 |**Kapsam** bölmesinden **sonuçlar** bölmesine geçmek için F6 tuşuna basın. |
| F10 |Menü çubuğuna gitmek için F10 tuşuna basın. |
| Sol ok tuşu |Sol ok tuşunu kullanarak bir menü çubuğu seçeneğinden bir önceki seçeneğe yatay olarak geçiş yapın. Menü çubuğundaki bir önceki öğeye geçtiğinizde, önceki öğenin eylem (veya bağlam) menüsü görüntülenir. |
| Sağ ok tuşu |Sağ ok tuşunu kullanarak bir menü çubuğu seçeneğinden bir sonrakine yatay geçiş yapın. Menü çubuğunda bir sonraki öğeye geçtiğinizde, yeni öğe için eylem (veya bağlam) menüsü görüntülenir. |
| Sekme tuşu |Konsolun bir sonraki bölmesine veya bir sayfadaki sonraki seçim ya da metin kutusuna gitmek için Tab tuşunu kullanın. |
| Yukarı ok tuşu |Bir menü veya bölmedeki bir önceki öğeye dikey olarak gitmek için yukarı ok tuşunu kullanın. |

### <a name="menu-bar-shortcut-keys"></a>Menü çubuğu kısayol tuşları
Aşağıdaki tabloda menü çubuğu için kısayol tuş birleşimleri açıklanmaktadır. Kısayol tuşlarına bastıktan sonra menü açıldıktan sonra menü kısayol tuşlarını (menüdeki altı çizili tuşları) kullanabilirsiniz. Menü çubuğu hakkında daha fazla bilgi için [menü çubuğuna](#menu-bar)gidin.

| Kısayol | Sonuç | Menü kısayol tuşu | Sonuç |
|:--- |:--- |:--- |:--- |
| ALT + F |**Dosya** menüsünü açar. |N |Yeni bir konsol örneği açar. |
|  |O |**Yönetim Araçları** sayfasını açar. | |
|  |S |StorSimple Snapshot Manager konsolunu kaydeder. | |
|  |A |**Farklı kaydet** sayfasını açar. | |
|  |M |**Ek bileşen Ekle/Kaldır** sayfasını açar. | |
|  |P |**Seçenekler** sayfasını açar. | |
|  |H |Çevrimiçi yardım 'ı açar. | |
| ALT + A |**Eylem** menüsünü açar. |I |İçeri aktarma görüntüleme seçeneğini açar ve kapatır. |
|  |W |Yeni bir StorSimple Snapshot Manager konsolunu açar. | |
|  |F |StorSimple Snapshot Manager konsolunu güncelleştirir. | |
|  |L |**Listeyi dışarı aktar** sayfasını açar. | |
|  |H |Çevrimiçi yardım 'ı açar. | |
| ALT + V |**Görünüm** menüsünü açar. |A |**Sütun Ekle/Kaldır** sayfasını açar. |
|  |U |**Görünümü Özelleştir** sayfasını açar. | |
| ALT+O |**Sık Kullanılanlar** menüsünü açar. |A |**Sık Kullanılanlara Ekle** sayfasını açar. |
|  |O |**Sık Kullanılanları Düzenle** sayfasını açar. | |
| ALT + W |**Pencere** menüsünü açar. |N |Başka bir StorSimple Snapshot Manager penceresi açar. |
|  |C |Tüm açık konsol pencerelerini basamaklı stilde görüntüler. | |
|  |T |Tüm açık konsol pencerelerini kılavuz düzenine göre görüntüler. | |
|  |I |Simgeleri ekranınızın alt kısmındaki yatay bir satırda yerleştirir. | |
| ALT + H |**Yardım** menüsünü açar. |H |Çevrimiçi yardım 'ı açar. |
|  |T |Microsoft TechNet Tech Center Web sayfasını açar. | |
|  |A |**Microsoft Yönetim Konsolu hakkında** sayfasını açar. | |

### <a name="scope-pane-shortcut-keys"></a>Kapsam bölmesi kısayol tuşları
Aşağıdaki tablolarda, **kapsam** bölmesindeki her bir düğüm için kısayol tuş birleşimleri gösterilmektedir. 

* [Cihazlar düğümü kısayol tuşları](#devices-node-shortcut-keys)
* [Birimler düğümü kısayol tuşları](#volumes-node-shortcut-keys)
* [Birim grupları düğümü kısayol tuşları](#volume-groups-node-shortcut-keys)
* [Yedekleme Ilkeleri düğüm kısayol tuşları](#backup-policies-node-shortcut-keys)
* [Yedekleme kataloğu düğümü kısayol tuşları](#backup-catalog-node-shortcut-keys)
* [İşler düğümü kısayol tuşları](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Cihazlar düğümü kısayol tuşları
| Menü kısayolu | Sonuç |
|:--- |:--- |
| C |**Cihaz yapılandırma** sayfasını açar. |
| D |Cihazların ve cihaz ayrıntılarının listesini yeniler. |
| V |**Görünüm** menüsünü açar. |
| W |**Ayrıntılar** düğümüne odaklanmış yeni bir storsimple Snapshot Manager konsolunu açar. |
| F |StorSimple Snapshot Manager konsolunu güncelleştirir. |
| L |**Listeyi dışarı aktar** sayfasını açar. |
| H |Çevrimiçi yardım 'ı açar. |

#### <a name="volumes-node-shortcut-keys"></a>Birimler düğümü kısayol tuşları
| Menü kısayolu | Sonuç |
|:--- |:--- |
| V |Birim listesini güncelleştirir. |
| V (iki kez bas) |**Görünüm** menüsünü açar. |
| W |**Birimler** düğümüne odaklanmış yeni bir storsimple Snapshot Manager konsolunu açar. |
| F |StorSimple Snapshot Manager konsolunu güncelleştirir. |
| L |**Listeyi dışarı aktar** sayfasını açar. |
| H |Çevrimiçi yardım 'ı açar. |

#### <a name="volume-groups-node-shortcut-keys"></a>Birim grupları düğümü kısayol tuşları
| Menü kısayolu | Sonuç |
|:--- |:--- |
| G |**Birim grubu oluştur** sayfasını açar. |
| V |**Görünüm** menüsünü açar. |
| W |**Birim grupları** düğümüne odaklanmış yeni bir storsimple Snapshot Manager konsolunu açar. |
| F |StorSimple Snapshot Manager konsolunu güncelleştirir. |
| L |**Listeyi dışarı aktar** sayfasını açar. |
| H |Çevrimiçi yardım 'ı açar. |

#### <a name="backup-policies-node-shortcut-keys"></a>Yedekleme Ilkeleri düğüm kısayol tuşları
| Menü kısayolu | Sonuç |
|:--- |:--- |
| B |**Ilke oluştur** sayfasını açar. |
| V |**Görünüm** menüsünü açar. |
| W |**Birim grupları** düğümüne odaklanmış yeni bir storsimple Snapshot Manager konsolunu açar. |
| F |StorSimple Snapshot Manager konsolunu güncelleştirir. |
| L |**Listeyi dışarı aktar** sayfasını açar. |
| H |Çevrimiçi yardım 'ı açar. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Yedekleme kataloğu düğümü kısayol tuşları
| Menü kısayolu | Sonuç |
|:--- |:--- |
| W |**Birim grupları** düğümüne odaklanmış yeni bir storsimple Snapshot Manager konsolunu açar. |
| F |StorSimple Snapshot Manager konsolunu güncelleştirir. |
| H |Çevrimiçi yardım 'ı açar. |

#### <a name="jobs-node-shortcut-keys"></a>İşler düğümü kısayol tuşları
| Menü kısayolu | Sonuç |
|:--- |:--- |
| V |**Görünüm** menüsünü açar. |
| W |**İşler** düğümüne odaklanmış yeni bir storsimple Snapshot Manager konsolunu açar. |
| F |StorSimple Snapshot Manager konsolunu güncelleştirir. |
| L |**Listeyi dışarı aktar** sayfasını açar. |
| H |Çevrimiçi Yardımı açar |

## <a name="next-steps"></a>Sonraki adımlar
* StorSimple [çözümünüzü yönetmek Için storsimple Snapshot Manager](storsimple-snapshot-manager-admin.md)nasıl kullanacağınızı öğrenin.
* [StorSimple Snapshot Manager kullanarak cihazları bağlama ve yönetme](storsimple-snapshot-manager-manage-devices.md)hakkında bilgi edinin.