---
title: Hızlı başlangıç-Azure portal kullanarak bir Azure özel DNS bölgesi oluşturma
description: Bu hızlı başlangıçta, Azure DNS bir özel DNS bölgesi ve kaydı oluşturup test edersiniz. Bu, Azure portal kullanarak ilk özel DNS bölgenizi ve kaydınızı oluşturup yönetmek için adım adım kılavuzudur.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- mode-portal
ms.openlocfilehash: 2115062db8615ca782cb987903e5ebfc83400cd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535455"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak bir Azure özel DNS bölgesi oluşturma

Bu hızlı başlangıç, Azure portal kullanarak ilk özel DNS bölgenizi ve kaydınızı oluşturma adımlarında size yol gösterir.

DNS bölgesi, belirli bir etki alanına ait DNS kayıtlarını barındırmak için kullanılır. Etki alanınızı Azure DNS'de barındırmaya başlamak için bir DNS bölgesi oluşturmanız gerekir. Ardından bu DNS bölgesinde etki alanınız için tüm DNS kayıtları oluşturulur. Sanal ağınıza özel bir DNS bölgesi yayımlamak için, bölgedeki kayıtları çözümlemesine izin verilen sanal ağların listesini belirtirsiniz.  Bunlara *bağlı* sanal ağlar denir. Oto kayıt etkinleştirildiğinde Azure DNS, bir sanal makine oluşturulduğunda bölge kayıtlarını da güncelleştirir, ' IP adresini değiştirir veya silinir.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

İsterseniz, [Azure PowerShell](private-dns-getstarted-powershell.md) veya [Azure CLI](private-dns-getstarted-cli.md)kullanarak bu hızlı başlangıcı tamamlayabilirsiniz.

## <a name="create-a-private-dns-zone"></a>Özel bir DNS bölgesi oluşturma

Aşağıdaki örnek, **MyAzureResourceGroup** adlı bir kaynak grubunda **PRIVATE.contoso.com** adlı bir DNS bölgesi oluşturur.

Bir DNS bölgesi, bir etki alanı için DNS girdilerini içerir. Etki alanınızı Azure DNS barındırılmaya başlamak için, bu etki alanı adı için bir DNS bölgesi oluşturun.

![Özel DNS bölgeleri arama](media/private-dns-portal/search-private-dns.png)

1. Portal arama çubuğunda, ara metin kutusuna **özel DNS bölgeleri** yazın ve **ENTER** tuşuna basın.
1. **Özel DNS bölgeyi** seçin.
2. **Özel DNS bölgesi oluştur**' u seçin.

1. **Özel DNS bölgesi oluştur** sayfasında, aşağıdaki değerleri yazın veya seçin:

   - **Kaynak grubu**: **Yeni oluştur**' u seçin, *MyAzureResourceGroup* girin ve **Tamam**' ı seçin. Kaynak grubu adı, Azure aboneliği dahilinde benzersiz olmalıdır.
   -  **Ad**: Bu örnek için *Private.contoso.com* yazın.
1. **Kaynak grubu konumu** için **Orta Batı ABD**' yi seçin.

1. **Gözden geçir + Oluştur**’u seçin.

1. **Oluştur**’u seçin.

Bölgenin oluşturulması birkaç dakika sürebilir.

## <a name="virtual-network-and-parameters"></a>Sanal ağ ve parametreler

Bu bölümde, adımlarda aşağıdaki parametreleri aşağıdaki bilgilerle değiştirmeniz gerekir:

| Parametre                   | Değer                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | MyAzureResourceGroup (mevcut kaynak grubunu Seç) |
| **\<virtual-network-name>** | MyAzureVNet          |
| **\<region-name>**          | Orta Batı ABD      |
| **\<IPv4-address-space>**   | 10.2.0.0 \ 16          |
| **\<subnet-name>**          | Myazuyeniden gönderiliyor        |
| **\<subnet-address-range>** | 10.2.0.0 \ 24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Sanal ağı bağlama

Özel DNS bölgesini bir sanal ağa bağlamak için, sanal ağ bağlantısı oluşturursunuz.

![Sanal ağ bağlantısı ekle](media/private-dns-portal/dns-add-virtual-network-link.png)

1. **MyAzureResourceGroup** kaynak grubunu açın ve **Private.contoso.com** özel bölgesini seçin.
2. Sol bölmede **sanal ağ bağlantıları**' nı seçin.
3. **Add (Ekle)** seçeneğini belirleyin.
4. **Bağlantı adı** Için **MyLink** yazın.
5. **Sanal ağ** Için, **Myazurevnet**' i seçin.
6. **Otomatik kaydı etkinleştir** onay kutusunu seçin.
7. **Tamam**’ı seçin.

## <a name="create-the-test-virtual-machines"></a>Test amaçlı sanal makineleri oluşturma

Şimdi özel DNS bölgenizi test etmek için iki sanal makine oluşturun:

1. Portal sayfası sol üst köşesinde, **kaynak oluştur**' u seçin ve ardından **Windows Server 2016 Datacenter**' ı seçin.
1. Kaynak grubu için **MyAzureResourceGroup** öğesini seçin.
1. Sanal makinenin adı için **myVM01** yazın.
1. **Bölge** için **Orta Batı ABD** seçin.
1. Yönetici Kullanıcı adı için bir ad girin.
2. Bir parola girin ve parolayı onaylayın.
5. **Ortak gelen bağlantı noktaları** için **Seçili bağlantı noktalarına izin ver**' i seçin ve ardından **gelen bağlantı noktalarını Seç** için **RDP (3389)** seçeneğini belirleyin
10. Sayfa için diğer varsayılanları kabul edin ve ardından Ileri ' ye tıklayın **: diskler >**.
11. **Diskler** sayfasında Varsayılanları kabul edin ve ardından İleri ' ye tıklayın **: ağ >**.
1. Sanal ağ için **Myazurevnet** ' in seçildiğinden emin olun.
1. Sayfa için diğer varsayılanları kabul edin ve ardından Ileri ' ye tıklayın **: yönetim >**.
2. **Önyükleme tanılaması** için **kapalı**' yı seçin, diğer varsayılanları kabul edin ve ardından **gözden geçir + oluştur**' u seçin.
1. Ayarları gözden geçirin ve ardından **Oluştur**' a tıklayın.

Bu adımları yineleyin ve **myVM02** adlı başka bir sanal makine oluşturun.

Her iki sanal makinenin da tamamlanması birkaç dakika sürer.

## <a name="create-an-additional-dns-record"></a>Ek bir DNS kaydı oluşturma

 Aşağıdaki örnek, **Private.contoso.com** kaynak GRUBUNDAKI **MyAzureResourceGroup** DNS bölgesinde göreli ad **DB** ile bir kayıt oluşturur. Kayıt kümesinin tam nitelikli adı **DB.Private.contoso.com**' dir. Kayıt türü, **MYVM01** IP adresi olan "A" dır.

1. **MyAzureResourceGroup** kaynak grubunu açın ve **Private.contoso.com** özel bölgesini seçin.
2. **+ Kayıt kümesi** seçeneğini belirleyin.
3. **Ad** için **DB** yazın.
4. **IP adresi** Için, **MYVM01** için gördüğünüz IP adresini yazın. Bu, sanal makine başlatıldığında otomatik olarak kaydedilmelidir.
5. **Tamam**’ı seçin.

## <a name="test-the-private-zone"></a>Özel bölgeyi test etme

Artık **Private.contoso.com** özel bölgeniz için ad çözümlemesini test edebilirsiniz.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Sanal makineleri gelen ICMP paketlerine izin verecek şekilde yapılandırma

Ad çözümlemesini test etmek için ping komutunu kullanabilirsiniz. Bunun için iki sanal makinedeki güvenlik duvarını da gelen ICMP paketlerine izin verecek şekilde yapılandırmanız gerekir.

1. myVM01 adlı sanal makineye bağlanın, yönetici ayrıcalıklarıyla bir Windows PowerShell penceresi açın.
2. Şu komutu çalıştırın:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

myVM02 için yineleyin.

### <a name="ping-the-vms-by-name"></a>Sanal makinelere ada göre ping gönderme

1. myVM02 Windows PowerShell komut isteminden otomatik olarak kaydedilen ana bilgisayar adını kullanarak myVM01 adlı makineye ping gönderin:
   ```
   ping myVM01.private.contoso.com
   ```
   Şuna benzer bir çıkışla karşılaşmanız gerekir:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Şimdi önceden oluşturduğunuz **db** adına ping gönderin:
   ```
   ping db.private.contoso.com
   ```
   Şuna benzer bir çıkışla karşılaşmanız gerekir:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu hızlı başlangıçta oluşturulan kaynakları silmek için **MyAzureResourceGroup** kaynak grubunu silin.


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure DNS Özel Bölgeleri senaryoları](private-dns-scenarios.md)
