---
title: 'Öğretici: Azure Service Fabric bir Java uygulaması oluşturma'
description: Bu öğreticide, bir ön uçla güvenilir hizmet Java uygulamasının nasıl oluşturulacağını, durum bilgisi olan güvenilir hizmet arka ucunun nasıl oluşturulacağını ve uygulamanın kümeye nasıl dağıtılacağını öğreneceksiniz.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/01/2018
ms.author: suhuruli
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 5e9b94f0c67b4b4630d554f4dde22502c90e777c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376366"
---
# <a name="tutorial-create-an-application-with-a-java-api-front-end-service-and-a-stateful-back-end-service-on-azure-service-fabric"></a>Öğretici: Azure Service Fabric bir Java API ön uç hizmeti ve durum bilgisi olan bir arka uç hizmetiyle uygulama oluşturma

Bu öğretici, bir dizinin birinci bölümüdür. İşiniz bittiğinde, bir Java Web ön ucuna sahip bir oylama uygulamanız olur ve bu, oylama sonuçlarını Azure Service Fabric 'te durum bilgisi olan bir arka uç hizmetine kaydeder. Bu öğretici serisi, çalışır durumda bir Mac OSX veya Linux geliştirici makineniz olmasını gerektirir. Oylama uygulamasını el ile oluşturmak istemiyorsanız, [tamamlanmış uygulamanın kaynak kodunu indirebilir](https://github.com/Azure-Samples/service-fabric-java-quickstart) ve [Oylama örnek uygulamasında izlenecek yol](service-fabric-tutorial-create-java-app.md#walk-through-the-voting-sample-application) bölümüne atlayabilirsiniz. Ayrıca, [Java güvenilir Hizmetleri Için hızlı](service-fabric-quickstart-java-reliable-services.md)başlangıcı takip etmeyi de göz önünde bulundurun.

![Service Fabric Voting örneği](./media/service-fabric-tutorial-create-java-app/service-fabric-java-voting-app-sample.png)

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Java Service Fabric Güvenilir Hizmetler uygulaması derleme
> * [Yerel kümede uygulamayı dağıtma ve uygulamanın hatasını ayıklama](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Azure kümesine uygulama dağıtma](service-fabric-tutorial-java-deploy-azure.md)
> * [Uygulama için izleme ve tanılamayı ayarlama](service-fabric-tutorial-java-elk.md)
> * [CI/CD ayarlama](service-fabric-tutorial-java-jenkins.md)


Serinin birinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Durum bilgisi olan güvenilir Java hizmeti oluşturma
> * Durum bilgisi olmayan Java web uygulaması hizmeti oluşturma
> * Durum bilgisi olan hizmetle iletişim kurmak için hizmet uzaktan iletişimini kullanma
> * Yerel bir Service Fabric kümesinde uygulamayı dağıtma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
* [Mac](service-fabric-get-started-mac.md) veya [Linux](service-fabric-get-started-linux.md) için geliştirme ortamınızı ayarlayın. Eclipse eklentisini, Gradle’ı, Service Fabric SDK’yı ve Service Fabric CLI’yı (sfctl) yükleme yönergelerini izleyin.

## <a name="create-the-front-end-java-stateless-service"></a>Ön uç durum bilgisi olmayan Java hizmeti oluşturma

İlk olarak, Oylama uygulamasının web ön ucunu oluşturun. AngularJS tarafından desteklenen bir Web Kullanıcı arabirimi, basit bir HTTP sunucusu çalıştıran Java olmayan hizmete istek gönderir. Bu hizmet her isteği işler ve oyları depolamak için durum bilgisi olan hizmete bir uzak yordam çağrısı gönderir. 

1. Eclipse’i açın.

2. **Dosya** > **Yeni** > **Diğer** > **Service Fabric** > **Service Fabric Projesi** seçeneğini kullanarak bir proje oluşturun.

    ![Çakışan Küreler yeni Service Fabric projesi](./media/service-fabric-tutorial-create-java-app/service-fabric-project-wizard.png)

3. **Servicefabric proje Sihirbazı** iletişim kutusunda, projeyi **Oylama** olarak adlandırın ve **İleri**' yi seçin.

    ![Yeni hizmet iletişim kutusunda durum bilgisi olmayan Java hizmetini seçme](./media/service-fabric-tutorial-create-java-app/name-service-fabric-project-wizard.png) 

4. **Hizmet Ekle** sayfasında, **durum bilgisi olmayan hizmet**' i seçin ve hizmetinizi **votingweb**olarak adlandırın. Projeyi oluşturmak için **son** ' u seçin.

    ![Service Fabric projeniz için durum bilgisi olmayan bir hizmet oluşturma]( ./media/service-fabric-tutorial-create-java-app/add-service-fabric-votingweb-service.png)

    Eclipse bir uygulama ve bir hizmet projesi oluşturup bunları Paket Gezgini'nde görüntüler.

    ![Uygulamanın oluşturulmasını izleyen Eclipse Paket Gezgini]( ./media/service-fabric-tutorial-create-java-app/eclipse-package-explorer.png)

Tablo, önceki ekran görüntüsünde yer alan paket gezginindeki her bir öğenin kısa bir açıklamasını sunar. 

| **Paket Gezgini Öğesi** | **Açıklama** |
| --- | --- |
| PublishProfiles | Yerel ve Azure Service Fabric kümelerinin profil ayrıntılarını açıklayan JSON dosyalarını içerir. Bu dosyaların içerikleri, uygulama dağıtılırken eklenti tarafından kullanılır. |
| Betikler | Bir küme ile uygulamanızı hızlı şekilde yönetmek için komut satırından kullanılabilecek yardımcı betikleri içerir. |
| VotingApplication | Service Fabric kümesine gönderilen Service Fabric uygulamasını içerir. |
| VotingWeb | İlgili gradle derleme dosyasıyla birlikte ön uç durum bilgisi olmayan hizmet kaynak dosyalarını içerir. |
| build.gradle | Projeyi yönetmek için kullanılan gradle dosyası. |
| settings.gradle | Bu klasördeki Gradle projelerinin adlarını içerir. |

### <a name="add-html-and-javascript-to-the-votingweb-service"></a>VotingWeb hizmetine HTML ve Javascript ekleme

Durum bilgisi olmayan hizmet tarafından işlenebilen bir kullanıcı arabirimi eklemek için bir HTML dosyası ekleyin. Bu HTML dosyası daha sonra durum bilgisi olmayan Java hizmetine eklenen basit HTTP sunucusu tarafından işlenir.

1. *VotingApplication/VotingWebPkg/Code* dizinine ulaşmak için *VotingApplication* dizinini genişletin.

2. *Kod* dizinine sağ tıklayıp **Yeni** > **klasörü**' nü seçin.

3. Klasörü *Wwwroot* olarak adlandırın ve **son**' u seçin.

    ![Eclipse wwwroot klasörü oluşturma](./media/service-fabric-tutorial-create-java-app/create-wwwroot-folder.png)

4. **index.html** adlı **wwwroot** klasörüne bir dosya ekleyin ve aşağıdaki içerikleri bu dosyaya yapıştırın.

```html
<!DOCTYPE html>
<html>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/0.13.4/ui-bootstrap-tpls.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
<body>

<script>
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.controller("VotingAppController", ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {
    $scope.votes = [];
    
    $scope.refresh = function () {
        $http.get('getStatelessList')
            .then(function successCallback(response) {
        $scope.votes = Object.assign(
            {},
            ...Object.keys(response.data) .
            map(key => ({[decodeURI(key)]: response.data[key]}))
        )
        },
        function errorCallback(response) {
            alert(response);
        });
    };

    $scope.remove = function (item) {
       $http.get("removeItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };

    $scope.add = function (item) {
        if (!item) {return;}
        $http.get("addItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };
}]);
</script>

<div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-offset-2">
                <form style="width:50% ! important;" class="center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="(key, value)  in votes">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(key)">
                            <span class="pull-left">
                                {{key}}
                            </span>
                            <span class="badge pull-right">
                                {{value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>
```

### <a name="update-the-votingwebjava-file"></a>VotingWeb.java dosyasını güncelleştirme

**VotingWeb** alt projesinde *VotingWeb/src/statelessservice/VotingWeb.java* dosyasını açın. **VotingWeb** hizmeti, durum bilgisi olmayan hizmete yönelik ağ geçididir ve ön uç API’si için iletişim dinleyicisini ayarlamaktan sorumludur.

Dosyadaki var olan **Createserviceınstancelisteners** metodunu aşağıdaki ile değiştirin ve değişikliklerinizi kaydedin.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {

    EndpointResourceDescription endpoint = this.getServiceContext().getCodePackageActivationContext().getEndpoint(webEndpointName);
    int port = endpoint.getPort();

    List<ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
    listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationListener(context, port)));
    return listeners;
}
```

### <a name="add-the-httpcommunicationlistenerjava-file"></a>HTTPCommunicationListener.java dosyası ekleme

HTTP iletişim dinleyicisi, HTTP sunucusunu ayarlayan bir denetleyici olarak hareket eder ve oylama eylemlerini tanımlayan API’leri gösterir. *Votingweb/src/statelessservice* klasöründeki *statelessservice* paketine sağ tıklayın ve ardından **Yeni** > **Dosya**' yı seçin.  *Httpcommunicationlistener. Java* dosyasını adlandırın ve **son**' u seçin.

Dosyanın içeriğini aşağıdakilerle değiştirin, sonra değişikliklerinizi kaydedin.  Daha sonra, HttpCommunicationListener. Java dosyasını güncelleştirme bölümünde bu dosya, arka uç hizmetinden oylama verilerini işlemek, okumak ve yazmak için değiştirilir.  Şimdilik dinleyici yalnızca Oylama uygulaması için statik HTML'i döndürür.

```java
// ------------------------------------------------------------
//  Copyright (c) Microsoft Corporation.  All rights reserved.
//  Licensed under the MIT License (MIT). See License.txt in the repo root for license information.
// ------------------------------------------------------------

package statelessservice;

import com.google.gson.Gson;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.Headers;

import java.io.File;
import java.io.OutputStream;
import java.io.FileInputStream;

import java.net.InetSocketAddress;
import java.net.URI;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.logging.Level;
import java.util.logging.Logger;

import microsoft.servicefabric.services.communication.runtime.CommunicationListener;
import microsoft.servicefabric.services.runtime.StatelessServiceContext;
import microsoft.servicefabric.services.client.ServicePartitionKey;
import microsoft.servicefabric.services.remoting.client.ServiceProxyBase;
import microsoft.servicefabric.services.communication.client.TargetReplicaSelector;
import system.fabric.CancellationToken;

public class HttpCommunicationListener implements CommunicationListener {

    private static final Logger logger = Logger.getLogger(HttpCommunicationListener.class.getName());

    private static final String HEADER_CONTENT_TYPE = "Content-Type";
    private static final int STATUS_OK = 200;
    private static final int STATUS_NOT_FOUND = 404;
    private static final int STATUS_ERROR = 500;
    private static final String RESPONSE_NOT_FOUND = "404 (Not Found) \n";
    private static final String MIME = "text/html";
    private static final String ENCODING = "UTF-8";

    private static final String ROOT = "wwwroot/";
    private static final String FILE_NAME = "index.html";
    private StatelessServiceContext context;
    private com.sun.net.httpserver.HttpServer server;
    private ServicePartitionKey partitionKey;
    private final int port;

    public HttpCommunicationListener(StatelessServiceContext context, int port) {
        this.partitionKey = new ServicePartitionKey(0);
        this.context = context;
        this.port = port;
    }

    // Called by openAsync when the class is instantiated
    public void start() {
        try {
            logger.log(Level.INFO, "Starting Server");
            server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(this.port), 0);
        } catch (Exception ex) {
            logger.log(Level.SEVERE, null, ex);
            throw new RuntimeException(ex);
        }

        // Responsible for rendering the HTML layout described in the previous step
        server.createContext("/", new HttpHandler() {
            @Override
            public void handle(HttpExchange t) {
                try {
                    File file = new File(ROOT + FILE_NAME).getCanonicalFile();

                    if (!file.isFile()) {
                      // Object does not exist or is not a file: reject with 404 error.
                      t.sendResponseHeaders(STATUS_NOT_FOUND, RESPONSE_NOT_FOUND.length());
                      OutputStream os = t.getResponseBody();
                      os.write(RESPONSE_NOT_FOUND.getBytes());
                      os.close();
                    } else {
                      Headers h = t.getResponseHeaders();
                      h.set(HEADER_CONTENT_TYPE, MIME);
                      t.sendResponseHeaders(STATUS_OK, 0);
    
                      OutputStream os = t.getResponseBody();
                      FileInputStream fs = new FileInputStream(file);
                      final byte[] buffer = new byte[0x10000];
                      int count = 0;
                      while ((count = fs.read(buffer)) >= 0) {
                        os.write(buffer,0,count);
                      }

                      fs.close();
                      os.close();
                    }
                } catch (Exception e) {
                    logger.log(Level.WARNING, null, e);
                }
            }
        });

        /*
        [Replace this entire comment block in the 'Connect the services' section]
        */

        server.setExecutor(null);
        server.start();
    }

    //Helper method to parse raw HTTP requests
    private Map<String, String> queryToMap(String query){
        Map<String, String> result = new HashMap<String, String>();
        for (String param : query.split("&")) {
            String pair[] = param.split("=");
            if (pair.length>1) {
                result.put(pair[0], pair[1]);
            }else{
                result.put(pair[0], "");
            }
        }
        return result;
    }

    //Called closeAsync when the service is shut down
    private void stop() {
        if (null != server)
            server.stop(0);
    }

    //Called by the Service Fabric runtime when this service is created on a node
    @Override
    public CompletableFuture<String> openAsync(CancellationToken cancellationToken) {
        this.start();
                    logger.log(Level.INFO, "Opened Server");
        String publishUri = String.format("http://%s:%d/", this.context.getNodeContext().getIpAddressOrFQDN(), port);
        return CompletableFuture.completedFuture(publishUri);
    }

    //Called by the Service Fabric runtime when the service is shut down
    @Override
    public CompletableFuture<?> closeAsync(CancellationToken cancellationToken) {
        this.stop();
        return CompletableFuture.completedFuture(true);
    }

    //Called by the Service Fabric runtime to forcibly shut this listener down
    @Override
    public void abort() {
        this.stop();
    }
}
```

### <a name="configure-the-listening-port"></a>Dinleme bağlantı noktasını yapılandırma

VotingWeb hizmeti ön uç hizmeti oluşturulduğunda Service Fabric, hizmetin dinlemesi için bir bağlantı noktası seçer.  VotingWeb hizmeti bu uygulama için ön uç işlevi görür ve dış trafiği kabul eder; bu nedenle şimdi bu hizmeti sabit ve iyi tanınan bir bağlantı noktasına bağlayalım. Paket Gezgini’nde *VotingApplication/VotingWebPkg/ServiceManifest.xml* dosyasını açın.  **Kaynaklar** bölümünde **uç nokta** kaynağını bulun ve **bağlantı noktası** değerini 8080 olarak değiştirin (öğreticinin tamamında bu bağlantı noktasını kullanmaya devam edeceğiz). Uygulamayı yerel olarak dağıtmak ve çalıştırmak için, uygulama dinleme bağlantı noktasının bilgisayarınızda açık ve kullanılabilir olması gerekir. Aşağıdaki kod parçacığını **ServiceManifest** öğesinin içine (```<DataPackage>``` öğesinin altına) yapıştırın.

```xml
<Resources>
    <Endpoints>
        <!-- This endpoint is used by the communication listener to obtain the port on which to
            listen. Please note that if your service is partitioned, this port is shared with
            replicas of different partitions that are placed in your code. -->
        <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
    </Endpoints>
  </Resources>
```

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Uygulamanıza durum bilgisi olan bir arka uç hizmeti ekleme

Java Web API hizmetinin çatısı tamamlandığına göre devam edelim ve durum bilgisi olan arka uç hizmetini tamamlayalım.

Service Fabric, güvenilir koleksiyonlar kullanarak verileri doğrudan hizmetinizin içinden tutarlı ve güvenilir bir şekilde depolamanıza olanak tanır. Güvenilir koleksiyonlar, yüksek düzeyde kullanılabilir ve güvenilir koleksiyon sınıfları kümesidir. Bu sınıfların kullanımı, Java koleksiyonları kullanan herkese tanıdık gelir.

1. Paket Gezgini 'nde, uygulama projesi içinde **Oylama** ' ye sağ tıklayın ve **Service Fabric** > **Service Fabric hizmeti Ekle**' yi seçin.

2. **Hizmet Ekle** Iletişim kutusunda **durum bilgisi olan hizmet** ' i seçin ve hizmeti **votingdataservice** olarak adlandırın ve **Hizmet Ekle**' yi seçin.

    Hizmet projeniz oluşturulduktan sonra, uygulamanızda iki hizmet olacaktır. Uygulamanızı oluşturmaya devam ederken, aynı yöntemle başka hizmetler de ekleyebilirsiniz. Bunlardan her birinin bağımsız olarak sürümü oluşturulabilir ve yükseltilebilir.

3. Eclipse bir hizmet projesi oluşturup bunları Paket Gezgini'nde görüntüler.

    ![Çakışan Küreler Proje Gezgini](./media/service-fabric-tutorial-create-java-app/service-fabric-package-explorer-java.png)

### <a name="add-the-votingdataservicejava-file"></a>VotingDataService.java dosyası ekleme

*VotingDataService.java* dosyası, güvenilir koleksiyonlardan oyları alma, ekleme ve kaldırma mantığını içeren yöntemleri içerir. Aşağıdaki **VotingDataService** sınıfı metotlarını *VotingDataService/src/statefulservice/VotingDataService.java* dosyasına ekleyin.

```java
package statefulservice;

import java.util.HashMap;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.atomic.AtomicInteger;

import microsoft.servicefabric.services.communication.runtime.ServiceReplicaListener;

import microsoft.servicefabric.services.runtime.StatefulService;

import microsoft.servicefabric.services.remoting.fabrictransport.runtime.FabricTransportServiceRemotingListener;

import microsoft.servicefabric.data.ReliableStateManager;
import microsoft.servicefabric.data.Transaction;
import microsoft.servicefabric.data.collections.ReliableHashMap;
import microsoft.servicefabric.data.utilities.AsyncEnumeration;
import microsoft.servicefabric.data.utilities.KeyValuePair;

import system.fabric.StatefulServiceContext;

import rpcmethods.VotingRPC;

class VotingDataService extends StatefulService implements VotingRPC {
    private static final String MAP_NAME = "votesMap";
    private ReliableStateManager stateManager;

    protected VotingDataService (StatefulServiceContext statefulServiceContext) {
        super (statefulServiceContext);
    }

    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        this.stateManager = this.getReliableStateManager();
        ArrayList<ServiceReplicaListener> listeners = new ArrayList<>();

        listeners.add(new ServiceReplicaListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));

        return listeners;
    }

    // Method that will be invoked via RPC from the front end to retrieve the complete set of votes in the map
    public CompletableFuture<HashMap<String,String>> getList() {
        HashMap<String, String> tempMap = new HashMap<String, String>();

        try {

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            AsyncEnumeration<KeyValuePair<String, String>> kv = votesMap.keyValuesAsync(tx).get();
            while (kv.hasMoreElementsAsync().get()) {
                KeyValuePair<String, String> k = kv.nextElementAsync().get();
                tempMap.put(k.getKey(), k.getValue());
            }

            tx.close();


        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(tempMap);
    }

    // Method that will be invoked via RPC from the front end to add an item to the votes list or to increase the
    // vote count for a particular item
    public CompletableFuture<Integer> addItem(String itemToAdd) {
        AtomicInteger status = new AtomicInteger(-1);

        try {

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            votesMap.computeAsync(tx, itemToAdd, (k, v) -> {
                if (v == null) {
                    return "1";
                }
                else {
                    int numVotes = Integer.parseInt(v);
                    numVotes = numVotes + 1;
                    return Integer.toString(numVotes);
                }
            }).get();

            tx.commitAsync().get();
            tx.close();

            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }

    // Method that will be invoked via RPC from the front end to remove an item
    public CompletableFuture<Integer> removeItem(String itemToRemove) {
        AtomicInteger status = new AtomicInteger(-1);
        try {
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            votesMap.removeAsync(tx, itemToRemove).get();
            tx.commitAsync().get();
            tx.close();

            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }

}
```

Şimdi ön uç durum bilgisi olmayan hizmetin çatısı ve arka uç hizmeti oluşturulur.

## <a name="create-the-communication-interface-to-your-application"></a>Uygulamanıza yönelik iletişim arabirimi oluşturma

 Sonraki adımda ön uç durum bilgisi olmayan hizmet ve arka uç hizmeti bağlanıyor. Her iki hizmet de, oylama uygulamasının işlemlerini tanımlayan VotingRPC adlı bir arabirim kullanır. Bu arabirim, iki hizmet arasında uzak yordam çağrılarını (RPC) etkinleştirmek için hem ön uç hem de arka uç hizmetleri tarafından uygulanır. Ne yazık ki, tutulma, Gradle alt projelerin eklenmesini desteklemez, bu nedenle bu arabirimi içeren paketin el ile eklenmesi gerekir.

1. Paket Gezgini 'nde **Oylama** projesine sağ tıklayın ve **Yeni** > **klasör**' ü seçin. Klasöre **VotingRPC/src/rpcmethods** adını verin.

    ![Çakışan Küreler Paket Gezgini 'nde VotingRPC paketi oluşturma](./media/service-fabric-tutorial-create-java-app/create-voting-rpc-package-java.png)

3. *Voting/VotingRPC/src/rpcmethods* bölümünde *VotingRPC.java* adlı bir dosya oluşturun ve **VotingRPC.java** dosyasının içine aşağıdakileri yapıştırın. 

    ```java
    package rpcmethods;
    
    import java.util.ArrayList;
    import java.util.concurrent.CompletableFuture;
    import java.util.List;
    import java.util.HashMap;
    
    import microsoft.servicefabric.services.remoting.Service;
    
    public interface VotingRPC extends Service {
        CompletableFuture<HashMap<String, String>> getList();
    
        CompletableFuture<Integer> addItem(String itemToAdd);
    
        CompletableFuture<Integer> removeItem(String itemToRemove);
    }
    ```

4. *Oylama/VotingRPC* dizininde *Build. Gradle* adlı boş bir dosya oluşturun ve içine aşağıdakini yapıştırın. Bu gradle dosyası, diğer hizmetler tarafından içeri aktarılan jar dosyasını derlemek ve oluşturmak için kullanılır. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/out"
        delete "${projectDir}/VotingRPC.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0')
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}

        manifest {
                attributes(
                'Main-Class': 'rpcmethods.VotingRPC')
            baseName "VotingRPC"
            destinationDir = file('./')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }

    defaultTasks 'clean', 'jar'
    ```

5. *Voting/settings.gradle* dosyasında, yeni oluşturulan projeyi derlemeye dahil etmek için bir satır ekleyin. 

    ```gradle
    include ':VotingRPC'
    ```

6. *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* dosyasında açıklama bloğunun yerine şunu getirin.  

    ```java
    server.createContext("/getStatelessList", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                t.sendResponseHeaders(STATUS_OK,0);
                OutputStream os = t.getResponseBody();
    
                HashMap<String,String> list = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").getList().get();
                String json = new Gson().toJson(list);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    
    server.createContext("/removeItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                OutputStream os = t.getResponseBody();
                URI r = t.getRequestURI();
    
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToRemove = params.get("item");

                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").removeItem(itemToRemove).get();
    
                if (num != 1)
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
    
        }
    });
    
    server.createContext("/addItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                URI r = t.getRequestURI();
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToAdd = params.get("item");

                OutputStream os = t.getResponseBody();
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").addItem(itemToAdd).get();
                if (num != 1)
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    ```
7. *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* dosyasının en üstüne uygun içeri aktarma bildirimini ekleyin. 

    ```java
    import rpcmethods.VotingRPC; 
    ```

Bu aşamada, ön uç, arka uç ve RPC arabirimlerinin işlevselliği tamamlanmıştır. Sonraki aşama, Gradle betiklerini bir Service Fabric kümesinde dağıtılmadan önce uygun şekilde yapılandırmaktır. 

## <a name="walk-through-the-voting-sample-application"></a>Oylama örnek uygulamasında izlenecek yol
Oylama uygulaması iki hizmetten oluşur:
- Web ön uç hizmeti (VotingWeb) - Web sayfasına hizmet veren ve arka uç hizmetiyle iletişim kurmak için API'leri kullanıma sunan bir Java web ön uç hizmetidir.
- Arka uç hizmeti (VotingDataService) - Oyları kalıcı hale getirmek için Uzak Yordam Çağrıları (RPC) üzerinden çağrılan yöntemleri tanımlayan bir Java web hizmeti.

![Oylama örnek diyagramı](./media/service-fabric-tutorial-create-java-app/walkthrough-java-voting.png)

Uygulamada bir işlem gerçekleştirdiğinizde (öğe ekleme, oy verme, öğe kaldırma) aşağıdaki olaylar gerçekleşir:
1. JavaScript, web ön uç hizmetindeki web API’sine uygun isteği HTTP isteği olarak gönderir.

2. Web ön uç hizmeti, isteği bulup arka uç hizmetine iletmek için Service Fabric’in yerleşik Hizmet Uzaktan İletişim işlevselliğini kullanır. 

3. Arka uç hizmeti, güvenilir bir sözlükteki sonucu güncelleştiren yöntemleri tanımlar. Bu güvenilir sözlüğün içerikleri, küme içindeki birden çok düğüme çoğaltılır ve diskte kalıcı hale getirilir. Tüm uygulama verileri kümede depolanır. 

## <a name="configure-gradle-scripts"></a>Gradle betiklerini yapılandırma 

Bu bölümde, proje için Gradle betikleri yapılandırılır. 

1. *Voting/build.gradle* dosyasının içeriklerini aşağıdaki kodla değiştirin.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    subprojects {
        apply plugin: 'java'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

2. *Voting/VotingWeb/build.gradle* dosyasının içeriklerini aşağıdaki kodla değiştirin.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/VotingWeb.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
    
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingWebPkg/Code/lib")
            include('lib*.so')
        }
    }
    
    compileJava.dependsOn(explodeDeps)
    
    jar {
        from configurations.compile.collect {(it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
    
        manifest {
            attributes(
                'Main-Class': 'statelessservice.VotingWebServiceHost')
            baseName "VotingWeb"
            destinationDir = file('../VotingApplication/VotingWebPkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

3. *Voting/VotingDataService/build.gradle* dosyasının içeriklerini değiştirin. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/VotingDataService.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
    
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    compileJava.dependsOn(explodeDeps)
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingDataServicePkg/Code/lib")
            include('lib*.so')
        }
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
    
        manifest {
            attributes('Main-Class': 'statefulservice.VotingDataServiceHost')
    
            baseName "VotingDataService"
            destinationDir = file('../VotingApplication/VotingDataServicePkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

## <a name="deploy-application-to-local-cluster"></a>Uygulamayı yerel kümede dağıtma

Bu noktada uygulama, yerel Service Fabric kümesinde dağıtılmaya hazırdır.

1. Paket Gezgini 'nde **Oylama** projesine sağ tıklayın ve uygulamanızı derlemek için **Service Fabric** > **derleme uygulaması** ' nı seçin.

2. Yerel Service Fabric kümenizi çalıştırın. Bu adım, geliştirme ortamınıza (Mac veya Linux) bağlıdır.

    Mac kullanıyorsanız, aşağıdaki komutla yerel kümeyi çalıştırın: **-v** parametresine gönderilen komutu, kendi çalışma alanınızın yoluyla değiştirin.

    ```bash
    docker run -itd -p 19080:19080 -p 8080:8080 -p --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    Bkz [. OS X kurulum kılavuzunda](service-fabric-get-started-mac.md) daha ayrıntılı yönergeler.

    Linux makinesinde çalıştırıyorsanız, aşağıdaki komutla yerel kümeyi başlatın: 

    ```bash 
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Daha ayrıntılı bilgi için bkz [. Linux kurulum kılavuzu.](service-fabric-get-started-linux.md)

4. Çakışan Küreler için paket Gezgini ' nde, **Oylama** projesine sağ tıklayın ve **Service Fabric** > **uygulama Yayımla** ' yı seçin. 
5. **Uygulamayı Yayımla** penceresinde, açılan listeden **Local. JSON** ' ı seçin ve **Yayımla**' yı seçin.
6. Yerel Service Fabric kümesinde çalışan uygulamanızı görüntülemek için Web tarayıcınıza gidin ve http: \//localhost: 8080 adresine erişin. 

## <a name="next-steps"></a>Sonraki adımlar

Öğreticinin bu bölümünde, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Durum bilgisi olan güvenilir hizmet olarak Java hizmeti oluşturma
> * Durum bilgisi olmayan web hizmeti olarak Java hizmeti oluşturma
> * Hizmetleriniz arasındaki Uzak Yordam Çağrılarını (RPC) işlemek için bir Java arabirimi ekleme
> * Gradle betiklerinizi yapılandırma
> * Uygulamanızı derleme ve yerel bir Service Fabric kümesinde dağıtma

Sonraki öğreticiye ilerleyin:
> [!div class="nextstepaction"]
> [Yerel kümedeki hata ayıklama ve günlük uygulamaları](service-fabric-tutorial-debug-log-local-cluster.md)
