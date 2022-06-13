---
layout: post
title:  "[Terraform] GitHub Actions를 이용한 간단한 Terraform CI"
date:   2021-01-19 23:00:14 +0900
categories: [devops]
tags : [terraform]
---
GitHub Actions를 이용한 간단한 Terraform용 CI를 추가해보자

<!--more-->

[Terraform](https://www.terraform.io)은 자체 CLI를 이용하여 CI를 구성할 수 있다.

formatting이 권장하지 않는 방법으로 되어 있을 때 에러를 발생시켜 주거나,

파일을 실행시켜보기 전에 문법 등을 확인하여 문제가 있을 시 에러를 발생시켜 준다거나 하는 간단한 것들은 지원해준다.

그 외에도 Terraform이 점점 많이 사용됨에 따라서 [TFLint](https://github.com/terraform-linters/tflint)처럼 lint를 확인할 수 있거나

유용한 Terraform wrapper인 [Terragrunt](https://terragrunt.gruntwork.io)를 만든 곳에서 만든 [Terratest](https://terratest.gruntwork.io)도 있다.

그 중에서 간단하게 파일 foramtting과 문법 확인 정도를 하는 수준의 간단한 CI를 GitHub Actions를 이용하여 작성하는 방법을 소개한다.

GitHub Actions에 대해서 잘 모른다면 [GitHub 공식문서](https://docs.github.com/en/actions)에서 확인하고 공부할 수 있다.

# Lint

우선 앞에서 소개했던 TFLint와 Terraform CLI를 이용하여 간단하게 lint와 formatting 확인을 하는 GitHub Actions를 만들어보자.

파일 내용은 다음과 같다.

{%highlight yaml%}
name: Lint

on: [push, pull_request]

jobs:
  tflint:
    name: TFLint
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@master
      - name: TFLint
        uses: docker://wata727/tflint
  fmt:
    name: Code Format
    runs-on: ubuntu-latest
    container:
      image: hashicorp/terraform:latest
    steps:
      - uses: actions/checkout@master
      - run: terraform fmt --recursive -check
{% endhighlight %}

# Validate
다음은 Terraform CLI를 이용하여 작성한 파일의 문법 등을 확인할 수 있게 만들어보자.

파일 내용은 다음과 같다.

{%highlight yaml%}
name: Continuous Integration

on: [push, pull_request]

jobs:
  terraform:
    name: 'Terraform'
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@master
      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v1
        with:
          terraform_version: {원하는 테라폼 버전}
      - name: Terraform Validate
        run: |
          # 테라폼 파일을 가지고 있는 디렉토리만 골라서 실행하기 위함
          for DIR in $(find . -type f -name '*.tf' | sed -r 's|/[^/]+$||' |sort |uniq)
          do
            terraform init $DIR
            terraform validate $DIR
          done
        env:
          AWS_DEFAULT_REGION: {원하는 AWS 리전} # AWS provider를 사용하지 않는다면 추가하지 않아도 된다
{% endhighlight %}


위 파일들은 [Repository](https://github.com/cold-hometown/skeletons/tree/main/terraform)에서 그 예제를 볼 수 있다.

