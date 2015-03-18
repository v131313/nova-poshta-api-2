# Nova Poshta API 2.0
Класс предоставляет доступ к функциям API 2.0 службы доставки Новая Почта

# Подготовка
## Получение ключа API
Для использования API необходимо: 
* зарегистрироваться на сайте [Новой Почты](http://novaposhta.ua)
* На [странице настроек](https://my.novaposhta.ua/settings/index#apikeys) в личном кабинете сгенерировать ключ для работы с API 

После получения ключа API предоставляется возможность использовать все методы класса [официальной из документации](https://my.novaposhta.ua/data/API2-200215-1622-28.pdf)

## Установка последней версии класса для работы с API
### Git
Необходимо выполнить в командной строке
```git
git clone https://github.com/lis-dev/nova-poshta-api-2
```
### Composer
Необходимо создать файл ``composer.json`` со следующим содержанием  
```json
{
    "require": {
        "lis-dev/nova-poshta-api-2": "dev-master"
    }
}
```
и запустить из командной строки команду ``php composer.phar install`` или ``php composer.phar update``
### Альтернативная установка
Необходимо скачать архив по ссылке
```
https://github.com/lis-dev/nova-poshta-api-2/archive/master.zip
```
# Форматы данных
Для входящих данных используются PHP массивы, ответ сервера может быть получен в формате:
* как PHP массив
* JSON
* XML

# Использование 
## Создание экземпляра класса
```php
$np = new NovaPoshtaApi2('Ваш_ключ_API_2.0');
```
## Получение информации о трек-номере
```php
$result = $np->documentsTracking('59000000000000');
```
## Получение сроков доставки
```php
$sender_city = $np->getCity('Белгород-Днестровский', 'Одесская');
$sender_city_ref = $sender_city['data'][0]['Ref'];
$recipient_city = $np->getCity('Киев', 'Киевская');
$recipient_city_ref = $recipient_city['data'][0]['Ref'];
$date = date('d.m.Y');
$result = $np->getDocumentDeliveryDate($sender_city_ref, $recipient_city_ref, 'WarehouseWarehouse', $date);	
```
## Получение стоимости доставки
```php
$sender_city = $np->getCity('Белгород-Днестровский', 'Одесская');
$sender_city_ref = $sender_city['data'][0]['Ref'];
$recipient_city = $np->getCity('Киев', 'Киевская');
$recipient_city_ref = $recipient_city['data'][0]['Ref'];
$weight = 7;
$price = 5450;
$result = $np->getDocumentPrice($sender_city_ref, $recipient_city_ref, 'WarehouseWarehouse', $weight, $price);

```
## Генерирование новой электронной накладной
```php
$result = $np->newInternetDocument(
	// Данные отправителя
	array(
		// Данные пользователя
		'FirstName' => 'Петр',
		'MiddleName' => 'Петрович',
		'LastName' => 'Петров',
		// Вместо FirstName, MiddleName, LastName можно ввести зарегистрированные ФИО отправителя или название фирмы для юрлиц
		// (можно получить, вызвав метод getCounterparties('Sender', 1, '', ''))
		'Description' => 'Иванов Иван Иванович',
		// Необязательное поле, в случае отсутствия будет использоваться из данных контакта
		'Phone' => '0631112233',
		// Город отправления
		'City' => 'Белгород-Днестровский',
		// Область отправления
		'Region' => 'Одесская',
		// Отделение отправления
		'Warehouse' => 'Отделение №2 (до 30 кг): ул. Дзержинского, 54',
	),
	// Данные получателя
	array(
		'FirstName' => 'Сидор',
		'MiddleName' => 'Сидорович',
		'LastName' => 'Сиродов',
		'Phone' => '0509998877',
		'City' => 'Киев',
		'Region' => 'Киевская',
		'Warehouse' => 'Отделение №3: ул. Калачевская, 13 (Старая Дарница)',
	),
	array(
		// Дата отправления
		'DateTime' => '13.12.2014',
		// Тип доставки, дополнительно - getServiceTypes()
		'ServiceType' => 'WarehouseWarehouse',
		// Тип оплаты, дополнительно - getPaymentForms()
		'PaymentMethod' => 'Cash',
		// Кто оплачивает за доставку
		'PayerType' => 'Recipient',
		// Стоимость груза в грн
		'Cost' => '500',
		// Кол-во мест
		'SeatsAmount' => '1',
		// Описание груза
		'Description' => 'Кастрюля',
		// Тип доставки, дополнительно - getCargoTypes
		'CargoType' => 'Cargo',
		// Вес груза
		'Weight' => '10',
		// Объем груза в куб.м.
		'VolumeGeneral' => '0.5',
		// Обратная доставка
		'BackwardDeliveryData' => array(
			array(
				// Кто оплачивает обратнку доставку
				'PayerType' => 'Recipient',
				// Тип доставки
				'CargoType' => 'Money',
				// Значение обратной доставки
	 			'RedeliveryString' => 4552,
 			)
		)
	)
);
```
## Получение складов в определенном городе
```php
// В параметрах указывается город и область (для более точного поиска)
$city = $np->getCity('Киев', 'Киевская');
$result = $np->getWarehouses($city);
```
## Вызов произвольного метода
```php
$result = $np
	->model('Имя_модели')
	->method('Имя_метода')
	->params(array(
		'Имя_параметра_1' => 'Значение_параметра_1',
		'Имя_параметра_2' => 'Значение_параметра_2',
	))
	->execute();
```
# Реализованные методы
* __construct()
* setKey()
* getKey()
* setLanguage()
* getLanguage()
* setFormat()
* getFormat()
* model()
* method()
* params()
* execute()
* documentsTracking()
* getCities()
* getWarehouses()
* getWarehouse()
* getStreet()
* getArea()
* getCity()
* __call()
* delete()
* update()
* save()
* getCounterparties()
* cloneLoyaltyCounterpartySender()
* getCounterpartyContactPersons()
* getCounterpartyAddresses()
* getCounterpartyOptions()
* getCounterpartyByEDRPOU()
* getDocumentPrice()
* getDocumentDeliveryDate()
* newInternetDocument()
* printDocument()
* printMarkings()
* findArea()
* findCityByRegion()
* checkInternetDocumentCounterparty()
* checkInternetDocumentParams()
* printGetLink()