---
title: 'Öğretici: kullanılabilirlik grubunu yapılandırma'
description: Bu öğreticide, Azure sanal makinelerinde SQL Server Always on kullanılabilirlik grubu oluşturma gösterilmektedir.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.custom: seo-lt-2019
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: 5c4eb5241cc5e50c11c05cac6909e37557ba106d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037506"
---
# <a name="tutorial-configure-availability-group-on-azure-sql-server-vm-manually"></a>Öğretici: Azure SQL Server VM kullanılabilirlik grubunu el ile yapılandırma

Bu öğreticide, Azure sanal makinelerinde SQL Server Always on kullanılabilirlik grubu oluşturma gösterilmektedir. Tüm öğreticide, iki SQL sunucusunda veritabanı çoğaltmasına sahip bir kullanılabilirlik grubu oluşturulur.

**Tahmini süre**: Önkoşullar karşılandığında yaklaşık 30 dakika sürer.

Diyagramda, öğreticide ne derledikleriniz gösterilmektedir.

![Kullanılabilirlik Grubu](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Önkoşullar

Öğretici, SQL Server her zaman açık kullanılabilirlik grupları hakkında temel bilgiler sahibi olduğunuzu varsayar. Daha fazla bilgiye ihtiyacınız varsa, bkz. [Always on kullanılabilirlik gruplarına genel bakış (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

Aşağıdaki tabloda, Bu öğreticiye başlamadan önce gerçekleştirmeniz gereken önkoşullar listelenmektedir:

|  |Gereksinim |Açıklama |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | İki SQL Server | -Bir Azure kullanılabilirlik kümesinde <br/> -Tek bir etki alanında <br/> -Yük Devretme Kümelemesi özelliği yüklü |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Küme tanığı için dosya paylaşma |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server hizmet hesabı | Etki alanı hesabı |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server Agent hizmet hesabı | Etki alanı hesabı |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Güvenlik Duvarı bağlantı noktaları açık | -SQL Server: varsayılan örnek için **1433** <br/> -Veritabanı yansıtma uç noktası: **5022** veya kullanılabilir herhangi bir bağlantı noktası <br/> -Kullanılabilirlik grubu yük dengeleyici IP adresi durum araştırması: **59999** veya kullanılabilir herhangi bir bağlantı noktası <br/> -Küme çekirdeği yük dengeleyici IP adresi durum araştırması: **58888** veya kullanılabilir herhangi bir bağlantı noktası |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Yük Devretme Kümelemesi özelliği Ekle | Her iki SQL Server için de bu özellik gerekir |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Yükleme etki alanı hesabı | -Her SQL Server yerel yönetici <br/> -SQL Server her bir örneği için SQL Server sysadmin sabit sunucu rolü üyesi  |


Öğreticiye başlamadan önce, [Azure sanal makinelerinde her zaman açık kullanılabilirlik grupları oluşturmak için önkoşulları gerçekleştirmeniz](virtual-machines-windows-portal-sql-availability-group-prereq.md)gerekir. Bu Önkoşullar zaten tamamlanırsa, [küme oluştur](#CreateCluster)' a atlayabilirsiniz.

  >[!NOTE]
  > Bu öğreticide sunulan adımların birçoğu artık [Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) ve [Azure hızlı başlangıç şablonlarıyla](virtual-machines-windows-sql-availability-group-quickstart-template.md)otomatikleştirilebilir.


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Kümeyi oluşturma

Önkoşullar tamamlandıktan sonra, ilk adım iki SQL sunucusunu ve bir tanık sunucusunu içeren bir Windows Server yük devretme kümesi oluşturmaktır.

1. Hem SQL Server hem de tanık sunucusunda yönetici olan bir etki alanı hesabı kullanan ilk SQL Server RDP.

   >[!TIP]
   >[Önkoşul belgesini](virtual-machines-windows-portal-sql-availability-group-prereq.md)izlediyseniz, **corp\ınstall**adlı bir hesap oluşturdunuz. Bu hesabı kullanın.

2. **Sunucu Yöneticisi** panosunda **Araçlar**' ı seçin ve ardından **Yük devretme kümesi Yöneticisi**' e tıklayın.
3. Sol bölmede **Yük devretme kümesi Yöneticisi**' a sağ tıklayın ve ardından **Küme Oluştur ' a**tıklayın.
   Küme oluşturma ![](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. Küme oluşturma Sihirbazı 'nda, aşağıdaki tabloda bulunan ayarlarla sayfaları düzenleyerek tek düğümlü bir küme oluşturun:

   | Sayfa | Ayarlar |
   | --- | --- |
   | Başlamadan önce |Varsayılanları Kullan |
   | Sunucuları seçin |**Sunucu adını girin** alanına ilk SQL Server adını yazın ve **Ekle**' ye tıklayın. |
   | Doğrulama uyarısı |**Hayır 'ı seçin. bu küme Için Microsoft desteği gerekmiyor ve bu nedenle doğrulama testlerini çalıştırmak istemiyor. Ileri 'yi tıkladığımda Kümeyi oluşturmaya devam**edin. |
   | Kümeyi yönetmek için erişim noktası |**Küme adı alanına bir**küme adı yazın, örneğin **SQLAGCluster1** .|
   | Onay |Depolama alanları kullanmadığınız takdirde Varsayılanları kullanın. Bu tablodan sonraki nota bakın. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Windows Server yük devretme kümesi IP adresini ayarlama

  > [!NOTE]
  > Windows Server 2019 ' de küme, **küme ağ adı**yerine bir **dağıtılmış sunucu adı** oluşturur. Windows Server 2019 kullanıyorsanız, bu öğreticide küme çekirdeği adına başvuran adımları atlayın. [PowerShell](virtual-machines-windows-portal-sql-create-failover-cluster.md#windows-server-2019)kullanarak bir küme ağ adı oluşturabilirsiniz. Daha fazla bilgi için blog [Yük devretme kümesini gözden geçirin: küme ağ nesnesi](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) . 

1. **Yük devretme kümesi Yöneticisi**' de, **küme çekirdeği kaynakları** ' na kaydırın ve küme ayrıntılarını genişletin. **Başarısız** durumunda hem **adı** hem de **IP adresi** kaynaklarını görmeniz gerekir. Küme, makinenin kendisi ile aynı IP adresine atandığından ve bu nedenle yinelenen bir adres olduğundan, IP adresi kaynağı çevrimiçi duruma getirilemiyor.

2. Başarısız **IP adresi** kaynağına sağ tıklayın ve ardından **Özellikler**' e tıklayın.

   ![Küme özellikleri](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. **STATIK IP adresi** ' ni seçin ve sanal makinelerinizdeki aynı alt ağdan kullanılabilir bir adres belirtin.

4. **Küme çekirdeği kaynakları** bölümünde, küme adı ' na sağ tıklayın ve **çevrimiçi getir**' e tıklayın. Sonra, her iki kaynak de çevrimiçi olana kadar bekleyin. Küme adı kaynağı çevrimiçi olduğunda, DC sunucusunu yeni bir AD bilgisayar hesabıyla güncelleştirir. Kullanılabilirlik grubu kümelenmiş hizmetini daha sonra çalıştırmak için bu AD hesabını kullanın.

### <a name="addNode"></a>Diğer SQL Server kümeye ekleme

Diğer SQL Server kümeye ekleyin.

1. Tarayıcı ağacında, kümeye sağ tıklayın ve **düğüm Ekle**' ye tıklayın.

    ![Kümeye düğüm ekleme](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. **Düğüm Ekleme sihirbazında** **İleri**' ye tıklayın. **Sunucuları seçin** sayfasında, ikinci SQL Server ekleyin. Sunucu adını **girin** alanına sunucu adını yazın ve ardından **Ekle**' ye tıklayın. İşiniz bittiğinde **İleri**' ye tıklayın.

1. **Doğrulama uyarısı** sayfasında **Hayır** ' a tıklayın (bir üretim senaryosunda doğrulama testlerini gerçekleştirmeniz gerekir). Ardından **İleri**'ye tıklayın.

8. Depolama alanları ' nı kullanıyorsanız **onay** sayfasında, **tüm uygun depolamayı kümeye ekle** etiketli onay kutusunu temizleyin.

   ![Düğüm onayı ekleme](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Depolama alanları 'nı kullanıyorsanız ve **tüm uygun depolama alanını kümeye ekle**seçeneğinin işaretini kaldırırsanız, Windows, kümeleme işlemi sırasında sanal diskleri ayırır. Sonuç olarak, depolama alanları kümeden kaldırılana ve PowerShell kullanılarak yeniden iliştirene kadar disk Yöneticisi veya Explorer 'da görünmez. Depolama alanları, depolama havuzlarında birden çok diski gruplandırır. Daha fazla bilgi için bkz. [depolama alanları](https://technet.microsoft.com/library/hh831739).

1. **İleri**’ye tıklayın.

1. **Son**'a tıklayın.

   Yük Devretme Kümesi Yöneticisi, kümenizin yeni bir düğüme sahip olduğunu gösterir ve **düğümleri düğümler** kapsayıcısında listeler.

10. Uzak Masaüstü oturumunun oturumunu kapatma.

### <a name="add-a-cluster-quorum-file-share"></a>Küme çekirdeği dosya paylaşma ekleme

Bu örnekte, Windows kümesi bir küme çekirdeği oluşturmak için bir dosya paylaşma kullanır. Bu öğretici bir düğüm ve dosya paylaşımının çoğunluğu çekirdeğini kullanır. Daha fazla bilgi için bkz. [Yük Devretme Kümesindeki Çekirdek Yapılandırmalarını Anlama](https://technet.microsoft.com/library/cc731739.aspx).

1. Dosya paylaşımının tanık üye sunucusuna Uzak Masaüstü oturumu ile bağlanın.

1. **Sunucu Yöneticisi**, **Araçlar**' a tıklayın. **Bilgisayar Yönetimi**'ni açın.

1. **Paylaşılan klasörler**' e tıklayın.

1. **Paylaşımlar**' a sağ tıklayın ve **yeni paylaşım...** öğesine tıklayın.

   ![Yeni paylaşma](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Paylaşım oluşturmak için **paylaşılan klasör oluşturma Sihirbazı 'nı** kullanın.

1. **Klasör yolu**sayfasında, **Araştır** ' a tıklayın ve paylaşılan klasör için bir yol bulun veya oluşturun. **İleri**’ye tıklayın.

1. **Ad, açıklama ve ayarlar** ' da, paylaşımın adını ve yolunu doğrulayın. **İleri**’ye tıklayın.

1. **Paylaşılan klasör izinleri** ayarlar **izinleri Özelleştir**. **Özel...** öğesine tıklayın.

1. **Izinleri Özelleştir**sayfasında **Ekle...** öğesine tıklayın.

1. Kümeyi oluşturmak için kullanılan hesabın tam denetime sahip olduğundan emin olun.

   ![Yeni paylaşma](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. **OK (Tamam)** düğmesine tıklayın.

1. **Paylaşılan klasör izinleri**' nde **son**' a tıklayın. Yeniden **son** ' a tıklayın.  

1. Sunucudan oturumu Kapat

### <a name="configure-cluster-quorum"></a>Küme çekirdeğini yapılandırma

Sonra, küme çekirdeğini ayarlayın.

1. Uzak Masaüstü ile ilk küme düğümüne bağlanın.

1. **Yük devretme kümesi Yöneticisi**, kümeye sağ tıklayın, **diğer eylemler**' ın üzerine gelin ve **küme çekirdek ayarlarını yapılandır...** ' ya tıklayın.

   ![Yeni paylaşma](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. **Küme çekirdeğini Yapılandırma Sihirbazı**' nda **İleri**' ye tıklayın.

1. **Çekirdek yapılandırması seçin seçeneğinde**, **çekirdek tanığını Seç**' i seçin ve **İleri**' ye tıklayın.

1. **Çekirdek tanığı seçin**sayfasında, **dosya paylaşma tanığını Yapılandır**' ı tıklatın.

   >[!TIP]
   >Windows Server 2016, bulut tanığını destekler. Bu tür bir tanık seçerseniz, dosya paylaşma tanığına gerek yoktur. Daha fazla bilgi için bkz. [Yük devretme kümesi için bulut tanığı dağıtma](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Bu öğretici, önceki işletim sistemleri tarafından desteklenen bir dosya paylaşma tanığı kullanır.

1. **Dosya paylaşma tanığını Yapılandır**sayfasında, oluşturduğunuz paylaşımın yolunu yazın. **İleri**’ye tıklayın.

1. **Onaylamada**ayarları doğrulayın. **İleri**’ye tıklayın.

1. **Son**'a tıklayın.

Küme çekirdek kaynakları, bir dosya paylaşma tanığı ile yapılandırılır.

## <a name="enable-availability-groups"></a>Kullanılabilirlik gruplarını etkinleştir

Sonra **AlwaysOn kullanılabilirlik grupları** özelliğini etkinleştirin. Her iki SQL Server üzerinde de bu adımları uygulayın.

1. **Başlangıç** ekranından **SQL Server Yapılandırma Yöneticisi**başlatın.
2. Tarayıcı ağacında, **SQL Server Hizmetleri**' ne ve ardından **SQL Server (MSSQLSERVER)** hizmetine sağ tıklayıp **Özellikler**' e tıklayın.
3. **AlwaysOn yüksek kullanılabilirlik** sekmesine tıklayın ve ardından aşağıdaki gibi **AlwaysOn kullanılabilirlik grupları etkinleştir**' i seçin:

    ![AlwaysOn Kullanılabilirlik Grupları etkinleştir](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. **Uygula**'ya tıklayın. Açılır iletişim kutusunda **Tamam** ' a tıklayın.

5. SQL Server hizmetini yeniden başlatın.

Diğer SQL Server bu adımları yineleyin.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>İlk SQL Server veritabanı oluşturma

1. RDP dosyasını, sysadmin sabit sunucu rolünün üyesi olan bir etki alanı hesabıyla ilk SQL Server başlatın.
1. SQL Server Management Studio açın ve ilk SQL Server bağlayın.
7. **Nesne Gezgini**, **veritabanları** ' na sağ tıklayın ve **Yeni veritabanı**' na tıklayın.
8. **Veritabanı adı**alanına **MyDB1**yazın ve ardından **Tamam**' a tıklayın.

### <a name="backupshare"></a>Yedek paylaşma oluşturma

1. **Sunucu Yöneticisi**Ilk SQL Server **Araçlar**' a tıklayın. **Bilgisayar Yönetimi**'ni açın.

1. **Paylaşılan klasörler**' e tıklayın.

1. **Paylaşımlar**' a sağ tıklayın ve **yeni paylaşım...** öğesine tıklayın.

   ![Yeni paylaşma](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Paylaşım oluşturmak için **paylaşılan klasör oluşturma Sihirbazı 'nı** kullanın.

1. **Klasör yolu**sayfasında, **Araştır** ' a tıklayın ve veritabanı yedeklemesi paylaşılan klasörü için bir yol bulun veya oluşturun. **İleri**’ye tıklayın.

1. **Ad, açıklama ve ayarlar** ' da, paylaşımın adını ve yolunu doğrulayın. **İleri**’ye tıklayın.

1. **Paylaşılan klasör izinleri** ayarlar **izinleri Özelleştir**. **Özel...** öğesine tıklayın.

1. **Izinleri Özelleştir**sayfasında **Ekle...** öğesine tıklayın.

1. Her iki sunucu için SQL Server ve SQL Server Agent hizmet hesaplarının tam denetime sahip olduğundan emin olun.

   ![Yeni paylaşma](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. **OK (Tamam)** düğmesine tıklayın.

1. **Paylaşılan klasör izinleri**' nde **son**' a tıklayın. Yeniden **son** ' a tıklayın.  

### <a name="take-a-full-backup-of-the-database"></a>Veritabanının tam yedeklemesini al

Günlük zincirini başlatmak için yeni veritabanını yedeklemeniz gerekir. Yeni veritabanının bir yedeğini almanız durumunda, bir kullanılabilirlik grubuna dahil edilemez.

1. **Nesne Gezgini**, veritabanına sağ tıklayın, **Görevler...** ' ın üzerine gelin, **Yedekle**' ye tıklayın.

1. Varsayılan yedekleme konumuna tam bir yedekleme gerçekleştirmek için **Tamam** ' a tıklayın.

## <a name="create-the-availability-group"></a>Kullanılabilirlik grubunu oluşturma
Artık aşağıdaki adımları kullanarak bir kullanılabilirlik grubu yapılandırmaya hazırsınız:

* İlk SQL Server bir veritabanı oluşturun.
* Veritabanının tam yedeklemesini ve bir işlem günlüğü yedeklemesini alın
* **Norecovery** seçeneğiyle, tam ve günlük yedeklemelerini ikinci SQL Server geri yükleme
* Zaman uyumlu tamamlama, otomatik yük devretme ve okunabilir ikincil çoğaltmalarla kullanılabilirlik grubu (**AG1**) oluşturun

### <a name="create-the-availability-group"></a>Kullanılabilirlik grubunu oluşturun:

1. Uzak Masaüstü oturumunda ilk SQL Server. SSMS 'de **Nesne Gezgini** , **AlwaysOn yüksek kullanılabilirlik** ' e sağ tıklayın ve **Yeni kullanılabilirlik Grubu Sihirbazı**' na tıklayın.

    ![Yeni kullanılabilirlik grubu Başlatma Sihirbazı](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. **Giriş** sayfasında **İleri**' ye tıklayın. Kullanılabilirlik grubu **adını belirtin** sayfasında, kullanılabilirlik grubu için bir ad yazın, örneğin **AG1**, **kullanılabilirlik grubu adı**. **İleri**’ye tıklayın.

    ![Yeni AG Sihirbazı, AG adını belirtin](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. **Veritabanları seçin** sayfasında, veritabanınızı seçin ve **İleri**' ye tıklayın.

   >[!NOTE]
   >Hedeflenen birincil çoğaltmada en az bir tam yedekleme gerçekleştirmenizin nedeni veritabanı bir kullanılabilirlik grubu önkoşullarını karşılar.

   ![Yeni AG Sihirbazı, veritabanlarını seçin](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. **Çoğaltmaları belirtin** sayfasında, **çoğaltma ekle**' ye tıklayın.

   ![Yeni AG Sihirbazı, çoğaltmaları belirtin](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. **Sunucuya Bağlan** iletişim kutusu açılır. **Sunucu adı**' nda ikinci sunucunun adını yazın. **Bağlan**'a tıklayın.

   **Çoğaltmaları belirtin** sayfasında, artık **kullanılabilirlik çoğaltmalarda**listelenen ikinci sunucuyu görmeniz gerekir. Çoğaltmaları aşağıdaki şekilde yapılandırın.

   ![Yeni AG Sihirbazı, çoğaltmaları belirtin (Tamam)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Bu kullanılabilirlik grubu için veritabanı yansıtma uç noktasını görmek için **uç noktalar** ' a tıklayın. [Veritabanı yansıtma uç noktaları için güvenlik duvarı kuralını](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall)ayarladığınızda kullandığınız bağlantı noktasını kullanın.

    ![Yeni AG Sihirbazı, Ilk veri eşitlemesini seçin](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. **Ilk veri eşitlemesini Seç** sayfasında **tam** ' ı seçin ve paylaşılan bir ağ konumu belirtin. Konum için, [oluşturduğunuz yedekleme payını](#backupshare)kullanın. Bu örnekte, **ilk SQL Server\>\Backup\\\<\\\\** . **İleri**’ye tıklayın.

   >[!NOTE]
   >Tam eşitleme, SQL Server ilk örneğindeki veritabanının tam yedeklemesini alır ve ikinci örneğe geri yükler. Büyük veritabanları için tam eşitleme, uzun sürebileceğinden önerilmez. Bu süreyi, veritabanının bir yedeklemesini el ile alarak ve `NO RECOVERY`geri yükleyerek azaltabilirsiniz. Veritabanı, kullanılabilirlik grubunu yapılandırmadan önce ikinci SQL Server `NO RECOVERY` ile zaten geri yüklenmişse **yalnızca Birleştir**' i seçin. Kullanılabilirlik grubunu yapılandırdıktan sonra yedeklemeyi almak istiyorsanız **ilk veri eşitlemesini atla**' yı seçin.

    ![Yeni AG Sihirbazı, Ilk veri eşitlemesini seçin](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. **Doğrulama** sayfasında, **İleri**' ye tıklayın. Bu sayfa aşağıdaki görüntüye benzer görünmelidir:

    ![Yeni AG Sihirbazı, doğrulama](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Bir kullanılabilirlik grubu dinleyicisi yapılandırmadığınıza ilişkin dinleyici yapılandırması için bir uyarı vardır. Azure sanal makinelerinde Azure Yük dengeleyiciyi oluşturduktan sonra dinleyiciyi oluşturduğunuz için bu uyarıyı yoksayabilirsiniz.

10. **Özet** sayfasında, **son**' a tıklayın, ardından sihirbaz yeni kullanılabilirlik grubunu yapılandırırken bekleyin. **İlerleme** sayfasında, ayrıntılı ilerlemeyi görüntülemek için **diğer ayrıntılar** ' a tıklayabilirsiniz. Sihirbaz tamamlandıktan sonra, kullanılabilirlik grubunun başarıyla oluşturulduğunu doğrulamak için **sonuçlar** sayfasını inceleyin.

     ![Yeni AG Sihirbazı, sonuçlar](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Sihirbazdan çıkmak için **Kapat** ' a tıklayın.

### <a name="check-the-availability-group"></a>Kullanılabilirlik grubunu denetleme

1. **Nesne Gezgini**' de, **AlwaysOn yüksek kullanılabilirlik**' i genişletin ve **kullanılabilirlik grupları**' nı genişletin. Artık bu kapsayıcıda yeni kullanılabilirlik grubunu görmeniz gerekir. Kullanılabilirlik grubuna sağ tıklayın ve **panoyu göster**' e tıklayın.

   ![AG panosunu göster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   **AlwaysOn panonuz** şuna benzer görünmelidir.

   ![AG panosu](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Çoğaltmaları, her çoğaltmanın yük devretme modunu ve eşitleme durumunu görebilirsiniz.

2. **Yük devretme kümesi Yöneticisi**, kümenize tıklayın. **Rolleri**seçin. Kullandığınız kullanılabilirlik grubu adı, kümedeki bir roldür. Bir dinleyici yapılandırmadıysanız, bu kullanılabilirlik grubunun istemci bağlantıları için bir IP adresi yok. Azure Yük dengeleyiciyi oluşturduktan sonra dinleyiciyi yapılandıracaksınız.

   ![Yük Devretme Kümesi Yöneticisi AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Yük Devretme Kümesi Yöneticisi kullanılabilirlik grubundan yük devretme yapmayı denemeyin. Tüm yük devretme işlemleri SSMS 'de **AlwaysOn Panosu** içinden gerçekleştirilmelidir. Daha fazla bilgi için bkz. [kullanılabilirlik gruplarıyla yük devretme kümesi Yöneticisi kullanma hakkında kısıtlamalar](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Bu noktada, iki SQL Server örneği üzerinde çoğaltmaları olan bir kullanılabilirlik grubunuz vardır. Kullanılabilirlik grubunu örnekler arasında taşıyabilirsiniz. Bir dinleyiciniz olmadığından kullanılabilirlik grubuna henüz bağlanamazsınız. Azure sanal makineler 'de, dinleyici bir yük dengeleyici gerektirir. Sonraki adım Azure 'da yük dengeleyici oluşturmaktır.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Azure yük dengeleyici oluşturma

Azure sanal makineler 'de, bir SQL Server kullanılabilirlik grubu yük dengeleyici gerektirir. Yük dengeleyici, kullanılabilirlik grubu dinleyicilerinin ve Windows Server yük devretme kümesinin IP adreslerini barındırır. Bu bölüm Azure portal yük dengeleyicinin nasıl oluşturulacağını özetler.

Azure Load Balancer bir Standart Load Balancer ya da temel Load Balancer olabilir. Standart Load Balancer temel Load Balancer daha fazla özelliğe sahiptir. Kullanılabilirlik grubu için, bir kullanılabilirlik bölgesi (bir kullanılabilirlik kümesi yerine) kullanıyorsanız, Standart Load Balancer gereklidir. Yük dengeleyici türleri arasındaki fark hakkında daha fazla bilgi için bkz. [Load Balancer SKU karşılaştırması](../../../load-balancer/load-balancer-overview.md#skus).

1. Azure portal, SQL sunucularınızın bulunduğu kaynak grubuna gidin ve **+ Ekle**' ye tıklayın.
1. **Load Balancer**arayın. Microsoft tarafından yayınlanan yük dengeleyiciyi seçin.

   ![Yük Devretme Kümesi Yöneticisi AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. **Oluştur**’ tıklayın.
1. Yük Dengeleyici için aşağıdaki parametreleri yapılandırın.

   | Ayar | Alan |
   | --- | --- |
   | **Adı** |Yük Dengeleyici için bir metin adı kullanın, örneğin **Sqllb**. |
   | **Tür** |İç |
   | **Sanal ağ** |Azure sanal ağının adını kullanın. |
   | **Alt ağ** |Sanal makinenin bulunduğu alt ağın adını kullanın.  |
   | **IP adresi ataması** |Statik |
   | **IP adresi** |Alt ağdan kullanılabilir bir adres kullanın. Kullanılabilirlik grubu dinleyiciniz için bu adresi kullanın. Bu, küme IP adresinizden farklı olduğunu unutmayın.  |
   | **Abonelik** |Sanal makineyle aynı aboneliği kullanın. |
   | **Konum** |Sanal makineyle aynı konumu kullanın. |

   Azure portal dikey penceresi şöyle görünmelidir:

   ![Yük Dengeleyici oluşturma](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Yük dengeleyiciyi oluşturmak için **Oluştur**' a tıklayın.

Yük dengeleyiciyi yapılandırmak için bir arka uç havuzu, bir araştırma oluşturmanız ve yük dengeleme kurallarını ayarlamanız gerekir. Bunları Azure portal yapın.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Kullanılabilirlik grubu dinleyicisi için arka uç Havuzu Ekle

1. Azure portal, kullanılabilirlik grubunuza gidin. Yeni oluşturulan yük dengeleyiciyi görmek için görünümü yenilemeniz gerekebilir.

   ![Kaynak grubunda Load Balancer bul](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Yük dengeleyiciye tıklayın, **arka uç havuzları**' na tıklayın ve **+ Ekle**' ye tıklayın.

1. Arka uç havuzu için bir ad yazın.

1. Arka uç havuzunu VM 'Leri içeren kullanılabilirlik kümesiyle ilişkilendirin.

1. **Hedef ağ IP yapılandırması**altında, **sanal makine** ' yi işaretleyin ve kullanılabilirlik Grubu çoğaltmalarını barındıracak sanal makinelerin her ikisini de seçin. Dosya paylaşma tanık sunucusunu dahil etme.

   >[!NOTE]
   >Her iki sanal makine de belirtilmemişse, bağlantılar yalnızca birincil çoğaltmaya başarılı olur.

1. Arka uç havuzunu oluşturmak için **Tamam** ' ı tıklatın.

### <a name="set-the-probe"></a>Araştırmayı ayarla

1. Yük dengeleyiciye tıklayın, **sistem durumu araştırmaları**' na tıklayın ve **+ Ekle**' ye tıklayın.

1. Dinleyici durumu araştırmasını aşağıdaki şekilde ayarlayın:

   | Ayar | Açıklama | Örnek
   | --- | --- |---
   | **Adı** | Metin | SQLAlwaysOnEndPointProbe |
   | **Protokolü** | TCP seçin | TCP |
   | **Bağlantı Noktası** | Kullanılmayan tüm bağlantı noktaları | 59999 |
   | **Aralık**  | Saniye cinsinden yoklama denemeleri arasındaki süre miktarı |5 |
   | **Sağlıksız eşik** | Bir sanal makinenin sağlıksız olarak kabul edilmesi için oluşması gereken birbirini izleyen yoklama hatalarının sayısı  | 2 |

1. Sistem durumu araştırmasını ayarlamak için **Tamam** ' ı tıklatın.

### <a name="set-the-load-balancing-rules"></a>Yük Dengeleme kurallarını ayarlama

1. Yük dengeleyiciye tıklayın, **Yük Dengeleme kuralları**' na tıklayın ve **+ Ekle**' ye tıklayın.

1. Dinleyici Yük Dengeleme kurallarını aşağıdaki şekilde ayarlayın.

   | Ayar | Açıklama | Örnek
   | --- | --- |---
   | **Adı** | Metin | SQLAlwaysOnEndPointListener |
   | **Ön uç IP adresi** | Bir adres seçin |Yük dengeleyiciyi oluştururken oluşturduğunuz adresi kullanın. |
   | **Protokolü** | TCP seçin |TCP |
   | **Bağlantı Noktası** | Kullanılabilirlik grubu dinleyicisi için bağlantı noktasını kullan | 1433 |
   | **Arka uç bağlantı noktası** | Bu alan, kayan IP doğrudan sunucu dönüşü için ayarlandığında kullanılmaz | 1433 |
   | **Yokla** |Araştırma için belirttiğiniz ad | SQLAlwaysOnEndPointProbe |
   | **Oturum kalıcılığı** | Açılan liste | **Yok.** |
   | **Boşta kalma zaman aşımı** | TCP bağlantısının açık tutulması için dakika sayısı | 4 |
   | **Kayan IP (doğrudan sunucu dönüşü)** | |Etkin |

   > [!WARNING]
   > Doğrudan sunucu dönüşü oluşturma sırasında ayarlanır. Bu değer değiştirilemez.

1. Dinleyici Yük Dengeleme kurallarını ayarlamak için **Tamam** ' ı tıklatın.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Windows Server yük devretme kümesi (WSFC) için küme çekirdeği IP adresini ekleme

WSFC IP adresinin Ayrıca yük dengeleyicide olması gerekir.

1. Portalda, aynı Azure Yük dengeleyicisinde, **ön uç IP yapılandırması** ' na tıklayın ve **+ Ekle**' ye tıklayın. Küme çekirdek kaynaklarında WSFC için yapılandırdığınız IP adresini kullanın. IP adresini statik olarak ayarlayın.

1. Yük dengeleyicide, **sistem durumu araştırmaları**' na tıklayın ve **+ Ekle**' ye tıklayın.

1. WSFC kümesi çekirdek IP adresi sistem durumu araştırmasını aşağıdaki şekilde ayarlayın:

   | Ayar | Açıklama | Örnek
   | --- | --- |---
   | **Adı** | Metin | Wsfcendpointaraştırması |
   | **Protokolü** | TCP seçin | TCP |
   | **Bağlantı Noktası** | Kullanılmayan tüm bağlantı noktaları | 58888 |
   | **Aralık**  | Saniye cinsinden yoklama denemeleri arasındaki süre miktarı |5 |
   | **Sağlıksız eşik** | Bir sanal makinenin sağlıksız olarak kabul edilmesi için oluşması gereken birbirini izleyen yoklama hatalarının sayısı  | 2 |

1. Sistem durumu araştırmasını ayarlamak için **Tamam** ' ı tıklatın.

1. Yük Dengeleme kurallarını ayarlayın. **Yük Dengeleme kuralları**' na tıklayın ve **+ Ekle**' ye tıklayın.

1. Küme çekirdeği IP adresi Yük Dengeleme kurallarını aşağıdaki şekilde ayarlayın.

   | Ayar | Açıklama | Örnek
   | --- | --- |---
   | **Adı** | Metin | WSFCEndPoint |
   | **Ön uç IP adresi** | Bir adres seçin |WSFC IP adresini yapılandırdığınızda oluşturduğunuz adresi kullanın. Bu, dinleyici IP adresinden farklıdır |
   | **Protokolü** | TCP seçin |TCP |
   | **Bağlantı Noktası** | Küme IP adresi için bağlantı noktasını kullanın. Bu, dinleyici araştırma bağlantı noktası için kullanılmayan kullanılabilir bir bağlantı noktasıdır. | 58888 |
   | **Arka uç bağlantı noktası** | Bu alan, kayan IP doğrudan sunucu dönüşü için ayarlandığında kullanılmaz | 58888 |
   | **Yokla** |Araştırma için belirttiğiniz ad | Wsfcendpointaraştırması |
   | **Oturum kalıcılığı** | Açılan liste | **Yok.** |
   | **Boşta kalma zaman aşımı** | TCP bağlantısının açık tutulması için dakika sayısı | 4 |
   | **Kayan IP (doğrudan sunucu dönüşü)** | |Etkin |

   > [!WARNING]
   > Doğrudan sunucu dönüşü oluşturma sırasında ayarlanır. Bu değer değiştirilemez.

1. Yük Dengeleme kurallarını ayarlamak için **Tamam** ' ı tıklatın.

## <a name="configure-listener"></a>Dinleyiciyi yapılandırma

Sonraki şey, yük devretme kümesinde bir kullanılabilirlik grubu dinleyicisi yapılandırmaktır.

> [!NOTE]
> Bu öğreticide tek bir ıLB IP adresiyle tek bir dinleyicinin nasıl oluşturulacağı gösterilmektedir. Bir veya daha fazla IP adresi kullanarak bir veya daha fazla dinleyici oluşturmak için bkz. [kullanılabilirlik grubu dinleyicisi oluşturma ve yük dengeleyici | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Dinleyici bağlantı noktasını ayarla

SQL Server Management Studio, dinleyici bağlantı noktasını ayarlayın.

1. SQL Server Management Studio başlatın ve birincil çoğaltmaya bağlanın.

1. **Kullanılabilirlik grubu dinleyicilerine** | **AlwaysOn yüksek kullanılabilirlik** | **kullanılabilirlik grupları** ' na gidin.

1. Artık Yük Devretme Kümesi Yöneticisi oluşturduğunuz dinleyici adını görmeniz gerekir. Dinleyici adına sağ tıklayın ve **Özellikler**' e tıklayın.

1. **Bağlantı noktası** kutusunda, kullanılabilirlik grubu dinleyicisinin bağlantı noktası numarasını belirtin. 1433 varsayılandır ve ardından **Tamam**' a tıklayın.

Artık Azure sanal makinelerinde Kaynak Yöneticisi modunda çalışan bir SQL Server kullanılabilirlik grubunuz var.

## <a name="test-connection-to-listener"></a>Dinleyiciye Bağlantıyı Sına

Bağlantıyı test etmek için:

1. Aynı sanal ağ içinde olan, ancak çoğaltmaya sahip olmayan bir SQL Server RDP. Kümedeki diğer SQL Server kullanabilirsiniz.

1. Bağlantıyı sınamak için **sqlcmd** yardımcı programını kullanın. Örneğin, aşağıdaki komut dosyası, Windows kimlik doğrulaması ile dinleyici aracılığıyla birincil çoğaltmaya bir **sqlcmd** bağlantısı kurar:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Dinleyici varsayılan bağlantı noktası (1433) dışında bir bağlantı noktası kullanıyorsa bağlantı dizesinde bağlantı noktasını belirtin. Örneğin, aşağıdaki sqlcmd komutu 1435 numaralı bağlantı noktasında bir dinleyiciye bağlanır:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

SQLCMD bağlantısı, birincil çoğaltmayı barındıran SQL Server her örneğine otomatik olarak bağlanır.

> [!TIP]
> Belirttiğiniz bağlantı noktasının her iki SQL Server güvenlik duvarında açık olduğundan emin olun. Her iki sunucu da kullandığınız TCP bağlantı noktası için bir gelen kuralı gerektirir. Daha fazla bilgi için bkz. [güvenlik duvarı kuralı ekleme veya düzenleme](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Sonraki adımlar

- [İkinci bir kullanılabilirlik grubu için yük dengeleyiciye BIR IP adresi ekleyin](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
