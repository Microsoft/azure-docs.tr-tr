---
title: Portalda HTTP-HTTPS yeniden yönlendirmesi-Azure Application Gateway
description: Azure portal kullanarak HTTP 'den HTTPS 'ye yeniden yönlendirilen trafiğe sahip bir uygulama ağ geçidi oluşturmayı öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: e7fb40d95c4659bf353366770da7c903ffa1bd09
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867232"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Azure portal kullanarak HTTP ile HTTPS yönlendirmesi arasında bir uygulama ağ geçidi oluşturma

TLS sonlandırma sertifikası ile bir [uygulama ağ geçidi](overview.md) oluşturmak için Azure Portal kullanabilirsiniz. Yönlendirme kuralı, HTTP trafiğini uygulama ağ geçidinizdeki HTTPS bağlantı noktasına yönlendirmek için kullanılır. Bu örnekte, iki sanal makine örneği içeren uygulama ağ geçidinin arka uç havuzu için bir [sanal makine ölçek kümesi](../virtual-machine-scale-sets/overview.md) de oluşturacaksınız.

Bu makalede şunları öğreneceksiniz:

* Otomatik olarak imzalanan sertifika oluşturma
* Ağ ayarlama
* Sertifikalı bir uygulama ağ geçidi oluşturma
* Dinleyici ve yeniden yönlendirme kuralı ekleme
* Varsayılan arka uç havuzuyla bir sanal makine ölçek kümesi oluşturma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğretici, bir sertifika oluşturmak ve IIS yüklemek için Azure PowerShell modülü sürüm 1.0.0 veya üstünü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). Bu öğreticideki komutları çalıştırmak için, `Login-AzAccount` Azure ile bağlantı oluşturmak için komutunu da çalıştırmanız gerekir.

## <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma

Üretim kullanımı için, güvenilir bir sağlayıcı tarafından imzalanmış geçerli bir sertifikayı içeri aktarmanız gerekir. Bu öğretici için [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) komutunu kullanarak otomatik olarak imzalanan bir sertifika oluşturursunuz. Sertifikadan pfx dosyası dışarı aktarmak için döndürülen Parmak izi ile [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate) komutunu kullanabilirsiniz.

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

Oluşturduğunuz kaynaklar arasında iletişim için bir sanal ağ gerekir. Bu örnekte iki alt ağ oluşturulmuştur: biri uygulama ağ geçidi ve diğeri de arka uç sunucuları içindir. Uygulama ağ geçidini oluştururken aynı zamanda bir sanal makine oluşturabilirsiniz.

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesine tıklayın.
3. **Ağ** ve ardından Öne Çıkanlar listesinde **Application Gateway**’i seçin.
4. Uygulama ağ geçidi için şu değerleri girin:

   - *myAppGateway* - Uygulama ağ geçidinin adı.
   - *myResourceGroupAG* - Yeni kaynak grubu.

     ![Yeni uygulama ağ geçidi oluşturma](./media/create-url-route-portal/application-gateway-create.png)

5. Diğer ayarların varsayılan değerlerini kabul edin ve sonra **Tamam**’a tıklayın.
6. **Sanal ağ Seç**' e tıklayın, **Yeni oluştur**' a tıklayın ve ardından sanal ağ için şu değerleri girin:

   - *myVNet* - Sanal ağın adı.
   - *10.0.0.0/16* - Sanal ağın adres alanı.
   - *myAGSubnet* - Alt ağın adı.
   - *10.0.0.0/24* - Alt ağın adres alanı.

     ![Sanal ağ oluşturma](./media/create-url-route-portal/application-gateway-vnet.png)

7. Sanal ağı ve alt ağı oluşturmak için **Tamam**’a tıklayın.
8. **Ön uç IP yapılandırması** altında, **IP adresi türünün** **ortak** olduğundan emin olun ve **Yeni oluştur** ' u seçilidir. Ad için *Myagpublicıpaddress* girin. Diğer ayarların varsayılan değerlerini kabul edin ve sonra **Tamam**’a tıklayın.
9. **Dinleyici yapılandırması** altında **https**' yi seçin, ardından **bir dosya seç** ' i seçin ve *C:\appgwcert.exe* dosyasına gidin ve **Aç**' ı seçin.
10. Sertifika adı ve Azure123456 için *appgwcert* yazın *!* girin.
11. Web uygulaması güvenlik duvarını devre dışı bırakın ve ardından **Tamam**' ı seçin.
12. Özet sayfasındaki ayarları gözden geçirin ve ardından ağ kaynaklarını ve uygulama ağ geçidini oluşturmak için **Tamam** ' ı seçin. Uygulama ağ geçidinin oluşturulması birkaç dakika sürebilir, bir sonraki bölüme geçmeden önce Dağıtım başarıyla bitene kadar bekleyin.

### <a name="add-a-subnet"></a>Alt ağ ekleme

1. Sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından kaynaklar listesinden **myvnet** ' i seçin.
2. **Alt ağlar**' ı seçin ve **alt ağ**' a tıklayın.

    ![Alt ağ oluşturma](./media/create-url-route-portal/application-gateway-subnet.png)

3. Alt ağ adı için *Mybackendsubnet* yazın.
4. Adres aralığı için *10.0.2.0/24* yazın ve ardından **Tamam**' ı seçin.

## <a name="add-a-listener-and-redirection-rule"></a>Dinleyici ve yeniden yönlendirme kuralı ekleme

### <a name="add-the-listener"></a>Dinleyiciyi ekleyin

İlk olarak, 80 numaralı bağlantı noktası için *MyListener* adlı dinleyiciyi ekleyin.

1. **MyResourceGroupAG** kaynak grubunu açın ve **myappgateway**' i seçin.
2. **Dinleyicileri** seçin ve **+ temel**' yı seçin.
3. Ad için *MyListener* yazın.
4. Yeni ön uç bağlantı noktası adı ve bağlantı noktası *80* Için *httpport* yazın.
5. Protokolün **http** olarak ayarlandığından emin olun ve ardından **Tamam**' ı seçin.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Yeniden yönlendirme yapılandırması ile yönlendirme kuralı ekleme

1. **Myappgateway**'de, **kurallar** ' ı seçin ve ardından **+ istek yönlendirme kuralı**' nı seçin.
2. **Kural adı** için *bağlanma2* yazın.
3. Dinleyici için **MyListener** öğesinin seçili olduğundan emin olun.
4. **Arka uç hedefleri** sekmesine tıklayın ve **hedef tür** ' i *yeniden yönlendirme* olarak seçin.
5. **Yeniden yönlendirme türü** için **kalıcı**' ı seçin.
6. **Yeniden yönlendirme hedefi** için **dinleyici**' i seçin.
7. **Hedef dinleyicinin** **Appgatewayhttplistener** olarak ayarlandığından emin olun.
8. **İçerme sorgu dizesi** ve **Içerme yolu** için *Evet*' i seçin.
9. **Add (Ekle)** seçeneğini belirleyin.

## <a name="create-a-virtual-machine-scale-set"></a>Sanal makine ölçek kümesi oluşturma

Bu örnekte uygulama ağ geçidinde arka uç havuzu için sunucu sağlayan bir sanal makine ölçek kümesi oluşturacaksınız.

1. Portalın sol üst köşesinde **+ kaynak oluştur**' u seçin.
2. **İşlem**’i seçin.
3. Arama kutusuna *Ölçek kümesi* yazın ve ENTER tuşuna basın.
4. **Sanal makine ölçek kümesi**' ni seçin ve ardından **Oluştur**' u seçin.
5. **Sanal makine ölçek kümesi adı** için *myvmss* yazın.
6. Işletim sistemi disk görüntüsü için * * **Windows Server 2016 Datacenter** ' ın seçili olduğundan emin olun.
7. **Kaynak grubu** için **myResourceGroupAG**' yi seçin.
8. **Kullanıcı adı** için *azureuser* yazın.
9. **Parola** için *Azure123456* yazın. ve parolayı onaylayın.
10. **Örnek sayısı** için değerin **2** olduğundan emin olun.
11. **Örnek boyutu** için **D2s_v3**' yi seçin.
12. **Ağ** altında, **Yük Dengeleme seçeneklerini seçme** seçeneğinin **Application Gateway** olarak ayarlandığından emin olun.
13. **Application Gateway** 'In **myappgateway** olarak ayarlandığından emin olun.
14. **Alt ağın** **mybackendsubnet** olarak ayarlandığından emin olun.
15. **Oluştur**’u seçin.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Ölçek kümesini uygun arka uç havuzuyla ilişkilendir

Sanal makine ölçek kümesi portalı Kullanıcı arabirimi, ölçek kümesi için yeni bir arka uç havuzu oluşturur, ancak bunu mevcut appGatewayBackendPool ile ilişkilendirmek istiyorsunuz.

1. **MyResourceGroupAg** kaynak grubunu açın.
2. **Myappgateway**' i seçin.
3. **Arka uç havuzlarını** seçin.
4. **Myappgatewaymyvmss** öğesini seçin.
5. **Arka uç havuzundan tüm hedefleri kaldır**' ı seçin.
6. **Kaydet**’i seçin.
7. Bu işlem tamamlandıktan sonra, **Myappgatewaymyvmss** arka uç havuzunu seçin, **Sil** ' i seçin ve ardından onaylamak için **Tamam** ' ı seçin.
8. **Appgatewaybackendpool** öğesini seçin.
9. **Hedefler** altında **VMSS**' yi seçin.
10. **VMSS** altında **myvmss**' yi seçin.
11. **Ağ arabirimi yapılandırması** altında, **Myvmssnıc**' i seçin.
12. **Kaydet**’i seçin.

### <a name="upgrade-the-scale-set"></a>Ölçek kümesini yükseltme

Son olarak, ölçek kümesini bu değişikliklerle yükseltmeniz gerekir.

1. **Myvmss** ölçek kümesini seçin.
2. **Ayarlar**’ın altında **Örnekler**’i seçin.
3. Her iki örneği de seçip **Yükselt**' i seçin.
4. Onaylamak için **Evet**’i seçin.
5. Bu işlem tamamlandıktan sonra, **Myappgateway** 'e dönün ve **arka uç havuzları**' nı seçin. Şimdi **Appgatewaybackendpool** 'un iki hedefi olduğunu ve  **Myappgatewaymyvmss** 'nin sıfır hedefi olduğunu görmeniz gerekir.
6. **Myappgatewaymyvmss**' yi seçin ve **Sil**' i seçin.
7. Onaylamak için **Tamam**’ı seçin.

### <a name="install-iis"></a>IIS yükleme

Ölçek kümesine IIS yüklemenin kolay bir yolu PowerShell kullanmaktır. Portaldan Cloud Shell simgesine tıklayın ve **PowerShell** 'in seçili olduğundan emin olun.

Aşağıdaki kodu PowerShell penceresine yapıştırın ve ENTER tuşuna basın.

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

### <a name="upgrade-the-scale-set"></a>Ölçek kümesini yükseltme

Örnekleri IIS ile değiştirdikten sonra ölçek kümesini bu değişiklik ile yeniden yükseltmeniz gerekir.

1. **Myvmss** ölçek kümesini seçin.
2. **Ayarlar**’ın altında **Örnekler**’i seçin.
3. Her iki örneği de seçip **Yükselt**' i seçin.
4. Onaylamak için **Evet**’i seçin.

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Uygulamanın genel IP adresini uygulama ağ geçidine genel bakış sayfasından alabilirsiniz.

1. **Myappgateway**' i seçin.
2. **Genel bakış** sayfasında, **ön uç genel IP adresi** altındaki IP adresine göz atın.

3. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. Örneğin, http://52.170.203.149

   ![Güvenli uyarı](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Otomatik olarak imzalanan bir sertifika kullandıysanız güvenlik uyarısını kabul etmek için **Ayrıntılar** ' ı seçin ve ardından **Web sayfasına gidin**. Güvenli IIS siteniz, sonra aşağıdaki örnekte olduğu gibi görüntülenir:

   ![Temel URL’yi uygulama ağ geçidinde test etme](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Sonraki adımlar

[İç yeniden yönlendirmeye sahip bir uygulama ağ geçidi oluşturmayı](redirect-internal-site-powershell.md)öğrenin.
