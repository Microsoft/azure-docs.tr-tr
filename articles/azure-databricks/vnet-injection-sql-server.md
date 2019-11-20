---
title: Azure Databricks ile SQL Server Linux Docker kapsayıcısını sorgulama
description: Bu makalede VNet ekleme olarak da bilinen sanal ağınıza Azure Databricks dağıtma açıklanmaktadır.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747671"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Öğretici: bir Azure Databricks not defteriyle sanal ağda SQL Server Linux Docker kapsayıcısını sorgulama

Bu öğretici, bir sanal ağda SQL Server Linux Docker kapsayıcısı ile Azure Databricks nasıl tümleştirileceğini öğretir. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir Azure Databricks çalışma alanını bir sanal ağa dağıtma
> * Bir Linux sanal makinesini genel ağa yükler
> * Docker'ı yükleme
> * Linux Docker kapsayıcısına Microsoft SQL Server yüklemesi
> * Databricks Not defterinden JDBC kullanarak SQL Server sorgulama

## <a name="prerequisites"></a>Ön koşullar

* [Bir sanal ağda Databricks çalışma alanı](quickstart-create-databricks-workspace-vnet-injection.md)oluşturun.

* [Windows Için Ubuntu](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)'ı yükler.

* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)’yu indirin.

## <a name="create-a-linux-virtual-machine"></a>Linux sanal makinesi oluşturma

1. Azure portal **sanal makineler**simgesini seçin. Ardından **+ Ekle**' yi seçin.

    ![Yeni Azure sanal makinesi Ekle](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. **Temel bilgiler** sekmesinde Ubuntu Server 18,04 LTS ' yi SEÇIN ve VM boyutunu B2s olarak değiştirin. Yönetici Kullanıcı adı ve parolası seçin.

    ![Yeni sanal makine yapılandırmasının temel bilgiler sekmesi](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. **Ağ** sekmesine gidin. Azure Databricks kümenizi içeren sanal ağı ve genel alt ağı seçin. Sanal makineyi dağıtmak için **gözden geçir + oluştur**' u ve ardından **Oluştur** ' u seçin.

    ![Yeni sanal makine yapılandırmasının ağ sekmesi](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Dağıtım tamamlandığında, sanal makineye gidin. **Genel bakış**BÖLÜMÜNDEKI genel IP adresi ve sanal ağ/alt ağa dikkat edin. **Genel IP adresini** seçin

    ![Sanal makineye genel bakış](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. **Atamayı** **statik** olarak değiştirin ve bir **DNS ad etiketi**girin. **Kaydet**' i seçin ve sanal makineyi yeniden başlatın.

    ![Genel IP adresi yapılandırması](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. **Ayarlar**altında **ağ** sekmesini seçin. Azure Databricks dağıtımı sırasında oluşturulan ağ güvenlik grubunun sanal makineyle ilişkilendirildiğine dikkat edin. **Gelen bağlantı noktası kuralı ekle**' yi seçin.

7. SSH için 22 numaralı bağlantı noktasını açmak üzere bir kural ekleyin. Aşağıdaki ayarları kullanın:
    
    |Ayar|Önerilen değer|Açıklama|
    |-------|---------------|-----------|
    |Kaynak|IP Adresleri|IP adresleri, belirli bir kaynak IP adresinden gelen trafiğe bu kural tarafından izin verileceğini veya reddedileceğini belirtir.|
    |Kaynak IP adresleri|Genel IP\> <|Genel IP adresinizi girin. [Bing.com](https://www.bing.com/) adresini ziyaret ederek ve **"My IP"** araması yaparak genel IP adresinizi bulabilirsiniz.|
    |Kaynak bağlantı noktası aralıkları|*|Herhangi bir bağlantı noktasından trafiğe izin verin.|
    |Hedef|IP Adresleri|IP adresleri, belirli bir kaynak IP adresi için giden trafiğe bu kural tarafından izin verileceğini veya reddedileceğini belirtir.|
    |Hedef IP adresleri|VM genel IP 'niz <\>|Sanal makinenizin genel IP adresini girin. Bunu, sanal makinenizin **genel bakış** sayfasında bulabilirsiniz.|
    |Hedef bağlantı noktası aralıkları|22|SSH için 22 numaralı bağlantı noktasını açın.|
    |Öncelik|290|Kurala öncelik verin.|
    |Ad|SSH-databricks-öğretici-VM|Kurala bir ad verin.|


    ![Bağlantı noktası 22 için gelen güvenlik kuralı ekle](./media/vnet-injection-sql-server/open-port.png)

8. Aşağıdaki ayarlarla SQL için 1433 numaralı bağlantı noktasını açmak üzere bir kural ekleyin:

    |Ayar|Önerilen değer|Açıklama|
    |-------|---------------|-----------|
    |Kaynak|Herhangi biri|Kaynak, belirli bir kaynak IP adresinden gelen trafiğe bu kural tarafından izin verileceğini veya reddedileceğini belirtir.|
    |Kaynak bağlantı noktası aralıkları|*|Herhangi bir bağlantı noktasından trafiğe izin verin.|
    |Hedef|IP Adresleri|IP adresleri, belirli bir kaynak IP adresi için giden trafiğe bu kural tarafından izin verileceğini veya reddedileceğini belirtir.|
    |Hedef IP adresleri|VM genel IP 'niz <\>|Sanal makinenizin genel IP adresini girin. Bunu, sanal makinenizin **genel bakış** sayfasında bulabilirsiniz.|
    |Hedef bağlantı noktası aralıkları|1433|SQL Server için 22 numaralı bağlantı noktasını açın.|
    |Öncelik|300|Kurala öncelik verin.|
    |Ad|SQL-databricks-öğretici-VM|Kurala bir ad verin.|

    ![1433 numaralı bağlantı noktası için gelen güvenlik kuralı ekle](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Docker kapsayıcısında SQL Server çalıştırma

1. [Windows Için Ubuntu](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)veya sanal makinede SSH kullanmanıza imkan tanıyan başka herhangi bir araç açın. Azure portal sanal makinenize gidin ve bağlanmanız gereken SSH komutunu almak için **Bağlan** ' ı seçin.

    ![Sanal makineye bağlanma](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Ubuntu terminalinizdeki komutu girin ve sanal makineyi yapılandırırken oluşturduğunuz yönetici parolasını girin.

    ![Ubuntu Terminal SSH oturum açma](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Sanal makineye Docker yüklemek için aşağıdaki komutu kullanın.

    ```bash
    sudo apt-get install docker.io
    ```

    Aşağıdaki komutla Docker yüklemesini doğrulayın:

    ```bash
    sudo docker --version
    ```

4. Görüntüyü yükler.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Görüntüleri kontrol edin.

    ```bash
    sudo docker images
    ```

5. Kapsayıcıyı görüntüden çalıştırın.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Kapsayıcının çalıştığını doğrulayın.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>SQL veritabanı oluşturma

1. SQL Server Management Studio açın ve sunucu adını ve SQL kimlik doğrulamasını kullanarak sunucuya bağlanın. Oturum açma Kullanıcı adı **sa** ve parola, Docker komutunda ayarlanan paroladır. Örnek komutta parola `Password1234`.

    ![SQL Server Management Studio kullanarak SQL Server bağlanma](./media/vnet-injection-sql-server/ssms-login.png)

2. Başarıyla bağlandıktan sonra **Yeni sorgu** ' yı seçin ve bir veritabanı, tablo oluşturmak ve tablodaki bazı kayıtları eklemek için aşağıdaki kod parçacığını girin.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![SQL Server veritabanı oluşturmak için sorgu](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Azure Databricks SQL Server sorgula

1. Azure Databricks çalışma alanınıza gidin ve önkoşulların bir parçası olarak bir küme oluşturdunuz. Ardından, **Not Defteri Oluştur**' u seçin. Not defterine bir ad verin, dil olarak *Python* ' ı seçin ve oluşturduğunuz kümeyi seçin.

    ![Yeni Databricks Not defteri ayarları](./media/vnet-injection-sql-server/create-notebook.png)

2. SQL Server sanal makinenin iç IP adresine ping eklemek için aşağıdaki komutu kullanın. Bu ping başarılı olmalıdır. Aksi takdirde, kapsayıcının çalıştığını doğrulayın ve ağ güvenlik grubu (NSG) yapılandırmasını gözden geçirin.

    ```python
    %sh
    ping 10.179.64.4
    ```

    Gözden geçirmek için nslookup komutunu da kullanabilirsiniz.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. SQL Server başarılı bir şekilde gönderdikten sonra veritabanını ve tabloları sorgulayabilirsiniz. Aşağıdaki Python kodunu çalıştırın:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, kaynak grubunu, Azure Databricks çalışma alanını ve tüm ilgili kaynakları silin. İşin silinmesi gereksiz faturalandırmayı önler. Azure Databricks çalışma alanını gelecekte kullanmayı planlıyorsanız kümeyi durdurup daha sonra yeniden başlatabilirsiniz. Bu Azure Databricks çalışma alanını kullanmaya devam edemeyecekecekseniz, aşağıdaki adımları kullanarak bu öğreticide oluşturduğunuz tüm kaynakları silin:

1. Azure portal sol taraftaki menüden **kaynak grupları** ' na ve ardından oluşturduğunuz kaynak grubunun adına tıklayın.

2. Kaynak grubu sayfanızda **Sil**' i seçin, metin kutusuna silinecek kaynağın adını yazın ve ardından yeniden **Sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Databricks kullanarak verileri ayıklama, dönüştürme ve yükleme hakkında bilgi edinmek için sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [Öğretici: Azure Databricks kullanarak verileri ayıklama, dönüştürme ve yükleme](databricks-extract-load-sql-data-warehouse.md)
