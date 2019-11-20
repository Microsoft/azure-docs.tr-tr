---
title: 'Öğretici: kullanılabilirlik grubu önkoşulları'
description: Bu öğreticide, Azure VM 'lerinde SQL Server Always on kullanılabilirlik grubu oluşturmaya yönelik önkoşulların nasıl yapılandırılacağı gösterilmektedir.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.custom: seo-lt-2019
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.openlocfilehash: 17b1f58a950f2e0589986e9f1da1295671599341
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037484"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Azure sanal makinelerinde SQL Server her zaman açık kullanılabilirlik grupları oluşturmaya yönelik önkoşullar

Bu öğreticide, [Azure sanal makinelerinde (VM) SQL Server Always on kullanılabilirlik grubu](virtual-machines-windows-portal-sql-availability-group-tutorial.md)oluşturmaya yönelik önkoşulların nasıl tamamlanacağı gösterilmektedir. Önkoşulları tamamladığınızda, tek bir kaynak grubunda bir etki alanı denetleyiciniz, iki SQL Server sanal makine ve bir tanık sunucu vardır.

**Tahmini süre**: önkoşulların tamamlanması birkaç saat sürebilir. Bu sürenin çoğu sanal makine oluşturmaya harcanmıştır.

Aşağıdaki diyagramda öğreticide oluşturduğunuz özellikler gösterilmektedir.

![Kullanılabilirlik grubu](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Kullanılabilirlik grubu belgelerini gözden geçirin

Bu öğreticide, SQL Server Always on kullanılabilirlik grupları hakkında temel bir anladığınızı varsaymaktadır. Bu teknolojiye alışkın değilseniz, bkz. [Always on kullanılabilirlik gruplarına genel bakış (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Bir Azure hesabı oluşturun
Bir Azure hesabınız olmalıdır. [Ücretsiz bir Azure hesabı açabilir](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) veya [Visual Studio abonesi avantajlarından](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits)yararlanabilirsiniz.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Portalda yeni bir nesne oluşturmak için **+** ' a tıklayın.

   ![Yeni nesne](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. **Market** Arama penceresinde **kaynak grubu** yazın.

   ![Kaynak grubu](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. **Kaynak grubu**' na tıklayın.
5. **Oluştur**’ tıklayın.
6. **Kaynak grubu adı**altında, kaynak grubu için bir ad yazın. Örneğin, **SQL-ha-RG**yazın.
7. Birden çok Azure aboneliğiniz varsa, aboneliğin üzerinde kullanılabilirlik grubu oluşturmak istediğiniz Azure aboneliği olduğunu doğrulayın.
8. Bir konum seçin. Konum, kullanılabilirlik grubunu oluşturmak istediğiniz Azure bölgesidir. Bu makale, bir Azure konumundaki tüm kaynakları oluşturur.
9. **Panoya sabitle** seçeneğinin işaretli olduğundan emin olun. Bu isteğe bağlı ayar, Azure portal panosuna kaynak grubu için bir kısayol koyar.

   ![Kaynak grubu](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Kaynak grubunu oluşturmak için **Oluştur** ' a tıklayın.

Azure kaynak grubunu oluşturur ve portalda kaynak grubuna bir kısayol sabitlemelerini sağlar.

## <a name="create-the-network-and-subnets"></a>Ağ ve alt ağları oluşturma
Sonraki adım, Azure Kaynak grubundaki ağları ve alt ağları oluşturmaktır.

Çözüm iki alt ağa sahip bir sanal ağ kullanır. [Sanal ağa genel bakış](../../../virtual-network/virtual-networks-overview.md) , Azure 'da ağlar hakkında daha fazla bilgi sağlar.

Sanal ağı oluşturmak için:

1. Azure portal, kaynak grubunuzda **+ Ekle**' ye tıklayın. 

   ![Yeni öğe](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. **Sanal ağ**araması yapın.

     ![Sanal ağ ara](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. **Sanal ağ ' a**tıklayın.
4. **Sanal ağda** **Kaynak Yöneticisi** dağıtım modeline ve sonra **Oluştur**' a tıklayın.

    Aşağıdaki tabloda, sanal ağın ayarları gösterilmektedir:

   | **Alan** | Değer |
   | --- | --- |
   | **Adı** |autoHAVNET |
   | **Adres alanı** |10.33.0.0/24 |
   | **Alt ağ adı** |Yönetici |
   | **Alt ağ adres aralığı** |10.33.0.0/29 |
   | **Abonelik** |Kullanmayı düşündüğünüz aboneliği belirtin. Yalnızca bir aboneliğiniz varsa, **abonelik** boştur. |
   | **Kaynak grubu** |**Mevcut olanı kullan** ' ı seçin ve kaynak grubunun adını seçin. |
   | **Konum** |Azure konumunu belirtin. |

   Adres alanınız ve alt ağ adres aralığınız tablodan farklı olabilir. Aboneliğinize bağlı olarak, Portal kullanılabilir bir adres alanı ve karşılık gelen alt ağ adres aralığını önerir. Kullanılabilir yeterli adres alanı yoksa, farklı bir abonelik kullanın.

   Örnek, **yönetici**alt ağ adı ' nı kullanır. Bu alt ağ etki alanı denetleyicileri içindir.

5. **Oluştur**’ tıklayın.

   ![Sanal ağı yapılandırma](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure sizi Portal panosuna döndürür ve yeni ağ oluşturulduğunda size bildirir.

### <a name="create-a-second-subnet"></a>İkinci bir alt ağ oluşturun
Yeni sanal ağın, **admin**adlı bir alt ağı vardır. Etki alanı denetleyicileri bu alt ağı kullanır. SQL Server VM 'Ler **SQL**adlı ikinci bir alt ağ kullanır. Bu alt ağı yapılandırmak için:

1. Panonuzda, **SQL-ha-RG**oluşturduğunuz kaynak grubuna tıklayın. **Kaynaklar**altındaki kaynak grubunda ağı bulun.

    **SQL-ha-RG** görünmüyorsa, **kaynak grupları** ' na tıklayarak ve kaynak grubu adına göre filtreleyerek bulun.
2. Kaynak listesinde **autoHAVNET** ' a tıklayın. 
3. **AutoHAVNET** sanal ağında, **Ayarlar** altında **alt ağlar**' ı seçin.

    Zaten oluşturduğunuz alt ağı unutmayın.

   ![Sanal ağı yapılandırma](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. İkinci bir alt ağ oluşturun. **+ Alt ağ**' a tıklayın.
6. **Alt ağ ekle**sayfasında, **ad**altında **sqlsubnet** yazarak alt ağı yapılandırın. Azure otomatik olarak geçerli bir **adres aralığı**belirtir. Bu adres aralığının en az 10 adresi olduğunu doğrulayın. Bir üretim ortamında, daha fazla adres gerekebilir.
7. **OK (Tamam)** düğmesine tıklayın.

    ![Sanal ağı yapılandırma](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

Aşağıdaki tabloda ağ yapılandırma ayarları özetlenmektedir:

| **Alan** | Değer |
| --- | --- |
| **Adı** |**autoHAVNET** |
| **Adres alanı** |Bu değer, aboneliğinizdeki kullanılabilir adres alanlarına bağlıdır. Tipik bir değer 10.0.0.0/16 ' dır. |
| **Alt ağ adı** |**yöneticileri** |
| **Alt ağ adres aralığı** |Bu değer, aboneliğinizdeki kullanılabilir adres aralıklarına bağlıdır. Tipik bir değer 10.0.0.0/24 ' dir. |
| **Alt ağ adı** |**sqlsubnet** |
| **Alt ağ adres aralığı** |Bu değer, aboneliğinizdeki kullanılabilir adres aralıklarına bağlıdır. Tipik bir değer 10.0.1.0/24 ' dir. |
| **Abonelik** |Kullanmayı düşündüğünüz aboneliği belirtin. |
| **Kaynak Grubu** |**SQL-HA-RG** |
| **Konum** |Kaynak grubu için seçtiğiniz konumu belirtin. |

## <a name="create-availability-sets"></a>Kullanılabilirlik kümeleri oluşturma

Sanal makineler oluşturmadan önce kullanılabilirlik kümeleri oluşturmanız gerekir. Kullanılabilirlik kümeleri, planlı veya planlanmamış bakım olayları için kapalı kalma süresini azaltır. Azure kullanılabilirlik kümesi, Azure 'un fiziksel hata etki alanlarına ve güncelleştirme etki alanlarına yerleştirdiği mantıksal bir kaynak grubudur. Bir hata etki alanı, kullanılabilirlik kümesi üyelerinin ayrı güç ve ağ kaynaklarına sahip olmasını sağlar. Bir güncelleştirme etki alanı, kullanılabilirlik kümesi üyelerinin bakım için aynı anda kullanılmamasını sağlar. Daha fazla bilgi için bkz. [sanal makinelerin kullanılabilirliğini yönetme](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

İki kullanılabilirlik kümesine ihtiyacınız vardır. Biri etki alanı denetleyicileri içindir. İkincisi SQL Server sanal makineler içindir.

Bir kullanılabilirlik kümesi oluşturmak için kaynak grubuna gidin ve **Ekle**' ye tıklayın. **Kullanılabilirlik kümesi**yazarak sonuçları filtreleyin. Sonuçlarda **kullanılabilirlik kümesi** ' ne tıklayın ve ardından **Oluştur**' a tıklayın.

Aşağıdaki tabloda yer alan parametrelere göre iki kullanılabilirlik kümesi yapılandırın:

| **Alan** | Etki alanı denetleyicisi kullanılabilirlik kümesi | SQL Server kullanılabilirlik kümesi |
| --- | --- | --- |
| **Adı** |adavailabilityset |sqlavailabilityset |
| **Kaynak grubu** |SQL-HA-RG |SQL-HA-RG |
| **Hata etki alanları** |3 |3 |
| **Etki alanlarını güncelleştir** |5 |3 |

Kullanılabilirlik kümelerini oluşturduktan sonra, Azure portal kaynak grubuna geri dönün.

## <a name="create-domain-controllers"></a>Etki alanı denetleyicileri oluşturma
Ağı, alt ağları, kullanılabilirlik kümelerini ve Internet 'e yönelik yük dengeleyiciyi oluşturduktan sonra, etki alanı denetleyicileri için sanal makineleri oluşturmaya hazırsınız demektir.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Etki alanı denetleyicileri için sanal makineler oluşturma
Etki alanı denetleyicileri oluşturup yapılandırmak için, **SQL-ha-RG** kaynak grubuna dönün.

1. **Ekle**'ye tıklayın. 
2. **Windows Server 2016 Datacenter**yazın.
3. **Windows Server 2016 Datacenter**' a tıklayın. **Windows Server 2016 Datacenter**'da dağıtım modelinin **Kaynak Yöneticisi**olduğunu doğrulayın ve ardından **Oluştur**' a tıklayın. 

İki sanal makine oluşturmak için yukarıdaki adımları tekrarlayın. İki sanal makineyi adlandırın:

* ad-birincil-DC
* ad-ikincil-DC

  > [!NOTE]
  > Active Directory Domain Services için yüksek kullanılabilirlik sağlamak üzere **ad-ikincil DC** sanal makinesi isteğe bağlıdır.
  >
  >

Aşağıdaki tabloda bu iki makinenin ayarları gösterilmektedir:

| **Alan** | Değer |
| --- | --- |
| **Adı** |İlk etki alanı denetleyicisi: *ad-birincil-DC*.</br>İkinci etki alanı denetleyicisi *ad-ikincil-DC*. |
| **VM disk türü** |SSD |
| **Kullanıcı adı** |DomainAdmin |
| **Parola** |Contoso! 0000 |
| **Abonelik** |*Aboneliğiniz* |
| **Kaynak grubu** |SQL-HA-RG |
| **Konum** |*Konumunuz* |
| **Boyut** |DS1_V2 |
| **Depolama** | **Yönetilen diskleri kullanın** - **Evet** |
| **Sanal ağ** |autoHAVNET |
| **Alt ağ** |yöneticileri |
| **Genel IP adresi** |*VM ile aynı ad* |
| **Ağ güvenlik grubu** |*VM ile aynı ad* |
| **Kullanılabilirlik kümesi** |adavailabilityset </br>**Hata etki alanları**: 2 </br>**Güncelleştirme etki alanları**: 2|
| **Tanılama** |Etkin |
| **Tanılama depolama hesabı** |*Otomatik olarak oluşturuldu* |

   >[!IMPORTANT]
   >Bir VM 'yi oluştururken bir kullanılabilirlik kümesine yerleştirebilirsiniz. VM oluşturulduktan sonra kullanılabilirlik kümesini değiştiremezsiniz. Bkz. [sanal makinelerin kullanılabilirliğini yönetme](../manage-availability.md).

Azure, sanal makineleri oluşturur.

Sanal makineler oluşturulduktan sonra etki alanı denetleyicisini yapılandırın.

### <a name="configure-the-domain-controller"></a>Etki alanı denetleyicisini yapılandırma
Aşağıdaki adımlarda, **ad-birincil DC** makinesini Corp.contoso.com için bir etki alanı denetleyicisi olarak yapılandırın.

1. Portalda **SQL-ha-RG** kaynak grubunu açın ve **ad-birincil-DC** makinesini seçin. **Ad-birincil-DC**'de, uzak masaüstü erişimi IÇIN bir RDP dosyası açmak üzere **Bağlan** ' a tıklayın.

    ![Sanal makineye bağlanma](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Yapılandırılmış Yönetici hesabınızla ( **\DomainAdmin**) ve parolanızla (**contoso! 0000**) oturum açın.
3. Varsayılan olarak, **Sunucu Yöneticisi** panosu görüntülenmelidir.
4. Panodaki **rol ve Özellik Ekle** bağlantısına tıklayın.

    ![Sunucu Yöneticisi-roller ekleme](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. **Sunucu rolleri** bölümüne gelene kadar **İleri ' yi** seçin.
6. **Active Directory Domain Services** ve **DNS sunucusu** rollerini seçin. İstendiğinde, bu roller için gereken ek özellikleri ekleyin.

   > [!NOTE]
   > Windows, hiçbir statik IP adresi olmadığını uyarır. Yapılandırmayı test ediyorsanız **devam**' a tıklayın. Üretim senaryolarında IP adresini Azure portal statik olarak ayarlayın veya [etki alanı denetleyicisi makinesinin STATIK IP adresini ayarlamak Için PowerShell kullanın](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Rol Ekle iletişim kutusu](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. **Onay** bölümüne ulaşana kadar **İleri** ' ye tıklayın. **Gerekirse hedef sunucuyu otomatik olarak yeniden Başlat** onay kutusunu seçin.
8. **Yükle**'ye tıklatın.
9. Özelliklerin yüklenmesi bittikten sonra, **Sunucu Yöneticisi** panosuna geri dönün.
10. Sol bölmedeki yeni **AD DS** seçeneğini belirleyin.
11. Sarı uyarı çubuğunda **daha fazla** bağlantıya tıklayın.

    ![DNS sunucusu VM 'sinde AD DS iletişim kutusu](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. **Tüm sunucu görevi ayrıntıları** Iletişim kutusunun **eylem** sütununda **Bu sunucuyu etki alanı denetleyicisi olarak yükselt ' e**tıklayın.
13. **Active Directory Domain Services Yapılandırma sihirbazında**aşağıdaki değerleri kullanın:

    | **Sayfa** | Ayar |
    | --- | --- |
    | **Dağıtım yapılandırması** |**Yeni orman ekle**<br/> **Kök etki alanı adı** = Corp.contoso.com |
    | **Etki alanı denetleyicisi seçenekleri** |**DSRM parolası** = contoso! 0000<br/>**Parolayı onaylayın** = contoso! 0000 |
14. Sihirbazdaki diğer sayfalara gitmek için **İleri** ' ye tıklayın. **Önkoşul denetimi** sayfasında, aşağıdaki iletiyi görmediğinizi doğrulayın: **tüm önkoşul denetimleri başarıyla geçildi**. Geçerli uyarı iletilerini gözden geçirebilirsiniz, ancak yüklemeye devam etmek mümkündür.
15. **Yükle**'ye tıklatın. **Ad-birincil-DC** sanal makinesi otomatik olarak yeniden başlatılır.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Birincil etki alanı denetleyicisinin IP adresini aklınızda

DNS için birincil etki alanı denetleyicisini kullanın. Birincil etki alanı denetleyicisi IP adresini aklınızda edin.

Birincil etki alanı denetleyicisi IP adresini almanın bir yolu Azure portal.

1. Azure portal, kaynak grubunu açın.

2. Birincil etki alanı denetleyicisine tıklayın.

3. Birincil etki alanı denetleyicisinde, **ağ arabirimleri**' ne tıklayın.

![Ağ arabirimleri](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Bu sunucunun özel IP adresini aklınızda edin.

### <a name="configure-the-virtual-network-dns"></a>Sanal ağı DNS 'yi yapılandırma
İlk etki alanı denetleyicisini oluşturduktan ve ilk sunucuda DNS 'i etkinleştirdikten sonra, sanal ağı DNS için bu sunucuyu kullanacak şekilde yapılandırın.

1. Azure portal sanal ağa tıklayın.

2. **Ayarlar**altında, **DNS sunucusu**' na tıklayın.

3. **Özel ' e**tıklayın ve birincil etki alanı DENETLEYICISININ özel IP adresini yazın.

4. **Save (Kaydet)** düğmesine tıklayın.

### <a name="configure-the-second-domain-controller"></a>İkinci etki alanı denetleyicisini yapılandırma
Birincil etki alanı denetleyicisi yeniden başlatıldıktan sonra, ikinci etki alanı denetleyicisini yapılandırabilirsiniz. Bu isteğe bağlı adım yüksek kullanılabilirliğe yöneliktir. İkinci etki alanı denetleyicisini yapılandırmak için aşağıdaki adımları izleyin:

1. Portalda **SQL-ha-RG** kaynak grubunu açın ve **ad-ikincil DC** makinesini seçin. **Ad-ikincil DC**'de, uzak masaüstü erişimi IÇIN bir RDP dosyası açmak üzere **Bağlan** ' a tıklayın.
2. Yapılandırılmış yönetici hesabınızı (**BUILTIN\DomainAdmin**) ve parolanızı (**contoso! 0000**) kullanarak VM 'de oturum açın.
3. Tercih edilen DNS sunucusu adresini etki alanı denetleyicisinin adresiyle değiştirin.
4. **Ağ ve Paylaşım Merkezi**' nde ağ arabirimine tıklayın.
   ![Ağ arabirimi](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. **Özellikler**'e tıklayın.
6. **Internet Protokolü sürüm 4 (TCP/IPv4)** öğesini seçin ve **Özellikler**' e tıklayın.
7. **AŞAĞıDAKI DNS sunucusu adreslerini kullan** ' ı seçin ve **tercih edilen DNS sunucusunda**birincil etki alanı denetleyicisinin adresini belirtin.
8. **Tamam**' a tıklayın ve ardından değişiklikleri kaydetmek için **kapatın** . Artık VM 'yi **corp.contoso.com**'e katabilirsiniz.

   >[!IMPORTANT]
   >DNS ayarını değiştirdikten sonra Uzak Masaüstü ile bağlantıyı kaybederseniz, Azure portal gidin ve sanal makineyi yeniden başlatın.

9. Uzak masaüstünden ikincil etki alanı denetleyicisine **Sunucu Yöneticisi Panosu**' nu açın.
10. Panodaki **rol ve Özellik Ekle** bağlantısına tıklayın.

    ![Sunucu Yöneticisi-roller ekleme](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. **Sunucu rolleri** bölümüne gelene kadar **İleri ' yi** seçin.
12. **Active Directory Domain Services** ve **DNS sunucusu** rollerini seçin. İstendiğinde, bu roller için gereken ek özellikleri ekleyin.
13. Özelliklerin yüklenmesi bittikten sonra, **Sunucu Yöneticisi** panosuna geri dönün.
14. Sol bölmedeki yeni **AD DS** seçeneğini belirleyin.
15. Sarı uyarı çubuğunda **daha fazla** bağlantıya tıklayın.
16. **Tüm sunucu görevi ayrıntıları** Iletişim kutusunun **eylem** sütununda **Bu sunucuyu etki alanı denetleyicisi olarak yükselt ' e**tıklayın.
17. **Dağıtım yapılandırması**altında, **mevcut bir etki alanına etki alanı denetleyicisi Ekle**' yi seçin.
    ![dağıtım yapılandırma](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. **Seç**'e tıklayın.
19. Yönetici hesabını kullanarak bağlanın (**corp. CONTOSO. COM\domainadmin**) ve parola (**contoso! 0000**).
20. **Ormandan bir etki alanı seçin**' de, etki alanına tıklayın ve ardından **Tamam**' a tıklayın.
21. **Etki alanı denetleyicisi seçenekleri**' nde, varsayılan değerleri kullanın ve DSRM parolasını ayarlayın.

    >[!NOTE]
    >**DNS seçenekleri** sayfasında, bu DNS sunucusu için bir temsilcinin oluşturuoluşturulamadığı hakkında uyarı alabilirsiniz. Bu uyarıyı, üretim dışı ortamlarda yoksayabilirsiniz.
22. İletişim kutusu **Önkoşul** denetimine ulaşıncaya kadar **İleri** ' ye tıklayın. Ardından **Yükle**'ye tıklayın.

Sunucu yapılandırma değişikliklerini tamamladıktan sonra sunucuyu yeniden başlatın.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>İkinci etki alanı denetleyicisine VPN DNS sunucusuna özel IP adresini ekleyin

Azure portal, sanal ağ altında, DNS sunucusunu ikincil etki alanı denetleyicisinin IP adresini içerecek şekilde değiştirin. Bu ayar, DNS hizmeti yedekliliği sağlar.

### <a name="DomainAccounts"></a>Etki alanı hesaplarını yapılandırma

Sonraki adımlarda Active Directory hesaplarını yapılandırırsınız. Aşağıdaki tabloda hesaplar gösterilmektedir:

| |Yükleme hesabı<br/> |SqlServer-0 <br/>SQL Server ve SQL Aracısı hizmet hesabı |SqlServer-1<br/>SQL Server ve SQL Aracısı hizmet hesabı
| --- | --- | --- | ---
|**Ad** |Yükleme |SQLSvc1 | SQLSvc2
|**Kullanıcı SamAccountName** |Yükleme |SQLSvc1 | SQLSvc2

Her hesabı oluşturmak için aşağıdaki adımları kullanın.

1. **Ad-birincil-DC** makinesinde oturum açın.
2. **Sunucu Yöneticisi** **Araçlar**' ı seçin ve ardından **Active Directory Yönetim Merkezi**' e tıklayın.   
3. Sol bölmeden **Corp (yerel)** öğesini seçin.
4. Sağ **Görevler** bölmesinde, **Yeni**' yi ve ardından **Kullanıcı**' yı seçin.
   ![Active Directory Yönetim Merkezi](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Her hesap için karmaşık bir parola ayarlayın.<br/> Üretim dışı ortamlar için, Kullanıcı hesabını hiçbir zaman sona ermeyecek şekilde ayarlayın.

5. Kullanıcıyı oluşturmak için **Tamam** ' ı tıklatın.
6. Üç hesabın her biri için önceki adımları tekrarlayın.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Yükleme hesabına gerekli izinleri verin
1. **Active Directory Yönetim Merkezi**sol bölmedeki **Corp (yerel)** öğesini seçin. Sonra sağ **Görevler** bölmesinde **Özellikler**' e tıklayın.

    ![CORP Kullanıcı Özellikleri](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. **Uzantılar**' ı seçin ve ardından **güvenlik** sekmesinde **Gelişmiş** düğmesine tıklayın.
3. **Corp Için gelişmiş güvenlik ayarları** Iletişim kutusunda **Ekle**' ye tıklayın.
4. **Sorumlu Seç**' e tıklayın, **corp\ınstall**araması yapın ve ardından **Tamam**' a tıklayın.
5. **Tüm özellikleri oku** onay kutusunu seçin.

6. **Bilgisayar nesneleri oluştur** onay kutusunu seçin.

     ![Corp Kullanıcı izinleri](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. **Tamam**' a ve ardından yeniden **Tamam** ' a tıklayın. **Corp** Özellikler penceresini kapatın.

Active Directory ve kullanıcı nesnelerini yapılandırmayı tamamladığınıza göre, iki SQL Server VM ve bir tanık sunucu VM 'si oluşturun. Ardından, etki alanına üç tane ekleyin.

## <a name="create-sql-server-vms"></a>SQL Server VM 'Leri oluşturma

Üç ek sanal makine oluşturun. Çözüm, SQL Server örneklerine sahip iki sanal makine gerektirir. Üçüncü bir sanal makine, tanık olarak çalışır. Windows Server 2016, bir [bulut tanığı](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)kullanabilir, ancak önceki işletim sistemleriyle tutarlı olması için bu belgede bir tanık için bir sanal makine kullanılmaktadır.  

Devam etmeden önce aşağıdaki tasarım kararlarını göz önünde bulundurun.

* **Depolama-Azure yönetilen diskler**

   Sanal makine depolama alanı için Azure yönetilen diskleri kullanın. Microsoft, SQL Server sanal makineler için yönetilen diskler önerir. Yönetilen Diskler, depolama alanını arka planda yönetir. Ayrıca, Yönetilen Disklere sahip sanal makineler aynı kullanılabilirlik kümesinde olduğunda Azure uygun artıklık düzeyini sağlamak için depolama kaynaklarını dağıtır. Daha fazla bilgi için bkz. [Azure Yönetilen Disklere Genel Bakış](../managed-disks-overview.md). Bir kullanılabilirlik kümesindeki yönetilen diskler hakkında daha fazla bilgi için bkz. [kullanılabilirlik kümesindeki VM 'ler Için yönetilen diskleri kullanma](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Üretimde ağ özel IP adresleri**

   Bu öğretici, sanal makineler için genel IP adreslerini kullanır. Genel bir IP adresi, internet üzerinden doğrudan sanal makineye uzak bağlantı sağlar. yapılandırma adımları daha kolay hale gelir. Üretim ortamlarında, Microsoft, SQL Server örneği VM kaynağının güvenlik açığı parmak izini azaltmak için yalnızca özel IP adresleri önerir.

### <a name="create-and-configure-the-sql-server-vms"></a>SQL Server VM 'Leri oluşturma ve yapılandırma
Ardından, üç VM oluşturun--iki SQL Server VM ve ek bir küme düğümü için VM. VM 'lerin her birini oluşturmak için, **SQL-ha-RG** kaynak grubuna geri dönün, **Ekle**' ye tıklayın, uygun Galeri öğesini arayın, **sanal makine**' ye tıklayın ve ardından **Galeri**' ye tıklayın. VM 'Leri oluşturmanıza yardımcı olması için aşağıdaki tablodaki bilgileri kullanın:


| Sayfa | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Uygun Galeri öğesini seçin |**Windows Server 2016 Datacenter** |**Windows Server 2016 üzerinde SQL Server 2016 SP1 Enterprise** |**Windows Server 2016 üzerinde SQL Server 2016 SP1 Enterprise** |
| Sanal makine yapılandırma **temelleri** |**Ad** = küme-FSW<br/>**Kullanıcı adı** = DomainAdmin<br/>**Parola** = contoso! 0000<br/>**Abonelik** = aboneliğiniz<br/>**Kaynak grubu** = SQL-ha-RG<br/>**Konum** = Azure konumunuz |**Ad** = SqlServer-0<br/>**Kullanıcı adı** = DomainAdmin<br/>**Parola** = contoso! 0000<br/>**Abonelik** = aboneliğiniz<br/>**Kaynak grubu** = SQL-ha-RG<br/>**Konum** = Azure konumunuz |**Ad** = SqlServer-1<br/>**Kullanıcı adı** = DomainAdmin<br/>**Parola** = contoso! 0000<br/>**Abonelik** = aboneliğiniz<br/>**Kaynak grubu** = SQL-ha-RG<br/>**Konum** = Azure konumunuz |
| Sanal makine yapılandırma **boyutu** |**Boyut** = DS1\_v2 (1 vCPU, 3,5 GB) |**Boyut** = DS2\_v2 (2 vCPU, 7 GB)</br>Boyut SSD depolamayı desteklemelidir (Premium disk desteği). )) |**Boyut** = DS2\_v2 (2 vCPU, 7 GB) |
| Sanal makine yapılandırma **ayarları** |**Depolama**: yönetilen diskleri kullanın.<br/>**Sanal ağ** = autoHAVNET<br/>**Subnet** = sqlsubnet (10.1.1.0/24)<br/>**Genel IP adresi** otomatik olarak oluşturulur.<br/>**Ağ güvenlik grubu** = yok<br/>**Izleme tanılaması** = etkin<br/>**Tanılama depolama hesabı** = otomatik olarak oluşturulan bir depolama hesabı kullan<br/>**Kullanılabilirlik kümesi** = sqlAvailabilitySet<br/> |**Depolama**: yönetilen diskleri kullanın.<br/>**Sanal ağ** = autoHAVNET<br/>**Subnet** = sqlsubnet (10.1.1.0/24)<br/>**Genel IP adresi** otomatik olarak oluşturulur.<br/>**Ağ güvenlik grubu** = yok<br/>**Izleme tanılaması** = etkin<br/>**Tanılama depolama hesabı** = otomatik olarak oluşturulan bir depolama hesabı kullan<br/>**Kullanılabilirlik kümesi** = sqlAvailabilitySet<br/> |**Depolama**: yönetilen diskleri kullanın.<br/>**Sanal ağ** = autoHAVNET<br/>**Subnet** = sqlsubnet (10.1.1.0/24)<br/>**Genel IP adresi** otomatik olarak oluşturulur.<br/>**Ağ güvenlik grubu** = yok<br/>**Izleme tanılaması** = etkin<br/>**Tanılama depolama hesabı** = otomatik olarak oluşturulan bir depolama hesabı kullan<br/>**Kullanılabilirlik kümesi** = sqlAvailabilitySet<br/> |
| Sanal makine yapılandırması **SQL Server ayarları** |Uygulanamaz |**SQL bağlantısı** = özel (sanal ağ içinde)<br/>**Bağlantı noktası** = 1433<br/>**SQL kimlik doğrulaması** = devre dışı<br/>**Depolama yapılandırması** = genel<br/>**Otomatik düzeltme eki uygulama** = Pazar 2:00<br/>**Otomatik yedekleme** = devre dışı</br>**Azure Key Vault tümleştirme** = devre dışı |**SQL bağlantısı** = özel (sanal ağ içinde)<br/>**Bağlantı noktası** = 1433<br/>**SQL kimlik doğrulaması** = devre dışı<br/>**Depolama yapılandırması** = genel<br/>**Otomatik düzeltme eki uygulama** = Pazar 2:00<br/>**Otomatik yedekleme** = devre dışı</br>**Azure Key Vault tümleştirme** = devre dışı |

<br/>

> [!NOTE]
> Burada önerilen makine boyutları, Azure VM 'lerinde kullanılabilirlik gruplarının test edilmesine yöneliktir. Üretim iş yükleri için en iyi performans için, [Azure sanal makinelerinde SQL Server yönelik performans en iyi uygulamalarında](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)SQL Server makine boyutlarına ve yapılandırmaya yönelik önerilere bakın.
>
>

Üç VM tam olarak sağlandıktan sonra, **corp.contoso.com** etki alanına katılmanız ve makinelere corp\ınstall yönetim hakları vermeniz gerekir.

### <a name="joinDomain"></a>Sunucuları etki alanına ekleme

Artık VM 'Leri **corp.contoso.com**'e katabilirsiniz. Hem SQL Server VM 'Ler hem de dosya paylaşma tanık sunucusu için aşağıdaki adımları uygulayın:

1. **BUILTIN\DomainAdmin**ile sanal makineye uzaktan bağlanın.
2. **Sunucu Yöneticisi**, **yerel sunucu**' ya tıklayın.
3. **Çalışma grubu** bağlantısına tıklayın.
4. **Bilgisayar adı** bölümünde **Değiştir**' e tıklayın.
5. **Etki alanı** onay kutusunu seçin ve metin kutusuna **corp.contoso.com** yazın. **OK (Tamam)** düğmesine tıklayın.
6. **Windows Güvenlik** açılır iletişim kutusunda, varsayılan etki alanı yönetici hesabı (**CORP\DomainAdmin**) ve parola (**contoso! 0000**) için kimlik bilgilerini belirtin.
7. "Corp.contoso.com etki alanına hoş geldiniz" iletisini gördüğünüzde **Tamam**' a tıklayın.
8. **Kapat**' a ve ardından açılan Iletişim kutusunda **Şimdi yeniden Başlat** ' a tıklayın.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Corp\ınstall kullanıcısını her küme sanal makinesine yönetici olarak ekleyin

Her bir sanal makine etki alanının bir üyesi olarak yeniden başlatıldıktan sonra, **Corp\ınstall** ' i yerel Yöneticiler grubunun bir üyesi olarak ekleyin.

1. VM yeniden başlatılana kadar bekleyin, sonra **CORP\DomainAdmin** hesabını kullanarak **SqlServer-0** ' a oturum açmak için RDP dosyasını birincil etki alanı denetleyicisinden yeniden başlatın.
   >[!TIP]
   >Etki alanı yönetici hesabıyla oturum açarak emin olun. Önceki adımlarda YERLEŞIK yönetici hesabını kullanıyorsunuz. Artık sunucu etki alanında olduğuna göre, etki alanı hesabını kullanın. RDP oturumunuzda *etki alanı*\\*Kullanıcı adı*' nı belirtin.

2. **Sunucu Yöneticisi** **Araçlar**' ı seçin ve ardından **Bilgisayar Yönetimi**' ne tıklayın.
3. **Bilgisayar Yönetimi** penceresinde, **yerel kullanıcılar ve gruplar**' ı genişletin ve ardından **gruplar**' ı seçin.
4. **Yöneticiler** grubuna çift tıklayın.
5. **Yöneticiler özellikleri** Iletişim kutusunda **Ekle** düğmesine tıklayın.
6. Kullanıcı **Corp\ınstall**' i girin ve ardından **Tamam**' a tıklayın.
7. **Yönetici özellikleri** iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.
8. **SqlServer-1** ve **cluster-FSW**üzerinde önceki adımları yineleyin.

### <a name="setServiceAccount"></a>SQL Server hizmet hesaplarını ayarlama

Her SQL Server VM SQL Server hizmet hesabını ayarlayın. Etki alanı hesaplarını yapılandırdığınızda oluşturduğunuz hesapları kullanın.

1. **SQL Server Yapılandırma Yöneticisi**açın.
2. SQL Server hizmetine sağ tıklayın ve ardından **Özellikler**' e tıklayın.
3. Hesabı ve parolayı ayarlayın.
4. Diğer SQL Server VM bu adımları yineleyin.  

SQL Server kullanılabilirlik grupları için, her SQL Server VM bir etki alanı hesabı olarak çalıştırılması gerekir.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Yükleme hesabı için her SQL Server VM bir oturum açma oluşturun

Kullanılabilirlik grubunu yapılandırmak için yükleme hesabı 'nı (Corp\ınstall) kullanın. Bu hesabın her bir SQL Server VM **sysadmin** sabit sunucu rolünün bir üyesi olması gerekir. Aşağıdaki adımlar yükleme hesabı için bir oturum açma oluşturur:

1. *\<MachineName\>\DomainAdmin* hesabını kullanarak Uzak Masaüstü Protokolü (RDP) üzerinden sunucuya bağlanın.

1. SQL Server Management Studio açın ve SQL Server yerel örneğine bağlanın.

1. **Nesne Gezgini**, **güvenlik**' e tıklayın.

1. **Oturum açmalar**' a sağ tıklayın. **Yeni oturum açma**' ya tıklayın.

1. **Oturum aç-yeni**' de **Ara**' ya tıklayın.

1. **Konumlar**' a tıklayın.

1. Etki alanı Yöneticisi ağ kimlik bilgilerini girin.

1. Yükleme hesabını kullanın.

1. Oturum açma adını **sysadmin** sabit sunucu rolünün bir üyesi olacak şekilde ayarlayın.

1. **OK (Tamam)** düğmesine tıklayın.

Yukarıdaki adımları diğer SQL Server VM tekrarlayın.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>SQL Server VM 'lerine Yük Devretme Kümelemesi özellikleri ekleme

Yük Devretme Kümelemesi özellikleri eklemek için, her iki SQL Server VM 'de aşağıdaki adımları uygulayın:

1. *Corp\ınstall* hesabını kullanarak Uzak Masaüstü Protokolü (RDP) aracılığıyla SQL Server sanal makinesine bağlanın. **Sunucu Yöneticisi panosunu**açın.
2. Panodaki **rol ve Özellik Ekle** bağlantısına tıklayın.

    ![Sunucu Yöneticisi-roller ekleme](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. **Sunucu özellikleri** bölümüne gelene kadar **İleri ' yi** seçin.
4. **Özellikler**' de **Yük Devretme Kümelemesi**' ni seçin.
5. Gerekli ek özellikleri ekleyin.
6. Özellikleri eklemek için **yüklensin** ' e tıklayın.

Diğer SQL Server VM adımları yineleyin.

  >[!NOTE]
  > Bu adım, SQL Server VM 'Lerin yük devretme kümesine katılmasını sağlar ve artık [Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) ve [Azure hızlı başlangıç şablonlarıyla](virtual-machines-windows-sql-availability-group-quickstart-template.md)otomatik olarak yapılabilir.


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> her SQL Server VM Güvenlik duvarını yapılandırma

Çözüm, güvenlik duvarında aşağıdaki TCP bağlantı noktalarının açılmasını gerektirir:

- **SQL Server VM**:<br/>
   SQL Server varsayılan örneği için bağlantı noktası 1433.
- **Azure yük dengeleyici araştırması:**<br/>
   Kullanılabilir herhangi bir bağlantı noktası. Örnekler genellikle 59999 kullanır.
- **Veritabanı yansıtma uç noktası:** <br/>
   Kullanılabilir herhangi bir bağlantı noktası. Örnekler genellikle 5022 kullanır.

Güvenlik Duvarı bağlantı noktalarının SQL Server VM 'lerde açık olması gerekir.

Bağlantı noktalarını açma yöntemi, kullandığınız güvenlik duvarı çözümüne bağlıdır. Sonraki bölümde, Windows güvenlik duvarında bağlantı noktalarının nasıl açılacağı açıklanmaktadır. SQL Server sanal makinelerinizin her birinde gerekli bağlantı noktalarını açın.

### <a name="open-a-tcp-port-in-the-firewall"></a>Güvenlik duvarında bir TCP bağlantı noktası açma

1. İlk SQL Server **Başlangıç** ekranında, **Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı**'nı başlatın.
2. Sol bölmede **gelen kuralları**' nı seçin. Sağ bölmede **Yeni kural**' a tıklayın.
3. **Kural türü**Için **bağlantı noktası**' nı seçin.
4. Bağlantı noktası için **TCP** ' yi belirtin ve uygun bağlantı noktası numaralarını yazın. Aşağıdaki örneğe bakın:

   ![SQL güvenlik duvarı](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. **İleri**’ye tıklayın.
6. **Eylem** sayfasında, **bağlantıya izin ver** ' i seçili bırakın ve **İleri**' ye tıklayın.
7. **Profil** sayfasında, varsayılan ayarları kabul edin ve ardından **İleri**' ye tıklayın.
8. **Ad** sayfasında, **ad** metin kutusunda bir kural adı ( **Azure lb araştırması**gibi) belirtin ve ardından **son**' a tıklayın.

İkinci SQL Server VM bu adımları yineleyin.

## <a name="configure-system-account-permissions"></a>Sistem hesabı izinlerini yapılandırma

Sistem hesabı için bir hesap oluşturmak ve uygun izinleri vermek için, her bir SQL Server örneği için aşağıdaki adımları izleyin:

1. Her bir SQL Server örneği üzerinde `[NT AUTHORITY\SYSTEM]` için bir hesap oluşturun. Aşağıdaki betik bu hesabı oluşturur:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Her SQL Server örneği üzerinde `[NT AUTHORITY\SYSTEM]` için aşağıdaki izinleri verin:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Aşağıdaki betik şu izinleri verir:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure sanal makinelerinde SQL Server Always on kullanılabilirlik grubu oluşturma](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
