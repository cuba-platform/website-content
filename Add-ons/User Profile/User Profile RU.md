## Описание
Этот компонент приложения добавляет следующие свойства при добавлении в проект на платформе CUBA:
- Предоставляет `UserProfileService` с функциями для получения и обновления данных залогинившегося пользователя
- (*профиль пользователя*)
- Предоставляет новую точку доступа  REST API (*Richardson Maturity Model - Level 2* compliant), `/rest/nxsecup/v1/userProfile` поддерживающую запросы GET (`getProfile`) и PUT (`updateProfile`), и точку доступа `/rest/nxsecup/v1/userProfile/password` с поддержкой POST-запросов для обновления пароля пользователя
- Добавляет экран `userProfile` и соответствующий пункт меню после Settings в главном меню Help
- Опционально скрывает кнопку *Change password* на экране `Settings` (потому что кнопка повторяется на экране `userProfile`)

Для дополнительной информации, пожалуйста, ознакомьтесь с  [README](https://github.com/pfurini/cuba-component-user-profile#cuba-user-profile-component) на GitHub.