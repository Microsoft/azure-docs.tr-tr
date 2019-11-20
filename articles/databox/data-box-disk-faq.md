---
title: Microsoft Azure Data Box Disk Hakkında SSS | Microsoft Docs
description: Büyük miktarlardaki verileri Azure 'a aktarmanızı sağlayan bir bulut çözümü olan Azure Data Box Disk için sık sorulan sorular ve yanıtlar içerir
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: eee33a2e1f5d081c1394368152d0d9afb7091e34
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098817"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk: Sıkça Sorulan Sorular

Microsoft Azure Data Box Disk bulut istasyonu çözümü, terabaytlarca veriyi Azure'a hızlı, uygun maliyetli ve güvenilir bir şekilde göndermenizi sağlar. Bu SSS belgesinde Azure portaldaki Data Box Disklerini kullanımınızla ilgili sorulara ve yanıtlarına yer verilmiştir. 

Sorular ve yanıtlar aşağıdaki kategorilere ayrılmıştır:

- Hizmet hakkında
- Yapılandırma ve bağlanma 
- Durumu izleme
- Geçiş verileri 
- Verileri doğrulama ve yükleme 


## <a name="about-the-service"></a>Hizmet hakkında

### <a name="q-what-is-azure-data-box-service"></a>S. Azure Data Box hizmeti nedir? 
A.  Azure Data Box hizmeti, çevrimdışı veri alımı için tasarlanmıştır. Bu hizmet farklı depolama kapasitelerinde veri aktarımı gerçekleştirmek için tasarlanmış olan bir dizi ürünü yönetir. 

### <a name="q-what-are-azure-data-box-disks"></a>S. Azure Data Box Diskleri nedir?
A. Azure Data Box Diskleri terabaytlarca veriyi Azure'da çift yönlü olarak hızlı, uygun maliyetli ve güvenli bir şekilde aktarmanızı sağlar. Microsoft size toplamda maksimum 35 TB depolama kapasitesi sunan 1 ile 5 arasında disk gönderir. Bu diskleri Azure portalda Data Box hizmetiyle kolayca yapılandırabilir, bağlayabilir ve disklerin kilidini açabilirsiniz.  

Diskler Microsoft BitLocker sürücü şifrelemesi ile şifrelenir ve şifreleme anahtarlarınız Azure portalda yönetilir. Ardından müşterinin sunucularındaki verileri kopyalarsınız. Microsoft verilerinizi veri merkezinde hızlı ve özel bir ağ yükleme bağlantısı aracılığıyla sürücüden buluta geçirir ve Azure'a yükler.

### <a name="q-when-should-i-use-data-box-disks"></a>S. Data Box Disklerini ne zaman kullanmalıyım?
A. Azure'a aktarmak istediğiniz 40 TB (veya daha az) boyutunda veriniz varsa Data Box Disklerinden faydalanabilirsiniz.

### <a name="q-what-is-the-price-of-data-box-disks"></a>S. Data Box Disklerinin maliyeti nedir?
A. Data Box disklerin fiyatı hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/databox/disk/)gidin.

### <a name="q-how-do-i-get-data-box-disks"></a>S. Data Box Disklerini nasıl edinebilirim? 
A.  Azure Data Box diskleri almak için Azure portal oturum açın ve diskler için bir Data Box sırası oluşturun. İletişim bilgilerinizi ve bildirim ayrıntılarını girin. Sipariş verdikten sonra kullanılabilirlik durumuna göre diskler 10 gün içinde gönderilir.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>S. Data Box Disklerine tek seferde en fazla ne kadar veri aktarabilirim?
A. Her biri 8 TB boyutunda (7 TB kullanılabilir kapasite) 5 disk için kullanabileceğiniz maksimum kapasite 35 TB olacaktır. Dolayısıyla tek seferde en fazla 35 TB veri aktarabilirsiniz. Daha fazla veri aktarmak için daha fazla disk sipariş etmeniz gerekir.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>S. Data Box Disklerinin bulunduğum bölgede kullanılabilir durumda olup olmadığını nasıl kontrol edebilirim? 
A.  Data Box disklerinin Şu anda kullanılabilir olduğunu görmek için [bölgenin kullanılabilirliğine](data-box-disk-overview.md#region-availability)gidin.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>S. Data Box Diskleri ile hangi bölgelerde veri depolayabilirim?
A. Data Box Disk ABD, Kanada, Avustralya, Batı Avrupa ve Kuzey Avrupa, Kore ve Japonya 'daki tüm bölgeler için desteklenir. Yalnızca Azure genel bulut bölgeleri desteklenir. Azure Kamu veya diğer bağımsız bulutlar desteklenmez.

### <a name="q-will-my-data-box-disk-cross-country-borders-during-shipping"></a>S. Data Box Disk nakliye sırasında çapraz ülke kenarlıklarım olacak mı?
A. Data Box Disk, hedefle aynı ülke içinden gönderilir ve uluslararası kenarlıkları geçmez. Tek istisna, Avrupa Birliği (AB) ' deki, disklerin herhangi bir AB ülkesine ve bu ülkeye nereden gönderdiklerinin bulunduğu emirlerdir.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>S. Data Box Diskleriyle ilgili sorun yaşamam halinde kiminle iletişim kurmam gerekir?
A. Data Box diskleriyle ilgili herhangi bir sorunla karşılaşırsanız lütfen [Microsoft desteği başvurun](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support).

## <a name="configure-and-connect"></a>Yapılandırma ve bağlanma
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>S. Siparişteki Data Box Diski sayısını seçebilir miyim?
A.  Hayır. Verilerinizin boyutuna ve disklerin kullanılabilirliğine göre 8 TB boyutunda diskler (en fazla 5 disk) gönderilir.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>S. Data Box Disklerinin kilidini nasıl açabilirim? 
A.  Azure portalda Data Box Disk siparişinize ve **Cihaz ayrıntıları**'na gidin. Geçiş anahtarını kopyalayın. İşletim sisteminiz için Azure portaldan Data Box kilidi açma aracını indirip açın. Aracı, disklere kopyalamak istediğiniz verileri içeren bilgisayarda çalıştırın. Disklerin kilidini açmak için geçiş anahtarını girin. Aynı geçiş anahtarı tüm disklerin kilidini açar. 

Yönerge adımları için [Windows istemcisinde disk kilidi açma](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client)'ya veya [Linux istemcisinde disk kilidi açma](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)'ya gidin.

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>S. Data Box Disklerine bağlanmak ve veri kopyalamak için Linux ana bilgisayarı kullanabilir miyim?
A.  Evet. Bağlanmak ve Data Box Disklerine veri kopyalamak için gerek Linux, gerekse Windows istemcisi kullanılabilir. Daha fazla bilgi için ana bilgisayarınızın [Desteklenen işletim sistemleri](data-box-disk-system-requirements.md) listesine gidin.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>S. Disklerim yola çıktı ancak şimdi siparişimi iptal etmek istiyorum. Neden iptal düğmesi kullanılamıyor?
A.  Siparişi yalnızca disk siparişi verdikten sonra ancak diskler gönderilmeden önce iptal edebilirsiniz. Diskler yola çıktıktan sonra siparişi iptal edemezsiniz. Ancak, disklerinizi bir ücretden döndürebilirsiniz. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>S. Veri aktarımı için aynı anda birden fazla Data Box Diskini ana bilgisayara bağlayabilir miyim?
A. Evet. Evet, birden fazla Data Box Diski aynı ana bilgisayara bağlanarak aynı anda birden fazla veri aktarım ve kopyalama işi çalıştırılabilir.

## <a name="track-status"></a>Durumu izleme

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>S. Diskleri geri göndermek için sipariş oluşturduktan sonra diskleri nasıl takip edebilirim? 
A.  Data Box Disk siparişinin durumunu Azure portaldan takip edebilirsiniz. Siparişi oluşturduğunuzda bildirimlerin gönderileceği bir e-posta adresi girmeniz de istenir. E-posta adresi girdiyseniz sipariş durumundaki tüm değişiklikler bu adrese gönderilir. [Bilgilendirme e-postalarını yapılandırma](data-box-portal-ui-admin.md#edit-notification-details) hakkında daha fazla bilgi edinin.

### <a name="q-how-do-i-return-the-disks"></a>S. Diskleri nasıl iade edebilirim? 
A.  Microsoft tarafından gönderilen Data Box Diski paketinde iade için kullanabileceğiniz bir sevkiyat etiketi bulunur. Etiketi kutunun üzerine yapıştırın ve kapattığınız paketi sevkiyat şirketine teslim edin. Etiketin hasar görmesi veya kaybolması durumunda **Genel bakış > Sevkiyat etiketini indir** sayfasına gidin ve yeni bir sevkiyat etiketi indirin.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>Data Box Disk sıraımı kendi kendinize alabilir miyim? Diskleri seçdiğim bir taşıyıcı aracılığıyla döndürebilir miyim?
A. Evet. Microsoft ayrıca yalnızca US Gov bölgede otomatik olarak yönetilen kargo da sunar. Data Box Disk sırayı yerleştirirken, kendi kendine yönetilen teslim seçeneğini belirleyebilirsiniz. Data Box Disk siparişinizi almak için aşağıdaki adımları uygulayın:
    
1. Siparişi yerleştirdikten sonra, sipariş işlenir ve diskler hazırlanır. Siparişinizin çekme için hazırlandığı bir e-posta ile bildirim alırsınız. 
2. Sipariş toplama için hazırsa, Azure portal sırasıyla sıraya gidin ve **genel bakış** dikey penceresine gidin. 
3. Azure portal kod içeren bir bildirim göreceksiniz. [Azure Data Box işlemler ekibine](mailto:adbops@microsoft.com) e-posta gönderin ve kodu sağlayın. Takım, konumu sağlayacak ve bir toplama tarihi ve saati zamanlamanıza izin verir. E-posta bildirimini aldıktan sonra ekibi 5 iş günü içinde çağırmanız gerekir.

Veri kopyalama ve doğrulama işlemi tamamlandıktan sonra, diskinizi döndürmek için aşağıdaki adımları uygulayın:

1. Veri doğrulama tamamlandıktan sonra diskleri çıkarın. Bağlantı kablolarını çıkarın.
2. Tüm diskleri ve bağlantı kablolarını kabarcıklı naylona sarın ve bunları sevkiyat kutusuna yerleştirin. Aksesuarlar eksikse ücret uygulanabilir.

    - Ürün size ilk ulaştığında gönderilen paketi kullanın. Disklerinizi, hava kabarcıklı ambalaj malzemesiyle sıkıca sararak paketlemenizi öneririz.
    - Diskin kutunun içinde hareket etmesini engellemek için kutuda boşluk kalmadığından emin olun.
3. Azure portal siparişiniz için **genel bakış dikey penceresine** gidin. Kod içeren bir bildirim görmeniz gerekir.
4. Bu kodu kullanın ve [Azure Data Box işlemler ekibine](mailto:adbops@microsoft.com) e-posta gönderin ve kodu sağlayın. Bunlar, disklerin nerede ve ne zaman kapatılabileceği hakkında bilgi verir.


## <a name="migrate-data"></a>Verileri geçirin

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>S. Data Box Diskleri ile kullanılabilecek maksimum veri boyutu nedir?  
A.  Data Box Diskleri çözümünde toplam 35 GB kapasiteye sahip en fazla 5 disk kullanılabilir. Her bir disk 8 TB (7 TB kullanılabilir) boyutundadır.

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>S. Data Box Diskleri tarafından desteklenen maksimum blok blobu ve sayfa blobu boyutu nedir? 
A.  Maksimum boyutlar Azure Depolama sınırları ile belirlenir. Maksimum blok blobu yaklaşık 4,768 TiB, maksimum sayfa blobu boyutu ise 8 TiB olarak belirlenmiştir. Daha fazla bilgi için bkz. [Azure Depolama Ölçeklenebilirlik ve Performans Hedefleri](../storage/common/storage-scalability-targets.md).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>S. Data Box Disklerinin veri aktarım hızı nedir?
A. USB 3.0 bağlantısıyla yapılan testlerde disk performansının 430 MB/sn seviyesine çıkabildiği görülmüştür. Gerçek performans kullanılan dosya boyutuna göre değişiklik gösterecektir. Daha küçük dosyalarda performans daha düşük olabilir.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>S. Verilerimin aktarım sırasında güvende olduğundan nasıl emin olabilirim? 
A.  Data Box Diskleri BitLocker AES-128 bit şifreleme kullanılarak şifrelenir ve geçiş anahtarına yalnızca Azure portaldan erişilebilir. Geçiş anahtarını almak için hesap kimlik bilgilerinizi kullanarak Azure portalda oturum açın. Data Box Disk kilit açma aracını çalıştırdığınızda bu geçiş anahtarını kullanın.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>S. Data Box Disklerine nasıl veri kopyalayabilirim? 
A.  Robocopy, Diskboss veya Windows Dosya Gezgini gibi bir SMB kopyalama aracı kullanarak verileri sürükleyip diske bırakın.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>S. Veri kopyalama işlemini hızlandırmaya yönelik ipuçları var mı?
A.  Kopyalama işlemini hızlandırmak için:

- Birden fazla veri kopyalama akışı kullanın. Örneğin Robocopy'de çok iş parçacıklı seçeneği kullanın. Kullanılan tam komut hakkında daha fazla bilgi için [Öğreticiye gidin: Verileri Azure Data Box Disk'e kopyalama ve doğrulama](data-box-disk-deploy-copy-data.md#copy-data-to-disks) kaynağını tamamladığınızdan emin olun.
- Birden fazla oturum kullanın.
- Ağ paylaşımı üzerinden kopyalama yapmak yerine (ağ hızları kısıtlayıcı olabilir) verilerin, disklerin bağlı olduğu bilgisayarın yerel depolama alanında bulunduğundan emin olun.
- Kopyalama işlemi boyunca USB 3.0 veya üzeri bağlantı kullandığınızdan emin olun. Bilgisayara bağlı USB denetleyicilerini ve USB cihazlarını tanımlamak için [USBView aracını](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview) indirin ve kullanın.
- Veri kopyalamak için kullanılan bilgisayarın performansını karşılaştırın. Sunucu donanımının performansını karşılaştırmak için [Bluestop FIO aracını](https://ci.appveyor.com/project/axboe/fio) indirin ve kullanın. En son x86 veya x64 yapısını seçin, **yapılar** sekmesini SEÇIN ve MSI 'yi indirin.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>S. Kaynak veriler küçük dosyalardan (KB veya birkaç MB) oluşuyorsa veri aktarımını nasıl hızlandırabilirim?
A.  Kopyalama işlemini hızlandırmak için:

- Hızlı depolama alanında yerel bir VHDx oluşturun veya HDD/SSD üzerinde boş bir VHD oluşturun (daha yavaştır).
- Bunu bir sanal makineye takın.
- Dosyaları sanal makinenin diskine kopyalayın.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>S. Data Box Diskleriyle birden fazla depolama hesabı kullanabilir miyim?
A.  Hayır. Şu an için Data Box Diskleri ile yalnızca tek bir depolama hesabı (genel veya klasik) kullanılabilir. Hem sık hem de seyrek erişimli bloblar desteklenir. Şu anda yalnızca, Azure genel bulutundaki ABD, Batı Avrupa ve Kuzey Avrupa depolama hesapları desteklenmektedir.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>S. Data Box diskleriyle verilerim için araç seti nedir?
A. Data Box Disk bulunan araç takımı üç araç içerir:
 - **Data Box disk kilit açma aracı**: Microsoft 'tan gelen şifrelenmiş disklerin kilidini açmak için bu aracı kullanın. Aracı kullanarak disklerin kilidini açarken, Azure portal Data Box Disk sırada kullanılabilir bir geçiş anahtarı sağlamanız gerekir. 
 - **Data Box disk doğrulama aracı**: Azure adlandırma kurallarına göre boyut, biçim ve BLOB adlarını doğrulamak için bu aracı kullanın. Ayrıca, Azure 'a yüklenen verileri doğrulamak için kullanılan kopyalanmış veriler için sağlama toplamı oluşturur.
 - **Bölünmüş kopyalama aracı Data Box disk**: Birden çok disk kullanırken ve tüm disklerde bölünmesi ve kopyalanması gereken büyük bir veri kümesine sahip olduğunuzda bu aracı kullanın. Bu araç şu anda Windows için kullanılabilir. Bu araç yönetilen disklerle desteklenmez. Bu araç Ayrıca verileri kopyalarken doğrular, bu nedenle bu aracı kullanırken doğrulama adımını atlayabilirsiniz.

Araç takımı hem Windows hem de Linux için kullanılabilir. Araç takımını buradan indirebilirsiniz:
- [Windows için Data Box Disk araç takımını indirin](https://aka.ms/databoxdisktoolswin) 
- [Linux için Data Box Disk araç takımını indirin](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>S. Data Box Disk kullanarak Azure dosyalarına veri aktarabilir ve sonra verileri Azure Dosya Eşitleme kullanabilir miyim? 
A. Azure dosyaları Data Box Disk desteklenir, ancak Azure Dosya Eşitleme ile birlikte çalışmaz. Dosya verileri Azure Dosya Eşitleme ile kullanılıyorsa meta veriler korunmaz.


## <a name="verify-and-upload"></a>Doğrulama ve yükleme

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>S. Diskleri geri gönderdikten sonra verilerime Azure'da nasıl erişebilirim? 
A.  Veri Kopyalama işlemi için sipariş durumu tamamlandı olarak değiştiğinde verilerinize doğrudan erişim sağlayabilirsiniz.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>S. Yükleme sonrasında verilerim Azure'da hangi konumda bulunur?
A.  Verileri diskinizdeki *BlockBlob* ve *PageBlob* klasörlerinin altına kopyaladığınızda Azure depolama hesabında *BlockBlob* ve *PageBlob* klasörünün her alt klasörü için bir kapsayıcı oluşturulur. Dosyaları doğrudan *BlockBlob* ve *PageBlob* klasörlerine kopyaladıysanız Azure Depolama hesabında *$root* adlı varsayılan kapsayıcıya yerleştirilir. Verileri *AzureFile* klasörü altındaki bir klasöre kopyaladığınızda, bir FileShare oluşturulur.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>S. Kapsayıcılarım için Azure adlandırma gereksinimlerine uygun hareket etmediğimi fark ettim. Verilerim yine de Azure'a yüklenir mi?
A. Kapsayıcı adlarında büyük harf kullandıysanız bunlar otomatik olarak küçük harfe dönüştürülür. Adlar diğer kurallara (özel karakterler, diğer diller gibi) uygun değilse yükleme işlemi başarısız olur. Daha fazla bilgi için [Azure adlandırma kurallarına](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) gidin.

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>S. Birden fazla Data Box Diskine kopyaladığım verileri nasıl doğrulayabilirim?
A.  Veri kopyalama işlemi tamamlandıktan sonra *DataBoxDiskImport* klasöründe bulunan `DataBoxDiskValidation.cmd` uygulamasını çalıştırarak doğrulama için sağlama toplamları alabilirsiniz. Birden fazla diskiniz varsa her disk için bir komut penceresi açarak bu komutu çalıştırmanız gerekir. Bu işlemin verilerinizin boyutuna göre uzun sürebileceğini (birkaç saat) unutmayın.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>S. Diskleri iade ettiğimde verilerime ne olur?
A.  Veriler Azure'a kopyalandıktan sonra disklerdeki veriler NIST SP 800-88 Revision 1 yönergelerine uygun ve güvenli bir şekilde silinir.  

### <a name="q-how-is-my-data-protected-during-transit"></a>S. Verilerim aktarım sırasında nasıl korunur? 
A.  Data Box Diskleri AES-128 Microsoft BitLocker şifrelemesi ile korunur. Tüm disklerin kilidini açmak ve verilere erişmek için tek bir geçiş anahtarı kullanılması gerekir.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>S. Data Box Disklerine daha fazla veri eklersem sağlama toplamı doğrulama işlemini tekrar çalıştırmam gerekir mi?
A. Evet. Verilerinizi doğrulamaya karar verirseniz (bunu yapmanızı öneririz!) disklere daha fazla veri eklemeniz durumunda doğrulama işlemini tekrarlamanız gerekir.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>S. Veri aktarımı için tüm diskleri kullandım ve daha fazla disk sipariş etmem gerekiyor. Siparişi hızlı bir şekilde vermek için kullanabileceğim bir yöntem var mı?
A. Önceki siparişinizi kopyalayabilirsiniz. Kopyalama işlemi, bir öncekiyle aynı bilgilere sahip bir sipariş oluşturur ve adres, iletişim ve bildirim ayrıntılarını yeniden girmenize gerek kalmadan yalnızca sipariş bilgilerini düzenleyebilirsiniz.

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>S. Verileri ManagedDisk klasörüne kopyaladım. Yönetilen diskler için belirtilen kaynak grubuyla hiçbir yönetilen disk görmüyorum. Verilerim Azure 'a yüklendi ve verileri nasıl bulabilirim?
A. Evet. Verileriniz Azure 'a yüklendi ancak belirtilen kaynak gruplarıyla yönetilen disk görmüyorsanız, bunun nedeni verilerin geçerli olmaması olabilir. Sayfa Blobları, blok Blobları, Azure dosyaları ve yönetilen diskler geçerli değilse, bunlar aşağıdaki klasörlere gider:
 - Sayfa Blobları, *databoxdisk-geçersiz-PB-* ile başlayan bir Blok Blobu kapsayıcısına gider.
 - Azure dosyaları *databoxdisk-geçersiz-AF-* ile başlayan bir Blok Blobu kapsayıcısına gider.
 - Yönetilen diskler databoxdisk ile başlayan bir Blok Blobu kapsayıcısına gidebilir *-geçersiz-MD-* .

## <a name="next-steps"></a>Sonraki adımlar

- [Data Box disk sistem gereksinimlerini](data-box-disk-system-requirements.md)gözden geçirin.
- [Data Box Disk sınırlarını](data-box-disk-limits.md) anlayın.
- Azure portalında [Azure Data Box Disk](data-box-disk-quickstart-portal.md)’i hızlı dağıtın.
