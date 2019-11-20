---
title: 'Hızlı başlangıç: Microsoft Azure Data Box Heavy| Microsoft Docs'
description: Azure portalında Azure Data Box Heavy'yi hızla dağıtmayı öğrenin
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: b2ebb4c5a0df360cede084a90ebe5da041a08aa0
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70241387"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Hızlı Başlangıç: Azure portalını kullanarak Azure Data Box Heavy'yi dağıtma

Bu hızlı başlangıçta Azure portalı kullanarak Azure Data Box Heavy'yi dağıtma adımları anlatılmaktadır. Adımlar kablolama, yapılandırma ve Azure'a yükleyebilmesi için verileri Data Box Heavy'ye kopyalama işlemlerinden oluşur. Hızlı başlangıç Azure portalında ve cihazın yerel web kullanıcı arabiriminde gerçekleştirilir.

Ayrıntılı adım adım dağıtım ve izleme yönergeleri için, [Öğretici: Azure Data Box Heavy sipariş etme](data-box-heavy-deploy-ordered.md) konusuna gidin

## <a name="prerequisites"></a>Ön koşullar

Cihazı dağıtmadan önce kurulum yeri, Data Box hizmeti ve cihazı için aşağıdaki yapılandırma önkoşullarını tamamlayın.

### <a name="for-installation-site"></a>Kurulum yeri için

Başlamadan önce aşağıdakilerden emin olun:

- Cihaz tüm girişlerinize uyabilir. Cihaz boyutları: genişlik: 26” uzunluk: 48” yükseklik: 28”.
- Zeminden başka bir yere kurmayı planlıyorsanız cihaza bir asansör veya rampa ile erişiminiz olmalıdır.
- Cihazı taşımak için iki kişi gerekir. Cihaz yaklaşık olarak ~500 lb ağırlığındadır ve tekerleklidir.
- Bu ayak izine sahip bir cihaza uyum sağlayabilecek kullanılabilir bir ağ bağlantısına yakın olan veri merkezindeki düz bir yeriniz olmalıdır.

### <a name="for-service"></a>Hizmet için

Başlamadan önce aşağıdakilerden emin olun:

- Erişim kimlik bilgilerine sahip bir Microsoft Azure Storage hesabınız var.
- Data Box hizmeti için kullandığınız abonelik [Microsoft Kurumsal Anlaşma (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Bulut Çözümü Sağlayıcısı (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview) veya [Microsoft Azure Sponsorluğu](https://azure.microsoft.com/offers/ms-azr-0036p/) olmalıdır.
- Data Box Heavy siparişi oluşturmak için abonelik üzerinde sahip veya katkıda bulunan erişimine sahip olmanız gerekir.

### <a name="for-device"></a>Cihaz için

Başlamadan önce aşağıdakilerden emin olun:

- [Data Box Heavy güvenlik yönergelerini](data-box-safety.md) gözden geçirmiş olmanız gerekir.
- Veri merkezi ağına bağlı bir ana bilgisayarınız olmalıdır. Data Box Heavy, verileri bu bilgisayardan kopyalayacaktır. Ana bilgisayarınızın [desteklenen bir işletim sistemi](data-box-heavy-system-requirements.md) çalıştırması gerekir.
- Yerel kullanıcı arabirimine bağlanmak ve cihazı yapılandırmak için RJ-45 kablosu olan bir dizüstü bilgisayar gereklidir. Cihazın her düğümünü bir kez yapılandırmak için dizüstü bilgisayarı kullanın.
- Veri merkezinizde yüksek hızlı bir ağ ve en az bir 10 GbE bağlantınız olmalıdır.
- Cihaz düğümü başına bir 40 Gb/sn kablo veya 10 Gb/sn kablo gerekir. Mellanox MCX314A-BCCT ağ arabirimi ile uyumlu kabloları seçin:
    - 40 Gb/sn kablonun cihaz tarafındaki ucunun QSFP+ olması gerekir.
    - 10 GB/sn kablo için, bir ucu 10 G kabloya bağlanan bir SFP+ kablo ve cihaza takılan uç için QSFP+’dan SFP+’ya bağdaştırıcı (veya QSA bağdaştırıcısı) gerekir.
- Güç kabloları, cihazın arkasındaki bir tepside bulunur.


## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="order"></a>Sipariş verme

Bu adım yaklaşık 5 dakika sürer.

1. Azure portalında yeni bir Azure Data Box kaynağı oluşturun.
2. Bu hizmetin etkinleştirildiği mevcut aboneliği seçin ve aktarım türünü **İçeri aktarma** olarak belirleyin. Verilerin bulunduğu **Kaynak ülkeyi** ve veri aktarımı için **Azure hedef bölgesini** seçin.
3. **Data Box Heavy**’yi seçin. Maksimum kullanılabilir kapasite 770 TB'tır ve daha büyük veri boyutları için birden çok sipariş oluşturabilirsiniz.
4. Sipariş ayrıntılarını ve sevkiyat bilgilerini girin. Hizmet bölgenizde kullanılabilir durumdaysa bildirim e-posta adreslerini girin, özeti gözden geçirin ve siparişi oluşturun.

Sipariş oluşturulduktan sonra cihaz gönderilmek üzere hazırlanır.

::: zone-end

::: zone target = "chromeless"

# <a name="cable-and-connect-to-your-device"></a>Kabloları bağlama ve cihazınıza bağlanma

Önkoşulları inceledikten sonra kablo bağlantısını yapacak ve cihazınıza bağlanacaksınız.

::: zone-end

## <a name="cable-for-power"></a>Güç kablosu

Bu adım yaklaşık 5 dakika sürer.

Data Box Heavy elinize ulaştığında, cihazın güç kablosu bağlantılarını yapın ve cihazı çalıştırın.

1. Cihazla oynandığına veya cihazın hasarlı olduğuna ilişkin bir kanıt varsa, devam etmeyin. Size yeni bir cihaz gönderilmesi için Microsoft Desteği'ne başvurun.
2. Cihazı kurulum yerine taşıyın ve arka tekerlekleri kilitleyin.
3. Dört güç kablosunun tamamını cihazın arkasındaki güç kaynaklarına bağlayın.
4. Cihaz düğümlerini açmak için ön düzlemdeki güç düğmelerini kullanın.

## <a name="cable-first-node-for-network"></a>Ağ için ilk düğümün kablo bağlantısını yapma

Bu adımın tamamlanması yaklaşık 10-15 dakika sürer.

1. Konak bilgisayarınızı cihazdaki yönetim bağlantı noktasına (MGMT) bağlamak için bir RJ 45 CAT 6 ağ kablosu kullanın.
2. Veriler için en az bir 40 Gb/sn'lik (1 Gb/sn'ye tercih edilir) ağ arabirimini, DATA 1 veya DATA 2'yi bağlamak üzere bir Twinax QSFP+ bakır kablo kullanın. 10 Gb/sn'lik bir anahtar kullanılıyorsa, veriler için 40 Gb/sn ağ arabirimini bağlamak üzere QSFP+’dan SFP+’ya bağdaştırıcı (QSA bağdaştırıcısı) ile bir Twinax SFP+ bakır kablo kullanın.
3. Cihazın kablolarını aşağıda gösterildiği gibi takın.  

    ![Data Box Heavy kablo bağlantısı](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>İlk düğümü yapılandırma

Bu adımın tamamlanması yaklaşık 5-7 dakika sürer.

1. Cihaz parolasını almak için [Azure portalında](https://portal.azure.com) **Genel > Cihaz ayrıntıları**'na gidin. Aynı parola, cihazın her iki düğümü için de kullanılır.
2. Data Box Heavy'ye bağlanmak için kullandığınız bilgisayardaki Ethernet bağdaştırıcısına 192.168.100.5 statik IP adresini ve 255.255.255.0 alt ağını atayın. Cihazın yerel web kullanıcı arabirimine `https://192.168.100.10` adresinden erişin. Siz cihazı açtıktan sonra bağlantı kurulması 5 dakika kadar sürebilir.
3. Azure portalından alınan parolayı kullanarak oturum açın. Web sitesinin güvenlik sertifikasında sorun olduğunu belirten bir hata görürsünüz. Web sayfasına ilerlemek için tarayıcıya özel yönergeleri izleyin.
4. Varsayılan olarak, arabirimlerin (MGMT hariç) ağ ayarları DHCP olarak yapılandırılır. Gerekirse, bu arabirimleri statik olarak yapılandırabilir ve bir IP adresi sağlayabilirsiniz.

## <a name="cable-and-configure-the-second-node"></a>Kablo bağlantısını yapma ve ikinci düğümü yapılandırma

Bu adımın tamamlanması yaklaşık 15-20 dakika sürer.

Cihazdaki ikinci düğümün kablo bağlantısını yapmak ve düğümü yapılandırmak için birinci düğümde kullanılan adımları uygulayın.  


::: zone target = "docs"

## <a name="copy-data"></a>Veri kopyalama

Bu işlemi tamamlamak için gereken süre verilerinizin boyutuna ve verilerin kopyalandığı ağın hızına göre değişir.
 
1. Her iki 40 GB/sn veri arabirimini birbirine paralel olarak kullanarak her iki cihaz düğümüne verileri kopyalayın.

    - Bir Windows ana bilgisayar kullanıyorsanız, [Robocopy](https://technet.microsoft.com/library/ee851678.aspx) gibi SMB uyumlu bir dosya kopyalama aracı kullanın.
    - NFS konağı için, `cp` veya `rsync` komutunu kullanarak verileri kopyalayın.
2. Cihaz üzerindeki paylaşımlara bağlanmak için şu yolu kullanın: `\\<IP address of your device>\ShareName`. Paylaşım erişimi kimlik bilgilerini almak için Data Box Heavy'nin yerel web kullanıcı arabiriminde **Bağlan ve kopyala** sayfasına gidin.
3. Paylaşım ve klasör adlarıyla verilerin, [Azure Depolama ve Data Box Heavy hizmet limitleri](data-box-heavy-limits.md) altında açıklanan yönergelere uyduğundan emin olun.

## <a name="prepare-to-ship"></a>Göndermeye hazırlama

Bu işlemi tamamlamak için gereken süre verilerinizin boyutuna göre değişir.

1. Veri kopyalama hatasız bir şekilde tamamlandıktan sonra yerel web kullanıcı arabirimindeki **Göndermeye hazırlama** sayfasına gidin ve gönderme hazırlığına başlayın.
2. **Göndermeye hazırlama** işlemi her iki düğümde de başarıyla tamamlandıktan sonra yerel web kullanıcı arabiriminden cihazı kapatın.

## <a name="ship-to-azure"></a>Azure'a gönderme

Bu işlemin tamamlanması yaklaşık 15-20 dakika kadar sürer.

1. Kabloları çıkarın ve cihazın arkasında bulunan tepsiye geri koyun.
2. Bölgenizdeki taşıyıcıdan bir kargo toplama randevusu alın.
3. Kargo toplama ile ilgili bilgi almak ve iade gönderim etiketini almak için [Data Box Operasyonları](mailto:DataBoxOps@microsoft.com) birimine ulaşın.
4. İade gönderim etiketi cihazın ön panelinde görünür olmalıdır.

## <a name="verify-data"></a>Verileri doğrulama

Bu işlemi tamamlamak için gereken süre verilerinizin boyutuna göre değişir.

1. Data Box Heavy cihazı, Azure veri merkezi ağına bağlandığında veriler otomatik olarak Azure'a yüklenir.
2. Veri kopyalama işlemi tamamlandığında Data Box hizmeti Azure portalından bildirim gönderir.

    1. Hata günlüklerini kontrol ederek hata olup olmadığını kontrol edin ve gerekli eylemleri gerçekleştirin.
    2. Kaynaktan silmeden önce verilerinizin depolama hesaplarında olduğundan emin olun.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu adımın tamamlanması 2-3 dakika sürer.

- Data Box Heavy siparişini işleme alınmadan önce Azure portalından iptal edebilirsiniz. Siparişler işleme alındıktan sonra iptal edilemez. Sipariş, tamamlanma aşamasına gelene kadar ilerler. Siparişi iptal etmek için **Genel bakış**'a gidin ve komut çubuğundan **İptal**'e tıklayın.

- Siparişin durumu **Tamamlandı** veya **İptal edildi** olduğunda Azure portaldan silebilirsiniz. Siparişi silmek için **Genel bakış**'a gidin ve komut çubuğundan **Sil**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Azure'a veri aktarımı konusunda yardım almak için Data Box Heavy'yi dağıttınız. Azure Data Box Heavy yönetimi hakkında daha fazla bilgi edinmek için aşağıdaki öğreticiye geçin:

> [!div class="nextstepaction"]
> [Azure portalı kullanarak Data Box Heavy'yi yönetme](data-box-portal-admin.md)

::: zone-end
