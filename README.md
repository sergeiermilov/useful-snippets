# Полезные сниппеты
Полезные сниппеты для PHP, CSS, Wordpress, Javascript...  
  
## Javascript
  
### Проверяем существование функция Javascript перед ее вызовом  

Всегда неприятно, когда вы получаете сообщение об ошибке при попытке вызвать функцию, которая не была определена, но есть простой способ предотвратить это. Чтобы проверить, существует ли функция Javascript перед ее вызовом, попробуйте этот сниппет выше.  

```
if (typeof yourFunctionName == 'function')
{
 yourFunctionName(); 
}
```
  
## Wordpress  
### Перевести слово “Reply” в WordPress  

```
function uzabila_comment_reply_text( $link ) {
    $link = str_replace( 'Reply', 'Ответить', $link );
    return $link;
}
add_filter( 'comment_reply_link', 'uzabila_comment_reply_text' );
```
### Защита файлов плагина от прямого доступа  
Предотвращает пользователям прямой доступ к вашим .php-файлам по URL. Иначе, если ваш файл содержит некоторые операции ввода/вывода, он может быть вызван (злоумышленником), и это может привести к неожиданному поведению.  
```
<?php 
    if ( ! defined( 'ABSPATH' ) ) {
        exit; // выйти если обращаются напрямую
    }
?>
```
Таким образом, использование сниппета может предотвратить доступ к вашим файлам (напрямую) и гарантирует, что ваши файлы темы или плагина будут выполняться только в среде WordPress.  
Использование:  
- может быть размещен в верхней части любого из ваших PHP-файлов (темы и плагинов);  
- может быть размещен в верхней части вашего wp-config.php.  
ABSPATH - это [константа PHP](https://www.php.net/manual/ru/language.constants.php), определяемая WordPress в нижней части wp-config.php:  
```
/* That's all, stop editing! Happy blogging. */

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__) . '/');
```
Как видно из блока комментариев выше, WordPress не рекомендует изменять эти строки кода - вероятно, потому, что многие плагины и темы полагаются на ABSPATH для проверки того, выполняются ли их PHP-файлы в среде WordPress.  
Если вы используете этот фрагмент в верхней части вашего файла wp-config.php, вы остановите выполнение wp-config.php, потому что ABSPATH на тот момент еще не был определен. А другие файлы, зависящие от wp-config.php, не будут работать (т.е. вы сломаете свой сайт).  
Этот сниппет широко используется PHP-файлами плагинов и тем только условно. Теоретически это означает, что вы можете добавить свою собственную константу в нижней части wp-config.php, и вы получите тот же практический результат.  
Ваш wp-config.php:  
```
if ( !defined('MY_CONSTANT') )
    define('MY_CONSTANT', 'fool');
```
Ваш файл темы или плагина:  
```
<?php 
    if ( ! defined( 'MY_CONSTANT' ) ) {
        exit; // выйти если обращаются напрямую
    }
```
Более подробно об этом почитать можно на официальном сайта PHP в справке по [синтаксису констант](https://www.php.net/manual/ru/language.constants.syntax.php) и [волшебным константам](https://www.php.net/manual/ru/language.constants.predefined.php).  
\
### Отображаем заголовок категории с номером страницы пагинации  
```
<?php if ( is_category() && !is_paged() ) { ?>
    <h1><?php single_cat_title(); ?></h1>				
<?php } elseif ( is_category() && is_paged() ) { ?>
    <h1><?php single_cat_title(); ?> - Страница <?php echo get_query_var('paged') ;?></h1>
<?php }; ?>
```

