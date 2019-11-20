---
title: Azure 'da SAP HANA için SMT Server ayarlama (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA için SMT Server 'ı ayarlama (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0d9e86e54a4c94db97b6c89b3ef8799855963020
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099758"
---
# <a name="set-up-smt-server-for-suse-linux"></a>SUSE Linux için SMT Server 'ı ayarlama
Büyük SAP HANA örneklerinin internet 'e doğrudan bağlantısı yoktur. Bu tür bir birimi işletim sistemi sağlayıcısına kaydetmek ve güncelleştirmeleri indirmek ve uygulamak için kolay bir işlem değildir. SUSE Linux çözümü, bir Azure sanal makinesinde bir SMT sunucusu ayarlamaya yöneliktir. Sanal makineyi, HANA büyük örneğine bağlı bir Azure sanal ağında barındırın. Bu tür bir SMT sunucusu sayesinde, HANA büyük örnek birimi güncelleştirmeleri kaydedebilir ve indirebilir. 

SUSE hakkında daha fazla belge için, bkz. [SLES 12 SP2 Için abonelik yönetim aracı](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

HANA büyük örneklere yönelik görevi yerine getiren bir SMT sunucusunu yüklemeye yönelik önkoşullar şunlardır:

- HANA büyük örnek ExpressRoute devresine bağlı bir Azure sanal ağı.
- Bir kuruluşla ilişkili SUSE hesabı. Kuruluşun geçerli bir SUSE aboneliğine sahip olması gerekir.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Bir Azure sanal makinesine SMT Server 'ı yükler

İlk olarak, [SUSE müşteri merkezinde](https://scc.suse.com/)oturum açın.

**Kuruluş** > **kuruluşu kimlik bilgileri**' ne gidin. Bu bölümde, SMT sunucusunu ayarlamak için gereken kimlik bilgilerini bulmanız gerekir.

Ardından, Azure sanal ağına bir SUSE Linux VM 'si yüklersiniz. Sanal makineyi dağıtmak için, Azure 'un SLES 12 SP2 Galeri görüntüsünü alın (KCG SUSE görüntüsünü seçin). Dağıtım sürecinde, DNS adı tanımlamaz ve statik IP adresleri kullanmayın.

![SMT sunucusu için sanal makine dağıtımının ekran görüntüsü](./media/hana-installation/image3_vm_deployment.png)

Dağıtılan sanal makine daha küçüktür ve 10.34.1.4 Azure sanal ağındaki iç IP adresini aldı. Sanal makinenin adı *smtserver*' dır. Yüklemeden sonra, HANA büyük örnek birimine veya birimlerine olan bağlantı denetlenir. Ad çözümlemesini nasıl düzenlediğinize bağlı olarak, Azure sanal makinesinin vb/konaklarındaki HANA büyük örnek birimlerinin çözümlenmesini yapılandırmanız gerekebilir. 

Sanal makineye bir disk ekleyin. Bu diski güncelleştirmeleri tutmak için kullanırsınız ve önyükleme diskinin kendisi çok küçük olabilir. Burada, disk aşağıdaki ekran görüntüsünde gösterildiği gibi/SRV/www/htdocs dizinine bağlandı. 100 GB bir disk yeterli olacaktır.

![SMT sunucusu için sanal makine dağıtımının ekran görüntüsü](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

HANA büyük örnek birimi veya birimlerinde oturum açın,/etc/Konakları koruyun ve ağ üzerinden SMT sunucusunu çalıştırmak için gereken Azure sanal makinesine erişip ulaşamayacağını denetleyin.

Bu denetim sonrasında, SMT sunucusunu çalıştırması gereken Azure sanal makinesinde oturum açın. Sanal makinede oturum açmak için Putty kullanıyorsanız, Bash pencerenizde bu komut dizisini çalıştırın:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Ayarları etkinleştirmek için bash 'nizi yeniden başlatın. Ardından YAST 'yi başlatın.

VM 'nizi (smtserver) SUSE sitesine bağlayın.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Sanal makine SUSE sitesine bağlandıktan sonra, SMT paketlerini yükler. Smt paketlerini yüklemek için aşağıdaki Putty komutunu kullanın.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Ayrıca, SMT paketlerini yüklemek için YAST aracını da kullanabilirsiniz. YAST 'de **Yazılım Bakımı**' na gidin ve SMT araması yapın. Otomatikolarak YaST2-smt öğesine geçiş yapar.

![YAST içindeki SMT ekran görüntüsü](./media/hana-installation/image5_smt_in_yast.PNG)


Smtserver üzerinde yükleme seçimini kabul edin. Yükleme tamamlandıktan sonra, SMT sunucu yapılandırmasına gidin. Daha önce aldığınız SUSE müşteri merkezinden kuruluş kimlik bilgilerini girin. Ayrıca, Azure sanal makinenizin ana bilgisayar adını SMT sunucu URL 'SI olarak girin. Bu gösteride, https:\//smtserver.

![SMT sunucu yapılandırması ekran görüntüsü](./media/hana-installation/image6_configuration_of_smtserver1.png)

Şimdi SUSE Customer Center bağlantısının çalışıp çalışmadığını test edin. Aşağıdaki ekran görüntüsünde gördüğünüz gibi, bu gösterim çalışmasında çalışır.

![SUSE müşteri merkezine yönelik test bağlantısı ekran görüntüsü](./media/hana-installation/image7_test_connect.png)

SMT kurulumu başladıktan sonra bir veritabanı parolası sağlayın. Yeni bir yükleme olduğundan, bu parolayı aşağıdaki ekran görüntüsünde gösterildiği gibi tanımlamanız gerekir.

![Veritabanı için parola tanımlamayı ekran görüntüsü](./media/hana-installation/image8_define_db_passwd.PNG)

Sonraki adım bir sertifika oluşturmaktır.

![SMT sunucusu için sertifika oluşturma ekran görüntüsü](./media/hana-installation/image9_certificate_creation.PNG)

Yapılandırmanın sonunda, eşitleme denetiminin çalıştırılması birkaç dakika sürebilir. SMT sunucusunu yükleme ve yapılandırmadan sonra, dizin deposunu/SRV/www/htdoc/bağlama noktası altında bulmalısınız. Depo altında bazı alt dizinler de vardır. 

Bu komutlarla, SMT sunucusunu ve ilgili hizmetlerini yeniden başlatın.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Paketleri SMT sunucusuna indir

Tüm hizmetler yeniden başlatıldıktan sonra, YAST kullanarak SMT yönetiminde uygun paketleri seçin. Paket seçimi, HANA büyük örnek sunucusunun işletim sistemi görüntüsüne bağlıdır. Paket seçimi, SMT sunucusunu çalıştıran sanal makinenin SLES sürümüne veya sürümüne bağlı değildir. Aşağıdaki ekran görüntüsünde, seçim ekranının bir örneği gösterilmektedir.

![Paket seçme ekran görüntüsü](./media/hana-installation/image10_select_packages.PNG)

Ardından, select paketlerinin ilk kopyasını ayarladığınız SMT sunucusuna başlatın. Bu kopya kabukta komut, SMT-yansıtma kullanılarak tetiklenir.

![Nesnelerin SMT sunucusuna indiriceği ekran görüntüsü](./media/hana-installation/image11_download_packages.PNG)

Paketler,/SRV/www/htges bağlama noktası altında oluşturulan dizinlere kopyalanmalıdır. Bu işlem, seçtiğiniz paket sayısına bağlı olarak bir saat veya daha fazla sürebilir. Bu işlem tamamlandığında, SMT istemci kurulumuna geçin. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>HANA büyük örnek birimlerinde SMT istemcisini ayarlama

Bu durumda, istemci veya istemciler HANA büyük örnek birimleridir. SMT Server kurulumu, clientSetup4SMT.sh betiğini Azure sanal makinesine kopyaladı. Bu betiği, SMT sunucunuza bağlamak istediğiniz HANA büyük örnek birimine kopyalayın. Betiği-h seçeneğiyle başlatın ve SMT sunucunuzun adını bir parametre olarak verin. Bu örnekte, ad *smtserver*' dır.

![SMT istemcisini yapılandırma ekran görüntüsü](./media/hana-installation/image12_configure_client.PNG)

Sertifikanın istemci tarafından sunucudan yüklenmesi başarılı olur, ancak aşağıdaki ekran görüntüsünde gösterildiği gibi kayıt başarısız olur.

![İstemci kayıt hatası ekran görüntüsü](./media/hana-installation/image13_registration_failed.PNG)

Kayıt başarısız olursa, [SUSE destek belgesi](https://www.suse.com/de-de/support/kb/doc/?id=7006024)' ne bakın ve burada açıklanan adımları çalıştırın.

> [!IMPORTANT] 
> Sunucu adı için, tam etki alanı adı olmadan sanal makinenin adını (Bu örnekte, *smtserver*) belirtin. 

Bu adımları çalıştırdıktan sonra, HANA büyük örnek biriminde aşağıdaki komutu çalıştırın:

```
SUSEConnect –cleanup
```

> [!Note] 
> Bu adımdan sonra birkaç dakika bekleyin. ClientSetup4SMT.sh 'i hemen çalıştırırsanız bir hata alabilirsiniz.

SUSE makalesinin adımlarını temel alarak düzeltilmesi gereken bir sorunla karşılaşırsanız, clientSetup4SMT.sh 'i HANA büyük örnek biriminde yeniden başlatın. Şimdi başarıyla tamamlanmalıdır.

![İstemci kaydı başarısı ekran görüntüsü](./media/hana-installation/image14_finish_client_config.PNG)

Azure sanal makinesine yüklediğiniz SMT sunucusuna bağlanmak için HANA büyük örnek biriminin SMT istemcisini yapılandırdınız. Bu aşamada, HANA büyük örneklerine işletim sistemi güncelleştirmeleri yüklemek veya ek paketler yüklemek için ' zypper of up ' veya ' zypper ' ' ölçeğini alabilirsiniz. Yalnızca SMT sunucusundan daha önce indirdiğiniz güncelleştirmeleri alabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
- [HLI 'Da Hana yüklemesi](hana-example-installation.md).











