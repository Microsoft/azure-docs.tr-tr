---
title: 'Hızlı başlangıç: Microsoft Azure Data Box | Microsoft Docs'
description: Azure portalında Azure Data Box'ınızı hızla dağıtmayı öğrenin
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 26578e277a9f6747063ee377e72845b599a0301b
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772740"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-using-the-azure-portal"></a>Hızlı Başlangıç: Azure portalını kullanarak Azure Data Box'ı dağıtma

::: zone-end

::: zone target="chromeless"

# <a name="get-started-with-azure-data-box"></a>Azure Data Box’ı kullanmaya başlayın 

::: zone-end

::: zone target="docs"

Bu hızlı başlangıçta Azure portalı kullanarak Azure Data Box'ı dağıtma adımları anlatılmaktadır. Adımlar kablolama, yapılandırma ve Azure'a yükleyebilmesi için verileri Data Box'a kopyalama işlemlerinden oluşur. Hızlı başlangıç Azure portalında ve cihazın yerel web kullanıcı arabiriminde gerçekleştirilir.

Ayrıntılı adım adım dağıtım ve izleme yönergeleri için, [Öğretici: Azure Data Box sipariş etme](data-box-deploy-ordered.md) bölümüne gidin

::: zone-end 

::: zone target="chromeless"

Bu kılavuzda Azure portalı kullanarak Azure Data Box'ı dağıtma adımları anlatılmaktadır. Bu adımlar önkoşulları gözden geçirmeyi, kablo bağlantısını yapıp cihazınıza bağlanmayı ve verilerinizi Azure'a yükleyebilmek için cihazınıza kopyalamayı içerir.

::: zone-end

::: zone target="docs"
 
## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce:

- Data Box hizmeti için kullandığınız aboneliğin aşağıdaki türlerden birinde olduğundan emin olun:
    - Microsoft Kurumsal Anlaşma (EA). [EA abonelikleri](https://azure.microsoft.com/pricing/enterprise-agreement/) hakkındaki yazıları okuyun.
    - Bulut Çözümü Sağlayıcısı (CSP). [Azure CSP programı](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview) hakkında daha fazla bilgi edinin.
    - Microsoft Azure Sponsorluğu. [Azure sponsorluğu programı](https://azure.microsoft.com/offers/ms-azr-0036p/) hakkında daha fazla bilgi edinin. 

- Data Box siparişi oluşturmak için, abonelik üzerinde sahip veya katkıda bulunan erişimine sahip olduğunuzdan emin olun.
- [Data Box'ınızın güvenlik yönergelerini](data-box-safety.md) gözden geçirin.
- Data Box üzerinden kopyalamak istediğiniz verileri içeren bir ana bilgisayarınız var. Ana bilgisayarınız:
    - [Desteklenen bir işletim sistemi](data-box-system-requirements.md) çalıştırılmalıdır.
    - Yüksek hızlı ağa bağlısınız. En az bir adet 10 GbE bağlantınızın olması önemle tavsiye edilir. 10 GbE bağlantı yoksa, 1 GbE veri bağlantısı kullanılabilir ancak kopyalama hızı etkilenir. 
- Data Box’ı yerleştirebileceğiniz düz bir yüzeye erişiminiz olmalıdır. Cihazı standart bir rafa yerleştirmek istiyorsanız, veri merkezi rafınızda bir 7U yuvası olmalıdır. Cihazı düz veya dik şekilde rafa yerleştirebilirsiniz.
- Data Box'ınızı ana bilgisayara bağlamak için aşağıdaki kabloları temin ettiniz.
    - İki adet 10 GbE SFP+ Twinax bakır kablo (DATA 1, DATA 2 ağ arabirimleri ile kullanın)
    - Bir RJ-45 CAT 6 ağ kablosu (MGMT ağ arabirimi ile kullanın)
    - Bir RJ-45 CAT 6A VEYA bir RJ-45 CAT 6 ağ kablosu (sırasıyla 10 Gb/sn veya 1 Gb/sn olarak yapılandırılmış DATA 3 ağ arabirimi ile birlikte kullanın)

::: zone-end 

::: zone target="chromeless"

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakileri yaptığınızdan emin olun:

1. [Öğretici: Azure Data Box sipariş etme](data-box-deploy-ordered.md) bölümünü tamamladınız.
2. Data Box’ı teslim aldınız ve portaldaki sipariş durumu **Teslim Edildi** oldu. 
3. [Data Box güvenlik yönergelerini](data-box-safety.md) gözden geçirdiniz.
4. 100 TB depolama cihazınızla birlikte kullanılacak bir topraklanmış güç kablosu aldınız.
5. Data Box üzerinden kopyalamak istediğiniz verileri içeren bir ana bilgisayara erişiminiz var. Ana bilgisayarınız:
    - [Desteklenen bir işletim sistemi](data-box-system-requirements.md) çalıştırılmalıdır.
    - Yüksek hızlı ağa bağlısınız. En az bir adet 10 GbE bağlantınızın olması önemle tavsiye edilir. 10 GbE bağlantı yoksa, 1 GbE veri bağlantısı kullanılabilir ancak kopyalama hızı etkilenir. 
6. Data Box’ı yerleştirmek için düz bir yüzeye erişiminiz var. Cihazı standart bir rafa dik ya da düz bir şekilde yerleştirmek istiyorsanız, rafınızda bir 7U yuvası olmalıdır.

::: zone-end

::: zone target="docs"

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="order"></a>Sipariş verme

Bu adım yaklaşık 5 dakika sürer.

1. Azure portalında yeni bir Azure Data Box kaynağı oluşturun.
2. Bu hizmetin etkinleştirildiği mevcut aboneliği seçin ve aktarım türünü **İçeri aktarma** olarak belirleyin. Verilerin bulunduğu **Kaynak ülkeyi** ve veri aktarımı için **Azure hedef bölgesini** seçin.
3. **Data Box**'ı seçin. Maksimum kullanılabilir kapasite 80 TB'tır ve daha büyük veri boyutları için birden çok sipariş oluşturabilirsiniz.
4. Sipariş ayrıntılarını ve sevkiyat bilgilerini girin. Hizmet bölgenizde kullanılabilir durumdaysa bildirim e-posta adreslerini girin, özeti gözden geçirin ve siparişi oluşturun.

Sipariş oluşturulduktan sonra cihaz gönderilmek üzere hazırlanır.



## <a name="cable"></a>Kablo 

Bu adım yaklaşık 10 dakika sürer.

Data Box elinize ulaştığında, cihazı kablolamak, bağlamak ve iade etmek için aşağıdaki adımları izleyin. Bu adım yaklaşık 10 dakika sürer.

1. Cihazla oynandığına veya cihazın hasarlı olduğuna ilişkin bir kanıt varsa, devam etmeyin. Size yeni bir cihaz gönderilmesi için Microsoft Desteği'ne başvurun.
2. Cihazınızın kablolarını takmadan önce aşağıdaki kablolara sahip olduğunuzdan emin olun:
    
    - (Dahil edilmiş) cihaza bağlamak için bir ucunda IEC60320 C-13 bağlayıcısı bulunan, en az 10 A gücünde topraklanmış bir güç kablosu.
    - Bir RJ-45 CAT 6 ağ kablosu (MGMT ağ arabirimi ile kullanın)
    - İki adet 10 GbE SFP+ Twinax bakır kablo (10 Gb/sn DATA 1, DATA 2 ağ arabirimleri ile kullanın)
    - Bir RJ-45 CAT 6A VEYA bir RJ-45 CAT 6 ağ kablosu (sırasıyla 10 Gb/sn veya 1 Gb/sn olarak yapılandırılmış DATA 3 ağ arabirimi ile birlikte kullanın)

3. Cihazı kaldırın ve düz bir yüzeye yerleştirin. 
    
4. Cihazın kablolarını aşağıda gösterildiği gibi takın.  

    ![Kablo bağlantısı yapılmış Data Box cihaz devre kartı](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Güç kablosunu cihaza bağlayın.
    2. Konak bilgisayarınızı cihazdaki yönetim bağlantı noktasına (MGMT) bağlamak için bir RJ 45 CAT 6 ağ kablosu kullanın. 
    3. Veriler için en az 10 Gb/sn'lik (1 Gb/sn'ye tercih edilir) ağ arabirimini, DATA 1 veya DATA 2'yi bağlamak üzere bir SFP + Twinax bakır kablo kullanın. 
    4. Cihazı açın. Güç düğmesi cihazın ön panelindedir.


## <a name="connect"></a>Bağlan

Bu adımın tamamlanması yaklaşık 5-7 dakika sürer.

1. Cihaz parolasını almak için [Azure portalında](https://portal.azure.com) **Genel > Cihaz ayrıntıları**'na gidin.
2. Data Box'a bağlanmak için kullandığınız bilgisayardaki Ethernet bağdaştırıcısına 192.168.100.5 statik IP adresini ve 255.255.255.0 alt ağını atayın. Cihazın yerel web kullanıcı arabirimine `https://192.168.100.10` adresinden erişin. Siz cihazı açtıktan sonra bağlantı kurulması 5 dakika kadar sürebilir. 
3. Azure portalından alınan parolayı kullanarak oturum açın. Web sitesinin güvenlik sertifikasında sorun olduğunu belirten bir hata görürsünüz. Web sayfasına ilerlemek için tarayıcıya özel yönergeleri izleyin.
4. Varsayılan olarak, 10 Gb/sn (veya 1 Gb/sn) veri arabirimi için ağ ayarları DHCP olarak yapılandırılır. Gerekirse, bu arabirimi statik olarak yapılandırabilir ve bir IP adresi sağlayabilirsiniz. 

## <a name="copy-data"></a>Veri kopyalama

Bu işlemi tamamlamak için gereken süre verilerinizin boyutuna ve ağın hızına göre değişir.
 
1. Bir Windows konağı kullanıyorsanız, Robocopy gibi SMB uyumlu bir dosya kopyalama aracı kullanın. NFS konağı için, `cp` veya `rsync` komutunu kullanarak verileri kopyalayın. Aracı cihazınıza bağlayın ve verileri paylaşımlara kopyalamaya başlayın. Robocopy kullanarak veri kopyalama hakkında daha fazla bilgi için [Robocopy](https://technet.microsoft.com/library/ee851678.aspx) adresine gidin.
2. Paylaşımlara bağlanmak için şu yolu kullanın: `\\<IP address of your device>\ShareName`. Paylaşım erişimi kimlik bilgilerini almak için Data Box'ın yerel web kullanıcı arabiriminde **Bağlan ve kopyala** sayfasına gidin.
3. Paylaşım ve klasör adlarıyla verilerin, [Azure Depolama ve Data Box hizmet sınırları](data-box-limits.md) altında açıklanan yönergelere uyduğundan emin olun.

## <a name="ship-to-azure"></a>Azure'a gönderme 

Bu işlemin tamamlanması yaklaşık 10-15 dakika kadar sürer.

1. Yerel web kullanıcı arabirimindeki **Göndermeye hazırla** sayfasına gidin ve gönderme hazırlığına başlayın. 
2. Cihazı yerel web kullanıcı arabiriminden kapatın. Kabloları cihazdan çıkarın. 
3. İade gönderim etiketinin E-ink ekranında görünür olduğundan emin olun. E-ink ekranında etiket görüntülenmiyorsa, Azure portalından gönderim etiketini indirin ve cihaza takılı boş kısma ekleyin.
4. Kutuyu kilitleyin ve Microsoft'a gönderin. 

## <a name="verify-data"></a>Verileri doğrulama

Bu işlemi tamamlamak için gereken süre verilerinizin boyutuna göre değişir.

1. Data Box cihazı, Azure veri merkezi ağına bağlandığında veriler otomatik olarak Azure'a yüklenir. 
2. Veri kopyalama işlemi tamamlandığında Azure Data Box hizmeti Azure portaldan bildirim gönderir. 

    1. Hata günlüklerini kontrol ederek hata olup olmadığını kontrol edin ve gerekli eylemleri gerçekleştirin.
    2. Kaynaktan silmeden önce verilerinizin depolama hesaplarında olduğundan emin olun.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu adımın tamamlanması 2-3 dakika sürer.

- Data Box siparişini işleme alınmadan önce Azure portaldan iptal edebilirsiniz. Siparişler işleme alındıktan sonra iptal edilemez. Sipariş, tamamlanma aşamasına gelene kadar ilerler. Siparişi iptal etmek için **Genel bakış**'a gidin ve komut çubuğundan **İptal**'e tıklayın.

- Siparişin durumu **Tamamlandı** veya **İptal edildi** olduğunda Azure portaldan silebilirsiniz. Siparişi silmek için **Genel bakış**'a gidin ve komut çubuğundan **Sil**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Azure'a veri aktarımı konusunda yardım almak için Azure Data Box'ı dağıttınız. Azure Data Box yönetimi hakkında daha fazla bilgi edinmek için aşağıdaki öğreticiye geçin: 

> [!div class="nextstepaction"]
> [Azure portalını kullanarak Data Box'ı yönetme](data-box-portal-admin.md)

::: zone-end


