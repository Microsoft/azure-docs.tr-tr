---
title: Azure Data Box Disk’i paketinden çıkarma, bağlanma, kilidini açma öğreticisi| Microsoft Docs
description: Azure Data Box Disk'inizi nasıl ayarlayabileceğinizi öğrenmek için bu öğreticiyi kullanın
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: alkohli
ms.localizationpriority: high
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 91baec55cb976a3bc64ac60b1759c80e64fd18a3
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375893"
---
::: zone target="docs"

# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Öğretici: Azure Data Box Disk'i paketinden çıkarma, bağlama ve kilidini açma

Bu öğreticide, Azure Data Box Disk'inizi paketinden çıkarma, bağlama ve kilidini açma işlemleri açıklanır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Data Box Disk'inizi paketinden çıkarma
> * Disklere bağlanma ve destek anahtarı alma
> * Windows istemcide disklerin kilidini açma
> * Linux istemcide disklerin kilidini açma

::: zone-end

::: zone target="chromeless"

## <a name="unpack-connect-and-unlock-azure-data-box-disk"></a>Azure Data Box Disk'i paketinden çıkarma, bağlama ve kilidini açma

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. [Öğretici: Azure Data Box Disk sipariş etme](data-box-disk-deploy-ordered.md) bölümüne gidin.
2. Disklerinizi aldınız ve portaldaki iş durumu **Teslim Edildi** olarak güncelleştirildi.
3. Data Box Disk kilidi açma aracını yükleyebileceğiniz bir istemci bilgisayarınız var. İstemci bilgisayarınızda:
    - [Desteklenen bir işletim sistemi](data-box-disk-system-requirements.md#supported-operating-systems-for-clients) çalıştırılmalıdır.
    - Windows istemciyse diğer [gerekli yazılımlar](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) yüklenmiş olmalıdır.  

## <a name="unpack-your-disks"></a>Disklerinizi paketinden çıkarma

 Disklerinizin paketinden çıkarmak için aşağıdaki adımları gerçekleştirin.

1. Data Box Disk'ler küçük bir sevkiyat kutusunda postalanır. Kutuyu açın ve içindekileri çıkarın. Paketi kontrol edip içinde 1 ile 5 arası katı hal sürücüsü (SSD) ve disk başına bir USB bağlantı kablosu bulunduğundan emin olun. Üzerinde oynanıp oynanmadığını veya başka herhangi bir hasarı olup olmadığını anlamak için kutuyu inceleyin. 

    ![Data Box Disk sevkiyat paketi](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Sevkiyat kutusuyla oynanmışsa veya üzerinde ciddi bir hasar varsa, kutuyu açmayın. Disklerin düzgün çalışır durumda olup olmadığını ve bunların yerine başkalarının gönderilmesinin gerekip gerekmediğini değerlendirmenize yardımcı olmaları için Microsoft Desteği'ne başvurun.
3. Kutuda, iade gönderimi için sevkiyat etiketini (geçerli etiketin altında) içeren bir şeffaf kılıf bulunduğunu doğrulayın. Bu etiket kayıpsa veya hasar görmüşse, istediğiniz zaman Azure portaldan yenisini indirebilir ve yazdırabilirsiniz. 

    ![Data Box Disk sevkiyat etiketi](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Disklerin iade gönderimi için kutuyu ve ambalaj köpüklerini saklayın.

## <a name="connect-to-disks-and-get-the-passkey"></a>Disklere bağlanma ve destek anahtarı alma 

1. Diski önkoşullarda belirtildiği gibi desteklenen bir işletim sisteminin çalıştırıldığı istemci bilgisayara bağlamak için, verilen kabloyu kullanın. 

    ![Data Box Disk bağlantısı](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Azure portalda **Genel > Cihaz ayrıntıları**'na gidin. Destek anahtarını kopyalamak için kopyala simgesini kullanın. Disklerin kilidini açmak için bu destek anahtarı kullanılacaktır.

    ![Data Box Disk kilidi açma destek anahtarı](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png) 

Bir Windows veya Linux istemciye bağlı olup olmadığınıza bağlı olarak, disklerin kilidini açma adımları farklıdır.

## <a name="unlock-disks-on-windows-client"></a>Windows istemcide disklerin kilidini açma

Disklerinizi bağlamak ve kilitlerini açmak için aşağıdaki adımları gerçekleştirin.
     
1. Azure portalda **Genel > Cihaz ayrıntıları**'na gidin. 
2. Windows istemciye karşılık gelen Data Box Disk araç takımını indirin. Bu araç takımında 3 araç bulunur: Data Box Disk Unlock aracı, Data Box Disk Validation aracı ve Data Box Disk Split Copy aracı. 

    Bu yordamda yalnızca Data Box Disk kilit açma aracını kullanacaksınız. Diğer iki araç sonraki bölümlerde kullanılacaktır.

    > [!div class="nextstepaction"]
    > [Windows için Data Box Disk araç takımını indirin](https://aka.ms/databoxdisktoolswin)         

3. Aracı takımını, verileri kopyalamak için kullanacağınız bilgisayarda ayıklayın. 
4. Aynı bilgisayarda bir Komut İstemi penceresi açın veya yönetici olarak Windows PowerShell'i çalıştırın.
5. (İsteğe bağlı) Diskin kilidini açarken kullandığınız bilgisayarın işletim sistemi gereksinimlerini karşıladığını doğrulamak için, system check komutunu çalıştırın. Örnek çıktı aşağıda gösterilmiştir. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. `DataBoxDiskUnlock.exe` dosyasını çalıştırın ve [Disklere bağlanma ve destek anahtarı alma](#connect-to-disks-and-get-the-passkey) bölümünde edindiğiniz destek anahtarını girin. Diske atanan sürücü harfi görüntülenir. Örnek çıktı aşağıda gösterilmiştir.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. Tüm gelecek disk yeniden eklemeleri için kilidi açma adımlarını yineleyin. Data Box Disk kilidi açma aracıyla ilgili yardıma ihtiyacınız olursa `help` komutunu kullanın.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>
    
    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
8. Diskin kilidi açıldıktan sonra, disk içeriğini görüntüleyebilirsiniz.    

    ![Data Box Disk içeriği](media/data-box-disk-deploy-set-up/data-box-disk-content.png)

Disklerin kilidini açarken sorun yaşarsanız [kilit açma sorunlarını giderme](data-box-disk-troubleshoot-unlock.md) bölümüne bakın. 

## <a name="unlock-disks-on-linux-client"></a>Linux istemcide disklerin kilidini açma

1. Azure portalda **Genel > Cihaz ayrıntıları**'na gidin. 
2. Linux istemciye karşılık gelen Data Box Disk araç takımını indirin.  

    > [!div class="nextstepaction"]
    > [Linux için Data Box Disk araç takımını indirin](https://aka.ms/databoxdisktoolslinux) 

3. Linux istemcinizde bir terminal açın. Yazılımı indirdiğiniz klasöre gidin. Bu dosyaları yürütebilmeniz için dosya izinlerini değiştirin. Aşağıdaki komutu yazın: 

    `chmod +x DataBoxDiskUnlock_x86_64` 
    
    `chmod +x DataBoxDiskUnlock_Prep.sh` 
 
    Örnek çıktı aşağıda gösterilmiştir. Chmod komutu çalıştırıldıktan sonra, `ls` komutunu çalıştırarak dosya izinlerinin değiştirildiğini doğrulayabilirsiniz. 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```
4. Data Box Disk Kilidi Açma yazılımı için gereken tüm ikili dosyaları yükleyecek şekilde betiği yürütün. Komutu kök olarak çalıştırmak için `sudo` öğesini kullanın. İkili dosyalar başarıyla yüklendikten sonra terminal üzerinde bu etkiye yönelik bir not görürsünüz.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    Betik ilk olarak istemci bilgisayarınızın desteklenen bir işletim sistemini çalıştırıp çalıştırmadığını denetler. Örnek çıktı aşağıda gösterilmiştir. 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 
    
        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```
    
 
5. Yüklemeye devam etmek için `y` yazın. Betiğin yüklediği paketler şunlardır: 
   - **epel-release** - Aşağıdaki üç paketi içeren depo. 
   - **dislocker ve fuse-dislocker** - Bu yardımcı programlar, BitLocker şifreli disklerin şifresinin çözülmesine yardımcı olabilir. 
   - **ntfs-3g** - NTFS birimlerinin takılmasına yardımcı olan paket. 
 
     Paketler başarıyla yüklendikten sonra terminal, bu etkiye yönelik bir bildirim görüntüler.     
     ```
     Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
     ruby-libs.x86 64 0:1.8.7.374-5.el6 
     Complete! 
     Loaded plugins: fastestmirror, refresh-packagekit, security 
     Setting up Remove Process 
     Resolving Dependencies 
     --> Running transaction check 
     ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
     Dependencies Resolved 
     Package        Architecture        Version        Repository        Size 
     Removing:  epel-release        noarch         6-8        @extras        22 k 
     Transaction Summary                                 
     Remove        1 Package(s) 
     Installed size: 22 k 
     Downloading Packages: 
     Running rpmcheckdebug 
     Running Transaction Test 
     Transaction Test Succeeded 
     Running Transaction 
     Erasing : epel-release-6-8.noarch 
     Verifying : epel-release-6-8.noarch 
     Removed: 
     epel-release.noarch 0:6-8 
     Complete! 
     Dislocker is installed by the script. 
     OpenSSL is already installed.
     ```

6. Data Box Disk Kilidi Açma aracını çalıştırın. [Disklere bağlanma ve destek anahtarı alma](#connect-to-disks-and-get-the-passkey) bölümünde edindiğiniz Azure portaldan destek anahtarını girin. İsteğe bağlı olarak, kilidi açılacak BitLocker şifreli birimlerin listesini belirtin. Destek anahtarı ve birim listesi tek tırnak içinde belirtilmelidir. 

    Aşağıdaki komutu yazın.
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’          

    Aşağıda örnek çıktı gösterilmektedir. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:’qwerqwerqwer’  
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    Verilerinizi kopyalayabildiğiniz birim için bağlama noktası görüntülenir.

7. Tüm gelecek disk yeniden eklemeleri için kilidi açma adımlarını yineleyin. Data Box Disk kilidi açma aracıyla ilgili yardıma ihtiyacınız olursa `help` komutunu kullanın. 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    Aşağıda örnek çıktı gösterilmektedir. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:’<passkey from Azure_portal>’ 
    
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ 
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ /Volumes:’/dev/sdbl’ 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. Diskin kilidi açıldıktan sonra, bağlama noktasına gidip diskin içeriklerini görüntüleyebilirsiniz. Şimdi *BlockBlob* veya *PageBlob* klasörlerine veri kopyalamaya hazırsınız demektir. 

    ![Data Box Disk içeriği](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)


Disklerin kilidini açarken sorun yaşarsanız [kilit açma sorunlarını giderme](data-box-disk-troubleshoot-unlock.md) bölümüne bakın. 

::: zone-end

::: zone target="chromeless"

1. Diskleri paketinden çıkarın ve diski istemci bilgisayara bağlamak için birlikte verilen kabloyu kullanın.
2. Data Box Disk araç takımını, verileri kopyalamak için kullanacağınız bilgisayara indirip ayıklayın.

    > [!div class="nextstepaction"]
    > [Windows için Data Box Disk araç takımını indirin](https://aka.ms/databoxdisktoolswin)

    or
    > [!div class="nextstepaction"]
    > [Linux için Data Box Disk araç takımını indirin](https://aka.ms/databoxdisktoolslinux) 

3. Bir Windows istemci üzerindeki disklerin kilidini açmak için aynı bilgisayarda bir Komut İstemi penceresi açın veya yönetici olarak Windows PowerShell'i çalıştırın:

    - Aşağıdaki komutu Data Box Disk Unlock aracının yüklü olduğu klasöre yazın.

        ``` 
        .\DataBoxDiskUnlock.exe
        ```
    -  Azure portalındaki **Genel > Cihaz ayrıntıları** menüsünden destek anahtarını alıp buraya girin. Diske atanan sürücü harfi görüntülenir. 
4. Linux istemcisindeki disklerin kilidini açmak için bir terminal açın. Yazılımı indirdiğiniz klasöre gidin. Bu dosyaları yürütebilmeniz için dosya izinlerini değiştirmek aşağıdaki komutları yazın: 

    ```
    chmod +x DataBoxDiskUnlock_x86_64
    chmod +x DataBoxDiskUnlock_Prep.sh
    ``` 
    Tüm gerekli ikili dosyaları yüklemek için betiği yürütün.

    ```
    sudo ./DataBoxDiskUnlock_Prep.sh
    ```
    Data Box Disk Kilidi Açma aracını çalıştırın. Azure portalındaki **Genel > Cihaz ayrıntıları** menüsünden destek anahtarını alıp buraya girin. İsteğe bağlı olarak, kilidi açılacak BitLocker şifreli birimlerin listesini tek tırnak içinde belirtin.

    ```
    sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’
    ```      
5. Tüm gelecek disk yeniden eklemeleri için kilidi açma adımlarını yineleyin. Data Box Disk kilidini açma aracıyla ilgili yardıma ihtiyacınız olursa, help komutunu kullanın.

Diskin kilidi açıldıktan sonra, disk içeriğini görüntüleyebilirsiniz.

Diskleri ayarlama ve kilidini açma hakkında daha fazla bilgi için [Data Box Disk’i Ayarlama](data-box-disk-deploy-set-up.md) bölümüne gidin.

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki Azure Data Box Disk konularını öğrendiniz:

> [!div class="checklist"]
> * Data Box Disk'inizi paketinden çıkarma
> * Disklere bağlanma ve destek anahtarı alma
> * Windows istemcide disklerin kilidini açma
> * Linux istemcide disklerin kilidini açma


Data Box Disk'inize verileri kopyalama hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Data Box Disk'inize verileri kopyalama](./data-box-disk-deploy-copy-data.md)

::: zone-end

