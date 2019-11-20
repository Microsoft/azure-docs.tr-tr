---
title: Hızlı başlangıç-Azure portal kullanarak bir Azure özel DNS bölgesi oluşturma
description: Bu hızlı başlangıçta, Azure DNS bir özel DNS bölgesi ve kaydı oluşturup test edersiniz. Bu, Azure portal kullanarak ilk özel DNS bölgenizi ve kaydınızı oluşturup yönetmek için adım adım kılavuzudur.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: victorh
ms.openlocfilehash: 352a63e611e3823e03bedb01a9c1a5071d628c4f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163817"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak bir Azure özel DNS bölgesi oluşturma

Bu hızlı başlangıç, Azure portal kullanarak ilk özel DNS bölgenizi ve kaydınızı oluşturma adımlarında size yol gösterir.

DNS bölgesi, belirli bir etki alanının DNS kayıtlarını barındırmak için kullanılır. Etki alanınızı Azure DNS'de barındırmaya başlamak için bir DNS bölgesi oluşturmanız gerekir. Ardından bu DNS bölgesinde etki alanınız için tüm DNS kayıtları oluşturulur. Sanal ağınızda özel bir DNS bölgesi yayımlamak için bölge içindeki kaynakları çözümleme izni olan sanal ağların listesini belirtmeniz gerekir.  Bunlara *bağlı* sanal ağlar denir. Oto kayıt etkinleştirildiğinde Azure DNS, bir sanal makine oluşturulduğunda bölge kayıtlarını da güncelleştirir, ' IP adresini değiştirir veya silinir.

Bu hızlı başlangıçta şunları yapmayı öğrenirsiniz:

> [!div class="checklist"]
> * Özel bir DNS bölgesi oluşturma
> * Sanal ağ oluşturun
> * Sanal ağı bağlama
> * Test amaçlı sanal makineleri oluşturma
> * Ek bir DNS kaydı oluşturma
> * Özel bölgeyi test etme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

İsterseniz, [Azure PowerShell](private-dns-getstarted-powershell.md) veya [Azure CLI](private-dns-getstarted-cli.md)kullanarak bu hızlı başlangıcı tamamlayabilirsiniz.

## <a name="create-a-private-dns-zone"></a>Özel bir DNS bölgesi oluşturma

Aşağıdaki örnek, **MyAzureResourceGroup**adlı bir kaynak grubunda **PRIVATE.contoso.com** adlı bir DNS bölgesi oluşturur.

Bir DNS bölgesi, bir etki alanı için DNS girdilerini içerir. Etki alanınızı Azure DNS barındırılmaya başlamak için, bu etki alanı adı için bir DNS bölgesi oluşturun.

![Özel DNS bölgeleri arama](media/private-dns-portal/search-private-dns.png)

1. Portal arama çubuğunda, ara metin kutusuna **özel DNS bölgeleri** yazın ve **ENTER**tuşuna basın.
1. **Özel DNS bölgeyi**seçin.
2. **Özel DNS bölgesi oluştur**' u seçin.

1. **Özel DNS bölgesi oluştur** sayfasında, aşağıdaki değerleri yazın veya seçin:

   - **Kaynak grubu**: **Yeni oluştur**' u seçin, *MyAzureResourceGroup*girin ve **Tamam**' ı seçin. Kaynak grubu adı, Azure aboneliği dahilinde benzersiz olmalıdır. 
   -  **Ad**: Bu örnek için *Private.contoso.com* yazın.
1. **Kaynak grubu konumu**için **Orta Batı ABD**' yi seçin.

1. **Gözden geçir + oluştur**' u seçin.

1. **Oluştur**'u seçin.

Bölgenin oluşturulması birkaç dakika sürebilir.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturun

1. Portal sayfası sol üst sayfasında, **kaynak oluştur**' u ve ardından **ağ**' ı seçin ve **sanal ağ**' ı seçin.
2. **Ad**Için **Myazurevnet**yazın.
3. **Kaynak grubu**için **MyAzureResourceGroup**' yi seçin.
4. **Konum**için **Orta Batı ABD**' yi seçin.
5. Diğer varsayılan değerleri kabul edin ve **Oluştur**' u seçin.

## <a name="link-the-virtual-network"></a>Sanal ağı bağlama

Özel DNS bölgesini bir sanal ağa bağlamak için bir sanal ağ bağlantısı oluşturursunuz.

![Sanal ağ bağlantısı ekle](media/private-dns-portal/dns-add-virtual-network-link.png)

1. **MyAzureResourceGroup** kaynak grubunu açın ve **Private.contoso.com** özel bölgesini seçin.
2. Sol bölmede **sanal ağ bağlantıları**' nı seçin.
3. **Add (Ekle)** seçeneğini belirleyin.
4. **Bağlantı adı**Için **MyLink** yazın.
5. **Sanal ağ**Için, **Myazurevnet**' i seçin.
6. **Otomatik kaydı etkinleştir** onay kutusunu seçin.
7. **Tamam**’ı seçin.

## <a name="create-the-test-virtual-machines"></a>Test amaçlı sanal makineleri oluşturma

Şimdi özel DNS bölgenizi test etmek için iki sanal makine oluşturun:

1. Portal sayfası sol üst köşesinde, **kaynak oluştur**' u seçin ve ardından **Windows Server 2016 Datacenter**' ı seçin.
1. Kaynak grubu için **MyAzureResourceGroup** öğesini seçin.
1. Sanal makinenin adı için **myVM01** yazın.
1. **Bölge**için **Orta Batı ABD** seçin.
1. Yönetici Kullanıcı adı için **azureadmin** yazın.
2. Parola için **Azure12345678** yazın ve parolayı onaylayın.

5. **Ortak gelen bağlantı noktaları**için **Seçili bağlantı noktalarına izin ver**' i seçin ve ardından **gelen bağlantı noktalarını Seç**için **RDP (3389)** seçeneğini belirleyin
10. Sayfa için diğer varsayılanları kabul edin ve ardından Ileri ' ye tıklayın **: diskler >** .
11. **Diskler** sayfasında Varsayılanları kabul edin ve ardından İleri ' ye tıklayın **: Ağ >** .
1. Sanal ağ için **Myazurevnet** ' in seçildiğinden emin olun.
1. Sayfa için diğer varsayılanları kabul edin ve ardından Ileri ' ye tıklayın **: yönetim >** .
2. **Önyükleme tanılaması**için **kapalı**' yı seçin, diğer varsayılanları kabul edin ve ardından **gözden geçir + oluştur**' u seçin.
1. Ayarları gözden geçirin ve ardından **Oluştur**' a tıklayın.

Bu adımları yineleyin ve **myVM02**adlı başka bir sanal makine oluşturun.

Her iki sanal makinenin da tamamlanması birkaç dakika sürer.

## <a name="create-an-additional-dns-record"></a>Ek bir DNS kaydı oluşturma

 Aşağıdaki örnek, **Private.contoso.com**kaynak GRUBUNDAKI **MyAzureResourceGroup**DNS bölgesinde göreli ad **DB** ile bir kayıt oluşturur. Kayıt kümesinin tam nitelikli adı **DB.Private.contoso.com**' dir. Kayıt türü, **MYVM01**IP adresi olan "A" dır.

1. **MyAzureResourceGroup** kaynak grubunu açın ve **Private.contoso.com** özel bölgesini seçin.
2. **+ Kayıt kümesi**’ni seçin.
3. **Ad**için **DB**yazın.
4. **IP adresi**Için, **MYVM01**için gördüğünüz IP adresini yazın. Bu, sanal makine başlatıldığında otomatik olarak kaydedilmelidir.
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

## <a name="delete-all-resources"></a>Tüm kaynakları silme

Artık gerekli değilse, bu hızlı başlangıçta oluşturulan kaynakları silmek için **MyAzureResourceGroup** kaynak grubunu silin.


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure DNS Özel Bölgeleri senaryolar](private-dns-scenarios.md)

