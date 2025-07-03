# Curso Completo de Rotas em PHP: Do Simples ao Avançado

Bem-vindo ao curso mais fácil e didático de rotas em PHP! Aqui você aprende do zero, com exemplos simples, desenhos feitos com texto (ASCII art), exercícios e explicações para quem nunca programou.

---

## O que você vai aprender?
- O que é uma rota?
- Como criar rotas simples em PHP
- Como criar rotas dinâmicas (com parâmetros)
- Como organizar seu código de rotas
- Como deixar as URLs bonitas
- Como proteger rotas (login, admin)
- Como usar frameworks para rotas
- Boas práticas e exercícios

---

## 1. O que é uma rota?

### 1.1 Conceito básico
- Rota é o caminho que você digita no navegador para acessar uma página do site.
- Exemplo: Em `https://meusite.com/sobre`, a rota é `/sobre`.

### 1.2 Analogia
- Imagine que o site é um prédio e cada rota é uma porta diferente. Você escolhe qual porta quer entrar digitando o caminho na barra de endereços.

### 1.3 Visualizando uma rota
```
+-------------------------------+
| https://meusite.com/sobre     |
+-------------------------------+
                |
                v
           [ /sobre ]
```

### 1.4 Termos importantes
- **Rota**: O caminho depois do nome do site (ex: `/contato`).
- **Handler**: O código que executa quando você entra naquela rota. Handler = "quem lida com aquilo".

### 1.5 Exercício
- Escreva 3 exemplos de rotas que você já viu em sites reais.
- Navegue em um site e anote o que muda na URL quando você troca de página.

---

## 2. Primeira rota em PHP (sem frameworks)

### 2.1 O que vamos fazer?
- Vamos criar um arquivo chamado `index.php`.
- Ele vai mostrar uma mensagem diferente para cada rota.

### 2.2 Código passo a passo
```php
<?php
// 1. Pega o caminho da URL acessada
$caminho = $_SERVER['REQUEST_URI'];

// 2. Verifica se o caminho é '/sobre'
if ($caminho === '/sobre') {
    echo 'Página Sobre'; // Mostra "Página Sobre"
}
// 3. Verifica se o caminho é '/contato'
else if ($caminho === '/contato') {
    echo 'Página Contato'; // Mostra "Página Contato"
}
// 4. Se não for nenhum dos dois, mostra a página inicial
else {
    echo 'Página Inicial'; // Mostra "Página Inicial"
}
```

### 2.3 Explicando cada linha
- `$_SERVER['REQUEST_URI']`: Pega o caminho que você digitou no navegador.
- O `if` compara esse caminho com as rotas que você quer tratar.
- O `echo` mostra o texto na tela.

### 2.4 Exemplo prático
- Se você acessar `http://localhost/sobre` ⟶ aparece: `Página Sobre`
- Se acessar `http://localhost/contato` ⟶ aparece: `Página Contato`
- Qualquer outro endereço ⟶ aparece: `Página Inicial`

### 2.5 Desenho do fluxo
```
[ Você digita /sobre ]
          |
          v
   [ Página Sobre ]

[ Você digita /contato ]
          |
          v
   [ Página Contato ]

[ Qualquer outro caminho ]
          |
          v
   [ Página Inicial ]
```

### 2.6 Exercício
- Crie um arquivo `index.php` e teste as URLs `/`, `/sobre` e `/contato` no seu navegador local.
- Modifique o código para adicionar uma nova rota `/produtos` que exibe "Página Produtos".

---

## 3. Rotas dinâmicas e parâmetros

### 3.1 O que é uma rota dinâmica?
- É uma rota que muda dependendo do que você digita, por exemplo: `/usuario/123`.
- O número pode ser qualquer um, e o site mostra o usuário correspondente.

### 3.2 Código passo a passo
```php
<?php
$caminho = $_SERVER['REQUEST_URI'];
// Verifica se a URL tem o formato /usuario/algum-numero
if (preg_match('#^/usuario/(\d+)$#', $caminho, $resultado)) {
    echo 'Usuário ID: ' . $resultado[1]; // Mostra o ID capturado
} else {
    echo 'Página não encontrada';
}
```

### 3.3 Explicando cada parte
- `preg_match`: Função do PHP que procura padrões em textos.
- `#^/usuario/(\d+)$#`: Expressão regular que significa "começa com /usuario/ e termina com um número".
- Se encontrar, o número vai para `$resultado[1]`.

### 3.4 Exemplo prático
- Acessando `/usuario/42` ⟶ aparece: `Usuário ID: 42`
- Acessando `/usuario/abc` ⟶ aparece: `Página não encontrada`

### 3.5 Desenho explicativo
```
[ Você digita /usuario/42 ]
          |
          v
   [ Usuário ID: 42 ]

[ Você digita /usuario/abc ]
          |
          v
   [ Página não encontrada ]
```

### 3.6 Exercício
- Modifique o código para aceitar `/produto/ID` e mostrar "Produto ID: X".
- Tente acessar `/produto/10` e `/produto/banana` e veja o resultado.

---

## 4. Organizando rotas com funções

### 4.1 Por que usar funções?
- Para não repetir código e deixar tudo mais organizado.

### 4.2 O que é cada termo?
- `route`: Nome da função que registra uma rota.
- `path`: O caminho da rota (ex: `/sobre`).
- `callback`: O que vai acontecer quando alguém acessar aquela rota. É uma função anônima (sem nome).

### 4.3 Código passo a passo
```php
<?php
// Função que registra uma rota
function route($path, $callback) {
    // Se o caminho da URL for igual ao da rota
    if ($_SERVER['REQUEST_URI'] === $path) {
        $callback(); // Executa o código da rota
        exit; // Para o script para não executar outras rotas
    }
}

// Registrando a rota '/'
route('/', function() {
    echo 'Home';
});
// Registrando a rota '/sobre'
route('/sobre', function() {
    echo 'Sobre';
});
```

### 4.4 Explicando cada parte
- `function route($path, $callback) {...}`: Cria uma função chamada `route`.
- `$path`: Caminho da rota (ex: `/sobre`).
- `$callback`: Função que será executada se a rota for acessada.
- `route('/sobre', function() {...})`: Diz: "Quando alguém acessar /sobre, execute este código".
- `exit;`: Para o PHP de continuar rodando, evitando que outras rotas sejam executadas.

### 4.5 Analogia
- Imagine que `$path` é o endereço da sua casa e `$callback` é o que você faz quando alguém toca a campainha nesse endereço.

### 4.6 Exercício
- Adicione uma rota `/contato` usando a função route.
- Crie uma rota `/servicos` que exibe "Página de Serviços".

---

## 5. Rotas com .htaccess (URL amigável)

Para esconder o `index.php` da URL e deixar tudo mais bonito, crie um arquivo `.htaccess` (em servidores Apache):

```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php [QSA,L]
```

**O que isso faz?**
- Todas as requisições vão para o `index.php`, que decide qual página mostrar.
- Assim, você pode acessar `/sobre` ao invés de `/index.php?sobre`.

**Desenho do funcionamento:**

```
[ Você digita /sobre ]
          |
          v
   [ index.php recebe a requisição ]
          |
          v
   [ index.php mostra a Página Sobre ]
```

### Exercício 5
- Crie um arquivo `.htaccess` e teste acessar suas rotas sem o `index.php` na URL.
- Tente acessar uma rota inexistente e veja o que acontece.

---

## 6. Criando um mini sistema de rotas

Vamos criar um roteador simples e prático:

```php
<?php
$routes = [
    '/' => function() { echo 'Home'; },
    '/sobre' => function() { echo 'Sobre'; },
    '/contato' => function() { echo 'Contato'; },
];

$url = parse_url($_SERVER['REQUEST_URI'], PHP_URL_PATH);
if (isset($routes[$url])) {
    $routes[$url](); // Executa a função da rota
} else {
    echo '404 - Página não encontrada';
}
```

**Exemplo real:**
- Acessando `/sobre` ⟶ mostra "Sobre"
- Acessando `/contato` ⟶ mostra "Contato"
- Acessando `/qualquercoisa` ⟶ mostra "404 - Página não encontrada"

**Desenho do mini sistema:**

```
[ Você digita /sobre ]
          |
          v
   [ Roteador verifica a rota ]
          |
          v
   [ Mostra a Página Sobre ]
```

### Exercício 6
- Adicione uma rota `/login` que exibe "Página de Login".
- Faça um teste acessando uma rota que não existe.

---

## 7. Rotas com frameworks (Slim, Laravel)

### Slim Framework

```php
require 'vendor/autoload.php';
$app = new \Slim\App();
$app->get('/sobre', function ($request, $response) {
    return $response->write('Sobre Slim');
});
$app->run();
```

- O Slim facilita a criação de rotas com poucas linhas.
- Basta instalar com Composer: `composer require slim/slim`

### Laravel (web.php)

```php
Route::get('/sobre', function () {
    return 'Sobre Laravel';
});
```

- No Laravel, as rotas ficam no arquivo `routes/web.php`.
- Super simples e poderoso!

**Desenho da instalação e uso:**

```
[ Instalação do Slim/Laravel ]
          |
          v
   [ Criação de rotas fáceis ]
          |
          v
   [ Execução do servidor ]
          |
          v
   [ Acesso às rotas criadas ]
```

### Exercício 7
- Instale o Slim ou Laravel e crie uma rota `/teste` que retorna "Funcionando!".
- Explore a documentação oficial dos frameworks.

---

## 8. Middlewares e autenticação

Middlewares são "camadas" que processam a requisição antes de chegar na rota. Muito útil para proteger áreas restritas!

Exemplo de middleware simples:

```php
function authMiddleware($callback) {
    if (!isset($_SESSION['user'])) {
        echo 'Acesso negado'; // Bloqueia se não estiver logado
        exit;
    }
    $callback(); // Executa a rota se estiver logado
}

route('/painel', function() {
    authMiddleware(function() {
        echo 'Bem-vindo ao painel!';
    });
});
```

**Exemplo real:**
- Se não estiver logado, ao acessar `/painel` aparece: `Acesso negado`
- Se estiver logado, aparece: `Bem-vindo ao painel!`

**Desenho do middleware:**

```
[ Requisição chega na rota ]
          |
          v
   [ Middleware verifica acesso ]
          |
          +--[ Se não logado: Acesso negado ]
          |
          +--[ Se logado: Executa a rota ]
```

### Exercício 8
- Implemente um middleware que só permite acesso à rota `/admin` se uma variável `isAdmin` for verdadeira.
- Teste o comportamento mudando o valor da variável.

---

## 9. Boas práticas e dicas avançadas
- Separe rotas em arquivos diferentes para projetos grandes
- Use controllers para lógica complexa
- Valide e sanitize parâmetros da URL (ex: nunca confie em dados vindos do usuário!)
- Use frameworks para projetos profissionais
- Implemente rotas RESTful para APIs
- Comente seu código! (Ajuda você e outros a entenderem depois)

---

## 10. Exercícios finais

1. Crie um sistema de rotas que tenha pelo menos 5 páginas diferentes.
2. Implemente rotas dinâmicas para produtos e usuários.
3. Adicione um middleware de autenticação para proteger uma rota.
4. Experimente usar Slim ou Laravel para refazer seu sistema de rotas.
5. Desenhe um fluxograma (pode ser à mão ou usando um site como draw.io) mostrando como as rotas funcionam no seu projeto.

---

## 11. Referências e materiais extras
- [Documentação do PHP](https://www.php.net/manual/pt_BR/)
- [Slim Framework](https://www.slimframework.com/)
- [Laravel](https://laravel.com/)
- [Regex101](https://regex101.com/)
- [draw.io (diagramas grátis)](https://app.diagrams.net/)
- [Como adicionar imagens no GitHub](https://docs.github.com/pt/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/adding-a-readme-to-your-repository#incluir-imagens)

---

Este curso foi feito para ser simples, direto e super explicativo. Se você seguir cada passo, vai dominar rotas em PHP!

> Dica: Para adicionar imagens reais no seu README, basta fazer upload delas no repositório e usar `![descrição](caminho/da/imagem.png)`.

---

> Dúvidas? Sugestões? Abra uma issue ou envie um pull request!

---

## Como criar e testar rotas em PHP usando o XAMPP

### O que é o XAMPP?
- XAMPP é um programa que simula um servidor web no seu computador. Ele instala o Apache (servidor), PHP e MySQL de forma fácil.
- Assim, você pode rodar seus arquivos PHP localmente, igualzinho a um site de verdade.

### Passo a passo para rodar rotas no XAMPP

#### 1. Instale o XAMPP
- Baixe em: https://www.apachefriends.org/pt_br/index.html
- Instale normalmente (só clicar em avançar).

#### 2. Inicie o Apache
- Abra o XAMPP Control Panel.
- Clique em "Start" no Apache (o quadradinho deve ficar verde).

#### 3. Coloque seus arquivos PHP na pasta certa
- Vá até a pasta onde o XAMPP foi instalado (geralmente `C:\xampp`).
- Entre na pasta `htdocs`.
- Crie uma nova pasta, por exemplo: `curso-rotas`.
- Coloque seu arquivo `index.php` dentro dessa pasta.

#### 4. Acesse pelo navegador
- Abra o navegador e digite:
  - `http://localhost/curso-rotas/`
  - `http://localhost/curso-rotas/sobre`
  - `http://localhost/curso-rotas/contato`
- O que você digitar depois de `curso-rotas/` será a rota!

#### 5. Exemplo de estrutura de pastas
```
C:\xampp
   └── htdocs
         └── curso-rotas
                └── index.php
```

#### 6. Dica para rotas funcionarem sem erro
- Se ao acessar uma rota aparecer erro 404, verifique se o Apache está rodando e se o nome da pasta está correto.
- Para rotas como `/sobre` funcionarem sem `index.php` na URL, use o arquivo `.htaccess` (veja a seção 5 deste curso).

#### 7. Exercício prático
- Instale o XAMPP, crie a pasta e coloque o `index.php` com as rotas do exemplo.
- Teste acessar diferentes rotas no navegador.

---
