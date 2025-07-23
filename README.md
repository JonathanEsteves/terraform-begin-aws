# Infraestrutura com Terraform na AWS

Este projeto cria uma instância EC2 Ubuntu na AWS usando Terraform.

## Variáveis

Crie um arquivo `terraform.tfvars` com o seguinte conteúdo:

```hcl
aws_region   = "us-west-2"
instance_type = "t2.micro"
key_name     = "nome-da-sua-chave"
