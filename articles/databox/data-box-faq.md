---
title: Microsoft Azure Data Box Hakkında SSS | Microsoft Docs
description: Büyük miktarlardaki verileri Azure 'a aktarmanızı sağlayan bir bulut çözümü olan Azure Data Box için sık sorulan sorular ve yanıtları içerir.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/25/2021
ms.author: alkohli
ms.custom: references_regions
ms.openlocfilehash: a692aeba312b6fcad580eac901f4b7bc65f059fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730584"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: Sık Sorulan Sorular

Microsoft Azure Data Box hibrit çözümü, bir aktarım cihazı kullanarak terabaytlarca veriyi Azure'a hızlı, uygun maliyetli ve güvenilir bir şekilde göndermenizi sağlar. Bu SSS belgesinde Azure portaldaki Data Box kullanımınızla ilgili sorulara ve yanıtlarına yer verilmiştir.

Sorular ve yanıtlar aşağıdaki kategorilere ayrılmıştır:

- Hizmet hakkında
- Cihaz sipariş etme
- Yapılandırma ve bağlanma 
- Durumu izleme
- Veri kopyalama 
- Cihaz gönderme
- Verileri doğrulama ve yükleme 
- Koruma zinciri desteği

## <a name="about-the-service"></a>Hizmet hakkında

### <a name="q-what-is-azure-data-box-service"></a>S. Azure Data Box hizmeti nedir? 
A.  Azure Data Box hizmeti, çevrimdışı veri alımı için tasarlanmıştır. Bu hizmet tamamı veri aktarımı için tasarlanmış olan farklı depolama kapasitelerine sahip ürün dizisini yönetir. 

### <a name="q-what-is-azure-data-box"></a>S. Azure Data Box nedir?
A. Azure Data Box, terabaytlık verilerin Azure 'a hızlı, pahalı ve güvenli bir şekilde aktarılmasına izin verir. Data Box cihazını Azure portalı üzerinden sipariş edersiniz. Microsoft, bölgesel bir taşıyıcı aracılığıyla 80 TB 'lık bir kapasiteye sahip bir depolama cihazı sevk eder. 

Cihaz size ulaştıktan sonra, yerel web kullanıcı arabirimini kullanarak cihazı hızla ayarlayabilirsiniz. Sunucularınızdaki verileri cihaza veya cihazdan sunuculara kopyalayın ve cihazı Azure 'a geri gönderin. Azure veri merkezinde içeri aktarma sırası için verileriniz cihazdan Azure 'a otomatik olarak yüklenir. Sürecin tamamı Azure portalındaki Data Box hizmeti tarafından uçtan uca izlenir.

### <a name="q-when-should-i-use-data-box"></a>S. Data Box'ı ne zaman kullanmalıyım?
A. Azure 'a veya Azure 'a aktarmak istediğiniz 40-500 TB veriniz varsa Data Box kullanmaktan faydalanabilirsiniz. 40 TB < veri boyutları için Data Box Disk ve veri boyutları > 500 TB [Data Box Heavy](data-box-heavy-overview.md)için kaydolun.

### <a name="q-what-is-the-price-of-data-box"></a>S. Data Box'ın maliyeti nedir?
A. Data Box için 10 gün boyunca nominal bir ücret tahsil edilir. Azure portalda sipariş oluştururken ürün modelini seçme aşamasında cihaz ücretleri görüntülenir. Azure depolama için standart teslimat ücretleri ve ücretleri de geçerlidir. Dışarı aktarma siparişleri, içeri aktarma siparişleri gibi benzer bir fiyatlandırma modelini takip edebilir, ancak ek çıkış ücretleri uygulanabilir. 

Daha fazla bilgi için [Azure Data Box fiyatlandırma](https://azure.microsoft.com/pricing/details/storage/databox/) ve [Çıkış ücretleri](https://azure.microsoft.com/pricing/details/bandwidth/)' ne gidin. 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>S. Data Box ile tek seferde en fazla ne kadar veri aktarabilirim?
A. Data Box toplamda 100 TB, kullanılabilir alan olarak ise 80 TB kapasiteye sahiptir. Data Box en fazla 80 TB veri aktarabilirsiniz. Daha fazla veri aktarmak için daha fazla cihaz sipariş etmeniz gerekir.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>S. Data Box'ın bulunduğum bölgede kullanılabilir durumda olup olmadığını nasıl kontrol edebilirim? 
A.  Data Box hangi ülkelerin/bölgelerin kullanılabildiği hakkında bilgi edinmek için [bölge kullanılabilirliği](data-box-overview.md#region-availability)' ne gidin.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>S. Data Box ile hangi bölgelerde veri depolayabilirim?
A. Data Box ABD, Batı Avrupa, Kuzey Avrupa, Fransa, UK, Japonya, Avustralya ve Kanada 'daki tüm bölgeler için desteklenir. Daha fazla bilgi için [bölge kullanılabilirliği](data-box-overview.md#region-availability)' ne gidin.

### <a name="q-how-can-i-import-source-data-at-my-location-in-a-particular-country-to-an-azure-region-in-a-different-countryregion-or-export-data-from-an-azure-region-in-one-country-to-a-different-countryregion"></a>S. Belirli bir ülkede yer alan konumdaki kaynak verileri farklı bir ülkede/bölgede bir Azure bölgesine aktarabilir veya bir ülkede bir Azure bölgesinden farklı bir ülkeye/bölgeye veri aktarabilirsiniz?

Data Box, yalnızca hedefle aynı ülke/bölge dahilinde veri alımı veya çıkışları destekler ve uluslararası kenarlıkları geçmez. Tek istisna, Avrupa Birliği (AB), veri kutularının herhangi bir AB ülkesine/bölgesine nereden gönderdikleriyle ilgili olan emirler için geçerlidir.

Örneğin, içeri aktarma senaryosunda, Kanada 'da bir Azure Batı ABD depolama hesabına taşımak istediğiniz kaynak veriler varsa, bunu aşağıdaki şekilde elde edebilirsiniz:

1. Kanada 'da bir depolama hesabı seçerek Kanada 'da sipariş Data Box. Cihaz, bir Azure veri merkezinden, sipariş oluşturma sırasında belirtilen nakliye adresine (Kanada 'da) gönderilir.

2. Data Box şirket içi veri kopyalama işlemi yapıldıktan sonra, cihazı Kanada 'daki Azure veri merkezine döndürün. Data Box bulunan veriler, sipariş oluşturma sırasında seçilen Kanada Azure bölgesindeki hedef depolama hesabına yüklenir.

3. Daha sonra AzCopy gibi bir araç kullanarak verileri Batı ABD bir depolama hesabına kopyalayabilirsiniz. Bu adım, Data Box faturalandırmaya dahil olmayan [Standart depolama](https://azure.microsoft.com/pricing/details/storage/) ve [bant genişliği ücretleri](https://azure.microsoft.com/pricing/details/bandwidth/) doğurur.

#### <a name="q-does-data-box-store-any-customer-data-outside-of-the-service-region"></a>S. Data Box tüm müşteri verilerini hizmet bölgesi dışında mı depolar?

A. Hayır. Data Box, hiçbir müşteri verisini hizmet bölgesinin dışında depolamaz. Müşteri, verilerinin tam sahipliğine sahiptir ve verileri, sipariş oluşturma sırasında seçtikleri depolama hesabına göre belirtilen bir konuma kaydedebilir.  

Müşteri verilerine ek olarak, cihazla ilgili güvenlik yapıtları, cihaz ve hizmet için günlükleri ve hizmetle ilgili meta verileri içeren Data Box veriler vardır. Tüm bölgelerde (Brezilya Güney ve Güneydoğu Asya dışında), veri kaybına karşı korumak için verileri Data Box bir coğrafi olarak yedekli depolama hesabı aracılığıyla eşleştirilmiş bölgede depolanır ve çoğaltılır.  

Brezilya Güney ve Güneydoğu Asya 'daki [veri](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) yerleşimi nedeniyle Data Box veriler, tek bir bölgede yer alması için bölge yedekli bir depolama (ZRS) hesabında depolanır. Güneydoğu Asya için tüm Data Box veriler Singapur 'da depolanır ve Brezilya Güney için veriler Brezilya 'da depolanır. 

Brezilya Güney ve Güneydoğu Asya 'da hizmet kesintisi varsa, müşteriler başka bir bölgeden yeni siparişler oluşturabilir. Yeni siparişler, oluşturuldukları bölgeden ve Data Box cihazının to ve testten sevkiyatından sorumlu olarak sunulur.

### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>S. Tüm bölge başarısız olursa verilerimi nasıl kurtarabilirim?

A. Önemli bir olağanüstü durum nedeniyle bölgenin kaybolduğu, Microsoft bölgesel bir yük devretme işlemi başlatabilir. Bu durumda, sizin bölüminizdeki hiçbir işlem yapmanız gerekmez. Siparişiniz aynı ülke veya ticari sınır içindeyse yük devretme bölgesi aracılığıyla yerine getirilir. Ancak, bazı Azure bölgelerinin aynı coğrafi veya ticaret sınırında eşleştirilmiş bir bölgesi yoktur. Bu bölgelerin herhangi birinde bir olağanüstü durum varsa, kullanılabilir farklı bir bölgeden Data Box sırayı yeniden oluşturmanız ve verileri yeni bölgede Azure 'a kopyalamanız gerekir. Daha fazla bilgi için bkz. [İş sürekliliği ve olağanüstü durum kurtarma (BCDR): Eşleştirilmiş Azure Bölgeleri](../best-practices-availability-paired-regions.md).

### <a name="q-who-should-i-contact-if-i-come-across-any-issues-with-data-box"></a>S. Data Box ile ilgili herhangi bir sorun yaşıyorsanız kimler iletişim kurmalıyım?
A. Data Box herhangi bir sorun yaşıyorsanız, [Microsoft desteği başvurun](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-lost-my-data-box-is-there-a-lost-device-charge"></a>S. Data Box kayboluyor. Kayıp bir cihaz ücreti var mı?
A. Evet. Kayıp veya hasarlı bir cihaz için ücret alınır. Bu ücret, [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/storage/databox/) ve [ürün hizmet koşulları](https://www.microsoft.com/licensing/product-licensing/products)'nda ele alınmıştır.


## <a name="order-device"></a>Cihaz sipariş etme

### <a name="q-how-do-i-get-data-box"></a>S. Data Box'ı nasıl edinebilirim? 
A.  Azure Data Box'ı almak için Azure portalda oturum açın ve bir Data Box siparişi oluşturun. İletişim bilgilerinizi ve bildirim ayrıntılarını girin. Sipariş verdikten sonra kullanılabilirlik durumuna göre Data Box 10 gün içinde gönderilir. Daha fazla bilgi için bkz. [Data Box sipariş etme](data-box-deploy-ordered.md).

### <a name="q-i-couldnt-create-a-data-box-order-in-the-azure-portal-why"></a>S. Azure portal Data Box sıra oluşturamadık. Neden?
A. Data Box sıra oluşturamıyoruz, abonelik türü veya erişim ile ilgili bir sorun vardır.

Aboneliğinizi kontrol edin. Data Box yalnızca Kurumsal Anlaşma (EA) ve Bulut çözüm sağlayıcısı (CSP) aboneliği teklifleriyle kullanılabilir. Bu abonelik türlerinden birini yoksa aboneliğinizi yükseltmek için Microsoft Desteği başvurun.

Aboneliğiniz, desteklenen teklif türlerinden birine aitse abonelik erişim düzeyinizi kontrol edin. Sipariş oluşturma için aboneliğinizde katkıda bulunan veya sahip olmanız gerekir.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>S. Sıramın Azure 'a yüklenen verilere göre sipariş oluşturma ne kadar sürer?

A. Sipariş işlemenin her aşaması için aşağıdaki tahmini sağlama süreleri, beklendiklerle ilgili size iyi bir fikir verecektir.  

Bu sağlama süreleri *tahminlerdir*. Her sipariş işleme aşamasına ilişkin süre, veri merkezinde, eşzamanlı siparişlerde ve diğer ortam koşullarında yük tarafından etkilenir.

**Data Box sırası için tahmini sağlama süreleri:**

1. Order Data Box: birkaç dakika, portaldan
2. Cihaz ayırma ve hazırlık: 1-2 iş günü, envanter kullanılabilirliğine ve yerine getirilmesi bekleyen diğer siparişlere tabi
3. Gönderim: 2-3 iş günü
4. Müşteri sitesindeki veri kopyalama: verilerin, boyutun ve dosya sayısının yapısına bağlıdır
5. İade gönderimi: 2-3 iş günü
6. Cihazı veri merkezinde işleme: 1-2 iş günü, işleme bekleyen diğer siparişlere tabi
7. Azure 'a veri yükleme: işlem tamamlandıktan hemen sonra cihaz bağlı olduğunda başlar. Karşıya yükleme süresi verilerin, boyutun ve dosya sayısının yapısına bağlıdır.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-cant-create-any-additional-orders-why"></a>S. Birkaç Data Box cihazı sipariş ettim. Ek sipariş oluşturamıyorum. Neden?
A. Her ticari sınır (ülke ve bölge birleşimi) başına abonelik başına en fazla beş etkin siparişe izin veririz. Daha fazla cihaz sipariş etmeniz gerekiyorsa Microsoft Desteği ile iletişime geçerek aboneliğinizin sınırını artırabilirsiniz.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>S. Sipariş oluşturmaya çalıştığımda Data Box hizmetinin kullanılabilir durumda olmadığını belirten bir bildirim alıyorum. Bu ne anlama geliyor?
A. Data Box hizmeti seçtiğiniz ülke ve bölge birleşimi için kullanılamaz. Bu birleşimi değiştirmeniz durumunda muhtemelen Data Box hizmetinden faydalanabileceksiniz. Hizmetin kullanılabildiği bölgelerin listesi için bkz. [Data Box için bölge kullanılabilirliği](data-box-overview.md#region-availability).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>S. Data Box siparişimi vereli birkaç gün oldu. Data Box cihazım ne zaman gelecek?
A. Sipariş verdiğinizde uygun bir cihazın olup olmadığı kontrol edilir. Cihaz varsa 10 gün içinde gönderilir. Talebin yüksek olduğu dönemlerle de karşılaşabilirsiniz. Bu durumda siparişiniz sıraya alınır ve durumunu Azure portaldan takip edebilirsiniz. Siparişiniz 90 gün içinde işleme alınmazsa otomatik olarak iptal edilir.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>S. Data Box verilerle doldurdum ve başka bir sıralama yapmanız gerekiyor. Siparişi hızlı bir şekilde vermek için kullanabileceğim bir yöntem var mı?
A. Önceki siparişinizi kopyalayabilirsiniz. Kopyalama işlemi, bir öncekiyle aynı bilgilere sahip bir sipariş oluşturur ve adres, iletişim ve bildirim ayrıntılarını yeniden girmenize gerek kalmadan yalnızca sipariş bilgilerini düzenleyebilirsiniz. Yalnızca içeri aktarma siparişlerinde kopyalamaya izin verilir.

## <a name="configure-and-connect"></a>Yapılandırma ve bağlanma

### <a name="q-how-do-i-unlock-the-data-box"></a>S. Data Box cihazının kilidini nasıl açabilirim? 
A.  Azure portalda Data Box siparişinize ve **Cihaz ayrıntıları**'na gidin. Kilit açma parolasını kopyalayın. Bu parolayı kullanarak Data Box cihazınızın yerel web arabiriminde oturum açabilirsiniz. Daha fazla bilgi için bkz. [Öğretici: Azure Data Box'ın paketini açma, kablolarını takma ve bağlantılarını yapma](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>S. Data Box'a bağlanmak ve veri kopyalamak için Linux ana bilgisayarı kullanabilir miyim?
A.  Evet. Data Box ile SMB ve NFS istemcilerine bağlanabilirsiniz. Daha fazla bilgi için ana bilgisayarınıza ilişkin [Desteklenen işletim sistemleri](data-box-system-requirements.md) listesine gidin.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>S. Data Box'ım yola çıktı ancak şimdi siparişimi iptal etmek istiyorum. Neden iptal düğmesi kullanılamıyor?
A.  Siparişi yalnızca Data Box siparişi verdikten sonra ancak sipariş işleme alınmadan önce iptal edebilirsiniz. Data Box siparişi işleme alındıktan sonra iptal edemezsiniz. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>S. Veri aktarımı için bir Data Box'ı aynı anda birden fazla ana bilgisayara bağlayabilir miyim?
A. Evet. Bir Data Box'a birden fazla ana bilgisayar bağlanarak aynı anda birden fazla veri aktarım ve kopyalama işi çalıştırılabilir. Daha fazla bilgi için bkz. [Öğretici: Azure Data Box'a veri kopyalama](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>S. Verileri aktarmak için Data Box hem 10 GbE arabirimine bağlanabilir miyim?
A. Evet. 10-GbE arabirimlerinin her ikisi de verileri aynı anda kopyalamak için Data Box bağlı olabilir. Verileri kopyalama hakkında daha fazla bilgi için bkz. [öğretici: verileri Azure Data Box kopyalama](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why?
A.

### Q. I could not set up Data Box using a Static IP address. Why?
A.

### Q. I could not set up Data Box on a private network. Why?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>S. Ön paneldeki sistem hatası göstergesi LED ışığı yanıyor. Ne yapmalıyım?
A. Data Box önünde güç düğmesi altında iki LED ışıkları vardır. En alttaki ışık sistem hatası göstergesidir ve sisteminizin sağlıklı olup olmadığını gösterir.

Kırmızı olan bir sistem hatası göstergesi, aşağıdaki sorunlardan birini gösterebilir:
- Fan hatası
- CPU sıcaklık yüksek
- Ana kart sıcaklığı yüksek
- Çift satır içi bellek modülü (DıMM) kod bağlama hatası (ECC) hatası

Şu adımları uygulayın:
1. Fanı 'ın çalışıp çalışmadığını denetleyin.
2. Cihazı daha fazla Airflow ile bir konuma taşıyın.

Sistem hata göstergesi ışığı hala açık ise [Microsoft desteği başvurun](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why"></a>S. Azure portalda Data Box kilit açma parolasına erişemiyorum. Neden?
A. Azure portal kilit açma parolasına erişemeyebilirsiniz, aboneliğiniz ve Depolama hesabınızdaki izinleri denetleyin. Kaynak grubu düzeyinde katkıda bulunan veya sahip izinlerine sahip olduğunuzdan emin olun. Erişim kimlik bilgilerini görmek için en az Data Box operatör rolü iznine sahip olmanız gerekir.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>S. Data Box bağlantı noktası kanal yapılandırması destekleniyor mu? MPIO nasıl?
A. Bağlantı noktası kanal yapılandırması, çok yollu GÇ (MPIO) yapılandırması veya Data Box üzerinde vLAN yapılandırması desteklemiyoruz.

## <a name="track-status"></a>Durumu izleme

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>S. Data Box'ı geri göndermek için sipariş oluşturduktan sonra cihazı nasıl takip edebilirim? 
A.  Data Box siparişinin durumunu Azure portaldan takip edebilirsiniz. Siparişi oluşturduğunuzda bir bildirim e-postası girmeniz istenir. Bir tane sağladıysanız, sipariş için tüm durum değişikliklerinin e-postası aracılığıyla size bildirilir. [Bilgilendirme e-postalarını yapılandırma](data-box-portal-ui-admin.md#edit-notification-details) hakkında daha fazla bilgi edinin.

### <a name="q-how-do-i-return-the-device"></a>S. Cihazı nasıl iade ederim? 
A.  Microsoft, E-mürekkep ekranında bir sevkiyat etiketi görüntüler. Sevkiyat Etiketi E-mürekkep görünümünde görünmüyorsa, **genel bakış > sevkiyat etiketini indirme** bölümüne gidin. Etiketi indirip yazdırın, etiketi cihazdaki açık plastik etiketine ekleyin ve cihazı sevkiyat taşıyıcısı konumundan bırakın. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>S. Cihazımın Azure veri merkezine ulaştığını bildiren bir e-posta bildirimi aldım. Veri yükleme işleminin ilerleme durumunu nasıl takip edebilirim?
A. Azure portalda Data Box siparişinize gidip **Genel bakış** sayfasını inceleyebilirsiniz. Azure'a veri yükleme işlemi başladıysa kopyalama ilerleme durumu sağ taraftaki bölmede gösterilir. 

## <a name="migrate-data"></a>Geçiş verileri

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>S. Data Box ile kullanılabilecek maksimum veri boyutu nedir?  
A.  Data Box'ın 80 TB kullanılabilir depolama kapasitesi vardır. 40 TB-80 TB arasındaki veriler için tek bir Data Box cihazı kullanabilirsiniz. 500 TB 'a kadar daha büyük veri boyutları için birden çok Data Box cihazı sipariş edebilirsiniz. 500 TB üzerindeki veriler için Data Box Heavy'ye kaydolun.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>S. Data Box tarafından desteklenen maksimum blok blobu ve sayfa blobu boyutu nedir? 
A.  Maksimum boyutlar Azure Depolama sınırları ile belirlenir. Maksimum blok blobu yaklaşık 4,768 TiB, maksimum sayfa blobu boyutu ise 8 TiB olarak belirlenmiştir. Daha fazla bilgi için bkz. [BLOB depolama Için ölçeklenebilirlik ve performans hedefleri](../storage/blobs/scalability-targets.md).

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>S. Verilerimin aktarım sırasında güvende olduğundan nasıl emin olabilirim? 
A. Data Box'ınızın taşıma sırasındaki güvenliğini sağlamak için kullanılan birçok güvenlik özelliği vardır. Bunların bazıları kurcalamaları gösteren mühürler, donanım ve yazılım kurcalama algılaması ve cihaz kilidini açma parolasıdır. Daha fazla bilgi için bkz. [Azure Data Box güvenliği ve veri koruması](data-box-security.md).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>S. Data Box'a nasıl veri kopyalayabilirim? 
A.  SMB istemcisi kullanıyorsanız, `Robocopy` `Diskboss` verileri cihaza kopyalamak için,, veya hatta Windows Dosya Gezgini gıbı bir SMB kopyalama aracı kullanabilirsiniz. 

NFS istemcisi kullanıyorsanız [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) veya [Ultracopier](https://ultracopier.first-world.info/) araçlarından faydalanabilirsiniz. 

Daha fazla bilgi için bkz. [Öğretici: Azure Data Box'a veri kopyalama](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>S. Veri kopyalama işlemini hızlandırmaya yönelik ipuçları var mı?
A.  Kopyalama işlemini hızlandırmak için:

- Birden fazla veri kopyalama akışı kullanın. Örneğin, ile `Robocopy` Çoklu iş parçacıklı seçeneğini kullanın. Kullanılan komut hakkında daha fazla bilgi için [Öğretici: Azure Data Box'a veri kopyalama ve doğrulama](data-box-deploy-copy-data.md) sayfasına gidin.
- Birden fazla oturum kullanın.
- Ağ paylaşımının üzerine kopyalamak yerine (Ağ hızları kopyalama hızını sınırlayabilir), verileri Data Box bağlı olduğu bilgisayarda yerel olarak depolayın.
- Veri kopyalamak için kullanılan bilgisayarın performansını karşılaştırın. Sunucu donanımının performansını kıyaslamaya yönelik [ `Bluestop` `FIO` Aracı](https://ci.appveyor.com/project/axboe/fio) indirin ve kullanın. En son x86 veya x64 yapısını seçin, **yapılar** sekmesini SEÇIN ve MSI 'yi indirin.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM's disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>S. Data Box ile birden fazla depolama hesabı kullanabilir miyim?
A.  Evet. Data Box ile genel amaçlı, klasik veya blob depolama olmak üzere en fazla 10 depolama hesabı kullanılabilir. Hem sık hem de seyrek erişimli bloblar desteklenir.


## <a name="ship-device"></a>Cihaz gönderme

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>S. Aygıtım teslim edildi, ancak cihaz hasarlı gibi görünüyor. Ne yapmalıyım?
A. Cihazınız hasar görmüşse veya izinsiz değişiklik bulgusu varsa, cihazı kullanmayın. [Microsoft desteği başvurun](data-box-disk-contact-microsoft-support.md) ve cihazı mümkün olan en kısa sürede geri döndürün. Yenisinin gönderilmesi için yeni bir Data Box siparişi de oluşturabilirsiniz. Bu durumda, yerine geçecek cihaz için ücretlendirilmezsiniz.

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>S. Data Box sıraımı kendi kendinize alabilir miyim? Seçdiğim bir taşıyıcı aracılığıyla Data Box döndürebilir miyim?
A. Evet. Microsoft, otomatik olarak yönetilen Kargo olanağı da sunar. Data Box sırayı yerleştirirken, kendi kendine yönetilen teslim seçeneğini belirleyebilirsiniz. Daha fazla bilgi için bkz. [Data Box Için kendi kendine yönetilen sevkıyat](data-box-portal-customer-managed-shipping.md).

### <a name="q-will-my-data-box-devices-cross-countryregion-borders-during-shipping"></a>S. Data Box Cihazlarım, Sevkiyat sırasında ülke/bölge kenarlıklarını ister misiniz?
A. Tüm Data Box cihazları, hedefle aynı ülke/bölge içinden gönderilir ve uluslararası kenarlıkları geçmez. Tek istisna, Avrupa Birliği (AB), cihazların herhangi bir AB ülkesine/bölgesine nereden gönderdiklerinden oluşan siparişler içindir. Bu hem Data Box hem de Data Box Heavy cihazları için geçerlidir.

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>S. ABD Doğu bir Data Box sipariş ediyorum, ancak ABD Batı bir konumdan gelen bir cihaz aldım. Cihazı nereye döndürmem gerekir?
A. Data Box bir cihazı mümkün olduğunca çabuk bir şekilde almaya çalışırız. Depolama hesabınızın konumuna en yakın bir veri merkezinden sevkıyatın sevk edeceğini önceliklendiriyoruz, ancak kullanılabilir envanterine sahip olan herhangi bir Azure veri merkezinden cihaz sevk edecek. Data Box, Sevkiyat etiketinde gösterildiği gibi, gönderildiği konuma döndürülmelidir.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>S. E-mürekkep üzerinde iade sevkiyat etiketi gösterilmiyor. Ne yapmalıyım?
A. E-mürekkep ekranı iade sevkiyat etiketini göstermezse aşağıdaki adımları uygulayın:
- Eski sevkiyat etiketini ve önceki sevkiyat işleminden kalan diğer tüm etiketleri sökün.
- Azure portalda sipariş sayfasına gidin. **Genel Bakış ' a** gidin ve **Sevkiyat etiketini indirin**. Daha fazla bilgi için bkz. [Sevkiyat etiketini indirme](data-box-portal-admin.md#download-shipping-label).
- Sevkiyat etiketini yazdırın ve cihaz üzerindeki şeffaf plastik cebe yerleştirin. 
- Sevkiyat etiketinin net bir şekilde göründüğünden emin olun. 

### <a name="q-how-is-my-data-protected-during-transit"></a>S. Verilerim aktarım sırasında nasıl korunur? 
A.  Aşağıdaki Data Box özellikleri taşıma sırasında verilerinizin korunmasına yardımcı olur.
 - Data Box diskleri AES 256 bit şifreleme ile şifrelenir. 
 - Cihaz kilitlenir ve verilere erişmek için bir parola kullanılması gerekir.
Daha fazla bilgi için bkz. [Data Box güvenlik özellikleri](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-for-my-import-order-and-shut-down-the-device-can-i-still-add-more-data-to-the-data-box"></a>S. İçeri aktarma siparişiniz için hazırlanma ve cihazı kapatma işlemi tamamlandı. Data Box daha fazla veri ekleyebilir miyim?
A. Evet. Cihazı açarak daha fazla veri ekleyebilirsiniz. Veri kopyalama işlemini tamamladıktan sonra **Göndermeye Hazırlama** adımlarını yeniden tamamlamanız gerekir.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>S. Cihazımı aldım ve bu cihaz önyüklenmiyor mu? Nasıl yaparım? cihaz geri başlatılsın mı?
A. Cihazınız önbaşlamazsa, Azure portal sırasıyla sıraya gidin. Bir sevkiyat etiketi indirin ve cihaza bağlayın. Daha fazla bilgi için bkz. [Sevkiyat etiketini indirme](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Doğrulama ve yükleme

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>S. Data Box geri gönderdikten sonra Azure 'daki verilerinize ne kadar yakında erişebilirim? 
A.  **Veri Kopyalama** işlemi için sipariş durumu **Tamamlandı** olarak değiştiğinde verilerinize doğrudan erişim sağlayabilirsiniz.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>S. Yükleme sonrasında verilerim Azure'da hangi konumda bulunur?
A.  Verilerin Blok Blobu veya Sayfa Blobu veya Azure dosyaları olmasına bağlı olarak Data Box verileri kopyaladığınızda, veriler Azure Depolama hesabınızdaki aşağıdaki yollardan birine yüklenir:
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  Alternatif olarak, Azure portal Azure Storage hesabınıza giderek buradan gidebilirsiniz.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>S. Kapsayıcılarım için Azure adlandırma gereksinimlerine uygun hareket etmediğimi fark ettim. Verilerim yine de Azure'a yüklenir mi?
A.  Kapsayıcı adlarında büyük harfler varsa, bu adlar otomatik olarak küçük harfe dönüştürülür. Adlar diğer kurallara (özel karakterler, diğer diller gibi) uygun değilse yükleme işlemi başarısız olur. Paylaşım, kapsayıcı ve dosyaları adlandırma hakkında daha fazla bilgi için şuraya gidin:
- [Paylaşımları adlandırma ve onlara başvurma](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blok blobları ve sayfa blobları kuralları](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>S. Data Box'a kopyaladığım verileri nasıl doğrulayabilirim?
A.  Veri kopyalama işlemi tamamlandıktan sonra **Göndermeye Hazırlama** adımlarında verileriniz doğrulanır. Data Box, doğrulama sırasında dosyaların ve veri sağlama toplamlarının bir listesini oluşturur. Dosya listesini indirebilir ve listeyi kaynak verilerdeki dosyalarla karşılaştırarak doğrulayabilirsiniz. Daha fazla bilgi için bkz. [Göndermeye hazırlama](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-return-the-data-box"></a>S. Data Box döndürmem durumunda verilerim ne olur?
A.  Veriler Azure'a kopyalandıktan sonra Data Box disklerindeki veriler NIST SP 800-88 Revision 1 yönergelerine uygun ve güvenli bir şekilde silinir. Daha fazla bilgi için bkz. [Data Box'tan verileri silme](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Denetim raporu

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Azure Data Box hizmeti müşterilere delil zinciri yordamı konusunda nasıl destek oluyor?
A.  Azure Data Box hizmeti delil zinciri belgeleri için kullanabileceğiniz raporlar sunar. Denetleme ve kopyalama günlükleri Azure'daki depolama hesabınızda mevcuttur ve siparişler tamamlandıktan sonra Azure portalda [sipariş geçmişini indirebilirsiniz](data-box-portal-admin.md#download-order-history).


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Koruma zinciri desteği için sunulan rapor türleri nelerdir?
A.  Koruma zinciri desteği için aşağıdaki rapor türleri sunulur:

- KGK 'dan taşıma lojistik.
- Cihazı açma ve kullanıcı paylaşımı erişimi günlükleri.
- 64 bitlik bir Döngüsel artıklık denetimi (CRC-64) veya Data Box başarıyla alınan her dosya için sağlama toplamı olan BOM veya manifest dosyası.
- Azure depolama hesabına yükleme işlemi başarısız olan dosyaların bildirilmesi.
- Veriler Azure depolama hesabınıza kopyalandıktan sonra Data Box cihazının temizlenmesi (NIST 800 88R1 standartlarına göre).

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Taşıyıcı izleme günlükleri (UPS 'lerden) kullanılabilir mi? 
A.  Taşıyıcı takip günlükleri, Data Box sipariş geçmişine kaydedilir. Bu rapor cihaz Azure veri merkezine iade edildikten ve cihaz üzerindeki veriler silindikten sonra sunulur. Anında gerek için, sipariş izleme numarası ile doğrudan taşıyıcının Web sitesine gidebilir ve izleme bilgilerini alabilirsiniz.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Data Box'ı Azure veri merkezine kendim götürebilir miyim? 
A.  Hayır. Microsoft yönetilen dağıtımını seçtiyseniz, verileri taşıyamazsınız. Azure veri merkezi şu anda müşterilerin veya UPS dışındaki taşıyıcılar arasından Data Box teslimini kabul etmez.

Kendi kendine yönetilen kargo ' i seçerseniz, Azure veri merkezinizden Data Box alabilir veya kapatabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

- [Data Box sistem gereksinimlerini](data-box-system-requirements.md) gözden geçirin.
- [Data Box sınırlarını](data-box-limits.md) anlayın.
- Azure portalda [Azure Data Box](data-box-quickstart-portal.md)’u hızla dağıtın.