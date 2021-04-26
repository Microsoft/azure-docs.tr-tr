---
title: Azure portal ' de GPU ile Azure Stack Edge Pro cihazını bağlama, yapılandırma, etkinleştirme öğreticisi | Microsoft Docs
description: Yerel Web Kullanıcı arabirimini kullanarak Azure Stack Edge cihazınıza yerleşik bir GPU ile nasıl bağlanacağınızı öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: 0046bc6376b0a03f75aff5461b99e3f450dc0a28
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062956"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-with-gpu"></a>Öğretici: GPU ile Azure Stack Edge Pro 'ya bağlanma

Bu öğreticide, yerel Web Kullanıcı arabirimini kullanarak Azure Stack Edge Pro cihazınıza yerleşik bir GPU ile nasıl bağlanabileceğinizi açıklanmaktadır.

Bağlantı işleminin tamamlanması 5 dakika kadar sürebilir.

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Önkoşullar
> * Fiziksel bir cihaza bağlanma


## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro cihazınızı GPU ile yapılandırmadan ve ayarlamadan önce şunları yaptığınızdan emin olun:

* Fiziksel cihazı [yükleme Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md)' da ayrıntılı olarak yüklediniz.


## <a name="connect-to-the-local-web-ui-setup"></a>Yerel Web Kullanıcı arabirimi kurulumuna bağlanma

1. Bilgisayarınızda Ethernet bağdaştırıcısını, 192.168.100.5 ve alt ağ 255.255.255.0 statik IP adresiyle Azure Stack Edge Pro cihazına bağlanacak şekilde yapılandırın.

2. Bilgisayarınızı cihazınızdaki PORT 1'e bağlayın. Bilgisayar cihaza doğrudan bağlanıyorsa (anahtar olmadan) çapraz bağlantılı kablo veya USB Ethernet bağdaştırıcısı kullanın. Cihazınızda PORT 1'i belirlemek için aşağıdaki çizimi kullanın.

    ![Kabloları takılmış bir cihazın arka yüzü](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    Cihazın geri düzlemi, almış olduğunuz modele bağlı olarak biraz farklı görünebilir. Daha fazla bilgi için bkz. [cihazınızı kabloyla](azure-stack-edge-gpu-deploy-install.md#cable-the-device)bağlama.


3. Tarayıcı penceresini açın ve `https://192.168.100.10` adresinden cihazın yerel web kullanıcı arabirimine erişin.  
    Siz cihazı açtıktan sonra bu eylem birkaç dakika sürebilir.

    Web sitesinin güvenlik sertifikasında sorun olduğunu belirten bir hata veya uyarı görürsünüz. 
   
    ![Web sitesi güvenlik sertifikası hata iletisi](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. **Bu Web sayfasına devam et**' i seçin.  
    Bu adımlar kullandığınız tarayıcıya bağlı olarak değişebilir.

5. Cihazınızın web kullanıcı arabiriminde oturum açın. Varsayılan parola *Parola1*' dir. 
   
    ![Azure Stack Edge Pro cihaz oturum açma sayfası](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. İstem sırasında cihaz yönetici parolasını değiştirin.  
    Yeni parola 8 ile 16 arasında karakter içermelidir. Parola şu karakterlerden üçünü içermelidir: küçük harf, büyük harf, rakam ve özel karakterler.

Artık cihazınızın **genel bakış** sayfasında olursunuz. Bir sonraki adım, cihazınızın ağ ayarlarını yapılandırmaktır.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Önkoşullar
> * Fiziksel bir cihaza bağlanma


Azure Stack Edge Pro cihazınızda ağ ayarlarını yapılandırma hakkında bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Ağı yapılandırma](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
