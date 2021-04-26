---
title: 'Öğretici: Azure portal kullanarak birden çok Web sitesini barındırır'
titleSuffix: Azure Application Gateway
description: Bu öğreticide, Azure portal kullanarak birden çok Web sitesini barındıran bir uygulama ağ geçidi oluşturmayı öğreneceksiniz.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: 8de0dd16c3b3b73b5d5e52017cf63b357654144f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030966"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak birden çok Web sitesini barındırmak için bir uygulama ağ geçidi oluşturma ve yapılandırma

[Uygulama ağ geçidi](overview.md)oluştururken [birden çok Web sitesinin barındırmayı yapılandırmak](multiple-site-overview.md) için Azure Portal kullanabilirsiniz. Bu öğreticide, sanal makineleri kullanarak arka uç adres havuzları tanımlarsınız. Daha sonra, Web trafiğinin havuzlardaki uygun sunuculara ulaşmasından emin olmak için iki etki alanını temel alan dinleyicileri ve kuralları yapılandırırsınız. Bu öğreticide *www.contoso.com* ve *www.fabrikam.com* örnekleri kullanılmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Uygulama ağ geçidi oluşturma
> * Arka uç sunucuları için sanal makineler oluşturma
> * Arka uç sunucularıyla arka uç havuzları oluşturma
> * Dinleyici oluşturma
> * Yönlendirme kuralları oluşturma
> * Ad çözümlemesi için ana bilgisayar dosyasını Düzenle

:::image type="content" source="./media/create-multiple-sites-portal/scenario.png" alt-text="Çok siteli Application Gateway":::

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

1. Azure portal sol menüsünde **kaynak oluştur** ' u seçin. **Yeni** pencere görüntülenir.

2. **Ağ** ' ı seçin ve ardından **öne çıkan** listede **Application Gateway** ' yi seçin.

### <a name="basics-tab"></a>Temel bilgiler sekmesi

1. **Temel bilgiler** sekmesinde, aşağıdaki uygulama ağ geçidi ayarları için şu değerleri girin:

   - **Kaynak grubu**: kaynak grubu için **myResourceGroupAG** öğesini seçin. Yoksa, oluşturmak için **Yeni oluştur** ' u seçin.
   - **Uygulama ağ geçidi adı**: uygulama ağ geçidinin adı Için *myappgateway* girin.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png" alt-text="Application Gateway oluştur":::

2.  Azure 'un, oluşturduğunuz kaynaklar arasında iletişim kurması için bir sanal ağa ihtiyacı vardır. Yeni bir sanal ağ oluşturabilir veya var olan bir ağı kullanabilirsiniz. Bu örnekte, uygulama ağ geçidini oluşturduğunuz sırada yeni bir sanal ağ oluşturacaksınız. Application Gateway örnekleri ayrı alt ağlarda oluşturulur. Bu örnekte iki alt ağ oluşturursunuz: bir tane uygulama ağ geçidi ve arka uç sunucuları için bir diğeri.

    **Sanal ağı Yapılandır** altında yeni bir sanal ağ oluşturmak Için **Yeni oluştur** ' u seçin. Açılan **sanal ağ oluştur** penceresinde, sanal ağ ve iki alt ağ oluşturmak için aşağıdaki değerleri girin:

    - **Ad**: sanal ağın adı Için *myvnet* girin.

    - **Alt ağ adı** (Application Gateway alt ağ): **alt ağlar** Kılavuzu *varsayılan* olarak adlandırılan bir alt ağ gösterir. Bu alt ağın adını *Myagsubnet* olarak değiştirin.<br>Application Gateway alt ağı yalnızca uygulama ağ geçitleri içerebilir. Başka hiçbir kaynağa izin verilmez.

    - **Alt ağ adı** (arka uç sunucusu alt ağı): alt **ağlar** kılavuzunun Ikinci satırında, **alt ağ adı** sütununa *mybackendsubnet* yazın.

    - **Adres aralığı** (arka uç sunucusu alt ağı): **alt ağlar** kılavuzunun Ikinci satırına *myagsubnet* adres aralığıyla çakışmayacak bir adres aralığı girin. Örneğin, *Myagsubnet* adres aralığı 10.0.0.0/24 Ise, *Mybackendsubnet* adres aralığı için *10.0.1.0/24* girin.

    **Sanal ağ oluştur** penceresini kapatmak ve sanal ağ ayarlarını kaydetmek için **Tamam ' ı** seçin.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png" alt-text="VNet oluştur":::
    
3. **Temel bilgiler** sekmesinde, diğer ayarlar için varsayılan değerleri kabul edin ve ardından **Ileri: ön uçlar**' ı seçin.

### <a name="frontends-tab"></a>Ön uçlar sekmesi

1. Ön **uçlar** sekmesinde, **ön uç IP adresi türünün** **genel** olarak ayarlandığını doğrulayın. <br>Ön uç IP 'sini kullanım çalışmanıza göre genel veya özel olacak şekilde yapılandırabilirsiniz. Bu örnekte, genel ön uç IP 'si seçersiniz.
   > [!NOTE]
   > Application Gateway v2 SKU 'SU için yalnızca **genel** ön uç IP yapılandırması ' nı seçebilirsiniz. Özel ön uç IP yapılandırması şu anda bu v2 SKU 'SU için etkin değil.

2. **Genel IP adresi** Için **Yeni Ekle** ' yı seçin ve genel IP adresi adı Için *myagpublicıpaddress* girin ve **Tamam**' ı seçin. 

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png" alt-text="Başka bir VNet oluştur":::

3. **İleri ' yi seçin: Backenler**.

### <a name="backends-tab"></a>Backends sekmesi

Arka uç havuzu, isteği sunan arka uç sunucularına istekleri yönlendirmek için kullanılır. Arka uç havuzları NIC 'Ler, sanal makine ölçek kümeleri, genel IP 'Ler, iç IP 'Ler, tam etki alanı adları (FQDN) ve Azure App Service gibi çok kiracılı arka uçlar olabilir. Bu örnekte, uygulama ağ geçidiniz ile boş bir arka uç havuzu oluşturacak ve arka uç havuzuna arka uç hedefleri ekleyeceğiz.

1. **Arka uç Havuzu Ekle**' yi seçin. 

2. Açılan **bir arka uç havuzu ekleyin** penceresinde, boş bir arka uç havuzu oluşturmak için aşağıdaki değerleri girin:

    - **Ad**: arka uç havuzunun adı Için *contosopool* girin.
    - **Hedefleri olmayan arka uç Havuzu Ekle**: hedefi olmayan bir arka uç havuzu oluşturmak için **Evet** ' i seçin. Uygulama ağ geçidini oluşturduktan sonra arka uç hedefleri ekleyeceksiniz.

3. Arka uç havuzu **Ekle** penceresinde, arka uç havuzu yapılandırmasını kaydetmek ve **backends** sekmesine dönmek için **Ekle** ' yi seçin.
4. Şimdi, *fabrikamPool* adlı başka bir arka uç havuzunu, önceki havuzu eklediğiniz şekilde ekleyin.
1. **Add (Ekle)** seçeneğini belirleyin.

    :::image type="content" source="./media/create-multiple-sites-portal/backend-pools.png" alt-text="Arka uçlar oluşturma":::

4. **Backends** sekmesinde **İleri: yapılandırma**' yı seçin.

### <a name="configuration-tab"></a>Yapılandırma sekmesi

**Yapılandırma** sekmesinde, bir yönlendirme kuralı kullanarak oluşturduğunuz ön uç ve arka uç havuzlarını bağlayacaksınız.

1. **Yönlendirme kuralları** sütununda **bir yönlendirme kuralı ekle** ' yi seçin.

2. Açılan **yönlendirme kuralı ekle** penceresinde, **kural adı** için *contosoRule* girin.

3. Yönlendirme kuralı bir dinleyici gerektirir. **Yönlendirme kuralı ekle** penceresinin içindeki **dinleyici** sekmesinde, dinleyici için aşağıdaki değerleri girin:

    - **Kural adı**: *contosoRule*.
    - **Dinleyici adı**: *contosolistener*.
    - **Ön uç IP**: ön uç için oluşturduğunuz ortak IP 'yi seçmek için **genel** ' i seçin.

   **Ek ayarlar** altında:
   - **Dinleyici türü**: birden çok site
   - **Ana bilgisayar adı**: **www.contoso.com**

   **Dinleyici** sekmesinde diğer ayarlar için varsayılan değerleri kabul edin, sonra yönlendirme kuralının geri kalanını yapılandırmak Için **arka uç hedefleri** sekmesini seçin.

   :::image type="content" source="./media/create-multiple-sites-portal/routing-rule.png" alt-text="Yönlendirme kuralı oluştur":::

4. **Arka uç** **hedefi** sekmesinde **contosopool** ' u seçin.

5. **Http ayarı** Için yeni **Ekle** ' yı seçerek yeni bir http ayarı oluşturun. HTTP ayarı, yönlendirme kuralının davranışını tespit eder. Açılan **http ayarı Ekle** penceresinde **http ayar adı** için *contosohttpsetting* yazın. **Http ayarı Ekle** penceresinde diğer ayarlar için varsayılan değerleri kabul edin ve ardından **Ekle** ' yi seçerek **yönlendirme kuralı ekle** penceresine dönün. 

6. Yönlendirme kuralı **Ekle** penceresinde, yönlendirme kuralını kaydetmek ve **yapılandırma** sekmesine dönmek için **Ekle** ' yi seçin.
7. **Yönlendirme kuralı ekle** ' yi seçin ve Fabrikam için benzer bir kural, dinleyici, arka uç hedefı ve http ayarı ekleyin.

     :::image type="content" source="./media/create-multiple-sites-portal/fabrikam-rule.png" alt-text="Fabrikam kuralı":::

7. Ileri ' yi seçin **: Etiketler** ve sonra **İleri: İnceleme + oluştur**.

### <a name="review--create-tab"></a>Gözden geçir + Oluştur sekmesi

**Gözden geçir + oluştur** sekmesindeki ayarları gözden geçirin ve ardından **Oluştur** ' u seçerek sanal ağı, genel IP adresini ve uygulama ağ geçidini oluşturun. Azure 'da uygulama ağ geçidini oluşturmak birkaç dakika sürebilir.

Bir sonraki bölüme geçmeden önce Dağıtım başarıyla bitene kadar bekleyin.

## <a name="add-backend-targets"></a>Arka uç hedefleri ekleme

Bu örnekte, sanal makineleri hedef arka uç olarak kullanacaksınız. Var olan sanal makineleri kullanabilir ya da yeni bir tane oluşturabilirsiniz. Azure 'un uygulama ağ geçidi için arka uç sunucusu olarak kullandığı iki sanal makine oluşturacaksınız.

Arka uç hedefleri eklemek için şunları yapmanız gerekir:

1. Arka uç sunucuları olarak kullanılmak üzere *Contosovm* ve *fabrikamVM* olmak üzere iki yeni VM oluşturun.
2. Uygulama ağ geçidinin başarıyla oluşturulduğunu doğrulamak için sanal makinelere IIS 'yi yükler.
3. Arka uç sunucularını arka uç havuzlarına ekleyin.

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

1. Azure portal **kaynak oluştur**' u seçin. **Yeni** pencere görüntülenir.
2. **Popüler** listede **Windows Server 2016 Datacenter** ' ı seçin. **Sanal makine oluştur** sayfası görüntülenir.<br>Application Gateway, arka uç havuzunda kullanılan herhangi bir sanal makine türüne trafiği yönlendirebilir. Bu örnekte, bir Windows Server 2016 Datacenter kullanırsınız.
3. Aşağıdaki sanal makine ayarları için **temel bilgiler** sekmesine bu değerleri girin:

    - **Abonelik**: Aboneliğinizi seçin.
    - **Kaynak grubu**: kaynak grubu adı için **myResourceGroupAG** öğesini seçin.
    - **Sanal makine adı**: sanal makinenin adı Için *contosovm* girin.
    - **Bölge**: daha önce kullandığınız bölgeyi seçin.
    - **Kullanıcı** adı: Yönetici Kullanıcı adı için bir ad girin.
    - **Parola**: yönetici için bir parola girin.
1. Diğer varsayılanları kabul edin ve ardından **İleri: diskler**' i seçin.  
2. **Diskler** sekmesi varsayılan değerlerini kabul edin ve ardından İleri ' yi seçin **: ağ**.
3. **Ağ** sekmesinde, **sanal ağ** için **Myvnet** öğesinin seçildiğini ve **alt ağın** **mybackendsubnet** olarak ayarlandığını doğrulayın. Diğer varsayılanları kabul edin ve ardından **İleri: yönetim**' i seçin.<br>Application Gateway, içinde bulunduğu sanal ağ dışındaki örneklerle iletişim kurabilir, ancak IP bağlantısı olduğundan emin olmanız gerekir.
4. **Yönetim** sekmesinde, **önyükleme tanılamayı** **devre dışı** olarak ayarlayın. Diğer varsayılanları kabul edin ve ardından **gözden geçir + oluştur**' u seçin.
5. **Gözden geçir + oluştur** sekmesinde ayarları gözden geçirin, doğrulama hatalarını düzeltin ve ardından **Oluştur**' u seçin.
6. Devam etmeden önce sanal makine oluşturma işleminin tamamlanmasını bekleyin.

### <a name="install-iis-for-testing"></a>Test için IIS 'yi yükler

Bu örnekte, yalnızca Azure 'un Application Gateway 'i başarıyla oluşturduğunu doğrulamak için sanal makinelere IIS yüklersiniz.

1. [Azure PowerShell](../cloud-shell/quickstart-powershell.md)açın. Bunu yapmak için, Azure portal üst gezinti çubuğundan **Cloud Shell** ' ı seçin ve ardından açılır listeden **PowerShell** ' i seçin. 

    ![Özel uzantıyı yükleme](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Sanal makineye IIS yüklemek için şu komutu çalıştırın: <konum için kaynak grubu bölgenizi değiştirin \> : 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location <location>
    ```

3. İkinci bir sanal makine oluşturun ve daha önce tamamladığınız adımları kullanarak IIS 'yi yükleyebilirsiniz. Sanal makine adı ve **set-Azvmexgeri** cmdlet 'Inin **VMName** ayarı için *fabrikamVM* kullanın.

### <a name="add-backend-servers-to-backend-pools"></a>Arka uç sunucularını arka uç havuzlarına ekleme

1. **Tüm kaynaklar**' ı ve ardından **myappgateway**' i seçin.

2. Sol menüden **arka uç havuzları** ' nı seçin.

3. **Contosopool** öğesini seçin.

4. **Hedef türü** altında, açılan listeden **sanal makine** ' yi seçin.

5. **Hedef** altında, açılan listeden **contosovm** sanal makinesinin ağ arabirimini seçin.

    ![Arka uç sunucuları ekleme](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. **Kaydet**’i seçin.
7. *FabrikamVM* ve arabirimini *fabrikamPool* öğesine eklemek için tekrarlayın.

Sonraki adıma geçmeden önce dağıtımın tamamlanmasını bekleyin.

## <a name="edit-your-hosts-file-for-name-resolution"></a>Ad çözümlemesi için hosts dosyanızı düzenleme

Uygulama ağ geçidi, genel IP adresiyle oluşturulduktan sonra, ve ' ı çözmek için ana bilgisayar Dosyanızı düzenlemek üzere IP adresini alabilir ve kullanabilirsiniz `www.contoso.com` `www.fabrikam.com` . Bir üretim ortamında, `CNAME` ad çözümlemesi IÇIN DNS olarak bir oluşturabilirsiniz.

1. **Tüm kaynaklar**' a ve ardından **Myagpublicıpaddress**' e tıklayın.

    ![Uygulama Ağ Geçidi DNS adresini Kaydet](./media/create-multiple-sites-portal/public-ip.png)

2. IP adresini kopyalayın ve Dosyanızı yeni girişler için değer olarak kullanın `hosts` .
1. Yerel makinenizde bir yönetim komut istemi açın ve adresine gidin `c:\Windows\System32\drivers\etc` .
1. Dosyasını açın `hosts` ve aşağıdaki girdileri ekleyerek `x.x.x.x` uygulama ağ GEÇIDININ genel IP adresidir:
   ```dos
   # Copyright (c) 1993-2009 Microsoft Corp.
   #
   # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
   #
   # This file contains the mappings of IP addresses to host names. Each
   # entry should be kept on an individual line. The IP address should
   # be placed in the first column followed by the corresponding host name.
   # The IP address and the host name should be separated by at least one
   # space.
   #
   # Additionally, comments (such as these) may be inserted on individual
   # lines or following the machine name denoted by a '#' symbol.
   #
   # For example:
   #
   #      102.54.94.97     rhino.acme.com          # source server
   #       38.25.63.10     x.acme.com              # x client host
   
   # localhost name resolution is handled within DNS itself.
   #    127.0.0.1       localhost
   #    ::1             localhost
   x.x.x.x www.contoso.com
   x.x.x.x www.fabrikam.com

   ```
1. Dosyayı kaydedin.
1. Ana bilgisayar dosyanızdaki değişiklikleri yüklemek ve göstermek için aşağıdaki komutları çalıştırın:
   ```dos
    ipconfig/registerdns
    ipconfig/displaydns
   ```
   
## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

1. Tarayıcınızın adres çubuğuna bir etki alanı adı yazın. Örneğin, `http://www.contoso.com`.

    ![Uygulama ağ geçidinde contoso test etme](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Adresi diğer etki alanıyla değiştirin ve aşağıdaki örneğe benzer bir şey görmeniz gerekir:

    ![Uygulama ağ geçidinde fabrikam sitesini test etme](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulama ağ geçidiyle oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu kaldırın. Kaynak grubunu kaldırdığınızda, uygulama ağ geçidini ve ilgili tüm kaynakları da kaldırırsınız.

Kaynak grubunu kaldırmak için:

1. Azure portal sol menüsünde **kaynak grupları**' nı seçin.
2. **Kaynak grupları** sayfasında, listede **myResourceGroupAG** araması yapın ve ardından seçin.
3. **Kaynak grubu sayfasında**, **kaynak grubunu sil**' i seçin.
4. **Kaynak grubu adını yazmak** için *MyResourceGroupAG* girin ve **Sil**' i seçin.

Hosts dosyasını geri yüklemek için:
1. `www.contoso.com` `www.fabrikam.com` Hosts dosyasındaki ve satırlarını silin ve `ipconfig/registerdns` `ipconfig/flushdns` komut isteminden komutunu çalıştırın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Application Gateway ile yapabilecekleriniz hakkında daha fazla bilgi edinin](./overview.md)
