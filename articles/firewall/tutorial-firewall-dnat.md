---
title: Azure portalını kullanarak Azure Güvenlik Duvarı DNAT ile gelen trafiği filtreleme
description: Bu öğreticide Azure portalını kullanarak Azure Güvenlik Duvarı DNAT’yi dağıtmayı ve yapılandırmayı öğreneceksiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f0a58382b9825a7b32aee69c00b9801d1c77251a
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114625"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Öğretici: Azure portalını kullanarak Azure Güvenlik Duvarı DNAT ile gelen trafiği filtreleme

Alt ağlarınıza gelen trafiği çevirmek ve filtrelemek için Azure Güvenlik Duvarı Hedef Ağ Adresi Çevirisi’ni (DNAT) yapılandırabilirsiniz. DNAT yapılandırdığınızda, NAT kuralı toplama eylemi **DNAT**olarak ayarlanır. Daha sonra NAT kural koleksiyonundaki her kural, güvenlik duvarı ortak IP'nizi ve bağlantı noktanızı özel bir IP'ye ve bağlantı noktasına çevirmek için kullanılabilir. DNAT kuralları, çevrilen trafiğe izin verecek ilgili ağ kuralını örtük olarak ekler. Bu davranışı, çevrilen trafikle eşleşen reddetme kuralları olan bir ağ kural koleksiyonunu açıkça ekleyerek geçersiz kılabilirsiniz. Azure Güvenlik Duvarı kural işleme mantığı hakkında daha fazla bilgi için bkz: [Azure Güvenlik Duvarı kural işleme mantığı](rule-processing.md).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Test amaçlı ağ ortamı oluşturma
> * Güvenlik duvarı dağıtma
> * Varsayılan rota oluşturma
> * DNAT kuralını yapılandırma
> * Güvenlik duvarını test etme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Bu öğretici için eşlenen iki sanal ağ oluşturuyorsunuz:

- **VN-Hub** - güvenlik duvarı bu sanal ağ içindedir.
- **VN-Spoke** - iş yükü sunucusu bu sanal ağ içindedir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Azure portal giriş sayfasında **Kaynak grupları**'na ve ardından **Ekle**'ye tıklayın.
3. **Kaynak grubu adı** alanına **RG-DNAT-Test** yazın.
4. **Abonelik** bölümünde aboneliğinizi seçin.
5. **Kaynak grubu konumu** bölümünde bir konum seçin. Bundan sonra oluşturacağınız tüm kaynakların aynı konumda olması gerekir.
6. **Oluştur**'a tıklayın.

## <a name="set-up-the-network-environment"></a>Ağ ortamını oluşturma

Önce sanal ağları oluşturun, sonra da bunları eşleyin.

### <a name="create-the-hub-vnet"></a>Hub sanal ağını oluşturma

1. Azure portal giriş sayfasında **Tüm hizmetler**'e tıklayın.
2. **Ağ** bölümünde **Sanal ağlar**'a tıklayın.
3. **Ekle**'yi tıklatın.
4. **Ad** için **VN-Hub** yazın.
5. **Adres alanı** için **10.0.0.0/16** yazın.
6. **Abonelik** bölümünde aboneliğinizi seçin.
7. **Kaynak grubu** için **Var olanı kullan**’ı ve **RG-DNAT-Test** girişini seçin.
8. **Konum** alanında önceden kullandığınız konumu seçin.
9. **Alt ağ** bölümünde **Ad** alanına **AzureFirewallSubnet** yazın.

     Güvenlik duvarı bu alt ağda yer alacaktır ve alt ağ adının **mutlaka** AzureFirewallSubnet olması gerekir.
     > [!NOTE]
     > AzureFirewallSubnet alt ağının boyutu/26 ' dır. Alt ağ boyutu hakkında daha fazla bilgi için bkz. [Azure Güvenlik DUVARı SSS](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

10. **Adres aralığı**için **10.0.1.0/26**yazın.
11. Diğer alanlar için varsayılan değerleri kullanın ve ardından **Oluştur**'a tıklayın.

### <a name="create-a-spoke-vnet"></a>Uç sanal ağını oluşturma

1. Azure portal giriş sayfasında **Tüm hizmetler**'e tıklayın.
2. **Ağ** bölümünde **Sanal ağlar**'a tıklayın.
3. **Ekle**'yi tıklatın.
4. **Ad** için **VN-Spoke** yazın.
5. **Adres alanı** için **192.168.0.0/16** yazın.
6. **Abonelik** bölümünde aboneliğinizi seçin.
7. **Kaynak grubu** için **Var olanı kullan**’ı ve **RG-DNAT-Test** girişini seçin.
8. **Konum** alanında önceden kullandığınız konumu seçin.
9. **Alt ağ** altında, **Ad** için **SN-Workload** yazın.

    Sunucu bu alt ağda yer alacaktır.
10. **Adres aralığı** için **192.168.1.0/24** yazın.
11. Diğer alanlar için varsayılan değerleri kullanın ve ardından **Oluştur**'a tıklayın.

### <a name="peer-the-vnets"></a>Sanal ağları eşleme

Şimdi iki sanal ağı eşleyin.

#### <a name="hub-to-spoke"></a>Merkezden uca

1. **VN-Hub** sanal ağına tıklayın.
2. **Ayarlar**'ın altında **Eşlemeler**'e tıklayın.
3. **Ekle**'yi tıklatın.
4. Ad olarak **Peer-HubSpoke** yazın.
5. Sanal ağ olarak **VN-Spoke**’u seçin.
6. **Tamam**'ı tıklatın.

#### <a name="spoke-to-hub"></a>Uçtan merkeze

1. **VN-Spoke** sanal ağına tıklayın.
2. **Ayarlar**'ın altında **Eşlemeler**'e tıklayın.
3. **Ekle**'yi tıklatın.
4. Ad olarak **Peer-SpokeHub** yazın.
5. Sanal ağ olarak **VN-Hub**’ı seçin.
6. **Yönlendirilen trafiğe izin ver**’e tıklayın.
7. **Tamam**'ı tıklatın.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

İş yükü sanal makinesi oluşturun ve bunu **SN-Workload** alt ağına yerleştirin.

1. Azure portal giriş sayfasında **Tüm hizmetler**'e tıklayın.
2. **İşlem** bölümünde **Sanal makineler**'e tıklayın.
3. **Ekle**, **Windows Server**, **Windows Server 2016 Datacenter** ve ardından **Oluştur**'a tıklayın.

**Temel Bilgiler**

1. **Ad** için **Srv-Workload** yazın.
5. Bir kullanıcı adı ve parola girin.
6. **Abonelik** bölümünde aboneliğinizi seçin.
7. **Kaynak grubu** için **Var olanı kullan**’a tıklayın ve **RG-DNAT-Test** girişini seçin.
8. **Konum** alanında önceden kullandığınız konumu seçin.
9. **Tamam**'ı tıklatın.

**Boyut**

1. Windows Server çalıştıran test amaçlı sanal makine için uygun bir boyut seçin. Örneğin: **B2ms** (8 GB RAM, 16 GB depolama alanı).
2. Tıklayın **seçin**.

**Ayarlar**

1. **Ağ** bölümünde **Sanal ağ** için **VN-Spoke**'u seçin.
2. **Alt ağ** için **SN-Workload**'u seçin.
3. **Genel IP adresi**’ne ardından ve **Hiçbiri**’ne tıklayın.
4. **Ortak gelen bağlantı noktası seçin** alanında **Ortak gelen bağlantı noktası yok**’u seçin. 
2. Diğer varsayılan ayarları değiştirmeden **Tamam**'a tıklayın.

**Özet**

Özeti gözden geçirin ve **Oluştur**'a tıklayın. İşlemin tamamlanması birkaç dakika sürebilir.

Dağıtım bittikten sonra sanal makineyle ilişkili özel IP adresini not alın. Daha sonra güvenlik duvarını yapılandırdığınızda bu adres kullanılacaktır. Sanal makinenin adına tıklayın ve özel IP adresini bulmak için **Ayarlar**'ın altında **Ağ**’a tıklayın.

## <a name="deploy-the-firewall"></a>Güvenlik duvarını dağıtma

1. Portal giriş sayfasında **Kaynak oluştur**'a tıklayın.
2. **Ağ**'a tıklayın ve **Öne çıkanlar**'ın sonrasında **Tümünü gör**'e tıklayın.
3. **Güvenlik duvarı**'na ve ardından **Oluştur**'a tıklayın. 
4. **Güvenlik duvarı oluştur** sayfasında aşağıdaki ayarları kullanarak güvenlik duvarını yapılandırın:

   |Ayar  |Value  |
   |---------|---------|
   |Name     |FW-DNAT-test|
   |Subscription     |\<aboneliğiniz\>|
   |Resource group     |**Mevcut olanı kullan**: RG-DNAT-test |
   |Location     |Önceden kullandığınız konumu seçin|
   |Bir sanal ağ seçin     |**Mevcut olanı kullan**: VN-hub|
   |Genel IP adresi     |**Yeni oluşturun**. Genel IP adresinin türü Standart SKU olmalıdır.|

5. **Gözden geçir ve oluştur**’a tıklayın.
6. Özeti gözden geçirin ve **Oluştur**'a tıklayarak güvenlik duvarını oluşturun.

   Dağıtma işlemi birkaç dakika sürebilir.
7. Dağıtım tamamlandıktan sonra **RG-DNAT-Test** kaynak grubuna gidin ve **FW-DNAT-test** güvenlik duvarına tıklayın.
8. Özel IP adresini not edin. Varsayılan rotayı oluştururken bu adresi kullanacaksınız.

## <a name="create-a-default-route"></a>Varsayılan rota oluşturma

**SN-Workload** alt ağında varsayılan giden rotayı güvenlik duvarından geçecek şekilde yapılandıracaksınız.

1. Azure portal giriş sayfasında **Tüm hizmetler**'e tıklayın.
2. **Ağ** bölümünde **Rota tabloları**'na tıklayın.
3. **Ekle**'yi tıklatın.
4. **Ad** için **RT-FWroute** yazın.
5. **Abonelik** bölümünde aboneliğinizi seçin.
6. **Kaynak grubu** için **Var olanı kullan**’ı ve **RG-DNAT-Test** girişini seçin.
7. **Konum** alanında önceden kullandığınız konumu seçin.
8. **Oluştur**'a tıklayın.
9. **Yenile**'ye ve ardından **RT-FWroute** yol tablosuna tıklayın.
10. **Alt ağlar**’a ve ardından **İlişkilendir**’e tıklayın.
11. **Sanal ağlar**'a tıklayın ve ardından **VN-Spoke** girişini seçin.
12. **Alt ağ** için **SN-Workload** girişine tıklayın.
13. **Tamam**'ı tıklatın.
14. **Rotalar**'a ve ardından **Ekle**'ye tıklayın.
15. **Rota adı** alanına **FW-DG** yazın.
16. **Adres ön eki** alanına **0.0.0.0/0** yazın.
17. **Sonraki atlama türü** için **Sanal gereç**'i seçin.

    Azure Güvenlik Duvarı, normalde yönetilen bir hizmettir ancak bu durumda sanal gereç kullanılabilir.
18. **Sonraki atlama adresi** alanına önceden not ettiğiniz güvenlik duvarı özel IP adresini yazın.
19. **Tamam**'ı tıklatın.

## <a name="configure-a-nat-rule"></a>NAT kuralı yapılandırma

1. **RG-DNAT-Test**’i açın ve **FW-DNAT-test** güvenlik duvarına tıklayın. 
2. **FW-DNAT-test** sayfasının **Ayarlar** bölümünde **Kurallar**'a tıklayın. 
3. **NAT kuralı koleksiyonu Ekle**' ye tıklayın. 
4. **Ad** için **RC-DNAT-01** yazın. 
5. **Öncelik** alanına **200** yazın. 
6. **Kurallar**'ın altında, **Ad** için **RL-01** yazın.
7. **Protokol** alanında **TCP**'yi seçin.
8. **Kaynak Adresler** için * yazın. 
9. **Hedef Adresler** için güvenlik duvarının ortak IP adresini yazın. 
10. **Hedef bağlantı noktaları** için **3389** yazın. 
11. **Çevrilmiş Adres** için Srv-Workload sanal makinesinin özel IP adresini yazın. 
12. **Çevrilmiş bağlantı noktası** için **3389** yazın. 
13. **Ekle**'yi tıklatın. 

## <a name="test-the-firewall"></a>Güvenlik duvarını test etme

1. Güvenlik duvarı genel IP adresine bir uzak masaüstü bağlayın. **Srv-Workload** sanal makinesine bağlı olmalısınız.
2. Uzak masaüstünü kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Güvenlik duvarı kaynaklarını bir sonraki öğretici için tutabilirsiniz veya artık gerekli değilse **RG-DNAT-Test** kaynak grubunu silerek güvenlik duvarıyla ilgili tüm kaynakları silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Test amaçlı ağ ortamı oluşturma
> * Güvenlik duvarı dağıtma
> * Varsayılan rota oluşturma
> * DNAT kuralını yapılandırma
> * Güvenlik duvarını test etme

Şimdi Azure Güvenlik Duvarı günlüklerini izleyebilirsiniz.

> [!div class="nextstepaction"]
> [Öğretici: Azure Güvenlik Duvarı günlüklerini izleme](./tutorial-diagnostics.md)
