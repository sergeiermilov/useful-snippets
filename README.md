# Полезные сниппеты
Полезные сниппеты для PHP, CSS, Wordpress, Javascript...  

## Содержание
1. [PHP](#php)
2. [Javascript](#Javascript)
3. [Wordpress](#Wordpress)
4. [CSS](#CSS)
4. [MySQL](#MySQL)

## PHP

### Количество запросов и время загрузки страницы
```
<?php echo get_num_queries(); ?> запросов за <?php timer_stop(1); ?> секунд.
```
  
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
### Боремся с исправлением кавычек WordPress
Фильтр wptexturize отвечает за работу с кавычками и именно он отвечает за их замену внутри функции the_content, которая выводит содержимое статьи на сайт на WordPress. Решение выглядит так:  
```
remove_filter('the_content', 'wptexturize');
```
Т.е., по сути, мы убираем фильтр ***wptexturize*** из содержимого статьи. Вы можете вставить эту строчку в файл **functions.php** вашей темы. В целом, не имеет значения в какое место файла functions.php вы вставляете эту строчку, главное, что она будет размещена после **<?php**. Также вы можете создать отдельный плагин с этой строчкой, если вы хотите сделать что-то оригинальное, или просто добавить в свой какой-нибудь существующие плагин эту строчку.  
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
### Отображаем заголовок категории с номером страницы пагинации  
```
<?php if ( is_category() && !is_paged() ) { ?>
    <h1><?php single_cat_title(); ?></h1>				
<?php } elseif ( is_category() && is_paged() ) { ?>
    <h1><?php single_cat_title(); ?> - Страница <?php echo get_query_var('paged') ;?></h1>
<?php }; ?>
```
### Убираем категорию с главной страницы  
Данное условие позволяет показывать дату обновления статьи только в том случае, если статья действительно была обновлена.  
```
function exclude_category_home( $query ) {
if ( $query->is_home ) {
$query->set( 'cat', '-5' );
}
return $query;
}
 
add_filter( 'pre_get_posts', 'exclude_category_home' );
```
### Обновленная дата статьи в Wordpress
```
<?php if ( get_the_modified_time() != get_the_time()) { ?>
<?php echo the_modified_time('j F Y в H:i '); ?>
<?php }; ?>
```
Короткий вариант
```
<?php if ( get_the_modified_time() != get_the_time()) : echo the_modified_time('j F Y в H:i '); endif; ?>
```
### Ограничиваем длину заголовка Wordpress  
```
<?php echo wp_trim_words( get_the_title(), 18, ' ...' ); ?>
```
Можно это решение сделать через функцию и файл functions.php в теме WordPress:  
```
add_filter( 'the_title', 'uzabila_trim_words' );

function uzabila_trim_words( $title )
{
    return wp_trim_words( $title, 10, '' );
}
```
Если вы хотите обрезать слова в зависимости от определенных свойств записи, попросите WP передать вашей функции обратного вызова идентификатор записи. Ниже пример фильтрации по типу записи. Но вы также можете реализовать это для дополнительных условий – возраста записи, автора или даже мета-тегов.  
```
add_filter( 'the_title', 'uzabila_trim_words_by_post_type', 10, 2 );

function uzabila_trim_words_by_post_type( $title, $post_id )
{

    $post_type = get_post_type( $post_id );

    if ( 'product' !== $post_type )
        return $title;

    return wp_trim_words( $title, 10, '' );
}
```
### Убираем кнопку “В корзину” или “Купить” в категориях Woocommerce
Добавляем сниппет в файл functions.php вашей темы.  
```
remove_action( 'woocommerce_after_shop_loop_item', 'woocommerce_template_loop_add_to_cart', 10 );
```
### Исключаем категории из поиска в WordPress
Рассмотрим как можно исключить статьи из определенных категорий, которые не должны появляться в результатах поиска WordPress. Добавляем данный сниппет в файл functions.php и меняем XXX на ID нужной нам категории или категорий.  
```
function exclude_category_from_search($query) {
	if ($query->is_search) {
		$query->set('cat', '-24,-45,-52');
	}
	return $query;
}
add_filter('pre_get_posts','exclude_category_from_search');
```
### Исключаем страницы из поиска в WordPress
Если вы придерживаетесь какого-то разделения между постами в блоге и обычными страницами, то часто нет смысла в том, чтобы последние появлялись в результатах поиска. Мы можем просто полностью исключить все страницы из результатов поиска следующим сниппетом:  
```
function exclude_posts_from_search($query) {
	if ($query->is_search) {
		$query->set('post_type', 'post');
	}
	return $query;
}
add_filter('pre_get_posts','exclude_posts_from_search');
```
### Выводим количество статей в категории WordPress
$ID заменяем на ID нужной категории с кавычками, например: '38'  
```
<?php 
$cat_count = get_category($ID);
echo $cat_count->count; 
?>
```
### Ограничить количество слов в отрывках (excerpt) WordPress
```
function custom_excerpt_length( $length ) {
        return 20;
    }
add_filter( 'excerpt_length', 'custom_excerpt_length', 999 );
```
### Вывод стандартной пагинации WordPress
```
<?php the_posts_pagination(array(
    'prev_next' => false, 
    'screen_reader_text' => false,
    'type' => 'list'
)); ?>
```
### Отключаем в WordPress создание миниатюр файлов определенных размеров
```
add_filter( 'intermediate_image_sizes', 'delete_intermediate_image_sizes' );
function delete_intermediate_image_sizes( $sizes ){
	// размеры которые нужно удалить
	return array_diff( $sizes, [
		'medium_large',
		'large'
	] );
}
```
### Проверяем наличие миниатюры у записи
```
<?php if ( has_post_thumbnail() ) {
    the_post_thumbnail();
}; ?>

// пример

<?php if ( has_post_thumbnail() ) { ?>
	<p><?php echo the_post_thumbnail_url(get_the_ID(),'thumbnail'); ?></p>
<?php } else { ?>
	<p>No image</p>
<?php }; ?>
```
### Стандартный цикл (loop) в WordPress
```
<?php if ( have_posts() ) : while ( have_posts() ) : the_post(); ?>
// здесь вывод статей, тегов и т.п.
<?php endwhile; ?>
<?php else :
    _e( 'Sorry, no posts matched your criteria.', 'textdomain' );
endif; ?>
```
### Получение URI каталога темы
```
<?php echo get_template_directory_uri(); ?>

// Пример
<img src="<?php echo get_template_directory_uri(); ?>/images/logo.png" width="" height="" alt="" />
```
### Делаем видео Youtube адаптивным
Код ниже, оборачивает ***iframe*** в ***div*** и добавляет некоторые классы. Затем CSS изменяет размер объекта до правильной пропорции 16:9. Нам нужны два файла нашей темы – **functions.php** и **style.css**. Скорее всего в вашей теме используются именно эти файлы. Для начала добавляем следующий код в ваш файл functions.php:  
```
add_filter('the_content', function($content) {
	return str_replace(array("<iframe", "</iframe>"), array('<div class="iframe-container"><iframe', "</iframe></div>"), $content);
});

add_filter('embed_oembed_html', function ($html, $url, $attr, $post_id) {
	if(strpos($html, 'youtube.com') !== false || strpos($html, 'youtu.be') !== false){
  		return '<div class="embed-responsive embed-responsive-16by9">' . $html . '</div>';
	} else {
	 return $html;
	}
}, 10, 4);


add_filter('embed_oembed_html', function($code) {
  return str_replace('<iframe', '<iframe class="embed-responsive-item" ', $code);
});
```
Далее, мы должны добавить в файл со стилями (скорее всего в вашем случае style.css) следующие стилевые решения:
```
.embed-responsive {
  position: relative;
  display: block;
  height: 0;
  padding: 0;
  overflow: hidden;
}
.embed-responsive .embed-responsive-item,
.embed-responsive iframe {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: 0;
}
.embed-responsive-16by9 {
  padding-bottom: 56.25%;
}
```
### Количество запросов и время загрузки страницы в Wordpress
```
function wpse_footer_db_queries(){
    echo '<!-- '.get_num_queries().' запросов за '.timer_stop(0).' секунд. -->'.PHP_EOL;
}
add_action('wp_footer', 'wpse_footer_db_queries');
```
## CSS
### Стилизуем скроллбар на сайте через CSS
```
/* scrollbar */
::-webkit-scrollbar {
  width: 10px;
}

/* Track */
::-webkit-scrollbar-track {
  background: #ddd; 
}
 
/* Handle */
::-webkit-scrollbar-thumb {
  background: #aaa; 
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: #777; 
}
```
## MySQL
### Изменение названия самой таблицы MySQL
```
RENAME TABLE `table` TO `table_new_name`;
```
### Вставляем $variable или $\_POST в таблицу mysql  
Когда вы используете {}, вам не нужно оборачивать значение в ‘ ‘:  
```
mysql_query("INSERT INTO users (column 1, column2) VALUES ('{$_POST[value1]}', '{$_POST[value2]}')");
```
### Преобразование формата даты из mysql
Сниппет PHP, который преобразует дату в нормальный формат типа mm/dd/yy H:M (AM/PM), dd.mm.YY H:i или любой другой.  
```
// $datetimeFromMysql выглядит примерно так: 2014-01-31 13:05:59
$time = strtotime($datetimeFromMysql);
$myFormatForView = date("m/d/y g:i A", $time);

// $myFormatForView будет выглядеть так: 01/31/14 1:05 PM
$goodTime = date("d.m.Y H:i", $time);

// $goodTime будет выглядеть так: 05.09.2020 09:58
```
### Смена домена сайта через MySQL для WordPress
```
UPDATE wp_options SET option_value = replace(option_value, 'http://domain.ru', 'http://newdomain.ru') WHERE option_name = 'home' OR option_name = 'siteurl';
UPDATE wp_posts SET guid = replace(guid, 'http://domain.ru','http://newdomain.ru');
UPDATE wp_posts SET post_content = replace(post_content, 'http://domain.ru', 'http://newdomain.ru');
UPDATE wp_postmeta SET meta_value = replace(meta_value,'http://domain.ru','http://newdomain.ru');
```
