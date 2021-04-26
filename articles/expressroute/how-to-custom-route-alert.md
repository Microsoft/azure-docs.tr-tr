---
title: 'ExpressRoute: tanıtılan yollar için özel uyarıları yapılandırma'
description: Bu makalede, 1000 yol sınırına vurmasını engellemek amacıyla ExpressRoute Gateway 'ten şirket içi ağlara tanıtılan yolların sayısını izlemek için Azure Otomasyonu ve Logic Apps nasıl kullanılacağı gösterilmektedir.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: 2291d1fa7f890296c59661060f5a823d8eb194ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654399"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>Kullanıma sunulan yolları izlemek için özel uyarılar yapılandırma

Bu makale, ExpressRoute ağ geçidinden şirket içi ağlara tanıtılan yolların sayısını sürekli izlemek için Azure Otomasyonu 'nu ve Logic Apps kullanmanıza yardımcı olur. İzleme, 1000 yol sınırının (ExpressRoute-FAQ. MD # nasıl-çok-önekleri------------------------------------------

**Azure Otomasyonu** , bir *runbook*'ta depolanan özel PowerShell betiğinin yürütülmesini otomatik hale getirmenizi sağlar. Bu makalede yapılandırma kullanılırken, runbook bir veya daha fazla ExpressRoute ağ geçidini sorgulayan bir PowerShell betiği içerir. Kaynak grubu, ExpressRoute ağ geçidi adı ve şirket içinde tanıtılan ağ ön ekleri sayısını içeren bir veri kümesi toplar.

**Azure Logic Apps** , Azure Otomasyonu runbook 'u çağıran özel bir iş akışını zamanlar. Runbook 'un yürütülmesi bir iş kullanılarak yapılır. Veri toplama çalıştıktan sonra, Azure Logic Apps iş akışı verileri sınıflandırır ve bir ön tanımlama eşiğinin üzerinde veya altında bulunan ağ ön ekleri sayısına göre eşleşme ölçütlerine göre bilgileri bir hedef e-posta adresine gönderir.

### <a name="workflow"></a><a name="workflow"></a>İş akışı

Özel bir uyarı ayarlamak üç ana adıma dayanır:

1. "Farklı çalıştır" hesabı ve izinleriyle bir Otomasyon hesabı oluşturun.

2. Runbook 'lar oluşturun ve yapılandırın.

3. Otomasyon hesabını harekete alacak ve sayı bir eşikten büyükse bir uyarı e-postası gönderen bir mantıksal uygulama oluşturun (örneğin, 160).

## <a name="before-you-begin"></a><a name="before"></a>Başlamadan önce

Yapılandırmanıza başlamadan önce aşağıdaki ölçütleri karşıladığınızı doğrulayın:

* Dağıtımınızda en az bir ExpressRoute ağ geçidiniz var.

* Azure Otomasyonu 'nda [Farklı Çalıştır hesaplarının temel olarak](../automation/manage-runas-account.md) anlaşılmış olursunuz.

* [Azure Logic Apps](../logic-apps/logic-apps-overview.md)hakkında bilgi sahibisiniz.

* Azure PowerShell kullanma hakkında bilgi sahibisiniz. ExpressRoute Gateway 'de ağ öneklerini toplamak için Azure PowerShell gereklidir. Genel olarak Azure PowerShell hakkında daha fazla bilgi için [Azure PowerShell belgelerine](/powershell/azure/)bakın.

### <a name="notes-and-limitations"></a><a name="limitations"></a>Notlar ve sınırlamalar

* Bu makalede ele alınan özel uyarı, daha iyi işlem ve denetim elde etmek için bir eklentidir. ExpressRoute 'daki yerel uyarıların yerini almaz.
* ExpressRoute ağ geçitleri için veri toplama, arka planda çalışır. Çalışma zamanı beklenenden uzun olabilir. İş sıraya alma işlemini önlemek için iş akışı tekrarının doğru şekilde ayarlanması gerekir.
* Betiklerine veya ARM şablonlarına göre dağıtımlar özel alarm tetikleyicisinden daha hızlı gerçekleşebilir. Bu, 1000 yol sınırının üzerindeki ExpressRoute ağ geçidinde bulunan ağ ön ekleri sayısının artmaya neden olabilir.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>Hesapları oluşturma ve yapılandırma

Azure portal bir Otomasyon hesabı oluşturduğunuzda, otomatik olarak bir [Farklı Çalıştır](../automation/automation-security-overview.md#run-as-accounts) hesabı oluşturulur. Bu hesap aşağıdaki eylemleri gerçekleştirir:

* Otomatik olarak imzalanan bir sertifika ile Azure Active Directory (Azure AD) uygulaması oluşturur. Farklı çalıştır hesabının kendisi, her yıl varsayılan olarak yenilenmesi gereken bir sertifikaya sahiptir.

* Azure AD 'de uygulama için bir hizmet sorumlusu hesabı oluşturur.

* , Kullanımdaki Azure aboneliğine katkıda bulunan rolünü (Azure RBAC) atar. Bu rol runbook 'ları kullanarak Azure Resource Manager kaynaklarını yönetir.

Bir Otomasyon hesabı oluşturmak için ayrıcalıkların ve izinlerinizin olması gerekir. Bilgi için, bkz. [Otomasyon hesabı oluşturmak için gereken izinler](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account).

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. bir Otomasyon hesabı oluşturun

Farklı Çalıştır izinleriyle bir Otomasyon hesabı oluşturun. Yönergeler için bkz. [Azure Otomasyonu hesabı oluşturma](../automation/automation-quickstart-create-account.md).

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Otomasyon hesabı ekle" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. Farklı Çalıştır hesabına rol atama

Varsayılan olarak, **katılımcı** rolü, **Farklı Çalıştır** hesabınız tarafından kullanılan hizmet sorumlusuna atanır. Hizmet sorumlusuna atanmış varsayılan rolü tutabilir veya [yerleşik bir rol](../role-based-access-control/built-in-roles.md) (örneğin, okuyucu) veya [özel bir rol](../active-directory/roles/custom-create.md)atayarak izinleri kısıtlayabilirsiniz.

 Farklı Çalıştır hesabınız tarafından kullanılan hizmet sorumlusuna atanacak rolü öğrenmek için aşağıdaki adımları kullanın:

1. Otomasyon hesabınıza gidin. **Hesap ayarları**' na gidin ve **Farklı Çalıştır hesapları**' nı seçin.

2. Kullanılmakta olan rol tanımını görüntülemek için **Roller** ' i seçin.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="Rol atama":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Runbook 'lar oluşturma ve yapılandırma

### <a name="1-install-modules"></a><a name="install-modules"></a>1. modülleri yükler

Azure Otomasyonu runbook 'larda PowerShell cmdlet 'lerini çalıştırmak için birkaç ek Azure PowerShell az modül yüklemeniz gerekir. Modülleri yüklemek için aşağıdaki adımları kullanın:

1. Azure Otomasyonu hesabınızı açın ve **modüller**' e gidin.

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="Modüllere git":::

2. Galeride arama yapın ve şu modülleri içeri aktarın: **az. Accounts**, **az. Network**, **az. Automation** ve **az. Profile**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="Modülleri arama ve içeri aktarma" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. runbook oluşturma

1. PowerShell runbook 'unuzu oluşturmak için Otomasyon hesabınıza gidin. **Işlem Otomasyonu** altında **runbook 'lar** kutucuğunu seçin ve ardından **runbook oluştur**' u seçin.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Runbook oluşturma.":::

2. Runbook 'u oluşturmak için **Oluştur** ' u seçin.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="Oluştur ' u seçin.":::

3. Yeni oluşturulan runbook 'u seçin ve ardından **Düzenle**' yi seçin.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Runbook’u düzenleme":::

4. **Düzenle**' de PowerShell betiğini yapıştırın. [Örnek betik](#script) , bir veya daha fazla kaynak grubunda ExpressRoute ağ geçitlerini izlemek için değiştirilebilir ve kullanılabilir.

   Örnek betikte aşağıdaki ayarlara dikkat edin:

    * Dizi **$rgList** , ExpressRoute ağ geçitleriyle kaynak gruplarının listesini içerir. Liste tabanlı ExpressRoute ağ geçitlerini özelleştirebilirsiniz.
    * Değişken **$thresholdNumRoutes** , bir ExpressRoute ağ geçidinden şirket içi ağlara tanıtılan ağ önekleri sayısı eşiğini tanımlar.

#### <a name="example-script"></a><a name="script"></a>Örnek betik

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. runbook 'u kaydetme ve yayımlama

1. Runbook 'un taslak kopyasını kaydetmek için **Kaydet** ' i seçin.
2. Runbook 'u Otomasyon hesabında runbook 'un resmi sürümü olarak yayımlamak için **Yayımla** ' yı seçin.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Runbook 'u kaydedin ve yayımlayın.":::

PowerShell betiğini çalıştırdığınızda bir değer listesi toplanır:
 
* Kaynak grubu

* ExpressRoute ağ geçidi adı

* İlk BGP eşinin IP adresi (peer1)

* İkinci BGP eşinin IP adresi (peer2)

* ExpressRoute ağ geçidinden ilk BGP eşine tanıtılan ağ önekleri sayısı (peer1)

* ExpressRoute ağ geçidinden ikinci BGP eşine tanıtılan ağ önekleri sayısı (peer2)

* Timestamp

* Durum, sınıflandırılan:

  * Yolların sayısı bir eşik değerinden küçükse ' Tamam '
  * Bir eşik değeri üzerindeyse yolların sayısı ' ALERT '
  * İki BGP eşine tanıtılan ağ öneki sayısı farklıysa ' uyarı '

* Durum ayrıntılı açıklaması için uyarı iletisi (Tamam, uyarı, uyarı)

PowerShell betiği, toplanan bilgileri bir JSON çıktısına dönüştürür. Runbook, bilgileri istemciye iletmek için çıkış akışı olarak PowerShell cmdlet 'i [yazma çıkışı](/powershell/module/Microsoft.PowerShell.Utility/Write-Output)  kullanır.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. runbook 'u doğrulama

Runbook oluşturulduktan sonra doğrulanması gerekir. **Başlat** ' ı seçin ve farklı iş akışları için çıktıyı ve hataları kontrol edin.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Runbook 'u doğrulama" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>Mantıksal uygulama oluşturma ve yapılandırma

Azure Logic Apps tüm koleksiyon ve eylem işlemlerinin Orchestrator 'ı. Aşağıdaki bölümlerde, Logic App kullanarak bir iş akışı oluşturursunuz.

### <a name="workflow"></a>İş akışı

Bu iş akışında, ExpressRoute ağ geçitlerini düzenli olarak izleyen bir mantıksal uygulama oluşturacaksınız. Yeni öğeler varsa, mantıksal uygulama her öğe için bir e-posta gönderir. İşlemi tamamladığınızda, mantıksal uygulamanız bu yüksek düzeyli iş akışı gibi görünür:

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Logic Apps iş akışı":::

### <a name="1-create-a-logic-app"></a>1. mantıksal uygulama oluşturma

**Mantıksal uygulama Tasarımcısı**' nda **boş mantıksal uygulama** şablonunu kullanarak bir mantıksal uygulama oluşturun. Adımlar için bkz. [oluşturma Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app).

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="Boş şablon":::

### <a name="2-add-a-trigger"></a>2. tetikleyici ekleme

Her mantıksal uygulama bir tetikleyici tarafından başlatılır. Bir tetikleyici, belirli bir olay gerçekleştiğinde veya belirli bir koşul karşılandığında ateşlenir. Tetikleyici her tetiklendiğinde Azure Logic Apps altyapısı, iş akışınızı başlatan ve çalıştıran bir mantıksal uygulama örneği oluşturur.

Önceden tanımlama zaman çizelgesine dayanan bir mantıksal uygulamayı düzenli olarak çalıştırmak için, iş akışınıza yerleşik **yinelenme: Schedule** ekleyin. Arama kutusuna **Schedule** yazın. **Tetikleyiciler**’i seçin. Tetikleyiciler listesinden **yineleme zamanlaması**' nı seçin.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="Yinelenme: zamanlama":::

Yineleme zamanlaması tetikleyicisinde, zaman dilimini ve bu iş akışını yinelemek için bir yinelenme belirleyebilirsiniz. Aralık ve sıklık özelliği, mantıksal uygulamanızın tetikleyicisi için zamanlamayı tanımlar. Makul bir en düşük yinelenme sıklığı oluşturmak için aşağıdaki faktörleri göz önünde bulundurun:

* Automation runbook 'taki PowerShell betiği tamamlananı sürer. Çalışma zamanı, izlenecek ExpressRoute ağ geçitlerinin sayısına bağlıdır. Çok kısa yinelenme sıklığı, iş sıraya alınmasına neden olur.

* PowerShell betiği arka planda bir iş olarak çalışır. Hemen başlamaz; bir değişken gecikmeden sonra çalışır.

* Azure ExpressRoute ağ geçitlerinde çok kısa bir yinelenme sıklığı gereksiz yük oluşturacak.

İş akışı yapılandırmasının sonunda, iş akışını birkaç kez çalıştırarak ve sonra **çalıştırma geçmişinde** sonucu doğrulayarak yinelenme sıklığının tutarlılığını kontrol edebilirsiniz.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="Ekran görüntüsü yineleme aralığı ve sıklık değerlerini gösterir." lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. iş oluşturma

Mantıksal uygulama, bağlayıcılar ve diğer uygulamalara, hizmetlere ve platforma erişir. Bu iş akışındaki sonraki adım, daha önce tanımlanmış olan Azure Otomasyonu hesabına erişmek için bir bağlayıcı seçdir.

1. **Logic Apps tasarımcısında**, **yinelenme** altında **yeni adım**' ı seçin. **Eylem Seç** ' in altında ve arama kutusuna **Tümü**' nü seçin.
2. Arama kutusuna **Azure Otomasyonu** ve arama yazın. **Iş oluştur**' u seçin. **Oluşturma işi** , daha önce oluşturulmuş Otomasyon Runbook 'unu başlatmak için kullanılacaktır.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="İş oluşturma":::

3. Hizmet sorumlusu kullanarak oturum açın. Mevcut bir hizmet sorumlusunu kullanabilir veya yeni bir tane oluşturabilirsiniz. Yeni bir hizmet sorumlusu oluşturmak için, [kaynaklara erişebilen bir Azure AD hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md)konusuna bakın. **Hizmet sorumlusu Ile Bağlan**' ı seçin.

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="' Hizmet sorumlusu ile bağlan ' eylemi vurgulanmış şekilde ' yinelenme ' bölümünü gösteren ekran görüntüsü.":::

4. Bir **bağlantı adı** yazın, **istemci KIMLIĞINIZI** (uygulama kimliği), **ISTEMCI gizli** anahtarını ve **kiracı kimliğinizi** ekleyin. Ardından **Oluştur**’u seçin.

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="Hizmet sorumlusu ile bağlanma":::

5. **Iş oluştur** sayfasında, hizmet sorumlusu Otomasyon hesabını barındıran **kaynak grubunda** "okuyucu" rolüne ve **Otomasyon hesabında**"Otomasyon işi operatörü" ' ne sahip olmalıdır. Ayrıca, **runbook adını** yeni bir parametre olarak eklediğinizi doğrulayın.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="Ekran görüntüsünde, runbook adını doğrulayabileceğiniz Tekrardaki iş değeri oluşturma işlemi gösterilir." lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. iş çıktısını alın

1. **Yeni adım**'ı seçin. "Azure Otomasyonu" araması yapın. **Eylemler** listesinden **iş çıktısını al**' ı seçin.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="İş çıkışı alma":::

2. **İş çıkışını al** sayfasında, Otomasyon hesabına erişmek için gereken bilgileri belirtin. Kullanmak istediğiniz **aboneliği, kaynak grubunu** ve **Otomasyon hesabını** seçin. **Iş kimliği** kutusunun içine tıklayın. **Dinamik içerik** listesi GÖRÜNDÜĞÜNDE **iş kimliği**' ni seçin.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="İş kimliği" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. JSON 'ı ayrıştırma

' Azure Otomasyonu oluşturma işi eyleminden (önceki adımlar) çıktıda bulunan bilgiler bir JSON nesnesi oluşturur. Logic Apps **AYRıŞTıRMA JSON** , özelliklerden ve BUNLARıN değerlerinin JSON içeriğindeki Kullanıcı dostu belirteçler oluşturmak için yerleşik bir eylemdir. Daha sonra bu özellikleri iş akışınızda kullanabilirsiniz.

1. Eylem ekleyin. **İş çıkışı al->eylemi** altında **yeni adım**' ı seçin.
2. **Eylem seçin** arama kutusuna, bu eylemi sunan bağlayıcılar aramak için "JSON ayrıştırma" yazın. **Eylemler** listesinde, kullanmak istediğiniz veri IŞLEMLERI için **JSON 'u Ayrıştır** eylemini seçin.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="JSON Ayrıştırma":::

3. **İçerik** kutusunun içine tıklayın. Dinamik içerik listesi göründüğünde **içerik**' i seçin.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="Ekran görüntüsünde Içerik seçili olan JSON ayrıştırma iletişim kutusu gösterilir." lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. JSON ayrıştırılırken bir şema olması gerekir. Şema, Automation runbook 'un çıktısı kullanılarak oluşturulabilir. Yeni bir Web tarayıcı oturumu açın, Otomasyon Runbook 'unu çalıştırın ve çıktıyı alın. **JSON veri işlemleri eylemini Logic Apps** döndürün. Sayfanın en altında, **şema oluşturmak için örnek yük kullan**' ı seçin.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="Şema oluşturmak için örnek yük kullanma":::

5. **Örnek BIR JSON yükü girin veya yapıştırın**, Automation runbook 'un çıkışını yapıştırın ve **bitti**' yi seçin.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="Örnek yükü Yapıştır" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. Bir şema, JSON giriş yükü ayrıştırılmasından otomatik olarak oluşturulur.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="Şema oluştur" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. bir değişken tanımlayın ve başlatın

İş akışının bu adımında, e-posta ile alarm göndermek için bir koşul oluşturacağız. E-posta gövdesi iletisinde esnek ve özel bir biçimlendirme için, iş akışında bir yardımcı değişken tanıtılmıştır.

1. **İş çıkışı al eyleminin** altında **yeni adım**' ı seçin. Arama kutusunda **değişkenleri** bulun ve seçin.

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="Ekran görüntüsü, arama kutusunda değişkenle ve seçilen değişkenlerde bir eylem Seç iletişim kutusunu gösterir.":::

2. **Eylemler** listesinden **değişkeni Başlat** eylemini seçin.

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="Değişkenleri Başlat":::

3. Değişkenin adını belirtin. **Tür** için **dize**' yi seçin. Değişkenin **değeri** daha sonra iş akışında atanır.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="Ekran görüntüsü, bir ad, tür ve değer girebileceğiniz başlatma değişkeniyle ilişkili JSON 'u gösterir." lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. bir "for each" eylemi oluşturun

JSON bir kez ayrıştırıldıktan sonra **JSON veri Işlemini Ayrıştır** eylemi içeriği *gövde* çıktısında depolar. Çıktıyı işlemek için dizideki her öğe için bir veya daha fazla eylemi yinelenen bir "for each" döngüsü oluşturabilirsiniz.

1. **Değişken Başlat** altında **Eylem Ekle**' yi seçin. Arama kutusuna filtreniz olarak "for each" yazın.

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="Ekran görüntüsü, arama kutusu ve Denetim seçili her biri için bir eylem Seç iletişim kutusunu gösterir.":::

2. **Eylemler** listesinden **her denetim için** eylemi seçin.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="Her denetim için":::

3. **Önceki adımlardan bir çıkış seçin** metin kutusunda tıklayın. **Dinamik içerik** listesi göründüğünde, ayrıştırılmış JSON 'dan çıktı olan **gövdeyi** seçin.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="Ekran görüntüsü, önceki adımlardan bir çıkış seçin metin kutusunda bulunan her biri Için ile ilişkili başlatılmış değişkeni gösterir.":::

4. Her JSON gövdesi öğesi için bir koşul ayarlamak istiyoruz. Eylem grubundan **Denetim**' i seçin.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="Denetim":::

5. **Eylemler** listesinde **koşul denetimi**' ni seçin. Condition-Control bir denetim yapısıdır, iş akışındaki verileri belirli değerler veya alanlarla karşılaştırır. Daha sonra, verilerin koşulu karşılayıp karşılamadığını temel alarak çalışan farklı eylemler belirtebilirsiniz.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Koşul denetimi":::

6. **Koşul** kökü eyleminde, mantıksal işlemi **veya** olarak değiştirin.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Veya" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. İki BGP eşine bir ExpressRoute ağ geçidinin tanıtan ağ önekleri sayısı değerini denetleyin. Yol sayısı, "numRoutePeer1" ve "numRoutePeer2" içinde **dinamik içerikte** kullanılabilir. Değer kutusuna **numRoutePeer1** değerini yazın.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="numRoutesPeer1":::

8. Koşulunuz için başka bir satır eklemek için **Ekle-> satır ekle**' yi seçin. İkinci kutuda, **dinamik Içerikten** **numRoutePeer2**' ı seçin.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="numRoutesPeer2":::

9. NumRoute1 veya numRoute2 gibi iki dinamik değişkenden biri eşikten büyük olduğunda Logic Condition değeri true 'dur. Bu örnekte, eşik 800 80 (en büyük değer olan 1000) ' e sabitlenmiştir. Eşik değerini gereksinimlerinize uyacak şekilde değiştirebilirsiniz. Tutarlılık için, bu değer runbook PowerShell betiğinde kullanılan değer olmalıdır.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="Mantıksal koşul":::

10. **Doğru ise**, uyarıyı e-posta ile göndermek için eylemleri biçimlendirin ve oluşturun. * * Bir eylem seçin, sonra **değişkenleri** arayın ve seçin.

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="True ise":::

11. Değişkenler ' de **Eylem Ekle**' yi seçin. **Eylemler** listesinde **değişkeni ayarla**' yı seçin.

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="' Actions ' sekmesi seçili ve ' set variable ' vurgulanmış olan ' Variables ' bölümünün ekran görüntüsü.":::

12. **Ad**' da, daha önce oluşturduğunuz **emailbody** adlı değişkeni seçin. **Değer** için, uyarı e-postasını biçimlendirmek IÇIN gereken HTML betiğini yapıştırın. JSON gövdesinin değerlerini dahil etmek için **dinamik içeriği** kullanın. Bu ayarları yapılandırdıktan sonra sonuç, **emailbody** değişkeninin, uyarı ile ilgili tüm bilgileri HTML biçiminde içermasıdır.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="Değişken ayarla":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. e-posta bağlayıcısını ekleyin

Logic Apps çok sayıda e-posta Bağlayıcısı sağlar. Bu örnekte, uyarıyı e-posta ile göndermek için bir Outlook Bağlayıcısı ekleyeceğiz. **Değişken ayarla** altında **Eylem Ekle**' yi seçin. **Eylem seçin** bölümünde, arama kutusuna "e-posta gönder" yazın.

1. **Office 365 Outlook**' u seçin.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="E-posta gönder":::

2. **Eylemler** listesinde, **e-posta gönder (v2)** seçeneğini belirleyin.

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="E-posta gönder (v2)":::

3. Office 365 Outlook bağlantısı oluşturmak için oturum açın.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="Oturum aç":::

4. **Gövde** alanında **dinamik içerik Ekle**' ye tıklayın. Dinamik içerik panelinden, **emailbody** değişkenini ekleyin. **Konuyu** **ve alanları** doldurun.

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="Gövde":::

5. **E-posta gönder (v2)** eylemi iş akışı kurulumunu tamamlar.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="E-posta v2 gönder" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. iş akışı doğrulama

Son adım iş akışı doğrulamadır. **Logic Apps genel bakış** bölümünde **tetikleyiciyi Çalıştır**' ı seçin. **Yinelenme** seçin. İş akışı, **çalışma geçmişinde** izlenebilir ve doğrulanabilir.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="Tetikleyiciyi Çalıştır":::

## <a name="next-steps"></a>Sonraki adımlar

İş akışını özelleştirme hakkında daha fazla bilgi edinmek için bkz. [Azure Logic Apps](../logic-apps/logic-apps-overview.md).
