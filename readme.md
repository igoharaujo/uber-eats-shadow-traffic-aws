# ShadowTraffic: Uber Eats para AWS S3

Este projeto gera dados sintéticos de Uber Eats e grava os objetos no bucket `s3://owshq-shadow-traffic`, na região `us-east-1`. Os arquivos principais preservam os prefixos de dados e os lookups entre geradores.

## Pré-requisitos

- Docker Desktop instalado e iniciado.
- Acesso ao repositório Git.
- Arquivo de licença `st-key.env` fornecido pela ShadowTraffic. Ele não deve ser compartilhado nem enviado ao Git.
- Credenciais AWS com permissão no bucket indicado abaixo.

## Clonar e preparar

Use a URL do repositório fornecida pela sua equipe ou plataforma Git:

```shell
git clone <URL_DO_REPOSITORIO>
cd uber-eats-shadow-traffic-main2
```

Coloque o arquivo de licença `st-key.env` na raiz do projeto. Baixe a imagem do ShadowTraffic:

```shell
docker pull shadowtraffic/shadowtraffic:latest
```

## Permissões AWS

A identidade AWS usada pelo processo precisa destas permissões:

- `s3:ListBucket` no bucket `arn:aws:s3:::owshq-shadow-traffic`.
- `s3:GetObject` e `s3:PutObject` nos objetos `arn:aws:s3:::owshq-shadow-traffic/*`.

Crie ou solicite credenciais para uma identidade com essas permissões. Não cole access keys no README, nos JSONs de geração ou diretamente no comando `export`. O bloco abaixo solicita os valores sem mostrá-los na tela; eles ficam somente no ambiente do terminal atual.

```shell
printf 'AWS Access Key ID: '
IFS= read -r -s AWS_ACCESS_KEY_ID
printf '\nAWS Secret Access Key: '
IFS= read -r -s AWS_SECRET_ACCESS_KEY
printf '\nSession token (pressione Enter se não for temporário): '
IFS= read -r -s AWS_SESSION_TOKEN
printf '\n'
export AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY
if [ -n "$AWS_SESSION_TOKEN" ]; then
  export AWS_SESSION_TOKEN
else
  unset AWS_SESSION_TOKEN
fi
```

Se as credenciais forem temporárias, informe também o session token. Ao fechar o terminal, essas variáveis deixam de existir. Para credenciais de longa duração, prefira solicitar à equipe AWS uma forma aprovada de autenticação, como IAM Identity Center ou uma IAM role.

## Executar o gerador

Execute a partir da pasta raiz do repositório, no mesmo terminal em que configurou as credenciais:

```shell
docker run --rm \
  --env-file st-key.env \
  -e AWS_REGION=us-east-1 \
  -e AWS_ACCESS_KEY_ID \
  -e AWS_SECRET_ACCESS_KEY \
  -e AWS_SESSION_TOKEN \
  -v "$PWD/gen/aws/uber-eats.json:/home/config.json:ro" \
  shadowtraffic/shadowtraffic:latest \
  --config /home/config.json
```

O processo gera dados continuamente. Pressione `Ctrl+C` para encerrá-lo.

### Executar o gerador CDC

Execute este comando no mesmo terminal em que configurou as credenciais:

```shell
docker run --rm \
  --env-file st-key.env \
  -e AWS_REGION=us-east-1 \
  -e AWS_ACCESS_KEY_ID \
  -e AWS_SECRET_ACCESS_KEY \
  -e AWS_SESSION_TOKEN \
  -v "$PWD/gen/aws/uber-eats-cdc.json:/home/config.json:ro" \
  shadowtraffic/shadowtraffic:latest \
  --config /home/config.json
```

## Arquivos de configuração

- `gen/aws/uber-eats.json`: conjunto completo de geradores.
- `gen/aws/uber-eats-cdc.json`: geradores CDC.
- `connections/s3-aws.json`: configuração do bucket AWS S3 e da região.

As credenciais não são armazenadas nesses arquivos. O Docker recebe as variáveis AWS exportadas no terminal.

## Outros exemplos

### PostgreSQL: drivers

```shell
docker run --rm \
  --env-file st-key.env \
  -v "$PWD/gen/postgres/drivers.json:/home/config.json:ro" \
  shadowtraffic/shadowtraffic:latest \
  --config /home/config.json
```

### Kafka

```shell
docker run --rm \
  --env-file st-key.env \
  -v "$PWD/gen/kafka/uber-eats.json:/home/config.json:ro" \
  shadowtraffic/shadowtraffic:latest \
  --config /home/config.json
```
