---
title: Azure Marketi 'nde sanal makine teklifi için planlar oluşturma
description: Azure Marketi 'nde bir sanal makine teklifi için planlar oluşturmayı öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 04/01/2021
ms.openlocfilehash: b1111caf2eaab1114371cbfed1e929afcff20898
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219374"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Bir sanal makine teklifi için plan oluşturma

**Plana genel bakış** sayfasında (Iş Ortağı Merkezi ' nde sol gezinti menüsünden seçim yapın), aynı teklifin içinde çeşitli plan seçenekleri sağlayabilirsiniz. Bir teklif en az bir plan (daha önce SKU olarak adlandırılır) gerektirir; Bu, Monaya getirme seyirci kitlesi, Azure bölgesi, özellikleri veya VM görüntülerine göre farklılık gösterebilir.

Her teklif için en fazla 100 plan oluşturabilirsiniz: bunların en fazla 45 ' e kadar özel olabilir. [Microsoft ticari Market 'Teki özel tekliflerle](private-offers.md)ilgili özel planlar hakkında daha fazla bilgi edinin.

Planlarınızı oluşturduktan sonra, görüntülenecek **plana genel bakış** sekmesini seçin:

- Plan adları
- Lisans modelleri
- Hedef kitle (genel veya özel)
- Geçerli yayımlama durumu
- Kullanılabilir eylemler

**Plana genel bakış** bölmesinde bulunan eylemlere, planınızın geçerli durumuna bağlı olarak değişiklik gösterir.

- Plan durumu bir taslak ise, **taslağı Sil**' i seçin.
- Plan durumu canlı olarak yayınlanıyorsa, **satışı durdur** veya **özel izleyiciyi Eşitle** seçeneğini belirleyin.

## <a name="create-a-new-plan"></a>Yeni bir plan oluşturun

En üstte **+ Yeni plan oluştur** ' u seçin.

**Yeni plan** iletişim kutusunda, bu teklifin her bir planı için benzersiz BIR **plan kimliği** girin. Bu KIMLIK, ürün web adresinde müşterilere görünür olacaktır. Yalnızca küçük harf ve rakam, tire veya alt çizgi ve en fazla 50 karakter kullanın.

> [!NOTE]
> Plan KIMLIĞI, **Oluştur**' u seçtikten sonra değiştirilemez.

Bir **Plan adı** girin. Müşteriler teklifiniz dahilinde hangi planın seçeceğinize karar verirken bu adı görür. Planlar arasındaki farkları açıkça gösteren benzersiz bir ad oluşturun. Örneğin, *Kullandıkça Öde*, *KLG*, *Gelişmiş* ve *Kurumsal* planlar ile **Windows Server** girebilirsiniz.

**Oluştur**’u seçin. Bu, **plan kurulumu** sayfasını açar.

## <a name="plan-setup"></a>Planı ayarla

Plan türü için üst düzey yapılandırmayı ayarlayın, başka bir plandan bir teknik yapılandırmayı yeniden kullanıp kullanmayacağını belirtin ve planın kullanılabilir olması gereken Azure bölgelerini belirleyin. Burada yaptığınız seçimler, aynı plan için diğer bölmelerde hangi alanların görüntülendiğini belirlemektir.

### <a name="reuse-technical-configuration"></a>Teknik yapılandırmayı yeniden kullanma

Aynı türde birden fazla planınız varsa ve paketler aralarında aynıysa, **Bu planı başka bir plandaki teknik yapılandırmayı yeniden kullanır**' ı seçebilirsiniz. Bu seçenek, bu teklif için aynı türdeki diğer planlardan birini seçmenizi sağlar ve teknik yapılandırmasını yeniden kullanmanıza olanak tanır.

> [!NOTE]
> Teknik yapılandırmayı başka bir plandan yeniden kullandığınızda, tüm **Teknik yapılandırma** sekmesi bu plandan kaybolur. Daha sonra yaptığınız tüm güncelleştirmeler dahil olmak üzere diğer plandaki teknik yapılandırma ayrıntıları, bu plan için de kullanılacaktır. Plan yayımlandıktan sonra bu ayar değiştirilemez.

### <a name="azure-regions"></a>Azure bölgeleri

Planınız en az bir Azure bölgesinde kullanılabilir duruma getirilmelidir.

Planınızı, ticari Market tümleştirmesi olan tüm Azure küresel bölgelerindeki müşterilerin kullanımına sunmak için **Azure Global** ' i seçin. Daha fazla bilgi için bkz. [coğrafi kullanılabilirlik ve para birimi desteği](marketplace-geo-availability-currencies.md).

Planınızı [Azure Kamu](../azure-government/documentation-government-welcome.md) bölgesinde kullanılabilir hale getirmek Için **Azure Kamu** ' yı seçin. Bu bölge, ABD Federal, eyalet, yerel veya üç aylık varlıklardan müşterilere denetimli erişim sağlar ve bunların yanı sıra onlara hizmeti sunmaya uygun olan iş ortakları için de çalışır. Yayımcı olarak tüm uyumluluk denetimleri, güvenlik ölçüleri ve en iyi uygulamalardan sorumludur. Azure Kamu fiziksel olarak yalıtılmış veri merkezleri ve ağlar (yalnızca ABD 'de bulunur) kullanır.

[Azure Kamu](../azure-government/documentation-government-manage-marketplace-partners.md)'da yayımlamadan önce, belirli uç noktalar farklı olabileceğinden planınızı ortamda test edin ve doğrulayın. Planınızı ayarlamak ve test etmek için [Microsoft Azure Kamu deneme](https://azure.microsoft.com/global-infrastructure/government/request/) sayfasından bir deneme hesabı isteyin.

> [!NOTE]
> Planınız yayımlandıktan ve belirli bir Azure bölgesinde kullanılabilir olduktan sonra bu bölgeyi kaldıramazsınız.

### <a name="azure-government-certifications"></a>Azure Kamu sertifikaları

Bu seçenek yalnızca, önceki bölümde yer alan Azure bölgesi olarak **Azure Kamu** 'yu seçtiyseniz görünür.

Azure Kamu Hizmetleri, belirli kamu düzenlemelerine ve gereksinimlerine tabi olan verileri işler. Örneğin, Fedrampa, NıST 800,171 (DIB), ıTAR, ıRS 1075, DoD L4 ve CJıS. Bu programlara yönelik sertifikalarınıza yönelik olarak bir açıklama getirmek için, bunları tanımlayan 100 'e kadar bağlantı sağlayabilirsiniz. Bunlar, program üzerinde doğrudan listelemesine bağlantılar ya da kendi Web sitelerinizde uyumluluğun açıklamalarını bağlar olabilir. Bu bağlantılar yalnızca Azure Kamu müşterileri tarafından görülebilir.

Sol gezinti planı menüsündeki bir sonraki sekmeye devam etmeden önce **Taslağı kaydet** ' i seçin, **listeleri planlayın**.

## <a name="plan-listing"></a>Planı listeleme

Planın listeleme ayrıntılarını yapılandırın. Bu bölme, aynı teklifteki diğer planlardan farklı olabilen belirli bilgileri görüntüler.

### <a name="plan-name"></a>Plan adı

Bu alan, oluşturduğunuz zaman planınızı verdiğiniz adla otomatik olarak doldurulur. Bu ad, Azure Marketi 'nde bu planın başlığı olarak görünür. 100 karakterle sınırlıdır.

### <a name="plan-summary"></a>Plan Özeti

Teklifinizi değil, planınızın kısa bir özetini sağlayın. Bu Özet 100 karakterle sınırlıdır.

### <a name="plan-description"></a>Plan açıklaması

Bu yazılım planının benzersiz olduğunu ve teklifinizdeki planlar arasındaki farkları açıkla. Teklifi değil yalnızca planı açıklama. Plan açıklaması en fazla 2.000 karakter içerebilir.

Sol gezinti planı menüsündeki bir sonraki sekmeye devam etmeden önce **Taslağı kaydet** ' i seçin, **fiyatlandırma ve kullanılabilirlik**.

## <a name="pricing-and-availability"></a>Fiyatlandırma ve kullanılabilirlik

Bu bölmede şunları yapılandırırsınız:

- Bu planın kullanılabildiği pazarlar. Her plan en az bir [pazarda](marketplace-geo-availability-currencies.md)kullanılabilir olmalıdır.
- Saat başına fiyat.
- Planın herkese veya yalnızca belirli müşterilere (özel bir hedef kitle) görünür hale yapılıp yapılmayacağını belirtir.

### <a name="markets"></a>Pazar

Her plan en az bir pazarda kullanılabilir olmalıdır. Çoğu piyasa varsayılan olarak seçilidir. Listeyi düzenlemek için, **pazarları Düzenle** ' yi seçin ve bu planın satın alma için kullanılabilir olması gereken her bir pazar konumunun onay kutularını işaretleyin veya temizleyin. Seçili pazarlardaki kullanıcılar teklifi yine ["plan kurulumu"](#plan-setup) bölümünde seçilen tüm Azure bölgelerine dağıtabilir.

Yalnızca Microsoft remits Sales ve sizin adınıza vergi kullanan ülkeler/bölgeler ' i seçmek için **yalnızca Microsoft vergi havalesi** Seç ' i seçin. Çin 'de yayımlama, *ücretsiz* veya *kendi lisansını getir* (KLG) planlarla sınırlıdır.

Planınız için ABD Doları (USD) para biriminde zaten fiyatlar ayarladıysanız ve başka bir pazar konumu eklerseniz, yeni pazar fiyatı geçerli döviz kurlarına göre hesaplanır. Yayımlamadan önce her bir pazar için fiyatı her zaman gözden geçirin. Değişikliklerinizi kaydettikten sonra **fiyatları dışarı aktar (xlsx)** seçeneğini belirleyerek fiyatlandırmanızı gözden geçirin.

Bir pazarı kaldırdığınızda, bu pazardan etkin dağıtımlar kullanan müşteriler Yeni dağıtımlar oluşturamaz veya mevcut dağıtımlarını ölçeklendiremez. Mevcut dağıtımlar etkilenmez.

Devam etmek için **Kaydet** ' i seçin.

### <a name="pricing"></a>Fiyatlandırma

**Lisans modeli** için, bu plana yönelik fiyatlandırmayı yapılandırmak üzere **Kullanım tabanlı aylık faturalandırılan plan** ' ı seçin veya müşterilerin bu planı mevcut lisansıyla kullanmasına izin vermek için **kendi lisansınızı getirin** .

Kullanım tabanlı aylık faturalandırılan bir plan için aşağıdaki üç fiyat girişi seçeneğinden birini kullanın:

- **Çekirdek başına** – USD cinsinden çekirdek başına fiyatlandırma sağlar. Microsoft, çekirdek boyutu başına fiyatlandırmayı hesaplar ve geçerli döviz ücretini kullanarak yerel para birimlerine dönüştürür.
- **Çekirdek boyutu başına** – USD cinsinden çekirdek boyutuna göre fiyatlandırma sağlar. Microsoft, fiyatlandırmayı hesaplar ve geçerli döviz ücretini kullanarak yerel para birimlerine dönüştürür.
- **Pazar ve çekirdek boyutu başına** – tüm pazarlar için her bir çekirdek boyutu için fiyatlandırma sağlayın. Fiyatları bir elektronik tablodan içeri aktarabilirsiniz.

**Çekirdek başına bir fiyat** girin ve fiyat/saat hesaplamaları tablosunu görmek için **çekirdek boyutu başına fiyat** ' ı seçin.

> [!NOTE]
> Fiyatlandırma verilerinin dışa aktarılmasını sağlamak için fiyatlandırma değişikliklerini kaydedin. Planınızdaki bir pazar bedeli yayımlandıktan sonra, daha sonra değiştirilemez. Fiyatları yayımlamadan önce fiyatların doğru olduğundan emin olmak için, fiyatlandırma elektronik tablosunu dışarı aktarın ve her pazardaki fiyatları gözden geçirin.

### <a name="free-trial"></a>Ücretsiz Deneme

Müşterilerinize bir adet, üç veya altı aylık **ücretsiz deneme** olanağı sunabilirsiniz.

### <a name="plan-visibility"></a>Plan görünürlüğü

Her planı herkese görünür olacak şekilde veya yalnızca önceden seçilmiş bir kitleye tasarlayabilirsiniz. Azure abonelik kimliklerini kullanarak bu kısıtlanmış hedef kitlelere üyelik atayın.

**Ortak**: planınız herkes tarafından görülebilir.

**Özel**: planınızı yalnızca önceden seçilmiş bir kitleye görünür hale getirin. Özel bir plan olarak yayımlandıktan sonra, hedef kitleyi güncelleştirebilir veya ortak olarak değiştirebilirsiniz. Planı herkese açık hale geçirdikten sonra, genel olarak kalması gerekir. Özel bir plana geri değiştirilemez.

**Azure ABONELIK kimliği**'ni kullanarak bu özel plana erişimi olacak hedef kitleleri atayın. İsteğe bağlı olarak, atadığınız her bir Azure abonelik KIMLIĞI için bir **Açıklama** ekleyin. Bir CSV elektronik tablosu içeri aktarıyorsanız, el ile en fazla 10 abonelik kimliği ekleyin veya 20.000 'e kadar. Azure abonelik kimlikleri GUID olarak temsil edilir ve tüm harflerin küçük harf olması gerekir.

> [!NOTE]
> Özel veya kısıtlanmış bir hedef kitle, **Önizleme** bölmesinde tanımladığınız önizleme izleyicilerinizden farklıdır. Bir önizleme hedef kitlesi, Azure Market 'Te canlı olarak yayımlanmadan *önce* teklifinizin erişimine açabilir. Özel hedef kitle seçimi yalnızca belirli bir plana uygulansa da, önizleme hedef kitlesi tüm özel ve genel planları doğrulama amaçlarıyla görüntüleyebilir.

Özel teklifler, bulut çözümü sağlayıcısı programı 'nın (CSP) satıcısı aracılığıyla oluşturulan Azure abonelikleri ile desteklenmez.

### <a name="hide-plan"></a>Planı gizle

Sanal makineniz, başka bir çözüm şablonu veya yönetilen uygulama aracılığıyla başvurulduğu zaman dolaylı olarak yalnızca dolaylı olarak kullanılacaksa, sanal makineyi yayımlamak için bu onay kutusunu işaretleyin, ancak doğrudan arama veya tarama yapmakta olabilecek müşterilerden gizleyin.

Tüm Azure müşterileri, PowerShell veya CLı kullanarak teklifi dağıtabilir.  Bu teklifi sınırlı bir müşteri kümesi için kullanılabilir hale getirmek isterseniz planı **özel** olarak ayarlayın. 

Gizli planlar önizleme bağlantıları oluşturmaz. Ancak, [Bu adımları izleyerek](azure-vm-create-faq.md#how-do-i-test-a-hidden-preview-image)size test edebilirsiniz. 

Sol gezinti planı menüsündeki **Teknik yapılandırma**' da bir sonraki sekmeye geçmeden önce **Taslağı kaydet** ' i seçin.

## <a name="technical-configuration"></a>Teknik yapılandırma

Bu planla ilişkili görüntüleri ve diğer teknik özellikleri sağlayın.

> [!NOTE]
> Bu plan, bu planı, paketi **plan kurulumu** sekmesinden başka bir şekilde yeniden kullanmak üzere yapılandırdıysanız görüntülenmez.

### <a name="operating-system"></a>İşletim sistemi

**Windows** veya **Linux** işletim sistemi ailesini seçin.

Windows **sürümü** veya Linux **satıcıyı** seçin.

İşletim sistemi için bir **işletim sistemi kolay adı** girin. Bu ad, müşteriler tarafından görülebilir.

### <a name="recommended-vm-sizes"></a>Önerilen VM boyutları

Azure Marketi 'nde görüntülenmek üzere en fazla altı önerilen sanal makine boyutu seçmek için bağlantıyı seçin.

### <a name="open-ports"></a>Bağlantı noktalarını açma

Dağıtılmış bir sanal makineye açık ortak veya özel bağlantı noktaları ekleyin.

### <a name="properties"></a>Özellikler

SANAL makinenizin **hızlandırılmış ağı destekleyip desteklemediğini** seçin. Ayrıntılar için bkz. [hızlandırılmış ağ](https://go.microsoft.com/fwlink/?linkid=2124513).

### <a name="generations"></a>İse

Bir sanal makine oluşturmak, kullandığı sanal donanımı tanımlar. Müşterinizin ihtiyaçlarına göre, 1. nesil bir VM, 2. nesil VM veya her ikisini de yayımlayabilirsiniz.

1. Yeni bir teklif oluştururken, bir **oluşturma türü** seçin ve istenen ayrıntıları girin:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-1.png" alt-text="Iş Ortağı Merkezi 'nin oluşturma ayrıntısı bölümünün bir görünümü.":::

2. Bir plana başka nesil bir oluşturma eklemek için **oluşturma oluştur**... seçeneğini belirleyin.

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="' Oluşturma ekleme ' bağlantısının bir görünümü.":::

    ... ve istenen ayrıntıları girin:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-3.png" alt-text="Nesil ayrıntıları penceresinin görünümü.":::

<!--    The **Generation ID** you choose will be visible to customers in places such as product URLs and ARM templates (if applicable). Use only lowercase, alphanumeric characters, dashes, or underscores; it cannot be modified once published.
-->
3. 1. nesil zaten yayınlanmış var olan bir VM 'yi güncelleştirmek için **Teknik yapılandırma** sayfasında ayrıntıları düzenleyin.

1. nesil ve 2. nesil yetenekler arasındaki farklar hakkında daha fazla bilgi edinmek için bkz. [Azure 'da 2. nesil sanal makineler Için destek](../virtual-machines/generation-2.md).

### <a name="vm-images"></a>VM görüntüleri

Sanal makine görüntüleri için bir disk sürümü ve paylaşılan erişim imzası (SAS) URI 'SI sağlayın. Her VM görüntüsü için en fazla 16 veri diski ekleyin. Belirtilen bir gönderideki plan başına yalnızca bir yeni görüntü sürümü sağlayın. Bir görüntü yayımlandıktan sonra düzenleyemezsiniz, ancak silebilirsiniz. Bir sürümü silmek, hem yeni hem de mevcut kullanıcıların silinen sürümün yeni bir örneğini dağıtmalarını engeller.

Bu iki zorunlu alan, yukarıdaki önceki görüntüde gösterilmektedir:

- **Disk sürümü**: sağlaettiğiniz görüntünün sürümü.
- **OS VHD bağlantısı**: işletim sistemi VHD 'Si için Azure Depolama hesabınızdaki konum. SAS URI 'sini alma hakkında bilgi edinmek için bkz. [VM Görüntünüz için paylaşılan erişim imzası URI 'Si alma](azure-vm-get-sas-uri.md).

Veri diskleri ( **veri diski Ekle (en fazla 16)** seçin) Ayrıca, Azure depolama HESAPLARıNDA depolanan VHD paylaşılan erişim imza URI 'larıdır. Bir plana gönderim başına yalnızca bir resim ekleyin.

Kullandığınız işletim sisteminden bağımsız olarak, yalnızca çözüm için gereken en az sayıda veri diski ekleyin. Dağıtım sırasında, müşteriler bir görüntünün parçası olan diskleri kaldıramaz, ancak dağıtım sırasında veya sonrasında her zaman disk ekleyebilirler.

**Taslağı kaydet**' i seçin, sonra yeni oluşturduğunuz planı görmek için sol üstteki **← plana genel bakış** ' ı seçin.

VM Görüntünüz yayımlandıktan sonra, görüntüyü Azure depolamadan silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [CSP’ler aracılığıyla satış](azure-vm-create-resell-csp.md)
