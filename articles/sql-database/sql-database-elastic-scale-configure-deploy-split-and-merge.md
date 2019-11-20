---
title: Ayırma-birleştirme hizmetini dağıtma
description: Verileri parçalı veritabanları arasında taşımak için bölünmüş birleştirme işlemini de kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: e7438674981115599637ac1763a8d24444fd0552
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823701"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Parçalı veritabanları arasında veri taşımak için bölünmüş birleştirme hizmeti dağıtma

Bölünmüş birleştirme aracı, verileri parçalı veritabanları arasında taşımanızı sağlar. Bkz. [ölçekli bulut veritabanları arasında veri taşıma](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Bölünmüş birleştirme paketlerini indirme
1. [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget)'den en son NuGet sürümünü indirin.
2. Bir komut istemi açın ve NuGet. exe dosyasını indirdiğiniz dizine gidin. İndirme, PowerShell komutlarını içerir.
3. En son bölünmüş birleştirme paketini şu komutla güncel dizine indirin:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Dosyalar, **Microsoft. Azure. SQLDatabase. Elalapscale. Service. SplitMerge. x. x. xxx. x** adlı bir dizine yerleştirilir; burada *x. x. xxx.* x sürüm numarasını yansıtır. Content\splitmerge\service alt dizinindeki bölünmüş birleştirme hizmeti dosyalarını ve, **content\splitmerge\powershell** alt dizinindeki bölünmüş birleştirme PowerShell betiklerini (ve gerekli istemci dll 'leri) bulun.

## <a name="prerequisites"></a>Ön koşullar
1. Bölünmüş birleştirme durum veritabanı olarak kullanılacak bir Azure SQL DB veritabanı oluşturun. [Azure Portal](https://portal.azure.com) gidin. Yeni bir **SQL veritabanı**oluşturun. Veritabanına bir ad verin ve yeni bir yönetici ve parola oluşturun. Daha sonra kullanmak üzere adı ve parolayı kaydettiğinizden emin olun.
2. Azure SQL DB sunucunuzun Azure hizmetlerinin bu sunucuya bağlanmasına izin verdiğinden emin olun. Portalda, **güvenlik duvarı ayarları**' nda, **Azure hizmetlerine erişime Izin ver** ayarının **Açık**olarak ayarlandığından emin olun. "Kaydet" simgesine tıklayın.
3. Tanılama çıktısı için bir Azure depolama hesabı oluşturun.
4. Bölünmüş birleştirme hizmetiniz için bir Azure bulut hizmeti oluşturun.

## <a name="configure-your-split-merge-service"></a>Bölünmüş birleştirme hizmetinizi yapılandırma
### <a name="split-merge-service-configuration"></a>Bölünmüş birleştirme hizmeti yapılandırması
1. Bölünmüş birleştirme derlemelerini indirdiğiniz klasörde, **Splitmergeservice. cspkg** ile birlikte gelen **ServiceConfiguration. Template. cscfg** dosyasının bir kopyasını oluşturun ve **ServiceConfiguration. cscfg**olarak yeniden adlandırın.
2. Visual Studio gibi bir metin düzenleyicisinde **ServiceConfiguration. cscfg** ' i açarak sertifika parmak izlerinin biçimi gibi girdileri doğrular.
3. Yeni bir veritabanı oluşturun veya bölünmüş birleştirme işlemlerine yönelik durum veritabanı olarak kullanılacak mevcut bir veritabanını seçin ve bu veritabanının bağlantı dizesini alın. 
   
   > [!IMPORTANT]
   > Şu anda, durum veritabanının Latin harmanlaması (SQL\_Latin1\_genel\_CP1\_CI\_AS) kullanması gerekir. Daha fazla bilgi için bkz. [Windows harmanlama adı (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Azure SQL DB ile bağlantı dizesi genellikle şu biçimdedir:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Bu bağlantı dizesini cscfg dosyasında, Elaun Scalemetadata ayarındaki **Splitmergeweb** ve **splitmergeworker** rol bölümlerine girin.
5. **Splitmergeworker** rolü Için, **Workerrolesynchronizationstorageaccountconnectionstring** ayarı için Azure depolama 'ya geçerli bir bağlantı dizesi girin.

### <a name="configure-security"></a>Güvenliği yapılandırma
Hizmetin güvenliğini yapılandırmaya yönelik ayrıntılı yönergeler için, [bölünmüş birleştirme güvenlik yapılandırması](sql-database-elastic-scale-split-merge-security-configuration.md)' na bakın.

Bu öğreticide basit bir test dağıtımının amaçları doğrultusunda hizmeti çalışır duruma getirmek için en az bir yapılandırma adımları kümesi gerçekleştirilecek. Bu adımlar, hizmet ile iletişim kurmak için yalnızca bir makine/hesap yürüten hesabı etkinleştirir.

### <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma
Yeni bir dizin oluşturun ve bu dizinden, [Visual Studio için bir geliştirici komut istemi](https://msdn.microsoft.com/library/ms229859.aspx) kullanarak aşağıdaki komutu yürütün:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Özel anahtarı korumak için bir parola istenir. Güçlü bir parola girin ve onaylayın. Bundan sonra parolanın daha sonra kullanılması istenir. Güvenilen sertifika yetkilileri kök deposuna içeri aktarmak için sonda **Evet** ' e tıklayın.

### <a name="create-a-pfx-file"></a>PFX dosyası oluşturma
MakeCert 'ın yürütüldüğü pencerede aşağıdaki komutu yürütün; sertifikayı oluşturmak için kullandığınız parolayı kullanın:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>İstemci sertifikasını Kişisel depoya aktarma
1. Windows Gezgini 'nde **mycert. pfx**dosyasına çift tıklayın.
2. **Sertifika Içeri aktarma sihirbazında** **Geçerli Kullanıcı** ' yı seçin ve **İleri**' ye tıklayın.
3. Dosya yolunu doğrulayın ve **İleri**' ye tıklayın.
4. Parolayı yazın, **Tüm genişletilmiş özellikleri içer** işaretli bırakın ve **İleri**' ye tıklayın.
5. **[...] Sertifika deposunu otomatik olarak seçin** ve **İleri**' ye tıklayın.
6. **Son** ve **Tamam**' a tıklayın.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>PFX dosyasını bulut hizmetine yükle
1. [Azure Portal](https://portal.azure.com) gidin.
2. **Cloud Services**seçin.
3. Bölünmüş/birleştirme hizmeti için yukarıda oluşturduğunuz bulut hizmetini seçin.
4. Üst menüdeki **Sertifikalar** ' a tıklayın.
5. Alt çubukta **karşıya yükle** ' ye tıklayın.
6. PFX dosyasını seçin ve yukarıdaki parolayla aynı parolayı girin.
7. Tamamlandıktan sonra, listedeki yeni girdiden sertifika parmak izini kopyalayın.

### <a name="update-the-service-configuration-file"></a>Hizmet yapılandırma dosyasını güncelleştirme
Yukarıda kopyalanmış sertifika parmak izini bu ayarların parmak izi/değer özniteliğine yapıştırın.
Çalışan rolü için:
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Web rolü için:

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

CA için ayrı sertifikaların üretim dağıtımları, şifreleme, sunucu sertifikası ve istemci sertifikaları için kullanılması gerektiğini lütfen unutmayın. Bunun hakkında ayrıntılı yönergeler için bkz. [Güvenlik Yapılandırması](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Hizmetinizi dağıtın
1. [Azure portal](https://portal.azure.com)'a gidin
2. Daha önce oluşturduğunuz bulut hizmetini seçin.
3. **Genel Bakış**'a tıklayın.
4. Hazırlama ortamını seçin ve ardından **karşıya yükle**' ye tıklayın.
5. İletişim kutusunda bir dağıtım etiketi girin. Hem ' paket ' hem de ' yapılandırma ' için ' yerelden ' seçeneğine tıklayın ve **Splitmergeservice. cspkg** dosyasını ve daha önce yapılandırdığınız cscfg dosyanızı seçin.
6. **Bir veya daha fazla rol tek bir örnek içeriyorsa bile dağıt** onay kutusunun işaretli olduğundan emin olun.
7. Dağıtıma başlamak için sağ alt köşedeki değer düğmesine basın. Tamamlanması için birkaç dakika sürer.


## <a name="troubleshoot-the-deployment"></a>Dağıtım sorunlarını giderme
Web rolünüzün çevrimiçi olması başarısız olursa, güvenlik yapılandırması ile ilgili bir sorun olabilir. SSL 'nin yukarıda açıklanan şekilde yapılandırılıp yapılandırılmadığını denetleyin.

Çalışan rolünüzün çevrimiçi olması, ancak Web rolünüzün başarılı olması durumunda, büyük olasılıkla daha önce oluşturduğunuz durum veritabanına bağlanmada bir sorun olabilir.

* Cscfg 'inizdeki bağlantı dizesinin doğru olduğundan emin olun.
* Sunucu ve veritabanının var olduğunu ve Kullanıcı kimliği ve parolasının doğru olduğundan emin olun.
* Azure SQL DB için bağlantı dizesi şu biçimde olmalıdır:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Sunucu adının **https://** ile başlamadığından emin olun.
* Azure SQL DB sunucunuzun Azure hizmetlerinin bu sunucuya bağlanmasına izin verdiğinden emin olun. Bunu yapmak için, veritabanınızı portalda açın ve **Azure hizmetlerine erişime Izin ver** ayarının * * * * * * * * olarak ayarlandığından emin olun.

## <a name="test-the-service-deployment"></a>Hizmet dağıtımını test etme
### <a name="connect-with-a-web-browser"></a>Bir Web tarayıcısı ile bağlanma
Bölünmüş birleştirme hizmetinizin Web uç noktasını belirleme. Bunu portalda, bulut hizmetinize **genel bakış** bölümüne giderek ve sağ taraftaki **site URL 'sini** arayarak bulabilirsiniz. Varsayılan güvenlik ayarları HTTP uç noktasını devre dışı bıraktıktan sonra **http://** değerini **https://** ile değiştirin. Bu URL için sayfayı tarayıcınıza yükleyin.

### <a name="test-with-powershell-scripts"></a>PowerShell betikleri ile test etme
Dağıtım ve ortamınız, eklenen örnek PowerShell betikleri çalıştırılarak test edilebilir.

Dahil edilen betik dosyaları şunlardır:

1. **Setupsamplesplitmergeenvironment. ps1** -bir test veri katmanını bölünmüş/birleştirme için ayarlar (ayrıntılı açıklama için aşağıdaki tabloya bakın)
2. **Executesamplesplitmerge. ps1** -test işlemlerini test veri katmanında yürütür (ayrıntılı açıklama için aşağıdaki tabloya bakın)
3. **GetMappings. ps1** -parça eşlemelerinin geçerli durumunu yazdıran en üst düzey örnek komut dosyası.
4. Shardmanagement **. psm1** -SHARDMANAGEMENT API 'sini sarmalayan yardım betiği
5. SQL veritabanlarını oluşturmak ve yönetmek için **Sqldatabaseyardımcıları. psm1** -yardımcı betiği
   
   <table style="width:100%">
     <tr>
       <th>PowerShell dosyası</th>
       <th>Adımlar</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment. ps1</th>
       <td>1.    Parça eşleme Yöneticisi veritabanı oluşturur</td>
     </tr>
     <tr>
       <td>2.    2 parçalı veritabanları oluşturur.
     </tr>
     <tr>
       <td>3.    Bu veritabanları için bir parça haritası oluşturur (bu veritabanlarında var olan parça haritalarını siler). </td>
     </tr>
     <tr>
       <td>4.    Her iki parça için de küçük bir örnek tablosu oluşturur ve tabloyu parçaların birindeki bir satıra doldurur.</td>
     </tr>
     <tr>
       <td>5.    Parçalı tablo için düzeni bildirir.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell dosyası</th>
       <th>Adımlar</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge. ps1 </th>
       <td>1.    Bölünmüş birleştirme hizmeti Web ön ucu için bölünmüş bir istek gönderir ve bu, verileri ilk parçadan ikinci parçaya böler.</td>
     </tr>
     <tr>
       <td>2.    Bölünen istek durumu için Web ön uç öğesini yoklar ve istek tamamlanana kadar bekler.</td>
     </tr>
     <tr>
       <td>3.    Bölünmüş birleştirme hizmeti Web ön ucu için birleştirme isteği gönderir ve bu, verileri ikinci parçadan ilk parçaya geri kaydırır.</td>
     </tr>
     <tr>
       <td>4.    Birleştirme isteği durumu için Web ön uç öğesini yoklar ve istek tamamlanana kadar bekler.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Dağıtımınızı doğrulamak için PowerShell 'i kullanma
1. Yeni bir PowerShell penceresi açın ve bölünmüş birleştirme paketini indirdiğiniz dizine gidin ve "PowerShell" dizinine gidin.
2. Parça eşleme Yöneticisi ve parçaları oluşturulacak bir Azure SQL veritabanı sunucusu (veya var olan bir sunucu seçin) oluşturun.
   
   > [!NOTE]
   > SetupSampleSplitMergeEnvironment. ps1 betiği, betiği basit tutmak için bu veritabanlarını varsayılan olarak aynı sunucuda oluşturur. Bu, bölünmüş birleştirme hizmetinin kendisi için bir kısıtlamadır.
   >
   
   Bölünmüş birleştirme hizmetinin verileri taşıması ve parça haritasını güncelleştirmesi için, DBs 'ye okuma/yazma erişimiyle birlikte bir SQL kimlik doğrulaması oturum açma gerekir. Bölünmüş birleştirme hizmeti bulutta çalıştığından, bu, şu anda tümleşik kimlik doğrulamasını desteklememektedir.
   
   Azure SQL Server 'ın, bu betikleri çalıştıran makinenin IP adresinden erişime izin verecek şekilde yapılandırıldığından emin olun. Bu ayarı Azure SQL Server/yapılandırma/izin verilen IP adresleri altında bulabilirsiniz.
3. Örnek ortamı oluşturmak için SetupSampleSplitMergeEnvironment. ps1 betiğini yürütün.
   
   Bu betiği çalıştırmak, parça eşleme Yöneticisi veritabanında ve parçalardaki tüm mevcut parça eşleme yönetimi veri yapılarını temizler. Parça haritasını veya parçaları yeniden başlatmak isterseniz betiği yeniden çalıştırmak yararlı olabilir.
   
   Örnek komut satırı:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Örnek ortamda mevcut olan eşlemeleri görüntülemek için GetMappings. ps1 betiğini yürütün.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Bölünmüş bir işlem yürütmek için ExecuteSampleSplitMerge. ps1 betiğini yürütün (ilk parça üzerindeki verilerin ikinci parçaya taşınması) ve ardından bir birleştirme işlemi (verileri ilk parça üzerine geri taşıyarak). SSL 'yi yapılandırdıysanız ve HTTP uç noktasını devre dışı bıraktıysanız, bunun yerine https://uç noktasını kullandığınızdan emin olun.
   
   Örnek komut satırı:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Aşağıdaki hatayı alıyorsanız, büyük olasılıkla web uç noktasının sertifikasıyla ilgili bir sorun olabilir. Web uç noktasına en sevdiğiniz Web tarayıcınızla bağlanmayı deneyin ve bir sertifika hatası olup olmadığını denetleyin.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Başarılı olursa, çıkış aşağıdaki gibi görünmelidir:
   
   ```
   > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
   > Sending split request
   > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
   > Polling split-merge request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > Sending merge request
   > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
   > Polling request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > 
   ```
6. Diğer veri türleriyle denemeler yapın! Bu betiklerin hepsi, anahtar türünü belirtmenizi sağlayan isteğe bağlı bir-ShardKeyType parametresi alır. Varsayılan değer Int32 'dir, ancak aynı zamanda Int64, GUID veya binary de belirtebilirsiniz.

## <a name="create-requests"></a>Oluşturma istekleri
Hizmet, Web Kullanıcı arabirimi kullanılarak veya Web rolü aracılığıyla isteklerinizi gönderecek olan SplitMerge. psm1 PowerShell modülünü içeri aktararak veya kullanarak kullanılabilir.

Hizmet, verileri parçalı tablolarda ve başvuru tablolarında taşıyabilir. Parçalı bir tabloda parçalı anahtar sütunu bulunur ve her parça üzerinde farklı satır verileri vardır. Başvuru tablosu, her parça için aynı satır verilerini içerecek şekilde parçalara ayrılmış değildir. Başvuru tabloları, sık değişolmayan ve sorgularda parçalı tablolarla BIRLEŞTIRMEK için kullanılan veriler için yararlıdır.

Bölünmüş birleştirme işlemi gerçekleştirmek için, taşınmasını istediğiniz parçalı tabloları ve başvuru tablolarını bildirmeniz gerekir. Bu, **Fermainınfo** API 'si ile gerçekleştirilir. Bu API, **Microsoft. Azure. SQLDatabase. Elakscale. ShardManagement. Schema** ad alanıdır.

1. Parçalı her tablo için, tablonun üst şema adını (isteğe bağlı, varsayılan olarak "dbo"), tablo adını ve parça anahtarını içeren tablodaki sütun adını açıklayan bir **Shardedtableınfo** nesnesi oluşturun.
2. Her başvuru tablosu için, tablonun üst şema adını (isteğe bağlı, varsayılan olarak "dbo") ve tablo adını açıklayan bir **Referencetableınfo** nesnesi oluşturun.
3. Yukarıdaki Tableınfo **nesnelerini yeni bir** nesne tablosu nesnesine ekleyin.
4. Bir **Shardmapmanager** nesnesine bir başvuru alın ve **Getıfermainfocollection**' ı çağırın.
5. Parça eşleme adını sağlayarak, **Fermainınfo** ' ı bir **Fermainfocollection**öğesine ekleyin.

SetupSampleSplitMergeEnvironment. ps1 betiğiyle buna örnek olarak görünebilirler.

Bölünmüş birleştirme hizmeti, sizin için hedef veritabanını (veya veritabanındaki herhangi bir tablo için şemayı) oluşturmaz. Hizmete bir istek gönderilmeden önce bunların önceden oluşturulması gerekir.

## <a name="troubleshooting"></a>Sorun giderme
Örnek PowerShell betiklerini çalıştırırken aşağıdaki iletiyi görebilirsiniz:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Bu hata, SSL sertifikanızın doğru yapılandırılmadığı anlamına gelir. Lütfen ' bir Web tarayıcısı ile bağlanma ' bölümündeki yönergeleri izleyin.

İstek gönderemeyebilirsiniz şunu görebilirsiniz:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

Bu durumda, özellikle **Workerrolesynchronizationstorageaccountconnectionstring**ayarında yapılandırma dosyanızı denetleyin. Bu hata genellikle çalışan rolünün ilk kullanımda olan meta veri veritabanını başarıyla başlatamadığını gösterir. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

