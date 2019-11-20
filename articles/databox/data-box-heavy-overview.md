---
title: Microsoft Azure Data Box Heavy'ye genel bakış | Verilerde Microsoft Docs
description: Muazzam miktarlarda verinin Azure’a aktarılmasını sağlayan bir hibrit çözüm olan Azure Data Box açıklanır
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 08/28/2019
ms.author: alkohli
ms.openlocfilehash: 362b7457538a16d389c3cc40fc44da19b073c0b0
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142909"
---
# <a name="what-is-azure-data-box-heavy"></a>Azure Data Box Heavy nedir?

Azure Data Box Heavy, Azure 'a hızlı, ucuz ve güvenilir bir şekilde yüzlerce terabayt veri göndermenize olanak tanır. Veriler, verilerinize göre doldurduğunuz ve Microsoft 'a geri göndereceğiniz 1 PB depolama kapasitesine sahip Data Box Heavy bir cihaz göndererek Azure 'a aktarılır. Cihazda, aktarım sırasında verilerinizi korumak ve güvenliğini sağlamak için bir Rugged büyük/küçük harfe sahip olabilirsiniz.

Cihaz, veri merkezinizde alındıktan sonra yerel Web Kullanıcı arabirimini kullanarak ayarlayın. Sunucularınızdaki verileri cihaza kopyalayın ve cihazı Azure'a geri gönderin. Azure veri merkezinde Verileriniz Azure depolama hesabınıza yüklenir. Azure portal uçtan uca işlemin tamamını izleyebilirsiniz.


> [!IMPORTANT]
> - Bir cihaz istemek için [Azure Portal](https://portal.azure.com)kaydolun.


## <a name="use-cases"></a>Uygulama alanları

Data Box Heavy yüzlerce terabayt 'daki veri boyutları için en uygun seçenektir; burada ağ bağlantısı, verileri Azure 'a yüklemek için yeterli değildir. Veri taşıma işlemi tek seferlik, düzenli veya başta toplu veri aktarımı ve ardından düzenli aktarımlardan oluşabilir. Burada Data Box Heavy'nin veri aktarımı için kullanılabileceği çeşitli senaryoları bulabilirsiniz.

 - **Tek seferlik geçiş**: Büyük miktardaki şirket içi verilerinin Azure'a taşınmasıdır.
     - Çevrimiçi bir medya kitaplığı oluşturmak için çevrimdışı bantlardan bir medya kitaplığını Azure 'a taşıyın.
     - VM grubunuzu, SQL Server 'ı ve uygulamalarınızı Azure 'a geçirin.
     - HDInsight 'ı kullanarak derinlemesine bir analiz ve rapor için geçmiş verileri Azure 'a taşıyın.

 - **İlk toplu aktarım**: Data Box Heavy (seed) ile toplu veri aktarımı yapıldıktan sonra ağ üzerinden artımlı aktarım işlemlerinin gerçekleştirilmesidir.
     - Örneğin, Data Box Heavy ve bir yedekleme çözümleri iş ortağı ilk büyük geçmişteki yedeklemeyi Azure 'a taşımak için kullanılır. İşlem tamamlandıktan sonra, artımlı veriler ağ üzerinden Azure depolamasına aktarılır.

 - **Düzenli yüklemeler**: Düzenli olarak oluşturulan büyük miktarlardaki verilerin Azure'a taşınması gerektiği durumlardır. Enerji keşfinde petrol kuyularında ve rüzgar enerjisini santrallerinde oluşturulan videolar örnek olarak verilebilir.

## <a name="benefits"></a>Avantajlar

Data Box Heavy, ağınızda çok büyük miktarlarda verileri Azure 'a taşımak için tasarlanmıştır. Çözümün şöyle avantajları vardır:

- **Hız** -Data Box Heavy yüksek performanslı 40-Gbps ağ arabirimlerini kullanır.

- **Güvenlik** -Data Box Heavy cihaz, veri ve hizmet için yerleşik güvenlik korumalarının bulunur.
    - Cihaz, üzerinde oynanmaya karşı korumalı vidalar ve üzerinde oynandığını belli eden çıkartmalarla korunan, sağlamlaştırılmış bir cihaz kasasına sahiptir.
    - Cihazdaki veriler her zaman AES 256 bit ile şifrelenir.
    - Cihaz kilidi yalnızca Azure portalından alınan parolayla açılabilir.
    - Hizmet, Azure güvenlik özelliklerinin koruması altındadır.
    - Verileriniz Azure 'a yüklendikten sonra, ulusal standartlar ve Teknoloji Enstitüsü (NıST) 800-88r1 standartlarına uygun olarak cihazdaki diskler temiz temizlenir.


## <a name="features-and-specifications"></a>Özellikler ve belirtimler

Data Box Heavy cihaz bu sürümde aşağıdaki özelliklere sahiptir.

| Belirtimler                                          | Açıklama              |
|---------------------------------------------------------|--------------------------|
| Ağırlık                                                  | ~ 500 lbs. <br>Taşıma için tekerlek kilitleme üzerindeki cihaz|
| Boyutlar                                              | Genişlik: 26 inç Yükseklik: 28 inç uzunluk: 48 inç |
| Raf alanı                                              | Rafa bağlı olamaz|
| Gerekli kablolar                                         | 4 topraklanmış 120 V/10 güç kablosu (NEMA 5-15) dahil <br> Cihaz en fazla 240 V gücünü destekler ve C-13 güç Alım ptacles 'e sahiptir <br> [MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) ile uyumlu ağ kablolarını kullanma  |
| Açılma                                                    | 4 cihaz düğümleri genelinde paylaşılan 4 yerleşik güç kaynağı birimi (PSUs) <br> 1\.200 watt tipik güç çiz|
| Depolama kapasitesi                                        | ~ 1-PB RAW, 70 her biri 14 TB disk <br> 770-TB kullanılabilir kapasite|
| Düğüm sayısı                                          | cihaz başına 2 bağımsız düğüm (her biri 500 TB) |
| Düğüm başına ağ arabirimleri                             | düğüm başına 4 ağ arabirimi <br><br> MGMT, DATA3 <ul><li> 2 X 1-GbE arabirimler </li><li> MGMT yönetim ve ilk kurulum içindir, Kullanıcı yapılandırılamaz </li><li> DATA3, varsayılan olarak Kullanıcı tarafından yapılandırılabilir ve dinamik ana bilgisayar Yapılandırma Protokolü (DHCP)</li></ul>VERI1, VERI2 veri arabirimleri <ul><li>2 X 40-GbE arabirimler </li><li> DHCP (varsayılan) veya statik için yapılandırılabilir Kullanıcı</li></ul>|


## <a name="components"></a>Bileşenler

Data Box Heavy aşağıdaki bileşenleri içerir:

* **Data Box Heavy cihazı**: Verileri güvenle depolayan, dış yüzeyi sağlamlaştırılmış bir fiziksel cihaz. Bu cihazda kullanılabilir 770 TB depolama kapasitesi vardır.
    
* **Data Box hizmeti**: Azure portalının, farklı coğrafi konumlardan erişebildiğiniz bir web arabiriminde Data Box Heavy cihazını yönetmenize olanak tanıyan bir uzantısı. Data Box Heavy cihazınızı yönetmek için Data Box hizmetini kullanın. Hizmetin görevleri, siparişleri oluşturma ve yönetmeyi, uyarıları görüntülemeyi ve yönetmeyi, paylaşımları yönetmeyi içerir.  

* **Yerel web kullanıcı arabirimi**: Yerel ağa bağlanabilmesi için cihazı yapılandırmak ve ardından Data Box hizmetine kaydetmek için kullanılan, web tabanlı kullanıcı arabirimi. Yerel web kullanıcı arabirimini cihazı kapatmak ve yeniden başlatmak, kopya günlüklerini görüntülemek ve hizmet isteğinde bulunmak üzere Microsoft Desteği'ne başvurmak için de kullanın.


## <a name="the-workflow"></a>İş akışı

Tipik iş akışı aşağıdaki adımlardan oluşur:

1. **Sipariş**: Azure portaldan bir sipariş oluşturup gönderim bilgilerini ve verileriniz için hedef Azure depolama hesabını belirtin. Cihaz varsa, Azure cihazı hazırlar, gönderir ve bir gönderi takip numarası iletir.

2. **Alma**: Cihaz teslim edildiğinde, belirtilen kabloları kullanarak cihazı ağa ve güç kaynağına kabloyla bağlayın. Cihazı açın ve bağlayın. Cihaz ağını yapılandırın ve verileri kopyalamak istediğiniz konak bilgisayarına paylaşımları takın.

3. **Veri kopyalama**: Verileri Data Box Heavy paylaşımlarına kopyalayın.

4. **İade etme**: Cihazı hazırlayın, kapatın ve Azure veri merkezine geri gönderin.

5. **Karşıya yükleme**: Veriler otomatik olarak cihazdan Azure'a kopyalanır. Cihazın diskleri Ulusal Standart ve Teknoloji Kurumu (NIST) yönergelerine uygun ve güvenli bir şekilde silinir.

Bu süreç boyunca tüm durum değişikliklerinde e-posta ile bildirim alırsınız.

## <a name="region-availability"></a>Bölge kullanılabilirliği

Data Box Heavy, Hizmetin dağıtıldığı bölge, cihazın gönderildiği ülke/bölge ve verileri aktardığınız hedef Azure depolama hesabı temelinde veri aktarabilir.

- **Hizmet kullanılabilirliği** - Bu sürümde, Data Box Heavy şu bölgelerde kullanılabilir:
    - Birleşik Devletler'deki tüm genel bulut bölgeleri: Orta Batı ABD, Batı ABD2, Batı ABD, Orta Güney ABD, Orta ABD, Orta Kuzey ABD, Doğu ABD ve Doğu ABD2.
    - Avrupa Birliği: Batı Avrupa ve Kuzey Avrupa.
    - Birleşik Krallık: UK Güney ve UK Batı.
    - Fransa: Fransa Orta ve Fransa Güney.

- **Hedef Depolama hesapları**: Verilerin depolandığı depolama hesapları, hizmetin kullanılabildiği tüm Azure bölgelerinde sağlanır.

Data Box Heavy bölgenin kullanılabilirliği hakkında en güncel bilgiler için [bölgeye göre Azure ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)' ne gidin.

## <a name="sign-up"></a>Kaydolun

Data Box Heavy kaydolmak için aşağıdaki adımları uygulayın:

1. Azure portalında oturum açın: https://portal.azure.com.
2. Yeni kaynak oluşturmak için **+ kaynak oluştur ' a** tıklayın. **Azure Data Box** için arama yapın. **Azure Data Box** hizmetini seçin.
3. **Oluştur**'a tıklayın.
4. Data Box Heavy için kullanmak istediğiniz aboneliği seçin. Data Box Heavy kaynağını dağıtmak istediğiniz bölgeyi seçin. **Data Box Heavy** seçeneğinde **Kaydol**'a tıklayın.
5. Veri aktarım ülkesi/bölgesi, zaman çerçevesi, veri aktarımı için hedef Azure hizmeti, ağ bant genişliği ve veri aktarımı sıklığı hakkındaki soruları yanıtlayın. Gizlilik ve koşulları gözden geçirdikten sonra, Microsoft sizinle iletişim kurmak için e-posta adresinizi kullanabilir onay kutusunu seçin.

Kaydolduktan sonra bir Data Box Heavy sipariş edebilirsiniz.

    
