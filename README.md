INSTITUIÇÃO: Anhanguera
DISCIPLINA: Computação em Nuvem
PROFESSORA: Simone Tatiane do Canto
ALUNA: Carine Mesquita Freitas

1. FUNDAMENTOS DA COMPUTAÇÃO EM NUVEM

QUESTÃO 1 – QUAL É A DIFERENÇA ENTRE IaaS E SaaS? CITE UM EXEMPLO DE UTILIZAÇÃO DE CADA MODELO.

RESPOSTA:

IaaS é um modelo que oferece recursos de infraestrutura pela internet, como servidores, armazenamento e redes. Um exemplo é o Amazon EC2, utilizado para criar servidores virtuais.

SaaS é um programa disponibilizado pela internet, sem a necessidade de instalação no computador do usuário. Um exemplo é o Google Drive, utilizado para armazenar e compartilhar arquivos.

QUESTÃO 2 – UMA EMPRESA UTILIZA SERVIDORES PRÓPRIOS E TAMBÉM RECURSOS DE UM PROVEDOR DE NUVEM PÚBLICA. QUAL MODELO DE IMPLEMENTAÇÃO EM NUVEM ESSA EMPRESA UTILIZA?

RESPOSTA:

A empresa utiliza o modelo de nuvem híbrida, pois combina sua infraestrutura própria (nuvem privada) com os recursos oferecidos por um provedor de nuvem pública.

QUESTÃO 3 – QUAL É A PRINCIPAL DIFERENÇA ENTRE UMA MÁQUINA VIRTUAL E UM CONTÊINER?

RESPOSTA:

A máquina virtual possui um sistema operacional próprio, funcionando de forma independente. Já o contêiner compartilha o sistema operacional da máquina hospedeira, sendo mais leve, rápido e consumindo menos recursos.

2. EXECUÇÃO DE APLICATIVO COM DOCKER

OBJETIVO: Executar um servidor NGINX utilizando o Docker, verificar seu funcionamento e personalizar a página web.

EVIDÊNCIA 1 – CONTÊINER EM EXECUÇÃO

COMANDO UTILIZADO:

bash
docker ps


EVIDÊNCIA 2 – PÁGINA PERSONALIZADA COM MEU NOME

Para executar o NGINX e personalizar a página, foram utilizados os seguintes comandos:

bash
docker run -d --name nginx-carine -p 8080:80 nginx

bash
echo '<h1>Pagina personalizada - Carine Mesquita Freitas</h1>' > index.html

bash
docker cp index.html nginx-carine:/usr/share/nginx/html/index.html


Para verificar o funcionamento:

bash
curl http://localhost:8080


INSERIR AQUI A CAPTURA DE TELA DO TERMINAL MOSTRANDO O CONTÊINER EM EXECUÇÃO E A PÁGINA PERSONALIZADA.

3. EXECUÇÃO DA APLICAÇÃO NO KUBERNETES

OBJETIVO: Criar um Deployment e executar um Pod utilizando o Kubernetes.

Para criar a página personalizada, foi utilizado:

bash
echo '<h1>Aplicacao Kubernetes - Carine Mesquita Freitas</h1>' > index.html


Em seguida, foi criado um configmap:

bash
kubectl create configmap pagina-carine --from-file=index.html


Foi criado o arquivo de configuração do Deployment:

yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-carine
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-carine
  template:
    metadata:
      labels:
        app: nginx-carine
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
        volumeMounts:
        - name: pagina
          mountPath: /usr/share/nginx/html
      volumes:
      - name: pagina
        configMap:
          name: pagina-carine


Salvar o arquivo como deployment.yaml e executar:

bash
kubectl apply -f deployment.yaml


COMANDO UTILIZADO:

bash
kubectl get pods


Para verificar a página personalizada:

bash
kubectl exec deployment/nginx-carine -- curl -s http://localhost


EVIDÊNCIA 3 – INSERIR A CAPTURA DE TELA MOSTRANDO O POD EM EXECUÇÃO E A MENSAGEM PERSONALIZADA COM MEU NOME.

4. ESCALABILIDADE E AUTORRECUPERAÇÃO

Para aumentar a quantidade de Pods da aplicação para três, foi utilizado o comando:

bash
kubectl scale deployment nginx-carine --replicas=3


COMANDO UTILIZADO:

bash
kubectl get pods


EVIDÊNCIA 4 – INSERIR A CAPTURA DE TELA MOSTRANDO OS TRÊS PODS DA APLICAÇÃO EM EXECUÇÃO.

EVIDÊNCIA 5 – AUTORRECUPERAÇÃO

COMANDOS UTILIZADOS:

Primeiro, foi consultada a lista de Pods:

bash
kubectl get pods


Depois, foi excluído um dos Pods:

bash
kubectl delete pod NOME_DO_POD


Em seguida, foi realizada uma nova consulta:

bash
kubectl get pods


O Kubernetes identifica a exclusão e cria automaticamente outro Pod para manter a quantidade de réplicas definida no Deployment.

INSERIR AQUI A CAPTURA DE TELA MOSTRANDO A EXCLUSÃO DO POD E A CRIAÇÃO DE UM NOVO.

5. DISPONIBILIZAÇÃO DA APLICAÇÃO

OBJETIVO: Criar um Service e verificar a comunicação HTTP dentro do cluster Kubernetes.

EVIDÊNCIA 6 – SERVICE E ACESSO HTTP

Para disponibilizar a aplicação dentro do cluster, foi criado um Service:

bash
kubectl expose deployment nginx-carine --name=nginx-carine --port=80 --target-port=80 --type=ClusterIP


COMANDOS UTILIZADOS:

bash
kubectl get services


Para testar a comunicação HTTP dentro do cluster:

bash
kubectl run teste --image=curlimages/curl --rm -it --restart=Never -- curl -s http://nginx-carine


O comando realiza uma requisição HTTP ao serviço NGINX e apresenta o conteúdo da página personalizada.

INSERIR AQUI A CAPTURA DE TELA MOSTRANDO O SERVICE E O RETORNO DA PÁGINA NGINX.

6. INTERPRETAÇÃO DOS RESULTADOS

QUESTÃO 1 – QUAL A DIFERENÇA ENTRE UMA IMAGEM DOCKER E UM CONTÊINER EM EXECUÇÃO?

RESPOSTA:

A imagem Docker é um modelo que contém tudo o que é necessário para executar uma aplicação. Já o contêiner é a instância dessa imagem em funcionamento, executando a aplicação de forma isolada.

QUESTÃO 2 – QUAL A FUNÇÃO DE UM DEPLOYMENT NO KUBERNETES?

RESPOSTA:

O Deployment é responsável por gerenciar os Pods da aplicação, controlar a quantidade de réplicas e garantir que elas permaneçam em execução. Também permite atualizar e administrar as aplicações.

QUESTÃO 3 – O QUE ACONTECEU QUANDO VOCÊ EXCLUIU UM POD? POR QUE ISSO ACONTECEU?

RESPOSTA:

Quando excluí um Pod, o Kubernetes identificou que a quantidade de réplicas estava abaixo do esperado e criou automaticamente outro Pod para substituí-lo. Isso aconteceu porque o Deployment mantém o número de réplicas configurado.

QUESTÃO 4 – QUAL É A FINALIDADE DE UM SERVICE NO KUBERNETES?

RESPOSTA:

O Service permite a comunicação com os Pods por meio de um endereço e uma porta estáveis. Ele também encaminha as requisições para os Pods disponíveis, facilitando o acesso à aplicação dentro do cluster.

7. CONCLUSÃO

Durante a atividade prática, aprendi a utilizar o Docker para executar e personalizar uma aplicação com NGINX. Também aprendi a criar e gerenciar Pods e Deployments no Kubernetes. Consegui compreender como funciona a escalabilidade e a recuperação automática dos Pods. Por fim, aprendi a criar um Service e testar a comunicação HTTP dentro do cluster.





