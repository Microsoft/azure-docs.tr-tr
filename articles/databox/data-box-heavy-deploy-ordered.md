---
title: Azure Data Box Heavy siparişi verme öğreticisi | Microsoft Docs
description: Dağıtım önkoşullarını ve Azure Data Box Heavy sipariş etmeyi öğrenin
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: dce3549bde4c62245e1d2b1f8ac0c88c0b70260c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70241399"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Öğretici: Azure Data Box Heavy sipariş etme


Azure Data Box Heavy, şirket içi verilerinizi Azure'a hızlı, kolay ve güvenilir bir şekilde aktarmanızı sağlayan bir hibrit çözümdür. Verilerinizi Microsoft tarafından sağlanan 770 TB'lık (yaklaşık kullanılabilir kapasite) depolama cihazına aktarabilir ve cihazı geri gönderebilirsiniz. Bu veriler daha sonra Azure'a yüklenir.

Bu öğreticide Azure Data Box Heavy siparişi verme adımları anlatılmaktadır. Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
> * Data Box Heavy önkoşulları
> * Data Box Heavy sipariş etme
> * Siparişi izleme
> * Siparişi iptal etme

## <a name="prerequisites"></a>Ön koşullar

Cihazı dağıtmadan önce Data Box hizmeti ve cihazı için aşağıdaki yapılandırma önkoşullarını tamamlayın.

### <a name="for-installation-site"></a>Kurulum yeri için

Başlamadan önce aşağıdakilerden emin olun:

- Cihaz standart kapı aralıklarına ve girişlere uyar. Ancak, cihazın tüm girişlerinize uyabildiğinden emin olun. Cihaz boyutları: genişlik: 26” uzunluk: 48” yükseklik: 28”.
- Zeminden başka bir yere kurulursa, cihaza bir asansör veya rampa ile erişiminiz olmalıdır. Cihaz yaklaşık olarak ~500 lb ağırlığındadır.
- Bu ayak izine sahip bir cihaza uyum sağlayabilecek kullanılabilir bir ağ bağlantısına yakın olan veri merkezindeki düz bir yerinizin olduğundan emin olun.


### <a name="for-service"></a>Hizmet için

Başlamadan önce aşağıdakilerden emin olun:
- Erişim kimlik bilgilerine sahip bir Microsoft Azure Storage hesabınız var.
- Data Box hizmeti için kullandığınız aboneliğin aşağıdaki türlerden birinde olduğundan emin olun:
    - Microsoft Kurumsal Anlaşma (EA). [EA abonelikleri](https://azure.microsoft.com/pricing/enterprise-agreement/) hakkındaki yazıları okuyun.
    - Bulut Çözümü Sağlayıcısı (CSP). [Azure CSP programı](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview) hakkında daha fazla bilgi edinin.
    - Microsoft Azure Sponsorluğu. [Azure sponsorluğu programı](https://azure.microsoft.com/offers/ms-azr-0036p/) hakkında daha fazla bilgi edinin.

- Data Box Heavy siparişi oluşturmak için, abonelik üzerinde sahip veya katkıda bulunan erişimine sahip olduğunuzdan emin olun.

### <a name="for-device"></a>Cihaz için

Başlamadan önce aşağıdakilerden emin olun:
- Cihazınız paketten çıkarılmıştır.
- Veri merkezi ağına bağlı bir konak bilgisayarınız olmalıdır. Data Box Heavy, verileri bu bilgisayardan kopyalayacaktır. [Azure Data Box Heavy sistem gereksinimleri](data-box-system-requirements.md) altında açıklandığı gibi konak bilgisayarınızın desteklenen bir işletim sistemini çalıştırması gerekir.
- Yerel kullanıcı arabirimine bağlanmak ve cihazı yapılandırmak için RJ-45 kablosu olan bir dizüstü bilgisayar gereklidir. Cihazın her düğümünü bir kez yapılandırmak için dizüstü bilgisayarı kullanın.
- Veri merkezinizin yüksek hızlı ağı olmalıdır. En az bir adet 10 GbE bağlantınızın olması önemle tavsiye edilir.
- Cihaz düğümü başına bir 40 Gb/sn veya 10 Gb/sn kablo gerekir. [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) ağ arabirimi ile uyumlu kabloları seçin:

    - 40 Gb/sn kablonun cihaz tarafındaki ucunun QSFP+ olması gerekir.
    - 10 GB/sn kablo için, bir ucu 10 G kabloya bağlanan bir SFP+ kablo ve cihaza takılan uç için QSFP+’dan SFP+’ya bağdaştırıcı (veya QSA bağdaştırıcısı) gerekir.
    - Güç kabloları cihaza dahildir.

## <a name="order-data-box-heavy"></a>Data Box Heavy sipariş etme

Cihaz sipariş etmek için Azure portalında aşağıdaki adımları izleyin.

1. Microsoft Azure kimlik bilgilerini kullanarak şu URL’de oturum açın: [https://portal.azure.com](https://portal.azure.com).
2. **+ Kaynak oluştur**’u seçin ve *Azure Data Box* araması yapın. **Azure Data Box**’ı seçin.
    
   [![Azure Data Box arama 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. **Oluştur**’u seçin.

4. Data Box hizmetinin bölgenizde kullanılabilir olup olmadığını kontrol edin. Aşağıdaki bilgileri girin veya seçin ve sonra **Uygula**'yı seçin.

    |Ayar  |Değer  |
    |---------|---------|
    |Abonelik     | Data Box hizmeti için bir EA, CSP veya Azure sponsorluk aboneliği seçin. <br> Abonelik fatura hesabınıza bağlıdır.       |
    |Aktarım türü     | **Azure’a içeri aktar**’ı seçin.        |
    |Kaynak ülke     | Verilerinizin bulunduğu ülkeyi/bölgeyi seçin.         |
    |Hedef Azure bölgesi     | Verileri aktarmak istediğiniz Azure bölgesini seçin.        |

    [![Data Box ailesi kullanılabilirliğini seçme](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. **Data Box Heavy**’yi seçin. Tek sipariş için en yüksek kullanılabilir kapasite 770 TB’tır.

    [![Data Box Heavy’yi seçin](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. **Sipariş** bölümünde **Sipariş ayrıntıları**'nı belirtin. Aşağıdaki bilgileri girin veya seçin ve sonra **İleri**'yi seçin.
    
    |Ayar  |Değer  |
    |---------|---------|
    |Adı     | Siparişi takip etmek için kullanılacak kolay bir ad girin. <br> Ad harf, rakam ve tirelerden oluşan 3-24 karakter arası uzunlukta olabilir. <br> Ad bir harf veya sayıyla başlamalı ve bitmelidir.      |
    |Kaynak grubu     | Var olan bir taneyi kullanın veya yenisini oluşturun. <br> Kaynak grubu, birlikte yönetilebilen ve ya dağıtılabilen kaynaklardan oluşan mantıksal kapsayıcıdır.         |
    |Hedef Azure bölgesi     | Depolama hesabınız için bir bölge seçin. <br> Daha fazla bilgi için [bölge kullanılabilirliği](https://azure.microsoft.com/global-infrastructure/services/?products=databox)’ne gidin.        |
    |Depolama hedefi     | Depolama hesabı veya yönetilen diskler ya da her ikisini seçin. <br> Belirtilen Azure bölgesine göre filtrelenen listeden var olan bir veya daha fazla depolama hesabı seçin. <br>Data Box Heavy en çok 10 depolama hesabına bağlanabilir. <br> Dilerseniz yeni bir **Genel amaçlı v1**, **Genel amaçlı v2** veya **Blob depolama hesabı** da oluşturabilirsiniz. <br> Azure Data Lake Storage 2. Nesil hesapları desteklenmez. [Cihazınızla desteklenen depolama hesaplarına](data-box-heavy-system-requirements.md#supported-storage-accounts) bakın. <br>Sanal ağları olan depolama hesapları desteklenir. Data Box hizmetinin güvenli depolama hesaplarıyla çalışmasına izin vermek için, depolama hesabı ağ güvenlik duvarı ayarları içinden güvenilen hizmetleri etkinleştirin. Daha fazla bilgi için bkz. [Azure Data Box hizmetini güvenilen hizmet olarak ekleme](../storage/common/storage-network-security.md#exceptions).|

    Depolama hesabı depolama hedefi olarak kullanılıyorsa aşağıdaki ekran görüntüsünü görürsünüz:

    ![Depolama hesabı için Data Box Heavy siparişi](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Depolama hedefi olarak depolama hesabına ek olarak Data Box Heavy’yi şirket içi VHD’lerden yönetilen diskler oluşturmak için kullanıyorsanız aşağıdaki bilgileri sağlamanız gerekir:

    |Ayar  |Değer  |
    |---------|---------|
    |Kaynak grupları     | Şirket içi VHD'lerden yönetilen diskler oluşturmayı düşünüyorsanız yeni kaynak grupları oluşturun. Data Box hizmeti tarafından yönetilen disk için bir Data Box Heavy siparişi oluştururken, mevcut bir kaynak grubunu yalnızca kaynak grubu daha önce oluşturulduysa kullanabilirsiniz. <br> Noktalı virgülle ayrılmış birden çok kaynak grubu belirtin. En fazla 10 kaynak grubu desteklenir.|

    ![Yönetilen disk için Data Box Heavy siparişi](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Yönetilen diskler için belirtilen depolama hesabı, hazırlama depolama hesabı olarak kullanılır. Data Box hizmeti, VHD'leri yönetilen disklere dönüştürmeden ve kaynak gruplarına taşımadan önce hazırlama depolama hesabına sayfa blobları olarak yükler. Daha fazla bilgi için bkz. [Azure'a veri yüklemeyi doğrulama](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. **Teslimat adresi**’ne adınızı, soyadınızı, şirket adını, posta adresini ve geçerli bir telefon numarasını girin. **Adresi doğrula**'yı seçin. 

    Hizmet, teslimat adresinde hizmetin kullanılabilirlik durumunu doğrular. Hizmet belirtilen teslimat adresinde kullanılabilir durumdaysa bu konuda bir bildirim gönderilir. **İleri**’yi seçin.

8. **Bildirim ayrıntıları** sayfasında e-posta adresi belirtin. Hizmet, belirtilen e-posta adreslerine sipariş durumundaki güncelleştirmelerle ilgili bilgi gönderir.

    Grup yöneticisinin ayrılması durumunda da bildirim almaya devam etmek için bir grup e-postası kullanmanız önerilir.

9. **Özet** sayfasında sipariş, iletişim, bildirim ve gizlilik koşullarıyla ilgili bilgileri gözden geçirin. Gizlilik koşullarını kabul ettiğinizi belirten kutuyu işaretleyin.

10. **Sipariş**'i seçin. Siparişin oluşturulması birkaç dakika sürer.


## <a name="track-the-order"></a>Siparişi izleme

Siparişi verdikten sonra durumunu Azure portalından takip edebilirsiniz. Data Box Heavy siparişinize gidin ve durumunu görüntülemek için **Genel bakış** sayfasını inceleyin. Portalda siparişin durumu **Sipariş edildi** olarak görünür.

Cihaz yoksa bir bildirim alırsınız. Cihaz varsa, Microsoft gönderilecek cihazı belirler ve gönderimi hazırlar. Cihaz hazırlanırken şu eylemler gerçekleştirilir:

- Cihazla ilişkili her depolama hesabı için SMB paylaşımları oluşturulur.
- Her paylaşım için, kullanıcı adı veya parola gibi erişim kimlik bilgileri oluşturulur.
- Cihaz kilidinin açılmasına yardımcı olan cihaz parolası da oluşturulur.
- Data Box Heavy, herhangi bir noktada cihaza yetkisiz erişimi önlemek için kilitlenmiştir.

Cihazın hazırlanması tamamlandığında portalda sipariş durumu **İşlendi** olarak değişir.

![Data Box Heavy siparişi işlendi](media/data-box-overview/data-box-order-status-processed.png)

Microsoft ardından cihazınızı hazırlar ve bölgeye uygun gönderim şirketine teslim eder. Cihaz gönderildikten sonra bir takip numarası iletilir. Portalda sipariş durumu **Yola çıktı** olarak değişir.

![Data Box Heavy siparişi gönderildi](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Siparişi iptal etme

Bu siparişi iptal etmek için Azure portalında **Genel bakış**'a gidin ve komut çubuğundan **İptal**'e tıklayın.

Sipariş verdikten sonra, sipariş durumu işlendi olmadan önce herhangi bir noktada siparişi iptal edebilirsiniz.
 
İptal edilen bir siparişi silmek için **Genel bakış**'a gidin ve komut çubuğundan **Sil**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide Azure Data Box Heavy ile ilgili aşağıdaki bilgileri öğrendiniz:

> [!div class="checklist"]
> * Ön koşullar
> * Data Box Heavy sipariş etme
> * Siparişi izleme
> * Siparişi iptal etme

Data Box Heavy'yi ayarlama hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Data Box Heavy’yi ayarlama](./data-box-heavy-deploy-set-up.md)
