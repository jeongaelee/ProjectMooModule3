# Step4. HTTP Header 값으로 Backend를 선택하여 Azure OpenAI On Your Data를 적용한 Azure OpenAI 서비스 호출

본 실습에서는 API Management의 Backend로 두개의 Azure OpenAI를 설정하고, HTTP Header에 따라 호출할 Backend를 분기합니다. 하나의 Azure OpenAI Backend에는 On Your Data를 적용하여 내가 업로드한 파일에서 쿼리에 대한 응답을 받고, 다른 하나의 Azure OpenAI Backend에서는 일반적인 모델의 응답을 받는 로직을 구현해 봅니다.

## Azure Storage Account 생성

1. [Azure Portal](https://portal.azure.com/)으로 접속하여 Azure Storage Account를 생성합니다.

    <img src="images/onyourdata01.png" width="500"/>

2. 이전 과정에서 생성한 리소스 그룹을 선택하고, 지역은 "West US"를 선택합니다. Primary service와 Primary workload, Performance를 아래와 같이 선택합니다. 본 실습에서는 Redundancy의 GRS 옵션은 필요하지 않으므로, Locally-redundant storage(LRS) 옵션을 선택합니다.

    <img src="images/onyourdata02.png" width="700"/>

3. Advanced 탭에서 "Allow enabling anonymous access on individual containers" 항목을 enable 해줍니다. 기본적으로 Blob Container는 컨텐츠에 대한 익명 액세스를 허용하지 않습니다. 이 설정을 사용하면, 승인된 사용자가 특정 컨테이너에서 익명 액세스를 선택적으로 활성화 할 수 있습니다. 나머지 과정은 디폴트 값으로 선택 후 "Review + create"를 누릅니다.

    <img src="images/onyourdata03.png" width="700"/>
 
4. Data Storage 아래의 Container 메뉴에서 새로운 Container를 생성합니다.

    <img src="images/4-01.png" width="300"/>

5. Container의 이름을 입력하고, Anonymous access level을 다음과 같이 선택합니다.

    <img src="images/4-02.png" width="300"/>

6. Container에 File을 업로드 합니다. Upload를 클릭한 후 본 Repository의 files 폴더에 있는 3개의 파일을 다운로드 받은 후 Container에 업로드 합니다. (참고: 3개의 파일은 2025년도 일부 초등학교의 모집 요강 파일입니다.)

    <img src="images/4-03.png" width="800"/>

## Azure AI Search 생성

1. Azure AI Search를 생성합니다.

    <img src="images/onyourdata04.png" width="500"/>

2. 지역은 "West US"로 선택하고, 가격 티어는 Basic 혹은 Standard를 선택합니다. Free 티어는 본 실습에서 사용될 On Your Data 기능을 적용할 수 없습니다.

    <img src="images/onyourdata05.png" width="600"/>

## Azure OpenAI Instance2에 On Your Data 추가

1. Azure OpenAI Studio의 Home 화면으로 이동하여 "Bring your own data"를 선택합니다.

    <img src="images/onyourdata06.png" width="700"/>

2. Chat playground에서 "Add your data" 탭을 선택한 후, "+ Add a data source"를 클릭합니다.

    <img src="images/onyourdata07.png" width="400"/>

3. 데이터 소스에서 "Azure Blob Storage (preview)"를 선택하고, 위의 단계에서 생성한 Azure Blob Storage와 Azure AI Search 리소스를 선택합니다. 이 데이터 소스를 참조하는데 사용할 인덱스 이름을 입력합니다. 데이터 수집이 완료된 후 제공된 이름의 새 AI Search Index가 생성됩니다.

    <img src="images/4-04.png" width="600"/>

4. Search type을 "Semantic"으로 선택하고 디폴트 청킹 사이즈를 선택합니다.

    <img src="images/onyourdata10.png" width="600"/>

5. Data connection을 위한 Azure 리소스의 인증 타입을 "API Key"로 선택합니다.

    <img src="images/onyourdata11.png" width="600"/>

6. 설정을 리뷰한 후 "Save and close"를 클릭합니다.

    <img src="images/onyourdata12.png" width="600"/>

7. Chat playground에서 "Ingestion in progress"가 표시되는 것을 보실 수 있습니다. 데이터 소스 (Azure Blob Storage)에서 Azure AI Search로 indexing 되고 있는 과정입니다.

    <img src="images/4-05.png" width="400"/>

8. 채팅 창에 "서울교대부초와 중대부초 중에 원서 접수가 먼저 마감되는 곳은 어디인가요?"이라는 프롬프트를 입력합니다. Azure Blob Storage에 업로드 한 파일의 내용에서 응답을 주는 것을 확인할 수 있습니다.

    <img src="images/4-06.png" width="700"/>

## Azure API Management에서 Header로 AOAI Instance 선택하는 Policy 구성

1. Azure API Management에서 이전 단계에서 생성한 AOAI-LB API를 Clone하여 새로운 API를 만듭니다. 

    <img src="images/4-07.png" width="700"/>

2. Design 탭의 Inbound Policy에 아래의 값을 복사하여 붙여 넣습니다. "work-type"이라는 Header 값이 있으면 openai-instance1를 없으면 openai-instance2을 Backend로 설정하는 규칙입니다.

    ```
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@(context.Request.Headers.ContainsKey("work-type") == true)">
                    <set-backend-service backend-id="openai-instance1" />
                </when>
                <when condition="@(context.Request.Headers.ContainsKey("work-type") == false)">
                    <set-backend-service backend-id="openai-instance2" />
                </when>
            </choose>
            <authentication-managed-identity resource="https://cognitiveservices.azure.com" output-token-variable-name="managed-id-access-token" ignore-error="false" />
            <set-header name="Authorization" exists-action="override">
                <value>@("Bearer " + (string)context.Variables["managed-id-access-token"])</value>
            </set-header>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```
3. 다운로드 받은 코드의 on-your-data 폴더 아래에 있는 on-your-data.ipynb 파일로 On Your Data를 Indexing한 Azure OpenAI 서비스에서 응답을 성공적으로 리턴하는지 테스트 합니다.

    <img src="images/4-08.png" width="300"/>

## 실습 순서

* [Step1. Azure API Management를 통하여 Azure OpenAI 액세스 하기](https://github.com/jeongaelee/ProjectMooModule3/blob/main/Step1.md)
* [Step2. Azure API Management로 Token rate limiting](https://github.com/jeongaelee/ProjectMooModule3/blob/main/Step2.md)
* [Step3. Azure API Management의 Backend Load Balancing](https://github.com/jeongaelee/ProjectMooModule3/blob/main/Step3.md)
* [Step4. HTTP Header로 Backend를 선택하여 Azure OpenAI On Your Data를 적용한 Azure OpenAI 서비스 호출](https://github.com/jeongaelee/ProjectMooModule3/blob/main/Step4.md)
