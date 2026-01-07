import telebot
from telebot import types
import random
import json
import os

TOKEN = "8462048670:AAEalaoEW-HRepmbbMprHoCF4Phu7aUN4Lo"

bot = telebot.TeleBot(TOKEN)

# ====== ФАЙЛ ДАННЫХ ======
DATA_FILE = "players.json"

def load_players():
    if not os.path.exists(DATA_FILE):
        return {}
    with open(DATA_FILE, "r", encoding="utf-8") as f:
        return json.load(f)

def save_players(data):
    with open(DATA_FILE, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=2)

players = load_players()

def get_player(uid):
    if uid not in players:
        players[uid] = {
            "balance": 0,
            "stand": None
        }
    return players[uid]

# ====== СТЕНДЫ ======
STANDS = [
    {
        "name": "Star Platinum",
        "desc": "Очень мощный стенд ближнего боя с огромной скоростью и силой.",
        "image": "https://i.pinimg.com/736x/87/f0/60/87f060646c825d74df4d1bc531dab812.jpg"
    },
    {
        "name": "Crazy Diamond",
        "desc": "Мощный стенд ближнего боя с возможностью восстанавливать объекты и лечить раны.",
        "image": "https://i.imgur.com/Q5ZQZQx.jpg"
    },
    {
        "name": "The World",
        "desc": "Стенд, способный останавливать время.",
        "image": "https://i.imgur.com/dJ7ZQZB.jpg"
    },
    {
        "name": "Killer Queen",
        "desc": "Стенд, превращающий всё, к чему прикасается, в бомбы.",
        "image": "https://i.imgur.com/fQK7JYQ.jpg"
    },
    {
        "name": "King Crimson",
        "desc": "Стенд, способный стирать время и предсказывать будущее.",
        "image": "https://i.imgur.com/5ZQZ9iC.jpg"
    }
]

# ====== БОССЫ ======
BOSSES = [
    {
        "id": "dio",
        "name": "Дио Брандо",
        "desc": "Легендарный вампир со стендом The World.",
        "image": "https://i.imgur.com/3Z3ZK5H.jpg",
        "win_chance": 0.25,
        "reward": 1500
    },
    {
        "id": "kira",
        "name": "Йошикаге Кира",
        "desc": "Серийный убийца со стендом Killer Queen.",
        "image": "https://i.imgur.com/fQK7JYQ.jpg",
        "win_chance": 0.5,
        "reward": 750
    },
    {
        "id": "diavolo",
        "name": "Дьяволо",
        "desc": "Босс мафии со стендом King Crimson.",
        "image": "https://i.imgur.com/5ZQZ9iC.jpg",
        "win_chance": 0.2,
        "reward": 1000
    },
    {
        "id": "pucci",
        "name": "Энрико Пуччи",
        "desc": "Священник, стремящийся к перезагрузке вселенной.",
        "image": "https://i.imgur.com/0G9QZQZ.jpg",
        "win_chance": 0.35,
        "reward": 1250
    }
]

# ====== КЛАВИАТУРА ======
def main_menu():
    kb = types.ReplyKeyboardMarkup(resize_keyboard=True)
    kb.add("Получить стенд🏹")
    kb.add("⚔️ Боссы")
    kb.add("Аккаунт👨‍🦱")
    return kb

# ====== СТАРТ ======
@bot.message_handler(commands=["start"])
def start(message):
    if message.chat.type != "private":
        return

    uid = str(message.from_user.id)
    get_player(uid)
    save_players(players)

    bot.send_message(
        message.chat.id,
        "🔥 Добро пожаловать в JoJo Stand Battle!",
        reply_markup=main_menu()
    )

# ====== АККАУНТ ======
@bot.message_handler(func=lambda m: m.text == "Аккаунт👨‍🦱")
def account(message):
    if message.chat.type != "private":
        return

    uid = str(message.from_user.id)
    player = get_player(uid)

    stand = player["stand"] if player["stand"] else "❌ Нет стенда"

    bot.send_message(
        message.chat.id,
        f"👤 Твой профиль:\n\n🎴 Стенд: {stand}\n💰 Баланс: {player['balance']} рублей"
    )

# ====== ПОЛУЧИТЬ СТЕНД ======
@bot.message_handler(func=lambda m: m.text == "Получить стенд🏹")
def get_stand(message):
    if message.chat.type != "private":
        return

    uid = str(message.from_user.id)
    player = get_player(uid)

    stand = random.choice(STANDS)
    player["stand"] = stand["name"]
    save_players(players)

    bot.send_photo(
        message.chat.id,
        stand["image"],
        caption=f"🎉 Тебе выпал стенд {stand['name']}!\n\n{stand['desc']}"
    )

# ====== БОССЫ ======
@bot.message_handler(func=lambda m: m.text == "⚔️ Боссы")
def bosses(message):
    if message.chat.type != "private":
        return

    kb = types.ReplyKeyboardMarkup(resize_keyboard=True)
    for b in BOSSES:
        kb.add(f"👿 {b['name']}")
    kb.add("⬅️ Назад")

    bot.send_message(message.chat.id, "⚔️ Выбери босса:", reply_markup=kb)

# ====== НАЗАД ======
@bot.message_handler(func=lambda m: m.text == "⬅️ Назад")
def back(message):
    if message.chat.type != "private":
        return

    bot.send_message(message.chat.id, "🏠 Главное меню", reply_markup=main_menu())

# ====== БОЙ ======
@bot.message_handler(func=lambda m: m.text and m.text.startswith("👿"))
def fight(message):
    if message.chat.type != "private":
        return

    uid = str(message.from_user.id)
    player = get_player(uid)

    boss_name = message.text.replace("👿 ", "")
    boss = None
    for b in BOSSES:
        if b["name"] == boss_name:
            boss = b
            break

    if not boss:
        return

    bot.send_photo(message.chat.id, boss["image"], caption=boss["desc"])

    if random.random() <= boss["win_chance"]:
        player["balance"] += boss["reward"]
        save_players(players)
        bot.send_message(message.chat.id, f"🎉 Ты победил {boss['name']}!\n💰 +{boss['reward']} рублей")
    else:
        bot.send_message(message.chat.id, f"💀 Ты проиграл бой против {boss['name']}...")

    bot.send_message(message.chat.id, "🏠 Возврат в меню", reply_markup=main_menu())

# ====== ЗАПУСК ======
print("Бот запущен!")
bot.infinity_polling()
