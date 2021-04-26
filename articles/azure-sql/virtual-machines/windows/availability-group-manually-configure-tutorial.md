---
title: 'Öğretici: SQL Server Always on kullanılabilirlik grubu yapılandırma'
description: Bu öğreticide, Azure sanal makinelerinde SQL Server Always on kullanılabilirlik grubu oluşturma gösterilmektedir.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: feab48f32396bcc89621433930c9a9f4689d8286
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97355452"
---
# <a name="tutorial-manually-configure-an-availability-group-sql-server-on-azure-vms"></a>Öğretici: bir kullanılabilirlik grubunu el Ile yapılandırma (Azure VM 'lerinde SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu öğreticide, Azure sanal makinelerinde (VM) SQL Server için her zaman açık kullanılabilirlik grubu oluşturma gösterilmektedir. Tüm öğreticide, iki SQL sunucusunda veritabanı çoğaltmasına sahip bir kullanılabilirlik grubu oluşturulur.

Bu makale kullanılabilirlik grubu ortamını el ile yapılandırdığında, [Azure Portal](availability-group-azure-portal-configure.md), [POWERSHELL veya Azure CLI](availability-group-az-commandline-configure.md)veya [Azure hızlı başlangıç şablonlarını](availability-group-quickstart-template-configure.md) da kullanmak da mümkündür. 


**Tahmini süre**: [Önkoşullar](availability-group-manually-configure-prerequisites-tutorial.md) karşılandığında yaklaşık 30 dakika sürer.


## <a name="prerequisites"></a>Önkoşullar

Öğretici, SQL Server her zaman açık kullanılabilirlik grupları hakkında temel bilgiler sahibi olduğunuzu varsayar. Daha fazla bilgiye ihtiyacınız varsa, bkz. [Always on kullanılabilirlik gruplarına genel bakış (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

Öğreticiye başlamadan önce, [Azure sanal makinelerinde her zaman açık kullanılabilirlik grupları oluşturmak için önkoşulları gerçekleştirmeniz](availability-group-manually-configure-prerequisites-tutorial.md)gerekir. Bu Önkoşullar zaten tamamlanırsa, [küme oluştur](#CreateCluster)' a atlayabilirsiniz.

Aşağıdaki tabloda, Bu öğreticiye başlamadan önce gerçekleştirmeniz gereken önkoşullar listelenmektedir:

| Gereksinim |Açıklama |
|----- |----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **İki SQL Server örneği** | -Bir Azure kullanılabilirlik kümesinde <br/> -Tek bir etki alanında <br/> -Yük Devretme Kümelemesi özelliği yüklü |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Windows Server** | Küme tanığı için dosya paylaşma |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server hizmet hesabı** | Etki alanı hesabı |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server Agent hizmet hesabı** | Etki alanı hesabı |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Güvenlik Duvarı bağlantı noktaları açık** | -SQL Server: varsayılan örnek için **1433** <br/> -Veritabanı yansıtma uç noktası: **5022** veya kullanılabilir herhangi bir bağlantı noktası <br/> -Kullanılabilirlik grubu yük dengeleyici IP adresi durum araştırması: **59999** veya kullanılabilir herhangi bir bağlantı noktası <br/> -Küme çekirdeği yük dengeleyici IP adresi durum araştırması: **58888** veya kullanılabilir herhangi bir bağlantı noktası |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Yük Devretme Kümelemesi özelliği Ekle** | SQL Server örneklerin her ikisi de bu özelliği gerektirir |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Yükleme etki alanı hesabı** | -Her SQL Server yerel yönetici <br/> -SQL Server her bir örneği için SQL Server sysadmin sabit sunucu rolü üyesi  |

>[!NOTE]
> Bu öğreticide sunulan adımların birçoğu artık [Azure Portal](availability-group-azure-portal-configure.md), [POWERSHELL ve az CLI](./availability-group-az-commandline-configure.md) ve [Azure hızlı başlangıç şablonlarıyla](availability-group-quickstart-template-configure.md)otomatikleştirilebilir.


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>Kümeyi oluşturma

Önkoşullar tamamlandıktan sonra, ilk adım iki SQL sunucusunu ve bir tanık sunucusunu içeren bir Windows Server yük devretme kümesi oluşturmaktır.

1. İlk SQL Server bağlanmak için Uzak Masaüstü Protokolü (RDP) kullanın. Hem SQL Server hem de tanık sunucusunda yönetici olan bir etki alanı hesabı kullanın.

   >[!TIP]
   >[Önkoşul belgesini](availability-group-manually-configure-prerequisites-tutorial.md)izlediyseniz, **corp\ınstall** adlı bir hesap oluşturdunuz. Bu hesabı kullanın.

2. **Sunucu Yöneticisi** panosunda **Araçlar**' ı seçin ve **Yük devretme kümesi Yöneticisi**' i seçin.
3. Sol bölmede **Yük devretme kümesi Yöneticisi**' a sağ tıklayın ve ardından **küme oluştur**' u seçin.

   ![Küme Oluştur](./media/availability-group-manually-configure-tutorial/40-createcluster.png)

4. Küme oluşturma Sihirbazı 'nda, aşağıdaki tabloda bulunan ayarlarla sayfaları düzenleyerek tek düğümlü bir küme oluşturun:

   | Sayfa | Ayarlar |
   | --- | --- |
   | Başlamadan Önce |Varsayılanları Kullan |
   | Sunucuları seçin |**Sunucu adını girin** alanına ilk SQL Server adı yazın ve **Ekle**' yi seçin. |
   | Doğrulama uyarısı |**Hayır 'ı seçin. bu küme Için Microsoft desteği gerekmiyor ve bu nedenle doğrulama testlerini çalıştırmak istemiyor. Ileri seçeneğini belirlediğinizde Kümeyi oluşturmaya devam** edin. |
   | Kümeyi yönetmek için erişim noktası |**Küme adı alanına bir** küme adı yazın, örneğin **SQLAGCluster1** .|
   | Onay |Depolama alanları kullanmadığınız takdirde Varsayılanları kullanın. Bu tablodan sonraki nota bakın. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Windows Server yük devretme kümesi IP adresini ayarlama

  > [!NOTE]
  > Windows Server 2019 ' de küme, **küme ağ adı** yerine bir **dağıtılmış sunucu adı** oluşturur. Windows Server 2019 kullanıyorsanız, bu öğreticide küme çekirdeği adına başvuran adımları atlayın. [PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-failover-cluster)kullanarak bir küme ağ adı oluşturabilirsiniz. Daha fazla bilgi için blog [Yük devretme kümesini gözden geçirin: küme ağ nesnesi](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) . 

1. **Yük devretme kümesi Yöneticisi**' de, **küme çekirdeği kaynakları** ' na kaydırın ve küme ayrıntılarını genişletin. **Başarısız** durumunda hem **adı** hem de **IP adresi** kaynaklarını görmeniz gerekir. Küme, makinenin kendisi ile aynı IP adresine atandığından ve bu nedenle yinelenen bir adres olduğundan, IP adresi kaynağı çevrimiçi duruma getirilemiyor.

2. Başarısız **IP adresi** kaynağına sağ tıklayın ve ardından **Özellikler**' i seçin.

   ![Küme özellikleri](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. **STATIK IP adresi** ' ni seçin ve sanal makinelerinizdeki aynı alt ağdan kullanılabilir bir adres belirtin.

4. **Küme çekirdeği kaynakları** bölümünde, küme adı ' na sağ tıklayın ve **çevrimiçi getir**' i seçin. Her iki kaynak da çevrimiçi olana kadar bekleyin. Küme adı kaynağı çevrimiçi olduğunda, etki alanı denetleyicisi (DC) sunucusunu yeni bir Active Directory (AD) bilgisayar hesabı ile güncelleştirir. Kullanılabilirlik grubu kümelenmiş hizmetini daha sonra çalıştırmak için bu AD hesabını kullanın.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>Diğer SQL Server kümeye ekleme

Diğer SQL Server kümeye ekleyin.

1. Tarayıcı ağacında, kümeye sağ tıklayın ve **düğüm Ekle**' yi seçin.

    ![Kümeye düğüm ekleme](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. **Düğüm Ekleme sihirbazında**, **İleri**' yi seçin. **Sunucuları seçin** sayfasında, ikinci SQL Server ekleyin. Sunucu adını **girin** alanına sunucu adını yazın ve ardından **Ekle**' yi seçin. İşiniz bittiğinde **İleri**' yi seçin.

1. **Doğrulama uyarısı** sayfasında **Hayır** ' ı seçin (bir üretim senaryosunda doğrulama testlerini gerçekleştirmeniz gerekir). Ardından **İleri**' yi seçin.

8. Depolama alanları ' nı kullanıyorsanız **onay** sayfasında, **tüm uygun depolamayı kümeye ekle** etiketli onay kutusunu temizleyin.

   ![Düğüm onayı ekleme](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

   >[!WARNING]
   >Depolama alanları 'nı kullanıyorsanız ve **tüm uygun depolama alanını kümeye ekle** seçeneğinin işaretini kaldırırsanız, Windows, kümeleme işlemi sırasında sanal diskleri ayırır. Sonuç olarak, depolama alanları kümeden kaldırılana ve PowerShell kullanılarak yeniden iliştirene kadar disk Yöneticisi veya Explorer 'da görünmez. Depolama alanları, depolama havuzlarında birden çok diski gruplandırır. Daha fazla bilgi için bkz. [depolama alanları](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)).
   >

1. **İleri**’yi seçin.

1. **Son**'u seçin.

   Yük Devretme Kümesi Yöneticisi, kümenizin yeni bir düğüme sahip olduğunu gösterir ve **düğümleri düğümler** kapsayıcısında listeler.

10. Uzak Masaüstü oturumunun oturumunu kapatma.

### <a name="add-a-cluster-quorum-file-share"></a>Küme çekirdeği dosya paylaşma ekleme

Bu örnekte, Windows kümesi bir küme çekirdeği oluşturmak için bir dosya paylaşma kullanır. Bu öğretici bir düğüm ve dosya paylaşımının çoğunluğu çekirdeğini kullanır. Daha fazla bilgi için bkz. [Yük Devretme Kümesindeki Çekirdek Yapılandırmalarını Anlama](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731739(v=ws.11)).

1. Dosya paylaşımının tanık üye sunucusuna Uzak Masaüstü oturumu ile bağlanın.

1. **Sunucu Yöneticisi** **Araçlar**' ı seçin. **Bilgisayar Yönetimi**'ni açın.

1. **Paylaşılan klasörler**' i seçin.

1. **Paylaşımlar**' a sağ tıklayın ve **yeni paylaşım...** öğesini seçin.

   ![Paylaşımlar ' a sağ tıklayın ve yeni paylaşım ' ı seçin.](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Paylaşım oluşturmak için **paylaşılan klasör oluşturma Sihirbazı 'nı** kullanın.

1. **Klasör yolu** sayfasında, **Araştır** ' ı seçin ve paylaşılan klasör için bir yol bulun veya oluşturun. **İleri**’yi seçin.

1. **Ad, açıklama ve ayarlar** ' da, paylaşımın adını ve yolunu doğrulayın. **İleri**’yi seçin.

1. **Paylaşılan klasör izinleri** ayarlar **izinleri Özelleştir**. Özel ' i seçin. **..**

1. **Izinleri Özelleştir** sayfasında **Ekle...** öğesini seçin.

1. Kümeyi oluşturmak için kullanılan hesabın tam denetime sahip olduğundan emin olun.

   ![Kümeyi oluşturmak için kullanılan hesabın tam denetime sahip olduğundan emin olun](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. **Tamam**’ı seçin.

1. **Paylaşılan klasör izinleri**' nde **son**' u seçin. **Son** ' u seçin.  

1. Sunucudan oturumu Kapat

### <a name="configure-the-cluster-quorum"></a>Küme çekirdeğini yapılandırma

Sonra, küme çekirdeğini ayarlayın.

1. Uzak Masaüstü ile ilk küme düğümüne bağlanın.

1. **Yük devretme kümesi Yöneticisi**, kümeye sağ tıklayın, **diğer eylemler**' ın üzerine gelin ve **küme çekirdek ayarlarını yapılandır...** seçeneğini belirleyin.

   ![Küme çekirdek ayarlarını yapılandır ' ı seçin](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. **Küme çekirdeğini Yapılandırma Sihirbazı**' nda, **İleri**' yi seçin.

1. **Çekirdek yapılandırması seçin seçeneğinde**, **çekirdek tanığını Seç**' i seçin ve **İleri**' yi seçin.

1. **Çekirdek tanığı seçin** sayfasında, **dosya paylaşma tanığını Yapılandır**' ı seçin.

   >[!TIP]
   >Windows Server 2016, bulut tanığını destekler. Bu tür bir tanık seçerseniz, dosya paylaşma tanığına gerek yoktur. Daha fazla bilgi için bkz. [Yük devretme kümesi için bulut tanığı dağıtma](/windows-server/failover-clustering/deploy-cloud-witness). Bu öğretici, önceki işletim sistemleri tarafından desteklenen bir dosya paylaşma tanığı kullanır.
   >

1. **Dosya paylaşma tanığını Yapılandır** sayfasında, oluşturduğunuz paylaşımın yolunu yazın. **İleri**’yi seçin.

1. **Onaylamada** ayarları doğrulayın. **İleri**’yi seçin.

1. **Son**'u seçin.

Küme çekirdek kaynakları, bir dosya paylaşma tanığı ile yapılandırılır.

## <a name="enable-availability-groups"></a>Kullanılabilirlik gruplarını etkinleştir

Sonra, **AlwaysOn kullanılabilirlik grupları** özelliğini etkinleştirin. Her iki SQL Server üzerinde de bu adımları uygulayın.

1. **Başlangıç** ekranından **SQL Server Yapılandırma Yöneticisi** başlatın.
2. Tarayıcı ağacında **SQL Server hizmetler**' i seçin, sonra **SQL Server (MSSQLSERVER)** hizmetine sağ tıklayıp **Özellikler**' i seçin.
3. **AlwaysOn yüksek kullanılabilirlik** sekmesini seçin ve ardından **AlwaysOn kullanılabilirlik grupları**'nı şu şekilde etkinleştir ' i seçin:

    ![AlwaysOn kullanılabilirlik gruplarını etkinleştir](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. **Uygula**’yı seçin. Açılır iletişim kutusunda **Tamam ' ı** seçin.

5. SQL Server hizmetini yeniden başlatın.

Diğer SQL Server bu adımları yineleyin.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an availability group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the availability groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, select **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and select **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and select **Next**.
6. In the **Profile** page, accept the default settings and select **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then select **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>İlk SQL Server veritabanı oluşturma

1. RDP dosyasını, sysadmin sabit sunucu rolünün üyesi olan bir etki alanı hesabıyla ilk SQL Server başlatın.
1. SQL Server Management Studio açın ve ilk SQL Server bağlayın.
7. **Nesne Gezgini**, **veritabanları** ' na sağ tıklayın ve **Yeni veritabanı**' nı seçin.
8. **Veritabanı adı** alanına **MyDB1** yazın ve ardından **Tamam**' ı seçin.

### <a name="create-a-backup-share"></a><a name="backupshare"></a> Yedek paylaşma oluşturma

1. **Sunucu Yöneticisi** Ilk SQL Server **Araçlar**' ı seçin. **Bilgisayar Yönetimi**'ni açın.

1. **Paylaşılan klasörler**' i seçin.

1. **Paylaşımlar**' a sağ tıklayın ve **yeni paylaşım...** öğesini seçin.

   ![Yeni paylaşma seçin](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Paylaşım oluşturmak için **paylaşılan klasör oluşturma Sihirbazı 'nı** kullanın.

1. **Klasör yolu** sayfasında, **Araştır** ' ı seçin ve veritabanı yedeklemesi paylaşılan klasörü için bir yol bulun veya oluşturun. **İleri**’yi seçin.

1. **Ad, açıklama ve ayarlar** ' da, paylaşımın adını ve yolunu doğrulayın. **İleri**’yi seçin.

1. **Paylaşılan klasör izinleri** ayarlar **izinleri Özelleştir**. Özel ' i seçin. **..**

1. **Izinleri Özelleştir** sayfasında **Ekle...** öğesini seçin.

1. Her iki sunucu için SQL Server ve SQL Server Agent hizmet hesaplarının tam denetime sahip olduğundan emin olun.

   ![Her iki sunucu için SQL Server ve SQL Server Agent hizmet hesaplarının tam denetime sahip olduğundan emin olun.](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. **Tamam**’ı seçin.

1. **Paylaşılan klasör izinleri**' nde **son**' u seçin. **Son** ' u seçin.  

### <a name="take-a-full-backup-of-the-database"></a>Veritabanının tam yedeklemesini al

Günlük zincirini başlatmak için yeni veritabanını yedeklemeniz gerekir. Yeni veritabanının bir yedeğini almanız durumunda, bir kullanılabilirlik grubuna dahil edilemez.

1. **Nesne Gezgini**, veritabanına sağ tıklayın, **Görevler...**' ın üzerine gelin, **Yedekle**' yi seçin.

1. Varsayılan yedekleme konumuna tam yedekleme almak için **Tamam ' ı** seçin.

## <a name="create-the-availability-group"></a>Kullanılabilirlik grubunu oluşturma

Artık aşağıdaki adımları kullanarak bir kullanılabilirlik grubu yapılandırmaya hazırsınız:

* İlk SQL Server bir veritabanı oluşturun.
* Veritabanının tam yedeklemesini ve bir işlem günlüğü yedeklemesini alın.
* Tam ve günlük yedeklemelerini ikinci SQL Server **norecovery** seçeneği ile geri yükleyin.
* Zaman uyumlu tamamlama, otomatik yük devretme ve okunabilir ikincil çoğaltmalarla kullanılabilirlik grubu (**AG1**) oluşturun.

### <a name="create-the-availability-group"></a>Kullanılabilirlik grubunu oluşturun:

1. Uzak Masaüstü oturumunda ilk SQL Server. SSMS 'de **Nesne Gezgini** , **AlwaysOn yüksek kullanılabilirlik** ' e sağ tıklayıp **Yeni kullanılabilirlik Grubu Sihirbazı**' nı seçin.

    ![Yeni kullanılabilirlik grubu Başlatma Sihirbazı](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. **Giriş** sayfasında, **İleri**' yi seçin. Kullanılabilirlik grubu **adını belirtin** sayfasında, kullanılabilirlik grubu **adı** alanına kullanılabilirlik grubu için bir ad yazın. Örneğin, **AG1**. **İleri**’yi seçin.

    ![Yeni kullanılabilirlik Grubu Sihirbazı, kullanılabilirlik grubu adını belirtin](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. **Veritabanları seçin** sayfasında, veritabanınızı seçin ve ardından **İleri**' yi seçin.

   >[!NOTE]
   >Hedeflenen birincil çoğaltmada en az bir tam yedekleme gerçekleştirmenizin nedeni veritabanı bir kullanılabilirlik grubu önkoşullarını karşılar.
   >

   ![Yeni kullanılabilirlik Grubu Sihirbazı, veritabanlarını seçin](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)

4. **Çoğaltmaları belirtin** sayfasında, **çoğaltma ekle**' yi seçin.

   ![Yeni kullanılabilirlik Grubu Sihirbazı, çoğaltmaları belirtin](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)

5. **Sunucuya Bağlan** iletişim kutusu açılır. **Sunucu adı**' nda ikinci sunucunun adını yazın. **Bağlan**’ı seçin.

   **Çoğaltmaları belirtin** sayfasında, artık **kullanılabilirlik çoğaltmalarda** listelenen ikinci sunucuyu görmeniz gerekir. Çoğaltmaları aşağıdaki şekilde yapılandırın.

   ![Yeni kullanılabilirlik Grubu Sihirbazı, çoğaltmaları belirtin (Tamam)](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. Bu kullanılabilirlik grubu için veritabanı yansıtma uç noktasını görmek için **uç noktalar** ' ı seçin. [Veritabanı yansıtma uç noktaları için güvenlik duvarı kuralını](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall)ayarladığınızda kullandığınız bağlantı noktasını kullanın.

    ![Yeni kullanılabilirlik Grubu Sihirbazı, Ilk veri eşitlemesini seçin](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. **Ilk veri eşitlemesini Seç** sayfasında **tam** ' ı seçin ve paylaşılan bir ağ konumu belirtin. Konum için, [oluşturduğunuz yedekleme payını](#backupshare)kullanın. Bu örnekte, **\\ \\ ilk SQL Server \> \\ \Backup<**. **İleri**’yi seçin.

   >[!NOTE]
   >Tam eşitleme, SQL Server ilk örneğindeki veritabanının tam yedeklemesini alır ve ikinci örneğe geri yükler. Büyük veritabanları için tam eşitleme, uzun sürebileceğinden önerilmez. Bu süreyi, veritabanının bir yedeklemesini el ile alarak ve ile geri yükleyerek azaltabilirsiniz `NO RECOVERY` . Veritabanı, `NO RECOVERY` kullanılabilirlik grubunu yapılandırmadan önce ikinci SQL Server ile zaten geri yüklenirse, **yalnızca Birleştir**' i seçin. Kullanılabilirlik grubunu yapılandırdıktan sonra yedeklemeyi almak istiyorsanız **ilk veri eşitlemesini atla**' yı seçin.
   >

   ![İlk veri eşitlemesini atla ' yı seçin](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. **Doğrulama** sayfasında, **İleri**' yi seçin. Bu sayfa aşağıdaki görüntüye benzer görünmelidir:

    ![Yeni kullanılabilirlik Grubu Sihirbazı, doğrulama](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >Bir kullanılabilirlik grubu dinleyicisi yapılandırmadığınıza ilişkin dinleyici yapılandırması için bir uyarı vardır. Azure sanal makinelerinde Azure Yük dengeleyiciyi oluşturduktan sonra dinleyiciyi oluşturduğunuz için bu uyarıyı yoksayabilirsiniz.

10. **Özet** sayfasında **son**' u seçin, sonra sihirbaz yeni kullanılabilirlik grubunu yapılandırırken bekleyin. **İlerleme** sayfasında, ayrıntılı ilerlemeyi görüntülemek Için **daha fazla ayrıntı** seçebilirsiniz. Sihirbaz tamamlandıktan sonra, kullanılabilirlik grubunun başarıyla oluşturulduğunu doğrulamak için **sonuçlar** sayfasını inceleyin.

     ![Yeni kullanılabilirlik Grubu Sihirbazı, sonuçlar](./media/availability-group-manually-configure-tutorial/74-results.png)

11. Sihirbazdan çıkmak için **Kapat** ' ı seçin.

### <a name="check-the-availability-group"></a>Kullanılabilirlik grubunu denetleme

1. **Nesne Gezgini**' de, **AlwaysOn yüksek kullanılabilirlik**' i genişletin ve ardından **kullanılabilirlik grupları**' nı genişletin. Artık bu kapsayıcıda yeni kullanılabilirlik grubunu görmeniz gerekir. Kullanılabilirlik grubuna sağ tıklayın ve **panoyu göster**' i seçin.

   ![Kullanılabilirlik grubu panosunu göster](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   **AlwaysOn panonuz** aşağıdaki ekran görüntüsüne benzer görünmelidir:

   ![kullanılabilirlik grubu panosu](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   Çoğaltmaları, her çoğaltmanın yük devretme modunu ve eşitleme durumunu görebilirsiniz.

2. **Yük devretme kümesi Yöneticisi**, kümenizi seçin. **Rolleri** seçin. Kullandığınız kullanılabilirlik grubu adı, kümedeki bir roldür. Bir dinleyici yapılandırmadınız, bu kullanılabilirlik grubunun istemci bağlantıları için bir IP adresi yok. Azure Yük dengeleyiciyi oluşturduktan sonra dinleyiciyi yapılandıracaksınız.

   ![Yük Devretme Kümesi Yöneticisi 'de kullanılabilirlik grubu](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Yük Devretme Kümesi Yöneticisi kullanılabilirlik grubundan yük devretme yapmayı denemeyin. Tüm yük devretme işlemleri SSMS 'de **AlwaysOn Panosu** içinden gerçekleştirilmelidir. Daha fazla bilgi için bkz. [kullanılabilirlik gruplarıyla yük devretme kümesi Yöneticisi kullanma hakkında kısıtlamalar](/sql/database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server).
    >

Bu noktada, iki SQL Server örneği üzerinde çoğaltmaları olan bir kullanılabilirlik grubunuz vardır. Kullanılabilirlik grubunu örnekler arasında taşıyabilirsiniz. Bir dinleyiciniz olmadığından kullanılabilirlik grubuna henüz bağlanamazsınız. Azure sanal makineler 'de, dinleyici bir yük dengeleyici gerektirir. Sonraki adım Azure 'da yük dengeleyici oluşturmaktır.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Azure yük dengeleyici oluşturma

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Azure sanal makineler 'de, bir SQL Server kullanılabilirlik grubu yük dengeleyici gerektirir. Yük dengeleyici, kullanılabilirlik grubu dinleyicilerinin ve Windows Server yük devretme kümesinin IP adreslerini barındırır. Bu bölüm Azure portal yük dengeleyicinin nasıl oluşturulacağını özetler.

Azure 'daki bir yük dengeleyici Standart Load Balancer veya temel bir Load Balancer olabilir. Standart Load Balancer temel Load Balancer daha fazla özelliğe sahiptir. Kullanılabilirlik grubu için, bir kullanılabilirlik bölgesi (bir kullanılabilirlik kümesi yerine) kullanıyorsanız, Standart Load Balancer gereklidir. Yük dengeleyici SKU 'Ları arasındaki fark hakkındaki ayrıntılar için bkz. [Load Balancer SKU karşılaştırması](../../../load-balancer/skus.md).

1. Azure portal, SQL sunucularınızın bulunduğu kaynak grubuna gidin ve **+ Ekle**' yi seçin.
1. **Load Balancer** arayın. Microsoft tarafından yayınlanan yük dengeleyiciyi seçin.

   ![Microsoft tarafından yayınlanan yük dengeleyiciyi seçin](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. **Oluştur**’u seçin.
1. Yük Dengeleyici için aşağıdaki parametreleri yapılandırın.

   | Ayar | Alan |
   | --- | --- |
   | **Ad** |Yük Dengeleyici için bir metin adı kullanın, örneğin **Sqllb**. |
   | **Tür** |İç |
   | **Sanal ağ** |Azure sanal ağının adını kullanın. |
   | **Alt ağ** |Sanal makinenin bulunduğu alt ağın adını kullanın.  |
   | **IP adresi ataması** |Statik |
   | **IP adresi** |Alt ağdan kullanılabilir bir adres kullanın. Kullanılabilirlik grubu dinleyiciniz için bu adresi kullanın. Bu, küme IP adresinizden farklı olduğunu unutmayın.  |
   | **Abonelik** |Sanal makineyle aynı aboneliği kullanın. |
   | **Konum** |Sanal makineyle aynı konumu kullanın. |

   Azure portal dikey penceresi şöyle görünmelidir:

   ![Load Balancer oluştur](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. Yük dengeleyiciyi oluşturmak için **Oluştur**' u seçin.

Yük dengeleyiciyi yapılandırmak için bir arka uç havuzu, bir araştırma oluşturmanız ve yük dengeleme kurallarını ayarlamanız gerekir. Bunları Azure portal yapın.

### <a name="add-a-backend-pool-for-the-availability-group-listener"></a>Kullanılabilirlik grubu dinleyicisi için bir arka uç havuzu ekleme

1. Azure portal, kullanılabilirlik grubunuza gidin. Yeni oluşturulan yük dengeleyiciyi görmek için görünümü yenilemeniz gerekebilir.

   ![Kaynak grubunda Load Balancer bul](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. Yük dengeleyiciyi seçin, **arka uç havuzları**' nı seçin ve **+ Ekle**' yi seçin.

1. Arka uç havuzu için bir ad yazın.

1. Arka uç havuzunu VM 'Leri içeren kullanılabilirlik kümesiyle ilişkilendirin.

1. **Hedef ağ IP yapılandırması** altında, **sanal makine** ' yi işaretleyin ve kullanılabilirlik Grubu çoğaltmalarını barındıracak sanal makinelerin her ikisini de seçin. Dosya paylaşma tanık sunucusunu dahil etme.

   >[!NOTE]
   >Her iki sanal makine de belirtilmemişse, bağlantılar yalnızca birincil çoğaltmaya başarılı olur.

1. Arka uç havuzunu oluşturmak için **Tamam ' ı** seçin.

### <a name="set-the-probe"></a>Araştırmayı ayarla

1. Yük dengeleyiciyi seçin, **sistem durumu araştırmaları**' nı seçin ve **+ Ekle**' yi seçin.

1. Dinleyici durumu araştırmasını aşağıdaki şekilde ayarlayın:

   | Ayar | Açıklama | Örnek
   | --- | --- |---
   | **Ad** | Metin | Sqlalwaysonendpointaraştırması |
   | **Protokol** | TCP’yi seçin | TCP |
   | **Bağlantı noktası** | Kullanılmayan tüm bağlantı noktaları | 59999 |
   | **Aralık**  | Saniye cinsinden yoklama denemeleri arasındaki süre miktarı |5 |
   | **İyi durumda olmayan durum eşiği** | Bir sanal makinenin sağlıksız olarak kabul edilmesi için oluşması gereken birbirini izleyen yoklama hatalarının sayısı  | 2 |

1. Sistem durumu araştırmasını ayarlamak için **Tamam ' ı** seçin.

### <a name="set-the-load-balancing-rules"></a>Yük Dengeleme kurallarını ayarlama

1. Yük dengeleyiciyi seçin, **Yük Dengeleme kuralları**' nı seçin ve **+ Ekle**' yi seçin.

1. Dinleyici Yük Dengeleme kurallarını aşağıdaki şekilde ayarlayın.

   | Ayar | Açıklama | Örnek
   | --- | --- |---
   | **Ad** | Metin | SQLAlwaysOnEndPointListener |
   | **Ön uç IP adresi** | Bir adres seçin |Yük dengeleyiciyi oluştururken oluşturduğunuz adresi kullanın. |
   | **Protokol** | TCP’yi seçin |TCP |
   | **Bağlantı noktası** | Kullanılabilirlik grubu dinleyicisi için bağlantı noktasını kullan | 1433 |
   | **Arka uç bağlantı noktası** | Bu alan, kayan IP doğrudan sunucu dönüşü için ayarlandığında kullanılmaz | 1433 |
   | **Yoklama** |Araştırma için belirttiğiniz ad | Sqlalwaysonendpointaraştırması |
   | **Oturum kalıcılığı** | Açılan liste | **Hiçbiri** |
   | **Boşta kalma zaman aşımı** | TCP bağlantısının açık tutulması için dakika sayısı | 4 |
   | **Kayan IP (doğrudan sunucu dönüşü)** | |Etkin |

   > [!WARNING]
   > Doğrudan sunucu dönüşü oluşturma sırasında ayarlanır. Bu değer değiştirilemez.
   >

1. Dinleyici Yük Dengeleme kurallarını ayarlamak için **Tamam ' ı** seçin.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Windows Server yük devretme kümesi (WSFC) için küme çekirdeği IP adresini ekleme

WSFC IP adresinin Ayrıca yük dengeleyicide olması gerekir.

1. Azure portal, aynı Azure yük dengeleyicisine gidin. **Ön uç IP yapılandırması** ' nı seçip **+ Ekle**' yi seçin. Küme çekirdek kaynaklarında WSFC için yapılandırdığınız IP adresini kullanın. IP adresini statik olarak ayarlayın.

1. Yük dengeleyicide **sistem durumu araştırmaları**' nı seçin ve **+ Ekle**' yi seçin.

1. WSFC kümesi çekirdek IP adresi sistem durumu araştırmasını aşağıdaki şekilde ayarlayın:

   | Ayar | Açıklama | Örnek
   | --- | --- |---
   | **Ad** | Metin | Wsfcendpointaraştırması |
   | **Protokol** | TCP’yi seçin | TCP |
   | **Bağlantı noktası** | Kullanılmayan tüm bağlantı noktaları | 58888 |
   | **Aralık**  | Saniye cinsinden yoklama denemeleri arasındaki süre miktarı |5 |
   | **İyi durumda olmayan durum eşiği** | Bir sanal makinenin sağlıksız olarak kabul edilmesi için oluşması gereken birbirini izleyen yoklama hatalarının sayısı  | 2 |

1. Sistem durumu araştırmasını ayarlamak için **Tamam ' ı** seçin.

1. Yük Dengeleme kurallarını ayarlayın. **Yük Dengeleme kuralları**' nı seçin ve **+ Ekle**' yi seçin.

1. Küme çekirdeği IP adresi Yük Dengeleme kurallarını aşağıdaki şekilde ayarlayın.

   | Ayar | Açıklama | Örnek
   | --- | --- |---
   | **Ad** | Metin | WSFCEndPoint |
   | **Ön uç IP adresi** | Bir adres seçin |WSFC IP adresini yapılandırdığınızda oluşturduğunuz adresi kullanın. Bu, dinleyici IP adresinden farklıdır |
   | **Protokol** | TCP’yi seçin |TCP |
   | **Bağlantı noktası** | Küme IP adresi için bağlantı noktasını kullanın. Bu, dinleyici araştırma bağlantı noktası için kullanılmayan kullanılabilir bir bağlantı noktasıdır. | 58888 |
   | **Arka uç bağlantı noktası** | Bu alan, kayan IP doğrudan sunucu dönüşü için ayarlandığında kullanılmaz | 58888 |
   | **Yoklama** |Araştırma için belirttiğiniz ad | Wsfcendpointaraştırması |
   | **Oturum kalıcılığı** | Açılan liste | **Hiçbiri** |
   | **Boşta kalma zaman aşımı** | TCP bağlantısının açık tutulması için dakika sayısı | 4 |
   | **Kayan IP (doğrudan sunucu dönüşü)** | |Etkin |

   > [!WARNING]
   > Doğrudan sunucu dönüşü oluşturma sırasında ayarlanır. Bu değer değiştirilemez.
   >

1. Yük Dengeleme kurallarını ayarlamak için **Tamam ' ı** seçin.

## <a name="configure-the-listener"></a><a name="configure-listener"></a> Dinleyiciyi yapılandırma

Sonraki şey, yük devretme kümesinde bir kullanılabilirlik grubu dinleyicisi yapılandırmaktır.

> [!NOTE]
> Bu öğreticide tek bir ıLB IP adresine sahip tek bir dinleyicinin nasıl oluşturulacağı gösterilmektedir. Bir veya daha fazla IP adresi kullanarak bir veya daha fazla dinleyici oluşturmak için bkz. [kullanılabilirlik grubu dinleyicisi oluşturma ve yük dengeleyici | Azure](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Dinleyici bağlantı noktasını ayarla

SQL Server Management Studio, dinleyici bağlantı noktasını ayarlayın.

1. SQL Server Management Studio başlatın ve birincil çoğaltmaya bağlanın.

1. **AlwaysOn yüksek kullanılabilirlik**  >  **kullanılabilirlik grupları**  >  **kullanılabilirlik grubu dinleyicileri**' ne gidin.

1. Artık Yük Devretme Kümesi Yöneticisi oluşturduğunuz dinleyici adını görmeniz gerekir. Dinleyici adına sağ tıklayın ve **Özellikler**' i seçin.

1. **Bağlantı noktası** kutusunda, kullanılabilirlik grubu dinleyicisinin bağlantı noktası numarasını belirtin. Varsayılan değer 1433 ' dir. **Tamam**’ı seçin.

Artık Azure sanal makinelerinde Kaynak Yöneticisi modunda çalışan bir SQL Server kullanılabilirlik grubunuz var.

## <a name="test-connection-to-listener"></a>Dinleyiciye Bağlantıyı Sına

Bağlantıyı test etmek için:

1. Aynı sanal ağdaki bir SQL Server bağlanmak için RDP kullanın, ancak çoğaltmaya sahip değildir. Kümedeki diğer SQL Server kullanabilirsiniz.

1. Bağlantıyı sınamak için **sqlcmd** yardımcı programını kullanın. Örneğin, aşağıdaki komut dosyası, Windows kimlik doğrulaması ile dinleyici aracılığıyla birincil çoğaltmaya bir **sqlcmd** bağlantısı kurar:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Dinleyici varsayılan bağlantı noktası (1433) dışında bir bağlantı noktası kullanıyorsa bağlantı dizesinde bağlantı noktasını belirtin. Örneğin, aşağıdaki `sqlcmd` komut 1435 numaralı bağlantı noktasında bir dinleyiciye bağlanır:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

SQLCMD bağlantısı, birincil çoğaltmayı barındıran SQL Server her örneğine otomatik olarak bağlanır.

> [!TIP]
> Belirttiğiniz bağlantı noktasının her iki SQL Server güvenlik duvarında açık olduğundan emin olun. Her iki sunucu da kullandığınız TCP bağlantı noktası için bir gelen kuralı gerektirir. Daha fazla bilgi için bkz. [güvenlik duvarı kuralı ekleme veya düzenleme](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)).
>

## <a name="next-steps"></a>Sonraki adımlar

- [İkinci bir kullanılabilirlik grubu için yük dengeleyiciye BIR IP adresi ekleyin](availability-group-listener-powershell-configure.md#Add-IP).