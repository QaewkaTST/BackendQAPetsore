Precondition	"Налаштування Postman Collection
Додати змінну:

Variable: baseUrl
Value: https://petstore.swagger.io/v2"			"Додати pre-request Script до коллекції // Константи для рандомайзера
const animals = [""Dog"", ""Cat"", ""Hamster"", ""Guinea Pig"", ""Rabbit"", ""Parrot""];
const statusList = [""available"", ""pending"", ""sold""];
const tagname = [""Playful"", ""Friendly"", ""Cuddly"", ""Energetic"", ""Loyal""];

// Рандомайзер
const randomAnimal = animals[Math.floor(Math.random() * animals.length)];
const randomStatus = statusList[Math.floor(Math.random() * statusList.length)];
const randomId = Math.floor(Math.random() * 10000);
const randomCategoryId = Math.floor(Math.random() * 100);
const randomTagId = Math.floor(Math.random() * 100);
const randomTagname = tagname[Math.floor(Math.random() * tagname.length)];

// Зберігання в варіабли
pm.collectionVariables.set(""random_animal"", randomAnimal);
pm.collectionVariables.set(""random_status"", randomStatus);
pm.collectionVariables.set(""random_id"", randomId);
pm.collectionVariables.set(""random_category_id"", randomCategoryId);
pm.collectionVariables.set(""random_tag_id"", randomTagId);
pm.collectionVariables.set(""random_tag_name"", randomTagname);"			
ID	Назва	Тип	Мета	Кроки виконання	Очікуваний результат	Request Body	Tests Script
TC001	Створення pet з усіма обов'язковими полями	Позитивний	Перевірити успішне створення pet з усіма обов'язковими полями	Крок 1: Створити новий POST запит в Postman	Новий запит створено, тип встановлено як POST	"{
  ""id"": {{random_id}},
  ""category"": {
    ""id"": {{random_category_id}},
    ""name"": ""{{random_animal}}""
  },
  ""name"": ""{{random_animal}}"",
  ""photoUrls"": [
    ""https://example.com/photo1.jpg""
  ],
  ""tags"": [
    {
      ""id"": {{random_tag_id}},
      ""name"": ""{{random_tag_name}}""
    }
  ],
  ""status"": ""{{random_status}}""
}"	"pm.test(""Status code is 200"", function () {
    pm.response.to.have.status(200);
});

pm.test(""Response time < 3000ms"", function () {
    pm.expect(pm.response.responseTime).to.be.below(3000);
});

pm.test(""Content-Type is JSON"", function () {
    pm.expect(pm.response.headers.get(""Content-Type"")).to.include(""application/json"");
});

pm.test(""Response contains correct data"", function () {
    var responseJson = pm.response.json();
    var sentId = pm.collectionVariables.get(""random_id"");
    var sentAnimal = pm.collectionVariables.get(""random_animal"");
    var sentStatus = pm.collectionVariables.get(""random_status"");
    
    pm.expect(responseJson.id).to.eql(sentId);
    pm.expect(responseJson.name).to.eql(sentAnimal);
    pm.expect(responseJson.status).to.eql(sentStatus);
    pm.expect(responseJson.photoUrls[0]).to.eql(""https://example.com/photo1.jpg"");
});"
				Крок 2: Встановити URL: {{baseUrl}}/pet	URL поле заповнено, змінна відображається синім кольором		
				Крок 3: В Headers додати: Content-Type: application/json	Header додано в таблицю Headers		
				Крок 4: В Body вставити JSON з усіма полями (id, category, name, photoUrls, tags, status)	JSON body додано, всі змінні {{}} відображаються синім кольором		
				Крок 5: Відправити запит	Запит відправлено, отримано response		
				"Перевірки:
• Status Code = 200
• Response Time < 3000ms
• Content-Type містить ""application/json""
• response.id = sent.id
• response.name = sent.name
• response.status = sent.status"	Всі перевірки пройдені успішно, pet створено з правильними даними		
TC002	Створення pet з валідним статусом "available"	Позитивний	Перевірити створення pet з конкретним валідним статусом	Крок 1: Створити новий POST запит в Postman	Новий запит створено, тип встановлено як POST	"{
  ""id"": {{random_id}},
  ""category"": {
    ""id"": 1,
    ""name"": ""Dogs""
  },
  ""name"": ""Buddy"",
  ""photoUrls"": [
    ""https://example.com/buddy.jpg""
  ],
  ""tags"": [
    {
      ""id"": 1,
      ""name"": ""Friendly""
    }
  ],
  ""status"": ""available""
}"	"pm.test(""Status code is 200"", function () {
    pm.response.to.have.status(200);
});

pm.test(""Response time < 3000ms"", function () {
    pm.expect(pm.response.responseTime).to.be.below(3000);
});

pm.test(""Status is available"", function () {
    var responseJson = pm.response.json();
    pm.expect(responseJson.status).to.eql(""available"");
});

pm.test(""Name is Buddy"", function () {
    var responseJson = pm.response.json();
    pm.expect(responseJson.name).to.eql(""Buddy"");
});

pm.test(""Category name is Dogs"", function () {
    var responseJson = pm.response.json();
    pm.expect(responseJson.category.name).to.eql(""Dogs"");
});"
				Крок 2: Встановити URL: {{baseUrl}}/pet	URL поле заповнено, змінна відображається синім кольором		
				Крок 3: В Headers додати: Content-Type: application/json	Header додано в таблицю Headers		
				Крок 4: В Body вставити JSON з фіксованим статусом "available"	JSON body додано з правильним статусом		
				Крок 5: Відправити запит	Запит відправлено, отримано response		
				"Перевірки:
• Status Code = 200
• response.status = ""available""
• response.name = ""Buddy""
• response містить всі відправлені поля"	Pet створено зі статусом "available", всі дані коректні		
TC003	Відсутнє обов'язкове поле id	Негативний	Перевірити валідацію при відсутності обов'язкового поля id	Крок 1: Створити новий POST запит в Postman	Новий запит створено, тип встановлено як POST	"{
  ""category"": {
    ""id"": {{random_category_id}},
    ""name"": ""{{random_animal}}""
  },
  ""name"": ""{{random_animal}}"",
  ""photoUrls"": [
    ""https://example.com/photo.jpg""
  ],
  ""tags"": [
    {
      ""id"": {{random_tag_id}},
      ""name"": ""{{random_tag_name}}""
    }
  ],
  ""status"": ""available""
}"	"pm.test(""Status code is 405"", function () {
    pm.response.to.have.status(405);
});

pm.test(""Response time < 3000ms"", function () {
    pm.expect(pm.response.responseTime).to.be.below(3000);
});

pm.test(""Response has error structure"", function () {
    var responseJson = pm.response.json();
    pm.expect(responseJson).to.have.property('code');
    pm.expect(responseJson).to.have.property('message');
    pm.expect(responseJson).to.have.property('type');
});

pm.test(""Error related to missing id"", function () {
    var responseJson = pm.response.json();
    pm.expect(responseJson.message.toLowerCase()).to.include('id');
});"
				Крок 2: Встановити URL: {{baseUrl}}/pet	URL поле заповнено, змінна відображається синім кольором		
				Крок 3: В Headers додати: Content-Type: application/json	Header додано в таблицю Headers		
				Крок 4: В Body вставити JSON БЕЗ поля id	JSON body додано без обов'язкового поля id		
				Крок 5: Відправити запит	Запит відправлено, отримано error response		
				"Перевірки:
• Status Code = 405
• Response містить error message
• Error пов'язаний з відсутнім полем id
• Pet НЕ створено в системі"	Система правильно валідує відсутність обов'язкового поля id		
TC004	Відсутнє обов'язкове поле category	Негативний	Перевірити валідацію при відсутності обов'язкового поля category	Крок 1: Створити новий POST запит в Postman	Новий запит створено, тип встановлено як POST	"{
  ""id"": {{random_id}},
  ""category"": {
    ""id"": 1,
    ""name"": ""Dogs""
  },
  ""name"": ""Buddy"",
  ""photoUrls"": [
    ""https://example.com/buddy.jpg""
  ],
  ""tags"": [
    {
      ""id"": 1,
      ""name"": ""Friendly""
    }
  ],
  ""status"": ""available""
}"	"pm.test(""Status code is 405"", function () {
    pm.response.to.have.status(405);
});

pm.test(""Response time < 3000ms"", function () {
    pm.expect(pm.response.responseTime).to.be.below(3000);
});

pm.test(""Response has error structure"", function () {
    var responseJson = pm.response.json();
    pm.expect(responseJson).to.have.property('code');
    pm.expect(responseJson).to.have.property('message');
    pm.expect(responseJson).to.have.property('type');
});

pm.test(""Error related to missing category"", function () {
    var responseJson = pm.response.json();
    pm.expect(responseJson.message.toLowerCase()).to.include('category');
});"
				Крок 2: Встановити URL: {{baseUrl}}/pet	URL поле заповнено, змінна відображається синім кольором		
				Крок 3: В Headers додати: Content-Type: application/json	Header додано в таблицю Headers		
				Крок 4: В Body вставити JSON БЕЗ поля category	JSON body додано без обов'язкового поля category		
				Крок 5: Відправити запит	Запит відправлено, отримано error response		
				"Перевірки:
• Status Code = 405
• Response містить error message
• Error пов'язаний з відсутнім полем category
• Pet НЕ створено в системі"	Система правильно валідує відсутність обов'язкового поля category		
TC005	Невалідний enum для status	Негативний	Перевірити валідацію enum значень для обов'язкового поля status	Крок 1: Створити новий POST запит в Postman	Новий запит створено, тип встановлено як POST	"{
  ""id"": {{random_id}},
  ""category"": {
    ""id"": {{random_category_id}},
    ""name"": ""{{random_animal}}""
  },
  ""name"": ""{{random_animal}}"",
  ""photoUrls"": [
    ""https://example.com/photo.jpg""
  ],
  ""tags"": [
    {
      ""id"": {{random_tag_id}},
      ""name"": ""{{random_tag_name}}""
    }
  ],
  ""status"": ""unavailable""
}"	"pm.test(""Status code is 405"", function () {
    pm.response.to.have.status(405);
});

pm.test(""Response time < 3000ms"", function () {
    pm.expect(pm.response.responseTime).to.be.below(3000);
});

pm.test(""Response has error structure"", function () {
    var responseJson = pm.response.json();
    pm.expect(responseJson).to.have.property('message');
});

pm.test(""Error about invalid status"", function () {
    var responseJson = pm.response.json();
    pm.expect(responseJson.message.toLowerCase()).to.include('status');
});

pm.test(""Valid status values mentioned"", function () {
    var responseJson = pm.response.json();
    var message = responseJson.message.toLowerCase();
    pm.expect(message).to.satisfy(function(msg) {
        return msg.includes('available') || msg.includes('pending') || msg.includes('sold');
    });
});"
				Крок 2: Встановити URL: {{baseUrl}}/pet	URL поле заповнено, змінна відображається синім кольором		
				Крок 3: В Headers додати: Content-Type: application/json	Header додано в таблицю Headers		
				Крок 4: В Body вставити JSON зі status: "unavailable" (невалідне значення)	JSON body додано з невалідним enum значенням		
				Крок 5: Відправити запит	Запит відправлено, отримано error response		
				"Перевірки:
• Status Code = 405
• Response містить error про невалідний status
• Error вказує валідні значення: [""available"", ""pending"", ""sold""]
• Pet НЕ створено"	Система правильно валідує enum значення status поля		
TC006	Невалідна структура category (відсутнє поле name)	Негативний	Перевірити валідацію структури обов'язкового об'єкта category	Крок 1: Створити новий POST запит в Postman	Новий запит створено, тип встановлено як POST	"{
  ""id"": {{random_id}},
  ""category"": {
    ""id"": {{random_category_id}}
  },
  ""name"": ""{{random_animal}}"",
  ""photoUrls"": [
    ""https://example.com/photo.jpg""
  ],
  ""tags"": [
    {
      ""id"": {{random_tag_id}},
      ""name"": ""{{random_tag_name}}""
    }
  ],
  ""status"": ""available""
}"	"pm.test(""Status code is 405"", function () {
    pm.response.to.have.status(405);
});

pm.test(""Response time < 3000ms"", function () {
    pm.expect(pm.response.responseTime).to.be.below(3000);
});

pm.test(""Response has error structure"", function () {
    var responseJson = pm.response.json();
    pm.expect(responseJson).to.have.property('message');
});

pm.test(""Error about incomplete category"", function () {
    var responseJson = pm.response.json();
    var message = responseJson.message.toLowerCase();
    pm.expect(message).to.satisfy(function(msg) {
        return msg.includes('category') || msg.includes('name');
    });
});"
				Крок 2: Встановити URL: {{baseUrl}}/pet	URL поле заповнено, змінна відображається синім кольором		
				Крок 3: В Headers додати: Content-Type: application/json	Header додано в таблицю Headers		
				Крок 4: В Body вставити JSON з category без поля name (тільки id)	JSON body додано з неповною структурою об'єкта category		
				Крок 5: Відправити запит	Запит відправлено, отримано error response		
				"Перевірки:
• Status Code = 405
• Response містить error про неповну структуру
• Error вказує на відсутність поля name в category
• Pet НЕ створено"	Система правильно валідує структуру вкладених об'єктів		
TC007	Порожній масив photoUrls	Негативний	Перевірити валідацію порожнього масиву для обов'язкового поля photoUrls	Крок 1: Створити новий POST запит в Postman	Новий запит створено, тип встановлено як POST	"{
  ""id"": {{random_id}},
  ""category"": {
    ""id"": {{random_category_id}},
    ""name"": ""{{random_animal}}""
  },
  ""name"": ""{{random_animal}}"",
  ""photoUrls"": [],
  ""tags"": [
    {
      ""id"": {{random_tag_id}},
      ""name"": ""{{random_tag_name}}""
    }
  ],
  ""status"": ""available""
}
}"	"pm.test(""Status code is 405"", function () {
    pm.response.to.have.status(405);
});

pm.test(""Response time < 3000ms"", function () {
    pm.expect(pm.response.responseTime).to.be.below(3000);
});

pm.test(""Response has error structure"", function () {
    var responseJson = pm.response.json();
    pm.expect(responseJson).to.have.property('message');
});

pm.test(""Error about empty photoUrls"", function () {
    var responseJson = pm.response.json();
    var message = responseJson.message.toLowerCase();
    pm.expect(message).to.satisfy(function(msg) {
        return msg.includes('photo') || msg.includes('url') || msg.includes('empty');
    });
});"
				Крок 2: Встановити URL: {{baseUrl}}/pet	URL поле заповнено, змінна відображається синім кольором		
				Крок 3: В Headers додати: Content-Type: application/json	Header додано в таблицю Headers		
				Крок 4: В Body вставити JSON з photoUrls: [] (порожній масив)	JSON body додано з порожнім масивом photoUrls		
				Крок 5: Відправити запит	Запит відправлено, отримано error response		
				"Перевірки:
• Status Code = 405
• Response містить error про порожній масив
• Error вказує на необхідність мінімум одного photo URL
• Pet НЕ створено"	Система правильно валідує мінімальну кількість елементів у масиві		
