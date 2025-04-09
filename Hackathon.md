# Hackathon - HTTP Header 값으로 Backend를 선택하여 Azure OpenAI On Your Data를 적용한 Azure OpenAI 서비스 호출

실습을 통하여 생성하고 활용하였던 Azure OpenAI, Azure AI Search, Azure API Management를 활용하여 API Request의 HTTP Header 값에 따라 Azure OpenAI Backend를 선택하여 호출하는 기능을 구현해봅니다.

## Challenge Detail

1. Azure OpenAI는 Azure API Management의 Backend로 연결됩니다. 본 챌린지에서는 두개의 지역 (예:WestUS, EastUS)에 생성된 Azure OpenAI 리소스를 Backend로 연결합니다.

2. 생성된 두개의 Azure OpenAI 리소스 중에 하나(Instance1)에 On Your Data을 사용하여 검색이 되도록 설정합니다. 다른 하나의 Azure OpenAI 리소스(Instance2)에는 기본 설정을 유지합니다.

3. Azure API Management에서 WestUS, EastUS에 생성된 Azure OpenAI를 Backend로 생성하고 연결합니다.

4. API Management에서 Azure OpenAI API 스펙으로 API를 구성합니다. (Azure OpenAI API 마법사 혹은 OpenAPI 3.0 import 기능을 이용)

5. Azure Inbound Policy에 Header 값으로 Backend를 선택하는 Policy를 설정합니다. Header에 "on-your-data"라는 Key가 포함되어있으면 OpenAI Instance1을 호출하고, "on-your-data" Key가 포함 되어있지 않으면 Instance2를 호출합니다.

- Policy 구성시 참고 자료
    - [Azure API Management의 정책](https://learn.microsoft.com/ko-kr/azure/api-management/api-management-howto-policies)
    - [API Management 정책 식](https://learn.microsoft.com/ko-kr/azure/api-management/api-management-policy-expressions)
    - [Common policy expressions](https://github.com/Azure/api-management-policy-snippets/blob/master/policy-expressions/README.md)

        **HTTP header가 존재하는지 체크하는 방법 (Policy Expression)**
        ```c#
        context.Request.Headers.ContainsKey("header-name") == true
        ```

6. Python 코드로 Header 값을 변경하여 API 호출을 테스트 합니다.
    - backend-pool-load-balancing.ipynb의 "🧪 직접 HTTP를 호출하여 API 테스트" 코드를 변경하여 테스트 하실 수 있습니다.

        **Python Code에서 data_source를 Azure AI Search로 설정하는 방법**
        ```c#
        completion = client.chat.completions.create(
            model=deployment,
            messages=[
                {
                    "role": "user",
                    "content": "서울교대부초에 원서 접수를 할 수 있는 조건은 무엇인가요?",
                },
            ],
            extra_body={
                "data_sources":[
                    {
                        "type": "azure_search",
                        "parameters": {
                            "endpoint": "{azure_ai_search_endpoint}",
                            "index_name": "s-index",
                            "authentication": {
                                "type": "api_key",
                                "key": "{azure_ai_search_key}",
                            }
                        }
                    }
                ],
            }
        )
        ```

## Azure 리소스 생성
- Azure OpenAI (WestUS, EastUS)
- Azure Storage Account 및 Container
- Azure AI Search (Basic or Strandard SKU)
- Azure API Management

## 권한 설정
- Storage Account와 Container를 액세스 할 수 있는 권한
