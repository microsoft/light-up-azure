이번 핸즈온랩 세션을 통해 VMSS용 이미지 생성, Compute Gallery 활용, 스케일링 규칙 설정 및 알림 설정에 관해 알아봅니다. 핸즈온랩 데모 시나리오는 아래와 같습니다.

-   VMSS 데모 시나리오
    -   커스텀 이미지 제작용 VM을 한 대 배포합니다(luavm).
    -   배포한 VM에 stress툴을 설치합니다.
    -   VM을 기반으로 이미지를 두 장 만들고(0.0.0, 0.1.0), Compute Gallery에 저장합니다.
    -   0.0.0 이미지를 기반으로 VMSS를 생성합니다(luavmss).
    -   비용 절감을 위해 기존 luavm은 삭제합니다.
    -   luavmss에 CPU percentage를 기반으로 한 오토스케일링 규칙을 설정합니다.
    -   Scale-In시 OldestVM부터 삭제되도록 규칙을 설정합니다.
    -   luavmss\_0 인스턴스가 어떤 이미지를 참조하고 있는지 확인합니다(0.0.0).
    -   CLI 명령어를 통해 0.1.0 이미지를 참조하도록 설정값을 변경합니다.
        -   az vmss update --resource-group luarg --name luavmss --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/luarg/providers/Microsoft.Compute/galleries/luacg/images/luadefinition/versions/0.1.0
    -   CLI 명령어를 통해 이미지를 0.1.0을 참조하도록 설정하였더라도 개별 인스턴스는 여전히 0.0.0이미지 기반임을 확인합니다.
        -   az vmss show --name luavmss --resource-group luarg --instance-id 0
    -   0번 인스턴스의 직렬 콘솔로 접속해서 로그인 후 stress --cpu 4 커맨드를 입력합니다.
    -   인스턴스가 스케일아웃 된 것과 또한 최신 이미지(0.1.0)를 기반으로 생성됐음을  확인합니다.
    -   stress를 멈추고 스케일인 되는 것을 확인합니다. 이 때 OldestVM인 인스턴스 숫자가 작은 VM부터 삭제되는 것을 확인합니다.
    -   Activity log에서 Autoscale scale up completed 와 Autoscale scale down completed 에 대해 알림을 설정합니다.
    -   마지막으로 실습에서 사용했던 리소스 그룹을 삭제합니다.



커스텀 이미지 제작용 VM을 생성합니다. 
![image](https://user-images.githubusercontent.com/102738814/216808780-bbdc5c77-72d3-454b-bf74-a040c83bf14e.png)
![image](https://user-images.githubusercontent.com/102738814/216808872-ad420661-980c-439e-8a1f-b25068b61566.png)
stress 툴 사용을 위해 Ubuntu 이미지를 사용합니다.
![image](https://user-images.githubusercontent.com/102738814/216808891-e8f52771-33c4-4a16-9393-d28c029567f7.png)
![image](https://user-images.githubusercontent.com/102738814/216808901-68c63395-df01-45f8-89a3-45323b4a1312.png)