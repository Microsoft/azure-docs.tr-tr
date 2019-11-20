---
title: Azure 'da Windows VM 'ye MongoDB 'yi yüklemeyin
description: Kaynak Yöneticisi dağıtım modeliyle oluşturulan Windows Server 2012 R2 çalıştıran bir Azure VM 'sine MongoDB yüklemeyi öğrenin.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 37c1b58d364e7eadb33803ce7eac1f2b956ec1b6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038545"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Azure 'da Windows VM 'ye MongoDB 'yi yükleyip yapılandırma
[MongoDB](https://www.mongodb.org) , popüler bir açık kaynaklı ve yüksek performanslı bir NoSQL veritabanıdır. Bu makale, Azure 'daki bir Windows Server 2016 sanal makinesine (VM) MongoDB Yükleme ve yapılandırma konusunda size rehberlik eder. Ayrıca, [Azure 'daki bir LINUX sanal makinesine MongoDB yükleyebilirsiniz](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Önkoşullar
MongoDB yüklemeden ve yapılandırmadan önce, bir VM oluşturmanız ve buna ideal olarak bir veri diski eklemeniz gerekir. Bir VM oluşturmak ve bir veri diski eklemek için aşağıdaki makalelere bakın:

* [Azure Portal](quick-create-portal.md) veya [Azure PowerShell](quick-create-powershell.md)kullanarak bir Windows Server sanal makinesi oluşturun.
* [Azure Portal](attach-managed-disk-portal.md) veya [Azure PowerShell](attach-disk-ps.md)kullanarak bir Windows Server VM 'sine veri diski ekleyin.

MongoDB 'yi yüklemeye ve yapılandırmaya başlamak için, Uzak Masaüstü kullanarak [Windows Server sanal makinenizde oturum açın](connect-logon.md) .

## <a name="install-mongodb"></a>MongoDB'yi yükleme
> [!IMPORTANT]
> Kimlik doğrulaması ve IP adresi bağlama gibi MongoDB güvenlik özellikleri varsayılan olarak etkinleştirilmemiştir. MongoDB bir üretim ortamına dağıtılmadan önce güvenlik özellikleri etkinleştirilmelidir. Daha fazla bilgi için bkz. [MongoDB güvenliği ve kimlik doğrulaması](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Sanal makinenize Uzak Masaüstü kullanarak bağlandıktan sonra, görev çubuğundan Internet Explorer 'ı açın.
2. Internet Explorer ilk açıldığında **Önerilen güvenlik, gizlilik ve uyumluluk ayarlarını kullan** ' ı seçin ve **Tamam**' a tıklayın.
3. Internet Explorer Artırılmış güvenlik yapılandırması varsayılan olarak etkinleştirilmiştir. MongoDB Web sitesini izin verilen siteler listesine ekleyin:
   
   * Sağ üst köşedeki **Araçlar** simgesini seçin.
   * **Internet seçenekleri**' nde **güvenlik** sekmesini seçin ve ardından **Güvenilen siteler** simgesini seçin.
   * **Siteler** düğmesine tıklayın. Güvenilen siteler listesine *https://\*. MongoDB.com* ekleyin ve iletişim kutusunu kapatın.
     
     ![Internet Explorer güvenlik ayarlarını yapılandırma](./media/install-mongodb/configure-internet-explorer-security.png)
4. [MongoDB-downloads](https://www.mongodb.com/downloads) sayfasına gidin (https://www.mongodb.com/downloads).
5. Gerekirse, **Community Server** sürümünü seçin ve ardından*Windows Server 2008 R2 64-bit ve üzeri*için en son geçerli kararlı yayını seçin. Yükleyiciyi indirmek için **İndir (MSI)** seçeneğine tıklayın.
   
    ![MongoDB yükleyicisini indir](./media/install-mongodb/download-mongodb.png)
   
    İndirme işlemi tamamlandıktan sonra yükleyiciyi çalıştırın.
6. Lisans sözleşmesini okuyun ve kabul edin. İstendiğinde, yüklemeyi **Tamam** ' ı seçin.
7. İsterseniz, MongoDB için bir grafik arabirim olan pusula 'i de yüklemeyi seçebilirsiniz.
8. Son ekranda, **yükler**' e tıklayın.

## <a name="configure-the-vm-and-mongodb"></a>VM ve MongoDB 'yi yapılandırma
1. Yol değişkenleri MongoDB yükleyicisi tarafından güncellenmez. Yol değişkeninizdeki MongoDB `bin` konumu olmadan, her MongoDB yürütülebilirini her kullandığınızda tam yolu belirtmeniz gerekir. Yol değişkeninizin konumunu eklemek için:
   
   * **Başlat** menüsüne sağ tıklayıp **sistem**' i seçin.
   * **Gelişmiş sistem ayarları**' na tıklayın ve ardından **ortam değişkenleri**' ne tıklayın.
   * **Sistem değişkenleri**altında **yol**' ı seçin ve ardından **Düzenle**' ye tıklayın.
     
     ![YOL değişkenlerini yapılandırma](./media/install-mongodb/configure-path-variables.png)
     
     Yolu MongoDB `bin` klasörünüze ekleyin. MongoDB genellikle *C:\Program Files\MongoDB*'ye yüklenir. VM 'nizin yükleme yolunu doğrulayın. Aşağıdaki örnek, varsayılan MongoDB yüklemesi konumunu `PATH` değişkenine ekler:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > `PATH` değişkenine bir konum eklediğinizden emin olmak için öndeki noktalı virgül (`;`) eklediğinizden emin olun.

2. Veri diskinizde MongoDB veri ve günlük dizinleri oluşturun. **Başlat** menüsünden **komut istemi**' ni seçin. Aşağıdaki örneklerde, F sürücüsündeki dizinler oluşturulur:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Aşağıdaki komutla bir MongoDB örneği başlatın ve veri ve günlük dizinlerinizin yolunu uygun şekilde ayarlayın:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    MongoDB 'nin günlük dosyalarını ayırması ve bağlantıları dinlemeye başlaması birkaç dakika sürebilir. Tüm günlük iletileri *F:\mongologs\mongolog.log* dosyasına yönlendirilir çünkü `mongod.exe` sunucusu başlar ve günlük dosyalarını ayırır.
   
   > [!NOTE]
   > MongoDB örneğiniz çalışırken komut istemi bu göreve odaklanmaya devam eder. MongoDB çalıştırmaya devam etmek için komut istemi penceresini açık bırakın. Veya, bir sonraki adımda açıklandığı gibi MongoDB hizmetini de Service olarak yükler.

4. Daha sağlam bir MongoDB deneyimi için `mongod.exe` hizmet olarak yükler. Hizmet oluşturmak, MongoDB 'yi her kullandığınızda bir komut istemi çalıştırmak zorunda olmadığınız anlamına gelir. Aşağıdaki gibi, veri ve günlük dizinlerinizin yolunu düzenleyerek hizmeti oluşturun:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Yukarıdaki komut, "Mongo DB" açıklamasıyla MongoDB adlı bir hizmet oluşturur. Aşağıdaki parametreler de belirtilmiştir:
   
   * `--dbpath` seçeneği, veri dizininin konumunu belirtir.
   * Çalışan hizmetin çıktıyı göstermek için bir komut penceresi olmadığından, bir günlük dosyası belirtmek için `--logpath` seçeneği kullanılmalıdır.
   * `--logappend` seçeneği, hizmetin yeniden başlatılmasının çıktının var olan günlük dosyasına eklenmesine neden olduğunu belirtir.
   
   MongoDB hizmetini başlatmak için aşağıdaki komutu çalıştırın:
   
    ```
    net start MongoDB
    ```
   
    MongoDB hizmetini oluşturma hakkında daha fazla bilgi için bkz. [MongoDB Için Windows hizmeti yapılandırma](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>MongoDB örneğini test etme
MongoDB 'nin tek bir örnek olarak çalıştığı veya hizmet olarak yüklendiği ile, artık veritabanlarınızı oluşturmaya ve kullanmaya başlayabilirsiniz. MongoDB Yönetim Kabuğu 'nu başlatmak için, **Başlat** menüsünden başka bir komut istemi penceresi açın ve aşağıdaki komutu girin:

```
mongo  
```

Veritabanlarını `db` komutuyla listeleyebilirsiniz. Bazı verileri aşağıdaki gibi ekleyin:

```
db.foo.insert( { a : 1 } )
```

Verileri aşağıdaki gibi arayın:

```
db.foo.find()
```

Çıktı aşağıdaki örneğe benzer:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

`mongo` konsolundan aşağıdaki gibi çıkın:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Güvenlik Duvarı ve ağ güvenlik grubu kurallarını yapılandırma
MongoDB artık yüklü ve çalışıyor olduğuna göre, MongoDB 'ye uzaktan bağlanabilmeniz için Windows Güvenlik Duvarı 'nda bir bağlantı noktası açın. TCP bağlantı noktası 27017 ' e izin veren yeni bir gelen kuralı oluşturmak için bir yönetim PowerShell istemi açın ve aşağıdaki komutu girin:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Ayrıca, **Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı** grafik yönetim aracı 'nı kullanarak kuralı oluşturabilirsiniz. TCP bağlantı noktası 27017 ' e izin vermek için yeni bir gelen kuralı oluşturun.

Gerekirse, mevcut Azure sanal ağ alt ağının dışından MongoDB erişimine izin vermek için bir ağ güvenlik grubu kuralı oluşturun. [Azure Portal](nsg-quickstart-portal.md) veya [Azure PowerShell](nsg-quickstart-powershell.md)kullanarak ağ güvenlik grubu kurallarını oluşturabilirsiniz. Windows güvenlik duvarı kurallarında olduğu gibi, MongoDB VM 'nizin sanal ağ arabirimine 27017 numaralı TCP bağlantı noktasına izin verin.

> [!NOTE]
> TCP bağlantı noktası 27017, MongoDB tarafından kullanılan varsayılan bağlantı noktasıdır. `mongod.exe` el ile veya bir hizmetten başlatırken `--port` parametresini kullanarak bu bağlantı noktasını değiştirebilirsiniz. Bağlantı noktasını değiştirirseniz, yukarıdaki adımlarda Windows Güvenlik Duvarı ve ağ güvenlik grubu kurallarını güncelleştirdiğinizden emin olun.


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Windows sanal makinenizde MongoDB 'yi yüklemeyi ve yapılandırmayı öğrendiniz. Artık [MongoDB belgelerindeki](https://docs.mongodb.com/manual/)gelişmiş konuları IZLEYEREK Windows sanal makinenizde MongoDB 'ye erişebilirsiniz.

