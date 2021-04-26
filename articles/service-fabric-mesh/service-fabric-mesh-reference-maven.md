---
title: Azure Service Fabric kafes Maven başvurusu
description: Service Fabric ağı için Maven eklentisinin nasıl kullanılacağına ilişkin başvuruyu içerir
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.custom: devx-track-java
ms.openlocfilehash: dd42752dc7ef2c7686073b7a2d780fe9356425c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625592"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Service Fabric ağı için Maven eklentisi

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

## <a name="prerequisites"></a>Önkoşullar

- Java SDK
- Maven
- Ağ uzantısı ile Azure CLı
- Service Fabric CLI

## <a name="goals"></a>Hedefler

### `azure-sfmesh:init`
- `servicefabric`Dosyasına sahip bir klasörü içeren bir klasör oluşturur `appresources` `application.yaml` . 

### `azure-sfmesh:addservice`
- `servicefabric`Hizmet adı ile klasörün içinde bir klasör oluşturur ve hizmetin YAML dosyasını oluşturur. 

### `azure-sfmesh:addnetwork`
- Klasörde, `network` belirtilen ağ adı ile bir YAML oluşturur `appresources` 

### `azure-sfmesh:addgateway`
- Klasörde, `gateway` belirtilen ağ geçidi adıyla bir YAML oluşturur `appresources` 

#### `azure-sfmesh:addvolume`
- Klasörde, `volume` belirtilen birim adıyla bir YAML oluşturur `appresources` .

### `azure-sfmesh:addsecret`
- Klasörde, `secret` belirtilen gizli dizi adıyla bir YAML oluşturur `appresources` 

### `azure-sfmesh:addsecretvalue`
- Klasörde, `secretvalue` belirtilen gizli anahtar ve gizli değer adı ile bir YAML oluşturur `appresources` 

### `azure-sfmesh:deploy`
- Klasörden yamls 'yi birleştirir `servicefabric` ve geçerli KLASÖRDE JSON Azure Resource Manager şablon oluşturur.
- Tüm kaynakları Azure Service Fabric kafes ortamına dağıtır 

### `azure-sfmesh:deploytocluster`
- `meshDeploy`Service Fabric kümeleri için geçerli olan yamls 'lerden oluşturulan dağıtım jlerini içeren bir klasör () oluşturur
- Tüm kaynakları Service Fabric kümesine dağıtır
 

## <a name="usage"></a>Kullanım

Maven Java uygulamanızda Maven eklentisini kullanmak için, pom.xml dosyanıza aşağıdaki kod parçacığını ekleyin:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Ortak yapılandırma

Maven eklentisi şu anda Azure için Maven eklentilerinin ortak yapılandırmasını desteklememektedir.

## <a name="how-to"></a>Nasıl Yapılır

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Azure Service Fabric ağı için Maven projesini başlatma
Uygulama kaynağı YAML dosyasını oluşturmak için aşağıdaki komutu çalıştırın.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Kök klasörünüzde adlı ve `servicefabric->appresources` adında bir uygulama YAML içeren bir klasör oluşturur. `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Uygulamanıza kaynak ekleme

#### <a name="add-a-new-network-to-your-application"></a>Uygulamanıza yeni bir ağ ekleyin
Bir ağ kaynağı YAML oluşturmak için aşağıdaki komutu çalıştırın. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Adlı klasörde bir ağ YAML oluşturur `servicefabric->appresources``network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Uygulamanıza yeni bir hizmet ekleyin
Bir hizmet YAML oluşturmak için aşağıdaki komutu çalıştırın. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- `servicefabric->helloworldservice` `service_helloworldservice` `helloworldservicenetwork` Uygulamanın & başvurduğu adlı klasörde bir Service YAML oluşturur `helloworldserver`
- Hizmet, 8080 numaralı bağlantı noktasını dinler
- Hizmet, bir ***helloworldserver: en son*** kapsayıcı görüntüsü olarak kullanılıyor.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Uygulamanıza yeni bir ağ geçidi kaynağı ekleyin
Bir ağ geçidi kaynağı YAML oluşturmak için aşağıdaki komutu çalıştırın. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Adlı klasörde yeni bir ağ geçidi YAML oluşturur `servicefabric->appresources``gateway_helloworldgateway`
- `helloworldservicelistener`Bu ağ geçidinden gelen çağrıları dinleyen hizmet dinleyicisi olarak başvurular. Ayrıca `helloworldservice` hizmet olarak, `helloworldservicenetwork` Ağ ve uygulama olarak da başvurur `helloworldserver` . 
- 80 numaralı bağlantı noktasında istekleri dinler

#### <a name="add-a-new-volume-to-your-application"></a>Uygulamanıza yeni bir birim ekleyin
Bir birim kaynağı YAML oluşturmak için aşağıdaki komutu çalıştırın. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Adlı klasörde bir birim YAML oluşturur `servicefabric->appresources``volume_vol1`
- Gerekli parametreler için `volumeAccountKey` ve yukarıdaki gibi özellikleri ayarlar `volumeShareName`
- Oluşturulan bu birime nasıl başvurulacağını hakkında daha fazla bilgi için, [Azure dosyalarını kullanarak uygulamayı dağıtma](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md) bölümünü ziyaret edin.

#### <a name="add-a-new-secret-resource-to-your-application"></a>Uygulamanıza yeni bir gizli kaynak ekleyin
Gizli bir kaynak kaynağı oluşturmak için aşağıdaki komutu çalıştırın. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Adlı klasörde bir gizli YAML oluşturur `servicefabric->appresources``secret_secret1`
- Oluşturulan bu gizliliğe başvurma hakkında daha fazla bilgi için aşağıdakileri ziyaret edin, [gizli dizileri yönetin](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Uygulamanıza yeni bir secretvalue kaynağı ekleyin
Bir secretvalue kaynağı YAML oluşturmak için aşağıdaki komutu çalıştırın. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Adlı klasörde bir secretvalue YAML oluşturun `servicefabric->appresources``secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Uygulamayı yerel olarak çalıştırma

Hedefe ilişkin yardım ile `azure-sfmesh:deploytocluster` uygulamayı aşağıdaki komutu kullanarak yerel olarak çalıştırabilirsiniz:

```cmd
mvn azure-sfmesh:deploytocluster
```

Varsayılan olarak bu hedef, kaynakları yerel kümeye dağıtır. Yerel kümeye dağıtıyorsanız, yerel bir Service Fabric kümenizin çalışır duruma sahip olduğunu varsayar. Kaynaklar için yerel Service Fabric kümesi şu anda yalnızca [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md)'da desteklenmektedir.

- Yamls 'lerden Service Fabric kümeler için geçerli olan jler oluşturur
- Daha sonra küme uç noktasına dağıtır

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Azure Service Fabric ağı 'na uygulama dağıtma

Hedef yardımı ile `azure-sfmesh:deploy` aşağıdaki komutu çalıştırarak Service Fabric ağ ortamına dağıtabilirsiniz:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Mevcut değilse adlı bir kaynak grubu oluşturur `todoapprg` .
- YAMLs 'Leri birleştirerek JSON Azure Resource Manager şablon oluşturur. 
- JSON 'ı Azure Service Fabric kafes ortamına dağıtır.
