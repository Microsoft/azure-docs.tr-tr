---
title: Azure SQL Edge 'i Docker ile dağıtma-Azure SQL Edge
description: Docker ile Azure SQL Edge dağıtımı hakkında bilgi edinin
keywords: SQL Edge, kapsayıcı, Docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: fce098767fffd36376399bbd9396699e3d9fbfd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392087"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>Docker ile Azure SQL Edge dağıtma

Bu hızlı başlangıçta, Azure SQL Edge kapsayıcı görüntüsünü çekmek ve çalıştırmak için Docker 'ı kullanırsınız. Ardından **sqlcmd** ile bağlanarak ilk veritabanınızı oluşturun ve sorguları çalıştırın.

Bu görüntü, Ubuntu 18,04 tabanlı Azure SQL Edge 'ten oluşur. Linux üzerinde veya Mac/Windows için Docker ile Docker motoru 1.8 + ile kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

- Docker Engine 1.8 + desteklenen Linux dağıtımında veya Mac/Windows için Docker. Daha fazla bilgi için bkz. [Docker 'ı Install](https://docs.docker.com/engine/installation/). Azure SQL Edge görüntüleri Ubuntu 18,04 tabanlı olduğundan, Ubuntu 18,04 Docker konağını kullanmanız önerilir.
- Docker **overlay2** depolama sürücüsü. Bu, çoğu kullanıcı için varsayılandır. Bu depolama sağlayıcısını kullanmadığınız ve değiştirmeniz gerektiğini fark ederseniz lütfen [overlay2 'yi yapılandırmak için Docker belgelerindeki](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver)yönergeler ve Uyarılar bölümüne bakın.
- En az 10 GB disk alanı.
- En az 1 GB RAM.
- [Azure SQL Edge Için donanım gereksinimleri](./features.md#hardware-support).


## <a name="pull-and-run-the-container-image"></a>Kapsayıcı görüntüsünü çekme ve çalıştırma

Aşağıdaki adımları başlatmadan önce, bu makalenin üst kısmında tercih ettiğiniz kabuğu (Bash, PowerShell veya cmd) seçtiğinizden emin olun.

1. Azure SQL Edge kapsayıcı görüntüsünü Microsoft Container Registry çekin.

    - Azure SQL Edge kapsayıcı görüntüsünü çekme
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> Bu makaledeki Bash komutları için `sudo` kullanılır. MacOS & Windows 'da sudo gerekli olmayabilir. Linux 'ta, Docker çalıştırmak için sudo kullanmak istemiyorsanız, bir Docker grubu yapılandırabilir ve bu gruba kullanıcı ekleyebilirsiniz. Daha fazla bilgi için bkz. [Linux için yükleme sonrası adımları](https://docs.docker.com/engine/install/linux-postinstall/).

Önceki komut, en son Azure SQL Edge kapsayıcı görüntülerini çeker. Tüm kullanılabilir görüntüleri görmek için bkz. [Azure-SQL-EGDE Docker Hub sayfası](https://hub.docker.com/_/microsoft-azure-sql-edge).

2. Kapsayıcı görüntüsünü Docker ile çalıştırmak için, bir bash kabuğu (Linux/macOS) veya yükseltilmiş PowerShell komut isteminden aşağıdaki komutu kullanabilirsiniz.
    
    - Geliştirici sürümü olarak çalışan bir Azure SQL Edge örneği başlatın
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - Premium sürümü olarak çalışan bir Azure SQL Edge örneği başlatın
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > Bu komutları çalıştırmak için Windows üzerinde PowerShell kullanıyorsanız tek tırnak yerine çift tırnak kullanın.


    > [!NOTE]
    > Parola, Microsoft SQL veritabanı altyapısı varsayılan parola ilkesini izlemelidir, aksi takdirde kapsayıcı SQL Engine 'i Ayarlayamayabilir ve çalışmayı durdurur. Varsayılan olarak, parola en az 8 karakter uzunluğunda olmalı ve şu dört kümeden üçünden karakterler içermelidir: büyük harfler, küçük harfler, taban 10 basamak ve semboller. [Docker logs](https://docs.docker.com/engine/reference/commandline/logs/) komutunu yürüterek hata günlüğünü inceleyebilirsiniz.
    
    Aşağıdaki tabloda, önceki örnekteki parametrelerin açıklaması verilmiştir `docker run` :

    | Parametre | Açıklama |
    |-----|-----|
    | **-e "ACCEPT_EULA = Y"** |  [Son Kullanıcı lisanslama sözleşmesinin](https://go.microsoft.com/fwlink/?linkid=2139274)kabul edildiğini onaylamak için **ACCEPT_EULA** değişkenini herhangi bir değere ayarlayın. Azure SQL Edge görüntüsü için gerekli ayar. |
    | **-e "MSSQL_SA_PASSWORD = sizin güçlü (!) Parolayı** | En az 8 karakter olan ve [Azure SQL Edge parola gereksinimlerini](/sql/relational-databases/security/password-policy)karşılayan kendi güçlü parolanızı belirtin. Azure SQL Edge görüntüsü için gerekli ayar. |
    | **-p 1433:1433** | Konak ortamında (ilk değer) bir TCP bağlantı noktasını kapsayıcıda bir TCP bağlantı noktasıyla (ikinci değer) eşleyin. Bu örnekte, Azure SQL Edge, kapsayıcıda TCP 1433 ' i dinliyor ve bu bağlantı noktası, ana bilgisayarda 1433 ' de kullanıma sunuldu. |
    | **--adı azuresqledge** | Rastgele oluşturulmuş bir tane yerine kapsayıcı için özel bir ad belirtin. Birden fazla kapsayıcı çalıştırırsanız, bu adı yeniden kullanamazsınız. |
    | **-d** | Kapsayıcıyı arka planda çalıştırma (daemon) |

    Tüm Azure SQL Edge ortam değişkeninin tüm listesi için bkz. [ortam değişkenleriyle Azure SQL Edge 'ı yapılandırma](configure.md#configure-by-using-environment-variables). Ayrıca, Azure SQL Edge kapsayıcılarını yapılandırmak için bir [MSSQL. conf dosyası](configure.md#configure-by-using-an-mssqlconf-file) kullanabilirsiniz.

3. Docker Kapsayıcılarınızı görüntülemek için `docker ps` komutunu kullanın.
   
   ```bash
    sudo docker ps -a
   ```

4. **Durum** sütununda bir durum **varsa, Azure** SQL Edge kapsayıcıda çalışır ve **bağlantı noktaları** sütununda belirtilen bağlantı noktasını dinler. Azure SQL Edge Kapsayıcınızın **durum** sütununda **çıkıldı** görünüyorsa, Azure SQL Edge belgelerinin sorun giderme bölümüne bakın.

    `-h`(Ana bilgisayar adı) parametresi de kullanışlıdır, ancak kolaylık olması için bu öğreticide kullanılmaz. Bu, kapsayıcının iç adını özel bir değer olarak değiştirir. Bu, aşağıdaki Transact-SQL sorgusunda döndürülen addır:

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    `-h` `--name` Aynı değere ayarlanması, hedef kapsayıcıyı kolayca belirlemek için iyi bir yoldur.

5. Son bir adım olarak, `SA_PASSWORD` Çıkış içinde göründüğünden `ps -eax` ve aynı ada sahip ortam değişkeninde DEPOLANDıĞıNDAN, sa parolanızı değiştirin. Aşağıdaki adımlara bakın.

## <a name="change-the-sa-password"></a> SA parolasını değiştirme

**Sa** hesabı, kurulum sırasında oluşturulan Azure SQL Edge örneğinde bir sistem yöneticisidir. Azure SQL Edge kapsayıcınızı oluşturduktan sonra, `MSSQL_SA_PASSWORD` belirttiğiniz ortam değişkeni kapsayıcıda çalıştırılarak bulunabilir `echo $SA_PASSWORD` . Güvenlik nedeniyle, SA parolanızı değiştirin.

1. SA kullanıcısı için kullanılacak güçlü bir parola seçin.

2. `docker exec`Transact-SQL kullanarak parolayı değiştirmek için **sqlcmd** 'yi çalıştırmak için kullanın. Aşağıdaki örnekte, eski parolayı `<YourStrong!Passw0rd>` ve yeni parolayı, `<YourNewStrong!Passw0rd>` kendi parola değerlerinizle değiştirin.

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>Azure SQL Edge 'e bağlanma

Aşağıdaki adımlar, Azure SQL Edge 'e bağlanmak için kapsayıcının içindeki **sqlcmd** Azure SQL Edge komut satırı aracını kullanır.

> [!NOTE]
> sqlcmd Aracı, SQL Edge kapsayıcıları 'nın ARM64 sürümünde kullanılamaz.

1. `docker exec -it`Çalışan kapsayıcının içinde etkileşimli bir bash kabuğu başlatmak için komutunu kullanın. Aşağıdaki örnekte, `azuresqledge` kapsayıcıyı oluştururken parametresi tarafından belirtilen addır `--name` .

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. Kapsayıcının içindeyken sqlcmd ile yerel olarak bağlanın. Sqlcmd, varsayılan olarak yolda değildir, bu nedenle tam yolu belirtmeniz gerekir.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > Komut satırında parolayı, girmeniz istenecek şekilde atlayabilirsiniz.

3. Başarılı olursa bir **sqlcmd** komut istemine almalısınız: `1>` .

## <a name="create-and-query-data"></a>Veri oluşturma ve sorgulama

Aşağıdaki bölümler, yeni bir veritabanı oluşturmak, veri eklemek ve basit bir sorgu çalıştırmak için **sqlcmd** ve Transact-SQL ' i kullanma konusunda size rehberlik sağlar.

### <a name="create-a-new-database"></a>Yeni veritabanı oluşturma

Aşağıdaki adımlar adlı yeni bir veritabanı oluşturur `TestDB` .

1. **Sqlcmd** komut isteminde, bir test veritabanı oluşturmak Için aşağıdaki Transact-SQL komutunu yapıştırın:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. Bir sonraki satırda, sunucunuzdaki tüm veritabanlarının adını döndürmek için bir sorgu yazın:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Veri ekleme

Sonra yeni bir tablo oluşturun `Inventory` ve iki yeni satır ekleyin.

1. **Sqlcmd** komut isteminde bağlamı yeni `TestDB` veritabanına geçir:

   ```sql
   USE TestDB
   ```

2. Adlı yeni tablo oluştur `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Yeni tabloya veri ekle:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. `GO`Önceki komutları yürütmek için yazın:

   ```sql
   GO
   ```

### <a name="select-data"></a>Verileri seçme

Şimdi tablodaki verileri döndürmek için bir sorgu çalıştırın `Inventory` .

1. **Sqlcmd** komut isteminde, `Inventory` miktardan 152 ' den büyük olan tablodaki satırları döndüren bir sorgu girin:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Komutu yürütün:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Sqlcmd komut isteminden çıkın

1. **Sqlcmd** oturumunuzu sonlandırmak için şunu yazın `QUIT` :

   ```sql
   QUIT
   ```

2. Kapsayıcıda etkileşimli komut isteminden çıkmak için yazın `exit` . Etkileşimli bash kabuğu 'ndan çıktıktan sonra Kapsayıcınız çalışmaya devam eder.

## <a name="connect-from-outside-the-container"></a>Kapsayıcının dışından Bağlan

Ayrıca, SQL bağlantılarını destekleyen herhangi bir harici Linux, Windows veya macOS aracından Docker makinenizde Azure SQL Edge örneğine bağlanabilirsiniz. Dışından bir SQL Edge kapsayıcısına bağlanma hakkında daha fazla bilgi için bkz. [Azure SQL Edge 'e bağlanma ve sorgulama](connect.md).

## <a name="remove-your-container"></a>Kapsayıcınızı kaldırma

Bu öğreticide kullanılan Azure SQL Edge kapsayıcısını kaldırmak istiyorsanız aşağıdaki komutları çalıştırın:

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> Kapsayıcıyı durdurma ve kaldırma, kapsayıcıdaki tüm Azure SQL Edge verilerini kalıcı olarak siler. Verilerinizi korumanız gerekiyorsa, [bir yedekleme dosyasını kapsayıcı dışına oluşturun ve kopyalayın](backup-restore.md) ya da [kapsayıcı veri kalıcılığı tekniğini](configure.md#persist-your-data)kullanın.

## <a name="next-steps"></a>Sonraki Adımlar

- [SQL Edge 'de ONNX ile Machine Learning ve yapay zeka](onnx-overview.md).
- [IoT Edge kullanarak SQL Edge ile uçtan uca IoT çözümü oluşturma](tutorial-deploy-azure-resources.md).
- [Azure SQL Edge 'de veri akışı](stream-data.md)