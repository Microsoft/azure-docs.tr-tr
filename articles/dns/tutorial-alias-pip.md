---
title: 'Öğretici: Azure genel IP adresine başvurmak için Azure DNS diğer ad kaydı oluşturma'
description: Bu öğreticide Azure genel IP adresine başvurmak için bir Azure DNS diğer ad kaydını yapılandırma işlemi gösterilmektedir.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: 28e37ad0b404b5275a224c8debab5c11c07948b4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738819"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Öğretici: Azure genel IP adresine başvurmak için diğer ad kaydı yapılandırma 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Ağ altyapısı oluşturma.
> * Genel IP ile bir Web sunucusu sanal makinesi oluşturun.
> * Genel IP 'yi işaret eden bir diğer ad kaydı oluşturun.
> * Diğer ad kaydını test etme.


Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar
Birlikte test edilecek Azure DNS içinde barındırabileceğiniz bir etki alanı adınızın olması gerekir. Bu etki alanı üzerinde tam denetime sahip olmanız gerekir. Tam denetim, etki alanı için ad sunucusu (NS) kayıtlarını ayarlama olanağını kapsar.

Azure DNS’te etki alanınızı barındırma yönergeleri için bkz. [Öğretici: Azure DNS’te etki alanınızı barındırma](dns-delegate-domain-azure-dns.md).

Bu öğreticide örnek olarak contoso.com etki alanı kullanılmaktadır ancak sizin kendi etki alanı adınızı kullanmanız gerekir.

## <a name="create-the-network-infrastructure"></a>Ağ altyapısını oluşturma
İlk olarak, web sunucularınızı içine yerleştirmek için bir sanal ağ ve alt ağ oluşturun.
1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Azure portal sol panelinden **kaynak oluştur** ' u seçin. Arama kutusuna *kaynak grubu* yazın ve **RG-DNS-Alias-pip** adlı bir kaynak grubu oluşturun.
3. **Kaynak oluştur**  >  **ağ**  >  **sanal ağ**' ı seçin.
4. **VNet-Server** adlı bir sanal ağ oluşturun. Bunu **RG-DNS-Alias-pip** kaynak grubunun içine yerleştirin ve alt ağı **SN-Web** olarak adlandırın.

## <a name="create-a-web-server-virtual-machine"></a>Web sunucusu sanal makinesi oluşturma
1. **Kaynak oluştur**  >  **Windows Server 2016 VM**' yi seçin.
2. Ad için **Web-01** girin ve VM’yi **RG-DNS-Alias-TM** kaynak grubuna yerleştirin. Kullanıcı adı ve parola girip **Tamam**'ı seçin.
3. **Boyut** için 8 GB RAM'e sahip bir SKU seçin.
4. **Ayarlar** için **VNet-Servers** sanal ağını ve **SN-Web** alt ağını seçin. Ortak gelen bağlantı noktaları için **http (80)**  >  **https (443)**  >  **RDP (3389)** öğesini seçin ve ardından **Tamam**' ı seçin.
5. **Özet** sayfasında **Oluştur**'u seçin.

Bu dağıtımın tamamlanabilmesi birkaç dakika sürer. Sanal makinede, Web-01-IP adlı temel dinamik genel IP 'ye sahip bir NIC eklenir. Ortak IP, sanal makine her yeniden başlatıldığında değişecektir.

### <a name="install-iis"></a>IIS yükleme

**Web-01**’e IIS yükleyin.

1. **Web-01**’e bağlanıp oturum açın.
2. **Sunucu Yöneticisi** panosunda **Rol ve özellik ekle**’ye tıklayın.
3. Üç kez **İleri**'yi seçin. **Sunucu Rolleri** sayfasında **Web Sunucusu (IIS)** öğesini seçin.
4. **Özellik Ekle**'yi ve ardından **İleri**'yi seçin.
5. **İleri**'yi dört kez seçin ve ardından **Yükle**'yi seçin. Bu işlemin tamamlanması birkaç dakika sürer.
6. Yükleme tamamlandıktan sonra **Kapat**'ı seçin.
7. Bir web tarayıcısı açın. Varsayılan IIS web sayfasının göründüğünü doğrulamak için **localhost** sayfasına göz atın.

## <a name="create-an-alias-record"></a>Diğer ad kaydı oluşturma

Genel IP adresine işaret eden bir diğer ad kaydı oluşturun.

1. Azure DNS bölgenizi açmak için bölgeyi seçin.
2. **Kayıt kümesi**’ni seçin.
3. **Ad** metin kutusuna **web01** yazın.
4. **Tür** alanını bir **A** kaydı olarak bırakın.
5. **Diğer Ad Kayıt Kümesi** onay kutusunu seçin.
6. **Azure hizmeti seçin**’i belirleyin ve ardından **Web-01-ip** genel IP adresini seçin.

## <a name="test-the-alias-record"></a>Diğer ad kaydını test etme

1. **RG-DNS-Alias-pip** kaynak grubunda **Web-01** sanal makinesini seçin. Genel IP adresini not edin.
1. Bir web tarayıcısından Web01-01 sanal makinesinin tam etki alanı adına göz atın. Örnek: **web01.contoso.com**. Varsayılan IIS web sayfasını görürsünüz.
2. Web tarayıcısını kapatın.
3. **Web-01** sanal makinesini durdurun ve sonra yeniden başlatın.
4. Yeniden başlatıldıktan sonra, sanal makinenin yeni genel IP adresini not edin.
5. Yeni bir tarayıcı açın. Yeniden Web01-01 sanal makinesinin tam etki alanı adına göz atın. Örnek: **web01.contoso.com**.

Standart bir A kaydı yerine genel IP adresi kaynağına işaret eden bir diğer ad kaydı kullandığınız için bu işlem başarılı olur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturulan kaynaklara ihtiyacınız kalmadığında **RG-DNS-Alias-pip** kaynak grubunu silebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide Azure genel IP adresine başvurmak için bir diğer ad kaydı oluşturdunuz. Azure DNS ve web uygulamaları hakkında daha fazla bilgi için web uygulaması öğreticileriyle devam edebilirsiniz.

> [!div class="nextstepaction"]
> [Özel etki alanında web uygulaması için DNS kayıtları oluşturma](./dns-web-sites-custom-domain.md)
