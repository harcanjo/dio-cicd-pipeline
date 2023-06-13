# Pipeline de Criação e Execução de uma Imagem Docker

### Passos

1. Criar o Repositorio no GitLab
    Visibilidade: Público

2. Criar uma pasta local para o projeto
    Configurar a pasta, iniciar o repositorio e conectar ao GitLab

3. Nas Settings CI/CD:
    Verificar se já tem algum Runner

4. Criar ou Confirgurar um Runner para o projeto
    ```shell
    $ sudo gitlab-runner register
    ```

    - Enter the GitLab instance URL
    - Enter the registration token
    - Enter a description for the runner
    - Enter tags for the runner
    - Enter the executor: shell

5. Dentro do repositorio local

    - Crie uma pasta 'app' e coloque o template baixado
    - Dentro da app crie o dockerfile:
        ```docker
        FROM httpd:latest

        WORKDIR /usr/local/apache2/htdocs/
        COPY . /usr/local/apache2/htdocs/

        EXPOSE 80
        ```
    - Fora da app crie o .gitlab-ci.yml
        ```git
        stages:
          - build
          - deploy

        criar_imagens:
          stage: build
          tags:
            - gcp
          script:
            - docker build -t nomedogcp/gcp-nomedoprojeto:1.0 app/.
            - docker push nomedogcp/gcp-nomedoprojeto:1.0

        executar_imagens:
          stage: deploy
          needs:
            - criar_imagens
          tags:
            - gcp
          script:
            - docker run -dti --name webserver -p 80:80 nomedogcp/gcp:1.0
        ```
6. GitLab
    ```
    git add .
    git commit -m "App 1.0"
    git push
    ```
7. Servidor
    ```
    docker ps
    ```
    Verificar no ip do docker se o site está o ar      

