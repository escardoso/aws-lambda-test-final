# Configuração AWS OIDC

Este repositório está configurado para autenticação AWS via OIDC (OpenID Connect).

## 🔧 Configuração Atual

- **Região AWS**: us-east-1
- **Conta AWS**: 368222779225
- **IAM Role**: aws-gh-role

## 🚀 Como Funciona

O GitHub Actions usa OIDC para assumir uma IAM Role na AWS sem precisar de credenciais estáticas (Access Keys).

### Workflow Configurado

O arquivo `.github/workflows/aws-deploy.yml` está configurado para:

1. ✅ Fazer checkout do código
2. ✅ Configurar credenciais AWS via OIDC
3. ✅ Verificar identidade AWS
4. 🔧 Executar deploy (personalize conforme necessário)

## 🔒 Segurança

- ✅ Sem credenciais estáticas no repositório
- ✅ Tokens temporários gerados automaticamente
- ✅ Permissões limitadas pela IAM Role
- ✅ Auditoria completa via CloudTrail

## 📝 Personalização

Para personalizar o deploy, edite o arquivo `.github/workflows/aws-deploy.yml`:

```yaml
- name: Deploy Application
  run: |
    # Seus comandos aqui
    aws s3 sync ./build s3://meu-bucket
    aws ecs update-service --cluster meu-cluster --service meu-service
```

## 🛠️ Pré-requisitos na AWS

Certifique-se de que a IAM Role `aws-gh-role` tem:

1. **Trust Policy** configurada para GitHub OIDC
2. **Permissões** necessárias para suas operações
3. **Condições** de segurança apropriadas

### Exemplo de Trust Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::368222779225:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
        },
        "StringLike": {
          "token.actions.githubusercontent.com:sub": "repo:OWNER/REPO:*"
        }
      }
    }
  ]
}
```

## 🔍 Troubleshooting

Se encontrar problemas:

1. Verifique se o OIDC Provider está configurado na AWS
2. Confirme as permissões da IAM Role
3. Verifique as condições na Trust Policy
4. Consulte os logs do GitHub Actions

## 📚 Documentação

- [GitHub OIDC](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect)
- [AWS IAM OIDC](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html)
