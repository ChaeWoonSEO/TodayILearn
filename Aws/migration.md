# terraform migration

## Infra Installation
AWS 기반 terraform backend 생성을 위해 s3 및 다이나모 DB 생성 가이드는 아래 링크 참고.
## terraform state migration
1. tf remote backend 설정로컬의 테라폼 백엔드를 테라폼 클라우드의 organization 과 workspace 를 바라보도록 설정해주어야 현재 테라폼 클라우드에 저장되어있는 상태파일을 통해 마이그레이션 진행 할 수 있음하단 
`Provider.tf 1 참고`

2. terraform state 가져와서 백업현재 테라폼 클라우드에 저장되어있는 상태파일을 로컬로 백업하는 과정, 이는 마이그레이션 하는 과정에서 문제가 생길 시 그냥 해당 state 파일을 Push 하기 위함. 혹은 state 파일 의 수정이 필요 할 경우 참고 용도 및 백업 용도 
    ```bash
    terraform init terraform state pull > terraform.tfstate
    ```

3. terraform cloud 였던 백엔드를 s3 로 변경 현재 로컬의 테라폼 작업환경의 백엔드가 terraform 클라우드로 설정하였는데, 이를 이전 단계들에서 생성하였던 s3 백엔드로 다시 재 설정 각 디렉토리의 `versions.tf` 파일 명칭 `provider.tf` 로 변경 및 데이터 변경  
`provider.tf 2 참고`

4. 모듈 및 기존 테라폼 코드에서 변경점이 있을경우 수정,기존 모듈을 끌고 와야할것 같을 경우, 먼저 깃랩의 원본 모듈 소스를 다른 repo로 생성 하든, 기존 모듈을 그대로 차용하든 하여 진행하지만, 기존 모듈에서 테라폼 클라우드를 사용하는것 같다면, 이를 필수적으로 s3 백엔드로 변경 해주어야 한다. 

    https://developer.hashicorp.com/terraform/language/modules/sources#generic-git-repository 참고해서 소스 변경 


5. 아래 명령어를 통해 특정 리소스만 지정해서 어느 부분이 변경되는지 확인 할 수 있으며, 또한 `--target` 옵션을 제거하여 모든 파일에 대해 변경점을 검토 해볼 수 있다.
   ```bash
   terraform plan --target="플랜 할 리소스명"
   ```
6. 변경할게 생기면 state list 및 모듈 참고해서 어떤점이 변경되는지 확인해서 변경ex) 
    ```bash
    terraform state list | grep module.vpc
    ```
7. 만약 이전단계에서 기존 모듈에서 다른 모듈 혹은 리소스로 생성 타입을 변경했을 경우 state 파일도 이와같이 맞춰주어야 한다. 윗 단계의 명령어를 통해, state list 들을 조회 해보며,모듈 이름을 뺴먹은것이 없는지 재차 확인 해보면서 진행해야한다. 
   아래는 명령어는 예시이다. 
   ```bash
    terraform state mv 'module.vpc.module.vpc.aws_vpc.this[0]' 'module.vpc.aws_vpc.this[0]'
    ```
8. terraform plan 진행 시에, 아무런 변경사항이 없을 경우 해당 workspace에 대한 작업은 완료된것이다.

provider.tf 1
```tf
terraform {
  backend "remote" {
    organization = "org_name"

    workspaces {
      name = "workspace_name"
    }
  }
}

```
provider.tf 2
```tf
terraform {
  backend "s3" {
    bucket         = "backend 용으로 만들어놓은 bucket 이름"
    key            = "버킷내부에 state 파일을 저장할 위치"
    region         = "ap-northeast-2"
    dynamodb_table = "terraform-lock"
    encrypt        = true
  }
  required_providers {
    aws = {
      source  = "hashicorp/aws"
    }
  }
}
```



> ⚠️ 에러상황 ⚠️

1. provider version 관련 오류
    ```
    ╷
    │ Error: Incompatible provider version
    │
    │ Provider registry.terraform.io/hashicorp/template v2.2.0 does not have a package
    │ available for your current platform, darwin_arm64.
    │
    │ Provider releases are separate from Terraform CLI releases, so not all providers are
    │ available for all platforms. Other versions of this provider may have different
    │ platforms supported.
    ╵
    ```
     위 에러에 대해 아래 해결방법으로 진행 아래와 같이 진행

    ```bash
    git clone https://github.com/hashicorp/terraform-provider-template
    cd terraform-provider-template
    
    # go 없을 경우 설치
    brew install go
    
    go build
    mkdir -p  ~/.terraform.d/plugins/registry.terraform.io/hashicorp/template/2.2.0/darwin_arm64
    mv terraform-provider-template ~/.terraform.d/plugins/registry.terraform.io/hashicorp/template/2.2.0/darwin_arm64/terraform-provider-template_v2.2.0_x5
    
    chmod +x ~/.terraform.d/plugins/registry.terraform.io/hashicorp/template/2.2.0/darwin_arm64/terraform-provider-template_v2.2.0_x5
    
    ```


