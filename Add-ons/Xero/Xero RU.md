## Описание
Компонент для быстрой интеграции с Xero API.

Главным образом, он использует официальный [Xero Java SDK](https://github.com/XeroAPI/Xero-Java). Конфигурация SDK настраивается с помощью свойств приложения CUBA.

Для скачивания необходимых зависимостей необходимо добавить следующие репозитории Maven в файл   `build.gradle`:

```
buildscript {  
    //...   
    repositories {
        maven { url "https://raw.github.com/XeroAPI/Xero-Java/mvn-repo" }
        maven { url "https://raw.github.com/XeroAPI/XeroAPI-Schemas/mvn-repo" }
        //...       
    }
}
```

Также репозитории можно добавить в Studio: откройте вкладку Project Properties, затем кликните на кнопку в поле Repository, чтобы открыть диалоговое окно конфигурации репозиториев.

## Ссылки
* [Видеоурок](https://www.youtube.com/watch?v=A5HZM-wlOJA), демонстрирующий настройку интеграции Xero SDK с приложением CUBA
* [Документация](https://github.com/mariodavid/cuba-component-xero/blob/master/README.md)