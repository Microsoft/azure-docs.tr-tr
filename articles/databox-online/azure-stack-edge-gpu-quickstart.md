---
title: Azure Stack Edge GPU 'YU yapılandırmak ve dağıtmak için hızlı başlangıç | Microsoft Docs
description: Cihaz alındıktan sonra Azure Stack Edge GPU 'YU dağıtmaya başlayın.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: quickstart
ms.date: 04/07/2021
ms.author: alkohli
ms.openlocfilehash: 43477f11effaafb0bcd6158be6843c79d19092ac
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210819"
---
# <a name="quickstart-get-started-with-azure-stack-edge-pro-with-gpu"></a>Hızlı başlangıç: GPU ile Azure Stack Edge Pro ile çalışmaya başlama 

Bu hızlı başlangıç, Azure Stack Edge Pro GPU cihazınızı dağıtmak için gereken önkoşulları ve adımları ayrıntılarıyla ayrıntılardır. Hızlı başlangıç adımları Azure portalında ve cihazın yerel web kullanıcı arabiriminde gerçekleştirilir. 

Toplam yordamın tamamlanması yaklaşık 1,5 saat sürer. Ayrıntılı adım adım yönergeler için [öğretici: Azure Stack Edge Pro GPU 'yu dağıtmaya hazırlanın](azure-stack-edge-gpu-deploy-prep.md#deployment-configuration-checklist)bölümüne gidin. 


## <a name="prerequisites"></a>Önkoşullar

Dağıtmadan önce, aşağıdaki önkoşulların yerinde olduğundan emin olun:

1. Azure Stack Edge Pro GPU cihazı, sitenize, [paketten](azure-stack-edge-gpu-deploy-install.md#unpack-the-device) [takılmış ve rafa takılı](azure-stack-edge-gpu-deploy-install.md#rack-the-device)olarak dağıtılır. 
1. Ağınızı, cihazınızın [LISTELENEN URL desenlerine ve bağlantı noktalarına](azure-stack-edge-gpu-system-requirements.md#networking-port-requirements)ulaşabileceği şekilde yapılandırın. 
1. Azure aboneliğine sahip veya katkıda bulunan erişiminiz var.
1. Azure portal, **abonelik > kaynak sağlayıcılarına > giriş > abonelikleri**' ne gidin. `Microsoft.DataBoxEdge`Kaynak sağlayıcısını arayın ve kaydedin. `Microsoft.Devices`İşlem iş yüklerini dağıtmak üzere bir IoT Hub kaynağı oluşturacağınızı, kaydolmak için tekrarlayın.
1. Kubernetes düğümleri için en az 2 ücretsiz, statik, bitişik IP ve IoT Edge hizmeti için en az 1 statik IP kullandığınızdan emin olun. Her modül veya dış hizmet için, dağıtırsınız, 1 ek IP gerekir.
1. Cihaz yapılandırması için ihtiyaç duyacağınız her şeyi almak için [dağıtım denetim listesine](azure-stack-edge-gpu-deploy-checklist.md) bakın. 


## <a name="deployment-steps"></a>Dağıtım adımları

1. **Install**: bağlantı noktası 1 ' i bir çapraz kablo veya USB Ethernet bağdaştırıcısı aracılığıyla bir istemci bilgisayara bağlayın. En az bir diğer cihaz bağlantı noktasını, tercihen 25 GbE (bağlantı noktası 3 ' ten bağlantı noktası 6 ' ya kadar), SFP + bakır kablolar aracılığıyla Internet 'e bağlayın veya RJ45 Patch kablosuyla bağlantı noktası 2 ' yi kullanın. Belirtilen güç gücünü güç kaynağı birimlerine bağlayın ve güç dağıtımı çıkışlarının birbirinden ayırın. Cihazı açmak için ön paneldeki güç düğmesine basın.  

    Uyumlu ağ kabloları ve anahtarları almak için bkz. [Cavium Fastlınq 41000 serisi birlikte çalışabilirlik matrisi](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) ve [Mellanox Dual Port 25G ConnectX-4 kanal ağ bağdaştırıcısı uyumlu ürünler](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products) .

    Cihazınızın dağıtılması için gereken en düşük kablolama yapılandırması aşağıda verilmiştir:  ![ bir kablolu cihazın arka düzlemi](./media/azure-stack-edge-gpu-quickstart/backplane-min-cabling-1.png)

2. **Bağlan**: bilgisayarınızdaki Ethernet bağdaştırıcısında IPv4 ayarlarını **192.168.100.5** ve alt ağ **255.255.255.0** statik IP adresiyle yapılandırın. Tarayıcınızı açın ve konumundaki cihazın yerel Web Kullanıcı arabirimine bağlanın https://192.168.100.10 . Bu birkaç dakika sürebilir. Güvenlik sertifikası uyarısını gördüğünüzde Web sayfasına devam edin.

3. **Oturum aç**: varsayılan parola *Parola1* ile cihazda oturum açın. Cihaz yönetici parolasını değiştirin. Parolanın uzunluğu 8 ila 16 karakter, büyük harf, küçük harf, sayısal ve özel karakterlerden oluşan 3 içermelidir.

4. **Ağı Yapılandır**: AĞıNıZDA bir DHCP sunucusu varsa, bağlı veri bağlantı noktası IÇIN varsayılan DHCP yapılandırmasını kabul edin. Aksi takdirde, bir IP, DNS sunucusu ve varsayılan ağ geçidi sağlayın. [Ağ ayarları](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-network)hakkında daha fazla bilgi için bkz..

5. **İşlem ağını yapılandırın**: cihazınızda bir bağlantı noktasını etkinleştirerek sanal anahtar oluşturun. Anahtarı oluşturduğunuz aynı ağdaki Kubernetes düğümleri için 2 boş, bitişik statik IP girin. IoT Edge hub hizmeti için, Kubernetes kümesi dışından erişmek istediğiniz her bir ek hizmet veya kapsayıcı için işlem modüllerine ve 1 statik IP 'ye erişmek üzere en az 1 statik IP sağlayın. 

    Tüm Kapsayıcılı iş yüklerini dağıtmak için Kubernetes gereklidir. [İşlem ağ ayarları](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)hakkında daha fazla bilgi için bkz..

6. **Web proxy 'Yi yapılandırma**: ortamınızda Web Proxy kullanıyorsanız, Web proxy sunucusu IP 'sini girin `http://<web-proxy-server-FQDN>:<port-id>` . Kimlik doğrulamasını **none** olarak ayarlayın. [Web proxy ayarları](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-web-proxy)hakkında daha fazla bilgi görüntüleyin.

7. **Cihazı yapılandırma**: bir cihaz adı ve DNS etki alanı girin veya Varsayılanları kabul edin. 

8. **Güncelleştirme sunucusunu yapılandırma**: varsayılan Microsoft Update sunucusunu kabul edin veya bir Windows Server Update SERVICES (WSUS) sunucusu ve sunucu yolunu belirtin. 

9. **Zaman ayarlarını yapılandırın**: varsayılan saat ayarlarını kabul edin veya yerel ağda veya ortak sunucular olarak saat dilimini, birincil NTP sunucusunu ve ikincil NTP sunucusunu ayarlayın.

10. **Sertifikaları yapılandırma**: Cihaz adı ve/veya DNS etki alanını değiştirdiyseniz, cihazı etkinleştirmek için sertifikalar oluşturmanız veya sertifika eklemeniz gerekir. 

    - Üretim dışı iş yüklerini test etmek için, [sertifika oluştur seçeneğini](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)kullanın. 
    - İmza Zinciri de dahil olmak üzere kendi sertifikalarınızı getiruyorsanız, sertifikaları uygun biçimde [ekleyin](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) . Önce imzalama zincirini karşıya yüklediğinizden emin olun. Bkz. [sertifikaları oluşturma](azure-stack-edge-gpu-create-certificates-tool.md) ve [Yerel Kullanıcı arabirimi aracılığıyla sertifika yükleme](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates).

11. **Etkinleştir**: etkinleştirme anahtarını almak için 

    1. Azure portal **Azure Stack Edge kaynağına gidin > genel bakış > cihaz kurulumu > > oluştur etkinleştirin**. Anahtarı kopyalayın. 
    1. Yerel Web Kullanıcı arabiriminde, etkinleştirme ' **> başlayın** ' a gidin ve etkinleştirme anahtarını sağlayın. Anahtar uygulandığında, cihazın etkinleştirilmesi birkaç dakika sürer. `<device-serial-number>`Gelecekteki bir kurtarma için gereken cihaz anahtarlarını güvenli bir şekilde depolamak isteyip istemediğiniz sorulduğunda. json dosyasını indirin. 

12. **Işlem yapılandırma**: Azure Portal **genel bakış > cihazına** gidin. Cihazın **çevrimiçi** olduğunu doğrulayın. Sol bölmedeki kenar işleme ' ya gidin **> Edge işlem > işlem > yapılandırma**. Var olan veya yeni bir IoT Hub hizmeti sağlayın ve işlem yapılandırması için yaklaşık 20 dakika bekleyin. Öğretici hakkında daha fazla bilgi için bkz [. Azure Stack Edge Pro GPU cihazında Işlem yapılandırma](azure-stack-edge-gpu-deploy-configure-compute.md)

İşlem iş yüklerini cihazınızda IoT Edge [, `kubectl` ](azure-stack-edge-gpu-create-kubernetes-cluster.md) [Azure Arc etkin Kubernetes](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)aracılığıyla veya aracılığıyla [aracılığıyla](azure-stack-edge-gpu-deploy-sample-module-marketplace.md)dağıtmaya hazır olursunuz! Kurulum sırasında herhangi bir sorunla karşılaşırsanız [cihaz sorunlarını giderme](), [sipariş sorunları](azure-stack-edge-gpu-troubleshoot.md), [sertifika sorunları](azure-stack-edge-gpu-certificate-troubleshooting.md)veya Kubernetes sorunlarını giderme bölümüne gidin. 

## <a name="next-steps"></a>Sonraki adımlar

[Azure Stack Edge Pro GPU 'YU yükler](./azure-stack-edge-gpu-deploy-install.md)



