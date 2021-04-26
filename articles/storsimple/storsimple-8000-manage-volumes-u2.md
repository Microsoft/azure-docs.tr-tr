---
title: StorSimple birimlerini yönetme (güncelleştirme 3)
description: StorSimple birimlerinin nasıl ekleneceğini, değiştirileceğini, izleneceğini ve silineceğini ve gerekirse bunları çevrimdışına alma açıklanmaktadır.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 12/09/2017
ms.author: alkohli
ms.openlocfilehash: 3d649b54b544c120198963f8094764e9590e20b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100547637"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Birimleri yönetmek için StorSimple Device Manager hizmetini kullanın (güncelleştirme 3 veya üzeri)

## <a name="overview"></a>Genel Bakış

Bu öğreticide, güncelleştirme 3 ve üzeri sürümlerini çalıştıran StorSimple 8000 serisi cihazlarda birimler oluşturmak ve yönetmek için StorSimple Device Manager hizmetinin nasıl kullanılacağı açıklanmaktadır.

StorSimple Device Manager hizmeti, StorSimple çözümünüzü tek bir web arabiriminden yönetmenizi sağlayan Azure portal bir uzantıdır. Tüm cihazlarınızda birimleri yönetmek için Azure portal kullanın. Ayrıca, StorSimple Hizmetleri oluşturabilir ve yönetebilir, cihazları, yedekleme ilkelerini ve yedekleme kataloğunu yönetebilir ve uyarıları görüntüleyebilirsiniz.

## <a name="volume-types"></a>Birim türleri

StorSimple birimleri şu olabilir:

* **Yerel olarak sabitlenmiş birimler**: Bu birimlerdeki veriler her zaman yerel StorSimple cihazında kalır.
* **Katmanlı birimler**: Bu birimlerdeki veriler buluta taşıbilirler.

Arşiv birimi katmanlı bir birim türüdür. Arşiv birimleri için kullanılan daha büyük yinelenenleri kaldırma öbek boyutu, cihazın daha büyük veri kesimlerini buluta aktarmasına olanak tanır.

Gerekirse, birim türünü yerel veya katmanlı iken yerel olarak değiştirebilirsiniz. Daha fazla bilgi için [birim türünü değiştirme](#change-the-volume-type)bölümüne gidin.

### <a name="locally-pinned-volumes"></a>Yerel olarak sabitlenmiş birimler

Yerel olarak sabitlenmiş birimler, buluta veri katmayan ve bulut bağlantısından bağımsız olarak birincil veriler için yerel garanti sağlayan tam olarak sağlanmış birimlerdir. Yerel olarak sabitlenmiş birimlerdeki veriler çoğaltılamaz ve sıkıştırılmaz; Ancak, yerel olarak sabitlenmiş birimlerin anlık görüntüleri yinelenenleri kaldırılır. 

Yerel olarak sabitlenmiş birimler tam olarak sağlanır; Bu nedenle, oluşturduğunuz sırada cihazınızda yeterli alana sahip olmanız gerekir. StorSimple 8100 cihazında en fazla 8 TB ve 8600 cihazında 20 TB olmak üzere, yerel olarak sabitlenmiş birimler sağlayabilirsiniz. StorSimple, anlık görüntüler, meta veriler ve veri işleme için cihazdaki kalan yerel alanı ayırır. Yerel olarak sabitlenmiş bir birimin boyutunu kullanılabilir maksimum alana artırabilirsiniz, ancak oluşturulduktan sonra bir birimin boyutunu azaltamazsınız.

Yerel olarak sabitlenmiş bir birim oluşturduğunuzda, katmanlı birimlerin oluşturulması için kullanılabilir alan azalır. Tersi de geçerlidir: mevcut katmanlı birimleriniz varsa, yerel olarak sabitlenmiş birimler oluşturmak için kullanılabilen alan, yukarıda belirtilen en fazla sınırdan daha düşük olacaktır. Yerel birimler hakkında daha fazla bilgi için [yerel olarak sabitlenmiş birimlerde sık sorulan sorulara](storsimple-8000-local-volume-faq.md)bakın.

### <a name="tiered-volumes"></a>Katmanlı birimler

Katmanlı birimler, sık erişilen verilerin cihazda yerel olarak kalması ve daha az sık kullanılan verilerin buluta otomatik olarak katmanlanmasından oluşan ölçülü kaynak sağlanmış birimlerdir. Ölçülü kaynak sağlama, kullanılabilir depolamanın fiziksel kaynakları aşacak şekilde göründüğü bir sanallaştırma teknolojisidir. StorSimple, önceden yeterli depolama alanı ayırmak yerine, geçerli gereksinimleri karşılamak için yeterli alan ayırmak üzere ölçülü kaynak sağlama kullanır. Bu yaklaşım, StorSimple tarafından değişiklik taleplerini karşılamak üzere bulut depolamasını artırabilir veya azaltababileceğinden, bulut depolamanın esnek yapısı bu yaklaşımı kolaylaştırır.

Arşiv verileri için katmanlı birim kullanıyorsanız, biriminizin yinelenenleri kaldırma öbeği boyutunu 512 KB olarak değiştirmek için **Bu birimi daha az sıklıkta erişilen arşiv verileri Için kullan** onay kutusunu seçin. Bu seçeneği seçmezseniz, ilgili katmanlı birim 64 KB 'lık öbek boyutunu kullanır. Daha büyük bir yinelenenleri kaldırma öbek boyutu cihazın büyük arşiv verilerinin buluta aktarımını hızlandırmasını sağlar.


### <a name="provisioned-capacity"></a>Sağlanan kapasite

Her cihaz ve birim türü için sağlanan maksimum kapasite için aşağıdaki tabloya bakın. (Yerel olarak sabitlenmiş birimlerin sanal bir cihazda kullanılabilir olmadığına unutmayın.)

| Tür | Maksimum katmanlı birim boyutu | Yerel olarak sabitlenmiş birim boyutu üst sınırı |
| --- | --- | --- |
| **Fiziksel cihazlar** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Sanal cihazlar** | | |
| 8010 |30 TB |Yok |
| 8020 |64 TB |Yok |

## <a name="the-volumes-blade"></a>Birimler dikey penceresi

**Birimler** dikey penceresi, başlatıcılarınız (sunucular) için Microsoft Azure StorSimple cihazında sağlanan depolama birimlerini yönetmenizi sağlar. Hizmetinize bağlı olan StorSimple cihazlarındaki birimlerin listesini görüntüler.

 ![Birimler sayfası](./media/storsimple-8000-manage-volumes-u2/volumes-list.png)

Bir birim bir dizi öznitelik içerir:

* **Birim adı** : benzersiz olması gereken açıklayıcı bir ad ve birimin tanımlanmasına yardımcı olur. Bu ad, belirli bir birimi filtreleyerek İzleme raporlarında da kullanılır. Birim oluşturulduktan sonra yeniden adlandırılamaz.
* **Durum** – çevrimiçi veya çevrimdışı olabilir. Bir birim çevrimdışıysa, birimi kullanmak için erişim izni verilen başlatıcılara (sunucular) görünmez.
* **Kapasite** : başlatıcı tarafından depolanabilecek toplam veri miktarını belirtir (sunucu). Yerel olarak sabitlenmiş birimler tam olarak sağlanır ve StorSimple cihazında bulunur. Katmanlı birimler ölçülü kaynak olarak sağlanır ve veriler kaldırılır. Ölçülü kaynak sağlanan birimlerde cihazınız, yapılandırılmış birim kapasitesine göre dahili olarak veya bulutta fiziksel depolama kapasitesini önceden ayırır. Birim kapasitesi talep üzerine ayrılır ve kullanılır.
* **Tür** – birimin **katmanlı** (varsayılan) veya **yerel olarak sabitlenmiş** olduğunu gösterir.

Aşağıdaki görevleri gerçekleştirmek için bu öğreticideki yönergeleri kullanın:

* Birim ekleme 
* Bir birimi değiştirme 
* Birim türünü değiştirme
* Bir birimi silme 
* Bir birimi çevrimdışı duruma getirme 
* Bir birimi izleme 

## <a name="add-a-volume"></a>Birim ekleme

StorSimple 8000 serisi cihazınızın dağıtımı sırasında [bir birim oluşturdunuz](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) . Birim eklemek benzer bir yordamdır.

#### <a name="to-add-a-volume"></a>Birim eklemek için

1. **Cihazlar** dikey penceresindeki tablosal cihaz listesinden cihazınızı seçin. **+ Birim ekle**’ye tıklayın.

    ![Yeni birim ekleme](./media/storsimple-8000-manage-volumes-u2/add-volume-01.png)

2. **Birim ekle** dikey penceresinde:
   
    1. **Cihazı seçin** alanı otomatik olarak geçerli cihazınızla doldurulur.

    2. Açılan listeden, birim eklemeniz gereken birim kapsayıcısını seçin.

    3.  Biriminiz için bir **Ad** yazın. Birim oluşturulduktan sonra birimi yeniden adlandıramazsınız.

    4. Açılan listede biriminizin **Tür**’ünü seçin. Yerel garantilerin, düşük gecikme sürelerinin ve yüksek performansın gerektiği iş yükleri için **Yerel olarak sabitlenmiş** bir birim seçin. Diğer tüm veriler için **Katmanlı** bir birim seçin. Bu birimi arşiv verileri için kullanıyorsanız **Bu birimi daha az sıklıkta erişilen arşiv verileri için kullanın**’ı işaretleyin.
      
       Katmanlı birim ölçülü sayıda kaynak kullanarak sağlanır ve hızlı oluşturulabilir. Arşiv verileri için hedeflenen katmanlı birime yönelik olarak **Bu birimi daha az sıklıkta erişilen arşiv verileri için kullan**’ın seçilmesi, biriminizle ilgili yinelenenleri kaldırma öbek boyutunu 512 KB olarak değiştirir. Bu alan işaretli değilse, ilgili katmanlı birim 64 KB öbek boyutu kullanır. Daha büyük bir yinelenenleri kaldırma öbek boyutu cihazın büyük arşiv verilerinin buluta aktarımını hızlandırmasını sağlar.
       
       Yerel olarak sabitlenmiş bir birim sıkı hazırlanmıştır ve birimdeki birincil verilerin cihazda yerel kalmasını ve buluta dağıtılmamasını sağlar.  Yerel olarak sabitlenmiş bir birim oluşturursanız cihaz, istenen boyutta birimi sağlamak için yerel katmanlardaki kullanılabilir alanı denetler. Yerel olarak sabitlenmiş birim oluşturma işlemi var olan verileri cihazdan buluta dağılmasını kapsayabilir ve birim oluşturmak için geçen süre uzun olabilir. Toplam süre hazırlanan birimin boyutuna, kullanılabilir ağ bant genişliğine ve cihazınızdaki verilere bağlıdır.

    5. Biriminiz için **Sağlanan Kapasite**’yi belirtin. Seçili birim türünü temel alan kullanılabilir kapasiteyi not edin. Belirtilen birim boyutu kullanılabilir alanı aşmamalıdır.
      
       8100 cihazında 8,5 TB'a kadar yerel olarak sabitlenmiş birim ya da 200 TB'a kadar katmanlı birim sağlayabilirsiniz. Daha büyük olan 8600 cihazında 22,5 TB'a kadar yerel olarak sabitlenmiş birim ya da 500 TB'a kadar katmanlı birim sağlayabilirsiniz. Katmanlı birimlerin çalışma kümesini barındırmak için cihazda yerel alan gerektiğinden, yerel olarak sabitlenmiş birimlerin oluşturulması, katmanlı birimlerin sağlanması için kullanılabilecek alanı etkiler. Bu nedenle, yerel olarak sabitlenmiş birim oluşturursanız, katmanlı birimlerin oluşturulması için gereken boş alan azalır. Benzer şekilde, katmanlı birim oluşturulduysa, yerel olarak sabitlenmiş birimlerin oluşturulması için kullanılabilir alan azalır.
      
       8100 cihazınızda 8,5 TB boyutunda (izin verilen en yüksek boyut) yerel olarak sabitlenmiş bir birim sağlarsanız, cihazdaki kullanılabilir yerel alanın tümünü kullanmış olursunuz. Katmanlı birimin çalışan kümesinin barındıracak cihazda yerel alan olmadığından, bu noktadan sonra herhangi bir katmanlı birim oluşturamazsınız. Var olan katmanlı birimler kullanılabilir alanı de etkiler. Örneğin, zaten 106 TB boyutunda katmanlı birimlerin bulunduğu bir 8100 cihazınız varsa, yerel olarak sabitlenmiş birimlerin kullanabileceği yalnızca 4 TB’lık alan kalır.

    6. **Bağlı konaklar** alanında oka tıklayın ve ardından bağlamak istediğiniz her BIR ACR öğesini seçin. **Bağlı konaklar** dikey penceresinde, var olan bir ACR seçin veya yeni bir ACR ekleyin. Yeni bir ACR seçerseniz, ACR 'niz için bir **ad** sağlayın, Windows ana bilgisayarınız Için **iSCSI tam adını** (IQN) sağlayın. IQN’niz yoksa Windows Server konağının IQN’sini al’a gidin.<!--Please verify: Is new ACR capability still available? Are the labels and controls the same?--> 

        ![Bağlı konak Ekle](./media/storsimple-8000-manage-volumes-u2/add-volume-02.png)<!--New graphic. Source: add-volume-connected host-->

   7. Ayarlarınızı tamamladığınızda **Oluştur**' a tıklayın. 

      Belirtilen ayarlarla bir birim oluşturulur. Yeni biriminiz kullanıma hazırlanıyor.

> [!NOTE]
> Yerel olarak sabitlenmiş bir birim oluşturup daha sonra hemen yerel olarak sabitlenmiş bir birim oluşturursanız, birim oluşturma işleri sırayla çalışır. İlk birim oluşturma işinin bir sonraki birim oluşturma işinin başlayabilmesi için önce son olması gerekir.

## <a name="modify-a-volume"></a>Bir birimi değiştirme

Bir birimi genişletmeniz veya birime erişen Konakları değiştirmeniz gerektiğinde değiştirin.

> [!IMPORTANT]
> * Cihazdaki birim boyutunu değiştirirseniz, birim boyutunun de konakta değiştirilmesi gerekir.
> * Burada açıklanan konak tarafı adımları Windows Server 2012 (2012R2) içindir. Linux veya diğer ana bilgisayar işletim sistemleri için yordamlar farklı olacaktır. Başka bir işletim sistemi çalıştıran bir konaktaki birimi değiştirirken ana bilgisayar işletim sistemi yönergelerinizi inceleyin.

#### <a name="to-modify-a-volume"></a>Bir birimi değiştirmek için

1. StorSimple Cihaz Yöneticisi hizmetinize gidin ve **Cihazlar**’a tıklayın. Cihazların tablolu listesinden, değiştirmek istediğiniz birimin bulunduğu cihazı seçin. **Ayarlar > birimler**' e tıklayın.

    ![Birimler dikey penceresine git](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

2. Birimlerin tablolu listesinden birim ' i seçin ve bağlam menüsünü çağırmak için sağ tıklayın. Çevrimdışı olarak değiştireceğiniz birimi çevrimdışına **Al** ' ı seçin.

    ![Birimi çevrimdışına al ve Seç](./media/storsimple-8000-manage-volumes-u2/modify-volume-04.png)

3. **Çevrimdışına Al** dikey penceresinde, birimi çevrimdışı duruma getirmenin etkilerini gözden geçirin ve ilgili onay kutusunu seçin. Konakta karşılık gelen birimin önce çevrimdışı olduğundan emin olun. StorSimple 'a bağlı ana bilgisayar sunucunuzda bir birimi çevrimdışı duruma getirme hakkında daha fazla bilgi için, işletim sistemine özgü yönergelere bakın. **Çevrimdışına Al**' a tıklayın.

    ![Birimi çevrimdışına alma etkisini gözden geçirin](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)

4. Birim çevrimdışı olduktan sonra (birim durumunda gösterildiği gibi), birimi seçin ve bağlam menüsünü çağırmak için sağ tıklayın. **Birimi Değiştir**' i seçin.

    ![Birimi Değiştir ' i seçin](./media/storsimple-8000-manage-volumes-u2/modify-volume-09.png)


5. **Birim Değiştir** dikey penceresinde aşağıdaki değişiklikleri yapabilirsiniz:
   
   1. Birim **adı** düzenlenemiyor.
   2. **Türü** yerel olarak sabitlenmiş veya katmanlı olan yerel olarak sabitlenmiş olarak dönüştürün (daha fazla bilgi için bkz. [birim türünü değiştirme](#change-the-volume-type) ).
   3. **Sağlanan kapasiteyi** artırın. **Sağlanan kapasite** yalnızca artırılabilir. Oluşturulduktan sonra bir birimi daraltamazsınız.
   4. **Bağlı konaklar**' ın altında ACR 'yi değiştirebilirsiniz. Bir ACR 'yi değiştirmek için birimin çevrimdışı olması gerekir.

       ![Birimi çevrimdışı duruma getirme etkisini gözden geçirme 2](./media/storsimple-8000-manage-volumes-u2/modify-volume-11.png)<!--Legacy screen doesn't match step. New graphic needed?-->

6. **Kaydet**’a tıklayarak değişikliklerinizi kaydedin. Onayınız istendiğinde **Evet**’e tıklayın. Azure portal güncelleştirme birimi iletisi görüntülenir. Birim başarıyla güncelleştirildiği zaman başarılı bir ileti görüntülenir.

    ![Birimi çevrimdışı duruma getirme etkisini gözden geçirme 3](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)<!--Updated graphic. Source: modify-volume-save-->

7. Bir birimi genişletiğiniz takdirde, Windows ana bilgisayarınızda aşağıdaki adımları uygulayın:
   
   1. **Bilgisayar Yönetimi**  -> **disk yönetimi**' ne gidin.
   2. **Disk yönetimi** ' ne sağ tıklayın ve **diskleri yeniden Tara**' yı seçin.
   3. Diskler listesinde, güncelleştirdiğiniz birimi seçin, sağ tıklayın ve ardından **Birimi Genişlet**' i seçin. Birim Genişletme Sihirbazı başlar. **İleri**’ye tıklayın.
   4. Varsayılan değerleri kabul ederek Sihirbazı doldurun. Sihirbaz tamamlandıktan sonra birimde artan boyut gösterilmelidir.
      
      > [!NOTE]
      > Yerel olarak sabitlenmiş bir birimi genişletir ve ardından daha sonra yerel olarak sabitlenmiş bir birimi genişletirseniz, birim genişletme işleri sırayla çalışır. Sonraki birim genişletme işinin başlayabilmesi için ilk birim genişletme işinin son olması gerekir.
      

## <a name="change-the-volume-type"></a>Birim türünü değiştirme

Birim türünü katmanlı iken yerel olarak sabitlenmiş veya yerel olarak sabitlenmiş olarak değiştirebilirsiniz. Ancak, bu dönüştürme sık görülen bir oluşum olmamalıdır.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Katmanlı yerel birim dönüştürme konuları

Bir birimi katmanlı iken yerel olarak sabitlenmiş olarak dönüştürmenin bazı nedenleri şunlardır:

* Veri kullanılabilirliği ve performansına ilişkin yerel garanti
* Bulut gecikme sürelerinin ve bulut bağlantı sorunlarının giderilmesi.

Genellikle, bunlar sıklıkla erişmek istediğiniz küçük hacimlerdir. Yerel olarak sabitlenmiş bir birim oluşturulduğunda tam olarak sağlanmış olur. 

Katmanlı bir birimi yerel olarak sabitlenmiş bir birime dönüştürüyorsanız, StorSimple, dönüştürmeye başlamadan önce cihazınızda yeterli alana sahip olduğunuzu doğrular. Yeterli alanınız yoksa bir hata alırsınız ve işlem iptal edilir. 

> [!NOTE]
> Katmanlı 'dan yerel olarak sabitlenmiş bir dönüştürmeye başlamadan önce, diğer iş yüklerinizin alan gereksinimlerini dikkate aldığınızdan emin olun. 

Katmanlı bilgisayardan yerel olarak sabitlenmiş bir birime dönüştürme, cihaz performansını olumsuz etkileyebilir. Ayrıca, aşağıdaki faktörler dönüştürme işleminin tamamlanma süresini artırabilir:

* Bant genişliği yetersiz.
* Geçerli yedekleme yok.

Bu faktörlerin sahip olabileceği etkileri en aza indirmek için:

* Bant genişliği azaltma ilkelerinizi gözden geçirin ve ayrılmış 40 Mbps bant genişliğinin kullanılabilir olduğundan emin olun.
* Dönüştürmeyi yoğun olmayan saatlere göre zamanlayın.
* Dönüştürme işlemine başlamadan önce bir bulut anlık görüntüsü alın.

Birden çok birimi dönüştürüyorsanız (farklı iş yüklerini destekleme), öncelikle daha yüksek öncelikli birimlerin dönüştürülmesi için birim dönüştürmeye öncelik vermeniz gerekir. Örneğin, birimleri dosya paylaşma iş yüklerine dönüştürmeden önce, sanal makineleri (VM) veya birimleri barındıran birimleri SQL iş yükleriyle dönüştürmeniz gerekir.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Yerelden katmanlı birim dönüştürme konuları

Diğer birimleri sağlamak için ek alana ihtiyaç duyuyorsanız, yerel olarak sabitlenmiş bir birimi katmanlı bir birim olarak değiştirmek isteyebilirsiniz. Yerel olarak sabitlenmiş birimi katmanlı olarak dönüştürdüğünüzde, cihazdaki kullanılabilir kapasite çıkarılan kapasitenin boyutuna göre artar. Bağlantı sorunları, bir birimin yerel türden katmanlı türe dönüştürülmesini engelliyorsa, yerel birim, dönüştürme tamamlanana kadar katmanlı bir birimin özelliklerini gösterir. Bunun nedeni bazı verilerin buluta taşabileceğinden kaynaklanabilir. Bu veriler, işlem yeniden başlatılana ve tamamlanana kadar, cihazdaki yerel alan boşaltmaya devam eder.

> [!NOTE]
> Bir birimi dönüştürmek biraz zaman alabilir ve başladıktan sonra dönüştürmeyi iptal edemezsiniz. Birim dönüştürme sırasında çevrimiçi kalır ve yedeklemeler alabilir, ancak dönüştürme gerçekleşirken birimi genişletemiyor veya geri yükleyemezsiniz.


#### <a name="to-change-the-volume-type"></a>Birim türünü değiştirmek için

1. StorSimple Cihaz Yöneticisi hizmetinize gidin ve **Cihazlar**’a tıklayın. Cihazların tablolu listesinden, değiştirmek istediğiniz birimin bulunduğu cihazı seçin. **Ayarlar > birimler**' e tıklayın.

    ![Birimlere git dikey penceresi 2](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

3. Birimlerin tablolu listesinden birim ' i seçin ve bağlam menüsünü çağırmak için sağ tıklayın. **Değiştir**'i seçin.

    ![Bağlam menüsünden Değiştir ' i seçin](./media/storsimple-8000-manage-volumes-u2/change-volume-type-02.png)

4. **Birim Değiştir** dikey penceresinde, **tür** açılan listesinden yeni türü seçerek birim türünü değiştirin.
   
   * Türü **yerel olarak sabitlenmiş** olarak değiştiriyorsanız, StorSimple yeterli kapasite olup olmadığını kontrol eder.
   * Türü **katmanlı** olarak değiştiriyorsanız ve bu birim arşiv verileri için kullanılacaksa, **daha az sıklıkta erişilen arşiv verileri Için bu birimi kullan** onay kutusunu seçin.
   * Yerel olarak sabitlenmiş bir birimi katmanlı veya _tam tersi_ şekilde yapılandırıyorsanız, aşağıdaki ileti görünür.
   
     ![Birim türü iletisini Değiştir](./media/storsimple-8000-manage-volumes-u2/change-volume-type-03.png)

7. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın. Onay istendiğinde, dönüştürme işlemini başlatmak için **Evet** ' e tıklayın. 

    ![Kaydet ve Onayla](./media/storsimple-8000-manage-volumes-u2/modify-volume-11.png)

8. Azure portal, birimi güncelleştiren iş oluşturma için bir bildirim görüntüler. Birim dönüştürme işinin durumunu izlemek için bildirime tıklayın.

    ![Birim dönüştürme işi](./media/storsimple-8000-manage-volumes-u2/change-volume-type-05.png)

## <a name="take-a-volume-offline"></a>Bir birimi çevrimdışı duruma getirme

Birimi değiştirmeyi veya silmeyi planlarken bir birimi çevrimdışına almanız gerekebilir. Bir birim çevrimdışıyken, okuma/yazma erişimi için kullanılamaz. Birimi konakta ve cihazda çevrimdışı duruma almalısınız.

#### <a name="to-take-a-volume-offline"></a>Bir birimi çevrimdışı duruma getirme

1. Söz konusu birimin çevrimdışı duruma getirmeden önce kullanımda olmadığından emin olun.
2. Birimi önce konakta çevrimdışına alın. Bu, birimdeki veri bozulması olasılığını ortadan kaldırır. Belirli adımlar için, ana bilgisayar işletim sisteminizle ilgili yönergelere bakın.
3. Konak çevrimdışı olduktan sonra aşağıdaki adımları gerçekleştirerek cihazdaki birimi çevrimdışına alın:
   
    1. StorSimple Cihaz Yöneticisi hizmetinize gidin ve **Cihazlar**’a tıklayın. Cihazların tablolu listesinden, değiştirmek istediğiniz birimin bulunduğu cihazı seçin. **Ayarlar > birimler**' e tıklayın.

        ![Birimler dikey penceresine git 3](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

    2. Birimlerin tablolu listesinden birim ' i seçin ve bağlam menüsünü çağırmak için sağ tıklayın. Çevrimdışı olarak değiştireceğiniz birimi çevrimdışına **Al** ' ı seçin.

        ![Birimi çevrimdışına al ve Seç](./media/storsimple-8000-manage-volumes-u2/modify-volume-04.png)

3. **Çevrimdışına Al** dikey penceresinde, birimi çevrimdışı duruma getirmenin etkilerini gözden geçirin ve ilgili onay kutusunu seçin. **Çevrimdışına Al**' a tıklayın. 

    ![Birimi çevrimdışı duruma getirme etkisini gözden geçirme 4](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)
      
      Birim çevrimdışıyken bilgilendirilirsiniz. Birim durumu da çevrimdışı olarak güncelleştirilir.
      
4. Bir birim çevrimdışı olduktan sonra, birimi seçer ve sağ tıkladıysanız bağlam menüsünde **çevrimiçi duruma getir** seçeneği kullanılabilir hale gelir.

> [!NOTE]
> **Çevrimdışına Al** komutu, birimi çevrimdışına almak için cihaza bir istek gönderir. Konaklar hala birim kullanıyorsa, bu durum kopuk bağlantılarla sonuçlanır, ancak birimi çevrimdışına almak başarısız olmaz.

## <a name="delete-a-volume"></a>Bir birimi silme

> [!IMPORTANT]
> Bir birimi yalnızca çevrimdışı ise silebilirsiniz.

Bir birimi silmek için aşağıdaki adımları izleyin.

#### <a name="to-delete-a-volume"></a>Bir birimi silmek için

1. StorSimple Cihaz Yöneticisi hizmetinize gidin ve **Cihazlar**’a tıklayın. Cihazların tablolu listesinden, değiştirmek istediğiniz birimin bulunduğu cihazı seçin. **Ayarlar > birimler**' e tıklayın.

    ![Birimler dikey penceresine git 4](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

3. Silmek istediğiniz birimin durumunu denetleyin. Silmek istediğiniz birim çevrimdışı değilse, önce çevrimdışına alın. [Birimi çevrimdışına alma](#take-a-volume-offline)bölümündeki adımları izleyin.
4. Birim çevrimdışı olduktan sonra, birimi seçin, bağlam menüsünü çağırmak için sağ tıklayın ve **Sil**' i seçin.

    ![Bağlam menüsünden Sil ' i seçin](./media/storsimple-8000-manage-volumes-u2/delete-volume-01.png)

5. **Sil** dikey penceresinde, bir birimi silmenin etkilerine karşı gözden geçirin ve onay kutusunu seçin. Bir birimi sildiğinizde, birimde bulunan tüm veriler kaybolur. 

    ![Değişiklikleri kaydetme ve onaylama](./media/storsimple-8000-manage-volumes-u2/delete-volume-02.png)

6. Birim silindikten sonra, silme birimlerinin Tablo listesi, silme işlemini belirtecek şekilde güncelleştirilir.

    ![Güncelleştirilmiş birim listesi](./media/storsimple-8000-manage-volumes-u2/delete-volume-03.png)
   
   > [!NOTE]
   > Yerel olarak sabitlenmiş bir birimi silerseniz, yeni birimler için kullanılabilir alan hemen güncelleştirilmemiş olabilir. StorSimple Device Manager hizmeti, düzenli olarak kullanılabilir yerel alanı güncelleştirir. Yeni birimi oluşturmayı denemeden önce birkaç dakika beklemenizi öneririz.
   >
   > Ayrıca, yerel olarak sabitlenmiş bir birimi siler ve ardından daha sonra yerel olarak sabitlenmiş bir birimi silerseniz, birim silme işleri sırayla çalışır. İlk birim silme işi, bir sonraki birim silme işinin başlayabilmesi için bitmelidir.

## <a name="monitor-a-volume"></a>Bir birimi izleme

Birim izleme, bir birim için g/ç ile ilgili istatistikleri toplamanıza olanak tanır. İzleme, oluşturduğunuz ilk 32 birim için varsayılan olarak etkindir. Ek birimlerin izlenmesi varsayılan olarak devre dışıdır. 

> [!NOTE]
> Kopyalanmış birimlerin izlenmesi varsayılan olarak devre dışıdır.


Bir birim için izlemeyi etkinleştirmek veya devre dışı bırakmak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Toplu izlemeyi etkinleştirmek veya devre dışı bırakmak için

1. StorSimple Cihaz Yöneticisi hizmetinize gidin ve **Cihazlar**’a tıklayın. Cihazların tablolu listesinden, değiştirmek istediğiniz birimin bulunduğu cihazı seçin. **Ayarlar > birimler**' e tıklayın.
2. Birimlerin tablolu listesinden birim ' i seçin ve bağlam menüsünü çağırmak için sağ tıklayın. **Değiştir**'i seçin.
3. **Birimi Değiştir** dikey penceresinde, **izleme** için **Etkinleştir** veya devre dışı **bırak** ' ı seçin.

    ! [İzlemeyi devre dışı bırak] (./Media/StorSimple-8000-Manage-Volumes-U2/Monitor-Volume-0 1.png) 

4. **Kaydet** ' e tıklayın ve onay istendiğinde **Evet**' e tıklayın. Azure portal, Birim başarıyla güncelleştirildikten sonra birimi güncelleştirmek için bir bildirim ve sonra bir başarı iletisi görüntüler.

## <a name="next-steps"></a>Sonraki adımlar

* [StorSimple birimini klonlamak](storsimple-8000-clone-volume-u2.md)hakkında bilgi edinin.
* StorSimple [cihazınızı yönetmek Için storsimple Device Manager hizmetini nasıl kullanacağınızı](storsimple-8000-manager-service-administration.md)öğrenin.

