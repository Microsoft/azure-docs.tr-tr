---
title: İç yük dengeleyici (ıLB) uç noktası yapılandırma
titleSuffix: Azure Application Gateway
description: Bu makalede, Application Gateway özel bir ön uç IP adresi ile nasıl yapılandırılacağı hakkında bilgi sağlanır
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: a9e3150a5382e4d690ddf66c43bbe51e125509d3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075211"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>İç yük dengeleyici (ıLB) uç noktası ile uygulama ağ geçidi yapılandırma

Azure Application Gateway, bir iç yük dengeleyici (ıLB) uç noktası olarak da bilinen, internet 'e yönelik bir VIP veya Internet 'e açık olmayan iç uç nokta ile yapılandırılabilir (ön uç IP adresi için özel bir IP kullanılarak). Ön uç özel IP adresi kullanarak ağ geçidini yapılandırmak, Internet 'e açık olmayan iç iş kolu uygulamaları için yararlıdır. Güvenlik sınırı içinde bulunan, İnternet’e sunulmamış ancak hala hepsini bir kez deneme yük dağıtımı, oturum sürekliliği veya Güvenli Yuva Katmanı (SLL) sonlandırması gerektiren çok katmanlı uygulamalar içindeki hizmetler ve katmanlar için de kullanışlıdır.

Bu makalede, Azure portalından ön uç özel IP adresine sahip bir uygulama ağ geçidi yapılandırma adımlarında izlenecek yol gösterilmektedir.

Bu makalede, öğreneceksiniz nasıl yapılır:

- Application Gateway için özel ön uç IP yapılandırması oluşturma
- Özel ön uç IP yapılandırması ile uygulama ağ geçidi oluşturma


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Azure'da oturum açma

<https://portal.azure.com> adresinden Azure portalında oturum açın

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

Azure 'un, oluşturduğunuz kaynaklar arasında iletişim kurması için bir sanal ağa ihtiyacı vardır. Yeni bir sanal ağ oluşturabilir veya var olan bir ağı kullanabilirsiniz. Bu örnekte, yeni bir sanal ağ oluşturacağız. Uygulama ağ geçidini oluştururken aynı zamanda bir sanal makine oluşturabilirsiniz. Application Gateway örnekleri ayrı alt ağlarda oluşturulur. Bu örnekte iki alt ağ oluşturursunuz: bir tane uygulama ağ geçidi ve arka uç sunucuları için bir diğeri.

1. Azure portal sol üst köşesinde bulunan **Yeni** ' ye tıklayın.
2. **Ağ** ve ardından Öne Çıkanlar listesinde **Application Gateway**’i seçin.
3. Uygulama ağ geçidinin adı ve yeni kaynak grubu için *myResourceGroupAG* Için *myappgateway* yazın.
4. Diğer ayarların varsayılan değerlerini kabul edin ve sonra **Tamam**’a tıklayın.
5. Tıklayın **bir sanal ağ seçin**, tıklayın **Yeni Oluştur**ve ardından sanal ağ için şu değerleri girin:
   - sanal ağın adı için myVNet *.
   - 10.0.0.0/16 *-sanal ağ adres alanı için.
   - *myAGSubnet* - Alt ağın adı.
   - *10.0.0.0/24* - Alt ağın adres alanı.  
     ![Private-frontendıp-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Sanal ağı ve alt ağı oluşturmak için **Tamam**’a tıklayın.
7. Ön uç IP yapılandırmasını özel olarak seçin ve varsayılan olarak dinamik bir IP adresi atamasıdır. Seçilen alt ağın ilk kullanılabilir adresi, ön uç IP adresi olarak atanır.
8. Alt ağ adres aralığından (statik ayırma) özel bir IP seçmek isterseniz, **belirli bir özel IP adresi seçin** kutusuna tıklayın ve IP adresini belirtin.
   > [!NOTE]
   > Ayrıldıktan sonra, IP adresi türü (statik veya dinamik) daha sonra değiştirilemez.
9. Protokol ve bağlantı noktası (gerekliyse) için dinleyici yapılandırmanızı seçip Tamam ' a tıklayın.
    ![Private-frontendıp-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Özet sayfasındaki ayarları gözden geçirin ve ardından **Tamam** ' a tıklayarak ağ kaynaklarını ve uygulama ağ geçidini oluşturun. Bu, uygulama ağ geçidinin oluşturulması, sonraki bölüme geçmeden önce dağıtımın başarıyla tamamlanana kadar bekleyin birkaç dakika sürebilir.

## <a name="add-backend-pool"></a>Arka uç Havuzu Ekle

Arka uç havuzu, istekleri isteğine hizmet edecek arka uç sunucularına yönlendirmek için kullanılır. Arka uç, NIC 'Ler, sanal makine ölçek kümeleri, genel IP 'Ler, iç IP 'Ler, tam etki alanı adları (FQDN) ve Azure App Service gibi çok kiracılı arka uçlar olabilir. Bu örnekte, sanal makineleri hedef arka uç olarak kullanacağız. Mevcut sanal makineleri kullanabilir ya da yenilerini oluşturabilirsiniz. Bu örnekte, Azure 'un uygulama ağ geçidi için arka uç sunucuları olarak kullandığı iki sanal makine oluşturacağız. Bunu yapmak için şunları yapacağız:

1. Arka uç sunucuları olarak kullanılmak üzere, *myvm* ve *myVM2*için 2 yeni VM oluşturun.
2. Uygulama ağ geçidinin başarıyla oluşturulduğunu doğrulamak için sanal makinelere IIS 'yi yükler.
3. Arka uç sunucularını arka uç havuzuna ekleyin.

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

1. **Yeni**’ye tıklayın.
2. **İşlem** ' e tıklayın ve ardından öne çıkan listede **Windows Server 2016 Datacenter** ' ı seçin.
3. Sanal makine için şu değerleri girin:
   - *myVM* - Sanal makinenin adı.
   - Yönetici kullanıcı adı için *azureuser*.
   - *Azure123456!* Parola.
   - **Mevcut olanı kullan**’ı seçin ve *myResourceGroupAG* seçeneğini belirleyin.
4. **OK (Tamam)** düğmesine tıklayın.
5. Sanal makinenin boyutu için **DS1_V2** seçin ve **Seç**' e tıklayın.
6. Sanal ağ için **myVNet** öğesinin seçili olduğundan ve alt ağın **myBackendSubnet** olduğundan emin olun.
7. Önyükleme tanılamalarını devre dışı bırakmak için **Devre Dışı** seçeneğine tıklayın.
8. **Tamam**’a tıklayın, özet sayfasındaki ayarları gözden geçirin ve sonra **Oluştur**’a tıklayın.

### <a name="install-iis"></a>IIS yükleme

1. Etkileşimli kabuğu açın ve **PowerShell**’e ayarlandığından emin olun.
    ![Private-frontendıp-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Sanal makineye IIS yüklemek için aşağıdaki komutu çalıştırın:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
