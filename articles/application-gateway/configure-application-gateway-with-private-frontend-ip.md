---
title: İç yük dengeleyici (ıLB) uç noktası yapılandırma
titleSuffix: Azure Application Gateway
description: Bu makalede, Application Gateway özel bir ön uç IP adresi ile nasıl yapılandırılacağı hakkında bilgi sağlanır
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: 224cbe1e34e5915a7fa5fc1cf415c35f86c3abe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711663"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>İç yük dengeleyici (ıLB) uç noktası ile uygulama ağ geçidi yapılandırma

Azure Application Gateway, Internet 'e yönelik bir VIP ile veya Internet 'e açık olmayan bir iç uç nokta ile yapılandırılabilir. İç uç nokta, *iç yük dengeleyici (ıLB) uç noktası* olarak da bilinen, ön uç için özel bir IP adresi kullanır.

Ön uç özel IP adresi kullanarak ağ geçidini yapılandırmak, Internet 'e açık olmayan iç iş kolu uygulamaları için yararlıdır. Ayrıca, Internet 'e açık olmayan bir güvenlik sınırında olan çok katmanlı bir uygulama içindeki hizmetler ve katmanlar için de yararlıdır:

- Hala hepsini bir kez deneme yük dağıtımı gerektir
- oturum sürekliliği
- veya Aktarım Katmanı Güvenliği (TLS) sonlandırma (daha önce Güvenli Yuva Katmanı (SSL) olarak bilinir).

Bu makale, Azure portal kullanarak bir ön uç özel IP adresi ile uygulama ağ geçidi yapılandırma adımlarında size rehberlik eder.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

<https://portal.azure.com> adresinden Azure portalında oturum açın.

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

Azure 'un, oluşturduğunuz kaynaklar arasında iletişim kurması için bir sanal ağa ihtiyacı vardır. Yeni bir sanal ağ oluşturun ya da var olan bir ağı kullanın. 

Bu örnekte, yeni bir sanal ağ oluşturursunuz. Uygulama ağ geçidini oluştururken aynı zamanda bir sanal makine oluşturabilirsiniz. Application Gateway örnekleri ayrı alt ağlarda oluşturulur. Bu örnekte iki alt ağ vardır: uygulama ağ geçidi için bir diğeri, arka uç sunucuları için bir diğeri.

1. Portal menüsünü genişletin ve **kaynak oluştur**' u seçin.
2. **Ağ** ve ardından Öne Çıkanlar listesinde **Application Gateway**’i seçin.
3. Uygulama ağ geçidinin adı ve yeni kaynak grubu için *myResourceGroupAG* Için *myappgateway* yazın.
4. **Bölge** için **Orta ABD**' yi seçin.
5. **Katman** için **Standart**' ı seçin.
6. **Sanal ağı Yapılandır** altında **Yeni oluştur**' u seçin ve ardından sanal ağ için şu değerleri girin:
   - *myVNet* - Sanal ağın adı.
   - *10.0.0.0/16* - Sanal ağın adres alanı.
   - *myAGSubnet* - Alt ağın adı.
   - *10.0.0.0/24* - Alt ağın adres alanı.
   - arka uç alt ağ adı için *Mybackendsubnet* .
   - *10.0.1.0/24* -arka uç alt ağ adres alanı için.

    ![Sanal ağ oluşturma](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Sanal ağı ve alt ağları oluşturmak için **Tamam ' ı** seçin.
7. **İleri ' yi seçin: ön uçlar**.
8. **Ön uç IP adresi türü** için **özel**' i seçin.

   Bu, varsayılan olarak dinamik bir IP adresi atamasıdır. Yapılandırılan alt ağın ilk kullanılabilir adresi, ön uç IP adresi olarak atanır.
   > [!NOTE]
   > Ayrıldıktan sonra, IP adresi türü (statik veya dinamik) daha sonra değiştirilemez.
9. **İleri ' yi seçin: Backenler**.
10. **Arka uç Havuzu Ekle**' yi seçin.
11. **Ad** Için *Appgatewaybackendpool* yazın.
12. **Hedefleri olmayan arka uç havuzu ekleme** için **Evet**' i seçin. Hedefleri daha sonra ekleyeceksiniz.
13. **Add (Ekle)** seçeneğini belirleyin.
14. Ileri 'yi seçin **: yapılandırma**.
15. **Yönlendirme kuralları** altında, **yönlendirme kuralı ekle**' yi seçin.
16. **Kural adı** Için *rrule-01* yazın.
17. **Dinleyici adı** Için, *Listener-01* yazın.
18. **Ön uç IP 'si** için **özel**' i seçin.
19. Kalan Varsayılanları kabul edin ve **arka uç hedefleri** sekmesini seçin.
20. **Hedef türü** Için **arka uç havuzu**' nu seçin ve ardından **appgatewaybackendpool**' u seçin.
21. **Http ayarı** Için **Yeni Ekle**' yi seçin.
22. **Http ayar adı** için *http-Setting-01* yazın.
23. **Arka uç Protokolü** için **http**' yi seçin.
24. **Arka uç bağlantı noktası** için *80* yazın.
25. Kalan Varsayılanları kabul edin ve **Ekle**' yi seçin.
26. **Yönlendirme kuralı ekle** sayfasında **Ekle**' yi seçin.
27. **Sonraki: Etiketler**' i seçin.
28. **Sonraki: Gözden geçirme ve oluşturma**’yı seçin.
29. Özet sayfasındaki ayarları gözden geçirin ve ardından **Oluştur** ' u seçerek ağ kaynaklarını ve uygulama ağ geçidini oluşturun. Uygulama ağ geçidinin oluşturulması birkaç dakika sürebilir. Bir sonraki bölüme geçmeden önce Dağıtım başarıyla bitene kadar bekleyin.

## <a name="add-backend-pool"></a>Arka uç Havuzu Ekle

Arka uç havuzu, isteği sunan arka uç sunucularına istekleri yönlendirmek için kullanılır. Arka uç, NIC 'ler, sanal makine ölçek kümeleri, genel IP adresleri, iç IP adresleri, tam etki alanı adları (FQDN) ve Azure App Service gibi çok kiracılı arka uçlar olabilir. Bu örnekte, sanal makineleri hedef arka uç olarak kullanacaksınız. Var olan sanal makineleri kullanabilir ya da yeni bir tane oluşturabilirsiniz. Bu örnekte, Azure 'un uygulama ağ geçidi için arka uç sunucuları olarak kullandığı iki sanal makine oluşturursunuz.

Bunu yapmak için şunları yapın:

1. Arka uç sunucuları olarak kullanılan, *Myvm* ve *myVM2* olmak üzere iki yeni sanal makine oluşturun.
2. Uygulama ağ geçidinin başarıyla oluşturulduğunu doğrulamak için sanal makinelere IIS 'yi yükler.
3. Arka uç sunucularını arka uç havuzuna ekleyin.

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma


1. **Kaynak oluştur**’u seçin.
2. **İşlem** ' ı seçin ve ardından **sanal makine**' yi seçin.
4. Sanal makine için şu değerleri girin:
   - Aboneliğinizi seçin.
   - **Kaynak grubu** için *myResourceGroupAG* öğesini seçin.
   - **Sanal makine adı** Için *myvm* yazın.
   - **Görüntü** Için **Windows Server 2019 Datacenter** ' u seçin.
   - Geçerli bir **Kullanıcı adı** yazın.
   - Geçerli bir **parola** yazın.
1. Kalan Varsayılanları kabul edin ve **İleri ' yi seçin: diskler**.
1. Varsayılanları kabul edin ve **İleri ' yi seçin: ağ**.
1. Sanal ağ için **Myvnet** 'in seçildiğinden ve alt ağın **Mybackendsubnet** olduğundan emin olun.
1. Kalan Varsayılanları kabul edin ve **İleri: yönetim**' i seçin.
1. Önyükleme tanılamayı devre dışı bırakmak için **devre dışı bırak** seçeneğini belirleyin
1. **Gözden geçir ve oluştur**’u seçin.
1. Özet sayfasında ayarları gözden geçirin ve ardından **Oluştur**' u seçin. VM 'nin oluşturulması birkaç dakika sürebilir. Bir sonraki bölüme geçmeden önce Dağıtım başarıyla bitene kadar bekleyin.

### <a name="install-iis"></a>IIS yükleme

1. Cloud Shell açın ve **PowerShell** olarak ayarlandığından emin olun.
    ![Ekran görüntüsü PowerShell kullanan açık bir Azure Cloud Shell konsol penceresini gösterir.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Sanal makineye IIS yüklemek için aşağıdaki komutu çalıştırın:

   ```azurepowershell
   Set-AzVMExtension `
        -ResourceGroupName myResourceGroupAG `
        -ExtensionName IIS `
        -VMName myVM `
        -Publisher Microsoft.Compute `
        -ExtensionType CustomScriptExtension `
        -TypeHandlerVersion 1.4 `
        -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
         -Location CentralUS 

   ```

3. İkinci bir sanal makine oluşturun ve yeni tamamladığınız adımları kullanarak IIS yükleyin. Sanal makine adı ve içindeki için myVM2 kullanın `VMName` `Set-AzVMExtension` .

### <a name="add-backend-servers-to-backend-pool"></a>Arka uç sunucularını arka uç havuzuna Ekle

1. **Tüm kaynaklar**' ı ve ardından **myappgateway**' i seçin.
2. **Arka uç havuzları**' nı seçin ve ardından **Appgatewaybackendpool**' u seçin.
3. **Hedef türü** ' nün altında **sanal makine** ' yi seçin ve **hedef** altında myvm ile ilişkili vNIC 'i seçin.
4. MyVM2 eklemek için tekrarlayın.
   ![Hedef türleri ve hedefleri vurgulanmış şekilde arka uç havuzu bölmesini düzenleyin.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. Kaydet ' i seçin **.**

## <a name="create-a-client-virtual-machine"></a>İstemci sanal makinesi oluşturma

İstemci sanal makinesi, uygulama ağ geçidi arka uç havuzuna bağlanmak için kullanılır.

- Önceki adımları kullanarak üçüncü bir sanal makine oluşturun. Sanal makine adı için myVM3 kullanın.

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

1. MyAppGateway sayfasında, ön uç özel IP adresini göz önünde bırakmak için **ön uç IP yapılandırması** ' nı seçin.
    ![Özel türü vurgulanmış ön uç IP Yapılandırması bölmesi.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Özel IP adresini kopyalayın ve sonra uygulama ağ geçidi arka uç havuzuna erişmek için myVM3 üzerindeki tarayıcı adres çubuğuna yapıştırın.

## <a name="next-steps"></a>Sonraki adımlar

Arka uç havuzunuzun durumunu izlemek isterseniz, [Application Gateway Için arka uç sistem durumu ve tanılama günlükleri](application-gateway-diagnostics.md)bölümüne bakın.
