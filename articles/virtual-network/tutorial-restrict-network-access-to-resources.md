---
title: PaaS kaynaklarına erişimi kısıtla-öğretici-Azure portal
description: Bu öğreticide Azure Depolama ve Azure SQL Veritabanı gibi Azure kaynaklarına ağ erişimini, Azure portalı kullanarak sanal ağ hizmet uç noktaları ile sınırlama ve kısıtlama hakkında bilgi edineceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/11/2020
ms.author: kumud
ms.openlocfilehash: 22494d292077f4b6018a4512b45b5fe2caa9c8ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057210"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Öğretici: Azure portalını kullanarak sanal ağ hizmet uç noktaları ile PaaS kaynaklarına ağ erişimini kısıtlama

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

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

1. Azure portalının sol üst köşesinde bulunan **+ Kaynak oluştur** seçeneğini belirleyin.
2. **Ağ**' ı ve ardından **sanal ağlar**' ı seçin.
3. **+ Ekle** ' ye tıklayın ve aşağıdaki bilgileri girin: 

   |Ayar|Değer|
   |----|----|
   |Abonelik| Aboneliğinizi seçin|
   |Kaynak grubu | **Yeni oluştur**’u seçin ve *myResourceGroup* değerini girin.|
   |Name| *MyVirtualNetwork* girin |
   |Region| **(US) Doğu ABD** seçin |

   ![Sanal ağınızla ilgili temel bilgileri girin](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

4. Ileri ' ye tıklayın **: IP adresleri >**
   
   |Ayar|Değer|
   |----|----|
   |IPv4Address alanı| Varsayılan olarak bırak |
   |Alt ağ adı| **Varsayılan** ' a tıklayın ve adı "varsayılan" Iken "genel" olarak değiştirin|
   |Alt ağ adres aralığı| Varsayılan olarak bırak|

5. Ileri ' ye tıklayın **: güvenlik >** 
   
   |Ayar|Değer|
   |----|----|   
   |BastionHost| Devre Dışı Bırak|
   |DDoS koruması| Devre Dışı Bırak|
   |Güvenlik Duvarı| Devre Dışı Bırak|

6. Tamamlandığında, **gözden geçir ve oluştur**' a tıklayın.
7. Doğrulama denetimleri başarılı olursa **Oluştur**' a tıklayın.
8. Dağıtımın bitmesini bekleyin, sonra **Kaynağa Git ' e** tıklayın veya bir sonraki bölüme geçin. 

## <a name="enable-a-service-endpoint"></a>Hizmet uç noktasını girin

Hizmet uç noktaları her hizmet ve her alt ağ için etkinleştirilir. Bir alt ağ oluşturmak ve alt ağ için bir hizmet uç noktası etkinleştirmek için:

1. Zaten sanal ağ kaynağı sayfasında değilseniz, portalın en üstündeki **kaynakları, hizmetleri ve belgeleri arayın** kutusunda yeni oluşturulan ağı arayabilir, *myVirtualNetwork* girebilir ve listeden seçebilirsiniz.
2. **Ayarlar** menüsünde (sol tarafta), **alt ağlar**' ı seçin ve ardından aşağıda gösterildiği gibi **+ alt ağ**' ı seçin:

    ![Alt ağ ekleme](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. **Alt ağ ekle** altında aşağıdaki bilgileri seçin veya girin ve sonra **Tamam**’ı seçin:

    |Ayar|Değer|
    |----|----|
    |Ad| Özel |
    |Adres aralığı| Varsayılan olarak bırak|
    |Hizmet uç noktaları| **Microsoft. Storage** 'ı seçin|
    |Hizmet uç noktası ilkeleri | Varsayılanı 0 olarak bırak |

> [!CAUTION]
> İçinde kaynaklar bulunan mevcut alt ağ için hizmet uç noktasını etkinleştirmeden önce, bkz. [Alt ağ ayarlarını değiştirme](virtual-network-manage-subnet.md#change-subnet-settings).

4. **Kaydet**' e tıklayın, ardından sağdaki alt ağ penceresini kapatın. Yeni oluşturulan alt ağ liste görünmelidir.

## <a name="restrict-network-access-for-a-subnet"></a>Bir kaynak için ağ erişimini kısıtlama

Varsayılan olarak, bir alt ağdaki tüm sanal makine örnekleri tüm kaynaklarla iletişim kurabilir. Bir ağ güvenlik grubu oluşturarak ve onu alt ağla ilişkilendirerek, bir alt ağdaki tüm kaynaklarla ve bunların iletişimini sınırlayabilirsiniz:

1. Azure portalının sol üst köşesindeki **Tüm hizmetler**'i seçin.
2. Ağ **' ı** seçin ve sonra **ağ güvenlik grupları**' nı seçin (veya arayın).
3. **Ağ güvenlik grupları** sayfasında **+ Ekle**' ye tıklayın.
4. Aşağıdaki bilgileri girin 

    |Ayar|Değer|
    |----|----|
    |Abonelik| Aboneliğinizi seçin|
    |Kaynak grubu | Listeden *Myresourcegroup* öğesini seçin|
    |Name| **Mynsgprivate** girin |
    |Konum| **Doğu ABD** seçin |

5. **Gözden geçir + oluştur**' a tıklayın ve doğrulama denetimi geçirildiğinde **Oluştur**' a tıklayın.
6. Ağ güvenlik grubu oluşturulduktan sonra **Kaynağa Git ' e** tıklayın veya *Mynsgprivate* için arama yapın.
7. Sol taraftaki **Ayarlar** altında **giden güvenlik kuralları**' nı seçin.
8. **+ Ekle**'yi seçin.
9. Azure Depolama hizmetine giden iletişime izin veren bir kural oluşturun. Aşağıdaki bilgileri girin veya seçin ve ardından **Ekle** seçeneğini belirleyin:

    |Ayar|Değer|
    |----|----|
    |Kaynak| **VirtualNetwork** öğesini seçin |
    |Kaynak bağlantı noktası aralıkları| * |
    |Hedef | **Hizmet Etiketi**’ni seçin|
    |Hedef hizmet etiketi | **Depolamayı** seçin|
    |Hedef bağlantı noktası aralıkları| Varsayılanı *8080* olarak bırak |
    |Protokol|Herhangi bir|
    |Eylem|İzin Ver|
    |Öncelik|100|
    |Ad|**Allow-Storage-All** olarak yeniden adlandır|

10. İnternet bağlantısını reddeden başka bir giden güvenlik kuralı oluşturun. Bu kural, giden İnternet iletişimine izin veren tüm ağ güvenlik gruplarında varsayılan kuralı geçersiz kılar. Aşağıdaki değerleri kullanarak yukarıdaki 6-9 arasındaki adımları uygulayın:

    |Ayar|Değer|
    |----|----|
    |Kaynak| **VirtualNetwork** öğesini seçin |
    |Kaynak bağlantı noktası aralıkları| * |
    |Hedef | **Hizmet Etiketi**’ni seçin|
    |Hedef hizmet etiketi| **İnternet**’i seçin|
    |Hedef bağlantı noktası aralıkları| * |
    |Protokol|Herhangi bir|
    |Eylem|**Varsayılanı *Reddet* olarak değiştir** |
    |Öncelik|110|
    |Name|*Reddet-Internet-tümü* olarak değiştir|

11. Alt ağa her yerden Uzak Masaüstü Protokolü (RDP) trafiğine izin veren bir *gelen güvenlik kuralı* oluşturun. Kural, internetten gelen tüm trafiği engelleyen bir varsayılan güvenlik kuralını geçersiz kılar. Daha sonraki bir adımda bağlantının test edilebilmesi için uzak masaüstü bağlantılarına izin verilir. 
12. **Ayarlar** altında **Gelen güvenlik kuralları**’nı seçin.
13. **+ Ekle** ' yi seçin ve aşağıdaki değerleri kullanın:

    |Ayar|Değer|
    |----|----|
    |Kaynak| Herhangi bir |
    |Kaynak bağlantı noktası aralıkları| * |
    |Hedef | **VirtualNetwork** öğesini seçin|
    |Hedef bağlantı noktası aralıkları| *3389* olarak değiştirin |
    |Protokol|Herhangi bir|
    |Eylem|İzin Ver|
    |Öncelik|120|
    |Name|*Allow-RDP-All* olarak değiştir|

   >[!WARNING] 
   > RDP bağlantı noktası 3389, Internet 'e açıktır. Bu yalnızca test için önerilir. *Üretim ortamları* IÇIN bir VPN veya özel bağlantı kullanmanızı öneririz.

1.  **Ayarlar**'ın altında **Alt ağlar**’ı seçin.
2.  **+ İlişkilendir**’e tıklayın.
3.  **Sanal ağ** altında **myVirtualNetwork** öğesini seçin.
4.  **Alt ağ** altında **özel**' i seçin ve ardından **Tamam**' ı seçin.

## <a name="restrict-network-access-to-a-resource"></a>Bir kaynağa ağ erişimini kısıtlama

Hizmet uç noktaları için etkinleştirilen Azure hizmetleri aracılığıyla oluşturulan kaynaklarla ağ erişimini kısıtlamak için gereken adımlar, hizmetler arasında farklılık gösterecektir. Bir hizmete yönelik belirli adımlar için ilgili hizmetin belgelerine bakın. Bu öğreticinin geri kalanında örnek olarak bir Azure Depolama hesabına ağ erişimini kısıtlamaya yönelik adımlar yer alır.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

1. Azure portalının sol üst köşesinde bulunan **+ Kaynak oluştur** seçeneğini belirleyin.
2. Arama çubuğuna "depolama hesabı" girin ve açılan menüden seçin.
3. **+ Ekle**'ye tıklayın.
4. Aşağıdaki bilgileri girin:

    |Ayar|Değer|
    |----|----|
    |Abonelik| Aboneliğinizi seçin|
    |Kaynak grubu| *Myresourcegroup* seçin|
    |Depolama Hesap Adı| Yalnızca sayı ve küçük harfler kullanarak tüm Azure konumlarında benzersiz olan 3-24 karakter uzunluğunda bir ad girin.|
    |Konum| **(US) Doğu ABD** seçin |
    |Performans|Standart|
    |Hesap türü| StorageV2 (genel amaçlı v2)|
    |Çoğaltma| Yerel olarak yedekli depolama (LRS)|

5. **Oluştur + gözden geçir**' i seçin ve doğrulama denetimleri geçtiğinde **Oluştur**' a tıklayın. 

>[!NOTE] 
> Dağıtımın tamamlanması birkaç dakika sürebilir.

6. Depolama hesabı oluşturulduktan sonra **Kaynağa Git ' e** tıklayın.

### <a name="create-a-file-share-in-the-storage-account"></a>Depolama hesabında dosya paylaşımı oluşturma

1. Depolama hesabınıza genel bakış sayfasına gidin.
2. **Dosya paylaşımları** uygulaması simgesini seçin ve **+ dosya paylaşımı**' na tıklayın.

    |Ayar|Değer|
    |----|----|
    |Ad| My-File-Share|
    |Kota| ' En yüksek ' olarak ayarla |

   ![Depolama hesabı](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. **Oluştur**’a tıklayın.
4. Dosya paylaşımının Azure penceresinde gösterilmesi gerekir, aksi takdirde **Yenile** ' ye tıklayın.

### <a name="restrict-network-access-to-a-subnet"></a>Bir alt ağa erişimi kısıtlama

Varsayılan olarak, depolama hesapları İnternet de dahil olmak üzere herhangi bir ağdaki istemcilerden gelen ağ bağlantılarını kabul eder. İnternet 'ten ve tüm sanal ağlardaki tüm diğer alt ağlardan ( *myVirtualNetwork* sanal ağındaki *özel* alt ağ dışında) ağ erişimini kısıtlayabilirsiniz. Bir alt ağa ağ erişimini kısıtlamak için:

1. (Benzersiz olarak adlandırılmış) depolama hesabınız için **Ayarlar** altında **ağ**' ı seçin.
2. **Seçili ağlar**'ı seçin.
3. **+ Var olan sanal ağı ekle** seçeneğini belirleyin.
4. **Ağ ekle** altında aşağıdaki değerleri ve sonra **Ekle**’yi seçin:

    |Ayar|Değer|
    |----|----|
    |Abonelik| Aboneliğinizi seçin|
    |Sanal ağlar| **myVirtualNetwork**|
    |Alt ağlar| **Özel**|

    ![Ekran görüntüsü, belirtilen değerleri girebileceğiniz ağları Ekle bölmesini gösterir.](./media/tutorial-restrict-network-access-to-resources/virtual-networks.png)

5. **Ekle** ' ye tıklayın ve ardından değişiklikleri kaydetmek için **Kaydet** simgesine hemen tıklayın.
6. Depolama hesabı **ayarları** altında, aşağıdaki görüntüde gösterildiği gibi **erişim anahtarları**' nı seçin:

      ![Ekran görüntüsü, bir anahtar edinebileceğiniz ayarlardan seçilen erişim tuşlarını gösterir.](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

7. Dosya paylaşımından bir VM harfine bir sürücü harfiyle eşlendiğine yönelik daha sonraki bir adımda el ile KEY1 girmeniz gerektiği için **anahtarları göster** ' e tıklayın ve **anahtar** değerlerini aklınızda yapın.

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Bir depolama hesabına ağ erişimini test etmek için her alt ağa bir VM dağıtın.

### <a name="create-the-first-virtual-machine"></a>İlk sanal makineyi oluşturma

1. "Arama kaynakları. . ." çubuğunda **sanal makineler** için arama yapın.
2. **+ > sanal makine Ekle**' yi seçin. 
3. Aşağıdaki bilgileri girin:

   |Ayar|Değer|
   |----|----|
   |Abonelik| Aboneliğinizi seçin|
   |Kaynak grubu| Daha önce oluşturulan * * myResourceGroup öğesini seçin.|
   |Sanal makine adı| *MyVmPublic* girin|
   |Region | (ABD) Doğu ABD
   |Kullanılabilirlik seçenekleri| Kullanılabilirlik alanı|
   |Kullanılabilirlik alanı | 1 |
   |Görüntü | Windows Server 2019 Datacenter-Gen1 |
   |Boyut | Kullanmak istediğiniz VM örnek boyutunu seçin |
   |Kullanıcı adı|Seçtiğiniz bir kullanıcı adını girin.|
   |Parola| Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.|
   |Genel gelen bağlantı noktaları | Seçili bağlantı noktalarına izin ver |
   |Gelen bağlantı noktalarını seçin | Varsayılan olarak RDP 'yi ayarlayın *(3389)* |

   ![Sanal ağ seçme](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
  
4. **Ağ** sekmesini seçin ve ardından **myVirtualNetwork**' ı seçin. 
5. *Ortak* alt ağı seçin.
6. **NIC ağ güvenlik grubu** altında **Gelişmiş**' i seçin. Portal sizin için otomatik olarak, sonraki bir adımda sanal makineye bağlanmak için açmanız gereken 3389 numaralı bağlantı noktasına izin veren bir ağ güvenlik grubu oluşturur. 

   ![Bir sanal makine hakkında temel bilgi girme](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)

7. **Gözden geçir ve oluştur**' u seçin, ardından **oluşturun** ve dağıtımın bitmesini bekleyin.
8. **Kaynağa Git ' e** tıklayın veya **ana > sanal makineler** sayfasını açın ve yeni oluşturduğunuz *myVmPublic*, başlatılması gereken VM 'yi seçin.

### <a name="create-the-second-virtual-machine"></a>İkinci sanal makineyi oluşturma

1. 1-8 adımlarını yeniden deneyin, ancak adım 3 ' te sanal makineyi *myVmPrivate* olarak adlandırın ve **ortak gelen bağlantı noktasını** "none" olarak ayarlayın. 
2. Adım 4-5 ' de **özel** alt ağı seçin.

>[!NOTE]
> **NIC ağ güvenlik grubu** ve **Genel gelen bağlantı noktaları** ayarları, aşağıda gösterilen mavi onay penceresi de dahil olmak üzere aşağıdaki görüntüyü yansıtmalıdır: "tüm genel İnternet trafiği varsayılan olarak engellenir".

   ![Özel bir sanal makine oluşturun](./media/tutorial-restrict-network-access-to-resources/create-private-virtual-machine.png)

3. **Gözden geçir ve oluştur**' u seçin, ardından **oluşturun** ve dağıtımın bitmesini bekleyin. 

>[!WARNING]
> Dağıtım tamamlanana kadar lütfen bir sonraki adıma devam edin.

4. Aşağıda gösterilen onay penceresini bekleyip **Kaynağa Git ' e** tıklayın.

   ![Özel bir sanal makine onay penceresi oluştur](./media/tutorial-restrict-network-access-to-resources/create-vm-confirmation-window.png)

## <a name="confirm-access-to-storage-account"></a>Depolama hesabına erişimi onaylama

1. *MyVmPrivate* VM oluşturulduktan sonra **Kaynağa Git ' e** tıklayın. 
2. **> RDP**'YI seçerek VM 'ye bağlanın.
3. **Bağlan** düğmesini seçtikten sonra bir Uzak Masaüstü Protokolü (. rdp) dosyası oluşturulur. Bilgisayarınıza indirmek için **RDP dosyasını indir** ' e tıklayın.  
4. İndirilen rdp dosyasını açın. İstendiğinde **Bağlan**’ı seçin. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **diğer seçenekler**' i ve ardından **farklı bir hesap kullanmanız** gerekebilir. E-posta alanı için, daha önce belirttiğiniz "yönetici hesabı: Kullanıcı adı" kimlik bilgilerini girin. 
5. **Tamam**’ı seçin.
6. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarıyı alırsanız, bağlantıya devam etmek için **Evet** ' i veya **devam et**' i seçin. Aşağıda gösterildiği gibi, VM 'nin başlangıcını görmeniz gerekir:

   ![Çalışan özel sanal makineyi göster](./media/tutorial-restrict-network-access-to-resources/virtual-machine-running.png)

7. VM penceresinde bir PowerShell CLı örneği açın.
8. Aşağıdaki betiği kullanarak, PowerShell kullanarak Azure dosya paylaşımından Z sürücüsüne eşleyin. İzleyen komutları çalıştırmadan önce `<storage-account-key>` ve her iki alanı da, `<storage-account-name>` [bir depolama hesabı oluşturma](#create-a-storage-account)bölümünde belirttiğiniz değerlerle ve "d daha önceki değerleri ile değiştirin.

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

9.   *myVmPrivate* VM ile uzak masaüstü oturumunu kapatın.

## <a name="confirm-access-is-denied-to-storage-account"></a>Depolama hesabına erişimin reddedildiğini onaylama

1. Portalın üst kısmındaki *Kaynak, hizmet ve belgeleri arayın* kutusuna **myVmPublic** yazın.
2. Arama sonuçlarında **myVmPublic** göründüğünde seçin.
3. *MyVmPublic* VM için [depolama hesabına erişimi onaylayın](#confirm-access-to-storage-account) bölümünde yukarıdaki 1-8 adımları tamamlanmıştır.

   Kısa süre bekledikten sonra bir `New-PSDrive : Access is denied` hatası alırsınız. *myVmPublic* VM *Genel* alt ağa dağıtıldığı için erişim reddedilir. *Genel* alt ağın Azure Depolama için etkinleştirilmiş bir hizmet uç noktası yoktur. Depolama hesabı yalnızca *Özel* alt ağdan ağ erişimine izin verir; *Genel* alt ağdan erişime izin vermez.

4. *myVmPublic* VM ile uzak masaüstü oturumunu kapatın.
5. Azure portal geri döndüğünüzde, daha önce oluşturduğunuz benzersiz adlı depolama hesabına gidin.
6. Dosya hizmeti altında, daha önce oluşturulan dosya **paylaşımları**' nı ( *My-File-Share*) seçin.
7. Aşağıdaki hata iletisini almalısınız:

   ![Erişim reddedildi hatası](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)
   
>[!NOTE] 
> Bilgisayarınız *MyVirtualNetwork* sanal ağının *Özel* alt ağında olmadığı için erişim reddedilir.

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
