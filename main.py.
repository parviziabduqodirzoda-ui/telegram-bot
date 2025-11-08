import os
import time
import requests
import telebot  # pyTelegramBotAPI

BOT_TOKEN = os.getenv("BOT_TOKEN")
if not BOT_TOKEN:
    raise SystemExit("Error: BOT_TOKEN not set in environment variables")

bot = telebot.TeleBot(BOT_TOKEN, parse_mode=None)

# Пример функции получения цены с Bybit (v5 public tickers)
def get_price(symbol="BTCUSDT"):
    url = f"https://api.bybit.com/v5/market/tickers?category=linear&symbol={symbol}"
    r = requests.get(url, timeout=10)
    r.raise_for_status()
    data = r.json()
    # безопасный доступ к цене
    try:
        return float(data["result"]["list"][0]["lastPrice"])
    except Exception:
        return None

@bot.message_handler(commands=["start"])
def cmd_start(message):
    bot.reply_to(message, "✅ Бот запущен и готов присылать сигналы.")

@bot.message_handler(commands=["price"])
def cmd_price(message):
    args = message.text.split()
    symbol = args[1].upper() if len(args) > 1 else "BTCUSDT"
    price = get_price(symbol)
    if price is None:
        bot.reply_to(message, f"⚠️ Не удалось получить цену для {symbol}")
    else:
        bot.reply_to(message, f"💎 Текущая цена {symbol}: {price}")

# Простой loop — long polling
if __name__ == "__main__":
    while True:
        try:
            bot.infinity_polling(timeout=20, long_polling_timeout=20)
        except Exception as e:
            print("Bot error:", e)
            time.sleep(5)
