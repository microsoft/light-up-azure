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

![image](https://user-images.githubusercontent.com/102738814/216808999-d776db87-4b9c-4dcd-ac63-fbe02ab28011.png)

![image](https://user-images.githubusercontent.com/102738814/216809017-7ad51d41-efac-4bf0-b8fc-df6520d2f9b1.png)

새로운 탭을 연 후 shell.azure.com에 접속하여 방금 생성한 VM의 public IP로 ssh연결합니다.
![image](https://user-images.githubusercontent.com/102738814/216809023-de0db236-4bf0-4453-930d-494efd64efb7.png)

id와 password를 입력한 후 stress툴을 설치합니다.

```
sudo apt-get update
sudo apt install stress
```
luavm 에서 캡쳐 버튼을 눌러 이미지를 만듭니다.
![image](https://user-images.githubusercontent.com/102738814/216809145-1fe4bd79-7445-480f-a981-e3167de5af9e.png)

이 때 이미지를 저장할 Compute Gallery도 함께 생성합니다.
![image](https://user-images.githubusercontent.com/102738814/216809156-b571b836-3b29-49ed-ae5f-fbf99e4af627.png)

![image](https://user-images.githubusercontent.com/102738814/216809162-fa6618bd-6559-4027-9401-872ec06e406d.png)

버전은 0.0.0으로 명시합니다.
![image](https://user-images.githubusercontent.com/102738814/216809165-0dd6d69c-7e93-409c-b46b-2d1c8668e089.png)

리소스 배포가 완료됐을 경우 definition리소스를 클릭하여 0.1.0 버전의 이미지를 하나 더 생성합니다.
![image](https://user-images.githubusercontent.com/102738814/216809170-ecf06a9d-fdb1-4c1c-b58f-4bde17c96722.png)

![image](https://user-images.githubusercontent.com/102738814/216809175-e3106871-3a71-45b0-8e45-ab2b25d397c3.png)

버전 변경(0.0.0 -> 0.1.0)만 확인할 것이기 때문에 기존 luavm의 disk를 기반으로 0.1.0 버전의 이미지를 만듭니다.
![image](https://user-images.githubusercontent.com/102738814/216809179-e9f0474c-eae6-4226-9a3c-6341db1b8fc6.png)

0.1.0 버전까지 제작 후 비용 절감을 위해 luavm은 삭제합니다.
![image](https://user-images.githubusercontent.com/102738814/216809182-0123045f-6d25-4b19-99b4-39b9cda592e0.png)


![image](https://user-images.githubusercontent.com/102738814/216809185-f80cba8a-38e2-40d3-9142-e54de5288671.png)

컴퓨팅 갤러리에서 0.0.0 이미지를 기반으로 VMSS를 생성합니다.
![image](https://user-images.githubusercontent.com/102738814/216809188-3d115356-40ec-40be-b7c0-cbf837238b60.png)

![image](https://user-images.githubusercontent.com/102738814/216809191-d331e813-49ba-4fb4-a338-d76488a99928.png)

![image](https://user-images.githubusercontent.com/102738814/216809197-f6e651c6-db9e-4bab-a418-1a0fde5ce5ab.png)

![image](https://user-images.githubusercontent.com/102738814/216809250-b66d203a-1081-40bd-afd9-32f7c0ba6842.png)

신규 워크로드에 대해서는 Flexible VMSS를 권장하고 있으나, 실습을 위해 'Uniform 모드(균일성 모드)'를 선택합니다.
![image](https://user-images.githubusercontent.com/102738814/216809259-24b9ef2f-7658-4edb-a586-8d6dc468897e.png)

이미지 버전이 0.0.0이 맞는지 확인합니다. 또한 실습의 편의성을 위해 인증 형식은 암호를 선택합니다. 필요한 값을 모두 입력하였으면 '검토+만들기' 버튼을 눌러 리소스를 배포합니다.
![image](https://user-images.githubusercontent.com/102738814/216809264-0f61f4c1-dc0e-4dd5-a2b2-b9db16bfa60d.png)

생성된 luavmss의 '설정 중'탭에 들어가서 오토스케일링을 설정합니다. '규칙 추가'버튼을 눌러 오토스케일링 조건을 입력합니다. 
![image](https://user-images.githubusercontent.com/102738814/216809266-f73e7491-134c-434b-8317-9ba1b372ec1b.png)

CPU Percentage가 30% 초과일 경우 스케일아웃, 미만일 경우 스케일인 조건을 줍니다. 조금 더 빠른 스케일 아웃을 위해 기간은 5분으로 설정합니다.
![image](https://user-images.githubusercontent.com/102738814/216809271-8d51ff4a-ec64-42e6-844d-97e76ca89572.png)

스케일인 규칙도 추가합니다.
![image](https://user-images.githubusercontent.com/102738814/216809277-2cfc561a-15b8-41a0-b67e-bde12b0dadbd.png)


![image](https://user-images.githubusercontent.com/102738814/216809287-445341fb-6451-4ebb-bc82-d1eab86e12b0.png)

마지막으로 최소값, 최대값, 기본값에 각각 1, 10, 2를 입력한 후 저장 버튼을 누릅니다.
![image](https://user-images.githubusercontent.com/102738814/216809293-79830e81-f74c-4aac-a3c4-23a7f9ee14be.png)

저장 후 규모 감축 정책 탭에서 OldestVM 옵션을 선택한 후 저장 버튼을 누릅니다.
![image](https://user-images.githubusercontent.com/102738814/216810025-380dacca-e433-459f-b74d-c72a03a92c19.png)

인스턴스 탭에서 현재 배포된 VM들이 최신 모델인지 확인합니다. 
![image](https://user-images.githubusercontent.com/102738814/216810031-91c490c6-4a5d-431b-a279-25e9f57281cb.png)

그리고 0번 인스턴스를 클릭해서 어떤 이미지를 기반으로 생성되었는지 확인할 수 있습니다.
![image](https://user-images.githubusercontent.com/102738814/216810034-24dc9c19-8b44-419c-99fd-298a72ccdaab.png)

![image](https://user-images.githubusercontent.com/102738814/216810036-32706700-edcc-456e-94b0-8b95ab39a3bb.png)

혹은 shell.azure.com에서 CLI를 통해 아래 명령어를 통해 현재 참조중인 이미지를 확인할 수 있습니다.
```
az vmss show –resource-group luarg –name luavmss

```
![image](https://user-images.githubusercontent.com/102738814/216810044-2ec78596-d4ab-4336-90fd-0b1453d1f906.png)

이제 VMSS가 참조중인 이미지 버전을 0.1.0으로 바꿔봅니다. 아래 명령어를 통해 바꿀 수 있습니다. 
```
az vmss update --resource-group luarg --name luavmss --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{yourAzureSubscriptionID}/luarg/providers/Microsoft.Compute/galleries/luacg/images/luadefinition/versions/0.1.0

```

여기서 imageReference.id는 아래 화면에서 확인하실 수 있습니다.
![image](https://user-images.githubusercontent.com/102738814/216854824-08aa9f7c-6b20-45db-86f0-74de5229074f.png)

![image](https://user-images.githubusercontent.com/102738814/216810046-02359b1e-ffad-4263-ae0c-eabeb6fab300.png)

VMSS에서 참조하는 이미지 버전은 바꾸었으나 기존 인스턴스는 여전히 0.0.0버전입니다. 아래 명령어를 통해서 확인합니다.
```
az vmss show --name luavmss --resource-group luarg --instance-id 0

```
![image](https://user-images.githubusercontent.com/102738814/216810048-0d0953fe-21ec-4353-a615-32b3c1f439e7.png)

VMSS의 인스턴스 탭에서 확인해 보니 참조 이미지를 변경했기 때문에 최신 모델이 아니라고 표시된 것을 확인할 수 있습니다. 0번 인스턴스에 stress를 주기 위해 0번 인스턴스를 클릭한 후 직렬 콘솔을 클릭합니다.
![image](https://user-images.githubusercontent.com/102738814/216810051-0f17fadf-73db-4a50-a92b-5b28bb114e15.png)


![image](https://user-images.githubusercontent.com/102738814/216810056-fb7ee772-5e5f-4c68-b4bd-3cc7835645b2.png)

id와 password를 입력합니다. 그리고 아래 명령어를 통해 stress를 줍니다.
```
stress --cpu 4
```
![image](https://user-images.githubusercontent.com/102738814/216810058-db7222e2-ef36-4b13-9b46-ca9bb52e2698.png)

잠시 후 기존에 없던 2번과 3번 인스턴스가 생성된 것을 확인할 수 있습니다. 또한 0번, 1번 인스턴스와 달리 최신 모델임을 역시 확인할 수 있습니다.
![image](https://user-images.githubusercontent.com/102738814/216810061-bcd77990-9ae7-4035-9dc4-e72cf2d8e63a.png)

CLI를 통해서도 확인할 수 있습니다.
```
az vmss show --name luavmss --resource-group luarg --instance-id 2
```
![image](https://user-images.githubusercontent.com/102738814/216810064-95c831ea-d70b-478a-a426-226ec310f20f.png)

Ctrl + C 를 눌러 스트레스 툴을 중지시킵니다.
![image](https://user-images.githubusercontent.com/102738814/216810067-3454d877-cd9d-4756-957b-20d429b27b35.png)

CPU Percentage가 낮아짐에 따라 Oldest VM인 0번 및 1번 인스턴스가 삭제되는 것을 확인할 수 있습니다. 이로써 0.0.0버전을 참조하는 VM은 모두 삭제되었고, 0.1.0버전을 참조하는 VM만 남게 되었습니다.
![image](https://user-images.githubusercontent.com/102738814/216810071-f0e1f4d6-48d5-4adc-8839-8c56ee5e384f.png)

VMSS에서 스케일링 활동이 일어날 때 담당자에게 문자나 메일로 알람이 가도록 설정할 수 있습니다. VMSS 화면에서 활동 로그 탭을 클릭합니다.
![image](https://user-images.githubusercontent.com/102738814/216810081-5b1df4f9-8025-4060-ba8e-28ec963f27d3.png)

Autoscale scale down completed를 선택 후 '새로운 경고 규칙' 버튼을 클릭합니다.
![image](https://user-images.githubusercontent.com/102738814/216810088-de10113d-b9b4-4c2f-9369-10c873148468.png)

범위를 확인합니다. 현재 실습에서는 luavmss에 대한 알림만 설정하도록 하겠습니다.
![image](https://user-images.githubusercontent.com/102738814/216810095-567a46e7-0e2d-4625-be2f-07d2af67a374.png)

'조건'탭에서 실패와 성공시 알림을 받을 수 있도록 선택합니다.
![image](https://user-images.githubusercontent.com/102738814/216810098-b0ed74db-b757-44dc-91c5-efe42307092d.png)

알림을 받을 그룹을 생성해야 합니다. 작업 그룹 만들기 버튼을 선택합니다. 
![image](https://user-images.githubusercontent.com/102738814/216810103-cf226f95-af05-4387-9161-e62fcd11421d.png)


![image](https://user-images.githubusercontent.com/102738814/216810106-9a4ebc02-0323-4358-955e-93922d22c1d5.png)

![image](https://user-images.githubusercontent.com/102738814/216810108-ad43a10f-57f6-4ef8-9731-5403f3be9ef7.png)

알림 이름을 설정하고 검토+만들기 버튼을 클릭합니다.
![image](https://user-images.githubusercontent.com/102738814/216810112-63c2c290-6ef1-4bb7-8e07-dd06433707ac.png)

작업 그룹 이름에 방금 만든 그룹이 추가된 것을 확인합니다.
![image](https://user-images.githubusercontent.com/102738814/216810113-eb3ff486-0e49-4c2e-a7f6-041a502fa612.png)

세부 정보에서 경고 규칙 이름을 입력한 후 검토+만들기 버튼을 클릭합니다. 그리고 스케일아웃에 대해서도 같은 방법으로 알림을 설정합니다.
![image](https://user-images.githubusercontent.com/102738814/216810115-ed8fc66e-d9c9-4d84-bb47-9cff69dd2b82.png)

작업 그룹에 포함되면 아래와 같은 이메일을 받게 됩니다.
![image](https://user-images.githubusercontent.com/102738814/216810118-43ff610e-4645-4472-8711-f23adf950b17.png)

스케일링 액션이 발생하면 아래와 같이 이메일로 알림을 받게 됩니다.
![image](https://user-images.githubusercontent.com/102738814/216810120-436b91f2-93b9-46b5-9da7-787d1250dbd0.png)

마지막으로 실습에 사용했던 리소스를 모두 삭제합니다.











