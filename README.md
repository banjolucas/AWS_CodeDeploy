# CodeDeploy
Configurando o CodeDeploy para Atualização de Versões no EC2

# Iremos separar esse tutorial em 3 etapas:

1. Criar Funções (Roles) no IAM;
2. Configurar a instância EC2 que deseja vincular ao seu CodeDeply;
3. Configurar o CodeDeploy;

# Iniciando as Configurações

# 1. Criar Função (Roles) no IAM

# Deve-se criar duas funções (roles). Uma para a instância EC2 que desejar e outra para o CodeDeploy.

1. EC2: Para a instância EC2 devemos utilizar a seguinte política “AmazonEC2RoleforAWSCodeDeploy”. Antes de finalizar o processo eu preciso dar um nome para essa função (role), eu optei por usar o seguinte nome “FuncaoEC2CodeDeploy".

2. CodeDeploy: Para o CodeDeploy devemos utilizar a seguinte política “AWSCodeDeployRole”. Antes de finalizar o processo eu preciso dar um nome para essa função (role), eu optei por usar o seguinte nome “FuncaoCodeDeploy".

OBS: Caso você tenha alguma dificuldade em como criar um ‘role’ na AWS, siga esse passo a passo (https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-sharing-logs-create-role.html) utilizando as políticas informadas acima, uma para o EC2 e outra para o CodeDeploy.

# 2. Configuração no EC2:

# Na instância do EC2 são necessários dois passos da configuração.

1. Modificar a função IAM na instância do EC2 selecionada. Lembre-se de escolher a função criada no início deste tutorial para o EC2 (“FuncaoEC2CodeDeploy”).

2. Criar uma Tag na instância EC2. Essa Tag será utilizada para vincular ao Codeploy. Exemplo "CodeDeployHomologação".

# Instalando o 'Agent CodeDeploy' na instância EC2.

Primeiramente você deverá acessar o terminar do linux através do Putty ou qualquer outra plataforma que acesse o seu servidor via SSH e em seguida rodar o seguinte comando:

`sudo service codedeploy-agent status`

Se o retorno for igual a essa mensagem: ‘No AWS CodeDeploy agent running’, você deverá instalar o agente codedeploy na sua instância EC2.

OU

Se o retorno for igual a essa mensagem: ‘The AWS CodeDeploy agent is running’, não será necessário instalar, visto que o mesmo está sendo executado na máquina podendo pular para próxima etapa que é a configuração do CodeDeploy.

Caso seja necessário instalar, seguir os passos abaixo:

`sudo yum update`
`sudo yum install ruby`
`sudo yum install wget` 
`cd /home/ec2-user`

# Atenção!!! ao executar o próximo comando, favor ler a observação que está logo abaixo do comando para que você faça a substituíção dos campos da forma correta;

`wget https://bucket-name.s3.region-identifier.amazonaws.com/latest/install`

OBS: bucket-name é o nome do bucket do Amazon S3 que contém os arquivos do kit de recursos do CodeDeploy para sua região. Region-identifier é o identificador da sua região. Por exemplo, para Região do Leste dos EUA (Ohio), substitua bucket-name por aws-codedeploy-us-east-2 e region-identifier por us-east-2. Para obter uma lista de nomes de buckets e identificadores de região, consulte Nomes dos poços do kit de recursos por Região (https://docs.aws.amazon.com/pt_br/codedeploy/latest/userguide/resource-kit.html#resource-kit-bucket-names).

`chmod +x ./install`

Para instalar a versão mais recente do CodeDeploy agente:

`sudo ./install auto`

Para verificar se o serviço está em execução, execute o seguinte comando:

`sudo service codedeploy-agent status`

OBS: Segundo a documentação da AWS, não é mais necessário reiniciar a instância EC2 após fazer instalações ou modificações, porém tanto na instância de estudos quanto na instância de produção, foi necessário reiniciar a instância para que as configurações fossem realmente ativadas na mesma.

# 3.	Configurando o CodeDeploy

# Após verficarmos e instalarmos o 'Agent CodeDeploy' iremos agora configurar a plataforma do CodeDeploy para que ela possa fazer o Deploy na instância EC2 que configuramos.

1. Na plataforma do CodeDeploy iremos clicar na opção 'Criar Aplicativo';

2. Com a tela aberta iremos colocar o 'Nome do Aplicativo' e a 'Plataforma de Computação'.

- Nome do Aplicativo: 'CodeDeploy_NomeDoProjeto'
- Plataforma de Computação: Selecione "EC2/local"

Após preencher essas informações, clique em 'Criar Aplicativo'. Em seguida com o aplicativo criado, você deve clicar nele e será redirecionado para uma página dentro do aplicativo selecionado.

3. Na tela em que você foi redicionadio haverá um botão com em que você deverá clicar com o seguinte nome "Criar Grupo de Implantação".

4. Na tela "Criar Grupo de Implatação" nós iremos preencher os campos com as seguintes informações que irei exemplificar abaixo:

- Nome do Grupo de Implantação: NomeDoProjeto_1 (OU o nome que preferir);
- Função de Serviço: "FuncaoCodeDeploy" (Nome da função que nós criamos no início deste tutorial para o CodeDeploy);
- Tipo de Implantação: No Local;
- Configuração do Ambiente: Selecionar a opção "Instâncias do EC2" e em seguida, adicionar a TAG que você criou na configuração da instância EC2 para vincular com o CodeDeploy, cujo o nome dado foi "CodeDeployHomologação";
- Configurações de Implantação: Selecione "CodeDeployDefault.OneAtATime";

Feito isso, reicinie a sua instância EC2 para que a instância reinicie com as suas novas configurações e para que não haja erro no Deploy em relação a vinculo de CodeDeploy e EC2.

# Prontinho, agora é só linkar o seu CodeDeploy ao CodePipeline ou a Pipeline do Azure DevOps para fazer suas implantações.

















