---
title: 'Öğretici: portalda SSL sonlandırmasını Yapılandırma-Azure Application Gateway'
description: Bu öğreticide, Azure portal kullanarak bir uygulama ağ geçidini yapılandırmayı ve SSL sonlandırma için bir sertifika eklemeyi öğreneceksiniz.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: b4278fc6d44f32921713681cb094b659901cc87c
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012318"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak SSL sonlandırmasına sahip bir uygulama ağ geçidi yapılandırma

Arka uç sunucuları için sanal makineler kullanan SSL sonlandırma sertifikasıyla bir [uygulama ağ geçidi](overview.md) yapılandırmak için Azure Portal kullanabilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Otomatik olarak imzalanan sertifika oluşturma
> * Sertifikalı bir uygulama ağ geçidi oluşturma
> * Arka uç sunucuları olarak kullanılan sanal makineleri oluşturma
> * Uygulama ağ geçidini test etme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma

Bu bölümde, kendinden imzalı bir sertifika oluşturmak için [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) kullanın. Uygulama ağ geçidi için dinleyiciyi oluştururken Azure portal sertifikayı karşıya yüklersiniz.

Yerel bilgisayarınızda, yönetici olarak bir Windows PowerShell penceresi açın. Sertifikayı oluşturmak için aşağıdaki komutu çalıştırın:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Şu yanıta benzer bir şey görmeniz gerekir:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Sertifikadan pfx dosyasını dışarı aktarmak için döndürülen parmak Izine sahip [Export-Pfxsertifikası](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) ' nı kullanın:

> [!NOTE]
> . Pfx dosya parolanbir özel karakter kullanmayın. Yalnızca alfasayısal karakterler desteklenir.

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

1. Azure portal sol menüsünde **kaynak oluştur** ' u seçin. **Yeni** pencere görüntülenir.

2. **Ağ** ' ı seçin ve ardından **öne çıkan** listede **Application Gateway** ' yi seçin.

### <a name="basics-tab"></a>Temel bilgiler sekmesi

1. **Temel bilgiler** sekmesinde, aşağıdaki uygulama ağ geçidi ayarları için şu değerleri girin:

   - **Kaynak grubu**: kaynak grubu için **myResourceGroupAG** öğesini seçin. Yoksa, oluşturmak için **Yeni oluştur** ' u seçin.
   - **Uygulama ağ geçidi adı**: uygulama ağ geçidinin adı Için *myappgateway* girin.

        ![Yeni uygulama ağ geçidi oluşturma: temel bilgiler](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Azure 'un, oluşturduğunuz kaynaklar arasında iletişim kurması için bir sanal ağa ihtiyacı vardır. Yeni bir sanal ağ oluşturabilir veya var olan bir ağı kullanabilirsiniz. Bu örnekte, uygulama ağ geçidini oluşturduğunuz sırada yeni bir sanal ağ oluşturacaksınız. Application Gateway örnekleri ayrı alt ağlarda oluşturulur. Bu örnekte iki alt ağ oluşturursunuz: bir tane uygulama ağ geçidi ve arka uç sunucuları için bir diğeri.

    **Sanal ağı Yapılandır**altında **Yeni oluştur**' u seçerek yeni bir sanal ağ oluşturun. Açılan **sanal ağ oluştur** penceresinde, sanal ağ ve iki alt ağ oluşturmak için aşağıdaki değerleri girin:

    - **Ad**: sanal ağın adı Için *myvnet* girin.

    - **Alt ağ adı** (Application Gateway alt ağ): **alt ağlar** Kılavuzu *varsayılan*olarak adlandırılan bir alt ağ gösterir. Bu alt ağın adını *Myagsubnet*olarak değiştirin.<br>Application Gateway alt ağı yalnızca uygulama ağ geçitleri içerebilir. Başka hiçbir kaynağa izin verilmez.

    - **Alt ağ adı** (arka uç sunucusu alt ağı): alt **ağlar** kılavuzunun Ikinci satırında, **alt ağ adı** sütununa *mybackendsubnet* yazın.

    - **Adres aralığı** (arka uç sunucusu alt ağı): **alt ağlar** kılavuzunun Ikinci satırına *myagsubnet*adres aralığıyla çakışmayacak bir adres aralığı girin. Örneğin, *Myagsubnet* adres aralığı 10.0.0.0/24 Ise, *Mybackendsubnet*adres aralığı için *10.0.1.0/24* girin.

    **Sanal ağ oluştur** penceresini kapatmak ve sanal ağ ayarlarını kaydetmek için **Tamam ' ı** seçin.

    ![Yeni uygulama ağ geçidi oluştur: sanal ağ](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. **Temel bilgiler** sekmesinde, diğer ayarlar için varsayılan değerleri kabul edin ve ardından **Ileri: ön uçlar**' ı seçin.

### <a name="frontends-tab"></a>Ön uçlar sekmesi

1. Ön **uçlar** sekmesinde, **ön uç IP adresi türünün** **genel**olarak ayarlandığını doğrulayın. <br>Ön uç IP 'sini kullanım çalışmanıza göre genel veya özel olacak şekilde yapılandırabilirsiniz. Bu örnekte, genel ön uç IP 'si seçersiniz.
   > [!NOTE]
   > Application Gateway v2 SKU 'SU için yalnızca **genel** ön uç IP yapılandırması ' nı seçebilirsiniz. Özel ön uç IP yapılandırması şu anda bu v2 SKU 'SU için etkin değil.

2. **Genel IP** adresi Için **Yeni oluştur** ' u seçin ve genel IP adresi adı Için *myagpublicıpaddress* girin ve **Tamam**' ı seçin. 

   ![Yeni uygulama ağ geçidi oluştur: ön uçlar](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. **İleri ' yi seçin: Backenler**.

### <a name="backends-tab"></a>Backends sekmesi

Arka uç havuzu, isteği sunan arka uç sunucularına istekleri yönlendirmek için kullanılır. Arka uç havuzları, NIC 'Ler, sanal makine ölçek kümeleri, genel IP 'Ler, iç IP 'Ler, tam etki alanı adları (FQDN) ve Azure App Service gibi çok kiracılı arka uçlar olabilir. Bu örnekte, uygulama ağ geçidiniz ile boş bir arka uç havuzu oluşturacak ve arka uç havuzuna arka uç hedefleri ekleyeceğiz.

1. **Backends** sekmesinde **+ arka uç Havuzu Ekle**' yi seçin.

2. Açılan **bir arka uç havuzu ekleyin** penceresinde, boş bir arka uç havuzu oluşturmak için aşağıdaki değerleri girin:

    - **Ad**: arka uç havuzunun adı Için *mybackendpool* girin.
    - **Hedefleri olmayan arka uç Havuzu Ekle**: hedefi olmayan bir arka uç havuzu oluşturmak için **Evet** ' i seçin. Uygulama ağ geçidini oluşturduktan sonra arka uç hedefleri ekleyeceksiniz.

3. Arka uç havuzu **Ekle** penceresinde, arka uç havuzu yapılandırmasını kaydetmek ve **backends** sekmesine dönmek için **Ekle** ' yi seçin.

   ![Yeni uygulama ağ geçidi oluştur: arka uçları](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. **Backends** sekmesinde **İleri: yapılandırma**' yı seçin.

### <a name="configuration-tab"></a>Yapılandırma sekmesi

**Yapılandırma** sekmesinde, bir yönlendirme kuralı kullanarak oluşturduğunuz ön uç ve arka uç havuzunu bağlayacaksınız.

1. **Yönlendirme kuralları** sütununda **Kural Ekle** ' yi seçin.

2. Açılan **yönlendirme kuralı ekle** penceresinde, **kural adı**için *myroutingrule* yazın.

3. Yönlendirme kuralı bir dinleyici gerektirir. **Yönlendirme kuralı ekle** penceresinin içindeki **dinleyici** sekmesinde, dinleyici için aşağıdaki değerleri girin:

    - **Dinleyici adı**: dinleyicinin adı Için *MyListener* girin.
    - **Ön uç IP**: ön uç için oluşturduğunuz ortak IP 'yi seçmek için **genel** ' i seçin.
    - **Protokol**: **https**seçeneğini belirleyin.
    - **Bağlantı noktası**: bağlantı noktası için 443 doğrulayın.

   **HTTPS sertifikası**altında:

   - **PFX Sertifika dosyası** -daha önce oluşturduğunuz c:\appgwcert.exe dosyasına gidin ve seçin.
   - **Sertifika adı** -sertifika adı için *mycert1* yazın.
   - Parola **için parola türü** *Azure123456* .
  
        **Dinleyici** sekmesinde diğer ayarlar için varsayılan değerleri kabul edin, sonra yönlendirme kuralının geri kalanını yapılandırmak Için **arka uç hedefleri** sekmesini seçin.

   ![Yeni uygulama ağ geçidi oluşturma: dinleyici](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. **Arka uç hedefleri** sekmesinde, **arka uç hedefi**Için **mybackendpool** ' u seçin.

5. **Http ayarı**Için yeni **Oluştur** ' u seçerek yeni bir http ayarı oluşturun. HTTP ayarı, yönlendirme kuralının davranışını tespit eder. Açılan **http ayarı Ekle** penceresinde **http ayar adı**için *myhttpsetting* girin. **Http ayarı Ekle** penceresinde diğer ayarlar için varsayılan değerleri kabul edin ve ardından **Ekle** ' yi seçerek **yönlendirme kuralı ekle** penceresine dönün. 

   ![Yeni uygulama ağ geçidi oluştur: HTTP ayarı](./media/create-ssl-portal/application-gateway-create-httpsetting.png)

6. Yönlendirme kuralı **Ekle** penceresinde, yönlendirme kuralını kaydetmek ve **yapılandırma** sekmesine dönmek için **Ekle** ' yi seçin.

   ![Yeni uygulama ağ geçidi oluşturma: yönlendirme kuralı](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Ileri ' yi seçin **: Etiketler** ve sonra **İleri: İnceleme + oluştur**.

### <a name="review--create-tab"></a>Gözden geçir + Oluştur sekmesi

**Gözden geçir + oluştur** sekmesindeki ayarları gözden geçirin ve ardından **Oluştur** ' u seçerek sanal ağı, genel IP adresini ve uygulama ağ geçidini oluşturun. Azure 'da uygulama ağ geçidini oluşturmak birkaç dakika sürebilir. Bir sonraki bölüme geçmeden önce Dağıtım başarıyla bitene kadar bekleyin.

## <a name="add-backend-targets"></a>Arka uç hedefleri ekleme

Bu örnekte, sanal makineleri hedef arka uç olarak kullanacaksınız. Var olan sanal makineleri kullanabilir ya da yeni bir tane oluşturabilirsiniz. Azure 'un uygulama ağ geçidi için arka uç sunucusu olarak kullandığı iki sanal makine oluşturacaksınız.

Bunu yapmak için şunları yapmanız gerekir:

1. Arka uç sunucuları olarak kullanılmak üzere *Myvm* ve *myVM2*olmak üzere iki yeni VM oluşturun.
2. Uygulama ağ geçidinin başarıyla oluşturulduğunu doğrulamak için sanal makinelere IIS 'yi yükler.
3. Arka uç sunucularını arka uç havuzuna ekleyin.

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

1. Azure portal **kaynak oluştur**' u seçin. **Yeni** pencere görüntülenir.
2. **Popüler** listede **Windows Server 2016 Datacenter** ' ı seçin. **Sanal makine oluştur** sayfası görüntülenir.

   Application Gateway, arka uç havuzunda kullanılan herhangi bir sanal makine türüne trafiği yönlendirebilir. Bu örnekte, bir Windows Server 2016 Datacenter kullanırsınız.

1. Aşağıdaki sanal makine ayarları için **temel bilgiler** sekmesine bu değerleri girin:

    - **Kaynak grubu**: kaynak grubu adı için **myResourceGroupAG** öğesini seçin.
    - **Sanal makine adı**: sanal makinenin adı Için *myvm* girin.
    - **Kullanıcı**adı: Yönetici Kullanıcı adı için *azureuser* girin.
    - **Parola**: yönetici parolası için *Azure123456* girin.
4. Diğer varsayılanları kabul edin ve ardından **İleri: diskler**' i seçin.  
5. **Diskler** sekmesi varsayılan değerlerini kabul edin ve ardından İleri ' yi seçin **: ağ**.
6. **Ağ** sekmesinde, **sanal ağ** için **Myvnet** öğesinin seçildiğini ve **alt ağın** **mybackendsubnet**olarak ayarlandığını doğrulayın. Diğer varsayılanları kabul edin ve ardından **İleri: yönetim**' i seçin.

   Application Gateway, içinde bulunduğu sanal ağ dışındaki örneklerle iletişim kurabilir, ancak IP bağlantısı olduğundan emin olmanız gerekir.
1. **Yönetim** sekmesinde, **önyükleme tanılamayı** **kapalı**olarak ayarlayın. Diğer varsayılanları kabul edin ve ardından **gözden geçir + oluştur**' u seçin.
2. **Gözden geçir + oluştur** sekmesinde ayarları gözden geçirin, doğrulama hatalarını düzeltin ve ardından **Oluştur**' u seçin.
3. Devam etmeden önce dağıtımın tamamlanmasını bekleyin.

### <a name="install-iis-for-testing"></a>Test için IIS 'yi yükler

Bu örnekte, yalnızca Azure 'un Application Gateway 'i başarıyla oluşturduğunu doğrulamak için sanal makinelere IIS yüklersiniz.

1. [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell)açın. Bunu yapmak için, Azure portal üst gezinti çubuğundan **Cloud Shell** ' ı seçin ve ardından açılır listeden **PowerShell** ' i seçin. 

    ![Özel uzantıyı yükleme](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Sanal makineye IIS yüklemek için aşağıdaki komutu çalıştırın: 

   ```azurepowershell-interactive
          Set-AzVMExtension `
            -ResourceGroupName myResourceGroupAG `
            -ExtensionName IIS `
            -VMName myVM `
            -Publisher Microsoft.Compute `
            -ExtensionType CustomScriptExtension `
            -TypeHandlerVersion 1.4 `
            -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
            -Location EastUS
   ```

3. İkinci bir sanal makine oluşturun ve daha önce tamamladığınız adımları kullanarak IIS 'yi yükleyebilirsiniz. Sanal makine adı ve **set-Azvmexgeri** cmdlet 'Inin **VMName** ayarı için *myVM2* kullanın.

### <a name="add-backend-servers-to-backend-pool"></a>Arka uç sunucularını arka uç havuzuna Ekle

1. **Tüm kaynaklar**' ı ve ardından **myappgateway**' i seçin.

2. Sol menüden **arka uç havuzları** ' nı seçin.

3. **Mybackendpool**öğesini seçin.

4. **Hedefler**' in altında, açılan listeden **sanal makine** ' yi seçin.

5. **Sanal makine** ve **ağ arabirimleri**altında, açılan listelerden **myvm** ve **myVM2** sanal makinelerini ve bunlarla ilişkili ağ arabirimlerini seçin.

    ![Arka uç sunucuları ekleme](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. **Kaydet**’i seçin.

7. Sonraki adıma geçmeden önce dağıtımın tamamlanmasını bekleyin.

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

1. **Tüm kaynaklar**' ı seçin ve ardından **Myagpublicıpaddress**' i seçin.

    ![Uygulama ağ geçidi genel IP adresini kaydetme](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Tarayıcınızın adres çubuğuna *https://\<yazın, uygulama ağ geçidi IP adresiniz\>* .

   Otomatik olarak imzalanan bir sertifika kullandıysanız güvenlik uyarısını kabul etmek için **Ayrıntılar** (veya Chrome üzerinde **Gelişmiş** ) seçeneğini belirleyin ve ardından Web sayfasına gidin:

    ![Güvenli uyarı](./media/create-ssl-portal/application-gateway-secure.png)

    Güvenli IIS siteniz, sonra aşağıdaki örnekte olduğu gibi görüntülenir:

    ![Temel URL’yi uygulama ağ geçidinde test etme](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [SSL desteği Application Gateway hakkında daha fazla bilgi edinin](ssl-overview.md)
