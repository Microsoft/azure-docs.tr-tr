---
title: Azure Data Box Heavy ayarlama öğreticisi| Microsoft Docs
description: Azure Data Box Heavy için kabloları bağlamayı ve bağlanmayı öğrenin
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 3b27a4788bd144a8d2d423af1fa23ef09c5379f8
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772845"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Öğretici: Azure Data Box Heavy için kabloları bağlama ve bağlanma

::: zone-end

::: zone target = "chromeless"

# <a name="get-started-with-azure-data-box-heavy"></a>Azure Data Box Heavy’yi kullanmaya başlayın

::: zone-end

::: zone target = "docs"

Bu öğreticide, Azure Data Box Heavy için kabloları bağlama, bağlanma ve çalıştırma işlemleri açıklanmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Data Box Heavy’nin kablolarını bağlama
> * Data Box Heavy’ye bağlanma

::: zone-end

::: zone target = "chromeless"

Bu kılavuzda önkoşulları inceleme, cihazınızın kablolarını bağlama ve cihazınızı bağlama, verileri kopyalama, Azure'a yükleme ve sonra yüklenen verileri doğrulama hakkında yönergeler sunulmaktadır.

::: zone-end

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. [Öğretici: Azure Data Box Heavy sipariş etme](data-box-heavy-deploy-ordered.md)’yi tamamladınız.
2. Data Box Heavy’yi teslim aldınız ve portaldaki sipariş durumu **Teslim Edildi** oldu.
3. [Data Box Heavy güvenlik yönergelerini](data-box-safety.md) gözden geçirdiniz.
4. Bu ayak izine sahip bir cihaza uyum sağlayabilecek kullanılabilir bir ağ bağlantısına yakın olan veri merkezindeki düz bir siteye erişiminizin olması gerekir. Bu cihaz bir rafa bağlanamaz.
5. Depolama cihazınızla birlikte kullanılacak dört topraklanmış güç kablosu aldınız.
6. Veri merkezi ağına bağlı bir konak bilgisayarınız olmalıdır. Data Box Heavy, verileri bu bilgisayardan kopyalayacaktır. Ana bilgisayarınızın [Desteklenen bir işletim sistemi](data-box-heavy-system-requirements.md) çalıştırması gerekir.
7. Veri merkezinizin yüksek hızlı ağı olmalıdır. En az bir adet 10 GbE bağlantınızın olması önemle tavsiye edilir. 
8. Yerel kullanıcı arabirimine bağlanmak ve cihazı yapılandırmak için RJ-45 kablosu olan bir dizüstü bilgisayar gereklidir. Cihazın her düğümünü bir kez yapılandırmak için dizüstü bilgisayarı kullanın.
9. Cihaz düğümü başına bir 40 Gb/sn kablo veya 10 Gb/sn kablo gerekir.
    - [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) ağ arabirimi ile uyumlu kabloları seçin.
    - 40 Gb/sn kablonun cihaz tarafındaki ucunun QSFP+ olması gerekir.
    - 10 GB/sn kablo için, bir ucu 10 Gb/sn kabloya bağlanan bir SFP+ kablo ve cihaza takılan uç için QSFP+’dan SFP+’ya bağdaştırıcı (veya QSA bağdaştırıcısı) gerekir.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Cihazınızın güç kablolarını bağlama

Cihazınızın kablolarını bağlamak için aşağıdaki adımları uygulayın.

1. Üzerinde oynanıp oynanmadığını veya başka herhangi bir hasarı olup olmadığını anlamak için cihazı inceleyin. Cihaz kurcalanmış ya da ciddi hasar görmüşse devam etmeyin. Cihazın düzgün çalışır durumda olup olmadığını ve yerine başka bir cihazın gönderilmesinin gerekip gerekmediğini değerlendirmenize yardımcı olmaları için hemen [Microsoft Desteği'ne başvurun](data-box-disk-contact-microsoft-support.md).
2. Cihazı kurulum alanına taşıyın.

    ![Data Box Heavy cihaz kurulum alanı](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Cihazın arka tekerleklerini aşağıda gösterildiği gibi kilitleyin.

    ![Data Box Heavy cihaz tekerlekleri kilitli](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Cihazın ön ve arka kapılarının kilidini açan düğmeleri bulun. Cihazın yanına yaslanıncaya kadar ön kapıyı açın ve hareket ettirin. Bu işlemi arka kapıyla da yineleyin.
    Cihazın içinden önden arkaya doğru ideal hava akışını sağlamak için cihaz çalışırken her iki kapının da açık olması gerekir.

    ![Data Box Heavy kapıları açık](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. Cihazın arkasında bulunan tepside dört güç kablosu olmalıdır. Tepsideki tüm kabloları çıkarın ve bir kenara koyun.

    ![Data Box Heavy tepsisindeki güç kabloları](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. Sonraki adımda cihazın arkasındaki çeşitli bağlantı noktalarını belirlenmelidir. **NODE1** ve **NODE2** şeklinde iki cihaz düğümü vardır. Her düğümde **MGMT**, **DATA1**, **DATA2**, **DATA3** şeklinde dört ağ arabirimi bulunur. **MGMT**, cihazın ilk yapılandırması sırasında yönetimi yapılandırmak için kullanılır. **DATA1**-**DATA3** veri bağlantı noktalarıdır. **MGMT** ve **DATA3** bağlantı noktaları 1 Gb/sn’dir, **DATA1** ile **DATA2** ise 40 Gb/sn bağlantı noktaları veya 10 Gb/sn bağlantı noktaları olarak çalışabilir. İki cihaz düğümünün en altında, iki cihaz düğümü arasında paylaşılan dört güç kaynağı birimi (PSU) vardır. Yüzünüz bu cihaza dönük olduğunda **PSU’lar** soldan sağa **PSU1**, **PSU2**, **PSU3** ve **PSU4** şeklinde görünür.

    ![Data Box Heavy bağlantı noktaları](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Dört güç kablosunun tamamını cihaz güç kaynaklarına bağlayın. Yeşil LED'ler açılır ve yanıp söner.
8. Cihaz düğümlerini açmak için ön düzlemdeki güç düğmelerini kullanın. Mavi ışıklar açılana kadar güç düğmesini birkaç saniye boyunca basılı tutun. Cihazın arkasında bulunan güç kaynaklarının tüm yeşil LED'leri kesintisiz yanıyor olmalıdır. Cihazın ön işletim panelinde de hata LED'leri bulunur. Hata LED’i yandığında hatalı bir PSU veya bir fan ya da disk sürücüleriyle ilgili bir sorun olduğunu gösterir.  

    ![Data Box Heavy ön işletim paneli](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Ağ için ilk düğümün kablo bağlantısını yapma

Cihazın düğümlerinden birinde, ağ için kablo bağlantısını yapmak üzere aşağıdaki adımları uygulayın.

1. Ana bilgisayarı 1 Gb/sn yönetim bağlantı noktasına bağlamak için CAT 6 RJ-45 ağ kablosunu (resimdeki mavi kablo) kullanın.
2. Veriler için en az bir 40 Gb/sn (1 Gb/sn’ye tercih edilir) ağ arabirimi bağlamak üzere bir QSFP+ kablosu (fiber veya bakır) kullanın. 10 Gb/sn'lik bir anahtar kullanılıyorsa, veriler için 40 Gb/sn ağ arabirimini bağlamak üzere QSFP+’dan SFP+’ya bağdaştırıcı (QSA bağdaştırıcısı) ile bir SFP+ kablosu kullanın.

    ![Data Box Heavy bağlantı noktaları kablo bağlantısı](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA 1 ve DATA2 anahtarlanır ve yerel web kullanıcı arabiriminde gösterilenlerle eşleşmez.
    > Aşağıda gösterildiği gibi takıldığında 40 Gb/sn bağdaştırıcı bağlanır.

    ![Data Box Heavy 40 Gb/sn kablo bağdaştırıcısı](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>İlk düğümü yapılandırma

Yerel yapılandırma ve Azure portalını kullanarak cihazınızı ayarlamak için aşağıdaki adımları uygulayın.

1. Portaldan cihaz kimlik bilgilerini indirin. **Genel > Cihaz ayrıntıları**’na gidin. **Cihaz parolası**’nı kopyalayın. Bu parolalar portalda belirli bir sıraya bağlıdır. Data Box Heavy’de iki düğüme karşılık iki cihaz seri numarasını göreceksiniz. Her iki düğüm için de cihaz yöneticisi parolası aynıdır.

    ![Data Box Heavy cihazı kimlik bilgileri](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. İstemci iş istasyonunuzu CAT6 RJ-45 ağ kablosu aracılığıyla cihaza bağlayın.
3. Cihazı `192.168.100.5` statik IP adresine ve `255.255.255.0` alt ağına bağlamak için kullanmakta olduğunuz bilgisayarda Ethernet bağdaştırıcısını yapılandırın.

    ![Data Box Heavy, yerel web Kullanıcı arabirimine bağlanır](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Cihazın şu URL’deki yerel web kullanıcı arabirimine bağlanın: `http://192.168.100.10`. **Gelişmiş** ve ardından **192.168.100.10’a ilerle (güvenli değil)** seçeneğine tıklayın.
5. Yerel web kullanıcı arabiriminin **Oturum açma** sayfasını görürsünüz.
    
    - Bu sayfadaki düğüm seri numaralarından biri, hem portal kullanıcı arabirimi hem de yerel web kullanıcı arabiriminde eşleşir. Düğüm numarası ile seri numarası eşlemesini not alın. Portalda iki düğüm ve iki cihaz seri numarası bulunur. Bu eşleme hangi düğümün hangi seri numarasına karşılık geldiğini anlamanıza yardımcı olur.
    - Bu noktada cihaz kilitlidir.
    - Cihazda oturum açmak için önceki adımda edindiğiniz cihaz yöneticisi parolasını sağlayın. **Oturum aç**’a tıklayın.

    ![Data Box Heavy yerel web kullanıcı arabiriminde oturum açın](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Pano’da ağ arabirimlerinin yapılandırıldığından emin olun. Cihaz düğümünüzde ikisi 1 Gb/sn ve ikisi 40 Gb/sn olmak üzere dört ağ arabirimi vardır. 1 Gb/sn arabirimlerden biri bir yönetim arabirimidir ve bu nedenle kullanıcı tarafından yapılandırılamaz. Kalan üç ağ arabirimi verilere ayrılmıştır ve kullanıcı tarafından yapılandırılabilir.

- Ortamınızda DHCP etkinse, ağ arabirimleri otomatik olarak yapılandırılır.
- DHCP etkin değilse, Ağ arabirimlerini ayarla’ya gidin ve gerekirse statik IP’ler atayın.

    ![Data Box Heavy pano düğümü 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>İkinci düğümü yapılandırma

Cihazın ikinci düğümü için [Birinci düğümü yapılandırma](#configure-first-node) bölümünde açıklanan adımları uygulayın.

![Data Box Heavy pano düğümü 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Cihaz kurulumu tamamlandıktan sonra cihaz paylaşımlarına bağlanabilir ve verileri bilgisayarınızdan cihaza kopyalayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide Azure Data Box Heavy ile ilgili aşağıdaki bilgileri öğrendiniz:

> [!div class="checklist"]
> * Data Box Heavy’nin kablolarını bağlama
> * Data Box Heavy’ye bağlanma

Data Box Heavy'ye verileri kopyalama hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Data Box'a verilerinizi kopyalama](./data-box-heavy-deploy-copy-data.md)

::: zone-end
