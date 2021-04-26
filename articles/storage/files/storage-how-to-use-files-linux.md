---
title: Linux ile Azure dosyaları kullanma | Microsoft Docs
description: Linux üzerinde bir Azure dosya paylaşımının SMB üzerinden nasıl bağlanacağını öğrenin. Önkoşullar listesine bakın. Linux istemcilerinde SMB güvenlik konularını gözden geçirin.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4ace5620bf98b06956c294a12b6b08881422e718
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952346"
---
# <a name="use-azure-files-with-linux"></a>Azure Dosyaları'nı Linux ile kullanma
[Azure Dosyaları](storage-files-introduction.md), Windows'un kolay kullanılan bulut dosya sistemidir. Azure dosya paylaşımları, [SMB çekirdek istemcisi](https://wiki.samba.org/index.php/LinuxCIFS)kullanılarak Linux dağıtımları ile bağlanabilir. Bu makalede bir Azure dosya paylaşımının bağlanması için iki yol gösterilmektedir: `mount` ' de bir giriş oluşturarak, komut ve önyükleme ile isteğe bağlı `/etc/fstab` .

Linux 'ta bir Azure dosya paylaşımının bağlanması için önerilen yol, SMB 3,0 ' i kullanmaktır. Azure dosyaları varsayılan olarak, yalnızca SMB 3,0 tarafından desteklenen aktarım için şifreleme gerektirir. Azure dosyaları, geçiş sırasında şifrelemeyi desteklemeyen SMB 2,1 ' ı da destekler, ancak Azure dosya paylaşımlarını başka bir Azure bölgesinden veya şirket içi güvenlik nedenleriyle SMB 2,1 ile birlikte bağlayamayabilir. Uygulamanız özellikle SMB 2,1 ' i gerektirmediği için, en popüler, son yayınlanan Linux dağıtımları SMB 3,0 ' yi destekleyen en popüler, bunu kullanmanın küçük bir nedeni vardır:  

| Linux dağıtımı | SMB 2.1 <br>(Aynı Azure bölgesindeki VM 'lerde takar) | SMB 3.0 <br>(Şirket içi ve çapraz bölge 'den takar) |
| --- | :---: | :---: |
| Ubuntu | 14.04 + | 16.04 + |
| Red Hat Enterprise Linux (RHEL) | 7 + | 7,5 + |
| CentOS | 7 + |  7,5 + |
| Debian | 8 + | 10+ |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12 | 12 SP2 + |

Yukarıdaki tabloda listelenmeyen bir Linux dağıtımını kullanıyorsanız Linux dağıtımının Linux çekirdek sürümünü denetleyerek şifreleme ile SMB 3,0 ' i destekleyip desteklemediğini kontrol edebilirsiniz. Şifreleme ile SMB 3,0, Linux çekirdek sürümü 4,11 ' e eklenmiştir. `uname`Bu komut, kullanımdaki Linux çekirdeğinin sürümünü döndürür:

```bash
uname -r
```

## <a name="prerequisites"></a>Önkoşullar
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**CIFS-utils paketinin yüklü olduğundan emin olun.**  
    CIFS-utils paketi, seçtiğiniz Linux dağıtımında Paket Yöneticisi kullanılarak yüklenebilir. 

    **Ubuntu** ve **detem tabanlı** dağıtımlar üzerinde, `apt` paket yöneticisini kullanın:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    **Fedora**, **Red Hat Enterprise Linux 8 +** ve **CentOS 8 +**' da `dnf` paket yöneticisini kullanın:

    ```bash
    sudo dnf install cifs-utils
    ```

    **Red Hat Enterprise Linux** ve **CentOS**'ın eski sürümlerinde Paket Yöneticisi ' ni kullanın `yum` :

    ```bash
    sudo yum install cifs-utils 
    ```

    **OpenSUSE**'de `zypper` paket yöneticisini kullanın:

    ```bash
    sudo zypper install cifs-utils
    ```

    Diğer dağıtımlarda, uygun paket yöneticisini veya [kaynaktan derlemeyi](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download) kullanın

* **Azure komut satırı arabirimi 'nin (CLı) en son sürümü.** Azure CLı 'nın nasıl yükleneceği hakkında daha fazla bilgi için bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli) ve işletim sisteminizi seçmek. PowerShell 6 + ' da Azure PowerShell modülünü kullanmayı tercih ederseniz, Azure CLı için aşağıdaki yönergeler sunulmaktadır.

* **Bağlantı noktası 445 ' ün açık olduğundan emin olun**: SMB, TCP bağlantı noktası 445 üzerinden iletişim kurar. güvenlik duvarınızın istemci MAKINESINDEN gelen TCP 445 bağlantı noktalarını engelleyip engellemediğini denetleyin.  `<your-resource-group>`Öğesini değiştirin ve `<your-storage-account>` sonra aşağıdaki betiği çalıştırın:
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Bağlantı başarılı olduysa aşağıdaki çıktıya benzer bir şey görmeniz gerekir:

    ```ouput
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Şirket ağınızda 445 numaralı bağlantı noktasını açabilmeniz veya bir ISS tarafından bu işlemi gerçekleştirmek engelleniyorsa, bağlantı noktası 445 ' yi çözmek için bir VPN bağlantısı veya ExpressRoute kullanabilirsiniz. Daha fazla bilgi için bkz. [doğrudan Azure dosya paylaşma erişimi Için ağ değerlendirmeleri](storage-files-networking-overview.md).

## <a name="mounting-azure-file-share"></a>Azure dosya paylaşımından bağlama
Linux dağıtımına sahip bir Azure dosya paylaşımının kullanılması için Azure dosya paylaşımının bağlama noktası olarak kullanılacak bir dizin oluşturmanız gerekir. Bir bağlama noktası, Linux sisteminizde herhangi bir yerde oluşturulabilir, ancak bunu/Mount altında oluşturmak yaygın bir kuraldır. Bağlama noktası oluşturulduktan sonra, `mount` Azure dosya paylaşımında erişim için komutunu kullanırsınız.

İsterseniz aynı Azure dosya paylaşımının birden çok bağlama noktasına bağlayabilirsiniz.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Azure dosya paylaşımından isteğe bağlı olarak bağlama `mount`
1. **Bağlama noktası için bir klasör oluşturun**: `<your-resource-group>` , `<your-storage-account>` , ve ile `<your-file-share>` ortamınıza uygun bilgileri değiştirin:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mount/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Azure dosya paylaşımından bağlama için Mount komutunu kullanın**. Aşağıdaki örnekte, yerel Linux dosyası ve klasörü izinleri varsayılan 0755 olur. Bu, sahip için okuma, yazma ve yürütme (dosya/dizin Linux sahibine göre), sahip grubundaki kullanıcılar için okuma ve yürütme ve sistemdeki diğerleri için okuma ve yürütme anlamına gelir. `uid` `gid` Bağlama IÇIN Kullanıcı kimliğini ve grup kimliğini ayarlamak için ve bağlama seçeneklerini kullanabilirsiniz. Ayrıca, `dir_mode` `file_mode` istediğiniz gibi özel izinleri ayarlamak için ve kullanabilirsiniz. İzinlerin nasıl ayarlanacağı hakkında daha fazla bilgi için bkz. Vikipde [UNIX sayısal gösterimi](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) . 

    ```bash
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > Yukarıdaki mount komutu SMB 3,0 ile takar. Linux dağıtım, şifreleme ile SMB 3,0 ' i desteklemiyorsa veya yalnızca SMB 2,1 ' i destekliyorsa, yalnızca depolama hesabıyla aynı bölgedeki bir Azure VM 'sinden bağlayabilirsiniz. Azure dosya paylaşımınızı şifreleme ile SMB 3,0 desteği olmayan bir Linux dağıtımına bağlamak için, [depolama hesabı için iletim sırasında şifrelemeyi devre dışı bırakmanız](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)gerekir.

Azure dosya paylaşımını kullanarak işiniz bittiğinde, `sudo umount $mntPath` paylaşımını çıkarmak için kullanabilirsiniz.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>İle Azure dosya paylaşımının kalıcı bir bağlama noktası oluşturun `/etc/fstab`
1. **Bağlama noktası için bir klasör oluşturun**: bir bağlama noktası klasörü dosya sisteminde herhangi bir yerde oluşturulabilir, ancak bunu/Mount altında oluşturmak yaygın bir kuraldır. Örneğin, aşağıdaki komut yeni bir dizin oluşturur,, ve ile `<your-resource-group>` `<your-storage-account>` `<your-file-share>` ortamınıza uygun bilgileri alır:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mount/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Dosya paylaşımının Kullanıcı adını (depolama hesabı adı) ve parolasını (depolama hesabı anahtarı) depolamak için bir kimlik bilgisi dosyası oluşturun.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Kimlik bilgisi dosyasındaki izinleri değiştirerek yalnızca kök parola dosyasını okuyabilir veya değiştirebilir.** Depolama hesabı anahtarı temel olarak depolama hesabı için bir süper yönetici parolası olduğundan, dosya üzerindeki izinleri yalnızca kök erişim gibi ayarlayarak, daha düşük ayrıcalıklı kullanıcıların depolama hesabı anahtarını alamamasını sağlayabilirsiniz.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. Aşağıdaki **satırı ' ye `/etc/fstab` eklemek için aşağıdaki komutu kullanın**: aşağıdaki örnekte, yerel Linux dosyası ve klasörü izinleri varsayılan 0755 olur. Bu, sahip için okuma, yazma ve yürütme (dosya/dizin Linux sahibine göre), sahip grubundaki kullanıcılar için okuma ve yürütme ve sistemdeki diğerleri için okuma ve yürütme anlamına gelir. `uid` `gid` Bağlama IÇIN Kullanıcı kimliğini ve grup kimliğini ayarlamak için ve bağlama seçeneklerini kullanabilirsiniz. Ayrıca, `dir_mode` `file_mode` istediğiniz gibi özel izinleri ayarlamak için ve kullanabilirsiniz. İzinlerin nasıl ayarlanacağı hakkında daha fazla bilgi için bkz. Vikipde [UNIX sayısal gösterimi](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) .

    ```bash
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > Yukarıdaki mount komutu SMB 3,0 ile takar. Linux dağıtım, şifreleme ile SMB 3,0 ' i desteklemiyorsa veya yalnızca SMB 2,1 ' i destekliyorsa, yalnızca depolama hesabıyla aynı bölgedeki bir Azure VM 'sinden bağlayabilirsiniz. Azure dosya paylaşımınızı şifreleme ile SMB 3,0 desteği olmayan bir Linux dağıtımına bağlamak için, [depolama hesabı için iletim sırasında şifrelemeyi devre dışı bırakmanız](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)gerekir.

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>Otomatik olarak Azure dosya paylaşımından bağlamak için otomatik FS kullanma

1. **Oto FS paketinin yüklü olduğundan emin olun.**  

    Bu paket, seçtiğiniz Linux dağıtımında Paket Yöneticisi kullanılarak yüklenebilir. 

    **Ubuntu** ve **detem tabanlı** dağıtımlar üzerinde, `apt` paket yöneticisini kullanın:
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    **Fedora**, **Red Hat Enterprise Linux 8 +** ve **CentOS 8 +**' da `dnf` paket yöneticisini kullanın:
    ```bash
    sudo dnf install autofs
    ```
    **Red Hat Enterprise Linux** ve **CentOS**'ın eski sürümlerinde Paket Yöneticisi ' ni kullanın `yum` :
    ```bash
    sudo yum install autofs 
    ```
    **OpenSUSE**'de `zypper` paket yöneticisini kullanın:
    ```bash
    sudo zypper install autofs
    ```
2. **Paylaşımlar için bağlama noktası oluştur**:
   ```bash
    sudo mkdir /fileshares
    ```
3. **Yeni bir özel oto Crete yapılandırma dosyası**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **/Etc/oto. fileshares öğesine aşağıdaki girişleri ekleyin**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **/Etc/oto ve Master 'a aşağıdaki girişi ekleyin**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **Oto yeniden Başlat**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **Paylaşımda belirtilen klasöre erişin**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>Linux güvenliğini sağlama
Linux üzerinde bir Azure dosya paylaşımının bağlanması için bağlantı noktası 445 ' e erişilebilir olmalıdır. Çoğu kuruluş SMB 1 kaynaklı güvenlik riskleri nedeniyle 445 numaralı bağlantı noktasını engeller. CIFS (ortak Internet dosya sistemi) olarak da bilinen SMB 1, birçok Linux dağılımlarına dahil edilmiş eski bir dosya sistemi protokolüdür. SMB 1 eski, verimsiz ve hepsinden önemlisi güvenli olmayan bir protokoldür. Azure dosyalarının SMB 1 ' i desteklememesi ve Linux çekirdek sürüm 4,18 ' den itibaren, Linux, SMB 1 ' i devre dışı bırakmayı olanaklı kılan bir habertir. Üretimde SMB dosya paylaşımlarını kullanmadan önce Linux istemcilerinizdeki SMB 1 ' i devre dışı bırakmayı [kesinlikle öneririz](https://aka.ms/stopusingsmb1) .

Linux Kernel 4,18 ' den itibaren, eski nedenler için çağrılan SMB çekirdek modülü, `cifs` adlı yeni bir modül parametresi (genellikle farklı dış belge tarafından *PARI* olarak adlandırılır) sunar `disable_legacy_dialects` . Linux Kernel 4,18 ' de tanıtılan halde, bazı satıcılar destekledikleri eski çekirdekler için bu değişikliği geri almaktadır. Kolaylık olması için aşağıdaki tabloda, bu modül parametresinin ortak Linux dağıtımları üzerinde kullanılabilirliği ayrıntılı olarak verilmiştir.

| Dağıtım | SMB 1 devre dışı bırakabilir |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | No |
| Ubuntu 18.04 | Yes |
| Ubuntu 19.04 + | Yes |
| De, 8-9 | No |
| De, 10 + | Yes |
| Fedora 29 + | Yes |
| CentOS 7 | No | 
| CentOS 8 + | Yes |
| Red Hat Enterprise Linux 6. x-7. x | No |
| Red Hat Enterprise Linux 8 + | Yes |
| openSUSE artık 15,0 | No |
| openSUSE artık 15.1 + | Yes |
| openSUSE Tpoed | Yes |
| SUSE Linux Enterprise 11. x-12. x | No |
| SUSE Linux Enterprise 15 | No |
| SUSE Linux Enterprise 15,1 | No |

Aşağıdaki komutla, Linux dağılımının modül parametresini destekleyip desteklemediğini kontrol edebilirsiniz `disable_legacy_dialects` .

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Bu komut aşağıdaki iletiyi içermelidir:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

SMB 1 ' i devre dışı bırakmadan önce, SMB modülünün sisteminizde yüklü olmadığından emin olmak için ' i denetlemeniz gerekir (bir SMB paylaşımından bağlandıysanız bu otomatik olarak gerçekleşir). Bu işlemi, SMB yüklenmediği takdirde hiçbir şey çıkışı gereken aşağıdaki komutla yapabilirsiniz:

```bash
lsmod | grep cifs
```

Modülü kaldırmak için ilk olarak tüm SMB paylaşımlarını çıkarın ( `umount` yukarıda açıklanan komutu kullanarak). Aşağıdaki komutla sisteminizdeki tüm bağlı SMB paylaşımlarını tanımlayabilirsiniz:

```bash
mount | grep cifs
```

Tüm SMB dosya paylaşımlarını kaldırdığınızda modülün kaldırılması güvenlidir. Bunu `modprobe` komutuyla yapabilirsiniz.

```bash
sudo modprobe -r cifs
```

Şu komutu kullanarak modülü, SMB 1 kaldırılmış şekilde el ile yükleyebilirsiniz `modprobe` :

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Son olarak, içindeki yüklenmiş parametrelere bakarak, parametresiyle SMB modülünün yüklenmiş olduğunu kontrol edebilirsiniz `/sys/module/cifs/parameters` :

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Ubuntu ve detem tabanlı dağıtımlardan SMB 1 ' i kalıcı olarak devre dışı bırakmak için, ayarıyla çağrılan yeni bir dosya oluşturmanız (diğer modüller için özel seçenekleriniz yoksa) oluşturmanız gerekir `/etc/modprobe.d/local.conf` . Bunu aşağıdaki komutla yapabilirsiniz:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

SMB modülünü yükleyerek bunun çalıştığını doğrulayabilirsiniz:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>Sonraki adımlar
Azure Dosyaları hakkında daha fazla bilgi edinmek için şu bağlantılara göz atın:

* [Azure Dosyalar dağıtımını planlama](storage-files-planning.md)
* [SSS](./storage-files-faq.md)
* [Sorun giderme](storage-troubleshoot-linux-file-connection-problems.md)