---
title: Azure Stack Edge Mini R fiziksel cihazı yüklemeye yönelik öğretici | Microsoft Docs
description: Azure Stack Edge Mini R cihazının yüklenmesiyle ilgili ikinci öğretici, fiziksel cihazın güç ve ağ için nasıl kablolanmasını içerir.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 8b154dabd6f672c6fdaf77c5f8d48f80fb40d5d8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060117"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>Öğretici: Azure Stack Edge Mini R 'Yi yükler

Bu öğreticide, Azure Stack Edge Mini R fiziksel cihazının nasıl yükleneceği açıklanmaktadır. Yükleme yordamı, cihazı kablolama ile ilgilidir.

Yüklemenin tamamlanması yaklaşık 30 dakika sürebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Cihazı İnceleme
> * Cihazın kablolarını bağlama

## <a name="prerequisites"></a>Önkoşullar

Fiziksel bir cihazı yükleme önkoşulları şunlardır:

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge kaynağı için

Başlamadan önce aşağıdakilerden emin olun:

* [Azure Stack Edge Mini R 'yi dağıtmaya hazırlanma](azure-stack-edge-mini-r-deploy-prep.md)bölümündeki tüm adımları tamamladınız.
    * Cihazınızı dağıtmak için bir Azure Stack Edge kaynağı oluşturdunuz.
    * Cihazınızı Azure Stack Edge kaynağıyla etkinleştirmek için etkinleştirme anahtarını oluşturdunuz.

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>Azure Stack Edge Mini R fiziksel cihazı için

Cihazı dağıtmadan önce:

- Cihazın düz, kararlı ve düzeyi bir iş yüzeyine güvenli bir şekilde oturduğu emin olun.
- Kurulumu gerçekleştirmeyi planladığınız yerde şunların bulunduğundan emin olun:
    - Bağımsız bir kaynaktan standart AC gücü-veya-
    - Bir raf güç dağıtım birimi (PDU). 
    

### <a name="for-the-network-in-the-datacenter"></a>Veri merkezindeki ağ için

Başlamadan önce:

- Azure Stack Edge Mini R 'yi dağıtmaya yönelik ağ gereksinimlerini gözden geçirin ve veri merkezi ağını gereksinimlere göre yapılandırın. Daha fazla bilgi için bkz. [Edge ağ gereksinimleri Azure Stack](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements).

- Cihazın en iyi şekilde çalışması için en düşük Internet bant genişliğinin 20 Mbps olduğundan emin olun. <!-- engg TBC -->


## <a name="inspect-the-device"></a>Cihazı İnceleme

Bu cihaz tek bir birim olarak sevk edilir. Cihazınızı kutusundan çıkarmak için aşağıdaki adımları gerçekleştirin.

1. Kutuyu düz ve sabit bir yüzeye yerleştirin.
2. Herhangi bir hasar için cihaz durumunu inceleyin. Büyük/küçük harf durumunu açın ve cihazı inceleyin. Büyük/küçük harf veya cihaz hasar görmüşse, cihazın iyi çalışma sırasında olup olmadığını değerlendirmenize yardımcı olmak için Microsoft Desteği başvurun.
3. Durum açıldıktan sonra, şunları yaptığınızdan emin olun:
    - Yan sekmeli kafaları eklenmiş bir taşınabilir Azure Stack Edge Mini R cihazı
    - Cihaza bağlı bir pil ve arka kapak. 
    - Pili güç kaynağına bağlamak için bir güç kablosu 

Burada listelenen öğelerin tümünü almadıysanız Azure Stack Edge desteğiyle iletişim kurun. Bir sonraki adım cihazınızı kablolayın.


## <a name="cable-the-device"></a>Cihazın kablolarını bağlama

Aşağıdaki yordamlarda Azure Stack Edge cihazınızın güç ve ağ için nasıl kablo yapılacağı açıklanmaktadır.

Cihazınızı kablolamayı başlatmaya başlamadan önce şunlar gerekir:

- Yükleme sitesinde Azure Stack Edge Mini R fiziksel cihazınız.
- Bir güç kablosu.
- Yönetim arabirimine bağlamak için en az bir 1-GbE RJ-45 ağ kablosu. Cihazda biri yönetim ve diğeri veri olmak üzere iki 1-GbE ağ arabirimi vardır.
- Yapılandırılacak her veri ağı arabirimi için 1 10-GbE SFP + bakır kablo. Bağlantı noktası 3 veya bağlantı noktası 4 arasından en az bir veri ağı arabiriminin Internet 'e bağlanması gerekir (Azure bağlantısı ile).  
- Bir güç dağıtım birimine erişim (önerilir).

> [!NOTE]
> - Yalnızca bir veri ağı arabirimini bağlıyorsanız, Azure 'a veri göndermek için bağlantı noktası 3 veya bağlantı noktası 4 gibi 10 GbE bir ağ arabirimi kullanmanızı öneririz. 
> - En iyi performansı elde etmek ve büyük miktarda veriyi işlemek için tüm veri bağlantı noktalarını bağlamak isteyebilirsiniz.
> - Azure Stack Edge cihazının veri kaynağı sunucularından veri alabilmesi için veri merkezi ağına bağlı olması gerekir. <!-- engg TBC -->

Azure Stack Edge cihazınızda:

- Ön panelde SSD taşıyıcısı vardır. 

    - Cihazda yuvada 1 SSD disk vardır. 
    - Cihazda Ayrıca, işletim sistemi diski için depolama görevi gören bir CFx kartı bulunur.
    
- Ön panelde ağ arabirimleri ve Wi-Fi erişimi vardır.

    - 2 X 1 GbE RJ 45 ağ arabirimi. Bunlar, cihazın yerel kullanıcı arabirimindeki bağlantı noktası 1 ve bağlantı noktası 2 ' dir.
    - 2 X 10 GbE SFP + ağ arabirimleri. Bunlar, cihazın yerel kullanıcı arabiriminde bağlantı noktası 3 ve bağlantı noktası 4 ' dir. 
    - Kendisine bağlı bir Wi-Fi alıcısı olan bir Wi-Fi bağlantı noktası.

- Ön panelde da bir güç düğmesi bulunur. 

- Arka panel, cihaza yüklenmiş bir pil ve bir kapak içerir. 


Cihazınızı güç ve ağ için kablolu yapmak üzere aşağıdaki adımları uygulayın.

1. Cihazınızın ön düzleinde çeşitli ağ ve depolama bileşenlerini belirler.

    ![Cihazdaki ağ ve depolama arabirimleri](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. Cihazın önünü sol alt köşesinde bulunan güç düğmesini bulun. 

    ![Cihazda güç düğmesi olan bir cihazın ön düzlemi](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. Pil, cihazınızın arka düzlemine bağlanır. Pilde bulunan ikinci güç düğmesini belirler. 

    ![Pilde güç düğmesi olan bir cihazın ön düzlemi](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    Güç kablosunu bir uca pil ve diğer güç prizine bağlayın. 

    ![Güç kablosu bağlantısı](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    Yalnızca pille çalışırken (pil, güç kaynağına bağlı değilse), hem ön uçın hem de anahtardaki güç anahtarı konuma göre değiştirilmelidir. Pil bir güç kaynağına bağlıyken, yalnızca cihazın önündeki güç düğmesi, konuma göre değiştirilmelidir. 

4. Cihazı açmak için ön düzlemdeki güç düğmesine basın. 
    
    > [!NOTE]
    > Cihaza gücü açmak veya kapatmak için, güç düğmesinin üzerine siyah düğmesini basılı olarak açıp güç düğmesini açık veya kapalı konumuna getirin. 

5. Bu cihazda Wi-Fi yapılandırıyorsanız, aşağıdaki kablo diyagramını kullanın:

    ![Wi-Fi için kablolama](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - 1-GbE ağ arabirimi bağlantı noktası 1 ' i fiziksel cihazı yapılandırmak için kullanılan bilgisayara bağlayın. PORT 1, yönetim için ayrılmış arabirimdir.


    Bu cihaz için kablolu bir yapılandırma kullanıyorsanız, aşağıdaki diyagramı kullanın:
     
    ![Kablolu için kablolama](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - 1-GbE ağ arabirimi bağlantı noktası 1 ' i fiziksel cihazı yapılandırmak için kullanılan bilgisayara bağlayın. PORT 1, yönetim için ayrılmış arabirimdir.
    - Bir veya daha fazla bağlantı noktası 2, bağlantı noktası 3 veya bağlantı noktası 4 ' ü veri merkezi ağına/Internete bağlayın.
    
        - PORT 2’yi bağlıyorsanız, RJ-45 ağ kablosunu kullanın.
        - 10 GbE ağ arabirimleri için, SFP + bakır kablolarını kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şu konularda nasıl yapılacağını Azure Stack öğrendiniz:

> [!div class="checklist"]
> * Cihazı kutusundan çıkarma
> * Cihazın kablolarını bağlama

Cihazınıza bağlanma, kurulumunu yapma ve etkinleştirme adımları için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Stack Edge 'i bağlama ve ayarlama](./azure-stack-edge-mini-r-deploy-connect.md)
