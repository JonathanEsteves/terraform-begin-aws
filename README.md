# 🌐 Infraestrutura AWS com Terraform — VPC, Subnets, NAT e EC2

Este projeto expande a infraestrutura na AWS utilizando Terraform, com foco na criação de uma VPC completa: subnets públicas e privadas, gateway NAT, Internet Gateway e instância EC2 em subnet privada. A configuração foi realizada com o módulo oficial `terraform-aws-modules/vpc/aws`.

---

## 📌 Visão Geral

- 🧱 Infraestrutura como Código com Terraform
- 🌍 VPC customizada (CIDR, subnets, NAT, IGW)
- 🔐 Instância EC2 em subnet privada com NAT Gateway
- 📦 Uso de módulos oficiais da comunidade Terraform

---
---

## 🗂️ Estrutura do Projeto

```bash
infra-aws-vpc/
├── main.tf               # Declaração da VPC, EC2 e seus recursos principais
├── provider.tf           # Configuração da AWS
├── variables.tf          # Declaração das variáveis
├── outputs.tf            # Saída de dados (ex: IPs, Subnets)
├── terraform.tfvars      # Valores reais das variáveis (NÃO subir ao GitHub)
├── .gitignore            # Ignora arquivos sensíveis e internos do Terraform
└── README.md             # Documentação do projeto
```

---

## ⚙️ Pré-requisitos

- ✅ Conta na AWS (com par de chaves SSH criado)
- ✅ Terraform instalado [→ Instale aqui](https://developer.hashicorp.com/terraform/downloads)
- ✅ AWS CLI configurado localmente com `aws configure`

---

## 🚧 Passo a passo — O que foi feito

### 1️⃣ Inicialização do projeto
```bash
mkdir infra-aws-terraform && cd infra-aws-terraform
terraform init
```

### 2️⃣ Configuração do Provider (provider.tf)
```hcl
provider "aws" {
  region = var.aws_region
}
```

### 3️⃣ Módulo VPC (main.tf)
```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.19.0"

  name = "terraform-vpc"
  cidr = "10.0.0.0/16"

  azs              = ["us-west-2a", "us-west-2b", "us-west-2c"]
  private_subnets  = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  public_subnets   = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]

  enable_dns_hostnames = true
  enable_nat_gateway   = true
  enable_vpn_gateway   = true

  tags = {
    Terraform   = "true"
    Environment = "dev"
  }
}
```

### 4️⃣ Provisão da instância EC2 em subnet privada
```hcl
resource "aws_instance" "app_server" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  subnet_id = module.vpc.private_subnet[0]
  vpc_security_group_ids = [module.vpc.default_security_group_id]
  tags = {
    Name = var.instance_name
  }
}
```

### 5️⃣ Declaração das variáveis (variables.tf)
```hcl
variable "aws_region"    { description = "Região AWS" }
variable "instance_type" { description = "Tipo da instância" }
variable "instance_name" { description = "Tag de nome da instância" }
```

### 6️⃣ Criação do arquivo de variáveis reais (terraform.tfvars)
```hcl
aws_region    = "us-west-2"
instance_type = "t2.micro"
instance_name = "learn-terraform"
```

> ⚠️ Esse arquivo está no `.gitignore` para proteger informações sensíveis.

### 7️⃣ Execução do Terraform
```bash
terraform plan      # Visualiza o que será criado
terraform apply     # Aplica e provisiona a infraestrutura
```

---

## 📤 Outputs (opcional)

Adicione no `outputs.tf` se quiser retornar IP da instância:
```hcl
output "vpc_id" {
  value = module.vpc.vpc_id
}

output "private_subnet_ids" {
  value = module.vpc.private_subnets
}

output "public_subnet_ids" {
  value = module.vpc.public_subnets
}

```

---

## 🔐 Segurança

- Dados sensíveis (ex: `terraform.tfvars`, `*.tfstate`) estão protegidos via `.gitignore`
- Não commitamos credenciais nem tokens
- Pode-se utilizar `AWS Secrets Manager` ou `GitHub Secrets` para projetos mais avançados
- Instancia EC2 em subnet privada(acesso via NAT Gateway)
- Utilização de variáveis internas para evitar hardcoded

---

## ✨ Possíveis melhorias futuras

- Adicionar Security Groups personalizados
- Conectar com S3 ou RDS
- Configurar CI/CD com GitHub Actions
- Modularização com Terraform Modules
- Utilizar `remote backend` (S3 + DynamoDB) para ambientes colaborativos

---

📚 Fontes oficiais utilizadas

    📘 Tutorial oficial HashiCorp

    📦 Módulo terraform-aws-vpc

## 🤝 Autor

**Jonathan Esteves** — _DevOps em transição com propósito e visão de impacto através da tecnologia._

---

## 📎 Licença

MIT © 2025 - Projeto educacional e open source.
