---
title: Azure Kubernetes hizmetinde hiper muhasebe doku Consortium dağıtma
description: Azure Kubernetes hizmetinde bir hiper muhasebe doku Consortium ağını dağıtma ve yapılandırma
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: 03f19d1922c011c1b5304b66488e9fa8de703bf9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478327"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Azure Kubernetes hizmetinde hiper muhasebe doku Consortium dağıtma

Azure Kubernetes Service (AKS) şablonunda hiper muhasebe dokusunu kullanarak Azure 'da bir hiper muhasebe doku Consortium ağı dağıtabilir ve yapılandırabilirsiniz.

Bu makaleyi okuduktan sonra şunları yapabilir olacaksınız:

- Hiper muhasebe dokusuna ve bir hiper muhasebe doku blok zinciri ağının yapı taşlarını oluşturan bileşenlere sahip bir çalışma bilgisine sahip olmanız gerekir.
- Üretim senaryolarınız için Azure Kubernetes hizmetinde bir hiper muhasebe doku Consortium ağını dağıtmayı ve yapılandırmayı öğrenin.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Bir Azure blok zinciri çözümü seçin

Bir çözüm şablonu kullanmayı seçmeden önce, senaryonuzu kullanılabilir Azure blok zinciri seçeneklerinin yaygın kullanım durumları ile karşılaştırın:

Seçenek | Hizmet modeli | Yaygın kullanım durumu
-------|---------------|-----------------
Çözüm şablonları | IaaS | Çözüm şablonları, tam olarak yapılandırılmış bir blok zinciri ağ topolojisi sağlamak için kullanabileceğiniz Azure Resource Manager şablonlardır. Şablonlar, blok zinciri ağ türü için Microsoft Azure işlem, ağ ve depolama hizmetleri dağıtır ve yapılandırır. Çözüm şablonları, hizmet düzeyi anlaşmadan sağlanır. Destek için [Microsoft Q&A sayfasını](/answers/topics/azure-blockchain-workbench.html) kullanın.
[Azure Blok Zinciri Hizmeti](../service/overview.md) | PaaS | Azure blok zinciri hizmeti önizlemesi, konsorsiyum blok zinciri ağlarının yönetimini, yönetimini ve yönetimini basitleştirir. PaaS, konsorsiyum yönetimi veya sözleşme ve işlem gizliliği gerektiren çözümler için Azure blok zinciri hizmetini kullanın.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS ve PaaS | Azure blok zinciri çalışma ekranı önizlemesi, iş süreçlerini ve verileri diğer kuruluşlarla paylaşmak üzere blok zinciri uygulamaları oluşturmanıza ve dağıtmanıza yardımcı olan bir Azure hizmetleri ve özellikleri koleksiyonudur. Blok zinciri çözümü için prototip yazmak üzere Azure blok zinciri ve blok zinciri uygulaması için bir kavram kanıtı kullanın. Azure blok zinciri çalışma ekranı, hizmet düzeyi anlaşmadan sağlanır. Destek için [Microsoft Q&A sayfasını](/answers/topics/azure-blockchain-workbench.html) kullanın.

## <a name="deploy-the-orderer-and-peer-organization"></a>Sipariş ve eş kuruluşu dağıtma

Başlamak için birkaç sanal makine ve standart depolama hesabı dağıtımı destekleyebilen bir Azure aboneliğine ihtiyacınız vardır. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı oluşturabilirsiniz](https://azure.microsoft.com/free/).

Hiper muhasebe doku ağ bileşenlerinin dağıtımına başlamak için [Azure Portal](https://portal.azure.com)gidin.

1. **Kaynak**  >  **blok zinciri** oluştur ' u seçin ve ardından **Azure Kubernetes hizmeti (Önizleme) ' de hiper muhasebe dokusunu** arayın.

2. **Temel bilgiler** sekmesinde proje ayrıntılarını girin.

    ![Temel bilgiler sekmesini gösteren ekran görüntüsü.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Şu ayrıntıları girin:
    - **Abonelik**: hiper muhasebe doku ağı bileşenlerini dağıtmak istediğiniz abonelik adını seçin.
    - **Kaynak grubu**: yeni bir kaynak grubu oluşturun veya var olan bir boş kaynak grubunu seçin. Kaynak grubu, şablonun bir parçası olarak dağıtılan tüm kaynakları tutacaktır.
    - **Bölge**: hiper muhasebe doku bileşenleri Için Azure Kubernetes hizmet kümesini dağıtmak istediğiniz Azure bölgesini seçin. Şablon, AKS 'in kullanılabildiği tüm bölgelerde kullanılabilir. Aboneliğinizin sanal makine (VM) kota sınırına ulaşmadığından bir bölge seçin.
    - **Kaynak ön eki**: dağıtılan kaynakların adlandırılması için bir ön ek girin. Altı karakterden kısa olmalıdır ve karakterlerin birleşimi hem sayı hem de harf içermelidir.
4. Dağıtılacak hiper muhasebe doku ağı bileşenlerini tanımlamak için **doku ayarları** sekmesini seçin.

    ![Doku ayarları sekmesini gösteren ekran görüntüsü.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Şu ayrıntıları girin:
    - **Kuruluş adı**: çeşitli veri düzlemi işlemleri Için gereken hiper muhasebe doku kuruluşunun adını girin. Kuruluş adının dağıtım başına benzersiz olması gerekir.
    - **Fabric ağ bileşeni**: ayarlamak istediğiniz blok zinciri ağ bileşenine göre hizmet veya **Eş düğümleri** **sıralama** seçeneğini belirleyin.
    - **Düğüm sayısı**: aşağıdaki iki düğüm türüdür:
        - **Sipariş hizmeti**: genel muhasebedeki işlem sıramasından sorumlu düğümler. Ağa hata toleransı sağlamak için düğüm sayısını seçin. Desteklenen sıra düğüm sayısı 3, 5 ve 7 ' dir.
        - **Eş düğümler**: bağış cihazları ve akıllı sözleşmeleri barındıran düğümler. Gereksiniminize göre 1 ila 10 düğüm seçebilirsiniz.
    - **Eş düğüm dünya durumu veritabanı**: Eş düğümleri için dünya durumu veritabanları. LevelDB, eş düğümüne katıştırılmış olan varsayılan durum veritabanıdır. Chaincode verilerini basit anahtar/değer çiftleri olarak depolar ve yalnızca anahtar, anahtar aralığı ve bileşik anahtar sorgularını destekler. Couşdb, chaincode veri değerleri JSON olarak modellendiği zaman zengin sorguları destekleyen, isteğe bağlı alternatif bir durum veritabanıdır. Bu alan, **doku ağ bileşeni** açılır listesinde **eş düğümler** ' i seçtiğinizde görüntülenir.
    - **Fabrıc CA Kullanıcı adı**: doku sertifika yetkilisi, sertifika yetkilisini barındıran bir sunucu işlemini başlatıp başlatmanıza olanak tanır. Kimlikleri ve sertifikaları yönetmenizi sağlar. Şablonun bir parçası olarak dağıtılan her bir AKS kümesi, varsayılan olarak bir doku CA Pod 'ı olacaktır. Doku CA kimlik doğrulaması için kullanılan Kullanıcı adını girin.
    - **Fabrıc CA parolası**: doku CA kimlik doğrulaması için parolayı girin.
    - **Parolayı onaylayın**: doku CA parolasını onaylayın.
    - **Sertifikalar**: doku CA 'sını başlatmak için kendi kök sertifikalarınızı kullanmak Istiyorsanız, **doku CA 'sı Için kök sertifikayı karşıya yükle** seçeneğini belirleyin. Aksi halde, doku CA varsayılan olarak otomatik olarak imzalanan sertifikalar oluşturur.
    - **Kök sertifika**: doku CA 'sının başlatılması gereken kök sertifikayı (ortak anahtar) karşıya yükleyin. . Pek biçimindeki sertifikalar desteklenir. Sertifikaların geçerli olması ve UTC saat diliminde olması gerekir.
    - **Kök sertifika özel anahtarı**: kök sertifikanın özel anahtarını karşıya yükleyin. Birleşik ortak ve özel anahtara sahip bir. pek sertifikanız varsa, bu sertifikaya de buradan yükleyin.


6. Azure Kubernetes hizmet kümesi yapılandırmasını tanımlamak için **aks kümesi ayarları** sekmesini seçin. AKS kümesinde, hiper muhasebe doku ağı bileşenlerini çalıştırmak için yapılandırılmış çeşitli yığınlar vardır. Dağıtılan Azure kaynakları şunlardır:

    - **Yapı Araçları**: hiper muhasebe doku bileşenlerini yapılandırmaktan sorumlu araçlar.
    - **Orderer/peer Pod**: hiper muhasebe doku ağının düğümleri.
    - **Proxy**: istemci uygulamalarının aks kümesiyle iletişim kurabildiği BIR NGNX proxy Pod 'si.
    - **Fabrıc CA**: doku CA 'sını çalıştıran Pod.
    - **PostgreSQL**: yapı CA kimliklerini tutan veritabanı örneği.
    - **Anahtar Kasası**: doku CA kimlik bilgilerini ve müşteri tarafından sunulan kök sertifikaları kaydetmek için dağıtılan Azure Key Vault hizmetinin örneği. Kasa, şablonun sayısını işlemek için şablon dağıtımı yeniden denemesi durumunda kullanılır.
    - **Yönetilen disk**: Azure yönetilen diskler hizmetinin, genel muhasebe ve eş düğümün dünya durumu veritabanı için kalıcı bir mağaza sağlayan örneği.
    - **Genel IP**: kümeyle iletişim kurmak için dağıtılan aks kümesinin uç noktası.

    Şu ayrıntıları girin: 

    ![Bir K S küme ayarları sekmesini gösteren ekran görüntüsü.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Kubernetes küme adı**: gerekirse aks kümesinin adını değiştirin. Bu alan, sağlanmış kaynak ön ekine göre önceden doldurulur.
    - **Kubernetes sürümü**: kümede dağıtılacak Kubernetes sürümünü seçin. **Temel bilgiler** sekmesinde seçtiğiniz bölgeye bağlı olarak, kullanılabilir desteklenen sürümler değişebilir.
    - **DNS ön eki**: aks kümesi Için bir etki alanı adı SISTEMI (DNS) ad öneki girin. Kümeyi oluşturduktan sonra kapsayıcıları yönetirken Kubernetes API 'sine bağlanmak için DNS kullanırsınız.
    - **Düğüm boyutu**: Kubernetes düğümünün boyutu için Azure 'DA bulunan VM stok tutma birimleri (SKU 'lar) listesinden seçim yapabilirsiniz. En iyi performansı elde etmek için standart DS3 v2 önerilir.
    - **Düğüm sayısı**: kümede dağıtılacak Kubernetes düğümlerinin sayısını girin. Bu düğüm sayısının **doku ayarları** sekmesinde belirtilen hiper muhasebe doku düğümlerinin sayısına eşit veya daha fazla tutulması önerilir.
    - **Hizmet sorumlusu ISTEMCI kimliği**: var olan bir hizmet SORUMLUSUNUN istemci kimliğini girin veya yeni bir tane oluşturun. AKS kimlik doğrulaması için bir hizmet sorumlusu gereklidir. [Hizmet sorumlusu oluşturma adımlarına](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal)bakın.
    - **Hizmet sorumlusu istemci parolası**: hizmet sorumlusu IÇIN İstemci kimliğinde belirtilen hizmet sorumlusunun istemci gizli anahtarını girin.
    - **İstemci parolasını onaylayın**: hizmet sorumlusu için istemci parolasını onaylayın.
    - **Kapsayıcı Izlemeyi etkinleştir**: aks izlemesini etkinleştirmek için seçin. Bu, aks günlüklerinin belirtilen Log Analytics çalışma alanına gönderim yapmasını sağlar.
    - **Log Analytics çalışma alanı**: Log Analytics çalışma alanı, izleme etkinse oluşturulan varsayılan çalışma alanı ile doldurulur.

8. **Gözden geçir ve oluştur** sekmesini seçin. Bu adım, verdiğiniz değerler için doğrulamayı tetikler.
9. Doğrulama geçtikten sonra **Oluştur**' u seçin.

    Dağıtım genellikle 10 ila 12 dakika sürer. Süre, belirtilen AKS düğümlerinin boyutuna ve sayısına bağlı olarak değişebilir.
10. Dağıtım başarılı olduktan sonra sağ üst köşedeki Azure bildirimleri aracılığıyla size bildirilir.
11. Şablon dağıtımının bir parçası olarak oluşturulan tüm kaynakları denetlemek için **kaynak grubuna git** ' i seçin. Tüm kaynak adları **temel bilgiler** sekmesinde belirtilen önek ile başlar.

## <a name="build-the-consortium"></a>Consortium oluşturma

Sıralama hizmeti ve eş düğümleri dağıttıktan sonra blok zinciri Konsorsiyumu oluşturmak için aşağıdaki adımları sırayla gerçekleştirin. Azure hiper muhasebe doku betiği (azhlf), Konsorsiyumu ayarlama, kanal oluşturma ve chaincode işlemlerini gerçekleştirme konusunda size yardımcı olur.

> [!NOTE]
> Azure hiper muhasebe yapısı (azhlf) betiği daha fazla işlevsellik sunacak şekilde güncelleştirilmiştir. Eski betiğe başvurmak istiyorsanız [GitHub 'daki Benioku dosyasına](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md)bakın. Bu betik, Azure Kubernetes Service Template sürüm 2.0.0 ve sonraki sürümlerinde hiper muhasebe dokuyla uyumludur. Dağıtımın sürümünü denetlemek için [sorun giderme](#troubleshoot)bölümündeki adımları izleyin.

> [!NOTE]
> Betik yalnızca tanıtım, geliştirme ve test senaryolarıyla ilgili yardım için sağlanır. Bu betiğin oluşturduğu kanal ve konsorsiyumun demo, dev ve test senaryolarını basitleştirecek temel hiper muhasebe doku ilkeleri vardır. Üretim Kurulumu için, yerel hiper muhasebe doku API 'Lerini kullanarak, kuruluşunuzun uyumluluk ihtiyaçlarına göre kanal/konsorsiyum hiper muhasebe doku ilkelerinin güncelleştirilmesini öneririz.


Azure hiper muhasebe doku betiğini çalıştırmaya yönelik tüm komutlar Azure Bash komut satırı arabirimi (CLı) aracılığıyla yürütülebilir. ![ ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) Azure Portal ' ın sağ üst köşesindeki Azure Kubernetes hizmet şablonu seçeneğinde Azure Cloud Shell throughÂ € ̄The Hyperledger dokusunda oturum açabilirsiniz. Komut istemine yazın `bash` ve Enter tuşunu seçerek Bash CLI 'ya geçin veya Cloud Shell araç çubuğundan **Bash** ' i seçin.

Daha fazla bilgi için bkz. [Azure Cloud Shell](../../cloud-shell/overview.md) .

![Azure Cloud Shell komutları gösteren ekran görüntüsü.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Aşağıdaki görüntüde, bir orderer organizasyonu ve bir eş kuruluş arasında bir konsorsiyum oluşturmak için adım adım işlem gösterilmektedir. Aşağıdaki bölümlerde bu adımları tamamlamaya yönelik ayrıntılı komutlar gösterilmektedir.

![Bir konsorsiyum oluşturma işleminin diyagramı.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

İlk kurulumu tamamladıktan sonra, aşağıdaki işlemleri gerçekleştirmek için istemci uygulamasını kullanın: â € ̄

- Kanal yönetimi
- Konsorsiyum yönetimi
- Chaincode yönetimi

### <a name="download-client-application-files"></a>İstemci uygulama dosyalarını indirin

İlk kurulum, istemci uygulama dosyalarını indirdir. Gerekli tüm dosya ve paketleri indirmek için aşağıdaki komutları çalıştırın:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Bu komutlar, Azure hiper muhasebe doku istemci uygulama kodunu genel GitHub deposundan, ardından tüm bağımlı NPM paketlerini yükleyerek kopyalayacaktır. Komutun başarılı bir şekilde yürütülmesi tamamlandıktan sonra, geçerli dizinde bir node_modules klasörü görebilirsiniz. Gerekli tüm paketler node_modules klasörüne yüklenir.

### <a name="set-up-environment-variables"></a>Ortam değişkenlerini ayarlama

Tüm ortam değişkenleri Azure Kaynak adlandırma kuralı ' nı izler.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Orderer kuruluşun istemcisi için ortam değişkenlerini ayarlama

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Eş kuruluşun istemcisi için ortam değişkenlerini ayarlama

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

Konsorsiyumdaki eş kuruluşların sayısına bağlı olarak, eş komutlarını tekrarlamanız ve ortam değişkenini uygun şekilde ayarlamanız gerekebilir.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Azure depolama hesabı için ortam değişkenlerini ayarlama

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Bir Azure depolama hesabı oluşturmak için aşağıdaki komutları kullanın. Azure depolama hesabınız zaten varsa, bu adımı atlayın.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Azure depolama hesabında bir dosya paylaşma oluşturmak için aşağıdaki komutları kullanın. Zaten bir dosya paylaşımınız varsa, bu adımı atlayın.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Azure dosya paylaşımında bir bağlantı dizesi oluşturmak için aşağıdaki komutları kullanın.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Kuruluş bağlantı profilini, yönetici kullanıcı kimliğini ve MSP 'yi içeri aktarma

Kuruluşun bağlantı profilini, yönetici kullanıcı kimliğini ve yönetilen hizmet sağlayıcısını (MSP) Azure Kubernetes hizmet kümesinden getirmek ve bu kimlikleri istemci uygulamasının yerel deposunda depolamak için aşağıdaki komutları kullanın. Bir yerel depoya bir örnek *Azhlftool/depoları* dizinidir.

Orderer organizasyonu için:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Eş kuruluş için:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>Kanal oluşturma

Orderer kuruluşunun istemcisinde, yalnızca orderer organizasyonunu içeren bir kanal oluşturmak için aşağıdaki komutu kullanın.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Konsorsiyum yönetimi için eş kuruluş ekleme

>[!NOTE]
> Herhangi bir konsorsiyum işlemine başlamadan önce, istemci uygulamasının ilk kurulumunu bitirdiğinizden emin olun.  

Bir kanala ve konsorsiyuma bir eş kuruluş eklemek için aşağıdaki komutları verilen sırada çalıştırın: 

1.  Eş kuruluşun istemcisinden, eşdüzey kuruluşun MSP 'sini Azure Storage 'a yükleyin.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Sipariş kuruluşun istemcisinden, eş kuruluşun MSP 'sini Azure depolama 'dan indirin. Ardından, eşe ve konsorsiyunuza eş organizasyonu eklemek için komutunu verin.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Sipariş kuruluşunun istemcisinde, eş kuruluşun bu bağlantı profilini kullanarak sipariş düğümlerine bağlanabilmesi için Azure Storage 'daki orderer 'un bağlantı profilini karşıya yükleyin.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Eş kuruluşun istemcisinden, Azure depolama 'dan orderer 'ın bağlantı profilini indirin. Ardından, kanala eşdüzey düğümler eklemek için komutunu çalıştırın.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Benzer şekilde, kanala daha fazla eş kurum eklemek için, eş ortam değişkenlerini gerekli eş kuruluşa göre güncelleştirin ve 1 ile 4 arasındaki adımları yineleyin.

### <a name="set-anchor-peers"></a>Yer işareti eşlerini ayarla

Eş kuruluşun istemcisinden, belirtilen kanaldaki eş kuruluşa ait bağlantı eşlerini ayarlamak için komutunu çalıştırın.

>[!NOTE]
> Bu komutu çalıştırmadan önce, Consortium yönetim komutları kullanılarak eş kuruluşun kanala eklendiğinden emin olun.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` , bir çapa eşi olarak ayarlanacak eş düğümlerinin boşlukla ayrılmış bir listesidir. Örnek:

  - `<anchorPeersList>` `"peer1"` Yalnızca peer1 düğümünü bir bağlantı eşi olarak ayarlamak istiyor musunuz?
  - `<anchorPeersList>` `"peer1" "peer3"` Hem peer1 hem de peer3 düğümlerini bağlayıcı eşleri olarak ayarlamak istiyorsanız olarak ayarlayın.

## <a name="chaincode-management-commands"></a>Chaincode yönetim komutları

>[!NOTE]
> Herhangi bir chaincode işlemine başlamadan önce, istemci uygulamasının ilk kurulumunu gerçekleştirdiğinizden emin olun.  

### <a name="set-the-chaincode-specific-environment-variables"></a>Chaincode 'a özgü ortam değişkenlerini ayarlama

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=â€œchaincode_example02â€
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=â€œ1â€ for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=â€œ/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/goâ€
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Chaincode 'u yükler  

Eş kuruluşa chaincode 'u yüklemek için aşağıdaki komutu çalıştırın.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Komut, ortam değişkeninde ayarlanan eşdüzey kuruluşun tüm eşdüzey düğümlerine chaincode yükler `ORGNAME` . Kanalınıza iki veya daha fazla eş kurum varsa ve bunların tümüne chaincode yüklemek istiyorsanız, her eş kuruluş için bu komutu ayrı olarak çalıştırın.  

Şu adımları izleyin:  

1.  `ORGNAME`' İ ayarlayın ve `USER_IDENTITY` `peerOrg1` komutuna göre `./azhlf chaincode install` komutunu çalıştırın.  
2.  `ORGNAME`' İ ayarlayın ve `USER_IDENTITY` `peerOrg2` komutuna göre `./azhlf chaincode install` komutunu çalıştırın.  

### <a name="instantiate-chaincode"></a>Chaincode örneği oluşturma  

Eş istemci uygulamasından, kanaldaki chaincode 'u başlatmak için aşağıdaki komutu çalıştırın.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Örnek oluşturma işlev adını ve bağımsız değişkenlerin boşluk listesini, `<instantiateFunc>` ve `<instantiateFuncArgs>` sırasıyla geçirin. Örneğin, chaincode_example02 oluşturmak için. chaincode, ve olarak ayarlayın `<instantiateFunc>` . `init` `<instantiateFuncArgs>` `"a" "2000" "b" "1000"`

Ayrıca, bayrağını kullanarak koleksiyonun yapılandırma JSON dosyasını da geçirebilirsiniz `--collections-config` . Ya da, `-t` özel işlemler için kullanılan chaincode örneklarken bayrağı kullanarak geçici bağımsız değişkenleri ayarlayın.

Örnek:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

`<collectionConfigJSONFilePath>`Bölüm, özel veri chaincode örneklenmesi için tanımlanan koleksiyonları IÇEREN JSON dosyasının yoludur. Aşağıdaki yolda, *Azhlftool* dizinine göre örnek bir KOLEKSIYONUN yapılandırma json dosyasını bulabilirsiniz: `./samples/chaincode/src/private_marbles/collections_config.json` .
`<transientArgs>`Dize biçiminde geçerli BIR JSON olarak geçirin. Özel karakterleri kaçış. Örnek: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Kanalda herhangi bir eş kuruluştan komutu bir kez çalıştırın. İşlem, sipariş 'e başarıyla gönderildikten sonra, sipariş bu işlemi kanaldaki tüm eş kuruluşlara dağıtır. Daha sonra chaincode, kanaldaki tüm eş kuruluşlardaki tüm eşdüzey düğümlerde oluşturulur.  

### <a name="invoke-chaincode"></a>Chaincode çağırma  

Eş kuruluşun istemcisinden, chaincode işlevini çağırmak için aşağıdaki komutu çalıştırın:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Çağırma işlevi adı ve boşluk ayrımı bağımsız değişkenlerinin InÂ € ̄ `<invokeFunction>` â € ̄andÂ € ̄ `<invokeFuncArgs>` â € ̄respectively bir listesini geçirin. Chaincode_example02. go chaincode örneğine devam etmek için, Invoke işlemini gerçekleştirmek için setâ € ̄ `<invokeFunction>` â € ̄toÂ € ̄ `invoke` â € ̄andÂ € ̄ `<invokeFuncArgs>` â € ̄to `"a" "b" "10"` .  

>[!NOTE]
> Kanalda herhangi bir eş kuruluştan komutu bir kez çalıştırın. İşlem, sipariş 'e başarıyla gönderildikten sonra, sipariş bu işlemi kanaldaki tüm eş kuruluşlara dağıtır. Daha sonra bu durum, kanaldaki tüm eş kuruluşların tüm eşdüzey düğümlerinde güncelleştirilir.  


### <a name="query-chaincode"></a>Sorgu chaincode  

Chaincode 'u sorgulamak için aşağıdaki komutu çalıştırın:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
Bunları onaylama, chaincode 'un yüklendiği ve işlemlerin yürütülmesi için çağrıldığı eşlerdir. `<endorsingPeers>`Geçerli eş kuruluştan eş düğüm adları içerecek şekilde ayarlamanız gerekir. Verili bir chaincode ve kanal birleşiminin boşluklarla ayrılmış olarak onaylama eşlerini listeleyin. Örneğin: `-p "peer1" "peer3"`.

Chaincode 'u yüklemek için *Azhlftool* kullanıyorsanız, herhangi bir eş düğüm adını, onaylama eşi bağımsız değişkenine bir değer olarak geçirin. Chaincode, o kuruluşun her eşdüzey düğümüne yüklenir. 

Sorgu işlev adı ve boşluk ayrımı bağımsız değişkenlerinin InÂ € ̄ `<queryFunction>` â € ̄andÂ € ̄ `<queryFuncArgs>` â € ̄respectively bir listesini geçirin. Bir başvuru olarak chaincode_example02. goâ € ̄chaincode, Birinci durumda "a" değerini sorgulamak, setâ € ̄ `<queryFunction>` â € ̄toÂ € ̄ `query` andÂ € ̄ `<queryArgs>` to `"a"` .  

## <a name="troubleshoot"></a>Sorun giderme

### <a name="find-deployed-version"></a>Dağıtılan sürümü bul

Şablon dağıtımınızın sürümünü bulmak için aşağıdaki komutları çalıştırın. Ortam değişkenlerini, şablonun dağıtıldığı kaynak grubuna göre ayarlayın.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Önceki sürüme yama

Sanal 3.0.0 ' nin altındaki şablon sürümünün herhangi bir dağıtımında, chaincode 'u çalıştırmaya yönelik sorunlarla karşılaşırsanız, bir düzeltme ile eşdüzey düğümlerinizi düzeltme eki uygulamak için aşağıdaki adımları izleyin.

Eş dağıtım betiğini indirin.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Eşdeğerinizle ilgili parametreleri değiştirerek aşağıdaki komutu kullanarak betiği çalıştırın.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Tüm eşdüzey düğümlerinizin düzeltme eki almasını bekleyin. Aşağıdaki komutu kullanarak eş düğümlerinizin durumunu, kabuğun farklı bir örneğine her zaman kontrol edebilirsiniz.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Destek ve geri bildirim

Blok zinciri hizmeti tekliflerini ve Azure blok zinciri Mühendisliği ekibinin bilgilerini güncel kalmak için [Azure blok zinciri blogu](https://azure.microsoft.com/blog/topics/blockchain/)' nu ziyaret edin.

Ürün geri bildirimi sağlamak veya yeni özellikler istemek için, [blok zinciri Için Azure geri bildirim Forumu](https://aka.ms/blockchainuservoice)aracılığıyla bir fikir gönderin veya oylayın.

### <a name="community-support"></a>Topluluk desteği

Microsoft mühendisleri ve Azure blok zinciri topluluk uzmanlarıyla birlikte katılın:

- [Microsoft Q bir sayfa&](/answers/topics/azure-blockchain-workbench.html) 
   
  Blok zinciri şablonları için mühendislik desteği, dağıtım sorunlarıyla sınırlıdır.
- [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
