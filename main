from aiogram import Bot, Dispatcher, executor, types
from aiogram.types import ReplyKeyboardMarkup, KeyboardButton, InlineKeyboardMarkup, InlineKeyboardButton
from aiogram.dispatcher import FSMContext
from aiogram.dispatcher.filters.state import State, StatesGroup
from aiogram.contrib.fsm_storage.memory import MemoryStorage
from db import Database
from pyqiwip2p import QiwiP2P
import random

db = Database("database.db")
p2p = QiwiP2P(auth_key = "eyJ2ZXJzaW9uIjoiUDJQIiwiZGF0YSI6eyJwYXlpbl9tZXJjaGFudF9zaXRlX3VpZCI6ImVvcTBsei0wMCIsInVzZXJfaWQiOiI3OTM3Njk5OTA2NiIsInNlY3JldCI6IjQ5MWU5ZWRiZTY1NDNhNDg3ODkzYmU4YTMxMTY4OWQyZWRkZjQ5MGExZmI4MWFjOWUxZjdiNzU1MzhhMDgwOGYifX0=")
bot = Bot(token = '5921463586:AAHvcgjK2Ni7S3OOioUr8ZWkpzAZEtnd3G4')
dp = Dispatcher(bot, storage = MemoryStorage())

product1_price = "179"
product2_price = "899"
product3_price = "349"
product4_price = "199"
product5_price = "149"

class FSMcheckproduct(StatesGroup):
    product_price = State()
    promo = State()
    help = State()
    qiw = State()
class UserState(StatesGroup):
    id = State()
    otvet = State() 
@dp.message_handler(commands = ['start'])
async def send_welcome(message: types.Message):
    if message.chat.type == 'private':
        reply_markup = ReplyKeyboardMarkup(resize_keyboard = True, row_width = 2)
        mbtn1 = KeyboardButton("📲Тех.поддержка")
        mbtn2 = KeyboardButton("👨‍💻Профиль")
        mbtn4 = KeyboardButton("🛒Каталог")
        mbtn5т= KeyboardButton("💰Заработать")
        
        reply_markup.add(mbtn1, mbtn2,  mbtn4, mbtn5)
        if not db.user_exists(message.from_user.id):
            db.add_user(message.from_user.id)
        await bot.send_message(message.from_user.id, f"<b>Привет! {message.from_user.first_name}.Ты попал в сервис по созданию телеграмм ботов от @belzgy\n Тут ты сможешь заказать бота по низким ценам, или заработать на реферальной ситеме подробнее о ней можешь почитать во вкладке ЗАРАБОТОК</b>", reply_markup = reply_markup, parse_mode='HTML')
        
@dp.message_handler(content_types = ['text','photo'])
async def main_markup(message: types.Message):
    inline_markup1 = InlineKeyboardMarkup()
    inline_markup2 = InlineKeyboardMarkup(row_width = 1)
    inline_markup3 = InlineKeyboardMarkup(row_width = 1)
    inline_markup4 = InlineKeyboardMarkup(row_width = 1)
    ibtn1 = InlineKeyboardButton("📤Пополнить", callback_data = "top_up")
    ibtn3 = InlineKeyboardButton("✅Купить", callback_data = "balance_profile")
    ibtn2 = InlineKeyboardButton("⚠️Вывести средства", callback_data = "promo")
    ibtn4 = InlineKeyboardButton("🛠За помощью", callback_data = "help")
    ibtn5 = InlineKeyboardButton("🛠Ответить на помощь", callback_data = "qi")
    ibtn6 = InlineKeyboardButton("Успешный вывод", callback_data = "pr")
    
    inline_markup1.add(ibtn1, ibtn2)
    inline_markup2.add(ibtn3)
    inline_markup3.add(ibtn4)
    inline_markup4.add(ibtn5)
    inline_markup5.add(ibtn6)
    if message.text == "📲Тех.поддержка":
        await bot.send_photo(message.from_user.id, photo = 'https://i.imgur.com/LrKgiqE.jpeg', caption= "Тех.Поддержка напишет вам после рассмотрения вашей заявки обычно в течении 14 минут\nЧтобы обратится в поддержку нажмите на кнопку", reply_markup = inline_markup3 )

    @dp.callback_query_handler(text="help")
    async def qiwi_product2(callback: types.CallbackQuery):
      await FSMcheckproduct.help.set()
      await bot.send_message(callback.from_user.id, "Опиши свою проблему:")

    @dp.message_handler(content_types=['text'], state=FSMcheckproduct.help)
    async def get_address(message: types.Message, state: FSMContext):
    
      await state.update_data(help=message.text)
      data = await state.get_data()
      await bot.send_message(1742466144,
                         f"@{message.from_user.username} У данного человека проблема⚠️\n"
                         f"{message.from_user.id} id - того чела у которого проблема\n"    
                         f"Описание: {data['help']}", reply_markup = inline_markup4)
    
      await state.finish()
    
    @dp.callback_query_handler(text="qi")
    async def user_register(message: types.Message):
       await message.answer("Введите ID пользователя, которому хотите послать сообщение:")
       await UserState.id.set()

  
    @dp.message_handler(state=UserState.id)
    async def get_username(message: types.Message, state: FSMContext):
        await state.update_data(id=message.text)
        await message.answer("Теперь ответное сообщение:")
        await UserState.next() # либо же UserState.adress.set()


    @dp.message_handler(state=UserState.otvet)
    async def get_address(message: types.Message, state: FSMContext):
      await state.update_data(otvet=message.text)
      data = await state.get_data()
      await bot.send_message(data['id'],
                         f"Вывод успешно совершен!: {data['otvet']}")

      await state.finish()



    if message.text == "👨‍💻Профиль":
        await bot.send_photo(message.from_user.id, photo = 'https://i.imgur.com/6miDm2z.jpeg',
        caption=f"👨‍💻Имя пользователя: @{message.from_user.username}\n"
                f"➖➖➖➖➖➖➖➖➖➖➖\n"
                f"🔍ID:{message.from_user.id}\n"
                f"➖➖➖➖➖➖➖➖➖➖➖\n"
                f"  └💰Заработано средств: {db.user_money(message.from_user.id)} руб.", reply_markup = inline_markup1, parse_mode='HTML')



  

    if message.text == "💰Заработать":
        await bot.send_photo(message.from_user.id, photo = 'https://i.imgur.com/6miDm2z.jpeg',
        caption="Супер, я рад, что у тебя есть рвение заработать, и одновременно начать сотрудничество с нашим сервисом\nОбьясню, твой заработок будет основаться на реферальной системе, то есть рассылать ссылку на наш сервис\nС каждого приглашенного человека, который оформит заказ вы будете получать от 149 руб!\n❗️Внимание только с тех, кто офомил и оплатил предоплату ", parse_mode='HTML')

    @dp.callback_query_handler(text="promo")
    async def qiwi_product2(callback: types.CallbackQuery):
      await FSMcheckproduct.qiw.set()
      await bot.send_message(callback.from_user.id, "Вывод средств осуществляется на киви кошелек\nДля начала введите номер кошелька для  вывода:")

    @dp.message_handler(state=UserState.qiw)
    async def get_username(message: types.Message, state: FSMContext):
      await state.update_data(qiw=message.text)
      await message.answer("Отлично теперь сумму вывода")
      await UserState.next()

  
    @dp.message_handler(content_types=['text'], state=FSMcheckproduct.promo)
    async def check_product_price2(message: types.Message, state: FSMContext):
      async with state.proxy() as data:

        
        data['promo'] = int(message.text)
        user_money = db.user_money(message.from_user.id)
        
        if (user_money - int(message.text)) >= 1:
           db.set_money(message.from_user.id, user_money - int(message.text))
           await bot.send_message(message.from_user.id, "Отлично вывод средств осуществляется, обычно это занимает около 30 минут")
           await bot.send_message(1742466144,
                         f"@{message.from_user.username} - имя человека, вывод\n"
                         f"Номер: {data['qiw']}\n"
                         f"Сумма: {data['promo']}")
        if (user_money - int(message.text)) <= 1:
           await bot.send_message(message.from_user.id, "Недостаточно средств на балансе!")
           await state.finish()


      

      


    if message.text == "🛒Каталог":
        number_inline_cat = InlineKeyboardMarkup(row_width =2)
        cat1 = InlineKeyboardButton("👾Скрипты", callback_data = "cat1")
        number_inline_cat.add(cat1)
        await bot.send_message(message.from_user.id, "Выберите категрию: ", reply_markup = number_inline_cat)






    
   

    @dp.callback_query_handler(text = "number8")
    async def top_up(callback: types.CallbackQuery):
        await bot.send_message(callback.from_user.id, "Товар номер: 3.\nВыберите способ оплаты: ", reply_markup = inline_markup2)
        @dp.callback_query_handler(text = "balance_profile")
        async def top_down(callback: types.CallbackQuery):

            user_money = db.user_money(callback.from_user.id)
            money = int(product3_price)
    
            if (user_money - money) >= 0:
                db.set_money(callback.from_user.id, user_money + money)
                await bot.send_message(message.from_user.id, "Покупка успешно совершенна!")
            else:
                await bot.send_message(message.from_user.id, "Недостаточно средств на балансе!")
        @dp.callback_query_handler(text = "qiwi")
        async def qiwi_product3(callback: types.CallbackQuery):
            await FSMcheckproduct.product_price.set()
            await bot.send_message(message.from_user.id, "Впишите число " + product3_price)
        @dp.message_handler(content_types = ['text'], state = FSMcheckproduct.product_price)
        async def check_product_price3(message: types.Message, state: FSMContext):
            async with state.proxy() as data:
                data['product_price'] = message.text
                if message.text == product3_price:
                    await bot.send_message(message.from_user.id, "Успешно!")
                if message.text != product3_price:
                    await bot.send_message(message.from_user.id, "Ошибка введите число еще раз")
                    return 
                if message.chat.type == "private":
                    def is_number(_str):
                        try:
                            int(_str)
                            return True
                        except ValueError:
                            return False
                    if is_number(message.text):
                        message_money = int(message.text)
                        if message_money >= 5:
                            comment = str(message.from_user.id) + "_" + str(random.randint(1000, 9999))
                            bill = p2p.bill(amount = message_money, lifetime = 15, comment = comment)

                            db.add_check(message.from_user.id, message_money, bill.bill_id)

                            await bot.send_message(message.chat.id, f"Вам нужно отправить {message_money} руб. на наш счет QIWI\nСсылка: {bill.pay_url}\nУказав комментарий к оплате: {comment}", reply_markup = buy_menu(url = bill.pay_url, bill = bill.bill_id))
                        else:
                            await bot.send_message(message.chat.id,"Минимальная сумма для платежа 5 руб")
            await state.finish()

    if message.chat.type == "private":
        def is_number(_str):
            try:
                int(_str)
                return True
            except ValueError:
                return False
        if is_number(message.text):
            message_money = int(message.text)
            if message_money >= 5:
                comment = str(message.from_user.id) + "_" + str(random.randint(1000, 9999))
                bill = p2p.bill(amount = message_money, lifetime = 15, comment = comment)

                db.add_check(message.from_user.id, message_money, bill.bill_id)

                await bot.send_message(message.from_user.id, f"Вам нужно отправить {message_money} руб. на наш счет QIWI\nСсылка: {bill.pay_url}\nУказав комментарий к оплате: {comment}", reply_markup = buy_menu(url = bill.pay_url, bill = bill.bill_id))
            else:
                await bot.send_message(message.from_user.id,"Минимальная сумма для платежа 5 руб")

def buy_menu(isURL = True, url = "", bill = ""):
        qiwiMenu = InlineKeyboardMarkup(row_width = 1)
        if isURL:
            btnUrlQIWI = InlineKeyboardButton(text = "Ссылка на оплату", url = url)
            qiwiMenu.insert(btnUrlQIWI)
        btnCheckQIWI = InlineKeyboardButton(text = "Проверить оплату", callback_data = "check_" + bill)
        qiwiMenu.insert(btnCheckQIWI)
        return qiwiMenu


@dp.callback_query_handler(text = "top_up")
async def top_up(callback: types.CallbackQuery):
    await bot.send_message(callback.from_user.id, "Введите сумму для пополнения!")

@dp.callback_query_handler(text_contains = "check_")
async def top_up(callback: types.CallbackQuery):
    bill = str(callback.data[1:])
    info = db.get_check(bill)
    if info != False:
        if str(p2p.check(bill_id = bill).status) == "PAID":
            user_money = db.user_money(callback.from_user.id)
            money = int(info[2])
            db.set_money(callback.from_user.id, user_money + money)
            await bot.send_message(callback.chat.id,"Ваш счет пополнен!")
        else:
            await bot.send_message(callback.from_user.id,"Вы не оплатили счет", reply_markup = buy_menu(False, bill = bill))
    else:
        await bot.send_message(callback.from_user.id,"Счет не найден")

if __name__ == '__main__':
    executor.start_polling(dp, skip_updates = True)
        
