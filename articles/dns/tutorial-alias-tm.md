---
title: 'Öğretici: etki alanı tepesinde adlarını desteklemek için bir diğer ad kaydı oluşturma-Traffic Manager'
titleSuffix: Azure DNS
description: Bu öğreticide Traffic Manager ile etki alanı tepe adının kullanılmasını desteklemek için Azure DNS diğer ad kaydı yapılandırma adımları gösterilmektedir.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: e0101133c68142845a8ada50d9921d341cf10ad0
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738801"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Öğretici: Traffic Manager ile tepe etki alanı adlarını desteklemek için diğer ad kaydı yapılandırma 

Bir Azure Traffic Manager profiline başvurmak üzere etki alanı tepe adı için diğer ad kaydı oluşturabilirsiniz. Örneğin: contoso.com. Yönlendirme hizmeti kullanmak yerine Azure DNS yapılandırması ile Traffic Manager profiline doğrudan bölgenizden başvurabilirsiniz. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Ana bilgisayar VM'si ve ağ altyapısı oluşturma.
> * Traffic Manager profili oluşturma.
> * Diğer ad kaydı oluşturma.
> * Diğer ad kaydını test etme.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar
Birlikte test edilecek Azure DNS içinde barındırabileceğiniz bir etki alanı adınızın olması gerekir. Bu etki alanı üzerinde tam denetime sahip olmanız gerekir. Tam denetim, etki alanı için ad sunucusu (NS) kayıtlarını ayarlama olanağını kapsar.

Azure DNS’te etki alanınızı barındırma yönergeleri için bkz. [Öğretici: Azure DNS’te etki alanınızı barındırma](dns-delegate-domain-azure-dns.md).

Bu öğreticide örnek olarak contoso.com etki alanı kullanılmaktadır ancak sizin kendi etki alanı adınızı kullanmanız gerekir.

## <a name="create-the-network-infrastructure"></a>Ağ altyapısını oluşturma

İlk olarak, web sunucularınızı içine yerleştirmek için bir sanal ağ ve alt ağ oluşturun.

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Portalda sol üst köşeden **Kaynak oluştur**'u seçin. Arama kutusuna *kaynak grubu* yazın ve **RG-DNS-Alias-TM** adlı bir kaynak grubu oluşturun.
3. **Kaynak oluştur**  >  **ağ**  >  **sanal ağ**' ı seçin.
4. **VNet-Servers** adlı bir sanal ağ oluşturun. Bunu **RG-DNS-Alias-TM** kaynak grubunun içine yerleştirin ve alt ağı **SN-Web** olarak adlandırın.

## <a name="create-two-web-server-virtual-machines"></a>İki web sunucusu sanal makinesi oluşturma

1. **Kaynak oluştur**  >  **Windows Server 2016 VM**' yi seçin.
2. Ad için **Web-01** girin ve VM’yi **RG-DNS-Alias-TM** kaynak grubuna yerleştirin. Kullanıcı adı ve parola girip **Tamam**'ı seçin.
3. **Boyut** için 8 GB RAM'e sahip bir SKU seçin.
4. **Ayarlar** için **VNet-Servers** sanal ağını ve **SN-Web** alt ağını seçin.
5. **Genel IP adresi**'ni seçin. **Atama** bölümünde **Statik**'i ve ardından **Tamam**'ı seçin.
6. Ortak gelen bağlantı noktaları için **http (80)**  >  **https (443)**  >  **RDP (3389)** öğesini seçin ve ardından **Tamam**' ı seçin.
7. **Özet** sayfasında **Oluştur**'u seçin. Bu işlemin tamamlanması birkaç dakika sürer.

Bu yordamı tekrarlayarak **Web-02** adlı başka bir sanal makine oluşturun.

### <a name="add-a-dns-label"></a>DNS etiketi ekleme

Genel IP adreslerinin Traffic Manager ile çalışabilmesi için bir DNS etiketi kullanılması gerekir.
1. **RG-DNS-Alias-TM** kaynak grubunda **Web-01-ip** genel IP adresini seçin.
2. **Ayarlar** bölümünde **Yapılandırma**‘yı seçin.
3. DNS adı metin kutusuna **web01pip** girin.
4. **Kaydet**’i seçin.

Bu yordamı **Web-02-ip** genel IP adresi için tekrarlayın ve DNS ad etiketi için **web02pip** değerini kullanın.

### <a name="install-iis"></a>IIS yükleme

**Web-01** ve **Web-02** üzerine IIS yükleyin.

1. **Web-01**’e bağlanıp oturum açın.
2. **Sunucu Yöneticisi** panosunda **Rol ve özellik ekle**’ye tıklayın.
3. Üç kez **İleri**'yi seçin. **Sunucu Rolleri** sayfasında **Web Sunucusu (IIS)** öğesini seçin.
4. **Özellik Ekle**'yi ve ardından **İleri**'yi seçin.
5. Dört kez **İleri**'yi seçin. Ardından **Yükle**’yi seçin. Bu işlemin tamamlanması birkaç dakika sürer.
6. Yükleme tamamlandıktan sonra **Kapat**'ı seçin.
7. Bir web tarayıcısı açın. Varsayılan IIS web sayfasının göründüğünü doğrulamak için **localhost** sayfasına göz atın.

Bu işlemleri tekrarlayarak **Web-02** üzerine de IIS yükleyin.


## <a name="create-a-traffic-manager-profile"></a>Traffic Manager profili oluşturma

1. **RG-DNS-Alias-TM** kaynak grubunu açın ve **Web-01-ip** Genel IP adresini seçin. Daha sonra kullanmak için IP adresini not edin. Bu adımı **Web-02-ip** genel IP adresi için tekrarlayın.
1. **Kaynak**  >  **ağ**  >  **Traffic Manager profili** oluştur ' u seçin.
2. Ad olarak **TM-alias-test** girin. Bunu **RG-DNS-Alias-TM** kaynak grubunun içine yerleştirin.
3. **Oluştur**’u seçin.
4. Dağıtım tamamlandıktan sonra **Kaynağa git**'i seçin.
5. Traffic Manager profili sayfasının **Ayarlar** bölümünde **Uç noktalar**'ı seçin.
6. **Add (Ekle)** seçeneğini belirleyin.
7. **Tür** olarak **Dış uç nokta**'yı seçin ve **Ad** alanına **EP-Web01** girin.
8. **Tam etki alanı adı (FQDN) veya IP** metin kutusuna önceden not ettiğiniz **Web-01-ip** IP adresini girin.
9. Diğer kaynaklarınızla aynı **Konum** değerini seçin ve **Tamam**'ı seçin.

Bu yordamı tekrarlayarak **Web-02** uç noktasını ekleyin ve önceden not ettiğiniz **Web-02-ip** IP adresini kullanın.

## <a name="create-an-alias-record"></a>Diğer ad kaydı oluşturma

Traffic Manager profiline işaret eden bir diğer ad kaydı oluşturun.

1. Azure DNS bölgenizi açmak için bölgeyi seçin.
2. **Kayıt kümesi**’ni seçin.
3. Etki alanı tepe adını kullanmak için **Ad** metin kutusunu boş bırakın. Örneğin: contoso.com.
4. **Tür** alanını bir **A** kaydı olarak bırakın.
5. **Diğer Ad Kayıt Kümesi** onay kutusunu seçin.
6. **Azure hizmeti seçin**'e tıklayıp **TM-alias-test** Traffic Manager profilini seçin.

## <a name="test-the-alias-record"></a>Diğer ad kaydını test etme

1. Web tarayıcısından etki alanı tepe adına gidin. Örneğin: contoso.com. Varsayılan IIS web sayfasını görürsünüz. Web tarayıcısını kapatın.
2. **Web-01** sanal makinesini kapatın. Tamamen kapanması için birkaç dakika bekleyin.
3. Yeni bir web tarayıcısı açın ve tekrar etki alanınızın tepe adına gidin.
4. Traffic Manager durumla ilgilenip trafiği **Web-02**'ye yönlendirdiğinden yine varsayılan IIS sayfasını görürsünüz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturulan kaynaklara ihtiyacınız kalmadığında **RG-DNS-Alias-TM** kaynak grubunu silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide Traffic Manager profiline başvurmak üzere etki alanınızın tepe adını kullanmak için bir diğer ad kaydı oluşturdunuz. Azure DNS ve web uygulamaları hakkında daha fazla bilgi için web uygulaması öğreticileriyle devam edebilirsiniz.

> [!div class="nextstepaction"]
> [Bölge zirvesinde yük dengeli web uygulamaları barındırma](./dns-alias-appservice.md)
