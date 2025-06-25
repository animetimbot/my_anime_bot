# my_anime_bot import telebot
import flask
import os

TOKEN = os.environ.get("BOT_TOKEN")  # Токен бота из переменной окружения
bot = telebot.TeleBot(TOKEN)
server = flask.Flask(__name__)

@bot.message_handler(commands=['start'])
def start(message):
    bot.send_message(message.chat.id, "Бот работает через вебхук!")

@server.route(f"/{TOKEN}", methods=["POST"])
def webhook():
    json_str = flask.request.get_data().decode("utf-8")
    update = telebot.types.Update.de_json(json_str)
    bot.process_new_updates([update])
    return "!", 200

@server.route("/")
def home():
    return "Бот онлайн."

if __name__ == "__main__":
    port = int(os.environ.get("PORT", 5000))
    server.run(host="0.0.0.0", port=port)
