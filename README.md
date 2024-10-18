# Desafio Técnico - Vaga de Estágio em DevOps - VExpenses

## Descrição Geral

Este projeto consiste na análise, modificação e melhoria de um arquivo Terraform (`main.tf`) que define a criação de uma infraestrutura básica na AWS. A infraestrutura inclui a criação de uma VPC, Subnet, Grupo de Segurança, Key Pair e uma instância EC2. O objetivo deste desafio é aprimorar o código original com melhorias de segurança e automação.


---

## Tarefa 1 - Análise Técnica do Código Original

### 1.1 Provedor AWS
O código utiliza o provedor AWS para definir a região onde os recursos serão criados:
- **Região:** us-east-1

### 1.2 Variáveis
Duas variáveis são definidas:
- **`projeto`**: Usada para nomear os recursos com o nome do projeto.
- **`candidato`**: Usada para incluir o nome do candidato nos recursos.

### 1.3 TLS Key Pair
Um par de chaves SSH é gerado com criptografia RSA de 2048 bits:
- **Key Pair:** Usado para acessar a instância EC2 via SSH.

### 1.4 VPC e Subnet
- **VPC:** Cria uma Virtual Private Cloud com o bloco CIDR `10.0.0.0/16`.
- **Subnet:** Cria uma sub-rede dentro da VPC com o bloco CIDR `10.0.1.0/24`.

### 1.5 Gateway de Internet e Tabela de Rotas
- **Internet Gateway:** Conecta a VPC à Internet.
- **Route Table:** Configura uma tabela de rotas para permitir que o tráfego saia da VPC para a internet.

### 1.6 Security Group
- **Ingress (Entrada):** Permite conexões SSH (porta 22) de qualquer IP (`0.0.0.0/0`).
- **Egress (Saída):** Permite todo o tráfego de saída da instância.

### 1.7 Instância EC2
- **AMI:** Utiliza uma AMI Debian 12 para a instância EC2.
- **Instância:** O tipo da instância é `t2.micro`.
- **User Data:** A instância executa atualizações automáticas do sistema ao ser criada.

### 1.8 Outputs
- **Chave Privada:** Exibe a chave privada gerada.
- **IP Público da EC2:** Exibe o IP público da instância EC2 criada.

---

## Tarefa 2 - Modificações e Melhorias

### 2.1 Melhorias de Segurança

Foram feitas melhorias no código do Security Group para restringir o acesso SSH a um IP confiável e habilitar o tráfego HTTP na porta 80, para o servidor Nginx.

- **Regras de Entrada (Ingress):**
  - **SSH (porta 22):** Acesso restrito ao IP do desenvolvedor.
  - **HTTP (porta 80):** Acesso liberado para qualquer IP (`0.0.0.0/0`).
  
- **Regras de Saída (Egress):** Permite todo o tráfego de saída.

### 2.2 Automação da Instalação do Nginx

Um script foi adicionado à instância EC2, utilizando o campo `user_data`, para instalar automaticamente o servidor web Nginx, iniciá-lo e habilitá-lo para ser executado em todas as inicializações do sistema.

```bash
#!/bin/bash
apt-get update -y
apt-get install -y nginx
systemctl start nginx
systemctl enable nginx
```

### 2.3 Outras Melhorias

- **Versões:** Foi adicionado a declararação da versão do provedor AWS para garantir que o Terraform use uma versão específica do provedor, o que pode evitar problemas de compatibilidade no futuro.

## 3 - Instruções de Uso

### 3.1 Pré-requisitos

Antes de começar, certifique-se de ter o seguinte:

- **Conta AWS:** Com permissões suficientes para criar recursos como VPC, EC2, Security Group, etc.

- **AWS CLI configurado:** O Terraform deve poder acessar sua conta AWS.
Terraform instalado: Certifique-se de que o Terraform está instalado e configurado no seu ambiente.

### 3.2 Passos para Aplicar a Configuração

1. Clone este repositório:
   ```bash
   git clone git@github.com:PauloMats/VExpenses_Desafio_Tecnico.git
   cd VExpenses_Desafio_Tecnico
   ```

2. Inicialize o Terraform:
   ```bash
    terraform init
    ```
3. Valide o código Terraform:
    ```bash
    terraform validate
    ```
4. Visualize o plano de execução:
    ```bash
    terraform plan
    ```
5. Aplique a configuração:
    ```bash
    terraform apply
    ```
**Confirme a aplicação digitando yes quando solicitado.**

6. Após a execução, o Terraform exibirá o endereço IP público da instância EC2. Você pode acessá-lo pelo navegador para verificar se o Nginx está funcionando:
   ```bash
   http://<ec2_public_ip>
   ```

### Considerações Finais

**Melhorias Aplicadas:**
  
- Segurança: O acesso SSH foi restringido a um IP específico, e o tráfego HTTP foi liberado para acesso ao servidor Nginx.

- Automação: A instalação e inicialização do Nginx são feitas automaticamente após a criação da instância EC2.

- Versão do Provedor: A versão do provedor AWS foi especificada para garantir a compatibilidade com o código.

### Autor  

Este projeto foi desenvolvido por Paulo Mateus dos Santos Silva, como parte do processo seletivo para a vaga de Estágio em DevOps na VExpenses.