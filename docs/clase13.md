# Material para la clase 13

Comienza por crear un Service Principal en Azure:

```bash
az ad sp create-for-rbac --name cursoDevops --role Contributor --scopes /subscriptions/subscription_id
```

Anota toda la información, la necesitarás.

```yml
name: Deploy infrastructure

on:
  push:
    branches: [ "main" ]

jobs:
  terraform:
    env:
      ARM_CLIENT_ID: ${{secrets.CLIENT_ID}}
      ARM_CLIENT_SECRET: ${{secrets.CLIENT_SECRET}}
      ARM_SUBSCRIPTION_ID: ${{secrets.SUBSCRIPTION_ID}}
      ARM_TENANT_ID: ${{secrets.TENANT_ID}}
    name: 'Terraform'
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: ${{ github.workspace }}/infra
    steps:
    - uses: actions/checkout@v2
    - uses: hashicorp/setup-terraform@v1 

    - name: Terraform format
      id: fmt
      run: terraform fmt -check
      continue-on-error: true

    - name: Terraform init
      id: init
      run: terraform init -backend-config="{{secrets.SAS_TOKEN}}"

    - name: Terraform validate
      id: validate
      run: terraform validate -no-color

    - name: Terraform plan
      id: plan
      run: terraform plan -out plan.out

    - name: Terraform apply
      id: apply
      run: terraform apply "plan.out"
```

Commit y espera a ver que todo esté ejecutado
