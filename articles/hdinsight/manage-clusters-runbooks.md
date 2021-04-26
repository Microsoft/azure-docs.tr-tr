---
title: "Öğretici: Azure Otomasyonu runbook 'larını kullanarak kümeler oluşturma-Azure HDInsight"
description: Azure Otomasyonu runbook 'ları kullanarak bulutta çalışan betiklerle Azure HDInsight kümeleri oluşturmayı ve silmeyi öğrenin.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 5eb0f353579233041bb5ccba46de2549ada7e9b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864797"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Öğretici: Azure Otomasyonu ile Azure HDInsight kümeleri oluşturma

Azure Otomasyonu, bulutta çalışan ve Azure kaynaklarını isteğe bağlı olarak veya bir zamanlamaya göre yöneten betikler oluşturmanızı sağlar. Bu makalede, Azure HDInsight kümelerini oluşturmak ve silmek için PowerShell runbook 'ların nasıl oluşturulacağı açıklanır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * HDInsight ile etkileşim kurmak için gereken modülleri yükler.
> * Küme oluşturma sırasında gereken kimlik bilgilerini oluşturun ve saklayın.
> * HDInsight kümesi oluşturmak için yeni bir Azure Otomasyonu runbook 'u oluşturun.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Mevcut bir [Azure Otomasyonu hesabı](../automation/automation-quickstart-create-account.md).
* Küme depolama alanı olarak kullanılacak mevcut bir [Azure depolama hesabı](../storage/common/storage-account-create.md).

## <a name="install-hdinsight-modules"></a>HDInsight modüllerini yükler

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Azure Otomasyonu hesaplarınızı seçin.
1. **Paylaşılan kaynaklar** altında **modüller Galerisi** ' ni seçin.
1. Kutuya **Azurerd. Profile** yazın ve aramak için ENTER tuşuna basın. Kullanılabilir arama sonucunu seçin.
1. **Azurerd. Profile** ekranında **içeri aktar**' ı seçin. Azure modüllerini güncelleştirmek için kutuyu işaretleyin ve ardından **Tamam**' ı seçin.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermprofile-module.png" alt-text="Azurerd. Profile modülünü içeri aktarma" border="false":::

1. **Paylaşılan kaynaklar** altında **modüller Galerisi** ' ni seçerek modüller galerisine dönün.
1. **HDInsight** yazın. **Azurerd. HDInsight** öğesini seçin.

    :::image type="content" source="./media/manage-clusters-runbooks/browse-modules-hdinsight.png" alt-text="HDInsight modüllerine gözatamıyorum" border="true":::

1. **Azurerd. HDInsight** panelinde **Içeri aktar** ve **Tamam**' ı seçin.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermhdinsight-module.png" alt-text="Azurerd. HDInsight modülünü içeri aktarma" border="true":::

## <a name="create-credentials"></a>Kimlik bilgileri oluştur

1. **Paylaşılan kaynaklar** altında **kimlik bilgileri**' ni seçin.
1. **Kimlik bilgisi ekle**' yi seçin.
1. **Yeni kimlik bilgileri** paneline gerekli bilgileri girin. Bu kimlik bilgisi, ambarı 'nda oturum açma olanağı sağlayacak şekilde küme parolasını deposıdır.

    | Özellik | Değer |
    | --- | --- |
    | Ad | `cluster-password` |
    | Kullanıcı adı | `admin` |
    | Parola | `SECURE_PASSWORD` |
    | Parolayı onayla | `SECURE_PASSWORD` |

1. **Oluştur**’u seçin.
1. `ssh-password`Kullanıcı adı `sshuser` ve seçtiğiniz bir parolayla aynı işlemi tekrarlayın. **Oluştur**’u seçin. Bu kimlik bilgisi, kümenizin SSH parolasını deposıdır.

    :::image type="content" source="./media/manage-clusters-runbooks/create-credentials.png" alt-text="kimlik bilgisi oluşturma" border="true":::

## <a name="create-a-runbook-to-create-a-cluster"></a>Küme oluşturmak için Runbook oluşturma

1. **Işlem Otomasyonu** altında **runbook 'ları** seçin.
1. **Runbook oluştur**' u seçin.
1. **Runbook oluştur** panelinde, runbook için gibi bir ad girin `hdinsight-cluster-create` . **Runbook türü** açılan listesinden **PowerShell** ' i seçin.
1. **Oluştur**’u seçin.

    :::image type="content" source="./media/manage-clusters-runbooks/create-runbook.png" alt-text="Runbook oluştur" border="true":::

1. **PowerShell runbook 'Unu Düzenle** ekranına aşağıdaki kodu girin ve **Yayımla**' yı seçin:

    :::image type="content" source="./media/manage-clusters-runbooks/publish-runbook.png" alt-text="Runbook 'u Yayımla" border="true":::

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Kümeyi silmek için Runbook oluşturma

1. **Işlem Otomasyonu** altında **runbook 'ları** seçin.
1. **Runbook oluştur**' u seçin.
1. **Runbook oluştur** panelinde, runbook için gibi bir ad girin `hdinsight-cluster-delete` . **Runbook türü** açılan listesinden **PowerShell** ' i seçin.
1. **Oluştur**’u seçin.
1. **PowerShell runbook 'Unu Düzenle** ekranına aşağıdaki kodu girin ve **Yayımla**' yı seçin:

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Runbook 'Ları yürütme

### <a name="create-a-cluster"></a>Küme oluşturma

1. **Işlem Otomasyonu** altında **runbook 'ları** seçerek Otomasyon hesabınızın runbook 'larının listesini görüntüleyin.
1. `hdinsight-cluster-create`Küme oluşturma runbook 'unuzu oluştururken kullandığınız adı seçin.
1. Runbook 'u hemen yürütmek için **Başlat** ' ı seçin. Ayrıca, runbook 'ları düzenli aralıklarla çalışacak şekilde zamanlayabilirsiniz. Bkz. [Azure Otomasyonu 'nda Runbook zamanlama](../automation/shared-resources/schedules.md)
1. Betik için gerekli parametreleri girin ve **Tamam**' ı seçin. Bu, **clustername** parametresinde belirttiğiniz ada sahip yeni bir HDInsight kümesi oluşturur.

    :::image type="content" source="./media/manage-clusters-runbooks/execute-create-runbook.png" alt-text="küme oluşturma runbook 'unu yürütme" border="true":::

### <a name="delete-a-cluster"></a>Küme silme

Oluşturduğunuz runbook 'u seçerek kümeyi silin `hdinsight-cluster-delete` . **Başlat**' ı seçin, **clustername** parametresini girip sselect **Tamam**' ı seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, istenmeyen ücretlerden kaçınmak için oluşturulan Azure Otomasyonu hesabını silin. Bunu yapmak için Azure portal gidin, Azure Otomasyonu hesabını oluşturduğunuz kaynak grubunu seçin, Otomasyon hesabını seçin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [HDInsight 'ta Azure PowerShell kullanarak Apache Hadoop kümelerini yönetme](hdinsight-administer-use-powershell.md)