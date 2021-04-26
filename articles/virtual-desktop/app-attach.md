---
title: Windows sanal masaüstü 'Nü yapılandırma MALTı uygulaması PowerShell betikleri iliştirme-Azure
description: Windows sanal masaüstü için MSIX uygulama iliştirme için PowerShell betikleri oluşturma.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 43a8cb00804927784982999db13ee193c34f55ca
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835389"
---
# <a name="create-powershell-scripts-for-msix-app-attach"></a>MSIX uygulama iliştirme için PowerShell betikleri oluşturma

Bu konu başlığı altında, MSIX uygulama iliştirme için PowerShell betikleri ayarlama işleminde size yol gösterilir.

## <a name="install-certificates"></a>Sertifikaları yükleme

Ana bilgisayar havuzundaki tüm oturum konaklarına sertifika yükleyerek, MSIX uygulama iliştirme paketinizdeki uygulamaları barındırabilirsiniz.

Uygulamanız ortak güvenilir olmayan ya da kendinden imzalı bir sertifika kullanıyorsa, bunu nasıl yükleyeceksiniz:

1. Pakete sağ tıklayın ve **Özellikler**' i seçin.
2. Görüntülenen pencerede **dijital imzalar** sekmesini seçin. Sekmedeki listede yalnızca bir öğe olmalıdır. Öğeyi vurgulamak için bu öğeyi seçin, sonra **Ayrıntılar**' ı seçin.
3. Dijital imza ayrıntıları penceresi göründüğünde **genel** sekmesini seçin, ardından **sertifikayı görüntüle**' yi seçin ve **sertifikayı yükler**' i seçin.
4. Yükleyici açıldığında, depolama konumunuz olarak **yerel makine** ' yi seçin ve ardından **İleri**' yi seçin.
5. Yükleyici, uygulamanın cihazınızda değişiklik yapmasına izin vermek isteyip istemediğinizi isterse, **Evet**' i seçin.
6. **Tüm sertifikaları aşağıdaki depolama alanına yerleştir**' i seçin ve ardından da **Araştır**' ı seçin.
7. Sertifika deposu Seç penceresi göründüğünde, **Güvenilen Kişiler**' i seçin ve ardından **Tamam**' ı seçin.
8. **İleri** ve **son**' u seçin.

## <a name="enable-microsoft-hyper-v"></a>Microsoft Hyper-V etkinleştir

Microsoft Hyper-V etkinleştirilmesi gerekir, çünkü komut için gereklidir `Mount-VHD` ve `Dismount-VHD` taşıma için gereklidir.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Bu değişiklik, sanal makineyi yeniden başlatmanızı gerektirir.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>MSIX uygulama iliştirme için PowerShell betikleri hazırlama

MSIX uygulama iliştirme, aşağıdaki sırayla gerçekleştirilmesi gereken dört farklı aşamaya sahiptir:

1. Aşama
2. Kaydol
3. Kaydını silmek
4. Gerçekleştirilen

Her aşama bir PowerShell betiği oluşturur. Her aşama için örnek betikler [burada](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)bulunabilir.

### <a name="stage-powershell-script"></a>Aşama PowerShell betiği

PowerShell betiklerini güncelleştirmeden önce, VHD 'deki birimin birim GUID 'sine sahip olduğunuzdan emin olun. Birim GUID 'sini almak için:

1.  VHD 'nin betiği çalıştıracağınız VM 'nin içinde bulunduğu ağ payını açın.

2.  VHD 'ye sağ tıklayın ve **bağla**' yı seçin. Bu, VHD 'yi bir sürücü harfine bağlayacaktır.

3.  VHD 'yi takduktan sonra **Dosya Gezgini** penceresi açılır. Üst klasörü yakala ve **$parentFolder** değişkenini Güncelleştir

    >[!NOTE]
    >Üst klasör görmüyorsanız, MSIX 'nin doğru genişletilmediği anlamına gelir. Önceki bölümü yineleyin ve yeniden deneyin.

4.  Üst klasörü açın. Doğru şekilde genişletilmişse, paketiyle aynı ada sahip bir klasör görürsünüz. **$PackageName** değişkenini bu klasörün adıyla eşleşecek şekilde güncelleştirin.

    Örneğin, `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Bir komut istemi açın ve **mountvol** yazın. Bu komut, birimlerin ve GUID 'Lerinin bir listesini görüntüler. 2. adımda sürücü harfinin VHD 'nizi bağladığınız sürücüyle eşleşen birimin GUID 'sini kopyalayın.

    Örneğin, mountvol komutuna yönelik bu örnek çıktıda, VHD 'nizi C sürücüsüne bağladıysanız yukarıdaki değeri kopyalamak isteyeceksiniz `C:\` :

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  **$VolumeGuid** değişkenini yeni kopyaladığınız birim GUID 'si ile güncelleştirin.

7. Bir yönetici PowerShell istemi açın ve aşağıdaki PowerShell betiğini ortamınıza uygulanan değişkenlerle güncelleştirin.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>PowerShell betiğini Kaydet

Kayıt betiğini çalıştırmak için aşağıdaki PowerShell cmdlet 'lerini ortamınıza uygulanan değerlerle birlikte yer tutucu değerleriyle birlikte çalıştırın.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>PowerShell betiğini kaydını kaldırma

Bu komut dosyası için **$PackageName** yer tutucusunu, Sınadığınızı paketin adıyla değiştirin.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>PowerShell betiği önbellekten taşıma

Bu komut dosyası için **$PackageName** yer tutucusunu, Sınadığınızı paketin adıyla değiştirin. Bir üretim dağıtımında bunun en iyi şekilde kapatılmasını sağlamak için bunu çalıştırın.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>MSIX uygulama iliştirme Aracısı için benzetim betikleri ayarlama

Betikleri oluşturduktan sonra kullanıcılar el ile çalıştırabilir veya başlatma, oturum açma, oturum kapatma ve kapatma betikleri olarak otomatik olarak çalışacak şekilde ayarlayabilir. Bu komut dosyaları türleri hakkında daha fazla bilgi edinmek için bkz. [Grup İlkesi başlatma, kapatma, oturum açma ve oturum kapatma betikleri kullanma](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Bu otomatik betiklerin her biri, uygulama komut dosyalarının bir aşamasını çalıştırır:

- Başlangıç betiği, aşama betiğini çalıştırır.
- Oturum açma betiği, kayıt betiğini çalıştırır.
- Oturumu kapatma betiği, kaydı kaldırma betiğini çalıştırır.
- Kapatılma betiği, taşıma betiğini çalıştırır.

## <a name="use-packages-offline"></a>Paketleri çevrimdışı kullanma

[İş için Microsoft Store](https://businessstore.microsoft.com/) veya ağınıza veya internet 'e bağlı olmayan cihazlarda [eğitim Microsoft Store](https://educationstore.microsoft.com/) kullanıyorsanız, uygulamayı başarılı bir şekilde çalıştırmak için Microsoft Store paket lisanslarını almanız ve cihazınıza yüklemeniz gerekir. Cihazınız çevrimiçiyse ve Iş için Microsoft Store bağlanabildiğinizde, gerekli lisanslar otomatik olarak indirilmelidir, ancak çevrimdışı çalışıyorsanız, lisansları el ile ayarlamanız gerekir.

Lisans dosyalarını yüklemek için, WMI Köprüsü sağlayıcısında MDM_EnterpriseModernAppManagement_StoreLicenses02_01 sınıfını çağıran bir PowerShell betiği kullanmanız gerekir.

Lisansları çevrimdışı kullanım için ayarlama:

1. Iş için Microsoft Store uygulama paketini, lisansları ve gerekli çerçeveleri indirin. Kodlanmış ve kodlanmamış lisans dosyalarının her ikisi de gereklidir. Ayrıntılı indirme yönergeleri [burada](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)bulunabilir.
2. 3. adım için komut dosyasında aşağıdaki değişkenleri güncelleştirin:
      1. `$contentID` , kodlanmamış lisans dosyasından (. xml) ContentID değeridir. Lisans dosyasını dilediğiniz bir metin düzenleyicisinde açabilirsiniz.
      2. `$licenseBlob` , kodlanmış lisans dosyasında (. bin), lisans blobu için tüm dizedir. Kodlanmış lisans dosyasını dilediğiniz bir metin düzenleyicisinde açabilirsiniz.
3. Yönetici PowerShell isteminden aşağıdaki betiği çalıştırın. Lisans yükleme işlemini gerçekleştirmek için iyi bir yer, bir yönetici isteminden çalıştırılması gereken [hazırlama betiğinin](#stage-powershell-script) sonunda yer alan bir yerdir.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu özellik şu anda desteklenmemektedir, ancak [Windows sanal masaüstü techcommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)'de topluluğa soru sorabilirsiniz.

Windows sanal masaüstü [Geri Bildirim Hub 'ında](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)Windows sanal masaüstü geri bildirimini de bırakabilirsiniz.
