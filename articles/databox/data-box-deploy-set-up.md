---
title: Azure Data Box ayarlama öğreticisi| Microsoft Docs
description: Azure Data Box’ınız için kabloları bağlamayı ve bağlanmayı öğrenin
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: b9d724a44a94ed13786391a235bb9967036f29dc
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338491"
---
::: zone target="docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Öğretici: Azure Data Box için kabloları bağlama ve bağlanma

::: zone-end

::: zone target="chromeless"

# <a name="cable-and-connect-to-your-device"></a>Kabloları bağlama ve cihazınıza bağlanma

::: zone-end

::: zone target="docs"

Bu öğreticide, Azure Data Box’ınız için kabloları bağlama, bağlanma ve çalıştırma işlemleri açıklanmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Data Box’ınızın kablolarını bağlama
> * Data Box’ınıza bağlanma

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. [Öğretici: Azure Data Box sipariş etme](data-box-deploy-ordered.md) bölümünü tamamladınız.
2. Data Box’ınızı teslim aldınız ve portaldaki sipariş durumu **Teslim Edildi** oldu. 
    - Cihazın üzerindeki etiketin altındaki şeffaf bölüme yerleştirilmiş bir sevkiyat etiketi vardır. Bu etiketi iade için kullanmak üzere saklayın.
    - Avrupa'daki bazı bölgelerde cihaz bir kutu içinde gönderilebilir. Cihazı paketten çıkarın ve iade gönderimi için kutuyu saklayın.
3. [Data Box güvenlik yönergelerini](data-box-safety.md) gözden geçirdiniz.
4. 100 TB depolama cihazınızla birlikte kullanılacak bir topraklanmış güç kablosu aldınız.
5. Data Box üzerinden kopyalamak istediğiniz verileri içeren bir ana bilgisayarınız var. Ana bilgisayarınız:
    - [Desteklenen bir işletim sistemi](data-box-system-requirements.md) çalıştırılmalıdır.
    - Yüksek hızlı ağa bağlısınız. En az bir adet 10 GbE bağlantınızın olması önemle tavsiye edilir. 10 GbE bağlantı yoksa, 1 GbE veri bağlantısı kullanılabilir ancak kopyalama hızı etkilenir. 
6. Data Box’ı yerleştirebileceğiniz düz bir yüzeye erişiminiz olmalıdır. Cihazı standart bir rafa yerleştirmek istiyorsanız, veri merkezi rafınızda bir 7U yuvası olmalıdır. Cihazı düz veya dik şekilde rafa yerleştirebilirsiniz.
7. Data Box'ınızı ana bilgisayara bağlamak için aşağıdaki kabloları temin ettiniz.
    - Bir veya daha fazla 10 GbE SFP+ Twinax bakır ya da SFP+ fiber optik kablo (DATA 1, DATA 2 ağ arabirimleri ile kullanın). Data Box; PCI Express 3.0 ağ arabirimi ile birlikte Mellanox ConnectX®-3 Pro EN Dual-Port 10GBASE-T Bağdaştırıcılara sahiptir; bu nedenle bu arabirimle uyumlu kablolar çalışmalıdır. Örneğin, şirket içi testlerde bir SFP-H10GB-CU3M 10GBASE-CU TWINAX SFP +3M kablo kullanılmıştır. Daha fazla bilgi için bkz. [Desteklenen Mellanox kablo ve anahtarlarının listesi](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf).
    - Bir RJ-45 CAT 6 ağ kablosu (MGMT ağ arabirimi ile kullanın)
    - Bir RJ-45 CAT 6A VEYA bir RJ-45 CAT 6 ağ kablosu (sırasıyla 10 Gb/sn veya 1 Gb/sn olarak yapılandırılmış DATA 3 ağ arabirimi ile birlikte kullanın)

## <a name="cable-your-device"></a>Cihazınızın kablolarını bağlama

Cihazınızın kablolarını bağlamak için aşağıdaki adımları uygulayın.

1. Üzerinde oynanıp oynanmadığını veya başka herhangi bir hasarı olup olmadığını anlamak için cihazı inceleyin. Cihaz kurcalanmış ya da ciddi hasar görmüşse devam etmeyin. Cihazın düzgün çalışır durumda olup olmadığını ve yerine başka bir cihazın gönderilmesinin gerekip gerekmediğini değerlendirmenize yardımcı olmaları için hemen Microsoft Desteği'ne başvurun.
2. Cihazı çalıştırılmasını istediğiniz konuma taşıyın. Cihazı düz bir yüzeye yerleştirin. Cihaz standart bir rafa da yerleştirilebilir.
3. Güç ve ağ kablolarını bağlayın. Genel bir yapılandırma için bağlı bir cihazın devre kartı aşağıda gösterilmiştir. Ortamınıza bağlı olarak diğer [kablolama seçenekleri](data-box-cable-options.md) arasından seçim yapabilirsiniz.
    
    ![Kablo bağlantısı yapılmış Data Box cihaz devre kartı](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Güç kablosunu etiketli güç girişi konumuna bağlayın. Güç kablosunun diğer ucu bir güç dağıtım birimine bağlı olmalıdır.
    2. RJ-45 CAT 6 kablosunu kullanarak MGMT bağlantı noktasını bir uca, dizüstü bilgisayarı ise diğer uca bağlayın.            
    3. RJ-45 CAT 6A kablosunun bir ucunu DATA 3 bağlantı noktasına bağlayın. RJ-45 CAT 6A kablosu ile bağlarsanız DATA 3 10 GbE olarak, RJ-45 CAT 6 kablosuyla bağlarsanız 1 GbE olarak yapılandırılır.
    4. Veri aktarımı için bağlanmak istediğiniz ağ arabirimlerine bağlı olarak, sırasıyla DATA 1 ve DATA 2 bağlantı noktalarını bağlamak için en fazla iki adet 10 GbE SFP+ Twinax bakır veya SFP+ fiber optik kablo kullanın. 
    5. Veri bağlantı noktalarından çıkan kabloların diğer ucu 10 GbE anahtar ile ana bilgisayara bağlanır.

4. Cihazın ön çalışma panelindeki güç düğmesini bulun. Cihazı açın.

    ![Data Box güç düğmesi](media/data-box-deploy-set-up/data-box-powered-door-open.png)

::: zone-end

::: zone target="chromeless"

Cihazı aldıktan sonra kablo bağlantılarını yapmanız ve cihazınıza bağlanmanız gerekir. 

## <a name="cable-your-device"></a>Cihazınızın kablolarını bağlama

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

::: zone-end

::: zone target="docs"


## <a name="connect-to-your-device"></a>Cihazınıza bağlanma

Yerel web kullanıcı arabirimi ve portal kullanıcı arabirimini kullanarak cihazınızı ayarlamak için aşağıdaki adımları uygulayın.

1. Cihaza 192.168.100.5 statik IP adresi ve 255.255.255.0 alt ağı ile bağlanmak için kullanmakta olduğunuz dizüstü bilgisayardaki Ethernet bağdaştırıcısını yapılandırın. 
2. Cihazınızın MGMT bağlantı noktasına bağlanın ve https\://192.168.100.10 konumundaki yerel web kullanıcı arabirimine erişin. Bu işlem, cihaz açıldıktan sonra 5 dakika sürebilir.
3. **Ayrıntılar**’a ve sonra **Web sayfasına gidin**’e tıklayın.

   ![Yerel web kullanıcı arabirimine bağlanma](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. Yerel web kullanıcı arabiriminin **Oturum açma** sayfasını görürsünüz. Portal kullanıcı arabiriminde ve yerel web kullanıcı arabiriminde cihaz seri numarasının eşleştiğinden emin olun. Bu noktada cihaz kilitlidir.
5. [Azure portal](https://portal.azure.com) oturum açın.
6. Portaldan cihaz kimlik bilgilerini indirin. **Genel > Cihaz ayrıntıları**’na gidin. **Cihaz parolası**’nı kopyalayın. Cihaz parolası, portalda belirli bir sıraya bağlıdır. 

    ![Cihaz kimlik bilgilerini alma](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. Cihazın yerel web kullanıcı arabiriminde oturum açmak için önceki adımda Azure portalından aldığınız cihaz parolasını sağlayın. **Oturum aç**’a tıklayın.
8. **Pano**’da ağ arabirimlerinin yapılandırıldığından emin olun. 
   - Ortamınızda DHCP etkinse, ağ arabirimleri otomatik olarak yapılandırılır. 
   - DHCP etkin değilse, **Ağ arabirimlerini ayarla**’ya gidin ve gerekirse statik IP’ler atayın.

     ![Cihaz panosu](media/data-box-deploy-set-up/data-box-dashboard-1.png)

Veri ağı arabirimleri yapılandırıldıktan sonra DATA 1 - DATA 3 arabirimlerinden herhangi birinin IP adresini kullanarak `https://<IP address of a data network interface>` adresinden yerel web arabirimine erişebilirsiniz. 

Cihaz kurulumu tamamlandıktan sonra cihaz paylaşımlarına bağlanabilir ve verileri bilgisayarınızdan cihaza kopyalayabilirsiniz. 

::: zone-end

::: zone target="chromeless"

## <a name="connect-your-device"></a>Cihazınızı bağlama

1. Cihaz parolasını almak için [Azure portalında](https://portal.azure.com) **Genel > Cihaz ayrıntıları**'na gidin.
2. Data Box'a bağlanmak için kullandığınız bilgisayardaki Ethernet bağdaştırıcısına 192.168.100.5 statik IP adresini ve 255.255.255.0 alt ağını atayın. Cihazın yerel web kullanıcı arabirimine `https://192.168.100.10` adresinden erişin. Siz cihazı açtıktan sonra bağlantı kurulması 5 dakika kadar sürebilir. 
3. Azure portalından alınan parolayı kullanarak oturum açın. Web sitesinin güvenlik sertifikasında sorun olduğunu belirten bir hata görürsünüz. Web sayfasına ilerlemek için tarayıcıya özel yönergeleri izleyin.
4. Varsayılan olarak, 10 Gb/sn (veya 1 Gb/sn) veri arabirimi için ağ ayarları DHCP olarak yapılandırılır. Gerekirse, bu arabirimi statik olarak yapılandırabilir ve bir IP adresi sağlayabilirsiniz. 

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki Azure Data Box konularını öğrendiniz:

> [!div class="checklist"]
> * Data Box’ınızın kablolarını bağlama
> * Data Box’ınıza bağlanma

Data Box'ınıza verileri kopyalama hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Data Box'a verilerinizi kopyalama](./data-box-deploy-copy-data.md)

::: zone-end

