---
title: 'Azure AD Connect eşitleme: İşletimsel görevler ve önemli noktalar | Microsoft Docs'
description: Bu konuda Azure AD Connect eşitleme ve bu bileşeni çalıştırmaya hazırlanma işlemleri açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc88640cdff4f716902a80bb149913b961d40ae3
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900051"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: Hazırlama sunucusu ve olağanüstü durum kurtarma
Hazırlama modundaki bir sunucu ile, yapılandırma üzerinde değişiklik yapabilir ve sunucuyu etkin hale gelmeden önce değişiklikleri önizleyebilirsiniz. Ayrıca, bu değişiklikleri üretim ortamınızda yapmadan önce tüm değişikliklerin beklendiğini doğrulamak üzere tam içeri aktarma ve tam eşitleme çalıştırmanızı sağlar.

## <a name="staging-mode"></a>Hazırlama modu
Hazırlama modu aşağıdakiler dahil olmak üzere çeşitli senaryolar için kullanılabilir:

* Yüksek kullanılabilirlik.
* Yeni yapılandırma değişikliklerini test edin ve dağıtın.
* Yeni bir sunucu tanıtın ve eskileri yetkisini alın.

Yükleme sırasında, **hazırlama modunda**olacak sunucuyu seçebilirsiniz. Bu eylem, sunucuyu içeri aktarma ve eşitleme için etkin hale getirir, ancak herhangi bir dışarı aktarma işlemi çalıştırmaz. Hazırlama modundaki bir sunucu, yükleme sırasında bu özellikleri seçmiş olsanız bile parola eşitleme veya parola geri yazma 'yı çalıştırmıyor. Hazırlama modunu devre dışı bıraktığınızda sunucu dışarı aktarmayı başlatır, parola eşitlemesini etkinleştirilir ve parola geri yazma özelliğini sunar.

> [!NOTE]
> Parola karması eşitleme özelliğinin etkin olduğu bir Azure AD Connect olduğunu varsayalım. Hazırlama modunu etkinleştirdiğinizde sunucu, şirket içi AD 'den parola değişikliklerini eşitlemeyi sonlandırır. Hazırlama modunu devre dışı bıraktığınızda sunucu, son kaldığınız yerden parola değişikliklerini eşitlemeyi sürdürür. Sunucu, uzun bir süre için hazırlama modunda bırakılırsa, sunucunun zaman diliminde oluşan tüm parola değişikliklerini eşitlemesi biraz zaman alabilir.
>
>

Eşitleme Hizmeti Yöneticisi 'ni kullanarak yine de dışarı aktarmayı zorlayabilirsiniz.

Hazırlama modundaki bir sunucu Active Directory ve Azure AD 'den değişiklikleri almaya devam eder ve hata durumunda başka bir sunucunun sorumluluklarını hızlıca alabilir. Birincil sunucunuzda yapılandırma değişikliği yaparsanız, sunucuda hazırlama modunda aynı değişiklikleri yapmak sizin sorumluluğunuzdadır.

Daha eski eşitleme teknolojileri hakkında bilgi sahibi olduğunuz için, sunucunun kendi SQL veritabanı olduğundan hazırlama modu farklıdır. Bu mimari, hazırlama modu sunucusunun farklı bir veri merkezinde yer almasına izin verir.

### <a name="verify-the-configuration-of-a-server"></a>Sunucu yapılandırmasını doğrulama
Bu yöntemi uygulamak için aşağıdaki adımları izleyin:

1. [Hazırlanır](#prepare)
2. [Yapılandırma](#configuration)
3. [İçeri ve dışarı aktarma](#import-and-synchronize)
4. [Doğru](#verify)
5. [Etkin sunucuyu Değiştir](#switch-active-server)

#### <a name="prepare"></a>Hazırlama
1. Azure AD Connect yükleme, **hazırlama modu**' nu seçin ve yükleme sihirbazındaki son sayfada **Eşitlemeyi Başlat** seçimini kaldırın. Bu mod, eşitleme altyapısını el ile çalıştırmanızı sağlar.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Oturumu kapatın/oturum açın ve Başlat menüsünde **eşitleme hizmeti**' ni seçin.

#### <a name="configuration"></a>Yapılandırma
Birincil sunucuda özel değişiklikler yaptıysanız ve yapılandırmayı hazırlama sunucusuyla karşılaştırmak istiyorsanız, [Azure AD Connect Configuration belgegir](https://github.com/Microsoft/AADConnectConfigDocumenter)' i kullanın.

#### <a name="import-and-synchronize"></a>İçeri ve dışarı aktarma
1. **Bağlayıcılar**' ı seçin ve **Active Directory Domain Services**türüne sahip ilk bağlayıcıyı seçin. **Çalıştır**' a tıklayın, **tam içeri aktar**' ı seçin ve **Tamam**. Bu adımları bu türden tüm bağlayıcılar için yapın.
2. **Azure Active Directory türü (Microsoft)** olan bağlayıcıyı seçin. **Çalıştır**' a tıklayın, **tam içeri aktar**' ı seçin ve **Tamam**.
3. Sekme bağlayıcılarının hala seçili olduğundan emin olun. Tür **Active Directory Domain Services**olan her bağlayıcı için **Çalıştır**' a tıklayın, **Delta eşitleme**' yi seçin ve **Tamam**' ı tıklatın
4. **Azure Active Directory türü (Microsoft)** olan bağlayıcıyı seçin. **Çalıştır**' a tıklayın, **Delta eşitlemesi**ve **Tamam**' ı seçin.

Artık Azure AD 'ye ve şirket içi AD 'ye dışarı aktarma değişiklikleri hazırladınız (Exchange karma dağıtımı kullanıyorsanız). Sonraki adımlar, dizinlere dışa aktarma işlemine başlamadan önce nelerin değişmekte olduğunu incelemenizi sağlar.

#### <a name="verify"></a>Doğrula
1. Bir komut istemi başlatın ve şuraya gidin`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Çalıştırın: `csexport "Name of Connector" %temp%\export.xml /f:x`Bağlayıcının adı, eşitleme hizmeti ' nde bulunabilir. Azure AD için "contoso.com – AAD" benzeri bir ada sahiptir.
3. Çalıştırın: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`% TEMP% adlı, Export. csv adlı, Microsoft Excel 'de incelenebilir bir dosyanız var. Bu dosya, verilmek üzere olan tüm değişiklikleri içerir.
4. Veri veya yapılandırmada gerekli değişiklikleri yapın ve dışarı aktarılacak değişiklikler beklenene kadar bu adımları yeniden çalıştırın (Içeri ve dışarı ve Doğrula).

**Export. csv dosyasını anlama** Dosyanın çoğu kendi kendine açıklayıcıdır. İçeriği anlamak için bazı kısaltmalar:
* OMODT – nesne değiştirme türü. Nesne düzeyindeki işlemin bir Add, Update veya delete olup olmadığını gösterir.
* AMODT – öznitelik değiştirme türü. Öznitelik düzeyindeki işlemin bir Add, Update veya delete olup olmadığını gösterir.

**Ortak tanımlayıcıları al** Export. csv dosyası dışarı aktarılmek üzere olan tüm değişiklikleri içerir. Her satır, bağlayıcı alanındaki bir nesnenin değişikliğine karşılık gelir ve nesne DN özniteliğiyle tanımlanır. DN özniteliği, bağlayıcı alanındaki bir nesneye atanan benzersiz bir tanıtıcıdır. Export. csv dosyasında analiz edilecek çok sayıda satır/değişiklik olduğunda, değişikliklerin yalnızca DN özniteliğine göre hangi nesneleri olduğunu anlamak zor olabilir. Değişiklikleri çözümleme işlemini basitleştirmek için csanalyzer. ps1 PowerShell betiğini kullanın. Betik, nesnelerin ortak tanımlayıcılarını (örneğin displayName, userPrincipalName) alır. Betiği kullanmak için:
1. PowerShell betiğini [CSAnalyzer](#appendix-csanalyzer) bölümünden adlı `csanalyzer.ps1`bir dosyaya kopyalayın.
2. Bir PowerShell penceresi açın ve PowerShell betiğini oluşturduğunuz klasöre gidin.
3. Şunu çalıştırın `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`:.
4. Artık, Microsoft Excel 'de incelenebilir **processedusers1. csv** adlı bir dosyanız vardır. Dosyanın DN özniteliğinden ortak tanımlayıcılara (örneğin, displayName ve userPrincipalName) bir eşleme sağladığını unutmayın. Şu anda, verilmek üzere olan gerçek öznitelik değişikliklerini içermez.

#### <a name="switch-active-server"></a>Etkin sunucuyu Değiştir
1. Şu anda etkin olan sunucuda, sunucuyu devre dışı bırakın (DirSync/FIM/Azure AD Eşitleme), Azure AD 'ye aktarılmaması veya hazırlama modunda (Azure AD Connect) ayarlanmaması gerekir.
2. Yükleme sihirbazını sunucusunda **hazırlama modunda** çalıştırın ve **hazırlama modunu**devre dışı bırakın.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Olağanüstü durum kurtarma
Uygulama tasarımının bir parçası, eşitleme sunucusunu kaybedeceğinizi bir olağanüstü durum olması durumunda ne yapılacağını planlıyor. Kullanılacak farklı modeller vardır ve bunlardan biri, aşağıdakiler dahil olmak üzere çeşitli etkenlere bağlıdır:

* Kapalı kalma süresi boyunca Azure AD 'de nesnelerde değişiklik yapamayan tolerans nedir?
* Parola eşitleme kullanıyorsanız, kullanıcılar şirket içinde değişmeleri için Azure AD 'de eski parolayı kullanmak zorunda olduklarını kabul eder mi?
* Parola geri yazma gibi gerçek zamanlı işlemlere yönelik bir bağımlılığa sahip misiniz?

Bu soruların ve kuruluşunuzun ilkesinin yanıtlarına bağlı olarak, aşağıdaki stratejilerden biri uygulanabilir:

* Gerektiğinde yeniden derleyin.
* **Hazırlama modu**olarak bilinen, yedek bir bekleme sunucusuna sahip olmak.
* Sanal makineleri kullanın.

Yerleşik SQL Express veritabanını kullanmıyorsanız, [SQL yüksek kullanılabilirlik](#sql-high-availability) bölümünü de gözden geçirmeniz gerekir.

### <a name="rebuild-when-needed"></a>Gerektiğinde yeniden derle
Uygun bir strateji gerektiğinde sunucu yeniden oluşturmayı planlıyor. Genellikle, eşitleme altyapısını yükleme ve ilk içeri aktarma ve eşitleme birkaç saat içinde tamamlanabilir. Kullanılabilir bir yedek sunucu yoksa, eşitleme altyapısını barındırmak için geçici olarak bir etki alanı denetleyicisi kullanmak mümkündür.

Eşitleme altyapısı sunucusu nesneler hakkında herhangi bir durum depolamaz, böylece veritabanı Active Directory ve Azure AD 'deki verilerden yeniden oluşturulabilir. **Sourcetutturucu** özniteliği, şirket içi ve buluttan nesneleri birleştirmek için kullanılır. Sunucuyu şirket içinde ve bulutta var olan nesnelerle yeniden yapılandırırsanız, eşitleme altyapısı yeniden yükleme sırasında bu nesnelerle birlikte eşleşir. Belge ve kaydetmeniz gereken işlemler, sunucuda yapılan, filtreleme ve eşitleme kuralları gibi yapılandırma değişiklerdir. Eşitlemeye başlamadan önce bu özel yapılandırmaların yeniden oluşturulması gerekir.

### <a name="have-a-spare-standby-server---staging-mode"></a>Yedek bekleme sunucu hazırlama moduna sahip
Daha karmaşık bir ortamınız varsa, bir veya daha fazla bekleme sunucusuna sahip olmanız önerilir. Yükleme sırasında, bir sunucunun **hazırlama modunda**olmasını sağlayabilirsiniz.

Daha fazla bilgi için bkz. [hazırlama modu](#staging-mode).

### <a name="use-virtual-machines"></a>Sanal makineleri kullanma
Ortak ve desteklenen bir yöntem, eşitleme altyapısını bir sanal makinede çalıştırmaya yönelik bir yöntemdir. Konakta bir sorun olması durumunda, eşitleme altyapısı sunucusuyla görüntü başka bir sunucuya geçirilebilir.

### <a name="sql-high-availability"></a>SQL yüksek kullanılabilirlik
Azure AD Connect ile birlikte gelen SQL Server Express kullanmıyorsanız, SQL Server için yüksek kullanılabilirlik de dikkate alınmalıdır. Desteklenen yüksek kullanılabilirlik çözümleri SQL kümeleme ve AOA (Always on kullanılabilirlik grupları) içerir. Desteklenmeyen çözümler yansıtma içerir.

Sürüm 1.1.524.0 ' deki Azure AD Connect SQL AOA desteği eklenmiştir. Azure AD Connect yüklemeden önce SQL AOA 'i etkinleştirmeniz gerekir. Yükleme sırasında, sağlanan SQL örneğinin SQL AOA için etkinleştirilip etkinleştirilmediğini algılar Azure AD Connect. SQL AOA etkinleştirilmişse, SQL AOA zaman uyumlu çoğaltma veya zaman uyumsuz çoğaltma kullanacak şekilde yapılandırıldıysa Azure AD Connect daha fazla şekil bulabilirsiniz. Kullanılabilirlik grubu dinleyicisini ayarlarken RegisterAllProvidersIP özelliğini 0 olarak ayarlamanız önerilir. Bunun nedeni, Azure AD Connect Şu anda SQL 'e bağlanmak için SQL Native Client kullandığından SQL Native Client MultiSubNetFailover özelliğinin kullanımını desteklemez.

## <a name="appendix-csanalyzer"></a>Ek CSAnalyzer
Bu betiği nasıl [](#verify) kullanacağınızı öğrenmek için bölümüne bakın.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as a CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Sonraki adımlar
**Genel Bakış konuları**  

* [Azure AD Connect eşitlemesi: Eşitlemeyi anlama ve özelleştirme](how-to-connect-sync-whatis.md)  
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)  
