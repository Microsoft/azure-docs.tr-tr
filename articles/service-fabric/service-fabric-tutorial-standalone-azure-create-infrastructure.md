---
title: Azure VM 'lerinde bir Service Fabric kümesi için altyapı oluşturma öğreticisi-Azure Service Fabric | Microsoft Docs
description: Bu öğreticide, Azure VM altyapısını bir Service Fabric kümesi çalıştıracak şekilde ayarlamayı öğreneceksiniz.
services: service-fabric
documentationcenter: .net
author: jpconnock
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: jeconnoc
ms.custom: mvc
ms.openlocfilehash: b24b4d95827dbd398c0eba43dcbad9fbfeb51469
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166269"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Öğretici: Service Fabric kümesi barındırmak için Azure VM altyapısı oluşturma

Service Fabric tek başına kümeleri, kendi ortamınızı seçme ve Service Fabric’in benimsediği "her işletim sistemi, her bulut" yaklaşımının bir parçası olarak bir küme oluşturma seçeneği sunar. Bu öğretici serisinde, Azure VM 'lerinde barındırılan tek başına bir küme oluşturup üzerine bir uygulama yüklersiniz.

Bu öğretici, bir serinin birinci bölümüdür. Bu makalede, tek başına Service Fabric kümenizi barındırmak için gereken Azure VM kaynaklarını üretin. Gelecek makalelerde, Service Fabric tek başına paketini yüklemeniz, kümenize bir örnek uygulama yüklemeniz ve son olarak kümenizi temizlemeniz gerekir.

Serinin birinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * AzureVM örnekleri kümesi oluşturma
> * Güvenlik grubunu değiştirme
> * Örneklerden birinde oturum açma
> * Service Fabric örneğini hazırlama

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için bir Azure aboneliğinizin olması gerekir.  Henüz bir hesabınız yoksa, bir hesap oluşturmak için [Azure Portal](https://portal.azure.com) gidin.

## <a name="create-azure-virtual-machine-instances"></a>Azure sanal makine örnekleri oluşturma

1. Azure portal oturum açın ve **sanal makineler** ' i (sanal makineler (klasik) değil) seçin.

   ![Azure portal VM][az-console]

2. **Sanal makine oluştur** formunun açılacağı **Ekle** düğmesini seçin.

3. **Temel bilgiler** sekmesinde, istediğiniz aboneliği ve kaynak grubunu seçtiğinizden emin olun (yeni bir kaynak grubu kullanarak önerilir).

4. **Görüntü** türünü **Windows Server 2016 Datacenter**olarak değiştirin. 
 
5. Örnek **boyutunu** **Standart DS2 v2**olarak değiştirin. Bir yönetici **Kullanıcı adı** ve **parola**ayarlayıp ne olduğunu belirterek belirleyin.

6. **Gelen bağlantı noktası kurallarını** şimdilik bloke bırakın; Sonraki bölümde bunları yapılandıracağız.

7. **Ağ** sekmesinde, yeni bir **sanal ağ** oluşturun ve adını bir yere göz atın.

8. Ardından, **NIC ağ güvenlik grubunu** **Gelişmiş**olarak ayarlayın. Adını belirterek yeni bir güvenlik grubu oluşturun ve herhangi bir kaynaktan gelen TCP trafiğine izin vermek için aşağıdaki kuralları oluşturun:

   ![SF-gelen][sf-inbound]

   * RDP ve ıCMP için bağlantı noktası `3389`(temel bağlantı).
   * Service Fabric için bağlantı noktaları `19000-19003`.
   * Service Fabric için bağlantı noktaları `19080-19081`.
   * Web tarayıcısı istekleri için bağlantı noktası `8080`.

   > [!TIP]
   > Service Fabric’te sanal makinelerinizi birbirine bağlamak için, altyapınızı barındıran VM’lerin aynı kimlik bilgilerine sahip olması gerekir.  Tutarlı kimlik bilgileri elde etmenin iki yaygın yolu vardır: tümünü aynı etki alanına eklemek veya her sanal makinede aynı yönetici parolasını ayarlamak. Neyse ki, Azure aynı **sanal ağ** üzerindeki tüm sanal makinelerin kolayca bağlanmasına izin verir. bu nedenle, tüm örneklerimizi aynı ağ üzerinde sunduğunuzdan emin olun.

9. Başka bir kural ekleyin. Kaynağı **hizmet etiketi** olarak ayarlayın ve kaynak hizmet etiketini **VirtualNetwork**olarak ayarlayın. Service Fabric, şu bağlantı noktalarının küme içindeki iletişim için açılmasını gerektiriyor: 135137-139, 445, 20.001-20031, 20606-20861.

   ![VNET-gelen][vnet-inbound]

10. Diğer seçeneklerin varsayılan durumunda kabul edilebilir. İsterseniz bunları gözden geçirin ve ardından sanal makinenizi başlatın.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Service Fabric kümeniz için daha fazla örnek oluşturma

Önceki bölümde özetlenen ayarların aynısını bulundurduğunuzdan emin olmak için, iki **sanal makine**başlatın. Özellikle de aynı Yönetici Kullanıcı adını ve parolasını koruyun. **Sanal ağ** ve **NIC ağ güvenlik grubu** yeniden oluşturulmamalıdır; açılır menüden zaten oluşturduğunuz olanları seçin. Her bir örneklerinizin dağıtılması birkaç dakika sürebilir.

## <a name="connect-to-your-instances"></a>Örneklerinizi bağlama

1. **Sanal makine** bölümünde örneklerinizin birini seçin.

2. **Genel bakış** sekmesinde *özel* IP adresini göz önünde atın. Ardından **Bağlan**' a tıklayın.

3. **RDP** sekmesinde, genel IP adresi ve bağlantı noktası 3389 ' ü kullandığımızda, daha önce daha önce açtığım unutulmamalıdır. RDP dosyasını indirin.
 
4. RDP dosyasını açın ve istendiğinde VM kurulumunda verdiğiniz kullanıcı adını ve parolayı girin.

5. Bir örneğe bağlandıktan sonra, uzak kayıt defterinin çalıştığını doğrulamanız, SMB 'yi etkinleştirmeniz ve SMB ve uzak kayıt defteri için gereken bağlantı noktalarını açmanız gerekir.

   SMB 'yi etkinleştirmek için bu PowerShell komutunuz:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Buradaki güvenlik duvarında bağlantı noktalarını açmak için şu PowerShell komutunu kullanın:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Diğer örnekleriniz için bu işlemi tekrarlayın, özel IP adreslerini yeniden de not edin.

## <a name="verify-your-settings"></a>Ayarlarınızı doğrulayın

1. Temel bağlantıyı doğrulamak için, RDP kullanarak VM 'lerden birine bağlanın.

2. **Komut istemi** 'NI bu VM içinden açın, ardından, bir VM 'den diğerine bağlanmak için ping komutunu kullanın, aşağıdaki IP adresini daha önce Not ETTIĞINIZ özel IP adreslerinden biriyle değiştirin (zaten bağlı olduğunuz VM 'nin IP 'si değil).

   ```
   ping 172.31.20.163
   ```

   Çıktınız dört kez tekrar eden `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` gibi görünüyorsa, örnekler arasındaki bağlantınız çalışıyordur.

3. Şimdi aşağıdaki komutla SMB paylaşımınızın çalıştığını doğrulayın:

   ```
   net use * \\172.31.20.163\c$
   ```

   Çıktı olarak `Drive Z: is now connected to \\172.31.20.163\c$.` döndürülmelidir.


   Artık örneklerinizin Service Fabric için uygun şekilde hazırlandığından emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Serinin birinci bölümünde, üç Azure sanal makine örneğini nasıl başlatacağınız ve Service Fabric yüklemesi için nasıl yapılandırılacağını öğrendiniz:

> [!div class="checklist"]
> * Azure VM örnekleri kümesi oluşturma
> * Güvenlik grubunu değiştirme
> * Örneklerden birinde oturum açma
> * Service Fabric örneğini hazırlama

Service Fabric’i kümenizde yapılandırmak için serinin ikinci bölümüne ilerleyin.

> [!div class="nextstepaction"]
> [Service Fabric yükleme](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
