---
title: Uzaktan Izleme çözümü Kullanıcı arabirimini özelleştirme-Azure | Microsoft Docs
description: Bu makalede, uzaktan Izleme çözümü Hızlandırıcısı Kullanıcı arabirimine yönelik kaynak koda nasıl erişebileceğiniz ve bazı özelleştirmeler yapabileceğiniz hakkında bilgi sağlanır.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: eb3d5fea68b5b1b6e648943cb3dbaab5857e9e07
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608013"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Uzaktan Izleme çözüm Hızlandırıcısını özelleştirme

Bu makalede, kaynak koda nasıl erişebileceğinizi ve uzaktan Izleme çözümü Hızlandırıcısı Kullanıcı arabirimini nasıl özelleştireceğiniz hakkında bilgi sağlanır.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Kullanıcı arabirimi için yerel bir geliştirme ortamı hazırlama

Uzaktan Izleme çözümü Hızlandırıcısı Kullanıcı arabirimi kodu, tepki. js çerçevesi kullanılarak uygulanır. Kaynak kodu [Azure-IoT-PCs-Remote-Monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub deposunda bulabilirsiniz.

Kullanıcı arabiriminde değişiklik yapmak için, bir kopyasını yerel olarak çalıştırabilirsiniz. Telemetriyi alma gibi eylemleri gerçekleştirmek için yerel kopya çözümün dağıtılan örneğine bağlanır.

Aşağıdaki adımlarda, UI geliştirmesi için yerel bir ortam ayarlama işlemi ana hatlarıyla verilmiştir:

1. **Bilgisayar** CLI 'sını kullanarak çözüm hızlandırıcının **temel** bir örneğini dağıtın. Dağıtımınızın adını ve sanal makine için verdiğiniz kimlik bilgilerini bir yere unutmayın. Daha fazla bilgi için bkz. [CLI kullanarak dağıtma](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Çözümünüzde mikro hizmetleri barındıran sanal makineye SSH erişimini etkinleştirmek için Azure portal veya Azure Cloud Shell kullanın. Örneğin:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Yalnızca test ve geliştirme sırasında SSH erişimini etkinleştirin. SSH 'yi etkinleştirirseniz, [kullanmayı bitirdikten hemen sonra devre dışı bırakmanız gerekir](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Sanal makinenizin adını ve genel IP adresini bulmak için Azure portal veya Azure Cloud Shell kullanın. Örneğin:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Sanal makinenize bağlanmak için SSH kullanın. Önceki adımda bulunan IP adresini ve çözümü dağıtmak için **bilgisayarları** çalıştırdığınızda verdiğiniz kimlik bilgilerini kullanın. `ssh` Komut Azure Cloud Shell kullanılabilir.

1. Yerel UX 'in bağlanmasına izin vermek için, sanal makinedeki bash kabuğu 'nda aşağıdaki komutları çalıştırın:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Komutun tamamlandığını ve Web sitesi başladıktan sonra, sanal makine bağlantısını kesebilirsiniz.

1. [Azure-IoT-PCs-Remote-Monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) deposunun yerel kopyasında, dağıtılan çözümünüzün URL 'sini eklemek için **. env** dosyasını düzenleyin:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Bir komut isteminde, `azure-iot-pcs-remote-monitoring-webui` klasörün yerel kopyasına gidin.

1. Gerekli kitaplıkları yüklemek ve Kullanıcı arabirimini yerel olarak çalıştırmak için aşağıdaki komutları çalıştırın:

    ```cmd/sh
    npm install
    npm start
    ```

1. Önceki komut, Kullanıcı arabirimini http:\//localhost: 3000/Dashboard konumunda yerel olarak çalıştırır. Site çalışırken kodu düzenleyebilir ve dinamik olarak güncelleştirme makalesine bakabilirsiniz.

## <a name="customize-the-layout"></a>Düzeni özelleştirme

Uzaktan Izleme çözümünde bulunan her sayfa, kaynak kodda *panel* olarak adlandırılan bir denetim kümesinden oluşur. **Pano** sayfası beş panelden oluşur: Genel bakış, eşleme, uyarılar, telemetri ve analiz. [Bilgisayarlar-Remote-Monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub deposundaki her bir sayfayı ve bunların panellerini tanımlayan kaynak kodunu bulabilirsiniz. Örneğin, **Pano** sayfasını, yerleşimini ve sayfadaki panelleri tanımlayan kod [src/Components/Pages/Dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) klasöründe bulunur.

Panolar kendi düzen ve boyutlandırlamalarını yönettiğinden, sayfanın yerleşimini kolayca değiştirebilirsiniz. `src/components/pages/dashboard/dashboard.js` Dosyadaki **PageContent** öğesinde aşağıdaki değişiklikleri yapın:

* Harita ve telemetri panellerinin konumlarını değiştirin.
* Harita ve analiz panellerinin göreli genişliklerini değiştirin.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Panel yerleşimini değiştir](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Aynı panelin birkaç örneğini veya [bir paneli çoğaltdıysanız ve özelleştirirseniz](#duplicate-and-customize-an-existing-control), birkaç sürümü de ekleyebilirsiniz. Aşağıdaki örnek, telemetri panelinin iki örneğinin nasıl ekleneceğini gösterir. Bu değişiklikleri yapmak için `src/components/pages/dashboard/dashboard.js` dosyayı düzenleyin:

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

Daha sonra her panelde farklı telemetri görüntüleyebilirsiniz:

![Birden çok telemetri paneli](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Varolan bir denetimi yineleme ve özelleştirme

Aşağıdaki adımlarda, var olan bir paneli yineleme, değiştirme ve değiştirilen sürümü kullanma ana hatlarıyla gösterilmiştir. Adımlar bir örnek olarak **Uyarılar** panelini kullanır:

1. Deponun yerel kopyasında, `src/components/pages/dashboard/panels` klasöründeki **Uyarılar** klasörünün bir kopyasını oluşturun. Yeni kopyayı **cust_alerts**olarak adlandırın.

1. **Cust_alerts** klasöründeki **alertspanel. js** dosyasında, **custalertspanel**olarak kullanılacak sınıfın adını düzenleyin:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Aşağıdaki satırı `src/components/pages/dashboard/panels/index.js` dosyasına ekleyin:

    ```javascript
    export * from './cust_alerts';
    ```

1. Dosyadaki ile `CustAlertsPanel` `alertsPanel`değiştirin: `src/components/pages/dashboard/dashboard.js`

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Şimdi özgün **Uyarılar** panelini **custalerts**adlı bir kopyayla değiştirmiş oldunuz. Bu kopya orijinalile aynıdır. Şimdi kopyayı değiştirebilirsiniz. Örneğin, **Uyarılar** panelinde sütun sıralamasını değiştirmek için:

1. `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js` dosyasını açın.

1. Sütun tanımlarını aşağıdaki kod parçacığında gösterildiği gibi değiştirin:

    ```javascript
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

Aşağıdaki ekran görüntüsünde, **Uyarılar** panelinin yeni sürümü gösterilmektedir:

![Uyarı bölmesi güncelleştirildi](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Telemetri grafiğini özelleştirme

`src/components/pages/dashboard/panels/telemtry` Klasördeki dosyalar, **Pano** sayfasında telemetri grafiğini tanımlar. Kullanıcı arabirimi, `src/services/telemetryService.js` dosyadaki çözüm arka ucundan Telemetriyi alır. Aşağıdaki adımlarda, telemetri grafiğinde görüntülenecek zaman döneminin 15 ila 5 dakikaya nasıl değiştirileceği gösterilmektedir:

1. Dosyasında, getTelemetryByDeviceIdP15M adlı işlevi bulun. `src/services/telemetryService.js` Bu işlevin kopyasını oluşturun ve kopyayı aşağıdaki şekilde değiştirin:

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Telemetri grafiğini doldurmak üzere bu yeni işlevi kullanmak için `src/components/pages/dashboard/dashboard.js` dosyasını açın. Telemetri akışını başlatan çizgiyi bulun ve aşağıdaki gibi değiştirin:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Telemetri grafiği artık beş dakikalık telemetri verilerini gösterir:

![Bir gün gösteren telemetri grafiği](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Yeni KPI Ekle

**Pano** sayfasında KPI 'lar **analiz** panelinde görüntülenir. Bu KPI 'ler `src/components/pages/dashboard/dashboard.js` dosyada hesaplanır. KPI 'ler `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` dosya tarafından işlenir. Aşağıdaki adımlarda, **Pano** sayfasında yenı bir KPI değerinin nasıl hesaplanacağı ve işlenmesi anlatılmaktadır. Gösterilen örnek, uyarı uyarıları KPI 'sinde yeni bir yüzde değişikliği eklemektir:

1. `src/components/pages/dashboard/dashboard.js` dosyasını açın. **InitialState** nesnesini şu şekilde bir **Warningalertschange** özelliği içerecek şekilde değiştirin:

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. **Currentalertsstats** nesnesini, bir özellik olarak **totalwarningcount** değerini içerecek şekilde değiştirin:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Yeni KPI 'yi hesaplayın. Kritik uyarı sayısı için hesaplamayı bulun. Kodu çoğaltın ve kopyayı aşağıdaki şekilde değiştirin:

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. KPI akışına yeni **Warningalertschange** KPI 'sini ekleyin:

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. Kullanıcı arabirimini oluşturmak için kullanılan durum verilerine yeni **Warningalertschange** KPI 'sini ekleyin:

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. KPI 'ler paneline geçirilen verileri güncelleştirin:

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

Artık `src/components/pages/dashboard/dashboard.js` dosyadaki değişiklikleri tamamladınız. Aşağıdaki adımlarda, yeni KPI 'yı göstermek için `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` dosyada yapılacak değişiklikler açıklanır:

1. Aşağıdaki kod satırını, yeni KPI değerini şu şekilde almak için değiştirin:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Yeni KPI değerini aşağıdaki gibi görüntüleyecek şekilde işaretlemeyi değiştirin:

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

**Pano** sayfası ŞIMDI yeni KPI değerini görüntülüyor:

![Uyarı KPI 'si](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Haritayı özelleştirme

Çözümdeki harita bileşenlerinin ayrıntıları için GitHub 'da [Haritayı özelleştirme](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) sayfasına bakın.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Diğer özelleştirme seçenekleri

Uzaktan Izleme çözümünde sunum ve görselleştirmeler katmanını daha fazla değiştirmek için kodu düzenleyebilirsiniz. İlgili GitHub depoları şunlardır:

* [Azure IoT çözümleri için yapılandırma mikro hizmeti (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Azure IoT çözümleri için yapılandırma mikro hizmeti (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Azure IoT BILGISAYARLARı uzaktan Izleme Web Kullanıcı arabirimi](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, uzaktan Izleme çözüm hızlandırıcısında Web Kullanıcı arabirimini özelleştirmenize yardımcı olacak kaynaklar hakkında bilgi edindiniz. Kullanıcı arabirimini özelleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Uzaktan Izleme çözümü Hızlandırıcısı Web Kullanıcı arabirimine özel bir sayfa ekleme](iot-accelerators-remote-monitoring-customize-page.md)
* [Uzaktan Izleme çözümü Hızlandırıcısı Web Kullanıcı arabirimine özel bir hizmet ekleme](iot-accelerators-remote-monitoring-customize-service.md)
* [Uzaktan Izleme çözümü Hızlandırıcısı Web Kullanıcı arabirimine özel kılavuz ekleme](iot-accelerators-remote-monitoring-customize-grid.md)
* [Uzaktan Izleme çözümü Hızlandırıcısı Web Kullanıcı arabirimine özel bir açılır pencere ekleyin](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Uzaktan Izleme çözümü Hızlandırıcısı Web Kullanıcı arabirimindeki panoya özel panel ekleme](iot-accelerators-remote-monitoring-customize-panel.md)

Uzaktan Izleme çözümü Hızlandırıcısı hakkında daha fazla kavramsal bilgi için bkz. [Uzaktan izleme mimarisi](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Uzaktan Izleme çözümü mikro hizmetlerini özelleştirme hakkında daha fazla bilgi için bkz. [bir mikro hizmeti özelleştirme ve yeniden dağıtma](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
