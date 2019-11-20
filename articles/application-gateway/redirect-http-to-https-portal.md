---
title: Portalda HTTP-HTTPS yeniden yönlendirmesi-Azure Application Gateway
description: HTTP yeniden yönlendirilen trafiğin Azure portalını kullanarak HTTPS ile bir uygulama ağ geçidi oluşturmayı öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: d67270896792ea506d2df04dcc3745a43d3d8251
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012875"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Application gateway, Azure portalını kullanarak HTTPS yeniden yönlendirmesi için HTTP ile oluşturma

Azure portalından oluşturmak için kullanabileceğiniz bir [uygulama ağ geçidi](overview.md) ile SSL sonlandırma için bir sertifika. Yönlendirme kuralı, application gateway'iniz HTTPS bağlantı noktasına HTTP trafiğini yönlendirmek için kullanılır. Ayrıca, bu örnekte, oluşturduğunuz bir [sanal makine ölçek kümesi](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) iki sanal makine örnekleri içeren application Gateway arka uç havuzu için.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Otomatik olarak imzalanan sertifika oluşturma
> * Ağ ayarlama
> * Sertifikalı bir uygulama ağ geçidi oluşturma
> * Dinleyici ve yeniden yönlendirme kuralı Ekle
> * Varsayılan arka uç havuzuyla bir sanal makine ölçek kümesi oluşturma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğretici, bir sertifika oluşturmak ve IIS yüklemek için Azure PowerShell modülü sürüm 1.0.0 veya üstünü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). Bu öğreticideki komutları çalıştırmak için de çalışmasına ihtiyacınız `Login-AzAccount` Azure ile bir bağlantı oluşturmak için.

## <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma

Üretim kullanımı için güvenilen bir sağlayıcı tarafından imzalanan geçerli bir sertifikayı içeri aktarmalısınız. Bu öğretici için [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) komutunu kullanarak otomatik olarak imzalanan bir sertifika oluşturursunuz. Sertifikadan pfx dosyası dışarı aktarmak için döndürülen Parmak izi ile [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) komutunu kullanabilirsiniz.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Bu sonuca benzer bir şey görmeniz gerekir:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

pfx dosyasını oluşturmak için parmak izini kullanın:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

Bir sanal ağ, oluşturduğunuz kaynakları arasındaki iletişim için gereklidir. Bu örnekte iki alt ağ oluşturulmuştur: biri uygulama ağ geçidi ve diğeri de arka uç sunucuları içindir. Uygulama ağ geçidini oluştururken aynı zamanda bir sanal makine oluşturabilirsiniz.

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesine tıklayın.
3. **Ağ** ve ardından Öne Çıkanlar listesinde **Application Gateway**’i seçin.
4. Uygulama ağ geçidi için şu değerleri girin:

   - *myAppGateway* - Uygulama ağ geçidinin adı.
   - *myResourceGroupAG* - Yeni kaynak grubu.

     ![Yeni uygulama ağ geçidi oluşturma](./media/create-url-route-portal/application-gateway-create.png)

5. Diğer ayarların varsayılan değerlerini kabul edin ve sonra **Tamam**’a tıklayın.
6. Tıklayın **bir sanal ağ seçin**, tıklayın **Yeni Oluştur**ve ardından sanal ağ için şu değerleri girin:

   - *myVNet* - Sanal ağın adı.
   - *10.0.0.0/16* - Sanal ağın adres alanı.
   - *myAGSubnet* - Alt ağın adı.
   - *10.0.1.0/24* - alt ağ adres alanı.

     ![Sanal ağ oluşturma](./media/create-url-route-portal/application-gateway-vnet.png)

7. Sanal ağı ve alt ağı oluşturmak için **Tamam**’a tıklayın.
8. Altında **ön uç IP yapılandırması**, olun **IP adresi türü** olduğu **genel**, ve **Yeni Oluştur** seçilir. Girin *myAGPublicIPAddress* adı. Diğer ayarların varsayılan değerlerini kabul edin ve sonra **Tamam**’a tıklayın.
9. Altında **dinleyici Yapılandırması**seçin **HTTPS**, ardından **bir dosya seçin** gidin *c:\appgwcert.pfx* dosya ve seçin **açık**.
10. Tür *appgwcert* sertifika adını ve *Azure123456!* Parola.
11. Web uygulaması güvenlik duvarı devre dışı bırakın ve ardından **Tamam**.
12. Özet sayfasında ayarları gözden geçirin ve ardından **Tamam** ağ kaynaklarının ve uygulama ağ geçidi oluşturmak için. Bu, uygulama ağ geçidinin oluşturulması, sonraki bölüme geçmeden önce dağıtımın başarıyla tamamlanana kadar bekleyin birkaç dakika sürebilir.

### <a name="add-a-subnet"></a>Alt ağ ekleme

1. Seçin **tüm kaynakları** seçin ve soldaki menüden **myVNet** ve kaynak listesinden.
2. Seçin **alt ağlar**ve ardından **alt**.

    ![Alt ağ oluşturma](./media/create-url-route-portal/application-gateway-subnet.png)

3. Tür *myBackendSubnet* için alt ağın adı.
4. Tür *10.0.2.0/24* adres aralığını ve seçin için **Tamam**.

## <a name="add-a-listener-and-redirection-rule"></a>Dinleyici ve yeniden yönlendirme kuralı Ekle

### <a name="add-the-listener"></a>Dinleyici Ekle

İlk olarak, adlı dinleyiciyi ekleyin *myListener* bağlantı noktası 80 için.

1. Açık **myResourceGroupAG** kaynak grubu ve select **myAppGateway**.
2. Seçin **dinleyicileri** seçip **+ temel**.
3. Tür *MyListener* adı.
4. Tür *httpPort* yeni ön uç bağlantı noktası adının ve *80* bağlantı noktası.
5. Protokol ayarlandığından emin olun **HTTP**ve ardından **Tamam**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Bir yeniden yönlendirme yapılandırması ile yönlendirme kuralı Ekle

1. Üzerinde **myAppGateway**seçin **kuralları** seçip **+ temel**.
2. İçin **adı**, türü *bağlanma2*.
3. Olun **MyListener** dinleyici için seçilir.
4. Seçin **yeniden yönlendirmeyi yapılandırma** onay kutusu.
5. İçin **yönlendirme türü**seçin **kalıcı**.
6. İçin **yeniden yönlendirme hedefi**seçin **dinleyici**.
7. Olun **hedef dinleyici** ayarlanır **appGatewayHttpListener**.
8. Seçin **sorgu dizesi dahil et** ve **yoluna** onay kutuları.
9. **Tamam**’ı seçin.

## <a name="create-a-virtual-machine-scale-set"></a>Sanal makine ölçek kümesi oluşturma

Bu örnekte uygulama ağ geçidinde arka uç havuzu için sunucu sağlayan bir sanal makine ölçek kümesi oluşturacaksınız.

1. Portal sol üst köşesinde, seçin **+ kaynak Oluştur**.
2. Seçin **işlem**.
3. Arama kutusuna *ölçek kümesi* ve Enter tuşuna basın.
4. Seçin **sanal makine ölçek kümesi**ve ardından **Oluştur**.
5. İçin **sanal makine ölçek kümesi adı**, türü *myvmss*.
6. İşletim sistemi disk görüntüsü için ** olun **Windows Server 2016 Datacenter** seçilir.
7. İçin **kaynak grubu**seçin **myResourceGroupAG**.
8. İçin **kullanıcı adı**, türü *azureuser*.
9. İçin **parola**, türü *Azure123456!* ve parolayı onaylayın.
10. İçin **örnek sayısı**, değeri olduğundan emin olun **2**.
11. İçin **örnek boyutu**seçin **D2s_v3**.
12. Altında **ağ**, olun **Yük Dengeleme seçeneklerini seçin** ayarlanır **Application Gateway**.
13. Olun **uygulama ağ geçidi** ayarlanır **myAppGateway**.
14. Olun **alt** ayarlanır **myBackendSubnet**.
15. **Oluştur**'u seçin.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Ölçek kümesini uygun arka uç havuzu ile ilişkilendirme

Sanal makine ölçek kümesi portalı kullanıcı arabirimini ölçek kümesi için yeni bir arka uç havuzu oluşturur, ancak, varolan appGatewayBackendPool ile ilişkilendirmek istediğiniz.

1. Açık **myResourceGroupAg** kaynak grubu.
2. Seçin **myAppGateway**.
3. Seçin **arka uç havuzları**.
4. Seçin **myAppGatewaymyvmss**.
5. Seçin **tüm hedefleri arka uç havuzundan kaldırmak**.
6. **Kaydet**’i seçin.
7. Bu işlem tamamlandıktan sonra seçin **myAppGatewaymyvmss** arka uç havuzu, select **Sil** ardından **Tamam** onaylamak için.
8. Seçin **appGatewayBackendPool**.
9. Altında **hedefleri**seçin **VMSS**.
10. Altında **VMSS**seçin **myvmss**.
11. Altında **ağ arabirimi yapılandırması**seçin **myvmssNic**.
12. **Kaydet**’i seçin.

### <a name="upgrade-the-scale-set"></a>Ölçek kümelerini yükseltme

Son olarak, bu değişikliklerle ölçek yükseltmeniz gerekir.

1. Seçin **myvmss** ölçek kümesi.
2. Altında **ayarları**seçin **örnekleri**.
3. Her iki örnek seçin ve ardından **yükseltme**.
4. Onaylamak için **Evet**’i seçin.
5. Bu tamamlandıktan sonra dönün **myAppGateway** seçip **arka uç havuzları**. Şimdi, görürsünüz **appGatewayBackendPool** iki hedefi vardır ve **myAppGatewaymyvmss** sıfır hedeflere sahip.
6. Seçin **myAppGatewaymyvmss**ve ardından **Sil**.
7. Seçin **Tamam** onaylamak için.

### <a name="install-iis"></a>IIS yükleme

Ölçek kümesinde IIS yüklemek için kolay bir yolu ise PowerShell kullanmaktır. Portal'dan Cloud Shell simgesine tıklayın ve emin **PowerShell** seçilir.

PowerShell penceresine aşağıdaki kodu yapıştırın ve Enter tuşuna basın.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Ölçek kümelerini yükseltme

IIS örnekleriyle değiştirdikten sonra yine bu değişiklikle birlikte ölçek yükseltmeniz gerekir.

1. Seçin **myvmss** ölçek kümesi.
2. Altında **ayarları**seçin **örnekleri**.
3. Her iki örnek seçin ve ardından **yükseltme**.
4. Onaylamak için **Evet**’i seçin.

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Uygulama genel IP adresi uygulama ağ geçidi genel bakış sayfasından alabilirsiniz.

1. Seçin **myAppGateway**.
2. Üzerinde **genel bakış** sayfasında, IP adresi altında **ön uç genel IP adresi**.

3. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. Örneğin, http://52.170.203.149

   ![Güvenli uyarı](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Otomatik olarak imzalanan sertifika kullandıysanız güvenlik uyarısını kabul etmek için, **Ayrıntılar**’ı seçin ve sonra **Web sayfasına gidin**: Güvenli IIS siteniz, sonra aşağıdaki örnekte olduğu gibi görüntülenir:

   ![Temel URL’yi uygulama ağ geçidinde test etme](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Sonraki adımlar

Bilgi edinmek için nasıl [iç yeniden yönlendirmeyi ile bir uygulama ağ geçidi oluşturma](redirect-internal-site-powershell.md).
