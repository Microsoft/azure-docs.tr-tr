---
title: Microsoft Azure Data Box'a genel bakış | Verilerde Microsoft Docs
description: Muazzam miktarlarda verinin Azure’a aktarılmasını sağlayan bir bulut çözümü olan Azure Data Box açıklanır
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 09/17/2019
ms.author: alkohli
ms.openlocfilehash: a3e27cf427de3a33f71d6f4a0af9b1590abeb573
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066993"
---
# <a name="what-is-azure-data-box"></a>Azure Data Box nedir?

Microsoft Azure Data Box bulut çözümü, terabaytlarca veriyi Azure'a hızlı, uygun maliyetli ve güvenilir bir şekilde göndermenizi sağlar. Site özel bir Data Box depolama cihazı gönderilerek güvenli veri aktarımı hızlandırılır. Her depolama cihazının maksimum kullanılabilir depolama kapasite 80 TB'tır ve bölgesel bir kargo firmasıyla veri merkezinize ulaştırılır. Ulaşım sırasında verileri korumalı ve güvenli tutmak için cihaz sağlamlaştırılmıştır.

Data Box cihazını Azure portalı üzerinden sipariş edebilirsiniz. Cihaz size ulaştıktan sonra, yerel web kullanıcı arabirimini kullanarak cihazı hızla ayarlayabilirsiniz. Sunucularınızdaki verileri cihaza kopyalayın ve cihazı Azure'a geri gönderin. Azure veri merkezinde, verileriniz cihazdan Azure'a otomatik olarak yüklenir. Sürecin tamamı Azure portaldaki Data Box hizmeti tarafından uçtan uca izlenir.


## <a name="use-cases"></a>Uygulama alanları

Data Box, ağ bağlantısının hiç olmadığı veya sınırlı olduğu senaryolarda 40 TB'tan büyük boyutlu veri aktarımları için idealdir. Veri taşıma işlemi tek seferlik, düzenli veya başta toplu veri aktarımı ve ardından düzenli aktarımlardan oluşabilir. Burada Data Box'ın veri aktarımı için kullanılabileceği çeşitli senaryoları bulabilirsiniz.

 - **Tek seferlik geçiş**: Büyük miktardaki şirket içi verilerinin Azure'a taşınmasıdır. 
     - Çevrimiçi bir medya kitaplığı oluşturmak için bir medya kitaplığını çevrimdışı teyplerden Azure'a taşıma.
     - VM grubunuzu, SQL sunucunuzu ve uygulamalarınızı Azure'a geçirme
     - HDInsight kullanarak derin analiz ve raporlama yapmak için geçmiş verilerini Azure'a taşıma

 - **İlk toplu aktarım**: Data Box (seed) ile toplu veri aktarımı yapıldıktan sonra ağ üzerinden artımlı aktarım işlemlerinin gerçekleştirilmesidir. 
     - Örneğin, ilk büyük geçmiş yedeklemesini Azure'a taşımak için Commvault ve Data Box gibi yedekleme çözümü iş ortakları kullanılır. İşlem tamamlandıktan sonra, artımlı veriler ağ üzerinden Azure depolamasına aktarılır.

- **Düzenli yüklemeler**: Düzenli olarak oluşturulan büyük miktarlardaki verilerin Azure'a taşınması gerektiği durumlardır. Enerji keşfinde petrol kuyularında ve rüzgar enerjisini santrallerinde oluşturulan videolar örnek olarak verilebilir.      

## <a name="benefits"></a>Avantajlar

Data Box, büyük miktarlarda veriyi ağ bağlantısını çok az etkileyerek veya hiç etkilemeden Azure'a taşımak için tasarlanmıştır. Çözümün şöyle avantajları vardır:

- **Hız**: Data Box, Azure'a 80 TB'a kadar veri taşımak için 1 Gb/sn veya 10 Gb/sn ağ arabirimlerini kullanır.

- **Güvenli**: Data Box cihaz, veriler ve hizmet için yerleşik güvenlik önlemlerine sahiptir.
  - Cihaz, üzerinde oynanmaya karşı korumalı vidalar ve üzerinde oynandığını belli eden çıkartmalarla korunan, sağlamlaştırılmış bir kasaya sahiptir. 
  - Cihazdaki veriler her zaman AES 256 bit ile şifrelenir.
  - Cihaz kilidi yalnızca Azure portalından alınan parolayla açılabilir.
  - Hizmet, Azure güvenlik özelliklerinin koruması altındadır.
  - Verileriniz Azure'a yüklendikten sonra cihazdaki diskler NIST 800-88r1 standartlarına göre silinir.
    
    Daha fazla bilgi için bkz. [Azure Data Box güvenliği ve veri koruması](data-box-security.md).

## <a name="features-and-specifications"></a>Özellikler ve belirtimler

Data Box cihazı bu sürümde aşağıdaki özelliklere sahiptir.

| Belirtimler                                          | Açıklama              |
|---------------------------------------------------------|--------------------------|
| Ağırlık                                                  | < 50 lb                |
| Boyutlar                                              | Cihaz genişliği: 309,0 mm Yükseklik: 430,4 mm Derinlik: 502,0 mm |            
| Raf alanı                                              | Yanlamasına rafa yerleştirildiğinde 7 U (rafa takılamaz)|
| Gerekli kablolar                                         | 1 X güç kablosu (dahildir) <br> 2 RJ45 kablosu <br> 2 X SFP+ Twinax bakır kablo|
| Depolama kapasitesi                                        | 100 TB boyutundaki cihaz, RAID 5 korumasından sonra 80 TB kullanılabilir kapasiteye sahip|
| Güç derecelendirmesi                                            | Güç kaynağı birimi 700 W için derecelendirilir. <br> Genellikle, birim 375 W çizer.|
| Ağ arabirimleri                                      | 2 X 1 GbE arabirimi - MGMT, DATA 3. <br> MGMT - yönetim için, kullanıcı tarafından yapılandırılamaz, ilk kurulumda kullanılır <br> DATA3 - veriler için, kullanıcı tarafından yapılandırılabilir ve varsayılan olarak dinamiktir <br> MGMT ve DATA 3, 10 GbE olarak da çalışabilir <br> 2 X 10 GbE arabirimi - DATA 1, DATA 2 <br> Her ikisi de veriler içindir, dinamik (varsayılan) veya statik olarak yapılandırılabilir |
| Veri aktarım medyası                                     | RJ45, SFP+ bakır 10 GbE Ethernet  |
| Güvenlik                                                | Üzerinde oynanmasına karşı dayanıklı özel vidaları olan, sağlamlaştırılmış cihaz kasası <br> Cihazın altına yapıştırılmış, üzerinde oynandığını belli eden çıkartmalar|
| Veri aktarımı hızı                                      | 10 GbE ağ arabirimi üzerinde bir günde en çok 80 TB        |
| Yönetim                                              | Yerel web kullanıcı arabirimi - tek seferlik ilk kurulum ve yapılandırma <br> Azure portalı - gündelik cihaz yönetimi        |

## <a name="data-box-components"></a>Data Box bileşenleri

Data Box aşağıdaki bileşenleri içerir:

* **Data Box cihazı**: Birincil depolama sağlayan, bulut depolamasıyla iletişimi yöneten ve cihazda depolanan tüm verilerin güvenliğini ve gizliliğini güvence altına almaya yardımcı olan fiziksel bir cihaz. Data Box cihazının 80 TB kullanılabilir depolama kapasitesi vardır. 

    ![Data Box'in ön ve arka yüzü](media/data-box-overview/data-box-combined3.png)

    
* **Data Box hizmeti**: Azure portalının, farklı coğrafi konumlardan erişebildiğiniz bir web arabiriminde Data Box cihazını yönetmenize olanak tanıyan bir uzantısı. Data Box cihazınızın gündelik yönetimini gerçekleştirmek için Data Box hizmetini kullanın. Hizmetin görevleri, siparişleri oluşturma ve yönetmeyi, uyarıları görüntülemeyi ve yönetmeyi, paylaşımları yönetmeyi içerir.  

    ![Azure portalında Data Box hizmeti](media/data-box-overview/data-box-service1.png)

    Daha fazla bilgi için, [Data Box cihazınızı yönetmek için Data Box hizmetini kullanma](data-box-portal-ui-admin.md) konusuna gidin.

* **Yerel web kullanıcı arabirimi**: Yerel ağa bağlanabilmesi için cihazı yapılandırmak ve ardından Data Box hizmetine kaydetmek için kullanılan, web tabanlı kullanıcı arabirimi. Yerel web kullanıcı arabirimini Data Box cihazını kapatmak ve yeniden başlatmak, kopya günlüklerini görüntülemek ve hizmet isteğinde bulunmak üzere Microsoft Desteği'ne başvurmak için de kullanın.

    ![Data Box yerel web kullanıcı arabirimi](media/data-box-overview/data-box-local-web-ui.png)

    Web tabanlı kullanıcı arabirimini kullanma hakkında bilgi için, [Data Box'ınızı yönetmek için web tabanlı kullanıcı arabirimini kullanma](data-box-portal-ui-admin.md) konusuna gidin.

## <a name="the-workflow"></a>İş akışı

Tipik iş akışı aşağıdaki adımlardan oluşur:

1. **Sipariş**: Azure portaldan bir sipariş oluşturup gönderim bilgilerini ve verileriniz için hedef Azure depolama hesabını belirtin. Cihaz varsa, Azure cihazı hazırlar, gönderir ve bir gönderi takip numarası iletir.

2. **Alma**: Cihaz teslim edildiğinde, belirtilen kabloları kullanarak cihazı ağa ve güç kaynağına kabloyla bağlayın. Cihazı açın ve bağlayın. Cihaz ağını yapılandırın ve verileri kopyalamak istediğiniz konak bilgisayarına paylaşımları takın.

3. **Veri kopyalama**: Verileri Data Box paylaşımlarına kopyalayın.

4. **İade etme**: Cihazı hazırlayın, kapatın ve Azure veri merkezine geri gönderin.

5. **Karşıya yükleme**: Veriler otomatik olarak cihazdan Azure'a kopyalanır. Cihazın diskleri Ulusal Standart ve Teknoloji Kurumu (NIST) yönergelerine uygun ve güvenli bir şekilde silinir.

Bu işlem boyunca tüm durum değişiklikleri e-posta ile bildirilir. Ayrıntılı akış hakkında daha fazla bilgi için, [Azure portalında Data Box dağıtımı](data-box-deploy-ordered.md) konusuna gidin.

## <a name="region-availability"></a>Bölge kullanılabilirliği

Data Box, Hizmetin dağıtıldığı bölge, cihazın gönderildiği ülke/bölge ve verileri aktardığınız hedef Azure depolama hesabı temelinde veri aktarabilir. 

- **Hizmet kullanılabilirliği** -bölge kullanılabilirliği hakkında bilgi için [bölgeye göre kullanılabilir Azure ürünlerine](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)gidin. Data Box, Azure Kamu Bulutu 'nda da dağıtılabilir. Daha fazla bilgi için bkz. [Azure Kamu nedir?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).

- **Hedef Depolama hesapları**: Verilerin depolandığı depolama hesapları, hizmetin kullanılabildiği tüm Azure bölgelerinde sağlanır.  


## <a name="next-steps"></a>Sonraki adımlar

- [Data Box sistem gereksinimlerini](data-box-system-requirements.md) gözden geçirin.
- [Data Box sınırlarını](data-box-limits.md) anlayın.
- Azure portalda [Azure Data Box](data-box-quickstart-portal.md)’u hızla dağıtın.




