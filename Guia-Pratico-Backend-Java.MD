# Guia Prático de Desenvolvimento Back-end com Java: Spring Boot, Maven, MVC, MapStruct, PostgreSQL, Docker e Supabase.

### Site para escolher dependencias do spring:
https://start.spring.io

## Dependências principais
Para sistemas que não tem Login, porque na lista abaixo esta sem o Spring Security.

```
Dependência             Função
	        
Spring Data JPA 	     Faz a persistência de dados e o mapeamento de entidades para tabelas do banco.
Spring Web MVC	         Permite criar APIs REST com controllers e endpoints HTTP.
Spring Boot DevTools	 Facilita o desenvolvimento com reinicialização automática da aplicação.
PostgreSQL Driver	     Permite a conexão da aplicação Java com o PostgreSQL.
Lombok	                 Reduz código repetitivo com anotações como @Getter, @Setter e @AllArgsConstructor.
```

## Exemplo de ClasseMapper:
```
@Mapper(componentModel = "spring")
public interface UsuarioMapper {

    UsuarioDTO toDTO(Usuario usuario);

    Usuario toEntity(UsuarioDTO dto);
}
```

## A dependencia não é achada no site do Spring

Dependencia:
```
<dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct</artifactId>
    <version>1.6.3</version>
</dependency>
```

## Como utilizar:

No seu `maven-compiler-plugin`, você possui dois blocos `<annotationProcessorPaths>`

Substitua o conteúdo de cada um desses blocos pelo seguinte código:

```
<annotationProcessorPaths>
    <!-- Lombok -->
    <path>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </path>

    <!-- MapStruct -->
    <path>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct-processor</artifactId>
        <version>1.6.3</version>
    </path>

    <!-- Integração Lombok + MapStruct -->
    <path>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok-mapstruct-binding</artifactId>
        <version>0.2.0</version>
    </path>
</annotationProcessorPaths>
```
Isso permite que o compilador reconheça as anotações do Lombok e gere as implementações dos seus Mappers.

### Depois de salvar o pom.xml:
- Clique em Reload All Maven Projects (ícone de atualização).
- Aguarde o download das dependências.
- Rode o projeto novamente e desenvolva seus mappers, ex: no mesma classe mapper `paraEntidade` e `paraDTO`.

<br>

# Padrão de desenvolvimento: MVC

| CAMADA        |  RESPONSABILIDADE                                |              
|---------------|--------------------------------------------------|
| Controller    | `Receber requisições HTTP e devolver respostas.` |    
| Service       | `Implementar regras de negócio.`                 | 
| Repository    | `Consultar, salvar, atualizar e excluir dados.`  | 
| Model         | `Representar entidades e conceitos do sistema.`  |  
| DTO's         | `Os dados recebidos e enviados pela API.`        |
| Mapper        | `Converter entidades em DTOs e vice-versa.`      |

### Comparação de Camadas de C# e Java

| C#            |  Java        |              
|---------------|--------------|
| Q             | `Repository` |   
| S             | `Service`    |   
| W             | `Controller` |   
| TO            | `DTO`        |   
| CLASSE        | `Model/Objeto/Entity`| 


### Exemplo de arquitetura de pastas:
```
com.example.demo
├── controller
│   └── UsuarioController.java
├── service
│   └── UsuarioService.java
├── repository
│   └── UsuarioRepository.java
├── model
│   └── Usuario.java
├── dto
│   ├── UsuarioRequestDTO.java
│   └── UsuarioResponseDTO.java
├── mapper
│   └── UsuarioMapper.java
└── DemoApplication.java
```


# Alternativas para Bancos de Dados PostgresSQL

## Com DOCKER: Local independente 

**Passo 1**

Instale a interface do Docker https://docs.docker.com/desktop/setup/install/windows-install/

**Passo 2**

Usando Docker, instale o banco de dados e configure o banco de dados Postgre e o pgAdmin usandos os comandos abaixo

`docker network create NOME_DO_PROJETO-network` - Aqui tu ta criando uma area de trabalho para todos os containers.

`docker run --name NOME_DO_PROJETODB -p 5432:5432 -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres -e POSTGRES_DB=NOME_DO_PROJETO -d --network NOME_DO_PROJETO-network postgres:17.6` - Criando o container que roda o postgres

`docker run --name pgadmin4 -p 15432:80 -e PGADMIN_DEFAULT_EMAIL=admin@admin.com -e PGADMIN_DEFAULT_PASSWORD=admin --network fruki-network -d dpage/pgadmin4` - Criando o container que roda PgAdmin, que é onde tu escreve os sql e ve o banco...

``NOME_DO_PROJETO`` = O que deve ser alteirado no comando com o nome do seu projeto, preferencialmente com letra minuscula

Ex: `docker network create desafiogc-network` e `docker run --name desafiogcdb`

Inicie o container do banco de dados e do pgAdmin(pela interface)

**Passo 3**

Configure o pgAdmin para acessar o bando de dados.

Acesse no navegador o endereço [http://localhost:15432/login](http://localhost:15432/login). Use o email _admin@admin.com_ e a senha _admin._

Clique em _Add New Server_ e insira as informações abaixo:

*   Em General/Name: NOME_DO_PROJETO
*   Em Connection/Host Name: NOME_DO_PROJETODB
*   Em Connection/Port: 5432
*   Em Connection/Maintenance database: NOME_DO_PROJETO
*   Em Connection/Username: postgres
*   Em Connection/Password: postgres

### Caso dispare o Erro a seguir no Log do Intelij :

```
SQL State  : 3000

Error Code : 0

Message    : FATAL : banco de dados *NOME_DO_PROJETO* não existe
```
É porque tu tens um Postgres Local rodando no Windows, para para-lo rode o seguinte comando no powershell:

`Stop-Service -Name postgresql-x64-17`

## Com SUPABASE: Em nuvem todos dev's no mesmo banco
SupaBase é um banco em nuvem que pode ser compartilhado, ou seja, todos os desenvolvedores usam o mesmo banco.

**Passo 1**

Entrar no site e ciar uma conta ou logar com alguma plataforma: Google,Git e etc
https://supabase.com/

**Passo 2**

Clique em começar seu projeto, coloque as informações:
- Nome do Projeto
- Senha do banco
- Região
- Opções de segurança

**Passo 3**

Agora com o banco criado,
clique em SQLeditor e insira seu código SQL para criar as tabelas e relacionamentos.

**Passo 4**

Para conectar o banco a IDE va em `direct` com o simbolo de banco de dados escolha o modo de conexão e copie as informações que aparecerem.

**Passo 5**

Va no arquivo `application.properties` e insira as informações tirados do passo anterior de conexão.

```
spring.datasource.url=${DB_URL}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}
```
**Passo 6**

crie um arquivo .env e adicione ao gitignore para criar a variavel de ambiente com:
```
DB_UR=Url do supaBase
DB_USERNAME=Usuario do supaBase
DB_PASSWORD=Senha do supaBase
```

Documentação auxiliar:
https://medium.com/@luizfelper/criando-uma-api-usando-o-supabase-com-java-spring-boot-38512ebdb2b


## Qual status HTTPS usar em cada operação da sua API?

### Endpoints - > Vaga

| Método | Caminho        | Descrição                  | Sucesso        | Erros             
|--------|----------------|----------------------------|----------------|-------------------
| GET    | `/vagas`       | Lista todas as vagas       | 200 - OK       | —                 
| GET    | `/vagas/{id}`  | Busca uma vaga pelo id     | 200 - OK       | 404 - NOT_FOUND   
| POST   | `/vagas`      | Cadastra uma nova vaga     | 201  - CREATED | 400 - BAD_REQUEST 
| PUT    | `/vagas/{id}` | Atualiza uma vaga existente | 200 - OK       | 404 - NOT_FOUND   
| DELETE | `/vagas/{id}` | Remove uma vagaa           | 204 - NO_CONTENT| 404 - NOT_FOUND            





