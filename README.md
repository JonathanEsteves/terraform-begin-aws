# 🚀 Infraestrutura AWS com Terraform — Instância EC2 Ubuntu 24.04

Este projeto provisiona uma instância EC2 Ubuntu 24.04 na AWS utilizando Terraform, com foco em boas práticas, segurança e estrutura modular para escalar em futuros projetos de infraestrutura como código.

---

## 📌 Visão Geral

- 🧱 **Infraestrutura como Código (IaC)** com Terraform
- ☁️ **AWS EC2 Ubuntu 24.04** provisionada dinamicamente
- 🔐 Segurança com `.gitignore` e variáveis sensíveis externas
- 🧩 Modular e personalizável via arquivos `.tfvars`

---

## 🗂️ Estrutura do Projeto

```bash
infra-aws-terraform/
├── main.tf               # Recurso EC2
├── provider.tf           # Configuração da AWS
├── variables.tf          # Declaração das variáveis
├── outputs.tf            # Saída de dados (ex: IP público)
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

### 3️⃣ Busca da AMI Ubuntu 24.04 (main.tf)
```hcl
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"] # Canonical

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd-gp3/ubuntu-noble-24.04-amd64-server-*"]
  }
}
```

### 4️⃣ Provisão da instância EC2
```hcl
resource "aws_instance" "app_server" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
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
output "public_ip" {
  value = aws_instance.app_server.public_ip
}
```

---

## 🔐 Segurança

- Dados sensíveis (ex: `terraform.tfvars`, `*.tfstate`) estão protegidos via `.gitignore`
- Não commitamos credenciais nem tokens
- Pode-se utilizar `AWS Secrets Manager` ou `GitHub Secrets` para projetos mais avançados

---

## ✨ Possíveis melhorias futuras

- Adicionar Security Groups personalizados
- Conectar com S3 ou RDS
- Configurar CI/CD com GitHub Actions
- Modularização com Terraform Modules
- Utilizar `remote backend` (S3 + DynamoDB) para ambientes colaborativos

---

## 🤝 Autor

**Jonathan Esteves** — _DevOps em transição com propósito e visão de impacto através da tecnologia._

---

## 📎 Licença

MIT © 2025 - Projeto educacional e open source.