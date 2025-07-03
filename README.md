# Curso Completo de Rotas em PHP: Do Simples ao Avançado

Bem-vindo ao curso mais completo e didático de rotas em PHP! Aqui você vai aprender desde o conceito mais básico até técnicas avançadas, com exemplos práticos, setas, comentários, exercícios, imagens ilustrativas e explicações detalhadas. Ideal para quem está começando ou quer dominar rotas em aplicações PHP.

---

## Índice
1. [O que são rotas?](#o-que-são-rotas)
2. [Primeira rota em PHP (sem frameworks)](#primeira-rota-em-php-sem-frameworks)
3. [Rotas dinâmicas e parâmetros](#rotas-dinâmicas-e-parâmetros)
4. [Organizando rotas com funções](#organizando-rotas-com-funções)
5. [Rotas com .htaccess (URL amigável)](#rotas-com-htaccess-url-amigável)
6. [Criando um mini sistema de rotas](#criando-um-mini-sistema-de-rotas)
7. [Rotas com frameworks (Slim, Laravel)](#rotas-com-frameworks-slim-laravel)
8. [Middlewares e autenticação](#middlewares-e-autenticação)
9. [Boas práticas e dicas avançadas](#boas-práticas-e-dicas-avançadas)
10. [Exercícios finais](#exercícios-finais)
11. [Referências e materiais extras](#referências-e-materiais-extras)

---

## 1. O que são rotas?

> **Rota** é o caminho que você digita no navegador para acessar uma página do seu site.

Exemplo real:
- Quando você acessa `https://meusite.com/sobre` ⟶ a rota é `/sobre`
- Quando acessa `https://meusite.com/contato` ⟶ a rota é `/contato`

**Resumo visual:**
![Exemplo de rota](https://i.imgur.com/0y0y0y0.png)

```
URL do navegador: https://meusite.com/sobre
                        ↑
                 Rota: /sobre
```

- **Rota**: Caminho da URL (ex: `/contato`)
- **Handler**: Código que executa quando a rota é acessada

### Exercício 1
- Escreva em um papel ou bloco de notas 3 exemplos de rotas que você já viu em sites reais.
- Tente acessar diferentes páginas de um site e anote o que muda na URL.

---

## 2. Primeira rota em PHP (sem frameworks)

Vamos criar um arquivo chamado `index.php`:

```php
<?php
// Pega o caminho da URL acessada
if ($_SERVER['REQUEST_URI'] === '/sobre') {
    echo 'Página Sobre'; // ⟵ Mostra "Página Sobre" se acessar /sobre
} else if ($_SERVER['REQUEST_URI'] === '/contato') {
    echo 'Página Contato'; // ⟵ Mostra "Página Contato" se acessar /contato
} else {
    echo 'Página Inicial'; // ⟵ Qualquer outro caminho mostra "Página Inicial"
}
```

**Exemplo real:**
- Se você acessar `http://localhost/sobre` ⟶ aparece: `Página Sobre`
- Se acessar `http://localhost/contato` ⟶ aparece: `Página Contato`
- Qualquer outro endereço ⟶ aparece: `Página Inicial`

![Fluxograma simples de rotas](https://i.imgur.com/1Q9Z1Z1.png)

### Exercício 2
- Crie um arquivo `index.php` e teste as URLs `/`, `/sobre` e `/contato` no seu navegador local.
- Modifique o código para adicionar uma nova rota `/produtos` que exibe "Página Produtos".

---

## 3. Rotas dinâmicas e parâmetros

E se quisermos acessar `/usuario/123` e mostrar o usuário 123?

```php
<?php
$url = $_SERVER['REQUEST_URI'];
// Verifica se a URL tem o formato /usuario/algum-numero
if (preg_match('#^/usuario/(\d+)$#', $url, $matches)) {
    echo 'Usuário ID: ' . $matches[1]; // ⟵ Mostra o ID capturado
} else {
    echo 'Página não encontrada';
}
```

**Exemplo real:**
- Acessando `http://localhost/usuario/42` ⟶ aparece: `Usuário ID: 42`
- Acessando `http://localhost/usuario/abc` ⟶ aparece: `Página não encontrada`

**Seta visual:**
![Parâmetro dinâmico](https://i.imgur.com/2X2X2X2.png)

```
URL: /usuario/42
         ↑
     Parâmetro dinâmico (ID)
```

### Exercício 3
- Modifique o código para aceitar `/produto/ID` e mostrar "Produto ID: X".
- Tente acessar `/produto/10` e `/produto/banana` e veja o resultado.

---

## 4. Organizando rotas com funções

Vamos criar uma função para registrar rotas e deixar o código mais limpo:

```php
<?php
function route($path, $callback) {
    if ($_SERVER['REQUEST_URI'] === $path) {
        $callback(); // ⟵ Executa o código da rota
        exit;
    }
}

route('/', function() {
    echo 'Home';
});
route('/sobre', function() {
    echo 'Sobre';
});
```

**Como funciona:**
- Você chama `route('/sobre', function() {...})` para cada rota.
- Quando a URL bate com o caminho, executa o código dentro da função.

![Organização de rotas](https://i.imgur.com/3Y3Y3Y3.png)

### Exercício 4
- Adicione uma rota `/contato` usando a função route.
- Tente criar uma rota `/servicos` que exibe "Página de Serviços".

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

![URL amigável](https://i.imgur.com/4Z4Z4Z4.png)

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
    $routes[$url](); // ⟵ Executa a função da rota
} else {
    echo '404 - Página não encontrada';
}
```

**Exemplo real:**
- Acessando `/sobre` ⟶ mostra "Sobre"
- Acessando `/contato` ⟶ mostra "Contato"
- Acessando `/qualquercoisa` ⟶ mostra "404 - Página não encontrada"

![Mini sistema de rotas](https://i.imgur.com/5A5A5A5.png)

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

![Frameworks PHP](https://i.imgur.com/6B6B6B6.png)

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
        echo 'Acesso negado'; // ⟵ Bloqueia se não estiver logado
        exit;
    }
    $callback(); // ⟵ Executa a rota se estiver logado
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

![Middleware](https://i.imgur.com/7C7C7C7.png)

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
