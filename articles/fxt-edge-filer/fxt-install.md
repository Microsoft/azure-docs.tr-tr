---
title: Azure FXT Edge Filer fiziksel cihazı yükleme öğreticisi | Microsoft Docs
description: Microsoft Azure FXT Edge Filer karma depolama önbelleğinin fiziksel cihaz bileşenini açma, bölme ve kablo oluşturma
services: ''
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 0f3c7b01ee9d4a62ec0d563af55f2086894081be
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256052"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Öğretici: Azure FXT Edge Filer 'ı kurma 

Bu öğreticide, Azure FXT Edge Filer karma depolama önbelleği için bir donanım düğümünün nasıl yükleneceği açıklanmaktadır. Azure FXT Edge Filer kümesi oluşturmak için en az üç donanım düğümü yüklemeniz gerekir.

Yükleme yordamı, cihazı açma ve rafa çıkarma ve kablo yönetimi ARM (CMA) ve ön kapak ekleme işlemini içerir. Ayrı bir öğreticide ağ kablolarını ekleme ve güç bağlama işlemleri açıklanmaktadır. 

Azure FXT Edge Filer düğümünü yüklemek yaklaşık bir saat sürer. 

Bu öğreticide aşağıdaki kurulum adımları yer almaktadır: 

> [!div class="checklist"]
> * Cihazın paketini açın
> * Cihazı bir rafa bağlama
> * Ön ucu (isteğe bağlı) yükler

## <a name="installation-prerequisites"></a>Yükleme önkoşulları 

Başlamadan önce, kullanacağınız veri merkezi ve rafın bu özelliklere sahip olduğundan emin olun:

* Cihazı bağlamak istediğiniz raftaki kullanılabilir bir 1U yuvası.
* Azure FXT Edge Filininin ihtiyaçlarını karşılayan AC güç kaynağı ve soğutma sistemleri. (Yüklemeyi planlamaya ve boyutlandırmasına yardımcı olmak için [güç ve sıcaklık belirtimlerini](fxt-specs.md#power-and-thermal-specifications) okuyun.)  

  > [!NOTE] 
  > İki yedekli güç kaynağı biriminden (PSUs) tam olarak yararlanabilmek için, AC gücü eklenirken iki farklı dalda güç dağıtım birimi kullanın. Ayrıntılar için [bağlantı gücü kablolarını](fxt-network-power.md#connect-power-cables) okuyun.  

## <a name="unpack-the-hardware-node"></a>Donanım düğümünü paketten çıkarma 

Her Azure FXT Edge Filer düğümü tek bir kutuda gönderilir. Bir cihazın paketini açmak için bu adımları uygulayın.

1. Kutuyu düz, düzey bir yüzeye yerleştirin.

2. Kutuyu ve paketlemeyi, kırma, keser, su hasarı veya başka bir açık hasar için paketleme. Kutu veya paketleme ciddi hasar görmüşse bunu açmayın. Cihazın iyi çalışma sırasında olup olmadığını değerlendirmenize yardımcı olmak için Microsoft Desteği başvurun.

3. Kutunun paketini açın. Aşağıdaki öğeleri içerdiğinden emin olun:
   * Tek bir kasa FXT cihazı
   * İki güç kablosu
   * Tek bir ön ve anahtar
   * Bir demiryolu seti derlemesi
   * Bir kablo yönetimi ARM (CMA)
   * CMA yükleme yönergeleri kitapçığı
   * Raf yükleme yönergeleri kitapçık
   * Güvenlik, çevresel ve mevzuat bilgileri kitapçık

Listelenen öğelerin tümünü almadıysanız, destek almak için cihaz satıcısına başvurun. 

Cihazın, yüklemeden veya kapatmadan önce oda ile aynı sıcaklığa ulaşması için yeterli zamana sahip olduğundan emin olun. Cihazın herhangi bir bölümünde daha fazla bildirimde bulunuruz, yüklemeden önce en az 24 saat bekleyin.

Sonraki adım, cihazınızı takmaya yönelik bir adımdır.

## <a name="rack-the-device"></a>Cihazı raf

Azure FXT Edge Filer cihazı, standart 19 inç bir rafa yüklenmiş olmalıdır. 

Azure FXT Edge Filer karma depolama önbelleği, üç veya daha fazla Azure FXT Edge Filer cihazdan oluşur. Sisteminizin parçası olan her bir cihaz için raf yükleme adımlarını yineleyin. 

### <a name="rack-install-prerequisites"></a>Raf yüklemesi önkoşulları

* Başlamadan önce, cihazıyla birlikte sunulan güvenlik, çevresel ve mevzuat bilgileri kitapçığındaki güvenlik yönergelerini okuyun.

  > [!NOTE]
  > Düğüm çalışırken her zaman iki kişi kullanın, bunu bir rafa yüklediğinizde ya da rafınızdan kaldırdığınızda. 

* Ekipman rafınız ile kullanılan demiryolu yükleme türünü belirler. 
  * Kare veya yuvarlak delik ek bileşen rafları için, aletsiz demiryolu yönergelerini izleyin.
  * İş parçacıklı delik raflar için, toulan demiryolu talimatlarını izleyin. 
  
    Toulan demiryolu yapılandırması için sekiz adet screws sağlamanız, 10-32, 12-24, M5 veya M6 yazmanız gerekir. Screws 'nin baş çapı 10 mm 'den (0,4 ") az olmalıdır.

### <a name="identify-the-rail-kit-contents"></a>Demiryolu Kit içeriğini tanımla

Demiryolu Kit derlemesini yüklemek için bileşenleri bulun:

1. İki A7 Dell Readyrayi II kayan demiryolu derlemeleri (1)
1. İki kanca ve döngü Straps (2)

![Demiryolu, Kııl Kit içeriğinin numaralandırılmış çizimi](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Demiryolu derlemesi-aletsiz raylar (köşeli delik veya yuvarlak delik raflar)

Ek bileşen kare veya yuvarlak delikleri olan raflar için, bu yordamı izleyerek rayları toplayın ve yükler. 

1. Sol ve sağ tarafındaki demiryolu bitiş parçalarını, **içe yönlü olarak** etiketlenen şekilde konumlandırın. Her bir son parçayı dikey Raf flantlerinin ön tarafındaki deliklere oturmasını sağlayacak şekilde konumlandırın. (1)

2. Her bir son parçayı, bağlamak istediğiniz alana en alta ve en üst deliklere hizalayın. (

3. Dikey Raf flanş üzerinde tam olarak oturana kadar, ve mandal bir yere tıkladığı sürece, rayın arka ucuna katılın. Bu adımları, ön uç parçasını dikey Raf flanş üzerinde konumlandırmak ve oturmek için tekrarlayın. 03

> [!TIP]
> Rayları kaldırmak için, son nokta orta noktasında (4) mandal yayını düğmesini çekin ve her bir demiryolu 'yi serbest bırakın.

![Araç-Less raylarını yükleme ve kaldırma diyagramı, adımlar numaralandırıldı](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Demiryolu derlemesi-toulan raylar (vida dişli delik rafları)

İş parçacıklı delikleri olan raflar için, bu yordamı izleyerek rayları toplayın ve yükler.

1. PIN 'leri ön ve arka bağlama ayraçları düz eğimli bir screwsürücü ile kaldırın. (1)
1. Montaj ayraçlarından kaldırmak için, demiryolu halkalarını çekin ve döndürün. (
1. Sol ve sağ montaj rayları, iki adet sayıda screws kullanarak ön dikey rafa monte edilebilir olarak ekleyin. 03
1. Sol dikey dolapta, sol ve sağ köşeli ayraçlara doğru kaydırın ve iki adet screws çifti kullanarak bunları ekleyin. 4

![Numaralandırılmış adımlarla birlikte toulan rayların yüklenmesi ve kaldırılması diyagramı](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Sistemi rafa yükler

Azure FXT Edge Filer cihazını rafa bağlamak için aşağıdaki adımları izleyin.

1. İç slaydı, parçalara çıkana kadar raf dışına çekin. (1)
1. Cihazın her tarafında arka rayları bulun ve bunları slayt derlemelerindeki arka uç yuvalarına düşürün. ( 
1. Tüm RAIL rap 'leri J yuvalarında kümelendirilene kadar cihazı aşağı döndürün. 03
1. Kilit bir yere tıklatalana kadar cihazı içe gönderin.
1. Her iki rayda (4) slayt yayını kilidi düğmelerine basın ve cihazı rafa kaydırın.

![Sistem, bir raf diyagramına, numaralanmış adımlarla birlikte yüklenir](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Sistemi rafa kaldır

Cihazı rafınızdan kaldırmak için bu yordamı izleyin. 

1. İç rayların (1) tarafındaki kilit çimlerini bulun.
2. Her mandalını, serbest bırakma konumuna (2) döndürerek açın.
3. Sistemin yüzlerini korkutın ve RAIL stanon, J yuvalarının önünde oluncaya kadar ileriye doğru çekin. Sistemi rafa kaldırın ve bir düzey yüzeyine (3) yerleştirin.

![Bir sistemin rafdan kaldırılmasına ve bu adımların numaralandırıldığı gösterimi](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Slad mandalına katılın

1. Önde oturun, sistemin her iki tarafında da SLA, (1) topolojinizi bulun.
2. Sistem rafa itildiğinde, bu, otomatik olarak kullanılır. 

Sistemi kaldırırken atmaları serbest bırakmak için, (2) çekin.

İsteğe bağlı sabit bağlama screws, sistemin sevkiyata veya diğer kararsız ortamlara karşı güvenlik altına almak için sağlanır. Her mandal altında vidalı bulun ve bunları bir #2 Phillips screwdriver (3) ile güçlendir.

![SLA 'nın, SLA 'nın ve serbest bırakılması için numaralandırılmış gösterimi](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Kablo yönetimi ARM 'yi yükler 

FXT Edge Filsi ile isteğe bağlı bir kablo yönetimi kolu (CMA) sağlanır. Yüklemeye yönelik yazılı yönergeler pakette verilmiştir. 

1. Kablo yönetimi ARM Kit bileşenlerinin paketini açın ve yapın:
   * CMA tepsisi (1)
   * CMA (2)
   * Nylon kablo bağlaması kaydırılır (3)
   * CMA ek ayraçları (4)
   * Durum göstergesi kablosu (5) 

   > [!TIP] 
   > CMA 'nın rafta sevkiyat amacıyla güvenliğini sağlamak için, her iki sepetin ve tepsinin yanı sıra, her iki sepetin da bir yanındaki halkalar alın. CMA 'nın bu şekilde güvenliğini sağlamak, sisteminizi kararsız ortamlarda da güvence altına alacak.

   ![CMA parçalarının çizimi](media/fxt-install/cma-kit-400.png)

2. CMA tepsisini yükler.

   CMA tepsisi destek sağlar ve CMA için Retainer işlevi görür. 

   1. Tepsinin iç kenarlarındaki alıcı ayraçları ile tepsiyi her tarafını hizalayın ve birlikte devreye koyun. 
   1. Bir yere tıklaana kadar tepsiyi ileri doğru gönderin. (1)
   1. Tepsiyi kaldırmak için, mandal yayınlama düğmelerini merkeze doğru sıkıştırın ve tepsiyi alıcı ayraçları dışına çıkarın (2).

   ![CMA tepsisi yüklemesinin çizimi](media/fxt-install/cma-tray-install-400.png)

3. CMA ek ayraçları 'nı yükler. 

   > [!NOTE]
   >
   > * CMA 'yı, sistem üzerinden kabloları nasıl yönlendirdiğinize bağlı olarak, sağ veya sol bağlamak üzere, bir Köl 'ye ekleyebilirsiniz. 
   > * Kolaylık sağlamak için, CMA 'yı güç tedariklerini (yan A) karşı tarafa bağlayın. B tarafına bağlanmışsa, dış güç kaynağını kaldırmak için CMA 'nın bağlantısı kesilmelidir. 
   > * Güç kaynakları kaldırılmadan önce her zaman tepsiyi kaldırın. 

   ![CMA ayracı yüklemesinin çizimi](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. CMA 'yı bağlamak istediğiniz taraf için uygun CMA eki köşeli ayracını seçin (yüz B veya yan A).
   1. CMA eki köşeli ayracını arka arkaya, karşılık gelen yan A veya yan B işaretine sahip olacak şekilde yükler.
   1. Köşeli ayracın içindeki delikleri, slayt rampaındaki PIN 'leri ile hizalayın. Yer işareti bitene kadar köşeli ayracı aşağı gönderin. 

4. CMA 'yı yükler.

   1. Sistemin arkasında, MANMA 'nın ön ucundaki manuca, mandal (1) kadar, slayt derlemesinin en içteki köşeli ayracına uydurun. 
   1. Mandal (2) bitene kadar en dıştaki ayracın sonundaki diğer mandal uydurun. 
   1. CMA 'yı kaldırmak için, iç ve dış mandallarda (3) en üstündeki CMA yayın düğmelerine basarak her iki kapsayıcıyı da ele edin.

   ![Ana CMA yüklemesinin çizimi](media/fxt-install/cma-install-400.png)

   CMA, erişim ve hizmet için sistemden dışarıda döndürülür. Alınan uçta, CMA 'yı tepsiden çıkarmak için tepsiyi kaldırın (1). Tepsiden ayrıldıktan sonra, CMA 'yı sistemden uzağa (2) geri dönüşümlü hale gelir.

   ![Hizmet için açık olan CMA döndürülmüş gösterimi](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Ön ucu (isteğe bağlı) yükler

Bu bölümde, Azure FXT Edge Filer donanımı için ön KACININ (çok yönlü levha) nasıl yükleneceği ve kaldırılacağı açıklanır. 

Ön ucu yüklemek için: 

1. Ana paket içinde sunulan bir kasa anahtarını bulun ve kaldırın. 
1. Üst öğeyi gövdenin önüne hizalayın ve grafiğin sağ tarafındaki iğneleri, düğümün sağ tarafındaki raf bağlama bezge 'daki deliklere ekleyin. 
1. Kasanın sol ucuna kasa üzerine uydurun. Sol taraftaki düğme gelene kadar gidilecek yere basın.
1. Ana nesneyi anahtarla kilitleyin.

Ön ucu kaldırmak için: 
1. Kasa anahtarını kullanarak, ana bilgisayarın kilidini açın.
1. Sol taraftaki yayın düğmesine basın ve kasanın sol ucuna kasadan uzağa çekin.
1. Sağ ucu kaldırır ve ön ucu kaldırın.
   
   ![Bir resmin sol tarafındaki yayın düğmesini ve sol taraftan dışarı doğru çekerek nasıl kaldırılacağını gösteren resim](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Sonraki adımlar

Cihazı paketten kaldırarak ve geçirdikten sonra, ağ kablolarını ekleyerek ve Azure FXT Edge Filkına AC gücü bağlayarak kuruluma devam edin.

> [!div class="nextstepaction"]
> [Ağ bağlantı noktaları ve sağlama gücü kablosu](fxt-network-power.md)