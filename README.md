Обёртка для простого взаимодействия с Api AmoCRM
===

#Подключение

В самом начале файла
```php
<?php
use AmoCRM\Amo;
```
Далее в любом месте перед использованием обёртки:
```php
<?php require_once 'wrap_amocrm/autoload.php'; ?>
```
#Использование
Главным объектом будет являться экземпляр класса Амо. В его конструкторе необходимо передать:

1. Субдомен црм
2. Логин юзера(почта)
3. Хэш этого юзера

```php
<?php
$amo = new Amo('test', 'test@test.ru', '011c2d7f862c688286b43ef552fb17f4');
?>
```
Далее все запросы к црм будут идти через этот бъект. По мимо удобных запросов к црм в ответе приходит не просто массив данных а экземпляр класса (например контакт) у которого все поля разобраны по свойствам этого объекта, опять же для удобного взаимодействия и исключения ошибок.

#Возможности
* Статичный метод для очистки телефона от лишних символов. Применяется при поиске по контактам.
* Поиск контакта по телефону или почте. Возвращается массив из "Контактов" которые имеют или данный телефон или почту). Даный метод можно использовать для поиска дублей.
* Изменение или создание нового контакта
* Изменение или создание новых сделок
* Удобная работа с доп полями, без знания их id и т.п.
* На все запросы возвращается не огромный масив с данными а удобные для работы объекты (Контакт или Сделка)

##В ближайших плана
* Присвоение статуса сделки не по id а текстом
* Получение статуса и воронки сделки в текстовом виде
* Добавление в кастомные поля обработку из множества значений

Описание методов
---
##Класс Amo
* **Amo::clearPhone($phone)** //(string) Статичный метод отчистки телефона от лишних символов. Возвращяет (int)
* **isAuthorization()** //Возвращает true в случаее успешной авторизации и false в случае провала
— save($object) //Contact|.. Сохраняет переданный объект в црм. Возвращает true в случае успеха и false в случае ошибки.
* **searchContact($phone, $email)** //(string) Производит поиск по контактам. При этом не учитывает формат телефона (телефоны начинающиеся с 7 или 8 считает разными). Возвращяет нумерованый массив из объектов класса Contact
* **loadLeadInId($id)** //(int) Возвращает объект класса Lead если находит сделку с $id в црм, в противном случае возвращает false
* **loadContactInId($id)** //(int) Возвращает объект класса Contact если находит контакт с $id в црм, в противном случае возвращает false
* **save($object)** //(Base) Сохраняет полученый объект в црм

##Абстрактный класс Base

* **getId()** //(int) Возвращает id сущьности
* **getName()** //(string) Возвращает наименование сущьности
* **setName($name)** //(string) Изменяет имя на $name
* **setResponsibleUserId($responsibleUser)** //(int|string) Устанавливает ответственного за контакт. Принимает либо id ответственного либо его имя (ищет эту строку во всех именах всех менеджеров црм). ВОзвращает true если такой менеджер есть или false если такого нет
* **getResponsibleUserId()** //(int) Возвращает id ответственного за сущьность
* **getTags()** //(string)[] Возвращает ассоциативный массив тэгов id => name
* **addTag($tag)** //(string) Добавляет тэг к имеющимся
* **delTag($tag)** //(string) Ищет и удаляет тэг из имеющихся. Возвращает true или false если $tag не найден
* **getLinkedCompanyId()** //(int) Возвращает id компании к которой привязан контакт
* **setLinkedCompanyId($linkedCompanyId)** //(int) Устанавливает id компании к которой будет привязан контакт
* **getCustomFields()** //CustomField[] Возврощает ассоциативный массив кастомных полей которые представленны экхемплярами класса хэлпера CustomField (id доп поля => объект)
* **addCustomField($customFieldNameOrId, $value)** //(string|int, string) Добавляет кастомное поле к имеющимся. Первый параметр имя кастомного поля или его id. Второй значение которое необходимо присвоить. Если всё верно и такое кастомное поле существует в црм возвращает true иначе false
* **delCustomField($customFieldNameOrId)** //(string|int) Ищет и очищает кастомное поле сделки. Принимает имя кастомного поля или его id. Возвращает true или false если такое не найдено

##Класс Contact

* **getPhones()** //(string)[] Нумерованый массив телефонов
* **addPhone($phone, $enum = 'OTHER')** //(string) Добавляет телефон $phone к текущим, не обязательны пораметр тип телефона, по умолчанию "Другой". Возвращяет true или false в случае если тип телефона не крректный
Возможные варианты:
WORK - Рабочий
WORKDD - Прямой
MOB - Мобильный
FAX - Факс
HOME - Домашний
OTHER - Другой
* **delPhone($phone)** //(string) Ищет и удаляет телфон из имеющихся. Не учитывает формат телефона. Учитывает начинается телефон с 7 или 8. Возвращает true или false если $phone не найден
* **getEmails()** //(string)[] Нумерованый массив email'ов
* **addEmails($email, $enum = 'OTHER')** //(string) Добавляет почту $email к текущим, не обязательны пораметр тип почты, по умолчанию "Другой". Возвращяет true или false в случае если тип телефона не крректный
Возможные варианты:
WORK - Рабочая
PRIV - Личная
OTHER - Другая
* **delEmail($email)** //(string) Ищет и удаляет почту из имеющихся. Возвращает true или false если $email не найден
* **getLinkedLeadsId()** //(int)[] Нумерованый массив id сделок привязанных к контакту
* **addLinkedLeadId($linkedLeadId)** //(int) Добавляет id сделки к имеющимся привязанным к контакту
* **delLinkedLeadId($linkedLeadId)** //(int) Ищет и удаляет id сделки из имеющихся. Возвращает true или false если $linkedLeadId не найден

##Класс Lead

* **getPrice()** //(int) Возвращает бюджет
* **setPrice($price)** //(int) Задаёт бюджет
* **getStatusId()** //(int) Возвращает id статуса
* **setStatusId($statusId)** //(int) Задаёт id статуса
* **getPipelineId()** //(int) Возвращает id воронки
* **getMainContactId()** //(int) Возвращает id основного контакта
* **setMainContactId($mainContactId)** //(int) Задаёт id основного контакта


##Классы хэлперы Info, CustomField и Value
Созданы как вспомогательные классы. В дальнейшем скорей всего будет исключенно прямое взаимодействие с ними, по этому описывать их не буду)

#Примеры
Для начала взаимодействия с црм неплохо было бы проверить авторизацию

```php
<?php
if ($amo->isAuthorization()) {
    echo 'Всё норм';
} else {
    die('косяк');
}
?>
```
##Поиск, изменение и сохранение контакта в црм
```php
<?php
$contacts = $amo->searchContact('79998887766', 'test@test.ru'); //Ищем контакт по телефону и почте
$contact = current($contacts); //берём первый найденый контакт
$contact->setName($contact->getName() . ' лучший'); //Меняем имя дописывая в текущее строчку
$contact->addPhone('78889998887766', 'MOB'); //Добавляем мобильный телефон
$contact->addEmail('test2@test.ru', 'WORK'); //Добавляем рабочую почту
$contact->delEmail('test@test.ru'); //Удаляем почту
$contact->setResponsibleUserId('Пётр Иванович'); //Меняем ответственного
$amo->save($contact); //Сохраняем все изменение на сервере црм
?>
```