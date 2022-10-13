Для публикации, нужно сделать npm i, потом сделать билд и обязательно увеличить версию приложени в пакедж джсон. 

Потом войти npm login, потом опубликовать через npm publish

пример использования
<InputPhoneMask
:value="phone"
@input="e => (phone = e.value, isValid = e.isValid)"
/>
