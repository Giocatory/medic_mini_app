# 🏥 Медицинский анализатор лабораторных результатов

[![Telegram Mini App](https://img.shields.io/badge/Telegram-Mini%20App-0088cc?logo=telegram)](https://core.telegram.org/bots/webapps)
[![HTML5](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)](https://developer.mozilla.org/ru/docs/Web/HTML)
[![CSS3](https://img.shields.io/badge/CSS3-1572B6?logo=css3&logoColor=white)](https://developer.mozilla.org/ru/docs/Web/CSS)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)](https://developer.mozilla.org/ru/docs/Web/JavaScript)

Многофункциональное Telegram Mini App для расшифровки и анализа медицинских лабораторных результатов. Приложение предоставляет подробные объяснения показателей крови, мочи и системы свертываемости.
> имеет информационных характер, за медицинской помощью - обратитесь к специалисту

## ✨ Возможности

### 🔬 Поддерживаемые типы анализов

1. **🩸 Общий анализ крови (ОАК)**
   - Гемоглобин, эритроциты, лейкоциты
   - Тромбоциты, гематокрит, СОЭ
   - Лейкоцитарная формула (нейтрофилы, лимфоциты)

2. **🧪 Биохимический анализ крови**
   - Углеводный обмен (глюкоза)
   - Липидный профиль (холестерин, ЛПНП, ЛПВП, триглицериды)
   - Печеночные показатели (билирубин, АЛТ, АСТ)
   - Почечные показатели (креатинин, мочевина)
   - Белковый обмен (общий белок, альбумин)

3. **💧 Общий анализ мочи (ОАМ)**
   - Физические свойства (цвет, прозрачность, удельный вес)
   - Химические показатели (белок, глюкоза, кетоны)
   - Микроскопия (лейкоциты, эритроциты, эпителий)

4. **🩺 Гемостазиограмма**
   - Показатели свертываемости (протромбиновое время, МНО, АЧТВ)
   - Фибриноген, тромбиновое время
   - D-димер

### 🎯 Основные функции

- **📊 Интеллектуальный анализ** - автоматическое определение отклонений от нормы
- **🎨 Визуальная индикация** - цветовые маркеры для нормальных и опасных показателей
- **📤 Мгновенная отправка** - результаты можно отправить прямо в Telegram чат
- **📋 Копирование в буфер** - альтернативный способ сохранения результатов
- **📱 Адаптивный дизайн** - оптимизирован для мобильных устройств
- **⚡ Быстрая работа** - не требует backend, работает полностью на фронтенде

## 🚀 Быстрый старт

### 1. Создание бота Telegram

1. Найдите в Telegram [@BotFather](https://t.me/BotFather)
2. Отправьте команду `/newbot`
3. Укажите имя и username для бота
4. Сохраните полученный токен

### 2. Настройка Mini App

1. Откройте настройки бота в @BotFather
2. Выберите пункт "Bot Settings" → "Menu Button"
3. Введите URL вашего приложения (после размещения на хостинге)

### 3. Размещение приложения

#### Вариант 1: GitHub Pages (рекомендуется)
1. Форкните этот репозиторий
2. В настройках репозитория перейдите в "Pages"
3. Выберите источник "GitHub Actions"
4. Файл будет доступен по адресу: `https://ваш-username.github.io/имя-репозитория`

#### Вариант 2: Любой статический хостинг
- Загрузите файл `index.html` на любой статический хостинг:
  - Netlify
  - Vercel
  - Firebase Hosting
  - или любой другой

### 4. Настройка бота для приема данных

Добавьте в код вашего бота обработчик данных из Mini App:

#### Пример на Python (python-telegram-bot)
```python
from telegram import Update
from telegram.ext import Application, CommandHandler, ContextTypes, MessageHandler, filters
import json
import html
import logging

# Включите логирование
logging.basicConfig(
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO
)

async def handle_web_app_data(update: Update, context: ContextTypes.DEFAULT_TYPE):
    try:
        # Получаем данные из Mini App
        data = json.loads(update.message.web_app_data.data)
        
        # Форматируем сообщение для отправки
        message_text = f"🧪 <b>Результаты анализа</b>\n\n"
        message_text += f"📊 <b>Тип анализа:</b> {data.get('type', 'Не указан')}\n"
        message_text += f"📅 <b>Время анализа:</b> {data.get('timestamp', 'Не указано')}\n\n"
        
        # Добавляем результаты
        for result in data.get('results', []):
            status_icon = "✅" if result.get('status') == 'normal' else "⚠️" if result.get('status') == 'warning' else "❌"
            message_text += f"{status_icon} <b>{html.escape(result.get('parameter', ''))}:</b> {html.escape(result.get('value', ''))}\n"
            message_text += f"{html.escape(result.get('explanation', ''))}\n\n"
        
        # Отправляем сообщение
        await update.message.reply_text(message_text, parse_mode='HTML')
        
    except Exception as e:
        logging.error(f"Ошибка обработки данных: {e}")
        await update.message.reply_text("❌ Произошла ошибка при обработке результатов")

def main():
    # Создаем Application
    application = Application.builder().token("ВАШ_ТОКЕН_БОТА").build()
    
    # Добавляем обработчик данных из Web App
    application.add_handler(MessageHandler(filters.StatusUpdate.WEB_APP_DATA, handle_web_app_data))
    
    # Запускаем бота
    application.run_polling()

if __name__ == '__main__':
    main()
```

#### Пример на Node.js (node-telegram-bot-api)
```javascript
const TelegramBot = require('node-telegram-bot-api');
const token = 'ВАШ_ТОКЕН_БОТА';
const bot = new TelegramBot(token, {polling: true});

bot.on('web_app_data', (msg) => {
    try {
        const data = JSON.parse(msg.web_app_data.data);
        let messageText = `🧪 <b>Результаты анализа</b>\n\n`;
        messageText += `📊 <b>Тип анализа:</b> ${data.type || 'Не указан'}\n`;
        messageText += `📅 <b>Время анализа:</b> ${data.timestamp || 'Не указано'}\n\n`;
        
        data.results.forEach(result => {
            const statusIcon = result.status === 'normal' ? '✅' : result.status === 'warning' ? '⚠️' : '❌';
            messageText += `${statusIcon} <b>${result.parameter}:</b> ${result.value}\n`;
            messageText += `${result.explanation}\n\n`;
        });
        
        bot.sendMessage(msg.chat.id, messageText, {parse_mode: 'HTML'});
    } catch (error) {
        console.error('Ошибка обработки данных:', error);
        bot.sendMessage(msg.chat.id, '❌ Произошла ошибка при обработке результатов');
    }
});
```

## 📁 Структура проекта

```
medical-analyzer/
├── index.html              # Основной файл приложения
├── README.md              # Документация
└── assets/                # Дополнительные ресурсы (при необходимости)
    ├── images/
    │   └── icon.png       # Иконка приложения
    └── css/
        └── style.css      # Дополнительные стили
```

## 🛠 Технические детали

### Архитектура
- **Frontend**: Чистый HTML5, CSS3, JavaScript (ES6+)
- **Backend**: Не требуется (полностью клиентское приложение)
- **Интеграция**: Telegram Web App API
- **Хостинг**: Любой статический хостинг

### Используемые технологии
- **Telegram Web App SDK** - интеграция с Telegram
- **CSS Grid/Flexbox** - адаптивная верстка
- **Modern JavaScript** - обработка данных и логика
- **Local Storage** (опционально) - кэширование данных

### Безопасность
- Валидация вводимых данных на клиенте
- Отсутствие хранения медицинских данных
- Все вычисления производятся локально

## 🎨 Кастомизация

### Изменение цветовой схемы
```css
:root {
    --primary-color: #4facfe;
    --warning-color: #ff6b6b;
    --success-color: #51cf66;
    --background-gradient: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}
```

### Добавление новых показателей
1. Добавьте поле ввода в соответствующую форму
2. Обновите функцию анализа в JavaScript
3. Добавьте нормативные значения и пояснения

### Пример добавления нового показателя
```javascript
// В функции analyzeBloodGeneral() добавить:
if (newParameter) {
    hasData = true;
    if (newParameter < minNormal) {
        results.push({
            parameter: 'Новый параметр',
            value: newParameter + ' ед.',
            status: 'warning',
            explanation: 'Пояснение при отклонении...'
        });
    } else if (newParameter > maxNormal) {
        // ... обработка повышенного значения
    } else {
        // ... обработка нормального значения
    }
}
```

## 📊 Нормативные значения

Приложение использует актуальные медицинские нормы:

- **Общий анализ крови**: по данным ВОЗ
- **Биохимические показатели**: стандартные лабораторные нормы
- **Анализ мочи**: клинические рекомендации
- **Гемостазиограмма**: стандарты коагулологии

> ⚠️ **Важно**: Нормативные значения могут варьироваться в зависимости от лаборатории и метода исследования. Всегда консультируйтесь с врачом для точной интерпретации результатов.

## 🌟 Возможности расширения

### Планируемые улучшения
- [ ] История анализов пользователя
- [ ] Графики динамики показателей
- [ ] Учет возраста и пола при анализе
- [ ] Поддержка дополнительных языков
- [ ] Экспорт результатов в PDF
- [ ] Интеграция с популярными лабораториями

### Разработка новых модулей
1. **Гормональный профиль** - анализ гормонов щитовидной железы, половых гормонов
2. **Иммунологический статус** - показатели иммунной системы
3. **Онкомаркеры** - анализ маркеров онкологических заболеваний
4. **Витаминный профиль** - уровень витаминов и микроэлементов

## 🤝 Участие в разработке

Мы приветствуем contributions! Как вы можете помочь:

1. **Сообщения об ошибках** - создавайте issues с описанием проблемы
2. **Новые функции** - предлагайте идеи через issues или pull requests
3. **Улучшение документации** - помогайте делать документацию лучше
4. **Переводы** - добавляйте поддержку новых языков

### Процесс разработки
1. Форкните репозиторий
2. Создайте ветку для вашей функции (`git checkout -b feature/amazing-feature`)
3. Закоммитьте изменения (`git commit -m 'Add some amazing feature'`)
4. Запушьте в ветку (`git push origin feature/amazing-feature`)
5. Откройте Pull Request

## 📄 Помощь

Можете мотивировать разработки донатом на ТБанк: 89965845547
Или USDT (TRC20): TSmubJ7FyW19BqvibprE1Ca7N2PBK81hYu

## ⚠️ Медицинское предупреждение

Данное приложение предназначено исключительно для информационных целей и не может заменить консультацию квалифицированного медицинского работника. Все решения относительно здоровья должны приниматься на основе консультации с врачом.

**Авторы не несут ответственности** за любые последствия, возникшие в результате использования данного приложения.

## 📞 Поддержка

Если у вас возникли вопросы или проблемы:

1. **Issues** - создайте issue в этом репозитории
2. **Email** - python@derkunov.ru
3. **Telegram** - напишите в [бот поддержки](https://t.me/your_support_bot)

## 🏥 Для медицинских учреждений

Заинтересованы во внедрении этого решения в вашем медицинском учреждении? Мы предлагаем:

- 📦 White-label версии
- 🔧 Кастомизация под ваши нужды
- 🏢 Интеграция с вашими системами
- 📊 Аналитика и отчетность

Свяжитесь с нами для обсуждения сотрудничества.

---

**⭐ Если этот проект был полезен для вас, поставьте звезду на GitHub!**
