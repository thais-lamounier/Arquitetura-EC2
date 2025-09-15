# Arquitetura de Acesso a Servidores Linux e Windows em VPC

Este projeto descreve como funciona o acesso a servidores Linux e Windows em uma **VPC (Virtual Private Cloud)** utilizando chaves públicas/privadas e portas específicas (SSH e RDP).

---

## 🔹 Visão Geral

- **VPC**: rede virtual onde as instâncias (servidores) estão hospedadas.  
- **Security Group**: firewall que controla o tráfego de entrada e saída.  
- **Instâncias EC2**:
  - **Servidor Linux**: acesso via **SSH** na **porta 22**.  
  - **Servidor Windows**: acesso via **Remote Desktop (RDP)** na **porta 3389**.  
- **Chaves públicas/privadas**:
  - **Chave pública** fica no servidor.  
  - **Chave privada** fica com o usuário que realiza a conexão.  

### Arquitetura (Diagrama)

![Arquitetura EC2](./Diagrama-EC2.jpg)

---

## 🔹 Fluxo da Conexão

### Servidor Linux (SSH - Porta 22)
1. O usuário gera um par de chaves:
   ```bash
   ssh-keygen -t rsa -b 4096 -f minha-chave
Isso gera:
- minha-chave → chave privada (ficará com o usuário).
- minha-chave.pub → chave pública (vai para o servidor).

2. A chave pública é colocada no arquivo:
~/.ssh/authorized_keys
3. Configura-se o Security Group para permitir tráfego TCP/22 apenas do IP do usuário.
4. O usuário conecta via SSH:
chmod 400 minha-chave.pem
ssh -i minha-chave.pem ec2-user@IP_PUBLICO

## Servidor Windows (RDP - Porta 3389)
1. No lançamento da instância, define-se um Key Pair (par de chaves).
2. O Security Group é configurado para permitir tráfego TCP/3389 apenas do IP do usuário.
3. A senha do usuário Administrator é gerada pela AWS e criptografada com a chave pública.
4. Para recuperar a senha:
- No console AWS: Get Windows Password e insira a chave privada (.pem).
5. O usuário conecta via RDP:
  IP_PUBLICO:3389
Usuário: Administrator
Senha: descriptografada com a chave privada.

## Boas Práticas de Segurança
- Nunca abra portas 22 ou 3389 para 0.0.0.0/0.
- Restrinja acesso apenas ao IP necessário (/32).
- Prefira VPN, Bastion Host ou AWS Session Manager em vez de acesso direto da internet.
  
- Para Linux:
  - Desabilite login por senha (PasswordAuthentication no em sshd_config).
- Para Windows:
  - Troque a senha do Administrator após o primeiro login.

- Proteja sempre a chave privada (chmod 400).

## 🔹 Resumo Visual do Diagrama

- Usuário → conecta com chave privada.
- Servidor → contém chave pública.
- Conexão:
 - SSH (porta 22) para Linux.
 - RDP (porta 3389) para Windows.
- Security Group controla quem pode acessar cada porta.





