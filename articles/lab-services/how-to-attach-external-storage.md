---
title: Laboratuvar hizmetlerinde dış dosya depolamayı kullanma | Microsoft Docs
description: Laboratuvar hizmetlerinde dış dosya depolaması kullanan bir laboratuvarı ayarlamayı öğrenin.
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: 888e04db76567051f8c5eae7cf94c77e684cb146
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112027"
---
# <a name="using-external-file-storage-in-lab-services"></a>Laboratuvar hizmetlerinde dış dosya depolamayı kullanma

Bu makalede Azure Lab Services kullanılırken dış dosya depolaması için bazı seçenekler ele alınacaktır.  [Azure dosyaları](https://azure.microsoft.com/services/storage/files/) , BULUTTA, [SMB 2,1 ve SMB 3,0 aracılığıyla erişilebilen](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) tam olarak yönetilen dosya paylaşımları sunar.  Bir Azure dosya paylaşma, bir sanal ağ içinde herkese açık veya özel olarak bağlanabilir.  Ayrıca, dosya paylaşımıyla bağlantı için öğrencinin AD kimlik bilgilerini kullanacak şekilde yapılandırılabilir.  Linux makineler için NFS birimlerinde Azure NetApp Files kullanmak, Azure Lab Services olan dış dosya depolamaya yönelik başka bir seçenektir.  

## <a name="deciding-which-solution-to-use"></a>Hangi çözümün kullanılacağına karar verme

Her çözümün farklı gereksinimleri ve becerileri vardır.  Aşağıdaki tabloda her çözüm için göz önünde bulundurmanız gereken önemli noktaları listelenmektedir.  

|     Çözüm    |    Bilmemiz için önemli    |
| -------------- | ------------------------ |
| [Ortak uç nokta ile Azure dosyaları paylaşma](#azure-files-share) | <ul><li>Herkesin okuma/yazma erişimi vardır.</li><li>VNET eşlemesi gerekli değil.</li><li>Yalnızca laboratuvar VM 'Leri değil, tüm VM 'lere erişilebilir.</li><li>Linux kullanıyorsanız, öğrencilerin depolama hesabı anahtarına erişimi olur.</li></ul> |
| [Özel uç nokta ile Azure dosyaları paylaşma](#azure-files-share) | <ul><li>Herkesin okuma/yazma erişimi vardır.</li><li>VNET eşlemesi gerekli.</li><li>Depolama hesabı olarak yalnızca aynı ağdaki (veya eşlenmiş ağ) VM 'lere erişilebilir.</li><li>Linux kullanıyorsanız, öğrencilerin depolama hesabı anahtarına erişimi olur.</li></ul> |
| [Kimlik tabanlı yetkilendirme ile Azure dosyaları](#azure-files-with-identity-base-authorization) | <ul><li>Klasör veya dosya için okuma ya da okuma/yazma erişim izinleri ayarlanabilir.</li><li>VNET eşlemesi gerekli.</li><li>Depolama hesabının Active Directory bağlı olması gerekir.</li><li>Laboratuvar sanal makineleri etki alanına katılmış olmalıdır.</li><li>Depolama hesabı anahtarı, öğrenciler için dosya paylaşımıyla bağlantı kurmak üzere kullanılmaz.</li></ul> |
| [NFS birimlerine sahip NetApp dosyaları](#netapp-files-with-nfs-volumes) | <ul><li>Birimler için okuma veya okuma/yazma erişimi ayarlanabilir.</li><li>İzinler, öğrenci VM 'sinin IP adresi kullanılarak ayarlanır.</li><li>VNET eşlemesi gerekli.</li><li>NetApp dosyaları hizmetini kullanmak için kaydolmanız gerekebilir.</li><li>Yalnızca Linux.</li></ul>

Dış depolama kullanma maliyeti Azure Lab Services kullanmanın maliyetine dahil değildir.  Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure dosyaları fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/files/) ve [Azure NetApp Files fiyatlandırması](https://azure.microsoft.com/pricing/details/netapp/).

## <a name="azure-files-share"></a>Azure dosya paylaşma

Azure dosya paylaşımlarına ortak veya özel bir uç nokta kullanılarak erişilir.  Azure dosya paylaşımları, parola olarak depolama hesabı anahtarı kullanılarak bağlanır.  Bu yaklaşımla, herkesin dosya paylaşımında okuma/yazma erişimi vardır.

Azure dosya paylaşımında ortak bir uç nokta kullanılıyorsa, dikkat edilmesi önemlidir:

- Depolama hesabı için sanal ağ, laboratuvar hesabına eşlenmelidir.  Dosya paylaşma, şablon VM yayınlanmadan önce herhangi bir zamanda oluşturulabilir.
- Bir kullanıcının depolama hesabı anahtarı varsa, dosya paylaşımının herhangi bir makineden erişilebilir olmasını sağlayabilirsiniz.  
- Linux öğrencileri depolama hesabı anahtarını görebilir.  Azure dosya paylaşımının bağlanması için kimlik bilgileri, Linux sanal makinelerinde ' de depolanır `{file-share-name}.cred` ve sudo tarafından okunabilir.  Öğrenciler Azure laboratuvar hizmeti VM 'lerinde varsayılan olarak sudo erişimi verildiğinden, depolama hesabı anahtarını okuyabilirler.  Depolama hesabı uç noktası genel ise, öğrenciler öğrenci VM 'leri dışında dosya paylaşımının erişimine sahip olabilir.  Sınıf sona erdikten ve özel dosya paylaşımlarını kullanarak depolama hesabı anahtarını döndürmeyi düşünün.

Azure dosya paylaşımında özel bir uç nokta kullanılıyorsa, dikkat edilmesi önemlidir:

- Erişim, Özel ağdan kaynaklanan trafikle kısıtlıdır ve genel İnternet üzerinden erişilemez.  Yalnızca özel sanal ağdaki VM 'Ler, özel sanal ağa eşlenen bir ağdaki VM 'Ler veya özel ağ için bir VPN 'ye bağlı makineler dosya paylaşımıyla erişebilir.  
- Linux öğrencileri depolama hesabı anahtarını görebilir.  Azure dosya paylaşımının bağlanması için kimlik bilgileri, Linux sanal makinelerinde ' de depolanır `{file-share-name}.cred` ve sudo tarafından okunabilir.  Öğrenciler Azure laboratuvar hizmeti VM 'lerinde varsayılan olarak sudo erişimi verildiğinden, depolama hesabı anahtarını okuyabilirler.  Sınıf sona erdikten sonra depolama hesabı anahtarını döndürmeyi düşünün.
- Bu yaklaşım, dosya paylaşımının sanal ağının laboratuvar hesabına eşlenmeye yönelik olmasını gerektirir.  Laboratuvar oluşturulmadan **önce** , Azure Storage hesabının sanal ağının laboratuvar hesabı için sanal ağa eşlenmiş olması gerekir.

> [!NOTE]
> 5 TB 'den büyük dosya paylaşımları yalnızca [[yerel olarak yedekli depolama (LRS) hesapları]](/azure/storage/files/storage-files-how-to-create-large-file-share#restrictions)için kullanılabilir.

Bir Azure dosya paylaşımıyla bağlantılı bir VM oluşturmak için bu adımları izleyin.

1. Bir [Azure depolama hesabı](/azure/storage/files/storage-how-to-create-file-share)oluşturun. ' Bağlantı yöntemi ' sayfasında ortak uç nokta veya özel uç nokta ' ı seçin.
2. Kullanıyorsanız, dosya paylaşımlarına sanal ağdan erişilebilmeleri için [özel bir uç nokta](/azure/private-link/create-private-endpoint-storage-portal) oluşturun.  Özel bir [DNS bölgesi](/azure/dns/private-dns-privatednszone) oluşturun veya var olan bir DNS bölgesi kullanın. Özel Azure DNS bölgeleri, bir sanal ağ içinde ad çözümlemesi sağlar.
3. Bir [Azure dosya paylaşma](/azure/storage/files/storage-how-to-create-file-share)oluşturun. Dosya paylaşımının depolama hesabının genel ana bilgisayar adı ile erişilebilir olması.
4. Azure dosya paylaşımından şablon sanal makinesine bağlama:
    - [Pencerelerin](/azure/storage/files/storage-how-to-use-files-windows)
    - [[Linux]](/azure/storage/files/storage-how-to-use-files-linux).  Öğrenci VM 'lerinde bağlama sorunları yaşamamak için bkz. [Linux Ile Azure dosyaları kullanma](#using-azure-files-with-linux) .
5. Şablon VM 'yi [yayımlayın](how-to-create-manage-template.md#publish-the-template-vm) .

> [!IMPORTANT]
> Güvenlik duvarının 445 numaralı bağlantı noktası üzerinden giden SMB bağlantısını engellemediğinden emin olun. Varsayılan olarak, Azure VM 'lerinde SMB 'ye izin verilir.

### <a name="using-azure-files-with-linux"></a>Linux ile Azure dosyaları kullanma

Bir Azure dosya paylaşımının bağlanması için _varsayılan yönergeleri_ kullanırsanız, şablon yayımlandıktan sonra öğrenci VM 'lerinde dosya paylaşımının kaybolması görünür.  Aşağıda bu sorunu gideren betik değiştirilmiştir.  

```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Azure dosya paylaşımından dizine bağlayan şablon VM zaten yayınlanıyorsa `/mnt` , öğrenci aşağıdakilerden birini yapabilir.

- Dosyanın üstüne bağlamak için yönergeyi taşıyın `/mnt` `/etc/fstab` .  
- Farklı bir dizine bağlama yönergesini değiştirin `/mnt/{file-share-name}` `/prm-mnt/{file-share-name}` .

Öğrenciler, `mount -a` dizinleri yeniden bağlamak için çalıştırılmalıdır.

Linux ile dosya paylaşımları kullanma hakkında daha fazla genel bilgi için bkz. [Azure dosyalarını Linux ile kullanma](/azure/storage/files/storage-how-to-use-files-linux).

## <a name="azure-files-with-identity-base-authorization"></a>Kimlik tabanlı yetkilendirme ile Azure dosyaları

Azure dosya paylaşımlarına, şu durumlarda AD kimlik doğrulaması kullanılarak da erişilebilir

1. Öğrenci sanal makinesi etki alanına katılmış.
2. AD kimlik doğrulaması, dosya paylaşımının barındıracağı [Azure depolama hesabında etkinleştirilmiştir](/azure/storage/files/storage-files-active-directory-overview) .  

Ağ sürücüsü, depolama hesabının anahtarı değil, kullanıcının kimliği kullanılarak sanal makineye bağlanır.  Depolama hesabına erişim, genel veya özel uç noktaları kullanabilir.

Bu yaklaşımın bazı önemli noktalarından daha fazla bakalım.

- İzinler, dizin veya dosya düzeyinde ayarlanabilir.
- Geçerli Kullanıcı kimlik bilgileri dosya paylaşımının kimliğini doğrulamak için kullanılır.

Azure dosya paylaşımında ortak bir uç nokta kullanılıyorsa, dikkat edilmesi önemlidir:

- Depolama hesabının sanal ağının laboratuvar hesabına eşdüzey olması gerekmez.  Dosya paylaşma, şablon VM yayınlanmadan önce herhangi bir zamanda oluşturulabilir.

Azure dosya paylaşımında özel bir uç nokta kullanılıyorsa, dikkat edilmesi önemlidir:

- Erişim, Özel ağdan kaynaklanan trafikle kısıtlıdır ve genel İnternet üzerinden erişilemez.  Yalnızca özel sanal ağdaki VM 'Ler, özel sanal ağa eşlenen bir ağdaki VM 'Ler veya özel ağ için bir VPN 'ye bağlı makineler dosya paylaşımıyla erişebilir.  
- Bu yaklaşım, dosya paylaşımının sanal ağının laboratuvar hesabına eşlenmeye yönelik olmasını gerektirir.  Laboratuvar oluşturulmadan **önce** , Azure Storage hesabının sanal ağının laboratuvar hesabı için sanal ağa eşlenmiş olması gerekir.

AD kimlik doğrulaması etkin bir Azure dosyaları paylaşma ve etki alanı laboratuvar VM 'lerine katmak için aşağıdaki adımları izleyin.

1. Bir [Azure depolama hesabı](/azure/storage/files/storage-how-to-create-file-share)oluşturun.
2. Kullanıyorsanız, dosya paylaşımlarına sanal ağdan erişilebilmeleri için [özel bir uç nokta](/azure/private-link/create-private-endpoint-storage-portal) oluşturun.  Özel bir [DNS bölgesi](/azure/dns/private-dns-privatednszone) oluşturun veya var olan bir DNS bölgesi kullanın. Özel Azure DNS bölgeleri, bir sanal ağ içinde ad çözümlemesi sağlar.
3. Bir [Azure dosya paylaşma](/azure/storage/files/storage-how-to-create-file-share)oluşturun.
4. Kimlik tabanlı yetkilendirmeyi etkinleştirmek için adımları izleyin.  Azure AD ile eşitlenen şirket içi AD kullanılıyorsa, [Azure dosya paylaşımları IÇIN SMB üzerinden şirket içi Active Directory Domain Services kimlik doğrulaması](/azure/storage/files/storage-files-identity-auth-active-directory-enable)adımlarını izleyin.  Yalnızca Azure AD kullanıyorsanız [Azure dosyalarında Azure Active Directory Domain Services kimlik doğrulamasını etkinleştirmek](/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable)için adımları izleyin.
    >[!IMPORTANT]
    >Yönergelerde listelenen tüm önkoşulların karşılandığını doğrulamak üzere AD 'nizi yöneten ekipte konuşun.
5. Azure 'da SMB Share izin rolleri atayın.  Her role verilen izinler hakkında daha fazla bilgi için bkz. [Share-Level Permissions](/azure/storage/files/storage-files-identity-ad-ds-assign-permissions).
    1. ' Depolama dosyası veri SMB paylaşımıyla yükseltilmiş katkıda bulunan ' rolü, dosya paylaşımının içeriği için izinleri ayarlanacak kişiye veya gruba atanmalıdır.
    2. ' Depolama dosya verileri SMB paylaşma katılımcısı ' rolü, dosya paylaşımında dosya eklemesi veya dosyaları düzenlemesi gereken öğrencilerine atanmalıdır.
    3. ' Depolama dosya verileri SMB paylaşma okuyucusu ' rolü yalnızca dosya paylaşımından dosyaları okumak için gereken öğrencilere atanmalıdır.
6. Dosya paylaşımının dizin/dosya düzeyi izinlerini ayarlayın.  İzinlerin, dosya paylaşımıyla ağ erişimi olan bir etki alanına katılmış makineden ayarlanması gerekir.  **Dizin/dosya düzeyi izinlerini değiştirmek için, AAD kimlik bilgilerinizi değil, depolama anahtarını kullanarak dosya paylaşımından bağlama yapın.**  [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) PowerShell komutu veya [ıacl 'ler](/windows-server/administration/windows-commands/icacls) , izin atarken kullanabileceğiniz önerilen araçlardır.
7. Depolama hesabının [sanal ağını](how-to-connect-peer-virtual-network.md) laboratuvar hesabına eşler.
8. [Derslik Laboratuvarı oluşturun](how-to-manage-classroom-labs.md).
9. Bir betiği, öğrenciler tarafından ağ sürücüsüne bağlanmak için çalıştırılabilen şablon VM 'ye kaydedin.  Örnek betik almak için:
    1. Azure portalında depolama hesabını açın.
    1. Menüdeki dosya **hizmeti** altında **dosya paylaşımları** ' nı seçin.
    1. Bağlanmak istediğiniz paylaşma 'yı bulun, en sağdaki üç nokta düğmesini seçin ve **Bağlan**' ı seçin.
    1. **Bağlantı** çıkar, Windows, Linux ve MacOS yönergeleriyle birlikte açılır.  Windows kullanıyorsanız, **kimlik doğrulama yöntemini** **Active Directory** olarak ayarlayın.
    1. Örnekteki kodu kopyalayın ve bir dosyadaki şablon makinesine `.ps1` veya `.sh` Linux için dosyaya kaydedin.
10. Şablon makinesinde, [öğrenci makinelerini etki alanına katmak](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage)için betiği indirip çalıştırın.  `Join-AzLabADTemplate`Betik, [şablon VM 'yi](how-to-create-manage-template.md#publish-the-template-vm) otomatik olarak yayımlar.  
    > [!NOTE]
    > Şablon makine, etki alanına katılmayacak. Eğitmenler, paylaşımdaki dosyaları görüntülemek için kendilerine yayımlanmış bir öğrenci VM 'si atamalıdır.
11. Windows kullanan öğrenciler dosya paylaşımının yolunu verildiğinde [Dosya Gezgini](/azure/storage/files/storage-how-to-use-files-windows) 'ni kullanarak kimlik bilgileriyle Azure dosyaları paylaşımıyla bağlantı oluşturabilir.  Alternatif olarak, öğrenciler ağ sürücüsüne bağlanmak için yukarıda oluşturulan betiği çalıştırabilir.  Linux kullanan öğrenciler için yukarıda oluşturulan betiği çalıştırın.

## <a name="netapp-files-with-nfs-volumes"></a>NFS birimlerine sahip NetApp dosyaları

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) kurumsal sınıf, yüksek performanslı, ölçülen bir dosya depolama hizmetidir.

- Erişim ilkeleri, birim başına temelinde ayarlanabilir.
- Her birim için izin ilkeleri IP tabanlıdır.
- Öğrenciler, diğer öğrencilerin erişimi olmayan kendi hacmine ihtiyaç duyduktan sonra, laboratuvar yayımlandıktan sonra izin ilkelerine atanmalıdır.
- Azure Lab Services bağlamında yalnızca Linux makineler desteklenir.
- Laboratuvar oluşturulmadan **önce** , Azure NetApp Files kapasite havuzunun sanal ağının laboratuvar hesabı için sanal ağa eşlenmiş olması gerekir.

Azure Lab Services bir Azure NetApp Files paylaşımının kullanılması için aşağıdaki adımları izleyin.

1. Gerekirse [Azure NetApp Files](https://aka.ms/azurenetappfiles)içine ekleyin.
2. NetApp dosyaları kapasite havuzu ve NFS birimleri oluşturmak için, bkz. [set up Azure NetApp Files and NFS Volume](/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes).  Hizmet düzeyleri hakkında bilgi için bkz. [Azure NetApp Files için hizmet düzeyleri](/azure/azure-netapp-files/azure-netapp-files-service-levels).
3. NetApp dosyaları kapasite havuzunun [sanal ağını](how-to-connect-peer-virtual-network.md) laboratuvar hesabına eşler.
4. [Derslik Laboratuvarı oluşturun](how-to-manage-classroom-labs.md).
5. Şablon VM 'de, NFS dosya paylaşımlarını kullanmak için gerekli bileşenleri yükler.
    - Ubuntu

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS

        ```bash
        sudo yum install nfs-utils
        ```

6. Şablon VM 'de, `mount_fileshare.sh` [NetApp dosya paylaşımının bağlanması](/azure/azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines)için aşağıdaki betiği kaydedin.  `capacity_pool_ipaddress`Kapasite havuzunun bağlama hedefı IP adresini değişkenine atayın.  Uygun değeri bulmak için birimle ilgili bağlama yönergelerini alın.  Betik, NetApp dosyaları biriminin yolunu/adını bekliyor.  `chmod u+x mount_fileshare.sh`Komut dosyasının kullanıcılar tarafından yürütülmesini sağlamak için çalıştırmayı unutmayın.

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ may cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. Tüm öğrenciler aynı NetApp dosyaları birimine erişimi paylaşıyor ise, `mount_fileshare.sh` betik yayımlamadan önce şablon makinesinde çalıştırılabilir.  Öğrencilerin her biri kendi hacmini alıyorsa, betiği daha sonra öğrenci tarafından çalıştırılacak şekilde kaydedin.
8. Şablon VM 'yi [yayımlayın](how-to-create-manage-template.md#publish-the-template-vm) .
9. Dosya paylaşımının [Ilkesini yapılandırın](/azure/azure-netapp-files/azure-netapp-files-configure-export-policy) .  Dışarı aktarma ilkesi, tek bir VM veya birden çok VM 'nin bir birime erişimine izin verebilir.  Salt okuma veya okuma/yazma erişimi verilebilir.
10. Öğrenciler, VM 'leri başlatıp dosya paylaşımının bağlanması için betiği çalıştırmalıdır.  Yalnızca bir kez betiği çalıştırması gerekir.  Komut şöyle görünecektir `./mount_fileshare.sh myvolumename` .

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adımlar, herhangi bir laboratuvarı ayarlamak için ortaktır.

- [Şablon oluşturma ve yönetme](how-to-create-manage-template.md)
- [Kullanıcı ekle](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kota ayarlama](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zamanlama ayarlama](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Öğrenciler için e-posta kaydı bağlantıları](how-to-configure-student-usage.md#send-invitations-to-users)