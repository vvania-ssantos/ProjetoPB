# ProjetoPB
Instalar e configurar o Docker em um host EC2:

Passo 1: Conecte-se à sua instância EC2
- Use um cliente SSH (Secure Shell) para se conectar à sua instância EC2 usando a chave de acesso que você configurou durante a criação da instância.

Passo 2: Atualize o sistema
- Antes de instalar o Docker, atualize os pacotes do sistema operacional executando o seguinte comando:
sudo apt update

Passo 3: Instale o Docker
- Execute os seguintes comandos para instalar o Docker no seu host EC2:
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y

Passo 4: Verifique se o Docker está instalado corretamente
- Execute o seguinte comando para verificar se o Docker foi instalado corretamente:

sudo docker version

Passo 5: Adicione seu usuário ao grupo "docker" (opcional)
- Para evitar ter que digitar o comando `sudo` sempre que executar comandos do Docker, você pode adicionar seu usuário atual ao grupo "docker". Execute o seguinte comando:
```
sudo usermod -aG docker $USER
```
- Depois disso, faça logout da sessão SSH e faça login novamente para que as alterações tenham efeito.

Passo 6: Inicie e habilite o serviço Docker
- Execute os seguintes comandos para iniciar o serviço Docker e configurá-lo para iniciar automaticamente na inicialização do sistema:
sudo systemctl start docker
sudo systemctl enable docker
Então  você tem o Docker instalado e configurado em sua instância EC2. É possível usar o Docker para criar e executar contêineres.


Deploy da aplicação WordPress com um contêiner de aplicação e um banco de dados MySQL no Amazon RDS
Passo 1: Configurar o Amazon RDS
- Acesse o Console de Gerenciamento da AWS e navegue até o serviço Amazon RDS.
- Clique em "Criar banco de dados" e selecione o tipo de banco de dados MySQL.
- Siga as etapas fornecidas pelo assistente de criação para configurar os detalhes do banco de dados, como nome de usuário, senha, nome do banco de dados, capacidade de armazenamento, entre outros.
- Lembre-se de selecionar a opção de segurança correta para permitir o acesso ao banco de dados a partir da instância EC2 onde você implantará o WordPress.
Passo 2: Criar uma instância EC2
- Acesse o Console de Gerenciamento da AWS e navegue até o serviço Amazon EC2.
- Clique em "Launch Instance" para iniciar o assistente de criação de instâncias EC2.
- Selecione uma imagem AMI (Amazon Machine Image) baseada no sistema operacional de sua escolha.
- Escolha um tipo de instância adequado às suas necessidades e siga as etapas para configurar a instância.
- Certifique-se de configurar as regras de segurança corretas para permitir o tráfego HTTP (porta 80) e HTTPS (porta 443) para a instância EC2.
Passo 3: Instalar o Docker na instância EC2
- Conecte-se à sua instância EC2 usando SSH.
- Siga as instruções fornecidas anteriormente na documentação sobre como instalar o Docker na instância EC2.

Passo 4: Executar o contêiner do WordPress
- Execute o seguinte comando para baixar e iniciar o contêiner do WordPress:
sudo docker run -e WORDPRESS_DB_HOST=<endpoint-do-banco-de-dados> -e WORDPRESS_DB_USER=<nome-de-usuário-do-banco-de-dados> -e WORDPRESS_DB_PASSWORD=<senha-do-banco-de-dados> -e WORDPRESS_DB_NAME=<nome-do-banco-de-dados> -p 80:80 --name wordpress-container wordpress
- Substitua `<endpoint-do-banco-de-dados>`, `<nome-de-usuário-do-banco-de-dados>`, `<senha-do-banco-de-dados>` e `<nome-do-banco-de-dados>` pelas informações corretas do seu banco de dados RDS.
Passo 5: Acessar o WordPress
- Após a execução bem-sucedida do comando acima, você pode acessar o WordPress em seu navegador, digitando o endereço IP público da instância EC2. Isso abrirá a página de configuração inicial do WordPress, onde você pode configurar o site.

Agora está o WordPress implantado em uma instância EC2 usando um contêiner de aplicação e um banco de dados MySQL no Amazon RDS. Certifique-se de que as configurações de segurança, como grupos de segurança e permissões de acesso, estejam corretamente configuradas para proteger a aplicação.

Configurar o uso do serviço Amazon EFS (Elastic File System) para arquivos estáticos em um contêiner de aplicação 
WordPress:

Passo 1: Criar um sistema de arquivos do EFS
- Acesse o Console de Gerenciamento da AWS e navegue até o serviço Amazon EFS.
- Clique em "Criar sistema de arquivos" e siga as etapas fornecidas pelo assistente de criação para configurar o sistema de arquivos.
- Certifique-se de selecionar as configurações adequadas, como a escolha da VPC correta e das zonas de disponibilidade.
Passo 2: Montar o sistema de arquivos do EFS na instância EC2
- Conecte-se à sua instância EC2 usando SSH.
- Execute os seguintes comandos para instalar o utilitário NFS e criar um ponto de montagem para o sistema de arquivos do EFS:
sudo apt-get update
sudo apt-get install nfs-common -y
sudo mkdir /mnt/efs
```
-Anote o ID do ponto de montagem do EFS fornecido no Console de Gerenciamento da AWS.
Passo 3: Configurar a montagem automática do EFS na inicialização
- Execute o seguinte comando para editar o arquivo `/etc/fstab`:
sudo nano /etc/fstab
- Adicione a seguinte linha no final do arquivo:
<id-do-ponto-de-montagem-do-efs>:/ /mnt/efs nfs4 defaults,_netdev 0 0
- Substitua `<id-do-ponto-de-montagem-do-efs>` pelo ID do ponto de montagem do EFS anotado anteriormente.
- Salve o arquivo pressionando "Ctrl + O" e saia do editor pressionando "Ctrl + X".
Passo 4: Configurar o contêiner do WordPress para usar o EFS
- Execute o contêiner do WordPress com o seguinte comando, substituindo `<id-do-ponto-de-montagem-do-efs>` pelo ID do ponto de montagem do EFS:
sudo docker run -e WORDPRESS_DB_HOST=<endpoint-do-banco-de-dados> -e WORDPRESS_DB_USER=<nome-de-usuário-do-banco-de-dados> -e WORDPRESS_DB_PASSWORD=<senha-do-banco-de-dados> -e WORDPRESS_DB_NAME=<nome-do-banco-de-dados> -v /mnt/efs:/var/www/html/wp-content/uploads -p 80:80 --name wordpress-container wordpress
- Certifique-se de substituir `<endpoint-do-banco-de-dados>`, `<nome-de-usuário-do-banco-de-dados>`, `<senha-do-banco-de-dados>` e `<nome-do-banco-de-dados>` pelas informações corretas do seu banco de dados RDS.
Passo 5: Testar a configuração
- Faça upload de um arquivo estático, como uma imagem, no WordPress e verifique se o arquivo é salvo no sistema de arquivos do EFS corretamente.
- Verifique se você pode acessar o arquivo estático no navegador para confirmar que está sendo servido corretamente pelo contêiner do WordPress.
Serviço Amazon EFS está então configurado para armazenar arquivos estáticos em um contêiner de aplicação WordPress. 

Configuração do Load Balancer AWS para a aplicação Wordpress.
Passo 1: Crie uma instância EC2 para o WordPress
- Acesse o Console de Gerenciamento da AWS e navegue até o serviço Amazon EC2.
- Clique em "Launch Instance" para iniciar o assistente de criação de instâncias EC2.
- Selecione uma imagem AMI (Amazon Machine Image) baseada no sistema operacional de sua escolha.
- Escolha um tipo de instância adequado às suas necessidades e siga as etapas para configurar a instância.
- Certifique-se de configurar as regras de segurança corretas para permitir o tráfego HTTP (porta 80) e HTTPS (porta 443) para a instância EC2.

Passo 2: Crie um Grupo de Destino (Target Group)
- Acesse o Console de Gerenciamento da AWS e navegue até o serviço "Elastic Load Balancing".
- Clique em "Target Groups" no painel de navegação esquerdo e, em seguida, clique em "Create target group".
- Forneça um nome para o grupo de destino e selecione o protocolo e a porta corretos (geralmente HTTP na porta 80 para o WordPress).
- Escolha a VPC correta e configure o Health Check para verificar a saúde das instâncias do WordPress.
- Selecione as instâncias EC2 adequadas para o grupo de destino e clique em "Create target group".

Passo 3: Crie um Balanceador de Carga (Load Balancer)
- Ainda no serviço "Elastic Load Balancing", clique em "Load Balancers" no painel de navegação esquerdo e, em seguida, clique em "Create Load Balancer".
- Selecione o tipo de balanceador de carga adequado às suas necessidades (geralmente "Application Load Balancer").
- Configure o balanceador de carga com as opções corretas, como nome, esquema, ouvintes e segurança.
- No passo "Configure Routing", selecione "Existing target group" e escolha o grupo de destino que você criou anteriormente.
- Continue seguindo as etapas restantes do assistente para concluir a criação do balanceador de carga.

Passo 4: Configurar os registros DNS
- Após a criação do balanceador de carga, você receberá um DNS para o balanceador de carga.
- Acesse o serviço de gerenciamento de DNS onde você registrou o domínio do seu WordPress.
- Crie registros DNS (como um registro CNAME) apontando para o DNS do balanceador de carga.

Passo 5: Testar o balanceamento de carga
- Após a configuração dos registros DNS, aguarde um tempo para que as alterações de DNS se propaguem.
- Acesse o domínio do seu WordPress e verifique se a aplicação está sendo carregada corretamente.
- Faça várias solicitações ao site e verifique se elas são distribuídas entre as instâncias EC2 configuradas no grupo de destino.
Load Balancer esntão está configurado para a aplicação WordPress, permitindo distribuir o tráfego entre as instâncias EC2.
