---
title: Azure Stack Edge Pro GPU üzerinde işlem ile verileri filtreleme, analiz etme öğreticisi | Microsoft Docs
description: Azure Stack Edge Pro GPU üzerinde işlem rolünü yapılandırmayı ve Azure 'a göndermeden önce verileri dönüştürmek için kullanmayı öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 626c4b192eeeeb8a07130d0a9ecf6754eebbe068
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065693"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Öğretici: Azure Stack Edge Pro GPU cihazında işlem yapılandırma

<!--ALPA WILL VERIFY - [!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Bu öğreticide, Azure Stack Edge Pro cihazınızda bir işlem rolünün nasıl yapılandırılacağı ve bir Kubernetes kümesinin nasıl oluşturulacağı açıklanmaktadır. 

Bu yordamın tamamlanması 20 ila 30 dakika kadar sürebilir.


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * İşlem yapılandırma
> * Kubernetes uç noktalarını al

 
## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro cihazınızda bir işlem rolü ayarlamadan önce şunları yaptığınızdan emin olun:

- Azure Stack Edge Pro cihazınızı [etkinleştirme Azure Stack Edge Pro 'Yu etkinleştir](azure-stack-edge-gpu-deploy-activate.md)bölümünde açıklandığı gibi etkinleştirdiniz.
- [İşlem ağını etkinleştir](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) bölümündeki yönergeleri izlediğinizden emin olun ve şunları yapın:
    - İşlem için bir ağ arabirimi etkinleştirildi.
    - Kubernetes düğüm IP 'Leri ve Kubernetes dış hizmet IP 'Leri atandı.

## <a name="configure-compute"></a>İşlem yapılandırma

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]

## <a name="get-kubernetes-endpoints"></a>Kubernetes uç noktalarını al

Bir istemciyi Kubernetes kümesine erişecek şekilde yapılandırmak için Kubernetes uç noktasına ihtiyacınız olacaktır. Azure Stack Edge Pro cihazınızın yerel kullanıcı arabiriminden Kubernetes API uç noktasını almak için aşağıdaki adımları izleyin.

1. Cihazınızın yerel Web Kullanıcı arabiriminde, **cihazlar** sayfasına gidin.
2. **Cihaz uç noktaları** altında **Kubernetes API hizmeti** uç noktasını kopyalayın. Bu uç nokta aşağıdaki biçimde bir dizedir: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` . 

    ![Yerel Kullanıcı arabirimindeki cihaz sayfası](./media/azure-stack-edge-gpu-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Uç nokta dizesini kaydedin. Bu uç nokta dizesini daha sonra, kubectl aracılığıyla Kubernetes kümesine erişmek için bir istemciyi yapılandırırken kullanacaksınız.

4. Yerel Web Kullanıcı arabirimdeyken şunları yapabilirsiniz:

    - Kubernetes API 'sine gidin, **Gelişmiş ayarlar**' ı seçin ve Kubernetes için gelişmiş bir yapılandırma dosyası indirin. 

        ![Yerel Kullanıcı arabirimi 1 ' deki cihaz sayfası](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Microsoft 'tan bir anahtar sağladıysanız (kullanıcıları seç bir anahtara sahip olabilir), bu yapılandırma dosyasını kullanabilirsiniz.

        ![Yerel Kullanıcı arabirimi 2 ' deki cihaz sayfası](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Ayrıca, **Kubernetes Pano** uç noktasına gidebilir ve bir `aseuser` yapılandırma dosyası indirebilirsiniz. 
    
        ![Yerel Kullanıcı arabirimi 3 ' te cihaz sayfası](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        Bu yapılandırma dosyasını Kubernetes panosunda oturum açmak veya Kubernetes kümenizdeki sorunları ayıklamak için kullanabilirsiniz. Daha fazla bilgi için bkz. [Kubernetes panosuna erişme](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * İşlem yapılandırma
> * Kubernetes uç noktalarını al


Azure Stack Edge Pro cihazınızı yönetme hakkında bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro 'Yu yönetmek için yerel Web Kullanıcı arabirimini kullanma](azure-stack-edge-manage-access-power-connectivity-mode.md)
