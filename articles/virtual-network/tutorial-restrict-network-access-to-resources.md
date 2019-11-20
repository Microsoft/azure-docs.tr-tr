---
title: PaaS kaynaklarına ağ erişimini kısıtlama - öğretici - Azure portalı | Microsoft Docs
description: Bu öğreticide Azure Depolama ve Azure SQL Veritabanı gibi Azure kaynaklarına ağ erişimini, Azure portalı kullanarak sanal ağ hizmet uç noktaları ile sınırlama ve kısıtlama hakkında bilgi edineceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/23/2018
ms.author: kumud
ms.openlocfilehash: 34cb2b6c5a770aa9ec38ce02a97d976fe28251ac
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638748"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak sanal ağ hizmet uç noktaları ile PaaS kaynaklarına ağ erişimini kısıtlama

Sanal ağ hizmet uç noktaları bazı Azure hizmet uç noktalarına ağ erişimini bir sanal ağ alt ağı ile sınırlamanıza olanak tanır. Ayrıca, kaynaklara internet erişimini de kaldırabilirsiniz. Hizmet uç noktaları, sanal ağınızdan desteklenen Azure hizmetlerine doğrudan bağlantı sağlar, böylece Azure hizmetlerine erişmek için sanal ağınızın özel adres alanını kullanabilirsiniz. Hizmet uç noktaları aracılığıyla Azure kaynaklarına gönderilen trafik her zaman Microsoft Azure omurga ağı üzerinde kalır. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Alt ağ ile sanal ağ oluşturma
> * Alt ağ ekleme ve hizmet uç noktasını etkinleştirme
> * Azure kaynağı oluşturma ve yalnızca bir alt ağdan ağ erişimine izin verme
> * Her alt ağa bir sanal makine (VM) dağıtma
> * Bir alt ağdan kaynağa erişimi onaylama
> * Bir alt ağdan ve internetten kaynağa erişimin reddedildiğini onaylama

Tercih ederseniz, [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) veya [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md) kullanarak bu öğreticiyi tamamlayabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="log-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com ) adresinden Azure portalında oturum açın.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

1. Azure portalının sol üst köşesinde bulunan **+ Kaynak oluştur** seçeneğini belirleyin.
2. **Ağ**’ı ve sonra **Sanal ağ**’ı seçin.
3. Aşağıdaki bilgileri girin veya seçin ve sonra **Oluştur**’u seçin:

   |Ayar|Value|
   |----|----|
   |Name| myVirtualNetwork |
   |Adres alanı| 10.0.0.0/16|
   |Subscription| Aboneliğinizi seçme|
   |Resource group | **Yeni oluştur**’u seçin ve *myResourceGroup* değerini girin.|
   |Location| **Doğu ABD**’yi seçin |
   |Alt Ağ Adı| Genel|
   |Alt Ağ Adresi aralığı| 10.0.0.0/24|
   |DDoS koruması| Temel|
   |Hizmet uç noktaları| Devre dışı|
   |Güvenlik Duvarı| Devre dışı|

   ![Sanal ağınızla ilgili temel bilgileri girin](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

## <a name="enable-a-service-endpoint"></a>Hizmet uç noktasını girin

Hizmet uç noktaları her hizmet ve her alt ağ için etkinleştirilir. Alt ağ oluşturun ve alt ağ için bir hizmet uç noktasını etkinleştirin.

1. Portalın üst kısmındaki **Kaynak, hizmet ve belgeleri arayın** kutusuna *myVirtualNetwork* yazın. Arama sonuçlarında **myVirtualNetwork** göründüğünde seçin.
2. Sanal ağa bir alt ağ ekleyin. **AYARLAR** altında **Alt Ağlar**’ı ve sonra aşağıdaki resimde gösterildiği gibi **+ Alt Ağ**’ı seçin:

    ![Alt ağ ekle](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. **Alt ağ ekle** altında aşağıdaki bilgileri seçin veya girin ve sonra **Tamam**’ı seçin:

    |Ayar|Value|
    |----|----|
    |Name| Özel |
    |Adres aralığı| 10.0.1.0/24|
    |Hizmet uç noktaları| **Hizmetler** altında **Microsoft.Storage** öğesini seçin|

> [!CAUTION]
> İçinde kaynaklar bulunan mevcut alt ağ için hizmet uç noktasını etkinleştirmeden önce, bkz. [Alt ağ ayarlarını değiştirme](virtual-network-manage-subnet.md#change-subnet-settings).

## <a name="restrict-network-access-for-a-subnet"></a>Bir kaynak için ağ erişimini kısıtlama

Varsayılan olarak, alt ağdaki tüm VM'ler tüm kaynaklarla iletişim kurabilir. Bir ağ güvenlik grubu oluşturup bunu alt ağ ile ilişkilendirerek, alt ağdaki tüm kaynaklara giden ve gelen iletişimi sınırlandırabilirsiniz.

1. Azure portalının sol üst köşesinde bulunan **+ Kaynak oluştur** seçeneğini belirleyin.
2. **Ağ**'ı ve sonra **Ağ güvenlik grubu**’nu seçin.
3. **Ağ güvenlik grubu oluşturun** altında aşağıdaki bilgileri girin veya seçin ve sonra **Oluştur**’u seçin:

    |Ayar|Value|
    |----|----|
    |Name| myNsgPrivate |
    |Subscription| Aboneliğinizi seçme|
    |Resource group | **Mevcut olanı kullan**’ı seçin ve *myResourceGroup* seçeneğini belirleyin.|
    |Location| **Doğu ABD**’yi seçin |

4. Ağ güvenlik grubu oluşturulduktan sonra portalın üst kısmındaki **Kaynak, hizmet ve belgeleri arayın** kutusuna *myNsgPrivate* ifadesini girin. Arama sonuçlarında **myNsgPrivate** göründüğünde seçin.
5. **AYARLAR** altında **Giden güvenlik kuralları**’nı seçin.
6. **+ Ekle** öğesini seçin.
7. Azure Depolama hizmetine giden iletişime izin veren bir kural oluşturun. Aşağıdaki bilgileri girin veya seçin ve ardından **Ekle** seçeneğini belirleyin:

    |Ayar|Value|
    |----|----|
    |Source| **VirtualNetwork** öğesini seçin |
    |Source port ranges| * |
    |Hedef | **Hizmet Etiketi**’ni seçin|
    |Hedef hizmet etiketi | **Depolama**’yı seçin|
    |Hedef bağlantı noktası aralıkları| * |
    |Protocol|Any|
    |Action|Allow|
    |Priority|100|
    |Name|İzin Ver-Depolama-Tümü|

8. İnternet bağlantısını reddeden başka bir giden güvenlik kuralı oluşturun. Bu kural, giden İnternet iletişimine izin veren tüm ağ güvenlik gruplarında varsayılan kuralı geçersiz kılar. Aşağıdaki değerleri kullanarak 5-7 arasındaki adımları tekrar tamamlayın:

    |Ayar|Value|
    |----|----|
    |Source| **VirtualNetwork** öğesini seçin |
    |Source port ranges| * |
    |Hedef | **Hizmet Etiketi**’ni seçin|
    |Hedef hizmet etiketi| **İnternet**’i seçin|
    |Hedef bağlantı noktası aralıkları| * |
    |Protocol|Any|
    |Action|Reddet|
    |Priority|110|
    |Name|Deny-Internet-All|

9. **AYARLAR** altında **Gelen güvenlik kuralları**’nı seçin.
10. **+ Ekle** öğesini seçin.
11. Herhangi bir yerden alt ağa yönelik Uzak Masaüstü Protokolü (RDP) trafiğine izin veren bir gelen güvenlik kuralı oluşturun. Kural, internetten gelen tüm trafiği engelleyen bir varsayılan güvenlik kuralını geçersiz kılar. Daha sonraki bir adımda bağlantının test edilebilmesi için uzak masaüstü bağlantılarına izin verilir. **AYARLAR** bölümünde **Gelen güvenlik kuralları**'nı seçin, **+Ekle** seçeneğini belirleyip aşağıdaki değerleri girin ve ardından **Ekle**'yi seçin:

    |Ayar|Value|
    |----|----|
    |Source| Any |
    |Source port ranges| * |
    |Hedef | **VirtualNetwork** öğesini seçin|
    |Hedef bağlantı noktası aralıkları| 3389 |
    |Protocol|Any|
    |Action|Allow|
    |Priority|120|
    |Name|İzin Ver-RDP-Tümü|

12. **AYARLAR** altında **Alt ağlar**’ı seçin.
13. **+ İlişkilendir**’i seçin
14. **Alt ağı ilişkilendir** altında **Sanal ağ**’ı seçin ve sonra **Bir sanal ağ seçin** altında **myVirtualNetwork** öğesini seçin.
15. **Alt ağ seçin** altında **Özel** ve ardından **Tamam**’ı seçin.

## <a name="restrict-network-access-to-a-resource"></a>Bir kaynağa ağ erişimini kısıtlama

Hizmet uç noktaları için etkinleştirilmiş Azure hizmetleri aracılığıyla oluşturulan kaynaklara ağ erişimini kısıtlamak için gereken adımlar, hizmetler arasında farklılık gösterir. Bir hizmete yönelik belirli adımlar için ilgili hizmetin belgelerine bakın. Bu öğreticinin geri kalanında örnek olarak bir Azure Depolama hesabına ağ erişimini kısıtlamaya yönelik adımlar yer alır.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

1. Azure portalının sol üst köşesinde bulunan **+ Kaynak oluştur** seçeneğini belirleyin.
2. **Depolama**’yı ve sonra **Depolama hesabı - blob, dosya, tablo, kuyruk** öğesini seçin.
3. Aşağıdaki bilgileri girin veya seçin, kalan varsayılan değerleri kabul edin ve sonra **Oluştur**’u seçin:

    |Ayar|Value|
    |----|----|
    |Name| Yalnızca sayı ve küçük harfler kullanarak tüm Azure konumlarında benzersiz olan 3-24 karakter uzunluğunda bir ad girin.|
    |Hesap türü|StorageV2 (genel amaçlı v2)|
    |Location| **Doğu ABD**’yi seçin |
    |Çoğaltma| Yerel olarak yedekli depolama (LRS)|
    |Subscription| Aboneliğinizi seçme|
    |Resource group | **Mevcut olanı kullan**’ı seçin ve *myResourceGroup* seçeneğini belirleyin.|

### <a name="create-a-file-share-in-the-storage-account"></a>Depolama hesabında dosya paylaşımı oluşturma

1. Depolama hesabı oluşturulduktan sonra portalın üst kısmındaki **Kaynak, hizmet ve belgeleri arayın** kutusuna depolama hesabının adını girin. Depolama hesabınızın adı arama sonuçlarında görüntülendiğinde seçin.
2. Aşağıdaki resimde gösterildiği gibi **Dosyalar**’ı seçin:

   ![Depolama hesabı](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. **+ Dosya paylaşımı**'nı seçin.
4. **Ad** altında *my-file-share* girip **Tamam**’ı seçin.
5. **Dosya hizmeti** kutusunu kapatın.

### <a name="restrict-network-access-to-a-subnet"></a>Bir alt ağa erişimi kısıtlama

Varsayılan olarak, depolama hesapları İnternet de dahil olmak üzere herhangi bir ağdaki istemcilerden gelen ağ bağlantılarını kabul eder. İnternet'ten ve *myVirtualNetwork* sanal ağındaki *Özel* alt ağı dışında tüm sanal ağlardaki diğer tüm alt ağlardan ağ erişimini reddedin.

1. Depolama hesabının **AYARLAR** menüsünde **Güvenlik duvarları ve sanal ağlar**’ı seçin.
2. **Seçili ağlar**'ı seçin.
3. **+Var olan sanal ağı ekle**'yi seçin.
4. **Ağ ekle** altında aşağıdaki değerleri ve sonra **Ekle**’yi seçin:

    |Ayar|Value|
    |----|----|
    |Subscription| Aboneliğinizi seçin.|
    |Sanal ağlar|**Sanal ağlar** altında **myVirtualNetwork** öğesini seçin|
    |Alt ağlar| **Alt ağlar** altında **Özel**’i seçin|

    ![Güvenlik duvarları ve sanal ağlar](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png)

5. **Kaydet**’i seçin.
6. **Güvenlik duvarları ve sanal ağlar** kutusunu kapatın.
7. Depolama hesabının **AYARKAR** menüsünde, aşağıdaki resimde gösterildiği gibi **Erişim anahtarları**’nı seçin:

      ![Güvenlik duvarları ve sanal ağlar](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Daha sonraki bir adımda, dosya paylaşımını VM içindeki bir sürücü harfine eşlerken el ile girmeniz gerekeceği için **Anahtar** değerini not edin.

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Bir depolama hesabına ağ erişimini test etmek için her alt ağa bir VM dağıtın.

### <a name="create-the-first-virtual-machine"></a>İlk sanal makineyi oluşturma

1. Azure portalının sol üst köşesinde bulunan **+ Kaynak oluştur** seçeneğini belirleyin.
2. **İşlem**'i seçin ve sonra da **Windows Server 2016 Datacenter**'ı seçin.
3. Aşağıdaki bilgileri girin veya seçin ve ardından **Tamam** seçeneğini belirleyin:

   |Ayar|Value|
   |----|----|
   |Name| myVmPublic|
   |Kullanıcı adı|Seçtiğiniz bir kullanıcı adını girin.|
   |istemcisiyle yönetilen bir cihaz için)| Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.|
   |Subscription| Aboneliğinizi seçin.|
   |Resource group| **Mevcut olanı kullan**’ı seçin ve **myResourceGroup** seçeneğini belirleyin.|
   |Location| **Doğu ABD**’yi seçin.|

   ![Bir sanal makine hakkında temel bilgi girme](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Sanal makine için bir boyut seçin ve sonra **Seç** seçeneğini belirleyin.
5. **Ayarlar** altında **Ağ**’ı ve sonra **myVirtualNetwork** öğesini seçin. Ardından, aşağıdaki resimde gösterildiği gibi **Alt Ağ** ve **Genel**’i seçin:

   ![Sanal ağ seçin](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)

6. **Ağ Güvenlik Grubu** bölümünde **Gelişmiş**'i seçin. Portal sizin için otomatik olarak, sonraki bir adımda sanal makineye bağlanmak için açmanız gereken 3389 numaralı bağlantı noktasına izin veren bir ağ güvenlik grubu oluşturur. **Ayarlar** sayfasında **Tamam**'ı seçin.
7. **Özet** sayfasında **Oluştur**’a tıklayarak sanal makine dağıtımını başlatın. VM’nin dağıtılması birkaç dakika sürer ancak VM oluşturulurken sonraki adıma geçebilirsiniz.

### <a name="create-the-second-virtual-machine"></a>İkinci sanal makineyi oluşturma

1-7 arasındaki adımları yeniden tamamlayın, ancak 3. adımda sanal makineyi *myVmPrivate* olarak adlandırın ve 5. adımda **Özel** alt ağı seçin.

Sanal makinenin dağıtılması birkaç dakika sürer. Oluşturma işlemi tamamlanmadıkça ve portalda ayarları açıkken sonraki adıma geçmeyin.

## <a name="confirm-access-to-storage-account"></a>Depolama hesabına erişimi onaylama

1. *myVmPrivate* VM oluşturma işlemi tamamlandığında Azure tarafından ayarları açılır. Aşağıdaki resimde gösterildiği gibi **Bağlan** düğmesini seçerek VM’ye bağlanın:

   ![Sanal makineye bağlanma](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. **Bağlan** düğmesini seçmenizin ardından bir Uzak Masaüstü Protokolü (.rdp) dosyası oluşturulur ve bilgisayarınıza indirilir.  
3. İndirilen rdp dosyasını açın. İstendiğinde **Bağlan**’ı seçin. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i ve sonra **Farklı bir hesap kullan** seçeneğini belirlemeniz gerekebilir. 
4. **Tamam**’ı seçin.
5. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarı alırsanız, bağlantıya devam etmek için **Evet**’i veya **Bağlan**’ı seçin.
6. *myVmPrivate* VM üzerinde PowerShell kullanarak Azure dosya paylaşımını Z sürücüsüne eşleyin. Aşağıdaki komutları çalıştırmadan önce `<storage-account-key>` ve `<storage-account-name>` değerlerini [Depolama hesabı oluşturma](#create-a-storage-account) bölümünde sağladığınız ve aldığınız değerlerle değiştirin.

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell aşağıdaki örnek çıktıya benzer bir çıktı döndürür:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   Z sürücüsüne başarıyla eşlenen Azure dosya paylaşımı.

7. Bir komut isteminden, VM’nin İnternet'e giden bağlantısının olmadığını doğrulayın:

   ```
   ping bing.com
   ```

   *Özel* alt ağı ile ilişkili ağ güvenlik grubu İnternet'e giden erişime izin vermediği için bir yanıt almazsınız.

8. *myVmPrivate* VM ile uzak masaüstü oturumunu kapatın.

## <a name="confirm-access-is-denied-to-storage-account"></a>Depolama hesabına erişimin reddedildiğini onaylama

1. Portalın üst kısmındaki *Kaynak, hizmet ve belgeleri arayın* kutusuna **myVmPublic** yazın.
2. Arama sonuçlarında **myVmPublic** göründüğünde seçin.
3. *myVmPublic* VM için [Depolama hesabına erişimi onaylama](#confirm-access-to-storage-account) bölümündeki 1-6. adımları tamamlayın.

   Kısa süre bekledikten sonra bir `New-PSDrive : Access is denied` hatası alırsınız. *myVmPublic* VM *Genel* alt ağa dağıtıldığı için erişim reddedilir. *Genel* alt ağın Azure Depolama için etkinleştirilmiş bir hizmet uç noktası yoktur. Depolama hesabı yalnızca *Özel* alt ağdan ağ erişimine izin verir; *Genel* alt ağdan erişime izin vermez.

4. *myVmPublic* VM ile uzak masaüstü oturumunu kapatın.

5. Bilgisayarınızdan Azure [portalına](https://portal.azure.com) göz atın.
6. **Kaynak, hizmet ve belgeleri arama** kutusunda oluşturduğunuz depolama hesabının adını girin. Depolama hesabınızın adı arama sonuçlarında görüntülendiğinde seçin.
7. **Dosyalar**’ı seçin.
8. Aşağıdaki resimde gösterilen hatayı alırsınız:

   ![Erişim reddedildi hatası](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

   Bilgisayarınız *MyVirtualNetwork* sanal ağının *Özel* alt ağında olmadığı için erişim reddedilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu ve içerdiği tüm kaynakları silin:

1. Portalın üst kısmındaki **Ara** kutusuna *myResourceGroup* değerini girin. Arama sonuçlarında **myResourceGroup** seçeneğini gördüğünüzde bunu seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **KAYNAK GRUBU ADINI YAZIN:** için *myResourceGroup* girin ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bir sanal ağ alt ağı için hizmet uç noktası etkinleştirdiniz. Hizmet uç noktalarını birden fazla Azure hizmetinden dağıtılmış kaynaklar için etkinleştirebileceğinizi öğrendiniz. Bir Azure Depolama hesabı oluşturdunuz ve depolama hesabına ağ erişimini yalnızca bir sanal ağ alt ağındaki kaynaklarla kısıtladınız. Hizmet uç noktaları hakkında daha fazla bilgi için bkz. [Hizmet uç noktalarına genel bakış](virtual-network-service-endpoints-overview.md) ve [Alt ağları yönetme](virtual-network-manage-subnet.md).

Hesabınızda birden fazla sanal ağ varsa, her bir sanal ağın içindeki kaynakların birbiriyle iletişim kurabilmesi iki sanal ağı birbirine bağlamak isteyebilirsiniz. Sanal ağları bağlama hakkında bilgi almak için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Sanal ağları bağlama](./tutorial-connect-virtual-networks-portal.md)
