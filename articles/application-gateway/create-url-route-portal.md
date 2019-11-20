---
title: 'Öğretici: Portal kullanılarak URL yolu tabanlı yönlendirme kuralları-Azure Application Gateway'
description: Bu öğreticide, Azure portal kullanarak bir uygulama ağ geçidi ve sanal makine ölçek kümesi için URL yol tabanlı yönlendirme kuralları oluşturmayı öğreneceksiniz.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: bc810ac7901d83f03d3f3ac2199561225326d261
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048133"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak yol tabanlı yönlendirme kuralları ile uygulama ağ geçidi oluşturma

[Uygulama ağ geçidi](application-gateway-introduction.md)oluştururken [URL yolu tabanlı yönlendirme kurallarını](application-gateway-url-route-overview.md) yapılandırmak için Azure Portal kullanabilirsiniz. Bu öğreticide, sanal makineleri kullanarak arka uç havuzları oluşturacaksınız. Daha sonra, Web trafiğinin havuzlardaki uygun sunuculara ulaştığınızdan emin olmak için yönlendirme kuralları oluşturursunuz.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Uygulama ağ geçidi oluşturma
> * Arka uç sunucuları için sanal makineler oluşturma
> * Arka uç sunucularıyla arka uç havuzları oluşturma
> * Arka uç dinleyicisi oluşturma
> * Yol tabanlı yönlendirme kuralı oluşturma

![URL yönlendirme örneği](./media/application-gateway-create-url-route-portal/scenario.png)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Bu örnekte, uygulama ağ geçidi için arka uç sunucular olarak kullanılacak üç sanal makine oluşturursunuz. Ayrıca, uygulama ağ geçidinin beklendiği gibi çalıştığını doğrulamak için sanal makinelere IIS yüklersiniz.

1. Azure portal **kaynak oluştur**' u seçin.
2. Popüler listede **Windows Server 2016 Datacenter** ' ı seçin.
3. Sanal makine için şu değerleri girin:

    - **Kaynak grubu**' nu seçin, **Yeni oluştur**' u ve ardından *myResourceGroupAG*yazın.
    - **Sanal makine adı**: *myVM1*
    - **Bölge**: *(US) Doğu ABD*
    - **Kullanıcı adı**: *azureuser*
    - **Parola**: *Azure123456!*


4. **İleri ' yi seçin: diskler**.
5. **İleri ' yi seçin: ağ**
6. **Sanal ağ**Için **Yeni oluştur** ' u seçin ve sanal ağ için şu değerleri yazın:

   - *myVNet* - Sanal ağın adı.
   - *10.0.0.0/16* - Sanal ağın adres alanı.
   - ilk alt ağ adı için *Mybackendsubnet*
   - *10.0.1.0/24* - alt ağ adres alanı.
   - *Myagsubnet* -ikinci alt ağ adı için.
   - *10.0.0.0/24* - Alt ağın adres alanı.
7. **Tamam**’ı seçin.

8. **Ağ arabirimi**altında **mybackendsubnet** alt ağ için seçili olduğundan emin olun ve ardından **İleri: yönetim**' i seçin.
9. Önyükleme tanılamayı devre dışı bırakmak için **Kapat** ' ı seçin.
10. **Gözden geçir + oluştur**' a tıklayın, Özet sayfasındaki ayarları gözden geçirin ve ardından **Oluştur**' u seçin.
11. *MyVM2* ve *myVM3* adlı iki sanal makine oluşturun ve bunları *Myvnet* sanal ağına ve *mybackendsubnet* alt ağına yerleştirin.

### <a name="install-iis"></a>IIS yükleme

1. Etkileşimli kabuğu açın ve **PowerShell**'e ayarlandığından emin olun.

    ![Özel uzantıyı yükleme](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Sanal makineye IIS yüklemek için aşağıdaki komutu çalıştırın: 

    ```azurepowershell
         $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

        Set-AzVMExtension `
         -ResourceGroupName myResourceGroupAG `
         -Location eastus `
         -ExtensionName IIS `
         -VMName myVM1 `
         -Publisher Microsoft.Compute `
         -ExtensionType CustomScriptExtension `
         -TypeHandlerVersion 1.4 `
         -Settings $publicSettings
    ```

3. İki sanal makine oluşturun ve az önce bitirdiğiniz adımları kullanarak IIS 'yi yükleyebilirsiniz. Adlar için *myVM2* ve *MyVM3* adlarını ve set-azvmexgerin içindeki VMName değerlerini girin.

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

1. Azure portal sol menüsünde **kaynak oluştur** ' u seçin. **Yeni** pencere görüntülenir.

2. **Ağ** ' ı seçin ve ardından **öne çıkan** listede **Application Gateway** ' yi seçin.

### <a name="basics-tab"></a>Temel bilgiler sekmesi

1. **Temel bilgiler** sekmesinde, aşağıdaki uygulama ağ geçidi ayarları için şu değerleri girin:

   - **Kaynak grubu**: kaynak grubu için **myResourceGroupAG** öğesini seçin.
   - **Uygulama ağ geçidi adı**: uygulama ağ geçidinin adı Için *myappgateway* girin.
   - **Bölge** -Seç **(US) Doğu ABD**.

        ![Yeni uygulama ağ geçidi oluşturma: temel bilgiler](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  **Sanal ağı Yapılandır**altında sanal ağın adı Için **myvnet** ' i seçin.
3. Alt ağ için **Myagsubnet** öğesini seçin.
3. Diğer ayarlar için varsayılan değerleri kabul edin ve ardından **İleri: ön uçlar**' ı seçin.

### <a name="frontends-tab"></a>Ön uçlar sekmesi

1. Ön **uçlar** sekmesinde, **ön uç IP adresi türünün** **genel**olarak ayarlandığını doğrulayın.

   > [!NOTE]
   > Application Gateway v2 SKU 'SU için yalnızca **genel** ön uç IP yapılandırması ' nı seçebilirsiniz. Özel ön uç IP yapılandırması şu anda bu v2 SKU 'SU için etkin değil.

2. **Genel IP** adresi Için **Yeni oluştur** ' u seçin ve genel IP adresi adı Için *myagpublicıpaddress* girin ve **Tamam**' ı seçin. 
3. **İleri ' yi seçin: Backenler**.

### <a name="backends-tab"></a>Backends sekmesi

Arka uç havuzu, isteği sunan arka uç sunucularına istekleri yönlendirmek için kullanılır. Arka uç havuzları, NIC 'Ler, sanal makine ölçek kümeleri, genel IP 'Ler, iç IP 'Ler, tam etki alanı adları (FQDN) ve Azure App Service gibi çok kiracılı arka uçlar olabilir.

1. **Backends** sekmesinde **+ arka uç Havuzu Ekle**' yi seçin.

2. Açılan **bir arka uç havuzu ekleyin** penceresinde, boş bir arka uç havuzu oluşturmak için aşağıdaki değerleri girin:

    - **Ad**: arka uç havuzunun adı Için *mybackendpool* girin.
3. **Arka uç hedefleri**altında **hedef tür**' i aşağı açılan listeden **sanal makine** ' yi seçin.

5. **Hedef** altında **myVM1**için ağ arabirimini seçin.
6. **Add (Ekle)** seçeneğini belirleyin.
7. Hedef olarak *myVM2* ile *bir görüntü arka uç* havuzu ve hedef olarak *myVM3* ile bir *video* arka uç havuzu eklemek için tekrarlayın.
8. Arka uç havuzu yapılandırmasını kaydetmek ve **backends** sekmesine dönmek için **Ekle** ' yi seçin.

4. **Backends** sekmesinde **İleri: yapılandırma**' yı seçin.

### <a name="configuration-tab"></a>Yapılandırma sekmesi

**Yapılandırma** sekmesinde, bir yönlendirme kuralı kullanarak oluşturduğunuz ön uç ve arka uç havuzunu bağlayacaksınız.

1. **Yönlendirme kuralları** sütununda **Kural Ekle** ' yi seçin.

2. Açılan **yönlendirme kuralı ekle** penceresinde, **kural adı**için *myroutingrule* yazın.

3. Yönlendirme kuralı bir dinleyici gerektirir. **Yönlendirme kuralı ekle** penceresinin içindeki **dinleyici** sekmesinde, dinleyici için aşağıdaki değerleri girin:

    - **Dinleyici adı**: dinleyicinin adı Için *MyListener* girin.
    - **Ön uç IP**: ön uç için oluşturduğunuz ortak IP 'yi seçmek için **genel** ' i seçin.
    - **Bağlantı noktası**: *8080* yazın
  
        **Dinleyici** sekmesinde diğer ayarlar için varsayılan değerleri kabul edin, sonra yönlendirme kuralının geri kalanını yapılandırmak Için **arka uç hedefleri** sekmesini seçin.

4. **Arka uç hedefleri** sekmesinde, **arka uç hedefi**Için **mybackendpool** ' u seçin.

5. **Http ayarı**Için yeni **Oluştur** ' u seçerek yeni bir http ayarı oluşturun. HTTP ayarı, yönlendirme kuralının davranışını tespit eder. 

6. Açılan **http ayarı Ekle** penceresinde **http ayar adı**için *myhttpsetting* girin. **Http ayarı Ekle** penceresinde diğer ayarlar için varsayılan değerleri kabul edin ve ardından **Ekle** ' yi seçerek **yönlendirme kuralı ekle** penceresine dönün.
7. **Yol tabanlı yönlendirme**altında, **yol tabanlı bir kural oluşturmak için birden çok hedef Ekle**' yi seçin.
8. **Yol**için */images/* \*yazın.
9. **Yol kuralı adı**için *görüntüler*yazın.
10. **Http ayarı**Için **myhttpsetting** ' i seçin.
11. **Arka uç hedefi**için **görüntüler**' i seçin.
12. Yol kuralını kaydetmek ve **yönlendirme kuralı ekle** sekmesine dönmek için **Ekle** ' yi seçin.
13. Video için başka bir kural eklemek üzere tekrarlayın.
14. Yönlendirme kuralını eklemek ve **yapılandırma** sekmesine dönmek için **Ekle** ' yi seçin.
15. Ileri ' yi seçin **: Etiketler** ve sonra **İleri: İnceleme + oluştur**.

> [!NOTE]
> Varsayılan örnekleri işlemek için özel bir */* * yol kuralı eklemeniz gerekmez. Bu otomatik olarak varsayılan arka uç havuzu tarafından işlenir.

### <a name="review--create-tab"></a>Gözden geçir + Oluştur sekmesi

**Gözden geçir + oluştur** sekmesindeki ayarları gözden geçirin ve ardından **Oluştur** ' u seçerek sanal ağı, genel IP adresini ve uygulama ağ geçidini oluşturun. Azure 'da uygulama ağ geçidini oluşturmak birkaç dakika sürebilir. Bir sonraki bölüme geçmeden önce Dağıtım başarıyla bitene kadar bekleyin.


## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

1. **Tüm kaynaklar**' ı ve ardından **myappgateway**' i seçin.

    ![Uygulama ağ geçidi genel IP adresini kaydetme](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. Örneğin, http:\//52.188.72.175:8080.

    ![Temel URL’yi uygulama ağ geçidinde test etme](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   Bağlantı noktası 8080 üzerindeki dinleyici, bu isteği varsayılan arka uç havuzuna yönlendirir.

3. URL 'YI *http://&lt;IP adresi&gt;: 8080/images/test.htm*olarak değiştirin, ıp adresi&gt; &lt;IP adresiniz ile değiştirin ve aşağıdaki örneğe benzer bir şey görmeniz gerekir:

    ![Görüntü URL’sini uygulama ağ geçidinde test etme](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   Bağlantı noktası 8080 ' deki dinleyici, bu isteği *görüntüler* arka uç havuzuna yönlendirir.

4. URL 'YI *http://&lt;IP adresi&gt;: 8080/video/test.htm*olarak değiştirin, ıp adresi&gt; &lt;IP adresiniz ile değiştirin ve aşağıdaki örneğe benzer bir şey görmeniz gerekir:

    ![Video URL’sini uygulama ağ geçidinde test etme](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   Bağlantı noktası 8080 üzerindeki dinleyici, bu isteği *video* arka uç havuzuna yönlendirir.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Application Gateway uçtan uca SSL 'yi etkinleştirme](application-gateway-backend-ssl.md)
