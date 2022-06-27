# Custom Grafana Dashboards for ArgoCD Instance - OpenShift GitOps Operator

커뮤니티 오퍼레이터는 기존 `openshift-monitoring` 네임스페이스에 배포할 수 없으므로 새로운 네임스페이스를 생성합니다. 예시에서는 ArgoCD 인스턴스가 생성된 `argocd-grafana` 네임스페이스를 사용하겠습니다.


### 1. Deploying Custom Grafana

- OpenShift 콘솔 > OpeatorHub > Grafana 검색 > Grafana Operator 설치 

  ![01_grafana_operator](C:\Works\01_자료\01_OCP\05_OCP_Demo_hyou\brown_bag_gitops\grafana-mon\01_grafana_operator.png)

- Grafana Operator를 선택하여 설치를 계속 진행합니다.

  ![02_grafana_operator_install](C:\Works\01_자료\01_OCP\05_OCP_Demo_hyou\brown_bag_gitops\grafana-mon\02_grafana_operator_install.png)

- `Install` 버튼을 선택하여 설치를 계속 진행합니다.

  ![03_grafana_operator_install_02](C:\Works\01_자료\01_OCP\05_OCP_Demo_hyou\brown_bag_gitops\grafana-mon\03_grafana_operator_install_02.png)

- Grafana Operator 설치가 완료되면, Grafana 인스턴스를 생성합니다. 가이드에서는 `argocd-grafana` 이름으로 인스턴스를 생성합니다.

  ![04_argocd_instance](C:\Works\01_자료\01_OCP\05_OCP_Demo_hyou\brown_bag_gitops\grafana-mon\04_argocd_instance.png)

- 생성한 인스턴스의 상태를 확인합니다.

  ![05_argocd_grafana_instance](C:\Works\01_자료\01_OCP\05_OCP_Demo_hyou\brown_bag_gitops\grafana-mon\05_argocd_grafana_instance.png)

### 2. Connecting Prometheus to our Custom Grafana

`openshift-gitops` 네임스페이스의 커뮤니티 grafana를 `openshift-monitoring` 네임스페이스의 OpenShift 모니터링에 연결합니다.



Grafana-Serviceaccount 서비스 계정은 Grafana 인스턴스와 함께 만들어졌으며, `cluster-monitoring-view` Cluster Role을 부여합니다.

```bash
$ oc adm policy add-cluster-role-to-user cluster-monitoring-view -z grafana-serviceaccount -n argocd-grafana
```



이 서비스 계정의 bearer token은 OpenShift 모니터링 네임스페이스에서 Prometheus에 대한 액세스를 인증하는데 사용됩니다. 다음 명령은 이 token을 표시합니다.

```bash
$ oc serviceaccounts get-token grafana-serviceaccount -n argocd-grafana
```

- 출력 메시지 예시

  ```bash
  $ oc serviceaccounts get-token grafana-serviceaccount -n argocd-grafana
  eyJhbGciOiJSUzI1NiIsImtpZCI6IlBOVkJURVR6WmJNLUgwbllMQ05ZTUUweXhfUHk0a0tHTHdkUXA4UkdIOVUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJhcmdvY2QtZ3JhZmFuYSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJncmFmYW5hLXNlcnZpY2VhY2NvdW50LXRva2VuLTh6OTQ3Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImdyYWZhbmEtc2VydmljZWFjY291bnQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiI4ZDEzMTU3MC1hN2QwLTRjODYtOWY2Ni1hYTNkMjkyYTE2ZTYiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6YXJnb2NkLWdyYWZhbmE6Z3JhZmFuYS1zZXJ2aWNlYWNjb3VudCJ9.ccfqtHNvUJmYaqjjOfBqbhz2UB7SepRv2LUEG7ohilLssZkIC6g0hG4ec9IUQ_RAP5u-88oerRSxw6Te2yHOgnQU6mCZ2DfIqpdPO2cz4HpdRhiezwgMQABj4Q_Rl5gOYeILCN63Xc_hSosY4uQUIALHR8oxWU8gWGb5uvjgDE3CC9pkNQpRJArf71i4xKTcTk85dW5NHpvLtwacfKYjx22FqoKZu64OqaWi2VGs-9msYLS9guTs3teHFb5-0P9js9OcMKl87Lw3iOyjIpp9hAqm2a_3m9s-JSLg42T8hlaTo5T76ZS3D0KA1HaMTBXYMMC1sOX2b70SXSCJozc8DQcWluoOs92D96fdnbzNe-8lH05mCq6pI8m1AHyx5Efm0ts5BicdNpP84qp5Um3l3XlO1ackxsoLvN7Q9eq2sYq21SxoA8Gl6yvZK5vEN6uCDoeM9qtut1yUKWGctq62fomlTH60UD17ShhBs96lcle0XlZu03fPoysa1PKE4iUorxsYwp5vkYdZmqNYwk14vlBA64f0w_vIwTZerZjXptQa0WPKeH-l2frDI6Qyrij5jZopIVxDvHRQG2QLboHgJ_cigh9LwJOcKSEp3BKn1c1EMKmKwbKL-wFqmJpG-wBRQsbqZFeGWJHwVHMM_9BUHRGqanqo20ieuVhEpwgRlAw
  ```

- Grafana Data Source 리소스 생성

  아래 YAML에서 `${BEARER_TOKEN}` 값은 위에서 출력된 Token으로 대체하여 작성한 후 리소스를 생성합니다.

  ```yaml
  apiVersion: integreatly.org/v1alpha1
  kind: GrafanaDataSource
  metadata:
    name: prometheus-grafanadatasource
    namespace: argocd-grafana
  spec:
    datasources:
      - access: proxy
        editable: true
        isDefault: true
        jsonData:
          httpHeaderName1: 'Authorization'
          timeInterval: 5s
          tlsSkipVerify: true
        name: Prometheus
        secureJsonData:
          httpHeaderValue1: 'Bearer ${BEARER_TOKEN}'
        type: prometheus
        url: 'https://thanos-querier.openshift-monitoring.svc.cluster.local:9091'
    name: prometheus-grafanadatasource.yaml
  ```

- 웹콘솔 생성 예시

  ![06_argocd_grafana_datasource](C:\Works\01_자료\01_OCP\05_OCP_Demo_hyou\brown_bag_gitops\grafana-mon\06_argocd_grafana_datasource.png)

- Grafana Route 생성

  Grafana 인스턴스에 접속할 수 있는 라우트 주소 생성

  - 명령어로 생성하는 방법

    - 서비스 확인

      ```bash
      $ oc get svc -n argocd-grafana
      NAME                                                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
      grafana-operator-controller-manager-metrics-service   ClusterIP   172.30.229.147   <none>        8443/TCP   7m24s
      grafana-service  
      ```

    - 서비스 노출 (라우트 생성)

      grafana-service를 외부에서 접속하기 위해 라우트를 생성합니다.

      ```bash
      $ oc expose svc/grafana-service -n argocd-grafana
      route.route.openshift.io/grafana-service exposed
      ```

    - 라우트 확인

      ```bash
      $ oc get route -n argocd-grafana
      NAME              HOST/PORT                                                                        PATH   SERVICES          PORT      TERMINATION   WILDCARD
      grafana-service   grafana-service-argocd-grafana.apps.cluster-677x8.677x8.sandbox542.opentlc.com          grafana-service   grafana                 None
      ```

  - admin 계정 패스워드 확인 

    ```bash
    $ oc extract secret/grafana-admin-credentials -n argocd-grafana --to=-
    ```

### 3. Access Grafana Instance

위에서 확인한 URL 및 계정 정보를 통해 Grafana 인스턴스에 접속합니다.

- Grafana Instance login

  ![07_argocd_grafana_instance_login](C:\Works\01_자료\01_OCP\05_OCP_Demo_hyou\brown_bag_gitops\grafana-mon\07_argocd_grafana_instance_login.png)



### 4. Creating ArgoCD Dashboard 

- `+` > Create > Import 선택 > Dashboard ID 입력 (grafana.com에서 확인한)  > Load

  ![08_argocd_grafana_dashboard_load](C:\Works\01_자료\01_OCP\05_OCP_Demo_hyou\brown_bag_gitops\grafana-mon\08_argocd_grafana_dashboard_load.png)

- Import 

  ![09_argocd_grafana_dashboard_import](C:\Works\01_자료\01_OCP\05_OCP_Demo_hyou\brown_bag_gitops\grafana-mon\09_argocd_grafana_dashboard_import.png)

- Dashboard 확인

  ![10_argocd_instance_dashboard](C:\Works\01_자료\01_OCP\05_OCP_Demo_hyou\brown_bag_gitops\grafana-mon\10_argocd_instance_dashboard.png)





참고 URL) https://grafana.com/grafana/dashboards/14584
