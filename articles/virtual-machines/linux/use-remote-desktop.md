---
title: Azure 'da bir Linux VM 'ye uzak masaüstü 'Nü kullanma
description: Grafik araçlarını kullanarak Azure 'da bir Linux VM 'sine bağlanmak için Uzak Masaüstü 'Nü (xrdp) yüklemeyi ve yapılandırmayı öğrenin
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/12/2019
ms.author: cynthn
ms.openlocfilehash: 8631b05bc42df86ef6865bf2a07c0e3deaaad2fe
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034289"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Azure 'da bir Linux VM 'sine bağlanmak için Uzak Masaüstü 'Nü yüklemek ve yapılandırmak
Azure 'daki Linux sanal makineleri (VM 'Ler), genellikle güvenli bir kabuk (SSH) bağlantısı kullanılarak komut satırından yönetilir. Linux 'ta yeni veya hızlı sorun giderme senaryolarında, uzak masaüstü kullanımı daha kolay olabilir. Bu makalede, Kaynak Yöneticisi dağıtım modelini kullanarak Linux sanal ağınız için masaüstü ortamının ([Xfce](https://www.xfce.org)) ve uzak masaüstü 'nün ([xrdp](https://www.xrdp.org)) nasıl yükleneceği ve yapılandırılacağı açıklanır.


## <a name="prerequisites"></a>Önkoşullar
Bu makalede, Azure 'da mevcut bir Ubuntu 18,04 LTS sanal makinesi gereklidir. Bir VM oluşturmanız gerekiyorsa aşağıdaki yöntemlerden birini kullanın:

- [Azure CLI](quick-create-cli.md)
- [Azure portalı](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Linux sanal makinenize masaüstü ortamı yükler
Azure 'daki Linux VM 'lerinin çoğunda varsayılan olarak bir masaüstü ortamı yüklü değildir. Linux VM 'Ler, masaüstü ortamı yerine genellikle SSH bağlantıları kullanılarak yönetilir. Linux 'ta seçebileceğiniz çeşitli masaüstü ortamları vardır. Masaüstü ortamınız seçiminize bağlı olarak, bir ila 2 GB disk alanı tüketebilir ve gerekli tüm paketleri yüklemek ve yapılandırmak için 5 ila 10 dakika sürebilir.

Aşağıdaki örnek, bir Ubuntu 18,04 LTS sanal makinesine basit [Xfce4](https://www.xfce.org/) masaüstü ortamı 'nı yükleme. Diğer dağıtımlara yönelik komutlar biraz farklılık gösterir (örneğin, Red Hat Enterprise Linux yüklemek ve uygun `selinux` kuralları yapılandırmak ya da SUSE 'e yüklemek için `zypper` kullanmak `yum` kullanın).

İlk olarak, sanal makinenize SSH. Aşağıdaki örnek, *azureuser*Kullanıcı adı ile *myvm.westus.cloudapp.Azure.com* adlı sanal makineye bağlanır. Kendi değerlerinizi kullanın:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Windows kullanıyorsanız ve SSH kullanma hakkında daha fazla bilgiye ihtiyacınız varsa bkz. [Windows Ile SSH anahtarlarını kullanma](ssh-from-windows.md).

Ardından, aşağıdaki gibi `apt` kullanarak Xfce 'yi yazın:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Uzak masaüstü sunucusunu yükleyip yapılandırma
Artık yüklü bir masaüstü ortamınız olduğuna göre, gelen bağlantıları dinlemek için bir uzak masaüstü hizmeti yapılandırın. [xrdp](http://xrdp.org) , çoğu Linux dağılımından kullanılabilen açık kaynaklı Uzak Masaüstü Protokolü (RDP) sunucusudur ve Xfce ile iyi çalışmaktadır. Aşağıdaki şekilde Ubuntu sanal makinenize xrdp 'yi yüklersiniz:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Oturumunuzu başlattığınızda xrdp 'ye hangi masaüstü ortamının kullanılacağını söyleyin. Xrdp 'yi masaüstü ortamınız olarak şu şekilde kullanacak şekilde yapılandırın:

```bash
echo xfce4-session >~/.xsession
```

Değişikliklerin etkili olması için xrdp hizmetini yeniden başlatın:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Yerel Kullanıcı hesabı parolası ayarlama
VM 'nizi oluştururken Kullanıcı hesabınız için bir parola oluşturduysanız, bu adımı atlayın. Yalnızca SSH anahtarı kimlik doğrulaması kullanıyorsanız ve yerel hesap parolası ayarlanmamışsa, sanal makinenizde oturum açmak için xrdp kullanmadan önce bir parola belirtin. xrdp, kimlik doğrulaması için SSH anahtarlarını kabul edemez. Aşağıdaki örnek, *azureuser*Kullanıcı hesabı için bir parola belirtir:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Bir parola belirtilmesi, SSHD yapılandırmanızı, şu anda yoksa parola oturumlarını açmaya izin verecek şekilde güncelleştirmez. Bir güvenlik perspektifinden, anahtar tabanlı kimlik doğrulaması kullanarak bir SSH tüneli ile sanal makinenize bağlanmak ve ardından xrdp 'ye bağlanmak isteyebilirsiniz. Bu durumda, uzak masaüstü trafiğine izin vermek için bir ağ güvenlik grubu kuralı oluşturma konusunda aşağıdaki adımı atlayın.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Uzak Masaüstü trafiği için bir ağ güvenlik grubu kuralı oluşturma
Uzak Masaüstü trafiğinin Linux VM 'nize erişmesine izin vermek için, 3389 numaralı bağlantı noktasında sanal makinenize ulaşmasını sağlayan bir ağ güvenlik grubu kuralı oluşturulması gerekir. Ağ güvenlik grubu kuralları hakkında daha fazla bilgi için bkz. [ağ güvenlik grubu nedir?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Ayrıca [, bir ağ güvenlik grubu kuralı oluşturmak için Azure Portal de kullanabilirsiniz](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Aşağıdaki örnek, *3389*numaralı bağlantı noktasında [az VM Open-Port](/cli/azure/vm#az-vm-open-port) ile bir ağ güvenlik grubu kuralı oluşturur. Sanal makinenize SSH oturumundan değil, Azure CLı 'dan aşağıdaki ağ güvenlik grubu kuralını açın:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Linux VM 'nizi uzak masaüstü istemcisiyle bağlama
Yerel Uzak Masaüstü istemcinizi açın ve Linux sanal makinenizin IP adresine veya DNS adına bağlanın. VM 'nizin Kullanıcı hesabı için Kullanıcı adını ve parolayı şu şekilde girin:

![Uzak Masaüstü istemcinizi kullanarak xrdp 'ye bağlanma](./media/use-remote-desktop/remote-desktop-client.png)

Kimlik doğrulamasından sonra, Xfce masaüstü ortamı yüklenir ve aşağıdaki örneğe benzer şekilde görünür:

![xrdp aracılığıyla Xfce masaüstü ortamı](./media/use-remote-desktop/xfce-desktop-environment.png)

Yerel RDP istemciniz ağ düzeyinde kimlik doğrulaması (NLA) kullanıyorsa, bu bağlantı ayarını devre dışı bırakmanız gerekebilir. XRDP Şu anda NLA 'yı desteklemiyor. Ayrıca, [FreeRDP](https://www.freerdp.com)gibi nla 'yı destekleyen alternatif RDP çözümlerine bakabilirsiniz.


## <a name="troubleshoot"></a>Sorun giderme
Linux VM 'nize uzak masaüstü istemcisi kullanarak bağlanamıyorsanız, sanal makinenizin RDP bağlantılarını dinlediğini doğrulamak için Linux sanal makinenizde `netstat` kullanın:

```bash
sudo netstat -plnt | grep rdp
```

Aşağıdaki örnek, TCP bağlantı noktası 3389 ' de beklendiği gibi dinleme yapan VM 'yi gösterir:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

*Xrdp-sesman* hizmeti dinlemediğinde, Ubuntu VM 'de hizmeti aşağıdaki şekilde yeniden başlatın:

```bash
sudo service xrdp restart
```

Hizmetin yanıt vermemesine neden olabilecek göstergeler için Ubuntu VM 'nizde */var/log* içindeki günlükleri gözden geçirin. Ayrıca, bir Uzak Masaüstü bağlantısı sırasında hataları görüntüleme denemesi sırasında Syslog 'yi izleyebilirsiniz:

```bash
tail -f /var/log/syslog
```

Red Hat Enterprise Linux ve SUSE gibi diğer Linux dağıtımlarındaki hizmetleri yeniden başlatmak için farklı yollar ve gözden geçirmek için alternatif günlük dosyası konumları olabilir.

Uzak Masaüstü istemcinizdeki herhangi bir yanıt almazsanız ve sistem günlüğünde herhangi bir olay görmüyorsanız, bu davranış uzak masaüstü trafiğinin sanal makineye ulaşamayacağını gösterir. 3389 numaralı bağlantı noktasında TCP 'ye izin veren bir kuralınız olduğundan emin olmak için ağ güvenlik grubu kurallarınızı gözden geçirin. Daha fazla bilgi için bkz. [uygulama bağlantı sorunlarını giderme](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Sonraki adımlar
Linux VM 'Leri ile SSH anahtarları oluşturma ve kullanma hakkında daha fazla bilgi için bkz. [Azure 'Da Linux VM 'ler IÇIN SSH anahtarları oluşturma](mac-create-ssh-keys.md).

Windows 'dan SSH kullanma hakkında bilgi için bkz. [Windows Ile SSH anahtarlarını kullanma](ssh-from-windows.md).

