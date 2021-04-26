---
title: Azure Stack Edge Pro R fiziksel cihazı yüklemeye yönelik öğretici | Microsoft Docs
description: Azure Stack Edge Pro R 'yi yükleme hakkında ikinci öğretici, fiziksel cihazın güç ve ağ için nasıl kablolanmasını içerir.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
ms.openlocfilehash: c751c1d9caa06973171d35d5e6bd1f945f9d7a77
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059913"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>Öğretici: Azure Stack Edge Pro R 'Yi yükler

Bu öğreticide, Azure Stack Edge Pro R fiziksel cihazının nasıl yükleneceği açıklanmaktadır. Yükleme yordamı, cihazı kablolama ile ilgilidir.

Yüklemenin tamamlanması yaklaşık 30 dakika sürebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Cihazı İnceleme
> * Cihazın kablolarını bağlama

## <a name="prerequisites"></a>Önkoşullar

Fiziksel bir cihazı yükleme önkoşulları şunlardır:

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge kaynağı için

Başlamadan önce aşağıdakilerden emin olun:

* [Azure Stack Edge Pro R 'yi dağıtmaya hazırlama](azure-stack-edge-pro-r-deploy-prep.md)bölümündeki tüm adımları tamamladınız.
    * Cihazınızı dağıtmak için bir Azure Stack Edge kaynağı oluşturdunuz.
    * Cihazınızı Azure Stack Edge kaynağıyla etkinleştirmek için etkinleştirme anahtarını oluşturdunuz.

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>Azure Stack Edge Pro R fiziksel cihazı için

Cihazı dağıtmadan önce:

- Cihazın düz, kararlı ve düzeyi bir iş yüzeyine güvenli bir şekilde oturduğu emin olun.
- Kurulumu gerçekleştirmeyi planladığınız yerde şunların bulunduğundan emin olun:
    - Bağımsız bir kaynaktan standart AC gücü

        -VEYA-
    - Bir raf güç dağıtım birimi (PDU). Cihaz, kesintisiz güç kaynağı (UPS) ile birlikte gönderilir
    

### <a name="for-the-network-in-the-datacenter"></a>Veri merkezindeki ağ için

Başlamadan önce:

- Azure Stack Edge Pro R 'yi dağıtmaya yönelik ağ gereksinimlerini gözden geçirin ve veri merkezi ağını gereksinimlere göre yapılandırın. Daha fazla bilgi için bkz. [Edge Pro R ağ gereksinimleri Azure Stack](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements).

- Cihazın en iyi şekilde çalışması için en düşük Internet bant genişliğinin 20 Mbps olduğundan emin olun.


## <a name="inspect-the-device"></a>Cihazı İnceleme

Bu cihaz tek bir birim olarak sevk edilir. Cihazınızı kutusundan çıkarmak için aşağıdaki adımları gerçekleştirin.

1. Kutuyu düz ve sabit bir yüzeye yerleştirin.
2. Herhangi bir hasar için cihaz durumunu inceleyin. Büyük/küçük harf durumunu açın ve cihazı inceleyin. Büyük/küçük harf veya cihaz hasar görmüşse, cihazın iyi çalışma sırasında olup olmadığını değerlendirmenize yardımcı olmak için Microsoft Desteği başvurun.
3. Durum açıldıktan sonra, şunları yaptığınızdan emin olun:
    - Tek bir kutu Azure Stack Edge Pro R cihazı
    - Bir kesintisiz güç kaynağı (UPS)
    - 2 cihaz UPS 'e bağlamak için kısa güç kablosu
    - UPS 'yi güç kaynağına bağlamak için 1 güç kablosu

Burada listelenen öğelerin tümünü almadıysanız Azure Stack Edge Pro R desteği 'ne başvurun. Bir sonraki adım cihazınızı kablolayın.


## <a name="cable-the-device"></a>Cihazın kablolarını bağlama

Aşağıdaki yordamlarda Azure Stack Edge Pro R cihazınızın güç ve ağ için nasıl kablo yapılacağı açıklanmaktadır.

Cihazınızı kablolamayı başlatmaya başlamadan önce şunlar gerekir:

- Yükleme sitesinde Azure Stack Edge Pro R fiziksel cihazınız.
- Bir güç kablosu.
- Yönetim arabirimine bağlamak için en az bir 1-GbE RJ-45 ağ kablosu. Cihazda biri yönetim ve diğeri veri olmak üzere iki 1-GbE ağ arabirimi vardır.
- Yapılandırılacak her veri ağı arabirimi için bir 10/25-GbE SFP + bakır kablo. Bağlantı noktası 3 veya bağlantı noktası 4 arasından en az bir veri ağı arabiriminin Internet 'e bağlanması gerekir (Azure bağlantısı ile).  
- Bir güç dağıtım birimine erişim (önerilir).

> [!NOTE]
> - Yalnızca bir veri ağı arabirimi bağlıyorsanız, Azure 'a veri göndermek için bağlantı noktası 3 veya bağlantı noktası 4 gibi bir 25/10-GbE ağ arabirimi kullanmanızı öneririz. 
> - En iyi performansı elde etmek ve büyük miktarda veriyi işlemek için tüm veri bağlantı noktalarını bağlamak isteyebilirsiniz.
> - Azure Stack Edge Pro R cihazının veri kaynağı sunucularından veri alabilmesi için veri merkezi ağına bağlı olması gerekir.

Azure Stack Edge Pro R cihazınızda:

- Ön panelde disk sürücüleri ve bir güç düğmesi bulunur.

    - Cihazınızın önünde 8 disk yuvası vardır.
    - Cihazda Ayrıca, işletim sistemi diskleri olarak çalışan içinde 2 X d. 2 SATA diski vardır. 

- Arka düzlem, yedekli güç kaynağı birimleri (PSUs) içerir.
- Arka düzlemi dört ağ arabirimine sahiptir:

    - 2 1 Gbps arabirimler.
    - 10 Gbps arabirim olarak da kullanılabilen 2 25 Gbps arabirim.
    - Bir temel kart yönetim denetleyicisi (BMC).

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
Cihazınızı güç ve ağ için kablolu yapmak üzere aşağıdaki adımları uygulayın.

1. Cihazınızın arka düzlemine ait çeşitli bağlantı noktalarını belirler.

    ![Kablolu cihazın arka düzlemi](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. Cihazın önünde disk yuvalarını ve güç düğmesini bulun.

    ![Bir cihazın ön düzlemi](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. Güç kablosunu bir uca bağlayın. Güç kablosunun diğer sonunu raf güç dağıtım birimine (PBu) ekleyin. 
5. Cihazı açmak için güç düğmesine basın.
6. 1-GbE ağ arabirimi bağlantı noktası 1 ' i fiziksel cihazı yapılandırmak için kullanılan bilgisayara bağlayın. PORT 1, yönetim için ayrılmış arabirimdir.
7. Bir veya daha fazla bağlantı noktası 2, bağlantı noktası 3 veya bağlantı noktası 4 ' ü veri merkezi ağına/Internete bağlayın.

    - PORT 2’yi bağlıyorsanız, RJ-45 ağ kablosunu kullanın.
    - 10/25-GbE ağ arabirimleri için, SFP + bakır kablolarını kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki gibi Edge Pro R konuları Azure Stack hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Cihazı kutusundan çıkarma
> * Cihazın kablolarını bağlama

Cihazınıza nasıl bağlanacağınızı öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro R 'ye bağlanma](./azure-stack-edge-pro-r-deploy-connect.md)
