# Полезные сниппеты
Полезные сниппеты для PHP, CSS, Wordpress, Javascript...  
  
## Javascript
  
Проверяем существование функция Javascript перед ее вызовом. Всегда неприятно, когда вы получаете сообщение об ошибке при попытке вызвать функцию, которая не была определена, но есть простой способ предотвратить это. Чтобы проверить, существует ли функция Javascript перед ее вызовом, попробуйте этот сниппет выше.  

```
if (typeof yourFunctionName == 'function')
{
 yourFunctionName(); 
}
```
  
## Wordpress  
Перевести слово “Reply” в WordPress  
```
function uzabila_comment_reply_text( $link ) {
    $link = str_replace( 'Reply', 'Ответить', $link );
    return $link;
}
add_filter( 'comment_reply_link', 'uzabila_comment_reply_text' );
```
