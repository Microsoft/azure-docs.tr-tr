---
title: Öğretici-Azure VM 'lerinde SAP HANA veritabanlarını yedekleme
description: Bu öğreticide, Azure VM 'de çalışan SAP HANA veritabanlarını Azure Backup kurtarma hizmetleri kasasına nasıl yedekleyeceğinizi öğrenin.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 00109de349c1fdfdbaff9de30d18f64d8b986a59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587653"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Öğretici: Azure VM 'de SAP HANA veritabanlarını yedekleme

Bu öğreticide, Azure VM 'lerinde çalışan SAP HANA veritabanlarının Azure Backup kurtarma hizmetleri kasasına nasıl yedekleneceği gösterilmektedir. Bu makalede şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
>
> * Kasa oluşturma ve yapılandırma
> * Veritabanlarını bul
> * Yedeklemeleri yapılandırma

Şu anda desteklediğimiz tüm senaryolar [aşağıda](sap-hana-backup-support-matrix.md#scenario-support) verilmiştir.

>[!NOTE]
>1 Ağustos 2020 itibariyle, RHEL için SAP HANA yedekleme (7,4, 7,6, 7,7 & 8,1) genel kullanıma sunulmuştur.

## <a name="prerequisites"></a>Önkoşullar

Yedeklemeleri yapılandırmadan önce aşağıdakileri yaptığınızdan emin olun:

* SAP HANA çalıştıran VM ile aynı bölgede ve abonelikte bir [Kurtarma Hizmetleri Kasası](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) oluşturun veya oluşturun.
* Aşağıdaki [ağ bağlantısı kurma](#set-up-network-connectivity) yordamında açıklandığı gibi, Azure 'a ULAŞABILMESI için VM 'den internet 'e bağlantıya izin verin.
* SAP HANA sunucusu VM adı ve kaynak grubu adının toplam uzunluğunun Azure Resource Manager (ARM_ VM 'Ler için (ve klasik VM 'Ler için 77 karakter) 84 karakteri aşmadığından emin olun. Bu sınırlama, bazı karakterlerin hizmet tarafından ayrıldığı bir kısıtlamadır.
* **Hdbuserstore** içinde aşağıdaki ölçütlere uyan bir anahtar bulunmalıdır:
  * Varsayılan **hdbuserstore** içinde mevcut olmalıdır. Varsayılan değer `<sid>adm` SAP HANA yüklendiği hesaptır.
  * MDC için anahtar, **nameserver** SQL bağlantı noktasını göstermelidir. SDC söz konusu olduğunda, **dizin sunucusunun** SQL bağlantı noktasını işaret etmelidir
  * Kullanıcı eklemek ve silmek için kimlik bilgilerine sahip olmalıdır
  * Ön kayıt betiği başarıyla çalıştırıldıktan sonra bu anahtarın silinebileceğini unutmayın
* Kök kullanıcı olarak, HANA 'nın yüklü olduğu sanal makinede SAP HANA yedekleme yapılandırma betiğini (ön kayıt betiği) çalıştırın. [Bu betik](https://aka.ms/scriptforpermsonhana) , yedekleme IÇIN hazırlanma Hana sistemini alır. Ön kayıt betiği hakkında daha fazla bilgi edinmek için [ön kayıt betiğinin ne olduğunu](#what-the-pre-registration-script-does) öğrenin bölümüne bakın.
* HANA kurulumlarınız özel uç noktalar kullanıyorsa, [ön kayıt betiğini](https://aka.ms/scriptforpermsonhana) *-sn* veya *--Skip-Network-denetimler* parametresiyle çalıştırın.

>[!NOTE]
>Preregistration betiği, RHEL (7,4, 7,6 ve 7,7) üzerinde çalışan SAP HANA iş yükleri için **COMPAT-unixODBC234** ve rhel 8,1 Için **unixODBC** 'yi yüklüyor. [Bu paket, SAP Solutions (RPMs) deposu için RHEL for SAP HANA (RHEL 7 Server) Güncelleştirme Hizmetleri ' nde bulunur](https://access.redhat.com/solutions/5094721).  Bir Azure Marketi RHEL görüntüsü için deponun **rhuı-RHEL-SAP-HANA-for-RHEL-7-Server-rhuı-E4s-RPMS** olması gerekir.

## <a name="set-up-network-connectivity"></a>Ağ bağlantısını ayarlama

Tüm işlemler için, bir Azure VM üzerinde çalışan bir SAP HANA veritabanı, Azure Backup hizmeti, Azure depolama ve Azure Active Directory bağlantı gerektirir. Bu, Özel uç noktalar kullanılarak veya gerekli genel IP adreslerine veya FQDN 'lere erişim izni vererek elde edilebilir. Gerekli Azure hizmetlerine doğru bağlantının yapılmasına izin verilmemesi, veritabanı bulma, yedeklemeyi yapılandırma, yedeklemeleri gerçekleştirme ve verileri geri yükleme gibi işlemlerde hata oluşmasına yol açabilir.

Aşağıdaki tabloda bağlantı kurmak için kullanabileceğiniz çeşitli alternatifler listelenmektedir:

| **Seçenek**                        | **Avantajlar**                                               | **Dezavantajlar**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Özel uç noktalar                 | Sanal ağ içindeki özel IP 'Lerde yedeklemelere izin ver  <br><br>   Ağ ve kasa tarafında ayrıntılı denetim sağlama | Standart özel uç nokta [maliyetleri](https://azure.microsoft.com/pricing/details/private-link/) |
| NSG hizmet etiketleri                  | Aralık değişikliklerinin otomatik olarak birleştirilmesi için daha kolay yönetilmesi   <br><br>   Ek maliyet yok | Yalnızca NSG 'ler ile kullanılabilir  <br><br>    Hizmetin tamamına erişim sağlar |
| Azure Güvenlik Duvarı FQDN etiketleri          | Gerekli FQDN 'Ler otomatik olarak yönetildiğinden bu yana yönetilmesi daha kolay | Yalnızca Azure Güvenlik Duvarı ile kullanılabilir                         |
| Hizmet FQDN/IP 'lerine erişime izin ver | Ek maliyet yok   <br><br>  Tüm ağ güvenliği araçları ve güvenlik duvarları ile birlikte geçerlidir | Geniş bir IP veya FQDN kümesine erişilmesi gerekebilir   |
| HTTP ara sunucusu kullanma                 | VM 'lere tek bir internet erişimi noktası                       | Proxy yazılımıyla VM çalıştırmak için ek maliyetler         |

Bu seçenekleri kullanma hakkında daha fazla ayrıntı aşağıdaki şekilde paylaşılır:

### <a name="private-endpoints"></a>Özel uç noktalar

Özel uç noktalar, bir sanal ağ içindeki sunuculardan kurtarma hizmetleri kasanıza güvenli bir şekilde bağlanmanıza olanak tanır. Özel uç nokta, kasanızın VNET adres alanından bir IP kullanır. Sanal ağın ve kasadaki kaynaklarınız arasındaki ağ trafiği, sanal ağınız üzerinden ve Microsoft omurga ağında özel bir bağlantı üzerinden dolaşır. Bu, genel İnternet 'ten etkilenme olasılığını ortadan kaldırır. [Azure Backup için](./private-endpoints.md)özel uç noktalar hakkında daha fazla bilgi edinin.

### <a name="nsg-tags"></a>NSG etiketleri

Ağ güvenlik grupları (NSG) kullanıyorsanız, Azure Backup giden erişime izin vermek için *AzureBackup* Service etiketini kullanın. Azure Backup etiketine ek olarak, Azure AD (*AzureActiveDirectory*) ve Azure depolama (*depolama*) için benzer [NSG kuralları](../virtual-network/network-security-groups-overview.md#service-tags) oluşturarak kimlik doğrulama ve veri aktarımı için de bağlantıya izin vermeniz gerekir. Aşağıdaki adımlar Azure Backup etiketi için bir kural oluşturma işlemini anlatmaktadır:

1. **Tüm hizmetler**' de **ağ güvenlik grupları** ' na gidin ve ağ güvenlik grubunu seçin.

1. **Ayarlar** altında **giden güvenlik kuralları** ' nı seçin.

1. **Add (Ekle)** seçeneğini belirleyin. [Güvenlik kuralı ayarları](../virtual-network/manage-network-security-group.md#security-rule-settings)' nda açıklandığı gibi yeni bir kural oluşturmak için gereken tüm ayrıntıları girin. Seçenek **hedefinin** *hizmet etiketi* olarak ayarlandığından ve **hedef hizmet etiketinin** *AzureBackup* olarak ayarlandığından emin olun.

1. Yeni oluşturulan giden güvenlik kuralını kaydetmek için **Ekle**  ' yi seçin.

Benzer şekilde, Azure depolama ve Azure AD için [NSG giden güvenlik kuralları](../virtual-network/network-security-groups-overview.md#service-tags) oluşturabilirsiniz. Hizmet etiketleri hakkında daha fazla bilgi için [Bu makaleye](../virtual-network/service-tags-overview.md)bakın.

### <a name="azure-firewall-tags"></a>Azure Güvenlik Duvarı etiketleri

Azure Güvenlik duvarı kullanıyorsanız, *AzureBackup* [Azure Güvenlik Duvarı FQDN etiketini](../firewall/fqdn-tags.md)kullanarak bir uygulama kuralı oluşturun. Bu, Azure Backup tüm giden erişimleri sağlar.

### <a name="allow-access-to-service-ip-ranges"></a>Hizmet IP aralıklarına erişime izin ver

Erişim hizmeti IP 'lerine izin vermeyi seçerseniz, [burada](https://www.microsoft.com/download/confirmation.aspx?id=56519)KULLANILABILIR olan JSON dosyasındaki IP aralıklarına bakın. Azure Backup, Azure depolama ve Azure Active Directory karşılık gelen IP 'lere erişim izni vermeniz gerekir.

### <a name="allow-access-to-service-fqdns"></a>Hizmet FQDN 'lere erişime izin ver

Sunucularınızdaki gerekli hizmetlere erişime izin vermek için aşağıdaki FQDN 'leri de kullanabilirsiniz:

| Hizmet    | Erişilecek etki alanı adları                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure depolama | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | [Bu makaleye](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) göre 56 ve 59 bölümlerinde FQDN 'lere erişime izin ver |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>Trafiği yönlendirmek için bir HTTP proxy sunucusu kullanma

Azure VM üzerinde çalışan bir SAP HANA veritabanını yedeklerken, VM 'deki yedekleme uzantısı, Azure depolama 'ya Azure Backup ve verilere yönetim komutları göndermek için HTTPS API 'Lerini kullanır. Yedekleme uzantısı, kimlik doğrulaması için Azure AD 'yi de kullanır. Bu üç hizmet için yedekleme uzantısı trafiğini HTTP ara sunucusu üzerinden yönlendirin. Gerekli hizmetlere erişim izni vermek için yukarıda bahsedilen IP 'Ler ve FQDN 'lerin listesini kullanın. Kimliği doğrulanmış proxy sunucuları desteklenmez.

## <a name="understanding-backup-and-restore-throughput-performance"></a>Yedekleme ve geri yükleme verimlilik performansını anlama

Backint aracılığıyla sunulan SAP HANA Azure VM 'lerinde yedeklemeler (log ve log olmayan), Azure kurtarma hizmetleri kasalarına akışlardır ve bu nedenle bu akış metodolojisini anlamak önemlidir.

HANA 'nın geri dönüş bileşeni, veritabanı dosyalarının bulunduğu temel disklere bağlı olan, daha sonra Azure Backup hizmeti tarafından okunan ve Azure kurtarma hizmetleri kasasından taşınan temel disklere bağlanan ' kanallar ' (bir kanal ve yazılacak bir kanal) sağlar. Azure Backup hizmeti, biriktirme listesi yerel doğrulama denetimlerine ek olarak akışları doğrulamak için de bir sağlama toplamı gerçekleştirir. Bu doğrulamalar, Azure kurtarma hizmetleri kasasında bulunan verilerin gerçekten güvenilir ve kurtarılabilir olmasını sağlayacaktır.

Akışlar öncelikli olarak disklerle ilgilendiğinden, yedekleme ve geri yükleme performansını ölçmek için disk performansını anlamanız gerekir. Azure VM 'lerinde disk aktarım hızını ve performansını derinlemesine anlamak için [Bu makaleye](../virtual-machines/disks-performance.md) bakın. Bunlar yedekleme ve geri yükleme performansı için de geçerlidir.

**Azure Backup hizmeti, günlük olmayan yedeklemeler (örneğin, tam, türev ve artımlı) ve Hana için günlük yedeklemeleri için 100 MB/sn 'ye kadar ~ 420 Mbps elde etme girişiminde bulunur**. Yukarıda belirtildiği gibi, bunlar garantili hızlardır ve aşağıdaki faktörlere bağlı olarak değişir:

* VM 'nin önbelleğe alınmamış disk aktarım hızı üst sınırı
* Temel disk türü ve verimlilik
* Aynı anda aynı diske okuma ve yazma işlemi gerçekleştirmeye çalışan işlem sayısı.

> [!IMPORTANT]
> Önbelleğe alınmamış disk üretiminin 400 MB/sn 'tan daha az olduğu daha küçük VM 'lerde, disk ıOPS 'nin tamamı, disklerden okuma/yazma işlemlerine ilişkin işlemleri SAP HANA etkileyebilecek yedekleme hizmeti tarafından tüketilebilir. Bu durumda, yedekleme hizmeti tüketimini kısıtlama veya en yüksek sınıra sınırlamak için bir sonraki bölüme başvurabilirsiniz.

### <a name="limiting-backup-throughput-performance"></a>Yedekleme verimlilik performansını sınırlandırma

Yedekleme hizmeti disk ıOPS tüketimini maksimum değere kısıtlamak istiyorsanız aşağıdaki adımları gerçekleştirin.

1. "Opt/msawb/bin" klasörüne git
2. "ExtensionSettingOverrides.JSÜZERINDE" adlı yeni bir JSON dosyası oluşturun
3. JSON dosyasına anahtar-değer çifti eklemek için aşağıdaki adımları izleyin:

    ```json
    {
    "MaxUsableVMThroughputInMBPS": 200
    }
    ```

4. Dosyanın izinlerini ve sahipliğini aşağıdaki şekilde değiştirin:
    
    ```bash
    chmod 750 ExtensionSettingsOverrides.json
    chown root:msawb ExtensionSettingsOverrides.json
    ```

5. Hiçbir hizmetin yeniden başlatılması gerekmez. Azure Backup hizmeti bu dosyada belirtilen verimlilik performansını sınırlandırmaya çalışacaktır.

## <a name="what-the-pre-registration-script-does"></a>Ön kayıt betiği ne yapar

Ön kayıt betiğini çalıştırmak aşağıdaki işlevleri gerçekleştirir:

* Komut dosyası, Linux dağılıcmanıza bağlı olarak, Azure Backup aracısının gerektirdiği gerekli paketleri de yüklüyor veya güncelleştirir.
* Azure Backup sunucularıyla giden ağ bağlantı denetimlerini ve Azure Active Directory ve Azure depolama gibi bağımlı hizmetleri gerçekleştirir.
* [Önkoşulların](#prerequisites)bir parçası olarak listelenen Kullanıcı anahtarını kullanarak Hana sisteminizde oturum açar. Kullanıcı anahtarı, HANA sisteminde bir yedek Kullanıcı (AZUREWLBACKUPHANAUSER) oluşturmak için kullanılır ve **ön kayıt betiği başarıyla çalıştıktan sonra Kullanıcı anahtarı silinebilir**.
* AZUREWLBACKUPHANAUSER bu gerekli roller ve izinler atanır:
  * MDC için: VERITABANı YÖNETICISI ve yedekleme YÖNETICISI (HANA 2,0 SPS05 onsürümleri): geri yükleme sırasında yeni veritabanları oluşturmak için.
  * SDC: BACKUP ADMIN: geri yükleme sırasında yeni veritabanları oluşturmak için.
  * Katalog okuma: yedekleme kataloğunu okumak için.
  * SAP_INTERNAL_HANA_SUPPORT: birkaç özel tabloya erişmek için. Yalnızca HANA 2,0 SPS04 Rev 46 altındaki SDC ve MDC sürümleri için gereklidir. Bu, kamu tablolarından gerekli bilgileri yalnızca HANA ekibinden düzeltme ile elde ettiğimiz için, bu, HANA 2,0 SPS04 Rev 46 ve üzeri için gerekli değildir.
* Betik, tüm işlemleri (veritabanı sorguları, geri yükleme işlemleri, yedek yapılandırma ve çalıştırma) işlemek üzere HANA yedekleme eklentisi için AZUREWLBACKUPHANAUSER için **hdbuserstore** öğesine bir anahtar ekler.

>[!NOTE]
> Ön kayıt betiğine, [önkoşulların](#prerequisites) bir parçası olarak listelenen Kullanıcı anahtarını açıkça geçirebilirsiniz: `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Betiğin kabul ettiği diğer parametreleri öğrenmek için komutunu kullanın `bash msawb-plugin-config-com-sap-hana.sh --help`

Anahtar oluşturmayı onaylamak için, SIDADDM kimlik bilgileriyle HANA makinesinde HDBSQL komutunu çalıştırın:

```hdbsql
hdbuserstore list
```

Komut çıktısı {SID} {DBNAME} anahtarını, AZUREWLBACKUPHANAUSER olarak gösterilen kullanıcıyla görüntülemelidir.

>[!NOTE]
> Altında farklı bir SSFS dosyası kümesine sahip olduğunuzdan emin olun `/usr/sap/{SID}/home/.hdb/` . Bu yolda yalnızca bir klasör olmalıdır.

Kayıt öncesi betik çalıştırmasını tamamlamak için gereken adımların özeti aşağıda verilmiştir.

|Sağlayan  |Kaynak  |Çalışmak için gerekenler  |Yorumlar  |
|---------|---------|---------|---------|
|```<sid>```adm (OS)     |  HANA IŞLETIM SISTEMI       |   Öğreticiyi Oku ve kayıt öncesi betiği indir      |   Yukarıdaki ön kayıt betiğini indirme [Yukarıdaki önkoşulları](#prerequisites) okuyun [](https://aka.ms/scriptforpermsonhana)  |
|```<sid>```adm (OS) ve SISTEM kullanıcısı (HANA)    |      HANA IŞLETIM SISTEMI   |   Hdbuserstore set komutunu çalıştır      |   örn. hdbuserstore kümesi SISTEM ana bilgisayar adı>:3 ```<Instance#>``` 13 sistem ```<password>``` AÇıKLAMASı **:**  IP adresi veya FQDN yerine ana bilgisayar adı kullandığınızdan emin olun      |
|```<sid>```adm (OS)    |   HANA IŞLETIM SISTEMI      |  Hdbuserstore List komutunu çalıştır       |   Sonucun aşağıdaki gibi varsayılan depoyu içerip içermediğinizi denetleyin: ```KEY SYSTEM  ENV : <hostname>:3<Instance#>13  USER: SYSTEM```      |
|Kök (işletim sistemi)     |   HANA IŞLETIM SISTEMI        |    Azure Backup HANA ön kayıt betiğini Çalıştır      |    ```./msawb-plugin-config-com-sap-hana.sh -a --sid <SID> -n <Instance#> --system-key SYSTEM```     |
|```<sid>```adm (OS)    |  HANA IŞLETIM SISTEMI       |   Hdbuserstore List komutunu çalıştır      |    Sonucun yeni satırları aşağıda gösterildiği gibi içerip içerne olduğunu kontrol edin:  ```KEY AZUREWLBACKUPHANAUSER  ENV : localhost: 3<Instance#>13   USER: AZUREWLBACKUPHANAUSER```     |

Ön kayıt betiğini başarıyla çalıştırdıktan ve doğruladıktan sonra, [bağlantı gereksinimlerini](#set-up-network-connectivity) denetlemeye ve sonra yedeklemeyi kurtarma hizmetleri kasasından [yapılandırmanıza](#discover-the-databases) devam edebilirsiniz

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

Kurtarma Hizmetleri Kasası, zaman içinde oluşturulan yedeklemeleri ve kurtarma noktalarını depolayan bir varlıktır. Kurtarma Hizmetleri Kasası, korunan sanal makinelerle ilişkili yedekleme ilkelerini de içerir.

Kurtarma Hizmetleri kasası oluşturmak için:

1. [Azure portalında](https://portal.azure.com/) aboneliğinizde oturum açın.

2. Sol taraftaki menüden **tüm hizmetler** ' i seçin.

   ![Tüm Hizmetler’i seçin](./media/tutorial-backup-sap-hana-db/all-services.png)

3. **Tüm hizmetler** iletişim kutusuna **Kurtarma Hizmetleri** yazın. Kaynak listesi, yazdıklarınıza göre filtrelenir. Kaynak listesinde **Kurtarma Hizmetleri kasaları**'nı seçin.

   ![Kurtarma Hizmetleri kasalarını seçin](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. **Kurtarma Hizmetleri** kasaları panosunda **Ekle**' yi seçin.

   ![Kurtarma Hizmetleri Kasası Ekle](./media/tutorial-backup-sap-hana-db/add-vault.png)

   **Kurtarma Hizmetleri kasası** iletişim kutusu açılır. **Ad, abonelik, kaynak grubu** ve **konum** için değerler sağlayın

   ![Kurtarma Hizmetleri kasası oluşturma](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Ad**: ad, kurtarma hizmetleri kasasını tanımlamak için kullanılır ve Azure aboneliğinin benzersiz olması gerekir. En az iki, 50 karakterden daha fazla olmayan bir ad belirtin. Ad bir harf ile başlamalıdır ve yalnızca harf, rakam ve kısa çizgi içerebilir. Bu öğreticide, **Saphanavault** adını kullandık.
   * **Abonelik**: Kullanılacak aboneliği seçin. Tek bir aboneliğiniz varsa yalnızca o seçenek görüntülenir. Hangi aboneliğin kullanılacağından emin değilseniz varsayılan (önerilen) aboneliği kullanın. Yalnızca iş veya okul hesabınızın birden çok Azure aboneliği ile ilişkili olması durumunda birden çok seçenek olur. Burada **SAP HANA çözüm Laboratuvarı abonelik** aboneliğini kullandık.
   * **Kaynak grubu**: Mevcut kaynak grubunu kullanın ya da yeni bir tane oluşturun. Burada **Saphanademo** kullandık.<br>
   Aboneliğinizdeki kullanılabilir kaynak gruplarının listesini görmek için **Varolanı kullan**' ı seçin ve ardından aşağı açılan liste kutusundan bir kaynak seçin. Yeni bir kaynak grubu oluşturmak için **Yeni oluştur**'u seçip bir ad girin. Kaynak grupları hakkında tüm bilgiler için bkz. [Azure Resource Manager genel bakış](../azure-resource-manager/management/overview.md).
   * **Konum**: Kasa için coğrafi bölgeyi seçin. Kasa, SAP HANA çalıştıran sanal makine ile aynı bölgede olmalıdır. **Doğu ABD 2** kullandık.

5. **Gözden geçir + Oluştur**’u seçin.

   ![Gözden geçirme & oluştur ' u seçin](./media/tutorial-backup-sap-hana-db/review-create.png)

Kurtarma Hizmetleri Kasası artık oluşturulmuştur.

## <a name="discover-the-databases"></a>Veritabanlarını bulma

1. Kasadaki **Başlarken** bölümünde **Yedekle**' yi seçin. **İş yükünüz nerede çalışıyor?**, **Azure VM 'de SAP HANA**' yi seçin.
2. **Bulmayı Başlat**' ı seçin. Bu işlem, kasa bölgesinde korunmayan Linux VM 'lerinin bulunmasını başlatır. Korumak istediğiniz Azure VM 'yi görürsünüz.
3. **Sanal makineler Seç** bölümünde, veritabanı bulma Için SAP HANA VM 'lerine erişmek üzere Azure Backup hizmetine izinler sağlayan betiği indirmek için bağlantıyı seçin.
4. Betiği, yedeklemek istediğiniz SAP HANA veritabanlarını barındıran VM üzerinde çalıştırın.
5. Betiği VM üzerinde çalıştırdıktan sonra, **sanal makineler Seç** bölümünde VM 'yi seçin. Ardından veritabanlarını **keşfet**' i seçin.
6. Azure Backup VM 'deki tüm SAP HANA veritabanlarını bulur. Bulma sırasında Azure Backup, VM 'yi kasayla kaydeder ve VM 'ye bir uzantı kurar. Veritabanına hiçbir aracı yüklü değil.

   ![Veritabanlarını bulma](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Yedeklemeyi yapılandırma

Şimdi yedeklemek istediğimiz veritabanları keşfedildiğine göre yedeklemeyi etkinleştirelim.

1. **Yedeklemeyi Yapılandır**' ı seçin.

   ![Yedeklemeyi yapılandırma](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. **Yedeklenecek öğeleri seçin**' de korumak istediğiniz bir veya daha fazla veritabanını seçin ve ardından **Tamam**' ı seçin.

   ![Yedeklenecek öğeleri seçin](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. **Yedekleme ilkesi > yedekleme ilkesi**' ni seçin, sonraki bölümdeki yönergelere uygun olarak veritabanları için yeni bir yedekleme ilkesi oluşturun.

   ![Yedekleme ilkesi seçin](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. İlkeyi oluşturduktan sonra **yedekleme menüsünde** **yedeklemeyi etkinleştir**' i seçin.

   ![Yedeklemeyi Etkinleştir ' i seçin](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Yedekleme Yapılandırma ilerlemesini portalın **Bildirimler** alanında izleyin.

## <a name="creating-a-backup-policy"></a>Yedekleme ilkesi oluşturma

Yedekleme ilkesi, yedeklemelerin ne zaman alındığını ve ne kadar süreyle korunduğunu tanımlar.

* İlkeler, kasa düzeyinde oluşturulur.
* Bir yedekleme ilkesi birden fazla kasa tarafından kullanılabilir ancak ilgili yedekleme ilkesini her kasaya ayrıca uygulamanız gerekir.

İlke ayarlarını aşağıdaki gibi belirtin:

1. **İlke adı** alanına yeni ilkenin adını girin. Bu durumda, **Saphana** girin.

   ![Yeni ilke için ad girin](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. **Tam yedekleme ilkesinde**, bir **yedekleme sıklığı** seçin. **Günlük** veya **haftalık** seçeneklerinden birini belirleyebilirsiniz. Bu öğretici için **günlük** yedeklemeyi seçtik.

   ![Yedekleme sıklığı seçin](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. **Bekletme aralığı**' nda, tam yedekleme için bekletme ayarlarını yapılandırın.
   * Varsayılan olarak, tüm seçenekler seçilidir. Kullanmak istemediğiniz tüm Bekletme aralığı sınırlarını temizleyin ve yaptığınız değişiklikleri ayarlayın.
   * Herhangi bir yedekleme türü için en düşük saklama süresi (tam/fark/günlük) yedi gündür.
   * Kurtarma noktaları, belirtilen bekletme aralığına göre etiketlenir. Örneğin günlük tam yedek seçerseniz her gün yalnızca bir yedekleme işlemi tetiklenir.
   * Belirli bir günün yedeklemesi, haftalık bekletme aralığına ve ayarına göre etiketlenebilir ve korunur.
   * Aylık ve yıllık bekletme aralıkları da benzer şekilde çalışır.
4. **Tam Yedekleme ilkesi** menüsünde **Tamam**'ı seçerek ayarları kabul edin.
5. Ardından bir fark ilkesi eklemek için **fark yedeklemesi** ' ni seçin.
6. **Değişiklik Yedeği ilkesi** sayfasında **Etkinleştir**'i seçerek sıklık ve bekletme denetimlerini açın. **30 gün** boyunca tutulan her **Pazar günü** **2:00**' de bir değişiklik yedeklemesini etkinleştirdik.

   ![Değişiklik yedekleme ilkesi](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Günlük yedekleme olarak bir fark veya artımlı seçebilirsiniz, ancak ikisini birden kullanamazsınız.

7. **Artımlı yedekleme ilkesinde**, sıklık ve bekletme denetimlerini açmak için **Etkinleştir** ' i seçin.
    * En çok, günde bir artımlı yedekleme tetikleyebilirsiniz.
    * Artımlı yedeklemeler en fazla 180 gün boyunca korunabilir. Daha uzun süre bekletmeniz gerekiyorsa tam yedekleme ilkesini kullanmanız gerekir.

    ![Artımlı yedekleme ilkesi](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

8. **Tamam**'ı seçerek ilkeyi kaydedin ve ana **Yedekleme ilkesi** menüsüne dönün.
9. İşlem günlüğü yedekleme ilkesi eklemek için **günlük yedeklemesi** ' ni seçin,
   * **Günlük yedeklemesi** varsayılan olarak **Enable** olarak ayarlanmıştır. Tüm günlük yedeklemelerini SAP HANA yönettiğinden bu devre dışı bırakılamaz.
   * Yedekleme zamanlaması ve **15 günlük** Bekletme dönemi olarak **2 saat** ayarlandık.

    ![Günlük yedekleme ilkesi](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Günlük yedeklemeleri, yalnızca bir başarılı tam yedekleme tamamlandıktan sonra Flow 'a başlar.
   >

10. **Tamam**'ı seçerek ilkeyi kaydedin ve ana **Yedekleme ilkesi** menüsüne dönün.
11. Yedekleme ilkesini tanımlamayı tamamladıktan sonra **Tamam**' ı seçin.

Artık SAP HANA veritabanınız için yedekleme (ler) i başarıyla yapılandırdınız.

## <a name="next-steps"></a>Sonraki Adımlar

* [Azure VM 'lerinde çalışan SAP HANA veritabanlarında isteğe bağlı yedeklemeler çalıştırmayı](backup-azure-sap-hana-database.md#run-an-on-demand-backup) öğrenin
* [Azure VM 'lerinde çalışan SAP HANA veritabanlarını geri yüklemeyi](sap-hana-db-restore.md) öğrenin
* [Kullanarak yedeklenen SAP HANA veritabanlarını yönetmeyi öğrenin Azure Backup](sap-hana-db-manage.md)
* [SAP HANA veritabanlarını yedeklerken sık karşılaşılan sorunları gidermeye nasıl sorun](backup-azure-sap-hana-database-troubleshoot.md) alabileceğinizi öğrenin